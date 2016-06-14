---
layout: post
title: "What you should know about Minecraft mods"
subtitle: "基础概念、Git与Github使用简介、许可证(License)简介、反编译与反混淆、持续集成与mod的构建、汉化mod与提交、如何看懂坠毁报告、如何科学地查看mod的源码"
date: 2016-06-11
author: GWYOG
category: Minecraft Mod
tags: Share Minecraft_Mod
finished: false
---

## What you should know about Minecraft mods

作为第一篇博文，我开了一个大坑=_=|||  
在这篇文章中，我将按照个人的一点心得，科普性质地介绍关于Minecraft Mod(1.7.10+)我认为比较重要的一些知识。知道了这些，相信你会对Minecraft Mod有一个更好的认识。  
正如副标题所写的，本文将分为以下几个小节：  
* 基础概念(关于java、Minecraft、Forge等)  
* 许可证(License)概览   
* Git与Github使用简介  
* 反编译与反混淆  
* 持续集成与mod的构建  
* 汉化mod与提交  
* 如何看懂坠毁报告  
* 如何科学地查看mod的源码  
所讲内容将由浅入深，不过如果你对某一个小节很熟悉的话，跳过也无妨。废话不多说了，现在就正式开始这篇教程吧！  

# 一、基础概念  
想要更好地了解Mod，一些关于java，Minecraft，forge的概念必须先要知道。这个部分我将简要的对这些概念做个解释，其中只会涉及“它是什么”，至于内部的原理我就不再赘述了。想要了解这些知识的话，看本编程的书或是找个Forge开发教程学习一下会更好些。  
我会尽量使用Minecraft相关的例子来解释这些概念，相信这会使得这个部分不再那么枯燥，同时也能为后面几章内容做个铺垫。  

----------  
**1.java一些名词的解释**  
**(1) JVM**  
JVM，全称是Java Virtual Machine，翻译过来就叫java虚拟机。  
用过虚拟机的人都该知道虚拟机是干什么的，java虚拟机实际上也差不多。它提供了一个虚拟的环境，让java的代码可以在不同的操作系统上运行。  
毕向东老师曾经举过一个形象的例子，他把JVM比作GBA模拟器(VBA)。正是由于这种模拟器的存在，原本卡带中需要在掌机上运行的程序才能在计算机上运行。JVM充当的就是这样的角色，它是java跨平台的根本保证。同样作为面向对象的语言，C++虽然拥有着出色的效率，但在这点上它就不如JAVA了。  
JVM这个名词在Minecraft中并不少见。比如说在许多启动器中都会有一条“JVM启动参数”，这个参数决定了JVM的配置，对客户端/服务端都非常重要。不过大多数启动器会把JVM启动参数做成可视化的界面让用户选择，因此单机的话一般也用不太到它了。(但这个参数不可以乱配置，不然会提示JVM无法创建而启动不了游戏)  
  
**(2) JRE**  
JRE，全称是Java Runtime Environment，中文名叫Java运行环境。  
我们一般所说的“玩Minecraft前先装Java”中的Java指的就是JRE(除了JRE以外还有JDK，下面会讲)。  
JRE有不同的版本，玩原版Minecraft的话Java6/7/8都可以。但是玩Mod的时候事情就没这么简单了，许多Mod作者可能会使用一些Java8的内容编写代码，或者使用Java8来编译(即把源代码变成Mod的过程)。这些很可能会导致在启动游戏或是加载游戏时Minecraft崩溃。因此如果可以的话，还是尽量用Java8来进行游戏。  
举个简单的例子，1.9.4的Extra Utilities只能使用Java8来玩。  
  
**(3) JDK**  
JDK，全称是Java Development Kit,中文名叫Java开发者工具，是专门给使用Java编写代码的人用的。  
作为玩家，在玩Minecraft时安装JRE就行了；但作为Mod作者，则需要使用JDK。不过在安装JDK的时候，同时会一并安装一个相同版本的JRE。因此，装了JDK后也就不用再装JRE了。  
对于JVM，JRE和JDK，它们是种层层包含的关系，即有JVM<JRE<JDK。

