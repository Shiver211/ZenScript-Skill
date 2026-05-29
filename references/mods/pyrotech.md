# Pyrotech CraftTweaker API 参考

> Mod ID: `pyrotech`
> 前置条件: 无

## API 列表

### Barrel（木桶）

> `import mods.pyrotech.Barrel;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, ILiquidStack outputFluid, ILiquidStack inputFluid, IIngredient[] inputItems, int timeTicks)` | void | 添加配方 |
| `.removeRecipes(ILiquidStack output)` | void | 移除匹配输出流体的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Bellows（风箱）

> `import mods.pyrotech.Bellows;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Bloomery（锻造炉）

> `import mods.pyrotech.Bloomery;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.removeAllBloomeryRecipes()` | void | 移除所有炼铁炉配方 |
| `.removeAllWitherForgeRecipes()` | void | 移除所有凋灵熔炉配方 |
| `.removeBloomeryRecipes(IIngredient output)` | void | 移除指定输出的炼铁炉配方 |
| `.removeWitherForgeRecipes(IIngredient output)` | void | 移除指定输出的凋灵熔炉配方 |
| `.createBloomeryBuilder(string name, IItemStack output, IIngredient input, @Optional boolean inherited)` | Bloomery | 创建炼铁炉配方构建器 |
| `.createWitherForgeBuilder(string name, IItemStack output, IIngredient input)` | Bloomery | 创建凋灵熔炉配方构建器 |
| `.addBloomeryFuelModifier(IIngredient fuel, double modifier)` | void | 设置炼铁炉燃料效率修正（1.0=100%） |
| `.addWitherForgeFuelModifier(IIngredient fuel, double modifier)` | void | 设置凋灵熔炉燃料效率修正 |
| `.setBloomGameStages(Stages stages)` | void | 设置使用铁锭所需的游戏阶段 |
| `.setBloomeryGameStages(Stages stages)` | void | 设置使用炼铁炉所需的游戏阶段 |
| `.setWitherForgeGameStages(Stages stages)` | void | 设置使用凋灵熔炉所需的游戏阶段 |

#### Bloomery 构建器方法
| 方法 | 返回 | 说明 |
|------|------|------|
| `.setBurnTimeTicks(int burnTimeTicks)` | Bloomery | 设置基础冶炼时间（tick） |
| `.setExperience(float experience)` | Bloomery | 设置锤击产物经验值 |
| `.setFailureChance(float failureChance)` | Bloomery | 设置失败概率（0.0-1.0） |
| `.setBloomYield(int min, int max)` | Bloomery | 设置产物数量范围 |
| `.setSlagItem(IItemStack slagItem, int slagCount)` | Bloomery | 设置矿渣物品及生成量 |
| `.addFailureItem(IItemStack itemStack, int weight)` | Bloomery | 添加失败产物（加权） |
| `.setLangKey(string langKey)` | Bloomery | 设置语言本地化键 |
| `.setAnvilTiers(string[] tiers)` | Bloomery | 设置有效砧类型（"granite", "ironclad"） |
| `.register()` | void | 注册配方 |

---

### Brick Crucible（耐火坩埚）

> `import mods.pyrotech.BrickCrucible;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, ILiquidStack output, IIngredient input, int burnTimeTicks)` | void | 添加配方 |
| `.removeRecipes(ILiquidStack output)` | void | 移除匹配输出流体的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Brick Faucet（耐火龙头）

> `import mods.pyrotech.FaucetBrick;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Brick Kiln（耐火窑）

> `import mods.pyrotech.BrickKiln;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, int burnTimeTicks)` | void | 添加简单配方 |
| `.addRecipe(string name, IItemStack output, IIngredient input, int burnTimeTicks, float failureChance, IItemStack[] failureItems)` | void | 添加带失败概率的配方 |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Brick Oven（耐火炉）

> `import mods.pyrotech.BrickOven;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input)` | void | 添加基础配方 |
| `.blacklistSmeltingRecipes(IIngredient[] output)` | void | 黑名单特定熔炼配方 |
| `.blacklistAllSmeltingRecipes()` | void | 黑名单所有熔炼配方 |
| `.whitelistSmeltingRecipes(IIngredient[] output)` | void | 白名单特定熔炼配方 |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Brick Sawmill（耐火切割机）

> `import mods.pyrotech.BrickSawmill;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, int burnTimeTicks, IIngredient blade, @Optional int woodChips)` | void | 添加锯木配方，`blade` 为锯片（使用 `:*` 匹配任意耐久） |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Brick Tank（耐火缸）

