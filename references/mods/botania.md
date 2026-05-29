# Botania CraftTweaker API 参考

> Mod ID: `botania`
> 前置条件: Modtweaker
> 导入: `import mods.botania.*;`

## API 列表

### Apothecary（花药台）

> `import mods.botania.Apothecary;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IItemStack output, IIngredient[] input)` | void | 添加配方 |
| `.addRecipe(String output, IIngredient[] input)` | void | 添加配方（Botania 花名） |
| `.removeRecipe(IItemStack output)` | void | 按输出移除配方 |
| `.removeRecipe(String output)` | void | 按花名移除配方 |

**注意**: 花药台**硬编码只接受花瓣**，虽然可以用任何物品添加配方，但只能投掷花瓣进去。

### Brew（酿造）

> `import mods.botania.Brew;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient[] input, String brewName)` | void | 添加酿造配方。`brewName` 为 Botania 注册的酿造名，可用 `/ct botbrews` 查看 |
| `.removeRecipe(String brewName)` | void | 按酿造名移除配方 |

### ElvenTrade（精灵交易）

> `import mods.botania.ElvenTrade;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient[] outputs, IIngredient[] input)` | void | 添加精灵交易配方 |
| `.removeRecipe(IIngredient output)` | void | 按输出移除配方 |

### ManaInfusion（魔力灌注）

> `import mods.botania.ManaInfusion;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addInfusion(IItemStack output, IIngredient input, int mana)` | void | 添加魔力池灌注配方 |
| `.addAlchemy(IItemStack output, IIngredient input, int mana)` | void | 添加炼金催化配方 |
| `.addConjuration(IItemStack output, IIngredient input, int mana)` | void | 添加炼造催化配方 |
| `.removeRecipe(IIngredient output)` | void | 按输出移除配方 |

### Orechid（凝矿兰）

> `import mods.botania.Orechid;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addOre(IOreDictEntry oreDict, int weight)` | void | 添加生成矿石。`weight` 为权重，**相同权重的两个配方会导致游戏崩溃** |
| `.addOre(String oreDict, int weight)` | void | 同上，使用字符串矿辞名 |
| `.removeOre(IOreDictEntry oreDict)` | void | 按矿辞移除配方 |
| `.removeOre(String oreDict)` | void | 同上，使用字符串矿辞名 |

### OrechidIgnem（炎矿兰）

> `import mods.botania.OrechidIgnem;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addOre(IOreDictEntry oreDict, int weight)` | void | 添加生成矿石 |
| `.addOre(String oreDict, int weight)` | void | 同上，使用字符串矿辞名 |
| `.removeOre(IOreDictEntry oreDict)` | void | 按矿辞移除配方 |
| `.removeOre(String oreDict)` | void | 同上，使用字符串矿辞名 |

### PureDaisy（白雏菊）

> `import mods.botania.PureDaisy;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IIngredient blockInput, IItemStack blockOutput, @Optional int time)` | void | 添加白雏菊配方。`time` 默认 150 tick |
| `.removeRecipe(IIngredient output)` | void | 按输出移除配方 |

### RuneAltar（符文祭坛）

> `import mods.botania.RuneAltar;`

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IItemStack output, IIngredient[] input, int mana)` | void | 添加符文祭坛配方 |
| `.removeRecipe(IIngredient output)` | void | 按输出移除配方 |

### Lexicon（植物魔法辞典）

> `import mods.botania.Lexicon;`

#### 页面管理

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addBrewPage(String name, String entry, int page_number, String brew, IIngredient[] recipe, String bottomText)` | void | 添加酿造页面 |
| `.addCraftingPage(String name, String entry, int page_number, String... recipeNames)` | void | 添加合成页面 |
| `.addElvenPage(String name, String entry, int page_number, IItemStack[] outputs, IIngredient[][] inputs)` | void | 添加精灵交易页面 |
| `.addEntityPage(String name, String entry, int page_number, String entity, int size)` | void | 添加实体页面 |
| `.addImagePage(String name, String entry, int page_number, String resource)` | void | 添加图片页面 |
| `.addLorePage(String name, String entry, int page_number)` | void | 添加传说页面 |
| `.addInfusionPage(String name, String entry, int page_number, IItemStack[] outputs, IIngredient[] inputs, int[] mana)` | void | 添加魔力注入页面 |
| `.addAlchemyPage(String name, String entry, int page_number, IItemStack[] outputs, IIngredient[] inputs, int[] mana)` | void | 添加炼金催化页面 |
| `.addConjurationPage(String name, String entry, int page_number, IItemStack[] outputs, IIngredient[] inputs, int[] mana)` | void | 添加复制催化页面 |
| `.addPetalPage(String name, String entry, int page_number, IItemStack[] outputs, IIngredient[][] inputs)` | void | 添加花瓣台页面 |
| `.addRunePage(String name, String entry, int page_number, IItemStack[] outputs, IIngredient[][] inputs, int[] mana)` | void | 添加符文祭坛页面 |
| `.addTextPage(String name, String entry, int page_number)` | void | 添加文本页面 |
| `.removePage(String entry, int page_number)` | void | 移除指定页面 |

