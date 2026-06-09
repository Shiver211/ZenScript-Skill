# 玩家交互事件 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.event.*;`

玩家攻击、右键、左键等交互事件。

---

## API 列表

### PlayerAttackEntityEvent（玩家攻击实体事件）

> `import crafttweaker.event.PlayerAttackEntityEvent;`

玩家攻击实体时触发。可取消以阻止攻击。

实现接口：IEventCancelable, IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `target` | IEntity | 被攻击的实体 |
| `player` | IPlayer | 攻击的玩家 |

继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerInteractEvent（玩家交互事件）

> `import crafttweaker.event.PlayerInteractEvent;`

玩家与方块交互时触发。可取消。

实现接口：IEventCancelable, IPlayerEvent, IEventPositionable

| 属性 | 类型 | 说明 |
|------|------|------|
| `world` | IWorld | 世界对象 |
| `blockState` | IBlockState | 方块状态 |
| `block` | IBlock | 方块对象 |
| `face` | IFacing | 交互面 |
| `item` | IItemStack | 手持物品 |
| `dimension` | int | 维度 ID |
| `hand` | string | 使用的手（主手/副手） |
| `player` | IPlayer | 交互的玩家 |

方法：
- `event.damageItem(int amount)` 对手持物品造成指定耐久损伤

继承自 IEventPositionable 的 `position`、`x`、`y`、`z`；继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerInteractBlockEvent（玩家右键方块事件）

> `import crafttweaker.event.PlayerInteractBlockEvent;`

玩家右键方块时触发。可取消。取消后可设置 `cancellationResult` 为 `success`、`fail` 或 `pass`（默认为 `pass`）。

实现接口：IEventCancelable, IHasCancellationResult, IEventPositionable, IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `hitvector` | IVector3d | 命中向量 |
| `useblock` | string | 方块使用结果（可设置，值为 `allow` / `deny` / `default`） |
| `useitem` | string | 物品使用结果（可设置，值为 `allow` / `deny` / `default`） |
| `cancellationResult` | string | 取消结果（可设置，值为 `success` / `pass` / `fail`） |
| `world` | IWorld | 世界对象 |
| `blockState` | IBlockState | 方块状态 |
| `block` | IBlock | 方块对象 |
| `face` | IFacing | 交互面 |
| `item` | IItemStack | 手持物品 |
| `dimension` | int | 维度 ID |
| `hand` | string | 使用的手 |
| `player` | IPlayer | 交互的玩家 |

方法：
- `event.damageItem(int amount)` 对手持物品造成指定耐久损伤

继承自 IEventPositionable 的 `position`、`x`、`y`、`z`；继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerInteractEntityEvent（玩家右键实体事件）

> `import crafttweaker.event.PlayerInteractEntityEvent;`

玩家右键实体时触发。可取消。取消后可设置结果。

实现接口：IEventCancelable, IEventPositionable, IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `target` | IEntity | 被交互的实体 |
| `world` | IWorld | 世界对象 |
| `blockState` | IBlockState | 方块状态 |
| `block` | IBlock | 方块对象 |
| `face` | IFacing | 交互面 |
| `item` | IItemStack | 手持物品 |
| `dimension` | int | 维度 ID |
| `hand` | string | 使用的手 |
| `player` | IPlayer | 交互的玩家 |

方法：
- `event.damageItem(int amount)` 对手持物品造成指定耐久损伤

继承自 IEventPositionable 的 `position`、`x`、`y`、`z`；继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerRightClickItemEvent（玩家右键物品事件）

> `import crafttweaker.event.PlayerRightClickItemEvent;`

在物品功能触发前触发（当玩家未指向方块或实体时）。可取消以阻止后续事件。

实现接口：IEventCancelable, IHasCancellationResult, IEventPositionable, IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `cancellationResult` | string | 取消结果（可设置，值为 `success` / `pass` / `fail`） |
| `world` | IWorld | 世界对象 |
| `blockState` | IBlockState | 方块状态 |
| `block` | IBlock | 方块对象 |
| `face` | IFacing | 交互面 |
| `item` | IItemStack | 手持物品 |
| `dimension` | int | 维度 ID |
| `hand` | string | 使用的手 |
| `player` | IPlayer | 交互的玩家 |

方法：
- `event.damageItem(int amount)` 对手持物品造成指定耐久损伤

继承自 IEventPositionable 的 `position`、`x`、`y`、`z`；继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerLeftClickBlockEvent（玩家左键方块事件）

> `import crafttweaker.event.PlayerLeftClickBlockEvent;`

玩家左键方块时触发。可取消以阻止左键操作（阻止方块破坏，但在创造模式下无效）。按住左键时即使取消也会持续触发。取消后可设置结果。

实现接口：IEventCancelable, IHasCancellationResult, IEventPositionable, IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `hitvector` | IVector3d | 命中向量 |
| `useblock` | string | 方块使用结果（可设置，值为 `allow` / `deny` / `default`） |
| `useitem` | string | 物品使用结果（可设置，值为 `allow` / `deny` / `default`） |
| `cancellationResult` | string | 取消结果（可设置，值为 `success` / `pass` / `fail`） |
| `world` | IWorld | 世界对象 |
| `blockState` | IBlockState | 方块状态 |
| `block` | IBlock | 方块对象 |
| `face` | IFacing | 交互面 |
| `item` | IItemStack | 手持物品 |
| `dimension` | int | 维度 ID |
| `hand` | string | 使用的手 |
| `player` | IPlayer | 交互的玩家 |

方法：
- `event.damageItem(int amount)` 对手持物品造成指定耐久损伤

继承自 IEventPositionable 的 `position`、`x`、`y`、`z`；继承自 IPlayerEvent 的 `entityLivingBase`。
