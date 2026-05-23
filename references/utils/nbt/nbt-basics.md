# NBT 标签与玩家数据持久化

> 导入: `import crafttweaker.data.IData;`

NBT（Named Binary Tag）是物品、玩家、实体等对象携带的附加信息，以 IData 类的形式在 ZenScript 中表示。

---

## NBT 标签构造

```zenscript
{key: value}                                                         // 基本结构
{display: {Name: "名字", Lore: ["第一行", "第二行"]}}                   // 嵌套
{Unbreakable: 1, HideFlags: 63}                                      // 数字
{Items: [{Slot: 0, id: "minecraft:diamond", Count: 1}]}              // 列表
```

### 物品 NBT 常用操作

| 方法 | 说明 |
|------|------|
| `item.tag` | 获取物品 NBT（IData） |
| `item.updateTag({key: val})` | 合并更新 NBT（原 NBT + 传入 NBT） |
| `item.withTag({key: val})` | 替换整个 NBT |
| `item.removeTag("key")` | 移除单个键 |
| `item.withEmptyTag()` | 清空 NBT |
| `item.hasTag` | 是否有 NBT |

**重要**：
- `updateTag` 前必须先 `item.mutable()`
- `updateTag` 是合并语义（`+`），`withTag` 是替换语义
- 不要直接修改从 NBT 中取出的 IData，必须通过 update 接口写回

---

## 玩家数据持久化

### ForgeData（死亡清空）

```zenscript
// 写入
player.update({"custom": 1 as byte} as IData);

// 读取
var data as IData = player.data;
if (!isNull(data.custom)) {
    val value = data.custom.asInt();
}
```

### PlayerPersisted（死亡保留）

```zenscript
// 写入
player.update({"PlayerPersisted": {"custom": 1}} as IData);

// 读取
if (!isNull(data.PlayerPersisted.custom)) {
    val value = data.PlayerPersisted.custom.asInt();
}

// 修改（不能直接赋值，需用 update 覆盖）
player.update({"PlayerPersisted": {"custom": 10}});
```

---

## NBT 操作一般流程

1. 获取 NBT（`item.tag` / `player.data` / `entity.nbt`）
2. 从外到内逐层判空检查
3. 对结果进行操作，得到新 IData
4. 通过目标对象的 update 接口写回

---

## 世界时间方法

| 方法 | 来源 | 说明 |
|------|------|------|
| `world.getWorldTime()` | IWorld | 世界总游玩时间 |
| `world.worldInfo.getWorldTotalTime()` | IWorldInfo | 世界总游玩时间 |
| `world.provider.getWorldTime()` | IWorldProvider | 昼夜更替时间 |

---

## 注意事项

- `item.mutable().updateTag()` 非立即生效，代码执行速度快于更新
- 每 Tick 触发四次：START/END × 服务端/客户端，需用 `world.remote` 过滤
- `world.remote == true` 为客户端，`false` 为服务端
- 修改游戏逻辑的代码应在服务端执行
