+++
title = "No More Disqusting Disqus"
lastmod = 2017-12-17T00:04:39-06:00
tags = ["social-network", "security"]
categories = ["site-related"]
draft = false
date = 2017-10-22
slug = "no-more-disqusting-disqus"
+++

A while back Disqus had a [user info breach](https://blog.disqus.com/security-alert-user-info-breach), which made me reconsider my choice of commenting system. If I am already hosting my own blog and email, why stop there and leave out commenting system to be served by a third-party platform?


## The Good, The Bad, and The Ugly {#the-good-the-bad-and-the-ugly}

I have mixed feelings for Disqus' idea of turning comments across different sites into a unified social network. Personally, I use most social media services as 'media' rather than a social tool: they are obviously ill-suited for posting large paragraphs (thus the plethora of external links), and even for posting random thoughts, the sheer time it takes to type out a sensible and logically coherent argument (especially on mobile devices) frequntly puts me off. Since I'm so used to being that creepy lurker, I inevitably got into the habit of judging my social media identity: what would I think about this Frankenstein's monster made up of retweets and likes.

Blog Comments work a little differently. I feel more relieved when commenting on a blog: it feels more like a convrsation with the blog owner rather than broadcasting myself to everyone on the Internet. Disqus, however, takes this away by social network-ifying blog comments. I guess the potential upside to Disqus is to attract more traffic, but I do not want my blog comments to become just another social media live feed: if one has valuable comment, the lack of Disqus should not deter him or her from posting it (while I've noticed the opposite happening quite a few times).

Here's comes the ugly part though. Not to mention the fact that embedding JavaScript that I have no control over is a very bad idea, it was only until yesterday did I notice viewer tracking in Disqus is an opt-out system. Since I don't plan on monetizing on my blog, it really isn't worth risking blog viewers' privacy for what Disqus provides. Besides, it really worried me when I realized majority of upvotes in my Disqus comments came from zombie accounts with profile links set to dating sites. Whether these 'disqusting' (bad pun alert) accounts were hijacked due to the security breach or were simply created by spammers is beyond me, but yeah, I don't want these zombies lurking around my blog's comments.


## The Search for Replacements {#the-search-for-replacements}

I have decided to selfhost a commenting system and my top priority is to avoid any external service if possible. After careful selection, the two finalists for the job are [isso](https://posativ.org/isso/) and [staticman](https://staticman.net). Isso is a lightweight comment server written in Python, while staticman is an interesting set of APIs that parses comments into text files and adds them to your site's Github repo. Installing isso means having to deal with databases, which I really dread and would like to avoid at all cost; using staticman allows the site to remain static, yet relies on GitHub's API (and staticman.com's API if I don't host a instance myself). While maintaining an entirely static site is very tempting, I decided to try out Isso first to see if ditching all external sites is worth the effort.

Just for shits and giggles, here's another interesting alternative: [Echochamber.js](https://github.com/tessalt/echo-chamber-js).


## Setting Up Isso {#setting-up-isso}

The official website provides fairly good [documentation](https://posativ.org/isso/docs/) already. I installed isso from [AUR](https://aur.archlinux.org/packages/isso/) and enabled it via `systemctl`. Setting isso up was surprisingly painless(including the part with database), and I used a different [configuration](https://posativ.org/isso/docs/setup/sub-uri/) than default since I am running isso on the same server. The only issue I encountered is with `smtp`. By checking the status of `postfix`, I quickly determined the problem lies in `smtpd_helo_restrictions`: by disabling the option `reject_unknown_helo_hostname`, isso can now use the local `smtp` server without issues. I took some extra effort to customize the CSS template for isso and the comment section looks fairly good now (a lot faster as well).

Happy Commenting!