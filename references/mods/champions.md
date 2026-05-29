# Champions CraftTweaker API 参考

> Mod ID: `champions`
> 前置条件: Random Addon
> 导入: `import mods.ra.champions.AffixBuilder;`

## API 列表

### AffixBuilder

> `import mods.ra.champions.AffixBuilder;`

#### 创建与注册方法
| 方法 | 返回 | 说明 |
|------|------|------|
| `.create(string id)` | AffixBuilder | 创建词条，`id` 为词条 ID |
| `.register()` | void | 注册词条到游戏 |

#### @ZenSetter
| 属性 | 类型 | 说明 |
|------|------|------|
| `{tier}` | int | 等级限制，该词条能出现在至少多少级的冠军怪身上 |

#### 回调函数
| 函数 | 说明 |
|------|------|
| `canApply(IEntityLiving living)` | 返回 bool，表示词条能否施加到该实体上 |
| `onAttack(IEntityLiving living, IEntityLivingBase target, IDamageSource damageSource, float damage, EntityLivingDamageEvent evt)` | 实体攻击目标时调用 |
| `onAttacked(IEntityLiving living, IDamageSource damageSource, float damage, EntityLivingAttackedEvent evt)` | 实体被攻击后调用 |
| `onDamaged(IEntityLiving living, IDamageSource damageSource, float damage, float newDamage)` | 实体受伤后调用，返回 newDamage 表示实际受到的伤害 |
| `onHurt(IEntityLiving living, IDamageSource damageSource, float amount, float newAmount)` | 实体受伤时调用，返回 newAmount 表示实际受到的伤害 |
| `onDeath(IEntityLiving living, IDamageSource damageSource, EntityLivingDeathEvent evt)` | 实体死亡时调用 |
| `onHealed(IEntityLiving living, float amount, float newAmount)` | 实体受到治疗时调用，返回 newAmount 表示回复的生命值 |
| `onInitialSpawn(IEntityLiving living)` | 实体生成的初始阶段调用 |
| `onSpawn(IEntityLiving living)` | 实体生成时调用 |
| `onKnockback(IEntityLiving living, LivingKnockBackEvent evt)` | 实体被击退时调用 |
| `onUpdate(IEntityLiving living)` | 实体更新时调用 |

### utils

> `import mods.ra.champions.utils;`

#### 静态方法
| 方法 | 返回 | 说明 |
|------|------|------|
| `.isChampion(IEntityLiving living)` | bool | 该实体是否为冠军怪 |
| `.getTier(IEntityLiving living)` | int | 获取该实体的冠军等级，若不是冠军怪则返回 0 |
| `.getAffixes(IEntityLiving living)` | string[] | 获取该实体的词缀列表 |

## 使用示例

### 创建自定义词条

```zenscript
import mods.ra.champions.AffixBuilder;

val test = mods.ra.champions.AffixBuilder.create("test");
test.tier = 2;
test.onSpawn = function(living) {
    print("Hello from Random Addon!");
};
test.onDeath = function(living, source, evt) {
    if(!isNull(source.getTrueSource()) && source.getTrueSource() instanceof IPlayer) {
        var player as IPlayer = source.getTrueSource();
        var world as IWorld = player.world;
        world.performExplosion(player, living.x, living.y, living.z, 15.0f, false, false);
    }
};
test.onUpdate = function(living) {
    if(!isNull(living) && !living.world.remote) {
        living.addPotionEffect(<potion:minecraft:regeneration>.makePotionEffect(40, 1, false, false));
        living.addPotionEffect(<potion:minecraft:speed>.makePotionEffect(40, 4, true, true));
    }
};
test.onHurt = function(living, source, dmg, newDmg) {
    return (living.health == living.maxHealth) ? dmg * 0.1f : dmg;
};
test.onHealed = function(living, amount, newAmount) {
    if(living.isPotionActive(<potion:minecraft:regeneration>)) {
        return 2.0f * amount;
    }
    return 0.0f;
};
test.onAttack = function(living, livingBase, source, dmg, evt) {
    if(livingBase instanceof IPlayer) {
        livingBase.addPotionEffect(<potion:minecraft:poison>.makePotionEffect(40, 3, false, false));
    }
};
test.onAttacked = function(living, source, dmg, evt) {
    if(source.getTrueSource() instanceof IPlayer) {
        living.health += dmg * 0.1f;
    }
};
test.onKnockback = function(living, evt) {
    if(evt.attacker instanceof IPlayer) {
        evt.cancel();
    }
};
test.register();
```

## 注意事项

- 自定义词条需要在 `config/champions/affixes.json` 中按格式添加配置
- 本地化格式：`champions.affix.{词条id}=显示名称`，需通过资源包导入
- `onKnockback` 的 `evt.cancel()` 效果可能不生效，有待考证
