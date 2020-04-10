+++
title = "外太空九号博客"
date = 2020-04-10T09:53:00-05:00
slug = "blog-9-from-outer-space"
draft = false
+++

最近，我在想办法将我的微型博客整合到当前网站里，所以我开始在重新考虑[IndieWeb](https://indieweb.org/)所提倡的一些构想：与希望一切都通过服务器API和JSON响应来动态完成的[ActivityPub](https://activitypub.rocks/)（长毛象（Mastodon），Pleroma等用于互联的协议）不同，IndieWeb社区推荐的不少标准都支持从有正确标记的静态HTML文件中直接生成机器可读的网站源。IndieWeb隐含地依赖的一大核心要素是URI（统一资源标志符）的稳定性，或者从更高的角度来说，站点所有者对域名的控制。由于最近关于.ORG域名的[闹剧](https://www.eff.org/deeplinks/2020/03/members-congress-once-again-urge-icann-save-dot-org)，我逐渐意识到，一个域名昂贵到无法维护（或可能随时被扣押）的未来可能并不是遥不可及的。这可能会严重破坏整个IndieWeb赖以建立的前提，更不用说更常见的链接失效。幸运的是，我觉得[IPFS](https://ipfs.io/)（星际文件系统）有潜力能够解决这两个问题。


## IPFS 速成班 {#ipfs-速成班}

好的，好的，我知道和一些类似的项目，例如[Dat协议](https://dat.foundation/)，[pingfs](https://github.com/yarrick/pingfs)，甚至[Scruttlebutt](https://scuttlebutt.nz/)，比起来，IPFS的名称听起来非常不靠谱（相信我，我开始时也和你一样怀疑），而不少加密货币类创业公司将IPFS与各种首字母缩写词混杂在其营销资料中的事实更降低了它的可信度，但IPFS看起来的确是类似项目中最成熟且易于使用的。以下我对解释IPFS所做的尝试，其信息大部分来自[官方文档](https://docs.ipfs.io/)和[这一演座](https://www.youtube.com/watch?v=HUVmypx9HGI)。如果你对进一步的实施细节感兴趣，[这一来自IPFS Camp 2019的专题讨论](https://www.youtube.com/watch?v=Z5zNPwMDYGg)是一个很好的资源。

简单地说，一条网页链接只是指向某服务器上文件路径的一种花哨说法。就像一般的文件路径一样，服务器下线后，即使坐在同一房间的某人可能缓存了网页内容，该链接也无法被访问。在IPFS中，文件（或数据块）通过与其内容相应的加密哈希值作为地址，并以分布式的方式存储在所有用户群中。这意味着我们不需要中心化的设施来访问文件、可以简单地地验证文件完整性、可以使用P2P共享来加快访问速度、以及以这种方式存储的文件内容是无法改变的。

无法更改文件内容相比我们所得到的好处来说似乎是一个相当昂贵的代价，但是就像计算机科学中的任何其他问题一样，这可以通过添加抽象层来解决。解决这一问题的IPNS（星际域名系统）利用公钥加密来创建可以指向不同文件的不可变地址。IPNS地址基本上就是某个公钥的哈希值。一次IPNS查找包括取回公钥本身、搜索具有相应的私钥签名的指针文件（一个包含IPFS地址的文件）、辨认最新的指针文件、以及重定向到正确的地址几个步骤。要利用IPNS，用户首先要创建一个公私钥对，然后将公钥、想分享的文件和带有签名的指针文件上传到IPFS上。当需要更新时，用户只需要签署并上传新的指针文件就可以了。

IPFS的不少方面都可以在过去的项目中看到踪影，例如BitTorrent（P2P共享）、Plan 9下的[Fossil](https://zh.wikipedia.org/zh-cn/Fossil%5F(%25E6%25AA%2594%25E6%25A1%2588%25E7%25B3%25BB%25E7%25B5%25B1))和[Venti](https://en.wikipedia.org/wiki/Venti)（一次写入的数据块和路径重定向）、和git（哈希树/有向无环图）。但是，IPFS的杀手级功能在于其与现有架构集成的便捷程度。专用的HTTP网关允许从浏览器（而不是IPFS客户端）中直接访问IPFS或IPNS地址，而且IPFS还具有与FUSE（用户空间中的文件系统）的兼容性，这意味着我们甚至可以将整个IPFS挂载为一个只读分区：这一兼容性也让我们能够托管静态网站，但是我必须承认，访问全球（甚至星际）规模的P2P共享盘是个明显更酷的用法。


## 在 IPFS 上架设静态网站 {#在-ipfs-上架设静态网站}

[官方指南](https://docs-beta.ipfs.io/how-to/command-line-quick-start/)已经很好地概述了使用方法。以下是简要概括：

-   运行`ipfs init`和`ipfs daemon`初始化并启动IPFS守护进程。
-   生成网站文件并运行`ipfs add -r <网站文件路径>`将其内容发送到IPFS。输出的最后几行会有路径根目录的哈希值。
-   如果要使用IPNS，请运行`ipfs name publish <网站根目录哈希>`以将IPNS链接定向到刚刚上传的文件夹上。IPNS公钥的哈希值可以通过`ipfs key list -l`获得。
-   在更新或重建网站文件时重复以上两个步骤。由于IPFS寻址过程固有的数据去重功能，该过程的实际开销并不大。对静态站点这一用例来说非常合适：越大的文件（例如照片）更新频率就越低。

完成此操作后，我们就可以从任何专用HTTP网关使用`<网关地址>/ipfs/<网站根目录哈希>`或`<网关地址>/ipns/<ipns地址>`来访问刚才上传的网站了：我们可以使用由IPFS守护进程启动的本地网关（通常位于`127.0.0.1:8080`），也可以使用[公共网关](https://ipfs.github.io/public-gateway-checker/)（由于IPFS文件取回需要在运行网关的服务器上进行，因此使用公共网关有遭受来自服务器所有者的中间人攻击的额外风险）。如果想要架设多个网站，则可以使用`ipns key gen`来生成更多IPNS密钥对，并在执行`ipfs name publish`时通过`--key`选项指定发布地址。

在IPFS[支持IPNS密钥的导入/导出](https://github.com/ipfs/go-ipfs/issues/4240)之前（这有助于我们备份密钥并从多台设备发布内容），[DNSLink](https://docs.ipfs.io/guides/concepts/dnslink/)可用于更方便地维护到站点的网关，但代价是需要拥有域名并信任DNS服务提供者。要想通过 `/ipns/<域名>` 从HTTP网关访问站点，只需为域名加入一下TXT记录：

```text
dnslink=/ipfs/<网站根目录哈希>
```

或

```text
dnslink=/ipns/<ipns地址>
```

例如本站就可以通过[`/ipns/shimmy1996.com`](https://ipfs.io/ipns/shimmy1996.com/)（该链接使用ipfs.io架设的公共网关）来访问。虽然算不上是一个完全没有缺点的办法，但对我来说这是个合理的妥协。

对了，还需要注意的是，正如同使用任何脱机HTML文件时一样，我们需要在生成的网页中使用相对链接。在Hugo中，这可以通过在`config.toml`中加入

```toml
relativeURLs = true
```

来实现。

当然，作为一个P2P网络，IPFS无法取回已经不存在于任何节点上的文件。默认情况下，IPFS客户端会[固定](https://docs.ipfs.io/guides/concepts/pinning/)从本地计算机共享的任何内容：固定内容不会被删除，这确保IPFS上至少有一个可用的副本。我们可以取消固定网站的过时版本，或者，如果需要，在多台设备上查找并固定网站地址以防万一。


## 繁星若尘 {#繁星若尘}

回到IndieWeb的问题上：越来越黑的域名系统和链接失效使基于HTTP的URI的稳定性难以保证。但是，如果我们使用IPFS或IPNS地址作为URI呢？简直完美！我们通过由数学而非FBI警告所控制的地址获得了（理论上可以永久持续下去的）对静态网页的稳定分布式访问。消除拥有服务器的需要还降低了拥有个人网站的门槛。HTTP协议已经存在了29年，而IPFS仅存在了5年。我不知道IPFS在接下来的24年中是否还会继续存在，但是如果是的话，我希望我们会看到一个或许更加混乱，但更加健壮、充满活力、多彩的在线世界。
