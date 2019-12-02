+++
title = "是 Spam 还是 Ham"
draft = false
date = 2017-10-14
slug = "spam-or-ham"
+++

遵循之前的计划，我打算将设置邮箱的过程记录下来。设置邮箱大部分地方都有教程，不过我需要添加不少额外的安全设置来避免邮件被扔进垃圾箱。


## 搭建邮件服务器 {#搭建邮件服务器}

我八成是写不出比 [这篇教程](http://www.netarky.com/programming/arch%5Flinux/Arch%5FLinux%5Fmail%5Fserver%5Fsetup%5F1.html) 更好的步骤说明的，所以我就在这里把我额外需要的一些设置记录下来。


### 设置 DNS 记录 {#设置-dns-记录}

在开始搭建邮箱之前，我需要设置 DNS 记录。我为我的邮件服务器地址设置了一条 `A` 记录，并用一条 `MX` 记录来标明所使用的邮件服务器。


### 创建 `Maildir` {#创建-maildir}

在设置好 `postfix` 之后，我需要手动创建 `Maildir` 文件夹并赋予其合适的权限：

```sh
$ mkdir -p /home/<username>/Maildir/{cur,new,tmp}
$ chown <username> /home/<username>/Maildir/{,cur,new,tmp}
$ chmod 0755 /home/<username>/Maildir/{,cur,new,tmp}
```


### SSL 证书 {#ssl-证书}

我没有使用 `dovecot` 自带的证书生成器，而是直接沿用了我网站的 SSL 证书。我将邮件服务器地址加入 `/etc/nginx/nginx.conf` 中的 `server_name` 下，并用 `certbot` 生成了合适的证书。在这之后，我修改了 `dovecot` 的设置文件 `/etc/dovecot/conf.d/10-ssl.conf` ：

```sh
use_ssl = yes
ssl_cert = </path/to/fullchain.pem
ssl_key = </path/to/privkey.pem
```

我也在 `postfix` 上用了这个证书。需要注意的是 `dovecot` 和 `postfix` 都需要用 `root` 账户运行才会有权限读取证书。


### 邮件客户端 {#邮件客户端}

我使用 Thunderbird 作为邮件客户端。收取邮件时，我使用 SSL/TLS，而发送邮件则使用 STARTTLS。


## 安全措施 {#安全措施}

设置好邮件服务器后，我试着发了几封邮件，不过发现都被 gmail 扔进了垃圾箱。似乎 gmail 最近添加了显示邮件安全检查状态的功能（在 gmail 中点击“查看原件”即可看到）。这些检查包括 SPF ， DKIM ，和 DMARC 。由于我的邮件服务器没有通过最基本的 SPF 检查，所以我的头像显示为一个标着问号的八边形。为了避免邮件被扔进垃圾箱，我进行了一系列安全设置以保证我的邮件服务器能通过网上邮箱安全测试平台（我使用的是 [intodns](https://intodns.com) 和 [mxtoolbox](https://mxtoolbox.com) ）的考验。


### 发件人策略框架（ SPF ） {#发件人策略框架-spf}

SPF TXT 记录标明了某一域名所承认的邮件服务器地址。在我的情况下，我只会用到 MX TXT 记录中所提到的邮件服务器，所以我的 SPF TXT 记录就是：

```sh
v=spf1 mx -all
```


### 域名密匙邮件认证（ DKIM ） {#域名密匙邮件认证-dkim}

我使用 `opendkim` 来为邮件署名，以验证邮件的确来自于我的服务器。在安装了 `opendkim` 软件包后，我遵循 [Arch Wiki](https://wiki.archlinux.org/index.php/OpenDKIM) 里的步骤完成了设置。首先，复制设置文件模板 `/etc/opendkim/opendkim.conf.sample` 到 `/etc/opendkim/opendkim.conf` 并编辑（端口可以随意选择）：

```sh
Domain                  <domainname>
KeyFile                 /path/to/keys.private
Selector                <myselector>
Socket                  inet:<dkimsocket>@localhost
UserID                  opendkim
Canonicalization        relaxed/simple
```

接下来，在设置文件所指定的密匙文件路径下（默认为 `/var/db/dkim/` ），生成密匙：

```sh
$ opendkim-genkey -r -s <myselector> -d <domainname> --bits=2048
```

执行指令后生成的不仅仅是 `.private` 密匙文件，还有装有完成 DKIM 设置所需 TXT 记录的 `.txt` 文件。这一记录的功能在于公布邮箱服务器的公共密匙。需要注意的是这条 TXT 记录有可能会被分成数条字符串，以满足每条字符串最长 255 字符的要求。为了查看 TXT 记录是否有设置成功，我运行了（需要安装 `dnsutils` 软件包）：

```sh
$ host -t TXT <myselector>._domainkey.<domainname>
```

接下，重启 `opendkim` 服务并确保 `postfix` 在发出邮件之前执行加密。编辑 `/etc/postfix/main.cf` ：

```sh
non_smtpd_milters=inet:127.0.0.1:<dkimsocket>
smtpd_milters=inet:127.0.0.1:<dkimsocket>
```

在重启 `postfix` 后， DKIM 就开始运作了。


### 基于域名的邮件认证，报告和一致性检查（ DMARC ） {#基于域名的邮件认证-报告和一致性检查-dmarc}

毫无意外，有一个软件包 `opendmarc` 可以用于部署 DMARC ，并且这一软件包也有对应的 [Arch Wiki](https://wiki.archlinux.org/index.php/OpenDMARC) 页面。需要注意的是， DMARC 必须在 SPF 和 DKIM 都设置完成后才能生效。在安装软件包后，编辑 `/etc/opendmarc/opendmarc.conf`:

```sh
Socket inet:<dmarcsocket>@localhost
```

在启动 `opendmarc` 服务后，在 `postfix` 中启用 DMARC 邮件滤网（与 DKIM 滤网用逗号隔开）：

```sh
non_smtpd_milters=inet:127.0.0.1:<dkimsocket>, inet:127.0.0.1:<dmarcsocket>
smtpd_milters=inet:127.0.0.1:<dkimsocket>, inet:127.0.0.1:<dmarcsocket>
```

最后，按照 Arch Wiki 所指示的步骤在 DNS 设置中加上一条 DMARC TXT 记录并重启 `postfix` 就大功告成了。


## 通过所有测试 {#通过所有测试}

给 `check-auth@verifier.port25.com` 发送测试邮件后，我收到了一份邮件服务器的测试报告。报告显示所有安全设置都在正常运作。除此之外， gmail 不再将我的邮件扔进垃圾箱了，而“查看原件”页面下也显示我的邮件通过了 SPF ， DKIM ， 和 DMARC 检查。 由于启用了 DMARC ， gmail 还会每天向我的邮箱发送一份安全报告。折腾到这个地步后，我对我的新邮箱比较满意了，并准备废除我之前所使用的邮箱。我还在寻找可以自己架设的日历服务，希望不久的将来我可以完全摆脱在通讯方面对 Google 服务的依赖。
