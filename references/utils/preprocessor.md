# 预处理器 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: 无需导入

预处理器指令，必须放在脚本最顶部（import 之前）。

---

## 预处理器列表

### CraftTweaker 预处理器

| 预处理器 | 说明 |
|---------|------|
| `#priority <数字>` | 加载优先级（越大越先加载） |
| `#modloaded <modID ...>` | 指定的 mod 全部加载时才执行（空格分隔指定多个Mod；单个前加 `!` 取反） |
| `#loader <loaderName ...>` | 指定加载器（默认 `crafttweaker`）。可用空格分隔**同时指定多个**加载器 |
| `#sideonly <side>` | 只在 client 或 server 执行 |
| `#debug` | 开启全局 debug 模式，输出**解析后的脚本文件**到日志 |
| `#ignoreBracketErrors` | 忽略尖括号错误 |
| `#norun` | 不执行脚本（仍检查语法） |
| `#nowarn` | 警告只写日志（不影响错误） |
| `#ikwid` | 警告和错误只写日志 |
| `#profile` | 日志打印配方修改耗时 |
| `#disable_search_tree` | 禁用配方表重算（加速加载） |

```zenscript
#priority 100
#modloaded thermalfoundation
#modloaded !ic2
import crafttweaker.item.IItemStack;
// ...
```

### ZenUtils 预处理器（需安装 ZenUtils）

#### #suppress

> 版本要求: 1.5.0+

| 指令 | 说明 |
|------|------|
| `#suppress warning` / `#suppress warnings` | 抑制当前脚本的警告 |
| `#suppress error` / `#suppress errors` / `#suppress all` | 抑制当前脚本的警告和错误 |

解析错误仍会显示，错误仍会记录到 `crafttweaker.log`。使用后 `#ikwid` 和 `#nowarn` 失效。

#### #hardfail

> 版本要求: 1.6.6+

| 指令 | 说明 |
|------|------|
| `#hardfail` | 脚本产生未被 `#suppress` 抑制的错误时使游戏崩溃 |

#### #reloadable / #notreloadable

> 版本要求: 1.12.0+

ZenUtils 允许重载部分脚本。运行 `/ct reload` 命令重载。

可重载的操作：事件、LateSetCoTFunction、自定义命令、实体掉落。安装 ZenRecipeReload 可支持配方重载。

| 指令 | 说明 |
|------|------|
| `#reloadable` | 标记当前脚本为可重载 |
| `#notreloadable` | 标记当前脚本不可重载（用于 `addReloadableLoader` 后排除特定脚本） |

#### addReloadableLoader

全局函数，标记指定 loader 的所有脚本为可重载。

```zenscript
addReloadableLoader("crafttweaker");
```

---

## Roids-Tweaker 扩展（需安装 Roids-Tweaker）

### #onside

> 用法：`#onside client` 或 `#onside server`

使代码仅在指定端执行。内部使用 `FMLCommonHandler.getSide()`，等同于 `@SideOnly` 注解。

```zenscript
#onside client
import crafttweaker.event.PlayerLoggedInEvent;
// 此脚本仅在客户端执行
```
