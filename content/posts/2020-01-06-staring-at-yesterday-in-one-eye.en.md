+++
title = "Staring at Yesterday in One Eye"
date = 2020-01-06
slug = "staring-at-yesterday-in-one-eye"
draft = false
+++

The dead speak! The galaxy has heard a mysterious broadcast, a record of THE **GREAT DIMENSIONAL FRACTURE** in the familiar yet distant tone of **MY PREVIOUS VESSEL**.


## A Link to the Past {#a-link-to-the-past}

Alright, that's enough puns and _The Rise of Skywalker_ jokes from me. As mentioned before, my blog was originally using WordPress and switched to Hugo in 2017-09-01. To be more specific, I actually had two WordPress blogs: one named Pandora (because of Borderlands 2, but this is such an cliche name that I'm sure there's a million other imaginary planets using this name) hosted with WordPress.com, and another being Library of Trantor (because of Isaac Asimov's _Foundation_ series) hosted on Bluehost, with the former written in English and latter in Chinese. Since I kept archives of both before taking them down, I was able to revive all those old posts from the grave using [this tool](https://github.com/SchumacherFM/wordpress-to-hugo-exporter) and some elbow grease. I refrained myself from leaving out any of the old posts, as the main motivation of this effort is really just to be able to easily see and be reminded of my younger self. It's a strange yet familiar experience reading those old writings: I can see parts of me that has changed and parts that are still distinctively shimmy1996.

Handling images is tricky and my old posts made quite liberal uses of them unfortunately: I opted for the simplest way out and just kept the originals without any kind of fancy compression or styling. I still need to figure out a more efficient way to both store and serve those images. Even with Git LFS available, I was reluctant to add over 300 MB of images to my blog repository (so they are currently in untracked-land), and now my blog could definitely benefit from a CDN setup. Perhaps I could also do what Jupyter notebooks do—encode all images in Base64—to get a single HTML file.

For the regular visitors of my blog out there (if there are any), you might notice that the comment section looks different: that's right, the search for the ideal static site commenting system is finally over for me (until it starts again)! Through out the years, I've used WordPress, Duoshuo (now defunct), Disqus, and Isso as my comment systems. Now, my [Hyperskip](https://git.shimmy1996.com/shimmy1996/hugo-hyperskip) has superseded them all: taking inspiration from [Staticman](https://staticman.net/), I set up Hyperskip to store all the comments in a TOML file, and opted to use email as the submission method, simplifying the setup. Gone are the days of databases, queries, and external scripts, and I get to migrate and version control all the comments (including the ones from the WordPress era) in the same Git repository as my blog too.


## The Other Old Friend {#the-other-old-friend}

One week into the New Year and I have already switched color scheme of the website five times, with another dozen sitting in my folder (totally not because of how unsightly these RGB color codes are). Much like how I'm a bit burned-out from [getting fonts right for everything](/en/posts/2019-12-01-fun-with-fonts-on-the-web/), I have decided to remove all custom color choices from the website: no more syntax highlighting, fancy buttons, nor dark modes.

As long as there are little knobs that I can toy around with, I always find myself distracted and spending way too much time worrying about the most insignificant choice of words, colors, or spacing (as you can tell by how much of I blog about the blog). The only cure I found is to simply remove the opportunities of making those choices altogether, and stick with the default. This is why I replaced Isso (I spent too much time trying to make it not look so foreign) and tags and categories are now gone, too.

Completely contrast to how the saying normally goes, I hardly ever find myself missing the things I cut away. More often than not, I sympathize with the elephant that finally broke free from the rope, rather than the remorse after losing something cherished. I do occasionally ask myself whether maintaining all those babbling by my past self is just another such rope holding me back that I just haven't realized yet. Well, my response is: a cowboy could always use a lasso on the road.
