# 玩家核心 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.player.IPlayer;`、`import crafttweaker.player.IFoodStats;`、`import crafttweaker.player.IUser;`

IPlayer、IFoodStats、IUser 核心 API 和数据持久化示例。

---

## API 列表

### IPlayer（玩家）

> `import crafttweaker.player.IPlayer;`

IPlayer 继承 IEntityLivingBase，IEntityLivingBase 继承 IEntity。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `name` | string | 玩家名称 |
| `displayName` | string | 显示名称 |
| `uuid` | string | UUID |
| `xp` | int | 经验值 |
| `xpLevel` | int | 经验等级 |
| `xpCap` | int | 当前等级经验上限 |
| `health` | float | 生命值 |
| `maxHealth` | float | 最大生命值 |
| `foodLevel` | int | 饥饿值 |
| `saturation` | float | 饱和度 |
| `creative` | bool | 是否创造模式 |
| `isCreative` | bool | 是否创造模式（同 creative） |
| `isSpectator` | bool | 是否旁观者模式 |
| `adventure` | bool | 是否冒险模式 |
| `isAdventure` | bool | 是否冒险模式（同 adventure） |
| `isSurvival` | bool | 是否生存模式 |
| `isFlying` | bool | 是否飞行 |
| `isSleeping` | bool | 是否睡觉 |
| `isSneaking` | bool | 是否潜行 |
| `isSprinting` | bool | 是否冲刺 |
| `isElytraFlying` | bool | 是否鞘翅飞行 |
| `isBlocking` | bool | 是否格挡 |
| `isBurning` | bool | 是否着火 |
| `isInWater` | bool | 是否在水中 |
| `isOnGround` | bool | 是否在地面上 |
| `isRiding` | bool | 是否骑乘 |
| `isInvisible` | bool | 是否隐身 |
| `isInvulnerable` | bool | 是否无敌 |
| `isDead` | bool | 是否死亡 |
| `isEntityAlive` | bool | 是否存活 |
| `dimension` | int | 维度 ID |
| `world` | IWorld | 所在世界 |
| `position` | IBlockPos | 位置 |
| `x` | double | X 坐标 |
| `y` | double | Y 坐标 |
| `z` | double | Z 坐标 |
| `eyeHeight` | float | 眼睛高度 |
| `rotationYaw` | float | 偏航角 |
| `rotationPitch` | float | 俯仰角 |
| `offHandHeldItem` | IItemStack | 副手物品 |
| `mainHandHeldItem` | IItemStack | 主手物品 |
| `data` | IData | 玩家数据 |
| `gameType` | string | 游戏模式 |
| `hotbarSize` | int | 快捷栏大小 |
| `inventorySize` | int | 背包大小 |
| `currentItem` | IItemStack | 当前手持物品 |
| `foodStats` | IFoodStats | 食物状态 |
| `bedLocation` | IBlockPos | 床的位置 |
| `fishHook` | IEntity | 鱼钩实体 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.update(IData)` | void | 更新玩家数据 |
| `.give(IItemStack)` | void | 给予物品 |
| `.sendChat(string)` | void | 发送消息 |
| `.sendChat(IChatMessage)` | void | 发送富文本消息 |
| `.sendStatusMessage(string, @Optional bool)` | void | 发送状态消息。第二个参数为 true 时显示在快捷栏 |
| `.sendRichTextStatusMessage(ITextComponent, @Optional bool)` | void | 发送富文本状态消息 |
| `.sendToast(string, string)` | void | 发送通知 |
| `.executeCommand(string)` | void | 执行命令 |
| `.attackEntityFrom(IDamageSource, float)` | void | 造成伤害 |
| `.heal(float)` | void | 治疗 |
| `.setHealth(float)` | void | 设置生命值 |
| `.setFoodLevel(int)` | void | 设置饥饿值 |
| `.setSaturation(float)` | void | 设置饱和度 |
| `.addExperience(int)` | void | 添加经验 |
| `.removeXP(int)` | void | 移除经验等级 |
| `.setXPLevel(int)` | void | 设置经验等级 |
| `.getHeldItem(EnumHand)` | IItemStack | 获取手持物品 |
| `.setItemToSlot(EntityEquipmentSlot, IItemStack)` | void | 设置装备 |
| `.hasGameRule(string)` | bool | 是否有游戏规则 |
| `.getGameRule(string)` | string | 获取游戏规则 |
| `.getHotbarStack(int)` | IItemStack | 获取快捷栏指定位置的物品 |
| `.getInventoryStack(int)` | IItemStack | 获取背包指定位置的物品 |
| `.teleport(Position3f)` | void | 传送到指定位置（同一维度） |
| `.dropItem(bool)` | void | 丢弃当前手持物品。true 丢弃整个堆叠 |
| `.dropItem(IItemStack)` | void | 在玩家位置丢弃指定物品 |
| `.getCooldown(IItemStack)` | int | 获取指定物品的冷却时间 |
| `.setCooldown(IItemStack, int)` | void | 设置指定物品的冷却时间 |

### IFoodStats（食物状态）

> `import crafttweaker.player.IFoodStats;`

IFoodStats 用于查看和操作玩家的食物状态，通过 `player.foodStats` 获取。

#### @ZenGetter / @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `foodLevel` | int | 饥饿值（可读写） |
| `saturationLevel` | float | 饱和度（可读写） |
| `needFood` | bool | 是否需要食物 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addStats(int, float)` | void | 添加食物值和饱和度 |
| `.onUpdate(IPlayer)` | void | 更新食物状态 |
| `.asNBT()` | IData | 获取食物状态的 NBT 数据 |
| `.addExhaustion(float)` | void | 添加疲劳值 |

