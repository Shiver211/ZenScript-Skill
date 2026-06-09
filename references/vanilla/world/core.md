# 世界核心 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 部分扩展需要 Roids-Tweaker 或 ZenUtils
> 导入: `import crafttweaker.world.IWorld;`、`import crafttweaker.world.IBlockPos;`、`import crafttweaker.world.IFacing;`

IWorld、IBlockPos、IFacing 核心 API。

---

## API 列表

### IWorld（世界）

> `import crafttweaker.world.IWorld;`

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `IWorld.getFromID(int)` | IWorld | 通过维度 ID 获取世界对象（需在游戏运行时调用，不可在加载阶段使用） |

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `remote` | bool | 是否为客户端 |
| `dimension` | int | 维度 ID |
| `dimensionName` | string | 维度名称 |
| `worldType` | string | 世界类型 |
| `difficulty` | string | 难度 |
| `dayTime` | bool | 是否白天 |
| `raining` | bool | 是否下雨 |
| `isThundering` | bool | 是否雷暴 |
| `rainStrength` | float | 雨强度 |
| `thunderStrength` | float | 雷强度 |
| `time` | long | 世界时间 |
| `totalTime` | long | 总时间 |
| `seed` | long | 种子 |
| `seaLevel` | int | 海平面高度 |
| `spawnPoint` | IBlockPos | 出生点 |
| `borderCenterX` | double | 边界中心 X |
| `borderCenterZ` | double | 边界中心 Z |
| `borderSize` | double | 边界大小 |
| `borderDamagePerBlock` | double | 边界每格伤害 |
| `borderWarningDistance` | double | 边界警告距离 |
| `borderWarningTime` | double | 边界警告时间 |
| `maxHeight` | int | 最大高度 |
| `minHeight` | int | 最小高度 |
| `surfaceWorld` | bool | 是否为地表世界 |
| `moonPhase` | int | 当前月相 |
| `dimensionType` | string | 维度类型名称 |
| `worldInfo` | IWorldInfo | 世界信息对象，可获取更详细的属性 |
| `provider` | IWorldProvider | 世界维度提供者对象，可获取更详细的维度信息 |
| `random` | IRandom | 世界随机生成器 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getBlock(IBlockPos)` | IBlock | 获取方块 |
| `.getBlock(int, int, int)` | IBlock | 获取方块 |
| `.setBlockState(IBlockState, IBlockPos)` | bool | 设置方块状态 |
| `.setBlockState(IBlockState, IData, IBlockPos)` | bool | 设置方块状态并指定 TileEntity NBT 数据 |
| `.getBlockState(IBlockPos)` | IBlockState | 获取方块状态 |
| `.getBlockState(int, int, int)` | IBlockState | 获取方块状态 |
| `.getTileEntity(IBlockPos)` | ITileEntity | 获取 Tile Entity |
| `.getLightFor(EnumSkyBlock, IBlockPos)` | int | 获取光照 |
| `.getLightValue(IBlockPos)` | int | 获取光照值 |
| `.getBrightness(int, int, int)` | int | 获取指定坐标的亮度 |
| `.getBrightness(IBlockPos)` | int | 获取指定位置的亮度 |
| `.isAirBlock(IBlockPos)` | bool | 是否空气方块 |
| `.isBlockLoaded(IBlockPos)` | bool | 方块是否已加载 |
| `.isBlockModifiable(IPlayer, IBlockPos)` | bool | 玩家是否可修改方块 |
| `.canSeeSky(IBlockPos)` | bool | 是否能看到天空 |
| `.canBlockSeeSky(IBlockPos)` | bool | 方块是否能看到天空 |
| `.isBlockNormalCube(IBlockPos)` | bool | 是否普通方块 |
| `.isBlockFullCube(IBlockPos)` | bool | 是否完整方块 |
| `.isBlockFullBlock(IBlockPos)` | bool | 是否完整方块 |
| `.getStrongPower(IBlockPos, IFacing)` | int | 获取强红石信号 |
| `.getRedstonePower(IBlockPos, IFacing)` | int | 获取红石信号 |
| `.isBlockIndirectlyGettingPowered(IBlockPos)` | bool | 是否间接被红石充能 |
| `.createExplosion(IEntity, double, double, double, float, bool, bool)` | IExplosion | 创建爆炸对象（不执行）。参数：放置者、坐标、大小、是否引火、是否破坏地形 |
| `.performExplosion(IEntity, double, double, double, float, bool, bool)` | IExplosion | 创建并执行爆炸。参数：放置者、坐标、大小、是否引火、是否破坏地形 |
| `.performExplosion(IExplosion)` | IExplosion | 执行已有的爆炸对象 |
| `.createLightningBolt(double, double, double, @Optional bool)` | IEntity | 在指定坐标创建闪电 |
| `.addWeatherEffect(IEntity)` | bool | 添加天气效果 |
| `.spawnEntity(IEntity)` | bool | 生成实体 |
| `.removeEntity(IEntity)` | bool | 移除实体 |
| `.removeEntity(int)` | void | 移除实体 |
| `.getEntity(int)` | IEntity | 获取实体 |
| `.getEntitiesWithinAABB(Class, IAxisAlignedBB)` | List | 获取 AABB 内实体 |
| `.addBlockEvent(IBlockPos, Block, int, int)` | void | 添加方块事件 |
| `.getBiome(IBlockPos)` | IBiome | 获取生物群系 |
| `.getBiome(IPosition3f)` | IBiome | 获取生物群系 |
| `.getTopSolidBlock(IBlockPos)` | IBlockPos | 获取最高实心方块 |
| `.getActualHeight()` | int | 获取实际高度 |
| `.getChunks()` | List | 获取所有区块 |
| `.getPlayers(Class, Predicate)` | List | 获取玩家列表 |
| `.getGameRules()` | IGameRules | 获取游戏规则 |
| `.setRainStrength(float)` | void | 设置雨强度 |
| `.setThunderStrength(float)` | void | 设置雷强度 |
| `.setTime(long)` | void | 设置时间 |
| `.setTotalTime(long)` | void | 设置总时间 |
| `.setSpawnPoint(IBlockPos)` | void | 设置出生点 |
| `.setBlock(int, int, int, Block)` | void | 设置方块 |
| `.setBlock(int, int, int, Block, int, int)` | void | 设置方块 |
| `.destroyBlock(IBlockPos, bool)` | bool | 破坏方块 |
| `.destroyBlock(int, int, int, bool)` | bool | 破坏方块 |
| `.playSound(IPlayer, IBlockPos, string, string, float, float)` | void | 播放声音 |
| `.playSound(IPlayer, double, double, double, string, string, float, float)` | void | 播放声音 |
| `.spawnParticle(string, double, double, double, double, double, double, int...)` | void | 生成粒子 |
| `.rayTraceBlocks(IVector3d, IVector3d, @Optional bool, @Optional bool, @Optional(true) bool)` | IRayTraceResult | 射线检测。第一个向量为起点，第二个为方向和长度。最后三个参数：是否在液体处停止、是否忽略无碰撞箱方块、是否返回最后不可碰撞方块 |
| `.getPickedBlock(IBlockPos, IRayTraceResult, IPlayer)` | IItemStack | 获取拾取的方块物品（可返回 null） |
| `.isSpawnChunk(int, int)` | bool | 检查是否为出生区块 |
| `.extinguishFire(IPlayer, IBlockPos, IFacing)` | bool | 扑灭火焰 |

### Roids-Tweaker IWorld 扩展

可在任何 IWorld 上调用。

| 方法 | 参数 | 返回 | 说明 |
|------|------|------|------|
| `.setOrCreateGameRule(String key, String value)` | key, value | void | 设置游戏规则，不存在则创建 |
| `.getGameRules()` | 无 | IGameRules | 获取游戏规则对象 |
| `.playSound(String soundResourceLocation, String soundCategory, Position3f location, float volume, float pitch, @Optional bool distanceDelay)` | sound, category, location, volume, pitch, distanceDelay | void | 在指定位置播放声音 |
| `.canSeeSky(IBlockPos pos)` | pos | bool | 指定位置是否能看到天空 |
| `.canSnowAt(IBlockPos pos, bool lightCheck)` | pos, lightCheck | bool | 指定位置是否可降雪 |
| `.canBlockFreeze(IBlockPos pos, bool noWaterAdj)` | pos, noWaterAdj | bool | 指定位置方块是否可冻结 |
| `.getBlockLight(IBlockPos pos)` | pos | int | 获取方块光照 |
| `.getEntityItemsWithinAABB(IAxisAlignedBB aabb)` | aabb | IEntityItem[] | 获取 AABB 内的物品实体 |
| `.getEntitiesWithinAABB(IAxisAlignedBB aabb, @Optional IEntityDefinition entityType)` | aabb, entityType | IEntity[] | 获取 AABB 内的实体 |
| `.getSkyLight(IBlockPos pos)` | pos | int | 获取天空光照 |
| `.getTopBlock(IBlockPos pos)` | pos | IBlockPos | 获取最高方块 |
| `.getTopSolidBlock(IBlockPos pos)` | pos | IBlockPos | 获取最高实心方块 |
| `.setSpawnerEntity(IBlockPos pos, IEntityLivingBase entity)` | pos, entity | bool | 设置刷怪笼实体 |
| `.createEntityXp(int value)` | value | IEntityXp | 创建经验球实体 |
| `.spawnEntityXp(int value, IBlockPos pos)` | value, pos | void | 在指定位置生成经验球 |

### IBlockPos（方块位置）

> `import crafttweaker.world.IBlockPos;`

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `x` | int | X 坐标 |
| `y` | int | Y 坐标 |
| `z` | int | Z 坐标 |

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `IBlockPos.fromLong(long)` | IBlockPos | 从 long 转换为方块位置 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.add(int, int, int)` | IBlockPos | 坐标加法 |
| `.add(IBlockPos)` | IBlockPos | 坐标加法 |
| `.subtract(IBlockPos)` | IBlockPos | 坐标减法 |
| `.north()` | IBlockPos | 北方坐标 |
| `.south()` | IBlockPos | 南方坐标 |
| `.east()` | IBlockPos | 东方坐标 |
| `.west()` | IBlockPos | 西方坐标 |
| `.up()` | IBlockPos | 上方坐标 |
| `.down()` | IBlockPos | 下方坐标 |
| `.offset(IFacing)` | IBlockPos | 指定方向坐标 |
| `.offset(IFacing, int)` | IBlockPos | 指定方向指定距离坐标 |
| `.distanceSq(IBlockPos)` | double | 距离平方 |
| `.distanceSq(double, double, double)` | double | 距离平方 |
| `.distanceSqToCenter(double, double, double)` | double | 到中心点距离平方 |
| `.withinDistance(IBlockPos, double)` | bool | 是否在指定距离内 |
| `.getX()` | int | 获取 X |
| `.getY()` | int | 获取 Y |
| `.getZ()` | int | 获取 Z |
| `.toLong()` | long | 转为 long |
| `.asPosition3f` | IPosition3f | 转为 Position3f 对象 |
| `.getOffset(IFacing, int)` | IBlockPos | 获取指定方向偏移指定距离后的新位置 |