> `import mods.pyrotech.BrickTank;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Burn（燃烧工艺）

> `import mods.pyrotech.Burn;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.createBuilder(string name, IItemStack output, string blockString)` | Burn | 创建燃烧配方构建器 |

#### Burn 构建器方法
| 方法 | 返回 | 说明 |
|------|------|------|
| `.setBurnStages(int burnStages)` | Burn | 设置燃烧阶段数 |
| `.setTotalBurnTimeTicks(int totalBurnTimeTicks)` | Burn | 设置总燃烧时间（tick） |
| `.setFluidProduced(ILiquidStack fluidProduced)` | Burn | 设置产生的流体 |
| `.setFailureChance(float failureChance)` | Burn | 设置失败概率（0.0-1.0） |
| `.addFailureItem(IItemStack failureItem)` | Burn | 添加失败产物 |
| `.setRequiresRefractoryBlocks(boolean requiresRefractoryBlocks)` | Burn | 设置是否需要耐火砖块 |
| `.setFluidLevelAffectsFailureChance(boolean fluidLevelAffectsFailureChance)` | Burn | 设置流体水平是否影响失败概率 |
| `.register()` | void | 注册配方 |

---

### Butcher's Block（屠夫桌）

> `import mods.pyrotech.ButchersBlock;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Campfire（篝火）

> `import mods.pyrotech.Campfire;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, @Optional int ticks)` | void | 添加基础配方，`ticks` 为烹饪时间 |
| `.blacklistSmeltingRecipes(IIngredient[] output)` | void | 黑名单特定熔炼配方 |
| `.blacklistAllSmeltingRecipes()` | void | 黑名单所有熔炼配方 |
| `.whitelistSmeltingRecipes(IIngredient[] output)` | void | 白名单特定熔炼配方 |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.whitelistFuel(IIngredient fuel)` | void | 白名单燃料 |
| `.blacklistFuel(IIngredient fuel)` | void | 黑名单燃料 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Carcass（动物尸体处理）

> `import mods.pyrotech.Carcass;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Chopping Block（砧板）

> `import mods.pyrotech.Chopping;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, @Optional boolean inherited)` | void | 添加基础配方 |
| `.addRecipe(string name, IItemStack output, IIngredient input, int[] chops, int[] quantities, @Optional boolean inherited)` | void | 添加自定义砍伐配方，`chops` 为砍伐次数数组，`quantities` 为产出数量数组 |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Compacting Bin（挤压桶）

> `import mods.pyrotech.CompactingBin;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, int amount, @Optional boolean inherited)` | void | 添加基础压缩配方，`amount` 为所需输入数量 |
| `.addRecipe(string name, IItemStack output, IIngredient input, int amount, int[] toolUsesRequired, @Optional boolean inherited)` | void | 添加自定义压缩配方 |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Compost Bin（堆肥桶）

> `import mods.pyrotech.CompostBin;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.removeRecipeByInput(IIngredient input)` | void | 通过输入物品移除配方 |
| `.removeRecipesByOutput(IIngredient output)` | void | 通过输出物品移除配方 |
| `.addRecipe(IIngredient output, IItemStack input)` | void | 添加基础堆肥配方 |
| `.addRecipe(IIngredient output, IItemStack input, int compostValue)` | void | 添加自定义堆肥配方，`compostValue` 为堆肥值[1-16] |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Crate（储物箱）

> `import mods.pyrotech.Crate;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Crude Drying Rack（简陋晾干架）

> `import mods.pyrotech.CrudeDryingRack;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, int dryTimeTicks, @Optional boolean inherited)` | void | 添加晾晒配方 |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |
| `.setBiomeSpeed(float speed, string biome)` | void | 设置单个生物群系的晾晒速度 |
| `.setBiomeSpeed(float speed, string[] biomes)` | void | 设置多个生物群系的晾晒速度 |

---

### Drying Rack（晾干架）

> `import mods.pyrotech.DryingRack;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, int dryTimeTicks, @Optional boolean inherited)` | void | 添加晾晒配方 |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |
| `.setBiomeSpeed(float speed, string biome)` | void | 设置单个生物群系的晾晒速度 |
| `.setBiomeSpeed(float speed, string[] biomes)` | void | 设置多个生物群系的晾晒速度 |

---

### Durable Crate（耐用储物箱）

> `import mods.pyrotech.DurableCrate;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Durable Rock Bag（耐用采石包）

> `import mods.pyrotech.DurableRockBag;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Durable Shelf（耐用架子）

> `import mods.pyrotech.DurableShelf;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Durable Stash（耐用储物盒）