**(4) 编译与.jar文件**  
编译是把人能看得懂的代码转变成机器语言的过程。机器读不懂人类的语言，人类也读不懂机器的语言。  
在Java中，编译的概念稍微有些不同。由于JVM(Java虚拟机)的存在，源代码并不是直接被编译成机器语言，而是变成JVM读得懂的字节码(Byte Code)，再由不同平台对应的不同的JVM将字节码解释为机器语言(这个部分离我们就有点远了)。这个过程保证了Java跨平台的特性，但与此同时却降低了Java的执行效率。
Java的源代码都是存在.java文件中，编译后的字节码存在.class文件中。在mod的.jar文件中，只能看到.class文件，没有.java文件，因此我们需要使用反编译的软件进行反编译才能看到源码(实际上，由于Minecraft对于代码的保护，我们还需要反混淆，不过这些等到后面的小节再谈)。  
mod的.jar文件类似于一个压缩包，里面包括了mod相关的代码(字节码形式)、材质、音效、语言文本等文件。我们可以先简单地把它理解为一个.zip压缩包。如果想要查看其里面的内容，和查看压缩包内文件的方法一样，只需要右击.jar文件，选择使用解压缩软件(比如WinRAR)打开就行了。如果要修改其里面的文件，拖拽外部的文件进来覆盖或者把里面的文件删除就可以了。不过需要注意的是，许多mod的作者很重视对于它们的mod的保护，这样对.jar进行修改的行为很可能违反了他们的许可证(License)。如果不是十分确定，修改后的.jar文件请不要公开发布(包括汉化文件，也请不要自己直接添加进去)。  

----------
**2.开源和闭源**  
软件分为开源软件和闭源软件。  
开源意味着“开放源码”，软件作者会通过某种渠道将源代码公开，比如说托管于GItHub，或是提供源代码的下载链接。但是开源并不意味着你可以自由的使用他的源代码，其他人的使用权限要根据作者提供的许可证(License)来决定，关于这部分的内容我们留到下一个部分再讲。   
闭源则与开源相反，作者不提供供他人获取其源代码的途径。闭源Mod往往伴随着一个比较严格的许可证(License)。
  
----------
**3.原版Minecraft一些概念的解释**  
**(1) Block与Metadata，TileEntity与NBT**  
Block也就是我们常说的方块。但请注意，并不是所有方形的、1x1x1的物体都可以称为Block。  
比如石头就是个Block，在任何情况下，任意的两个石头都是完全一样的。这就是Block的特性，它基本上不能存放个体的相关信息。  
但是有人就会问了，那羊毛呢？虽然它们有那么多颜色，但不都是羊毛吗？    
对，Block虽然不能存放太多个体的信息，但它可以存放一个Metadata，有时我们也称它为子ID。但由于Minecraft内部数据存储机制的原因，这个Metadata只能存放0-15这16个整数，这也就是羊毛为什么只有16种颜色的原因。  
那么，那些可以存放各种数据的方块又是什么呢？原版的箱子，Mod里的各种机器……这种“方块”随处可见。事实上，Minecraft里还有TileEntity，用来存放Block的各种数据。如果需要的话，每个Block的坐标处可以创建一个TileEntity，TileEntity可以被渲染，也可以不被渲染，它是Block摆脱装饰方块、成为多功能的高级方块的基础。  
TileEntity主要有两个功能，一个就是存放各种信息，另一个就是进行动态的渲染。单从原版Minecraft来说的话，存放信息的TileEntity比较多，比如各种箱子、熔炉、铁砧、附魔台等等；进行动态渲染的TileEntity也有，比如活塞。我们这里只介绍TileEntity存放信息的功能。  
Minecraft中的数据是以NBT形式存放的，相信大家都不会陌生(不知道NBT是什么的人先去看看Minecraft中文Wiki)。TileEntity会读取NBT进来，进行更新，再保存NBT。如果有需要的话，我们可以通过一些修改NBT的工具对它进行修改以达到改变它特性的效果，这点之后会讲。不过总而言之，因为NBT存储数据的数量之多，各个方块便因与其绑定的TileEntity而得以“有”它个体的信息，称为了高级方块。  
  
**(2) Item与ItemStack**  
Item指的是抽象的那些物品，比如小麦、苹果或是斧子。然而我们一般所见到的是具体化的物品，称为ItemStack——即可以放在我们背包里的物品。  
ItemStack相对于Item有了更多的东西，比如堆叠数、DamageValue(损害值)、NBT数据。  
堆叠数顾名思义，就是一格中该种Item堆叠的多少，每种Item有个最大堆叠数，比如石头是64，末影珍珠是16，斧子是1(即不可堆叠)。Minecraft有限制任何Item的堆叠数最多是64。  
DamageValue，或者说损害值，会在物品ID后面体现出来，因此有时我们也会将它称为“Metadata”。它和Block的Metadata不同，它是属于ItemStack的，而且可以存放16bit(最多是2的16次方个数)，相比之下，Block的Metadata只有4bit(最多是16个数)。  
NBT数据就是NBT数据，它是Minecraft种数据存放的形式。与TileEntity类似，我们也可以用工具(后面会讲)修改物品(ItemStack)的NBT数据来得到物品特殊的属性。比如我们可以修改剑的攻击力、耐久值等属性。  
  
