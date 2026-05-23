# 自定义方块 CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: ContentTweaker（部分需要 ZenUtils 或 RandomTweaker）
> 导入: `import mods.contenttweaker.VanillaFactory;`、`import mods.contenttweaker.Block;`

ContentTweaker 和 ZenUtils 自定义方块 API。

---

## ContentTweaker 扩展（需安装 ContentTweaker）

> `import mods.contenttweaker.VanillaFactory;`
> `import mods.contenttweaker.Block;`

CoT 脚本第一行必须为 `#loader contenttweaker`。

### VanillaFactory 方块方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.createBlock(string id, BlockMaterial)` | Block | 创建自定义方块 |

### Block（自定义方块）

> `import mods.contenttweaker.Block;`

| ZenProperty | 类型 | 默认值 | 说明 |
|-------------|------|--------|------|
| `unlocalizedName` | string | — | **必填**，名称，全小写 |
| `blockHardness` | float | 5.0 | 方块硬度 |
| `blockResistance` | float | 5.0 | 防爆等级 |
| `blockSoundType` | SoundType | `<soundtype:metal>` | 方块声音类型 |
| `creativeTab` | ICreativeTab | 杂项 | 所在创造标签 |
| `lightValue` | int | 0 | 光照等级（0-1，乘以 15 得实际值） |
| `lightOpacity` | int | 255/0 | 不透明度（fullBlock 时 255，否则 0） |
| `fullBlock` | bool | true | 是否完整方块（影响渲染和光照） |
| `translucent` | bool | false | 是否半透明 |
| `blockLayer` | string | "SOLID" | 渲染层: "SOLID"/"CUTOUT_MIPPED"/"CUTOUT"/"TRANSLUCENT" |
| `gravity` | bool | false | 是否受重力影响 |
| `slipperiness` | float | 0.6 | 滑度（冰为 0.98） |
| `toolClass` | string | "pickaxe" | 需要的挖掘工具 |
| `toolLevel` | int | 2 | 需要的挖掘等级 |
| `entitySpawnable` | bool | true | 生物是否可在此方块上生成 |
| `witherProof` | bool | false | 是否抵御凋灵爆炸 |
| `beaconBase` | bool | false | 是否可作为信标基座 |
| `replaceable` | bool | 取决于材质 | 是否可直接替换 |
| `passable` | bool | 取决于材质 | 玩家是否可通过 |
| `blockMaterial` | IMaterialDefinition | Iron | 方块材质 |
| `blockColorSupplier` | IBlockColorSupplier | -1 | 方块颜色函数 |
| `itemColorSupplier` | IItemColorSupplier | -1 | 物品形式颜色函数 |
| `dropHandler` | IBlockDropHandler | null | 自定义掉落物函数 |
| `onBlockBreak` | IBlockAction | null | 方块破坏回调 |
| `onBlockPlace` | IBlockAction | null | 方块放置回调 |
| `onRandomTick` | IBlockAction | null | 随机 tick 回调 |
| `onUpdateTick` | IBlockAction | null | 方块更新回调 |
| `axisAlignedBB` | MCAxisAlignedBB | 整格 | 自定义碰撞箱 |
| `enumBlockRenderType` | string | "MODEL" | 渲染类型: "INVISIBLE"/"LIQUID"/"ENTITYBLOCK_ANIMATED"/"MODEL" |
| `textureLocation` | CTResourceLocation | null | 纹理位置 |

| 方法 | 返回 | 说明 |
|------|------|------|
| `.register()` | void | 注册方块进游戏 |

### ContentTweaker 方块示例

```zenscript
#loader contenttweaker
import mods.contenttweaker.VanillaFactory;
import mods.contenttweaker.Block;

var antiIceBlock as Block = VanillaFactory.createBlock("anti_ice", <blockmaterial:ice>);
antiIceBlock.lightOpacity = 3;
antiIceBlock.blockHardness = 1.0;
antiIceBlock.blockResistance = 5.0;
antiIceBlock.toolClass = "pickaxe";
antiIceBlock.toolLevel = 0;
antiIceBlock.blockSoundType = <soundtype:snow>;
antiIceBlock.slipperiness = 0.75;
antiIceBlock.register();
```
---

## ContentTweaker 方块回调函数

### IBlockAction（方块动作回调）

> `import mods.contenttweaker.IBlockAction;`

用于 `onBlockBreak`、`onBlockPlace`、`onRandomTick`、`onUpdateTick`。

| 参数 | 类型 | 说明 |
|------|------|------|
| world | IWorld | 方块所在世界 |
| position | IBlockPos | 方块位置 |
| state | ICTBlockState | 方块状态 |

返回 void。

```zenscript
block.onBlockBreak = function(world, blockPos, blockState) {
    print("方块被破坏！");
};
```

