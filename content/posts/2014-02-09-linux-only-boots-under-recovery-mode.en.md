+++
title = "Linux Only Boots Under Recovery Mode"
draft = false
date = 2014-02-09
slug = "linux-only-boots-under-recovery-mode"
+++

This is part of the Plan.

I tried to install linux for my PC, and tried all sorts of distributions, including the most recent 3-4 versions of Mint and Ubuntu, but all of them (including live CD mode) only runs under recovery mode (with really low resolution and software-only rendering), which makes me MEGA unhappy. When I tried to boot them normally, all I get are black screens, or more precisely, purple screens.

After consulting online, I found that the only special thing about recovery mode is that it added `nomodeset` and `xforcevesa` into the `GRUB_CMDLINE_LINUX` parameter of grub file located at `/etc/default/grub`.

So, basically, the problem was solved by finding out which of the two parameters recovery mode added into  `GRUB_CMDLINE_LINUX`  made the system bootable again. It turned out it was the `xforcevesa` that solved the problem, and here is what I did:

-   If the live CD mode failed to load, enter the recovery mode of live CD. (Mint by directly selecting the second option, Ubuntu by pressing F6)
-   Now you should be able to get into the system no matter what happens to your system before. Install them as usual, and use "logical partition" since this is probably the second system on the PC. Install the boot on the entire hard drive.
-   Restart and get into the system in recovery mode (second option in grub, select `continue` when option menu occurs). Open the terminal, and here's the most important part:

<!--listend-->

```sh
sudo gedit /etc/default/grub
```

-   Edit the grub file, replace the parameter in `GRUB_CMDLINE_LINUX=<PARAMETERS>` to `xforcevesa`, save and close the file. Hold on, we are not done yet, always remember to:

<!--listend-->

```sh
sudo update-grub
```

Then, restart your computer and you should be able to boot the system normally.

Great, problem solved! Wow, very Mint, so Ubuntu.

There is still another thing bothering me, though, that is I can only boot into system successfully without any USB devices plugged in, which is superconfusing. I will try to figure this out.
