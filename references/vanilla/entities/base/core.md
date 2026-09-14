# 实体基础 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.entity.IEntity;`

IEntity 核心 API。

---

## API 列表

### IEntity（实体）

> `import crafttweaker.entity.IEntity;`

IEntity 继承 ICommandSender，因此 ICommandSender 的所有方法也可用于 IEntity 对象。

ICommandSender 派生方法：`entity.displayName`、`entity.position`、`entity.world`、`entity.server`、`entity.sendMessage(String text)`

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `air` | int | 剩余空气值 |
| `alive` | boolean | 是否存活 |
| `alwaysRenderNameTag` | boolean | 是否始终渲染名称标签 |
| `armorInventory` | List<IItemStack> | 盔甲物品列表 |
| `canBeAttackedWithItem` | boolean | 是否可被物品攻击 |
| `canBeCollidedWith` | boolean | 是否可被碰撞 |
| `canPassengerSteer` | boolean | 乘客是否可操控 |
| `canRiderInteract` | boolean | 骑乘者是否可交互 |
| `controllingPassenger` | IEntity | 控制乘客 |
| `customName` | string | 自定义名称 |
| `definition` | IEntityDefinition | 实体定义 |
| `dimension` | int | 维度 ID |
| `doesTriggerPressurePlate` | boolean | 是否触发压力板 |
| `equipmentAndArmor` | List<IItemStack> | 装备和盔甲物品列表 |
| `eyeHeight` | float | 眼睛高度 |
| `hasCustomName` | boolean | 是否有自定义名称 |
| `hasNoGravity` | boolean | 是否无重力 |
| `heldEquipment` | List<IItemStack> | 手持物品列表 |
| `id` | int | 实体 ID |
| `immuneToFire` | boolean | 是否免疫火焰 |
| `isBeingRidden` | boolean | 是否被骑乘 |
| `isBoss` | boolean | 是否为 Boss |
| `isBurning` | boolean | 是否着火 |
| `isGlowing` | boolean | 是否发光 |
| `isImmuneToExplosions` | boolean | 是否免疫爆炸 |
| `isInLava` | boolean | 是否在岩浆中 |
| `isInsideOpaqueBlock` | boolean | 是否在不透明方块内 |
| `isInvisible` | boolean | 是否隐身 |
| `isInvulnerable` | boolean | 是否无敌 |
| `isInWater` | boolean | 是否在水中 |
| `isLightningbolt` | boolean | 是否为闪电 |
| `isOutsideBorder` | boolean | 是否在世界边界外 |
| `isOverWater` | boolean | 是否在水面上方 |
| `isPushedByWater` | boolean | 是否被水推动 |
| `isRiding` | boolean | 是否骑乘 |
| `isSilent` | boolean | 是否静音 |
| `isSneaking` | boolean | 是否潜行 |
| `isSprinting` | boolean | 是否冲刺 |
| `lowestRidingEntity` | IEntity | 最底层骑乘实体 |
| `maxFallHeight` | int | 最大掉落高度 |
| `maxInPortalTime` | int | 最大传送门停留时间 |
| `nbt` | IData | NBT 数据 |
| `onGround` | boolean | 是否在地面上 |
| `parts` | IEntity[] | 实体部件数组 |
| `passengers` | List<IEntity> | 乘客列表 |
| `passengersRecursive` | List<IEntity> | 递归乘客列表 |
| `portalCooldown` | int | 传送门冷却时间 |
| `position3f` | Position3f | 精确位置 |
| `ridingEntity` | IEntity | 骑乘的实体 |
| `shouldRiderSit` | boolean | 骑乘者是否坐下 |
| `tags` | List<string> | 标签列表 |
| `team` | ITeam | 所属队伍 |
| `wet` | boolean | 是否潮湿 |
| `world` | IWorld | 所在世界 |
| `x` / `y` / `z` | double | 坐标 |
| `motionX` / `motionY` / `motionZ` | double | 速度 |
| `posX` / `posY` / `posZ` | double | 精确坐标 |
| `rotationYaw` | float | 偏航角 |
| `rotationPitch` | float | 俯仰角 |
| `lookingDirection` | IVector3d | 视线方向 |
| `horizontalFacing` | IFacing | 水平朝向 |
| `updateBlocked` | boolean | 是否阻止更新 |
| `inPortal` | boolean | 是否在传送门中 |
| `portalCounter` | int | 传送门计数器 |
| `lastPortalVec` | IVector3d | 上次传送门向量 |
| `lastPortalPos` | IBlockPos | 上次传送门位置 |
| `lastPortalDirection` | IFacing | 上次传送门方向 |

