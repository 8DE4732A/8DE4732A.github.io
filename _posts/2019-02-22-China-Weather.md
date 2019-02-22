---
layout: post
title:  "China Weather"
date:   2019-02-22 16:34:22 +0800
categories: tools
---

## China Weather 

weather.py
```python
import urllib.request
import json
import time
req = urllib.request.Request("http://d1.weather.com.cn/satellite2015/JC_YT_DL_WXZXCSYT_4A.html?jsoncallback=readSatellite&callback=jQuery18208455971171376718_" + str(round(time.time() * 1000)) + "&_=" + str(round(time.time() * 1000)))
req.add_header('Referer', 'http://www.weather.com.cn/satellite/')
req.add_header('User-Agent', 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.109 Safari/537.36')

with urllib.request.urlopen(req) as f:
    data = f.read().decode('utf-8')
    data = data[14:-1]
    j = json.loads(data.replace('\'','\"'))
    for a in j['radars']:
        print(a['ft'])
        urllib.request.urlretrieve("http://pi.weather.com.cn/i/product/pic/l/sevp_nsmc_" + a['fn'] + "_lno_py_" + a['ft'] + ".jpg", "D://weather/" + a['ft'] + ".jpg")
```

ln.py
```python
from pathlib import Path

p = Path('D:\weather')
file_names = [x.name for x in p.iterdir()]
nums = sorted([x.split('.')[0] for x in file_names])

for i in range(len(nums)):
    Path('D:\weather-ffmpeg\\' + '{0:06d}'.format(i) + '.jpg').symlink_to('D:\weather\\' + nums[i] + '.jpg')

```

convert to mp4
```shell
ffmpeg -f image2 -framerate 5 -i d:\\weather-ffmpeg\%06d.jpg -s 960x650 weather.mp4
```

<video src="/assets/weather.mp4" controls="controls"></video>