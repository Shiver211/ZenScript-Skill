# Damage CraftTweaker API 参考

> Mod ID: `minecraft`
> 前置条件: 无
> 导入: `import crafttweaker.damage.IDamageSource;`

伤害来源 API，用于创建和操作伤害来源。

---

## API 列表

### IDamageSource（伤害来源）

> `import crafttweaker.damage.IDamageSource;`

#### @ZenGetter

| 属性 | 类型 | 说明 |
|------|------|------|
| `name` | string | 伤害来源名称 |
| `commandString` | string | 命令字符串 |
| `damageType` | string | 伤害类型 |
| `harmInCreative` | bool | 是否可在创造模式造成伤害 |
| `hungerDamage` | float | 饥饿伤害 |
| `immediateSource` | IEntity | 直接来源实体 |
| `trueSource` | IEntity | 真正来源实体 |
| `creativePlayer` | bool | 是否创造模式玩家 |
| `damageLocation` | IVector3d | 伤害位置 |
| `damageAbsolute` | bool | 是否绝对伤害 |
| `damageUnblockable` | bool | 是否不可格挡 |
| `difficultyScaled` | bool | 是否随难度缩放 |
| `explosion` | bool | 是否爆炸伤害 |
| `fireDamage` | bool | 是否火焰伤害 |
| `magicDamage` | bool | 是否魔法伤害 |
| `projectile` | bool | 是否弹射物伤害 |
| `isUnblockable` | bool | 是否不可格挡（同 damageUnblockable） |
| `isDamageAbsolute` | bool | 是否绝对伤害（同 damageAbsolute） |
| `isDifficultyScaled` | bool | 是否随难度缩放（同 difficultyScaled） |
| `isFireDamage` | bool | 是否火焰伤害（同 fireDamage） |
| `isMagicDamage` | bool | 是否魔法伤害（同 magicDamage） |
| `isExplosion` | bool | 是否爆炸伤害（同 explosion） |
| `isProjectile` | bool | 是否弹射物伤害（同 projectile） |
| `isFallDamage` | bool | 是否摔落伤害 |
| `isCreativePlayer` | bool | 是否创造模式玩家（同 creativePlayer） |
| `damageEntity` | IEntity | 造成伤害的实体 |

#### 方法

| 方法 | 返回 | 说明 |
|------|------|------|
| `.canHarmInCreative()` | bool | 是否可在创造模式造成伤害 |
| `.getDamageType()` | string | 获取伤害类型 |
| `.getHungerDamage()` | float | 获取饥饿伤害 |
| `.getImmediateSource()` | IEntity | 获取直接来源实体 |
| `.getTrueSource()` | IEntity | 获取真正来源实体 |
| `.isCreativePlayer()` | bool | 是否创造模式玩家 |
| `.getDamageLocation()` | IVector3d | 获取伤害位置 |
| `.isDamageAbsolute()` | bool | 是否绝对伤害 |
| `.isDamageUnblockable()` | bool | 是否不可格挡 |
| `.isDifficultyScaled()` | bool | 是否随难度缩放 |
| `.isExplosion()` | bool | 是否爆炸伤害 |
| `.isFireDamage()` | bool | 是否火焰伤害 |
| `.isMagicDamage()` | bool | 是否魔法伤害 |
| `.isProjectile()` | bool | 是否弹射物伤害 |
| `.getDeathMessage(IEntity)` | string | 获取实体被此伤害来源杀死时的死亡消息 |
| `.setDamageAllowedInCreativeMode()` | IDamageSource | 设置允许在创造模式造成伤害 |
| `.setDamageBypassesArmor()` | IDamageSource | 设置伤害无视护甲 |
| `.setDamageIsAbsolute()` | IDamageSource | 设置绝对伤害 |
| `.setDifficultyScaled()` | IDamageSource | 设置随难度缩放 |
| `.setExplosion()` | IDamageSource | 设置为爆炸伤害 |
| `.setFireDamage()` | IDamageSource | 设置为火焰伤害 |
| `.setMagicDamage()` | IDamageSource | 设置为魔法伤害 |
| `.setProjectile()` | IDamageSource | 设置为弹射物伤害 |
| `.setFallDamage()` | IDamageSource | 设置为摔落伤害 |

