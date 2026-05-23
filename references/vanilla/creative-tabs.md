# Creative Tabs CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.creativetabs.ICreativeTab;`

创造模式标签页 API，用于操作创造模式标签页。

---

## API 列表

### ICreativeTab（创造模式标签页）

> `import crafttweaker.creativetabs.ICreativeTab;`

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `id` | int | 标签页 ID |
| `name` | string | 标签页名称 |
| `displayName` | string | 显示名称 |
| `commandString` | string | 命令字符串 |
| `iconItem` | IItemStack | 图标物品 |
| `tabIndex` | int | 标签页索引 |
| `tabLabel` | string | 标签页标签文本 |
| `searchBarWidth` | int | 搜索栏宽度 |
| `isSearchTab` | bool | 是否搜索标签页 |
| `hasSearchBar` | bool | 是否有搜索栏 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setTabIconItem(IItemStack)` | void | 设置标签页图标物品 |
| `.setTabLabel(string)` | void | 设置标签页标签 |
| `.setTabTitle(string)` | void | 设置标签页标题 |
| `.setBackgroundImage(string)` | void | 设置背景图片 |
| `.setHasSearchBar(bool)` | void | 设置是否有搜索栏 |
| `.setSearchBarWidth(int)` | void | 设置搜索栏宽度 |
| `.setTabsVisibleSingleColumn(bool)` | void | 设置单列是否可见 |
| `.setTabsVisibleDoubleColumn(bool)` | void | 设置双列是否可见 |
| `.setNoScrollBar(bool)` | void | 设置无滚动条 |
| `.setNoTitle()` | void | 设置无标题 |

---

## 使用示例

### 获取创造模式标签页

```zenscript
// 获取所有创造模式标签页
for tab in game.creativeTabs {
    print(tab.name ~ ": " ~ tab.displayName);
}

// 按名称获取
val buildingBlocks = game.getCreativeTab("buildingBlocks");
```

### 物品设置创造模式标签页

```zenscript
// 设置物品的创造模式标签页
<minecraft:stone>.definition.setCreativeTab("buildingBlocks");

// 获取物品的创造模式标签页
val tab = <minecraft:stone>.definition.creativeTab;
```

---

## ContentTweaker 扩展（需安装 ContentTweaker）

> `import mods.contenttweaker.VanillaFactory;`
> `import mods.contenttweaker.CreativeTab;`

CoT 脚本第一行必须为 `#loader contenttweaker`。

### CreativeTab（自定义创造标签）

#### @ZenGetter / @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `unlocalizedName` | string | 标签页名称（创建时设置） |
| `iconStack` | IItemStack | 图标物品 |
| `iconStackSupplier` | IItemStackSupplier | 图标供应函数 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.createCreativeTab(string, IItemStack)` | CreativeTab | 用物品堆叠作为图标 |
| `.createCreativeTab(string, ItemRepresentation)` | CreativeTab | 用 CoT 物品作为图标 |
| `.createCreativeTab(string, BlockRepresentation)` | CreativeTab | 用 CoT 方块作为图标 |
| `.createCreativeTab(string, IItemStackSupplier)` | CreativeTab | 用函数动态提供图标 |
| `.register()` | void | 注册创造标签（注册后不可修改） |

### ContentTweaker 创造标签示例

```zenscript
#loader contenttweaker
import mods.contenttweaker.VanillaFactory;
import mods.contenttweaker.CreativeTab;

var tab = VanillaFactory.createCreativeTab("my_tab", <item:minecraft:diamond>);
tab.register();
```

### Creative Tab 括号处理器

通过 `<creativetab:name>` 获取已有的创造标签。

```zenscript
val miscTab = <creativetab:misc>;
```