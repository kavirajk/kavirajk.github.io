---
layout: post
title: "Python: Generating pdf containing emojis using reportlab"
summary: Creating pdf docs that supports emojis. We use "Emojipy" python library to parse emoji's unicode
comments: True
twitter: True
tags: [python, emoji, reportlab]
---

## Understanding Emoji
[Emojis](https://en.wikipedia.org/wiki/Emoji) 😃 🎉 👍 💕 have become so popular. Anyone who used [Whatsapp](https://www.whatsapp.com/) or
[Instagram](https://instagram.com/) knows how cool emojis are 😎. And they are everywhere now. Emoji originated in Japan. The word "emoji" means "Picture(e) letter(moji)" in Japanese.

Technically, emojis are [subset](http://unicode.org/emoji/charts/full-emoji-list.html) of [Unicode character set](http://www.unicode.org/Public/UCD/latest/ucd/UnicodeData.txt).  In short, Unicode is the collection of every writable symbol available on the planet. That being said, unicode includes symbols from every language that exist.

There are different types of encoding used to interpret different sets of unicode. Most popular is "UTF-8" encoding. To understand how unicode are
interpreted, stored and retrieved here is a great article [The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About
Unicode and Character Sets (No Excuses!)](http://www.joelonsoftware.com/articles/Unicode.html)

Every emoji is a single unicode character that can be upto 4 bytes long depending on type of encoding used. <strong>Yes, every character is just one byte is no longer true.</strong>

<!--break-->

### Interpreting Emoji
The letter <strong>'A'</strong> corresponds to <strong>'65'</strong> in decimal and <strong>'0x41'</strong> in hex. Similarly emoji 😃 corresponds
to <strong>'128515'</strong> in decimal and <strong>'0x1F603'</strong> in hexadecimal. Though every emoji(unicode) can be represented in decimal or
hexadecimal, the standard way to denote emoji is by 'code point'.

Code point for 😃 is <strong>U+1F603</strong>. An emoji(unicode) in code point is represented as "U+" (to denote unicode) followed by its
hex value "1F603". In Python it is represented as ```"\U0001f603" (u"\U0001f603" in python 2)```.
[Here](http://apps.timwhitlock.info/emoji/tables/unicode) is a list of emojis with its code points.

Every emoji have a name. Some apps like Github, Slack, Basecamp, etc, provide short name for emoji to make it easy to remember. You can find the list
of emoji with its short name here [emoji-cheat-sheet](http://www.emoji-cheat-sheet.com/)

### Interpreting with Python
Get the name and category of an emoji using ```unicodedata``` module of python
{% highlight python %}
In [1]: import unicodedata

# in python2 use u'\U0001f603'

In [2]: print('\U0001f603')
😃

In [3]: ord('\U0001f608')
128515

# use 'unichr' in python2

In [4]: chr(0x1f603) # hex
😃

In [5]: chr(128515) # decimal
😃

In [6]: unicodedata.name('\U0001f603') 
'SMILING FACE WITH OPEN MOUTH'

In [7]: unicodedata.category('\U0001f603') 
'So' # S- Symbol, o- Other

{% endhighlight %}
For more info on ```unicode.category```
[https://docs.python.org/2/howto/unicode.html#unicode-properties](https://docs.python.org/2/howto/unicode.html#unicode-properties)

Emojis are becoming popular. You happened to see emojis is many web apps and mobile apps. Rendering these emojis inside a browser or an app is another problem.

If you are able to see all colorful emojis I have used in this blog, then your browser supports emojis well.
Recently in our [company](https://launchyard.com) we happend to work on some features related to printing text containing emojis to pdf.

Wait.. But how do we render these emojis in pdf?. The problem boils-down to type of font you are using.

## Printing emojis to pdf

We are going to use ```reportlab``` python library to generate pdf

{% highlight bash %}
sudo pip install reportlab
{% endhighlight %}

<i>Note: All the code samples used here are tested using python 3.4</i>

Here is a simple example that writes text containing emoji to pdf

{% highlight python%}
from reportlab.platypus import Paragraph
from reportlab.lib.styles import getSampleStyleSheet
from reportlab.pdfgen import canvas
from reportlab.rl_config import defaultPageSize

width, height = defaultPageSize
pdf_content = "It's emoji time \u263A \U0001F61C. Let's add some cool emotions \U0001F48F \u270C. And some more \u2764 \U0001F436"

styles = getSampleStyleSheet()
para = Paragraph(pdf_content, styles["Title"])
canv = canvas.Canvas('emoji.pdf')

para.wrap(width, height)
para.drawOn(canv, 0, height/2)

canv.save()

{% endhighlight %}

Here is the preview of how it looks

<img src='/asserts/images/emoji.png' border="1px solid blue"/>

What!! this is obviously not we wanted. Unsupported emojis are shown as "black-box" by default font(Helvetica-Bold)

Lets try with custom font that supports emoji symbols.

### Using custom font
[Symbola](http://www.fonts2u.com/symbola.font) font supports most of the emoji symbols.

Simple program that writes emoji content to pdf using custom font(Symbola)

{% highlight python %}
from reportlab.platypus import Paragraph
from reportlab.lib.styles import getSampleStyleSheet
from reportlab.pdfgen import canvas
from reportlab.rl_config import defaultPageSize
from reportlab.pdfbase import pdfmetrics
from reportlab.pdfbase.ttfonts import TTFont

# Register font 'font_file' is location of symbola.ttf file

font_file = '/home/kaviraj/Downloads/fonts/Symbola_full/Symbola.ttf'
symbola_font = TTFont('Symbola', font_file)
pdfmetrics.registerFont(symbola_font)

width, height = defaultPageSize
pdf_content = "It's emoji time \u263A \U0001F61C. Let's add some cool emotions \U0001F48F \u270C. And some more \u2764 \U0001F436"

styles = getSampleStyleSheet()
styles["Title"].fontName = 'Symbola'
para = Paragraph(pdf_content, styles["Title"])
canv = canvas.Canvas('emoji.pdf')

para.wrap(width, height)
para.drawOn(canv, 0, height/2)

canv.save()

{% endhighlight %}

Here is the preview of how it looks with Symbola font.

<img src='/asserts/images/emoji_symbola.png' border="1px solid blue"/>

Cool. Symbola print these emojis as promised. But Wait! can't we get colorful emoji like we see in browser?. Yes. here comes the [Emojione](http://emojione.com/)

### Intro to Emojione
[Emojione](http://emojione.com/) provides collection of wide range of emojis(Almost every emojis supported by Instagram, Facebook, Slack, etc..)

Collection includes all the png and svg image files for every emoji they support. Emojione provide following cool features

Any emoji can be coverted from it's

* Unicode to corresponding PNG or SVG image
* Short name to corresponding PNG or SVG image
* Unicode to short name (and vice versa)
* Ascii symbol to corresponding PNG or SVG image( say, convert :) to 😄)

But unfortunately they doesn't provide any library for python. So we have written one by ourselves 👍 and we call it [Emojipy](https://github.com/launchyard/emojipy)

### Inserting emoji using Emojipy and reportlab

Usually any app(web or mobile) render emojis by replacing its unicode with corresponding PNG or SVG image(thats why you could see all the colorful emojis present in this blog).

Lets do the same thing with pdf

### Installing ```emojipy```
{% highlight bash%}
git clone git@github.com:launchyard/emojipy.git
cd emojipy
sudo python setup.py install
{% endhighlight %}

Below is the simple python program that use ```emojipy``` library to render emoji in pdf

{% highlight python%}
from reportlab.platypus import Paragraph
from reportlab.lib.styles import getSampleStyleSheet
from reportlab.pdfgen import canvas
from reportlab.rl_config import defaultPageSize
from reportlab.pdfbase import pdfmetrics
from reportlab.pdfbase.ttfonts import TTFont
from emojipy import Emoji
import re

# Pdf doesn't need any unicode inside <image>'s alt attribute
Emoji.unicode_alt = False


def replace_with_emoji_pdf(text, size):
    """
    Reportlab's Paragraph doesn't accept normal html <image> tag's attributes
    like 'class', 'alt'. Its a little hack to remove those attrbs
    """

    text = Emoji.to_image(text)
    text = text.replace('class="emojione"', 'height=%s width=%s' %
                        (size, size))
    return re.sub('alt="'+Emoji.shortcode_regexp+'"', '', text)

# Register font 'font_file' is location of symbola.ttf file

font_file = '/home/kaviraj/Downloads/fonts/Symbola_full/Symbola.ttf'
symbola_font = TTFont('Symbola', font_file)
pdfmetrics.registerFont(symbola_font)

width, height = defaultPageSize
pdf_content = "It's emoji time \u263A \U0001F61C. Let's add some cool emotions \U0001F48F \u270C. And some more \u2764 \U0001F436"

styles = getSampleStyleSheet()
styles["Title"].fontName = 'Symbola'
style = styles["Title"]
content = replace_with_emoji_pdf(Emoji.to_image(pdf_content), style.fontSize)

para = Paragraph(content, style)
canv = canvas.Canvas('emoji.pdf')

para.wrap(width, height)
para.drawOn(canv, 0, height/2)

canv.save()

{% endhighlight %}

Here is the preview how it looks with ```emojipy```

<img src='/asserts/images/emoji_emojipy.png' border="1px solid blue"/>

Wooo!! This is what we wanted!!

## References

* [http://unicode.org/emoji/charts/full-emoji-list.html](http://unicode.org/emoji/charts/full-emoji-list.html)
* [http://apps.timwhitlock.info/emoji/tables/unicode](http://apps.timwhitlock.info/emoji/tables/unicode)
* [http://www.joelonsoftware.com/articles/Unicode.html](http://www.joelonsoftware.com/articles/Unicode.html)
