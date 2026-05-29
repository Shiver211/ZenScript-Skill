# Zen Summoning CraftTweaker API 参考

> Mod ID: `zensummoning`
> 前置条件: 无
> 导入: `import mods.zensummoning.*;`

用于自定义祭坛召唤配方的 CraftTweaker 附加模组。支持设置催化剂、反应物、召唤生物及自定义条件。

---

## API 列表

### SummoningDirector（入口类）

> `import mods.zensummoning.SummoningDirector;`

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.addSummonInfo(SummoningInfo info)` | void | 注册一条召唤配方 |

---

### SummoningInfo（召唤信息）

> `import mods.zensummoning.SummoningInfo;`

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.create()` | SummoningInfo | 创建新的召唤信息实例 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setCatalyst(IIngredient catalyst)` | SummoningInfo | 设置催化剂。玩家手持此物品 shift 右键祭坛触发召唤，催化剂会被消耗 |
| `.setReagents(IItemStack[] reagents)` | SummoningInfo | 设置反应物。放置在祭坛中的物品，召唤开始时被消耗 |
| `.addMob(MobInfo mob)` | SummoningInfo | 添加一种要召唤的生物 |
| `.setMutator(function(SummingAttempt) mutator)` | SummoningInfo | 设置自定义条件函数。通过修改 `SummoningAttempt` 的属性控制召唤是否成功及显示消息 |

---

### MobInfo（生物信息）

> `import mods.zensummoning.MobInfo;`

#### 静态方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.create()` | MobInfo | 创建新的生物信息实例 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.setMob(string mobId)` | MobInfo | 设置生物 ID，如 `"minecraft:cow"` |
| `.setCount(int count)` | MobInfo | 设置召唤数量 |
| `.setOffset(int x, int y, int z)` | MobInfo | 设置相对于祭坛的偏移位置 |
| `.setSpread(int x, int y, int z)` | MobInfo | 设置随机散布范围。如 `(1,0,0)` 表示生物可能出现在祭坛 X 轴 ±1 格内 |
| `.setData(IData data)` | MobInfo | 设置生物的 NBT 数据，如 `{"Health": 200}` |

---

### SummoningAttempt（召唤尝试）

> `import mods.zensummoning.SummoningAttempt;`

传递给 `setMutator` 回调函数的参数对象，用于控制召唤行为。

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `world` | IWorld | 当前世界 |

#### @ZenGetter / @ZenSetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `success` | bool | 是否允许召唤。设为 `false` 可阻止召唤，物品不会被消耗 |
| `message` | string | 显示给玩家的消息，无论成功与否 |

---

## 使用示例

### 基础示例

```zenscript
import mods.zensummoning.SummoningDirector;
import mods.zensummoning.SummoningInfo;
import mods.zensummoning.MobInfo;

SummoningDirector.addSummonInfo(
    SummoningInfo.create()
        .setCatalyst(<ore:treeSapling>)
        .setReagents([<minecraft:stone>])
        .addMob(MobInfo.create()
            .setMob("minecraft:cow")
        )
);
```

### 完整示例（含 Mutator）

```zenscript
import mods.zensummoning.SummoningDirector;
import mods.zensummoning.SummoningAttempt;
import mods.zensummoning.SummoningInfo;
import mods.zensummoning.MobInfo;

SummoningDirector.addSummonInfo(
    SummoningInfo.create()
        .setCatalyst(<minecraft:stick>)
        .setReagents([<minecraft:stone>, <minecraft:egg>*12])
        .addMob(MobInfo.create()
            .setMob("minecraft:cow")
            .setCount(4)
            .setOffset(0,4,0)
            .setSpread(3,3,3)
            .setData({"Health": 200, "Attributes":[{"Name":"generic.maxHealth","Base":200}]})
        )
        .setMutator(function (attempt as SummoningAttempt) {
            if (attempt.world.raining) {
                attempt.success = false;
                attempt.message = "Can't summon this in the rain!";
            } else {
                attempt.message = "Woohoo!";
            }
        })
);
```

## 注意事项

- 催化剂（Catalyst）：玩家手持的物品，shift 右键祭坛触发召唤，会被消耗
- 反应物（Reagents）：放置在祭坛中的物品，召唤开始时被消耗
- 一个 `SummoningInfo` 可召唤多种生物，每种由 `MobInfo` 描述
- `setReagents` 参数为数组，使用 `[]` 包裹
- 所有 `SummoningInfo` 和 `MobInfo` 的方法支持链式调用
