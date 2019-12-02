+++
title = "字体配置 Emacs 篇"
date = 2018-06-24
slug = "fun-with-fonts-in-emacs"
draft = false
+++

我终于花了些时间清理我 Emacs 里一团糟的字体设定。在折腾这些设定的过程中，我了解
到了一些中日韩（ CJK ）字体排版上的豆知识。

我主要使用 Emacs 的方式是使用一个图形 `emacsclient` 窗口链接在后台运行的守护进程。
我所要解决的主要问题有三个：缺少精细控制字体映射的方法、字形宽度和字符宽度不一致、
emoji 时常显示为豆腐块。虽然终端 Emacs 不大受这些问题的影响，但我不想放弃图形
Emacs 的其他好处，例如系统剪贴板和更加丰富的键位选择，所以我只好迎难而上着手解决
这些问题。


## 使用字体集（ Fontset ）设置后备字体 {#使用字体集-fontset-设置后备字体}

在最理想的状况下，我想指定两套字体，一套默认的等宽字体和一套专门显示中日韩字符的字
体。我原来是这么设定 Emacs 字体的：

```emacs-lisp
(setq default-frame-alist '((font . "Iosevka-13")))
```

这种方法显然无法指定后备字体。不过 `font` 除了接受单一字体外，也可以接受字体集。
根据 [Emacs手册](https://www.gnu.org/software/emacs/manual/html%5Fnode/emacs/Fontsets.html) ，字体集可以大致理解为从 Unicode 到字体的映射，并且我可以很容易地
[修改](https://www.gnu.org/software/emacs/manual/html%5Fnode/emacs/Modifying-Fontsets.html) 字体集。

听上去似乎很容易？且慢。我并不知道应该被修改的是哪一个字体集： Emacs 最终使用的
字体集似乎会因语言环境（ locale ）、使用图形还是终端窗口、使用 `emacsclient` 还
是 `emacs` 而变化。尽管有方法可以获得目前使用的字体集（ `(frame-parameter nil
'font)` ），但这 [并不完全可靠](https://lists.gnu.org/archive/html/emacs-devel/2006-12/msg00285.html) 。

在不少失败的尝试之后，我终于找到了 [答案](https://stackoverflow.com/questions/17102692/using-a-list-of-fonts-with-a-daemonized-emacs) ：直接定义一个新的字体集。

```emacs-lisp
(defvar user/standard-fontset
  (create-fontset-from-fontset-spec standard-fontset-spec)
  "Standard fontset for user.")

;; Ensure user/standard-fontset gets used for new frames.
(add-to-list 'default-frame-alist (cons 'font user/standard-fontset))
(add-to-list 'initial-frame-alist (cons 'font user/standard-fontset))
```

由于我除了指定中日韩字体外还对字体集做了其他更改，我会在阐明所有改变后再贴出全部
设定。


### 显示 Emoji {#显示-emoji}

解决 emoji 显示的方法与中日韩文字类似——找到一款支持 emoji 的字体不就好了吗——至少
我是这么想的。我一开始试图使用 Noto Color Emoji 作为 emoji 用后备字体，但发现
Emacs 目前并不支持彩色 emoji 字体。 Emacs 曾经在 macOS 上支持彩色 emoji字体，但
后来 [移除](https://github.com/emacs-mirror/emacs/blob/emacs-25.1/etc/NEWS#L1723) 了。

我最后选择了 [Symbola](http://users.teilar.gr/~g1951d/) 作为 emoji 后备字体（事实上我把它设为了所有 Unicode 字符的
后备字体）。 Symbola 可以显示 [所有 emoji](https://unicode.org/Public/emoji/11.0/emoji-test.txt) 和许多特殊符号。还需要注意的一点是，在
Emacs 25 之后，要想在字体集中自定义包含了大部分标点、特殊符号、 emoji 的
`symbols` [字符集（ charset ）](https://www.gnu.org/software/emacs/manual/html%5Fnode/emacs/Charsets.html)，需要[一些额外的设置](https://github.com/emacs-mirror/emacs/blob/emacs-25/etc/NEWS#L58)：

```emacs-lisp
(setq use-default-font-for-symbols nil)
```

如果实在想要显示彩色 emoji 倒也不是完全没有办法，不过不是通过设置字体，而是将
Unicode 字符替换为图片。[`emacs-emojify`](https://github.com/iqbalansari/emacs-emojify) 就是一个提供这种功能的 Emacs 包。由于这
个包会给 Emacs 带来一定的延迟，而且大部分彩色 emoji 图片库并不完整，我最终决定不
予采用。


### 引号风波 {#引号风波}

我一直习惯在书写中文时使用成对的全角弯引号（““””和“‘’”）以及在书写英文时
使用 ASCII 里的对称直引号（“"”和“'”）。然而我并不知道“全角弯引号”其实根本
不存在： Unicode 中只存在一组弯引号编码（ U+2018 、 U+2019 、 U+201C 、U+201D ），
而所谓的全角与半角弯引号之分完全是字体引起的。虽然已经有相关的 [提案](https://www.unicode.org/L2/L2014/14006-sv-western-vs-cjk.pdf) 建议将这两种
不同的表示方法标准化，但目前弯引号就是这么一个烂摊子。

了解来龙去脉之后，就不难理解为什么弯引号在 Emacs 里隶属 `symbol` 字符集而非某个
中日韩字符集了。这也导致这些弯引号会使用我的默认等宽字体并显示为半角字符。我并没
有从根本上解决这一问题的办法，所以为了保证显示风格和书写风格保持一致，我通过为特
定的 Unicode 编码指定字体将这些弯引号字符统一显示为全角。当我知道直引号其实也有
着 [充满误会的过去](https://www.cl.cam.ac.uk/~mgk25/ucs/quotes.html) 的时候，我已经不知道应该用什么表情来面对了——也许我们在这方面真
的很糟糕。

我的后备字体设置可以在 [GitHub](https://github.com/shimmy1996/.emacs.d#fontset-with-cjk-and-unicode-fallback) 和 [川陀网络](https://git.shimmy1996.com/shimmy1996/.emacs.d#fontset-with-cjk-and-unicode-fallback) 上找到。为了日志的完整性，我在这里也放
一份：

```emacs-lisp
(defvar user/cjk-font "Noto Sans CJK SC"
  "Default font for CJK characters.")

(defvar user/latin-font "Iosevka Term"
  "Default font for Latin characters.")

(defvar user/unicode-font "Symbola"
  "Default font for Unicode characters, including emojis.")

(defvar user/font-size 17
  "Default font size in px.")

(defun user/set-font ()
  "Set Unicode, Latin and CJK font for user/standard-fontset."
  ;; Unicode font.
  (set-fontset-font user/standard-fontset 'unicode
                    (font-spec :family user/unicode-font)
                    nil 'prepend)
  ;; Latin font.
  ;; Only specify size here to allow text-scale-adjust work on other fonts.
  (set-fontset-font user/standard-fontset 'latin
                    (font-spec :family user/latin-font :size user/font-size)
                    nil 'prepend)
  ;; CJK font.
  (dolist (charset '(kana han cjk-misc hangul kanbun bopomofo))
    (set-fontset-font user/standard-fontset charset
                      (font-spec :family user/cjk-font)
                      nil 'prepend))
  ;; Special settings for certain CJK puncuation marks.
  ;; These are full-width characters but by default uses half-width glyphs.
  (dolist (charset '((#x2018 . #x2019)    ;; Curly single quotes "‘’"
                     (#x201c . #x201d)))  ;; Curly double quotes "“”"
    (set-fontset-font user/standard-fontset charset
                      (font-spec :family user/cjk-font)
                      nil 'prepend)))

;; Apply changes.
(user/set-font)
;; For emacsclient.
(add-hook 'before-make-frame-hook #'user/set-font)
```


## 中日韩字体大小比例 {#中日韩字体大小比例}

最后需要解决的问题就是中日韩字体字宽和等宽字体比例不一致的问题了。理论上全角的中
日韩字符应该是半角字符宽度的两倍，但这并不在所有字号下成立。看起来原因是中日韩字
体在字号上其实偷懒了：在使用 Noto Sans CJK SC 时， 16px 和 17px 大小的中日韩字符
是没有任何大小区别的，直到 18px 才会出现大一号的字形，不像拉丁字符始终表现出预期
的尺寸增幅。这一现象使得中日韩字符和拉丁字符在每隔数个字号后大小比例相称，但使用
夹在中间的字号时中日韩字符会略微偏小。

一种解决方式时在修改字体集时给中日韩字体设置一个稍大一些的默认字号。不过这会导致
`text-scale-adjust` （通常被绑定在 <kbd>C-x C-=</kbd> 和 <kbd>C-x C--</kbd> 上）对中日韩字体不生效。
一种更好的办法是修改 `face-font-rescale-alist` 设置缩放比例：

```emacs-lisp
(defvar user/cjk-font "Noto Sans CJK SC"
  "Default font for CJK characters.")

(defvar user/font-size 17
  "Default font size in px.")

(defvar user/cjk-font-scale
  '((16 . 1.0)
    (17 . 1.1)
    (18 . 1.0))
  "Scaling factor to use for cjk font of given size.")

;; Specify scaling factor for CJK font.
(setq face-font-rescale-alist
      (list (cons user/cjk-font
                  (cdr (assoc user/font-size user/cjk-font-scale)))))
```

虽然在使用 `text-scale-adjust` 后字体大小比例依然可能会乱掉，但我只要默认字号下
对齐就行。具体的缩放比例只能通过反复测试来确定。我用以下几行字符是否对齐来判断缩
放比例是否合适（这张 [表格](https://websemantics.uk/articles/font-size-conversion/) 会是很好的帮手）：

```nil
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云
雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲
ㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞ
ああああああああああああああああああああああああああああああああああああああああ
가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가
```

不巧的是，我所使用中日韩字体的谚文比其他全角字符都要窄，所以最终结果仍不完美——解
决方案是再添加一个谚文专用的字体和缩放比例——不过对我来说够用了。

我在解决这些看似简单的问题上花的精力比想象的多不少，不过值得庆幸的是 Emacs 提供
了所需的各项工具。顺便一提， Emacs Wiki 上的 [这篇文章](https://www.emacswiki.org/emacs/FontSets) 也提供了一些类似的问题的解
决方案：要是我早一些看到，配置过程大概会顺利许多。
