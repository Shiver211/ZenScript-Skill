# 自定义物品 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: ContentTweaker（部分需要 ZenUtils）
> 导入: `import mods.contenttweaker.VanillaFactory;`、`import mods.contenttweaker.Item;`、`import mods.contenttweaker.ItemFood;`

ContentTweaker 和 ZenUtils 自定义物品 API。

---

## ContentTweaker 扩展（需安装 ContentTweaker）

> `import mods.contenttweaker.VanillaFactory;`
> `import mods.contenttweaker.Item;`
> `import mods.contenttweaker.ItemFood;`

CoT 脚本第一行必须为 `#loader contenttweaker`。

### VanillaFactory 物品方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.createItem(string id)` | Item | 创建自定义物品（ID 全小写，字母开头） |
| `.createItemFood(string id, int healAmount)` | ItemFood | 创建自定义食物 |

### Item（自定义物品）

> `import mods.contenttweaker.Item;`

| ZenProperty | 类型 | 默认值 | 说明 |
|-------------|------|--------|------|
| `maxStackSize` | int | 64 | 最大堆叠数 |
| `maxDamage` | int | -1 | 耐久（<0 为普通物品，>0 为工具） |
| `rarity` | string | "COMMON" | 稀有度: "COMMON"/"UNCOMMON"/"RARE"/"EPIC" |
| `creativeTab` | ICreativeTab | 杂项 | 所在创造标签 |
| `glowing` | bool | false | 是否有附魔光芒 |
| `beaconPayment` | bool | false | 是否可作为信标消耗品 |
| `toolClass` | string | null | 工具类型（"pickaxe"/"axe" 等） |
| `toolLevel` | int | -1 | 工具挖掘等级 |
| `itemRightClick` | IItemRightClick | null | 右键回调 |
| `onItemUse` | IItemUse | null | 对方块使用回调 |
| `onItemFoodEaten` | IItemFoodEaten | null | 吃下回调（仅 ItemFood） |
| `onItemUpdate` | IItemUpdate | null | 物品更新回调 |
| `onItemUseFinish` | IItemUseFinish | null | 使用完成回调 |
| `onDestroyedBlock` | IItemDestroyedBlock | null | 破坏方块回调 |
| `destroySpeed` | IItemDestroySpeed | null | 破坏速度函数 |
| `itemInteractionForEntity` | IItemInteractionForEntity | null | 对实体交互回调 |
| `containerItem` | IItemGetContainerItem | null | 容器物品函数 |
| `itemColorSupplier` | IItemColorSupplier | null | 物品颜色函数 |
| `localizedNameSupplier` | ILocalizedNameSupplier | null | 显示名函数 |
| `textureLocation` | IResourceLocationSupplier | null | 纹理位置函数 |

| 方法 | 返回 | 说明 |
|------|------|------|
| `.register()` | void | 注册物品进游戏（注册后不可修改） |

### ItemFood（自定义食物）

> `import mods.contenttweaker.ItemFood;`

继承 Item 的所有属性，另有：

| ZenProperty | 类型 | 默认值 | 说明 |
|-------------|------|--------|------|
| `healAmount` | int | - | 恢复饥饿值 |
| `saturation` | float | 0.6 | 相对饱和度 |
| `alwaysEdible` | bool | false | 饥饿值满时是否可吃 |
| `wolfFood` | bool | false | 是否可喂给狼 |
| `onItemFoodEaten` | function | null | 吃下后执行的函数 |

### ContentTweaker 物品示例

```zenscript
#loader contenttweaker
import mods.contenttweaker.VanillaFactory;
import mods.contenttweaker.ItemFood;

var soup as ItemFood = VanillaFactory.createItemFood("sweet_soup", 4);
soup.saturation = 0.8;
soup.alwaysEdible = true;
soup.onItemFoodEaten = function(stack, world, player) {
    if (!world.remote) {
        player.addPotionEffect(<potion:minecraft:speed>.makePotionEffect(100, 1));
    }
};
soup.register();
```

