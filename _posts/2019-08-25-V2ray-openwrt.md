---
layout: post
title:  "V2ray on Openwrt"
date:   2019-08-25 17:22:22 +0800
categories: tools
---

用于switch hgbshop的v2ray全局代理

dns使用90DNS: 163.172.141.219

![hbgshop](/assets/20190825200646.jpg)

config.json
```js
{
  "log":{
		"access": "/tmp/log/v2ray/access.log",
		"error": "/tmp/log/v2ray/error.log",
		"loglevel": "info"
	},
  "dns":{},
  "stats":{},
  "inbounds":[
	{
		"port": 5354,
		"tag": "dns",
		"protocol": "dokodemo-door",
		"settings": {
			"address": "163.172.141.219",
			"port": 53,
			"network": "tcp,udp",
			"timeout": 0,
			"followRedirect": false
		}
	},
	{
		"port": 1090,
		"tag": "door",
		"protocol": "dokodemo-door",
		"settings": {
		  "network": "tcp,udp",
		  "timeout": 0,
		  "followRedirect": true
		},
		"sniffing": {
			"enabled": false,
			"destOverride": ["http", "tls"]
		},
		"allocate": {
			"strategy": "always",
			"refresh": 5,
			"concurrency": 3
		}
	}],
	"outbounds":[
    {
      "streamSettings":{
        "network":"ws",
        "security":"tls",
        "wsSettings":{
          "path":"/veekxt_websocket_test"
        },
		"sockopt": {
			"mark": 255
		}
      },
      "settings":{
        "vnext":[
          {
            "address":"":"**************"",
            "users":[
              {
                "alterId":**,
                "id":"**************"
              }
            ],
            "port":***
          }
        ]
      },
      "tag":"out-0",
      "protocol":"vmess"
    },
    {
      "settings":{},
      "protocol":"freedom",
      "tag":"direct"
    },
    {
      "settings":{},
      "protocol":"blackhole",
      "tag":"blocked"
    }
  ],
  "routing":{
    "rules":[
		{
            "type": "field",
            "inboundTag": ["door", "dns"],
            "outboundTag": "out-0"
        }
    ],
    "domainStrategy":"IPOnDemand"
  },
  "policy":{},
  "reverse":{},
  "transport":{}
}

```
iptables
```shell
iptables -t nat -N V2RAY
iptables -t nat -A V2RAY -d 0.0.0.0/8 -j RETURN
iptables -t nat -A V2RAY -d ************** -j RETURN
iptables -t nat -A V2RAY -d 10.0.0.0/8 -j RETURN
iptables -t nat -A V2RAY -d 127.0.0.0/8 -j RETURN
iptables -t nat -A V2RAY -d 169.254.0.0/16 -j RETURN
iptables -t nat -A V2RAY -d 172.16.0.0/12 -j RETURN
iptables -t nat -A V2RAY -d 192.168.0.0/16 -j RETURN
iptables -t nat -A V2RAY -d 224.0.0.0/4 -j RETURN
iptables -t nat -A V2RAY -d 240.0.0.0/4 -j RETURN

iptables -t nat -A V2RAY -p tcp -j RETURN -m mark --mark 0xff
iptables -t nat -A V2RAY -p tcp -j REDIRECT --to-ports 1090
iptables -t nat -A PREROUTING -p tcp -j V2RAY
iptables -t nat -A OUTPUT -p tcp -j V2RAY

ip rule add fwmark 1 table 100
ip route add local 0.0.0.0/0 dev lo table 100

iptables -t mangle -N V2RAY_MASK
iptables -t mangle -A V2RAY_MASK -d 0.0.0.0/8 -j RETURN
iptables -t mangle -A V2RAY_MASK -d ************** -j RETURN
iptables -t mangle -A V2RAY_MASK -d 10.0.0.0/8 -j RETURN
iptables -t mangle -A V2RAY_MASK -d 127.0.0.0/8 -j RETURN
iptables -t mangle -A V2RAY_MASK -d 169.254.0.0/16 -j RETURN
iptables -t mangle -A V2RAY_MASK -d 172.16.0.0/12 -j RETURN
iptables -t mangle -A V2RAY_MASK -d 192.168.0.0/16 -j RETURN
iptables -t mangle -A V2RAY_MASK -d 224.0.0.0/4 -j RETURN
iptables -t mangle -A V2RAY_MASK -d 240.0.0.0/4 -j RETURN
iptables -t mangle -A V2RAY_MASK -p udp -j TPROXY --on-port 1090 --tproxy-mark 1
iptables -t mangle -A PREROUTING -j V2RAY_MASK

iptables -t nat -I PREROUTING -p udp --dport 53 -j DNAT --to 192.168.1.1
iptables -t nat -I PREROUTING -p tcp --dport 53 -j DNAT --to 192.168.1.1
```