# Game CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.game.IGame;`、`import crafttweaker.api.IClient;`、`import crafttweaker.server.IServer;`、`import crafttweaker.game.ITeam;`、`import crafttweaker.mods.ILoadedMods;`、`import crafttweaker.mods.IMod;`

游戏全局对象 API，包括游戏、客户端、服务器、队伍、模组列表等。

---

## 全局字段

无需 import，直接使用的全局对象。

| 字段 | 类型 | 说明 |
|------|------|------|
| `recipes` | IRecipeManager | 工作台配方管理 |
| `furnace` | IFurnaceManager | 熔炉配方管理 |
| `brewing` | IBrewingManager | 酿造台管理 |
| `game` | IGame | 游戏相关函数 |
| `server` | IServer | 服务器相关 |
| `client` | IClient | 客户端相关 |
| `events` | IEventManager | 事件管理 |
| `format` | IFormatter | 格式化处理 |
| `itemUtils` | IItemUtils | 物品工具 |
| `loadedMods` | ILoadedMods | 已加载 mod 列表 |
| `logger` | ILogger | 日志工具 |
| `oreDict` | IOreDict | 矿物词典管理 |
| `vanilla` | IVanilla | 原版函数（如 seeds） |

---

## API 列表

### IGame（游戏）

> `import crafttweaker.game.IGame;`

通过 `game` 全局关键字访问。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `biomes` | List\<IBiome\> | 所有注册的生物群系 |
| `blocks` | List\<IBlockDefinition\> | 所有注册的方块定义 |
| `enchantments` | List\<IEnchantmentDefinition\> | 所有注册的附魔定义 |
| `entities` | List\<IEntityDefinition\> | 所有注册的实体定义 |
| `items` | List\<IItemDefinition\> | 所有注册的物品定义 |
| `liquids` | List\<ILiquidDefinition\> | 所有注册的流体定义 |
| `potions` | List\<IPotion\> | 所有注册的药水效果 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setLocalization(string, string)` | void | 设置本地化（key, value） |
| `.setLocalization(string, string, string)` | void | 设置本地化（lang, key, value） |
| `.localize(string)` | string | 获取本地化字符串 |
| `.localize(string, string)` | string | 获取指定语言的本地化字符串 |
| `.getEntity(string)` | IEntityDefinition | 获取实体定义（等同于 `<entity:...>`） |

### IClient（客户端）

> `import crafttweaker.api.IClient;`

通过 `client` 全局关键字访问。仅在客户端可用，服务端无效。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `player` | IPlayer | 当前客户端玩家 |
| `language` | string | 客户端语言 |

### IServer（服务器）

> `import crafttweaker.server.IServer;`

通过 `server` 全局关键字访问。IServer 继承 ICommandSender。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `commandManager` | ICommandManager | 获取命令管理器 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.isOp(IPlayer)` | bool | 检查玩家是否有 OP 权限 |

---

## Roids-Tweaker 扩展

### IServer 扩展

可在 `server` 全局对象或任何 IServer 上调用。

| 方法 | 参数 | 返回 | 说明 |
|------|------|------|------|
| `.getPlayers()` | 无 | IPlayer[] | 获取所有在线玩家 |
| `.getPlayerCount()` | 无 | int | 获取在线玩家数 |
| `.getMaxPlayers()` | 无 | int | 获取最大玩家数 |
| `.getServerCommandSender()` | 无 | ICommandSender | 获取服务器命令发送者（静态方法） |
| `.getPlayerByUUID(String uuid)` | uuid | IPlayer | 通过 UUID 获取玩家 |
| `.getPlayerByUsername(String name)` | name | IPlayer | 通过用户名获取玩家 |
| `.broadcastMessage(ITextComponent message, @Optional bool isSystem)` | message, isSystem | void | 广播消息。isSystem=true 为系统消息，否则为聊天消息 |

### ITeam（队伍）

> `import crafttweaker.game.ITeam;`

ITeam 代表一个队伍。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `name` | string | 队伍名称 |
| `allowFriendlyFire` | bool | 是否允许友军伤害 |
| `colorPrefix` | string | 颜色前缀 |
| `membershipCollection` | List\<string\> | 成员列表 |
| `deathMessageVisibility` | string | 死亡消息可见性 |
| `collisionRule` | string | 碰撞规则 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.formatString(string)` | string | 格式化队伍字符串 |

### ILoadedMods / IMod（模组列表）

> `import crafttweaker.mods.ILoadedMods;`
> `import crafttweaker.mods.IMod;`

通过 `loadedMods` 全局关键字访问。

#### ILoadedMods 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `loadedMods in "modid"` | bool | 检查 mod 是否已加载 |
| `loadedMods.contains(string)` | bool | 检查 mod 是否已加载 |
| `loadedMods["modid"]` | IMod | 获取指定 mod |
| 遍历 `loadedMods` | IMod | 遍历所有已加载 mod |

#### IMod @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `id` | string | mod ID |
| `name` | string | mod 名称 |
| `version` | string | mod 版本 |
| `description` | string | mod 描述 |
| `items` | IItemStack[] | mod 添加的所有物品 |

---

## EventTweaker 扩展（需安装 EventTweaker）

> `import mods.eventtweaker.Minecraft;`

### Minecraft（客户端实例）

提供对 Minecraft 客户端实例信息的访问。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `getPlayer()` | `IPlayer` | 获取当前玩家 |
| `getfps()` | `int` | 获取当前帧率 |
| `isFullScreen()` | `bool` | 是否全屏模式 |
