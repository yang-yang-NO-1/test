---
title: 树莓派python相关
date: 2023-04-14 15:27:30
tags: 树莓派
---
# 安装pip
<!--more-->
```
python3 -V  //查看python版本
pip3 -V  //查看pip版本
sudo apt-get install python3-pip  //安装pip3
pip install -r requirements.txt //安装requirments内的库
http://pypi.mirrors.ustc.edu.cn/simple/
pip install <包名> -i http://pypi.v2ex.com/simple  //指定单次安装源
国内pypi镜像
V2EX：pypi.v2ex.com/simple
豆瓣：http://pypi.douban.com/simple
中国科学技术大学：http://pypi.mirrors.ustc.edu.cn/simple/
```
# 定时运行程序
```
sudo nano /etc/crontab
按需添加即可
00 07 * * * root python3 /home/pi/wechat-push-multi/main.py  //每天7点运行该py程序
```
# 树莓派网络测速
```
sudo apt-get install speedtest-cli //安装speedtest
speedtest //运行speedtest
sudo apt-get remove speedtest-cli  //卸载speedtest
```
