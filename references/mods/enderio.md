# EnderIO CraftTweaker API 参考

> Mod ID: `enderio`
> 前置条件: EnderTweaker
> 导入: `import mods.enderio.*;`

EnderIO 模组的 CraftTweaker 集成，支持修改合金冶炼炉、SAG 磨粉机、巫术酿造釜、灵魂绑定器、储罐、附魔台、燃烧发电机等机器的配方。

---

## API 列表

### AlloySmelter（合金炉）

> `import mods.enderio.AlloySmelter;`

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IItemStack output, IIngredient[] input, @Optional int energyCost, @Optional float xp)` | void | 添加配方。input 需 1~3 个材料；energyCost 默认 5000 FE；xp 不可为负 |
| `.removeRecipe(IItemStack output)` | void | 按输出移除配方 |
| `.removeByInputs(IItemStack... input)` | void | 按输入移除配方 |

---

### SagMill（SAG 磨粉机）

> `import mods.enderio.SagMill;`

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IItemStack[] output, float[] chances, IIngredient input, @Optional String bonusType, @Optional int energyCost, @Optional float[] xp)` | void | 添加配方。chances 长度须与 output 相同；bonusType 影响研磨球效果：`NONE`（无加成）、`MULTIPLY_OUTPUT`（概率可超 1.0）、`CHANCE_ONLY`（概率上限 1.0）；energyCost 默认 5000 FE |
| `.addRecipe(WeightedItemStack[] output, IIngredient input, @Optional String bonusType, @Optional int energyCost, @Optional float[] xp)` | void | 添加配方（使用 WeightedItemStack 形式） |
| `.removeRecipe(IItemStack input)` | void | 按输入移除配方 |

---

### Vat（酿液桶）

> `import mods.enderio.Vat;`

通过各槽位物品的乘数计算输入与输出的流体量。

- 每次合成消耗输入流体：`slot1Mult × slot2Mult × 1000 mb`

> ⚠️ 上述换算公式、以及 `slot1Mults` / `slot2Mults` 的具体语义，在官方 wiki 中均未说明（wiki 只写 "The multipliers for the items in slot 1"），需实测确认。

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(ILiquidStack output, ILiquidStack input, IIngredient[] slot1Solids, float[] slot1Mults, IIngredient[] slot2Solids, float[] slot2Mults, @Optional int energyCost)` | void | 添加配方。slot1Mults 长度须与 slot1Solids 相同；slot2Mults 长度须与 slot2Solids 相同；energyCost 默认 5000 FE |
| `.removeRecipe(ILiquidStack output)` | void | 按输出流体移除配方 |

---

### SliceNSplice（头颅装配机）

> `import mods.enderio.SliceNSplice;`

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IItemStack output, IIngredient[] input, @Optional int energyCost, @Optional float xp)` | void | 添加配方。input 需 1~6 个材料；energyCost 默认 5000 FE |
| `.removeRecipe(IItemStack output)` | void | 按输出移除配方 |

---

### SoulBinder（灵魂绑定器）

> `import mods.enderio.SoulBinder;`

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IItemStack output, IIngredient input, String[] entities, int xp, @Optional int energyCost)` | void | 添加配方。input 为非灵魂瓶主材料；entities 为允许的实体 ID 数组，需在灵魂瓶中存在；xp 为经验等级消耗；energyCost 默认 5000 FE |
| `.removeRecipe(IItemStack output)` | void | 按输出移除配方 |

---

### Enchanter（附魔器）

> `import mods.enderio.Enchanter;`

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IEnchantmentDefinition output, IIngredient input, int amountPerLevel, double costMultiplier)` | void | 添加配方。amountPerLevel 为每级附魔所需输入物品数；costMultiplier 用于调整配方花费 |
| `.removeRecipe(IEnchantmentDefinition output)` | void | 按输出附魔移除配方 |

---

### CombustionGen（燃烧发电机）

> `import mods.enderio.CombustionGen;`

燃烧发电机不使用配方系统，而是通过燃料和冷却液决定能量产出。

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addFuel(ILiquidStack fuel, int powerPerCycleRF, int totalBurnTime)` | void | 添加燃料。powerPerCycleRF 为基准机器每 tick 产能；totalBurnTime 为一桶燃料的总燃烧时间 |
| `.addCoolant(ILiquidStack coolant, float degreesCoolingPerMB)` | void | 添加冷却液。degreesCoolingPerMB 为每 mb 冷却液吸收的热量（升温 1K 所需） |
| `.removeFuel(ILiquidStack fuel)` | void | 移除燃料 |
| `.removeCoolant(ILiquidStack coolant)` | void | 移除冷却液 |

---

### Tank（储罐）

> `import mods.enderio.Tank;`

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(boolean fill, IIngredient input, ILiquidStack fluid, IItemStack output)` | void | 添加配方。fill 为 true 时消耗流体（填充），为 false 时产生流体（排空） |
| `.removeRecipe(boolean fill, ILiquidStack fluid, IItemStack output)` | void | 按参数移除配方 |

---

## 注意事项

- 所有机器默认能量消耗为 5000 FE（@Optional 参数）
- xp 参数不可为负数
- SAG 磨粉机的 bonusType 影响研磨球行为：`NONE` 无加成、`MULTIPLY_OUTPUT` 允许概率超 100%、`CHANCE_ONLY` 概率上限 100%
- 酿液桶的输出/输入流体数量参数被忽略，实际量由乘数系统计算
- 灵魂绑定器需要实体以灵魂瓶形式存在
- 燃烧发电机不是配方系统，使用独立的燃料和冷却液管理
