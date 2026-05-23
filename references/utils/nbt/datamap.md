# DataMap 与 DataList

> 导入: `import crafttweaker.data.IData;`

DataMap 是 NBT 的实际存储格式，DataList 是 IData 数组。

---

## DataMap（关联数组）

键必须为 String，值为任意 IData。可嵌套形成树状结构。

### 创建

```zenscript
var myMap as IData = {
    "key1": "value1",
    "key2": 42 as int,
    "key3": {
        "nested": "value"
    } as IData
} as IData;
```

### Key 解析规则

- 不加引号的关键词解析为字符串（而非变量）
- 含 `.` 或 `[]` 的无引号 Key 解析为指针/索引表达式
- 重复 Key 后者覆盖前者
- **建议 Key 始终使用带引号的字符串**

```zenscript
var key as string = "myKey";
print(({key: 1} as IData).asString());       // {key:1} — "key" 是字符串
print(({"key": 1} as IData).asString());     // {key:1}
// 含 "." 或 "[]" 的无引号 Key 会被解析为指针，可能导致意外行为
```

### Value 解析规则

Value 处优先解析为变量，不会自动转为字符串：

```zenscript
var value as string = "abc";
var dm as IData = {
    "key1": value,         // 解析为变量值 "abc"
    "key2": "value"        // 字面字符串 "value"
} as IData;
```

### 访问

```zenscript
// 点号访问（常用）
print(myMap.key1.asString());

// 方法访问（支持变量作 Key）
print(myMap.memberGet("key1").asString());

// 特殊 Key 用引号包裹
print(dm."key3.2".asString());    // Key 含 "."
print(dm."key3[3]".asString());   // Key 含 "[]"
print(dm."1".asString());         // Key 为纯数字
```

### 判空

访问不存在的 Key 会抛出空指针，必须先检查：

```zenscript
// isNull 检查
if (!isNull(myMap.memberGet("key"))) { ... }

// has 关键词（推荐用 has，in 的逻辑与英文语法相反）
print(myMap has "key");    // true/false
```

**空 DataMap `{}`**：只要存在就不是 null，即使内部无任何 Key-Value 对。

### 合并与裁剪（`+` / `-`）

```zenscript
var dm1 as IData = {"key1": 1, "key2": 2} as IData;
var dm2 as IData = {"key1": 10, "key3": 3} as IData;

// "+" 并集：共有 Key 后者覆盖前者
(dm1 + dm2).asString();  // {key1:10, key2:2, key3:3}

// "-" 差集：移除共有 Key
(dm1 - dm2).asString();  // {key2:2}
(dm2 - dm1).asString();  // {key3:3}
```

**多层 `+` 规则**：
1. 保留不共有 Key 的 Key-Value 对
2. 共有 Key：Value 类型不同则后者覆盖；均为 DataMap 则递归相加

**多层 `-` 规则**：
1. 保留第一个 Map 中不存在于第二个 Map 的 Key
2. 共有 Key：Value 类型不同则移除；均为 DataMap 则递归相减

### update 方法

```zenscript
// 返回合并后的新 IData，不修改原数据
var result as IData = map2.update(map1);
// 等同于 map2 + map1
```

**区分**：`IData.update()` 只返回结果；`IItemStack.updateTag()` 和 `IPlayer.update()` 会实际修改目标 NBT。

---

## DataList（列表）

可存储任意可转 IData 的对象（包括 IItemStack），但**不能使用增强 for 循环**。

### 遍历

```zenscript
var list as IData = [1, 2, 3] as IData;

// 只能用索引遍历
for i in 0 .. list.length {
    print(list[i].asString());
}
```

### 创建空可变对象（ZenUtils）

```zenscript
// 可修改的空 DataMap
var mutableMap as IData = IData.createEmptyMutableDataMap();
mutableMap.memberSet("key", 1 as IData);

// 可修改的空 DataList
var mutableList as IData = IData.createEmptyMutableDataList();
mutableList.memberSet(0, "value" as IData);
```

---

## 常见错误

| 错误 | 原因 | 修复 |
|------|------|------|
| 空指针异常 | 访问不存在的 Key | 先 `isNull()` 或 `has` 检查 |
| `for i in dataList` 报错 | DataList 不支持增强 for | 用 `for i in 0 .. list.length` |
| 直接赋值 NBT 失败 | IData 不可变 | 用 `updateTag()` / `update()` |

## 注意事项

- 自定义 NBT Key 不要与原版重名（如 `RepairCost`、`ench` 等），建议加特异性前缀
