+++
title = "TIReD：我的评分系统"
date = 2020-07-17T21:30:00-05:00
slug = "tired-a-personal-rating-system"
draft = false
+++

疫情给了我更多时间来回顾想看的电影、节目和书籍，所以我考虑建立一套自己的评分系统，以简化写（如果有的话）评论的过程。


## 指导原则 {#指导原则}

一般的评分标准具有10个或更多级别。这个选择范围在我看来太大了，更不用说那些采用百分制的评分系统了。即使是最常见的五颗星系统也会在引入半颗星后变得繁琐。6分与7分或4.6分与5.1分间究竟有什么区别？较高的精细度在汇总大量评分中可能会有用，但从单个评论者的角度却不是这样。我更喜欢[S1漫区投票](http://s1vote.com/)所采用的方法：让评论者在更少但区别更明显的几个级别中作出选择。

我的身边统计学表明，大多数在线评分都聚集在70％周围，这是个与[预测任何事物的成功率都为40％](https://statmodeling.stat.columbia.edu/2020/06/29/this-one-quick-trick)一样没用的分数。换句话说，大多数评分系统的下半部分都没有得到充分利用：我想不到任何我会给出一星半而非一星评价的情况。此外，我查看评分和评论的目的在于了解高质量的作品，而不是差的那些。一个评分系统仅关注“更好的那一半”就足够了：为什么我在忍受完一个糟糕的节目后，还要给它一个评分？《米其林指南》可没有负一颗星，不是吗？

单纯地用一个分数来概括任何东西的质量都不怎么公平。我想有一个更具表现力的评分系统，以传达我对所喜欢作品不同方面的看法。至少一个恰好对上我的波长的作品应该和一个更具大众吸引力的作品得到不一样的评价。


## 评分方法 {#评分方法}

隆重推出TIReD评分系统！以下主要以动画/电视节目为例，但这种方法中的许多内容也适用于其他艺术形式。每个作品会在以下项目中进行评分，分数之和构成最终评分：

| 项目                                 | 范围 |
|------------------------------------|----|
| 有形（<strong>T</strong>angible）    | 0-2 |
| 无形（<strong>I</strong>ntangible）  | 0-2 |
| 回味（<strong>Re</strong>visit-ability） | 0-1 |
| 酌情加分（<strong>D</strong>iscretionary） | 0-1 |

作品的有形方面包括视觉风格、动画、配乐、CG质量、特殊效果等等。简单来说，作品的制作是否精良。以0分为起点，评分标准为：

-   1分：如果作品总体上吸引人，且维持了稳定的高质量、缺点极少（完美）或利用独特的想法、技术产生了非常棒的效果（高妙）；
-   2分：作品采取的表现手法本身就足以作为观看的理由，即使作品在其他项目中得分全部为0分。

作品的无形方面包括故事本身、角色塑造、剧情节奏、文化背景参考等等。这些特质应该一定程度上独立于作品的具体表现介质而存在：我至少可以同样地享受基于其他艺术形式对作品的忠实再现。评分标准与之前类似，不过对于明显遵循原作内容的翻拍或改编作品，且我看过原作时，评分标准会有所调整：评分将以原作在无形方面的得分-1为起点，视翻拍、改编的质量、难度、效果作出最多1分的调整后舍去超出0-2分的部分。例如，对+2故事的平庸重述最多只能授予+1。翻拍和改编的起点往往比原创容易一些，所以我想以“作品本来可以有多好”为基础作出调整，对“如果我看过原作，还应该看吗”这类问题作出回应，并方便挑选出那些“不必看原作”或“已经超越了原作”的作品。

回味，顾名思义，衡量我是否想重温这一作品。这更多地与我自己的口味或怀旧之情相关：这应该是我会在一个无所事事的午后重新观看的作品。较长的作品在这一指标上会处于比较不利的地位，所以我会将特别令人难忘的片段也计算在内。在翻拍和改编的情况下，这一项目的得分大多数时候都应该仅授与我认为最好的版本。

酌情加分不应该轻易使用，且对已经在其他三个项目中获得满分的作品无效，这使得最高总得分为5分而不是6分。设置这一项目的主要目的是对于我认为作品优点没能很好地被现有评分标准体现出来时作出调整。适用的常见情况包括但不限于：

-   类型天花板：同类最佳；
-   作品的有形和无形方面有着紧密的联系，缺一不可；
-   超越客观条件限制的质量，尤其是对于年代久远或预算紧张的作品。


## 记录格式 {#记录格式}

TIReD评分记录的格式为`X=T/I/Re[+D]`。例如：

-   一个有形得分为1分、无形得分为2分、回味得分为0分、酌情加分为0分的作品会被记录为`3=1/2/0`；
-   一个有形得分为1分、无形得分为0分、回味得分为0分、酌情加分为1分的作品会被记录为`2=1/0/0+1`。

对于我半途放弃看下去的作品（意味着我无法给出评分），会被标记为`DNF`（did not finish）。


## 自我问答 {#自我问答}

我在制定TIReD时的一些想法碎片。

问：应该评定书籍的有形得分？

答：我觉得应该就是看行文本身的质量，例如其能否称为“文学作品”。虽然我对自己鉴定优秀作品的能力并不那么有信心，但是至少获得2分的作品应当比《哈利波特》要好。

问：在其他相关作品中建立的世界设定如何影响评分？

答：世界设定会影响回味得分（系统或世界是否有趣、让我想进一步了解）和无形得分（人物背景是否合乎其行为）。

问：酌情加分的规则是如何确定的？

答：不论分数范围多大，最好的作品始终会获得满分，所以给它们再加分没有什么意义。另一方面，有一些看似不起眼的作品却充满了制作团队、作者的热爱和诚意，还有一些作品存在本身就足以称为爱好者的福音。我想在保证较为客观地评判作品的有形和无形方面得分的前提下表达自己的欣赏，而酌情加分提供了一个途径。

问：在看原作前后，改编或翻拍作品的评分会如何变化？

答：在看过原作后，我将调整改编或翻拍作品的分数。

问：翻译常常会漏掉一些细节，如何处理翻译作品？

答：处理方式与翻拍相同，在看过原作后评分会被调整。

问：“TIReD”（以及分项得分名称）是怎么确定的？

答：第一个有具体名称的项目是回味（revisit-ability），后面的大部分时间都花在玩单词排列和缩写上。由于[Urban Dictionary](https://www.urbandictionary.com/define.php?term=tird)，我差点将这套评分标准命名为“TIRD”。不过，至少不是所有的作品都是\*。😜
