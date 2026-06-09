# 物品核心 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.item.IItemStack;`、`import crafttweaker.item.IMutableItemStack;`、`import crafttweaker.item.IItemDefinition;`

IItemStack、IMutableItemStack、IItemDefinition 核心 API。

---

## API 列表

### IItemStack（物品堆叠）

> `import crafttweaker.item.IItemStack;`

#### 获取 IItemStack

```zenscript
<minecraft:apple>                           // 尖括号（最常用）
<minecraft:stone>.definition.makeStack(0)   // 通过 IItemDefinition
<ore:ingotGold>.items                       // 从矿辞获取列表
<ore:ingotGold>.firstItem                   // 从矿辞获取第一个
loadedMods["minecraft"].items               // 从 mod 获取所有物品
```

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `definition` | IItemDefinition | 物品定义 |
| `name` | string | 物品内部名 |
| `displayName` | string | 显示名称 |
| `hardness` | float | 方块硬度（破坏所需时间，仅方块有效） |
| `damage` | int | 耐久损耗值 |
| `maxDamage` | int | 最大耐久 |
| `metadata` | int | Meta 值 |
| `hasTag` | bool | 是否有 NBT |
| `tag` | IData | NBT 数据（无 NBT 返回 `{}`） |
| `ores` | List\<IOreDictEntry\> | 所属矿辞列表 |
| `toolClasses` | List\<string\> | 工具类型 |
| `itemEnchantability` | int | 附魔能力 |
| `containerItem` | IItemStack | 容器物品（如空桶） |
| `hasContainerItem` | bool | 是否有容器物品 |
| `canEditBlocks` | bool | 是否可编辑方块 |
| `isOnItemFrame` | bool | 是否在物品展示框 |
| `isEnchantable` | bool | 是否可附魔 |
| `isEnchanted` | bool | 是否已附魔 |
| `isDamaged` | bool | 是否有耐久损耗 |
| `isDamageable` | bool | 是否可损耗 |
| `isItemBlock` | bool | 是否为方块物品 |
| `isStackable` | bool | 是否可堆叠 |
| `isBeaconPayment` | bool | 是否可作为信标消耗品 |
| `hasEffect` | bool | 是否有效果粒子 |
| `hasDisplayName` | bool | 是否有自定义显示名 |
| `hasSubtypes` | bool | 是否有子类型 |
| `isEmpty` | bool | 是否为空 |
| `burnTime` | int | 熔炉燃烧时间 |
| `showsDurabilityBar` | bool | 是否显示耐久条 |
| `hasCustomEntity` | bool | 是否有自定义实体 |
| `enchantments` | List\<IEnchantment\> | 附魔列表 |
| `matchTagExact` | bool | 精确匹配 NBT |
| `maxItemUseDuration` | int | 最大使用时长 |
| `capNBT` | IData | 能力 NBT |
| `repairCost` | int | 修复花费 |

#### @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `displayName` | string | 修改显示名称（全局生效） |
| `maxStackSize` | int | 修改最大堆叠数 |
| `hardness` | float | 修改方块硬度（仅方块有效） |
| `maxDamage` | int | 修改最大耐久 |
| `repairCost` | int | 修改修复花费 |

#### 数量方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.anyAmount()` | IItemStack | 任意数量（配方输入用） |
| `.amount(int)` | IItemStack | 设置数量 |
| `.withAmount(int)` | IItemStack | 设置数量（同上） |
| `stack * n` | IItemStack | 设置数量 |
| `.splitStack(int)` | IItemStack | 分割堆叠（原堆叠减少） |

#### 耐久方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.anyDamage()` | IItemStack | 任意耐久（配方输入用） |
| `.withDamage(int)` | IItemStack | 设置耐久值 |
| `.damageItem(int, IEntity)` | void | 消耗耐久 |

#### NBT 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.withEmptyTag()` | IItemStack | 设置空 NBT |
| `.withTag(IData)` | IItemStack | 设置 NBT（覆盖） |
| `.withTag(IData, bool)` | IItemStack | bool 为 true 时合并而非覆盖 |
| `.removeTag(String)` | IItemStack | 移除指定 NBT 键 |
| `.updateTag(IData)` | IItemStack | 合并 NBT（不覆盖已有键） |
| `.updateTag(IData, bool)` | IItemStack | 同上，bool 控制行为 |

