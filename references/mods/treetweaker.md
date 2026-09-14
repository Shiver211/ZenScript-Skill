# TreeTweaker CraftTweaker API 参考

> Mod ID: `treetweaker`
> 前置条件: treetweaker
> 导入: `import mods.treetweaker.TreeFactory;`

TreeTweaker 允许通过脚本自定义树木类型并添加到世界生成中。

**重要**: 脚本第一行必须为 `#loader preinit`。

---

## API 列表

### TreeFactory（树木工厂）

> `import mods.treetweaker.TreeFactory;`

#### 工厂方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `TreeFactory.createTree(string name)` | Tree | 创建新树木。`name` 用于错误日志和树苗命名，应全小写 |

---

### Tree（树木对象）

由 `TreeFactory.createTree()` 创建。支持 setter 方法和直接字段赋值两种方式。

#### 属性

| 属性 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| `setTreeType(string)` | string | 是 | `"OAK"` | 树木形状 |
| `setLog(string)` | string | 否 | `"minecraft:log"` | 树干方块（支持 `:metadata` 格式） |
| `setLeaf(string)` | string | 否 | `"minecraft:leaves"` | 树叶方块（支持 `:metadata` 格式） |
| `setMinHeight(int)` | int | 否 | 5 | 最小高度 |
| `setExtraHeight(int)` | int | 否 | 3 | 随机额外高度 |
| `setGenFrequency(int)` | int | 否 | 5 | 生成频率（1/N 概率成功生成） |
| `setGenAttempts(int)` | int | 否 | 1 | 每次成功生成时尝试生成的树木数量（可用于生成树群） |
| `extraThick` | boolean | 否 | false | 树干变为 2×2 粗。仅 LARGE_OAK、PINE、CANOPY、SPRUCE 有效 |
| `setGenBiome(string)` | string | 否 | null | 指定生成生物群系（忽略原版规则）。留空则在所有含树木的生物群系生成 |
| `setGenBiomeByTag(string)` | string | 否 | null | 按标签指定生物群系（HOT、SWAMP、SNOWY、MOUNTAIN 等）。仅在 setGenBiome 为空时生效 |
| `setBaseBlock(string)` | string | 否 | null | 树木生成的基底方块。留空则使用原版默认（草方块、泥土、耕地） |
| `setDimWhitelist(int/int[])` | int 或 int[] | 否 | null | 允许生成的维度 ID。null 则不限维度 |
| `addSapling()` | — | 否 | — | 添加同名树苗（需自行提供材质） |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.register()` | 未说明 | 注册树木以加入世界生成（必须调用） |

---

### 可用树木类型

| 类型 | 说明 |
|------|------|
| `OAK` | 橡树 |
| `LARGE_OAK` | 大型橡树（丛林木） |
| `JUNGLE` | 丛林木 |
| `CANOPY` | 树冠树 |
| `LARGE_CANOPY` | 大型树冠树 |
| `PINE` | 松树 |
| `LARGE_PINE` | 大型松树 |
| `SPRUCE` | 云杉 |
| `LARGE_SPRUCE` | 大型云杉 |
| `ACACIA` | 金合欢 |
| `BIRCH` | 白桦 |
| `DARK_OAK` | 深色橡木 |
| `RED_MUSHROOM` | 红蘑菇 |
| `BROWN_MUSHROOM` | 棕蘑菇 |
| `BRAIDED` | 螺旋树 |
| `PALM` | 棕榈树 |
| `WILLOW` | 柳树 |

---

## 使用示例

### 基础自定义树木

```zenscript
#loader preinit
import mods.treetweaker.TreeFactory;

var tree = TreeFactory.createTree("wool_tree");
tree.setTreeType("SPRUCE");
tree.setLog("minecraft:wool:3");
tree.setLeaf("minecraft:melon_block");
tree.register();
```

### 沙漠云杉（2×2 粗、限定生物群系和基底）

```zenscript
var deserttree = TreeFactory.createTree("desert_spruce");
deserttree.setTreeType("SPRUCE");
deserttree.setLeaf("minecraft:black_glazed_terracotta");
deserttree.setLog("minecraft:red_sandstone");
deserttree.setGenFrequency(4);
deserttree.setMinHeight(9);
deserttree.setExtraHeight(9);
deserttree.extraThick = true;
deserttree.setGenBiome("minecraft:desert");
deserttree.setBaseBlock("minecraft:sand");
deserttree.register();
```

### 树群生成

```zenscript
var cluster = TreeFactory.createTree("slimy_cluster");
cluster.setTreeType("ACACIA");
cluster.setLog("minecraft:sponge");
cluster.setLeaf("minecraft:slime");
cluster.setMinHeight(5);
cluster.setExtraHeight(7);
cluster.setGenFrequency(8);
cluster.setGenAttempts(5); // 每次尝试生成 5 棵，形成树群
cluster.register();
```

### 下界蘑菇（限定维度）

```zenscript
var nether = TreeFactory.createTree("quartz_mushroom");
nether.setTreeType("RED_MUSHROOM");
nether.setLeaf("minecraft:quartz_block");
nether.setLog("minecraft:obsidian");
nether.setMinHeight(6);
nether.setExtraHeight(5);
nether.setBaseBlock("minecraft:netherrack");
nether.setGenBiome("minecraft:hell");
nether.setDimWhitelist(-1);
nether.register();
```

### 带树苗的柳树

```zenscript
var willow = TreeFactory.createTree("willow");
willow.setTreeType("WILLOW");
willow.setLog("minecraft:log2:1");
willow.setLeaf("minecraft:leaves2:1");
willow.setMinHeight(4);
willow.addSapling(); // 添加同名树苗
willow.register();
```
