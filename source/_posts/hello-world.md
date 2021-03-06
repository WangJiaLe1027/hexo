---
title: Hello Hexo
---
为了准备周三的组内分享，利用开发间隙，做了这样一个完全由自己掌握的博客，极客感十足。


一直有写博客的情怀，就像[刘未鹏 | MIND HACKS](http://mindhacks.cn)在[为什么你应该现在就开始写博客](http://mindhacks.cn/2009/02/15/why-you-should-start-blogging-now/)中说的那样

<!--more-->
**用博客记录下你有价值的思考，会带来很多好处，却没有任何明显的坏处。**

**一次表达，无数次阅读，极大的增强了话语的复用性；**

**没有地域限制；**

**书写可以帮助你更好的思考；**

**"教"是最好的"学",如果一件事情你不能讲清楚，十有八九你还没有完全理解；**

举个例子看[其他组程序员的博客](http://blog.csdn.net/woaifen3344/article/details/50498847)可以很快的了解他。

非常多的好处，吸引着我跃跃欲试，于是我找了一些博客平台，准备开始自己的博客记录，但一段时间后发现，博客平台内容管理在其他公司数据库，真的很不方便，一点都不自由，像是被约束在一个狭小的空间里，不符合互联网的开放自由精神。

于是动手买了域名，买了空间，准备开始;
选择了[WordPress](https://cn.wordpress.org/)建站。
一切搞定之后，维持了一年；因为访问量感人。还要花钱在域名和空间里，真的很可怜啊，就没有再续费。当初购买的域名[helloaim.com](http://www.helloaim.com)已经变成一个泰国网站了……

直到现在，我遇到了[Hexo](https://hexo.io/)，太美了。

**Hexo: A fast, simple & powerful blog framework**

## 关于 Hexo
hexo是一个基于Node.js的静态博客框架。作者为台湾大学生[SkyArrow](https://twitter.com/tommy351)。

类似的还有[jekyll](http://jekyll.bootcss.com/),[octopress](http://octopress.org/)等；

Hexo的作者是这么说的：[Hexo 颯爽登場！](https://zespia.tw/blog/2012/10/11/hexo-debut/)


## 快速开始

### 工具准备
[homebrew](http://brew.sh/index_zh-cn.html)
``` bash
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
[node](https://nodejs.org)自动装好node和npm
``` bash
$ brew install node
```
[hexo](http://www.hexo.io/)
``` bash
$ npm install -g hexo-cli
```
### 建立博客

``` bash
//初始化blog
$ hexo init blog

//打开blog
$ cd blog

//生成静态blog
$ hexo g

//运行本地服务
$ hexo s
```
正常执行到这里，你的博客已经可以在本地访问了[http://localhost:4000](http://localhost:4000)

### 配置blog
[配置博客](https://hexo.io/zh-cn/docs/setup.html)
blog中的文件结构就是这样，主要的配置文件在_config.yml，可以配置根目录，主题，作者等。配置好了再次执行**$ hexo g **生成静态文件public。
``` bash
.
├── _config.yml//主要配置文件
├── package.json
├── scaffolds
├── public//生成的静态页面文件
├── source
|   ├── _drafts
|   └── _posts
└── themes//配置主题
```

### 发布
如果你选择和我一样免费发布在github上；那你需要注册一个[github](https://github.com)的账号。并且新建一个库；

**按照'username.github.io'命名库。**

![img](http://7xrxzy.com1.z0.glb.clouddn.com/QQ20160316-0.png)
将hexo生成的public中的文件push到库中。
这样在浏览器输入username.github.io就可以访问自己的博客了。

### 写新的blog
``` bash
$ hexo new newBlogTitle

//在source文件夹中找到newBlogTitle.md 编辑博客内容。

$ hexo g

//提交public文件的变动到username.github.io库中即可更新。
```