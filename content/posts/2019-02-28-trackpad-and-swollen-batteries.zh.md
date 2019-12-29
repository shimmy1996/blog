+++
title = "触摸板和膨胀的电池"
draft = false
date = 2019-02-28
slug = "trackpad-and-swollen-batteries"
+++

在过去的几周中，我的 Dell XPS 13 触控板右键变得越来越难以使用：整个触控板的右半部分沉入了掌托下方约 2mm 的位置，使右击难以被记录。最初我认为是正常磨损，但事实上是膨胀的电池将触控板的左半部分拱起，导致触摸板变形。 我立即订购了 OEM 部件（ Dell JD25G ），更换了膨胀的电池。XPS 13 （ 9343 ）还算易于维修。固定底板（相当大的一块铝块）的螺钉都清晰可见，并且组件布局允许在打开底板后直接更换电池。 我还将无线网卡（ Dell DW1560 ）换成了 Intel AC9560，其驱动程序在主线 Linux 内核中，方便不少。

更换电池后，触控板恢复了正常。但是，笔记本电脑电池平均在 18 个月左右开始性能下降这一事实仍使我感到非常惊讶。 我这块持续了近四年的电池已经算不错了。较新的笔记本电脑大多使用方形电芯（它们也被用在智能手机中的平板状电池里），而非我的第一台笔记本电脑 Dell Vostro 3750 中搭载那种的圆柱形电芯。电池膨胀一般是由气体积聚引起的，这在带有通风孔的圆柱形电芯中可以避免。有趣的是，可拆卸电池在消费类笔记本电脑中已基本消失 - 即使是大型的台式机替代品（虽然这些笔记本电脑大部分时间都插在电源上）。我能想到的唯一仍然几乎总是具有可拆卸电池的消费电子产品是相机。

这一事件之后，我开始浏览当前市面上的笔记本电脑，因为带有新的四、六核心 CPU 的笔记本电脑是极具诱惑力的升级（我的 XPS 13 配置了 i5-5200U ）。我不怎么喜欢最新版本的 XPS 13（9380），主要是因为端口选择：我目前没有任何 USB Type-C 设备，因此我认为 XPS 13 （9360）上的一个 Type-C 加两个 Type-A 的组合更加优越。除了端口之外，板载无线网卡和全尺寸 SD 卡插槽的移除也使最新型号的吸引力降低。

我还查看了 Panasonic 的 Let's Note 系列笔记本电脑。这些笔记本电脑是可靠而轻便的商务笔记本电脑，并通常配备可拆卸电池和各种端口。如果要是它们没有那么夸张的价格、没有那些丑陋的“ Wheel Pad ”、并配备美式键盘布局，那它们就是理想的笔记本电脑。我最喜欢 2016 年推出的 CF-MX5 系列的外观，但这一系列的性能比起我目前的配置并不会有多大提升。

更为现实的选择包括惠普的 EliteBook ，联想的 ThinkPad T 系列和戴尔的 Latitude 、 Precision 系列。 我否决了 EliteBook ，因为系列所有机器上都有一个巨大的、我可能永远不会使用的专用坞站端口。由于采用了设计功耗 45W 的 CPU， Latitude 5491 似乎有散热问题，但 Latitude 7390 和 7490 看起来都不错，不仅可以禁用 Intel ME 还带有官方 Linux 支持。 ThinkPad T480 几乎满足了我的所有要求，但下一次代的 T490 似乎将不再具有桥接电池系统并仅保留一个 SODIMM 插槽，与 T480s 差不多。

寻找二手机器也是一种选择，但是由于我的主要动机是购买新的四核 CPU ，所以这达不到升级的目的。 有的人认为我们的笔记本电脑的处理性能早已超过我们的日常需求，况且我的 XPS 13 使用时确实感觉不慢，因此我并不急需进行升级。不过我还是列了一下我对理想中笔记本电脑的需求，以备万一。

-   良好的 Linux 驱动程序支持。
-   尺寸小于 15 英寸，旅行重量轻。 XPS 13 将我从 DTR 爱好者转变为 Ultrabook 追随者：能够整天携带笔记本电脑而几乎感觉不到重量非常棒。
-   非 Nvidia 显卡。 AMD 和 Intel 都具有更好的开源驱动程序支持，而且高度依赖 GPU 的任务还有台式机可以分担。
-   合理的电池寿命（ 6 小时或更长时间）和可拆卸电池。
-   不过于激进的接口选择，至少直到所有鼠标和闪存驱动器默认接口都为 USB C 型的那一天。
-   使用标准组件，易于升级，例如内存使用 SODIMM 插槽、无线网卡和硬盘使用 PCIe 等等。
-   不错的触控板。我对笔记本电脑键盘的质量不太敏感，任何质量尚可的键盘我都能接受。不过要是有搭载 ErgoDox 的笔记本电脑就好了。
-   非超高分辨率的显示屏。我对屏幕也不是很挑剔，但是对于这种大小的笔记本电脑来说，采用 4K 分辨率完全是高射炮打蚊子。我通常使用 16：9 比例的屏幕，但不反对尝试其他分辨率比例。