# AbyssalCraft CraftTweaker API 参考

> Mod ID: `abyssalcraft`
> 前置条件: AbyssalCraft Integration

## API 列表

### Transmutator（嬗变器）

> `import mods.abyssalcraft.Transmutator;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addTransmutation(IItemStack input, IItemStack output, float experience)` | void | 添加嬗变配方，`experience` 为给予的经验 |
| `.removeTransmutationInput(IItemStack inputOrOutput)` | void | 移除配方（按输入或输出物品匹配） |
| `.addFuel(IItemStack fuel, int burnTime)` | void | 添加燃料，`burnTime` 为燃烧时间（tick） |
| `.removeFuel(IItemStack fuel)` | void | 移除燃料 |
| `.removeAll()` | void | 移除所有嬗变配方 |

---

### Crystallizer（结晶器）

> `import mods.abyssalcraft.Crystallizer;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addSingleCrystallization(IItemStack input, IItemStack output, float experience)` | void | 添加单输出结晶配方 |
| `.addCrystallization(IItemStack input, IItemStack output1, IItemStack output2, float experience)` | void | 添加双输出结晶配方 |
| `.removeCrystallizationInput(IItemStack inputOrOutput)` | void | 移除配方（按输入或输出物品匹配） |
| `.addFuel(IItemStack fuel, int burnTime)` | void | 添加燃料，`burnTime` 为燃烧时间（tick） |
| `.removeFuel(IItemStack fuel)` | void | 移除燃料 |
| `.removeAll()` | void | 移除所有结晶配方 |

---

### CreationRitual（创造仪式）

> `import mods.abyssalcraft.CreationRitual;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRitual(string unlocalizedName, int bookType, int dimension, float requiredEnergy, bool livingSacrifice, IItemStack output, IItemStack[] offerings, IData nbt*)` | void | 添加创造仪式。`unlocalizedName` 仪式名称（本地化格式 `ac.ritual.{name}.desc`）；`bookType` 书等级（0=死灵之书, 1=深渊荒原, 2=恐惧之地, 3=奥穆索, 4=深渊之书）；`dimension` 维度 ID（-1=所有维度）；`requiredEnergy` 需要能量；`livingSacrifice` 是否需要活祭品；`offerings` 输入物品（最多8个） |
| `.removeRitual(IItemStack output)` | void | 移除输出物品匹配的仪式 |
| `.removeAll()` | void | 移除所有创造仪式 |

---

### InfusionRitual（合成仪式）

> `import mods.abyssalcraft.InfusionRitual;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRitual(string unlocalizedName, int bookType, int dimension, float requiredEnergy, bool livingSacrifice, IItemStack output, IItemStack sacrifice, IItemStack[] offerings, IData nbt*, string[] tags*)` | void | 添加合成仪式。`unlocalizedName` 仪式名称；`bookType` 书等级（0=死灵之书, 1=深渊荒原, 2=恐惧之地, 3=奥穆索, 4=深渊之书）；`dimension` 维度 ID（-1=所有维度）；`requiredEnergy` 需要能量；`livingSacrifice` 是否需要活祭品；`sacrifice` 祭坛中央物品；`offerings` 输入物品（最多8个） |
| `.removeRitual(IItemStack output)` | void | 移除输出物品匹配的仪式 |
| `.removeAll()` | void | 移除所有合成仪式 |

## 使用示例


```

### 创造仪式

```zenscript
import mods.abyssalcraft.CreationRitual;

// 创建仪式：8块煤炭 + 1000能量 → 钻石，任意书，不需要祭品，任意维度
mods.abyssalcraft.CreationRitual.addRitual("creationRitualTest", 0, -1, 1000, false, <minecraft:diamond>, [<minecraft:coal>, <minecraft:coal>, <minecraft:coal>, <minecraft:coal>, <minecraft:coal>, <minecraft:coal>, <minecraft:coal>, <minecraft:coal>]);
```

### 合成仪式

```zenscript
import mods.abyssalcraft.InfusionRitual;

// 创建仪式：8块煤炭 + 泥土（祭坛中央）+ 1000能量 → 钻石
mods.abyssalcraft.InfusionRitual.addRitual("creationRitualTest", 0, -1, 1000, false, <minecraft:diamond>, <minecraft:dirt>, [<minecraft:coal>, <minecraft:coal>, <minecraft:coal>, <minecraft:coal>, <minecraft:coal>, <minecraft:coal>, <minecraft:coal>, <minecraft:coal>], false);
```

## 注意事项

- `offerings` 参数最多支持 8 个输入物品
- 本地化格式：`ac.ritual.{unlocalizedName}.desc`，不建议使用中文
