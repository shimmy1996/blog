+++
title = "用GNU sed开天辟地"
date = 2020-01-09
slug = "becoming-pangu-with-sed"
draft = false
+++

如果你熟悉中国神话或博客众，那么你说不定听说过被半开玩笑地称作“盘古之白”的排版习惯：在中文字符（但不包括标点符号）和拉丁字符或数字之间增加一定间隔。我所实行的这一规则的变体还包括在所有HTML元素（如链接和强调）的周围也加上间隔。

到目前为止，我一直在源文件（Markdown或org格式）中手动添加空格：这无疑是实行这一规则最糟糕的方法。除了要费额外的工夫之外，这类排版规则还应该，在我看来，仅在输出/渲染时应用。更不用说手动调整我刚刚恢复的那一大堆旧日志不是什么吸引人的差事。因为不愿加载额外的[JavaScript](https://github.com/vinta/pangu.js)，我转向了万能的GNU sed。为了将盘古之白添加到Hugo生成的HTML和XML文件中（通常在`./public`目录里），我使用了以下shell脚本：

```sh
#! /usr/bin/env sh
# For punctuation marks to be recongnized correctly.
export LC_CTYPE=en_US.UTF-8
find . -path "./public/*" \( -name "*.html" -or -name "*.xml" \) -print -exec sed \
     -e 's/\([a-zA-Z0-9]\|<\/[a-z]*>\)\([^[:punct:][:space:]a-zA-Z0-9\s]\)/\1 \2/g' \
     -e 's/\([^[:punct:][:space:][:alnum:]]\)\([a-zA-Z0-9]\|<[a-z]\)/\1 \2/g' \
     -i {} ";"
```

如果你想坚持履行这一[W3C工作草案](https://www.w3.org/TR/clreq/#mixed%5Ftext%5Fcomposition%5Fin%5Fhorizontal%5Fwriting%5Fmode)给出的第一选择，且并不在意生成网页的大小的话，可以换用CSS来生成这一间隔：

```sh
find . -path "./public/*" \( -name "*.html" -or -name "*.xml" \) -print -exec sed \
     -e 's/\([a-zA-Z0-9]\|<\/[a-z]*>\)\([^[:punct:][:space:]a-zA-Z0-9\s]\)/\1<span style="margin:0.25ch;"><\/span>\2/g' \
     -e 's/\([^[:punct:][:space:]a-zA-Z0-9]\)\([a-zA-Z0-9]\|<[a-z]\)/\1<span style="margin:0.25ch;"><\/span>\2/g' \
     -i {} ";"
```

如果你也是盘古之白的信徒，那么在本站留下评论时请不必担心手动添加空格：由于[Hyperskip](https://git.shimmy1996.com/hugo-hyperskip)评论会在Hugo构建站点时插入，它们也会被以上的脚本影响到。请尽管坐下、放松、享受这空白一片的绝景吧。
