---
layout: post
title: "ASCII Art With Python"
category: 
tags: ["python", "programming"]
---
{% include JB/setup %}

I was always curious about creating cool ASCII "pictures" from actual images and finally decided to figure out how to do it.
It's fairly simple. What we need to do is to resize image to fit in ASCII console (120 columns) keeping it's original ratio.
Then convert it to greyscale and map grey level to ASCII symbol. 

<!-- more -->

I decided to keep it simple so there are only 10 grey levels without any symbols randomizations, so white is always `' '` and black is always `@`.
The whole script is below.

```python
from PIL import Image

im = Image.open("images.gif")
ratio = float(im.size[1]) / float(im.size[0])
im = im.resize((120, int(120 * ratio)), Image.BILINEAR).convert("L")
greyscale = " .:-=+*#%@"
str=""
for y in range(0,im.size[1]):
    for x in range(0,im.size[0]):
        lum=255-im.getpixel((x,y))
        row= lum // 26
        possibles=greyscale[row]
        str=str+possibles
    str=str+"\n"
print str
```