“NBT是一种特殊的IData——DataMap，而DataMap则可以被看作IData类下的特殊映射（映射也被称为关联数组、Map）”——我如果这么介绍NBT，估计很多人会直接放弃。所以我们不妨换种介绍方式，逐一引入这四个概念。

    首先，我们需要知道NBT是干什么用的。一个物品Item（特指IItemStack类下的Item对象），除了自己本身是什么（IItemDefinition）、有多少数量、有多少耐久、meta值是多少外，还得带着一个盒子装一点“附加信息”——这个附加信息包括自己被修改后的显示名字（Name）、标签（Lore）、其上的附魔、各个模组作者往里边丧心病狂地添加的各种附加信息等等等等。这个“盒子”便是所谓的NBT。我们来看一个实例：
    这是一把比较特殊的钻石剑，它有自定义的名字“super 钻石剑”，一个附魔“经验修补”，两个标签“测试lore:test”和“累计击杀：15”，而在F3+h打开了高级显示框后，其倒数第二栏出现了“NBT：5个标签”的字样，表示这个钻石剑存在NBT这个“盒子”，里边存了5个数据。我们可以用/ct nbt指令（/ct hand也行，不过格式不够美观）直接查看这把钻石剑的NBT：（上边有个“NBT-Data:”没展示出来，不过不影响下图钻石剑的NBT完整性）
    从上图中我们可以看到，钻石剑中5个数据分别是：
//ench标签数据：来自原版，储存物品的附魔信息
ench:[{
    lvl:1 as short,id:70 as short
}]
 
 
//RepairCost标签数据：来自原版，储存物品的铁砧修复惩罚信息
RepairCost:1
 
 
//Quality标签数据：来自于QualityTools模组，储存武器的品质信息
//此处没有品质，所以信息为空
Quality:{
 
}
 
 
//display标签数据：来自原版，主要负责物品的展示信息
display:{
    Lore:[
        "bb测试lore:test",
        "累计击杀:5"
    ],
    Name:"super 钻石剑"
}
 
 
//DeathCount标签数据：笔者自己加的击杀计数信息
DeathCount:15

我们可以通过访问NBT从而直接获取这些信息，但得遵守一定的规则——否则你会得到一堆的字节码报错开始算命。这个“盒子”的储存方式便是IData类，其访问规则也由IData类的接口（Getter/Setter/Method/Operator）所规定。只不过比起一般的IData类对象，NBT的储存格式更加特殊：它属于IData类中的一种特殊的“映射”——DataMap。

1.关联数组（Map/映射）

    仔细观察NBT的结构我们可以发现：对于每一个单独的数据标签，它都遵循着“<名字>+‘:’+<数据值>”的格式进行储存，实际访问过程中我们也是通过访问<名字>来获取对应<数据值>。在数学上这种“给定一个值——返回一个对应值作为结果”的操作我们称之为“映射”。Java也沿用了这一操作，并且用了一个被称为“关联数组”（Associative Array，也被称为Map或Dictionary）的特殊数组来储存映射的键（Key）与值/结果（Value）。在这个数组中，键（Key）与值/结果（Value）是一一对应的（每一对Key和Value被称为“Entry”，中文名叫键值对），与数组不同的是其用Key代替数字作为下标。这样，通过访问这个特殊的数组，给出我们的键（Key），它就可以给我们对应的值/结果（Value）。

    或许有人会提出：这不和函数的操作是一样的嘛，只不过只能放入一个参数罢了。笔者承认：映射是一种广义的函数，但就像数学上的函数，我们既可以通过枚举写出每一个自变量对应的因变量，然后做成一张函数表，通过查表的方式获取因变量，又可以通过数学运算法则直接计算出某个自变量对应的因变量。而我们之前学习的函数更多的是给出“计算方法”，放入参数然后通过运算法则计算出结果；“关联数组”则是储存一张函数对应表，表中写明了自变量对应的因变量的值，只需要告知参数取出相应值即可。函数的优点在于适配性高，规律性强，但关联数组可以继承数组的大部分操作，对于一些有限的、人为关联的、规律性较差的映射方法，直接枚举出Key与Value并使用关联数组进行储存比写函数然后一个一个枚举条件比较并return要更有优势。

    关联数组是一种特殊的数组，作用丰富，应用广泛。它可以同时存储本来需要两个数组存储的、需要一一对应的数据，也可以用来给存储的数据分类标记。在CrT中，关联数组的格式为：

    var myMap as TypeValue[TypeKey] = {
    key1:value1,
    key2:value2,
    key3:value3
};
//这里的TypeKey指的是Key的类型，TypeValue同理
//这里对TypeKey和TypeValue两个类型并不存在任何限制，但一般而言TypeKey更多都是字符串String类型

如这里我用String类型作Key表示药水名称，用int类型作Value表示药水持续时间，写一个关联数组用于储存一批准备被添加到攻击目标上的药水效果以及持续时间，以供后边的事件使用：
var durationMap as int[string] = {
    "hunger":200,
    "poison":300,
    "wither":50
};
 
print(durationMap["hunger"]);//输出：200
print(durationMap["poison"]);//输出：300

这里也可以用IPotion类来做Key，因此我们可以这样写：

import crafttweaker.potions.IPotion;
 
var potionMap as int[IPotion] = {
    <potion:minecraft:hunger>:200,
    <potion:minecraft:poison>:300,
    <potion:minecraft:wither>:50
};
 
print(potionMap[<potion:minecraft:wither>]);
//输出：100
    对关联数组，我们也可以进行遍历操作，但和数组不同的是，for后变量必须是“key”（对于每一个key）、“key,value”（对于每一个key-value对），或者用entry遍历map.entrySet（同样也是对于每一个key-value对），这里笔者用上边做的那个durationMap为例演示：

var durationMap as int[string] = {
    "hunger":200,
    "poison":300,
    "wither":50
};
 
//=====================
for key in durationMap {
    print(key);
}
//输出：
//poison
//wither
//hunger
//=====================
 
//=====================
for key,value in durationMap {
    print(key~":"~(value as string));
    //注意这里key与value是一一对应的
}
//输出
//poison:300
//wither:50
//hunger:200
//=====================
 
//=====================
//注意这里，不再是durationMap而是durationMap.entrySet
for entry in durationMap.entrySet {
    print(
        entry.key
        ~":"~
        entry.value as string
    );
    //用entry.key返回遍历中某一个key-value对的key
    //用entry.value返回遍历中某一个key-value对的value
}
//输出
//poison:300
//wither:50
//hunger:200
//=====================


    下边用一个例子进行说明关联数组的一个用法：我希望给钻石剑添加一个特殊的机制：在攻击敌人时给对方上10s的饥饿、15s的中毒与2.5s的凋零（均为I级）。事件本身比较简单，在不使用关联数组的情况下使用枚举就可以写出来：（看不懂事件的没关系，可以不用管事件怎么写的，着重关注优化的那一步）


import crafttweaker.event.EntityLivingHurtEvent;
import crafttweaker.damage.IDamageSource;
import crafttweaker.entity.IEntityLivingBase;
import crafttweaker.player.IPlayer;
 
events.onEntityLivingHurt(function(event as EntityLivingHurtEvent){
    if(event.entityLivingBase.world.remote)return;
    var dmgsrc as IDamageSource = event.damageSource;
    var entity as IEntityLivingBase = event.entityLivingBase;
    if(
        !isNull(dmgsrc)&&
        !isNull(dmgsrc.damageType)&&
        !(dmgsrc.projectile)&&
        !isNull(dmgsrc.getTrueSource())&&
        (dmgsrc.getTrueSource() instanceof IPlayer)
    ){
        var player as IPlayer = dmgsrc.getTrueSource();
        if(!isNull(player.currentItem)&&<item:minecraft:diamond_sword:*>.matches(player.currentItem)){
            //=======================================
            //对这一段进行优化
            entity.addPotionEffect(<potion:minecraft:hunger>.makePotionEffect(200,0));
            entity.addPotionEffect(<potion:minecraft:poison>.makePotionEffect(300,0));
            entity.addPotionEffect(<potion:minecraft:wither>.makePotionEffect(100,0));
            //=======================================
        }
    }
});
    这里我们使用关联数组对其中添加药水部分进行优化，把上边那个potionMap给拿出来

import crafttweaker.potions.IPotion;
 
var potionMap as int[IPotion] = {
    <potion:minecraft:hunger>:200,
    <potion:minecraft:poison>:300,
    <potion:minecraft:wither>:50
};
    改写上述代码：

import crafttweaker.event.EntityLivingHurtEvent;
import crafttweaker.damage.IDamageSource;
import crafttweaker.entity.IEntityLivingBase;
import crafttweaker.player.IPlayer;
import crafttweaker.potions.IPotion;
 
static potionMap as int[IPotion] = {
    <potion:minecraft:hunger>:200,
    <potion:minecraft:poison>:300,
    <potion:minecraft:wither>:50
};
 
events.onEntityLivingHurt(function(event as EntityLivingHurtEvent){
    if(event.entityLivingBase.world.remote)return;
    var dmgsrc as IDamageSource = event.damageSource;
    var entity as IEntityLivingBase = event.entityLivingBase;
    if(
        !isNull(dmgsrc)&&
        !isNull(dmgsrc.damageType)&&
        !(dmgsrc.projectile)&&
        !isNull(dmgsrc.getTrueSource())&&
        (dmgsrc.getTrueSource() instanceof IPlayer)
    ){
        var player as IPlayer = dmgsrc.getTrueSource();
        if(!isNull(player.currentItem)&&<item:minecraft:diamond_sword:*>.matches(player.currentItem)){
            //=======================================
            //优化后
            for key,value in potionMap{
                entity.addPotionEffect(key.makePotionEffect(value,0));
            }
            //=======================================
        }
    }
});
    这样写的一个优势在于配置便捷，换言之我们可以把potionMap放在专门的配置文件脚本中，然后在这个事件代码中使用跨脚本调用。这样只需要更改配置文件的内容而非源代码，便可以实现效果的更改。

    实际上对于那些知道怎样建立数组批量添加配方的读者可能会想到：对于那些框架大致相同、但部分位置存在多处不同的配方（比如说不同原木拆出不同数量的木板），单个数组无能为力，但可以使用关联数组遍历进行优化。

    当然能做value的不止普通的类，数组同样也可以作value，笔者曾用Crt写过一个机制并附带了配置文件，里边大量运用了关联数组：（百合真好磕）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第3张图片

   关联数组还有各种其他的操作，这里就不一一列举说明了，感兴趣可以去上文相关链接查阅。



2.IData类

    就像各种编程语言中有基础的数据类型、数组一样，IData类也包装了一套基础的数据类型与数组，统称为“IData类数据”。IData类下的数据在官方文档中被称为“Data”+<对应基础数据类型>，如“DataInt”、“DataDouble”等。IData共有以下12种数据类型：

IData数据类型	对应基础数据类型
DataInt	int
DataFloat	float
DataDouble	double
DataBool	bool
DataString	string
DataInt[]	int[]
DataByte	byte（字节）
DataShort	short（短整型）
DataLong	long（长整型）
DataByte[]	byte[]
DataList*	数组
DataMap**	关联数组
    注：*经过测试，这里的List数组可以接受任一能被转化为IData对象的数据作为其数据成员，甚至你可以放一个IItemStack进去（某群dalao：可序列化是这样的）（但经过测试IPotion/IPotionEffect、IWorld、IEntityLivingBase、IPlayer等都不行）（换言之List数组本身就是一个IData类下的IData数组）；

        （警告：DataList不能使用增强for循环！也就是说不能用for i in dataList{}的形式来遍历！）

           **DataMap是可以被视为一种特殊的关联数组，下文会详细介绍DataMap

    我们可以用以下语句来获取一个简单的Data类的数据：

import crafttweaker.data.IData;//下文默认已导包
 
var myIData as IData = "String" as IData;//DataString类型的IData数据
 
print(myIData);//IData可以直接输出，默认转化为字符串，输出结果为：String；
 
myIData  = 1 as IData;//DataInt类型的IData数据
 
print(myIData);//输出结果为：1
    注意此处必须要在基础数据类型后边加上as IData，否则会出现字节码报错。

    你可以直接把一个“任意成员都可以转化成IData数据”的数组直接赋值给IData对象而不需要as IData，虽然我个人反对不加as IData这种做法：

var mySpecialData as IData =[
    1 as int,
    2 as byte,
    3 as short,
    4 as long,
    5 as float,
    6 as double,
    7 as string,
    [
        8 as int,
        9 as int
    ] as int[],
    [
        10 as byte,
        11 as byte
    ] as byte[],
    {
        "This is a key":"This is not a value"
    } as IData
];//这里笔者演示了没用as IData的语句，实际操作中建议各位加上
print(mySpecialIData);
//输出结果：[1, 2 as byte, 3 as short, 4 as long, 5.0 as float, 6.0, "7", [8, 9] as int[], （接下行）
//[10, 11] as byte[] as byte[],{"This is a key": "This is not a value"}]
    甚至你还可以更疯狂：

//这里已经获取了玩家数据储存在player变量中
var data as IData = [
    1 as int,
    2.0d,
    "str",
    [
        player.currentItem,//IItemStack对象
        "what the hell?!",
        player.currentItem.tag
    ]
] as IData;
 
player.sendChat("data:"~data~"\n");
//这里应该使用player.sendChat("data:"~data.asString()~"\n")这种表达，具体asString()下文会详述
    手持上文展示的钻石剑后触发，输出结果为：（变色效果为Lore中带的格式化代码，这里建议使用格式化代码后记得在对应字符串后加“§r”，不然这个变色效果会影响调试结果，就像下图一样）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第4张图片

    比较可惜的是，这种疯狂的操作只能对IData起作用；直接对一个变量赋两个数据类型不同的值的数组（如：var test = [1,2.0d]）会直接字节码报错；

    和我们对int、double类型的数据进行加减乘除、大小比较，对bool类型的数据进行取和、取且、取反等操作一样，IData类下的数据同样支持各种运算（操作符），如：

var data1 as IData = 1 as IData;
var data2 as IData = 2 as IData;
 
print(data1+data2);//输出结果：3
print(data1-data2);//输出结果：-1
print(data1*data2);//输出结果：2
print(data1/data2);//输出结果：0，注意此处int向下取整
print(data1.asDouble()/data2.asDouble());//输出结果：0.5
 
print(data2/(2 as IData)!=data1);//输出结果：false
    在运算过程中，可以将作为IData类下int、float、double等非字符串、数组和关联数组的基础数据类型直接与ZenScript的数据类型进行运算：
//此处借用上文中的data1与data2变量
print(2/data2!=1)//输出：false
print((5/2+2.99f)==data2*2);//输出：true，注意int向下取整问题
    DataString类型的IData对象也可以进行“+”而不是“~”进行字符串的连接运算操作：

var ds1 = "ds1" as IData;
var ds2 = "123" as IData;
print(ds1+ds2);//输出结果：ds1123
    IData类型的各种运算操作参见文档：Official / Zentutorial （主要是看各种数据类型能否进行特定运算操作）



    由于IData类型下的数据类型和ZenScript类型下数据类型是一一对应的，IData类提供了<asType()>的Method来进行数据转换，如：asInt()、asString()，例如：

var str as string = "3.1415926";
var data as IData = str as IData;
 
print(data+1);//输出：3.14159261
print(data.asDouble()+1)//输出：4.1415926
    值得注意的是，如果字符串非纯数字/对应有效数字，会弹出一个报错并返回默认值0。这给我们提供了一个Crt中从string到int/double/float的数据类型转化方法：

print( ("3.14" as IData).asInt() );//报错并输出0
print( ("3.14" as IData).asFloat() );//输出3.14
print( ("3.14" as IData).asDouble() );//输出3.14
    这里必须得强调一个规范问题：在你准备调试输出IData对象时，请务必使用.asString()将其转化为字符串再print()或player.sendChat()输出。上文中所有理应按照这种形式输出（但实际并没有）的情况仅作为教程演示案例，笔者不推荐这样做！

3.DataMap

    实际上对于IData类的实操而言，我们更多接触的是其子类——DataMap。DataMap可以被视为一种特殊的关联数组，其只接受String类型的变量作Key，任一IData类型的数据作Value，一般格式为：

var myDataMap = {
    Key1:Value1,
    Key2:Value2,
    Key3:Value3
} as IData;
    其中必须得强调的一点是：若在Key位置的关键词若不加“ "" ”则系统只会将其解析为一个该名字的字符串而不是自定义的变量，如果该关键词带有“[]”或“.”则会被解析为指针；重复的key后者会覆盖前者，从而可能导致歧义，如下例：（感谢友谊妈提醒！）

var key as string = "key1";
 
//===================================//
 
print(({key:1 as int} as IData).asString());//输出：{key:1}，这里不加 as IData会报错
 
print(({"key":1 as int} as IData).asString());//输出：{key:1}，同上，这里不加 as IData也会报错
 
//===================================//
 
var myDataMap = {
    key:1 as int,
    "key":2 as int
} as IData;
print(myDataMap.asString());//输出：{key:2}
 
myDataMap = {
    "key":20 as int,
    key:10 as int
}
print(myDataMap.asString());//输出：{key:10}
 
//===================================//
 
//此处未定义名为“dataKey”的IData变量
 
myDataMap = {
    dataKey.asString():1 as int,
    "dataKey.asString()":2 as int
};
print(myDataMap.asString());//找不到“dataKey”，系统报错
 
//以下输出忽略上述报错
 
var dataKey as IData = "key1" as IData;
myDataMap = {
    dataKey.asString():1 as int,
    "dataKey.asString()":2 as int
};
print(myDataMap.asString());//输出：{key1:1,"dataKey.asString()":2}

//===================================//
 
//此处未定义名为“keyArray”的数组
 
myDataMap = {
    keyArray[0]:1 as int,
    "keyArray[0]":2 as int
} as IData;
print(myDataMap.asString());//系统找不到变量keyArray，直接字节码报错
 
//以下输出忽略上述报错
 
var keyArray as string[] = ["thisKey"];
myDataMap = {
    keyArray[0]:1 as int,
    "keyArray[0]":2 as int
} as IData;
print(myDataMap.asString());//输出：{thisKey:1,"keyArray[0]":2}
 
//===================================//
 
//此处假定获取了名为“Aoi_Mahara”的玩家的数据信息并储存在player变量中，但未定义名为entity的变量：
 
myDataMap = {
    player.name:1 as int,
    "player.name":2 as int
};
print(myDataMap.asString());//输出：{Aoi_Mahara:1,player.name:2}
 
myDataMap = {
    entity.name:1 as int,
    "entity.name":2 as int
}
print(myDataMap.asString());//未找到变量entity，系统报错
 
//以下输出忽略上述报错
 
var entity as IPlayer= player;
myDataMap = {
    entity.name:1 as int,
    "entity.name":2 as int
}
print(myDataMap.asString());//输出：{Aoi_Mahara:1,"entity.name":2}
    而在Value处则会优先解释为自定义变量而不会转化为字符串，如下例：

//此处未定义名为value的变量
 
var myDataMap as IData = {
    "key1":value as string,
    "key2":"value" as string
} as IData;
print(myDataMap.asString());//找不到value，系统报错
 
//以下输出忽略上述报错
 
var value as string = "abc";
var mySecnodDataMap as IData = {
    "key1":value as string,
    "key2":"value" as string
} as IData;
print(mySecnodDataMap.asString());//输出：{key1:"abc",key2:"value"}
    由于Key与Value处对自定义变量的解析情况不同，笔者建议有以下建议：

    1.对Key的值尽量使用手打的、带有“ "" ”的字符串，不要用自定义变量传入，也尽可能避免在你不熟悉解析规则的时候使用指针传入（熟练了请随意）；

    2.由于Value处系统会优先解析自定义变量的值，所以对Value的值请随意（



    由于Value处的值可以是任意IData对象，所以DataMap可以嵌套DataMap，使其成为DataMapTree，大部分NBT都是这种结构，如：


var nestedMap as IData = {
    "key1":{
        "key1":"value1"
    } as IData
} as IData;
 
//上述钻石剑的NBT：
{
    ench: [
        {
            lvl: 1 as short, 
            id: 70 as short
        }
    ], 
    RepairCost: 1, 
    Quality: {
    },
    display: {
        Lore: [
            "§b测试lore:test", 
            "累计击杀：16"
        ], 
        Name: "super 钻石剑"
    }, 
    DeathCount: 16
}
    注：DataMap不允许递归调用！如下列的语句会报错：

var recDM as IData = {
    "key1":1 as byte,
    "key2":recDM
} as IData;
//报错：在crafttweaker.data.IData中找不到静态对象recDM
    既然有了DataMap来储存数据，那如何访问数据呢？我们可以用“<IData>.memberGet(String targetKeyName)”或者“<IData>.<targetKeyName>”来访问，如下例：

var myDataMap as IData = {
    "key1":"value1 is a string",
    "key2":3.14 as float,
    "key3":{
        "key3":"value3.0",
        "key1ofkey3":"value3.1",
        "key3.2":"value3.2",
        "key3[3]":"value3.3",
        "key3.asString()":"value3.4",
        "1":1 as int
    },
    "key5":{}//这里key5的Value是一个空的DataMap
};
    我们先访问前两个Key：

//<IData>.memberGet(String targetKeyName)方法访问：
print(myDataMap.memberGet("key1").asString());//输出：value1 is a string
 
//<IData>.<targetKeyName>方法访问：
print(myDataMap.key2.asString());//输出：3.14
    两种方法各有优劣，一般而言第二种方法更常用（符合ZenGetter的调用习惯），但第一种方法是万金油，特别是对于需要调用的Key是一个string类型的自定义变量储存的值时，第二种方法无能为力，此时就需要使用第一种方法。

    但这里有个问题：如果目标访问的Key值不存在，则会扔出空指针报错；若是第一种则会返回一个null，所以对DataMap访问某个Key时必须先检查非空！

//上述myDataMap并未含有名为“key4”的Key，此处尝试访问：
 
print(myDataMap.memberGet("key4").asString());//程序在运行中抛出空指针报错
 
print(myDataMap.key4.asString());//程序在运行中抛出空指针报错
 
//注意可以直接用print(null)输出null，但不能直接对空指针进行print(<nullPointer>)，系统会抛出空指针异常
//player.sendChat(String message)甚至不能以null为参数
    检测非空可以用以下方法：

    1.对memberGet(String targetKeyName)或<IData>.<targetKeyName>进行非空检测；

    2.in/has关键字（非常不建议用“in”！因为“in”必须按照“has”的英文逻辑来理解！）；

print(isNull(myDataMap.memberGet("key1")));    //false
print(isNull(myDataMap.key1));    //false
print(isNull(myDataMap.memberGet("key4")));    //true
print(isNull(myDataMap.key4));    //true
 
//下例中in/has关键词可以互换
print(myDataMap in "key1");    //true
print(myDataMap has "key4");    //false
    通过上述例子可以看出，对于不存在的目标Key，尽管我们可以直接用<IData>.<targetKeyName>来调用，但只要一涉及其他非判空的操作就极有可能报空指针错误，不要随意挥舞空指针！

    （P.S.为了教程简洁，下文所有用于演示举例的、手写赋值的、可以一眼看出目标成员是否存在的DataMap笔者就不再进行检查非空了（实战部分除外））



    对于key3，直接访问就可以得到其中的DataMap：

