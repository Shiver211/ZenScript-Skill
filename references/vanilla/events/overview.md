# Events CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.events.IEventManager;`

事件系统允许在游戏特定时间点执行自定义行为。通过全局 `events` 字段访问事件管理器。

---

## 基础用法

```zenscript
events.onPlayerCrafted(function(event as crafttweaker.event.PlayerCraftedEvent){
    if (!event.player.world.remote) { // !world.remote 保证服务端执行。MC 分为服务端和客户端。与存档相关的操作（召唤实体、修改方块等）必须在服务端执行。
        // 服务端逻辑
    }
});
```

**清除所有事件处理器**：`events.clear();`

**重要**：必须将 event 强制转换为正确的事件类型，否则无法访问其 ZenGetter/ZenSetter。

---

## 事件接口

### IBlockEvent

> `import crafttweaker.event.IBlockEvent;`

继承自 IEventPositionable。

| 属性 | 类型 | 说明 |
|------|------|------|
| `world` | IWorld | 世界对象 |
| `blockState` | IBlockState | 方块状态 |
| `block` | IBlock | 方块对象 |
| `position` | IBlockPos | 方块位置 |
| `x` | int | X 坐标 |
| `y` | int | Y 坐标 |
| `z` | int | Z 坐标 |

### IEntityEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `entity` | IEntity | 实体对象 |

### ILivingEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `entityLivingBase` | IEntityLivingBase | 生物实体对象 |

### IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `player` | IPlayer | 玩家对象 |

### ITickEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `phase` | string | Tick 阶段，值为 `START` 或 `END` |
| `side` | string | 执行端，值为 `CLIENT` 或 `SERVER` |

### IEventCancelable

| 属性 | 类型 | 说明 |
|------|------|------|
| `canceled` | bool | 事件是否已取消（可设置） |

方法：`event.cancel()` 取消事件。

> **两条重要行为**：
> 1. 在 CrT 收到之前就已被其它来源取消的事件，**不会再传给 handler**；
> 2. 注册了多个 handler 时，其中一个 `cancel()` **不影响其它 CrT handler 照样收到该事件** —— `cancel()` 无法用来拦截后续 handler。

### IEventHasResult

| 属性 | 类型 | 说明 |
|------|------|------|
| `result` | string | 事件结果，值为 `default`、`deny` 或 `allow` |

方法：`event.deny()` / `event.allow()` / `event.default()`

### IEventPositionable

| 属性 | 类型 | 说明 |
|------|------|------|
| `position` | IBlockPos | 位置 |
| `x` | int | X 坐标 |
| `y` | int | Y 坐标 |
| `z` | int | Z 坐标 |

### IExplosionEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `world` | IWorld | 世界对象 |
| `explosion` | IExplosion | 爆炸对象 |
| `position` | IBlockPos | 爆炸位置 |
| `x` | double | X 坐标 |
| `y` | double | Y 坐标 |
| `z` | double | Z 坐标 |

### IProjectileImpactEvent

继承自 IEntityEvent。

| 属性 | 类型 | 说明 |
|------|------|------|
| `rayTrace` | IRayTraceResult | 射线追踪结果 |
| `entity` | IEntity | 投射物实体（继承自 IEntityEvent） |

### IMinecartEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `minecart` | IEntity | 矿车实体 |

### IPotionBrewEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `length` | int | 酿造台中的物品数量 |

方法：`event.getItem(int index)` / `event.setItem(int index, IItemStack item)`

### IProcessableEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `processed` | bool | 是否已处理 |

方法：`event.process()` 将事件标记为已处理

---

## 完整事件列表

