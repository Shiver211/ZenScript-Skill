# Color CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: ContentTweaker
> 导入: `import mods.contenttweaker.Color;`

ContentTweaker 颜色对象，用于方块和物品的颜色函数。

---

## API 列表

### Color（颜色）

> `import mods.contenttweaker.Color;`

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `Color.fromInt(int)` | Color | 从整数创建颜色 |
| `Color.fromHex(string)` | Color | 从十六进制字符串创建颜色（如 "FF69B4"） |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.getIntColor()` | int | 获取颜色整数值 |

---

## 使用示例

```zenscript
#loader contenttweaker
import mods.contenttweaker.Color;

// 从十六进制创建
var pink = Color.fromHex("FF69B4");

// 从整数创建
var red = Color.fromInt(0xFF0000);

// 获取整数值
var intValue = pink.getIntColor();
```

### 在颜色函数中使用

```zenscript
#loader contenttweaker
import mods.contenttweaker.IBlockColorSupplier;
import mods.contenttweaker.Color;

block.blockColorSupplier = function(state, access, pos, tintIndex) {
    return Color.fromInt(0x00FF00);  // 绿色
};
```