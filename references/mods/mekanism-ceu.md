# Mekanism: Community Edition Unofficial (CEu) CraftTweaker API 参考

> Mod ID: `mekanism`
> 前置条件: 无
> 导入: `import mods.mekanism.<ClassName>;`
> 注意：该文档为 Mekanism CEu（社区非官方版）参考。不是原版 Mekanism，请一定询问用户的具体版本！

## 气体系统

### IGasStack

Mekanism CEu 添加了气体括号处理器（Bracket Handler），用于定义气体物质。

```zenscript
<gas:oxygen>
<gas:water>  // 注意：<gas:water> 与 <liquid:water> 不同
```

- 使用 `/ct gases` 查看所有已注册气体
- 使用 `mods.mekanism.MekanismHelper.getGas(string)` 通过字符串获取气体

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `definition` | IGasDefinition | 返回气体定义 |
| `name` | string | 返回气体名称 |
| `displayName` | string | 返回气体显示名称 |
| `amount` | int | 返回气体数量（毫桶） |

#### 设置数量

```zenscript
var gas1 = <gas:water> * 500;
var gas2 = <gas:water>.withAmount(500);
```

### IGasDefinition

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `name` | string | 气体名称 |
| `displayName` | string | 气体显示名称 |

支持乘法运算创建 IGasStack：`<gas:water>.definition * 1000`

---

## MekanismHelper

> `import mods.mekanism.MekanismHelper;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getGas(string name)` | IGasStack | 通过气体名称获取 IGasStack 对象 |

---

## API 列表

### GasConversion（气体转换）

> `import mods.mekanism.GasConversion;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.register(IIngredient ingredient, IGasStack gas)` | void | 注册物品到气体的转换 |
| `.unregister(IIngredient ingredient, IGasStack gas)` | void | 移除物品到气体的转换（两个参数都是必填的） |
| `.unregisterAll()` | void | 移除所有气体转换 |

### Infuser（冶金灌注机）

> `import mods.mekanism.infuser;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(String infusionType, int infusionConsumed, IIngredient inputStack, IItemStack outputStack)` | void | 添加冶金灌注配方 |
| `.removeRecipe(IIngredient outputStack, @Optional IIngredient inputStack, @Optional String infusionType)` | void | 移除配方。指定输入参数仅移除特定配方，省略则移除所有产出该物品的配方 |
| `.removeAllRecipes()` | void | 移除所有冶金灌注配方（不包括 CraftTweaker 添加的） |

内置灌注类型："CARBON"、"TIN"、"DIAMOND"、"REDSTONE"、"FUNGI"、"BIO"、"OBSIDIAN"。使用 `/ct infuseTypes` 查看所有已注册类型。

### Enrichment Chamber（富集仓）

> `import mods.mekanism.enrichment;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack outputStack)` | void | 添加富集配方 |
| `.removeRecipe(IIngredient inputStack, @Optional IIngredient outputStack)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Crusher（粉碎机）

> `import mods.mekanism.crusher;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack outputStack)` | void | 添加粉碎机配方 |
| `.removeRecipe(IIngredient outputStack, @Optional IIngredient inputStack)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Combiner（融合机）

> `import mods.mekanism.combiner;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient itemInput, @Optional IIngredient extraInput, IItemStack itemOutput)` | void | 添加融合机配方 |
| `.removeRecipe(IIngredient outputStack, @Optional IIngredient inputStack, @Optional IIngredient extraInput)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Energized Smelter（电力熔炼炉）

> `import mods.mekanism.smelter;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack outputStack)` | void | 添加电力熔炼配方 |
| `.removeRecipe(IIngredient inputStack, @Optional IIngredient outputStack)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Osmium Compressor（锇压缩机）

> `import mods.mekanism.compressor;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, @Optional IGasStack inputGas, IItemStack itemOutput)` | void | 添加锇压缩机配方 |
| `.removeRecipe(IIngredient outputStack, @Optional IIngredient inputStack, @Optional IIngredient inputGas)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Precision Sawmill（精密锯木机）

> `import mods.mekanism.sawmill;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack outputStack, @Optional IItemStack bonusOutput, @Optional double bonusChance)` | void | 添加锯木机配方。outputStack 为主产物，bonusOutput 为副产物，bonusChance 为副产物概率（1.0 制） |
| `.removeRecipe(IIngredient inputStack, @Optional IIngredient outputStack, @Optional IIngredient bonusOutput)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Chemical Oxidizer（化学氧化机）

> `import mods.mekanism.chemical.oxidizer;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IGasStack outputGas)` | void | 添加化学氧化器配方 |
| `.removeRecipe(IIngredient outputGas, @Optional IIngredient inputStack)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Chemical Infuser（化学灌注器）

> `import mods.mekanism.chemical.infuser;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IGasStack inputGas1, IGasStack inputGas2, IGasStack outputGas)` | void | 添加化学灌注器配方 |
| `.removeRecipe(IIngredient outputGas, @Optional IIngredient inputGas1, @Optional IIngredient inputGas2)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Chemical Injection Chamber（化学压射室）

