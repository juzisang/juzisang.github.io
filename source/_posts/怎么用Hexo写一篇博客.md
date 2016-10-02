---
title: 怎么用Hexo写一篇博客
date: 2016-10-02 15:23:47
categories: Hexo
tags: 
- Hexo
- 笔记
---

<blockquote class="blockquote-center">这里是因为太久没更新，所以忘记Hexo怎么写blog的笨蛋橘子QAQ，所以，我决定写个模版</blockquote>

<!--more-->

## 复制粘贴即可写文章啦

```markdown
---
title: 怎么用Hexo写一篇博客
date: 2016-10-02 15:23:47
categories: Hexo
tags: 
- Hexo
- 笔记
---

这里是因为太久没更新，所以忘记Hexo怎么写blog的笨蛋橘子QAQ，所以，我决定写个模版
<!--more-->
```

## 各个属性的详细讲解

```markdown
---
title: 怎么用Hexo写一篇博客
//创建时间
date: 2016-10-02 15:23:47
//更新时间
updated: 2016-10-02 15:23:47
//开启评论
comments: true
//这干嘛的，我也不知道= =
permalink: ???

categories: Hexo 
tags:
- Hexo
- 笔记
---

这里是因为太久没更新，所以忘记Hexo怎么写blog的笨蛋橘子QAQ，所以，我决定写个模版

//在<!--more-->这个标签上的文字将会为首页列表的显示的摘要
<!--more-->

//下面为正文了

```

## 新建一篇文章

在你本地blog根目录下执行如下命令
```
hexo new "怎么用Hexo写一篇博客"
```
会在**source > _posts > 怎么用Hexo写一篇博客**中生成
默认会生成如下格式的文章
```
---
title: 我的文章
date: 2016-10-02 16:39:30
tags:
---
```

### 怎么修改默认生成文件格式

Hexo默认生成的格式只有三个配置项，如果我要加一个categories还要手动添加
那么如何使用上面的命令，自定义生成的文件格式呢  
**根目录 > scaffolds > post.md** 
这个文件为默认生成的格式,我修改成如下格式
```
---
title: {{ title }}
date: {{ date }}
categories: 
tags:
---

<!--more-->

```

### 怎么选择生成的文件格式

上面已经修改了默认生成的格式，但是如果我们要生成另外的格式
```
hexo new [page] "怎么用Hexo写一篇博客"
```
**根目录 > scaffolds >** 
同样 [page]为scaffolds下的<b>page.md</b>的格式，你也可以自己新建个新的文件，[ ]中写你要生成的文件名就行

## 添加categories页面

发现就算在格式中写了categories属性，但是点分类的时候，还是报404。
发现生成文件中是有categories文件夹的，但是其中没有index.html，所以404，解决办法
```
hexo new page categories
```
修改**根目录 > source > categories > index.md**
```
---
title: categories
date: 2016-10-02 15:53:01
//关键
type: "categories"
//关闭评论
comments: false
---
```

## 添加tags页面
同样
```
hexo new page tags
```
修改**根目录 > source > tags > index.md**
```
---
title: categories
date: 2016-10-02 15:53:01
//关键
type: "tags"
//关闭评论
comments: false
---
```

