+++
title = "Hello Darkness, My Old Friend"
tags = ["design"]
categories = ["site-related"]
draft = false
date = 2019-09-15
slug = "hello-darkness-my-old-friend"
+++

With system wide dark modes becoming commonplace, I took the effort to tweak the color scheme of my blog and added a dark mode specific one using `prefers-color-scheme` in CSS. I also toyed around the idea of adding a user toggle using JavaScript per instructions [here](https://flaviocopes.com/dark-mode/), but ultimately decided against it because of my (totally unjustified and groundless) distaste towards the language.

| Color Usage     | Light Theme | Dark Theme |
|-----------------|-------------|------------|
| Accent          | `#700000`   | `#8fffff`  |
| Background      | `#f7f3e3`   | `#080c1c`  |
| Text            | `#2e2d2b`   | `#d1d2d4`  |
| Code Background | `#e3dacb`   | `#1c2534`  |
| Border 1        | `#e7e3d3`   | `#181c2c`  |
| Border 2        | `#d7d3c3`   | `#282c3c`  |

Writing CSS is a such tiring endeavor, but on the bright side, picking colors is a surprisingly relaxing activity. The light mode color scheme now has reduced contrast, and I updated the isso style sheets with matching colors. Yes, I only inverted the colors in dark mode and did not reduce the font weights because of the peculiar way in which human vision work. Part of me already screams heresy when I look at the color codes formed by three numbers that seem to have no connection whatsoever—they are like dissonant chords that cause itches in brain—so I **need** them to at least sum up to a nice number.

Wissen ist Nacht!
