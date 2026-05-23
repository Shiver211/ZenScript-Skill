# IData 类型与运算符

> 导入: `import crafttweaker.data.IData;`

IData 通用数据接口，所有基本类型及某些数组均可转换为 IData。

---

## 类型体系

| IData 类型 | 基础类型 | 说明 |
|-----------|---------|------|
| DataInt | int | 整数 |
| DataFloat | float | 浮点数 |
| DataDouble | double | 双精度浮点 |
| DataBool | bool | 布尔 |
| DataString | string | 字符串 |
| DataInt[] | int[] | 整数数组 |
| DataByte | byte | 字节 |
| DataShort | short | 短整型 |
| DataLong | long | 长整型 |
| DataByte[] | byte[] | 字节数组 |
| DataList | 数组 | 可接受任意可转 IData 的元素，**不能用增强 for** |
| DataMap | 关联数组 | 键 String / 值 IData，即 NBT 存储结构 |

---

## 创建与转换

```zenscript
import crafttweaker.data.IData;

// 基础类型转 IData（必须加 as IData）
var myData as IData = "hello" as IData;   // DataString
myData = 42 as IData;                     // DataInt
myData = 3.14 as double;                  // DataDouble

// 数组转 IData（建议加上 as IData）
var listData as IData = [1 as int, 2 as int, 3 as int] as IData;

// 类型转换方法
myData.asInt();      myData.asLong();     myData.asDouble();
myData.asFloat();    myData.asBool();     myData.asString();
myData.asList();     myData.asMap();
```

**注意**：字符串转数值时若非纯数字，报错并返回 0：

```zenscript
print(("3.14" as IData).asInt());     // 报错，输出 0
print(("3.14" as IData).asFloat());   // 输出 3.14
print(("3.14" as IData).asDouble());  // 输出 3.14
```

---

## 二元运算符

| 子类 | `+` | `-` | `*` | `/` | `%` | `&` | `\|` | `^` | `in` | `==` | `<, >, <=, >=` |
|------|-----|-----|-----|-----|-----|-----|------|------|------|------|----------------|
| DataBool | - | - | - | - | - | Y | Y | Y | Y | Y | - |
| DataByte | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y |
| DataDouble | Y | Y | Y | Y | Y | - | - | - | Y | Y | Y |
| DataFloat | Y | Y | Y | Y | Y | - | - | - | Y | Y | Y |
| DataInt | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y |
| DataLong | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y |
| DataShort | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y |
| DataString | Y | - | - | - | - | - | - | - | Y | Y | Y |
| DataMap | Y | Y | - | - | - | - | - | - | Y | Y | - |
| DataList | Y | - | - | - | - | - | - | - | Y | Y | - |

- DataInt 除法向下取整：`(1 as IData) / (2 as IData)` 结果为 0
- DataString 的 `+` 可拼接字符串
- 数值 IData 可与 ZenScript 原生类型混合运算

## 一元运算符

| 子类 | `-`（取反） | `!`（非） |
|------|------------|----------|
| DataBool | - | Y |
| DataInt/Long/Short/Byte | Y | Y |
| DataFloat/Double | Y | - |

## 索引与成员访问

| 子类 | `[i]` | `.member` | `.length` | `.immutable` | `.update(v)` |
|------|-------|-----------|-----------|--------------|--------------|
| DataBool | - | - | 返回 0 | Y | Y |
| DataInt/Long/Short/Byte/Float/Double | - | - | 返回 0 | Y | Y |
| DataString | Y | - | Y | Y | Y |
| DataList | Y | - | Y | Y | Y |
| DataMap | - | Y | Y | Y | Y |

---

## 常见错误

| 错误 | 原因 | 修复 |
|------|------|------|
| 字节码报错 | 基础类型未加 `as IData` | `"hello" as IData` |
| `asString()` 找不到 | 对 DataString 调用了 `asString()` | 字符串已是 string，无需转换 |
| 减号不识别 | `a-b` 未加空格 | 写成 `a - b` |

## 注意事项

- 调试时务必用 `.asString()` 转换后再输出
- IData 的 `+` / `-` 和 `update()` 只返回新值，不修改原对象
