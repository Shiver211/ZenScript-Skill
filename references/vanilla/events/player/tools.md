# 玩家工具使用事件 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.event.*;`

玩家使用锄头、挖掘、骨粉、酿造、睡觉、获得进度等事件。

---

## API 列表

### PlayerUseHoeEvent（玩家使用锄头事件）

> `import crafttweaker.event.PlayerUseHoeEvent;`

玩家使用锄头时触发。可取消。

实现接口：IEventCancelable, IProcessableEvent, IEventPositionable, IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `world` | IWorld | 世界对象 |
| `block` | IBlock | 方块对象 |
| `blockState` | IBlockState | 方块状态 |
| `dimension` | int | 维度 ID |
| `item` | IItemStack | 手持物品（锄头） |
| `player` | IPlayer | 使用锄头的玩家 |
| `result` | string | 事件结果（可设置），值为 `default`、`deny` 或 `allow` |

方法：
- `event.deny()` 设置结果为 `deny`
- `event.allow()` 设置结果为 `allow`
- `event.default()` 设置结果为 `default`

继承自 IEventPositionable 的 `position`、`x`、`y`、`z`；继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerBreakSpeedEvent（玩家挖掘速度事件）

> `import crafttweaker.event.PlayerBreakSpeedEvent;`

玩家尝试破坏方块时计算挖掘速度触发。可取消以阻止破坏。

实现接口：IEventCancelable, IPlayerEvent, IEventPositionable

| 属性 | 类型 | 说明 |
|------|------|------|
| `blockState` | IBlockState | 方块状态 |
| `block` | IBlock | 方块对象 |
| `originalSpeed` | float | 原始挖掘速度 |
| `newSpeed` | float | 新挖掘速度（可设置） |
| `player` | IPlayer | 挖掘的玩家 |

继承自 IEventPositionable 的 `position`、`x`、`y`、`z`；继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerBonemealEvent（玩家使用骨粉事件）

> `import crafttweaker.event.PlayerBonemealEvent;`

玩家在方块上使用骨粉时触发。可取消。

实现接口：IEventCancelable, IProcessableEvent, IEventPositionable, IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `world` | IWorld | 世界对象 |
| `block` | IBlock | 方块对象 |
| `blockState` | IBlockState | 方块状态 |
| `dimension` | int | 维度 ID |
| `item` | IItemStack | 手持物品（骨粉） |
| `player` | IPlayer | 使用骨粉的玩家 |
| `result` | string | 事件结果（可设置），值为 `default`、`deny` 或 `allow` |

方法：
- `event.deny()` 设置结果为 `deny`
- `event.allow()` 设置结果为 `allow`
- `event.default()` 设置结果为 `default`

继承自 IEventPositionable 的 `position`、`x`、`y`、`z`；继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerBrewedPotionEvent（玩家酿造药水事件）

> `import crafttweaker.event.PlayerBrewedPotionEvent;`

玩家从酿造台取出药水时触发。

实现接口：IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `potion` | IItemStack | 药水物品 |
| `player` | IPlayer | 取出药水的玩家 |

继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerSleepInBedEvent（玩家睡觉事件）

> `import crafttweaker.event.PlayerSleepInBedEvent;`

玩家尝试睡觉时触发。可通过设置 `result` 控制是否允许睡觉。

实现接口：IPlayerEvent, IEventPositionable

| 属性 | 类型 | 说明 |
|------|------|------|
| `result` | string | 结果（可设置），值为 `NOT_POSSIBLE_HERE`、`NOT_POSSIBLE_NOW`、`NOT_SAFE`、`OK` 或 `OTHER_PROBLEM` |
| `player` | IPlayer | 尝试睡觉的玩家 |

继承自 IEventPositionable 的 `position`、`x`、`y`、`z`；继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerAdvancementEvent（玩家获得进度事件）

> `import crafttweaker.event.PlayerAdvancementEvent;`

玩家获得进度时触发。

实现接口：IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `id` | string | 进度 ID（如 `minecraft:story/mine_diamond`） |
| `player` | IPlayer | 获得进度的玩家 |

继承自 IPlayerEvent 的 `entityLivingBase`。
