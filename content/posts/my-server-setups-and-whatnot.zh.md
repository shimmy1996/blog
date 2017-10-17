+++
title = "新站点架设过程"
lastmod = 2017-10-16T22:44:19-05:00
tags = ["arch-linux", "server"]
categories = ["site-related"]
draft = false
date = 2017-09-25
slug = "my-server-setups-and-whatnot"
+++

## 为何要重新建站？新站建在哪里？ {#为何要重新建站-新站建在哪里}

在忍受了笨重的 Wordpress 三年后（以及 Bluehost 充满2003年设计感的管理面板），我终于决定放弃旧站另起炉灶。我对 Wordpress 博客主要有这些不满：

-   体积庞大，占用很多不必要的服务器空间。
-   没法使用我熟悉的工具来方便地备份。
-   没有良好的命令行界面，而命令行正在成为我做任何事情的首选工具。
-   缺少一些我所需要的基本功能，比如多语言支持。在部分人手里 WordPress 或许极为强大，但是我不想投入时间学习 CSS/js/php ，也不想从那些源头不明的 WordPress 插件商店里下载任何东西。
-   这些 WordPress 主题/插件商店让我想起 Ubuntu 软件中心。
-   WordPress 有很多我用不到的功能，比如用户系统。这用在个人博客上显然是杀鸡用牛刀。

在我确定了自己的需求后，我很容易地就找到了替代品：一个用我所知道的编程语言（或者我愿意学习的编程语言）所实现的快而小巧并带有原生多语言支持的静态站点生成器，那就是 `hugo` 。

至于站点托管服务，我本来考虑使用 github pages 或 netlify 这种简单快速的解决办法，但是考虑到是个人站点，还是 VPS 这种功能强大一些的选择比较合适。而且 github pages 不支持自定义域名的 https ，这对我来说无法接受。我列出了所有比较出名的 VPS 服务提供商，筛出支持 Arch Linux 的部分，最后选择了 DigitalOcean 。由于我想要完全切断和 Bluehost 的联系，我把自己的域名也转移到了 Google Domains 。


## 安装 Arch Linux {#安装-arch-linux}

注意 Arch Linux 其实并不适合用作服务器操作系统。如果一切以系统稳定性为优先，那么选择一个非滚动更新的 Linux 发行版比较合适。我在服务器上用 Arch Linux 主要是因为我在我的所有其他电脑上也都运行 Arch Linux。如果你选择使用 Arch Linux 作为服务器操作系统，最好勤于备份：虽然我还没遇到这种情况，但是常有人抱怨 Arch Linux 很容易被玩坏。


### 安装系统 {#安装系统}

