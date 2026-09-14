# Liquids CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.liquid.ILiquidStack;`、`import crafttweaker.liquid.ILiquidDefinition;`、`import crafttweaker.item.WeightedLiquidStack;`

流体系统 API，用于操作流体堆叠和流体定义。

---

## API 列表

### ILiquidStack（流体堆叠）

> `import crafttweaker.liquid.ILiquidStack;`

ILiquidStack 由流体定义和可选的 tag/数量组成。ILiquidStack 实现了 IIngredient 接口。

#### 获取 ILiquidStack

```zenscript
<liquid:water>                    // 尖括号
<liquid:lava> * 1000              // 带数量（mB）
<liquid:lava>.withTag(DATA)       // 带 NBT 数据
<minecraft:water_bucket>.liquid   // 从桶获取
```

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `name` | string | 流体名称（未本地化） |
| `displayName` | string | 显示名称（已本地化） |
| `amount` | int | 数量（mB） |
| `luminosity` | int | 亮度 |
| `density` | int | 密度 |
| `temperature` | int | 温度 |
| `viscosity` | int | 粘度 |
| `gaseous` | bool | 是否气体 |
| `tag` | IData | NBT 数据 |
| `definition` | ILiquidDefinition | 流体定义 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `stack * n` | ILiquidStack | 设置数量（mB） |
| `.matches(ILiquidStack)` | bool | 是否匹配（来自 IIngredient） |

#### IIngredient 限制

- ILiquidStack 实现了 IIngredient，但有以下限制：
- 不能标记（.marked 无效）
- .items 返回空列表
- .itemArray 返回空数组
- .liquids 返回自身
- 不能有 Transformer
- 不能有条件（.only 无效）
- 与物品匹配始终返回 false

### ILiquidDefinition（流体定义）

> `import crafttweaker.liquid.ILiquidDefinition;`

ILiquidDefinition 定义流体本身，与 ILiquidStack 不同，它允许修改流体属性。通过 `liquid.definition` 获取。

#### 创建 ILiquidStack

```zenscript
val def = <liquid:lava>.definition;
val bucketOfLava = def * 1000;  // 等同于 <liquid:lava> * 1000
```

#### @ZenGetter / @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `name` | string | 流体名称（未本地化，只读） |
| `displayName` | string | 显示名称（已本地化，只读） |
| `luminosity` | int | 亮度（可读写） |
| `density` | int | 密度（可读写） |
| `temperature` | int | 温度（可读写） |
| `viscosity` | int | 粘度（可读写） |
| `gaseous` | bool | 是否气体（可读写） |

注意：ZenSetter 只修改流体注册表，不影响世界中的流体。

### WeightedLiquidStack（带权重的流体堆叠）

> `import crafttweaker.item.WeightedLiquidStack;`

WeightedLiquidStack 是带有百分比权重的 ILiquidStack，用于基于概率的操作。

#### 创建

```zenscript
val liquidStack = <liquid:lava>;
val weighted = liquidStack % 20;        // 20% 概率
val weighted2 = liquidStack.weight(0.2); // 0.2 = 20%
```

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `stack` | ILiquidStack | 关联的流体堆叠 |
| `chance` | float | 概率（小数形式，如 0.2） |
| `percent` | float | 概率（百分比形式，如 20.0） |

---

## 使用示例

### 流体转换

```zenscript
// 桶物品转流体
<liquid:water> * 1000  // 1000mB 水
<minecraft:water_bucket>.liquid  // 从桶获取
```

### 配方中使用流体

```zenscript
// 流体可作为 IIngredient 使用
recipes.addShaped("test", <minecraft:stone>, [
    [<liquid:water>, null, null],
    [null, null, null],
    [null, null, null]
]);

// 或使用桶
recipes.addShaped("test2", <minecraft:stone>, [
    [<minecraft:water_bucket>, null, null],
    [null, null, null],
    [null, null, null]
]);
```

---

## ZenUtils 扩展（需安装 ZenUtils）

> `import mods.zenutils.LiquidHandler;`
> 版本要求: 1.9.8+

### CrTLiquidHandler（流体处理器）

通过 `world.getLiquidHandler(IBlockPos)` 获取方块实体的流体容器。

| 方法 | 返回 | 说明 |
|------|------|------|
| `world.getLiquidHandler(IBlockPos, @Optional IFacing)` | LiquidHandler | 获取方块实体的流体容器 |
| `tankProperties` | List\<ILiquidTankProperties\> | 获取流体槽信息（只读） |
| `drain(int, bool)` | ILiquidStack | 排出流体。false 时仅模拟 |
| `drain(ILiquidStack, bool)` | ILiquidStack | 排出指定类型流体。false 时仅模拟 |
| `fill(ILiquidStack, bool)` | int | 填充流体。返回实际填充量。false 时仅模拟 |

### ILiquidTankProperties（流体槽信息）

> `import mods.zenutils.ILiquidTankProperties;`

| 属性/方法 | 返回 | 说明 |
|------|------|------|
| `contents` | ILiquidStack | 流体槽内容的副本，可能为 null |
| `capacity` | int | 流体槽最大容量（mB） |
| `canFill` | bool | 是否可以填充 |
| `canDrain` | bool | 是否可以排出 |
| `canFillFluidType(ILiquidStack)` | bool | 检查是否可以填充指定类型的流体（不考虑当前状态） |
| `canDrainFluidType(ILiquidStack)` | bool | 检查是否可以排出指定类型的流体（不考虑当前状态） |

---

## ContentTweaker 扩展（需安装 ContentTweaker）

> `import mods.contenttweaker.VanillaFactory;`
> `import mods.contenttweaker.Fluid;`

CoT 脚本第一行必须为 `#loader contenttweaker`。

### VanillaFactory 流体方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.createFluid(string id, int color)` | Fluid | 创建自定义流体 |

### Fluid（自定义流体）

> `import mods.contenttweaker.Fluid;`

| ZenProperty | 类型 | 默认值 | 说明 |
|-------------|------|--------|------|
| `density` | int | 1000 | 密度（水=1000，熔岩=3000） |
| `viscosity` | int | 1000 | 黏度（水=1000，熔岩=3000） |
| `temperature` | int | 300 | 温度（水=300，熔岩=1300） |
| `luminosity` | int | 0 | 亮度 |
| `vaporize` | bool | false | 在下界是否蒸发 |
| `colorize` | bool | true | 材质是否受颜色参数影响 |
| `stillLocation` | string | "contenttweaker:fluids/fluid" | 源头材质路径 |
| `flowingLocation` | string | "contenttweaker:fluids/fluid_flow" | 流动材质路径 |
| `material` | IMaterialDefinition | `<blockmaterial:water>` | 方块材质 |
| `gaseous` | bool | false | 是否反重力流动 |

| 方法 | 返回 | 说明 |
|------|------|------|
| `.register()` | void | 注册流体进游戏 |
