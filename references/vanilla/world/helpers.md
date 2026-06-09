# 世界辅助对象 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.world.IBlockAccess;`、`import crafttweaker.world.IExplosion;`、`import crafttweaker.world.IRayTraceResult;`、`import crafttweaker.world.IVector3d;`

IBlockAccess、IExplosion、IRayTraceResult、IVector3d 辅助对象。

---

## API 列表

### IBlockAccess（方块访问接口）

> `import crafttweaker.world.IBlockAccess;`

IBlockAccess 是 IWorld 的父接口，所有 IWorld 对象都可以使用 IBlockAccess 的方法。

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getBlockState(IBlockPos)` | IBlockState | 获取指定位置的方块状态 |
| `.isAirBlock(IBlockPos)` | bool | 检查指定位置是否为空气方块 |
| `.getStrongPower(IBlockPos, IFacing)` | int | 获取指定方块面的强红石信号 |

### IExplosion（爆炸）

> `import crafttweaker.world.IExplosion;`

IExplosion 代表一个爆炸对象，可以通过 IWorld 的 `createExplosion` 或 `performExplosion` 获取。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `world` | IWorld | 爆炸所在的世界 |
| `placedBy` | IEntityLivingBase | 发起爆炸的实体（TNT 则为放置 TNT 的实体，可能为 null） |
| `position` | Position3f | 爆炸位置 |
| `affectedBlockPositions` | IBlockPos[] | 爆炸影响的方块位置列表（`doExplosionA()` 调用前可能为空，可读写） |
| `playerKnockbackMap` | Map\<IPlayer, IVector3d\> | 爆炸区域内玩家的击退映射 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.clearAffectedBlockPositions()` | void | 清除受影响方块位置列表 |
| `.onExplosionStart(IWorld)` | bool | 触发 ExplosionStart 事件，可用于在手动引爆前取消爆炸 |
| `.doExplosionA()` | void | 执行爆炸第一部分：实体伤害、击退、赋值 affectedBlockPositions |
| `.doExplosionB(bool)` | void | 执行爆炸第二部分：声音、粒子、方块破坏/掉落、火焰生成 |

### IRayTraceResult（射线检测结果）

> `import crafttweaker.world.IRayTraceResult;`

IRayTraceResult 代表玩家视线或点击的射线检测结果。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `isMiss` | bool | 是否未命中 |
| `isBlock` | bool | 是否命中方块 |
| `blockPos` | IBlockPos | 命中的方块位置（非 bool 返回值可为 null） |
| `sideHit` | IFacing | 命中的方块面（非 bool 返回值可为 null） |

#### 已弃用 @ZenGetter

以下 getter 已弃用，因为当前没有可靠的方式同时射线检测方块和实体。

| 属性 | 类型 | 说明 |
|------|------|------|
| `isEntity` | bool | 是否命中实体（始终返回 `false`） |
| `entity` | IEntity | 命中的实体（始终返回 `null`） |

### IVector3d（三维向量）

> `import crafttweaker.world.IVector3d;`

IVector3d 是一个使用三个 double 表示方向的向量对象。

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `IVector3d.create(double, double, double)` | IVector3d | 创建新的 IVector3d 对象 |

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `x` | double | X 分量 |
| `y` | double | Y 分量 |
| `z` | double | Z 分量 |
| `normalized` | IVector3d | 归一化后的向量 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.dotProduct(IVector3d)` | double | 点积 |
| `.crossProduct(IVector3d)` | IVector3d | 叉积 |
| `.subtract(IVector3d)` | IVector3d | 向量减法 |
| `.subtractReverse(IVector3d)` | IVector3d | 反向减法（other - this） |
| `.add(IVector3d)` | IVector3d | 向量加法 |
| `.distanceTo(IVector3d)` | double | 到另一个向量的距离 |
| `.scale(double)` | IVector3d | 缩放向量 |
