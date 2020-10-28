---
title: opencv
date: 2020-10-28 11:28:38
tags: img
categories: img
mp3:
cover:
---
# 人脸检测
```python
import cv2
import os
from pathlib import Path

IMAGE_PATH = "luhan1994.github.io/assets/"

p = Path(IMAGE_PATH)
file_names = [x.name for x in p.iterdir()]
for file_name in file_names:
    img = cv2.imread(IMAGE_PATH + file_name)
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    face_cascade = cv2.CascadeClassifier(
        cv2.data.haarcascades + 'haarcascade_frontalface_alt.xml')
    faces = face_cascade.detectMultiScale(
        gray, scaleFactor=1.2, minNeighbors=5)
    for (x, y, w, h) in faces:
        img = cv2.rectangle(img, (x, y), (x + w, y + h), (0, 255, 0), 1)
        cv2.imwrite(IMAGE_PATH + file_name, img)
```

[http://woshicver.com/](http://woshicver.com/)

[http://objectdetection.cn/](http://objectdetection.cn/)