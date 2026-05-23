# NetworkHandler 服务端/客户端通信参考

> 前置条件: ZenUtils
> 导入: `import mods.zenutils.NetWorkHandler;` `import mods.zenutils.IByteBuf;`

ZenUtils 提供的服务端与客户端通信接口。用于在服务端更新 NBT 后通知客户端同步数据（如 tooltip 显示）。

---

## 概念说明

Minecraft 分为 Server（服务端）和 Client（客户端）：
- 服务端运行游戏逻辑，处理各种请求
- 客户端负责渲染，处理来自服务端的信息
- 单人游戏时两者同时存在
- `world.remote == true` 表示客户端执行，`false` 表示服务端执行
- 修改游戏逻辑的代码必须在服务端执行（用 `if (world.remote) return;` 过滤）

由于事件中 `world.remote` 判断只让服务端执行修改，客户端不会同步更新 NBT，导致 tooltip 等客户端功能读取到过时数据。NetworkHandler 解决此问题。

---

## NetWorkHandler 方法

> `import mods.zenutils.NetWorkHandler;`

所有方法均为静态、无返回值（void）。

| 方法 | 参数 | 说明 |
|------|------|------|
| `registerClient2ServerMessage(bufName, callback)` | bufName: string, callback: (IServer, IByteBuf, IPlayer) → void | 注册客户端→服务端消息处理 |
| `registerServer2ClientMessage(bufName, callback)` | bufName: string, callback: (IPlayer, IByteBuf) → void | 注册服务端→客户端消息处理 |
| `sendToServer(bufName, @optional writer)` | bufName: string, writer: (IByteBuf) → void | 客户端向服务端发包 |
| `sendTo(bufName, player, @optional writer)` | bufName: string, player: IPlayer, writer: (IByteBuf) → void | 服务端向指定玩家发包 |
| `sendToAll(bufName, @optional writer)` | bufName: string, writer: (IByteBuf) → void | 服务端向所有玩家发包 |
| `sendToAllAround(bufName, x, y, z, range, dimensionID, @optional writer)` | 坐标、半径、维度 | 服务端向指定范围内玩家发包 |
| `sendToDimension(bufName, dimensionID, @optional writer)` | bufName: string, dimensionID: int | 服务端向指定维度所有玩家发包 |

---

## IByteBuf（数据包）

> `import mods.zenutils.IByteBuf;`

### 写入方法

| 方法 | 参数 | 说明 |
|------|------|------|
| `writeInt(int value)` | int | 写入 int |
| `writeFloat(float value)` | float | 写入 float |
| `writeDouble(double value)` | double | 写入 double |
| `writeBoolean(boolean value)` | bool | 写入 bool |
| `writeString(string str)` | string | 写入 string |
| `writeLong(long value)` | long | 写入 long |
| `writeByte(byte value)` | byte | 写入 byte |
| `writeData(IData data)` | IData | 写入 IData（支持 DataMap） |
| `writeBlockPos(IBlockPos pos)` | IBlockPos | 写入方块位置 |
| `writeItemStack(IItemStack item)` | IItemStack | 写入物品 |
| `writeUUID(UUID uuid)` | UUID | 写入 UUID |

### 读取方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `readInt()` | int | 读取 int |
| `readFloat()` | float | 读取 float |
| `readDouble()` | double | 读取 double |
| `readBoolean()` | bool | 读取 bool |
| `readString()` | string | 读取 string |
| `readLong()` | long | 读取 long |
| `readByte()` | byte | 读取 byte |
| `readData()` | IData | 读取 IData |
| `readBlockPos()` | IBlockPos | 读取方块位置 |
| `readItemStack()` | IItemStack | 读取物品 |
| `readUUID()` | UUID | 读取 UUID |

**重要**：读取顺序必须与写入顺序一致！

---

## 使用示例

### CD 同步到客户端

服务端触发 CD 后发包通知客户端：

```zenscript
import mods.zenutils.NetWorkHandler;

// 服务端：触发 CD 时发包
function triggerCooldown(player as IPlayer, cdName as string, cdTick as int) as void {
    NetWorkHandler.sendTo("syncCooldown", player, function(byteBuf) {
        byteBuf.writeLong(player.world.getWorldTime() + (cdTick as long));
        byteBuf.writeString(cdName);
    });
}

// 注册：客户端收到后更新本地 NBT
NetWorkHandler.registerServer2ClientMessage("syncCooldown", function(player, byteBuf) {
    var endTime as long = byteBuf.readLong();
    var cdName as string = byteBuf.readString();
    var updateData as IData = IData.createEmptyMutableDataMap();
    updateData.memberSet(cdName, endTime);
    player.update(updateData);
});
```

### 玩家登入时同步

客户端登入时服务端发送当前 CD 状态：

```zenscript
events.onPlayerLoggedIn(function(event as PlayerLoggedInEvent) {
    var player as IPlayer = event.player;
    if (player.world.remote) return;
    if (!isNull(player.data.myCooldown) && player.data.myCooldown > player.world.getWorldTime()) {
        NetWorkHandler.sendTo("syncCooldown", player, function(byteBuf) {
            byteBuf.writeLong(player.data.myCooldown);
            byteBuf.writeString("myCooldown");
        });
    }
});
```

---

## 注意事项

- `sendTo` / `sendToAll` 等由**服务端**调用
- `sendToServer` 由**客户端**调用
- `registerServer2ClientMessage` / `registerClient2ServerMessage` 只需注册一次（通常在脚本顶层）
- 只发包不注册接收事件 = 白发；只注册不发包 = 干等
- 读取顺序必须与写入顺序一致，否则数据错乱
