# Commands CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.command.ICommandSender;`、`import crafttweaker.command.ICommand;`、`import crafttweaker.command.ICommandManager;`

命令系统 API，用于操作游戏内命令。

---

## API 列表

### ICommandSender（命令发送者）

> `import crafttweaker.command.ICommandSender;`

ICommandSender 是所有实体和玩家的基接口。IEntity 和 IPlayer 都实现了此接口。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `displayName` | string | 显示名称 |
| `position` | IBlockPos | 位置 |
| `world` | IWorld | 所在世界 |
| `server` | IServer | 服务器 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.sendMessage(string)` | void | 发送消息 |
| `.sendRichTextMessage(ITextComponent)` | void | 发送富文本消息 |

### ICommand（命令）

> `import crafttweaker.command.ICommand;`

ICommand 代表一个游戏内的命令。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `name` | string | 命令名 |
| `aliases` | List\<String\> | 命令别名列表 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getUsage(ICommandSender)` | string | 获取用法说明 |
| `.execute(IServer, ICommandSender, String[])` | void | 执行命令 |
| `.checkPermission(IServer, ICommandSender)` | bool | 检查权限 |
| `.getTabCompletions(IServer, ICommandSender, String[], @Optional IBlockPos)` | List | 获取 Tab 补全 |
| `.isUsernameIndex(String[], int)` | bool | 检查参数索引是否为用户名 |

### ICommandManager（命令管理器）

> `import crafttweaker.command.ICommandManager;`

ICommandManager 用于管理命令，通过 `server.commandManager` 获取。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `commands` | Map\<String, ICommand\> | 所有注册的命令 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.executeCommand(ICommandSender, string)` | int | 执行命令 |
| `.getTabCompletions(ICommandSender, string, @Optional IBlockPos)` | List\<String\> | 获取 Tab 补全 |
| `.getPossibleCommands(ICommandSender)` | List\<ICommand\> | 获取可用命令列表 |

---

## ZenUtils 扩展（需安装 ZenUtils）

### ICommandManager 扩展

对 `crafttweaker.command.ICommandManager` 的扩展。

| 方法 | 返回 | 说明 |
|------|------|------|
| `executeCommandSilent(ICommandSender, String)` | int | 静默执行命令，不发送反馈消息 |

### 自定义命令系统

#### ZenCommand

> `import mods.zenutils.command.ZenCommand;`

| 方法/属性 | 返回 | 说明 |
|------|------|------|
| `ZenCommand.create(String)` | ZenCommand | 创建命令实例 |
| `register()` | void | 注册命令到游戏（必须调用，否则命令不生效） |
| `name` | String | 获取命令名 |
| `getCommandUsage` | IGetCommandUsage | 设置用法说明（返回未本地化键名） |
| `execute` | ICommandExecute | 设置执行逻辑 |
| `requiredPermissionLevel` | int | 设置权限等级（默认 4） |
| `tabCompletionGetters` | IGetTabCompletion[] | 设置 Tab 补全 |

```zenscript
import mods.zenutils.command.ZenCommand;
import mods.zenutils.command.CommandUtils;

