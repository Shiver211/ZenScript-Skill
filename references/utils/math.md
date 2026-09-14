# Math CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.util.Math;`

数学工具 API。

---

## API 列表

### Math（数学工具）

> `import crafttweaker.util.Math;`

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `Math.max(double/float/int, double/float/int)` | 同类型 | 最大值 |
| `Math.min(double/float/int, double/float/int)` | 同类型 | 最小值 |
| `Math.floor(double)` | int | 向下取整 |
| `Math.ceil(double)` | int | 向上取整 |
| `Math.round(double/float)` | int | 四舍五入 |
| `Math.abs(double/float/int)` | 同类型 | 绝对值 |
| `Math.sqrt(double)` | double | 开方 |
| `Math.log(double)` | double | 自然对数 |
| `Math.log10(double)` | double | 常用对数 |
| `Math.sin(double)` | double | 正弦（弧度） |
| `Math.cos(double)` | double | 余弦（弧度） |
| `Math.tan(double)` | double | 正切（弧度） |
| `Math.asin(double)` | double | 反正弦 |
| `Math.acos(double)` | double | 反余弦 |
| `Math.atan(double)` | double | 反正切 |
| `Math.sinh(double)` | double | 双曲正弦 |
| `Math.cosh(double)` | double | 双曲余弦 |
| `Math.tanh(double)` | double | 双曲正切 |
| `Math.clamp(同类型, 同类型, 同类型)` | 同类型 | 区间限制 clamp(x, min, max) |

---

## Roids-Tweaker 扩展（需安装 CraftTweaker Utilities）

> `import mods.ctutils.utils.Math;`

以下方法补充原版 Math 缺失的功能。`Number` 指 double、float、int、long 中的任意类型，方法按类型重复实现。除非另有说明，同一方法的所有 Number 参数必须类型相同。

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getE()` | double | 数学常量 e |
| `.getPi()` | double | 数学常量 π |
| `.getSqrt2()` | double | 数学常量 √2 |
| `.floor(double)` | double | long | 向下取整 |
| `.ceil(double)` | double | long | 向上取整 |
| `.round(float)` | float | int | 四舍五入 |
| `.round(double)` | double | long | 四舍五入 |
| `.average(Number[])` | Number[] | float 或 double | 平均值 |
| `.sign(Number)` | Number | Number | 符号（-1, 0, 1） |
| `.hypot(double, double)` | double, double | double | √(a²+b²)（直角三角形斜边） |
| `.cbrt(double)` | double | double | 立方根 ³√x |
| `.fastInvSqrt(double)` | double | double | 快速反平方根 1/√x |
| `.log(double, @Optional double base)` | input, base | double | 任意底数对数，默认 e |
| `.exp(double, @Optional double base)` | exponent, base | double | 任意底数指数，默认 e |
| `.random()` | 无 | Double | 已弃用，使用 `Math.getRandom().nextDouble` |
| `.getRandom(@Optional long seed)` | seed | IRandom | 获取随机数生成器 |