> `import mods.pyrotech.DurableStash;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Granite Anvil（花岗岩砧）

> `import mods.pyrotech.GraniteAnvil;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, int hits, string type, @Optional boolean inherited)` | void | 添加锻造配方，`hits` 为锤击次数，`type` 为工具类型（"hammer"\|"pickaxe"） |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Ironclad Anvil（复合砧）

> `import mods.pyrotech.IroncladAnvil;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, int hits, string type, @Optional boolean inherited)` | void | 添加锻造配方，`hits` 为锤击次数，`type` 为工具类型（"hammer"\|"pickaxe"） |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Mechanical Bellows（机械风箱）

> `import mods.pyrotech.MechanicalBellows;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Mechanical Compactor（机械挤压机）

> `import mods.pyrotech.MechanicalCompactor;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, int amount)` | void | 添加基础压缩配方 |
| `.addRecipe(string name, IItemStack output, IIngredient input, int amount, int[] toolUsesRequired)` | void | 添加自定义压缩配方 |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

---

### Mechanical Hopper（机械漏斗）

> `import mods.pyrotech.MechanicalHopper;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Mechanical Mulcher（土壤疏松机）

> `import mods.pyrotech.MechanicalMulcher;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Obsidian Anvil（黑曜石砧）

> `import mods.pyrotech.ObsidianAnvil;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, int hits, string type)` | void | 添加配方，`hits` 为锤击次数，`type` 为工具类型（"hammer"\|"pickaxe"） |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Oil Lamp（油灯）

> `import mods.pyrotech.OilLamp;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Pit Kiln（坑窑）

> `import mods.pyrotech.PitKiln;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, int burnTimeTicks, @Optional boolean inherited)` | void | 添加配方（无失败产物） |
| `.addRecipe(string name, IItemStack output, IIngredient input, int burnTimeTicks, float failureChance, IItemStack[] failureItems, @Optional boolean inherited)` | void | 添加配方（带失败产物） |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Shelf（架子）

> `import mods.pyrotech.Shelf;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Simple Rock Bag（采石包）

> `import mods.pyrotech.SimpleRockBag;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Soaking Pot（浸泡壶）

> `import mods.pyrotech.SoakingPot;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, ILiquidStack inputFluid, IIngredient inputItem, int timeTicks)` | void | 添加配方 |
| `.addRecipe(string name, IItemStack output, ILiquidStack inputFluid, IIngredient inputItem, boolean requiresCampfire, int timeTicks)` | void | 添加配方，`requiresCampfire` 为是否需要置于营火上方 |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Stages（阶段）

> `import mods.pyrotech.Stages;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.and(Object[] stages)` | Stages | 逻辑与操作，接受字符串或 Stages 对象数组 |
| `.or(Object[] stages)` | Stages | 逻辑或操作 |
| `.not(string stage)` | Stages | 逻辑非操作 |
| `.not(Stages stages)` | Stages | 逻辑非操作 |

---

### Stash（储物盒）

> `import mods.pyrotech.Stash;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Stone Crucible（石坩埚）

> `import mods.pyrotech.StoneCrucible;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, ILiquidStack output, IIngredient input, int burnTimeTicks, @Optional boolean inherited)` | void | 添加配方 |
| `.removeRecipes(ILiquidStack output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Stone Faucet（石龙头）

> `import mods.pyrotech.FaucetStone;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Stone Kiln（石窑）

> `import mods.pyrotech.StoneKiln;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, int burnTimeTicks, @Optional boolean inherited)` | void | 添加配方（无失败产物） |
| `.addRecipe(string name, IItemStack output, IIngredient input, int burnTimeTicks, float failureChance, IItemStack[] failureItems, @Optional boolean inherited)` | void | 添加配方（带失败产物） |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Stone Oven（石炉）

> `import mods.pyrotech.StoneOven;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, @Optional boolean inherited)` | void | 添加配方 |
| `.blacklistSmeltingRecipes(IIngredient[] output)` | void | 将烧制配方加入黑名单 |
| `.blacklistAllSmeltingRecipes()` | void | 将所有烧制配方加入黑名单 |
| `.whitelistSmeltingRecipes(IIngredient[] output)` | void | 将烧制配方加入白名单 |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Stone Sawmill（石切割机）

> `import mods.pyrotech.StoneSawmill;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, int burnTimeTicks, IIngredient blade, @Optional int woodChips, @Optional boolean inherited)` | void | 添加配方，`blade` 为锯片（使用 `:*` 匹配任意耐久） |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Stone Tank（石缸）

> `import mods.pyrotech.StoneTank;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Tanning Rack（晾晒架）

> `import mods.pyrotech.TanningRack;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(string name, IItemStack output, IIngredient input, IItemStack failureItem, int timeTicks)` | void | 添加配方，`failureItem` 为下雨时的失败产物 |
| `.removeRecipes(IIngredient output)` | void | 移除匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Trip Hammer（夹板锤）

> `import mods.pyrotech.TripHammer;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Wood Rack（储木架）