#### @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `air` | int | 设置剩余空气值 |
| `alwaysRenderNameTag` | boolean | 设置是否始终渲染名称标签 |
| `customName` | string | 设置自定义名称 |
| `dimension` | int | 设置维度 ID |
| `fire` | int | 设置着火时间 |
| `hasNoGravity` | boolean | 设置无重力 |
| `id` | int | 设置实体 ID |
| `isGlowing` | boolean | 设置发光 |
| `isInvisible` | boolean | 设置隐身 |
| `isOutsideBorder` | boolean | 设置是否在世界边界外 |
| `isSilent` | boolean | 设置静音 |
| `isSneaking` | boolean | 设置潜行 |
| `isSprinting` | boolean | 设置冲刺 |
| `position` | IBlockPos | 设置位置 |
| `rotationYaw` | float | 设置偏航角 |
| `rotationPitch` | float | 设置俯仰角 |
| `motionX` / `motionY` / `motionZ` | double | 设置速度 |
| `posX` / `posY` / `posZ` | double | 设置精确坐标 |
| `nbt` | IData | 设置 NBT 数据 |
| `team` | ITeam | 设置所属队伍 |
| `updateBlocked` | boolean | 设置是否阻止更新 |
| `inPortal` | boolean | 设置是否在传送门中 |
| `portalCounter` | int | 设置传送门计数器 |
| `lastPortalVec` | IVector3d | 设置上次传送门向量 |
| `lastPortalPos` | IBlockPos | 设置上次传送门位置 |
| `lastPortalDirection` | IFacing | 设置上次传送门方向 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.attackEntityFrom(IDamageSource, float)` | boolean | 对实体造成伤害 |
| `.canTrample(IWorld, IBlockDefinition, IBlockPos, float)` | boolean | 检查是否可以踩踏 |
| `.isInsideOfMaterial(IMaterial)` | boolean | 检查是否在指定材质内 |
| `.getDistanceSqToEntity(IEntity)` | double | 获取到指定实体的距离平方 |
| `.getPickedResult()` | IItemStack | 获取拾取结果物品 |
| `.addTag(String)` | void | 添加标签 |
| `.extinguish()` | void | 熄灭火焰 |
| `.onEntityUpdate()` | void | 实体更新回调 |
| `.onKillCommand()` | void | 执行 kill 命令回调 |
| `.onUpdate()` | void | 更新回调 |
| `.removeTag(String)` | void | 移除标签 |
| `.setDead()` | void | 杀死实体 |
| `.spawnRunningParticles()` | void | 生成奔跑粒子效果 |
| `.removePassengers()` | void | 移除所有乘客 |
| `.dismountRidingEntity()` | void | 取消骑乘 |
| `.isOnSameTeam(IEntity)` | boolean | 检查是否在同一队伍 |
| `.setInWeb()` | void | 设为被蜘蛛网缠住 |
| `.doWaterSplashEffect()` | void | 执行水花效果 |
| `.isEntityEqual(IEntity)` | boolean | 检查实体是否相等 |
| `.isInvulnerableTo(IDamageSource)` | boolean | 检查是否对指定伤害来源无敌 |
| `.shouldRiderDismountInWater(IEntity)` | boolean | 检查骑乘者是否应在水中下马 |
| `.isPassenger(IEntity)` | boolean | 检查是否为乘客 |
| `.isRidingSameEntity(IEntity)` | boolean | 检查是否骑乘同一实体 |
| `.getRayTrace(double, float, @Optional boolean, @Optional boolean, @Optional(valueBoolean = true) boolean)` | IRayTraceResult | 射线追踪。参数依次为（距离, 局部刻, 命中流体即停, 忽略无碰撞箱方块, **返回最后未碰撞方块——默认 true**） |