**(3) Entity**  
Entity的意思是实体，指的是那些出现在Minecraft中的可以移动的东西。Entity包含的内容十分广泛，大体上可以分为以下几类：  
* 无生命实体：比如掉落在地上的物品，或是经验球，又或者是一个矿车，它们都是实体。  
* 动物和怪物：比如牛、羊、猪、村民。又或者是僵尸、骷髅、爬行者。  
* 玩家  
实体有它的碰撞箱，原版Minecraft中使用F3+B可以看到实体的碰撞箱。1.8以后F3+B开启查看后还能看到实体的视线，这也是实体的一大特征。

----------
**4.MCP(Mod Coder Pack)**  
MCP的全称是Mod Coder Pack，它是Forge的基础，在这个部分只对它做个简单地介绍，在后面的“反编译与反混淆”小节我们还会遇见它的。了解MCP的人会知道，MCP说白了就是Minecraft的源代码集。那我们为什么不直接反编译Minecraft得到源代码呢？事实上，由于Minecraft是一款闭源的商业软件，出于对代码的保护，Minecraft对它的代码进行了一次混淆，即把原先有意义的类名、变量名、方法名全部变成了无意义的字母的组合。如果没有MCP的名称对照表，我们根本无法知道这些代码是什么意思。  
有意思的是，MCP最初的创始人Searge已经成为了Mojang的一名MinecraftPC端开发人员。这在某种程度上也显示了Mojang对于Mod存在的默许态度。  
  
----------
**5.Minecraft Forge**  
现在该谈谈Forge了，Forge经过了很长时间的演化，现在主要由3(2)个部分组成。请注意，一般我们说的Forge并不代表它们中的某一个，而是Forge和ForgeModLoader的总称。(虽然1.8以后FML已经并入了Forge)   

**(1) Forge**  
这儿Forge指的是一个基于MCP，面向mod开发者的API(API的概念一会再讲)。通过Forge，开发者可以编写他们自己的mod。LexManos是Forge的创始人。   

**(2) ForgeModLoader**  
ForgeModLoader常常简称FML，它的作者是cpw。FML的作用是使游戏能够加载Mod，并给Mod之间的联动提供了一个平台。在Minecraft版本从1.7.10升到1.8的时候(也是之前微软收购Mojang的时期)，cpw宣布之后将与Minecraft断绝联系(简而言之，弃坑了)，于是LexManos将FML更新到1.8的版本，并入了Forge，包名也从cpw.mods.fml.xxxx变成了net.minecraftforge.fml.xxxx。虽然之后cpw又回来了，不过FML目前仍然在Forge里面。   

**(3) ForgeGradle**   
ForgeGradle的作者是AbrarSyed，它是一个Gradle插件，主要被用来搭建Forge开发环境和构建mod。ForgeGradle的功能十分强大，而且用起来十分方便，后面在“反编译与反混淆”小节我们还会遇到它。  
   
----------
**6.杂项**   
**(1) API**   
API是Application Programming Interface的缩写，翻译成中文就是应用程序接口。它是软件不同部分间相互衔接的约定。正是有了这些API，软件不同部分之间才能互不冲突。在Mod界，主要有两种API。  
一种是面向所有Mod开发者的，供Mod修改Minecraft原本设定或是为Minecraft添加新元素的API。比如Forge或LiteLoader就是这种API。  
另一种是Mod自身的API。为了有更好的兼容性，也为了更多地与其他Mod联动，或是能有更多的附属Mod，Mod作者们一般会制作自己Mod的API供其他Mod作者使用。这些API，比如IC2的API或是BC的API，之于玩家毫无作用，但对于其他Mod作者意义非凡。举一个简单的例子，MineTweaker或ModTweaker之所以可以使用脚本文件修改别的Mod机器的配方，就是因为使用了别的Mod的API；也正因为IC2的API中没有删除机器配方的相关内容，MineTweaker便无法做到比如删除IC2打粉机某个配方的这件看似简单的事情。除此之外，神秘时代TC为什么会有那么多的拓展，为什么那么多Mod会与农业工艺AC有联动，都是因为它们有一个很好的API。  
   
**(2) Dev**   
在Mod界“Dev”这个缩写一般会在两种地方看到，不过据信它们都是“Developer”(开发者)的缩写。   
一种地方就是在各种国外论坛，比如Reddit的/r/feedthebeast，里面随处都能看到个顶着"xxx Mod Dev"红字标签的Mod界大佬。这里的Dev表示他/她是xxxMod的开发者，有可能是原作者，也可能是维护者。除此之外，"Dev Team"这个词也很常见，表示开发者团队。  
另一种地方则是各个Jenkins或是一些论坛的下载链接处。许多Mod，尤其是那些闭源mod，会放出一个“Dev Build”(或者Mod名后跟着一个"dev")，这个版本的Mod是面向其他开发者的，作为玩家千万不要下载使用，不然基本上是要打不开游戏的。
     
(未完待续，以上内容可能有误，日后有空会再做校正的:P)
