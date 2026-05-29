# Extended Crafting CraftTweaker API 参考

> Mod ID: `extendedcrafting`
> 前置条件: 无
> 导入: `import mods.extendedcrafting.<ClassName>;`

## API 列表

### TableCrafting（合成台配方）

Extended Crafting 添加了 4 种等级的扩展合成台：3x3（原版）、5x5（Tier 1）、7x7（Tier 2）、9x9（Tier 3）。

> `import mods.extendedcrafting.TableCrafting;`

#### 添加有序配方

```
TableCrafting.addShaped(<output>, [[...], [...], ...]);
TableCrafting.addShaped(tier, <output>, [[...], [...], ...]);
```
支持的网格大小：3x3、5x5、7x7、9x9。输入数组格式与原版合成配方相同。

| 参数 | 类型 | 说明 |
|------|------|------|
| `tier` | int | 可选。所需合成台等级。1-4 对应不同等级，0 为任意足够大的合成台。不填默认为 0 |

#### 添加无序配方

```
TableCrafting.addShapeless(<output>, [<input>, ...]);
TableCrafting.addShapeless(tier, <output>, [<input>, ...]);
```

| 参数 | 类型 | 说明 |
|------|------|------|
| `tier` | int | 可选。所需合成台等级。不填默认为 0 |
| `inputs` | IIngredient[] | 输入物品数组，最多 81 个 |

#### 移除配方

```
TableCrafting.remove(<output>);
```

### CombinationCrafting（核心基座配方）

核心基座（Crafting Core）+ 基座（Pedestal）的配方系统。最多支持 48 个基座。

> `import mods.extendedcrafting.CombinationCrafting;`

#### 添加配方

```
CombinationCrafting.addRecipe(<output>, rfCost, <input>, [<pedestalItem>, ...]);
CombinationCrafting.addRecipe(<output>, rfCost, rfRate, <input>, [<pedestalItem>, ...]);
```

| 参数 | 类型 | 说明 |
|------|------|------|
| `rfCost` | int | 所需 RF 总量 |
| `rfRate` | int | 可选。每 tick 消耗的 RF。合成所需 tick = rfCost / rfRate。不填则使用配置文件默认值 |
| `input` | IItemStack | 放在核心基座中间的输入物品 |
| `pedestalItems` | IIngredient[] | 基座上需要的物品数组，0-48 个 |

#### 移除配方

```
CombinationCrafting.remove(<output>);
```

### CompressionCrafting（量子压缩机配方）

量子压缩机（Quantum Compressor）的配方系统。

> `import mods.extendedcrafting.CompressionCrafting;`

#### 添加配方

```
CompressionCrafting.addRecipe(<output>, <input>, inputCount, <catalyst>, rfCost);
CompressionCrafting.addRecipe(<output>, <input>, inputCount, <catalyst>, rfCost, rfRate);
```

| 参数 | 类型 | 说明 |
|------|------|------|
| `inputCount` | int | 所需输入物品数量 |
| `catalyst` | IItemStack | 催化剂物品，不会被消耗，放在左侧小槽中 |
| `rfCost` | int | 合成阶段所需 RF 总量 |
| `rfRate` | int | 可选。每 tick 消耗的 RF。合成所需 tick = rfCost / rfRate。不填则使用配置文件默认值 |

#### 移除配方

```
CompressionCrafting.remove(<output>);
```

### EnderCrafting（末影合成台配方）

末影合成台（Ender Crafter）的配方系统，3x3 网格。

> `import mods.extendedcrafting.EnderCrafting;`

#### 添加有序配方

```
EnderCrafting.addShaped(<output>, [[<>, <>, <>], [<>, <>, <>], [<>, <>, <>]], seconds);
```

| 参数 | 类型 | 说明 |
|------|------|------|
| `seconds` | int | 可选。合成所需秒数。不填则使用配置文件默认值 |

#### 添加无序配方

```
EnderCrafting.addShapeless(<output>, [<input>, ...], seconds);
```

| 参数 | 类型 | 说明 |
|------|------|------|
| `seconds` | int | 可选。合成所需秒数。不填则使用配置文件默认值 |

#### 移除配方

```
EnderCrafting.remove(<output>);
```