---

## ContentTweaker 物品回调函数

### IItemRightClick（右键点击）

> `import mods.contenttweaker.IItemRightClick;`

| 参数 | 类型 | 说明 |
|------|------|------|
| itemStack | IMutableItemStack | 物品堆叠 |
| world | IWorld | 世界 |
| player | ICTPlayer | 玩家 |
| hand | string | "MAIN_HAND" 或 "OFF_HAND" |

返回 `"SUCCESS"`、`"PASS"` 或 `"FAIL"`。

### IItemUse（对方块使用）

> `import mods.contenttweaker.IItemUse;`

| 参数 | 类型 | 说明 |
|------|------|------|
| player | ICTPlayer | 玩家 |
| world | IWorld | 世界 |
| pos | IBlockPos | 方块位置 |
| hand | Hand | 使用的手 |
| facing | Facing | 方块面 |
| blockHit | Position3f | 点击位置（0-1） |

返回 ActionResult。

```zenscript
item.onItemUse = function(player, world, pos, hand, facing, blockHit) {
    var firePos = pos.getOffset(facing, 1);
    if (world.getBlockState(firePos).isReplaceable(world, firePos)) {
        world.setBlockState(<block:minecraft:fire>, firePos);
        player.getHeldItem(hand).damage(1, player);
        return ActionResult.success();
    }
    return ActionResult.pass();
};
```

### IItemFoodEaten（吃下食物）

> `import mods.contenttweaker.IItemFoodEaten;`

| 参数 | 类型 | 说明 |
|------|------|------|
| mutableItemStack | IMutableItemStack | 食物堆叠 |
| world | IWorld | 世界 |
| player | ICTPlayer | 玩家 |

返回 void。注意：如果设置了 `onItemUseFinish`，此回调不会触发。

### IItemUpdate（物品更新）

> `import mods.contenttweaker.IItemUpdate;`

| 参数 | 类型 | 说明 |
|------|------|------|
| itemStack | IMutableItemStack | 物品堆叠 |
| world | IWorld | 世界 |
| owner | IEntity | 持有者 |
| slot | int | 物品栏槽位 |
| isSelected | bool | 是否被选中 |

返回 void。

### IItemUseFinish（使用完成）

> `import mods.contenttweaker.IItemUseFinish;`

| 参数 | 类型 | 说明 |
|------|------|------|
| itemStack | IMutableItemStack | 物品堆叠 |
| world | IWorld | 世界 |
| entity | IEntityLivingBase | 使用者 |

返回 IItemStack（替换原物品）。

### IItemDestroyedBlock（破坏方块）

> `import mods.contenttweaker.IItemDestroyedBlock;`

| 参数 | 类型 | 说明 |
|------|------|------|
| stack | IMutableItemStack | 物品堆叠 |
| world | IWorld | 世界 |
| blockState | ICTBlockState | 方块状态 |
| pos | IBlockPos | 方块位置 |
| entity | IEntityLivingBase | 破坏者 |

返回 bool（true=成功）。

### IItemDestroySpeed（破坏速度）

> `import mods.contenttweaker.IItemDestroySpeed;`

| 参数 | 类型 | 说明 |
|------|------|------|
| mutableItemStack | IMutableItemStack | 物品堆叠 |
| blockState | ICTBlockState | 方块状态 |

返回 float（破坏速度倍率）。

### IItemInteractionForEntity（对实体交互）

> `import mods.contenttweaker.IItemInteractionForEntity;`

| 参数 | 类型 | 说明 |
|------|------|------|
| itemStack | IMutableItemStack | 物品堆叠 |
| player | ICTPlayer | 玩家 |
| target | IEntityLivingBase | 目标实体 |
| hand | string | "MAIN_HAND" 或 "OFF_HAND" |

返回 bool。

