---
title: Arch Linux 安装流水账
slug: arch-linux-installation-walkthrough
author: shimmy1996
type: post
date: 2015-03-20T11:25:00+00:00
dsq_thread_id:
  - 3611599480
categories:
  - 伪技术
tags:
  - Arch Linux
  - Linux
  - 安装
  - 流水帐

---
用了大半个学期了，这里记下安装过程。<del>放假万岁！</del>

开头按Arch Linux Wiki的[Beginner&#8217;s Guide][1]走。用`cfdisk`完成分区比较容易；

`chroot`后无法直接使用`wifi-menu`，但可以让`wifi-menu`先生成网络配置文件，然后再用`netctl enable ProfileName`，省得自己打；

安装完成后默认用户名是`root`；

首先[新建用户][2]，并记得设置密码；

把用户加进`sudoer`：`UserName ALL(ALL)=ALL`；

用新建用户登入，安装`packer`：

<pre># pacman -S base-devel fakeroot jshon expac git
# wget https://aur.archlinux.org/packages/pa/packer/PKGBUILD
# makepkg
# pacman -U packer-*.pkg.tar.gz
</pre>

安装`xorg`，使用`nvidia-libgl`；

安装触摸板驱动`xf86-input-synaptics`：

安装`nvidia`；

`lspci | grep VGA` 查看显示设备Bud ID；

将这些设备写入`xorg.conf`：

<pre>Section "Device"
Identifier "Card0"
Driver "nvidia"
BusID "PCI:1:0:0"
EndSection
</pre>

测试`startx`；

如果可以运行，用`pkill -15 X`退出x，开始装gui/dm

安装`xfce4`；

安装`lightdm`；

安装`lightdm-gtk-greeter`；

`xec startxfce4` 测试桌面；

如果显示器默认在右侧，可以用xrandr或arandr将显示器调到右侧；

要使改动永久化，把所做改动写成一个命令文本，如`display_setup.sh`，记得改动权限`chmod +x`，并加入`lightdm.conf`<del datetime="2015-03-20T02:57:08+00:00">豪华午餐</del>：

<pre># nano /etc/lightdm/lightdm.conf</pre>

<pre>[SeatDefaults]
display-setup-script=/etc/lightdm/display_setup.sh</pre>

加入greeter设置

<pre>/etc/lightdm/lightdm.conf</pre>

<pre>[SeatDefaults]
greeter-session=lightdm-greetername
</pre>

`systemctl start lightdm.service` 测试，可以后`enable lightdm.service`，重启；

先装一些好看的主题/字体

主题`numix`，图标numix cirles/elementary

用`xfce-theme-manager`方便些；

安装`wisker-menu`和`notifyd`panel插件，时钟设成UTC时区；

安装字体：

英：`ttf-oxygen`，`adobe-source-code-pro-fonts`，`adobe-source-serif-pro-fonts`；

中：`wqy-zenhei`，`wqy-microhei`；

日：`otf-ipafont`，`ttf-ipa-mona`；

装`google-chrome`；

用`lightdm-gtk-greeter-settings` 把登陆界面统一一下，注意`root`应有对壁纸/图标的访问权限才能在登录界面显示；

接下来安装音频驱动`pulseaudio`， `puavcontrol`；

安装`gvfs`，这样才有回收站；

`xdg-user-dirs`提供归档文件夹；

安装`dropbox`，无法启动看[官网][3]。

 [1]: https://wiki.archlinux.org/index.php/Beginners%27_guide
 [2]: https://wiki.archlinux.org/index.php/Users_and_groups#Example_adding_a_user
 [3]: https://www.dropbox.com/help/72
