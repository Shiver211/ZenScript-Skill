# 配方原料 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.item.IIngredient;`、`import crafttweaker.item.IWeightedIngredient;`、`import crafttweaker.item.WeightedItemStack;`

IIngredient、IWeightedIngredient、WeightedItemStack 配方原料 API。

---

## API 列表

### IIngredient（配方原料接口）

> `import crafttweaker.item.IIngredient;`

IItemStack、IOreDictEntry、ILiquidStack 都实现了此接口。

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.mark(string)` | IIngredient | 标记（用于配方函数） |
| `.marked(string)` | IIngredient | 标记（同上） |
| `.amount` | int | 获取数量 |
| `.items` | List\<IItemStack\> | 匹配的物品列表 |
| `.itemArray` | IItemStack[] | 匹配的物品数组 |
| `.liquids` | List\<ILiquidStack\> | 匹配的流体列表 |
| `.commandString` | string | 命令字符串 |
| `.transform(IItemStack function(IItemStack, IPlayer))` | IIngredient | 自定义转换 |
| `.transformNew(IItemStack function(IItemStack))` | IIngredient | 自定义转换（新物品，只接收 item 参数） |
| `.only(IItemStack function(IItemStack))` | IIngredient | 自定义条件（只接收 item 参数） |
| `.matches(IItemStack)` | bool | 是否匹配 |
| `.matchesExact(IItemStack)` | bool | 是否精确匹配 |
| `.matches(ILiquidStack)` | bool | 是否匹配流体 |
| `ingredient has item` | bool | 是否包含 |
| `ingredient | other` | IIngredient | 或运算 |
| `.or(IIngredient)` | IIngredient | 或运算 |
| `.applyTransform(IItemStack, IPlayer)` | IItemStack | 应用物品转换器，返回转换后的物品堆叠 |
| `.hasTransformers()` | bool | 是否绑定了物品转换器 |

### IWeightedIngredient / WeightedItemStack（带权重的物品）

> `import crafttweaker.item.IWeightedIngredient;`
> `import crafttweaker.item.WeightedItemStack;`

带百分比权重的物品，用于概率性掉落或副产物输出。WeightedItemStack 实现了 IWeightedIngredient 接口。

通过 `%` 运算符或 `.weight()` 方法从 IItemStack 创建：

```zenscript
val wItem = <minecraft:diamond> % 20;      // 20% 概率
val wItem2 = <minecraft:diamond>.weight(0.2); // 同上（0.2 = 20%）
```

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `stack` | IItemStack | 关联的物品堆叠 |
| `ingredient` | IIngredient | 关联的配方原料 |
| `chance` | float | 小数形式概率（如 0.2） |
| `percent` | float | 百分比形式概率（如 20.0） |

---

## 物品条件（Item Conditions）

附加到物品上，用于配方的特殊需求。

> **注意：** 使用耐久相关条件时，先加 meta 通配符禁用默认精确耐久匹配：
> `<minecraft:iron_pickaxe:*>.onlyDamaged()`

| 条件 | 说明 |
|------|------|
| `.anyDamage()` | 耐久值不影响合成 |
| `.onlyDamaged()` | 只有有损耗的物品才能参与 |
| `.onlyDamageAtLeast(int)` | 耐久不小于指定值 |
| `.onlyDamageAtMost(int)` | 耐久不大于指定值 |
| `.onlyDamageBetween(int, int)` | 耐久在两者之间 |
| `.withTag(IData)` | 需要带有指定 NBT |
| `.onlyWithTag(IData)` | 需要**只**带有指定 NBT |
| `.withDamage(int)` | 输出带有指定耐久 |
| `.only(function)` | 自定义条件函数 |
| `.onlyStack(int)` | 物品堆叠数量至少为指定值 |

```zenscript
// 有损耗的铁镐才能参与合成
<minecraft:iron_pickaxe:*>.onlyDamaged().withTag({display: {Lore: ["损坏的铁镐"]}});

// 自定义条件：只在下界生效
recipes.addShapeless("nether_recipe", <minecraft:netherrack>,
    [<ore:cobblestone>, <ore:cobblestone>, <ore:cobblestone>],
    function(output, ins, info) {
        return info.player.world.dimension == -1 ? output : null;
    },
null);
```

---

## 物品转换器（Item Transformers）

用于合成后物品不消耗、转换为其他物品等场景。

| 转换器 | 说明 |
|--------|------|
| `.reuse()` | 物品不消耗，留在工作台 |
| `.giveBack()` | 物品不消耗，整组回到物品栏 |
| `.giveBack(IItemStack)` | 物品被消耗，但返回指定物品到物品栏 |
| `.transformReplace(IItemStack)` | 合成后变为指定物品放入工作台槽位 |
| `.transformDamage()` | 合成后掉 1 点耐久 |
| `.transformDamage(int)` | 合成后掉指定点耐久 |
| `.noReturn()` | 强制消耗（即使有容器物品） |
| `.transformConsume(int)` | 消耗指定个数 |
| `.transform(function(IItemStack, IPlayer))` | 自定义转换函数（返回物品替换槽位内容，null 清空槽位，可能在 1.13 移除） |
| `.transformNew(function(IItemStack))` | 新版自定义转换函数（不需要 player 参数时优先使用） |

```zenscript
// 石斧 + 木板 = 3 木棍，石斧掉 1 耐久
recipes.addShapeless(<minecraft:stick> * 3, [
    <minecraft:stone_axe>.transformDamage(), <ore:plankWood>
]);

// 牛奶桶合成蛋糕后变空桶（自动，无需手动转换）
// 如需阻止返回空桶：用 .noReturn()

// giveBack(IItemStack)：物品被消耗但返回指定物品
<minecraft:bucket>.giveBack(<minecraft:stick>)

// transform：自定义转换（旧版，接收 item 和 player）
item.transform(function(item, player) { return item; });

// transformNew：自定义转换（新版，只接收 item）
item.transformNew(function(item) { return item; });
```

---

## Roids-Tweaker 扩展

### IIngredient 扩展

可在任何 IIngredient 上调用。

| 方法 | 返回 | 说明 |
|------|------|------|
| `.spread()` | IIngredient[] | 返回长度为 this.count 的 IIngredient[]，每个元素为 this.setCount(1) |

### IItemStack 扩展

可在任何 IItemStack 上调用。

| 方法 | 返回 | 说明 |
|------|------|------|
| `.asData()` | IData | 将物品序列化为 NBT 形式（也是 ZenCaster） |
| `IItemStack.fromData(IData data)` | IItemStack | 从 NBT 反序列化（静态方法） |
| `.isEnergyStorage()` | boolean | 是否可存储能量 |
| `.getEnergy()` | IEnergyStorage | 获取能量存储接口。非能量容器会报错 |

---

## RandomTweaker 扩展（需安装 RandomTweaker）

> `import mods.randomtweaker.vanilla.IItemStack;`

IItemStack 的扩展类，IItemStack 实例可直接使用这些方法。由于类名与 CrT 自带的 IItemStack 重合，如需用类名调用需使用 `as` 重定向：`import mods.randomtweaker.vanilla.IItemStack as IItemStackExpansion;`

### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getTagSize()` | int | 返回物品 NBT 中有多少个 key（空 NBT `{}` 返回 0）