#### 条目管理

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addEntry(String entry, String catagory, IItemStack stack)` | void | 添加辞典条目 |
| `.removeEntry(String entry)` | void | 移除辞典条目 |

#### 分类管理

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addCategory(String name)` | void | 添加辞典分类 |
| `.removeCategory(String name)` | void | 移除辞典分类 |
| `.setCategoryIcon(String name, String icon)` | void | 设置分类图标 |

#### 配方映射

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipeMapping(IItemStack stack, String Entry, int page)` | void | 添加配方映射 |
| `.removeRecipeMapping(IItemStack stack)` | void | 移除配方映射 |

---

## RandomTweaker 扩展（需安装 RandomTweaker）

### IManaItemHandler（魔力物品操作）

> `import mods.randomtweaker.botania.IManaItemHandler;`

暴露植魔的魔力操作方法，可向玩家提取/发送魔力。

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getManaItems(player as IPlayer)` | IItemStack[] | 返回玩家背包中可容纳魔力的物品 |
| `.getManaBaubles(player as IPlayer)` | IItemStack[int] | 返回玩家饰品栏中的魔力物品（非魔力饰品），int 为饰品格位置 |
| `.requestMana(stack as IItemStack, player as IPlayer, manaToGet as int, remove as bool)` | int | 提取魔力。`stack` 为提取魔力的物品（跳过该物品以免自扣），`remove` 为是否真实提取。返回实际减少的魔力 |
| `.requestManaExact(stack as IItemStack, player as IPlayer, manaToGet as int, remove as bool)` | bool | 精确提取魔力。魔力不足 manaToGet 时返回 false |
| `.requestManaForTool(stack as IItemStack, player as IPlayer, manaToGet as int, remove as bool)` | int | 提取魔力（考虑魔力减免）。返回本应减少的魔力数量 |
| `.requestManaExactForTool(stack as IItemStack, player as IPlayer, manaToGet as int, remove as bool)` | bool | 精确提取魔力（考虑魔力减免） |
| `.dispatchMana(stack as IItemStack, player as IPlayer, manaToSend as int, add as bool)` | int | 向玩家发送魔力。`add` 为是否真实发送。返回实际发送的魔力 |
| `.dispatchManaExact(stack as IItemStack, player as IPlayer, manaToSend as int, add as bool)` | bool | 精确发送魔力。可容纳量不足时返回 false |
| `.getFullDiscountForTools(player as IPlayer, tool as IItemStack)` | float | 返回玩家的魔力减免值 |

**注意：** 操纵魔力的方法优先检查背包，没有符合条件的魔力容器再去饰品栏找。

#### 使用示例

```zenscript
import mods.randomtweaker.botania.IManaItemHandler;

// 获取魔力减免
var discount as float = IManaItemHandler.getFullDiscountForTools(player, <minecraft:stick>);

// 模拟提取 100 魔力，足够则真实提取
if(IManaItemHandler.requestMana(<minecraft:stick>, player, 100, false) >= 80) {
    IManaItemHandler.requestMana(<minecraft:stick>, player, 100, true);
}

// 精确提取 10000 魔力
if(IManaItemHandler.requestManaExact(<minecraft:stick>, player, 10000, false)) {
    IManaItemHandler.requestManaExact(<minecraft:stick>, player, 10000, true);
}

// 考虑魔力减免的提取（穿泰拉套减免 20%）
IManaItemHandler.requestManaForTool(<minecraft:stick>, player, 1000, true);

// 发送魔力
IManaItemHandler.dispatchMana(<minecraft:stick>, player, 10000, true);
```

