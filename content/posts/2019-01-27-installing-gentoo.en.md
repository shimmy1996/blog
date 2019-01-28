+++
title = "Installing Gentoo"
tags = ["linux"]
categories = ["geekery"]
draft = false
date = 2019-01-27
slug = "installing-gentoo"
+++

I finally bite the bullet and installed Gentoo on VirtualBox (totally not motivated by the front page wishlist), thereby achieving my ultimate digital @5c3n510n (or descent according to DistroWatch).

Jokes aside, the installation process is surprisingly pleasant: the [Gentoo Handbook](https://wiki.gentoo.org/wiki/Handbook:Main%5FPage) is wonderfully written, and seems to have a plan for everything that might go wrong. I like the Handbook more than ArchWiki's [Installation Guide](https://wiki.archlinux.org/index.php/Installation%5Fguide) as it also details the rationale behind each step I took, which is often a fun read in its own right. I would go as far as saying the Gentoo Handbook is actually more beginner friendly, as it carefully assembles bits of information that are normally scattered all over the place, providing a great starting point for learning how to tame the operating system. Besides, Gentoo Handbook covers more than installation: it also contains other necessary setup processes to set up a usable system. I will be gradually replicating my current desktop setup to decide if a migration is worth the time.

My very first encounter with GNU/Linux operating systems is Ubuntu 12.04: one of my classmates (vacuuny/A2Clef) was installing it in school's computer labs. There was a time when I would switch between various Ubuntu variants every few days. I dual booted Windows and Ubuntu for a while before switching entirely to Ubuntu in 2014. Much annoyed by the Amazon ads, I tried out Arch Linux as part of my New Year's resolution in 2015. Even with a second computer to look up instructions, it still took me quite a while to adapt to the new system. I ranted "maybe I still haven't gotten the Arch way" in my old blog, but never looked back once I got the knack of it.

I still try out other distributions from time to time in VirtualBox, but never find them to offer much improvements compared with Arch beyond the setup processes, and even more so when considering the excellent documentation on ArchWiki (well now we have a contender). Once I have my desktop environment set up, the experience between distributions is not that different, but the distinctions kicks in when problems occur and I search online for troubleshooting tips. Having more up-to-date packages is another charm Arch has. More recently, the `systemd` controversy caused me to start shopping around for a new distribution to try out, not so much because of the actual security concerns, but just to see what it is like to use different init system: I mostly used GUI under Ubuntu (`apt-get` and `nano` was probably the only command I knew for the longest time) and Arch was already using `systemd` when I switched. Aside from Gentoo, the candidates include Void Linux and the BSDs. Void Linux was easy to set up with its installer wizard, yet I didn't feel compelled to move to it. Let's see if Gentoo would change my mind.