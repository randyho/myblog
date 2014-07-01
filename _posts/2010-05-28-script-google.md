---
layout: blog
title:  "使用Google快照的油猴脚本"
categories: Coding
tags: Script Google
---


由于学校的教育网不让访问外网，Google快照就相当于半个代理，极大的方便了我查找资料。但是Google退出大陆后，我朝就把快照给墙了，给我的生活带来了不便。上周Google 推出 SSL 加密搜索，使用 Google 时不会出现链接重置的情况，重要的是快照可以正常查看了（因为网页快照之前就支持 SSL 加密连接了）。但是默认点击快照还是使用http协议，不想每次看快照都手动改成https，昨天在谷奥看到有网友写的脚本，就拿来用了。

<!--more-->

## Google快照-普通模式

Firefox 用户，请先安装[Greasemonkey](https://addons.mozilla.org/zh-CN/firefox/addon/748/)插件以驱动油猴脚本，Chrome 用户无需进行此步；然后，可以安装经过 Ray Chow 修改的[SSL Certificates Pro](http://userscripts.org/scripts/show/72944)脚本。

默认的脚本里面网址比较多，我就留了快照和reader，效果不错。但是每次打开网页会先出现一次链接重置，所以与作者联系了下，经过作者的修改，现在不会出现链接重置了。

不管怎样，终于用回Google快照了，爽哉！

## Google快照-纯文本模式

Ray Chow的[Google SSL油猴脚本](http://userscripts.org/scripts/show/77725)算是不错，但是每次搜索都自动切换到https去了，很不习惯。
在去食堂的路上想了下，大概明白了GreaseMonkey的原理，于是参考[Google Search URL Change(Cache and Tracking](http://userscripts.org/scripts/show/74154)和 Google Cached Text 自己写了个，以满足自己的需求。

原理是把Google返回的搜索结果的那个Cached的链接修改了，变成 https开头，“&strip=1” 结尾，这样每次点击Cached（网页快照）就会自动跳到纯文本模式的快照了。以下是js脚本：

    ```javascript
    // ==UserScript==
    // @name    Google Text Cache
    // @description    Change Google Cache URL
    // @include        http://www.google.com/search?*
    // @include        https://www.google.com/search?*
   
    // ==/UserScript==
   
    var cachedLinks = document.evaluate("//span[@class=\'gl\']/a[1]", document, null,
       XPathResult.UNORDERED_NODE_SNAPSHOT_TYPE, null);
    for (var i = 0; i < cachedLinks.snapshotLength; i++)
    {
        cachedLinks.snapshotItem(i).removeAttribute("onmousedown");
        cachedLinks.snapshotItem(i).href = cachedLinks.snapshotItem(i).href.replace
            ("http://webcache.googleusercontent.com",
            "https://webcache.googleusercontent.com");
        cachedLinks.snapshotItem(i).href = cachedLinks.snapshotItem(i).href + "&strip=1";
    }
    ```
