+++
title = "Fun with Fonts on the Web"
date = 2019-12-01
slug = "fun-with-fonts-on-the-web"
draft = false
+++

A more accurate version of the title probably should be "Fun with Fonts in Web Browsers", but oh well, it sounds cooler that way. Text rendering is [hard](https://gankra.github.io/blah/text-hates-you/), and it certainly doesn't help that we have a plethora of different writing systems (blame the Tower of Babel for that, I guess) which cannot be elegantly fitted into a uniform system. Running a bilingual blog doubles the trouble in font picking, and here's a compilation of the various problems I encountered.


## Space Invaders {#space-invaders}

Most browsers join consecutive lines of text in HTML to a single one with an added space in between, so

```html
<html>Line one and
line two.</html>
```

renders to

```text
Line one and line two.
```

Such a simplistic rule doesn't work for CJK languages where no separators is used between words. The solution is to specify the `lang` attribute for the page (or any specific element on the page) like so:

```html
<html lang="zh">第一行和
第二行。</html>
```

If your browser is smart enough (like Firefox), it will join the lines correctly. All the Blink based browsers, however, still stubbornly shove in the extra space, so it looks like I will be stuck in unwrapped source files like a barbarian for a bit longer. While not a cure-all solution, specifying the `lang` attribute still have the added benefit of enabling language-specific CSS rules, which comes in handy later.


## Return of the Quotation Marks {#return-of-the-quotation-marks}

As mentioned in a [previous post](https://www.shimmy1996.com/en/posts/2018-06-24-fun-with-fonts-in-emacs/), CJK fonts would render quotation marks as full-width characters, different from Latin fonts. This won't be a problem as long as a web page doesn't try to mix-and-match fonts: just use language specific font-stack.

```css
body:lang(en) {
    font-family: "Oxygen Sans", sans-serif;
}

body:lang(zh) {
    font-family: "Noto Sans SC", sans-serif;
}
```

Coupled with matching `lang` attributes, the story would have ended here. Firefox even allows you to specify default fonts on a per language basis, so you can actually get away with just the fallback values, like `sans-serif` or `serif`, and not even bother writing language specific CSS.

However, what if I want to use Oxygen Sans for Latin characters, Noto Sans SC for CJK characters? While seemingly an sensible solution, specifying font stack like so,

```css
body:lang(zh) {
    font-family: "Oxygen Sans", "Noto Sans SC", sans-serif;
}
```

would cause the quotation marks to be rendered using Oxygen Sans, which displays them as half-width characters. The solution I found is to declare an override font with a specified `unicode-range` that covers the quotation marks,

```css
@font-face {
    font-family: "Noto Sans SC Override";
    unicode-range: U+2018-2019, U+201C-201D;
    src: local("NotoSansCJKsc-Regular");
}
```

and revise the font stack as

```css
body:lang(zh) {
    font-family: "Noto Sans SC Override", "Oxygen Sans", "Noto Sans SC", sans-serif;
}
```

Now we can enjoy the quotation marks in their full-width glory!


## Font Ninja {#font-ninja}

Font files are quite significant in size, and even more so for CJK ones: the Noto Sans SC font just mentioned is [over 8MB](https://github.com/googlefonts/noto-cjk/blob/master/NotoSansSC-Regular.otf) in size. No matter how determined I am to serve everything from my own server, this seems like an utter overkill considering the average HTML file size on my site is probably closer to 8KB. How does all the web font services handle this then?

Most web font services work by adding a bunch of [`@font-face`](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face) definitions into a website's style sheet, which pulls font files from dedicated servers. To reduce the size of files been served, Google Fonts slice the font file into smaller chunks, and declare corresponding `unicode-range` for each chunk under `@font-face` blocks (this is exactly how they handle [CJK fonts](https://fonts.googleapis.com/css?family=Noto+Sans+SC)). They also compress the font files into WOFF2, further reducing file size. On the other hand, [Adobe Fonts](https://fonts.adobe.com/) (previously known as Typekit) seem to have some JavaScript wizardry that dynamically determines which glyphs to load from a font file.

Combining best of both worlds, and thanks to the fact that this is a static site, it is easy to gather all the used characters and serve a font file containing just that. The tools of choice here would be pyftsubset (available as a component of [fonttools](https://pypi.org/project/fonttools/)) and GNU AWK. Compressing font files into WOFF2 also requires Brotli, a compression library. Under Arch Linux, the required packages are [python-fonttools](https://www.archlinux.org/packages/community/any/python-fonttools/), [gawk](https://www.archlinux.org/packages/core/x86%5F64/gawk/), [brotli](https://www.archlinux.org/packages/community/x86%5F64/brotli/), and [python-brotli](https://www.archlinux.org/packages/community/x86%5F64/python-brotli/).

Here's a shell one-liner to collect all the used glyphs from generated HTML files:

```sh
find . -type f -name "*.html" -printf "%h/%f " | xargs -l awk 'BEGIN{FS="";ORS=""} {for(i=1;i<=NF;i++){chars[$(i)]=$(i);}} END{for(c in chars){print c;} }' > glyphs.txt
```

You may need to `export LANG=en_US.UTF-8` (or any other UTF-8 locale) for certain glyphs to be handled correctly. With the list of glyphs, we can extract the useful part of font files and compress them:

```sh
pyftsubset NotoSansSC-Regular.otf --text-file=glyphs.txt --flavor=woff2 --output-file=NotoSansSC-Regular.woff2
```

Specifying `--no-hinting` and `--desubroutinize` can further reduce size of generated file at the cost of some aesthetic fine-tuning. A similar technique can be used to shrink down Latin fonts to include only ASCII characters (or keep the extended ASCII range with `U+0000-00FF`):

```sh
pyftsubset Oxygen-Sans.ttf --unicodes="U+0000-007F" --flavor=woff2 --output-file=Oxygen-Sans.woff2
```

Once this is done, available glyphs can be checked using most font manager software, or this [online checker](http://torinak.com/font/lsfont.html) (no support for WOFF2 though, but you can convert into other formats first, such as WOFF).

I also played around the idea of actually dividing the glyphs into further chunks by popularity, so here's another one liner to get list of glyphs sorted by number of appearances

```sh
find . -type f -name "*.html" -printf "%h/%f " | xargs -l awk 'BEGIN{FS=""} {for(i=1;i<=NF;i++){chars[$(i)]++;}} END{for(c in chars){printf "%06d %s\n", chars[c], c;}}' | sort -r > glyph-by-freq.txt
```

It turns out my blog has around 1000 different Chinese characters, with roughly 400 of them appearing more than 10 times. Since the file sizes I get from directly a single subsetting is already good enough, I didn't bother proceeding with another split.


## For Your Browsers Only {#for-your-browsers-only}

With all the tricks in my bag, I was able to cut down the combined font file size to around 250KB, still magnitudes above that of an HTML file though. While it is nice to see my site appearing the same across all devices and screens, I feel the benefit is out of proportion compared to the 100-fold increase in page size.

Maybe it is just not worth it to force the choice of fonts. In case you want to see my site as I would like to see it, here are my go-to fonts:

-   Proportional Latin font: [Oxygen Sans](https://github.com/KDE/oxygen-fonts). Note that the KDE version has nuanced differences from the [Google Fonts version](https://fonts.google.com/specimen/Oxygen), and I like the KDE version much more.
-   Proportional CJK font: [Noto Sans CJK](https://www.google.com/get/noto/help/cjk/).
-   Monospace font: [Iosevka](https://typeof.net/Iosevka/), the ss09 variant, to be more exact.
