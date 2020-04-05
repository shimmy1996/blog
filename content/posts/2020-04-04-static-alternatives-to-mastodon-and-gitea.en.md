+++
title = "Static Alternatives to Mastodon and Gitea"
date = 2020-04-04
slug = "static-alternatives-to-mastodon-and-gitea"
draft = false
+++

Like how I decided to switch off Wordpress, I think I've had enough running
Mastodon and Gitea.

Keeping up with configuration changes with Gitea had been annoying, whereas with
Mastodon, breakages are common due the mismatching system library versions
(mostly protobuf) in the dependencies. While the latter is not a fault of
Mastodon itself, having to install two package managers (for Ruby and Node.js,
respectively) just to run a program is rather ridiculous to me.

I started hosting both applications in 2018: Mastadon first as a replacement for
Twitter, and Gitea later in reaction to Microsoft's acquisition of
Github. Looking back, they were probably overkill for my needs: my primary use
case for a git server and a micro blog are both very much single-user focused
and write-only, which means these content should be available in read-only form
for my site's visitors, making static pages the perfect replacement for both web
front ends.

Starting with Mastadon, I'm using the [twtxt](https://twtxt.readthedocs.io/en/latest/) format to store and serve my micro
blog. The format has existed for some time now, but enjoyed a recent resurgence
in the tildeverse (a series of websites offering public access Unix-like
systems). While there is now a whole community supported ecosystem of various
syntax extensions and software seeking to add more features to the format, I
have found the barebone timestamp-tab-and-then-text syntax to be sufficient. The
write-and-forget cycle is really addicting, and even more so when using a
command line client (mine is aptly named [twixter](https://git.shimmy1996.com/twixter/)).

As for Gitea, while an excellent Github replacement in my opinion, is more
suitable for community collaboration than as a personal project dumping
ground. I opted to manage the git repositories directly (see Chapter [4.4](https://git-scm.com/book/en/v2/Git-on-the-Server-Setting-Up-the-Server) and [4.5](https://git-scm.com/book/en/v2/Git-on-the-Server-Git-Daemon)
of _Pro Git_), and use [stagit](https://git.codemadness.org/stagit/) to generate the corresponding HTML files. These
stagit-generated pages have replaced Gitea as the new [Trantor Holocron](https://git.shimmy1996.com/).

Now that I have found satisfactory solution for the write-only portion of my
online presence, I will continue to explore options for the remaining two
pillars: read-only (content consumption) and interaction (means of
communication). Web feeds and email are my best answers now, but they still
don't cover all the bases in my experience.
