---
name: zenscript
description: >
  CraftTweaker 1.12.2 ZenScript 编写、修改和排错支持。用于编写、编辑、
  审查或调试 .zs 脚本；当用户提到 CraftTweaker、ZenScript、CrT、整合包
  配方调整、scripts/ 目录、合成、熔炉、酿造、矿物词典、物品/方块/实体
  事件或模组专属 CraftTweaker 接口时使用。通过优先查阅参考资料来避免
  自造 ZenScript 方法和错误的配方语法。
---

# ZenScript Skill For CraftTweaker 1.12.2

## 核心原则

- 只使用 `references/` 中明确记录的 API；禁止凭记忆补全或自造方法。禁止任何联网搜索。
- 禁止把未在 ZenScript 参考中确认的语法当作可用语法。
- 除非用户已经点名文件，否则先读 `references/index.md`，再决定要打开的正文文件。
- 先缩小到 1 到 3 个最可能文件；不要靠目录名猜路径，也不要整批打开同类拆分文件。
- 只有在语法、类型、全局字段、函数、控制流或 import 不确定时，才读 `references/language-basics.md` 的相关章节。
- 在物品 id、方块 id、meta、NBT、配方名不确定时，让用户确认。
- 写完脚本后，提醒用户执行 `/ct syntax`，并把报错行号发回来。
- 内容锚定：脚本中每个方法调用、属性访问、事件名必须能回溯到 `references/` 中的具体文件。无法回溯的用法一律不写。
- 推断标记：若某用法在已读文档中找不到但确有存在可能，不直接写入脚本，而是以「推断：」开头向用户说明，让用户确认后再写。

## 工作流

处理非琐碎脚本时，按这个顺序执行：
1. 读 `references/index.md`，按关键词和任务类型定位最可能的文件。
2. 只打开命中的精确文件；长文件先搜标题、方法名、事件名、机器名、导入或示例，再读相关章节。
3. 先看文件头或章节头的 `Mod ID`、`前置条件`、`导入`、`加载器` 和重要说明。
4. 只使用已读文档里出现的方法和属性；找不到就按“不可用”处理。
5. 如果多个已记录 API 都能完成目标但行为不同，先问用户要哪种行为。

## 开发规范

1. 4 空格缩进。
2. K&R 大括风格，`} else {` 同行。
3. `if`/`else`/`for`/`while` 必须使用大括号，即使一行。
4. 公共函数参数、返回值、复杂变量显式类型；简单局部变量可依赖推断。
5. 变量/方法 lowerCamelCase，物品 ID 字符串 snake_case，静态常量 camelCase。杜绝不规范缩写。
6. 代码必须写成最简洁的形态，同一逻辑重复 2 次以上才提取函数，不做无意义提取。
7. 少用 `if` 嵌套，优先 early return 扁平化控制流。
8. 涉及修改世界状态（生成实体、修改方块、改变物品等）的操作应在服务端执行；必要时先判断 `world.remote`，避免在客户端执行。
9. 在可能出现 Null 的地方必须使用全局函数 `isNull()` 检查 + early return 作为函数入口守卫。
10. 如果使用了 ContentTweaker 的方法，那么必须加 `#loader contenttweaker` 预处理器，item 必须使用 `<item:...>` 前缀。
11. 在保证功能正确的前提下，优先使用 ZenUtils 提供的方法或 native 方法处理 NBT。
12. 除了NBT操作，尽量避免使用native方法。

## 路由规则

### Vanilla

先看 `references/index.md` 的 Vanilla 区域，找到相关文件后再跳到精确文件。

### Utils

工具类，遇到下面这类需求，先看 `references/index.md` 的 Utils 区域：
- 字符串、文本格式、tooltip、本地化、模板字符串
- 日志、调试输出、预处理器
- 正则、数学、日期时间、UUID、十六进制
- 判空、可选链、空值合并
- 原生方法访问、自定义类、链式执行、区块坐标、配方模式

### Mods

模组接口在 `references/mods/`。

查找顺序：
1. 提取用户提及的名称,规范化后先匹配 `references/mods/` 文件名。
2. 文件名未命中时，再搜文档开头的标题、`Mod ID`、`导入`、重要说明或常见缩写。
3. 只读取少量候选文件的文件头和目标章节；不要先打开多个模组文档做大范围比较。
4. 若有多个候选模组无法确定时，先问用户，不要自行决定。
5. 高风险变体必须确认版本，例如：
  - `modularmachinery.md` / `modularmachinery-ce.md`
  - `nuclearcraft.md` / `nuclearcraft-overhauled.md`
  - `immersiveengineering.md` / `immersivepetroleum.md` / `immersiveintelligence.md`

如果用户目标很模糊，例如“矿石翻倍”或“自定义机器配方”，先用 `references/index.md` 缩小 vanilla/utils 范围；若索引未覆盖，再去 `references/mods/` 缩小候选。

## 读取与使用规则

- 长文件不要整篇硬读。优先搜索 `^##`、`^###`、`导入`、方法名、事件名、机器名、`使用示例`。
- 不要因为同一文件里存在别的 API，就推断目标 API 也存在。
- 参考文件常见结构是：文件头、`API 列表`、方法表、属性表、示例、注意事项。只把与当前任务相关的部分作为依据。

API 表读取规则：
- `@ZenGetter`：只读属性，用 `.{属性名}` 访问
- `@ZenSetter`：可写属性，用 `.{属性名} = {值}`
- `@ZenGetter / @ZenSetter`：可读可写
- 方法：按文档中的签名调用

## 输出前检查

- import 与参考文件一致，并且只在需要时加入
- 用到的每个方法和属性都能在已读文档里找到
- 若语法、类型或 import 有不确定之处，是否已查 `references/language-basics.md`
- 不确定的物品 id、meta、NBT、模组可用性或模组版本已明确指出
- 锚定自检：脚本中每个 API 调用是否都能说出它来自 `references/` 的哪个文件？若任一项只能说"应该是"而指不出文件，删除该调用并替换为向用户确认。

## 未知声明
- 无法从 `references/` 确定的 API、方法签名、物品 id、meta、NBT 或模组版本，直接声明「当前参考资料无法确定。缺失信息：[具体项]」，禁止编造。

## 脚本文件约定

- 文件位置：`.minecraft/scripts/`，支持子目录
- 文件扩展名：`.zs`