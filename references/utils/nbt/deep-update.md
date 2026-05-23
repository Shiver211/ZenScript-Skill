# IData Deep Update（深度更新）

> 前置条件: ZenUtils
> 导入: `import mods.zenutils.DataUpdateOperation;`

`IData.update` 覆盖子数据，`deepUpdate` 递归更新子数据，保留未覆盖的字段。

---

## 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.deepUpdate(IData toUpdate)` | IData | 深度更新，默认 OVERWRITE |
| `.deepUpdate(IData toUpdate, IData updateOperation)` | IData | 指定更新操作 |

## DataUpdateOperation

| 操作 | 说明 |
|------|------|
| `OVERWRITE` | 覆盖旧数据（默认，等同 `update`） |
| `APPEND` | 列表/数组：追加到末尾；Map：合并 |
| `MERGE` | 列表/数组：非重复才追加；Map：同 APPEND |
| `REMOVE` | 移除指定元素；更新数据为空则清空原数据 |
| `BUMP` | 将更新数据列表作为单个元素处理（可与其他操作组合） |

## 使用示例

```zenscript
import mods.zenutils.DataUpdateOperation.*;

// Map 深度更新
val a as IData = {foo: {bar: 0}, baz: 5};
print(a.deepUpdate({foo: {abc: 1}}, OVERWRITE));
// {foo: {abc: 1}} — bar 被覆盖

// 列表操作
val list as IData = ["a", "b", "c", "d"];
print(list.deepUpdate(["d", "e", "f", "g"], APPEND));
// ["a","b","c","d","d","e","f","g"]

print(list.deepUpdate(["d", "e", "f", "g"], MERGE));
// ["a","b","c","d","e","f","g"] — 重复不添加

print(list.deepUpdate(["d", "e", "f", "g"], REMOVE));
// ["a","b","c"] — d 被移除

print(list.deepUpdate(["d", "e", "f", "g"], BUMP | APPEND));
// ["a","b","c","d",["d","e","f","g"]]

// 按键指定操作
print(a.deepUpdate({foo: {abc: 1}}, {foo: OVERWRITE}));
// {baz: 5, foo: {abc: 1}}

// 按索引指定操作
val nested as IData = [[1, 2], [3, 4], [5, 6]];
print(nested.deepUpdate([[3, 4], [4, 5], [5, 7]], [OVERWRITE, MERGE]));
// [[3,4],[3,4,5],[5,6,7]]
```

---

## Roids-Tweaker 扩展

### DataUtil

> `import mods.ctintegration.data.DataUtil;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.fromJSON(String json)` | IData | JSON/NBT 字符串转 IData，错误返回 null |
| `.parse(String json)` | IData | 同 `fromJSON` |
| `.toNBTString(IData data)` | string | 转 NBT 字符串 |
| `.getRawString(IData data)` | string | 舍弃类型定义，输出基本类型 |
| `.toJson(IData data)` | string | 转 JSON（可能丢失类型信息） |
| `.read(String filePath)` | IData | 读取 JSON 文件 |
| `.write(String filePath, IData data)` | void | 写入数据到文件 |

### IData 扩展

| 方法 | 返回 | 说明 |
|------|------|------|
| `.toNBTString()` | string | 转 NBT 字符串 |
| `.getRawString()` | string | 获取原始字符串 |
| `.toJson()` | string | 转 JSON |

**性能提示**：文件操作开销较大，重复使用的文件应保存到变量。
