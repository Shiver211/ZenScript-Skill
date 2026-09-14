# 玩家其他事件 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.event.*;`

玩家 Tick、死亡掉落、物品损坏、填充桶、容器、铁砧、重生点、可见性、使用物品等事件。

---

## API 列表

### PlayerTickEvent（玩家 Tick 事件）

> `import crafttweaker.event.PlayerTickEvent;`

每个玩家每 Tick 触发（客户端和服务端都会触发）。

实现接口：IPlayerEvent, ITickEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `player` | IPlayer | 玩家 |
| `phase` | string | Tick 阶段，值为 `START` 或 `END` |
| `side` | string | 执行端，值为 `CLIENT` 或 `SERVER` |

继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerDeathDropsEvent（玩家死亡掉落事件）

> `import crafttweaker.event.PlayerDeathDropsEvent;`

玩家死亡导致物品掉落时触发。

实现接口：IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `items` | List\<IEntityItem\> | 掉落物列表（可设置） |
| `damageSource` | IDamageSource | 伤害来源 |
| `player` | IPlayer | 死亡的玩家 |

方法：
- `event.addItem(IItemStack)` 向掉落列表添加物品
- `event.addItem(IEntityItem)` 向掉落列表添加物品实体

继承自 IPlayerEvent 的 `entityLivingBase`。

```zenscript
events.onPlayerDeathDrops(function(event as PlayerDeathDropsEvent) {
    event.items = []; // 清空掉落
    event.addItem(<item:minecraft:iron_ingot>); // 添加物品
});
```

### PlayerDestroyItemEvent（玩家物品损坏事件）

> `import crafttweaker.event.PlayerDestroyItemEvent;`

玩家物品损坏（耐久耗尽）时触发。

实现接口：IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `originalItem` | IItemStack | 损坏前的物品 |
| `hand` | string | 使用的手（主手/副手） |
| `player` | IPlayer | 物品损坏的玩家 |

继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerFillBucketEvent（玩家填充桶事件）

> `import crafttweaker.event.PlayerFillBucketEvent;`

玩家填充桶时触发。可取消。

实现接口：IEventCancelable, IProcessableEvent, IEventPositionable, IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `result` | IItemStack | 填充后的桶物品（可设置） |
| `emptyBucket` | IItemStack | 空桶物品 |
| `world` | IWorld | 世界对象 |
| `blockState` | IBlockState | 方块状态 |
| `block` | IBlock | 方块对象 |
| `dimension` | int | 维度 ID |
| `rayTraceResult` | IRayTraceResult | 射线追踪结果 |
| `player` | IPlayer | 填充桶的玩家 |

方法：
- `event.deny()` 设置结果为 `deny`
- `event.allow()` 设置结果为 `allow`
- `event.default()` 设置结果为 `default`

继承自 IEventPositionable 的 `position`、`x`、`y`、`z`；继承自 IPlayerEvent 的 `entityLivingBase`。

注：此事件同时继承 IProcessableEvent 的 `result`（string 类型，值为 `default`/`deny`/`allow`）和自身的 `result`（IItemStack 类型，用于设置填充后的物品）。使用 `event.result = <item:minecraft:stick>` 会将玩家手中的桶替换为木棍。

### PlayerOpenContainerEvent（玩家打开容器事件）

> `import crafttweaker.event.PlayerOpenContainerEvent;`

玩家打开容器时触发。

实现接口：IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `container` | IContainer | 被打开的容器 |
| `player` | IPlayer | 打开容器的玩家 |

继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerCloseContainerEvent（玩家关闭容器事件）

> `import crafttweaker.event.PlayerCloseContainerEvent;`

玩家关闭容器时触发。

实现接口：IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `container` | IContainer | 被关闭的容器 |
| `player` | IPlayer | 关闭容器的玩家 |

继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerAnvilRepairEvent（玩家铁砧修复事件）

> `import crafttweaker.event.PlayerAnvilRepairEvent;`

玩家在铁砧合成物品时触发。可修改铁砧损坏概率。

实现接口：IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `itemInput` | IItemStack | 左侧输入物品 |
| `itemIngredient` | IItemStack | 右侧输入物品 |
| `itemResult` | IItemStack | 输出物品 |
| `breakChance` | float | 铁砧损坏概率（可设置） |
| `player` | IPlayer | 修复的玩家 |

继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerAnvilUpdateEvent（玩家铁砧更新事件）

> `import crafttweaker.event.PlayerAnvilUpdateEvent;`

玩家在铁砧左右槽位都放入物品时触发。可取消。取消后原版行为不执行且输出设为 null。若未取消但 `outputItem` 不为 null，则使用自定义输出。若 `outputItem` 为 null 且未取消，执行原版行为。

实现接口：IEventCancelable

| 属性 | 类型 | 说明 |
|------|------|------|
| `leftItem` | IItemStack | 左侧输入物品 |
| `rightItem` | IItemStack | 右侧输入物品 |
| `outputItem` | IItemStack | 输出物品（可设置，设置后跳过原版行为） |
| `itemName` | string | 玩家设置的物品名称 |
| `xpCost` | int | 经验消耗（可设置，**仅在 `outputItem` 不为 null 时修改才生效**） |
| `materialCost` | int | 右侧消耗物品数量（可设置，0 表示消耗整个堆叠） |

### PlayerSetSpawnEvent（玩家设置重生点事件）

> `import crafttweaker.event.PlayerSetSpawnEvent;`

玩家重生点位置变化时触发。可取消。

实现接口：IPlayerEvent, IEventCancelable

| 属性 | 类型 | 说明 |
|------|------|------|
| `isForced` | bool | 是否强制设置 |
| `newSpawn` | IBlockPos | 新重生点位置 |
| `player` | IPlayer | 设置重生点的玩家 |

继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerVisibilityEvent（玩家可见性事件）

> `import crafttweaker.event.PlayerVisibilityEvent;`

确定玩家可见性时触发，用于计算生物发现玩家的距离。无法直接设置 `modifier`，需要通过 `modifyVisibility` 方法修改。**可见性只能调低或还原，不能提高到超过原版最大索敌距离**（即标准目标距离）。

实现接口：IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `modifier` | double | 可见性修正值 |
| `player` | IPlayer | 玩家 |

方法：
- `event.modifyVisibility(double value)` 将 `modifier` 替换为 `modifier * value`

继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerUseItemStartEvent（玩家开始使用物品事件）

> `import crafttweaker.event.PlayerUseItemStartEvent;`

玩家开始使用物品时触发（如开始拉弓、食用食物等）。可取消。

实现接口：IEventCancelable, IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `item` | IItemStack | 被使用的物品 |
| `player` | IPlayer | 使用物品的玩家 |

继承自 IPlayerEvent 的 `entityLivingBase`。

### PlayerUseItemTickEvent（玩家使用物品 Tick 事件）

> `import crafttweaker.event.PlayerUseItemTick;`

玩家持续使用物品时每 Tick 触发。可取消。

实现接口：IEventCancelable, IPlayerEvent

| 属性 | 类型 | 说明 |
|------|------|------|
| `item` | IItemStack | 被使用的物品 |
| `duration` | int | 使用持续时间 |
| `player` | IPlayer | 使用物品的玩家 |

继承自 IPlayerEvent 的 `entityLivingBase`。
