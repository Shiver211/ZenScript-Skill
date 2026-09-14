# Potions CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.potions.IPotion;`、`import crafttweaker.potions.IPotionEffect;`、`import crafttweaker.potions.IPotionType;`

药水效果系统 API，用于操作药水效果和药水类型。

---

## API 列表

### IPotion（药水效果）

> `import crafttweaker.potions.IPotion;`

通过 `<potion:...>` 获取。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `name` | string | 药水效果内部名称 |
| `liquidColor` | int | 液体颜色 |
| `liquidColour` | int | 液体颜色（同上，英式拼写） |
| `badEffect` | bool | 是否负面效果 |
| `isBeneficial` | bool | 是否正面效果 |
| `isInstant` | bool | 是否即时效果 |
| `hasStatusIcon` | bool | 是否有状态图标 |
| `curativeItems` | List\<IItemStack\> | 治愈物品列表 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.makePotionEffect(int, int)` | IPotionEffect | 创建药水效果（持续时间 ticks，等级） |
| `.makePotionEffect(int, int, bool, bool)` | IPotionEffect | 创建药水效果（持续时间，等级，是否环境效果，是否显示粒子） |

### IPotionEffect（药水效果实例）

> `import crafttweaker.potions.IPotionEffect;`

IPotionEffect 是带有持续时间和等级的药水效果。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `potion` | IPotion | 药水效果 |
| `duration` | int | 持续时间（ticks） |
| `amplifier` | int | 等级 |
| `isAmbient` | bool | 是否环境效果 |
| `doesShowParticles` | bool | 是否显示粒子 |
| `isPotionDurationMax` | bool | 是否最大持续时间（可读写） |
| `curativeItems` | List\<IItemStack\> | 治愈物品列表 |
| `effectName` | string | 效果名称 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.isCurativeItem(IItemStack)` | bool | 指定物品是否可治愈此效果 |
| `.performEffect(IEntity)` | void | 对实体执行效果 |

### IPotionType（药水类型）

> `import crafttweaker.potions.IPotionType;`

IPotionType 代表一种药水类型（如力量药水、治疗药水等），通过 `<potiontype:...>` 获取。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `effects` | List\<IPotionEffect\> | 此药水类型包含的效果列表 |

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `IPotionType.fromString(string)` | IPotionType | 从字符串获取药水类型（等同于括号处理器） |

---

## 使用示例

### 获取药水效果

```zenscript
// 通过药水括号处理器获取
val speed = <potion:minecraft:speed>;
val slowness = <potion:minecraft:slowness>;

// 创建药水效果
val speedEffect = speed.makePotionEffect(600, 1);  // 30秒，等级1
```

### 遍历所有药水效果

```zenscript
for potion in game.potions {
    print(potion.name ~ ": " ~ potion.displayName);
}
```
---
## randomtweaker 扩展

### IPotion（自定义药水效果）

> `import mods.randomtweaker.cote.IPotion;`

需安装 ContentTweaker。使用 CoT 来自定义药水效果。加载器: `#loader contenttweaker`

#### @ZenGetter / @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `instant` | bool | 药水效果是否即刻生效（true 为即刻，false 为持续） |
| `badEffectIn` | bool | 药水效果是否为负面效果 |
| `beneficial` | bool | 药水效果是否对玩家有益（有益的药水会放在第一格） |
| `shouldRender` | bool | 药水效果是否在背包栏渲染 |
| `shouldRenderHUD` | bool | 药水效果是否在 HUD（右上角）渲染 |

#### 函数

| 函数 | 说明 |
|------|------|
| `isReady` | 决定当前 Tick 是否触发 performEffect 函数 |
| `performEffect` | 此函数每 Tick 都会调用 |
| `affectEntity` | 此函数仅 instant 为 true 时触发 |

#### VanillaFactory 创建方法

```zenscript
var potion as IPotion = VanillaFactory.createPotion(unlocalizedName as string, liquidColorIn as int);
```

- `unlocalizedName`：注册名，不可重复
- `liquidColorIn`：药水颜色，例如 `0xF7D575`
- 贴图位置：`contenttweaker:textures/gui/unlocalizedName.png`

#### 使用示例

```zenscript
#loader contenttweaker

import mods.contenttweaker.VanillaFactory;
import mods.randomtweaker.cote.IPotion;
import mods.contenttweaker.Player;

var potion as IPotion = VanillaFactory.createPotion("lhhd", 0xF7D575);
potion.shouldRender = false;
potion.shouldRenderHUD = false;
potion.badEffectIn = false;
potion.isReady = function(duration, amplifier) {
    return duration % 20 == 0; // 20tick 触发一次（1秒）
};
potion.performEffect = function(living, amplifier) {
    if(!living.world.remote && living instanceof Player) {
        var player as Player = living;
        player.sendChat("didiidid~~~");
    }
};
potion.register();
```
