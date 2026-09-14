# 高级配方技巧 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.item.IItemStack;`、`import crafttweaker.item.IIngredient;`

遍历配方、数据驱动修改、常见配方模式。

---

## 遍历所有配方

```zenscript
for recipe in recipes.all {
    if (recipe.ingredients1D has <minecraft:iron_ingot>) {
        recipes.removeByRecipeName(recipe.name);
    }
}
```

---

## 数据驱动合成修改

使用映射保存配方信息，批量处理。

```zenscript
import crafttweaker.item.IItemStack;
import crafttweaker.item.IIngredient;

// 有序配方映射
val shapedRecipes as IIngredient[][][IItemStack] = {
    <minecraft:iron_block> : [
        [<ore:ingotIron>, <ore:ingotIron>, <ore:ingotIron>],
        [<ore:ingotIron>, <ore:ingotGold>, <ore:ingotIron>],
        [<ore:ingotIron>, <ore:ingotIron>, <ore:ingotIron>]
    ],
    <minecraft:stick> * 4 : [
        [<ore:plankWood>, null],
        [null, <ore:plankWood>]
    ]
};

// 无序配方映射
val shapelessRecipes as IIngredient[][IItemStack] = {
    <minecraft:grass> : [<minecraft:dirt>, <minecraft:sapling:*>]
};

// 禁用物品列表
val bannedItems as IItemStack[] = [
    <extrautils2:angelring:*>,
    <mekanism:basicblock:6>
];

// 执行修改
for item, ingredients in shapedRecipes {
    recipes.remove(item);
    recipes.addShaped(item.displayName ~ "_resized", item, ingredients);
}

for item, ingredients in shapelessRecipes {
    recipes.remove(item);
    recipes.addShapeless(item.displayName ~ "_reshaped", item, ingredients);
}

for item in bannedItems {
    recipes.remove(item);
}
```

---

## 常见配方模式

### 批量修改配方（循环）

```zenscript
import crafttweaker.item.IItemStack;

var logs as IItemStack[] = [
    <minecraft:log>, <minecraft:log:1>, <minecraft:log:2>,
    <minecraft:log:3>, <minecraft:log:4>, <minecraft:log:5>
];
var planks as IItemStack[] = [
    <minecraft:planks>, <minecraft:planks:1>, <minecraft:planks:2>,
    <minecraft:planks:3>, <minecraft:planks:4>, <minecraft:planks:5>
];

for i, log in logs {
    var plank = planks[i];
    recipes.removeShapeless(plank, [log]);
    recipes.addShapeless("plank_double_" ~ i, plank * 2, [log]);
}
```

### 用 IItemDefinition 批量操作

```zenscript
import crafttweaker.item.IItemDefinition;
val woolDef as IItemDefinition = <minecraft:wool>.definition;

for i in 0 to 16 {
    recipes.remove(woolDef.makeStack(i));
}
```

### NBT 物品配方

```zenscript
// 带 NBT 的输出
recipes.addShapeless("soap", <minecraft:iron_ingot>.withTag({display: {Name: "肥皂"}}),
    [<ore:dirt>, <ore:dirt>, <minecraft:clay_ball>]);

// 带 NBT 的输入（精确匹配）
recipes.addShaped("nbt_recipe", <minecraft:diamond>, [
    [<minecraft:iron_ingot>.withTag({Unbreakable: 1}), null, null],
    [null, null, null],
    [null, null, null]
]);
```

### 条件加载（多 mod 兼容）

```zenscript
#modloaded thermalfoundation
#priority 10
import crafttweaker.item.IItemStack;

// 只有热力基础加载时才执行
recipes.remove(<thermalfoundation:material:128>);
recipes.addShaped("copper_ingot", <thermalfoundation:material:128>, [
    [<ore:oreCopper>]
]);
```

### 物品转换器组合

```zenscript
// 工具不消耗 + 掉耐久
recipes.addShapeless("plank_axe", <minecraft:planks> * 6, [
    <minecraft:iron_axe>.reuse().transformDamage(), <minecraft:log>
]);

// 物品转换：牛奶桶 → 空桶
recipes.addShapeless("cake", <minecraft:cake>, [
    <minecraft:milk_bucket>.transformReplace(<minecraft:bucket>),
    <minecraft:sugar>, <minecraft:wheat>, <minecraft:egg>
]);
```
