# Chest Cavity Legacy - CraftTweaker API 参考

## 概述

本文档是 Chest Cavity Legacy 模组的 CraftTweaker API 参考手册。

---

## 1. 器官数据管理 (`mods.chestcavity.OrganData`)

### 注册类

| API | 说明 |
|-----|------|
| `OrganData.register(item, scores)` | 注册新器官 |
| `OrganData.registerPseudo(item, scores)` | 注册伪器官(不会有归属，也不会发生排异) |

### 修改类

| API | 说明 |
|-----|------|
| `OrganData.addScore(item, scoreId, value)` | 添加器官分数(Ability属于特殊Score,也用此方法添加) |
| `OrganData.removeScore(item, scoreId)` | 移除器官分数 |
| `OrganData.remove(item)` | 移除器官定义 |
| `OrganData.setPseudo(item, boolen)` | 设置伪器官标志 |

### 查询类

| API | 返回类型 | 说明 |
|-----|----------|------|
| `OrganData.get(item)` | OrganDataView | 获取器官数据视图 |
| `data.isPseudoOrgan` | bool | 是否为伪器官 |
| `data.organScores` | float[string] | 器官分数映射 |

### 示例

> **注意**：`addScore(item, scoreId, value)` 中 value 为放入一整组物品时增加的分数。
> 传入 `float[string]` 类型的 Map 时，**必须**使用 `as float[string]` 显式类型转换。
> ZenScript 的小数字面量默认为 `double` 类型，不加转换可能导致类型不匹配。
> 此规则适用于所有接受 `float[string]` 参数的 API（包括 `ChestCavityType` 的特殊器官注册等）。

```zenscript
import mods.chestcavity.OrganData;

// 注册新器官（注意 as float[string] 转换）
OrganData.register(<minecraft:apple>, {
    "health": 3.0,
    "strength": 1.5,
    "regeneration": 0.5
} as float[string]);

// 注册伪器官
OrganData.registerPseudo(<minecraft:gold_nugget>, {
    "ease_of_access": 4.0
} as float[string]);

// 修改现有器官
OrganData.addScore(<chestcavity:heart>, "health", 0.5);
OrganData.removeScore(<chestcavity:heart>, "luck");

// 查询器官数据
var organ = OrganData.get(<minecraft:apple>);
if (!isNull(organ)) {
    print("Is pseudo: " ~ organ.isPseudoOrgan);
    for scoreId, scoreValue in organ.organScores {
        print(scoreId ~ ": " ~ scoreValue);
    }
}
```

---

## 2. 胸腔类型管理 (`mods.chestcavity.ChestCavityType`)

### 注册类

| API | 说明 |
|-----|------|
| `ChestCavityType.register(typeId)` | 注册新的空胸腔类型 |

### 修改类

| API | 说明 |
|-----|------|
| `ChestCavityType.setSize(typeId, columns, rows)` | 设置胸腔类型的网格尺寸（列数与行数，界面自动适配居中） |
| `ChestCavityType.setSize(typeId, slots)` | 按总槽位数设置尺寸（默认以 9 列排列，不足 9 则为单行） |
| `ChestCavityType.addBaseScore(typeId, scoreId, value)` | 添加固有基础分数（胸腔自带的底分，即使不装任何器官也始终生效） |
| `ChestCavityType.removeBaseScore(typeId, scoreId)` | 移除固有基础分数 |
| `ChestCavityType.setSlot(typeId, index, stack)` | 设置默认槽位器官（定义生物健康状态下的默认胸腔配置） |
| `ChestCavityType.clearSlots(typeId)` | 清空所有默认槽位 |
| `ChestCavityType.addForbiddenSlot(typeId, slot)` | 添加禁止槽位（槽位索引从 0 开始计数） |
| `ChestCavityType.removeForbiddenSlot(typeId, slot)` | 移除禁止槽位 |
| `ChestCavityType.setDropRateMultiplier(typeId, value)` | 设置掉落倍率 |
| `ChestCavityType.setBossChestCavity(typeId, value)` | 设置 Boss 标志 |
| `ChestCavityType.setPlayerChestCavity(typeId, value)` | 设置玩家标志 |
| `ChestCavityType.addExceptionalOrgan(typeId, item, scores)` | 添加特殊器官(按物品) |
| `ChestCavityType.addExceptionalOrganByOre(typeId, oreName, scores)` | 添加特殊器官(按矿辞) |
| `ChestCavityType.clearExceptionalOrgans(typeId)` | 清空特殊器官 |

