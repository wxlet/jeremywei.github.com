---
layout: post
title: 浏览器中F5和CTRL F5的行为区别
city: 南京
tags: [tech]
---

## 前言

在印象中，浏览器中的F5和刷新按钮是一样的效果，都是对当前页面进行刷新；Ctrl-F5的行为也是刷新页面，但是会清除浏览器缓存，这在前端调试时候会常用。二者真正的区别是什么呢？在stackoverflow上有人给出了很详细的[解释][1]，整理如下。

## 说明

在不同的浏览器中F5和CTRL-F5的行为是不一样的，但是他们的主要行为还是非常相似的，以下结果是在FF，IE，Opera和Chrome中进行过测试得出。

* F5使用缓存，并且只有在资源内容发生变化的时候才会去更新资源。

当刷新一个页面的时候，浏览器会尝试使用各种类型的缓存，并且会发送`If-Modified-Since`头到服务器，如果服务器返回`304 Not Modified`，那么浏览器会使用本地的缓存；如果服务器返回`200 OK`和资源内容，那么浏览器会使用返回的资源内容，并把资源内容进行缓存，待下次使用。

* CTRL-F5 强制更新页面资源的缓存。

MSIE会发送`Cache-Control: no-cache`头，Firefox和Chrome除了发送`Cache-Control: no-cache`头之外，还会发送`Pragma: no-cache`头。Opera比较另类，不发送任何和缓存相关的头。

以下表格很直观的表明了F5和CTRL-F5的行为，由于原文中测试的浏览器版本较低，所以进行了更新。

	F5 and CTRL-F5
	┌────────────┬───────────────────────────────────────────────┐
	│  UPDATED   │                 Firefox 3.x 4.x               │
	│2011-04-24  │  ┌────────────────────────────────────────────┤
	│            │  │              MSIE 7 8                      │
	│            │  │  ┌─────────────────────────────────────────┤
	│            │  │  │           MSIE 9                        │
	│            │  │  │  ┌──────────────────────────────────────┤
	│            │  │  │  │        Chrome 10                     │
	│            │  │  │  │  ┌───────────────────────────────────┤
	│            │  │  │  │  │     Opera 11                      │
	│            │  │  │  │  │  ┌────────────────────────────────┤
	│            │  │  │  │  │  │ I = "If─Modified─Since"        │
	├────────────┼──┼──┼──┼──┼──┤ P = "Pragma: No─cache"         │
	│          F5│IM│IM│I │IM│I │ C = "Cache─Control: no─cache"  │
	│     CTRL─F5│CP│C │C │CP│- │ M = "Cache─Control: max─age=0" │
	│  Click Icon│IM│I │I │IM│I │ Click Icon= "a mouse click on  │
	│            │  │  │  │  │  │ refresh icon"                  │
	└────────────┴──┴──┴──┴──┴──┴──-─────────────────────────────┘

## HTTP协议说明

HTTP/1.1规范14.9.4中规定：

* End-to-end reload(即CTRL-F5强制刷新)会发送如下HTTP头：     
Cache-Control: no-cache    
Pragma: no-cache     

* Specific end-to-end revalidation(即F5 刷新)会发送如下HTTP头：     
Cache-Control: max-age=0    
If-Modified-Since: Fri, 15 Apr 2011 12:08:21 GMT


## 参考

* [http://stackoverflow.com/questions/385367/what-requests-do-browsers-f5-and-ctrl-f5-refreshes-generate][1]


[1]: http://stackoverflow.com/questions/385367/what-requests-do-browsers-f5-and-ctrl-f5-refreshes-generate
