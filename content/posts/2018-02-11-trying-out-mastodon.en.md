+++
title = "Trying Out Mastodon"
draft = false
date = 2018-02-11
slug = "trying-out-mastodon"
+++

As mentioned in my [previous post](https://www.shimmy1996.com/en/posts/2017/10/22/no-more-disqusting-disqus/), I am not really accustomed to posting on social networks. However, the other day I encountered a term I haven't heard in a long time: micro-blogging. Yes, quite a few social networks, Twitter for instance, is before anything a micro-blogging service. This definition of Twitter makes it immensely more appealing to me: it's a bite-sized blog for random thoughts, funny incidents, and many other pieces of my life that might not fit well with a regular blog post. However, I still find posting on Twitter has the 'broadcasting to the entire Internet' feeling stamped into it. Guess I'll just host my own then.

[Mastodon](https://github.com/tootsuite/mastodon) turned out to be one such solution. Mastodon's federated and decentralized nature makes it ideal for someone like me who struggles between building my online identity while minimizing the number of different companies I expose my information to. If people are willing to give away their personal information for fancy profile pictures, then hosting a Mastodon wouldn't seem like such a bad deal.


## Installation on Arch Linux {#installation-on-arch-linux}

It was kinda surprising that there doesn't exist a Arch Linux specific installation guide for Mastodon. Not that the installation process would be more difficult on Arch Linux than Ubuntu, but installation can be made a lot due simpler to the abundance of packages. Since the [official production guide](https://github.com/tootsuite/documentation/blob/master/Running-Mastodon/Production-guide.md) is already fairly comprehensive, I'll just document some Arch Linux specific steps here.


### Dependencies {#dependencies}

Here's a table detailing all the dependencies and their corresponding packages in Arch Linux. There really is no need to git clone anything. `npm` was also required in the installation process, but was not listed in the official guide.

| Dependency                            | Package                                                                                       |
|---------------------------------------|-----------------------------------------------------------------------------------------------|
| `node.js` 6.x                         | [`nodejs-lts-boron`](https://www.archlinux.org/packages/community/x86%5F64/nodejs-lts-boron/) |
| `yarn`                                | [`yarn`](https://www.archlinux.org/packages/community/any/yarn/)                              |
| `imagemagick`                         | [`imagemagick`](https://www.archlinux.org/packages/extra/x86%5F64/imagemagick/)               |
| `ffmpeg`                              | [`ffmpeg`](https://www.archlinux.org/packages/extra/x86%5F64/ffmpeg/)                         |
| `libprotobuf` and `protobuf-compiler` | [`protobuf`](https://www.archlinux.org/packages/?sort=&q=protobuf&maintainer=&flagged=)       |
| `nginx`                               | [`nginx`](https://www.archlinux.org/packages/extra/x86%5F64/nginx/)                           |
| `redis`                               | [`redis`](https://www.archlinux.org/packages/community/x86%5F64/redis/)                       |
| `postgresql`                          | [`postgresql`](https://www.archlinux.org/packages/extra/x86%5F64/postgresql/)                 |
| `nodejs`                              | [`nodejs`](https://www.archlinux.org/packages/community/x86%5F64/nodejs/)                     |
| `rbenv`                               | [`rbenv`](https://aur.archlinux.org/packages/rbenv/)                                          |
| `ruby-build`                          | [`ruby-build`](https://aur.archlinux.org/packages/ruby-build/)                                |
| `npm`                                 | [`npm`](https://www.archlinux.org/packages/community/any/npm/)                                |

For `rbenv`, I needed to add `'eval "$(rbenv init -)"` to `.bashrc` or `.zshrc` after installation as prompted by the post installation script.


### Mastodon {#mastodon}

Create user `mastodon` and to `sudoers` using `visudo`.

```sh
useradd -m -G wheel -s /bin/bash mastodon
```

Then I can clone the repository and start [installing node.js and ruby dependencies](https://github.com/tootsuite/documentation/blob/master/Running-Mastodon/Production-guide.md#nodejs-and-ruby-dependencies). This is where `npm` is required. Besides, I encountered a `ENONT` error when running `yarn` saying `./.config/yarn/global/.yarnclean` is missing, which is resolved by creating the file.


### PostgreSQL {#postgresql}

In addition to installing the `postgresql` package, I followed [Arch Wiki](https://wiki.archlinux.org/index.php/PostgreSQL) to initialize the data cluster:

```sh
$ sudo su postgres
[postgres]$ initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data'
```

After starting and enabling `postgresql` with `systemctl`, I can then start the `psql` shell as the `postgres` user and create user for Mastodon (use `psql` command `\du` to check the user is actually there):

```sh
$ sudo su postgres
[postgres]$ psql
[psql]# CREATE USER mastodon CREATEDB;
[psql]# \q
```

Port selection is customizable in `postgresql.service` and the port number will be used in `.env.porduction` customization.


### Redis {#redis}

Pretty much the same drill as `postgresql`, I installed `redis` and start/enabled `redis.service`. The port selection and address that have access can all be configured from `/etc/redis.conf`.


### Nginx & Let's Encrypt {#nginx-and-let-s-encrypt}

The official production guide covers this part pretty well already.


### `.env.production` {#dot-env-dot-production}

The config file is fairly self-explanatory. The only thing I got wrong the first time is the variable `DB_HOST` for `postgresql`. I then obtained the correct path, `/run/postgresql`, by checking status of `postgresql.service`.


### Scheduling Services & Cleanups {#scheduling-services-and-cleanups}

Again, just follow the official production guide. I installed [`cronie`](https://www.archlinux.org/packages/core/x86%5F64/cronie/) to schedule cron jobs.


## My Experience {#my-experience}

The web interface is fairly good, I like how I can write toots while browsing timelines instead of been forced to stay at the top of the page. I tried out quite a few Mastodon clients on my phone and I settled on [Pawoo](https://pawoo.net/about), which is built by Pixiv. So far Mastodon feels like a more comfy twitter to me and a platform where I am actually willing to write on. I'm pushing myself to write something on Mastodon every few days. So far it's been mostly running logs, but I'll come up more stuff to post in the future.

One thing I would really like to see though is multilingual post support in Mastodon. A workaround I currently use is appending different tags for Chinese vs. English posts, which not only bloats my toots, but also fragmented my timeline so that it's only 50% comprehensible for most people. Regrettably, it seems that out of the various micro-blogging/social networking services, only [Facebook](https://code.facebook.com/posts/597373993776783) has something similar to this at the moment.

In the footer section, I've replaced Twitter with [my Mastodon profile](https://mstdn.shimmy1996.com/@shimmy1996). Feel free to take a peek inside. :P
