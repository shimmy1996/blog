+++
title = "Mastodon 尝鲜"
tags = ["social-network", "mastodon", "arch-linux"]
categories = ["site-related"]
draft = false
date = 2018-02-11
slug = "trying-out-mastodon"
+++

我在 [上一篇日志](https://www.shimmy1996.com/zh/posts/2017/10/22/no-more-disqusting-disqus/) 里提到过，我并不习惯于在社交网络上发帖子。不过不久之前，我偶然撞见了一个很久没有听到过的词：微型博客。不少社交网络站点，例如 Twitter ，本质上还是一个微型博客服务。这种定义下的 Twitter 对我更加具有吸引力：我可以把一些随想，趣事和生活中其他不大适合写进博客的点点滴滴塞进去。但即使如此，我还是发推有一种“向整个互联网发送座标”的感觉。嗯，那么只好自己搭建一个微型博客了。

[Mastodon](https://github.com/tootsuite/mastodon) 就是一个很好的解决方案。对与在最大化网络人格和最小化个人信息泄露之间进退两难的我来说，存储去中心化、但实例之间又紧密相联的 Mastodon 非常理想。如果人们愿意为了好看的头像将个人信息拱手送出的话，搭建一个 Mastodon 实例可以算得上是一个划算的多的买卖。


## 在 Arch Linux 上安装 Mastodon {#在-arch-linux-上安装-mastodon}

我在一番搜寻后，居然没有找到一篇专门针对 Arch Linux 的 Mastodon 安装指南，这有点让我惊讶。不过得益于丰富的软件包，在 Arch Linux 上安装 Mastodon 其实比在 Ubuntu 上更加简单。 [官方指南](https://github.com/tootsuite/documentation/blob/master/Running-Mastodon/Production-guide.md) 上有很详尽的步骤说明，我就简单补充一些只适用于 Arch Linux 的部分。


### 软件包依赖 {#软件包依赖}

我把所有依赖以及相对应的 Arch Linux 软件包都列了出来。完全没有手动克隆 git 仓库的必要。 `npm` 会在安装过程中用到，但没有在官方指南中列出。

| 依赖                                | 软件包                                                                                        |
|-----------------------------------|--------------------------------------------------------------------------------------------|
| `node.js` 6.x                       | [`nodejs-lts-boron`](https://www.archlinux.org/packages/community/x86%5F64/nodejs-lts-boron/) |
| `yarn`                              | [`yarn`](https://www.archlinux.org/packages/community/any/yarn/)                              |
| `imagemagick`                       | [`imagemagick`](https://www.archlinux.org/packages/extra/x86%5F64/imagemagick/)               |
| `ffmpeg`                            | [`ffmpeg`](https://www.archlinux.org/packages/extra/x86%5F64/ffmpeg/)                         |
| `libprotobuf` 和 `protobuf-compiler` | [`protobuf`](https://www.archlinux.org/packages/?sort=&q=protobuf&maintainer=&flagged=)       |
| `nginx`                             | [`nginx`](https://www.archlinux.org/packages/extra/x86%5F64/nginx/)                           |
| `redis`                             | [`redis`](https://www.archlinux.org/packages/community/x86%5F64/redis/)                       |
| `postgresql`                        | [`postgresql`](https://www.archlinux.org/packages/extra/x86%5F64/postgresql/)                 |
| `nodejs`                            | [`nodejs`](https://www.archlinux.org/packages/community/x86%5F64/nodejs/)                     |
| `rbenv`                             | [`rbenv`](https://aur.archlinux.org/packages/rbenv/)                                          |
| `ruby-build`                        | [`ruby-build`](https://aur.archlinux.org/packages/ruby-build/)                                |
| `npm`                               | [`npm`](https://www.archlinux.org/packages/community/any/npm/)                                |

在安装 `rbenv` 后，我需要把 `'eval "$(rbenv init -)"` 加入 `.bashrc` 或 `.zshrc` 中（安装脚本也会提示这一步骤）。


### Mastodon {#mastodon}

创建用户 `mastodon` 并用 `visudo` 把该用户加入 `sudoers` 。

```sh
useradd -m -G wheel -s /bin/bash mastodon
```

接下来就可以克隆 Mastodon 的 git 仓库并开始 [安装 node.js 和 ruby 的依赖](https://github.com/tootsuite/documentation/blob/master/Running-Mastodon/Production-guide.md#nodejs-and-ruby-dependencies) 了。在这一过程中会用到 `npm` 。我在运行 `yarn` 时，遇到了一个 `ENONT` 错误：无法找到 `./.config/yarn/global/.yarnclean` 。手动创建了迷失的文件解决了这个错误。


### PostgreSQL {#postgresql}

除了安装 `postgresql` 软件包外, 我遵循 [Arch Wiki](https://wiki.archlinux.org/index.php/PostgreSQL) 里的步骤初始化了数据库集群：

```sh
$ sudo su postgres
[postgres]$ initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data'
```

在开始并启用 `postgresql.service` 后，我就可以以用户 `postgres` 的身份登录 `psql` 命令行并给 Mastodon 建立用户了（可以使用 `psql` 命令 `\du` 来确认用户列表）：

```sh
$ sudo su postgres
[postgres]$ psql
[psql]# CREATE USER mastodon CREATEDB;
[psql]# \q
```

端口设置可以在 `postgresql.service` 里找到，这会在编辑 `.env.porduction` 时用到。


### Redis {#redis}

和 `postgresql` 差不多，我安装了 `redis` ，开始／启用了 `redis.service` 。端口选择和允许连接的地址都可以在 `/etc/redis.conf` 里设置。


### Nginx 和 Let's Encrypt {#nginx-和-let-s-encrypt}

官方指南已经提供了很详尽的步骤，这里不再赘述。


### `.env.production` {#dot-env-dot-production}

照配置文件里的说明做就可以了。我唯一弄错的地方是连接 `postgresql` 所需的 `DB_HOST` 。在查看 `postgresql.service` 的状态后，我找到了正确的路径， `/run/postgresql` 。


### 计划进程和缓存清理 {#计划进程和缓存清理}

照官方指南做就好。我安装了 [`cronie`](https://www.archlinux.org/packages/core/x86%5F64/cronie/) 来安排 cron 作业。


## 使用感受 {#使用感受}

网页版界面很不错，我很喜欢可以一边刷时间线一边慢慢写嘟文这一点（而不是在被迫停留在页面顶端）。我在尝试了数个 Mastodon 手机客户端后选定了 Pixiv 开发的 [Pawoo](https://pawoo.net/about) 。到目前为止， Mastodon 给我的感觉是一个比 Twitter 更加舒适、更能激发我写东西的平台。我试着推动自己每隔几天就写一条嘟文。虽然目前为止我写的大都是跑步的记录，但我会渐渐丰富我的嘟文内容的。

我很希望看到 Mastodon 对多语言嘟文提供支持。目前我用的办法是给中文和英文的嘟文打上不同的标签，但这么做不仅使嘟文更加臃肿，也使得我的时间线对大部分人来说可读性只有 50% 。可惜的是，目前的诸多社交网络里只看到 [Facebook](https://code.facebook.com/posts/597373993776783) 对此提供了支持。

在网站页脚，我已经把 Twitter 换成了我的[Mastodon 页面](https://mstdn.shimmy1996.com/@shimmy1996) ，随时欢迎各位来访。
