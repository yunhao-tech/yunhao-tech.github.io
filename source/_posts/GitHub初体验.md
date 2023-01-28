---
title: Github及Git使用记录
tags:
  - GitHub
  - Git
categories: Github
swiper_index: 2
abbrlink: 251714f7
---

# Github初体验

这不是要做算法课的编程项目嘛，两个人协同写代码。其实对于我来说，和别人一起写代码是比较新奇的体验。想起来前段时间注册的GitHub账号，这下正好派上用场！

## 搭建远程仓库
这一步倒没什么难的，create repository，输入仓库名就好。但注意命名时也要讲究一下，言简意赅。

##　本地安装Git
要想在本地使用仓库，pull远端代码到本地抑或是push本地代码到远端仓库，都需要本地安装Git．Git的GUI界面虽然很简单，但也是避免了在cmd下敲命令行了。

##  Git的使用术语？
经过我的摸索，终于对于Git这个工具熟练了一些。一般来说，从远端把代码同步到本地叫做pull; 与之相对，从本地同步到远端叫push. 
而一个仓库允许拥有多个分支branch，多个人可以在不同子分支上同时工作，每隔一段时间后merge到主分支main上（以前叫master,据说为了政治正确被改了）
push代码前，需要先commit; push时，一定要注意指定推送到哪个分支，这非常重要。

如果看到一个好的项目，可以fork一下，将代码仓库克隆到远程服务端；之后，可以在该仓库自由提交；也可以通过pull request试着将自己补充的代码贡献回原仓库。这样世界各地千千万万的程序员可以自由在某个项目上贡献自己的力量，哪怕只是加了一个小小的函数。

Remark: 就在写这篇blog的时候，我突然明白了为什么要叫pull request. 这是我们向仓库owner请求，将我们的代码拉(pull)到TA的仓库中。

## 本地创建仓库
可以从Git GUI新建仓库；也选择一个文件夹（一般新建一个），在该文件夹下进入cmd，通过命令
```bash
git init
```
指定该文件夹为新的仓库。这时在该文件夹下会创建一个名为.git的隐藏文件夹。
接下来，通过命令
```bash
git remote add origin Github上的仓库网址
```
来将该本地仓库连接到Github上的仓库。
接下来可以把远程端Github上的某个分支pull下来【下面的命令是将主分支main的内容拉下来】
```bash
git pull origin main
```
之后，你就可以在本地文件夹中看到相应的内容了。

## 本地代码push到远程端Github
当我们添加或修改了代码后，就可以将本地内容推送到远程了。这一步我喜欢用GUI操作。
打开相应的仓库，这时会显示我们已有的changes. Save these changes, 填写相应的commit message，大致描述为什么提交，新增或修改了哪些功能等等
然后点击commit, 之后push就ok了。

之前提到过Github上的主分支叫main，而在Git GUI中，默认还是推送到master. 如果不想之后手动merge, 在push前可以创建名为main的分支，push时选择该分支。

## 本地代码push到远程端Github （命令行方法）
在本地仓库目录下进入cmd，命令如下：
```bash
git pull --rebase origin main
git push origin main
```

# 使用Tips

1. 这次的项目使用Java写的，IDE用的是Eclipse. 创建Eclipse项目并写完一定量代码后，我决定先上传到远程仓库。
这时我犯了一个错误：直接把整个项目文件夹上传了。这里面还有一些Eclipse的配置文件。我现在还没有在其他IDE环境下运行pull下来的代码，但或许会有配置问题。这个另说。
然而当我在本地把项目文件夹（记作A）当作仓库，并下拉Github代码时，它在A下又创建了一个A. 有点类似套娃？？？
所以我觉得应该只把src, bin这两个上传就好了，其他的配置文件不用管。
在网上就这个问题查了一下，回答说利用.gitignore文件可以自定义上传时要忽略的文件。
并且，[一个可自动生成.gitignore文件的网站](https://www.toptal.com/developers/gitignore)只需要输入操作系统、IDE、编程语言就好了。

2. 就哪些文件需要上传而言，原则之一就是可以让别人fork代码后在任何IDE下都能用。
另外，如果有设计文档、说明文档、更新日志之类帮助理解项目内容的，也要放进去。


# 写在最后

Git真是个不错的工具，Github更是个很棒的平台，不愧是“饭桶中心”、“全球最大的同性交友网站”（手动狗头）
