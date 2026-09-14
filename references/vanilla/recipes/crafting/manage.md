# 合成配方管理 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.recipes.ICraftingRecipe;`

移除、查询、替换配方和模拟合成。

---

## 移除配方

```zenscript
// 移除物品的所有配方
recipes.remove(<minecraft:iron_pickaxe>);

// 移除物品的所有配方（可选 NBT 匹配）
recipes.remove(<minecraft:iron_pickaxe>, true);  // true = 仅移除 NBT 完全匹配的配方

// 移除特定有序配方（inputBox 可省略表示匹配所有）
recipes.removeShaped(<minecraft:stick> * 4, [
    [<ore:plankWood>],
    [<ore:plankWood>]
]);

// 移除特定无序配方
recipes.removeShapeless(<minecraft:book>, [
    <minecraft:paper>, <minecraft:paper>, <minecraft:paper>, <minecraft:leather>
]);

// 移除特定无序配方（wildcard=true 时允许配方含有未指定的额外材料）
recipes.removeShapeless(<minecraft:book>, [
    <minecraft:paper>, <minecraft:leather>
], true);

// 按配方 ID 移除
recipes.removeByRecipeName("minecraft:golden_chestplate");

// 按正则匹配移除
recipes.removeByRegex("minecraft:.*_sword");

// 按输入材料移除（移除所有以该物品为输入的配方）
recipes.removeByInput(<minecraft:iron_ingot>);

// 移除某 mod 所有配方
recipes.removeByMod("botania");

// 移除所有工作台合成配方（仅合成台，不含熔炉/酿造等）
recipes.removeAll();
```

- `recipes.remove(output, NBTMatch)`: `output` 是 IIngredient，`NBTMatch` 是可选的 boolean（默认 false），为 true 时仅移除 NBT 数据完全匹配的配方
- `recipes.removeShapeless(output, inputs, wildcard)`: `wildcard` 是可选的 boolean（默认 false），为 true 时会移除含有指定材料但不限制其他材料的无序配方
- `recipes.removeByInput(ingredient)`: `ingredient` 是 IIngredient，移除所有包含该材料作为输入的配方

---

## 查询配方

```zenscript
// 获取所有已注册的工作台配方
var allRecipes = recipes.all;  // 返回 List<ICraftingRecipe>

// 获取包含指定材料的所有配方
var ironRecipes = recipes.getRecipesFor(<minecraft:iron_ingot>);  // 返回 List<ICraftingRecipe>
```

---

## 替换配方材料

```zenscript
// 将配方中所有某材料替换为另一材料
recipes.replaceAllOccurences(<minecraft:stick>, <minecraft:stone>);

// 仅替换特定输出配方中的材料
recipes.replaceAllOccurences(<ore:gemDiamond>, <ore:blockDiamond>, <minecraft:diamond_sword>);

// 替换时可使用条件过滤输出
recipes.replaceAllOccurences(<ore:gunpowder>, <minecraft:tnt>, <*>.only(function(item) {
    return !isNull(item) & !<minecraft:tnt>.matches(item);
}));
```

- `recipes.replaceAllOccurences(toReplace, replaceWith, forOutput)`: `toReplace` 是要被替换的 IIngredient，`replaceWith` 是替换后的 IIngredient，`forOutput` 是可选的 IIngredient 用于限定只替换特定输出的配方

---

## 模拟合成

```zenscript
// 根据输入内容模拟合成，返回 IItemStack 或 null
var result = recipes.craft([[<minecraft:iron_ingot>]]);
```

- `recipes.craft(content)`: `content` 是 IItemStack[][]，返回 IItemStack（找到配方时）或 null（未找到配方时）
