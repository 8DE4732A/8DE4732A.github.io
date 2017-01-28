---
layout: post
title:  "Modify XiaoMi R1CL Router WLAN mac"
date:   2017-01-28 21:37:59 +0800
categories: tools
---

## 修改小米路由器青春版无线mac

1. R1CL刷PandoraBox固件： [链接](https://www.zhihu.com/question/37146304)

2. 修改无线mac:
 * 通过breed页面修改
 * mtd 刷写Factory 修改
 ```shell
random_mac(){
rm -rf /tmp/Factory.bin.1
rm -rf /tmp/Factory.bin.2
local mac1=$(echo $(hexdump -n6 -e '/1 ":%02x"' /dev/urandom) | sed 's/:/\\x/g')
local mac2=$(echo $(hexdump -n6 -e '/1 ":%02x"' /dev/urandom) | sed 's/:/\\x/g')
local reg1=s/\x28\x76\x00\x02\xF0\xB4\x29\x20\xB7\xB4/\x28\x76\x00\x02$mac1/g
local reg1=s/\x00\x20\x00\x00\x00\xF0\xB4\x29\x20\xB7\xB5/\x00\x20\x00\x00\x00$mac2/g
sed reg1 Factory.bin > Factory.bin.1
sed reg2 Factory.bin.1 > Factory.bin.2
mtd -r write /tmp/Factory.bin.2 factory
}; random_mac
 ```