```zenscript
item.itemInteractionForEntity = function(stack, player, target, hand) {
    if (target.definition.id == "minecraft:sheep") {
        target.removeFromWorld();
        stack.shrink(1);
        return true;
    }
    return false;
};
```

### IItemGetContainerItem（容器物品）

> `import mods.contenttweaker.IItemGetContainerItem;`

| 参数 | 类型 | 说明 |
|------|------|------|
| stack | IItemStack | 物品堆叠 |

返回 IItemStack（合成后留下的物品，如桶→空桶），返回 null 表示不留。

### ILocalizedNameSupplier（显示名函数）

> `import mods.contenttweaker.LocalizedNameSupplier;`

| 参数 | 类型 | 说明 |
|------|------|------|
| itemStack | IItemStack | 物品堆叠 |

返回 string（显示名称）。

### IResourceLocationSupplier（纹理位置函数）

> `import mods.contenttweaker.IResourceLocationSupplier;`

无参数，返回 CTResourceLocation。

---

## ContentTweaker 物品类型

### IMutableItemStack（可变物品堆叠）

> `import mods.contenttweaker.MutableItemStack;`

继承 IItemStack 和 IIngredient，可通过 ICTPlayer 获取。

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setCount(int)` | void | 设置数量 |
| `.grow(int)` | void | 增加数量 |
| `.shrink(int)` | void | 减少数量 |
| `.damage(int, ICTPlayer)` | void | 造成耐久损伤 |

### ActionResult（动作结果）

> `import mods.contenttweaker.ActionResult;`

值：`fail`、`pass`、`success`

```zenscript
ActionResult.success()
ActionResult.pass()
ActionResult.fail()
```

---

## ZenUtils 扩展（需安装 ZenUtils + ContentTweaker）

> `import mods.zenutils.cotx.*;`

该部分是对 ContentTweaker 物品系统的扩展(见上方ContentTweaker 扩展)。

### VanillaFactory 物品扩展方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `VanillaFactory.createEnergyItem(String, int, int, int)` | EnergyItem | 创建能量物品 |
| `VanillaFactory.createExpandItem(String)` | ExpandItem | 创建扩展物品 |

### ExpandItem（扩展物品）

> `import mods.zenutils.cotx.Item;`

继承 ContentTweaker 的 `ItemRepresentation`。

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `onEntityItemUpdate` | IEntityItemUpdate | null | 物品实体更新回调。返回 true 跳过后续更新 |
| `noRepair` | bool | false | 是否不可修复 |
| `getEntityLifeSpan` | IGetEntityLifeSpan | null | 掉落物存活时间回调。返回 tick 数（默认 6000） |

#### IEntityItemUpdate（函数式接口）

> `import mods.zenutils.cotx.IEntityItemUpdate;`

参数：`IEntityItem`。返回 `bool`（true=跳过后续更新）。

#### IGetEntityLifeSpan（函数式接口）

> `import mods.zenutils.cotx.IGetEntityLifeSpan;`

参数：`IItemStack`, `IWorld`。返回 `int`（tick 数，默认 6000）。

### EnergyItem（能量物品）

> `import mods.zenutils.cotx.EnergyItem;`

继承 `ExpandItem`，无额外 ZenProperties。通过 `VanillaFactory.createEnergyItem` 创建。

### LateSetCoTFunction 物品部分

| Bracket | 返回 | 说明 |
|------|------|------|
| `<cotItem:name>` | IItemRepresentation | 获取 ContentTweaker 物品 |

| 属性 | 回调签名 | 说明 |
|------|------|------|
| `IItemRepresentation.onItemUse` | (player, world, pos, hand, facing, blockHit) → ActionResult | 物品使用回调 |

```zenscript
#loader crafttweaker reloadable
import mods.zenutils.cotx.LateSetCoTFunction;

<cotItem:test_item>.onItemUse = function(player, world, pos, hand, facing, blockHit) {
    // 自定义物品使用逻辑
    return ActionResult.success();
};
```
