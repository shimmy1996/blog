+++
title = "不再使用 Disqus"
lastmod = 2017-12-17T17:40:03-06:00
tags = ["social-network", "security"]
categories = ["site-related"]
draft = false
date = 2017-10-22
slug = "no-more-disqusting-disqus"
+++

不久之前， Disqus 发生了一起 [用户信息泄露事件](https://blog.disqus.com/security-alert-user-info-breach) 。这导致我开始重新考虑评论系统的选择：既然已经架设了自己的博客和电子邮件，也不差一个评论系统。


## 好家伙，坏家伙，丑家伙 {#好家伙-坏家伙-丑家伙}

我对 Disqus 将不同网站的评论统一成一个社交网络的主意抱有比较复杂的看法。我个人使用社交媒体时更多的是作为 “媒体” 而不是社交的工具：这些服务显然不适合发表长篇大论（所以大多社交网站帖子都充斥着链接和长图），而就算是作为发表随感的工具，我也觉得在这些网站上编辑文字不怎么理想（尤其是在手机上）。常年潜水的习惯使得我时常反过来审视自己的社交网站人格：我站在第三者的角度会怎么看这堆转推和赞所构成的怪物。

博客评论则比较不同。我在评论博客时更为放松：这种感觉更接近与博主一对一对话，而不是向整个网络广播自己的座标。社交媒体化的博客评论就不再给我这种感觉。使用 Disqus 的一大潜在好处大概在于能够吸引更多访客，不过我并不想让自己的博客评论区沦为又一个社交网络时间线：高价值的评论并不会因为我的网站不使用 Disqus 而消失（虽然相反的事情时有发生，并不是所有人都会愿意为了评论而注册新的社交网络帐号的）。

接下来就要说到 Disqus 比较丑陋的一面了。且不提在网站上嵌入我自己没有办法控制的 JavaScript 是一大安全隐患，我直到昨天才意识到 Disqus 的访客数据收集是默认启用的。由于我不打算通过博客获得收入，以牺牲访客隐私为代价换取 Disqus 的服务并不值得。除此之外，我还发现 Disqus 评论中的大部分“赞同”都来自于挂着交友网站的僵尸帐号。至于这些僵尸帐号的来源是被盗用的帐号还是水军机器人我就无从了解了。总之我可不想让这些僵尸帐号在我的博客上晃悠。


## 寻找替代品 {#寻找替代品}

我决定自己架设评论系统并尽量避开任何第三方服务。在细心搜寻后，[isso](https://posativ.org/isso/) 和 [staticman](https://staticman.net) 成为了最终的候选者。 Isso 是一个使用 Python 写成的轻量评论服务器；而 staticman 则是一套将评论转换成文本文件并自动加入博客 Github 仓库的 API 。安装 isso 意味着我必须使用我之前一直尽力避免的数据库；使用 staticman 则可以让我的网站保持静态，但是必须依靠 Github 的 API （如果我不自己架设 staticman 的话，还需要 staticman.net 的官方 API ）。虽然保持一个完全静态的站点很吸引人，不过我还是决定先尝试 isso ，看看脱离第三方服务是否值得我花时间鼓捣数据库。

我在寻找评论系统的过程中还发现了一个有趣的替代品：[Echochamber.js](https://github.com/tessalt/echo-chamber-js) 。


## 设置 isso {#设置-isso}

Isso 的官网有很详细的 [说明文档](https://posativ.org/isso/docs/)。我
从 [AUR](https://aur.archlinux.org/packages/isso/) 安装了 isso 并使
用 `systemctl` 启用了它。设置过程出奇的顺利（包括数据库的部分），因为
我的 isso 和博客共用一台服务器，我使用了与默认不同的
[设置](https://posativ.org/isso/docs/setup/sub-uri/) 。我所遇到的唯一
问题在于 `smtp` 。在检查 `postfix` 的运行状态后，我很快发现问题在于
`smtpd_helo_restrictions` ：在停用 `reject_unknown_helo_hostname` 后， isso 就能使用 `smtp` 发送通知邮件了。除此之外，我稍微花了点时间修改 isso 的 CSS 模板。新的评论区看起来不仅更契合博客主题，速度也比 Disqus 快多了。

祝评论愉快！