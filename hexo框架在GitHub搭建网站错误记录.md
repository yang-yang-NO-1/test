---
title: hexo框架在GitHub搭建网站错误记录
date: 2022-01-06 18:20:52
tags: hexo
---
# hexo框架在GitHub搭建网站错误记录
<!--more-->
1. 申请GitHub账号和安装hexo框架见
* https://zhuanlan.zhihu.com/p/60578464
* https://zhuanlan.zhihu.com/p/26625249

next配置见<https://zhuanlan.zhihu.com/p/60424755>
2. 部署 Hexo 到 GitHub Pages修改_config.yml 文件末尾的 Deployment 部分，修改成如下,**注意repository末尾的``.git``**
```
deploy:
  type: git
  repository: git@github.com:用户名/用户名.github.io.git
  branch: master
```
3. 修改主题至next时，本地预览网页出现``“ {% extends ‘_layout.swig‘ %} {% import ‘_macro/post.swig‘ as post_template %}“``问题，原因是原因是hexo在5.0之后把swig给删除了需要自己手动安装
```
npm i hexo-renderer-swig 
```
4. hexo常用的写博客相关的命令
```
hexo clean          # 清除缓存文件等
hexo g              # 生成页面
hexo s              # 启动预览
hexo d              # 部署
```
5. 侧边栏添加网易云音乐
```
打开网页版的网易云音乐，选择喜欢的音乐 （不需要会员的），点击生成外链播放器
``` 
![alt 网易云](https://pic2.zhimg.com/v2-fcb7d44ccdca3760c98db0d13817f2b5_r.jpg)
```
复制外链的代码
```
![alt 网易云](https://pic4.zhimg.com/v2-16eec195312cde7b1d257fac6f3c8d0b_r.jpg)
```
比如在侧栏插入这首歌的音乐播放器，修改 blog\themes\next\layout\_macro的sidebar.swig文件，添加刚刚复制的外链代码
```
![alt 网易云](https://pic4.zhimg.com/v2-03db51002497b27e4d5888e0efd577c7_r.jpg)
**重新生成、部署网页**

6. hexo框架使用markdown语法，详见
* https://www.runoob.com/markdown/md-tutorial.html

