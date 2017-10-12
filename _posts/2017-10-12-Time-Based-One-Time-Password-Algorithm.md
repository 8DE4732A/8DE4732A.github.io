---
layout: post
title:  "TOTP: Time-Based One-Time Password Algorithm"
date:   2017-09-29 11:22:22 +0800
categories: learning
---

```shell
pip install pyopt
```

```python
import pyopt
import base64

topt = pyopt.TOTP(base64.b32encode("123123"))
topt.now()
totp.verify(492039)
```

google au:
'otpauth://totp/somelabel?secret=' + encodedForGoogle
