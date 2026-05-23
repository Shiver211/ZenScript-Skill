# References 快速索引

按关键词查找参考文件，减少不必要的文件读取。

---

## Vanilla

| 文件 | 内容 | 关键词 |
|------|------|--------|
| vanilla/items/core.md | 物品堆叠/物品定义 | IItemStack, 物品操作, NBT, 耐久, 显示名 |
| vanilla/items/ingredients.md | 配方原料/带权重物品 | IIngredient, WeightedItemStack, 物品条件, 转换器 |
| vanilla/items/factory.md | 自定义物品 | VanillaFactory, ContentTweaker, Item, ItemFood |
| vanilla/blocks/core.md | 方块/方块状态 | IBlock, IBlockState, IBlockDefinition, 方块操作 |
| vanilla/blocks/properties.md | 方块属性/材质 | IBlockProperties, IBlockStateMatcher, IMaterial, IMobilityFlag |
| vanilla/blocks/factory.md | 自定义方块 | VanillaFactory, ContentTweaker, Block, ExpandBlock |
| vanilla/recipes/crafting/api.md | 合成配方 API | addShaped, addShapeless, ICraftingRecipe |
| vanilla/recipes/crafting/manage.md | 移除/查询/替换配方 | remove, removeByInput, replaceAllOccurences |
| vanilla/recipes/crafting/functions.md | 配方函数/标记 | RecipeFunction, RecipeAction, marked |
| vanilla/recipes/furnace-brewing/core.md | 熔炉/酿造/种子 | furnace, brewing, seeds, 燃料 |
| vanilla/recipes/furnace-brewing/advanced.md | 高级配方技巧 | 数据驱动, 批量修改, 条件加载 |
| vanilla/entities/base/core.md | 实体基础 | IEntity, 实体操作, NBT, 标签 |
| vanilla/entities/base/living.md | 有生命实体 | IEntityLivingBase, IEntityLiving, 药水, 装备 |
| vanilla/entities/base/attributes.md | 实体属性 | IEntityAttribute, AttributeModifier, 属性修饰符 |
| vanilla/entities/base/extensions.md | 实体扩展 | 掉落, ZenUtils, tick 回调 |
| vanilla/entities/specialized.md | 特殊实体 | 箭矢, 钓鱼钩, 经验球, 实体定义 |
| vanilla/players/core.md | 玩家基础 | IPlayer, IFoodStats, 数据持久化, 物品栏 |
| vanilla/players/interaction.md | 玩家交互模拟 | simulateRightClick, simulateLeftClick |
| vanilla/players/stats.md | 玩家统计 | PlayerStat, IStatFormatter, 统计格式化 |
| vanilla/damage.md | 伤害/伤害来源 | IDamageSource, 伤害类型 |
| vanilla/liquids.md | 流体 | ILiquidStack, 流体操作, 自定义流体 |
| vanilla/commands.md | 命令系统 | ICommand, 自定义命令, ZenUtils 命令 |
| vanilla/world/core.md | 世界/位置/方向 | IWorld, IBlockPos, IFacing, 红石, 爆炸 |
| vanilla/world/helpers.md | 辅助对象 | IExplosion, IRayTraceResult, IVector3d, IBlockAccess |
| vanilla/world/info.md | 世界信息 | IWorldInfo, IWorldProvider, GameRuleHelper |
| vanilla/world/containers.md | 容器操作 | CrTItemHandler, CrTLiquidHandler, 物品容器 |
| vanilla/events/player/lifecycle.md | 玩家生命周期 | 登录, 登出, 重生, 维度切换, 克隆 |
| vanilla/events/player/interaction.md | 玩家交互 | 攻击, 右键, 左键, 交互, 方块 |
| vanilla/events/player/items.md | 玩家物品事件 | 合成, 熔炼, 拾取, 物品实体 |
| vanilla/events/player/hunger.md | 饥饿/食物 | 食物, 饥饿值, 疲劳, 生命恢复, HungerTweaker |
| vanilla/events/player/tools.md | 工具使用 | 锄头, 挖掘速度, 骨粉, 酿造, 睡觉, 进度 |
| vanilla/events/player/other.md | 其他玩家事件 | Tick, 死亡, 容器, 铁砧, 重生点, 可见性, 使用物品 |
| vanilla/events/combat.md | 战斗事件 | 箭矢, 暴击, 投射物, 爆炸 |
| vanilla/events/world.md | 世界事件 | 世界, Tick, 实体移除, 物品销毁, 矿车, 睡觉 |
| vanilla/events/system.md | 系统事件 | 命令, 附魔, 酿造, 物品, 通用管理器, EventPriority |
| vanilla/events/client.md | 客户端事件 | 聊天, 渲染覆盖层, EventTweaker |
| vanilla/events/entity/lifecycle.md | 实体生命周期 | 加入世界, 生成, 死亡, 死亡掉落, 跨维度, 末影传送 |
| vanilla/events/entity/combat.md | 实体战斗事件 | 被攻击, 受伤, 回血, 跳跃, 击退 |
| vanilla/events/entity/other.md | 其他实体事件 | 装备变化, 摔落, 更新, 骑乘, 雷击, 掉落经验 |
| vanilla/events/block.md | 方块事件 | 方块破坏, 方块放置 |
| vanilla/events/overview.md | 事件概述 | 事件接口, 事件监听, 事件列表 |
| vanilla/ore-dictionary.md | 矿物词典 | IOreDictEntry, 矿辞操作 |
| vanilla/enchantments.md | 附魔 | IEnchantmentDefinition, EnchantmentBuilder, 自定义附魔 |
| vanilla/potions.md | 药水 | IPotion, IPotionEffect, 药水效果 |
| vanilla/container.md | 容器 | IContainer, 容器操作 |
| vanilla/biomes.md | 生物群系 | IBiome, 生物群系操作 |
| vanilla/dispenser.md | 发射器 | 发射器配方 |
| vanilla/tile-entity.md | 方块实体 | ITileEntity, 方块实体操作 |
| vanilla/creative-tabs.md | 创造标签页 | ICreativeTab, CreativeTab, 自定义创造标签 |
| vanilla/villager.md | 村民 | 村民交易 |
| vanilla/village.md | 村庄 | 村庄声望, IVillage, 村庄半径, 村庄门 |
| vanilla/gamerules.md | 游戏规则 | 游戏规则操作 |
| vanilla/game.md | 游戏 | IGame, IClient, IServer, ITeam, ILoadedMods |

