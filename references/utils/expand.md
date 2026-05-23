# 扩展方法（$expand）

为已有类添加自定义成员方法，使用 `$expand` 语法，`this` 指向被扩展的实例。

---

## 语法

```zenscript
$expand <ClassName>$<methodName>(参数) as <返回类型> {
    // this 指代 ClassName 对应的实例
    return ...;
}
```

调用时等同于该类的成员方法：

```zenscript
$expand IItemStack$show() as void {
    print(this.commandString);
}
<minecraft:apple>.show();
```

---

## 基本示例

### 默认参数

```zenscript
$expand string$wrap(prefix as string = "(", suffix as string = ")") as string {
    return prefix ~ this ~ suffix;
}
print("test".wrap());        // "(test)"
print("test".wrap("[", "]")); // "[test]"
```

### 获取物品指定 NBT

```zenscript
$expand IItemStack$getCertainNBT(nbtString as string) as IData {
    if (isNull(nbtString) || isNull(this.tag) || isNull(this.tag.memberGet(nbtString))) return null;
    return this.tag.memberGet(nbtString);
}
// 使用
item.getCertainNBT("RepairCost");
```

### 多层 NBT 访问

```zenscript
$expand IItemStack$getCertainNBT(nbtString as string[]) as IData {
    if (isNull(this.tag)) return null;
    var returnNBT as IData = this.tag;
    if (isNull(nbtString) || nbtString.length == 0) return returnNBT;
    for i in 0 .. nbtString.length {
        if (isNull(returnNBT.memberGet(nbtString[i]))) return null;
        returnNBT = returnNBT.memberGet(nbtString[i]);
    }
    return returnNBT;
}
// 使用
item.getCertainNBT(["display", "Lore"]);
```

### 获取指定附魔

```zenscript
$expand IItemStack$getCertainEnchantment(targetEnch as IEnchantmentDefinition) as IEnchantment {
    if (!this.isEnchanted) return null;
    for ench in this.enchantments {
        if (ench.definition == targetEnch) return ench;
    }
    return null;
}
```

### 检测匠魂特性

```zenscript
$expand IItemStack$hasCertainTConTrait(traitName as string) as bool {
    if (!isNull(traitName) && !isNull(this.getCertainNBT(["Traits"]))) {
        if (this.getCertainNBT(["Traits"]).asString().contains("\"" ~ traitName ~ "\"")) return true;
    }
    return false;
}

$expand IItemStack$certainTConTraitActive(traitName as string) as bool {
    if (
        this.hasCertainTConTrait(traitName) &&
        !isNull(this.getCertainNBT(["Stats"])) &&
        (isNull(this.getCertainNBT(["Stats", "Broken"])) ||
        this.getCertainNBT(["Stats", "Broken"]).asInt() == 0)
    ) return true;
    return false;
}
```

### 获取范围内实体

```zenscript
$expand IEntity$getEntityLivingBasesInCube(range as float) as IEntityLivingBase[] {
    var r as double = range as double;
    var areaStart as Position3f = Position3f.create(
        (this.getX() as double + r), (this.getY() as double + r), (this.getZ() as double + r)
    );
    var areaEnd as Position3f = Position3f.create(
        (this.getX() as double - r + 1.0d), (this.getY() as double - r + 1.0d), (this.getZ() as double - r + 1.0d)
    );
    var result as IEntityLivingBase[] = [];
    for entity in this.world.getEntitiesInArea(areaStart, areaEnd) {
        if (entity instanceof IEntityLivingBase) {
            result += entity as IEntityLivingBase;
        }
    }
    return result;
}
```

### 调试输出

```zenscript
$expand IPlayer$debugMessage() as void {
    this.sendChat("Check!");
}
```

---

## 注意事项

- `$expand` 脚本建议使用 `#priority 999` 确保优先加载
- `this` 不可能为空，无需判空
- 扩展方法在所有脚本中自动可用，无需 import
