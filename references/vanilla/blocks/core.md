# 方块核心 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.block.IBlock;`、`import crafttweaker.block.IBlockState;`、`import crafttweaker.block.IBlockDefinition;`

IBlock、IBlockState、IBlockDefinition 核心 API。

---

## API 列表

### IBlock（方块）

> `import crafttweaker.block.IBlock;`

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `definition` | IBlockDefinition | 方块定义 |
| `meta` | int | Meta 值 |
| `data` | IData | 方块的 TileData（仅通过 `IWorld.getBlock()` 获取时非空） |
| `fluid` | ILiquidDefinition | 方块的流体 |
| `blocks` | List\<IBlock\> | 此对象所有可能的方块（来自 IBlockPattern） |
| `displayName` | string | 显示名称（来自 IBlockPattern） |


### IBlockState（方块状态）

> `import crafttweaker.block.IBlockState;`

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `block` | IBlock | 方块 |
| `meta` | int | Meta 值 |
| `commandString` | string | 命令字符串（可用作方块状态括号处理器表达式） |

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `IBlockState.getBlockState(string, string...)` | IBlockState | 运行时解析 IBlockState。第一个参数为 "modid:blockname"，后续为 "property=value" 对。未指定的属性使用默认值 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getPropertyNames()` | List\<string\> | 获取所有属性名 |
| `.getPropertyValue(string)` | string | 获取指定属性的值 |
| `.getAllowedValuesForProperty(string)` | List\<string\> | 获取指定属性的所有允许值 |
| `.withProperty(string, string)` | IBlockState | 创建新 IBlockState 并设置指定属性值 |
| `.getProperties()` | Map | 获取所有属性（属性名 → 值 的映射） |
| `.isReplaceable(IWorld, IBlockPos)` | bool | 检查方块是否可替换 |
| `.compare(IBlockState)` | int | 比较两个状态，相等返回 0（也可使用 `==` `!=`） |
| `.matchBlock()` | IBlockStateMatcher | 获取匹配此方块所有状态的 IBlockStateMatcher |

### IBlockDefinition（方块定义）

> `import crafttweaker.block.IBlockDefinition;`

IBlockDefinition 提供方块的额外信息，可通过 `block.definition` 获取，或通过 `game.blocks` 获取所有方块定义列表。

#### @ZenGetter / @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `id` | string | 方块 ID（只读） |
| `name` | string | 方块名称（只读） |
| `displayName` | string | 显示名称（只读） |
| `commandString` | string | 命令字符串（只读） |
| `unlocalizedName` | string | 未本地化名称（只读） |
| `creativeTab` | ICreativeTab | 创造模式标签页（可读写） |
| `defaultState` | IBlockState | 默认方块状态（只读） |
| `harvestLevel` | int | 挖掘等级（只读）。设置请用 `.setHarvestLevel(string, int, @Optional IBlockState)` |
| `harvestTool` | string | 挖掘工具（只读） |
| `hardness` | int | 硬度（可读写） |
| `resistance` | int | 爆炸抗性（可读写） |
| `lightLevel` | int | 亮度（可读写） |
| `lightOpacity` | int | 光照不透明度（可读写） |
| `tickRandomly` | bool | 是否随机 tick（可读写） |
| `canSpawnInBlock` | bool | 实体是否可在此方块内生成（只读） |
| `defaultSlipperiness` | float | 默认滑度（**只写**，读不到） |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.makeStack(int)` | IItemStack | 创建物品堆叠 |
| `.setHarvestLevel(string, int, @Optional IBlockState)` | void | 设置挖掘等级。省略 IBlockState 则设置所有状态 |
| `.getHarvestLevel(IBlockState)` | int | 获取指定状态的挖掘等级 |
| `.getHarvestTool(IBlockState)` | string | 获取指定状态的挖掘工具 |
| `.getTickRate(IWorld)` | int | 获取指定世界的 tick 速率 |
| `.canPlaceBlockOnSide(IWorld, IBlockPos, IFacing)` | bool | 检查方块是否可以在指定面放置 |
| `.canPlaceBlockAt(IWorld, IBlockPos)` | bool | 检查方块是否可以在指定位置放置 |
| `.getSlipperiness(IBlockState, IBlockAccess, IBlockPos, @Optional IEntity)` | float | 获取方块滑度 |
| `.getLightOpacity(IBlockState)` | float | 获取指定状态的光照不透明度 |
| `.getLightOpacity(IBlockState, IWorld, IBlockPos)` | float | 获取指定位置的光照不透明度 |
| `.getLightLevel(IBlockState)` | float | 获取指定状态的亮度 |
| `.getLightLevel(IBlockState, IWorld, IBlockPos)` | float | 获取指定位置的亮度 |
| `.getResistance(IWorld, IBlockPos, IEntity, IExplosion)` | float | 获取指定位置和爆炸的爆炸抗性 |
| `.getStateFromMeta(int)` | IBlockState | 从 Meta 获取方块状态 |
| `.isToolEffective(string, IBlockState)` | bool | 检查工具是否对指定状态有效 |
| `.setUnbreakable()` | void | 设置不可破坏（等同于 `hardness = -1`） |
| `.setTickRandomly(bool)` | void | 设置随机 tick |
| `.setHardness(int)` | void | 设置硬度 |
| `.setResistance(int)` | void | 设置爆炸抗性 |
| `.setLightLevel(int)` | void | 设置亮度 |
| `.setLightOpacity(int)` | void | 设置光照不透明度 |
| `.setHarvestLevel(string, int)` | void | 设置挖掘等级 |
| `.setToolHarvest(string, int)` | void | 设置工具挖掘等级 |
| `.setSoundType(string)` | void | 设置声音类型 |
| `.setFlammability(int)` | void | 设置可燃性 |
| `.setFireSpreadSpeed(int)` | void | 设置火焰传播速度 |
| `.setFireSource(bool)` | void | 设置火源 |
| `.setFullBlock(bool)` | void | 设置完整方块 |
| `.setFullCube(bool)` | void | 设置完整立方体 |
| `.setNormalCube(bool)` | void | 设置普通方块 |
| `.setOpaque(bool)` | void | 设置不透明 |
| `.setReplaceable(bool)` | void | 设置可替换 |
| `.setAir(bool)` | void | 设置空气 |
| `.setCollidable(bool)` | void | 设置可碰撞 |
| `.addDrop(IItemStack)` | void | 添加掉落物 |
| `.addDrop(IItemStack, int, int)` | void | 添加掉落物（数量范围） |
| `.removeDrop(IItemStack)` | void | 移除掉落物 |
| `.clearDrops()` | void | 清除所有掉落 |
