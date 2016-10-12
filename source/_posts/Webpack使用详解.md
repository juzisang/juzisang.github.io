---
title: Webpack使用详解
date: 2016-10-03 14:26:31
categories: 自动化构建工具
tags:
- Webpack
- Web
---

webpack是近期最火的一款模块加载器兼打包工具，它能把各种资源，例如JS（含JSX）、coffee、样式（含less/sass）、图片等都作为模块来使用和处理。

webpack是用来开发SPA(单页面应用)的利器，React 和 Vue 等框架，推荐使用webpack来开发单页应用

<!--more-->

## 安装

### 安装webpack

```
//初始化项目
cnpm init

//全局安装，第一次安装使用一次
cnpm install webpack -g

//添加项目依赖
cnpm install webpack --save-dev

```

### 安装cnpm
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
注：由于一些不可描述的原因，导致npm在国内下载**速度缓慢**。这里我使用的cnpm为淘宝的**国内npm镜像**。[**cnpm官网地址**](http://npm.taobao.org/)

## webpack.config.js（配置文件）

webpack.config.js是webpack的配置文件，用来配置各个模块的加载和编译方式，比如es6，sass,less,的代码怎么编译。

### webpack.config.js基本配置

```js
//path为node的自带的API
var path = require('path')

module.exports = {
    // 入口文件
    entry: './src/main.js',
    output: {
        // 打包后输出的目录
        path: path.resolve(__dirname, './dist'),
        // 打包后资源文件的前缀
        publicPath: '/dist/',
        //打包后的文件名
        filename: 'build.js'
    },
}

```
运行上面代码，会**编译main,js**中的代码，合并成一个文件**build.js**
执行**webpack**  
![运行结果](http://odlhbbkmh.bkt.clouddn.com/16-10-5/10223730.jpg)

### webpack开发多页应用

```js
//path为node的自带的API
var path = require('path')

module.exports = {
    // 入口文件
    entry: {
        //以key val的来配置多个入口文件
        //注意这里的key，将会是编译生成的文件名
        main:'./src/main.js',
        test:'./src/js/test.js'
    },
    output: {
        // 打包后输出的目录
        path: path.resolve(__dirname, './dist'),
        // 打包后资源文件的前缀
        publicPath: '/dist/',
        //打包后的文件名
        //[name]为entry的key名 [Hash]为文件的hash值
        filename: '[name][Hash].js'
    },
}

```

![运行结果](http://odlhbbkmh.bkt.clouddn.com/16-10-5/70107406.jpg)

### 简化webpack命令
后续webpack的执行命令会越来越长，比如`webpack --progress --colors`  
如果每次都敲这么一大串命令，显然得不偿失。  

修改`cnpm init`生成的`package.json`  
**注：**`package.json`中是**不能写注释**的，这里只是为了演示

```json
{
  "name": "webpack-test01",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "dependencies": {},
  "devDependencies": {},
  "scripts": {
    //这里为增加的命令
    "build":"webpack",
  },
  "author": "",
  "license": "ISC"
}

```
以后运行只需要

```
npm run build
```


## webpack-dev-server（浏览器自动刷新）

虽然我们已经简化了编译代码的命令，但是如果我们每修改一次代码，都要npm run build一次，然后再刷新一下浏览器，想想都感觉有点可怕= =   
不过幸好，webpack有提供简便的方案

```
//安装webpack-dev-server
cnpm install webpack-dev-server --save-dev
```

**package.json**
```js
{
  "scripts": {
    "build": "webpack",
    "dev": "webpack-dev-server --devtool eval --progress --colors --hot --inline --content-base ./dist",
  }
}
```


-  webpack-dev-server ： 在 localhost:8080 建立一个 Web 服务器
-  --devtool eval ： 为你的代码创建源地址。当有任何报错的时候可以让你更加精确地定位到文件和行号
-  --progress ： 显示合并代码进度
-  --colors ：命令行中显示颜色！
-  --hot --inline：开启热更新
-  --content-base build ： 表示开启的服务器将会以dist为根目录

**main.js**

```js
var body = document.getElementsByTagName("body");
//修改了页面背景色
body[0].style.background='red';
```

**index.html**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>main</title>
</head>
<body>
//不要忘了引入bundle
<script src="dist/bundle.js"></script>
</body>
</html>
```

**注：** 这里bundle的引入需要特别注意下  

- **webpack-dev-server的热更新是把bundle文件编译在内存当中，所以我们在项目中是看不到编译后的bundle.js的**
- **bundle.js在html的引入的路径是向对于publicPath中设置的路径的，所以要写成dist/bundle.js**
- **如果我们没有设置publicPath，就可以直接写成bundle.js**




现在的目录结构为
```
 Test01
    │  package.json
    │  webpack.config.js
    │  
    ├─dist
    │      index.html
    │      
    └─src
        │  main.js
        │  
        └─js
                test.js
                
```

```
//运行这个就可以自动刷新页面了
cnpm run dev
```

## 引入文件

所谓的引入文件，就是把我们需要的模块，引入到**主文件(main.js)当中**，
webpack打包时，会把主文件当中引入的模块，打包成一个文件

### 引入方式

```
//ES6 模块
import MyModule from './MyModule.js';

//CommonJS
var MyModule = require('./MyModule.js');

//AMD
define(['./MyModule.js'], function (MyModule) {

});
```

- webpack支持上面三种引入文件的方式，其中webpack内置CommonJS,AMD两种方式，
- ES6件，必须经过编译（下面会讲）

webpack支持引入所有类型的文件，只要配置了对应的loader编译方式，可以加载sass,less,jsx等等

### 理解文件路径

加载一个模块，必须指定它的路径。以下面项目结构为例
```
     app
    │  package.json
    │  webpack.config.js
    │      
    └─src
        │  main.js
        │  util.js
        └─js
            test.js

```
我们打开main.js
```js
// ES6
import MyModule from './js/test.js';

// CommonJS
var MyModule = require('./js/test.js');

```

* `./`是当前路径的意思（当前main.js所在的路径）

**引入util.js**
```js
// ES6 相对路径
import utils from './utils.js';

// ES6 绝对路径
import utils from '/utils.js';

// CommonJS 相对路径
var utils = require('./utils.js');

// CommonJS 绝对路径
var utils = require('/utils.js');
```
相对路径是相对当前目录。绝对路径是相对入口文件的路径，这个案例中是 main.js。


## loader

loader使用来配置不同类型文件的编译处理方式

### 加载css

### 加载sass和less

### 加载图片

### 加载ES6



## webpack最佳实践