#### 显示方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.withDisplayName(String)` | IItemStack | 设置显示名（仅单物品，支持 `§` 颜色代码） |
| `.withLore(String[])` | IItemStack | 设置 Lore（支持 `§` 颜色代码） |
| `.clearCustomName()` | void | 清除自定义名称 |
| `.addTooltip(String)` | IIngredient | 添加提示（全局生效，链式调用） |
| `.addShiftTooltip(String)` | IIngredient | 添加 Shift 提示（链式调用） |
| `.addShiftTooltip(String, String)` | IIngredient | Shift 提示 + 非 Shift 时的提示文字（链式调用） |
| `.addAdvancedTooltip(ITooltipFunction fn)` | IIngredient | 添加动态提示（函数接收 IItemStack 返回 string，链式调用） |
| `.addShiftTooltip(ITooltipFunction fn, @Optional ITooltipFunction infoFn)` | IIngredient | 添加动态 Shift 提示（链式调用） |
| `.clearTooltip()` | IIngredient | 清除所有提示（链式调用） |
| `.clearTooltip(bool leaveName)` | IIngredient | 清除所有提示，为 true 时保留物品名称（链式调用） |
| `.removeTooltip(String regex)` | IIngredient | 移除匹配正则表达式的提示（链式调用） |
| `.removeTooltipLine(int line)` | IIngredient | 移除指定行的提示（链式调用） |

#### 附魔方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.canApplyAtCraftingTable(IEnchantmentDefinition)` | bool | 是否可在工作台附魔 |
| `.addEnchantment(IEnchantment)` | void | 添加附魔 |

#### 方块方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.asBlock()` | IBlock | 转为方块 |
| `stack as IBlock` | IBlock | 转为方块 |
| `.canPlaceOn(IBlockDefinition)` | bool | 是否可放置在 |
| `.canDestroy(IBlockDefinition)` | bool | 是否可破坏 |
| `.canHarvestBlock(IBlockState)` | bool | 是否可挖掘 |
| `.getStrengthAgainstBlock(IBlockState)` | float | 对方块的破坏速度 |

#### 权重方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.percent(float)` | WeightedItemStack | 百分比权重（100 = 100%） |
| `.weight(float)` | WeightedItemStack | 权重（1 = 100%） |
| `stack % 50` | WeightedItemStack | 50% 权重 |

#### 实体方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.createEntityItem(IWorld, float, float, float)` | IEntityItem | 创建掉落物实体 |
| `.createEntityItem(IWorld, IBlockPos)` | IEntityItem | 创建掉落物实体 |

#### 其他方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.mutable()` | IMutableItemStack | 转为可变堆叠（原地修改） |
| `.getCapNBT()` | IData | 获取能力 NBT |
| `.withCapNBT(IData)` | IItemStack | 设置能力 NBT |
| `.liquid` | ILiquidStack | 获取关联流体（如桶） |
| `.isFood` | bool | 是否为食物 |
| `.healAmount` | int | 恢复饥饿值 |
| `.saturation` | float | 饱和度 |

### IMutableItemStack（可变物品堆叠）

> `import crafttweaker.item.IMutableItemStack;`

可变物品堆叠。`withTag`、`withAmount` 等方法会直接修改并返回原堆叠，而非创建新堆叠。

通过 `IItemStack.mutable()` 获取，继承 IItemStack 的所有方法。

#### 数量方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.grow(int count)` | IMutableItemStack | 增加指定数量 |
| `.shrink(int count)` | IMutableItemStack | 减少指定数量 |

#### 耐久方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.attemptDamageItem(int amount, @Optional IPlayer player)` | bool | 尝试损耗耐久，返回是否成功 |

#### 其他方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.copy()` | IItemStack | 复制为新的不可变堆叠（避免意外修改时使用） |

### IItemDefinition（物品定义）

> `import crafttweaker.item.IItemDefinition;`

通过 `<minecraft:wool>.definition` 获取。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `id` | string | 物品 ID |
| `name` | string | 内部名 |
| `maxStackSize` | int | 最大堆叠数 |
| `maxDamage` | int | 最大耐久 |
| `hasSubtypes` | bool | 是否有子类型 |
| `ores` | List\<IOreDictEntry\> | 所属矿辞列表（包含引用子物品的矿辞） |
| `owner` | string | 所属 mod 名称 |
| `defaultInstance` | IItemStack | 默认物品实例 |
| `creativeTab` | ICreativeTab | 创造模式标签页 |
| `creativeTabs` | ICreativeTab[] | 所有标签页 |
| `canItemEditBlocks` | bool | 是否可编辑方块 |
| `itemEnchantability` | int | 附魔能力 |
| `subItems` | List\<IItemStack\> | 所有子物品列表（服务端 mod 不保证正确） |

#### @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `creativeTab` | ICreativeTab | 设置创造模式标签页 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.makeStack(int meta)` | IItemStack | 按 Meta 值创建物品（meta 可选） |
| `.setHarvestLevel(String type, int level)` | void | 设置挖掘等级 |
| `.setNoRepair()` | void | 设置物品不可修复 |
| `.setContainerItem(IItemDefinition item)` | void | 设置容器物品 |
| `.getSubItems(ICreativeTab creativeTab)` | List\<IItemStack\> | 获取指定创造标签页下的子物品（服务端 mod 不保证正确） |
| `.getItemBurntime(IItemStack item)` | int | 获取燃烧时间：-1 为默认逻辑，0 为不可燃烧，其他为实际燃烧时间 |