### 查询类

| API | 返回类型 | 说明 |
|-----|----------|------|
| `ChestCavityType.isBossChestCavity(typeId)` | bool | 是否为 Boss 类型 |
| `ChestCavityType.isPlayerChestCavity(typeId)` | bool | 是否为玩家类型 |
| `ChestCavityType.getDropRateMultiplier(typeId)` | float | 获取掉落倍率 |
| `ChestCavityType.getColumns(typeId)` | int | 获取胸腔网格列数 |
| `ChestCavityType.getRows(typeId)` | int | 获取胸腔网格行数 |
| `ChestCavityType.getSlotCount(typeId)` | int | 获取胸腔总槽位数 |

### 机制说明：胸腔基础分数（固有底分）与默认基准总分

> **1. 胸腔基础分数（`addBaseScore`）= 固有底分**
> - 代表该生物**肉身天生自带的分数**。即使生物胸腔完全被掏空，该分数也始终生效！
> - *典型例子*：原版骷髅体内没有肌肉、心脏和胃，但系统在它的 `baseOrganScores` 中赋予了力量、速度和消化底分，因此无肉身的骷髅依然能跑能攻击。
>
> **2. 默认基准总分（`defaultOrganScores`）= 固有底分 + 默认槽位器官总分**
> - 系统的各种生理惩罚（如减速、瘫痪）是对比**当前实际总分**与**默认基准总分**计算差值的。
> - **脊椎与瘫痪**：仅当生物基准神经分 `defaultNerves != 0` 且当前神经分 `nerves <= 0` 时才会判定为瘫痪（移速 -100%）。天生无脊椎生物（如史莱姆、或未配置脊椎的自定义类型）默认神经为 0，系统会将其判定为天生无神经生物，**不会触发瘫痪**。
> - **肌肉与速度**：移速修正为 `(当前速度分 - 基准速度分) * 系数`。如果未配置肌肉且无速度底分，基准为 0，生物将保留原版原生速度。

### 示例

> **注意：槽位索引以 0 开始计数（0-indexed）**。
> 例如设置 `setSize("demon", 2, 1)`（2 列 1 行，共 2 个槽位），有效槽位索引为 `0` 和 `1`。如果填写 `2`（代表第 3 格）会超出范围而不会生效。

```zenscript
import mods.chestcavity.ChestCavityType;
import mods.chestcavity.EntityAssignment;

// ===注册新的胸腔类型===
ChestCavityType.register("demon");

// 设置胸腔网格大小为 4 列 2 行（共 8 格）
ChestCavityType.setSize("demon", 4, 2);

// 配置新类型
ChestCavityType.setBossChestCavity("demon", true);
ChestCavityType.setDropRateMultiplier("demon", 3.0);
ChestCavityType.addBaseScore("demon", "health", 20.0);
ChestCavityType.addBaseScore("demon", "fire_resistant", 1.0);

// 设置默认器官布局（索引从 0 开始）
ChestCavityType.setSlot("demon", 0, <chestcavity:dragon_heart> * 1);
ChestCavityType.setSlot("demon", 1, <chestcavity:dragon_lung> * 1);
// 禁用第 4 个槽位（索引 3）
ChestCavityType.addForbiddenSlot("demon", 3);

// ===修改现有类型===
ChestCavityType.addBaseScore("human", "health", 5.0);
ChestCavityType.removeBaseScore("human", "luck");

// 查询类型属性
var isBoss = ChestCavityType.isBossChestCavity("demon");
var dropRate = ChestCavityType.getDropRateMultiplier("demon");
var cols = ChestCavityType.getColumns("demon");
var rows = ChestCavityType.getRows("demon");
var slots = ChestCavityType.getSlotCount("demon");
```

