+++
title = "Becoming Pangu with GNU sed"
date = 2020-01-09
slug = "becoming-pangu-with-sed"
draft = false
+++

In case you aren't familiar with Chinese mythology or blogosphere, there's an old meme aptly named "Space of Pangu": a typesetting rule of thumb in favor of additional spacing between Chinese characters (but not punctuation marks) and Latin characters or numbers. My variant of the rule also includes additional spacing around any HTML elements like links and emphasis.

Up till now, I've been manually adding spaces in my source files (in Markdown or org), which is admittedly the worst way to do it. Aside from the additional chore, such a typesetting rule should, in my opinion, be implemented in the output/rendering format, not the source. Besides, manually fixing all the old posts I just brought back is not exactly a rewarding task. Unwilling to load additional [JavaScript](https://github.com/vinta/pangu.js), I turned to the all-mighty GNU sed. To add Space of Pangu to the final HTML and XML files that Hugo produces (normally in the `./public` directory), I used the following shell script:

```sh
#! /usr/bin/env sh
# For punctuation marks to be recongnized correctly. Any UTF-8 locale would do.
export LANG=en_US.UTF-8
find . -path "./public/*" \( -name "*.html" -or -name "*.xml" \) -print -exec sed \
     -e 's/\([a-zA-Z0-9]\|<\/[a-z]*>\)\([^[:punct:][:space:]a-zA-Z0-9\s]\)/\1 \2/g' \
     -e 's/\([^[:punct:][:space:][:alnum:]]\)\([a-zA-Z0-9]\|<[a-z]\)/\1 \2/g' \
     -i {} ";"
```

In case you are adamant about adhering to the recommendation by this [W3C Working Draft](https://www.w3.org/TR/clreq/#mixed%5Ftext%5Fcomposition%5Fin%5Fhorizontal%5Fwriting%5Fmodegg) and wouldn't mind bloating up the resulting web page, using CSS to create the spacing should do the trick:

```sh
find . -path "./public/*" \( -name "*.html" -or -name "*.xml" \) -print -exec sed \
     -e 's/\([a-zA-Z0-9]\|<\/[a-z]*>\)\([^[:punct:][:space:]a-zA-Z0-9\s]\)/\1<span style="margin:0.25ch;"><\/span>\2/g' \
     -e 's/\([^[:punct:][:space:]a-zA-Z0-9]\)\([a-zA-Z0-9]\|<[a-z]\)/\1<span style="margin:0.25ch;"><\/span>\2/g' \
     -i {} ";"
```

If you are another one of those Space of Pangu disciples, just note that there's no need to worry about adding spaces when leaving comments here: thanks to [Hyperskip](https://git.shimmy1996.com/shimmy1996/hugo-hyperskip) comments being inserted at Hugo's building stage, they are affected by those scripts as well. Just sit back, relax, and enjoy staring at the blank spaces.
