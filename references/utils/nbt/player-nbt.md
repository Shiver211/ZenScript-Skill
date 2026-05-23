# 玩家 NBT 操作与 CD 机制

> 导入: `import crafttweaker.data.IData;` `import crafttweaker.player.IPlayer;`

---

## 读取

```zenscript
var data as IData = player.data;
var custom as IData = data.memberGet("myKey");
```

## 写入

```zenscript
// ForgeData（死亡清空）
player.update({"myKey": 1 as int} as IData);

// PlayerPersisted（死亡保留）
player.update({"PlayerPersisted": {"myKey": 1 as int}} as IData);

// 读取 PlayerPersisted
if (!isNull(data.PlayerPersisted.myKey)) {
    val value = data.PlayerPersisted.myKey.asInt();
}
```

---

## 内置 CD 机制（基于世界时间）

利用世界时间实现无需每 Tick 递减的 CD 系统：

```zenscript
// 检测 CD 并设置（返回 true 表示 CD 已就绪）
function cooldownCheck(player as IPlayer, cdNBTName as string, cdTick as int, isPersisted as bool) as bool {
    var updateNBT as IData = IData.createEmptyMutableDataMap();
    updateNBT.memberSet(cdNBTName, (cdTick as long + player.world.getWorldTime()) as long);

    if (isPersisted) {
        var updatePersistedNBT as IData = IData.createEmptyMutableDataMap();
        updatePersistedNBT.memberSet("PlayerPersisted", updateNBT);
        if (isNull(player.data.PlayerPersisted) || isNull(player.data.PlayerPersisted.memberGet(cdNBTName))) {
            player.update(updatePersistedNBT);
            return true;
        }
        return player.data.PlayerPersisted.memberGet(cdNBTName).asLong() <= player.world.getWorldTime();
    }

    if (isNull(player.data.memberGet(cdNBTName))) {
        player.update(updateNBT);
        return true;
    }
    return player.data.memberGet(cdNBTName).asLong() <= player.world.getWorldTime();
}
```

### 世界时间方法区别

| 方法 | 来源 | 说明 |
|------|------|------|
| `world.getWorldTime()` | IWorld | 世界总游玩时间 |
| `world.worldInfo.getWorldTotalTime()` | IWorldInfo | 世界总游玩时间 |
| `world.provider.getWorldTime()` | IWorldProvider | 昼夜更替时间 |
