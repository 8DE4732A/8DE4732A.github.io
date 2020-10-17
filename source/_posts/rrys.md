---
title: 人人影视
date: 2020-10-17 10:46:44
tags: linux
categories: linux
mp3:
cover:
---
树莓派接入了两个isp(长城宽带，移动),由于长城宽带限制，使用人人影视时只想用移动的网。
使用的方案可以用veth，使用不同的网路命名空间加iptables, 还有直接借用docker

使用docker解决此问题
```Dockerfile
FROM ubuntu:18.04
LABEL maintainer "8de4732a"
EXPOSE 3001
VOLUME ["/opt/work/rrshareweb/data"]

WORKDIR /rrys
RUN apt update && apt -y install wget && wget http://appdown.rrys.tv/rrshareweb_rpi.2.20.tar.gz && tar -zxf rrshareweb_rpi.2.20.tar.gz
CMD ["/rrys/rrshareweb_rpi/rrshareweb"]
```
```shell
docker build . -t rrys:0.0.1
```
alpine启动不了，只能用ubuntu了

启动
```shell
docker run -it -v /mnt/sd/rrys:/opt/work/rrshareweb/data -p 3001:3001 -name rrys rrys:0.0.1
```

设置路由规则
```
ip route add default via 192.168.1.1 dev wlan1 table 100
ip route add 192.168.99.0/24 via 192.168.1.1 dev eth0 table 100
ip rule add from 172.17.0.0/16 table 100

或者利用MARK
iptables -A PREROUTING -t mangle -s 172.17.0.0/16 -j MARK --set-mark 3
ip rule add fwmark 3 table 100
```


参考

[http://linux-ip.net/html/tools-ip-routing.html](http://linux-ip.net/html/tools-ip-routing.html)

[http://www.study-area.org/tips/adv-route/Adv-Routing-HOWTO-4.html](http://www.study-area.org/tips/adv-route/Adv-Routing-HOWTO-4.html)