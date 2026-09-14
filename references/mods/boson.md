# Boson CraftTweaker API 参考

> Mod ID: `boson`
> 前置条件: 无
> 导入: 各类**不在统一包下**——序列/数学/反射位于 `zenscriptx.*`，标签与命名空间字符串位于 `net.thesilkminer.mc.boson.zen.*`，见各小节

Boson 是一个库模组，提供标签（Tags）、序列（Sequences）、反射（Reflection）、数学函数等功能。

---

## API 列表

### Tags（标签系统）

> `import net.thesilkminer.mc.boson.zen.tag.Tag;`
> `import net.thesilkminer.mc.boson.zen.tag.TagIngredient;`
> `import net.thesilkminer.mc.boson.zen.tag.TagType;`

标签是 Boson 提供的强大功能，类似于矿辞但更灵活。

#### 尖括号语法

```zenscript
<tag-TYPE:NAMESPACE:NAME>  // 获取标签
```

- `TYPE`: 标签类型（`items`、`blocks`、`fluids`）
- `NAMESPACE`: 命名空间（通常是模组ID）
- `NAME`: 标签名称

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `name` | NameSpacedString | 标签的唯一标识名称 |
| `type` | TagType | 标签类型 |
| `elements` | [T] | 标签中的元素列表（只读） |

#### 标签操作方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.add(elements... as NameSpacedString)` | void | 通过注册名添加元素 |
| `.addAll(elements as any[])` | void | 通过数组添加元素 |
| `.addFrom(other as Tag)` | void | 从另一个标签添加元素（引用） |
| `.replace(elements... as NameSpacedString)` | void | 替换标签内容 |
| `.replaceAll(elements as any[])` | void | 通过数组替换标签内容 |
| `.replaceWith(other as Tag)` | void | 用另一个标签替换内容 |
| `.remove(elements... as NameSpacedString)` | void | 移除元素 |
| `.removeAll(elements as any[])` | void | 通过数组移除元素 |
| `.removeFrom(other as Tag)` | void | 移除标签引用 |
| `.clear()` | void | 清空标签内容 |

#### 运算符重载

| 运算符 | 语法 | 等效方法 | 说明 |
|--------|------|----------|------|
| `+` | `TAG + ARG1` | `addFrom`/`addAll`/`add` | 添加操作 |
| `-` | `TAG - ARG1` | `removeFrom`/`removeAll`/`remove` | 移除操作 |
| `~` | `TAG ~ ARG1` | `addFrom`/`addAll`/`add` | 添加操作（同+） |
| `&` | `TAG & ARG1` | `addFrom`/`addAll`/`add` | 添加操作（同+） |
| `==` | `TAG == ARG1` | - | 相等性检查 |
| `()` | `TAG()` | `elements` | 获取元素列表 |

### TagIngredient（标签配料）

> `import net.thesilkminer.mc.boson.zen.tag.TagIngredient;`

TagIngredient 是 IIngredient 的实现，允许在配方中使用标签。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `tagName` | NameSpacedString | 标签的唯一标识名称 |

### TagType（标签类型）

> `import net.thesilkminer.mc.boson.zen.tag.TagType;`

标签类型定义了标签中可以存储的元素类型。

#### 已知标签类型

| 助记符 | 存储对象 |
|--------|----------|
| `blocks` | IBlockState |
| `fluids` | 流体（暂不可用） |
| `items` | IItemStack |

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `directoryName` | string | 数据包中标签JSON的目录名 |
| `name` | string | 标签类型助记符 |
| `classType` | Class | 存储对象的类型 |
| `converterFunction` | Function | 名称转换函数 |

### NameSpacedString（命名空间字符串）

> `import net.thesilkminer.mc.boson.zen.naming.NameSpacedString;`

Boson 使用 NameSpacedString 来表示带命名空间的字符串。

#### 创建实例

```zenscript
NameSpacedString.from("minecraft", "iron_ingot"); // 推荐方式
"minecraft:iron_ingot" as NameSpacedString; // 转换方式
```

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `nameSpace` | string | 命名空间部分 |
| `path` | string | 路径部分 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.asString()` | string | 转换回普通字符串 |

### tags Loader（标签加载器）

> `#loader tags`

标签加载器允许操作标签内容。