### ISubTileEntity（自定义植魔花基类）

> `import mods.randomtweaker.cote.SubTileEntity;`

需同时安装 ContentTweaker。用于自定义产魔花或功能花的基础类。

贴图存放位置：`resources/contenttweaker/textures/blocks/unlocalizedName.png`

#### @ZenGetter / @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `unlocalizedName` | string | 注册名 |
| `range` | int | 自定义花的工作范围 |
| `color` | int | 自定义花的魔力条颜色 |
| `maxMana` | int | 最大魔力容量 |
| `acceptsRedstone` | bool | 是否接受红石信号 |
| `overgrowthAffected` | bool | 是否受蕴魔土的影响 |

#### 本地化

```
tile.botania:flower.自定义花的名字.name = 本地化名称
tile.botania:flower.自定义花的名字.reference = 本地化 tooltip
```

### ISubTileEntityGenerating（产魔花）

> `import mods.randomtweaker.cote.ISubTileEntityGenerating;`

继承 ISubTileEntity。创建方式：`VanillaFactory.createSubTileGenerating(name, color)`

#### @ZenGetter / @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `PassiveFlower` | bool | 是否为被动产魔花 |
| `valueForPassiveGeneration` | int | 每 Tick 被动产出多少魔力（受 canGeneratePassively 函数影响） |
| `delayBetweenPassiveGeneration` | int | 被动产魔后的冷却时间（Tick） |
| `shouldSyncPassiveGeneration` | bool | 被动产魔时是否同步 TileEntity |

#### 使用示例

```zenscript
#loader contenttweaker
import mods.contenttweaker.VanillaFactory;
import mods.randomtweaker.cote.ISubTileEntityGenerating;

var test_flower0 as ISubTileEntityGenerating = VanillaFactory.createSubTileGenerating("test_flower0", 0xFFFFFF);
test_flower0.maxMana = 2000;
test_flower0.onUpdate = function(subtile, world, pos) {
    if(!world.remote) {
        if(isNull(subtile.data.time))
            subtile.updateCustomData({time : 0});
        if(!isNull(subtile.data.time)) {
            subtile.updateCustomData({time : subtile.data.time.asInt() + 1});
            if(subtile.data.time.asInt() == 100) {
                server.commandManager.executeCommand(server, "dididididididididi~~~");
            }
        }
    }
};
test_flower0.register();
```

### ISubTileEntityFunctional（功能花）

> `import mods.randomtweaker.cote.ISubTileEntityFunctional;`

继承 ISubTileEntity。创建方式：`VanillaFactory.createSubTileFunctional(name, color)`

小功能花贴图：`resources/contenttweaker/textures/blocks/unlocalizedName_chibi.png`

#### @ZenGetter / @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `hasMini` | bool | 是否创建小型功能花 |
| `miniRange` | int | 小型功能花的工作范围 |

#### 使用示例

```zenscript
#loader contenttweaker
import mods.contenttweaker.VanillaFactory;
import mods.randomtweaker.cote.ISubTileEntityFunctional;

var test_flower1 as ISubTileEntityFunctional = VanillaFactory.createSubTileFunctional("test_flower1", 0x000000);
test_flower1.maxMana = 10086;
test_flower1.register();
```

### ISubTileFunctions（自定义花回调函数）

自定义花可使用的回调函数。以下函数均赋值给对应的 ISubTileEntity 实例。

#### onBlockAdded

当自定义花被添加到世界上时调用。

> `import mods.randomtweaker.cote.BlockAdded;`

```zenscript
subTileEntityObj.onBlockAdded = function(world as IWorld, pos as IBlockPos, state as IBlockState) { };
```

#### canSelect

森林法杖是否可以选择此自定义花并绑定到方块上。需返回 bool。

> `import mods.randomtweaker.cote.CanSelect;`

```zenscript
subTileEntityObj.canSelect = function(player as ICTPlayer, wand as IItemStack, pos as IBlockPos, side as IFacing) {
    return true;
};
```

