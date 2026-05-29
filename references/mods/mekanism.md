# Mekanism CraftTweaker API 参考

> Mod ID: `mekanism`
> 前置条件: 无
> 导入: `import mods.mekanism.<ClassName>;`
> 注意： 该文档为原版mekanism参考。不是社区非官方版，请一定询问用户的具体版本！

## 气体系统

### IGasStack

Mekanism 添加了气体括号处理器（Bracket Handler），用于定义气体物质。

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

## API 列表

### GasConversion（气体转换）

> `import mods.mekanism.GasConversion;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.register(IIngredient ingredient, IGasStack gas)` | void | 注册物品到气体的转换 |
| `.unregister(IIngredient ingredient, IGasStack gas)` | void | 移除物品到气体的转换 |
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

### Energized Smelter（充能冶炼炉）

> `import mods.mekanism.smelter;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack outputStack)` | void | 添加能量冶炼配方 |
| `.removeRecipe(IIngredient inputStack, @Optional IIngredient outputStack)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Osmium Compressor（锇压缩机）

> `import mods.mekanism.compressor;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, @Optional IGasStack inputGas, IItemStack itemOutput)` | void | 添加锇压缩机配方。9.7.0+ inputGas 不再仅限于锇 |
| `.removeRecipe(IIngredient outputStack, @Optional IIngredient inputStack, @Optional IIngredient inputGas)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Precision Sawmill（精密锯木机）

> `import mods.mekanism.sawmill;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient inputStack, IItemStack outputStack, @Optional IItemStack bonusOutput, @Optional double bonusChance)` | void | 添加锯木机配方 |
| `.removeRecipe(IIngredient inputStack, @Optional IIngredient outputStack, @Optional IIngredient bonusOutput)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Metallurgic Infuser

见上方 Infuser 部分。

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
| `.removeRecipe(IIngredient inputFluid, @Optional IIngredient outputGas1, @Optional IIngredient outputGas2)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Pressurised Reaction Chamber（加压反应室）

> `import mods.mekanism.reaction;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient itemInput, ILiquidStack liquidInput, IGasStack gasInput, IItemStack itemOutput, IGasStack gasOutput, double energy, int duration)` | void | 添加加压反应室配方 |
| `.removeRecipe(IIngredient itemOutput, IIngredient gasOutput, @Optional IIngredient itemInput, @Optional IIngredient liquidInput, @Optional IIngredient gasInput)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Purification Chamber（净化仓）

> `import mods.mekanism.purification;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient itemInput, @Optional IGasStack gasInput, IItemStack itemOutput)` | void | 添加净化仓配方 |
| `.removeRecipe(IIngredient itemOutput, @Optional IIngredient itemInput, @Optional IIngredient gasInput)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Solar Neutron Activator（太阳能中子活化器）

> `import mods.mekanism.solarneutronactivator;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IGasStack gasInput, IGasStack gasOutput)` | void | 添加太阳能中子活化器配方 |
| `.removeRecipe(IIngredient gasInput, @Optional IIngredient gasOutput)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |

### Thermal Evaporation（热力蒸馏）

> `import mods.mekanism.thermalevaporation;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(ILiquidStack liquidInput, ILiquidStack liquidOutput)` | void | 添加配方 |
| `.removeRecipe(IIngredient liquidInput, @Optional IIngredient liquidOutput)` | void | 移除配方 |
| `.removeAllRecipes()` | void | 移除所有配方 |