var key3DM as IData = myDataMap.key3;
print(key3DM.asString());
//{"1": 1, "key3[3]": "value3.3", "key3.2": "value3.2", （接下文）
//key3: "value3.0", key1ofkey3: "value3.1", "key3.asString()": "value3.4"}
    但是，有只要不去作就不会出现的时候，使用“<IData>.<targetKeyName>”方法访问时语法中的Key会出现歧义，如上文中的key3DM：

//以下是输出没问题的
print(key3DM.key3.asString());    //输出：value3.0
print(key3DM.key1ofkey3.asString());    //输出：value3.1
 
//以下是输出有问题的
//此处不使用.asString()输出用于演示歧义
print(key3DM.key3.asString());    //期望输出value3.4，实际输出value3.0
print(key3DM.key3[3]);    //期望输出value3.3，实际输出：u（"value3.0"的第4个字母）
print(key3DM.key3[3].asString());    //期望输出value3.3，实际输出：u
//p.s.此处可以通过for循环输出key3DM中key3的每一个字符，倒不如说String/DataString都可以这样做
//可以用.length获取字符串长度
//只是不能用foreach直接遍历
 
//以下是输出直接报错的
print(key3DM.key3.asString().asString());    //报错：对字符串找不到asString()方法
print(key3DM.1);    //报错：非法表达
print(key3DM.key3.2);    //报错：非法表达
print(key3DM.1.asString());    //报错：非法表达
print(key3DM.key3.2.asString());    //报错：非法表达
    那除了memberGet()外，我们还有没有办法来解决这个问题呢？还真有！不然我就不会写了。既然Key处只能是字符串，我们只需要把Key给套个“ "" ”，写成字符串就行：

print(key3DM."1".asString());    //输出：1
print(key3DM."key3.2".asString());    //输出：value3.2
print(key3DM."key3[3]".asString());    //输出：value3.3
print(key3DM."key3.asString()".asString());    //输出：value3.4
    愿你们永远不要用到这一条......（划去）笔者表示，真有Modder在Key里边塞“.”，比如这个模组。

    除了获取值，原版Crt中DataMap还支持修改（+ / - ），具体规则如下：

var data1 as IData = {
    "key1":1 as int,
    "key2":2 as int
} as IData;
 
var data2 as IData = {
    "key1":1 as int,
    "key3":3 as int
} as IData;
 
//“+”会将两个DataMap作并集，合并相同元素，保留不同元素
//即data1 + data2 = data1∪data2
print((data1 + data2).asString());//输出：{key1:1,key2:2,key3:3}
 
 
//“+”会将两个DataMap作交集后对第一个集合作补集，去掉相同元素，保留不同元素
//即data1 - data2 = data1\(data1∩data2)
print((data1 - data2).asString());//输出：{key2:2}
 
//=================================//
 
//此处修改data2中key1的值，使得两个DataMap中key1的Value不同，再尝试输出：
data2 = {
    "key1":2 as int,
    "key3":3 as int
} as IData;
 
//“+”操作中，对于公有的Key，后边DataMap中的值会覆盖前边的值
print((data1 + data2).asString());//输出：{key1:2,key2:2,key3:3};
print((data2 + data1).asString());//输出：{key1:1,key2:2,key3:3};
 
//“-”操作会对第一个DataMap裁去相同的Key对应的Key-Value对，即使二者Value不同：
print(isNull(data1 - data2)? "null":(data1 - data2));//输出：{key2:2}
print(isNull(data2 - data1)? "null":(data2 - data1));//输出：{key3:3}
 
//----------------------------------------//
 
//此处继续验证上述结论
 
var dm1 as IData = {
    "key1":1 as int,
    "key2":"value2",
    "key3":2.718 as float
} as IData;
 
var dm2 as IData = {
    "key1":"anything!",
    "key2":{}//这里是一个空的DataMap
} as IData;
 
print((dm1+dm2).asString());//输出：{key1:"anything!",key2:{},key3:2.718 as float}
print((dm2+dm1).asString());//输出：{key1:1,key2:"value2",key3:2.718 as float}
 
print(isNull(dm1 - dm2)? "null":(dm1 - dm2).asString());//输出：{key3:2.718 as float}
print(isNull(dm2 - dm1)? "null":(dm2 - dm1).asString());//输出：{}，这是一个空的DataMap
    这里有一个必须得说的，多层DataMap中的“+/-”操作规则，如下例所示：

var dm1 as IData = {
    "key1":{
        "key1":1 as int,
        "key2":2 as int
    },
    "key2":{
        "key1":1 as int,
        "key2":2 as int
    },
    "key3":{
        "key1":1 as int,
        "key2":2 as int
    },
    "key4":{
        "key1":1 as int,
        "key2":2 as int
    },
    "key5":{
        "key1":1 as int,
        "key2":2 as int
    }
} as IData;
 
var dm2 as IData = {
    "key1":1 as int,
    "key2":{
        "key1":1 as int,
        "key2":2 as int
    },
    "key3":{
        "key1":1 as int,
        "key3":3 as int
    },
    "key4":{
        "key1":1.0 as double,
        "key2":2.0 as double
    },
    "key5":{}
} as IData;
 
print((dm1+dm2).asString());
//输出：{
//    key1: 1, 
//    key2: {key1: 1, key2: 2}, 
//    key5: {key1: 1, key2: 2}, 
//    key3: {key1: 1, key2: 2, key3: 3}, 
//    key4: {key1: 1.0, key2: 2.0}
//}
 
print((dm2+dm1).asString());
//输出：{
//    key1: {key1: 1, key2: 2}, 
//    key2: {key1: 1, key2: 2}, 
//    key5: {key1: 1, key2: 2}, 
//    key3: {key1: 1, key2: 2, key3: 3}, 
//    key4: {key1: 1, key2: 2}
//}
 
print(isNull(dm1 - dm2)? "null":(dm1 - dm2).asString());
//输出： {
//    key2: {},
//    key5: {key1: 1, key2: 2}, 
//    key3: {key2: 2}, 
//    key4: {}
//}
 
print(isNull(dm2 - dm1)? "null":(dm2 - dm1).asString());
//输出： {
//    key2: {}, 
//    key5: {}, 
//    key3: {key3: 3}, 
//    key4: {}
//}
    · 首先我们来研究“+”操作：

        (1)对于第一层中的key1，dm1+dm2结果中后者key1的值把前者给覆盖掉了，得到了“key1:1”这个结果，同理dm2+dm1得到的是dm1的key1：“key1: {key1: 1, key2: 2}”;

        (2)对于第一层中的key2，由于dm1.key2与dm2.key2完全相同，结果保留；

        (3)对于第一层中的key3，其值等于原先两个DataMap相加；

        (4)对于第一层中的key4，dm1+dm2得到的结果是dm2.key4把dm1.key4给覆盖掉了，反之dm2+dm1则是dm1.key4覆盖dm2.key4；

        (5)对于第一层中的key5，由于dm2.key5是一个空的DataMap（指内部没有任何一个Key-Value对的DataMap），故结果保留dm1.key5；

    · 然后是难点——“-”操作：

        (1)对于第一层中的key1，由于dm1.key1属于DataMap而dm2.key1属于DataInt，所属类型（子类）不同，故无论是dm1-dm2还是dm2-dm1二者互相裁去对方的key1；

        (2)对于第一层中的key2，二者都是DataMap但完全相同，此时第一层的key2保留，但key2里边的两个第二层的key1、key2相互被对方裁去，最终得到“key2:{}”；

        (3)对于第一层中的key3，二者都是DataMap但同时存在相同和不同元素，此时key3依旧保留，但内部相同元素被对方裁去，得到“key3: {key2: 2}”（dm1-dm2）和“key3: {key3: 3}”（dm2-dm1）；

        (4)对于第一层中的key4，二者依旧属于DataMap但key完全相同、内部值不相同，故此处key4保留但两个key第二层中的1、key2相互被对方裁去，最终得到“key4:{}”；

        (5)对于第一层中的key5，dm1.key5有值但dm2.key5为空的DataMap，dm1-dm2不裁去任何Key，dm2-dm1没法裁去任何key，最终得到“key5: {key1: 1, key2: 2}”（dm1-dm2）和“key5: {}”（dm2-dm1）；

    · 总结以上现象，我们可以得到两个DataMap的+/-操作准则：

    1.“+”操作：

        (1)首先检查两个DataMap中的Key，保留所有不共有Key的Key-Value对，对于共有Key进入下一步操作；

        (2)对比两个Key对应的Value的类型，若不均为DataMap则后者的Value覆盖前者的Value；否则保留该Key，并将两个DataMap相加（回到第一步）的结果作为Value；

    2.“-”操作：

        (1)首先检查两个DataMap中第一个DataMap里不存在于第二个DataMap的Key，保留其Key-Value对；对于共有的Key则进入下一步操作；

        (2)其次比较两个Key对应的Value的类型，若不均为DataMap则前者裁去此Key对应的Key-Value对，直至所有共有Key对应的Key-Value对被裁去或自身为“空”的DataMap（“{}”）；否则保留该Key，并将两个DataMap相减（回到第一步）的结果作为Value；

    （简单总结来说就是保留两个DataMap中相同的DataMapTree框架，而对其余部分进行覆盖/裁剪。）



    这里补充一个关于自身成员为“空”的DataMap（"{}"）的判空检测（memberGet(String targetKeyName)和.targetKeyName），如下例：

var egDM as IData = {
    "nullDataMapPointer":{}
} as IData;
 
//这里未给出名为“key1”的Key
print(isNull(egDM.memberGet("key1")));//true
print(isNull(egDM.key1));//true
 
print(isNull(egDM.memberGet("nullDataMapPointer")));//false
print(isNull(egDM.nullDataMapPointer));//false
    可以看到，一个DataMap只要存在就不会是空，哪怕它里边一个Key-Value对都没有。

    此外，IData还有一个Method：<IData>.update(IData dataToBeUpdate)。它返回一个更新（Updated）后的IData对象，但自身并不对原有的两个IData对象进行修改（注意与后边讲的IMutableItemStack的updateTag(IData newNBT)方法进行区分），相当于上述运算符“+”：

//备注：此处沿用上述data1/data2对象
//data1输出为：{key1:1,key2:2}
//data2输出为：{key1:2,key3:3}
 
print("===Before Update()===");
print("data1:"~data1.asString());
print("data2:"~data2.asString());
var data3 as IData = data2.update(data1);
print("===After Update()===")；
print("data2.update(data1)):"~data3.asString());
print("data1:"~data1.asString());
print("data2:"~data2.asString());
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第5张图片
这里笔者使用player.sendChat()进行输出

    实际上update()函数的操作方式和IMutableItemStack的updateTag()函数、IPlayer的update()函数对NBT更新操作一样，不同的是IData的update()函数只是返回Update操作后的IData数据值，而前边说的来自IMutableItemStack、IPlayer的update相关功能则是将Update操作后的IData覆写在原来NBT数据上，通过这一点我们可以对物品和玩家的NBT进行修改（笔者没尝试过IEntity的setNBT()函数，推测和前边一样）。

    （注意：我并不赞同友谊妈在Zentutorial里说的“+/-是你一可以修改值的操作方式”这种说法，实际上无论是+/-运算符还是update()函数，抑或是下文提到的Zen Utils模组提供的deepUpdate()功能，都只是进行运算，得出并返回结果，而没有对原有IData数据的值进行修改！）

4.事件中NBT操作的一般方法

    这里贴出友谊妈在Zentutorial中关于NBT操作的两个简略案例：Click Here（讲得真的很简略（捂脸））

    在事件中，我们如果想要操作一个能搭载NBT对象（比如说物品IItemStack、玩家IPlayer、生物IEntity）其上的NBT时，一般来说会通过以下几个步骤实现：

        1.尝试获取其上的NBT信息（比如item.tag，player.data或者entity.nbt）；

        2.对拿到手的IData类的NBT数据，我们需根据目标NBT，从内到外逐层检查是否非空，并最终获取到结果（要么是这个数据，要么是没有这个数据）。

            比如说我想要知道一个物品，其display下Lore标签的内容，那在通过item.tag拿到这个物品的NBT后，我们需要先判断这个NBT是否为空（物品是否不具有NBT），再是这个NBT是否包含名为“display”的数据，然后是这个"display"数据下是否存在名为“Lore”的数据，在经过以上判断并且得到存在的结果后，我们才能放心地用item.tag.display.Lore来获得这个内容；

        3.对结果进行操作（使用IData的各种操作、运算符），并最终得到一个新的IData对象，把它包装成和原来NBT相同的格式（数值可能不同）后，用对应物品/玩家/生物提供的update()函数进行操作，把这个新的NBT给update上去。

    值得注意的是，最后一个update操作相当于：把物品原来的NBT取出来（这里叫originalNBT）然后和传入的NBT（newNBT）进行update（也就是originalNBT+newNBT或者originalNBT.update(newNBT)），然后把得到的NBT替换掉原来物品的NBT；而对于物品来说想要使用updateTag(IData newNBT)函数，必须先mutable()一下，也即得写成item.mutable().updateTag(IData newNBT)。

    这里给一个简单的例子，我们写一个拿着物品放右手右键一下就能消除附魔惩罚（使物品NBT中"RepairCost"对应数据归零）的调试脚本：

import crafttweaker.event.PlayerRightClickItemEvent;
 
import crafttweaker.data.IData;
import crafttweaker.player.IPlayer;
import crafttweaker.item.IItemStack;
 
events.onPlayerRightClickItem(function(event as PlayerRightClickItemEvent){
    var player as IPlayer = event.player;
    var item as IItemStack = event.item;
    //事件规范
    if(event.world.remote)return;
    //排除玩家手上没拿着物品（物品为空）、检测的物品并不在右手的情况
    if(isNull(item)||!player.currentItem.matchesExact(item)) return;
     
    //然后开始读取并操作NBT
    //目标NBT形式为{"RepairCost":xxx}
    if(
        //首先得是几个检查：
        //1.物品需要有NBT
        !isNull(item.tag)&&
         
        //2.物品需要有“RepairCost”这个NBT
        !isNull(item.tag.memberGet("RepairCost"))&&
         
        //3.物品具有附魔惩罚（RepairCost对应数据需要大于0）
        item.tag.RepairCost >= 0
    ){
        //上述检查都通过后我们才能修改它
        //这里由于是要让附魔惩罚归零，新的NBT形式为{"RepairCost":0 as byte}
        //我们只需要让物品mutable()一下然后把这个NBT给update上去即可
        item.mutable().updateTag({"RepairCost":0 as byte} as IData);
         
        //然后我们给玩家发送一个调试信息，说脚本已经成功执行了
        player.sendChat("RepairCost checked and zeroized!");
        return;
    }
    //这里给一个失败的调试信息
    player.sendChat("RepairCost Not Found or is Zero!");
    
});
    这里我们拿着附魔钻石剑右键一下，弹出了成功的调试信息：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第6张图片

    /ct nbt检查一下：确实RepairCost归零了：（放进铁砧打附魔/改名字也可以检查）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第7张图片

    换个例子：右键给主手上物品添加一个可视化标签（Lore）

import crafttweaker.event.PlayerRightClickItemEvent;
import crafttweaker.player.IPlayer;
import crafttweaker.data.IData;
import crafttweaker.item.IItemStack;
 
events.onPlayerRightClickItem(function(event as PlayerRightClickItemEvent){
    var player as IPlayer = event.player;
    var item as IItemStack = event.item;
    if(player.world.remote||isNull(item)||isNull(player.currentItem)||!player.currentItem.matchesExact(item))return;
     
    //这里开始操作NBT
    //由于Lore相关的NBT结构为：{display:{Lore:[<DataString>]}}
    //我们只需要添加标签，没必要管原有标签是啥
    //所以这里我们直接写一个这样的结构
    var loreToBeUpdatedWith as IData = {"display":{"Lore":["§b测试标签！§r"]}} as IData;
     
    //然后把自定义好的NBT IData数据给update上去，别忘了mutable()
    item.mutable().updateTag(loreToBeUpdatedWith);
     
});
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第8张图片
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第9张图片
    或许有的读者会提出：为啥我能读取NBT后不直接进行赋值操作，非得绕这样一个大弯来搞什么来自NBT搭载对象的update()函数？直接赋值不是更简单？

    这样说吧，一般而言来自NBT的IData都不会允许你使用赋值操作直接修改其中的值，如下例：

     由于物品的附魔也是靠NBT储存的，那我们能不能通过操作NBT来修改附魔呢？答案当然是可以的，不过操作需要讲究方法。

//要求：右键将带有经验修补（id:70）的魔咒转化为耐久I（id:34）
 
import crafttweaker.event.PlayerRightClickItemEvent;
import crafttweaker.player.IPlayer;
import crafttweaker.data.IData;
import crafttweaker.item.IItemStack;
 
