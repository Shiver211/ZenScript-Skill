# ZenScript 语言基础

## 括号处理器

| 类型 | 语法 | 返回 | 示例 |
|------|------|------|------|
| 物品 | `<modid:itemname:meta>` | IItemStack | `<minecraft:apple>`, `<minecraft:coal:1>`, `<minecraft:coal:*>` |
| 方块状态 | `<blockstate:modid:block:props>` | IBlockState | `<blockstate:minecraft:log:variant=oak,axis=y>` |
| 流体 | `<liquid:name>` / `<fluid:name>` | ILiquidStack | `<liquid:lava>` |
| 矿辞 | `<ore:name>` | IOreDictEntry | `<ore:ingotIron>` |
| 实体 | `<entity:modid:name>` | IEntityDefinition | `<entity:minecraft:sheep>` |
| 药水 | `<potion:modid:name>` | IPotion | `<potion:minecraft:strength>` |
| 药水类型 | `<potiontype:modid:name>` | IPotionType | `<potiontype:minecraft:strength>` |
| 附魔 | `<enchantment:modid:name>` | IEnchantmentDefinition | `<enchantment:minecraft:protection>` |
| 伤害来源 | `<damageSource:NAME>` | IDamageSource | `<damageSource:MAGIC>`, `<damageSource:FALL>` |
| 创造标签 | `<creativetab:name>` | ICreativeTab | `<creativetab:buildingBlocks>` |

---

## 变量类型

### 基本类型（无需 import）

`int`, `bool`, `long`, `float`, `double`, `void`, `string`

### 对象类型（需 import）

| 类型 | 导入路径 |
|------|---------|
| IItemStack | `crafttweaker.item.IItemStack` |
| IIngredient | `crafttweaker.item.IIngredient` |
| IOreDictEntry | `crafttweaker.oredict.IOreDictEntry` |
| ILiquidStack | `crafttweaker.liquid.ILiquidStack` |
| IItemDefinition | `crafttweaker.item.IItemDefinition` |
| IBlockDefinition | `crafttweaker.block.IBlockDefinition` |
| IData | `crafttweaker.data.IData` |
| IEnchantment | `crafttweaker.enchantments.IEnchantment` |
| IEntity | `crafttweaker.entity.IEntity` |
| IPlayer | `crafttweaker.player.IPlayer` |
| IWorld | `crafttweaker.world.IWorld` |
| IBlock | `crafttweaker.block.IBlock` |
| IBlockState | `crafttweaker.block.IBlockState` |

### 变量声明

```zenscript
val a as int = 10;      // 不可重新赋值
var b as int = 10;      // 可重新赋值
var c = 10;             // 自动推断为 int
```

### 类型强制转换

第二个操作数转换为第一个操作数的类型：`13 % 6.5` → `13 % 6 = 1`

### 作用域

```zenscript
global a as int = 450;                        // 全局作用域
static b as int = 100;                        // 全局作用域
val item as IItemStack = <minecraft:apple>;   // 脚本作用域
```

### 字符串

```zenscript
"Hello".length              // 长度
"Hello"[1]                  // 索引字符
"Hello" in "Hell"           // 包含检查
"Hel" ~ "lo"                // 连接
```

支持所有不使用 `char` 类型的 Java String 方法。

### 整数

```zenscript
0 to 10        // 返回整数范围（0 到 9）
1 ~ 10         // 连接整数，返回 "110"
```

### 全局/静态变量 workaround

```zenscript
static arr as int[] = [1];
arr[0] = 10;  // 可以修改元素
```

---

## 运算符

### 算术

`+`, `-`, `*`, `/`, `%`, `~`（字符串拼接）

### 比较与逻辑

`!`, `==`, `!=`, `>`, `>=`, `<`, `<=`, `&&`, `||`, `^`

### 短路

`||`/`&&` 会短路，`|`/`&` 会计算所有条件。短路适合 null 检查：

```zenscript
if (!isNull(item) && item.amount == 1) { ... }
```

### `in`/`has`

检查某物是否包含在某集合中。`A has B` 要求 B 的**所有**匹配物品都在 A 中才为真。

---

## 全局字段（无需 import）

| 字段 | 类型 | 说明 |
|------|------|------|
| `recipes` | IRecipeManager | 工作台配方 |
| `furnace` | IFurnaceManager | 熔炉配方 |
| `brewing` | IBrewingManager | 酿造台 |
| `game` | IGame | 游戏函数 |
| `server` | IServer | 服务器 |
| `client` | IClient | 客户端（仅客户端） |
| `events` | IEventManager | 事件管理 |
| `format` | IFormatter | 格式化 |
| `itemUtils` | IItemUtils | 物品工具 |
| `loadedMods` | ILoadedMods | 已加载 mod |
| `logger` | ILogger | 日志 |
| `oreDict` | IOreDict | 矿物词典 |
| `vanilla` | IVanilla | 原版函数 |

---

## 控制流

```zenscript
if (condition) { ... } else if (condition) { ... } else { ... }
val result = condition ? valueA : valueB;
for i in 0 .. 10 { }          // 0 到 9
for i, item in array { }      // 带索引遍历
for key, val in map { }       // 遍历映射
while (condition) { }
break; continue;
```

---

## 函数

### 全局函数（无需 import）

| 函数 | 返回 | 说明 |
|------|------|------|
| `print(obj)` | void | 打印到日志 |
| `isNull(obj)` | bool | 检查 null |
| `instanceof obj Type` | bool | 类型检查 |
| `max(int, int)` | int | 返回较大值 |
| `min(int, int)` | int | 返回较小值 |
| `pow(double, double)` | double | 幂运算 |
| `totalActions()` | int | 已注册函数数量 |
| `enableDebug()` | void | 启用调试模式 |

### ZenUtils 全局函数（需安装 ZenUtils）

| 函数 | 返回 | 说明 |
|------|------|------|
| `typeof(Object)` | String | 获取类型名 |
| `toString(Object)` | String | 字符串表示 |
| `addRegexLogFilter(String)` | void | 抑制日志 |
| `arrayOf(int, @Optional Object)` | Object[] | 创建数组 |
| `intArrayOf(int, @Optional int)` | int[] | 创建 int 数组 |
| `scriptStatus()` | int | 游戏阶段（0=初始化, 1=重载, 2=已启动） |
| `addReloadableLoader(String)` | void | 标记可重载 loader |

### 匿名函数

```zenscript
function(参数列表) { return 返回值; }

// 用于物品条件
<minecraft:iron_pickaxe>.only(function(item) {
    return !isNull(item) && item.damage > 100;
});
```

### 自定义函数

```zenscript
function name(a as int, b as int = 2) as 返回类型 {
    return a + b;
}
```

### 全局函数变量

```zenscript
global addition as function(int, int)int = function(a as int, b as int) as int {
    return a + b;
};
```

---

## 变量作用域

### global

全局变量，声明后不可修改，必须初始化。不可在子文件夹中声明。

```zenscript
global myGlobalValue as IItemStack = <minecraft:dirt>;
```

### static

脚本绑定变量，需通过 `scripts.` 前缀跨脚本引用。

```zenscript
static myStaticValue as IItemStack = <minecraft:sand>;
```

---

## Import 语法

```zenscript
import mods.jei.JEI;                    // 导入包
import mods.jei.JEI.hide as h;          // 别名导入
```