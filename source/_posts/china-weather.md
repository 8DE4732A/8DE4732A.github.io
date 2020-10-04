---
title: 气象云图
date: 2020-10-04 12:04:15
tags: python
categories: python
mp3:
cover:
---
发现[中国天气网](http://www.weather.com.cn/satellite/)的气象云图挺好看的，vps上搞了个定时任务，抓取图片，生成视频

定时抓取
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

生成图片
先创建软链接用于ffmpeg
```python
from pathlib import Path

p = Path('D:\weather')
file_names = [x.name for x in p.iterdir()]
nums = sorted([x.split('.')[0] for x in file_names])

for i in range(len(nums)):
    Path('D:\weather-ffmpeg\\' + '{0:06d}'.format(i) + '.jpg').symlink_to('D:\weather\\' + nums[i] + '.jpg')
```

```shell
/root/ffmpeg/ffmpeg -f image2 -i /root/weather-ffmpeg/%06d.jpg -vcodec libx264 -r 10 /var/http/weather.mp4
```


[https://github.com/8DE4732A/china-weather](https://github.com/8DE4732A/china-weather)