```zenscript
#loader tags
val tag = <tag-blocks:minecraft:enderman_holdable>;
tag.add("minecraft:iron_block" as NameSpacedString);
```

---

## Sequences（序列系统）

> `import zenscriptx.sequence.Sequence;`

序列是惰性求值的列表操作工具。

### 创建序列

```zenscript
<sequence:IItemStack>(<minecraft:iron_ingot>, <minecraft:gold_ingot>);
<sequence:string>(); // 空序列
```

### 终端方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.contains(element as T)` | bool | 检查元素是否存在 |
| `.elementAt(index as int)` | T | 获取指定索引的元素 |
| `.find(predicate as Predicate<T>)` | T? | 查找匹配的第一个元素 |
| `.first()` | T | 获取第一个元素 |
| `.last()` | T | 获取最后一个元素 |
| `.count()` | int | 获取元素数量 |
| `.toList()` | [T] | 转换为列表 |
| `.forEach(action as Consumer<T>)` | void | 遍历所有元素 |
| `.any(predicate as Predicate<T>)` | bool | 检查是否有匹配元素 |
| `.all(predicate as Predicate)` | bool | 检查是否所有元素匹配 |

### 中间惰性方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.filter(predicate as Predicate<T>)` | Sequence<T> | 过滤元素 |
| `.map(transform as Function<T, R>)` | Sequence<R> | 转换元素 |
| `.drop(n as int)` | Sequence<T> | 丢弃前n个元素 |
| `.take(n as int)` | Sequence<T> | 保留前n个元素 |
| `.distinct()` | Sequence<T> | 去重 |
| `.plus(elements as T[])` | Sequence<T> | 添加元素 |
| `.minus(elements as T[])` | Sequence<T> | 移除元素 |

---

## Math（数学函数）

> `import zenscriptx.math.Math;`

### 数学常数

| 方法 | 返回 | 说明 |
|------|------|------|
| `.pi()` | double | 圆周率π |
| `.e()` | double | 自然常数e |

### 三角函数

| 方法 | 返回 | 说明 |
|------|------|------|
| `.sin(x as double)` | double | 正弦（弧度） |
| `.cos(x as double)` | double | 余弦（弧度） |
| `.tan(x as double)` | double | 正切（弧度） |
| `.asin(x as double)` | double | 反正弦 |
| `.acos(x as double)` | double | 反余弦 |
| `.atan(x as double)` | double | 反正切 |

### 幂和根

| 方法 | 返回 | 说明 |
|------|------|------|
| `.sqrt(x as double)` | double | 平方根 |
| `.pow(base as double, exp as double)` | double | 幂运算 |

### 对数函数

| 方法 | 返回 | 说明 |
|------|------|------|
| `.ln(x as double)` | double | 自然对数 |
| `.log10(x as double)` | double | 常用对数 |
| `.log2(x as double)` | double | 二进制对数 |

### 取整函数

| 方法 | 返回 | 说明 |
|------|------|------|
| `.ceil(x as double)` | double | 向上取整 |
| `.floor(x as double)` | double | 向下取整 |
| `.round(x as double)` | double | 四舍五入 |

### 其他函数

| 方法 | 返回 | 说明 |
|------|------|------|
| `.abs(x as double)` | double | 绝对值 |
| `.min(a as double, b as double)` | double | 最小值 |
| `.max(a as double, b as double)` | double | 最大值 |
| `.clamp(x as double, min as double, max as double)` | double | 钳制值 |

---

## Reflection（反射系统）

> `import zenscriptx.reflect.Class;`
> `import zenscriptx.reflect.NativeClass;`

### Class

#### 创建实例

```zenscript
Class.byName("crafttweaker.item.IItemStack");
Class.from(<blockstate:minecraft:pumpkin>);
```

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `simpleName` | string | 简单类名 |
| `qualifiedName` | string | 完整类名 |

### NativeClass

#### 创建实例

```zenscript
NativeClass.byName("crafttweaker.api.item.IItemStack");
NativeClass.fromZen(<blockstate:minecraft:pumpkin>);
```

---

## 注意事项

- 标签操作需要在 `#loader tags` 中进行
- 序列是惰性求值的，只有在终端方法调用时才执行
- 运算符重载会修改原标签，不会创建新标签
- NameSpacedString 用于避免命名空间混淆
