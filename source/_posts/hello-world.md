---
title: 第一个博客网站&第一篇博客
tags:
  - hexo
categories: 博客个性化
swiper_index: 1
abbrlink: bc4240cc
---

## 第一个博客网站

欧吼，起飞！用了一天上午搭好了网站，又在接下来的几个小时中对它进行了一些个性化的设置。
用了node, git, hexo, sublime走完了整个搭建流程，主题我暂时选了大路边上的Next


首先要感谢一些UP主和博主们的分享。
[手把手教你搭建属于自己的hexo+github博客](https://www.bilibili.com/video/BV1cW411A7Jx) 这真是保姆级教学了，对小白十分友好。
[live2d-widget-models](https://github.com/xiazeyu/live2d-widget-models) 这对我网站看板娘的搭建帮助极大
[小晓晓晓林](https://xiaoxiaoxiaoxiaolin.github.io/posts/ccfc6699.html/) 在网站个性化方面，借鉴了一些这位博主的经验
[Hexo设置/Next7.0+背景的设置](http://mkco.xyz/2020/04/02/Hexo%E8%AE%BE%E7%BD%AE/Next7.0+%E8%83%8C%E6%99%AF%E7%9A%84%E8%AE%BE%E7%BD%AE/) 在较新版本的Next中设置背景图或背景色
[网站图标下载](https://www.flaticon.com/) 很棒的网站，好多可爱的icon图标可以下载
[一篇关于blog写作技巧的博客](http://yearito.cn/posts/hexo-writing-skills.html) 写得也不错



## 正文
搭建这个网站的初衷，是为了记录一下学习过程中遇到的技术难题和解决方法，以及一些有趣的课程和知识。作为一篇个人博客，我也会发布一些随笔。

我目前对于Markdown语法挺不熟悉，但是多练练就好了吧，正好锻炼一下Markdown语法

先Mark一下写博客的流程：
1. 在sublime中打开博客所在目录，在source下找到_posts，在该文件夹下新建.md文件即可。
2. 每篇博客的head一般都有title, tags, categories
语法Example:
```bash
title: 博客
tags:
  - hexo
  - github //多标签语法
categories: 博客个性化
```

3. 在博客目录下运行cmd，命令如下。
```bash
hexo clean 
hexo g //生成静态网页
hexo server //生成静态网页后，可以先在本地预览一下，最后再推送
hexo d //推送到GitHub
```



最后，
Welcome to my small warm house.