显然我得到的关于 DigitalOcean 支持 Arch Linux 的情报已经过时了，他们已经停止支持 Arch Linux 有一阵子了。好在有 [digitalocean-debian-to-arch](https://github.com/gh2o/digitalocean-debian-to-arch) ，使得在水滴（ droplet ， DigitalOcean 对每个服务器的称呼）上安装 Arch Linux 并不困难。我只需要 [新建一个 droplet](https://www.digitalocean.com/community/tutorials/how-to-create-your-first-digitalocean-droplet) ，通过 `ssh` 登录服务器，并执行：

```sh
# wget https://raw.githubusercontent.com/gh2o/digitalocean-debian-to-arch/debian9/install.sh -O install.sh
# bash install.sh
```


### 系统设置 {#系统设置}

上述安装完成后，我的 droplet 上就有了带有网络的 Arch Linux 。绝大部分的额外设置都可以在 [Arch Wiki](https://wiki.archlinux.org/index.php/Installation_guide) 找到。我并没有想把这篇日志写成完整的教程，所以细节部分最好参考 Arch Wiki。记录在这篇日志里的指令只是做个人记录之用。


#### 系统时钟 {#系统时钟}

同步系统时钟并设置时区。

```sh
# timedatectl set-ntp true
# timedatectl settimezone <Region>/<City>
```


#### 安装基础软件包 {#安装基础软件包}

安装/升级 `base` 和 `base-devel` 软件包。

```sh
# pacman -S base base-devel
```


#### Fstab {#fstab}

生成 `fstab` 。

```sh
# genfstab -U / >> /etc/fstab
```


#### 设置系统语言环境 {#设置系统语言环境}

在 `/etc/locale.conf` 里去掉 `en_US.UTF-8 UTF-8` 的注释，然后运行：

```sh
locale-gen
```

在 `/etc/locale.conf` 里设置 `LANG=en_US.UTF-8` 。


#### 主机名 {#主机名}

编辑 `/etc/hosts` 以加入水滴的主机名：

```sh
127.0.1.1    <hostname>.localdomain <hostname>
```


#### 引导加载程序和 Initramfs {#引导加载程序和-initramfs}

针对英特尔处理器的优化：

```sh
# pacman -S intel-ucode
# grub-mkconfig -o /boot/grub/grub.cfg
```

在 initramfs 里加入 `crc32` 模组，不然可能导致水滴无法启动。编辑 `/etc/mkinitcpio.conf` ：

```sh
MODULES= "crc32 libcrc32c crc32c_generic crc32c-intel crc32-pclmul"
```

重新生成 initramfs 镜像。

```sh
# mkinitcpio -p linux
```


#### Root 用户密码 {#root-用户密码}

你懂的。

```sh
# passwd
```


### 用户设置 {#用户设置}

这是一些让 Arch Linux 使用起来更加友好的设置。


#### 创建用户帐号 {#创建用户帐号}

显然只使用 root 帐号不是什么好主意：

```sh
# useradd -m -G wheel -s /bin/bash <username>
# passwd <username>
```


#### 把用户加入 Sudoer {#把用户加入-sudoer}

编辑 `/etc/sudoers` 并加入：

```sh
<username> ALL=(ALL) ALL
```


#### 以用户身份登录 {#以用户身份登录}

剩下的设置都会以用户身份执行：

```sh
# su <username>
```


#### 软件包管理器 {#软件包管理器}

我一开始使用 `packer` 来同时使用 `pacman` 和安装 AUR 软件包。但是在我了解到其软件安装过程有 [诸多安全隐患](https://wiki.archlinux.org/index.php/AUR_helpers#Comparison_table) 后，我开始改用 `trizen` （ `pacaur` 是另一个较为稳妥的选择，而且在 reddit 上有一个机器人会在所有提到 `yaourt` 的帖子下面安利 `pacaur` ）： `trizen` 会提示用户在安装前检查 `PKGBUILD` ， `*.install` 以及其他代码，而且 `trizen` 是用 Perl 而不是 Bash 写的。想要安装 `trizen` ，先根据 [AUR 页面](https://aur.archlinux.org/packages/trizen/) 通过 `pacman` 安装 `trizen` 所依赖的软件包，然后克隆其 [git 仓库](https://github.com/trizen/trizen) 到本地。进入包含 `PKGBUILD` 的文件夹并运行：

```sh
$ makepkg
```

来编译软件包，

```sh
$ pacman -U trizen-*.pkg.tzr.xz
```

来安装 `trizen`.


#### 常用软件包 {#常用软件包}

在设置完软件包管理器后，就可以大肆安装各种软件了！我的一些必备软件包括 `emacs` （在服务器上我只安装了命令行版本， `emacs-nox=）， =tmux` （可以使用同一个命令行窗口来同时运行多个指令，非常有用）， `zsh` ， `vim` （作快速编辑之用）。


## 安全相关 {#安全相关}

Arch Linux 安装完成之后，我在把网站搬进去之前进行了一些安全方面的设置。


### 使用 `ssh` 安全登录 {#使用-ssh-安全登录}

在本地机器上生成 ssh 密匙：

```sh
$ ssh-keygen -t rsa
```

把 ssh 密匙发送到服务器：

```sh
$ ssh-copy-id <username>@<server>
```

接下来再服务器上编辑 `/etc/ssh/sshd_config` ：

```sh
PermitRootLogin no
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
AllowUsers <username>
```

这些改动会禁止 root 账户登录，禁止使用密码登录，并只允许特定用户通过 ssh 登录。

除此之外，把用于 ssh 的端口（默认为22）改掉也是一个很棒的安全措施。继续编辑同一个文件（请记牢所选择的端口）：

```sh
port <non-std-port>
```

为了让这些改动生效，重启 `ssh` 进程：

```sh
$ sudo systemctl restart sshd.service
```

接下来保留目前的 `ssh` 链接不变并在一个新窗口内尝试建立新链接以确认一切正常（保留原有链接以防设置出错）：

```sh
$ ssh -p <non-std-port> <username>@<server>
```


### 防火墙设置 {#防火墙设置}

`ufw` 作为防火墙非常方便易用。使用 `trizen` 来安装 `ufw` 并开放允许连接的端口：

```sh
$ trizen -S ufw
$ sudo ufw allow <port>/<protocol>
```

如果想要允许 `ssh` 链接，开放 `22/tcp` 或 `ssh` （如果你改掉了默认端口，开放 `<non-std-port>/tcp` ）。其他一些常用的端口有：

| Port      | Usage                 |
|-----------|-----------------------|
| `80/tcp`  | `http`                |
| `443/tcp` | `https`               |
| `143`     | imap 通信             |
| `993`     | imap `ssl` 通信       |
| `25`      | 收取外界来的邮件      |
| `587`     | smtp 通信 （不论有无 `ssl` ） |

回顾已开放的端口并开启防火墙：

```sh
$ sudo ufw show added
$ sudo ufw enable
```

设置自动启动：

```sh
$ sudo systemctl enable ufw.service
```


### 同步服务器时间 {#同步服务器时间}

使用 `ntp` 同步服务器时间：

```sh
$ trizen -S ntp
$ sudo systemctl enable ntpd.service
```

检查时间服务器的状态：

```sh
$ ntpq -p
```


### 设置 PTR 记录 {#设置-ptr-记录}

DigitalOcean 会自动设置 PTR 记录，我唯一需要做的就是将水滴的名字改为绝对领域名称（ FQDN ，帅气但是八成是机翻的译名取自 [Wikipedia](https://zh.wikipedia.org/wiki/完整網域名稱) ），也就是 `www.shimmy1996.com` 。完成这一设置后，我可以通过以下命令来查看设置是否成功。

```sh
$ dig -x <ip_address>
```


## 正式启动服务器 {#正式启动服务器}

在完成以上设置后，就可以为服务器托管网站做准备了。


### 建立网页文件夹 {#建立网页文件夹}

创建一个网页文件夹来放网页文件，一个比较普遍的选择是：

```sh
$ mkdir ~/public_html
```

确认该文件夹（以及用户的 `home` 文件夹）有合适的权限设置。权限设置可以用 `chmod` 修改（一般设成 `755` 就好）。可以在网页文件夹中放一个简单的 `index.html` 来方便测试。


### 安装 `nginx` {#安装-nginx}

用 `trizen` 安装 `nginx` ，并编辑 `/etc/nginx/nginx.conf` 来设立 `http` 服务器（带有 `listen 80 default_server` 设置的部分）：

```sh
server_name www.<domainname> <domainname>
root /path/to/public_html
```

在 `server_name` 这一行可以多列一些网址。如果你想一并架设邮箱服务器的话，一并将邮箱服务器地址列入网址就免去了生成额外的 ssl 证书的麻烦。在这些设置完成后，（重新）开始 `nginx` 并将其设为开机启动：

```sh
$ sudo systemctl restart nginx.service
$ sudo systemctl enable nginx.service
```


### DNS 设置 {#dns-设置}

下一步是为服务器完成 DNS 记录的设置。一开始必须设置的记录有三种： `NS` ， `A` ，和 `CNAME` 。我记下了一些比较常用的记录：

| 记录种类 | 主机名        | 数值           | 用法                      |
|------|------------|--------------|-------------------------|
| `NS`    | @             | DNS 服务器的地址 | 确认用于解析域名的服务器  |
| `A`     | @             | 水滴的 IPv4 地址 | 将主机名重定向到 IP 地址  |
| `CNAME` | www （可以是任何东西） | @              | 将 `www.<hostname>` 设为主机名的 |
| `MX`    | @             | 邮箱服务器地址 | 指定邮箱服务器            |
| `CAA`   | @             | 标明 SSL 证书的授权机构 | 阻止其他授权机构为本站发行 SSL 证书 |

我的域名托管在 Google Domains ，但我的网站用的是 DigitalOcean 的 DNS ，所以我需要在 DigitalOcean 上完成设置并在 Google Domains 里加入 `NS` 记录。

在完成这些设置后，我就可以通过我的域名访问所架设的网站了，不过 DNS 记录通常需要数小时才会完全生效。


### SSL 证书 {#ssl-证书}

[Let's Encrypt](https://letsencrypt.org) 是个非常棒的项目。使用 [`certbot`](https://certbot.eff.org/) 这个工具就可以很方便的生成 SSL 证书。这里向 EFF 和 Linux 基金会的人们致以谢意。生成证书只需要运行 [EFF 网站](https://certbot.eff.org/#arch-nginx) 上所记载的命令即可:

```sh
$ sudo pacman -S certbot-nginx
$ sudo certbot --nginx
```

为了给证书增加一点可信度，我还在 DNS 记录中加了一条 `CAA` 记录，标明 `letsencrypt.org` 是唯一允许给本站 SSL 证书授权的机构。目前 Let's Encrypt 还不支持通配符证书，不过会在 [2018年1月](https://letsencrypt.org/2017/07/06/wildcard-certificates-coming-jan-2018.html) 添加这一支持。由于没有通配符证书，我只好在 `nginx.config` 里加上所有二级域名（这样生成证书的才能够为这些域名提供验证）。


## 下一步？ {#下一步}

在鼓捣了几个小时后（其实大部分时间是在等 DNS 记录扩散），我的新站就上线了。既然选择了运行 VPS，我打算好好发挥它的潜能并架设了自己的电子邮箱。我正在考虑把架设邮箱过程中从各个网站七拼八凑其来的命令行笔记也整理成一篇日志。目前我仍在试图寻找使用 `org-mode` 在 `hugo` 里写多语言日志的最优工作流程。当我确信已经找到一套可以接受的解决方案的时候我会一并写成日志。