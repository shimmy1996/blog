+++
title = "安装 Gentoo"
draft = false
date = 2019-01-27
slug = "installing-gentoo"
+++

我终于下定决心在 VirtualBox 上尝试了安装 Gentoo（绝对不是因为首页的愿望清单），从而实现了我的终极数字飞升（按照 DistroWatch 排名的话应该是下凡）。

话说回来，安装过程十分顺畅： [Gentoo 手册](https://wiki.gentoo.org/wiki/Handbook:Main%5FPage) 编写的很出色，似乎预料到了所有可能出错的地方并准备好了后备反感。与 ArchWiki 的 [安装指南](https://wiki.archlinux.org/index.php/Installation%5Fguide) 相比，我更喜欢该手册，因为手册还详细介绍了我采取的每一步背后的原因。我甚至觉得，Gentoo 手册实际上是对初学者更友好的，因为它精心汇总了了通常散布在各处的信息，为学习如何驯服你的操作系统提供了一个很好的起点。 此外， Gentoo 手册不仅涉及安装，还包含其他设置一个可用的系统的必要步骤。我将逐步复制我当前的台式机设置，以决定是否值得进行迁移。

我第一次接触 GNU/Linux 操作系统是 Ubuntu 12.04 ：我的一位同学（ vacuuny/A2Clef ）在学校的计算机实验室中安装了它。曾经有一段时间我每隔几天会在各种 Ubuntu 版本之间进行切换。在同时使用 Windows 和 Ubuntu 一段时间后，我在 2014 年完全切换到 Ubuntu 。由于 Ubuntu 上亚马逊广告的猖獗，我尝试了 Arch Linux ，作为 2015 年新年计划的一部分。即使有第二台计算机来查找说明，我也花了相当长的时间来适应新系统。我在旧博客中还曾写到“大概我还没有 get 到 the Arch way ”。但是完全熟悉 Arch Linux 后，我就再也没有回头。

我仍然会不时在 VirtualBox 中尝试其他发行版，但是除了设置过程之外，我从未发现它们与 Arch 相比能够提供多少改进，更不用提 ArchWiki 上极为出色的文档（现在我们有一个竞争者了）。设置好桌面环境后，发行版之间的体验并没有太大区别，但是当我遇到问题并在线搜索如何故障排除时，区别就开始出现了。拥有更多、更新的软件包是 Arch 的另一项魅力。最近，关于 `systemd` 的争议使我开始四处寻找新发行版以进行试用。与其说是因为实际的安全问题，不如说我只是想试试使用不同的初始化系统：在 Ubuntu 下我主要使用图形界面（ `apt-get` 和 `nano` 可能是我很长一段时间里知道的唯二命令）所以并没有什么直观感受，而在我换用 Arch 时， Arch 已经在使用 `systemd` 了。除了 Gentoo ，候选对象还包括 Void Linux 和 BSD 。 Void Linux 有易于使用的安装向导，但我并不感到它有特别吸引我的地方。看看 Gentoo 是否会改变我的想法。
