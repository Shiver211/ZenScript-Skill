# 动态信息展示

> 导入: `import crafttweaker.data.IData;` `import crafttweaker.item.IItemStack;`

两种方式为物品添加动态信息显示。

---

## 方法一：Tooltip Function

通过 `addAdvancedTooltip` 为特定物品添加动态 tooltip，只在客户端运行。

### 基本用法

```zenscript
<item:minecraft:diamond_sword:*>.addAdvancedTooltip(function(item) {
    var value = getItemCertainNBT(item, "DeathCount");
    if (isNull(value)) return null;
    return "§o§a累计击杀：" ~ value.asString() ~ "§r";
});
```

### Shift 显示

```zenscript
<item:minecraft:diamond_sword:*>.addShiftTooltip(
    function(item) {    // Shift 时显示
        return "§a详细信息";
    },
    function(item) {    // 非 Shift 时显示
        return "§7按 Shift 查看";
    }
);
```

### 矿辞批量添加

```zenscript
<ore:myTools>.add(<item:minecraft:iron_axe:*>, <item:minecraft:diamond_axe:*>);
<ore:myTools>.addAdvancedTooltip(function(item) {
    return "Damage: " ~ item.damage ~ " / " ~ item.maxDamage;
});
```

**局限性**：需要指定物品或矿辞，不适合大批量物品。

---

## 方法二：display 下 Lore 标签

通过修改 `display.Lore` 实现，适配所有物品但操作复杂度高。

### 工具函数

```zenscript
// 获取 Lore
function getLore(item as IItemStack) as IData {
    if (isNull(item) || isNull(item.tag) || isNull(item.tag.display) || isNull(item.tag.display.Lore)) return null;
    return item.tag.display.Lore;
}

// 更新 Lore
function updateLore(item as IItemStack, lore as IData) as void {
    item.mutable().updateTag({"display": {"Lore": lore}} as IData);
}
```

### 添加/更新特定 Lore 行

```zenscript
var lore as IData = getLore(item);
var newLore as IData = [] as IData;
var found as bool = false;

if (!isNull(lore) && lore.length > 0) {
    for i in 0 .. lore.length {
        if (lore[i].asString().contains("§c累计击杀：")) {
            newLore += ["§c累计击杀：" ~ count.asString() ~ "§r"] as IData;
            found = true;
        } else {
            newLore += [lore[i]] as IData;
        }
    }
}
if (!found) newLore += ["§c累计击杀：" ~ count.asString() ~ "§r"] as IData;
updateLore(item, newLore);
```

**局限性**：字符串匹配不可靠，不建议用于数据存储（仅用于显示）。

---

## 对比

| 方式 | 适配范围 | 难度 | 实时性 | 数据可靠性 |
|------|---------|------|--------|-----------|
| Tooltip Function | 指定物品/矿辞 | 低 | 函数自动刷新 | NBT 存储可靠 |
| Lore 标签 | 所有物品 | 高 | 需事件驱动更新 | 字符串解析不可靠 |