## Utils（工具类）

| 文件 | 内容 | 关键词 |
|------|------|--------|
| utils/nbt/idata.md | IData 类型与运算符 | IData 类型, DataString, DataInt, 类型转换, 运算符 |
| utils/nbt/datamap.md | DataMap 与 DataList | 关联数组, Key 解析, 判空, 合并裁剪, DataList 遍历 |
| utils/nbt/nbt-basics.md | NBT 标签与持久化 | NBT 构造, updateTag, withTag, PlayerPersisted, ForgeData |
| utils/nbt/deep-update.md | 深度更新 | deepUpdate, DataUpdateOperation, OVERWRITE, APPEND, MERGE, REMOVE, DataUtil |
| utils/nbt/item-nbt.md | 物品 NBT 操作 | item.tag, 物品 NBT 读写, 附魔检测, 工具函数 |
| utils/nbt/player-nbt.md | 玩家 NBT 与 CD | player.data, 玩家 NBT, CD 机制, 世界时间 |
| utils/nbt/display.md | 动态信息展示 | tooltip, addAdvancedTooltip, Lore 标签, 动态显示 |
| utils/nbt/crafting-nbt.md | 配方 NBT 操作 | 配方函数, 事件函数, NBT 继承, 拒绝合成, JEI 提示 |
| utils/network-handler.md | 服务端/客户端通信 | NetWorkHandler, IByteBuf, 发包, 收包, CD 同步 |
| utils/arrays.md | 数组 | 数组操作, 切片, 遍历 |
| utils/maps.md | Map | 关联数组, 键值遍历 |
| utils/text.md | 文本 | 颜色, 样式, tooltip, 聊天文本 |
| utils/static-string.md | 字符串 | 字符串方法, ZenUtils |
| utils/template-strings.md | 模板字符串 | 反引号, 插值 |
| utils/regex-matcher.md | 正则 | 正则匹配, CTUtils |
| utils/math.md | 数学库 | 随机数, 取整, 三角函数 |
| utils/date.md | 日期时间 | 日期, 时间, 日历 |
| utils/uuid.md | UUID | UUID 生成, 解析 |
| utils/hex-helper.md | 十六进制 | 十六进制转换 |
| utils/raw-logger.md | 日志 | 原始日志, 调试输出 |
| utils/preprocessor.md | 预处理器 | #modloaded, #loader, #priority 等|
| utils/nullish-operators.md | 空值操作 | 判空, 可选链, 空值合并 |
| utils/native-method-access.md | 原生方法 | Java 方法, MCP 名 |
| utils/zenclass.md | 自定义类 | zenClass, 辅助类 |
| utils/catenation.md | 链式执行 | 延迟执行, 链式任务 |
| utils/i18n.md | 本地化 | 翻译键, 多语言 |
| utils/GlStateManager.md | 渲染状态 | 客户端渲染, 矩阵, 颜色 |
| utils/mixin.md | Mixin | ZenScript Mixin |
| utils/color.md | 颜色对象 | Color, ContentTweaker, 颜色函数 |
| utils/expand.md | 扩展方法 | $expand, this, 自定义成员方法, 扩展类 |