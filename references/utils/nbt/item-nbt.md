# 物品 NBT 操作

> 导入: `import crafttweaker.data.IData;` `import crafttweaker.item.IItemStack;`

---

## 获取与判空

```zenscript
var nbt as IData = item.tag;

// 逐层判空
if (isNull(item.tag)) { /* 无 NBT */ }
if (isNull(item.tag.memberGet("display"))) { /* 无 display */ }
if (isNull(item.tag.display.Lore)) { /* 无 Lore */ }
```

## 读取

```zenscript
var repairCost as int = item.tag.RepairCost.asInt();
var name as string = item.tag.display.Name.asString();
var lore as IData = item.tag.display.Lore;
```

## 修改（updateTag）

```zenscript
// 合并更新，必须先 mutable()
item.mutable().updateTag({"RepairCost": 0 as byte} as IData);

// 添加 Lore
item.mutable().updateTag({"display": {"Lore": ["§b标签§r"]}} as IData);
```

## 替换（withTag）

```zenscript
// 直接替换整个 NBT（丢失原有数据）
var newItem = item.withTag({"Unbreakable": 1 as byte} as IData);
```

## 移除

```zenscript
item.mutable().removeTag("display");
```

## 常用工具函数

```zenscript
// 获取物品指定 Key 的 NBT
function getItemCertainNBT(item as IItemStack, nbtString as string) as IData {
    if (!isNull(item) && !isNull(nbtString) && !isNull(item.tag) && !isNull(item.tag.memberGet(nbtString))) {
        return item.tag.memberGet(nbtString);
    }
    return null;
}

// 判断物品是否拥有指定附魔
function itemHasCertainEnchantment(item as IItemStack, enchDef as IEnchantmentDefinition) as bool {
    if (!item.isEnchanted) return false;
    for ench in item.enchantments {
        if (ench.definition == enchDef) return true;
    }
    return false;
}
```

---

## 常见错误

| 错误 | 原因 | 修复 |
|------|------|------|
| 空指针访问 NBT | 未逐层判空 | 从外到内 `isNull()` 检查 |
| `updateTag` 后立即读取 | 更新未生效 | 用标记变量记录状态 |
