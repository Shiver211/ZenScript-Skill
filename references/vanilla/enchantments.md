# Enchantments CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.enchantments.IEnchantment;`、`import crafttweaker.enchantments.IEnchantmentDefinition;`

附魔系统 API，用于操作附魔和物品附魔。

---

## API 列表

### IEnchantment（附魔实例）

> `import crafttweaker.enchantments.IEnchantment;`

IEnchantment 是附魔定义加上附魔等级的组合。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `definition` | IEnchantmentDefinition | 附魔定义 |
| `level` | int | 附魔等级 |
| `displayName` | string | 显示名称（可读写） |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.makeStack(int)` | IEnchantment | 创建指定等级的附魔 |
| `.makeTag()` | IData | 获取附魔的 NBT 标签（也可用 `ench as IData`） |

### IEnchantmentDefinition（附魔定义）

> `import crafttweaker.enchantments.IEnchantmentDefinition;`

IEnchantmentDefinition 是附魔本身的定义（不含等级），通过 `<enchantment:...>` 获取。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `id` | int | 附魔 ID |
| `name` | string | 附魔名称（可读写） |
| `maxLevel` | int | 最大等级 |
| `minLevel` | int | 最小等级 |
| `isAllowedOnBooks` | bool | 是否允许出现在附魔台 |
| `isTreasureEnchantment` | bool | 是否宝藏附魔 |
| `isCurse` | bool | 是否诅咒 |
| `registryName` | string | 注册名 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.makeEnchantment(int)` | IEnchantment | 创建指定等级的附魔（也可用 `ench * level`） |
| `.canApply(IItemStack)` | bool | 是否可应用到物品 |
| `.canApplyAtEnchantmentTable(IItemStack)` | bool | 是否可通过附魔台应用到物品 |
| `.getMinEnchantability(int)` | int | 获取指定等级的最低附魔需求 |
| `.getMaxEnchantability(int)` | int | 获取指定等级的最高附魔需求 |
| `.getTranslatedName(int)` | string | 获取翻译后的名称（如 "smite IV"） |
| `enchA == enchB` | bool | 比较两个附魔定义是否相同（基于 ID） |

---

## 使用示例

### 物品附魔方法

```zenscript
// 检查是否可在工作台附魔
<minecraft:diamond_pickaxe>.canApplyAtCraftingTable(<enchantment:fortune>);

// 添加附魔
<minecraft:diamond_pickaxe>.addEnchantment(<enchantment:fortune> * 3);  // 时运 III

// 获取物品附魔列表
<minecraft:diamond_pickaxe>.enchantments  // List<IEnchantment>

// 检查物品是否已附魔
<minecraft:diamond_pickaxe>.isEnchanted  // bool

// 检查物品是否可附魔
<minecraft:diamond_pickaxe>.isEnchantable  // bool
```

### 遍历所有附魔

```zenscript
for enchantment in game.enchantments {
    print(enchantment.name ~ ": " ~ enchantment.displayName);
}
```

---

## ContentTweaker 扩展（需安装 ContentTweaker）

> `import mods.contenttweaker.enchantments.EnchantmentBuilder;`

CoT 脚本第一行必须为 `#loader contenttweaker`。

### EnchantmentBuilder（附魔构建器）

用于创建自定义附魔。

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `EnchantmentBuilder.create(string name)` | EnchantmentBuilder | 创建附魔构建器 |

#### 属性

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `name` | string | — | 附魔名称 |
| `allowedOnBooks` | bool | — | 是否允许出现在附魔台 |
| `applicableSlots` | IEntityEquipmentSlot[] | [] | 可应用的装备槽位 |
| `curse` | bool | — | 是否诅咒 |
| `domain` | string | "contenttweaker" | 域名 |
| `maxLevel` | int | 1 | 最大等级 |
| `minLevel` | int | 1 | 最小等级 |
| `rarity` | string | — | 稀有度（使用下方方法设置） |
| `treasure` | bool | — | 是否宝藏附魔 |
| `type` | string | — | 附魔类型（使用下方方法设置） |

#### 稀有度设置方法

| 方法 | 说明 |
|------|------|
| `.setRarityCommon()` | 普通稀有度 |
| `.setRarityUncommon()` | 较少见稀有度 |
| `.setRarityRare()` | 稀有稀有度 |
| `.setRarityVeryRare()` | 非常稀有稀有度 |

#### 类型设置方法

| 方法 | 说明 |
|------|------|
| `.setTypeAll()` | 所有类型 |
| `.setTypeArmor()` | 盔甲 |
| `.setTypeFeed()` | 腿甲 |
| `.setTypeLegs()` | 腿甲 |
| `.setTypeChest()` | 胸甲 |
| `.setTypeHead()` | 头盔 |
| `.setTypeWeapon()` | 武器 |
| `.setTypeDigger()` | 挖掘工具 |
| `.setTypeFishingRod()` | 钓鱼竿 |
| `.setTypeBreakable()` | 可损坏物品 |
| `.setTypeBow()` | 弓 |
| `.setTypeWearable()` | 可穿戴物品 |

#### 计算属性（函数）

| 属性 | 参数 | 返回 | 说明 |
|------|------|------|------|
| `canApply` | IEnchantmentDefinition, IItemStack | bool | 是否可应用到物品 |
| `canApplyAtEnchantmentTable` | IEnchantmentDefinition, IItemStack | bool | 是否可通过附魔台应用 |
| `canApplyTogether` | IEnchantmentDefinition, IEnchantmentDefinition | bool | 是否可与其他附魔共存 |
| `calcDamageByCreature` | IEnchantmentDefinition, int level, string creatureType | float | 计算对生物伤害 |
| `calcEnchantabilityMin` | IEnchantmentDefinition, int level | int | 计算最低附魔需求 |
| `calcEnchantabilityMax` | IEnchantmentDefinition, int level | int | 计算最高附魔需求 |
| `calcModifierDamage` | IEnchantmentDefinition, int level, IDamageSource | int | 计算伤害修正 |
| `calcTranslatedName` | IEnchantmentDefinition, int level | string | 计算翻译名称 |
| `onEntityDamaged` | IEnchantmentDefinition, IEntityLivingBase user, IEntity target, int level | void | 对实体造成伤害时触发 |
| `onUserHurt` | IEnchantmentDefinition, IEntityLivingBase user, IEntity attacker, int level | void | 使用者受伤时触发 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.register()` | IEnchantmentDefinition | 注册附魔并返回定义 |

### EnchantmentBuilder 示例

```zenscript
#loader contenttweaker
import mods.contenttweaker.enchantments.EnchantmentBuilder;

var builder = EnchantmentBuilder.create("my_enchantment");
builder.applicableSlots = [mainHand, offhand, feet, legs, chest, head];
builder.setTypeAll();
builder.setRarityVeryRare();
builder.maxLevel = 3;

builder.onUserHurt = function(thisEnch, entity, attacker, level) {
    entity.health = entity.maxHealth;
    if (entity instanceof crafttweaker.player.IPlayer) {
        val player as crafttweaker.player.IPlayer = entity;
        player.foodStats.addStats(100, 10.0f);
    }
};

builder.register();
```