> `import mods.pyrotech.WoodRack;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setGameStages(Stages stages)` | void | 设置使用设备所需的游戏阶段 |

---

### Worktable（工作台）

> `import mods.pyrotech.Worktable;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.buildShaped(IItemStack output, IIngredient[][] ingredients)` | Worktable | 构建有形状配方的构建器 |
| `.buildShapeless(IItemStack output, IIngredient[] ingredients)` | Worktable | 构建无形状配方的构建器 |
| `.addShaped(@Nullable string name, IItemStack output, IIngredient[][] ingredients)` | void | 添加有形状配方，`name` 为 null 时自动生成 |
| `.addShaped(@Nullable string name, IItemStack output, IIngredient[][] ingredients, @Nullable IIngredient tool, int toolDamage, @Optional boolean mirrored, @Optional boolean hidden, @Optional IRecipeFunction function, @Optional IRecipeAction action, @Optional Stages gamestages)` | void | 添加有形状配方（完整参数） |
| `.addShapeless(@Nullable string name, IItemStack output, IIngredient[] ingredients)` | void | 添加无形状配方 |
| `.addShapeless(@Nullable string name, IItemStack output, IIngredient[] ingredients, @Nullable IIngredient tool, int toolDamage, @Optional boolean hidden, @Optional IRecipeFunction function, @Optional IRecipeAction action, @Optional Stages gamestages)` | void | 添加无形状配方（完整参数） |
| `.blacklistVanillaRecipes(string[] resourceLocations)` | void | 将原版配方加入黑名单 |
| `.blacklistAllVanillaRecipes()` | void | 将所有原版配方加入黑名单 |
| `.whitelistVanillaRecipes(string[] resourceLocations)` | void | 将原版配方加入白名单 |
| `.removeRecipes(IIngredient output)` | void | 移除预设配方 |
| `.setGameStages(Stages stages)` | void | 设置工作台的通用游戏阶段 |
| `.setStoneGameStages(Stages stages)` | void | 设置石制工作台的游戏阶段 |

#### Worktable 构建器方法
| 方法 | 返回 | 说明 |
|------|------|------|
| `.setName(string name)` | Worktable | 设置配方名称 |
| `.setTool(IIngredient tool, int toolDamage)` | Worktable | 设置配方所需工具及耐久损耗 |
| `.setMirrored(boolean mirrored)` | Worktable | 设置配方是否镜像 |
| `.setHidden(boolean hidden)` | Worktable | 设置配方是否隐藏 |
| `.setRecipeFunction(IRecipeFunction recipeFunction)` | Worktable | 设置配方函数 |
| `.setRecipeAction(IRecipeAction recipeAction)` | Worktable | 设置配方动作 |
| `.setRecipeGameStages(Stages stages)` | Worktable | 设置配方所需游戏阶段 |
| `.register()` | void | 注册配方 |

## 使用示例

### 木桶配方

```zenscript
import mods.pyrotech.Barrel;

// 用水和4个树叶制作单宁（耗时10分钟）
Barrel.addRecipe("tannin_from_water_and_leaves", <liquid:tannin>, <liquid:water>, [<ore:treeLeaves>, <ore:treeLeaves>, <ore:treeLeaves>, <ore:treeLeaves>], 10 * 60 * 20);
```

### 锻造炉配方

```zenscript
import mods.pyrotech.Bloomery;

// 铁矿石冶炼配方
Bloomery.createBloomeryBuilder("bloom_from_iron_ore", <minecraft:iron_nugget>, <minecraft:iron_ore>)
    .setAnvilTiers(["granite", "ironclad"])
    .setBurnTimeTicks(28800)
    .setFailureChance(0.25)
    .setBloomYield(12, 15)
    .setSlagItem(<pyrotech:generated_slag_iron>, 4)
    .addFailureItem(<pyrotech:slag>, 1)
    .addFailureItem(<pyrotech:generated_slag_iron>, 2)
    .register();
```

### 工作台配方

```zenscript
import mods.pyrotech.Worktable;
import mods.pyrotech.Stages;

// 带游戏阶段的配方
Worktable.buildShaped(<minecraft:furnace>, [
    [<minecraft:cobblestone>, <minecraft:cobblestone>, <minecraft:cobblestone>],
    [<minecraft:cobblestone>, null, <minecraft:cobblestone>],
    [<minecraft:cobblestone>, <minecraft:cobblestone>, <minecraft:cobblestone>]])
    .setRecipeGameStages(Stages.and(["a", "b"]))
    .register();
```

## 注意事项

- 锯片的 meta 值使用 `:*` 确保锯片在损坏后仍然有效
- 游戏阶段系统需要安装 GameStages 模组