---
title: 蚂蚁笔记
date: 2020-11-07 10:56:09
tags: linux
categories: linux
mp3:
cover:
---

安装蚂蚁笔记


```shell
#启动专属mongo
docker run -d -p 37017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password -v /opt/leanote/mongo:/data/db --name leanote_mongo --restart always mongo:latest

#修改app.conf
vim /opt/leanote/app.conf

#创建leanote文件夹
mkdir -p leanote-data/{files,mongodb_backup,public/upload}
#启动leanote 应该使用birdge网络 用nginx 代理
docker run -d -v /opt/leanote/app.conf:/leanote/conf/app.conf -v /opt/leanote/leanote-data:/leanote/data --name leanote --network host --restart always jim3ma/leanote
```

[https://hub.docker.com/r/jim3ma/leanote](https://hub.docker.com/r/jim3ma/leanote)