> `import mods.mekanism.chemical.injection;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IGasStack inputGas, IItemStack outputStack)` | void | 添加化学压射室配方 |
| `.removeRecipe(IIngredient outputStack, @Optional IIngredient inputStack, @Optional IIngredient inputGas)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Chemical Dissolution Chamber（化学溶解室）

> `import mods.mekanism.chemical.dissolution;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IGasStack outputGas)` | void | 添加化学溶解室配方 |
| `.removeRecipe(IIngredient outputGas, @Optional IIngredient inputStack)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Chemical Washer（化学清洗机）

> `import mods.mekanism.chemical.washer;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IGasStack inputGas, IGasStack outputGas)` | void | 添加化学清洗机配方 |
| `.removeRecipe(IIngredient outputGas, @Optional IIngredient inputGas)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Chemical Crystallizer（化学结晶器）

> `import mods.mekanism.chemical.crystallizer;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IGasStack inputGas, IItemStack outputStack)` | void | 添加化学结晶器配方 |
| `.removeRecipe(IIngredient outputStack, @Optional IIngredient inputGas)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Electrolytic Separator（电解分离器）

> `import mods.mekanism.separator;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(ILiquidStack inputFluid, double inputRF, IGasStack outputGas1, IGasStack outputGas2)` | void | 添加电解分离器配方 |
| `.removeRecipe(IIngredient inputFluid, @Optional IIngredient outputGas1, @Optional IIngredient outputGas2)` | void | 移除配方。液体输入必选，两个气体输出可选 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Pressurised Reaction Chamber（加压反应室）

> `import mods.mekanism.reaction;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient itemInput, ILiquidStack liquidInput, IGasStack gasInput, IItemStack itemOutput, IGasStack gasOutput, double energy, int duration)` | void | 添加加压反应室配方 |
| `.removeRecipe(IIngredient itemOutput, IIngredient gasOutput, @Optional IIngredient itemInput, @Optional IIngredient liquidInput, @Optional IIngredient gasInput)` | void | 移除配方。物品输出和气体输出必选，三个输入可选 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Purification Chamber（提纯仓）

