---
title: 树莓派python相关及测试、重启日志
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
# 定时运行程序(/etc/crontab)系统级别的定时任务
```
sudo nano /etc/crontab
按需添加即可
00 07 * * * root python3 /home/pi/wechat-push-multi/main.py  //每天7点运行该py程序（出现错误，只会运行一次）
```
crontab任务在执行时所能读取到环境变量与用户登录后所读取到到的环境变量是不同的。 用户登录shell后所能读取到的环境变量通常定义在如下几个地方：~/.bashrc，/etc/profile，而crontab定时任务所能读取到的环境变量是定义在 /etc/crontab中的
解决办法：将所有使用的非bash内置命令都改为绝对路径调用，如下即可
```
30 07 * * * root /usr/bin/python3 /home/pi/wechat-push-multi/main.py

```
另外
```
在脚本开头先加载环境变量
将需要用到的命令加到环境变量中
export PATH=$PATH:/usr/local/bin/python3
再执行命令
将/usr/local/bin/python3 添加到/etc/profile或者~/.bashrc中，然后在crontab任务的执行命令中动态加载环境变量
登录后复制 
source /etc/profile
source /home/appadmin/.bash_profile
```
# 树莓派网络测速

```
sudo apt-get install speedtest-cli //安装speedtest
speedtest //运行speedtest
sudo apt-get remove speedtest-cli  //卸载speedtest
```
# 系统重启时间查询
```
//Linux last 命令用于显示用户最近登录信息。
//单独执行 last 指令，它会读取位于 /var/log/目录下，名称为 wtmp 的文件，并把该文件记录登录的用户名，全部显示出来。
//以reboot system boot 3.10.0-1127.19.1 Fri Apr 9 19:59 - 10:48 (116+14:49)为例，它所表达的意思是：
//Linux内核版本3.10.0-1127.19.1，系统重启，4月9日，星期五，19:59开机，持续时间116天14小时49分钟
last
//上线时间
//以10:48:43 up 116 days, 22:49, 3 users, load average: 0.08, 0.08, 0.06为例，它所表达的意思是：
//当前时间是10:48:43，已经持续运行116天22小时49分，两个用户登录过，系统过去1分钟、5分钟和15分钟内的平均负载分别是0.08, 0.08, 0.06。
uptime 
```