---

## 3. 实体分配管理 (`mods.chestcavity.EntityAssignment`)

### 修改类

| API | 说明 |
|-----|------|
| `EntityAssignment.register(entityId, typeId)` | 为实体注册胸腔类型 |
| `EntityAssignment.unregister(entityId)` | 移除实体的胸腔分配 |

### 查询类

| API | 返回类型 | 说明 |
|-----|----------|------|
| `EntityAssignment.getAssignedType(entityId)` | string | 获取实体的胸腔类型 |

### 示例

```zenscript
import mods.chestcavity.EntityAssignment;

// 为其他 mod 的生物添加胸腔
EntityAssignment.register("twilightforest:wild_boar", "carnivore");
EntityAssignment.register("twilightforest:bighorn_sheep", "herbivore");

// 移除实体的胸腔分配
EntityAssignment.unregister("minecraft:zombie");

// 查询实体的胸腔类型
var typeId = EntityAssignment.getAssignedType("minecraft:zombie");
print("Zombie type: " ~ typeId);
```

---

## 4. 掉落管理 (`mods.chestcavity.DropManager`)

### 修改类

| API | 说明 |
|-----|------|
| `DropManager.addOrganDrop(entityId, stack, weight)` | 添加掉落条目 |
| `DropManager.setDropProbability(entityId, value)` | 设置掉落概率 |
| `DropManager.removeOrganDrop(entityId, stack)` | 移除掉落条目 |
| `DropManager.removeAllOrganDrops(entityId)` | 移除所有自定义掉落 |

### 示例

```zenscript
import mods.chestcavity.DropManager;

// 添加掉落条目
DropManager.addOrganDrop("minecraft:zombie", <chestcavity:heart>, 5);
DropManager.addOrganDrop("minecraft:zombie", <minecraft:apple>, 10);

// 修改掉落概率
DropManager.setDropProbability("minecraft:zombie", 0.25);

// 移除掉落条目
DropManager.removeOrganDrop("minecraft:zombie", <chestcavity:heart>);

// 移除所有自定义掉落
DropManager.removeAllOrganDrops("minecraft:zombie");
```

---

## 5. Score(被动效果) 定义 (`mods.chestcavity.ScoreManager`)

### 修改类

| API | 说明 |
|-----|------|
| `ScoreManager.addScore(scoreId, displayName)` | 定义被动 score 的显示名称 |

### 示例

```zenscript
import mods.chestcavity.ScoreManager;

// 定义被动 score 显示名。该名称会用于器官 tooltip 和分数总结。
ScoreManager.addScore("spell_power", "Spell Power");
```

---

## 6. Ability(主动技能) 定义(`mods.chestcavity.AbilityManager`)

### 修改类

| API | 说明 |
|-----|------|
| `AbilityManager.registerAbility(scoreId, displayName, cooldownTicks)` | 注册新能力 score ID 并设置显示名（无回调，默认进入轮盘） |
| `AbilityManager.registerAbility(scoreId, displayName, cooldownTicks, handler)` | 注册新能力 score ID 并设置显示名（带回调，默认进入轮盘）。handler 中可调用 `event.cancel()` 取消本次能力激活 |

### 示例

```zenscript
import mods.chestcavity.AbilityManager;
import mods.chestcavity.event.AbilityActivatedEvent;

// 注册新的能力 score ID（无回调）
AbilityManager.registerAbility("fire_blast", "Fire Blast", 120);

// 注册新的能力 score ID（带回调）
AbilityManager.registerAbility("regeneration", "Regeneration", 60, function(event as AbilityActivatedEvent) {
    // 分数不足时取消激活
    if (event.score < 1.0) {
        event.cancel();
        return;
    }
    event.player.heal(2.0);
    event.entity.sendMessage("Regeneration triggered! Score: " ~ event.score);
});
```