> `import mods.mekanism.purification;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient itemInput, @Optional IGasStack gasInput, IItemStack itemOutput)` | void | 添加提纯仓配方 |
| `.removeRecipe(IIngredient itemOutput, @Optional IIngredient itemInput, @Optional IIngredient gasInput)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Solar Neutron Activator（太阳能中子活化器）

> `import mods.mekanism.solarneutronactivator;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IGasStack gasInput, IGasStack gasOutput)` | void | 添加太阳能中子活化器配方 |
| `.removeRecipe(IIngredient gasInput, @Optional IIngredient gasOutput)` | void | 移除配方。不填输出则移除所有匹配输入的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Thermal Evaporation（热力蒸馏塔）

> `import mods.mekanism.thermalevaporation;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(ILiquidStack liquidInput, ILiquidStack liquidOutput)` | void | 添加热力蒸馏配方 |
| `.removeRecipe(IIngredient liquidInput, @Optional IIngredient liquidOutput)` | void | 移除配方。不填输出则移除所有匹配输入的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Stamping Machine（数控机床）

> `import mods.mekanism.stamping;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack outputStack)` | void | 添加数控机床配方 |
| `.removeRecipe(IIngredient outputStack, @Optional IIngredient inputStack)` | void | 移除配方。不填输入则移除所有匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Turning Factory（车削工厂）

> `import mods.mekanism.turning;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack outputStack)` | void | 添加车削工厂配方 |
| `.removeRecipe(IIngredient inputStack, @Optional IIngredient outputStack)` | void | 移除配方。不填输出则移除所有匹配输入的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Alloy（合金炉）

> `import mods.mekanism.alloy;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient input1, IIngredient input2, IItemStack output)` | void | 添加合金炉配方 |
| `.removeRecipe(IIngredient output, @Optional IIngredient input1, @Optional IIngredient input2)` | void | 移除配方。不填输入则移除所有匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Ambient Accumulator（环境气体收集器）

> `import mods.mekanism.ambientaccumulator;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(int dimension, IGasStack outputGas, double chance)` | void | 添加环境气体收集器配方。chance 为概率（1.0 制） |
| `.removeRecipe(int dimension, @Optional IIngredient outputGas)` | void | 移除配方。不填输出则移除所有匹配维度的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Brushed Machine（数控拉丝机）

> `import mods.mekanism.brushed;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack outputStack)` | void | 添加数控拉丝机配方 |
| `.removeRecipe(IIngredient outputStack, @Optional IIngredient inputStack)` | void | 移除配方。不填输入则移除所有匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Cell Extractor（细胞提取机）

> `import mods.mekanism.cellextractor;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack output1, IItemStack output2, double chance)` | void | 添加细胞提取机配方。output1 为主产物，output2 为副产物，chance 为副产物概率（1.0 制） |
| `.removeRecipe(IIngredient inputStack, @Optional IIngredient output1, @Optional IIngredient output2)` | void | 移除配方。不填输出则移除所有匹配输入的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Cell Separator（细胞分离机）

> `import mods.mekanism.cellseparator;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack output1, IItemStack output2, double chance)` | void | 添加细胞分离机配方。output1 为主产物，output2 为副产物，chance 为副产物概率（1.0 制） |
| `.removeRecipe(IIngredient inputStack, @Optional IIngredient output1, @Optional IIngredient output2)` | void | 移除配方。不填输出则移除所有匹配输入的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Isotopic Centrifuge（同位素分离机）

> `import mods.mekanism.isotopiccentrifuge;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IGasStack gasInput, IGasStack gasOutput)` | void | 添加同位素分离机配方 |
| `.removeRecipe(IIngredient gasOutput, @Optional IIngredient gasInput)` | void | 移除配方。不填输入则移除所有匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Nucleosynthesizer（反质子核合成器）

> `import mods.mekanism.nucleosynthesizer;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient itemInput, IGasStack gasInput, IItemStack itemOutput, double energy, int duration)` | void | 添加反质子核合成器配方。energy 为总能量消耗，duration 为处理时间（tick） |
| `.removeRecipe(IIngredient itemOutput, @Optional IIngredient itemInput, @Optional IIngredient gasInput)` | void | 移除配方。不填输入则移除所有匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Nutritional Liquifier（营养液化机）

> `import mods.mekanism.chemical.nutritionalliquifier;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IGasStack outputGas)` | void | 添加营养液化机配方 |
| `.removeRecipe(IIngredient outputGas, @Optional IIngredient inputStack)` | void | 移除配方。不填输入则移除所有匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Organic Farm（有机农场）

> `import mods.mekanism.organicfarm;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient itemInput, IGasStack gasInput, IItemStack itemOutput, IItemStack bonusOutput, double bonusChance)` | void | 添加有机农场配方。bonusOutput 为副产物，bonusChance 为副产物概率（1.0 制） |
| `.removeRecipe(IIngredient itemInput, @Optional IIngredient gasInput, @Optional IIngredient itemOutput, @Optional IIngredient bonusOutput)` | void | 移除配方。不填其他参数则移除所有匹配输入的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Recycler（回收机）

> `import mods.mekanism.recycler;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack outputStack, double chance)` | void | 添加回收机配方。chance 为回收概率（1.0 制） |
| `.removeRecipe(IIngredient inputStack, @Optional IIngredient outputStack)` | void | 移除配方。不填输出则移除所有匹配输入的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Rolling Machine（压延工厂）

> `import mods.mekanism.rolling;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack outputStack)` | void | 添加压延工厂配方 |
| `.removeRecipe(IIngredient outputStack, @Optional IIngredient inputStack)` | void | 移除配方。不填输入则移除所有匹配输出的配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Gas To Energy（燃气发电机）

> `import mods.mekanism.GasToEnergy;`
> 于 9.9.2.234 版本添加

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient input, double energy)` | void | 添加燃气发电机配方。input 为气体或物品，energy 为总输出能量 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Replicator（复制机）

#### 复制机（物品）

> `import mods.mekanism.replicator.itemstack;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IItemStack itemOutput, IGasStack gasInput, int duration, double energy)` | void | 添加物品复制配方。energy 为总能量输入 |
| `.removeAllRecipes()` | void | 移除所有配方 |

#### 复制机（气体）

> `import mods.mekanism.replicator.gases;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IGasStack gasOutput, IGasStack gasInput, int duration, double energy)` | void | 添加气体复制配方。gasOutput 为复制的气体，gasInput 为消耗的气体 |
| `.removeAllRecipes()` | void | 移除所有配方 |

#### 复制机（流体）

> `import mods.mekanism.replicator.fluidstack;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IGasStack gasInput, ILiquidStack fluidOutput, int duration, double energy)` | void | 添加流体复制配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

---

## 回旋式气液转换机

通过设置 IGasDefinition 的 `liquid` 属性，可以将气体与对应的流体关联。

```zenscript
import mods.mekanism.gas.IGasDefinition;
import mods.mekanism.gas.IGasStack;
import crafttweaker.liquid.ILiquidDefinition;

val gasDef as IGasDefinition = <gas:deuterium>.definition;
gasDef.liquid = <liquid:water>.definition;
```
