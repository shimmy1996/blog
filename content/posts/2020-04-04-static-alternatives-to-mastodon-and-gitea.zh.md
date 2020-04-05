+++
title = "Mastodon和Gitea的静态替代品"
date = 2020-04-04
slug = "static-alternatives-to-mastodon-and-gitea"
draft = false
+++

就像我当时决定换掉Wordpress一样，我觉得我已经受够了运行Mastodon和Gitea。

跟上Gitea设置选项的频繁改变一直很烦人，而使用Mastodon时，由于系统库版本不匹配（通常是protobuf）造成的依赖软件包出错十分常见。尽管后者不是Mastodon本身的错，但需要额外安装两个软件包管理器（分别用于Ruby和Node.js）来运行一个程序对我来说是很荒谬的。

我于2018年开始在服务器上运行两者：先开始的是作为Twitter替代品的Mastadon，而Gitea则是我后来对微软收购Github的回应。回头想想，相对于我的需求，这有点大材小用：我的git服务器和微型博客的主要用例都是单用户中心的只写任务。这意味着这些内容应以只读形式提供给我网站的访问者，而静态网页正是完美的替代品。

从Mastadon开始说起，我现在使用[twtxt](https://twtxt.readthedocs.io/en/latest/)格式存储和发布我的微型博客。这一格式已经存在了一段时间，但最近在波浪号社区（tildeverse，一系列提供可公共访问的类Unix系统的网站）中开始重新流行。尽管现在有一整个旨在为该格式添加更多功能的社区支持的生态系统（其中包含各种语法扩展和软件），但我发现最基本的的时间戳加制表键加文字的语法已足够满足我的需求。这种即写即忘的形式确实很容易上瘾，尤其是与自己写的命令行客户端搭配使用时（我的版本被恰当地命名为[twixter](https://git.shimmy1996.com/twixter/)）。

至于Gitea，虽然我认为是Github的优秀替代品，但它更适合于多用户协作，而不是作为个人项目的闲置场。我决定直接自己管理git仓库（参阅_Pro Git_的[4.4](https://git-scm.com/book/zh/v2/%25E6%259C%258D%25E5%258A%25A1%25E5%2599%25A8%25E4%25B8%258A%25E7%259A%2584-Git-%25E9%2585%258D%25E7%25BD%25AE%25E6%259C%258D%25E5%258A%25A1%25E5%2599%25A8)和[4.5](https://git-scm.com/book/zh/v2/%25E6%259C%258D%25E5%258A%25A1%25E5%2599%25A8%25E4%25B8%258A%25E7%259A%2584-Git-Git-%25E5%25AE%2588%25E6%258A%25A4%25E8%25BF%259B%25E7%25A8%258B)章），然后使用[stagit](https://git.codemadness.org/stagit/)生成相应的HTML文件。这些由stagit生成的页面已经替代Gitea作为新生的[川陀全系档案馆](https://git.shimmy1996.com/)。

在解决了我在线存在的只写任务需求后，我将继续探索剩下的两个难题：只读（内容消费）和交互（通讯方式）类操作。目前，订阅源和电子邮件是我最好的答案，但是它们仍然不足以涵盖我所有的需求。