### IBlockDropHandler（方块掉落处理）

> `import mods.contenttweaker.IBlockDropHandler;`

| 参数 | 类型 | 说明 |
|------|------|------|
| drops | ICTItemList | 掉落物列表 |
| world | IBlockAccess | 世界 |
| position | IBlockPos | 方块位置 |
| state | ICTBlockState | 方块状态 |
| fortune | int | 时运等级 |

返回 void，通过 `drops.add()` 添加掉落物。

```zenscript
block.setDropHandler(function(drops, world, position, state, fortune) {
    drops.add(<item:minecraft:bedrock>);
    drops.add(<item:minecraft:carrot> % 50);  // 50% 概率掉落
});
```

### IBlockColorSupplier（方块颜色函数）

> `import mods.contenttweaker.IBlockColorSupplier;`

| 参数 | 类型 | 说明 |
|------|------|------|
| state | ICTBlockState | 方块状态 |
| access | IBlockAccess | 世界 |
| pos | IBlockPos | 方块位置 |
| tintIndex | int | 色调索引 |

返回 Color 对象。

### IItemColorSupplier（物品颜色函数）

> `import mods.contenttweaker.IItemColorSupplier;`

| 参数 | 类型 | 说明 |
|------|------|------|
| itemStack | IItemStack | 物品堆叠 |
| tintIndex | int | 色调索引 |

返回 Color 对象。

---

## ContentTweaker 方块类型

### ICTBlockState（CoT 方块状态）

> `import mods.contenttweaker.BlockState;`

通过 `<block:modid:blockname>` 或 IBlockAction 回调获取。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `block` | IBlock | 方块 |
| `meta` | int | Meta 值 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.canProvidePower()` | bool | 是否可提供红石信号 |
| `.getMobilityFlag()` | PushReaction | 活塞推动反应 |
| `.isReplaceable(IWorld, IBlockPos)` | bool | 是否可替换 |
| `.getLightValue(IWorld, IBlockPos)` | int | 获取光照值 |
| `.getWeakPower(IWorld, IBlockPos, Facing)` | int | 获取弱红石信号 |
| `.getComparatorInputOverride(IWorld, IBlockPos)` | int | 获取比较器输入 |

### IBlockPos（CoT 方块位置）

> `import mods.contenttweaker.BlockPos;`

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `x` | int | X 坐标 |
| `y` | int | Y 坐标 |
| `z` | int | Z 坐标 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getOffset(String, int)` | IBlockPos | 获取偏移后的位置（"down"/"up"/"north"/"south"/"east"/"west"） |
| `.getOffset(Facing, int)` | IBlockPos | 获取偏移后的位置 |

### MCAxisAlignedBB（碰撞箱）

> `import mods.contenttweaker.AxisAlignedBB;`

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `AxisAlignedBB.create(double, double, double, double, double, double)` | MCAxisAlignedBB | 创建碰撞箱（参数范围 0-1） |

#### @ZenGetter / @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `minX` | double | 最小 X |
| `minY` | double | 最小 Y |
| `minZ` | double | 最小 Z |
| `maxX` | double | 最大 X |
| `maxY` | double | 最大 Y |
| `maxZ` | double | 最大 Z |

### IMaterialDefinition（方块材质）

> `import mods.contenttweaker.BlockMaterial;`

通过 `<blockmaterial:name>` 获取。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `blocksLight` | bool | 是否阻挡光线 |
| `blocksMovement` | bool | 是否阻挡移动 |
| `canBurn` | bool | 是否可燃 |
| `mobilityFlag` | PushReaction | 活塞推动反应 |
| `liquid` | bool | 是否液体 |
| `opaque` | bool | 是否不透明 |
| `replaceable` | bool | 是否可替换 |
| `solid` | bool | 是否固体 |
| `toolNotRequired` | bool | 是否不需要工具 |

可用 `==` 比较两个材质。

### Facing（方向枚举）

> `import mods.contenttweaker.Facing;`

值：`north`、`east`、`south`、`west`、`down`、`up`

### PushReaction（活塞推动反应）

> `import mods.contenttweaker.PushReaction;`

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `PushReaction.normal()` | PushReaction | 正常推动 |
| `PushReaction.destroy()` | PushReaction | 推动时破坏 |
| `PushReaction.block()` | PushReaction | 阻止推动 |
| `PushReaction.ignore()` | PushReaction | 忽略推动 |
| `PushReaction.pushOnly()` | PushReaction | 仅可推（不可拉） |

### ICTItemList（掉落物列表）