### IFacing（方向）

> `import crafttweaker.world.IFacing;`

IFacing 代表一个方向（北、南、东、西、上、下）。

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `IFacing.north()` | IFacing | 获取北方向 |
| `IFacing.east()` | IFacing | 获取东方向 |
| `IFacing.south()` | IFacing | 获取南方向 |
| `IFacing.west()` | IFacing | 获取西方向 |
| `IFacing.down()` | IFacing | 获取下方向 |
| `IFacing.up()` | IFacing | 获取上方向 |
| `IFacing.fromString(string)` | IFacing | 从字符串获取方向（如 "NORTH"） |
| `IFacing.getDirectionFromEntityLiving(IBlockPos, IEntityLivingBase)` | IFacing | 根据实体位置获取方向（常用于确定方块放置朝向，可返回 UP/DOWN） |

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `name` | string | 方向名称 |
| `rotateY` | IFacing | 绕 Y 轴旋转后的方向 |
| `opposite` | IFacing | 反方向 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.compare(IFacing)` | int | 比较两个方向，相等返回 0 |

---

## ContentTweaker 扩展（需安装 ContentTweaker）

> `import mods.contenttweaker.World;`
> `import mods.contenttweaker.Random;`

CoT 脚本中使用的世界和随机数类型。

### IWorld（CoT 世界）

> `import mods.contenttweaker.World;`

ContentTweaker 回调函数中的世界对象。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `remote` | bool | 是否为客户端 |
| `raining` | bool | 是否下雨 |
| `thundering` | bool | 是否雷暴 |
| `moonPhase` | int | 当前月相 |
| `dayTime` | bool | 是否白天 |
| `time` | long | 世界时间 |
| `dimension` | int | 维度 ID |
| `surfaceWorld` | bool | 是否地表世界 |
| `dimensionType` | string | 维度类型名称 |
| `random` | CTRandom | 随机数生成器 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getBiome(IBlockPos)` | IBiome | 获取生物群系 |
| `.setBlockState(ICTBlockState, IBlockPos)` | bool | 设置方块状态 |
| `.getBlockState(IBlockPos)` | ICTBlockState | 获取方块状态 |

### 声音括号处理器

| 括号 | 返回 | 说明 |
|------|------|------|
| `<soundevent:name>` | ISoundEventDefinition | 获取声音事件 |
| `<soundtype:name>` | ISoundTypeDefinition | 获取声音类型 |

**SoundEvent 格式**:
- 原版: `<soundevent:ambient.cave>`
- 模组: `<soundevent:modid:name>`