---

## 7. 胸腔信息 API (`mods.chestcavity.ChestCavityHelper`)

### 获取胸腔对象

| API | 返回类型 | 说明 |
|-----|----------|------|
| `ChestCavityHelper.get(entity)` | IChestCavity | 获取实体的胸腔对象 |

### IChestCavity 对象 - 状态查询

| API | 返回类型 | 说明 |
|-----|----------|------|
| `cc.isOpened` | bool | 胸腔是否已打开 |
| `cc.slotCount` | int | 槽位总数量 |
| `cc.columns` | int | 网格列数 |
| `cc.rows` | int | 网格行数 |
| `cc.hasCustomSize` | bool | 是否设置了实体级独立自定义尺寸 |

### IChestCavity 对象 - 尺寸动态调整

| API | 说明 |
|-----|------|
| `cc.setSize(columns, rows)` | 动态调整当前实体的胸腔尺寸（列数与行数）。扩容自动补空格，缩容安全掉落多余器官 |
| `cc.setSize(slots)` | 按总槽位数动态调整当前实体的胸腔尺寸（自适应排列） |
| `cc.resetSize()` | 清除实体的独立尺寸设置，恢复回所属生物类型的默认尺寸 |

### IChestCavity 对象 - Score 查询

| API | 返回类型 | 说明 |
|-----|----------|------|
| `cc.getOrganScore(scoreId)` | float | 获取单个 score 值 |
| `cc.getOrganScores()` | float[string] | 获取所有 scores |

### IChestCavity 对象 - 器官查询

| API | 返回类型 | 说明 |
|-----|----------|------|
| `cc.hasOrgan(item)` | bool | 检查是否有某个器官 |
| `cc.getOrganCount(item)` | int | 获取器官数量 |
| `cc.getOrganSlots(item)` | int[] | 获取器官所在槽位列表 |
| `cc.getOrgan(slot)` | IItemStack | 获取指定槽位器官 |

### 槽位操作

| API | 说明 |
|-----|------|
| `cc.setOrgan(slot, stack)` | 设置槽位器官 |

### 操作类

| API | 说明 |
|-----|------|
| `cc.recalculateScores()` | 强制重新计算分数 |
| `cc.openChestCavity()` | 强制打开胸腔 |

### 示例

#### 基础查询与操作
```zenscript
import mods.chestcavity.ChestCavityHelper;

events.onPlayerTick(function(event as crafttweaker.event.PlayerTickEvent) {
    if (!event.player.world.remote && event.phase == "END") {
        var cc = ChestCavityHelper.get(event.player);
        if (!isNull(cc)) {
            // 状态查询
            var opened = cc.isOpened;
            var slotCount = cc.slotCount;
            var cols = cc.columns;
            var rows = cc.rows;
            var isCustom = cc.hasCustomSize;
            
            // Score 查询
            var health = cc.getOrganScore("health");
            var scores = cc.getOrganScores();
            
            // 器官查询
            var hasHeart = cc.hasOrgan(<chestcavity:heart>);
            var heartCount = cc.getOrganCount(<chestcavity:heart>);
            var heartSlots = cc.getOrganSlots(<chestcavity:heart>);

            // 设置槽位器官
            cc.setOrgan(0, <minecraft:apple>);

            // 强制操作
            cc.recalculateScores();
            cc.openChestCavity();
        }
    }
});
```

