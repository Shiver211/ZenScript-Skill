# 战斗事件 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.event.*;`

箭矢、暴击、投射物命中、爆炸等战斗相关事件。

---

## API 列表

### 箭矢事件

#### ArrowLooseEvent（射箭事件）

> `import crafttweaker.event.ArrowLooseEvent;`

玩家松开弓弦（射箭）时触发。可取消。

实现接口：IPlayerEvent, IEventCancelable

| 属性 | 类型 | 说明 |
|------|------|------|
| `bow` | IItemStack | 弓 |
| `charge` | int | 蓄力值（可设置） |
| `world` | IWorld | 世界对象 |
| `player` | IPlayer | 射箭的玩家 |

#### ArrowNockEvent（搭箭事件）

> `import crafttweaker.event.ArrowNockEvent;`

玩家开始拉弓（搭箭）时触发。

实现接口：IPlayerEvent, IEventHasResult

| 属性 | 类型 | 说明 |
|------|------|------|
| `bow` | IItemStack | 弓 |
| `hand` | string | 使用的手 |
| `player` | IPlayer | 玩家 |
| `result` | string | 事件结果 |

方法：
- `event.deny()` / `event.allow()` / `event.default()`

### 暴击事件

#### CriticalHitEvent（暴击判定事件）

> `import crafttweaker.event.CriticalHitEvent;`

玩家攻击时暴击判定触发。

实现接口：IPlayerEvent, IEventHasResult

| 属性 | 类型 | 说明 |
|------|------|------|
| `target` | IEntity | 攻击目标 |
| `oldDamageModifier` | float | 原始伤害倍率 |
| `damageModifier` | float | 伤害倍率（可设置） |
| `isVanillaCrit` | boolean | 是否原版暴击 |
| `player` | IPlayer | 攻击的玩家 |
| `result` | string | 当前判定结果，值为 `default` / `deny` / `allow` |

方法：
- `event.deny()` 阻止暴击
- `event.allow()` 强制暴击
- `event.default()` 使用原版行为

### 爆炸事件

#### ExplosionStartEvent（爆炸开始事件）

> `import crafttweaker.event.ExplosionStartEvent;`

爆炸即将开始时触发。可取消以阻止爆炸。

实现接口：IExplosionEvent, IEventCancelable

继承自 IExplosionEvent 的 `world`、`explosion`、`position`、`x`、`y`、`z`。

#### ExplosionDetonateEvent（爆炸引爆事件）

> `import crafttweaker.event.ExplosionDetonateEvent;`

爆炸引爆时触发。不可取消。

实现接口：IExplosionEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `affectedEntities` | IEntity[] | 受影响的实体列表 |
| `affectedPositions` | IBlockPos[] | 受影响的位置列表 |

继承自 IExplosionEvent 的 `world`、`explosion`、`position`、`x`、`y`、`z`。

### 投射物命中事件

#### ProjectileImpactArrowEvent（箭矢命中事件）

> `import crafttweaker.event.ProjectileImpactArrowEvent;`

箭矢命中实体时触发（在伤害计算之前）。可取消以阻止命中处理。可通过属性调整伤害、击退力度、拾取状态和暴击判定。

实现接口：IProjectileImpactEvent, IEventCancelable

| 属性 | 类型 | 说明 |
|------|------|------|
| `arrow` | IEntity | 箭矢实体 |
| `shooter` | IEntity | 射手实体 |
| `damage` | double | 伤害值（可设置） |
| `knockbackStrength` | int | 击退力度（仅可设置） |
| `isCritical` | boolean | 是否暴击（可设置） |
| `pickupStatus` | string | 拾取状态 |
| `rayTrace` | IRayTraceResult | 射线追踪结果 |

方法：
- `event.setPickupDisallowed()` 禁止拾取箭矢
- `event.setPickupAllowed()` 允许拾取箭矢
- `event.setPickupCreative()` 仅在创造模式下允许拾取

继承自 IProjectileImpactEvent 的 `entity`（IEntity）。

#### ProjectileImpactFireballEvent（火球命中事件）

> `import crafttweaker.event.ProjectileImpactFireballEvent;`

火球命中实体时触发（在伤害计算之前）。可取消。

实现接口：IProjectileImpactEvent, IEventCancelable

| 属性 | 类型 | 说明 |
|------|------|------|
| `fireball` | IEntity | 火球实体 |
| `shooter` | IEntityLivingBase | 射手实体 |
| `accelerationX` | double | X 轴加速度（可设置） |
| `accelerationY` | double | Y 轴加速度（可设置） |
| `accelerationZ` | double | Z 轴加速度（可设置） |
| `rayTrace` | IRayTraceResult | 射线追踪结果 |

继承自 IProjectileImpactEvent 的 `entity`（IEntity）。

#### ProjectileImpactThrowableEvent（投掷物命中事件）

> `import crafttweaker.event.ProjectileImpactThrowableEvent;`

投掷物（如雪球、末影珍珠）命中时触发（在伤害计算之前）。可取消。

实现接口：IProjectileImpactEvent, IEventCancelable

| 属性 | 类型 | 说明 |
|------|------|------|
| `throwable` | IEntityThrowable | 投掷物实体 |
| `thrower` | IEntityLivingBase | 投掷者 |
| `rayTrace` | IRayTraceResult | 射线追踪结果 |

继承自 IProjectileImpactEvent 的 `entity`（IEntity）。