#### onBlockPlaceBy

当自定义花被一个实体放置在世界时调用。

> `import mods.randomtweaker.cote.BlockPlacedBy;`

```zenscript
subTileEntityObj.onBlockPlaceBy = function(world as IWorld, pos as IBlockPos, state as IBlockState, entity as IEntityLivingBase, stack as IItemStack) { };
```

#### onUpdate

每 Tick 调用。

> `import mods.randomtweaker.cote.Update;`

```zenscript
subTileEntityObj.onUpdate = function(subtile as SubTileEntityInGame, world as IWorld, pos as IBlockPos) { };
```

#### onBlockHarvested

当自定义花被挖掘完时调用。

> `import mods.randomtweaker.cote.BlockHarvested;`

```zenscript
subTileEntityObj.onBlockHarvested = function(world as IWorld, pos as IBlockPos, state as IBlockState, player as ICTPlayer) { };
```

#### onBlockActivated

当玩家右键自定义花时调用。需返回 bool。

> `import mods.randomtweaker.cote.BlockActivated;`

```zenscript
subTileEntityObj.onBlockActivated = function(world as IWorld, pos as IBlockPos, state as IBlockState, player as ICTPlayer, hand as Hand, side as IFacing, hitX as float, hitY as float, hitZ as float) {
    if(<botania:twigwand>.matches(player.getHeldItem(hand))) {
        return false;
    }
    return true;
};
```

#### canGeneratePassively（仅产魔花）

自定义花是否被动产能。需返回 bool。

> `import mods.randomtweaker.cote.CanGeneratePassively;`

```zenscript
subTileGeneratingObj.canGeneratePassively = function(pos as IBlockPos, world as IWorld) { };
```

#### populateDropStackNBTs（仅产魔花）

决定挖掘完自定义花的掉落物。

> `import mods.randomtweaker.cote.PopulateDropStackNBTs;`

```zenscript
subTileGeneratingObj.populateDropStackNBTs = function(drops as IItemStack[]) { };
```

### TileData（ITileData，自定义花 NBT 数据管理）

> `import mods.randomtweaker.utils.ITileData;`

主要用于管理自定义植魔花内部的 NBT 数据。

#### @ZenGetter / @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `data` | IData | 存储的数据 |

#### 使用示例

```zenscript
TileDataObj.data = {"test" : "testValue" as string};
TileDataObj.data = {"testTwo" : {"testThree" : "testValue"}};
print(TileDataObj.data.asString());
```

---

## BotaniaTweaks 扩展（需安装 BotaniaTweaks）

BotaniaTweaks 替换了聚合板的方块和 TileEntity，支持通过 ZenScript 自定义聚合配方。可调整配方的输入、输出、魔力消耗，自定义聚合板下方多方块结构，以及配方完成后多方块结构的替换/消耗。

### Agglomeration（凝聚板配方）

> `import mods.botaniatweaks.Agglomeration;`