| ZenMethod | 事件类 | 说明 |
|-----------|--------|------|
| `onAllowDespawn` | EntityLivingSpawnEvent | 允许实体消失 |
| `onAnimalTame` | AnimalTameEvent | 动物驯服 |
| `onArrowLoose` | ArrowLooseEvent | 射箭 |
| `onArrowNock` | ArrowNockEvent | 搭箭 |
| `onBlockBreak` | BlockBreakEvent | 方块被破坏 |
| `onBlockHarvestDrops` | BlockHarvestDrops | 方块破坏掉落 |
| `onBlockNeighborNotify` | BlockNeighborNotifyEvent | 方块邻居通知 |
| `onBlockPlace` | BlockPlaceEvent | 方块放置 |
| `onCheckSpawn` | EntityLivingExtendedSpawnEvent | 检查实体生成 |
| `onClientTick` | ClientTickEvent | 客户端 Tick |
| `onCommand` | CommandEvent | 命令执行 |
| `onCriticalHit` | CriticalHitEvent | 暴击判定 |
| `onCropGrowPost` | CropGrowPostEvent | 作物生长后 |
| `onCropGrowPre` | CropGrowPreEvent | 作物生长前 |
| `onEnchantmentLevelSet` | EnchantmentLevelSetEvent | 附魔台等级生成 |
| `onEnderTeleport` | EnderTeleportEvent | 末影人传送 |
| `onEntityJoinWorld` | EntityJoinWorldEvent | 实体加入世界 |
| `onEntityLivingAttacked` | EntityLivingAttackedEvent | 实体被攻击 |
| `onEntityLivingDamage` | EntityLivingDamageEvent | 实体受伤（最终伤害） |
| `onEntityLivingDeath` | EntityLivingDeathEvent | 实体死亡 |
| `onEntityLivingDeathDrops` | EntityLivingDeathDropsEvent | 实体死亡掉落 |
| `onEntityLivingEquipmentChange` | EntityLivingEquipmentChangeEvent | 实体装备变化 |
| `onEntityLivingFall` | EntityLivingFallEvent | 实体摔落 |
| `onEntityLivingHeal` | EntityLivingHealEvent | 实体回血 |
| `onEntityLivingHurt` | EntityLivingHurtEvent | 实体受伤 |
| `onEntityLivingJump` | EntityLivingJumpEvent | 实体跳跃 |
| `onEntityLivingUpdate` | EntityLivingUpdateEvent | 实体更新 |
| `onEntityLivingUseItem` | EntityLivingUseItemEvent.All | 实体使用物品 |
| `onEntityLivingUseItemFinish` | EntityLivingUseItemEvent.Finish | 实体完成使用物品 |
| `onEntityLivingUseItemStart` | EntityLivingUseItemEvent.Start | 实体开始使用物品 |
| `onEntityLivingUseItemStop` | EntityLivingUseItemEvent.Stop | 实体停止使用物品 |
| `onEntityLivingUseItemTick` | EntityLivingUseItemEvent.Tick | 实体使用物品 Tick |
| `onEntityMount` | EntityMountEvent | 实体骑乘/下骑 |
| `onEntityStruckByLightning` | EntityStruckByLightningEvent | 实体被雷击 |
| `onEntityTravelToDimension` | EntityTravelToDimensionEvent | 实体跨维度传送 |
| `onExplosionDetonate` | ExplosionDetonateEvent | 爆炸引爆 |
| `onExplosionStart` | ExplosionStartEvent | 爆炸开始 |
| `onFarmlandTrample` | FarmlandTrampleEvent | 农田被踩踏 |
| `onItemExpire` | ItemExpireEvent | 物品实体过期 |
| `onItemFished` | ItemFishedEvent | 钓鱼获得物品 |
| `onItemToss` | ItemTossEvent | 丢弃物品 |
| `onLivingDestroyBlock` | LivingDestroyBlockEvent | 生物破坏方块 |
| `onLivingExperienceDrop` | LivingExperienceDropEvent | 生物死亡掉落经验 |
| `onLivingKnockBack` | LivingKnockBackEvent | 生物被击退 |
| `onLootingLevel` | LootingLevelEvent | 抢夺等级判定 |
| `onMinecartCollision` | MinecartCollisionEvent | 矿车碰撞 |
| `onMinecartInteract` | MinecartInteractEvent | 矿车交互 |
| `onMobGriefing` | MobGriefingEvent | 生物破坏 |
| `onPlayerAdvancement` | PlayerAdvancementEvent | 玩家获得进度 |
| `onPlayerAnvilRepair` | PlayerAnvilRepairEvent | 玩家铁砧修复 |
| `onPlayerAnvilUpdate` | PlayerAnvilUpdateEvent | 玩家铁砧更新 |
| `onPlayerAttackEntity` | PlayerAttackEntityEvent | 玩家攻击实体 |
| `onPlayerBonemeal` | PlayerBonemealEvent | 玩家使用骨粉 |
| `onPlayerBreakSpeed` | PlayerBreakSpeed | 玩家挖掘速度 |
| `onPlayerBrewedPotion` | PlayerBrewedPotion | 玩家酿造药水 |
| `onPlayerChangedDimension` | PlayerChangedDimensionEvent | 玩家切换维度 |
| `onPlayerClone` | PlayerCloneEvent | 玩家克隆 |
| `onPlayerCloseContainer` | PlayerCloseContainerEvent | 玩家关闭容器 |
| `onPlayerCrafted` | PlayerCraftedEvent | 玩家合成物品 |
| `onPlayerDeathDrops` | PlayerDeathDropsEvent | 玩家死亡掉落 |
| `onPlayerDestroyItem` | PlayerDestroyItem | 玩家物品损坏 |
| `onPlayerFillBucket` | PlayerFillBucketEvent | 玩家填充桶 |
| `onPlayerInteract` | PlayerInteractEvent | 玩家交互 |
| `onPlayerInteractBlock` | PlayerInteractBlockEvent | 玩家右键方块 |
| `onPlayerInteractEntity` | PlayerInteractEntityEvent | 玩家右键实体 |
| `onPlayerItemPickup` | PlayerItemPickupEvent | 玩家拾取物品 |
| `onPlayerLeftClickBlock` | PlayerLeftClickBlockEvent | 玩家左键方块 |
| `onPlayerLoggedIn` | PlayerLoggedInEvent | 玩家登录 |
| `onPlayerLoggedOut` | PlayerLoggedOutEvent | 玩家登出 |
| `onPlayerOpenContainer` | PlayerOpenContainerEvent | 玩家打开容器 |
| `onPlayerPickupItem` | PlayerPickupItemEvent | 玩家拾取物品实体 |
| `onPlayerPickupXp` | PlayerPickupXpEvent | 玩家拾取经验 |
| `onPlayerRespawn` | PlayerRespawnEvent | 玩家重生 |
| `onPlayerRightClickItem` | PlayerRightClickItemEvent | 玩家右键物品 |
| `onPlayerSetSpawn` | PlayerSetSpawn | 玩家设置重生点 |
| `onPlayerSleepInBed` | PlayerSleepInBedEvent | 玩家睡觉 |
| `onPlayerSmelted` | PlayerSmeltedEvent | 玩家熔炼物品 |
| `onPlayerTick` | PlayerTickEvent | 玩家 Tick |
| `onPlayerUseHoe` | PlayerUseHoeEvent | 玩家使用锄 |
| `onPlayerVisibility` | PlayerVisibilityEvent | 玩家可见性 |
| `onPortalSpawn` | PortalSpawnEvent | 传送门生成 |
| `onPotionBrewPost` | PotionBrewPostEvent | 药水酿造后 |
| `onPotionBrewPre` | PotionBrewPreEvent | 药水酿造前 |
| `onProjectileImpactArrow` | ProjectileImpactArrowEvent | 箭矢命中 |
| `onProjectileImpactFireball` | ProjectileImpactFireballEvent | 火球命中 |
| `onProjectileImpactThrowable` | ProjectileImpactThrowableEvent | 投掷物命中 |
| `onRenderTick` | RenderTickEvent | 渲染 Tick |
| `onServerTick` | ServerTickEvent | 服务端 Tick |
| `onSleepingLocationCheck` | SleepingLocationCheckEvent | 睡觉位置检查 |
| `onSleepingTimeCheck` | SleepingTimeCheckEvent | 睡觉时间检查 |
| `onSpecialSpawn` | EntityLivingExtendedSpawnEvent | 特殊生成 |
| `onWorldTick` | WorldTickEvent | 世界 Tick |
