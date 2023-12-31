# 骆驼大乱斗
# Luotuo-Fighter: Massive Game Content Generated by LLM

百川期间天梯临时链接 https://luotuo.sirly.cc/

[![Code License](https://img.shields.io/badge/Code%20License-Apache_2.0-green.svg)]()
[![Data License](https://img.shields.io/badge/Data%20License-CC%20By%20NC%204.0-red.svg)]()
[![Huggingface Gradio](https://img.shields.io/static/v1?label=Demo&message=Huggingface%20Gradio&color=orange)](https://huggingface.co/spaces/silk-road/Luotuo-Fighter-GO)

**骆驼大乱斗**是使用语言模型生成游戏内容的战斗文字游戏。

骆驼大乱斗初步在百川Hackathon期间搭建，包括初步的战斗引擎和简单的关卡成长系统。后续会继续完善。骆驼大乱斗的核心思路，是仅在关卡创建或者角色创建的时候，使用语言模型生成大量的游戏内容，而后则尽可能去使用状态机逻辑进行运行。


<p align="center">
    <img src="https://github.com/LC1332/Luotuo-Fighter/blob/main/figures/FeatureList.jpg">
</p>

预计在百川Hackathon期间我们会初步完成骆驼大乱斗的战斗引擎部分。伴随着这个战斗引擎，我们还会做一个自动生成关卡，带有属性成长性质MiniRPG。

同时如果有时间，我们后面再利用LuotuoFighter的战斗引擎去做一个门派经营模拟类的游戏。

StoryTeller是做到一半的原计划，可以实现一些剧情的尝试和游戏。


## 快速开始

可以直接尝试运行以下的colab链接启动项目或者Hugging Face Demo来进行体验


| 名称 |colab链接| 说明         |
|---|---|---|
|骆驼大乱斗天梯| [临时链接](https://luotuo.sirly.cc/) | 百川期间的链接，现场前5+名我们请喝奶茶 |
|骆驼大乱斗天梯| [![Huggingface Gradio](https://img.shields.io/static/v1?label=Demo&message=Huggingface%20Gradio&color=orange)](https://huggingface.co/spaces/silk-road/Luotuo-Fighter-GO) | 百川备用链接，现场前5+名我们请喝奶茶 |
|骆驼大乱斗-魔搭 | [🤖魔搭](https://modelscope.cn/studios/Jyshen/Luotuo-Fighter-GO/summary) | 百川期间魔搭的版本 |
|骆驼大乱斗HF | [![Huggingface Gradio](https://img.shields.io/static/v1?label=Demo&message=Huggingface%20Gradio&color=orange)](https://huggingface.co/spaces/silk-road/Luotuo-Fighter-GO) | 骆驼大乱斗，仅包含战斗引擎和天梯 |
|骆驼RPG | | 一个带上数值成长的RPG版本 |
|骆驼RPG-HF | | RPG版本的Hugging Face Demo |
|关卡生成 | | 用于生成骆驼RPG的关卡的prompt |
|人物生成 | | 一个简易的人物生成代码，已经集成在大乱斗中 |
|战斗视频| [B站链接](https://www.bilibili.com/video/BV1SN41147ae/) | 用DALLE3补全战斗画面后生成的视频 |

## 百川期间天梯规则

+ 点击百川期间天梯的链接（或者备用链接）。尽量使用自己的姓名生成人物

+ 生成人物后可以参与天梯对战，多选几个人随机对战，提升天梯排名

+ 需要和我们说一下这个姓名是你生成的。如果有重名，尽量使用网名

+ 一个名字可以被不同的描述多次生成，天梯对战的时候会自动取Elo战力高的。

+ 截止路演那一天 max(5, 现场参与人数*20%) 我们会请奶茶 所以哪个人物是你生成的一定要和我们说一下我们会记录的 不然到时候找不到人发奶茶 （精确的截止日期按照我们slides提交，也就是路演前一天晚上来算）

Just for Fun


# 效果展示

<p align="center">
    <img src="https://github.com/LC1332/Luotuo-Fighter/blob/main/figures/BattleExample.jpg">
</p>

战斗描述由骆驼大乱斗生成，战斗画面需要用户自行脑补。或者输入DALLE3进行生成。

## 一个简单的战斗的例子

## 骆驼RPG的展示

## TODO List

- [ ] 发布Gradio版本的骆驼大乱斗colab
- [ ] 骆驼大乱斗部署到Hugging Face
- [ ] 骆驼大乱斗-RPG Print版本
- [ ] 发布简单的角色生成和关卡生成的代码
- [ ] 增加躲避、招架的文本描述
- [ ] 增加伤害结算文字化的描述
- [ ] 增加流血、中毒等异常状态，为招式增加异常状态附着比例
- [ ] 增加一个全局的胜率统计系统

---

# 骆驼大乱斗的tech report:
# Luotuo-Fighter: Massive Game Content Generated by LLM

# 引言

随着2023年前后语言模型的蓬勃发展，尤其是ChatGPT, Stanford的Generative Agents，以及OpenAI的Code Interpreter发布之后，语言模型在游戏生成的潜力受到很大的关注。

然而，使用语言模型来代替游戏中的状态机运行，或者是利用语言模型推动游戏的Agents行为，有一定的缺陷。首先，运行语言模型，无论是使用API的方式还是使用本地模型的方式，都有较高的成本。其次，较高的计算资源占用的同时，语言模型的反应速度也慢于传统的基于状态机的游戏形式。最后，语言模型还会遇到输出不稳定的情况。

所以，在本项目中，我们使用了另一种思路，我们希望在一个规定的框架下，利用语言模型去大量生成游戏中的关键内容。包括技能、关卡和一部分数值的设计。

本项目的主要贡献如下:

1、我们建立了一套PVP的战斗引擎。并且玩家可以使用语言模型，批量生成战斗中的人物和技能。并且我们提出的系统，可以根据用户的姓名和对于角色的描述，生成复合用户期望的角色。

2、在确定了战斗引擎的超参数后，我们建立了一套关卡创建系统，这套系统可以根据游戏开发者的需求，创建结构化的关卡游戏数据。结合之前提到的战斗引擎，形成一个大多数要素都是由AI生成的，成长型的战斗游戏。

3、在百川黑客松中，我们预期会完成骆驼Fighter战斗系统，并且提供一个天梯系统，对玩家创建的人物进行排名。同时骆驼Fighter和骆驼RPG的代码都会在 https://github.com/LC1332/Luotuo-Fighter 中发布。


# 相关游戏


+ **姓名大作战** 姓名大作战可以看成非常早期的AIGC游戏。姓名大作战中使用用户输入姓名的base64编码，结合随机数对用户指定姓名的角色进行生成。不过姓名大作战没有平衡性的设计。在骆驼大乱斗的尝试中，我们也希望人物的生成能够简化到用户输入姓名和角色描述，就能够对角色进行完整的生成。所以也可以将骆驼大乱斗看成是姓名大作战的自然语言模型生成版本。

+ **Pokemon** 作为一个经典的，拥有各地区锦标赛的游戏。神奇宝贝对战拥有较好的平衡性。在伤害和命中设计中，我们参考了神奇宝贝的伤害计算公式，并进行了一定的修改。

+ **白金英雄坛说** 白金英雄坛说是一个非常早期，运行在文曲星平台上的文字对战游戏。我们的骆驼大乱斗参考了白金英雄坛说的文字输出型。

+ **人生重开模拟器** 人生重开模拟器是2021年流行过的一个文字游戏。这个游戏给了一个最简单的状态机事件的例子，并且在2021年获得了广泛的关注。

+ **LifeReloaded( Code Interpreter )** 在OpenAI发布Code Interpreter之后，有包括陈财猫等不同的group开始研究使用Code Interpreter进行语言模型进行人生重开模拟的尝试 https://github.com/EmbraceAGI/LifeReloaded 。本项目借鉴了这里的一部分prompt。另一方面，我们希望使用更好的数值模拟的方式，来解决游玩这个游戏的时候数值发散的问题，以及使用生成内容载入到传统游戏框架，来成倍降低AIGC游戏的运行成本。

# 战斗机制设计

## 人物属性

在目前的骆驼大乱斗版本中，有三个最简单的属性。力量STR，体质VIT和敏捷SPD。力量关系到人物的伤害。VIT关系到人物的血量和防御，敏捷关系到人物的出手顺序和命中率。在这里我们希望人物在各个属性的加点的期望收益是类似的，我们先介绍伤害和命中率公式的设计，再介绍如何通过一个简单的实验，来平衡三个属性的收益。

## 伤害计算

假设技能本身的伤害为 $SkillDamage$ , 攻击者的力量为 $STR_1$ ，被攻击者的体质为 $VIT_2$。

那么技能伤害本身的产生的基础伤害为

$$ D_0 = STR_1 * STRStrength + SkillDamage $$

这个伤害会随着进攻方的力量呈现一定的增长，其中$STRStrength$为2。同时，伤害会随着进攻者的攻击力和防御者的防御力的差距发生正负25%的浮动。

$$ D = D_0 * ( 1 + 0.5 * (1/( \textup{exp}(-(STR_1 - VITDecayRatio *VIT_2)/6.0) ) - 0.5 )   ) $$

因为角色的血量已经会随着 $VIT$ 的数值线性增长，所以需要用 $VITDecayRatio$ 去削弱 $VIT$ 的效果，这里 $VITDecayRatio$ 取0.5。

## 命中率计算

每个招式会有一个招式本身的命中率 $SkillHit$

命中率会受到进攻者的速度 $SPD_1$和被攻击者的速度 $SPD_2$ 的影响。实际的命中率为

$$ H = SkillHit +\textup{max}(30, SkillHit) * (-0.5 + 1.0 / (1.0 +\textup{exp}(- (SPD_1 - SPD_2) / 6.0)) )/2.0 ) $$

命中率会受到两者的速度差，产生正负25%的影响。


## 各个系数的确定

我们希望游戏中角色的 $STR$, $VIT$ 和 $SPD$ 处在相对平衡的位置。这里我们研究角色一次攻击，对被攻击者血量的百分比影响，即研究

$$ R = \textup{min}(100,H) * D / HP_2 $$ 

和三个属性之间的关系。

在这里我们规定了角色每个维度的值为0-20之间的整数，并且将角色的三个维度之和选取在30。

我们根据这个规则生成了40个不同的角色，并且让这些角色相互之间使用一个标准威力为50的招式进行相互攻击，并且统计单词攻击对对方血量造成的伤害百分比。

<p align="center">
    <img src="https://github.com/LC1332/Luotuo-Fighter/blob/main/figures/dmg_per_hp.png">
</p>

这里因为角色的血量为 

$$ HP_a * (VIT + HP_b) $$

我们可以通过调整下面这些系数，来平衡 $STR$ , $SPD$ 和 $VIT$ 之间的影响

|属性|正向调整|负向调整|
|---|---|---|
|$STR$| $STRStrength$ , 伤害Logistic乘数 | - |
|$VIT$| 伤害Logistic乘数 | $HP_b$ , $VITDecayRatio$ | 
|$SPD$| 命中率乘数 | $HP_a$ |

通过简易的调整后，我们基本确定了战斗系统中的超参数。命中率乘数和伤害Logistic乘数都为0.5。$HP_b$ 选为10。这个时候期望伤害百分比关于STR和VIT的斜率都接近。

速度的收益影响比较复杂，因为速度更快的角色可以先手攻击。所以这里我们接受了上图中各个速度对于对手的伤害期望都接近这个现象。

## 技能伤害和命中率之间的关系

在Pokemon中，伤害较高的技能，会拥有相对更低的命中率。所以在骆驼大乱斗中，我们也使用命中率去平衡技能的伤害。我们进一步模拟了不同的伤害和命中率组合的情况。

<p align="center">
    <img src="https://github.com/LC1332/Luotuo-Fighter/blob/main/figures/Hit_Damage_Relationship.png">
</p>


这个图给出了Hit-Damage的热图，这里为了方便可视化，我们把数值转化了到期望扣血=14%的距离。可以看到在给定一个扣血比例下，我们可以找到一条伤害-命中率的等高线。在实际的骆驼大乱斗中，我们也使用总期望相同的招式伤害-damage组合。

需要说明的是，我们这里尽可能平衡了属性和技能伤害的数值，但是我们并没有进一步去搜索在这一设定下人物属性和技能的最优组合，比如非常容易想到的是，玩家可以创建速度较高的角色，使用更高伤害的技能，利用速度值来弥补命中率的缺陷。或者建立一个高攻高防但是速度较低的角色，使用更容易命中的技能。或者取一些中间的状态，这些应该都是可行的。

当然，在后续的版本中，我们还希望招式能够附带上一定小概率的流血、破甲、中毒等特殊状态，不论是Pokemon还是姓名大作战，都有这些特殊状态的设计，来进一步增加游戏性。并且一个更好的游戏系统，应该对各个参数和胜率之间的影响进行统计，从而动态去调整各个参数的收益，产生一个更为平衡的AIGC的游戏。这些都可以在后续的研究中进行探索。

# 利用语言模型生成人物

在拥有基础的战斗系统之后，本项目的关键是玩家有较高的自由度去设计或者创建属于自己的人物。我们希望玩家可以用简单的形式，即只输入姓名和简单的描述，就创建出一个人物。同时，如果玩家对人物的细节有更高的要求，也可以在描述中给出更详细的内容。

## 根据人物描述生成详细的技能

在骆驼大乱斗中，我们希望玩家可以用一个姓名和一个最简单的描述就能够创建人物。所以我们首先使用这样的prompt来对玩家的描述进行扩写

```js
我的名字叫“{Role_Name}”，假设我是一个武侠中的游戏角色，{Description}。请基于我的名字和门派，先为我设计2套武功和一套身法或者招架。

再为每套武功设计4个对应招式的名字。
```

在这里语言模型会根据玩家的名字和描述，进一步生成更详细的角色描述。同时会确定角色每个武功中4个招法的名字，下面是这个prompt输出的一个例子

```python
role_name = "黄蓉"
description = "丐帮帮主黄蓉，擅长打狗棒法和落英神剑掌，身法为逍遥游。"
```

这个时候ChatGPT的输出为

```
作为丐帮帮主黄蓉，您擅长打狗棒法和落英神剑掌，身法为逍遥游。以下是为您设计的两套武功和一套身法（招架）：

套路一：打狗棒法
- 武功一：打狗棒法是丐帮的绝技，以棒法为主要攻击手段，威力巨大，灵活多变。
- 武功二：落英神剑掌是黄蓉自创的剑掌技法，结合了剑法和掌法的特点，攻守兼备。
- 身法/招架：逍遥游是黄蓉独特的身法，轻盈灵动，能够迅速躲避敌人的攻击。

套路二：落英神剑掌
- 武功一：落英神剑掌是黄蓉的拿手绝技，以剑掌相结合，攻击迅猛而准确。
- 武功二：打狗棒法是黄蓉在丐帮学习的棒法，以棒为主要武器，威力强大。
- 身法/招架：逍遥游是黄蓉的身法，轻灵敏捷，能够迅速躲避敌人的攻击。

以下是每套武功对应的四个招式的名字：

套路一：打狗棒法
1. 狗啸山林：以棒法挥舞如狗啸声，迅猛有力，攻敌要害。
2. 棒影重重：以迅猛的棒影覆盖敌人，让对手难以躲避。
3. 狗尾续貂：以迅捷的棒法攻击对手的下盘，削弱其防守。
4. 狗急跳墙：以快速的棒法攻势，迫使对手无处可逃。

套路二：落英神剑掌
1. 落英如雨：以掌法如落英般飘逸，攻敌时如雨点般密集。
2. 神剑破空：以剑掌相结合，剑气激荡，破空而出，攻敌要害。
3. 飞花流水：以灵动的剑掌连绵不断，如水中飞舞的花瓣。
4. 剑指苍穹：以剑掌合一，指向天空，释放出强大的攻击力。

请注意，以上是根据您的名字和门派进行的设计，如有需要，您可以根据角色的特点和背景进行进一步的调整和发挥。
```

当然，因为ChatGPT的训练数据中，对中文武侠数据不是很熟悉。在这次百川比赛中，我们也会尝试一下在这一步使用百川来生成看看。另外在这一步中，我们允许语言模型使用非结构化的输出，这样可以把模型的生成温度调高，使得模型产生更多样化的生成结果。

## 根据人物描述总结成格式化数据


在让语言模型进行"发散性思维"，生成多样化的详细描述之后，我们希望语言模型可以进一步把详细描述，转化为游戏中可以载入的结构化数据。

```js
我正在为我的武侠游戏设计人物。给定一个人物和这个人物的背景描述(可选)

我希望你根据这个人物的背景和名字，为我生成这个人的资料，以json的格式输出，包括

description: 这个角色的背景等信息
weapon: 这个人使用的武器种类，
major_martial_art: 这个人的主要武学
major_movements: 这个人主要武学的4个对应招式的名字。招式的名字需要尽可能和major_martial_art有关。movements的名字尽量不要使用重复的文字。可以使用形象的动作或者是一些动物的比喻。
secondary_movements: 这个角色的次要武学
secondary_movements: 这个人次要武学的4个对应招式的名字。招式的名字需要尽可能和secondary_movements有关
footwork_and_body_method: 这个角色的轻功或者防御功法

example_input:
name: 令狐冲
description: -
suggestion:-

example_output:
{
        "name": "令狐冲",
        "description": "华山派的大师兄",
        "weapon": "剑",
        "major_martial_art": "独孤九剑",
        "major_movements": ["独孤九剑破剑式","独孤九剑总诀式","独孤九剑破气式","独孤九剑破刀式"],
        "major_damage": 70,
        "secondary_martial_art": "华山剑法",
        "secondary_movements": ["有凤来仪","飞沙走石","百丈飞瀑","青山隐隐"],
        "secondary_damage":80,
        "footwork_and_body_method": "华山身法",
        "STR":10,
        "VIT":3,
        "SPD":20
}

input:
name: <role_name>
description: <description>
suggestion:<detailed_description>
```

这个时候我们使用one-shot的in-context learning，把之前生成的详细描述转化为角色的属性。由于大多数信息在前一步已经由详细描述生成，在这一步主要负责将之前的非结构化数据转化为结构化的游戏数据。

## 根据技能名称实现战斗描述


<p align="center">
  <img src="./figures/YingXiong.png" height="320">
</p>

为了进一步凸显文字游戏的特性，这里我们希望像文曲星平台的《白金英雄坛说》一样，使用文字去进行招式的描述。所以在这里我们需要将之前生成的招式名字，变为战斗的描述，同时还要考虑招式命中和非命中的情况。

```
现在我希望你根据武侠小说中的武功和招式的名字。为招式补全合理的description和missing描述。
在description的描述中，player可以击中目标，可以有兵器撞击声响等描述。
在missing描述中，player并不一定击中目标，不可以有击落、击中、夺去这样的描述
在用[player]代表技能的使用者，用[target]代表技能的目标。结果需用合理的jsonl格式展示，下面是一个例子。

example input:
martial_art_name = 华山剑法
{"招式":"百丈飞瀑"}
{"招式":"百鸟朝凤"}
{"招式":"青山隐隐"}
{"招式":"飞沙走石"}

example output:
{"招式":"百丈飞瀑","description":"[player]翻身回剑，剑诀斜引，一招“百丈飞瀑”，剑锋从半空中直泻下来","missing":"[player]一咬牙，翻身回剑，剑诀斜引，一招“百丈飞瀑”，剑锋从半空中直泻下来"}
{"招式":"百鸟朝凤","description":"[player]长剑一起，使一招“百鸟朝凤”，但见剑尖乱颤，霎时间便如化为数十个剑尖，罩住[target]中盘","missing":"[player]使一招“百鸟朝凤”，但见剑尖乱颤，霎时间便如化为数十个剑尖，向[target]飞去"}
{"招式":"青山隐隐", "description":"[player]吸一口气，长剑中宫直进，剑尖不住颤动，剑到中途，忽然转而向上，乃是一招“青山隐隐”，端的是若有若无，变幻无方。","missing":"[player]吸一口气，长剑中宫直进，剑尖不住颤动，剑到中途，忽然转而向上，乃是一招“青山隐隐”，端的是若有若无，变幻无方。"}
{"招式":"飞沙走石", "description":"长剑挺起，使一招“飞沙走石”，内劲直贯剑尖，寒光点点，挡的一声，击中的[target]胸口。","missing":"长剑挺起，使一招“飞沙走石”，内劲直贯剑尖，寒光点点，直向[target]胸口刺去。"}


input:
martial_art_name = <matrial_art_name>
{"招式":"<movement1>"}
{"招式":"<movement2>"}
{"招式":"<movement3>"}
{"招式":"<movement4>"}
```

同样我们发现one-shot的in-context learning可以较好的解决这个问题。由此我们就完成了所有角色描述数据的生成。

在百川Hackathon的过程中，我们首先完成了角色攻击数据的生成。身法和招架也可以用近似的道理去完成。

## 其他模态的考虑

其实原则上来说，一个简易的武侠战斗动画也是可以被生成的，比如可以收集大量的武侠动作的pose轨迹，然后生成一个剪影型的招法动画（类似太吾的战斗效果）。当然黑客松的时间较为有限，这个有空或者有合适数据的时候，可以拿来尝试一下。

## 骆驼大乱斗的实践

在完成战斗数值和战斗描述的基本系统后，我们已经初步获得一个可以进行玩家角色创建——战斗的系统。在这个前提下，我们在百川Hackathon中上线了一个基础的天梯挑战系统。如果读这篇报告的时候你在百川Hackathon的现场，可以在[快速开始](@快速开始)中找到天梯的链接，在14日晚之前，我们会进行天梯排名，Elo前几名将在15日路演的时候进行奶茶的发放。


<p align="center">
    <img src="https://github.com/LC1332/Luotuo-Fighter/blob/main/figures/BattleLogs.jpg">
</p>

上图展示了几次用户生成的战斗过程。

# 游戏关卡的自动生成

有了基础的战斗系统之后，我们可以考虑在战斗系统中，叠加更多的游戏模式。比较容易想到的是增加一些数值养成的系统，或者去模仿了不起的修仙模拟器(or RimWorld那样)，去制作一些养成类的游戏。在百川Hackathon期间，我们先完成了一个带有RPG成长的关卡系统。

## 游戏 as 有向图状态机

在这里我们参考了一些游戏，包括人生重开模拟器的设计。并且在前期试错的时候我们也尝试了构建人生重开模拟器这样的应用

<p align="center">
    <img src="https://github.com/LC1332/Luotuo-Fighter/blob/main/figures/LifeReloaded.jpg">
</p>

在类似这样的一个事件推进的游戏中，游戏的状态（除掉玩家属性、道具的部分）可以被描述成一个有向图。每个事件之间可以通过有向图来进行连接。每个事件是一个节点。比如在[这个repo](https://github.com/EwenQuim/renpy-graphviz)中，甚至可以找到可以将Renpy游戏引擎脚本可视化为有向图的代码。

<p align="center">
    <img src="https://github.com/EwenQuim/renpy-graphviz/blob/master/data/DDLC_extract.png">
</p>

所以在这里，我们也将骆驼大乱斗的RPG版本设计为一个简单的有向图。


## RPG游戏中关卡节点的设计

在生成RPG关卡数据的时候，我们先生成节点数据。在节点上，我们会考虑发生战斗，我们把敌人的STR，SPD和VIT的和合并称为一个敌人的战斗力。当一个节点敌人战斗力为0时，则不会发生战斗。并且每个节点会有对应的属性奖励信息。

在这里我们使用了较为保守的设计，即每个节点的奖励和战斗力我们是预先设置的。这样可以防止在完全LLM生成游戏中观察到的数值发散的现象。

```
我正在设计一个基于图论的rpg游戏。请为我游戏中的节点补充描述，我游戏中人物的属性只有str, spd和vit（分别代表力量 速度和防御）
对于节点数据，根据事件名，敌人的战斗力（0表示没有战斗）以及对应的奖励，补充关于这个节点的描述。

例子输入:
节点数据
序号|事件名|敌人战斗力|奖励|
1|初始节点|0|-
2|获取新手装备|0|str+1,spd+1,vit+1

例子输出:
节点数据
序号|事件名|敌人战斗力|奖励|描述
1|初始节点|0|-|你转身到了异世界成为勇者，得知了击败魔王的任务
2|获取新手装备|0|str+1,spd+1,vit+1|村长表示村子里有一些基础的装备，可以使得你的属性获得基本的提升。

输入:
节点数据
序号|事件名|敌人战斗力|奖励|
1|初始节点|0|-
2|力量之路|0|str+1
3|敏捷之路|0|spd+1
4|普通力量怪|15|vit+1
5|普通敏捷怪|15|spd+1
6|精英力量怪|22|vit+1, str+1
7|普通敏捷怪|16|spd+1
8|普通力量怪|23|str+1
9|精英敏捷怪|25|spd+1, str+1
10|试炼|28|str+1,spd+1,vit+1
11|boss守卫|30|str+1,spd+1,vit+1
12|boss|35|-
```

在这一步的生成中，语言模型会初步生成关卡的描述。这个关卡描述是类似这样的

```
1|初始节点|0|-|你来到了一个神秘的世界,开始了你的冒险之旅
2|力量之路|0|str+1|你选择了提升力量的道路,在训练中你的力量将得到提升
3|敏捷之路|0|spd+1|你选择了提升敏捷的道路,在训练中你的速度将得到提升
```

当然，这个关卡描述并不是完全令人满意，在完成边的描述设计之后，我们将会展示怎么将这里的描述进一步anology成特定的风格。


## PRG游戏中边的设计

在很多游戏中，都会给予玩家一定的路径选择，比如上古卷轴五中，通过新手山洞之后有力量之石盗贼之石的选择，不同的选择将会将玩家引导到不同的节点，这个时候我们需要为边给出描述。我们使用这个描述，来提供给玩家让玩家进行选择。

```
我正在设计一个基于图论的rpg游戏。我已经完成了游戏中的节点描述

请为我进一步补充边信息中，场景转换的信息描述

对于边数据，补充这个边是在哪两个节点之间进行了转化，并且补充转换的时候，向用户展示的transition选项的文字描述

当同一个节点可以向不同的节点进行转化时，对应的描述应该有所不同。

例子输入:
节点数据
序号|事件名|敌人战斗力|奖励|描述
1|初始节点|0|-|你来到了一个神秘的世界,开始了你的冒险之旅
2|体质之路|0|vit+1|你选择了提升体质的道路,在训练中你的体质将得到提升
3|敏捷之路|0|spd+1|你选择了提升敏捷的道路,在训练中你的速度将得到提升


边:
Source|Target
1|2
1|3

例子输出:
Source|Target|Events|Transition
1|2|初始节点->体质之路|选择体质之路，这条路将帮助你成为一个强壮的骑士
1|3|初始节点->敏捷之路|选择敏捷之路，如果你偏好盗贼或者刺客，这条路将是你的选择

输入:
节点数据:
序号|事件名|敌人战斗力|奖励|描述
1|初入门派|0|-|你踏入江湖,加入了一个门派开始修炼
2|力量型普通怪|15|str+1|你遇到了一个15战力的力量型普通怪物,通过战胜它你的力量获得了提高  
3|挑战力量型精英怪|20|str+3|你挑战了一个20战力的力量型精英怪物,艰难取胜后你的力量大幅提升
4|普通怪|16|-|你遇到了一个16战力的普通怪物,它的战斗对你来说并不简单
5|敏捷型普通怪|17|spd+1|你遇到了一个17战力的敏捷型普通怪物,战胜它你的敏捷得到了提升
6|敏捷型精英怪|21|spd+3|你挑战了一个21战力的敏捷型精英怪物,取胜后你的敏捷大幅提升  
7|普通怪|18|-|你遇到了一个18战力的普通怪物,需要花费一定力气打败它
8|体质型普通怪|19|vit+1|你遇到了一个19战力的体质型普通怪物,战胜它你的体质获得提升
9|体质型精英怪|21|vit+3|你挑战了一个21战力的体质型精英怪物,艰难取胜后你的体质大幅提升
10|最终的试炼|25|-|这是最后的试炼,只有战胜25战力的怪物才能通过

边:
Source|Target
1|2
1|3
2|4
3|4
4|5
4|6
5|7
6|7
7|8
7|9
8|10
9|10
```

在骆驼大乱斗的RPG版本中，边被简化为任何条件都可以转移。而在人生重开模拟器或者博德之门中，边是有一定的附加条件的。比如要求特定的属性维度超过一定的点数。如果想要创造更复杂的节点图像，可能还需要进行更深入的研究。

## 将节点和边anology到特定风格的故事中

这个时候我们可以使用语言模型的anology能力，将描述转化为特定风格的描述

```
我正在将我的游戏转化为北欧神话风格

注意这个表中的描述

序号|事件名|敌人战斗力|奖励|描述
1|初入门派|0|-|你踏入江湖,加入了一个门派开始修炼
2|力量型普通怪|15|str+1|你遇到了一个15战力的力量型普通怪物,通过战胜它你的力量获得了提高  
3|挑战力量型精英怪|20|str+3|你挑战了一个20战力的力量型精英怪物,艰难取胜后你的力量大幅提升
...
9|体质型精英怪|21|vit+3|你挑战了一个21战力的体质型精英怪物,艰难取胜后你的体质大幅提升
10|最终的试炼|25|-|这是最后的试炼,只有战胜25战力的怪物才能通过

我希望描述更接近武侠风格，

并且不要使用"普通力量怪"这样直白的名字，而是以"巨狼芬尼尔"这样更北欧神话风格的名字来标记每个敌人

将boss改名为某个家族首领的名字。并且保持各个描述之间的风格一致性

请根据这些规则为我重新生成这个表格
```

这个方式是非常有效的，这个prompt的输出如下

```
序号|事件名|敌人战斗力|奖励|描述
-|-|-|-|-
1|踏上英灵殿|0|-|你来到英灵殿,开始了成为英雄的旅程
2|北方食人妖|15|str+1|北方食人妖芬尼尔持有神秘战斧,如果打败它,你的力量将得到提升
3|挑战巨龙法夫纳|20|str+3|恶龙法夫纳拥有无穷力量,如果你能打败它,你的力量将大幅提升
...
9|神圣武士泰尔|21|vit+3|神圣武士泰尔拥有神力,如果你能战胜他,你的生命力将大幅提升
10|最后的试炼|25|-|这是成为英雄的最后试炼,只有打败25战力的对手才能通过
```

这个时候我们就可以用转化后的节点和边数据，输入到骆驼大乱斗-RPG中。

## 骆驼大乱斗-RPG

这里给出骆驼大乱斗-RPG的效果


【等一个图】


# 结论



# 附录: 使用的prompt