#### 方法方式

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addRecipe(IItemStack output, IIngredient[] inputs, @Optional int manaCost, @Optional int color1, @Optional int color2, @Optional IIngredient center, @Optional IIngredient edge, @Optional IIngredient corner, @Optional IIngredient centerReplace, @Optional IIngredient edgeReplace, @Optional IIngredient cornerReplace)` | void | 添加凝聚配方。output/inputs 必填，其余可选 |
| `.removeRecipe(...)` | void | 移除配方，参数同 addRecipe |
| `.removeDefaultRecipe()` | void | 移除默认泰拉钢凝聚配方（无参数） |

**参数说明：**

| 参数 | 默认值 | 类型 | 说明 |
|------|--------|------|------|
| `output` | *(必填)* | IItemStack | 配方输出 |
| `inputs` | *(必填)* | IIngredient[] | 配方输入（放在凝聚板上的物品） |
| `manaCost` | 500000 | int | 魔力消耗（默认值等价于泰拉钢） |
| `color1` | 0x0000FF | int | 配方开始时粒子颜色（0xRRGGBB） |
| `color2` | 0x00FF00 | int | 配方结束时粒子颜色（0xRRGGBB） |
| `center` | `<botania:livingrock>` | IIngredient | 凝聚板正下方的方块 |
| `edge` | `<minecraft:lapis_block>` | IIngredient | 凝聚板四边下方的方块 |
| `corner` | `<botania:livingrock>` | IIngredient | 凝聚板四角下方的方块 |
| `centerReplace` | null | IIngredient | 配方完成后替换中心方块。null 则不替换 |
| `edgeReplace` | null | IIngredient | 配方完成后替换四边方块 |
| `cornerReplace` | null | IIngredient | 配方完成后替换四角方块 |

**注意：** 若指定 `center`，则必须同时指定 `edge` 和 `corner`（即必须定义完整的多方块结构，或全部使用默认值）。

### AgglomerationRecipe（凝聚配方对象）

> `import mods.botaniatweaks.AgglomerationRecipe;`

用于组织复杂配方的辅助类。构造方式：`AgglomerationRecipe.create()`

#### @ZenSetter（字段）

| 字段 | 默认值 | 类型 | 说明 |
|------|--------|------|------|
| `output` | null | IItemStack | 配方输出 |
| `inputs` | null | IIngredient[] | 配方输入 |
| `manaCost` | 500000 | int | 魔力消耗 |
| `color1` | 0x0000FF | int | 开始粒子颜色 |
| `color2` | 0x00FF00 | int | 结束粒子颜色 |
| `multiblock` | 默认多方块 | AgglomerationMultiblock | 该配方的多方块结构 |

#### 方法（链式设置器）

所有 setter 方法均返回 `AgglomerationRecipe`，支持链式调用：`AgglomerationRecipe.create().output(<minecraft:stone>).inputs(...)`

### AgglomerationMultiblock（凝聚多方块结构）

> `import mods.botaniatweaks.AgglomerationMultiblock;`

构造方式：`AgglomerationMultiblock.create()`

#### @ZenSetter（字段）

| 字段 | 默认值 | 类型 | 说明 |
|------|--------|------|------|
| `center` | `<botania:livingrock>` | IIngredient | 凝聚板正下方的方块 |
| `edge` | `<minecraft:lapis_block>` | IIngredient | 四边下方的方块 |
| `corner` | `<botania:livingrock>` | IIngredient | 四角下方的方块 |
| `centerReplace` | null | IIngredient | 配方完成后替换中心方块 |
| `edgeReplace` | null | IIngredient | 配方完成后替换四边方块 |
| `cornerReplace` | null | IIngredient | 配方完成后替换四角方块 |

#### 快捷方法（链式）

| 方法 | 签名 | 说明 |
|------|------|------|
| `all` | `(IIngredient all)` | 将多方块结构的 9 个方块全部设为同一方块 |
| `checker` | `(IIngredient five, IIngredient four)` | 棋盘格布局。five 为中心+四角，four 为四边。例：`checker(<botania:livingrock>, <minecraft:lapis_block>)` |
| `allReplace` | `(IIngredient allReplace)` | 同 `all`，但设置替换方块 |
| `checkerReplace` | `(IIngredient fiveReplace, IIngredient fourReplace)` | 同 `checker`，但设置替换方块 |
| `consumeCenter` | `()` | 配方完成后消耗中心方块（等价于 `centerReplace(<minecraft:air>)`） |
| `consumeEdge` | `()` | 配方完成后消耗四边方块 |
| `consumeCorner` | `()` | 配方完成后消耗四角方块 |

### AgglomerationPage（凝聚辞典页面）

> `import mods.botaniatweaks.AgglomerationPage;`

为自定义凝聚配方在植物魔法辞典中添加页面。

| 方法 | 返回 | 说明 |
|------|------|------|
| `.add(String unlocalizedName, String entry, int pageNumber, AgglomerationRecipe recipe)` | void | 添加聚合辞典页面 |
| `.add(String unlocalizedName, String entry, int pageNumber, IItemStack output, IIngredient[] inputs, ...)` | void | 同上，直接传入配方参数（与 Agglomeration.addRecipe 参数相同） |

**参数说明：**

| 参数 | 类型 | 说明 |
|------|------|------|
| `unlocalizedName` | String | 未本地化名称，会被翻译并显示在配方下方 |
| `entry` | String | 植物魔法辞典条目的未本地化名称 |
| `pageNumber` | int | 该页面前面一页的页码 |