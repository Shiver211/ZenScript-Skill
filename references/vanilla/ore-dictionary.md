# Ore Dictionary CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.oredict.IOreDict;`、`import crafttweaker.oredict.IOreDictEntry;`、`import crafttweaker.oredict.WeightedOreDictEntry;`

矿物词典 API，用于管理矿辞和物品分组。

---

## API 列表

### IOreDict（矿物词典）

> `import crafttweaker.oredict.IOreDict;`

通过 `oreDict` 全局关键字访问。

#### 获取 IOreDictEntry

```zenscript
oreDict.ingotIron;         // 点号语法
oreDict.get("ingotIron");  // 方法调用
oreDict["ingotIron"];      // 索引语法
// 如果不存在则自动创建
```

#### 检查矿辞是否存在

```zenscript
if (oreDict in "ingotIron") { ... }
if (oreDict has "ingotIron") { ... }
if (oreDict.contains("ingotIron")) { ... }
```

#### 遍历所有矿辞

```zenscript
for entry in oreDict.entries { print(entry.name); }
// 或直接遍历
for entry in oreDict { print(entry.name); }
```

### IOreDictEntry（矿辞条目）

> `import crafttweaker.oredict.IOreDictEntry;`

通过 `<ore:ingotIron>` 获取。IOreDictEntry 实现了 IIngredient 接口。

> 若尖括号里带 `*`（如 `<ore:ingotIron:*>`），返回的是 **`List<IOreDictEntry>`** 而不是单个条目。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `name` | string | 矿辞名称 |
| `items` | List\<IItemStack\> | 包含的物品列表 |
| `itemArray` | IItemStack[] | 包含的物品数组 |
| `firstItem` | IItemStack | 第一个物品 |
| `empty` | bool | 是否为空 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.add(IItemStack...)` | void | 添加一个或多个物品到矿辞 |
| `.addItems(IItemStack[])` | void | 添加物品数组到矿辞 |
| `.remove(IItemStack...)` | void | 从矿辞移除一个或多个物品 |
| `.removeItems(IItemStack[])` | void | 从矿辞移除物品数组 |
| `.addAll(IOreDictEntry)` | void | 将另一个矿辞的所有物品加入 |
| `.mirror(IOreDictEntry)` | void | 映射（当前矿辞的物品替换目标矿辞的所有物品） |
| `.matches(IItemStack)` | bool | 是否匹配 |
| `.has(IItemStack)` | bool | 是否包含 |

### WeightedOreDictEntry（带权重的矿辞条目）

> `import crafttweaker.oredict.WeightedOreDictEntry;`

WeightedOreDictEntry 是带有百分比权重的 IOreDictEntry，用于基于概率的操作（如掉落）。

#### 创建

```zenscript
val oreDictEntry = <ore:ingotGold>;
// 使用 % 运算符或 .weight() 方法
val weighted = oreDictEntry % 20;        // 20% 概率
val weighted2 = oreDictEntry.weight(0.2); // 0.2 = 20%
```

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `entry` | IOreDictEntry | 关联的矿辞条目 |
| `chance` | float | 概率（小数形式，如 0.2） |
| `percent` | float | 概率（百分比形式，如 20.0） |

---

## 使用示例

### 矿物词典操作

```zenscript
// 添加物品到矿辞（不存在则创建）
<ore:ingotCopper>.add(<thermalfoundation:material:128>);

// 从矿辞移除
<ore:sand>.remove(<minecraft:sand>);

// 将一个矿辞的所有物品加入另一个
<ore:ingotMyMetal>.addAll(<ore:ingotIron>);

// 映射：A 的物品全部替换 B 的物品
<ore:ingotA>.mirror(<ore:ingotB>);
```