> `import mods.contenttweaker.ItemList;`

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.add(IItemStack)` | void | 添加掉落物 |
| `.add(WeightedItemStack)` | void | 添加带概率的掉落物 |
| `.remove(int)` | void | 移除指定索引的掉落物 |
| `.clear()` | void | 清空掉落物 |
| `.get(int)` | IItemStack | 获取指定索引的掉落物 |
| `.getArray()` | IItemStack[] | 获取掉落物数组 |
| `.getList()` | List | 获取掉落物列表 |
| `.getLength()` | int | 获取掉落物数量 |

支持 `list + <item:minecraft:carrot>` 语法。

---

## ContentTweaker 方块括号处理器

| 括号 | 返回 | 说明 |
|------|------|------|
| `<block:modid:blockname>` | ICTBlockState | 获取方块状态 |
| `<blockmaterial:name>` | IMaterialDefinition | 获取方块材质 |
| `<soundtype:name>` | ISoundTypeDefinition | 获取声音类型 |

**可用的 BlockMaterial**: Air, Grass, Ground, Wood, Rock, Iron, Anvil, Water, Lava, Leaves, Plants, Vine, Sponge, Cloth, Fire, Sand, Circuits, Carpet, Glass, Redstone_Light, TNT, Coral, Ice, Packed_Ice, Crafted_Snow, Cactus, Clay, Gourd, Dragon_Egg, Portal, Cake, Web

**可用的 SoundType**: Wood, Ground, Plant, Stone, Metal, Glass, Cloth, Sand, Snow, Ladder, Anvil, Slime

---

## ZenUtils 扩展（需安装 ZenUtils + ContentTweaker）

> `import mods.zenutils.cotx.*;`

该部分是对 ContentTweaker 方块系统的扩展。(见上方ContentTweaker 扩展)

### VanillaFactory 方块扩展方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `VanillaFactory.createExpandBlock(String, IBlockMaterialDefinition)` | ExpandBlock | 创建扩展方块 |

### ExpandBlock（扩展方块）

> `import mods.zenutils.cotx.Block;`

继承 ContentTweaker 的 `BlockRepresentation`。

| 属性 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `onBlockActivated` | IBlockActivated | null | 方块右键交互回调 |
| `onEntityWalk` | IEntityWalk | null | 实体踩踏回调 |
| `onEntityCollidedWithBlock` | IEntityCollided | null | 实体碰撞回调 |
| `tileEntity` | TileEntity | null | 关联的方块实体 |

#### IBlockActivated（函数式接口）

> `import mods.zenutils.cotx.IBlockActivated;`

| 参数 | 类型 |
|------|------|
| world | IWorld |
| pos | IBlockPos |
| state | ICTBlockState |
| player | ICTPlayer |
| hand | Hand |
| facing | Facing |
| blockHit | Position3f |

返回 `bool`。

#### IEntityWalk（函数式接口）

> `import mods.zenutils.cotx.IEntityWalk;`

参数：`IWorld`, `IBlockPos`, `IEntity`。返回 void。

#### IEntityCollided（函数式接口）

> `import mods.zenutils.cotx.IEntityCollided;`

参数：`IWorld`, `IBlockPos`, `ICTBlockState`, `IEntity`。返回 void。

### LateSetCoTFunction 方块部分

| Bracket | 返回 | 说明 |
|------|------|------|
| `<cotBlock:name>` | IBlockRepresentation | 获取 ContentTweaker 方块 |

| 方法 | 返回 | 说明 |
|------|------|------|
| `VanillaFactory.putTileEntityTickFunction(int, ITileEntityTick)` | void | 注册方块实体 tick 函数（可重载） |

---

## RandomTweaker 扩展（需安装 RandomTweaker）

> `import mods.randomtweaker.vanilla.IBlockPos;`

IBlockPos 的扩展类，IBlockPos 实例可直接使用这些方法。

### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `IBlockPos.getAllInBox(from as IBlockPos, to as IBlockPos)` | IBlockPos[] | 返回 from 到 to 范围内所有的 IBlockPos 对象集合。只能用类名调用，用对象调用会报错 |

### 扩展实例方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.add(x as int, y as int, z as int)` | IBlockPos | 返回坐标偏移 xyz 后的新坐标 |
| `.up(@Optional n as int)` | IBlockPos | 返回坐标向上偏移 n 格后的新坐标，默认 n=1 |
| `.down(@Optional n as int)` | IBlockPos | 返回坐标向下偏移 n 格后的新坐标，默认 n=1 |
| `.north(@Optional n as int)` | IBlockPos | 返回坐标向北偏移 n 格后的新坐标，默认 n=1 |
| `.south(@Optional n as int)` | IBlockPos | 返回坐标向南偏移 n 格后的新坐标，默认 n=1 |
| `.west(@Optional n as int)` | IBlockPos | 返回坐标向西偏移 n 格后的新坐标，默认 n=1 |
| `.east(@Optional n as int)` | IBlockPos | 返回坐标向东偏移 n 格后的新坐标，默认 n=1 |
