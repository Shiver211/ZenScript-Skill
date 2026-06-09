# 合成配方 API CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.recipes.ICraftingRecipe;`、`import crafttweaker.recipes.ICraftingInventory;`、`import crafttweaker.recipes.ICraftingInfo;`

工作台合成配方 API 和添加配方方法。

---

## API 列表

### ICraftingRecipe（工作台配方）

> `import crafttweaker.recipes.ICraftingRecipe;`

工作台配方在 ZenScript 中的表示对象。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `output` | IItemStack | 配方输出（可能为 null） |
| `ingredients1D` | IIngredient[] | 一维材料列表 |
| `ingredients2D` | IIngredient[][] | 二维材料列表 |
| `shaped` | boolean | 是否为有序配方 |
| `hidden` | boolean | 是否隐藏 |
| `hasTransformers` | boolean | 是否含有转换器 |
| `hasRecipeFunction` | boolean | 是否含有配方函数 |
| `hasRecipeAction` | boolean | 是否含有配方事件 |
| `resourceDomain` | string | 添加该配方的 mod 的 modid |
| `fullResourceDomain` | string | 完整的 resourceDomain |
| `name` | string | 配方名称 |
| `commandString` | string | 配方的命令字符串表示 |

#### 方法

```zenscript
// 获取配方名称（等同于 name 属性）
rec.getName();

// 获取配方的命令字符串表示（等同于 commandString 属性）
rec.toCommandString();
```

### ICraftingInventory（合成容器）

> `import crafttweaker.recipes.ICraftingInventory;`

合成过程中工作台容器的信息对象。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `player` | IPlayer | 拥有该容器的玩家 |
| `size` | int | 容器大小 |
| `width` | int | 容器宽度 |
| `height` | int | 容器高度 |
| `stackCount` | int | 实际填充的物品槽位数量 |
| `items` | IItemStack[][] | 工作台中的物品（二维） |
| `itemArray` | IItemStack[] | 工作台中的物品（一维） |

#### 方法

```zenscript
// 按索引获取物品（返回 IItemStack 或 null）
inventory.getStack(index);       // index: int

// 按索引设置物品（传 null 清空槽位）
inventory.setStack(index, item); // index: int, item: IItemStack

// 按行列获取物品（左上角为 0,0）
inventory.getStack(row, column); // row: int, column: int

// 按行列设置物品（传 null 清空槽位）
inventory.setStack(row, column, item); // row: int, column: int, item: IItemStack
```

### ICraftingInfo（合成信息）

> `import crafttweaker.recipes.ICraftingInfo;`

合成过程本身的信息对象。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `inventory` | ICraftingInventory | 合成所在的容器 |
| `player` | IPlayer | 执行合成的玩家 |
| `dimensionID` | int | 合成所在维度的 ID |
| `world` | IWorld | 合成所在的世界 |

---

## 添加有序合成

```zenscript
recipes.addShaped(recipeName, output, inputBox, @Optional recipeFunction, @Optional recipeAction);
recipes.addShapedMirrored(recipeName, output, inputBox, @Optional recipeFunction, @Optional recipeAction);  // 可翻转
recipes.addHiddenShaped(recipeName, output, inputBox, @Optional recipeFunction, @Optional recipeAction, @Optional mirrored);  // JEI 隐藏
```

- `recipeName`: string，配方 ID，不能重复。省略则自动生成哈希名
- `output`: IItemStack，输出物品
- `inputBox`: IIngredient[][]，二维数组，每行最多 3 个，最多 3 行
- `recipeFunction`: IRecipeFunction，可选，动态决定输出的函数（详见[配方函数](functions.md)）
- `recipeAction`: IRecipeAction，可选，合成完成后执行的函数（详见[配方函数](functions.md)）

`addHiddenShaped` 还支持一个可选的 `mirrored` boolean 参数，省略则为 false。

```zenscript
// 铁腿甲
recipes.addShaped("iron_leggings", <minecraft:iron_leggings>, [
    [<ore:ingotIron>, <ore:ingotIron>, <ore:ingotIron>],
    [<ore:ingotIron>, null, <ore:ingotIron>],
    [<ore:ingotIron>, null, <ore:ingotIron>]
]);

// 多输出数量
recipes.addShaped("sticks", <minecraft:stick> * 4, [
    [null, <ore:plankWood>],
    [null, <ore:plankWood>]
]);

// 可翻转（像弓）
recipes.addShapedMirrored("bow", <minecraft:bow>, [
    [null, <minecraft:stick>, <minecraft:string>],
    [<minecraft:stick>, null, <minecraft:string>],
    [null, <minecraft:stick>, <minecraft:string>]
]);
```

---

## 添加无序合成

```zenscript
recipes.addShapeless(recipeName, output, inputList, @Optional recipeFunction, @Optional recipeAction);
recipes.addHiddenShapeless(recipeName, output, inputList, @Optional recipeFunction, @Optional recipeAction);
```

- `inputList`: IIngredient[]，一维数组
- `recipeFunction`: IRecipeFunction，可选，动态决定输出的函数（详见[配方函数](functions.md)）
- `recipeAction`: IRecipeAction，可选，合成完成后执行的函数（详见[配方函数](functions.md)）

```zenscript
recipes.addShapeless("ender_eye", <minecraft:ender_eye>, [
    <minecraft:ender_pearl>, <minecraft:blaze_powder>
]);
```
