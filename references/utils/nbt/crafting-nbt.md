# 工作台配方中的 NBT 操作

> 导入: `import crafttweaker.data.IData;` `import crafttweaker.item.IItemStack;`

---

## 配方函数与事件函数

```zenscript
recipes.addShapeless("name", output, [inputs],
    // 配方函数：确定实际输出，每次摆好配方时调用
    function(out, ins, cInfo) {
        // out: 名义产物 IItemStack
        // ins: 标记物品映射（如 ins.sword 获取 .marked("sword") 的物品）
        // cInfo: ICraftInfo，含 .player、.world、.inventory 等
        return out;  // 返回实际输出（null 则拒绝合成）
    },
    // 事件函数：合成取出瞬间执行
    function(out, cInfo, player) {
        // player 可能为 null
        if (isNull(player) || cInfo.world.remote) return;
        // 扣经验、扣血等效果
    }
);
```

两个函数均可为 `null`。

---

## 继承 NBT

```zenscript
// 只继承附魔
function(out, ins, cInfo) {
    var sword as IItemStack = ins.sword;
    if (!isNull(sword.tag) && !isNull(sword.tag.memberGet("ench"))) {
        var enchNBT as IData = {"ench": sword.tag.ench} as IData;
        return out.withTag({"Unbreakable": 1 as byte} + enchNBT);
    }
    return out.withTag({"Unbreakable": 1 as byte});
}
```

## 拒绝合成

```zenscript
function(out, ins, cInfo) {
    if (!isNull(cInfo.player) && cInfo.player.xp < 20 && !cInfo.player.creative) {
        cInfo.player.sendChat("§4经验不足！");
        return null;  // 拒绝合成
    }
    return out;
}
```

## 返还物品

```zenscript
// 合成后返还指定物品（如牛奶合成蛋糕返还桶）
<item:minecraft:diamond>.withTag({key: value}).transformReplace(<item:minecraft:diamond>)
```

## JEI 提示

在名义产物上添加 Lore 提示，配方函数中去掉：

```zenscript
// 名义产物带提示
<item:minecraft:diamond>.withTag({
    "display": {"Lore": ["§e需要 21 级经验§r"]}
} as IData),

// 配方函数中去掉提示
function(out, ins, cInfo) {
    return out.withEmptyTag().withTag(actualNBT);
}
```
