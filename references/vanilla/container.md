# Container CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.container.IContainer;`

容器和物品栏 API，用于操作物品容器。

---

## API 列表

### IContainer（容器）

> `import crafttweaker.container.IContainer;`

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `containerSize` | int | 容器大小（槽位总数） |
| `inventorySize` | int | 物品栏大小 |
| `name` | string | 容器名称 |
| `displayName` | string | 显示名称 |
| `commandString` | string | 命令字符串 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getStack(int)` | IItemStack | 获取指定槽的物品 |
| `.setStack(int, IItemStack)` | void | 设置指定槽的物品 |
| `.asString()` | string | 容器的字符串表示（也可用 `container as string`） |

> **IContainer 是 `Iterable<IItemStack>`**，可以直接用 for 循环遍历容器内的所有物品：

---

## 使用示例

### 玩家物品栏

```zenscript
// 获取主手物品
val mainHand = player.mainHandHeldItem;

// 获取副手物品
val offHand = player.offHandHeldItem;

// 给予物品
player.give(<minecraft:diamond>);

// 遍历玩家背包（IEntityLivingBase 提供 getItemInSlot）
for i in 0 .. player.inventorySize {
    val stack = player.getInventoryStack(i);
    print(stack.commandString);
}
```

---

## ZenUtils 扩展（需安装 ZenUtils）

> `import mods.zenutils.ItemHandler;`

### CrTItemHandler（物品处理器）

通过 `world.getItemHandler(IBlockPos)` 获取方块实体的物品容器。

| 方法 | 返回 | 说明 |
|------|------|------|
| `world.getItemHandler(IBlockPos, @Optional IFacing)` | ItemHandler | 获取方块实体的物品容器 |
| `sizeSlots` | int | 获取槽位数量 |
| `getStackInSlot(int)` | IItemStack | 获取指定槽位的物品 |
| `insertItem(int, IItemStack, bool)` | IItemStack | 向指定槽位插入物品。第三个参数为 false 时仅模拟 |
| `extractItem(int, int, bool)` | IItemStack | 从指定槽位提取物品。第三个参数为 false 时仅模拟 |
| `setStackInSlot(int, IItemStack)` | void | 直接设置指定槽位的物品 |
| `getSlotLimit(int)` | int | 获取指定槽位的最大堆叠数 |
