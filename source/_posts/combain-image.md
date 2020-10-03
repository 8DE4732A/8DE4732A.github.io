---
title: 合并图片
date: 2020-10-03 14:09:25
tags: python
categories: python
---
```python
import sys
from PIL import Image

width = 0
height = 0
for imageFile in sys.argv[1:]:
    with Image.open(imageFile) as im2:
        width = width + im2.size[0]
        height = max(height, im2.size[1])
im = Image.new('RGB', (width, height))
width = 0
height = 0
for imageFile in sys.argv[1:]:
    with Image.open(imageFile) as im2:
        im2_width = im2.size[0]
        im2_height = im2.size[1]
        im.paste(im2.crop((0, 0, im2_width, im2_height)), (width, 0, width + im2_width, im2_height))
        width = width + im2_width
im.save('result.jpeg', 'JPEG')

```