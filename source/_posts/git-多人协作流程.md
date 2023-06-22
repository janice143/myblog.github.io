---
title: git 多人协作流程
date: 2022-08-17 19:10:27
tags:
academia: true
---

## 基本指令

```shell
git clone *** # 下载远程仓库的代码
git branch develop master    # 从master分支上新建develop分支
git checkout develop    # 切换到develop分支
# 此处可进行功能开发，并add和commit到develop分支
在多人协作中，可以新建自己的分支，
git checkout -b dev1

在这条分支上开发完后，可以把这条分支合并到develop分支上，
git checkout develop // 切换到develop分支
git merge dev1 // 合并dev1

很多时候合并会报错，因为很有可能团队其他人也在develop分支上进行修改了，这时候需要拉取远端的代码，查看更新，解决冲突
git pull origin develop    # 更新远端代码，看develop分支是否有更新（无更新）

检查没问题后，提交到远程仓库
git add . 
git commit -m "message"
git push origin develop # 推送develop分支到远端的origin/develop

一般来讲，在push之前最后pull一下


当你完成一个develop分支的代码更新之后，你再次回到dev1分支，或者新建其他分支，继续完善项目，首先你需要git pull origin develop拉取最新的代码

如果需要删除分支，
git checkout -d dev1

强制切换分支
git checkout develop -f


```

## 参考博客

1. [图文详解如何利用Git+Github进行团队协作开发](https://zhuanlan.zhihu.com/p/23478654)

2. [使用Git进行多人协作开发](https://juejin.cn/post/6844904168033894414#heading-9)

## 详细步骤版

1、首先，你需要在远程仓库创建一个新的项目（github、gitee或是你自己的远程服务器），这个新的项目默认会有一个主分支 **master**,现在你需要新建一个 **dev** 分支，这个dev分支就是所谓的主战场，在以后的开发中，就在这个**dev**以及**dev**下的分支进行开发。

2、然后使用git命令***git clone\***到一个空的文件夹



3、克隆下来的这个项目相当于你在远程创建的那个项目的镜像，所以现在你本地的项目里也有个**dev**分支，当然也有**master**分支，现在切换到dev分支 ***git checkout dev\***



4、现在就可以在这个dev分支上新建自己的分支愉快的开发了，命令 ***git checkout -b dev1\***



5、由于是多人开发，另一个人也可以以同样的方式（经历2、3、4步骤后）新建一个自己的分支**dev2** 命令***git checkout -b dev2\***

6、在dev1 或 dev2 开发完成后，需要提交到本地仓库，在dev1分支下使用命令 git add . 和 git commit -a -m 'commit msg'



7、提交完之后，需要汇总到**dev**分支上，先切回**dev**分支，命令***git checkout dev\***，这个时候是没有你在dev1上修改或添加的东西的，需要合并之后才会有；

8、合并 在**dev**分支下使用命令 ***git merge dev1\***



现在就可以在dev分支下看到你在dev1分支下修改的东西了

9、提交 合并到dev分支之后就是提交到远程仓库的dev分支，可以先看一下现在都有什么分支，命令 ***git branch -a\***



10、提交到远程dev 命令 ***git push origin dev\***



11、现在远程仓库的dev分支上已经是最新的代码了；最后再把远程的dev分支合并到远程的master主分支上就可以了。一般主分支主要是存放比较稳定版本的版本，dev分支才是最活跃的。


