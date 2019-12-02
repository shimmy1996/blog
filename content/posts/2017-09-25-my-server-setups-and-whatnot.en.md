+++
title = "My Server Setups and Whatnot"
draft = false
date = 2017-09-25
slug = "my-server-setups-and-whatnot"
+++

## Why move the blog? And to where? {#why-move-the-blog-and-to-where}

After putting up with the clunky WordPress blog (and Bluehost's 2003-looking admin panel for that matter) for three years, I finally decided to ditch everything I currently have and restart my blog in a more civilized manner. There was a couple of things that I was not happy about my old WordPress setup, namely:

-   Clunky and eats up my server storage.
-   Not as easy way to back up with tools I know.
-   Does not come with a command line interface, which is becoming my preferred way of doing almost anything.
-   Lacking some basic features I wanted, i.e. multilingual support. As powerful as WordPress may be in the right hands, I do not want to invest too much effort in learning CSS/js/php nor do I want to use some plugin from some sketchy WordPress plugin marketplace.
-   These theme and plugin marketplaces creeps me out in the same way as ubuntu software center.
-   WordPress has a lot of features I do not actually need, i.e. user permission system, which is an overkill for my personal blog site.

Picking an alternative blogging system was not too hard once I am aware of my needs: a fast and minimalist static site generator implemented in a language I know (or I found valuable to learn) with out-of-the-box multilingual support, a.k.a. `hugo`.

As for hosting services, I considered github pages and netlify to be fast and easy solutions but I want something more substantial for a personal blog, like a VPS. Besides, github pages not supporting https for custom domains is a deal breaker for me. I filtered down the list of VPS hosting providers with Arch Linux support and I ended up with DigitalOcean. Since I wanted to completely sever my connection with Bluehost, I also moved my domain name host to Google Domains.


## Install Arch Linux {#install-arch-linux}

Do note that Arch Linux is probably not the best suited server Linux distro. Use a non-rolling distro if stability is a concern. I use it only because I also run it on all my other computers. Backup the droplet often if you decided to go down this route: it hasn't happened to me yet but I've heard people complaining about Arch breaking too often.


### Installation {#installation}

Apparently my information on DigitalOcean supporting Arch Linux is outdated, as they stopped supporting it a while back. Thankfully, it is still not to hard to bring Arch Linux to a droplet (this is how DigitalOcean refer to a server) due to the awesome project [digitalocean-debian-to-arch](https://github.com/gh2o/digitalocean-debian-to-arch). All I needed to to was [set up a droplet](https://www.digitalocean.com/community/tutorials/how-to-create-your-first-digitalocean-droplet), `ssh` into the server, and follow the instructions:

```sh
# wget https://raw.githubusercontent.com/gh2o/digitalocean-debian-to-arch/debian9/install.sh -O install.sh
# bash install.sh
```


### Low Level Setup {#low-level-setup}

Once the script finishes running, I have an Arch Linux system running on my droplet with internet access. Most of the additional setups needed can be found in [Arch Wiki](https://wiki.archlinux.org/index.php/Installation%5Fguide). Since I am by no means a great tutorial writer, I suggest referring to Arch Wiki for detailed steps. The recorded commands here are just for book-keeping purposes and is by no means the best way to do things.


#### System Clock {#system-clock}

Sync system clock and set time zone.

```sh
# timedatectl set-ntp true
# timedatectl settimezone <Region>/<City>
```


#### Base Packages {#base-packages}

Install/update base packages.

```sh
# pacman -S base base-devel
```


#### Fstab {#fstab}

Generate `fstab`.

```sh
# genfstab -U / >> /etc/fstab
```


#### Set Locale {#set-locale}

Uncomment `en_US.UTF-8 UTF-8` in `/etc/locale.conf` then generate locale with:

```sh
locale-gen
```

Set `LANG=en_US.UTF-8` in `/etc/locale.conf`.


#### Hostname {#hostname}

Edit `/etc/hosts` and add hostname of droplet:

```sh
127.0.1.1    <hostname>.localdomain <hostname>
```


#### Boot Loader and Initramfs {#boot-loader-and-initramfs}

Optimizations for intel processors:

```sh
# pacman -S intel-ucode
# grub-mkconfig -o /boot/grub/grub.cfg
```

Add `crc32` modules to initramfs, as otherwise the droplet fails to boot. Edit `/etc/mkinitcpio.conf` :

```sh
MODULES= "crc32 libcrc32c crc32c_generic crc32c-intel crc32-pclmul"
```

Regenerate the initramfs image.

```sh
# mkinitcpio -p linux
```


#### Root Password {#root-password}

You know the drill.

```sh
# passwd
```


### User Setups {#user-setups}

Here are some additional settings to make Arch Linux more useable.


#### Creature User {#creature-user}

Obviously it is not a good idea to use root account:

```sh
# useradd -m -G wheel -s /bin/bash <username>
# passwd <username>
```


#### Add User to Sudoer {#add-user-to-sudoer}

Edit `/etc/sudoers` and add:

```sh
<username> ALL=(ALL) ALL
```


#### Login As User {#login-as-user}

We will finish the rest of the configuration using the user account.

```sh
# su <username>
```


#### Package Manager {#package-manager}

I used to use `packer` as wrapper around AUR and `pacman`. However, after learning about [inherent insecurity](https://wiki.archlinux.org/index.php/AUR%5Fhelpers#Comparison%5Ftable) in their package building processes, I switched to a more secure AUR helper `trizen` (`pacaur` is another choice, and fun fact: there is a reddit bot that tells you to switch to `pacaur` every time `yaourt` is mentioned in a post): `trizen` prompts user to inspect `PKGBUILD`, `*.install` and other scripts before sourcing them and `trizen` is written in Perl instead of Bash. To install `trizen`, first install dependencies via `pacman` according to its [AUR Page](https://aur.archlinux.org/packages/trizen/), then clone its [git repo](https://github.com/trizen/trizen) to a local directory. Navigate to the directory containing `PKGBUILD` and run

```sh
$ makepkg
```

to make package and

```sh
$ pacman -U trizen-*.pkg.tzr.xz
```

to install `trizen`.


#### Useful Packages {#useful-packages}

Once package manager is in place, install packages to your heart's content! Some of my bread-and-butter packages include `emacs` (I installed the cli-only version, `emacs-nox`), `tmux` (terminal multiplexor, very useful), `zsh`, `vim` (for quick edits), and etc.


## Security Related Stuff {#security-related-stuff}

Now that a usable Arch Linux installation is in place, I would employ some security measures before hosting my website on it.


### Secure Login via `ssh` {#secure-login-via-ssh}

On local machine, generate your ssh keypair:

```sh
$ ssh-keygen -t rsa
```

Send your ssh keys to server:

```sh
$ ssh-copy-id <username>@<server>
```

Now, on server, make the following edits to `/etc/ssh/sshd_config` :

```sh
PermitRootLogin no
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
AllowUsers <username>
```

These changes will disable root login, disable password login and only allow specified user to login via ssh.

It is advisible to also change the default port (22) used for ssh connection, in the same file, specify port by (please remember this port selection):

```sh
port <non-std-port>
```

For these changes to take effect, restart `ssh` daemon:

```sh
$ sudo systemctl restart sshd.service
```

Keep this `ssh` session intact and attempt to start another `ssh` connection in local machine to see if the changes have taken effect (the original session is needed in case things are not working):

```sh
$ ssh -p <non-std-port> <username>@<server>
```


### Firewall Settings {#firewall-settings}

I use `ufw` as my firewall and it is very easy to setup. Install `ufw` with `trizen` and enable the desired ports:

```sh
$ trizen -S ufw
$ sudo ufw allow <port>/<protocol>
```

For instance, to allow `ssh` communication, allow `22/tcp` or `ssh` (if you used a non-standard port, allow `<non-std-port>/tcp`). Some other useful ports are:

| Port      | Usage                               |
|-----------|-------------------------------------|
| `80/tcp`  | `http`                              |
| `443/tcp` | `https`                             |
| `143`     | imap access                         |
| `993`     | imap over `ssl`                     |
| `25`      | receive incoming mail               |
| `587`     | smtp access (with or without `ssl`) |

To review the added ports and enable them:

```sh
$ sudo ufw show added
$ sudo ufw enable
```

Auto start up:

```sh
$ sudo systemctl enable ufw.service
```


### Sync Server Time {#sync-server-time}

Sync server time with `ntp` :

```sh
$ trizen -S ntp
$ sudo systemctl enable ntpd.service
```

Check time server status with:

```sh
$ ntpq -p
```


### Setting up PTR Record {#setting-up-ptr-record}

It turns out that DigitalOcean handles this automatically, all I needed to do is set the droplet name to a Fully Qualified Domain Name (FQDN), in this case `www.shimmy1996.com`. I then checked if the record is in place with:

```sh
$ dig -x <ip_address>
```


## Firing up the Server {#firing-up-the-server}

Next step would be actually preparing the server for serving contents.


### Create Web Directory {#create-web-directory}

Create a directory for serving web contents, a common choice would be:

```sh
$ mkdir ~/public_html
```

Make sure to give this directory (including the user `home` folder) appropriate permission with `chmod` (`755` would normally work). Populate the directory with a simple `index.html` for testing if you want.


### Instal `nginx` {#instal-nginx}

Install `nginx` with `trizen`, and edit `/etc/nginx/nginx.conf` to set up `http` server (the one set to `listen 80 default_server`):

```sh
server_name www.<domainname> <domainname>
root /path/to/public_html
```

For the `server_name` line add as many as you want. You may want to put your mail server address on it as well so that you can generate a single ssl certificate for everything. After these changes are made, (re)start and enable `nginx`:

```sh
$ sudo systemctl restart nginx.service
$ sudo systemctl enable nginx.service
```


### DNS Setup {#dns-setup}

The next step is to set up DNS records for our server. There are three types of records that need to be set up initially, `NS`, `A`, and `CNAME`. I also included some other useful records:

| Type    | Hostname              | Value                         | Usage                                                    |
|---------|-----------------------|-------------------------------|----------------------------------------------------------|
| `NS`    | @                     | nameserver address            | specifiec name server to use                             |
| `A`     | @                     | supplied IPv4 address         | redirects host name to IPv4 address                      |
| `CNAME` | www (can be anything) | @                             | sets `www.<hostname>` as an alias                        |
| `MX`    | @                     | mail server address           | specifiec mail server to use                             |
| `CAA`   | @                     | authorizor of SSL certificate | prevents other authority from certifying SSL certificate |

In my case, though I use Google Domains to host my domain, I still use DigitalOcean's name server. So I needed to setup these records on DigitalOcean and `NS` records on Google Domains.

After this step, you website should be accessible via your domain name, although it may take a few hours for the DNS record to populate.


### SSL Certificate {#ssl-certificate}

[Let's Encrypt](https://letsencrypt.org) is a great project and [`certbot`](https://certbot.eff.org/) is an awesome tool for SSL certificate generation. Kudos to the nice folks at EFF and Linux Foundation. I simply followed the instructions on [EFF site](https://certbot.eff.org/#arch-nginx):

```sh
$ sudo pacman -S certbot-nginx
$ sudo certbot --nginx
```

To provide some extra credibility to the certificate, I added an `CAA` record in my DNS settings with issue authority granted for `letsencrypt.org`. For now Let's Encrypt does not support wildcard certificate but will be [January 2018](https://letsencrypt.org/2017/07/06/wildcard-certificates-coming-jan-2018.html), and this is why I added a bunch of subdomains into my `nginx.config` (so that the certificate covers these subdomains as well).


## What Now? {#what-now}

After a couple hours (mostly waiting for DNS records to populate), and my website is online again. With a VPS at my disposal, I also host my personal email now and I might organize my random notes pieced from various websites into a post as well. I am still trying to figure out an efficient workflow for writing multilingual post with `org-mode` in `hugo` and once I am convinced I have found an acceptable solution, I will also post it.
