# 方块属性 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.block.IBlockPattern;`、`import crafttweaker.block.IBlockProperties;`、`import crafttweaker.block.IBlockStateMatcher;`、`import crafttweaker.block.IMaterial;`、`import crafttweaker.block.IMobilityFlag;`

IBlockPattern、IBlockProperties、IBlockStateMatcher、IMaterial、IMobilityFlag 属性相关 API。

---

## API 列表

### IBlockPattern（方块模式）

> `import crafttweaker.block.IBlockPattern;`

IBlockPattern 是一个将多个方块组合为一个对象的接口，类似于 IIngredient 之于 IItemStack。IBlock 继承 IBlockPattern。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `blocks` | List\<IBlock\> | 列出此对象的所有可能方块 |
| `displayName` | string | 返回匹配方块的显示名称 |

#### 操作

- 可以使用 `|` 运算符合并两个 IBlockPattern
- 可以使用 `in` 关键字检查一个方块是否在 IBlockPattern 中

### IBlockProperties（方块属性接口）

> `import crafttweaker.block.IBlockProperties;`

IBlockProperties 是 IBlockState 的父接口，所有 IBlockState 的方法也适用于 IBlockProperties。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `canProvidePower` | bool | 是否可提供红石信号 |
| `mobilityFlag` | string | 移动标志 |
| `material` | IMaterial | 方块材质 |
| `causesSuffocation` | bool | 是否会导致窒息 |
| `hasCustomBreakingProgress` | bool | 是否有自定义破坏进度 |
| `blockNormalCube` | bool | 是否普通方块 |
| `fullBlock` | bool | 是否完整方块 |
| `fullCube` | bool | 是否完整立方体 |
| `normalCube` | bool | 是否普通方块 |
| `opaqueCube` | bool | 是否不透明立方体 |
| `translucent` | bool | 是否半透明 |
| `useNeighborBrightness` | bool | 是否使用邻居亮度 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.isReplaceable(IWorld, IBlockPos)` | bool | 检查方块是否可替换 |
| `.getLightValue(IWorld, IBlockPos)` | int | 获取指定位置的光照值 |
| `.getWeakPower(IBlockAccess, IBlockPos, IFacing)` | int | 获取弱红石信号 |
| `.getStrongPower(IBlockAccess, IBlockPos, IFacing)` | int | 获取强红石信号 |
| `.getComparatorInputOverride(IWorld, IBlockPos)` | int | 获取比较器输入覆盖 |
| `.canEntitySpawn(IEntity)` | bool | 检查实体是否可以在此方块上生成 |
| `.getActualState(IBlockAccess, IBlockPos)` | IBlockProperties | 获取实际方块属性 |
| `.getBlockHardness(IWorld, IBlockPos)` | float | 获取方块硬度 |
| `.getLightOpacy(IWorld, IBlockPos)` | int | 获取光照不透明度 |
| `.getPlayerRelativeBlockHardness(IPlayer, IWorld, IBlockPos)` | float | 获取玩家相对破坏速度 |
| `.isSideSolid(IBlockAccess, IBlockPos, IFacing)` | bool | 检查方块面是否为实体面 |

### IBlockStateMatcher（方块状态匹配器）

> `import crafttweaker.block.IBlockStateMatcher;`

IBlockStateMatcher 用于匹配 IBlockState 对象的一组要求。每个 IBlockState 也是 IBlockStateMatcher（只匹配自身）。

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `IBlockStateMatcher.create(IBlockState...)` | IBlockStateMatcher | 创建匹配器。零个参数则永不匹配；一个参数则匹配该方块的任意属性值；多个参数等同于 OR 合并 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.matches(IBlockState)` | bool | 检查方块状态是否匹配（也可用 `has` 运算符） |
| `.withMatchedValuesForProperty(string, string...)` | IBlockStateMatcher | 添加属性匹配要求（仅非复合匹配器可用） |
| `.getMatchedValuesForProperty(string)` | List\<string\> | 获取指定属性的匹配值（仅非复合匹配器可用） |
| `.getMatchedProperties()` | Map\<string, List\<string\>\> | 获取所有匹配属性（仅非复合匹配器可用） |
| `.getMatchingBlockStates()` | Collection\<IBlockState\> | 获取所有匹配的方块状态集合 |
| `.isCompound()` | bool | 检查是否为复合匹配器 |
| `commandString` | string | 获取命令字符串表示 |