#### 在游戏事件中动态改变胸腔大小（例如道具扩容）
```zenscript
import crafttweaker.event.PlayerRightClickItemEvent;
import mods.chestcavity.ChestCavityHelper;

// 监听玩家右键使用物品事件：使用下界之星为自己的胸腔扩展一行
events.onPlayerRightClickItem(function(event as PlayerRightClickItemEvent) {
    var player = event.player;
    var world = event.world;
    var item = event.item;

    if (!world.remote && !isNull(item) && item.definition.id == "minecraft:nether_star") {
        var cc = ChestCavityHelper.get(player);
        if (!isNull(cc)) {
            if (cc.rows < 6) {
                var newRows = cc.rows + 1;
                cc.setSize(cc.columns, newRows);
                player.sendChat("§a你的胸腔发生了异变！当前尺寸扩充为: " ~ cc.columns ~ "x" ~ newRows);
            } else {
                player.sendChat("§c你的胸腔已经扩充到了极限！");
            }
        }
    }
});
```

---

## 9. 事件

### `onAbilityActivated` - 主动能力触发

| 属性 | 类型 | 说明 |
|------|------|------|
| `event.entity` | IEntityLivingBase | 触发能力的实体 |
| `event.player` | IPlayer | 触发能力的玩家（如果是玩家） |
| `event.world` | IWorld | 世界对象 |
| `event.abilityId` | string | 能力 ID（如 "chestcavity:pyromancy"） |
| `event.score` | float | 当前能力的分数值 |
| `event.isServer` | bool | 是否在服务端 |
| `event.canceled` | bool | 是否已被取消 |

| 方法 | 说明 |
|------|------|
| `event.cancel()` | 取消本次能力激活。|

```zenscript
import mods.chestcavity.event.AbilityActivatedEvent;

events.onAbilityActivated(function(event as AbilityActivatedEvent) {
    // 可以通过 cancel() 取消能力激活
    if (event.abilityId == "dangerous_ability" && event.score < 2.0) {
        event.cancel();
        return;
    }
    if (event.abilityId == "fire_blast") {
        event.entity.sendMessage("Fire Blast! Score: " + event.score);
        event.player.world.newExplosion(
            event.player,
            event.player.x, event.player.y, event.player.z,
            3.0, false, false
        );
    }
});
```

### `onOrganEquipped` - 器官穿上

| 属性 | 类型 | 说明 |
|------|------|------|
| `event.entity` | IEntityLivingBase | 拥有胸腔的实体 |
| `event.player` | IPlayer | 玩家（如果是玩家） |
| `event.world` | IWorld | 世界对象 |
| `event.slot` | int | 槽位索引（0-26） |
| `event.organ` | IItemStack | 器官物品 |
| `event.organId` | string | 器官物品 ID（如 "chestcavity:heart"） |
| `event.isPseudoOrgan` | bool | 是否为伪器官 |
| `event.isServer` | bool | 是否在服务端 |

```zenscript
import mods.chestcavity.event.OrganEquippedEvent;

events.onOrganEquipped(function(event as OrganEquippedEvent) {
    event.entity.sendMessage("装备: " ~ event.organId ~ " 到槽位 " ~ event.slot);
    if (event.isPseudoOrgan) {
        event.entity.sendMessage("这是伪器官");
    }
});
```

### `onOrganUnequipped` - 器官脱下

| 属性 | 类型 | 说明 |
|------|------|------|
| `event.entity` | IEntityLivingBase | 拥有胸腔的实体 |
| `event.player` | IPlayer | 玩家（如果是玩家） |
| `event.world` | IWorld | 世界对象 |
| `event.slot` | int | 槽位索引（0-26） |
| `event.organ` | IItemStack | 器官物品 |
| `event.organId` | string | 器官物品 ID（如 "chestcavity:heart"） |
| `event.isPseudoOrgan` | bool | 是否为伪器官 |
| `event.isServer` | bool | 是否在服务端 |

```zenscript
import mods.chestcavity.event.OrganUnequippedEvent;

events.onOrganUnequipped(function(event as OrganUnequippedEvent) {
    event.entity.sendMessage("卸下: " ~ event.organId ~ " 从槽位 " ~ event.slot);
});
```