### 创建 IDamageSource（静态方法）

除了使用伤害来源括号处理器外，还可以通过以下静态方法创建 IDamageSource：

| 方法 | 返回 | 说明 |
|------|------|------|
| `IDamageSource.createMobDamage(IEntityLivingBase)` | IDamageSource | 创建生物伤害 |
| `IDamageSource.createIndirectDamage(IEntity, IEntityLivingBase)` | IDamageSource | 创建间接伤害（真正来源，直接来源） |
| `IDamageSource.createPlayerDamage(IPlayer)` | IDamageSource | 创建玩家伤害 |
| `IDamageSource.createThrownDamage(IEntity, @Optional IEntity)` | IDamageSource | 创建投掷伤害 |
| `IDamageSource.createIndirectMagicDamage(IEntity, @Optional IEntity)` | IDamageSource | 创建间接魔法伤害 |
| `IDamageSource.createThornsDamage(IEntity)` | IDamageSource | 创建荆棘伤害 |
| `IDamageSource.createExplosionDamage(@Optional IEntityLivingBase)` | IDamageSource | 创建爆炸伤害 |
| `IDamageSource.createOfType(string)` | IDamageSource | 创建指定类型的伤害 |
| `IDamageSource.createEntityDamage(string, IEntity)` | IDamageSource | 创建实体伤害（类型名，来源实体） |
| `IDamageSource.createIndirectDamage(string, IEntity, @Optional IEntity)` | IDamageSource | 创建指定类型的间接伤害 |

### 预定义伤害来源

**若尖括号里写的不是下列预定义名，会以该名字新建一个伤害源**，不会报错。

```zenscript
<damageSource:MAGIC>             // 魔法伤害
<damageSource:GENERIC>           // 通用伤害
<damageSource:IN_FIRE>           // 火焰伤害
<damageSource:LAVA>              // 岩浆伤害
<damageSource:IN_WALL>           // 窒息伤害
<damageSource:STARVE>            // 饥饿伤害
<damageSource:OUT_OF_WORLD>      // 虚空伤害
<damageSource:FALL>              // 摔落伤害
<damageSource:CRAMMING>          // 挤压伤害
<damageSource:DROWN>             // 溺水伤害
<damageSource:LIGHTNING_BOLT>    // 闪电伤害
<damageSource:ON_FIRE>           // 着火伤害
<damageSource:HOT_FLOOR>         // 烫脚伤害
<damageSource:CACTUS>            // 仙人掌伤害
<damageSource:FLY_INTO_WALL>     // 撞墙伤害
<damageSource:WITHER>            // 凋零伤害
<damageSource:ANVIL>             // 铁砧伤害
<damageSource:FALLING_BLOCK>     // 下落方块伤害
<damageSource:DRAGON_BREATH>     // 龙息伤害
<damageSource:FIREWORKS>         // 烟花伤害
```

---

## 使用示例

### 造成伤害

```zenscript
// 对实体造成伤害
entity.attackEntityFrom(<damageSource:MAGIC>, 5.0f);

// 对玩家造成伤害
player.attackEntityFrom(<damageSource:MAGIC>, 5.0f);

// 条件伤害（合成后扣血）
recipes.addShapeless("hurt_recipe", <minecraft:sapling>,
    [<minecraft:stick>, <minecraft:leaves>],
    function(out, ins, info) {
        return info.player.health > 5 ? out : null;  // 血量 > 5 才能合成
    },
    function(out, info, player) {
        player.attackEntityFrom(<damageSource:MAGIC>, 5.0f);  // 扣 5 血
    }
);
```