#### 操作

- 可以使用 `|` 运算符合并两个 IBlockStateMatcher 得到复合匹配器
- 复合匹配器不绑定特定方块，不能使用 `withMatchedValuesForProperty`

### IMaterial（方块材质）

> `import crafttweaker.block.IMaterial;`

IMaterial 代表方块的材质。

#### 静态方法（获取原版材质）

| 方法 | 返回 | 说明 |
|------|------|------|
| `IMaterial.air()` | IMaterial | 空气 |
| `IMaterial.anvil()` | IMaterial | 铁砧 |
| `IMaterial.barrier()` | IMaterial | 屏障 |
| `IMaterial.cactus()` | IMaterial | 仙人掌 |
| `IMaterial.cake()` | IMaterial | 蛋糕 |
| `IMaterial.carpet()` | IMaterial | 地毯 |
| `IMaterial.circuits()` | IMaterial | 红石线路 |
| `IMaterial.clay()` | IMaterial | 粘土 |
| `IMaterial.cloth()` | IMaterial | 布 |
| `IMaterial.coral()` | IMaterial | 珊瑚 |
| `IMaterial.craftedSnow()` | IMaterial | 雪块 |
| `IMaterial.dragonEgg()` | IMaterial | 龙蛋 |
| `IMaterial.fire()` | IMaterial | 火 |
| `IMaterial.glass()` | IMaterial | 玻璃 |
| `IMaterial.gourd()` | IMaterial | 葫芦 |
| `IMaterial.grass()` | IMaterial | 草 |
| `IMaterial.ground()` | IMaterial | 泥土 |
| `IMaterial.ice()` | IMaterial | 冰 |
| `IMaterial.iron()` | IMaterial | 铁 |
| `IMaterial.lava()` | IMaterial | 岩浆 |
| `IMaterial.leaves()` | IMaterial | 树叶 |
| `IMaterial.packedIce()` | IMaterial | 浮冰 |
| `IMaterial.piston()` | IMaterial | 活塞 |
| `IMaterial.plants()` | IMaterial | 植物 |
| `IMaterial.portal()` | IMaterial | 传送门 |
| `IMaterial.redstoneLight()` | IMaterial | 红石灯 |
| `IMaterial.rock()` | IMaterial | 石头 |
| `IMaterial.sand()` | IMaterial | 沙子 |
| `IMaterial.snow()` | IMaterial | 雪 |
| `IMaterial.sponge()` | IMaterial | 海绵 |
| `IMaterial.structureVoid()` | IMaterial | 结构空位 |
| `IMaterial.tnt()` | IMaterial | TNT |
| `IMaterial.vine()` | IMaterial | 藤蔓 |
| `IMaterial.water()` | IMaterial | 水 |
| `IMaterial.web()` | IMaterial | 蛛网 |
| `IMaterial.wood()` | IMaterial | 木头 |

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `blocksLight` | bool | 是否阻挡光线 |
| `blocksMovement` | bool | 是否阻挡移动 |
| `canBurn` | bool | 是否可燃 |
| `mobilityFlag` | string | 移动标志 |
| `liquid` | bool | 是否液体 |
| `opaque` | bool | 是否不透明 |
| `replaceable` | bool | 是否可替换 |
| `solid` | bool | 是否固体 |
| `toolNotRequired` | bool | 是否不需要工具 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setReplaceable()` | IMaterial | 设置为可替换材质 |
| `.matches(IMaterial)` | bool | 检查两个材质是否相同 |

### IMobilityFlag（移动标志）

> `import crafttweaker.block.IMobilityFlag;`

IMobilityFlag 代表方块的移动标志（如活塞推动行为）。

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `MobilityFlag.normal()` | IMobilityFlag | 正常 |
| `MobilityFlag.destroy()` | IMobilityFlag | 被破坏 |
| `MobilityFlag.block()` | IMobilityFlag | 阻挡 |
| `MobilityFlag.ignore()` | IMobilityFlag | 忽略 |
| `MobilityFlag.pushOnly()` | IMobilityFlag | 仅推 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.matches(IMobilityFlag)` | bool | 检查两个移动标志是否相同 |
