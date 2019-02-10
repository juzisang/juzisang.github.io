---
title: JS传递数据给IOS Android
date: 2016-04-24
categories:
  - Android
tags:
  - Android
  - JavaScript
---

最近学习 Html，需要调用传递数据到 IOS 和 Android，写博客记录下~

<!--more-->

最近学习 Html，需要调用传递数据到 IOS 和 Android，写博客记录下~

## 一、判断设备是 Android 还是 IOS

```js
var browser = {
  versions: (function() {
    var a = navigator.userAgent,
      b = navigator.appVersion;
    return {
      trident: a.indexOf("Trident") > -1,
      presto: a.indexOf("Presto") > -1,
      webKit: a.indexOf("AppleWebKit") > -1,
      gecko: a.indexOf("Gecko") > -1 && a.indexOf("KHTML") == -1,
      mobile: !!a.match(/AppleWebKit.*Mobile.*/),
      ios: !!a.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/),
      android: a.indexOf("Android") > -1 || a.indexOf("Linux") > -1,
      iPhone: a.indexOf("iPhone") > -1,
      iPad: a.indexOf("iPad") > -1,
      webApp: a.indexOf("Safari") == -1
    };
  })(),
  language: (navigator.browserLanguage || navigator.language).toLowerCase()
};
```

用法：

```js
if (browser.versions.android) {
  //Android
} else if (browser.versions.ios) {
  //ios
}
```

## 二、调用 Android 的代码

Android 这边相对 IOS 要简单很多，叫 Android 提供一个类名，方法名直接调用即可

```js
//这里的Android全部都是Android程序猿给你里，叫他提供这两个调用就可以了
//data为传递过去的数据
Android.Android(data);
```

## 三、调用 IOS 的代码

```js
function GoIos(data) {
	var iframe = document.createElement("iframe");
	var url = "myapp:";
	url = url + "&data=" + data;
	iframe.src = url;
	iframe.style.display = 'none';
	document.body.appendChild(iframe);
	iframe.parentNode.removeChild(iframe);
	iframe = null;
}
```

data 为 String 数据，传递过去，IOS 就会触发相应回调了

## 四、应用

```js
//照上面的写法，代码应该如下
function GoToApp(data){
//只能传递字符串，如果是JSON，转成字符串
data = JSON.stringify(data);
if (browser.versions.android) {
		//Android
		Android.Android(data);
	} else if (browser.versions.ios) {
		//ios
		GoIos(data);
	}
}
```

参考：
[js(javascript)与 ios(Objective-C)相互通信交互](http://www.skyfox.org/javascript-ios-navive-message.html)
