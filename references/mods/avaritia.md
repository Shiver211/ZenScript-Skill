# Avaritia CraftTweaker API 参考

> Mod ID: `avaritia`
> 前置条件: ModTweaker

## API 列表

### ExtremeCrafting（终极工作台）

> `import mods.avaritia.ExtremeCrafting;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addShaped(IItemStack output, IIngredient[][] ingredients)` | void | 添加有序终极合成配方，`ingredients` 为 9x9 二维数组 |
| `.addShapeless(string placeholder, IItemStack output, IIngredient[] ingredients)` | void | 添加无序终极合成配方，`placeholder` 为占位符（每个配方需不同） |
| `.remove(IItemStack output)` | void | 移除终极合成配方 |

---

### Compressor（中子态素压缩机）

> `import mods.avaritia.Compressor;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.add(string placeholder, IItemStack output, int amount, IItemStack input)` | void | 添加压缩配方，`placeholder` 为占位符（每个配方需不同），`amount` 为所需材料数量 |

## 使用示例

### 有序终极合成

```zenscript
import mods.avaritia.ExtremeCrafting;

// 9x9 合成配方
mods.avaritia.ExtremeCrafting.addShaped(<Avaritia:Infinity_Bow> * 1,
    [[null, null, null, null, null, null, null, null, null],
     [null, null, null, null, null, null, null, null, null],
     [null, null, null, null, <ThaumicTinkerer:brightNitor>, null, null, null, null],
     [null, null, null, <Botania:manaResource:5>, <minecraft:diamond_block>, <Botania:manaResource:5>, null, null, null],
     [null, null, <ThaumicTinkerer:brightNitor>, <minecraft:diamond_block>, <minecraft:bow>, <minecraft:diamond_block>, <ThaumicTinkerer:brightNitor>, null, null],
     [null, null, null, <Botania:manaResource:5>, <minecraft:diamond_block>, <Botania:manaResource:5>, null, null, null],
     [null, null, null, null, <ThaumicTinkerer:brightNitor>, null, null, null, null],
     [null, null, null, null, null, null, null, null, null],
     [null, null, null, null, null, null, null, null, null]]);
```

### 中子态素压缩机

```zenscript
import mods.avaritia.Compressor;

// 压缩配方：12450个石头 → 黑曜石
mods.avaritia.Compressor.add("obsidian", <minecraft:obsidian>, 12450, <minecraft:stone>);
```

## 注意事项

- 需要安装 ModTweaker 模组
- `placeholder` 参数用于区分不同配方，每个配方需使用不同的占位符
- 终极工作台为 9x9 合成格
