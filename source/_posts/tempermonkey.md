---
title: tempermonkey
date: 2020-10-30 13:56:58
tags: js
categories: js
mp3:
cover:
---
恰到好处的油猴

[http://woshicver.com/](http://woshicver.com/) 去除关注
```javascript
// ==UserScript==
// @name         New Userscript
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  try to take over the world!
// @author       You
// @match        http://woshicver.com/ThirdSection/*
// @grant        none
// ==/UserScript==

(function() {
    'use strict';
    $('#read-more-mask').remove();
    $('#read-more-wrap').remove();
    var style = $('#main').first().attr("style");
    $('#main').attr("style", style.replace(/hidden/, "false"));
    $('footer').remove();
})();
```