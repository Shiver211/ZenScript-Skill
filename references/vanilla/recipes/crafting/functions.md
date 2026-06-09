# 配方函数 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.recipes.ICraftingRecipe;`

配方函数（Recipe Function）和标记物品（Marked）。

---

## 配方函数（Recipe Function）

配方函数是 `recipes.addShaped` / `recipes.addShapeless` 的第 4 个参数，用于动态决定输出。

```zenscript
recipes.addShaped(recipeName, output, inputBox, recipeFunction, recipeAction);
```

### IRecipeFunction

- `out`: IItemStack — 配方定义的输出
- `ins`: 映射 — 包含所有 `.marked()` 标记的输入物品
- `cInfo`: ICraftingInfo — 合成信息（player, inventory 等）

返回值：
- 返回 IItemStack 作为实际输出
- 返回 null 表示不可合成

注意：此函数在合成网格每次变化时触发，而非取出结果时。不建议在此函数中修改 `ins`。

### IRecipeAction

配方事件是第 5 个参数，在合成完成后执行。此函数仅在结果与配方输出完全相同时触发。

- `out`: IItemStack — 合成结果
- `cInfo`: ICraftingInfo — 合成信息
- `player`: IPlayer — 合成的玩家（**可能为 null**）

### 示例

#### 假合成（JEI 可见但不可用）

```zenscript
recipes.addShapeless("fake", <minecraft:diamond>, [<ore:dirt>, <ore:dirt>, <ore:dirt>],
    function(out, ins, cInfo) {
        return null;  // 永不输出
    },
null);
```

#### 条件合成（仅下界可用）

```zenscript
recipes.addShapeless("nether_recipe", <minecraft:netherrack>,
    [<ore:cobblestone>, <ore:cobblestone>, <ore:cobblestone>],
    function(out, ins, cInfo) {
        return cInfo.player.world.dimension == -1 ? out : null;
    },
null);
```

#### 继承 NBT 的升级配方

```zenscript
import crafttweaker.data.IData;

recipes.addShaped("upgrade", <minecraft:diamond_pickaxe>, [
    [<ore:gemDiamond>, <ore:gemDiamond>, <ore:gemDiamond>],
    [null, <minecraft:golden_pickaxe:*>.marked("p"), null]
],
function(out, ins, cInfo) {
    var data as IData = ins.p.tag;  // 获取标记物品的 NBT
    return out.withTag(data);        // 继承到输出
},
null);
```

#### 修复工具（Meta 值操控）

```zenscript
recipes.addShapeless("repair", <minecraft:diamond_pickaxe>,
    [<minecraft:diamond_pickaxe>.onlyDamaged().marked("p"), <minecraft:diamond>],
    function(out, ins, cInfo) {
        return ins.p.withDamage(max(0, ins.p.damage - 500));
    },
null);
```

#### 合成后扣血

```zenscript
recipes.addShapeless("hurt_recipe", <minecraft:sapling>,
    [<minecraft:stick>, <minecraft:leaves>],
    function(out, ins, cInfo) {
        return cInfo.player.health > 5 ? out : null;  // 血量 > 5 才能合成
    },
    function(out, cInfo, player) {
        player.attackEntityFrom(<damageSource:MAGIC>, 5.0f);  // 扣 5 血
    }
);
```

---

## 标记物品（Marked）

使用 `.marked("name")` 标记输入物品，配方函数中通过 `ins.name` 访问。

```zenscript
recipes.addShaped("test", <minecraft:iron_pickaxe>, [
    [<ore:ingotIron>, <ore:ingotIron>, <ore:ingotIron>],
    [null, <minecraft:stick>.marked("stick"), null],
    [null, <minecraft:stick>, null]
],
function(out, ins, cInfo) {
    // ins.stick 访问标记的木棍
    return out;
},
null);
```
