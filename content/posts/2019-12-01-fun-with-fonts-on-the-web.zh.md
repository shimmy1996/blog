+++
title = "字体配置万维网篇"
date = 2019-12-01
slug = "fun-with-fonts-on-the-web"
draft = false
+++

用《字体配置浏览器篇》作为标题或许更为准确，不过现在的标题听起来更吸引人一些。渲染文本 [不是一件简单的事](https://gankra.github.io/blah/text-hates-you/) ，如果还要考虑书写系统之间的巨大差异（这大概得怪巴别塔）无异于雪上加霜。运行双语博客会导致字体选择的麻烦加倍，这里是我遇到的一些问题的汇总。


## 空格侵略者 {#空格侵略者}

大多数浏览器会将 HTML 中的连续文本合并为一行，并在链接处加上空格。所以

```html
<html>Line one and
line two.</html>
```

会被渲染为

```text
Line one and line two.
```

这种一刀切的方法显然不适用与字符之间不带分隔的 CJK 语言。解决方案是为页面（或页面上的任何特定元素）指定 `lang` 属性，如下所示：

```html
<html lang="zh">第一行和
第二行。</html>
```

如果你的浏览器足够聪明（例如 Firefox），渲染的结果就不会有额外的空格。但是，所有基于 Blink 的浏览器仍然顽固地将多余的空格塞进去，所以我只能像野蛮人那样继续写一段一行的源文件。尽管不是万能的解决方案，但是指定 `lang` 属性仍然具有启用特定于某种语言的CSS规则的额外好处，这稍后会派上用场。


## 引号归来 {#引号归来}

如 [之前的日志](https://www.shimmy1996.com/zh/posts/2018-06-24-fun-with-fonts-in-emacs/) 所说， CJK 字体会将引号显示为全角字符，不同于拉丁字体。只要网页不尝试混搭字体，这就不会成为问题：只需使用特定于语言的字体栈就行。

```css
body:lang(en) {
    font-family: "Oxygen Sans", sans-serif;
}

body:lang(zh) {
    font-family: "Noto Sans SC", sans-serif;
}
```

再加上匹配的 `lang` 属性，所有问题就都解决了。 Firefox 甚至允许为每种语言指定默认字体，所以仅使用后备字体（例如 `sans-serif` 或 `serif` ）也可行，不一定要费心编写特定于语言的CSS。

那么，如果我想用 Oxygen Sans 来渲染拉丁字符，并用 Noto Sans SC 来渲染 CJK 字符怎么办？虽然看似没有问题，但像这样指定字体堆栈，

```css
body:lang(zh) {
    font-family: "Oxygen Sans", "Noto Sans SC", sans-serif;
}
```

会导致引号被 Oxygen Sans 渲染、显示为半角字符。我的解决方案是通过 `unicode-range` 定义一个涵盖了引号的替代字体，

```css
@font-face {
    font-family: "Noto Sans SC Override";
    unicode-range: U+2018-2019, U+201C-201D;
    src: local("NotoSansCJKsc-Regular");
}
```

并修改字体栈为

```css
body:lang(zh) {
    font-family: "Noto Sans SC Override", "Oxygen Sans", "Noto Sans SC", sans-serif;
}
```

这样我们就可以享受全角引号了！


## 字体忍者 {#字体忍者}

字体文件通常都不小，对于 CJK 字体来说更是如此：刚才提到的 Noto Sans SC 的大小 [超过8MB](https://github.com/googlefonts/noto-cjk/blob/master/NotoSansSC-Regular.otf) 。尽管我已经下定主意要从自己的服务器上提供所有文件，考虑到我网站上的平均 HTML 文件大小更接近 8KB，这显得有些过头了。那么那些网络字体服务如何处理这一问题呢？

大多数网络字体服务的工作方式是在网站的样式表里添加一堆 [`@font-face` ](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@font-face)定义，以从专用服务器上提取字体文件。为了减少所提供的文件大小， Google Fonts 会将字体文件大卸八块，并在 `@font-face` 里声明每一块所对应的 `unicode-range` （这正是它们处理 [CJK 字体](https://fonts.googleapis.com/css?family=Noto+Sans+SC) 的方式）。他们还将字体文件压缩为 WOFF2 以进一步缩减文件大小。而 [Adobe Fonts](https://fonts.adobe.com/) （以前称为 Typekit ）似乎有一些 JavaScript 奇技淫巧，可以动态确定要从字体文件加载的字形。

博采众家之长，得益于这是一个静态站点，我们可以简单地统计所有用到的字符，并提供一个只包含这些字符的字体文件。所要用到的工具主要是 pyftsubset （属于 [fonttools](https://pypi.org/project/fonttools/) 下的一个组件）和 GNU AWK 。将字体压缩为 WOFF2 还需要 Brotli 压缩库。在 Arch Linux 下，获取这些程序需要安装 [python-fonttools](https://www.archlinux.org/packages/community/any/python-fonttools/) 、 [gawk](https://www.archlinux.org/packages/core/x86%5F64/gawk/) 、 [brotli](https://www.archlinux.org/packages/community/x86%5F64/brotli/) 和 [python-brotli](https://www.archlinux.org/packages/community/x86%5F64/python-brotli/) 。

收集生成的HTML文件中的所有使用的字形可以使用这条 shell 命令：

```sh
find . -type f -name "*.html" -printf "%h/%f " | xargs -l awk 'BEGIN{FS="";ORS=""} {for(i=1;i<=NF;i++){chars[$(i)]=$(i);}} END{for(c in chars){print c;} }' > glyphs.txt
```

你可能需要 `export LANG=en_US.UTF-8` （或者其他 UTF-8 语言环境）以便正确处理某些字形。有了字形清单，我们就可以提取字体文件的有用部分并进行压缩：

```sh
pyftsubset NotoSansSC-Regular.otf --text-file=glyphs.txt --flavor=woff2 --output-file=NotoSansSC-Regular.woff2
```

指定 `--no-hinting` 和 `--desubroutinize` 可以进一步减小生成文件的大小，但会降低字体的美观程度。拉丁字体也可以使用类似的技术来瘦身，例如只提取包含 ASCII 字符的部分（或将范围设为 `U+0000-00FF` 以涵盖 Extended ASCII 字符）：

```sh
pyftsubset Oxygen-Sans.ttf --unicodes="U+0000-007F" --flavor=woff2 --output-file=Oxygen-Sans.woff2
```

大部分字体管理器都可以用来检查最后生成文件中可用的字形，也可以使用这一 [在线检查器](http://torinak.com/font/lsfont.html) （不支持 WOFF2，但是可以先试着转为其他格式后查看，例如 WOFF）。

我还考虑过将字形按受欢迎程度划分为更多块。获取按出现次数排序的字形列表可以使用以下命令：

```sh
find . -type f -name "*.html" -printf "%h/%f " | xargs -l awk 'BEGIN{FS=""} {for(i=1;i<=NF;i++){chars[$(i)]++;}} END{for(c in chars){printf "%06d %s\n", chars[c], c;}}' | sort -r > glyph-by-freq.txt
```

结果显示我的博客用到了大约 1000 个不同的汉字，其中大约 400 个出现了10次以上。由于上一步中获得的字体文件大小已经足够好，我没有继续进行拆分。


## 孔中窥见真理之貌（好像没有啥不对） {#孔中窥见真理之貌-好像没有啥不对}

我最终将字体文件的总大小减少到了 250KB 左右，但这仍然比 HTML 文件大好几个数量级。虽然看到我的网站在所有设备和屏幕上都保持一致很让人开心，但是与页面大小增加上百倍的代价相比，我觉得这点好处不成比例。

费劲心思指定字体或许并不值得。如果你希望看到我眼中本站的样子的话，以下是我的常用字体：

-   比例拉丁字体： [Oxygen Sans](https://github.com/KDE/oxygen-fonts) 。注意 KDE 版本与 [Google Fonts 版本](https://fonts.google.com/specimen/Oxygen) 有一些微妙的区别，我更喜欢前者。
-   比例 CJK 字体： [Noto Sans CJK](https://www.google.com/get/noto/help/cjk/) ，即思源黑体。
-   等宽字体： [Iosevka](https://typeof.net/Iosevka/) ，确切地说是 ss09 样式。
