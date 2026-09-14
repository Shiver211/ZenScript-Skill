# ContentTweaker CraftTweaker API 参考

> Mod ID: `contenttweaker`
> 前置条件: CraftTweaker
> 导入: `import mods.contenttweaker.*;`

ContentTweaker (CoT) 允许用 ZenScript 为游戏添加自定义物品、方块、流体、创造标签等。

**重要**: CoT 脚本第一行必须为 `#loader contenttweaker`。

---

## 指令

| 指令 | 用途 |
|------|------|
| `/ct blockmaterial` | 打印所有 Block Material 至日志 |
| `/ct creativetab` | 打印所有创造标签至日志 |
| `/ct soundevent` | 打印所有 SoundEvent 至日志 |
| `/ct soundtype` | 打印所有 SoundType 至日志 |
| `/ct parts` | 打印所有已注册的材料部件至日志 |

---

## API 列表

### MaterialSystem（材料系统）

> `import mods.contenttweaker.MaterialSystem;`

允许批量添加金属的不同部件（齿轮、板、锭等）。

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getMaterialBuilder()` | MaterialBuilder | 获取材料构建器 |
| `.getPartBuilder()` | PartBuilder | 获取部件构建器 |
| `.getPartType(string name)` | PartType | 获取部件类型 |

### MaterialBuilder（材料构建器）

> `import mods.contenttweaker.MaterialBuilder;`

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setName(string name)` | MaterialBuilder | 设置材料 ID |
| `.setColor(int color)` | MaterialBuilder | 设置 RGB 颜色 |
| `.setHasEffect(bool hasEffect)` | MaterialBuilder | 设置是否有附魔光芒 |
| `.build()` | Material | 构建 Material 对象 |

### Material（材料）

> 由 `MaterialBuilder.build()` 获取。

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.registerParts(string[] parts)` | MaterialPart[] | 注册多个部件，返回 MaterialPart 列表 |
| `.registerParts(IPart[] parts)` | MaterialPart[] | 注册多个部件（IPart 数组重载） |
| `.registerPart(string part)` | MaterialPart | 注册单个部件，返回 MaterialPart（可链式调用 `.getData()`） |

### PartBuilder（部件构建器）

> `import mods.contenttweaker.PartBuilder;`

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setName(string name)` | PartBuilder | 设置部件 ID |
| `.setPartType(PartType type)` | PartBuilder | 设置部件类型 |
| `.setOreDictName(string name)` | PartBuilder | 设置矿辞前缀 |
| `.setHasOverlay(bool hasOverlay)` | PartBuilder | 设置是否有覆盖层 |
| `.build()` | Part | 构建 Part 对象 |

### 预设部件类型

**物品**: beam, bolt, casing, clump, crystal, crushed_ore, dense_plate, dirty_dust, dust, gear, ingot, nugget, plate, rod, shard

**方块**: block

**矿石**: ore, dense_ore, poor_ore

**流体**: molten

**护甲**: armor (head, chest, legs, feet)

### MaterialPartData（材料部件数据）

> `import mods.contenttweaker.MaterialPartData;`

由 **MaterialPart** 的 `.getData()` 获取，例如 `metal.registerPart("ore").getData()`。

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addDataValue(string name, string value)` | void | 添加数据字段 |
| `.getIntValue(string name, int defaultValue)` | int | 读取整数字段，字段不存在时返回 defaultValue |
| `.getStringValue(string name, string defaultValue)` | string | 读取字符串字段，字段不存在时返回 defaultValue |

**物品字段**: burn (燃烧时间)

**盔甲字段**: durability, enchantability, reduction, toughness

**流体字段**: temperature, density, luminosity, viscosity, vaporize

**矿石字段**: drops, variants, hardness, resistance, harvestLevel, harvestTool

---

## 使用示例

### 批量创建材料部件

```zenscript
#loader contenttweaker
import mods.contenttweaker.MaterialSystem;

val copper as Material = MaterialSystem.getMaterialBuilder().setName("Copper").setColor(0xFF9933).build();
copper.registerParts(["gear", "casing", "rod"]); // 同时注册铜齿轮、铜外壳、铜杆

// 自定义部件
val denseIngotPart = MaterialSystem.getPartBuilder()
    .setName("dense_ingot")
    .setPartType(MaterialSystem.getPartType("item"))
    .setOreDictName("denseIngot")
    .build();
copper.registerPart(denseIngotPart); // 注册致密铜锭
```
---

## ChickenFactory（鸡工厂）

> `import mods.contenttweaker.ChickenFactory;`

**注意**: 需要安装 Chickens 模组。

### 创建鸡

```zenscript
ChickenFactory.createChicken(name as string, color as CTColor, item as IItemStack);
```

**参数说明**:
- `name`: 鸡的实体名称
- `color`: 鸡的颜色
- `item`: 鸡下的蛋

### ChickenRepresentation（鸡模板）

> `import mods.contenttweaker.Chicken;`

#### @ZenGetter / @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `name` | string | 鸡的名称 |
| `layItem` | IItemStack | 下的蛋 |
| `dropItem` | IItemStack | 掉落物 |
| `backgroundColor` | CTColor | 背景颜色 |
| `foregroundColor` | CTColor | 前景颜色 |
| `textureLocation` | CTResourceLocation | 纹理位置 |
| `spawnType` | string | 生成类型 |
| `layCoefficient` | float | 下蛋系数 |
| `parentOne` | CTResourceLocation | 父亲1 |
| `parentTwo` | CTResourceLocation | 父亲2 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.register()` | void | 注册鸡 |

---

## DropTableBuilder（掉落表构建器）

> `import mods.contenttweaker.DropTableBuilder;`

用于创建矿石和样本的掉落表。

### 创建掉落表

```zenscript
var table = DropTableBuilder.newSlot()
    .addItem("minecraft:diamond", 1, 2)  // 钻石，权重1，数量2
    .addItem("minecraft:coal", 9)         // 煤炭，权重9，数量1
    .enableFortune()                      // 启用时运
    .newSlot()                            // 新槽
    .addItem("oredict:stone")             // 矿辞
    .newSlot()                            // 新槽
    .addItem("minecraft:cobblestone")
    .addItem("empty");                    // 空
```

### 使用掉落表

```zenscript
var oreData = MaterialSystem.getMaterialBuilder()
    .setColor(12345678).setName("Fake Lapis").build()
    .registerPart("ore").getData();
oreData.addDataValue("drops", table);
```