### IUser（用户接口）

> `import crafttweaker.player.IUser;`

IUser 是一个内部接口，用于统一不同用户类型（控制台、玩家、命令方块）。IUser 继承 ICommandSender。IUser 目前没有独有的方法。

---

## CraftTweakerAdditions 扩展（需安装 CraftTweakerAdditions）

> 无需额外 import，通过 `player.属性` 或 `player.方法()` 调用

### @ZenGetter
| 属性 | 类型 | 说明 |
|------|------|------|
| `viewType` | int | 获取玩家当前视角类型（0=第一视角，1=第三视角后，2=第三视角前），仅当前客户端玩家有效 |
| `village` | [IVillage] | 获取玩家所在位置的村庄（搜索半径 32 格），未找到村庄时返回 null |

### 客户端方法
| 方法 | 返回 | 说明 |
|------|------|------|
| `.togglePlayerView(int viewType)` | void | 设置玩家视角类型（0=第一视角，1=第三视角后，2=第三视角前），仅当前客户端玩家有效，支持 shoulder-surfing 模组的越肩视角 |

---

## 使用示例

### 玩家数据持久化

#### 写入数据

```zenscript
events.onPlayerCrafted(function(event as PlayerCraftedEvent) {
    if (event.player.world.remote) return;
    // 写入到 ForgeData（死亡时清空）
    event.player.update({custom: 1 as byte});
    // 写入到 PlayerPersisted（死亡后保留）
    event.player.update({PlayerPersisted: {custom: 1}});
});
```

#### 读取数据

```zenscript
events.onPlayerCrafted(function(event as PlayerCraftedEvent) {
    if (event.player.world.remote) return;
    var data = event.player.data;
    if (!isNull(data.PlayerPersisted.custom)) {
        val value = data.PlayerPersisted.custom.asInt();
        print(value);
    }
});
```

#### 修改数据

```zenscript
// 不能直接赋值：event.player.data.PlayerPersisted.custom = 10; // 错误！
// 需要用 update 覆盖
event.player.update({PlayerPersisted: {custom: 10}});
```

---

## ContentTweaker 扩展（需安装 ContentTweaker）

> `import mods.contenttweaker.Player;`

CoT 回调函数中的玩家对象。

### ICTPlayer（CoT 玩家）

> `import mods.contenttweaker.Player;`

继承 IPlayer 的所有方法。

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getHeldItem(Hand)` | IMutableItemStack | 获取手持物品（返回可变版本） |

```zenscript
#loader contenttweaker
import mods.contenttweaker.Player;
import mods.contenttweaker.Hand;

// 在回调中使用
var mainHandItem = player.getHeldItem(Hand.main());
mainHandItem.shrink(1);  // 减少数量
```
