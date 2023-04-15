---
title: git、github、blog使用笔记
date: 2022-04-20 11:53:09
tags: git
---
# git命令、新建博客命令记录（clone加速在http://后加gitclone.com/）
<!--more-->
## git命令备忘
```
#初始化
git init
#用命令git add告诉Git，把文件添加到仓库：
git add *
#提交本地仓库
git commit -m "all is new"
#添加
git remote add origin 你的仓库地址
#提交
git push -u origin master
其他命令
添加一个或多个文件到暂存区：
git add [file1] [file2] ...
添加指定目录到暂存区，包括子目录：
git add [dir]
添加当前目录下的所有文件到暂存区：
git add .
以下实例拷贝远程 git 项目，本地项目名为 another-runoob-name：
git clone 你的仓库地址 another-runoob-name
git status 命令用于查看在你上次提交之后是否有对文件进行再次修改。
git status
通常我们使用 -s 参数来获得简短的输出结果：
git status -s
显示所有远程仓库：
git remote -v
以下我们先载入远程仓库，然后查看信息：
git clone https://github.com/tianqixin/runoob-git-test
cd runoob-git-test
git remote -v
origin  https://github.com/tianqixin/runoob-git-test (fetch)
origin  https://github.com/tianqixin/runoob-git-test (push)
origin 为远程地址的别名
git push 命用于从将本地的分支版本上传到远程并合并。
命令格式如下：
git push <远程主机名> <本地分支名>:<远程分支名>如果本地分支名与远程分支名相同，则可以省略冒号：
git push <远程主机名> <本地分支名>
实例
以下命令将本地的 master 分支推送到 origin 主机的 master 分支。
git push origin master
相等于：
git push origin master:master
如果本地版本与远程版本有差异，但又要强制推送可以使用 --force 参数：
git push --force origin master
删除主机的分支可以使用 --delete 参数，以下命令表示删除 origin 主机的 master 分支：
git push origin --delete master
git版本回退并覆盖远程仓库
git reset --hard HEAD^
git push -f //此时如果用“git push”会报错，因为我们本地库HEAD指向的版本比远程库的要旧
```
[git命令详细教程](https://www.runoob.com/git/git-basic-operations.html)   
https://www.runoob.com/git/git-basic-operations.html
## 备忘新建博客命令
```
git bash here
 hexo new <title>
 hexo new "我的第一篇文章"
 hexo g  生成博客
 hexo d 部署博客
 修改代理ip，在gitbash中修改
git config --global http.proxy(查询http代理)
git config --global http.proxy http://192.168.11.251:7890（修改http代理）

```