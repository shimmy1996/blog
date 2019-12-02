+++
title = "Fun With Fonts in Emacs"
date = 2018-06-24
slug = "fun-with-fonts-in-emacs"
draft = false
+++

I finally took some time to look at the my font configurations in Emacs and
cleaned them up as much as possible. This dive into the rabbit hole have been
tiring yet fruitful, revealing the cravat of typesetting that I didn't know
before, especially for CJK characters.

I primarily use Emacs by running a daemon and connecting to it via a graphical
`emacsclient` frame, and I am attempting to tackle three major problems: I don't
have granular control over font mapping, glyph widths are sometimes inconsistent
with character widths, and emoji show up as weird blocks. Terminal Emacs doesn't
suffer as much from these problems, yet I don't want to give away the nice perks
like system clipboard access and greater key binding options, so here goes
nothing.


## Font Fallback Using Fontsets {#font-fallback-using-fontsets}

Ideally, I want to specify two sets of fonts, a default monospace font and a
CJK-specific font. Here's how I originally specified the font in Emacs:

```emacs-lisp
(setq default-frame-alist '((font . "Iosevka-13")))
```

The method above obviously leaves no ground for fallback fonts. However, it
turns out I can specify the `font` to be a fontset instead of an individual
font. According to [Emacs Manual](https://www.gnu.org/software/emacs/manual/html%5Fnode/emacs/Fontsets.html), a fontset is essentially a mapping from Unicode
range to a font or hierarchy of fonts and I can [modify](https://www.gnu.org/software/emacs/manual/html%5Fnode/emacs/Modifying-Fontsets.html) one with relative ease.

Sounds like an easy job now? Not so fast. I don't really know which fontset to
modify: fontset behavior is quirky in that the fontset Emacs ends up using seems
to differ between `emacsclient` and normal `emacs`, between terminal and
graphical frames, and even between different locales. While there is a way to
get the current active fontset (`(frame-parameter nil 'font)`), this method is
unreliable and may cause errors like [this one](https://lists.gnu.org/archive/html/emacs-devel/2006-12/msg00285.html).

After all kinds of attempts and DuckDuckGoing (that really rolled right off the
tongue, and no, I am [not the first one](https://www.reddit.com/r/duckduckgo/comments/8cm51u/what%5Fing%5Fverb%5Fdo%5Fyou%5Fuse%5Ffor%5Fduckduckgo/)), I finally found the [answer](https://stackoverflow.com/questions/17102692/using-a-list-of-fonts-with-a-daemonized-emacs): just define
a new fontset instead of modifying existing ones.

```emacs-lisp
(defvar user/standard-fontset
  (create-fontset-from-fontset-spec standard-fontset-spec)
  "Standard fontset for user.")

;; Ensure user/standard-fontset gets used for new frames.
(add-to-list 'default-frame-alist (cons 'font user/standard-fontset))
(add-to-list 'initial-frame-alist (cons 'font user/standard-fontset))
```

I won't bore you with the exact logic just yet, as I also made other changes to
the fontset.


### Displaying Emoji {#displaying-emoji}

Solution to emoji display is similar—just specify a fallback font with emoji
support—or so I thought. I tried to use Noto Color Emoji as my emoji font,
only to find Emacs does not yet support colored emoji font. Emacs used to
support colored emoji on macOS, but this functionality was later [removed](https://github.com/emacs-mirror/emacs/blob/emacs-25.1/etc/NEWS#L1723).

I ended up using [Symbola](http://users.teilar.gr/~g1951d/) as my emoji fallback font (actually I used it as a
fallback for all Unicode characters), which provided comprehensive coverage over
[all the emoji](https://unicode.org/Public/emoji/11.0/emoji-test.txt) and special characters. Also note that since Emacs 25,
customization to the `symbols` [charset](https://www.gnu.org/software/emacs/manual/html%5Fnode/emacs/Charsets.html), which contains puncation marks, emoji,
etc., requires [some extra work](https://github.com/emacs-mirror/emacs/blob/emacs-25/etc/NEWS#L58):

```emacs-lisp
(setq use-default-font-for-symbols nil)
```

There does exist a workaround for colored emoji though, not with fancy fonts,
but by replacing Unicode characters with images. [`emacs-emojify`](https://github.com/iqbalansari/emacs-emojify) is a package
that provides this functionality. I ultimately decided against it as it does
slow down Emacs quite noticeably and the colored emoji image library is not as
comprehensive.


### Quotation Marks {#quotation-marks}

I've always used full-width directional curly quotation marks ("“”" and
"‘’") when typing in Chinese, and ASCII style ambidextrous straight quotation
marks (""" and "'") when typing in English. Little did I know there really is no
such thing as full-width curly quotation marks: there is only one set of curly
quotation mark codepoints in Unicode (U+2018, U+2019, U+201C, and U+201D) and
the difference between alleged full-width and half-width curly quotation marks
is caused solely by fonts. There have been [proposals](https://www.unicode.org/L2/L2014/14006-sv-western-vs-cjk.pdf) to standardize the two
distinct representations, but for now I'm stuck with this ambiguous mess.

It came as no surprise that these curly quotation marks are listed under
`symbols` charset, instead of a CJK one, thus using normal monospace font
despite the fact that I want them to show up as full-width characters. I don't
have a true solution for this—being consistent is the only thing I can do, so
I forced curly quotation marks to display as full width characters by overriding
these exact Unicode codepoints in my fontset. I'm not really sure how I feel
when I then realized ASCII style quotation marks also suffered from
[confusion](https://www.cl.cam.ac.uk/~mgk25/ucs/quotes.html)—maybe we are just really bad at this.

My fallback font configurations can be found on both [GitHub](https://github.com/shimmy1996/.emacs.d#fontset-with-cjk-and-unicode-fallback) and [Trantor Network](https://git.shimmy1996.com/shimmy1996/.emacs.d#fontset-with-cjk-and-unicode-fallback)
and I'll list them here just for sake of completeness:

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


## CJK Font Scaling {#cjk-font-scaling}

My other gripe is the width of CJK fonts does not always match up with that of
monospace font. Theoretically, full-width CJK characters should be exactly twice
of that half-width characters, but this is not the case, at least not in all
font sizes. It seems that CJK fonts provide less granularity in size, i.e. 16px
and 17px versions of CJK characters in Noto Sans CJK SC are exactly the same,
and does not increase until size is bumped up to 18px, while Latin characters
always display the expected size increase. This discrepancy means their size
would match every couple sizes, but different in between with CJK fonts being a
bit too small.

One solution is to specify a slightly larger default size for CJK fonts in the
fontset. However, this method would render `text-scale-adjust` (normally bound
to <kbd>C-x C-=</kbd> and <kbd>C-x C--</kbd>) ineffective against CJK fonts for some reason. A
better way that preserves this functionality is to scale the CJK fonts up by
customizing `face-font-rescale-alist`:

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

bWhile the font sizes might still go out of sync after `text-scale-adjust`, I am
not too bothered. The exact scaling factor took me a few trial and error to find
out. I just kept adjusting the factor until these line up (I found [this table](https://websemantics.uk/articles/font-size-conversion/)
really useful):

```nil
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云云
雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲雲
ㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞㄞ
ああああああああああああああああああああああああああああああああああああああああ
가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가가
```

Unfortunately, the CJK font I used has narrower Hangul than other full-width CJK
characters, so this is still not perfect—the solution would be to specify a
Hangul specific font and scaling factor—but good enough for me.

It took me quite some effort to fix what may seem like a minor annoyance, but at
least Emacs did offer the appropriate tools. By the way, I certainly wish I had
found [this article](https://www.emacswiki.org/emacs/FontSets) on Emacs Wiki sooner, as it also provides a neat write up of
similar workarounds.
