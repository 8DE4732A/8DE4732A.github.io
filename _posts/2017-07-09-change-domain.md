---
layout: post
title:  "Change Domain"
date:   2017-07-09 09:23:59 +0800
categories: tools
---

## 修改域名
1. 域名修改为 [liuping.win](http://liuping.win) 49块 10年。
2. 迁入cloudflare CDN，配置ssl。导致http://liuping.ml:9090/{\d}.js 浏览器拒绝访问，申请腾讯免费ssl证书挂到nginx上，再反向代理本地web.py后台。反向代理后程序获取不到真实请求原ip, 重新获取X-Real-IP头。[查看](https://liuping.ml:9090/query)。
3. 多说已经关闭，修改为gitment评论框架。
4. google搜索反向代理改为 [g.liuping.win](http://g.liuping.win)
5. 域名邮箱使用阿里云免费企业邮箱，![liuping@liuping.win](/assets/email_image.png)
6. 原域名增加重定向声明。[fireliuping.me](http://fireliuping.me)