var cmd = ZenCommand.create("hello");
cmd.execute = function(command, server, sender, args) {
    CommandUtils.getCommandSenderAsPlayer(sender).sendMessage("Hello!");
};
cmd.register();
```

#### ZenCommandTree

> `import mods.zenutils.command.ZenCommandTree;`

#### IGetCommandUsage（函数式接口）

> `import mods.zenutils.command.IGetCommandUsage;`

接收 `ZenUtilsCommandSender`，返回 `string`（未本地化的用法键名）。可通过语言文件或 `game.setLocalization` 本地化。

#### ICommandExecute（函数式接口）

> `import mods.zenutils.command.ICommandExecute;`

| 参数 | 类型 | 说明 |
|------|------|------|
| command | ZenCommand | 正在执行的命令 |
| server | IServer | 执行命令的服务器 |
| sender | ZenUtilsCommandSender | 执行命令的发送者 |
| args | String[] | 命令参数 |

#### IGetTabCompletion（函数式接口）

> `import mods.zenutils.command.IGetTabCompletion;`

| 参数 | 类型 | 说明 |
|------|------|------|
| server | IServer | 服务器 |
| sender | ZenUtilsCommandSender | 命令发送者 |
| pos | IBlockPos | @Nullable 目标位置 |

返回 `StringList`。

| 预置方法 | 返回 | 说明 |
|------|------|------|
| `IGetTabCompletion.empty()` | StringList | 空列表 |
| `IGetTabCompletion.item()` | StringList | 所有物品 |
| `IGetTabCompletion.block()` | StringList | 所有方块 |
| `IGetTabCompletion.player()` | StringList | 所有在线玩家 |
| `IGetTabCompletion.potion()` | StringList | 所有药水 |
| `IGetTabCompletion.x()` | StringList | 目标位置 X 坐标 |
| `IGetTabCompletion.y()` | StringList | 目标位置 Y 坐标 |
| `IGetTabCompletion.z()` | StringList | 目标位置 Z 坐标 |

#### CommandUtils（命令工具）

> `import mods.zenutils.command.CommandUtils;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `getCommandSenderAsPlayer(ZenUtilsCommandSender)` | IPlayer | 将命令发送者转为玩家 |
| `getPlayer(IServer, ZenUtilsCommandSender, String)` | IPlayer | 通过目标选择器获取单个玩家 |
| `getPlayers(IServer, ZenUtilsCommandSender, String)` | List\<IPlayer\> | 获取多个玩家 |
| `getEntity(IServer, ZenUtilsCommandSender, String)` | IEntity | 获取单个实体 |
| `getEntityList(IServer, ZenUtilsCommandSender, String)` | List\<IEntity\> | 获取多个实体 |
| `getItemByText(ZenUtilsCommandSender, String)` | IItemDefinition | 通过文本 ID 查找物品定义 |
| `getBlockByText(ZenUtilsCommandSender, String)` | IBlockDefinition | 通过文本 ID 查找方块定义 |
| `notifyWrongUsage(String)` | void | 抛出用法错误（停止命令执行） |
| `notifyWrongUsage(String, String...)` | void | 抛出带替换值的用法错误 |
| `notifyWrongUsage(ZenCommand, ZenUtilsCommandSender)` | void | 使用命令定义的用法信息抛出错误 |

#### ZenUtilsCommandSender

> `import mods.zenutils.command.ZenUtilsCommandSender;`

继承 `ICommandSender`，所有 `ICommandSender` 方法可用。**禁止 instanceof 或强转为 ICommandSender 子类**，始终返回 false 或抛出 ClassCastException。使用 `CommandUtils.getCommandSenderAsPlayer` 获取玩家。

### ZenUtils 命令示例

```zenscript
import mods.zenutils.command.ZenCommand;
import mods.zenutils.command.CommandUtils;
import mods.zenutils.command.IGetTabCompletion;

var cmd = ZenCommand.create("greet");
cmd.requiredPermissionLevel = 0; // 无需权限
cmd.getCommandUsage = function(sender) {
    return "/greet <player>";
};
cmd.execute = function(command, server, sender, args) {
    var player = CommandUtils.getPlayer(server, sender, args[0]);
    player.sendMessage("你好, " ~ player.name ~ "!");
};
cmd.tabCompletionGetters = [IGetTabCompletion.player()];
cmd.register();
```

---

## ContentTweaker 扩展（需安装 ContentTweaker）

用于在 CoT 脚本中执行命令。

### Commands（命令执行）

> `import mods.contenttweaker.Commands;`

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.call(string, IPlayer, IWorld)` | void | 执行命令（logToChat=true, overridePermissions=true） |
| `.call(string, IPlayer, IWorld, bool, bool)` | void | 执行命令 |

**参数说明**:
- `command`: 要执行的命令
- `player`: 执行命令的玩家
- `world`: 命令执行的世界
- `logToChat`: 是否将命令输出显示在聊天
- `overridePermissions`: 是否忽略权限要求

```zenscript
#loader contenttweaker
import mods.contenttweaker.Commands;

// 在回调中执行命令
Commands.call("give @p minecraft:diamond 1", player, world);
```