events.onPlayerRightClickItem(function(event as PlayerRightClickItemEvent){
    var player as IPlayer = event.player;
    var item as IItemStack = event.item;
    if(player.world.remote||isNull(item)||isNull(player.currentItem)||!player.currentItem.matchesExact(item))return;
     
    //这里开始操作NBT
    //由于附魔相关NBT结构为：{ench:[{id:<value> as short,lvl:<value> as short},......]}
    //我们需要对物品的NBT进行访问：
    if(
        !isNull(item.tag)&&//物品有NBT
        !isNull(item.tag.ench)//物品有附魔的NBT
    ){
        var enchList as IData = item.tag.ench;//取出附魔相关NBT数据，这是一个DataList
        var targetEnch as IData = null;//先自定义一个null的IData局部变量备用
        var index as int = 0 ;//先自定义一个index
        if(enchList.length==0) return;//遍历前检测数组长度是否为0，养成好习惯
        //准备遍历
        for i in 0 .. enchList.length{
            if(enchList[i].id.asInt()==70){ //检测到经验修补魔咒
                targetEnch = enchList[i];//把这个数据给放到之前的局部变量中
                player.sendChat("Mending Found!");//输出调试
                index = i;//储存index备用
                break;//退出循环
            }
        }
        if(isNull(targetEnch))return;//如果没有经验修补则直接return
        //下文放修改代码
        //========
        //Code
        //========
});
    这里我们试着用以下代码去修改这个值：(放在//Code处)

1
targetEnch.id = 34 as short;
    /ct reload，不出意料地报错了：（第一次reload没放上一行的代码，可以检测到经验修补，第二次直接报错）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第10张图片
两次reload，第二次报错

    这给了我们一个教训：不能直接修改DataMap的值！

    好在这是一个DataList，查询官方wiki，我们可以通过修改对应下标的值继续尝试改附魔：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第11张图片

1
enchList[index] = {"id":34 as short,"lvl": 1 as short} as IData;
    /ct reload后没报错，但右键后不出意料地又报错了：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第12张图片

    这又是一个教训：不要随便、轻易地去直接修改NBT。

    没辙了？老老实实地推翻重来吧。

    P.S.此处用到了下文提到的IData Deep Update，或者遍历也可以处理：

events.onPlayerRightClickItem(function(event as PlayerRightClickItemEvent){
    var player as IPlayer = event.player;
    var item as IItemStack = event.item;
    if(player.world.remote||isNull(item)||isNull(player.currentItem)||!player.currentItem.matchesExact(item))return;
    
    //这里开始操作NBT
    //由于附魔相关NBT结构为：{ench:[{id:<value> as short,lvl:<value> as short},......]}
    //我们需要对物品的NBT进行访问：
    if(
        !isNull(item.tag)&&//物品有NBT
        !isNull(item.tag.ench)//物品有附魔的NBT
    ){
        var enchList as IData = item.tag.ench;//取出附魔相关NBT数据，这是一个DataList
        var check as bool = false ;//给一个check变量储存检测结果
        if(enchList.length==0) return;//遍历前检测数组长度是否为0，养成好习惯
        //准备遍历
        for i in 0 .. enchList.length{
            if(enchList[i].id.asInt()==70){ //检测到经验修补魔咒
                check = true;//修改标记
                player.sendChat("Mending Found!");//输出调试
                break;//退出循环
            }
        }
        if(!check)return;//如果没有经验修补则直接return
        var newEnchList as IData = 
            enchList.deepUpdate([{"id":70 as short,"lvl":1 as short}],REMOVE)//删去经验修补
                         .deepUpdate([{"id":34 as short,"lvl": 1 as short}],MERGE);//添加耐久
        item.mutable().updateTag({"ench":newEnchList});//更新tag
    }
});
    这回是终于成功了：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第13张图片

    所以，对于NBT的操作，一定要通过搭载对象的update接口进行更新。这也是我一直没提IData和DataMap赋值（注意不是局部变量赋值）操作的原因。

· 补充：in/has关键词对DataMap操作

    在友谊妈的Zentutorial文档中提及了可以使用in/has关键词对DataMap进行操作，而官方文档也说明DataMap可以使用in关键词：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第14张图片
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第15张图片

    但是，这里必须要指出的是，in/has操作符是可以互换的，in的操作逻辑并不遵循英文语法逻辑！所以笔者非常推荐使用has而不是in！

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第16张图片

    这里笔者测试了一下in/has关键词能否对Value进行检测，结果如下：

var testDM as IData = {
    "key1":"value1",
    "key2":"value2"，
    "key3":"value3"
} as IData;
 
 
print(testDM has "key1");   //true
print(testDM has "key4");   //false
print(testDM has "value1");//false
print(testDM has "value4");//false
 
//即使是memberGet()也不行：
print(testDM has testDM.memberGet("key1"));//false
    可以看到，in/has关键词并不能对DataMap的Value进行检测（否则程序会有大问题）。但如果对象是DataMap，则in/has可以对其进行检测（类似于检测集合的“是否包含”），如下例：

var testDM2 as IData = {
    "key1":"value1"
} as IData;
 
print(testDM has testDM2);//true
 
testDM2= {
    "key1":"value1",
    "key2":"value2"
} as IData;
 
print(testDM has testDM2);//true
 
testDM2= {
    "key1":"value1",
    "key2":"value3"
} as IData;
 
print(testDM has testDM2);//false
print(testDM has testDM );//true
· 拓展内容：Zen Utils的IData Deep Update

    既然说到了DataMap的update()方法，这里稍微提一下来自友谊妈的Zen Utils模组了不会有人学魔改学到这里都还不知道/没用过ZU吧（，除了事件热重载reloadableevents这个让你热加载修改后脚本而不用让MC一遍又一遍重开的神级功能外，ZU还添加了一个和DataMap相关的功能：IData Deep Update（IData深度更新）

    先贴相关链接：

        · 官方Wiki（GitHub）：Click Here；

        · 魔改up吕不才对IData Deep Update的视频介绍（BiliBili）：Click Here；

    IData Deep Update是对IData（不限制一定是DataMap）的Update()函数（方法，功能）作了补充完善（因为update()函数只能进行覆写操作）（注意：只是因为这个是对原有Update功能的完善才被叫做Deep Update而不能改变用以Update的两个IData对象的值！），其调用方法为<IData>.deepUpdate(IData dataToBeUpdatedWith,@optional IData updateOperation)，通过deepUpdate我们可以对两个IData对象进行更加精确的Update操作。注意这里的deepUpdate()依旧只是进行运算而无法改变原来IData的值。

    这里介绍以下第二个可选参数更新操作（Update Operation）。友谊妈给了你以下5个可选的操作方法，其分别是：

    (1)OVERWRITE：覆写，默认的操作方法，让待更新的数据覆写被更新的数据；

    (2)APPEND：附加，对于数组（Array）和表单（List）来说，新的IData数据会被添加在原有数据的末尾；而对DataMap则会在保持原有数据的基础上添加新的数据（共有Key中非数组/表单/DataMap的Value依旧会被覆写）；

    (3)MERGE：合并，对于数组和表单来说，新的非重复的IData数据会被添加在原有数据的末尾；对于DataMap操作和上述APPEND相同；

    (4)REMOVE：移除，从数组、表单或DataMap中移除特定的元素。如果用以更新的IData本身为空，则会返回原有的IData中所有元素将会被清空后的IData对象（依旧不会动原有IData数据）；

    (5)BUMP：独立（稍微意译了一下），它是对上述四种操作方法的一个可选标记（使用“|”进行标记），使得操作后的结果中新数据会被独立地放出来，而不是合并成一个列表；

    鉴于此内容属于拓展内容，教程实战部分对该内容要求不高，笔者这里就不贴代码了。感兴趣的各位可以自行去官方wiki上查看样例代码，或跟着up吕不才的视频进行学习。

    （才不是笔者搓到这里搓得老眼昏花思维混沌而教程估计很难用到（个头啊！）所以才懒得去详细写了呢~）


    辛苦各位看完前边的理论操作部分了，接下来欢迎来到实战部分！在这一节笔者会以实战的形式由浅入深逐步介绍NBT操作的思路，并指出操作过程中的注意事项。

    不过实际上NBT的处理都是比较公式化的，这里笔者建议各位根据自己需求创建一个属于自己的NBT工具箱（NBTUtils），把常用的函数啥的都扔进去。

    警告：以下代码严禁商用！

    1.样例1：“噬魂者”附魔与简单NBT的添加与读写

        →这一节主要介绍物品上简单NBT的添加与读写。

    玩过匠魂2的玩家都听说过，匠魂附属模组TAIGA中有一个非常强的特性：噬魂者，它会根据武器的击杀数与击杀敌人的最大生命值为武器提供额外的攻击加成，以西洋剑为例，受制于匠魂的伤害衰减机制，最高可以额外提高约270点左右的攻击力（这是一个非常恐怖的数值，毕竟西洋剑的高攻速以及无视无敌帧的机制使得玩家可以在短时间内对怪物造成毁灭性打击）。我们这里用附魔的方式简单复刻一下这个特性：

    要求：带有魔咒“噬魂者”的武器会依据自身击杀数提高攻击力，每击杀一个敌人提高0.01点伤害，最高不超过50。

    首先我们进行分析：这个附魔一方面要求在击杀生物时统计击杀数，另一方面会在攻击的时候调用该击杀数完成增伤。对于统计的击杀数我们可以用NBT的形式保存在武器上。由此我们需要用两个事件处理这个附魔：一是生物死亡事件，用于添加/修改NBT，二是生物受伤事件，用于增伤（注：原版Cot给出的函数实在是不够用，并且为了方便/ct reload，笔者专门开一个.zs文件来处理两个事件）。

    其次我们先把基础框架搭好：（注册部分不多说）

#loader contenttweaker
#priority 999
 
import mods.contenttweaker.enchantments.EnchantmentBuilder;
import crafttweaker.entity.IEntityEquipmentSlot;
 
val soulEater = EnchantmentBuilder.create("soul_eater");
 
soulEater.allowedOnBooks = true;
soulEater.treasure = true;
soulEater.applicableSlots = [
    IEntityEquipmentSlot.mainHand,
    IEntityEquipmentSlot.offhand
];
soulEater.setTypeWeapon();
soulEater.setRarityVeryRare();
 
soulEater.register();
    zh_cn.lang文件也要完善一下：

1
2
enchantment.contenttweaker.soul_eater=§u噬魂者
enchantment.contenttweaker.soul_eater.desc=根据武器击杀数提高伤害，每击杀1个生物增伤0.01，最高不超过50
    单独开一个.zs文件，命名为EventHandler.zs，内容如下：

#loader reloadableevents
 
import crafttweaker.event.ILivingEvent;
import crafttweaker.event.EntityLivingDeathEvent;
import crafttweaker.event.EntityLivingHurtEvent;
import crafttweaker.entity.IEntityLivingBase;
import crafttweaker.player.IPlayer;
import crafttweaker.data.IData;
import crafttweaker.item.IItemStack;
import crafttweaker.damage.IDamageSource;
 
//由于两个事件中都有共同的判空环节，这里用自定义函数简化代码表达
//注意EntityLivingDeathEvent和EntityLivingHurtEvent均属于ILivingEvent
function getPlayerMainhandItemFromEvent(event as ILivingEvent,dmgsrc as IDamageSource)as IItemStack{
 
    //先把客户端处理、伤害来源为空、伤害真实来源不为玩家的情况排除掉
    if(
        event.entityLivingBase.world.remote||
        isNull(dmgsrc)||
        isNull(dmgsrc.getTrueSource())||
        !(dmgsrc.getTrueSource() instanceof IPlayer)
    ) return null;
     
    var player as IPlayer = dmgsrc.getTrueSource();
    var item as IItemStack = player.currentItem;
     
    //排除掉玩家没拿武器的情况
    if(isNull(item))return null;
     
    //返回玩家主手物品
    return item;
}
 
events.onEntityLivingDeath(function(event as EntityLivingDeathEvent){
    var item as IItemStack = getPlayerMainhandItemFromEvent(event,event.damageSource);
    //函数判空、物品没有附魔则直接return掉，简化代码表达
    if(isNull(item)||!item.isEnchanted)return;
     
    //接下来处理附魔：寻找是否有“噬魂者”这个附魔
    //这里“噬魂者”附魔可以用尖括号调用：<enchantment:contenttweaker:soul_eater>
    //返回的是一个IEnchantmentDefinition对象
    //IEnchantmentDefinition可以使用“==”操作
    var enchCheck as bool = false;
     
    //对物品的附魔列表进行遍历：
    for ench in item.enchantments{
     
        //找到这个附魔则跳出遍历
        if(ench.definition==<enchantment:contenttweaker:soul_eater>){
            enchCheck = true;
            break;
        }
    }
     
    //如果没找到这个附魔直接return掉
    if(!enchCheck)return;
    //上述代码可以写成函数（详见下文优化处理后的代码）
     
    //以下是NBT处理：
    //Code1
});
 
events.onEntityLivingHurt(function(event as EntityLivingHurtEvent){
    //相同结构直接照抄，避免重新撰写函数体内部各种判空结构
    var item as IItemStack = getPlayerMainhandItemFromEvent(event,event.damageSource);
    if(isNull(item))return;
    //Code2
});
    然后是Code1和Code2部分的代码。由于原版MC没有记录有关于“武器击杀死亡数”的NBT，所以这里我们得自己创建一个并自己进行调用：

    对Code1处：

//预想结构：{ "DeathCount" : <intValue> as int,......}
//这是最简单的单层结构
 
//首先我们获取并储存NBT的IData数据：
var nbt as IData = item.tag;
 
//接下来便是要对其进行操作了，操作之前必须判空，避免出现“物品没有NBT的形式”
//我们先完成NBT的修改，对于不存在的情况放在后边处理：
if(isNull(nbt)){
    //Code1.1
}
else{
 
    //有了NBT还不够，我们还需要知道有没有“DeathCount”这个成员（Key）
    //同样，没有的情况放在后边处理
    if(isNull(nbt.DeathCount)){
        //Code1.2
    }
    else{
     
        //有DeathCount这个成员，我们需要读取它的Value
        //创造一个Key为“DeathCount”、Value比原来多1的DataMap将其覆盖掉
        //这里提供两种方法，任选其一即可
        //先新建一个局部变量来储存这个DataMap
        var newDeathCount as IData = null;
         
        //======方法一：直接手写DataMap======//
         
        newDeathCount = {
            "DeathCount" : (nbt.DeathCount + ( 1 as int ) as IData) 
        } as IData;
        //Value处可以写自定义变量，此处使用多个as规范类型转换
         
        //=============================//
         
        //======方法二：用ZU的createEmptyMutableDataMap()静态方法======//
         
        //创建一个可以被改变的DataMap
        newDeathCount = IData.createEmptyMutableDataMap();
         
        //设置成员，参数类型：memberSet(string keyName,IData value);
        //由于调用的是Method，所以这里传入的keyName不加“ "" ”会被解释为局部变量
        newDeathCount.memberSet("DeathCount",(nbt.DeathCount + ( 1 as int ) as IData));
         
        //===============================================//
         
        //然后把这个DataMap给Update到原来物品上（注意mutable()！！！）
        item.mutable().updateTag(newDeathCount);
    }
}
    然后是Code1.1和Code1.2，我们分析一下它出现的原因：一个是没有NBT，一个是NBT缺少“DeathCount”成员，解决方法都是把一个新的、Key为“DeathCount”的DataMap给Update上去，操作如下：

var newDeathCount as IData = null;
//这里使用上述两种方法创建这个DataMap
//======方法一======//
newDeathCount = {"DeathCount": 1 as int};
 
//======方法二======//
newDeatnCount = IData.createEmptyMutableDataMap();
newDeathCount.memberSet("DeathCount",(1 as int) as IData);
 
item.mutable().updateTag(newDeathCount);
    显然上述代码有很多可以优化的地方：首先是附魔检测，可以直接提出来做成一个函数；其次是Code1.1和Code1.2，可以把二者的判定条件写在一起，调用同一套代码；最后是可以新建自定义变量value，赋值NBT.DeathCount+1（如果存在的话）或1（如果不存在NBT/“DeathCount”成员）然后直接写进Value处或memberSet("DeathCount",value)；由此，优化后的击杀挂载NBT的部分代码如下：（为了精简教程代码，笔者将完整代码中前置导包部分省略了，下同）

#loader reloadableevents
 
import ......
 
function getPlayerMainhandItemFromEvent(event as ILivingEvent,dmgsrc as IDamageSource) as IItemStack{
    if(
        event.entityLivingBase.world.remote||
        isNull(dmgsrc)||
        isNull(dmgsrc.getTrueSource())||
        !(dmgsrc.getTrueSource() instanceof IPlayer)
    ) return null;
    var player as IPlayer = dmgsrc.getTrueSource();
    var item as IItemStack = player.currentItem;
    if(isNull(item))return null;
    return item;
}
 
function getItemCertainEnchantment(item as IItemStack,targetEnch as IEnchantmentDefinition ) as IEnchantment{
    if(!item.isEnchanted)return null;
    for ench in item.enchantments{
        if(ench.definition==targetEnch){
            return ench;
        }
    }
    return null;
}
 
function getItemCertainNBT(item as IItemStack, nbtString as string) as IData {
    if(!isNull(item)&&!isNull(nbtString)&&!isNull(item.tag)&&!isNull(item.tag.memberGet(nbtString))) {
        return item.tag.memberGet(nbtString);
    }
    else return null;
}
 
events.onEntityLivingDeath(function(event as EntityLivingDeathEvent){
    var item as IItemStack = getPlayerMainhandItemFromEvent(event,event.damageSource);
    if(isNull(item)||isNull(getItemCertainEnchantment(item,<enchantment:contenttweaker:soul_eater>)))return;
    var value as IData = 1 as IData;
    var deathCountValue as IData = getItemCertainNBT(item,"DeathCount");
    if(!isNull(deathCountValue)) value += deathCountValue;
    item.updateTag({"DeathCount":value});
});
 
//增伤部分代码放在后边详细介绍
    进入游戏测试：（不知道怎么回事，没有显示本地化中文名，倒是描述有了...）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第17张图片
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第18张图片
    击杀生物测试一下：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第19张图片
击杀一只生物后
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第20张图片
击杀三只生物后
    可以看到代码可以正常运行，NBT被修改了。

    然后是增伤部分：（接上文//Code2处代码）

//Crt的答辩min/max函数只支持扔int传int，比起去翻Math库我更喜欢自己写这种简单函数
function minFloat(a as float,b as float) as float{
    if(a<b)return a;
    return b;
}
 
events.onEntityLivingHurt(function(event as EntityLivingHurtEvent){
    var item as IItemStack = getPlayerMainhandItemFromEvent(event,event.damageSource);
    if(
        isNull(item)||
        isNull(getItemCertainEnchantment(item,<enchantment:contenttweaker:soul_eater>))||
        isNull(getItemCertainNBT(item,"DeathCount"))
    )return;
    var deathCountValue as int = getItemCertainNBT(item,"DeathCount").asInt();
    event.amount += minFloat(50.0f,0.01f*deathCountValue);
});
    这里笔者用一把击杀了5只只因的钻石剑攻击测试假人，可以看到伤害增加了0.05f（MmmMmmMmmMmm模组测试假人的显示结果为实际伤害的1/2）：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第21张图片
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第22张图片
    这里提一个必须得注意的问题：自己所添加的NBT最好不要和任何一个可能被游戏自身或其他模组所调用的NBT相重合导致NBT被意外使用、修改（如你不可以用"RepairCost"来作为统计击杀生物数NBT中DataMap的Key）。最简单有效的解决方式是在相关DataMap的Key中加入具有特异性、辨识性的成分（如加入作者名字、相关机制名字等，上述例子中“DeathCount”我可以换成“DeahtCount_byJackStuart”（如果出现非法语法的情况请参照DataMap中介绍“不要作”时相关Key值的处理）），这样可以极大程度上减少重复调用NBT的可能性。由于作者懒得去修改上文中代码和展示图片啥的，此处此例继续使用“DeathCount”作为介绍演示。

    2.样例1：“噬魂者”附魔与动态信息展示Part.1.1：tooltip function

        →这一节主要介绍利用tooltip function完成动态信息展示

    首先得感谢友谊妈的大力支持！友谊妈yyds！！！

    或许已经有读者开始提出疑问了：JSJS，你写了这么多，我们只看到了NBT处理，动态信息展示呢？还有，上边那个“击杀数”并没有显示，我怎么知道我杀了多少生物呢？别急，接下来笔者会开始解决这个问题，介绍如何撰写动态信息展示。

    在MC中我们会碰到这些物品说明：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第23张图片
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第24张图片
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第25张图片
    但只要我们查询NBT就会发现：它们都不存在NBT/NBT与显示内容无关：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第26张图片

    和最开始那把钻石剑不一样，它的这个说明并不是通过NBT中display下Lore标签而是通过tooltip来实现的。这里笔者将会介绍tooltip的用法，以及如何利用tooltip function实现动态信息展示及其利弊，作为动态信息展示的教程第一部分。

    首先还是贴链接：Official / Zentutorial

    说实话，友谊妈的Zentutorial对这一部分介绍非常简单（毕竟是面向新手的教学文档），导致笔者一直以为只能添加静态的tooltip。笔者刚刚接触NBT的操作时曾经想过做一个类似于上述展示击杀数（实际是展示CD）的tooltip，奈何笔者能力不足（当时笔者试图操作第一层NBT已经很勉强了，对于二层还带DataList的Lore标签根本就是无从下手），甚至闹出“每Tick添加一次Lore导致整个屏幕都是Lore”的笑话。现在通过翻阅官方文档，我们可以使用tooltip function来解决这个需求。

    先贴几个静态tooltip的使用案例：

<tconstruct:scythe:*>.addTooltip(format.red(format.italic("该物品的伤害倍率经过作者调整，调整倍率为1.5")));
<item:minecraft:diamond_sword:*>.addShiftTooltip(
    "这是一把钻石剑！",
    format.gray(format.italic("按下shift以显示tooltip"))
);
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第27张图片
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第28张图片
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第29张图片
    很简单的调用，笔者就不多举例赘述了。

    这里额外提一下，只有IIngredient的子类（如IItemStack、IOreDict等）才能调用tooltip相关函数。并且这个tooltip是全局添加的，只要满足给出的IIngredient条件就会被添加这个tooltip。尖括号内物品加上“:*”以匹配所有耐久度（meta值）的物品，否则只会对满耐久度（meta值为0）的添加这个tooltip。

    接下来是tooltip function，用于调用函数计算“应该显示什么样的tooltip”，具体调用方法如下：（格式化代码wiki：Click Here，记得使用后在字符串最后加一个“§r”养成好习惯）

<IIngredient>.addAdvancedTooltip(function(item) {
    //Code
    //需要返回一个string作为显示的tooltip
});
 
<IIngredient>.addShiftTooltip(
    function(item) {//显示按下Shift后显示的tooltip
        //Code
        //需要返回一个string作为按下shift后显示的tooltip
    },
    function(item) {//（可选）显示按下Shift前显示的tooltip
        //Code
        //需要返回一个string作为按下shift前显示的tooltip
    }
);
 
//注：上述item属于IItemStack对象，表示所持有这个tooltip的那个实际的IItemStack
//实际返回要求必须是一个String，需要修饰的话可以使用格式化代码
//对于tooltip不能/ct reload！
    我们利用上述功能对“噬魂者”的击杀显示进行完善，这里以钻石剑为例：

    注：此处笔者使用Crt版本为：CraftTweaker2-1.12-4.1.20.698，低于此版本的Crt可能会在当tooltip function中return值为null的时候，在游戏加载的第七阶段报错闪退！

<item:minecraft:diamond_sword:*>.addAdvancedTooltip(function(item) {
    var deathCountValue = getItemCertainNBT(item,"DeathCount");
    if(isNull(deathCountValue))return null;
    return "§o§a累计击杀生物数量："~deathCountValue.asString()~"§r";
});
    实际效果很理想：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第30张图片
击杀生物前
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第31张图片
击杀生物后
    如果要大批量添加这个tooltip，除了使用数组遍历外，还可以使用矿辞添加，以下是Crt作者给出的官方案例：

<ore:myAxeOreDictionary>.add(
    <minecraft:iron_axe:*>, 
    <minecraft:golden_axe:*>, 
    <minecraft:diamond_axe:*>
);
 
<ore:myAxeOreDictionary>.addAdvancedTooltip(
    function(item) {
        return "Damage: " ~ item.damage ~ " / " ~ item.maxDamage;
    }
);
 
//注意IItemStack和IOreDict也属于IIngredient的子类！
<ore:myAxeOreDictionary>.addShiftTooltip(
    function(item) {
        return "Uses left: " ~ (item.maxDamage - item.damage);
    },
    function(item){
        return "Hold shift for some juicy math.";
    }
);
    这里使用矿辞进行批量添加演示：（感谢友谊妈提供的方法！）

//创建新的矿辞并添加不多说：
<ore:toolShowTag>.add(
    <item:minecraft:wooden_sword:*>,
    <item:minecraft:stone_sword:*>,
    <item:minecraft:golden_sword:*>,
    <item:minecraft:iron_sword:*>
);
 
//这里矿辞添加有多种操作方法：
//其一是直接照抄上文中的tooltip function
//如下代码：
<ore:toolShowTag>.addAdvancedTooltip(function(item) {
    var deathCountValue = getItemCertainNBT(item,"DeathCount");
    if(isNull(deathCountValue))return null;
    return "§o§a累计击杀生物数量："~deathCountValue.asString()~"§r";
});
 
//其二是用Ingredient中only(IItemCodition conditionFunction)方法
//通过IItemCondition类的conditionFunction（需要返回一个bool作为是否通过的条件依据）检测NBT
//其操作难度、对读者水平要求比较高，但也不失为一种方法
//具体格式：<IIngredient>.only(function(<IItemStack>){//Code1}).addAdvancedTooltip{//Code2};
<ore:toolShowTag>
    .only(function(item){    //condition function会自动把“带有这个矿辞的物品”作为参数给你
        return item.hasTag&&(item.tag has "DeathCount");    //需要返回一个bool，true则通过判定（添加tooltip）
    }).addAdvancedTooltip(function(item){    //only()方法返回一个IIngredient对象，之后照常添加即可
        var deathCountValue = getItemCertainNBT(item,"DeathCount");
        return "§o§a累计击杀生物数量："~deathCountValue.asString()~"§r";
    });
     
//二者任选其一即可
    效果如下：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第32张图片
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第33张图片
    之前，笔者在使用较旧版本的Crt时碰到了tooltip function中return值为null而报错时，尝试了一些解决方法。但这种方法需要额外添加新的NBT，增加了数据量，不是一种好的方法。笔者将其也贴在下方仅作参考：

    由于不能使用通配符“ * ”来控制DataMap的匹配（如{"DeathCount":*}是不合语法的），笔者考虑另一种方法：在添加“DeathCount”NBT的同时新增加一个Key为“DeathCountDisplay”、Value固定的NBT，这里暂且设定为“{"DeathCountDisplay":1 as byte}”，然后通过IIngredient类下.onlyWithTag(IData targetDataMap)进行匹配（IItemStack也可以用.withTag(...)进行匹配）代码如下：

//首先是统计击杀数的事件代码，上下相关代码不变
events.onEntityLivingDeath(function(event as EntityLivingDeathEvent){
    var item as IItemStack = getPlayerMainhandItemFromEvent(event,event.damageSource);
    if(isNull(item)||isNull(getItemCertainEnchantment(item,<enchantment:contenttweaker:soul_eater>)))return;
    var value as IData = 1 as IData;
    var deathCountValue as IData = getItemCertainNBT(item,"DeathCount");
    if(!isNull(deathCountValue)) value += deathCountValue;
    item.updateTag({"DeathCount":value,"DeathCountDisplay":1 as byte});//这里额外添加了{"DeathCountDisplay":1 as byte}这个DataMap
});
 
//然后是addAdvancedTooltip代码：
//这里先演示IItemStack
//添加.withTag({"DeathCountDisplay":1 as byte})以精准匹配展示对象
//当然也可以使用.onlyWithTag({"DeathCountDisplay":1 as byte})
<item:minecraft:diamond_sword:*>.withTag({"DeathCountShow":1 as byte}).addAdvancedTooltip(function(item) {
    //由于{"DeathCountShow":1 as byte}和{"DeathCount":<value>}是同时被添加的，可以省去isNull的过程（虽然不是很建议）
    return "§o§a累计击杀生物数量："~getItemCertainNBT(item,"DeathCount").asString()~"§r";
});
 
//然后演示IOreDict
//这里必须使用.onlyWithTag({"DeathCountDisplay":1 as byte})而不能用.withTag(...)
//当然你也可以用.only(...)来做，只是难度更高了
//沿用上文中的<ore:toolShowTag>
<ore:toolShowTag>.onlyWithTag({"DeathCountDisplay":1 as byte}).addAdvancedTooltip(function(item) {
    return "§o§a累计击杀生物数量："~getItemCertainNBT(item,"DeathCount").asString()~"§r";
});


    接下来该谈谈这种添加信息展示方法的利弊了：

    利处当然是编写非常简单。你甚至不需要知道啥是事件，只需要对IItemStack和IIngredient的各种Getter/Setter/Method的运用了熟于心即可。

    但其最大的弊处便是：需要指定添加对象。由于tooltip function需要指定“对特定物品/含有特定矿辞的物品添加”，对于少量工具武器都还好说，毕竟你可以通过枚举等方式解决；但如果我们碰见一个给大量/所有物品都要显示动态tooltip的需求（比如说我在创造强制给木棍附上“噬魂者”并且还要它能够显示击杀生物数），使用tooltip处理显然是不现实的。这里我们可以考虑使用上边展示的修改display下Lore的值的方式解决这个问题（将作为“动态信息展示Part.2:display下Lore标签的修改”在下文“进阶”部分详细介绍），但这种方式难度就会比较高了。

    综上所述，tooltip function是一个相当简单易行的添加物品动态信息方式，尽管受限于其作用原理，它不能对物品进行大批量处理，但已经非常实用、能够达成很理想的效果了。

【附注：笔者对上述问题的解决尝试】

    为了试图解决tooltip指定添加对象的问题，笔者翻阅依旧是由友谊妈编写的ProbeZS模组（一个可以以ZenScript语言形式导出所有Crt相关接口、函数的模组，非常实用，GitHub链接：Click Here）导出的关于IItemStack模组时发现了一个函数：（P.S.笔者不知道这个函数是否是原版的，或是由哪个模组提供的qwq。笔者安装的Crt拓展模组有：ZU、CTIG、RT、CTUtils，估计ZU和RT其一提供的可能性比较大）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第34张图片
    笔者用上述函数将tooltip function改写如下：

IItemStack.fromData({"DeathCountShow":1 as byte}).addAdvancedTooltip(function(item) {
    return "§o§a累计击杀生物数量："~getItemCertainNBT(item,"DeathCount").asString()~"§r";
});
    打开游戏时弹出空指针报错，而/ct syntax未报语法错误：
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第35张图片

--------（2024/3/31更新）--------

    后边友谊妈提出用通配符“<*>”代替所有IIngredient的物品配合only筛选出任意有某一附魔的物品加tooltip，为此笔者作出了以下尝试：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第36张图片

    1.直接对<*>使用addAdvancedTooltip并在内部函数里边筛选NBT添加显示：

<*>.addAdvancedTooltip(function(item) {
    var deathCountValue = getItemCertainNBT(item,"DeathCount");
    if(isNull(deathCountValue))return null;
    return "§o§a累计击杀生物数量："~deathCountValue.asString()~"§r";
});
    空指针报错，但/ct syntax并未报错
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第37张图片

    2.对<*>使用.onlyWithTag()配合额外显示Tag筛选：

<*>.onlyWithTag({"DeathCountDisplay":1 as byte}).addAdvancedTooltip(function(item) {
    return "§o§a累计击杀生物数量："~getItemCertainNBT(item,"DeathCount").asString()~"§r";
});
    进游戏并没有报错，但并没有按照代码所示添加tooltip，/ct syntax也并没有报错

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第38张图片

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第39张图片

    3.对<*>使用.only()筛选NBT：

<*>
    .only(function(item){
        return item.hasTag&&(item.tag has "DeathCount");
    }).addAdvancedTooltip(function(item){
        var deathCountValue = getItemCertainNBT(item,"DeathCount");
        return "§o§a累计击杀生物数量："~deathCountValue.asString()~"§r";
    });
    结果同上。

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第40张图片

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第41张图片

    4.对<*>使用.only()筛选附魔：

<*>
    .only(function(item){
        return !isNull(getItemCertainEnchantment(item,<enchantment:contenttweaker:soul_eater>));
    }).addAdvancedTooltip(function(item){
        var deathCountValue = getItemCertainNBT(item,"DeathCount");
        if(isNull(deathCountValue))return null;
        return "§o§a累计击杀生物数量："~deathCountValue.asString()~"§r";
    });
    结果......同上............（笔者哭了，/捂脸）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第42张图片

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第43张图片

    至此，这种方法正式宣告失败（捂脸）

    3.样例2：“神弑者之怒”与以NBT形式挂载的内置CD处理

        →这一节主要以NBT形式挂载在玩家身上的内置CD的处理

    JS你又在夹带私货了......

    （实际上是对同时Neta以下元素和JS的某个好基友的特性的复刻）

    总所周知，隔壁有一款同为“沙盒游戏”分类的、与MC齐名的游戏——泰拉瑞亚（Terraria，简称Tr）。而在Tr的知名度最高的模组“灾厄”（Calamity）中有这样一位boss：一条自诩为“神”、并以“神”为食物的巨大蠕虫——神明吞噬者（The Devourer of Gods，简称神吞，英文区则简称DoG）。由于其话痨属性、初见的高难度以及DM出色的配乐一度成为灾厄最受欢迎的boss之一。（现在已经沦为老玩家们的玩具、欧米伽触手的玩物、各种后期武器的实验对象了......）

（P.S.灾厄模组同样也是各种玩家整合包自定义元素的来源之一，比如说某个深度魔改的科技向匠魂整合包，里边大量的元素、贴图都来源于该模组......当然，本例也不例外）

    在玩家与该boss战的最后一阶段，神吞会在20%左右血以下放弃释放被称为“激光墙”的弹幕方式，召唤三只小弟并以肉身疯狂冲向玩家，同时发出讯息“神！不惧死亡！”（A GOD DOES NOT FEAR DEATH!!!），如下图：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第44张图片
虽然这一战笔者还是寄了

    说实话，笔者很喜欢这个元素，所以打算在MC中用匠魂（TiC2）Neta一下，来看一下需求吧：



    要求：制作特性“神弑者之怒”，该特性会在玩家濒死的时候以扣除工具最大耐久值的40%（最低不低于500点）为代价，像不死图腾一样免除死亡，点燃周围实体，迸发粒子效果，发出不死图腾的音效，并让玩家获得持续10s的“神弑者之怒”buff，该buff会使玩家获得抗火、力量V、跳跃提升III和速度Vbuff，并免疫一切非虚空伤害。该触发有10min的CD。

    首先先把匠魂特性创建好，这里直接给代码，不多赘述了：


#loader contenttweaker
#modloaded tconstruct
 
import mods.contenttweaker.tconstruct.TraitBuilder;
 
var rageofgodslayer = TraitBuilder.create("rage_of_god_slayer");
rageofgodslayer.color = 0xDA42F6;
rageofgodslayer.localizedName = "神弑者之怒";
rageofgodslayer.localizedDescription ="§oA GOD DOES NOT FEAR DEATH!!!\n§r在危急时刻某位自诩为神明的强大生物的怒火将给予你强大的力量";
rageofgodslayer.addItem(<item:minecraft:skull:*>);    //这里我用头颅当强化材料
rageofgodslayer.register();
    然后是用RT模组自定义药水：（教程见这里：传送门）（笔者只负责做效果，贴图笔者就不做了）（没有美术细胞的家伙←__←）到头来还是做了

#loader contenttweaker
#priority 100
import mods.contenttweaker.VanillaFactory;
import mods.randomtweaker.cote.IPotion;
import crafttweaker.player.IPlayer;
 
var rageofgodslayer as IPotion = VanillaFactory.createPotion("rage_of_god_slayer", 0xDA42F6);
rageofgodslayer.badEffectIn = false;
rageofgodslayer.beneficial = true;
rageofgodslayer.instant = false;
rageofgodslayer.isReady =function(duration, amplifier) {
    if (duration % 20 == 0) {
        return true;
    }
    return false;
};
rageofgodslayer.performEffect = function(living, amplifier) {
    if(!living.world.remote&&living instanceof IPlayer){
        var player as IPlayer = living;
        player.addPotionEffect(<potion:minecraft:strength>.makePotionEffect(21,4));
        player.addPotionEffect(<potion:minecraft:jump_boost>.makePotionEffect(21,2));
        player.addPotionEffect(<potion:minecraft:speed>.makePotionEffect(21,2));
        player.addPotionEffect(<potion:minecraft:fire_resistance>.makePotionEffect(21,0));
    }
};
rageofgodslayer.shouldRender = true;
rageofgodslayer.shouldRenderHUD = true;
rageofgodslayer.register();
    本地化Key做一下：

1
effect.contenttweaker.rage_of_god_slayer=神弑者之怒
    当然，这个药水效果并不完整，毕竟添加药水效果达到了，免伤效果还没达到，我们需要从外部借助事件（EntityLivingHurtEvent）完成这个效果。免伤事件部分也不多说了，直接放代码：

events.onEntityLivingHurt(function(event as EntityLivingHurtEvent){
    if(
        event.entityLivingBase.world.remote||
        isNull(event.entityLivingBase.getActivePotionEffect(<potion:contenttweaker:rage_of_god_slayer>))||
        isNull(event.damageSource)||
        event.damageSource.getDamageType()=="outOfWorld"
    ) return;  
    player.sendChat("Damage Canceled!");
    event.amount =0.0f;
    event.cancel();
});
    药水做好了，特性基础做好了，接下来便是把特性内容给做出来。由于匠魂的各个事件函数并不能处理生物死亡事件，这里同样需要借助外部事件（EntityLivingDeathEvent）来完成效果。

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第45张图片

    先捋一捋思路：我们需要在玩家死亡的时候检测玩家主/副手物品是否含有该特性，为此一方面我们需要对玩家手上的工具进行NBT检测检查出该特性（匠魂的特性也是以NBT形式储存的），另一方面我们还需要检测玩家身上的触发CD。触发CD的问题我们后边再说，先看匠魂工具的特性检测：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第46张图片

    把事件基础搭好：

events.onEntityLivingDeath(function(event as EntityLivingDeathEvent){
    if(
        event.entityLivingBase.world.remote||
        !(event.entityLivingBase instanceof IPlayer)
    )return;
    var player as IPlayer = event.entityLivingBase;
    if(
        !isNull(event.entityLivingBase.getActivePotionEffect(<potion:contenttweaker:rage_of_god_slayer>))&&
        event.damageSource.getDamageType()!="outOfWorld"
    ){  //有神弑者之怒，取消所有非虚空死亡事件
        event.cancel();
        player.health+=0.5f;//注意这里得给玩家一些基础血量，不然玩家依旧会死亡
    }
    else{   //没有该药水效果，则开始检测特性
        //Code
    }
});
    对于特性检测，一个比较简单、基础的办法是直接将Key为“Trait”的Value部分取出来转化成String（如上文中转化结果为：["aurorianempowered","umbra","established","crystalizing","yearning_towards_night","strength","sakura_blessing","duritos","toolleveling","rage_of_god_slayer"]），然后检测其中是否有关键词“ "rage_of_god_slayer" ”。当然由于这个Value属于DataList，你也可以使用遍历的操作检测（该操作将会在下文进阶篇详述）。鉴于此处属于NBT操作的入门部分，笔者选择使用该基础方法：

    这里特性检测可以直接做成一个函数（然后放入你的工具包中），这里笔者再次使用了案例一中编写的getItemCertainNBT这个函数：

function getItemCertainNBT(item as IItemStack, nbtString as string) as IData {
    if(!isNull(item)&&!isNull(nbtString)&&!isNull(item.tag)&&!isNull(item.tag.memberGet(nbtString))) {
        return item.tag.memberGet(nbtString);
    }
    else return null;
}
 
function itemHasCertainTrait(item as IItemStack, traitName as string) as bool{
    if(!isNull(item)&&!isNull(traitName)&&!isNull(getItemCertainNBT(item,"Traits"))){
        if(getItemCertainNBT(item,"Traits").asString().contains("\""~traitName~"\"")) return true;
        //注意这里对“ " ”字符检测需要加转义符“ \ ”
    }
    return false;
}
    然后在//Code部分继续编写代码：

var items as IItemStack[] = [
    player.currentItem,
    player.getInventoryStack(40)
];//副手物品栏编号为40，所以player.getInventoryStack(40)是获取副手物品
for i in 0 .. items.length{
    if(
        itemHasCertainTrait(items[i],"rage_of_god_slayer")&&
        //这里笔者把cd检测做成一个函数先放在这里
        //等会儿要完善这个函数
        //四个参数分别为：IPlayer player，string cdNBTName，int cdTime，bool isPersisted
        //Crt没有自定义函数重载功能真让人难受......
        cooldownCheck(player,"rage_of_god_slayer_cooldown",12000,false)
    ){
        items[i].mutable().attemptDamageItem(max(500,(0.4f*player.currentItem.maxDamage) as int));
        //这里就是写各种效果了，笔者使用自定义函数effectConduct(IPlayer player)来做这个效果
        effectConduct(player);
        player.health = 0.5f;
        event.cancel();
        return;
    }
}
    效果部分单独拿出来写：

function effectConduct(player as IPlayer) as void{
    //先给玩家添加这个药水效果
    player.addPotionEffect(<potion:contenttweaker:rage_of_god_slayer>.makePotionEffect(200,0));
    //然后是点燃16*16*16的实体点燃效果，这里笔者额外做了一个伤害效果
    var Xt = player.getX() as double;
    var Yt = player.getY() as double;
    var Zt = player.getZ() as double;
    var areaStart as Position3f = Position3f.create( ( Xt + 8.0d ) , ( Yt + 8.0d ) , ( Zt + 8.0d ) );
    var areaEnd as Position3f = Position3f.create( ( Xt - 7.0d ) , ( Yt - 7.0d ) , ( Zt - 7.0d ) );
    for entity in player.world.getEntitiesInArea( areaStart , areaEnd ){//注意这个函数获取的是IEntity[]
        if(entity instanceof IEntityLivingBase){
            var entityNear as IEntityLivingBase = entity;
            entityNear.setFire(10);
            entityNear.attackEntityFrom(IDamageSource.createIndirectDamage("God Slayer Inferno",player,player),10.0f);
        }
    }
    //这里是音效和粒子效果，具体实现参照下方effectCommandForOthers函数
    for otherPlayer in player.world.getAllPlayers(){
        if(otherPlayer.world.dimension!=player.world.dimension) continue;//非同一维度就没必要发效果了
        effectCommandForOthers(player,otherPlayer);
    }
}
 
//音效与粒子效果，笔者用原版的命令执行方式做的
function effectCommandForOthers(player as IPlayer,otherPlayer as IPlayer){
    //音效
    server.commandManager.executeCommandSilent(
        server,
        "playsound item.totem.use master "~
        otherPlayer.name~" "~
        (player.x as string)~" "~(player.y as string)~" "~(player.z as string)~
        " 25 "~(1.0d+player.world.random.nextDouble()/2.0d) as string~" 0"
    );
    //两个粒子效果，一个是大量冒出怒火
    server.commandManager.executeCommandSilent(
        server,
        "particle angryVillager "~
        (player.x as string)~" "~(player.y as string)~" "~(player.z as string)~
        " 2 2 2 0.3 50 force "~otherPlayer.name
    );
    //一个是大量火焰飞溅
    server.commandManager.executeCommandSilent(
        server,
        "particle flame "~
        (player.x as string)~" "~(player.y as string)~" "~(player.z as string)~
        " 2 2 2 2 1000 force "~otherPlayer.name
    );
}
    这里笔者测试效果，效果听不错的：（右边输出的Damage Type是笔者启用的另一个测试脚本）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第47张图片

    问题也随之而来了：一是没内置CD（等下文解决），二是武器坏了都还能触发，这是否有点imba......（某个名为“交互”的特性点了个认同）

    内置CD的问题先放一边，武器坏了肯定不能触发嘛，所以这里是特性检测函数出了问题：不能只是“有某个特性”，需要同时检测“工具是否损坏”。

    /ct nbt了一下发现：损坏的在“Stats”下有一个“Broken”的Key，Value为1，将其修好后这个值变成了0：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第48张图片
损坏的工具

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第49张图片
修好的工具

    对比从未损坏的武器则会发现，它在“Stats”下并没有这个Key：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第50张图片
从未损坏的工具

   所以这里我们得对上边的itemHasCertainTrait函数进行改进，这里笔者更倾向于重新写一个itemCertainTraitActive函数：

function itemCertainTraitActive(item as IItemStack, traitName as string) as bool{
    if(
        itemHasCertainTrait(item,traitName)&&//首先得必须要有这个Trait
        !isNull(getItemCertainNBT(item,"Stats"))&&//然后需要有Stats这个Key
        //要么就是完好无损，没有“Broken”这个Key
        (isNull(getItemCertainNBT(item,"Stats").memberGet("Broken"))||
        //要么就得是修复过的，“Broken”的Value为
        (
            !isNull(getItemCertainNBT(item,"Stats").memberGet("Broken"))&&
            getItemCertainNBT(item,"Stats").Broken.asInt()==0)
        )
    )return true;
    return false;
}
    然后是玩家身上内置CD的挂载，也就是cooldownCheck函数的实现

    这里笔者预设了四个参数：对应玩家（IPlayer player），CD对应NBT的名字（Key）（string cdNBTName），CD时间（单位为tick，0.05s）（int cdTickTime）是否死亡保存该NBT数据（bool isPersisted）

    这里谈谈实现形式。最简单、最容易想到的方式是直接加一个int类型的cd，玩家更新的每tick都调用一次，每次减少1的值，直至为零。IPotionEffect就是用这种方法做的：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第51张图片

    还有另一个实现形式：由于游戏本身就有游玩时间计时，它本身就是一个全局的计时器。我可以选择挂载NBT的时候给Value赋值为目前世界存在时间（注意不是游戏内昼夜循环时间！）+cd时长作为静态的NBT，然后再在检测的时候检查目前时间是否大于等于该游戏时间，Moar Tcon模组的特性Afterimage就是这种方式做的：（注：(24/7/21日更新) 在模组最新版本V12中此特性代码被修改了，不过其机制原理仍然不变）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第52张图片

    第一种方式的好处在于直观、易想，但会造成大量计算（分布在每一tick中）。尽管根据某个大佬的说法，这种量级计算对MC运行时的影响比较小。此外还需要额外开一个PlayerTickEvent事件进行处理，如以下代码：

//这里笔者预设函数updatePlayerIntNBT没有考虑保存数据的情况
//所以保存数据就不做了
function updatePlayerIntNBT(player as IPlayer,nbtString as string,intValue as int){
    var updateNBT as IData = IData.createEmptyMutableDataMap();
    updateNBT.memberSet(nbtString,intValue);
    player.update(updateNBT);
}
 
events.onPlayerTick(function(event as PlayerTickEvent){
    //注意这里，对于任何一个ITickEvent，每个Tick都会触发总计四次
    //每Tick开始（event.phase为“START”）和结束（event.phase为“END”）都会调用一次
    //服务端（!world.remote）和客户端（world.remote）都会计算一次
    //这里将运算只放在服务端，每Tick开始执行
    if(event.player.world.remote||event.phase=="END")return;
    //由于玩家一定会有NBT（比如说储存玩家背包物品啥的）
    if(!isNull(event.player.data.memberGet("rage_of_god_slayer_cooldown"))){
        updatePlayerIntNBT(
            event.player,
            "rage_of_god_slayer_cooldown",
            event.player.data.RageOfStaryCoolDown.asInt() - 1
        );
        //时间为0记得移除该NBT
        if(event.player.data.RageOfStaryCoolDown.asInt()<=0){
            player.removeTag("rage_of_god_slayer_cooldown");
        }
    }
});
 
//这里甚至只需要检测是否有“rage_of_god_slayer_cooldown”即可
//甚至只需要传一个player参数
function cooldownCheck(player as IPlayer , cdNBTName as string , cdTick as int , isPersisted as bool) as bool{
    return isNull(player.data.memberGet(cdNBTName));
}
 
//不过在effectConduct()里边必须加一行代码：
player.update({"rage_of_god_slayer_cooldown":12000});
    第一种方法不是我们今天的重点，对于稍微会NBT读写调用的和事件处理的读者都可以做到。第二种方法则不需要额外调用playerTickEvent，只需要做一个函数即可，且计算量较小。但其缺点在于需要挂在Value为long类型的NBT，储存数据量相对较大（虽然也大不到哪里去（）。我们这里着重介绍一下第二种方法：

function cooldownCheck(player as IPlayer , cdNBTName as string , cdTick as int , isPersisted as bool) as bool{
    //函数作用：检测是否没在CD中，是则返回true并添加CD，否则返回false
 
    //先把要update的NBT给存好，下文要用
    //由于Key只会把未带“ "" ”的关键词解释为字符串而不是局部变量，这里使用2.1中样例1的方法二来做NBT
    var updateNBT as IData = IData.createEmptyMutableDataMap();
    updateNBT.memberSet(cdNBTName,(cdTick as long+player.world.getWorldTime()) as long);
    //做出来的NBT输出为：{<cdNBTName>:(cdTick+worldTime) as long}
     
    if(isPersisted){//死亡保存数据，具体做法类似下文死亡不保存，此处就不介绍了
        //这里再次使用了ZU的方法，做出来的NBT为：
        //{"PlayerPersisted":{<cdNBTName>:(cdTick+worldTime) as long}}
        var updatePersistedNBT as IData = IData.createEmptyMutableDataMap();
        updatePersistedNBT.memberSet("PlayerPersisted",updateNBT);
        if(isNull(player.data.PlayerPersisted)||isNull(player.data.PlayerPersisted.memberGet(cdNBTName))){
            player.update(updatePersistedNBT);
            return true;
        }
        else{
            if(player.data.PlayerPersisted.memberGet(cdNBTName).asLong()<=player.world.getWorldTime()){
                    player.update(updatePersistedNBT);
                    return true;
                }
                else return false;
        }
    }
    else{//死亡清空数据
        //是否没这个NBT，是则添加这个NBT并返回true
        if(isNull(player.data.memberGet(cdNBTName))){
            player.update(updateNBT);
            return true;
        }
        //否的话检查时间
        else{
            //如果玩家cd时间小于等于触发世界时间，cd到了，更新CD并返回true
            if(player.data.memberGet(cdNBTName).asLong()<=player.world.getWorldTime()){
                player.update(updateNBT);
                return true;
            }
            //否则返回false
            else return false;
        }
    }
}
   这里需要注意一下，在IWorld、IWorldInfo和IWorldProvider中都有一个获取世界时间的Method，其区别如下：

//获取世界总游玩时间
player.sendChat("<IWorld>.getWorldTime():"~player.world.getWorldTime());
player.sendChat("<IWorldInfo>.getWorldTotalTime():"~player.world.worldInfo.getWorldTotalTime());
//获取世界昼夜更替时间
player.sendChat("<IWorldProvider>.getWorldTotalTime():"~player.world.provider.getWorldTime());
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第53张图片

    最后放一下完整代码：（导包省略）

//#loader reloadableevents
 
import ......
 
function getItemCertainNBT(item as IItemStack, nbtString as string) as IData {
    if(!isNull(item)&&!isNull(nbtString)&&!isNull(item.tag)&&!isNull(item.tag.memberGet(nbtString))) {
        return item.tag.memberGet(nbtString);
    }
    else return null;
}
 
function itemHasCertainTrait(item as IItemStack, traitName as string) as bool{
    if(!isNull(item)&&!isNull(traitName)&&!isNull(getItemCertainNBT(item,"Traits"))){
        if(getItemCertainNBT(item,"Traits").asString().contains("\""~traitName~"\"")) return true;
    }
    return false;
}
 
function itemCertainTraitActive(item as IItemStack, traitName as string) as bool{
    if(
        itemHasCertainTrait(item,traitName)&&
        !isNull(getItemCertainNBT(item,"Stats"))&&
        //完好无损，没有“Broken”
        (isNull(getItemCertainNBT(item,"Stats").memberGet("Broken"))||
        //修复过的，Value为0
        (
            !isNull(getItemCertainNBT(item,"Stats").memberGet("Broken"))&&
            getItemCertainNBT(item,"Stats").Broken.asInt()==0)
        )
    )return true;
    return false;
}
 
function cooldownCheck(player as IPlayer , cdNBTName as string , cdTick as int , isPersisted as bool) as bool{
    var updateNBT as IData = IData.createEmptyMutableDataMap();
    updateNBT.memberSet(cdNBTName,(cdTick as long+player.world.getWorldTime()) as long);
    
    //函数作用：检测是否没在CD中，是则返回true，否则返回false
    
    if(isPersisted){//死亡保存数据
        var updatePersistedNBT as IData = IData.createEmptyMutableDataMap();
        updatePersistedNBT.memberSet("persisted",updateNBT);
        if(isNull(player.data.persisted)||isNull(player.data.persisted.memberGet(cdNBTName))){
            player.update(updatePersistedNBT);
            return true;
        }
        else{
            if(player.data.persisted.memberGet(cdNBTName).asLong()<=player.world.getWorldTime()){
                    player.update(updatePersistedNBT);
                    return true;
                }
                else return false;
        }
    }
    else{//死亡清空数据
        //是否没这个NBT，是则添加CD并返回true
        if(isNull(player.data.memberGet(cdNBTName))){
            player.update(updateNBT);
            return true;
        }
        //否的话检查时间
        else{
            if(player.data.memberGet(cdNBTName).asLong()<=player.world.getWorldTime()){
                player.update(updateNBT);
                return true;
            }
            else return false;
        }
    }
}
 
 
events.onEntityLivingHurt(function(event as EntityLivingHurtEvent){
    if(
        event.entityLivingBase.world.remote||
        isNull(event.entityLivingBase.getActivePotionEffect(<potion:contenttweaker:rage_of_god_slayer>))||
        event.damageSource.getDamageType()=="outOfWorld"
    ) return;
    event.amount =0.0f;
    event.cancel();
});
 
events.onEntityLivingDeath(function(event as EntityLivingDeathEvent){
    if(
        event.entityLivingBase.world.remote||
        !(event.entityLivingBase instanceof IPlayer)
    )return;
    var player as IPlayer = event.entityLivingBase;
    player.sendChat("Damage Type:"~event.damageSource.getDamageType());
    if(
        !isNull(player.getActivePotionEffect(<potion:contenttweaker:rage_of_god_slayer>))&&
        event.damageSource.getDamageType()!="outOfWorld"
    ){  //有神弑者之怒，取消所有非虚空死亡事件
        event.cancel();
        player.health =0.5f;//注意这里得给玩家一些基础血量，不然玩家依旧会死亡
    }
    else{   //没有该药水效果，则开始检测特性（能对outOfWorld伤害进行一次免疫）
        
        var items as IItemStack[] = [
            player.currentItem,
            player.getInventoryStack(40)
        ];
        for i in 0 .. items.length{
            if(itemCertainTraitActive(items[i],"rage_of_god_slayer")&&cooldownCheck(player,"rage_of_god_slayer_cooldown",12000,false)){
                items[i].mutable().attemptDamageItem(max(500,(0.4f*player.currentItem.maxDamage) as int));
                effectConduct(player);
                player.health = 0.5f;
                event.cancel();
                return;
            }
        }
    }  
});
 
function effectConduct(player as IPlayer) as void{
    player.sendChat("§d§lA GOD DOES NOT FEAR DEATH!!!");
    player.addPotionEffect(<potion:contenttweaker:rage_of_god_slayer>.makePotionEffect(200,0));
    var Xt = player.getX() as double;
    var Yt = player.getY() as double;
    var Zt = player.getZ() as double;
    var areaStart as Position3f = Position3f.create( ( Xt + 8.0d ) , ( Yt + 8.0d ) , ( Zt + 8.0d ) );
    var areaEnd as Position3f = Position3f.create( ( Xt - 7.0d ) , ( Yt - 7.0d ) , ( Zt - 7.0d ) );
    for entity in player.world.getEntitiesInArea( areaStart , areaEnd ){
        if(entity instanceof IEntityLivingBase){
            var entityNear as IEntityLivingBase = entity;
            entityNear.setFire(10);
            entityNear.attackEntityFrom(IDamageSource.createIndirectDamage("God Slayer Inferno",player,player),10.0f);
        }
    }
    for otherPlayer in player.world.getAllPlayers(){
        if(otherPlayer.world.dimension!=player.world.dimension) continue;
        effectCommandForOthers(player,otherPlayer);
    }
}
 
function effectCommandForOthers(player as IPlayer,otherPlayer as IPlayer){
    //音效
    server.commandManager.executeCommandSilent(
        server,
        "playsound item.totem.use master "~
        otherPlayer.name~" "~
        (player.x as string)~" "~(player.y as string)~" "~(player.z as string)~
        " 25 "~(1.0d+player.world.random.nextDouble()/2.0d) as string~" 0"
    );
    //两个粒子效果，一个是大量冒出怒火
    server.commandManager.executeCommandSilent(
        server,
        "particle angryVillager "~
        (player.x as string)~" "~(player.y as string)~" "~(player.z as string)~
        " 2 2 2 0.3 50 force "~otherPlayer.name
    );
    //一个是大量火焰飞溅
    server.commandManager.executeCommandSilent(
        server,
        "particle flame "~
        (player.x as string)~" "~(player.y as string)~" "~(player.z as string)~
        " 2 2 2 2 1000 force "~otherPlayer.name
    );
}
    （如果有bug请及时和笔者反馈！） 

    4.样例2：“神弑者之怒”与动态信息展示Part.1.2：tooltip function与服务端对客户端通信

        →这一节主要介绍动态信息展示中tooltip function与服务端对客户端间的通信处理

    “神弑者之怒”本质上是一个有CD限制的不死图腾向的特性。既然有CD，那不做一下动态信息展示是否有点不够意思？[doge]

    由于这里做的是特性，本质上对所有匠魂工具生效，因此可以用tooltip function来做这个效果。这里笔者先提一点关于服务端、客户端的进阶知识：（注意：这里只是笔者的个人理解，可能会有误！如有错误欢迎指出！）

    这里贴一个面向Modder的、关于“服务端”与“客户端”的介绍（非魔改内容警告！）：Click Here

    （以Forge为例）对于以Forge为ModLoader的Minecraft游戏，其本质上分为：Server（服务端）和Client（客户端）。服务端负责运行游戏底层代码，处理游戏各种请求，并与客户端通信；客户端主要负责游戏渲染，处理来自服务端的信息，并对服务端发包“告知”玩家行为等。一般来说客户端可以有多个，但连接的服务端只能有一个（特殊情况除外）。我们在进行联机的时候，本质上就是用我们电脑上的客户端连接到目标服务器的服务端；而如果是好友间用公网IP联机/内网穿透联机，则是被联机的一方同时提供服务端和客户端；甚至单人游戏的情况下，服务端和客户端也依旧同时存在，比如说我们退出存档时会有一个“关闭内置服务端”的信息说明：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第54张图片

    那知道服务端和客户端有什么用呢？因为你需要用到这些知识（笑）。这里简单介绍一下：某些知名的客户端模组，如JEI、旅行地图、Apple Skin等（高版本也有Tweakeroo、Litematica（投影）等辅助模组）只需要在客户端安装即可有效运行，甚至各种改Gamma值、光影、资源包（包括被各大腐竹痛恨的某矿透材质包）从功能上也可被视为“客户端模组”（尽管本质上它们并不属于模组）。究其原因，则是服务端和客户端的数据不完全对等，并且其相互之间并不知道对方都在干啥以及干了啥导致的（除非你使用相关通信）。客户端模组主要处理各种存在于“本地”而不是“云端”（对于客户端而言）的信息（尽管这些信息可以被服务端篡改从而导致信息不对称，笔者还是萌新的时候曾在某个服里使用过某矿透材质包，但由于防矿透插件的存在，被渲染的珍贵矿石的量要远少于实际存在的量（各位请千万不要这样做！！！）），不涉及游戏底层逻辑代码的运行而是给予游玩的玩家更好的体验，因此没必要把这些模组/材质包扔给服务端运行（部分模组服务端运行也没用）。但对于大部分模组，由于其涉及了游戏运行逻辑的修改而需要放在服务端运行，这也是为什么我们写各种.zs脚本都需要放在服务端运行的原因（客户端运行这些脚本不会反馈在服务端上，因为一方面服务端“不接受”这些来自客户端的运行结果，另一方面事件都用了world.remote判断）。

    让我们换个我们熟悉的例子：事件中的world.remote。在刚刚接触Crt的时候我们不止一次被嘱咐“写事件的时候要优先判断if(!world.remote){}然后再写内容”，“这是代码规范”，现在来看则是因为当world.remote为真时判断的是“客户端运行”，为假时判断的时“服务端运行”，各种事件的修改都涉及了游戏运行逻辑的修改，因此客户端处理无效，没必要交给客户端运行（笔者不慎有次在if(world.remote)return;的判定条件前加了“!”，导致事件运行的attackEntityFrom(......)一直无效，甚至让笔者一度怀疑这个Method的可行性）（当然更重要的原因是对于服务端而言所有来自客户端的运行结果都是“不可信”的，好比银行服务器不会把处理金钱账户请求的运行放在客户端，不然很容易被黑客劫持修改运行结果）（某度网盘早期就是把限速运行放在了客户端执行，从而导致CE修改器能够靠游戏加速功能“加速”网盘下载）（现在已经被修复了）。

    回到正题：由于我们所有的事件都执行了“if(world.remote)return;”，所有修改都不会反馈在客户端，因此即使是我们触发了神弑者之怒，也只是服务端更新了玩家的NBT，但客户端并没有更新，尽管这并不影响效果的正常触发。

    这个说法可能过于抽象了，我们这里举个例子，给“神弑者之怒”做一个CD展示：

//先写一个矿辞把所有匠魂工具都给包含进去
<ore:toolTConstruct>.add(
    <item:tconstruct:pickaxe:*>,
    <item:tconstruct:shovel:*>,
    <item:tconstruct:hatchet:*>,
    <item:tconstruct:mattock:*>,
    <item:tconstruct:kama:*>,
    <item:tconstruct:hammer:*>,
    <item:tconstruct:excavator:*>,
    <item:tconstruct:lumberaxe:*>,
    <item:tconstruct:scythe:*>,
    <item:tconstruct:broadsword:*>,
    <item:tconstruct:longsword:*>,
    <item:tconstruct:rapier:*>,
    <item:tconstruct:frypan:*>,
    <item:tconstruct:battlesign:*>,
    <item:tconstruct:cleaver:*>,
    <item:tconstruct:shortbow:*>,
    <item:tconstruct:longbow:*>,
    <item:tconstruct:arrow:*>,
    <item:tconstruct:crossbow:*>,
    <item:tconstruct:bolt:*>,
    <item:tconstruct:shuriken:*>,
    <item:tconstruct:bolt_core:*>
);
 
//然后再用only()方法筛选出具有“rage_of_god_slayer”这个特性的工具并添加tooltip function
//当然这里也可以在tooltip function里边检测是否有这个特性
//itemHasCertainTrait()函数原函数体见上
 
//tooltip是一个只在客户端调用的函数，其作用只是用来渲染tooltip
//服务端调用并不能处理什么东西
//所以我们只需要考虑客户端处理的问题就行了
//下边使用了client关键词表示“调用该函数的客户端”
//这样就能精确定位“是哪位玩家调用的客户端”需要处理该函数
//从而避免获取全局玩家然后一个一个找啥的
<ore:toolTConstruct>
    .only(function(item){
        return itemHasCertainTrait(item,"rage_of_god_slayer");
    }).addAdvancedTooltip(function(item){
        if(!isNull(client.player.data.rage_of_god_slayer_cooldown)){
            if(client.player.world.getWorldTime()>=client.player.data.rage_of_god_slayer_cooldown){
                return"§b神弑者之怒已就绪！";
            }
            else{
                return"§d神弑者之怒CD:"~showTime(
                    client.player.data.rage_of_god_slayer_cooldown - client.player.world.getWorldTime()
                );
            }
        }
        else return "§b神弑者之怒已就绪！";
    });
     
//然后是关于时间：
//这里笔者自己搓了个函数用于接受long类型的时间并转化为字符串：
//long和int不能混用，即使是int转long也没有这个隐式转换......
//笔者因为这个破防了（上一次破防还是要double扔float结果报错）
function showTime(timeTick as long) as string{
    if(timeTick==0 as long)return "";
    var hour as long = timeTick/(72000 as long);
    var min as long = ((timeTick - hour*(72000 as long))/(1200 as long)) as long;
    var sec as long = (timeTick - hour*(72000 as long) - min*(1200 as long))/(20 as long) as long;
    var tick as long = (timeTick - hour*(72000 as long) - min*(1200 as long) - sec*(20 as long)) as long;
    var secTotal as int = (sec as int) + 1;
    if(sec==0 as long&&tick == 0 as long){
        secTotal = 0;
    }
    if(secTotal == 60){
        min +=1 as long;
        secTotal = 0;
    }
    if(min == 60 as long){
        min = 0 as long;
        hour +=1 as long ;
    }
    var outputString as string[] = [
        "",//hour
        "",//min
        "" //sec
    ];
    if(hour != 0 as long)outputString[0] = (hour as string)~"h";
    if(min != 0) outputString[1] = (min as string)~"min";
    if(secTotal != 0) outputString[2] = (secTotal as string)~"s";
    return outputString[0]~outputString[1]~outputString[2];
    
}
    如果就这样写，那即使是触发了“神弑者之怒”效果，我们也只会得到如下效果，根本没显示CD：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第55张图片

    这肯定不是我们想要的。问题就出在服务端和客户端之间信息不对称上，简单来说是服务端更新了玩家NBT中{rage_of_god_slayer_cooldown:xxx}这个NBT但客户端并没有，导致了从客户端获取这个NBT时不是过时了就是根本没有。怎么办呢？让客户端也跑一遍这个函数？没有这个必要，毕竟修改游戏逻辑的事情让服务端做就行了，客户端没必要去操这个心；而tooltip运行的整个过程放在客户端就可以处理了，只需要麻烦服务端告知一下客户端“需要在本地更新一下这个NBT就行了”。这就是今天的重点：服务端与客户端的通信。

    ZenUtils提供了一整套服务端与客户端通信的方法，只不过这个方法ZU的wiki上似乎没有写（至少笔者没有找到），笔者也只是在翻阅隔壁那篇事件教程的时候偶然发现的。这里笔者把接口啥的都列在下方：

    · 补充内容：ZenUtils的NetWorkHandler

    （笔者估计会把这个内容额外独立出来，做成一篇教程（?）至少等笔者更完这篇再说）（笔者表示已弃坑）

    ·NetWorkHandler类：用于处理服务端与客户端间的通信问题

    （注意：以下所有“数据包”均指服务端与客户端通信间收发的“数据包缓存”，并非高版本MC中类似于模组的、用于修改规则玩法的“数据包”）

    导包：

1
import mods.zenutils.NetWorkHandler;
    以下方法默认是静态的（static）、无返回值的（as void）

ZenMethod	作用
registerClient2ServerMessage(

    string bufName, 

    function (IServer server, IByteBuf byteBuf, IPlayer player){......}

)

注册一个客户端向服务端发送数据包、服务端接受该包后要处理的事件；

其中function函数体中表示服务端在接受到该名为<bufName>、

来自客户端的数据包后需要执行的操作；

server表示服务端，byteBuf表示该数据包（IByteBuf类下文会介绍），

player表示发送包的客户端的玩家；

registerServer2ClientMessage(

    string bufName, 

    function (IPlayer player, IByteBuf byteBuf){......}

)

注册一个服务端向客户端发送数据包、客户端接受该包后要处理的事件；

其中function函数体中表示客户端在接受到该名为<bufName>、

来自服务端的数据包后需要执行的操作；

player表示收到该包的客户端的玩家，byteBuf表示该数据包；

sendToServer(

    string bufName, 

    @Optional function (byteBuf as IByteBuf){......}

)

执行到该函数时向服务端发送一个

内容由function(byteBuf as IByteBuf){......}所撰写的、

名为<bufName>的数据包；（由客户端调用！）

如果不填写函数体处参数则会默认发送一个空的数据包；

（服务端仍然会接收到该数据包）

sendTo(

    string bufName, IPlayer player, 

    @Optional function (byteBuf as IByteBuf){......}

)

执行到该函数时向玩家<player>的客户端发送一个

内容由function(byteBuf as IByteBuf){......}所撰写的、

名为<bufName>的数据包；（由服务端调用！）

如果不填写函数体处参数则会默认发送一个空的数据包；

（客户端仍然会接收到该数据包）

sendToAll(

    string bufName, 

    @Optional function (byteBuf as IByteBuf){......}

)

执行到该函数时向所有玩家的客户端发送一个

内容由function(byteBuf as IByteBuf){......}所撰写的、

名为<bufName>的数据包；（由服务端调用！）

函数体处参数为空处理同上；

sendToAllAround(

    string bufName, 

    double x, double y, double z, 

    double range, int dimensionID, 

    @Optional function (byteBuf as IByteBuf){......}

)

执行到该函数时向维度ID为<dimensionID>、

以位置( x , y , z )为中心半径为<range>格内所有玩家发送一个

内容由function(byteBuf as IByteBuf){......}所撰写的、

名为<bufName>的数据包；（由服务端调用！）

函数体处参数为空处理同上；

sendToDimension(

    string bufName, int dimensionID, 

    @Optional function (byteBuf as IByteBuf){......}

)

执行到该函数时向维度ID为<dimensionID>的所有玩家发送一个

内容由function(byteBuf as IByteBuf){......}所撰写的、

名为<bufName>的数据包；（由服务端调用！）

函数体处参数为空处理同上；

    

    · IByteBuf类：上述通信间收发的“数据包”类

    导包：

1
import mods.zenutils.IByteBuf;
    数据包的写入、读取有要求，写入需要用<writeXXX()>来写入，读取需要用<readXXX()>来读取，它并没有任何的ZenGetter/Setter；

    首先是写入部分，默认全部返回值为void：

ZenMethod	作用
writeInt(int value)
对数据包写入一个int类型的数据
writeFloat(float value)
对数据包写入一个float类型的数据
writeDouble(double value)	对数据包写入一个double类型的数据
writeBoolean(boolean value)	对数据包写入一个bool类型的数据
writeString(string str)	对数据包写入一个string类型的数据
writeLong(long value)	对数据包写入一个long类型的数据
writeByte(byte value)
对数据包写入一个byte类型的数据
writeData(IData data)	
对数据包写入一个IData类型的数据

（可以写DataMap）

writeBlockPos(IBlockPos pos)	对数据包写入一个方块位置信息数据
writeItemStack(IItemStack item)
对数据包写入一个物品信息数据
writeUUID(UUID* uuid)	对数据包写入一个uuid
    *此处UUID为ZU中的UUID类（包为：mods.zenutils.UUID），可用ProbeZS模组导出查看接口，此处不详述。



    然后是读取部分：

ZenMethod	返回值	作用
readInt()
int	读取数据包中的int类型
readFloat()
float	读取数据包中的float类型数据
readDouble()	double	读取数据包中的double类型数据
readBoolean()	bool	读取数据包中的bool类型数据
readString()	string	读取数据包中的string类型数据
readLong()	long	读取数据包中的long类型数据
readByte()
byte	读取数据包中的byte类型数据
readData()	IData	
读取数据包中的IData类型数据

（可以读取DataMap）

readBlockPos()	IBlockPos	读取数据包中的方块位置信息数据
readItemStack()
IItemStack	读取数据包中的物品信息数据
readUUID()	UUID	读取数据包中的uuid数据
    使用注意：

    正所谓“既要有收，也要有发”，数据包就像快递一样，一方面需要一端有“发送数据包”这个行为，另一方面需要另一端有“接收数据包”对应的动作。只发送数据包不定义行为那这个数据包发了等于白发，只定义接收行为而不发送数据包等于这个接口干等着。所以我们需要注意将数据包的“发送”和“接受”相互对应匹配。

    而对于数据包的写入与读取，数据包需要按照写入顺序逐步读取！（也就是说如果先写了个int后写了个String，读的时候必须先读int后读String）否则则会出现意想不到的错误！

    让我们回到“神弑者之怒”的CD展示中：为了展示CD，我们需要在客户端对玩家NBT中的{rage_of_god_slayer_cooldown:xxx}这个NBT进行更新，这就需要服务器发包告诉我们“什么时候需要更新”，并且我们还需要定义接收到包后“客户端应该做什么”。并且考虑到服务端与客户端通信会存在一个延迟，所以发送数据包时附带一个发送时的时间是必要的。

    我们首先来看发包过程，这里笔者选择在effectConduct()函数中执行发包过程（别忘了导包NetWorkHandler！）：

//这里我们选择sendTo()对指定玩家对象发包，包命名为“updateCDByWorldTime”
//这个包笔者设计为“触发CD时调用发包”作为一个通用包，当然你也可以把它做成一个函数
//也可以选择对该特性单独制定一个收法包的规则
NetworkHandler.sendTo("updateCDByWorldTime",player,function(byteBuf){
 
    //然后我们需要给这个包写入数据，包括但不限于：
    //CD结束时间
    byteBuf.writeLong(player.world.getWorldTime()+(12000 as long));
     
    //要修改玩家身上CD对应的NBT key
    byteBuf.writeString("rage_of_god_slayer_cooldown");
     
});
    然后我们跳出整个事件，单独起一行来写收包规则：

//首先得要注册一个收包的事件，告知收到啥包后要做什么：
NetworkHandler.registerServer2ClientMessage("updateCDByWorldTime",function(player,byteBuf){
    //然后就是读取各种数据：
    //警告：这里必须得按照write的顺序阅读！
    //先读long然后才能到string
    var time as long = byteBuf.readLong();
    var NBTName as string = byteBuf.readString();
     
     
    //把这些信息给做成我们想要的NBT
    var updateData as IData = IData.createEmptyMutableDataMap();
    updateData.memberSet(NBTName ,time);
     
    //更新上去
    player.update(updateData);
});
    来看一下成果：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第56张图片

    这个是实时更新的，效果可以说非常理想！

    当然，效果做到这里还没完，笔者在自己测试的过程中发现：如果退出游戏后再登入，武器会刷新显示CD并显示为“神弑者之怒已就绪”，即使仍然处于CD中，如下图：（请忽略笔者那个ghost item报错，那与该案例无关）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第57张图片

    这依旧是服务端与客户端间信息不对称导致的，服务端保存了“{"rage_of_god_slayer_cooldown":xxx as int}”这个CD的NBT但客户端并没有，所以我们需要再开一个事件，在玩家登入的时候发一个包，如下：

events.onPlayerLoggedIn(function(event as PlayerLoggedInEvent){
    var player as IPlayer = event.player;
    if(player.world.remote)return;
    if(!isNull(
        player.data.rage_of_god_slayer_cooldown)&&
        player.data.rage_of_god_slayer_cooldown>player.world.getWorldTime()
    ){
        NetworkHandler.sendTo("updateCDByWorldTime",player,function(byteBuf){
                    byteBuf.writeLong(player.data.rage_of_god_slayer_cooldown);
                    byteBuf.writeString("rage_of_god_slayer_cooldown");
                });
    }
});
    最后放一下完整代码（tooltip部分代码已经放在上边了，此处略；这里我把发包放在effectConduct()外，不影响效果；导包略）：

import ......
 
function getItemCertainNBT(item as IItemStack, nbtString as string) as IData {
    if(!isNull(item)&&!isNull(nbtString)&&!isNull(item.tag)&&!isNull(item.tag.memberGet(nbtString))) {
        return item.tag.memberGet(nbtString);
    }
    else return null;
}
 
function itemHasCertainTrait(item as IItemStack, traitName as string) as bool{
    if(!isNull(item)&&!isNull(traitName)&&!isNull(getItemCertainNBT(item,"Traits"))){
        if(getItemCertainNBT(item,"Traits").asString().contains("\""~traitName~"\"")) return true;
        //注意这里对“ " ”字符检测需要加转义符“ \ ”
    }
    return false;
}
 
function itemCertainTraitActive(item as IItemStack, traitName as string) as bool{
    if(
        itemHasCertainTrait(item,traitName)&&
        !isNull(getItemCertainNBT(item,"Stats"))&&
        (isNull(getItemCertainNBT(item,"Stats").memberGet("Broken"))||
        (
            !isNull(getItemCertainNBT(item,"Stats").memberGet("Broken"))&&
            getItemCertainNBT(item,"Stats").Broken.asInt()==0)
        )
    )return true;
    return false;
}
 
function cooldownCheck(player as IPlayer , cdNBTName as string , cdTick as int , isPersisted as bool) as bool{
    var updateNBT as IData = IData.createEmptyMutableDataMap();
    updateNBT.memberSet(cdNBTName,(cdTick as long+player.world.getWorldTime()) as long);
    
    //函数作用：检测是否没在CD中，是则返回true，否则返回false
    
    if(isPersisted){//死亡保存数据
        var updatePersistedNBT as IData = IData.createEmptyMutableDataMap();
        updatePersistedNBT.memberSet("persisted",updateNBT);
        if(isNull(player.data.persisted)||isNull(player.data.persisted.memberGet(cdNBTName))){
            player.update(updatePersistedNBT);
            return true;
        }
        else{
            if(player.data.persisted.memberGet(cdNBTName).asLong()<=player.world.getWorldTime()){
                    player.update(updatePersistedNBT);
                    return true;
                }
                else return false;
        }
    }
    else{//死亡清空数据
        if(isNull(player.data.memberGet(cdNBTName))){
            player.update(updateNBT);
            return true;
        }
        else{
            if(player.data.memberGet(cdNBTName).asLong()<=player.world.getWorldTime()){
                player.update(updateNBT);
                return true;
            }
            else return false;
        }
    }
}
 
 
events.onEntityLivingHurt(function(event as EntityLivingHurtEvent){
    if(
        event.entityLivingBase.world.remote||
        isNull(event.entityLivingBase.getActivePotionEffect(<potion:contenttweaker:rage_of_god_slayer>))||
        event.damageSource.getDamageType()=="outOfWorld"
    ) return;
    event.amount =0.0f;
    event.cancel();
});
 
events.onEntityLivingDeath(function(event as EntityLivingDeathEvent){
    if(
        event.entityLivingBase.world.remote||
        !(event.entityLivingBase instanceof IPlayer)
    )return;
    var player as IPlayer = event.entityLivingBase;
    player.sendChat("Damage Type:"~event.damageSource.getDamageType());
    if(
        !isNull(player.getActivePotionEffect(<potion:contenttweaker:rage_of_god_slayer>))&&
        event.damageSource.getDamageType()!="outOfWorld"
    ){  //有神弑者之怒，取消所有非虚空死亡事件
        event.cancel();
        player.health =0.5f;
    }
    else{   //没有该药水效果，则开始检测特性
        
        var items as IItemStack[] = [
            player.currentItem,
            player.getInventoryStack(40)
        ];
        for i in 0 .. items.length{
            if(
                itemCertainTraitActive(items[i],"rage_of_god_slayer")&&
                cooldownCheck(player,"rage_of_god_slayer_cooldown",12000,false)
            ){
                items[i].mutable().attemptDamageItem(max(500,(0.4f*items[i].maxDamage) as int));
                effectConduct(player);
                player.health = 0.5f;
                event.cancel();
                NetworkHandler.sendTo("updateCDByWorldTime",player,function(byteBuf){
                    byteBuf.writeLong(player.world.getWorldTime());
                    byteBuf.writeString("rage_of_god_slayer_cooldown");
                    byteBuf.writeInt(12000);
                });
                return;
            }
        }
    }  
});
 
events.onPlayerLoggedIn(function(event as PlayerLoggedInEvent){
    var player as IPlayer = event.player;
    if(player.world.remote)return;
    if(
        !isNull(player.data.rage_of_god_slayer_cooldown)&&
        player.data.rage_of_god_slayer_cooldown>player.world.getWorldTime()
    ){
        NetworkHandler.sendTo("updateCDByWorldTime",player,function(byteBuf){
                    byteBuf.writeLong(player.world.getWorldTime());
                    byteBuf.writeString("rage_of_god_slayer_cooldown");
                    byteBuf.writeInt((player.data.rage_of_god_slayer_cooldown - player.world.getWorldTime()) as int);
                });
    }
});
 
NetworkHandler.registerServer2ClientMessage("updateCDByWorldTime",function(player,byteBuf){
    var time as long = byteBuf.readLong();
    var NBTName as string = byteBuf.readString();
    var cooldown as int = byteBuf.readInt();
    var updateData as IData = IData.createEmptyMutableDataMap();
    updateData.memberSet(NBTName ,time+(cooldown as long) as long);
    player.update(updateData );
});
 
function effectConduct(player as IPlayer) as void{
    player.sendChat("§d§lA GOD DOES NOT FEAR DEATH!!!");
    player.addPotionEffect(<potion:contenttweaker:rage_of_god_slayer>.makePotionEffect(200,0));
    var Xt = player.getX() as double;
    var Yt = player.getY() as double;
    var Zt = player.getZ() as double;
    var areaStart as Position3f = Position3f.create( ( Xt + 8.0d ) , ( Yt + 8.0d ) , ( Zt + 8.0d ) );
    var areaEnd as Position3f = Position3f.create( ( Xt - 7.0d ) , ( Yt - 7.0d ) , ( Zt - 7.0d ) );
    for entity in player.world.getEntitiesInArea( areaStart , areaEnd ){
        if(entity instanceof IEntityLivingBase){
            var entityNear as IEntityLivingBase = entity;
            entityNear.setFire(10);
            entityNear.attackEntityFrom(
                IDamageSource.createIndirectDamage("God Slayer Inferno",player,player),10.0f
            );
        }
    }
    for otherPlayer in player.world.getAllPlayers(){
        if(otherPlayer.world.dimension!=player.world.dimension) continue;
        effectCommandForOthers(player,otherPlayer);
    }
}
 
function effectCommandForOthers(player as IPlayer,otherPlayer as IPlayer){
    //音效
    server.commandManager.executeCommandSilent(
        server,
        "playsound item.totem.use master "~
        otherPlayer.name~" "~
        (player.x as string)~" "~(player.y as string)~" "~(player.z as string)~
        " 25 "~(1.0d+player.world.random.nextDouble()/2.0d) as string~" 0"
    );
    //两个粒子效果，一个是大量冒出怒火
    server.commandManager.executeCommandSilent(
        server,
        "particle angryVillager "~
        (player.x as string)~" "~(player.y as string)~" "~(player.z as string)~
        " 2 2 2 0.3 50 force "~otherPlayer.name
    );
    //一个是大量火焰飞溅
    server.commandManager.executeCommandSilent(
        server,
        "particle flame "~
        (player.x as string)~" "~(player.y as string)~" "~(player.z as string)~
        " 2 2 2 2 1000 force "~otherPlayer.name
    );
}
（注：Treasure！2调试棒NBT修改的案例代码过于冗余，风格过旧，不值得放在这里作为参考案例，笔者决定将其删除。（才不是Treasure2！垃圾加载优化卡重启MC时间卡得笔者一点也不想留这个模组！））

三、进阶！DataMap中DataList的操作

    上述两个例子只是对于Value为DataInt、DataLong等简单的IData数据类型进行操作，这一大节我们则是要深入IData类型，着重介绍Value为DataList的NBT的操作

    1.样例3：匠魂粉碎机复刻——匠魂工具粉碎机制

    这个作者到底是有多喜欢匠魂啊（恼！）

    玩过TITHS的各位想必对这个匠魂粉碎机印象深刻吧，它不仅一触就碎（至少笔者当初玩的版本是这样的），还会让你在不小心点到它的时候就把你手上珍贵的工具拆成了碎片——尽管这个机制可以让你回收不需要的工具、不慎用珍贵材料做成的武器。这里我们就来复刻一下这个机制，以下是笔者的个人设计。

    要求：当玩家左手手持锤子、右手手持匠魂工具时，右击铁砧顶部，即可拆毁右手工具，返回对应材料部件/碎片。

    在开始之前我们必须要来看看匠魂的相关工具和材料NBT机制。总所周知，一个匠魂工具需要2-4个对应部件进行制作，这些部件在制作的时候存在放入顺序的要求，这里我们以镐子为例：为了合成一把镐子，我们需要按照以下顺序对着默认界面按住shift放入部件：手柄→镐头→绑定结，这样才能在默认界面制作出一把镐子（这里笔者使用的是阿迪特手柄、钴镐头、骑士史莱姆绑定结）。尽管作者已经优化使得各位可以打开对应工具的制作界面然后无脑shift放，但在这里我们仍然不能忽略这个放入顺序。

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第58张图片

    那放入顺序有什么用呢？它关系着特性的触发先后次序、NBT中材料的排布顺序。关于特性触发顺序参考笔者的一篇文章：传送门，这里我们着重关注NBT的排布顺序。拿着上述镐子输入/ct nbt查询一下NBT：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第59张图片
    可以看到，在TinkerData下有一个Key：Materials，其值储存了构成这把镐子的三种材料：阿迪特、钴、骑士史莱姆。这三种材料正好对应了笔者放入的三个部件的顺序。换言之如果我们拿到了这样一把镐子，从TinkerData的Materials标签中得知它的三个材料（及其顺序）是A、C和K，那它一定是用了A材料的手柄、C材料的镐头和K材料的绑定结所制成的。匠魂的各种武器工具都有自己的材料放入顺序，我们可以打开对应工具合成界面查看，“组件”一栏从上到下便对应了这个工具部件的放入顺序，如下例：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第60张图片



    知道了部件材料和放入顺序的关系，接下来我们还要来看不同材料的部件所对应的NBT。以手柄和碎片为例，拿着它们/ct nbt查询一下NBT便可以发现：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第61张图片
阿迪特手柄

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第62张图片
骑士史莱姆碎片

    每种材料部件都会用一个“Material”的NBT标签储存这个部件对应的材料名字信息。当然这里有个特例：弩箭和弩箭核心。这里以 木-铁弩箭核心 和 木-铁弩箭 为例，查询其NBT得知：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第63张图片
木-铁弩箭核心
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第64张图片
木-铁弩箭
    由于弩箭核心独特的制作方式（制作A箭杆后浇筑金属B得到A-B弩箭核心）以及和其他工具相同的NBT结构，弩箭和弩箭核心都可以被视为“工具”进行处理。

    有了以上基础，我们便有了拆解的思路：获取工具中TinkerData下Materials的各种材料名称以及顺序，然后消耗这个工具并给予玩家对应的材料部件/碎片。前置事件代码（是否蹲下、判断副手物品是锤子、判断准心是否对准铁砧顶部等）笔者已经写好了：

import ......
 
//两个播放音效函数
function playSoundDeny(player as IPlayer) as void{
    server.commandManager.executeCommandSilent(
        server,
        "playsound entity.villager.no master "~player.name~" "~
        (player.x as string)~" "~(player.y as string)~" "~(player.z as string)~
        " 0.8 "~(0.50d+player.world.random.nextDouble()) as string~" 0"
    );
}
 
function playSoundCrash(player as IPlayer) as void{
    server.commandManager.executeCommandSilent(
        server,
        "playsound entity.item.break master "~player.name~" "~
        (player.x as string)~" "~(player.y as string)~" "~(player.z as string)~
        " 0.8 "~(0.50d+player.world.random.nextDouble()) as string~" 0"
    );
}
 
events.onPlayerRightClickItem(function(event as PlayerRightClickItemEvent){
    if(event.world.remote)return;
    var player = event.player;
     
    //对玩家使用getRayTrace()表示获取玩家光标所指向的方块信息（实则返回的是一个IRayTraceResult类）。
    //其五个参数中第一个表示获取光标指向的方块的最远距离，一般设置成5.0d，和玩家手长一致
    //第三个表示是否停止于液体方块上（若指向路径上有液体方块，true则停止于液体方块上，false则穿过液体方块）
    //第四个表示是否忽略无碰撞箱的方块（如草、蜘蛛网等，true则穿过这些方块，false则表示不穿过这些方块）
    //第二和五个笔者暂时没发现其用法，此处设为0.0f和false
    //当然rayTrace可能为null（如玩家目视天空/远处），所以得排除掉这些情况
    var rayTrace as IRayTraceResult = player.getRayTrace(5.0d, 0.0f , false, false, false);
    if(
        !isNull(event.item)&&
        event.item.matchesExact(player.currentItem)&&
        player.isSneaking
    ){
        if(!isNull(rayTrace)){
            var targetBlock as IBlock = player.world.getBlock(rayTrace.blockPos);
            var offhandItem as IItemStack = player.getInventoryStack(40);
            if(
                    targetBlock.definition.id==<minecraft:anvil>.definition.id&&//目标方块必须是铁砧
                    rayTrace.sideHit.name=="UP"&&//所指方位必须是顶部
                    <item:tconstruct:hammer:*>.matches(offhandItem)//副手必须是匠魂锤子
                ){
                if(
                    //锤子必须是好的
                    (isNull(offhandItem.tag.Stats.Broken)||
                    offhandItem.tag.Stats.Broken.asInt()==0)
                ){
                    //这里笔者用自定义的crashTool()函数来实现拆毁功能
                    //包括判断主手物品、拆毁并给予玩家部件/碎片等功能
                    //函数将在下文编写
                    crashTool(event.item,player,offhandItem);
                }
                else{
                    //否则不拆毁
                    player.sendStatusMessage("§4§l锤子已损坏！无法继续拆毁！");
                    playSoundDeny(player);
                }
            }
        }
    }
});
    接下来我们需要把各种匠魂工具和其材料部件以及制作顺序给统计出来，这里笔者已经把匠魂2和匠魂盔甲的每种工具及其对应的材料部件和顺序做成了一个关联数组（为后续的完整拆解做准备），外加每种部件对应的材料值（注意2个碎片对应1材料值！），为下文的拆解返还做准备。（这里笔者把弩箭核心放进去一并处理了，浇筑材料视为4个碎片作为部件处理）

static materialParts as IItemStack[][IItemStack] = {
    <item:tconstruct:pickaxe:*>:[<item:tconstruct:tool_rod>,<item:tconstruct:pick_head>,<item:tconstruct:binding>],
    <item:tconstruct:shovel:*>:[<item:tconstruct:tool_rod>,<item:tconstruct:shovel_head>,<item:tconstruct:binding>],
    <item:tconstruct:hatchet:*>:[<item:tconstruct:tool_rod>,<item:tconstruct:axe_head>,<item:tconstruct:binding>],
    <item:tconstruct:mattock:*>:[<item:tconstruct:tool_rod>,<item:tconstruct:axe_head>,<item:tconstruct:shovel_head>],
    <item:tconstruct:kama:*>:[<item:tconstruct:tool_rod>,<item:tconstruct:kama_head>,<item:tconstruct:binding>],
    <item:tconstruct:hammer:*>:[<item:tconstruct:tough_tool_rod>,<item:tconstruct:hammer_head>,<item:tconstruct:large_plate>,<item:tconstruct:large_plate>],
    <item:tconstruct:excavator:*>:[<item:tconstruct:tough_tool_rod>,<item:tconstruct:excavator_head>,<item:tconstruct:large_plate>,<item:tconstruct:tough_binding>],
    <item:tconstruct:lumberaxe:*>:[<item:tconstruct:tough_tool_rod>,<item:tconstruct:broad_axe_head>,<item:tconstruct:large_plate>,<item:tconstruct:tough_binding>],
    <item:tconstruct:scythe:*>:[<item:tconstruct:tough_tool_rod>,<item:tconstruct:scythe_head>,<item:tconstruct:tough_binding>,<item:tconstruct:tough_binding>],
    <item:tconstruct:broadsword:*>:[<item:tconstruct:tool_rod>,<item:tconstruct:sword_blade>,<item:tconstruct:wide_guard>],
    <item:tconstruct:longsword:*>:[<item:tconstruct:tool_rod>,<item:tconstruct:sword_blade>,<item:tconstruct:hand_guard>],
    <item:tconstruct:rapier:*>:[<item:tconstruct:tool_rod>,<item:tconstruct:sword_blade>,<item:tconstruct:cross_guard>],
    <item:tconstruct:frypan:*>:[<item:tconstruct:tool_rod>,<item:tconstruct:pan_head>],
    <item:tconstruct:battlesign:*>:[<item:tconstruct:tool_rod>,<item:tconstruct:sign_head>],
    <item:tconstruct:cleaver:*>:[<item:tconstruct:tough_tool_rod>,<item:tconstruct:large_sword_blade>,<item:tconstruct:large_plate>,<item:tconstruct:tough_tool_rod>],
    <item:tconstruct:shortbow:*>:[<item:tconstruct:bowlimb>,<item:tconstruct:bowlimb>,<item:tconstruct:string>],
    <item:tconstruct:longbow:*>:[<item:tconstruct:bowlimb>,<item:tconstruct:bowlimb>,<item:tconstruct:large_plate>,<item:tconstruct:string>],
    <item:tconstruct:arrow:*>:[<item:tconstruct:arrow_shaft>,<item:tconstruct:arrow_head>,<item:tconstruct:fletching>],
    <item:tconstruct:crossbow:*>:[<item:tconstruct:tough_tool_rod>,<item:tconstruct:bowlimb>,<item:tconstruct:tough_binding>,<item:tconstruct:string>],
    <item:tconstruct:bolt:*>:[<item:tconstruct:arrow_shaft>,<item:tconstruct:shard>*4,<item:tconstruct:fletching>],
    <item:tconstruct:bolt_core:*>:[<item:tconstruct:arrow_shaft>,<item:tconstruct:shard>*4],
    <item:tconstruct:shuriken:*>:[<item:tconstruct:knife_blade>,<item:tconstruct:knife_blade>,<item:tconstruct:knife_blade>,<item:tconstruct:knife_blade>],
    <item:conarm:helmet:*>:[<item:conarm:helmet_core>,<item:conarm:armor_plate>,<item:conarm:armor_trim>],
    <item:conarm:chestplate:*>:[<item:conarm:chest_core>,<item:conarm:armor_plate>,<item:conarm:armor_trim>],
    <item:conarm:leggings:*>:[<item:conarm:leggings_core>,<item:conarm:armor_plate>,<item:conarm:armor_trim>],
    <item:conarm:boots:*>:[<item:conarm:boots_core>,<item:conarm:armor_plate>,<item:conarm:armor_trim>]
};
 
static materialCost as int[IItemStack] = {
    <item:tconstruct:pick_head:*>:2,
    <item:tconstruct:shovel_head:*>:2,
    <item:tconstruct:axe_head:*>:2,
    <item:tconstruct:broad_axe_head:*>:8,
    <item:tconstruct:sword_blade:*>:2,
    <item:tconstruct:large_sword_blade:*>:8,
    <item:tconstruct:hammer_head:*>:8,
    <item:tconstruct:excavator_head:*>:8,
    <item:tconstruct:kama_head:*>:2,
    <item:tconstruct:scythe_head:*>:8,
    <item:tconstruct:pan_head:*>:3,
    <item:tconstruct:sign_head:*>:3,
    <item:tconstruct:tool_rod:*>:1,
    <item:tconstruct:tough_tool_rod:*>:3,
    <item:tconstruct:binding:*>:1,
    <item:tconstruct:tough_binding:*>:3,
    <item:tconstruct:wide_guard:*>:1,
    <item:tconstruct:hand_guard:*>:1,
    <item:tconstruct:cross_guard:*>:1,
    <item:tconstruct:large_plate:*>:8,
    <item:tconstruct:knife_blade:*>:1,
    <item:tconstruct:bowlimb:*>:3,
    <item:tconstruct:string:*>:1,
    <item:tconstruct:arrow_head:*>:2,
    <item:tconstruct:arrow_shaft:*>:2,
    <item:tconstruct:fletching:*>:2,
    <item:tconstruct:shard:*>*4:1,
    <item:conarm:helmet_core:*>:4,
    <item:conarm:chest_core:*>:6,
    <item:conarm:leggings_core:*>:5,
    <item:conarm:boots_core:*>:4,
    <item:conarm:armor_trim:*>:1,
    <item:conarm:armor_plate:*>:3
};
    然后我们来实现crashTool()这个函数。为此我们需要把主手物品、玩家信息和副手锤子三个参数传进去（传副手锤子是为了当拆解成功后消耗耐久）。这里笔者设计了3种模式，分别是：精准拆毁（返还所有部件）、完全拆毁（返还所有相当于制作部件材料值的碎片）、部分拆毁（返还少量对应材料碎片），对应下文crashConfig布尔数组。

//=====================================================
 
static crashConfig as bool[] = [
 
    //============================
    //三种模式开启开关
    false,//精准拆毁，返还所有部件，覆盖以下所有设置
    false,//完全拆毁，返回所有部件对应材料值的碎片，覆盖以下所有设置
    //若以上均为false则表示是部分拆毁
    //============================
     
    //============================
    //部分拆毁模式微调部分：
    //P.S.内置返回碎片最大值为对应部件所需材料值*2（材料值对应碎片值）
    //1.maxShardAmount
    //是否启用额外最大返回上限设定（maxShardAmount）
    //若maxShardAmount为正则最终返还碎片数量为内置最大值和该最大值间最小的一个
    //若maxShardAmount为非正则完全拆毁、不返回任何碎片
    false,
    //2.随机模式
    //是否启用随机模式
    //是则启用随机模式，在0到能够返回的最大值间随机
    //否则启用上限模式，返回能够返回最大值的碎片量
    true
    //===========================
     
];
 
//每个部件最大返回碎片上限，超过部件材料值对应返回上限则以后者为限
//需要配合上文种“1.maxShardAmount”使用
static maxShardAmount as int = 2;
 
//=======================================================
 
function crashTool(tool as IItemStack,player as IPlayer,offhandItem as IItemStack) as void{
 
    if( //排除四种情况：
        isNull(tool)||//tool为空
        isNull(tool.tag)||//tool没tag
        isNull(tool.tag.TinkerData)||//tool没“TinkerData”成员
        isNull(tool.tag.TinkerData.Materials)//tool没“TinkerData”下“Materials”成员
    ){
        //不然就不给拆毁
        player.sendStatusMessage("§4§l该物品无法被拆毁！");
        playSoundDeny(player);
        return;
    }
     
    //把TinkerData下Materials的对应的材料名称列表（IData下的DataList）给拿出来
    var materialDataList as IData = tool.tag.TinkerData.Materials;
    //先存一个partList部件列表的物品数组
    var partList as IItemStack[] = null;
     
    //先处理部件列表，遍历上文“materialParts”的Key和Value，找到tool对应的工具
    for key in materialParts{
        if(key.matches(tool)){
            //如果找到把对应工具的部件带着顺序赋值给partList
            partList = materialParts[key];
        }
    }
     
    //如果没找到那就是没这个工具，需要自己添加
    if(isNull(partList)){
        player.sendStatusMessage("§4§l该物品无法被拆毁！请更新配置文件以兼容该物品！");
        playSoundDeny(player);
        return;
    }
     
    //接下来处理三种模式
    if(crashConfig[0]){//首先是精准拆毁模式
        //这里我们需要对materialDataList这个DataList进行遍历操作
        //将其中材料信息和对应部件相匹配
        //由于materialDataList和partList两个数组长度相同、在顺序上是一一对应的
        //这里我们需要用普通的for循环来遍历
        //for i in a .. b结构中i会从a取到(b-1)的值
        //不用担心数组越界问题
        //-------------------------------------------------------
        //警告：这里不能使用增强for循环（foreach），原因有二：
        //一是不能对DataList使用foreach！
        //二是因为foreach在运行过程中并不是按顺序遍历！！！
        //-------------------------------------------------------
        for i in 0 .. materialDataList.length{
            //用materialDataList[i]表示该DataList下第i号（第(i+1)个）元素
            //也就是第(i+1)个以DataString储存的材料名称
            //因为其本身就是IData对象，不需要额外转类型
            player.give(partList[i].withTag({"Material":materialDataList[i]}));
        }
    }
    else{//其次是返回碎片模式
        if(crashConfig[1]){//第一是完整拆毁模式：返回完整碎片
            //对materialDataList处理同上
            for i in 0 .. materialDataList.length{
                //这里笔者用giveShard()和getMaterialCost两个函数分别处理给予碎片操作和获取对应部件材料值操作
                //函数本体见下
                giveShard(materialDataList[i],getMaterialCost(partList[i])*2,player);
            }
        }
        else{//第二是部分拆毁模式：返回部分碎片
            for i in 0 .. materialDataList.length{
                //获取部件对应材料值的碎片量
                var shardAmount = getMaterialCost(partList[i])*2;
                 
                //首先是启用了最大上限（maxShardAmount）
                //shardAmount需要在maxShardAmount和内置最大限制间取最小值
                if(crashConfig[2]){
                    if(maxShardAmount<=0){
                        //这里是对maxShardAmount为非正的一个处理
                        tool.mutable().shrink(1);
                        offhandItem.mutable().attemptDamageItem(100);
                        playSoundCrash(player);
                        return;
                    }
                    shardAmount = min(maxShardAmount,getMaterialCost(partList[i])*2);
                }
                 
                //然后是启用随机模式
                //把碎片返回值从上限换成0~上限的一个随机数
                if(crashConfig[3]){
                    shardAmount = player.world.random.nextInt(0,shardAmount);
                }
                //然后给玩家碎片
                giveShard(materialDataList[i],shardAmount,player);
            }
        }
    }
    //销毁1个手上的工具
    tool.mutable().shrink(1);
    //锤子损失100点耐久
    offhandItem.mutable().attemptDamageItem(100);
    //播放音效
    playSoundCrash(player);
}
 
function getMaterialCost(part as IItemStack) as int{
    if(!isNull(part)){
        for key in materialCost{
            if(key.matches(part)) return materialCost[key] as int;
        }
        print("No Such Part!");
        return 0;
    }
    print("Null Item!");
    return 0;
}
 
function giveShard(materialData as IData,amount as int,player as IPlayer) as void{
    if(amount==0)return;
    player.give(<item:tconstruct:shard:0>.withTag({"Material":materialData})*amount);
}
    以下是测试结果：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第65张图片
精准拆毁模式

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第66张图片
完全拆毁模式

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第67张图片
设置maxShardAmount为2的上限模式

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第68张图片
关闭maxShardAmount的随机模式

    效果非常完美！

    （如有bug请及时反馈！）



    这里额外补充一点内容：关于上述“神弑者之怒”中的itemHasCertainTrait()函数，在我们了解了对DataList进行遍历处理后可以将其改写为：

function itemHasCertainTrait(item as IItemStack, traitName as string) as bool{
    if(!isNull(item)&&!isNull(traitName)&&!isNull(getItemCertainNBT(item,"Traits"))){
        var traitList as IData = getItemCertainNBT(item,"Traits");
        for i in 0 .. traitList.length{
            if(traitList[i].asString()==traitName)return true;
        }
    }
    return false;
}


    2.样例4：动态信息展示Part.2：display下的Lore标签操作

    前一个案例中我们对DataList使用遍历的方法获取其中信息，而这一节我们则需要更进一步：对DataList进行修改操作。和前三个案例不同的是，这一节不会给出特别具体的实用案例，而是着重于完成我们动态信息展示的第二部分：display标签下Lore标签的修改操作。

    我们来看这个例子：这是一把有着三个展示标签的钻石剑。和part1.1、part1.2中的信息展示不同，实现这个展示标签并没有用到tooltip function，而是使用MC原版自带的display下的Lore标签进行展示处理的：（这里的变色、加粗显示使用了MC的格式化代码）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第69张图片
钻石剑的tooltip

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第70张图片
钻石剑的NBT，这里的“b”表示“§”符号

    显然，通过查看NBT我们可以发现：展示的三个标签内容我们都可以在Lore对应的DataList中找到，因此通过修改这个标签内容，我们就可以修改物品的标签显示。

    这里我们给一个比较具体的要求：既然上文中tooltip function无法高效解决对所有物品都添加死亡计数显示，那Lore标签能否做呢？答案是：能！这也是笔者当初成功地完成了Lore修改从而达成了动态信息展示后，动笔写下这篇教程的原因。

    让我们把这个要求给具象化：用修改Lore标签的方式，为每一个物品添加一个击杀计数显示。

    首先当然得把一些基础的操作给做成函数，比如说：（导包省略）

//获取一个物品的Lore标签
function getLore(item as IItemStack) as IData{
    if(isNull(item)||isNull(item.tag)||isNull(item.tag.display)||isNull(item.tag.display.Lore))return null;
    return item.tag.display.Lore;
}
 
//修改一个物品的Lore标签（传入一个DataList）
function updateLore(item as IItemStack,lore as IData) as void{
    item.mutable().updateTag({"display":{"Lore":lore}});
}
 
//DataList转换为String数组
//由于相关检测难度过高
//笔者不在这个函数内部设置传入参数为DataList的相关检测
//不对此函数因传入参数内部类型不匹配导致的运行报错负责
//请各位保证自己使用的时候传入的是一个DataList！
function dataListToStringArr(dataList as IData) as string[]{
    var stringArr as string[] = [];
    if(dataList.length == 0) return stringArr;
    for i in 0 .. dataList.length{
        stringArr+=dataList[i].asString();
    }
    return stringArr;
}
 
//老朋友，获取一个第一层的NBT
function getItemCertainNBT(item as IItemStack, nbtString as string) as IData {
    if(!isNull(item)&&!isNull(nbtString)&&!isNull(item.tag)&&!isNull(item.tag.memberGet(nbtString))) {
        return item.tag.memberGet(nbtString);
    }
    else return null;
}
    然后是事件框架：

events.onEntityLivingDeath(function(event as EntityLivingDeathEvent){
    var entity as IEntityLivingBase = event.entityLivingBase;
    var dmgsrc as IDamageSource = event.damageSource;
    if(
        entity.world.remote||
        isNull(dmgsrc)||
        isNull(dmgsrc.getTrueSource())||
        !(dmgsrc.getTrueSource() instanceof IPlayer)
    )return;
    var player as IPlayer = dmgsrc.getTrueSource();
    var item as IItemStack = player.currentItem;
    if(isNull(item))return;
    var lore as IData = getLore(item);
    //Code
});
    关于击杀计数，有两种解决方式：一是添加一个{"DeathCount":xxx as int}然后使得Lore与之同步；二是直接用Lore来储存击杀数。前一种方法代码难度不大，但比较复杂，代码量较大；而后一种方法对String的处理要求比较高，但代码量相对较小（？）。我们都来做一下：

    首先是第一种，先把{"DeathCount":xxx as int}这个NBT的部分给处理好：（续接//Code处）

var deathCount as IData = getItemCertainNBT(item,"DeathCount");
var deathCountCheck as bool = false;
if(isNull(deathCount)){
    item.mutable().updateTag({"DeathCount":1 as int});
}
else{
    item.mutable().updateTag({"DeathCount":(1+deathCount)});
    deathCountCheck = true;
}
    这里就来第一个得注意的点：item.mutable().updateTag(...)并不是立刻生效的，代码运行的速度要远快于updateTag()这个函数处理完成的速度。所以我们如果这个时候用getItemCertainNBT()这个函数覆盖掉deathCount这个局部变量，它返回的不是修改后的值而是修改前的值。所以这里笔者加了一个deathCountCheck来储存是否检测到DeathCount这个NBT。

    然后是Lore部分。getLore()中已经将“没有NBT”、“没有‘display’”和“‘display’下没有‘Lore’”三种情况给归纳出来了，对于这种情况我们只需要检测后添加Lore就行了。麻烦的是第二种情况：物品本身带了Lore。笔者的设计是如果没有我们需要显示Lore则在最后加上这个Lore；如果有的话则在原位置上更新。而对于设计显示的Lore，笔者简单地设计为：“§c累计击杀：xx§r”，我们来看代码：

//如果没有NBT、NBT下没有“display”、“display”下没有“Lore”则自己加一个：
if(isNull(lore)||lore.length == 0) updateLore(item,["§c累计击杀："~deathCount.asString()~"§r"] as IData);
//如果有Lore，则我们需要把它加工一下
else{
    //这里使用遍历法
    //当然用IDataDeepUpdate也能实现
    //第一个变量用于储存重构后的DataList
    //第二个变量用于检测是否具有“累计击杀：”这个显示的LoreTag
    var loreToBeUpdated as IData = [] as IData;
    var targetLoreCheck as bool = false;
    for i in 0 .. lore.length{
        //这里lore[i]取出来表示显示的每一行
        //使用“contains()”这个Method需要注意的是
        //后期添加的所有其他LoreTag都不能带有完整且连续的检测字段
        if(lore[i].asString().contains("§c累计击杀：")){
            loreToBeUpdated += ["§c累计击杀："~deathCount.asString()~"§r"] as IData;
            targetLoreCheck = true;
        }
        else{
            loreToBeUpdated += [lore[i]] as IData;
        }
    }
    if(!targetLoreCheck)loreToBeUpdated += ["§c累计击杀："~deathCount.asString()~"§r"] as IData;
    updateLore(item,loreToBeUpdated);
}
    效果如下：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第71张图片
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第72张图片
    让我们来看看第二种情况：直接使用这个显示标签存储击杀数据。依旧是从事件中//Code部分代码入手，使用遍历法对DataList进行操作：

//没有NBT、NBT下没有“display”、“display”下没有“Lore”：
if(isNull(lore)||lore.length == 0) updateLore(item,["§c累计击杀：1§r"] as IData);
//有Lore
else{
    var loreToBeUpdated as IData = [] as IData;
    //这里预设局部变量targetLore，遍历后需检测其是否为null来检测是否检测到了目标字符串
    var targetLore as string = null;
    for i in 0 .. lore.length{
        if(lore[i].asString().contains("§c累计击杀：")&&!isNull(targetLore)){
            //把有“§c累计击杀：”的标签取出来
            targetLore = lore[i].asString();
            //由于其一定拥有“：”（中文全角的冒号），所以可以以这个为分界线将字符串分割
            //使用方法：str.split("：");   返回一个string[]
            //假设这个Lore为：“§c累计击杀：15§r”，则分割后获得的string[]为：
            //["§c累计击杀","15§r"]
            //如果对应分割符不存在则返回整个字符串作为元素的、长度为1的string[]
            var targetLoreStrArr as string[] = targetLore.split("：");
            //检测字符串是否被分成了两个部分，是则继续，否则表示分错了字符串
            //将targetLore清空并且继续遍历
            if(targetLoreStrArr.length!=2) {
                targetLore = null;
                continue;
            }
            //把第二部分后边颜色、字体还原符“§r”给切掉：
            targetLoreStrArr = targetLoreStrArr[1].split("§r");
            //这里照样需要检测切出来后是否是只有一个部分
            if(targetLoreStrArr.length!=1) {
                targetLore = null;
                continue;
            }
            targetLore = targetLoreStrArr[0];
            //P.S.ZenScript支持这样的操作：targetLore = targetLoreStrArr[1].split("§r")[0];
            //然后把这个string类型的targetLore给转化为int并且+1
            loreToBeUpdated += ["§c累计击杀："~((targetLore as IData).asInt() + 1)~"§r"] as IData;
        }
        else{
            loreToBeUpdated += [lore[i]] as IData;
        }
    }
    if(isNull(targetLore))loreToBeUpdated += ["§c累计击杀：1§r"] as IData;
    updateLore(item,loreToBeUpdated);
}
    效果：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第73张图片

    说实话笔者并不喜欢这种直接储存的方法，毕竟一方面它非常容易与别的可能的显示Lore相冲突（毕竟ZenScript并没有像java一样强大的字符串处理系统，并不能保证专一处理目标字符串，这也是所有以Lore方式进行信息显示的通病），第二方面它获取数据非常麻烦，每获取一次都得要遍历--检测，且由于第一方面的限制它获取数据并不可靠。解决方案除了设计专一、有辨识特征的字符串显示外就是尽可能不用这种方式进行储存（显示还好说，如果需要额外应用的话还是乖乖地用第一种方式进行储存吧）。尽管这种方法属于治标不治本，但总归是在ZenScript框架下比较可行的方案了......

    说起来还有一点得注意：如果碰到了这个Lore：“§c累计击杀：1个生物§r”，上述判断都可以通过，但最后类型转换会因为“1个生物”无法被转化为int而默认转化为0，并且将该字符串强行改造为“§c累计击杀：1§”。这种情况除了暴露上述第一方面的问题外，还暴露了ZenScript本身并没有一个比较可靠的字符串“是否为纯数字”的判断方式的问题（据笔者所知，java里边判断字符串是否是纯数字需要先转化为char数组然后再对每一个char进行isDigit()遍历检测，可惜ZenScript并没有这种功能），我们只能通过IData左手捯右手，背负着报错的风险强制转换。

    （所以有没有dalao能写个mod给String加一个isNumeric()的判断功能啊qwq）

    最后来总结一下用display下Lore标签进行动态信息展示的优劣。其优势肯定在于适配度相当高，几乎所有的IItemStack都可以用这种方法来进行信息展示处理而不仅仅局限于某类物品，对比tooltip function没有对付大批量物品处理乏力的问题存在。当然缺点也是非常明显：NBT操作难度高（需要对DataList进行遍历、字符串匹配等操作）、灵活度低（如果要做显示CD需要每Tick都要调用事件进行修改，不像tooltip只需要传入参数就可以达成）、出错几率高（ZS本身的功能所限）。估计如果不是不知道tooltip function或者是需要大批量添加显示，都不会采用这种方法吧......

    四、附加专题：工作台合成的NBT继承与调整专题

        →这一节主要介绍工作台合成配方中、NBT继承与调整

    老实说笔者没有想到会有这么多人问合成配方相关的问题，毕竟在笔者眼里NBT操纵的难度要大于事件撰写，并且本篇教程对事件编写有要求；而如果事件已经会写了，那工作台配方函数和事件函数也并非啥难事，因此笔者忽略了那些对事件了解较少、但又希望合成配方能达成xx功能的玩家的需求。不过既然有人提出了这个问题，于是笔者打算解决一下，所以额外添加了这个附加专题，从配方函数和配方事件函数开始，专门介绍工作台上NBT的相关操作。这一节的门槛比较低，只需要知道怎么改工作台配方，并且阅读完本篇教程的第一大节前三小节即可。

    （不过笔者还是比较建议先去了解一下事件再来......尽管用工作台配方函数、事件函数来入门事件也不是不行）

    首先我们从添加合成配方开始。对于工作台添加配方，我们一般会写成：（以无序配方为例）

1
recipes.addShapeless(String recipeName,IIngredient outputIngredient,IIngredient[] inputIngredientArray);
    P.S.这里的IIngredient是原料类，实际操作中我们一般以IItemStack来替代。

    但实际上这是个省略版的配方添加代码，完整的配方代码还包含两部分：配方函数和（配方）事件函数，关于这部分可以参考友谊妈的教程：Click Here。

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
recipes.addShapeless(String recipeName,IIngredient outputIngredient,IIngredient[] inputIngredientArray,
    //===配方函数===
    function(out,ins,cInfo){
        //Code
        return <IItemStack outputItem>;
        //这里尖括号表示你需要输出的物品（IItemStack）
        //别傻乎乎地抄连同尖括号带着里边的内容一起抄走
    },
    //===========
     
    //===事件函数===
    function(out,cInfo,player){
        //Code
    }
    //===========
);
    配方函数和事件函数并非必须，不需要的话可以用null代替function那一堆，也就是说可以写成这样：

1
recipes.addShapeless(String recipeName,IIngredient outputIngredient,IIngredient[] inputIngredientArray,null,null);
    不过如果我们想整点花活的话就需要用到这俩了，下边说说这两者的作用：

    配方函数的作用是计算（或者叫确定）配方输出的物品，它与参与合成的物品相关，控制着实际输出的那个物品的相关信息。换句话说，如果说outputIngredient给出的是名义上的合成结果（JEI里显示的结果，这里笔者称为“名义产物”）的话，那配方函数则可以实际修改这个结果，从而达到诸如修改耐久、继承附魔，甚至是修改产物、拒绝合成等效果。值得注意的是这个函数在玩家每次摆好配方时都会调用一次，可以看成每次摆好配方后都会执行的一个事件，换言之如果配方中含有例如player.attackEntityFrom(null,1.0f)这类语句的话那每次摆好配方时玩家都会受到1点的伤害。注意：配方函数需要return一个IItemStack对象（可以是null以拒绝合成）作为输出产物。

    事件函数是在配方合成（取出配方物品）的那一瞬间执行的一个事件，它更多的是控制合成后发生的相关效果（比如合成扣除经验值等）。

    关于这两个函数，典型例子就是ExU2的黄金套索：它需要消耗8级经验才能合成，如果玩家经验等级小于8级则不能合成。这里，消耗经验的效果是通过事件函数来执行的，判断经验等级从而确定是否允许合成这一效果则是通过配方函数来确定的。我们可以试着复刻这个效果，但在此之前我们必须知道两个函数各自三个参数的意义：

    配方函数中三个参数分别是out、ins和cInfo，其中：

out类型为IItemStack，它代表着，这个配方代码给出的那个outputIngredient参数（名义产物）所对应的IItemStack；

ins则是一个映射（关联数组），在玩家对inputIngredientArray这个合成数组中某个物品做了标记后，标记的名字和实际合成时的对应的物品会构成Key-Value对并且被添加进ins中（比如我在合成表里写了个<item:minecraft:diamond_sword>.marked("sword")，那我可以通过ins.sword来获取这个钻石剑对应、实际合成时放入的那把钻石剑的IItemStack物品对象；如果这把钻石剑带有附魔，那这个ins.sword也可以获取到这把钻石剑的附魔）；

cInfo是ICraftInfo类对象，它包含了有关这个配方在实际 摆放形成后和合成取出时的各种相关信息（接口请查询官方wiki）；

    事件函数中三个参数分别是out、cInfo和player，其中out、cInfo和上述一致，player则是合成这个配方的玩家。

    注意：官方wiki中提到，这里的player可能为空！而ICraftInfo可以通过cInfo.world直接获取对应IWorld

    我们这里先从几个简单的例子来入手。首先是事件函数的应用，这里就用一个直观且实用的例子：钻石剑能用钻石修复，每枚钻石修复100点耐久，修复后不继承附魔。（如何编写让钻石剑修复后继承附魔？这个问题留给读者思考。笔者提示：看完下边的相关NBT操作，这个问题就会了！）

    分析一下：任何物品附魔都是通过NBT来实现的，所以我们可以通过标记以及ins调用来获取实际放入的那把钻石剑的NBT，再给它塞到修复后的钻石剑上；这里不需要事件函数，所以事件函数可以扔个null进去；最后是工具修复，笔者只强调一点：item.damage获取的是已损失耐久，而item.maxDamage获取的是最大耐久，item.withDamage(amount)则是返回一个损失耐久为amount的新物品，如一把损失了5点耐久的钻石剑，其damage返回的是5，但maxDamage返回的是1561，如果用.withDamage(100)来调用，这把新返回的钻石剑耐久则是1461笔者承认被这个构史参数设计搞破防了。这个案例中笔者只用1枚钻石和钻石剑合成来举例：

import crafttweaker.item.IItemStack;
recipes.addShapeless("repair_with_enchantments_reserved",
    <item:minecraft:diamond_sword>,
    //这里用.anyDamage()以适配所有耐久度的钻石剑
    //用.marked(String nameString)方便我们在配方函数中用ins.<nameString>来调出这个物品
    [<item:minecraft:diamond_sword>.anyDamage().marked("sword"),<item:minecraft:diamond>],
    function(out,ins,cInfo){
        var sword as IItemStack = ins.sword;//把实际放进工作台合成的那把钻石剑用局部变量给存起来
        if(sword.damage==0) return null;//完整的钻石剑不需要修复
        return out.withDamage(max(0,-100+sword.damage));
        //说明：out给出的钻石剑默认满耐久并且不带任何NBT信息
        //这里用withDamage()设定成所需要的、“已经损失的”耐久
        //用min()函数控制合成后耐久不超过钻石剑的最大耐久，避免可能出现的意外情况
        //注意：ZenScript自带的min()/max()只能操作整数int
    },
    null//不需要事件函数
);
    效果：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第74张图片

    然后是事件函数。实际上这里已经是事件的范畴了。关于其触发内容可以是五花八门，其上限完全由玩家的脑洞（和CrT提供的功能）决定，但不论怎么说关键是需要知道“怎样实现”——笔者认为这是个经验问题，多看看别人的案例、自己上手实操一下才能加深理解。

    这里我们接着上个配方的来举例：笔者认为这个配方光消耗钻石不够，还需要玩家付出一点额外的小小代价：掉血！换言之在每次玩家修复钻石剑的时候，都会受到4点（）魔法伤害。实现这个效果需要通过事件函数来操作，实现伤害需要调用IEntity.attackEntityFrom(IDamageSource dmgsrc,float amount)函数，上代码：

import crafttweaker.item.IItemStack;
import crafttweaker.damage.IDamageSource;
recipes.addShapeless("repair_with_enchantments_reserved",
    <item:minecraft:diamond_sword>,
    [<item:minecraft:diamond_sword>.anyDamage().marked("sword"),<item:minecraft:diamond>],
    function(out,ins,cInfo){
        var sword as IItemStack = ins.sword;
        if(sword.damage==0) return null;
        return out.withDamage(max(0,-100+sword.damage)).withTag(sword.tag);
    },
    function(out,cInfo,player){
        if(isNull(player)||cInfo.world.remote)return;
        //说明：这里包含两个判断
        //第一个判断为判断玩家是否为空（毕竟你需要对玩家造成伤害）
        //第二个判断则是一个事件规范的语句
        //写事件的第一时间应该首先考虑：
        //找到各种实体（可以是IEntity/IEntityLivingBase/IPlayer）
        //然后写上if(<IEntity/IEntityLivingBase/IPlayer>.world.remote) return;（注意返回值类型！）
        //当然如果你能直接拿到IWorld对象然后world.remote判断也行，
        //就好比这里用player有空指针的风险，而用cInfo.world.remote更加保险
        //（用if(!entity.world.remote){//code}来写也是可行的）
        //它的作用是保证事件只在服务端（Server）执行而客户端（Client）不执行
        //新手暂时可以不用考虑服务端/客户端的事情
        //想要了解可以看本文中2.4节关于服务端与客户端通信相关内容
        player.attackEntityFrom(IDamageSource.MAGIC(),2.0f);
    }
);
    下一步我们来尝试复刻黄金套索的合成效果，这里以笔者曾经写的一个配方为例，配方中<contenttweaker:experienced_silt>为笔者自定义的一个物品，它需要用Bountiful Baubles模组的幽冥粉，在玩家经验等级不低于21级（或者是创造模式）的时候才能合成，合成时如果经验等级不足则会提示玩家无法合成并拒绝合成，合成后扣除1点经验等级：

recipes.addShapeless("Experienced_Silt",
    //这里的withTag()添加的NBT是Quark模组符文染色效果的NBT，用于给物品添加变色效果
    <contenttweaker:experienced_silt>.withTag({"Quark:RuneColor": 16,"Quark:RuneAttached":1 as byte}),
    [<bountifulbaubles:spectralsilt>],
    function (out,ins,cInfo) {
        //通过cInfo.player获取合成摆放时的玩家对象
        //记得这里先对player判定是否为空
        //然后通过player.xp获取玩家经验等级、player.creative查询玩家是否是创造模式
        if(!isNull(cInfo.player)&&cInfo.player.xp<=20 && !cInfo.player.creative){
            //如果满足上述判断，通过player.sendChat()来给玩家发送一条提示信息
            cInfo.player.sendChat("§4缺少足够的经验等级！至少需要21级才能合成！");
            //拒绝合成
            return null;
        }
        //不满足上述判断，输出合成产物
        return out;
    },
    function (out,cInfo,player) {
        //第一个判断检测玩家是否为空
        //第二个判断让事件默认跳过执行客户端执行，代码规范不解释
        if(isNull(player)||cInfo.world.remote)return;
        //如果玩家不是创造模式则给合成后玩家经验等级减少1
        if(!player.creative)player.xp-=1;
        //给玩家播放音效（没记错的话这个是ZU模组加的，原版CrT没这个效果）
        player.sendPlaySoundPacket("entity.experience_orb.pickup","master",player.position,0.5f,getPitch());    
    }
);
    效果：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第75张图片
JEI里的合成表示

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第76张图片
实际不满足21级则拒绝合成

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第77张图片



    下边，难度升级！我们正式进入工作台上NBT检测与调整的时代！来看几个案例：

    第一个是8个钻石块和一把钻石镐合成无法破坏（目标NBT为{"Unbreakable":1 as byte}）的钻石镐，脚本很简单，只需要把对应NBT用.withTag()函数塞到合成产物里边，甚至不需要用到配方函数和事件函数：

import crafttweaker.item.IItemStack;
recipes.addShaped(
"unbreakable_diamond_pickaxe",
    <item:minecraft:diamond_pickaxe>.withTag({"Unbreakable":1 as byte}),
    [
        [<ore:blockDiamond>,<ore:blockDiamond>,<ore:blockDiamond>],
        [<ore:blockDiamond>,<item:minecraft:diamond_pickaxe>.anyDamage(),<ore:blockDiamond>],
        [<ore:blockDiamond>,<ore:blockDiamond>,<ore:blockDiamond>]
    ],
    null,
    null
);
    这里我们提一个新的要求：这把钻石镐只继承附魔而丢失其他NBT。看样子我们是在对合成产物进行操作，与合成后发生的事无关，所以我们要用到配方函数，事件函数不需要。先把脚本框架搭好：

import crafttweaker.item.IItemStack;
//这里额外导了个IData类的包，为下文做准备
import crafttweaker.data.IData;
recipes.addShaped(
"unbreakable_diamond_pickaxe",
    <item:minecraft:diamond_pickaxe>.withTag({"Unbreakable":1 as byte}),
    [
        [<ore:blockDiamond>,<ore:blockDiamond>,<ore:blockDiamond>],
        [<ore:blockDiamond>,<item:minecraft:diamond_pickaxe>.anyDamage().marked("pickaxe"),<ore:blockDiamond>],
        [<ore:blockDiamond>,<ore:blockDiamond>,<ore:blockDiamond>]
    ],
    function(out,ins,cInfo){
        //Code
    },
    null
);
    好了，难点来了：如何通过操作NBT的形式知道这个物品是否已经附魔、它的附魔数据有哪些、怎么把它放进新镐子里？

    实际上重点已经在1.3节DataMap中介绍的差不多了，无非是操作前先判断是否为空，获得对应的IData对象后把它和不可破坏的NBT合并一下就行。由于这里主要是对合成配方而非在事件中进行操作，所以1.4节相关内容与这里介绍的会存在出入（毕竟配方函数不需要先mutable()后updateTag()）。这里我们已经知道了附魔对应的那个Key叫"ench"，那么我们先试试：

function(out,ins,cInfo){
    var pickaxe as IItemStack = ins.pickaxe;
    if(
        //这里开始我们需要检查附魔，可以用<IItemStack>.isEnchanted来检测，但这里我们选择用NBT的形式来做：
        //首先这个镐子必须得有NBT
        !isNull(pickaxe.tag)&&
        //其次它还得有"ench"这个Key
        !isNull(pickaxe.tag.memberGet("ench"))
    ){
        //然后我们才能用item.tag.ench访问这个NBT，并存起来
        //这里需要注意的是：pickaxe.tag.ench获取的是一个DataList类型的数据
        //和原本我们需要的{"ench":[{"id":xxx,"lvl":xxx}]}不一样
        //所以我们需要稍微改一下，使它成为一个DataMap
        //这样我们才能使用“+”将俩DataMap合并
        //笔者没注意这个DataList和DataMap导致debug整了半天[捂脸]
       var enchNBT as IData = {"ench":pickaxe.tag.ench} as IData;
       //这里我们把附魔相关NBT和不可破坏的NBT合成一下
       return <item:minecraft:diamond_pickaxe>.withTag(
           {"Unbreakable":1 as byte} + enchNBT
        );
    }
    //没有NBT或者没有附魔，那就返回原本的那把镐子
    return <item:minecraft:diamond_pickaxe>.withTag({"Unbreakable":1 as byte});
}
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第78张图片
JEI的配方显示

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第79张图片
实际合成展示

    第二个是为药水指环模组中并不在JEI里的其他药水指环添加合成表。在这个案例之前，我们需要知道药水指环相关NBT内容。

    将速速度的药水指环拿在手里，输入/ct nbt查看其上NBT，可以看到：（这里的Quality的NBT是另一个模组，Quality Tools添加的，和原本药水指环模组无关）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第80张图片
    这里effect这个Key对应的Value恰好就是速度buff的id，这是巧合嘛？我们用其他的药水指环（这里是抗性提升的）也看看，发现effect对应的值同样是对应buff的id，由此我们不妨提出一个猜想：能不能通过这个effect对应的值，达到获得其他（不在JEI表中）的药水指环？

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第81张图片

    这里我们用/give指令来尝试获取“反胃”的药水指环，输入指令为：

1
/give @p potionfingers:ring 1 1 {effect:"minecraft:nausea"}
    然后我们就获得了一个能提供反胃效果的药水指环，装上了之后整个世界开始天旋地转。（这里的“迅捷”是饰品模组Bountiful Baubles提供的前缀）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第82张图片

    实际上，任何注册了的药水效果都可以把id塞进去，比如说这里笔者装了Sakura模组，那用以下指令就可获取其提供的“金色生命”药水指环：

1
/give @p potionfingers:ring 1 1 {effect:"sakura:golden_heart"}
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第83张图片

    然后我们来写配方：一个金西瓜放左上角、四个金胡萝卜围绕着一个基础药水指环合成一个金色生命的药水指环：

//金色生命药水指环的CrT表示为<item:potionfingers:ring:1>.withTag({"effect":"sakura:golden_heart"})
//把它塞到产物中就行了，不需要用到配方函数和事件函数
recipes.addShaped(
    "golden_heart_potionring",
    <item:potionfingers:ring:1>.withTag({"effect":"sakura:golden_heart"}),
    [
        [<minecraft:speckled_melon>,<minecraft:golden_carrot>,null],
        [<minecraft:golden_carrot>,<potionfingers:ring>,<minecraft:golden_carrot>],
        [null,<minecraft:golden_carrot>,null]
    ]
);
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第84张图片

    但这还不够！我们需要给它添加一个催化剂！我们需要用恶魂泪和钻石来合成一枚特殊的钻石，并将其放在配方右下角才能合成这枚戒指，合成后返还一枚普通的钻石。我们先来做这枚特殊的钻石吧，看代码：

recipes.addShapeless(
    "special_diamond",
    <item:minecraft:diamond>,
    [<item:minecraft:diamond>,<item:minecraft:ghast_tear>]
);
    很奇怪，这种合成配方合成出来的仅仅只是一个普通的钻石，仅仅只是“消耗了一枚恶魂泪，把一枚普通的钻石转换成另一枚普通的、一样的钻石”，而根本没有啥“特殊性”。没有特殊性？那我们就自己创建特殊性：自己给这枚钻石挂一个自定义的NBT。啥？你问我怎么自定义？自己取名字然后做成NBT不就行了？这里笔者用{"crafting_times":1 as byte}来当作特殊的NBT，当然Key你可以自己编，前提是：最好别和其他任何可能已经用过的NBT冲突（比如说你不能给钻石剑挂一个Key为"RepairCost"的NBT然后用这个NBT去统计其他信息，因为这是官方设置的、专门用于统计工具附魔惩罚的NBT）。

import crafttweaker.data.IData;
recipes.addShapeless(
    "special_diamond",
    <item:minecraft:diamond>.withTag({"crafting_times":1 as byte} as IData),
    [<item:minecraft:diamond>,<item:minecraft:ghast_tear>]
);
    然后我们再把这枚钻石放进药水戒指配方里，然后用IIngredient类的方法<IIngredient>.transformReplace(IItemStack resultItem)来、就像牛奶合成蛋糕返还桶一样返还一枚普通的钻石：

recipes.addShaped(
    "golden_heart_potionring",
    <item:potionfingers:ring:1>.withTag({"effect":"sakura:golden_heart"}),
    [
        [<minecraft:speckled_melon>,<minecraft:golden_carrot>,null],
        [<minecraft:golden_carrot>,<potionfingers:ring>,<minecraft:golden_carrot>],
        [    
            null,
            <minecraft:golden_carrot>,
            <item:minecraft:diamond>.withTag({"crafting_times":1 as byte} as IData)
                                                        .transformReplace(<item:minecraft:diamond>)
        ]
    ]
);
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第85张图片
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第86张图片

    /ct hand可以看到这个新加的NBT。

    但这还是不够复杂开始丧心病狂了。这里JS提出了一些要求：

    1.特殊钻石拥有多次合成次数（最大为3），如果合成次数达到/超过上限则和恶魂泪无法合成，否则补满合成次数；

    2.每次合成药水戒指让钻石合成次数-1，直至无法合成，此时返回一枚普通钻石；

    3.除了合成药水戒指外，钻石上的其他无关NBT不能动。

    首先我们处理特殊钻石的合成配方，需要用到配方函数，我们可以这样做：

import crafttweaker.data.IData;
import crafttweaker.item.IItemStack;
recipes.addShapeless(
    "special_diamond",
    <item:minecraft:diamond>.withTag({"crafting_times":3 as int} as IData),
    [<item:minecraft:diamond>.marked("d"),<item:minecraft:ghast_tear>],
    function(out,ins,cInfo){
        var d as IItemStack = ins.d;
         
        //这里我们先把预先设计好的NBT给存到preDM中
        var preDM as IData = {"crafting_times":3 as int} as IData;
         
        //然后开始检测钻石上的NBT
        //首先，如果没有NBT，那我们就按照名义产物输出
        if(isNull(d.tag)) return out;
         
        //其次，如果有NBT，那我们得看看到底有没有我们设计的NBT（crafting_times）
        //如果没有，那我们把原本NBT和preDM给合并一下后挂在钻石上然后输出
        if(isNull(d.tag.memberGet("crafting_times"))) return <item:minecraft:diamond>.withTag(d.tag + preDM);
         
        //如果有，那我们看看这个NBT的值是多少
        //这里，如果crafting_times大于等于3，说明“充能”已经满了，那我们就拒绝合成
        if(d.tag.crafting_times.asInt() >= 3) return null;
         
        //否则说明这个钻石可以充能，那我们就用preDM给覆盖一下
        //注意这里“+”运算符，右边的DataMap会覆盖掉前边的相同Key对应的Value
        //这里使用d.tag.update(preDM)也是相同的效果
        return <item:minecraft:diamond>.withTag(d.tag + preDM);
        //当然不放心的话也可以先裁掉后加上去
        //也即d.tag-preDM+preDM
    },
    null
);
    然后是合成药水戒指的配方，这里我们一方面在合成前需要检测钻石是否满足标准，另一方面我们还需要更改这个钻石本身而非合成产物的NBT。前者需要使用配方函数解决，而后者，则发生在“合成后”，适合用事件函数解决。这里JS额外提一下：这里的NBT修改和1.4节及以后章节中介绍的NBT修改已经算是一回事了！

import crafttweaker.data.IData;
import crafttweaker.item.IItemStack;
recipes.addShaped(
    "golden_heart_potionring",
    <item:potionfingers:ring:1>.withTag({"effect":"sakura:golden_heart"}),
    [
        [<minecraft:speckled_melon>,<minecraft:golden_carrot>,null],
        [<minecraft:golden_carrot>,<potionfingers:ring>,<minecraft:golden_carrot>],
        [null,<minecraft:golden_carrot>,<item:minecraft:diamond>.marked("d").reuse()]
        //因为要修改这个钻石的数据，必须得把它设成reuse()以让它在工作台上保留
    ],
    //这里先解决合成判定条件的问题
    function(out,ins,cInfo){
        if(
            //三个判定：是否有NBT、NBT里是否有所需的数据、对应数据是否小于等于0
            //额外提一句：类似于"||"、"&&"这种bool运算符属于“短路运算符”
            //运行时从左往右判定，只要有一个判断true（对于"||"）/false（对于"&&"）
            //那后边的语句不会执行
            //这样可以避免后边语句执行时出现空指针报错
            isNull(ins.d.tag)||
            isNull(ins.d.tag.memberGet("crafting_times"))||
            ins.d.tag.crafting_times.asInt()<=0
        ){
            //三者满足其一则拒绝合成
            return null;
        }
        //否则说明满足条件，允许合成
        return out;
    },
    //然后我们才能解决钻石数据处理问题
    function(out,cInfo,player){
        //事件规范不解释
        if(cInfo.world.remote) return;
        //这里，cInfo.inventory可以获取ICraftingInfo对象
        //它记录了合成表上物品的相关信息
        //经过测试，如果是标记了.reuse()，那我们可以通过mutable()修改这个物品
        //或者使用setStack(int row,int column,IItemStack item)来替换这个物品
        //注意：这里用getStack(int row,int column)时
        //认为左上角那个物品的位置为(0,0)（也就是使用数组的index）
        var d as IItemStack = cInfo.inventory.getStack(2, 2);
        if(
            //依旧是多重判定，排除空指针，笔者认为属于规范问题
            //（虽然已经在配方函数那里提前检测过就是了）
            //最后一个判定对应的数据是否大于零、能够合成
            isNull(d)||
            isNull(d.tag)||
            isNull(d.tag.memberGet("crafting_times"))||
            d.tag.crafting_times.asInt()<=0
        ){
            //几个条件满足其一则......你能拿它有啥办法
            //只能return掉了
            return;
        }
        //然后是，如果对应的crafting_times只有1点，那就把它转换成普通的钻石
        if(d.tag.crafting_times.asInt()==1){
            cInfo.inventory.setStack(2,2,<item:minecraft:diamond>);
            return;
        }
        //否则直接修改这个物品的数据
        //这里的操作和1.4节相同
        //必须强调：在事件中对一个物品（IItemStack）进行修改时
        //需要先mutable()后修改（此操作对修改物品耐久、数量、NBT等数据均必要）
        //这里我们修改NBT，则我们可以用updateTag(IData newNBT)进行操作
        //它的作用相当于把物品原来的NBT取出来（这里叫originalNBT）然后和传入NBT（newNBT）进行update
        //也就是originalNBT+newNBT或者originalNBT.update(newNBT)
        //然后把得到的NBT替换掉原来物品的NBT
        //这里我们需要基于原来的NBT数据来做新的NBT
        d.mutable().updateTag({"crafting_times" : ( d.tag.crafting_times.asInt() - 1 ) as int} as IData);
        //值得一提的是：在使用类似于“a-b”的语句时
        //需要在减号“-”左或右添加一个空格
        //否则垃圾的ZenScript无法识别这个减号
        //笔者被这个点坑过很多次了（捂脸）
    }
);
    这里展示部分成果：（那个带有“经验修补”附魔的钻石已经把它的crafting_times消耗成1了）

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第87张图片
合成前
[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第88张图片
合成后



    然而，这里出现了一个问题：对于这些带有条件的配方，JEI里只是显示这个配方“能这样合成”，但实际玩家摆好了却发现没有满足合成条件，显示无法合成——这是否有点不够人性化？针对这一点，我们来思考一下怎么给出条件：

    1.对于这样一个配方，它没有使用配方函数，合成产物就是给出的那个outputIngredient参数（也叫名义产物）；

1
recipes.addShapeless(String recipeName,IIngredient outputIngredient,IIngredient[] inputIngredientArray);
    2.如果使用了配方函数，那最终合成结果是由配方函数控制，和“名义产物”可以无关；

    3.那我们能不能在名义产物上做点手脚，比如说给个合成提示啥的？

    好想法！笔者实际上就是这么做的（应该可以调用JEI相关的函数接口来额外添加说明，但毕竟这篇教程介绍的是NBT操作和动态信息展示嘛，这里肯定需要用教程内容）。这里给出笔者的解决方案：

    在配方中的名义产物上以tooltip的形式显示提示文字。我们可以根据正文介绍的tooltip函数来添加相关提示（这里笔者只给思路：给名义产物添加某个用于显示的特定NBT，然后tooltip函数检测是否含有这个NBT，有则添加tooltip，没有则返回null；实际输出产物则不要这个NBT），也可以使用下文即将介绍的Display下Lore标签来制作。实际上这里使用后者操作更佳，但鉴于此处尚未介绍到Display和Lore标签，笔者这里会直接给出可用函数，以前文介绍的幽冥粉合成为例：

import crafttweaker.data.IData;
 
//这里把变色NBT给存成一个静态对象方便调用
static colorNBT as IData = {"Quark:RuneColor": 16,"Quark:RuneAttached":1 as byte} as IData;
 
//这个函数是将输入的字符串转化成可以添加到物品上显示的NBT对象（IData对象，DataMap）
function createDisplayNBT(displayStringObj as string) as IData{
    return {"display":{"Lore":[displayStringObj] as IData}} as IData;
}
 
recipes.addShapeless("Experienced_Silt",
    //这里调用两次createDisplayNBT()是为了换行，“+”可以合并NBT
    <contenttweaker:experienced_silt>.withTag(
        colorNBT+createDisplayNBT("§e合成消耗1级经验！§r")+createDisplayNBT("§6需要经验等级至少21级才能合成！§r")
    ),
    [<bountifulbaubles:spectralsilt>],
    function (out,ins,cInfo) {
        if(isNull(cInfo.player))return;
        if(cInfo.player.xp<=20 && !cInfo.player.creative){
            cInfo.player.sendChat("§4缺少足够的经验等级！至少需要21级才能合成！");
            return null;
        }
        //这里需要我们砍掉输出产物的那堆显示NBT
        //先清空NBT，然后再添加上变色的NBT
        return out.withEmptyTag().withTag(colorNBT);
    },
    function (out,cInfo,player) {
        if(cInfo.world.remote||isNull(player))return;
        if(!player.creative)player.xp-=1;
        player.sendPlaySoundPacket("entity.experience_orb.pickup","master",player.position,0.5f,getPitch());
    }
);
    效果：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第89张图片
JEI显示

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第90张图片
实际合成

    然后我们再来看，上边那个我们折腾了很久的特殊钻石的合成配方，我们也可以用这种名义产物的形式给出提示，代码如下：（这里笔者选择不使用自定义函数，直接写那个介绍的NBT）

import crafttweaker.data.IData;
import crafttweaker.item.IItemStack;
 
recipes.addShapeless(
    "special_diamond",
    <item:minecraft:diamond>.withTag({
        "display":{
            "Lore":[
                "§e该合成可以对这个钻石进行充能，使得它可以合成“金色生命药水指环”§r",
                "§e最大充能次数为3次§r"
            ]
        }
    } as IData),
    [<item:minecraft:diamond>.marked("d"),<item:minecraft:ghast_tear>],
    function(out,ins,cInfo){
        var d as IItemStack = ins.d;
        var preDM as IData = {"crafting_times":3 as int} as IData;
        if(isNull(d.tag)) return <item:minecraft:diamond>.withTag(preDM);
        if(isNull(d.tag.memberGet("crafting_times"))) return <item:minecraft:diamond>.withTag(d.tag + preDM);
        if(d.tag.crafting_times.asInt() >= 3) return null;
        return <item:minecraft:diamond>.withTag(d.tag + preDM);
    },
    null
);
    效果：

[1.12.2]简单NBT的操纵与物品动态信息展示（25.5.8更新，基本完结）-第91张图片

    我们也可以对药水指环的配方作出相同的修改，这里笔者就不过多赘述了。

    这里给大家留一个思考题：对于那个挂载了特殊NBT的钻石，怎么显示它的crafting_times？笔者给出提示：要解决这个问题，需要读者阅读整篇教程，然后使用教程中所提到的tooltip function或者是display下Lore标签操作。如果使用的是后者，需要在对crafting_times这个数据进行修改的同时同步修改对应的Lore内容。由于（对于本附加节而言）难度过高且笔者懒得写，这里就不过多介绍了。