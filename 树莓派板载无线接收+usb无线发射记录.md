---
title: 树莓派板载无线接收+usb无线发射记录
date: 2023-04-11 15:27:30
tags: 树莓派
---
# 流程如下
<!--more-->

```
将usb无线接口wlan0的IP配置成静态地址。默认板载无线网接口wlan1默认DHCP配置并接入因特网,与下述网页有所不同
```

<div style="position: relative; padding: 30% 45%;">
<iframe style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;" src="https://shumeipai.nxez.com/2018/03/13/raspberry-pi-double-network-cards-for-wireless-hotspot.html" frameborder="1" scrolling="yes" width="320" height="240"
</iframe>
</div>

## 网页备份
```
配置网络
安装 dnsmasq 和 hostapd

sudo apt-get update
sudo apt-get install dnsmasq hostapd udhcpd
将无线接口wlan0的IP配置成静态地址。外置无线网接口wlan1默认DHCP配置并接入因特网。在树莓派系统中，默认是DHCPCD配置网络接口。所以要告诉系统我们给wlan0分配静态IP地址，操作是打开配置文件并增加配置参数指令。

sudo nano /etc/dhcpcd.conf

interface wlan0
static ip_address=192.168.88.1/24
修改 /etc/network/interfaces 设置wlan1为 DHCP 并自动连接WIFI，wlan0 为固定IP

# 表示使用localhost
auto lo 
iface lo inet loopback
 
# wlan1 自动获取IP
auto wlan1
iface wlan1 inet dhcp
pre-up wpa_supplicant -Dwext -i wlan1 -c /etc/wpa_supplicant/wpa_supplicant.conf -B
 
# wlan0 为静态IP
auto wlan0
allow-hotplug wlan0
iface wlan0 inet static
address 192.168.88.1
netmask 255.255.255.0
重启树莓派

sudo service dhcpcd restart
sudo reboot
配置 UDHCP
编辑配置文件/etc/udhcpd.conf

start 192.168.88.100 #配置网段
end 192.168.88.150
interface wlan0 # The device uDHCP listens on.
remaining yes
opt dns 192.168.1.1 8.8.8.8
opt subnet 255.255.255.0
opt router 192.168.88.1 # 无线lan网段
opt lease 864000 # 租期10天
配置 HOSTAPD
创建hostapd.conf

sudo nano /etc/hostapd/hostapd.conf
添加如下配置

interface=wlan0
driver=nl80211
ssid=H-Pi
hw_mode=g
channel=6
wmm_enabled=1
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=abc12345
wpa_key_mgmt=WPA-PSK
rsn_pairwise=CCMP
修改 /etc/default/hostapd ，让系统每次启动都自动加载AP模式下的配置。

DAEMON_CONF="/etc/hostapd/hostapd.conf"
设置开机启动

sudo update-rc.d hostapd enable
配置 DNSMASQ
备份默认配置文件

sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.bak
添加以下内容

interface=wlan0
bind-interfaces
server=218.2.2.2
server=114.114.114.114
server=8.8.8.8
domain-needed
bogus-priv
dhcp-range=192.168.88.2,192.168.88.254,12h
设置 IPV4 转发
打开系统配置文件sysctl.conf，去掉IPV4转发那一行的#注释

sudo nano /etc/sysctl.conf

# Uncomment the next line toenable packet forwarding for IPv4
net.ipv4.ip_forward=1
外置无线接口共享给wlan0上网，需要配置NAT：

sudo iptables -F
sudo iptables -X
sudo iptables -t nat -APOSTROUTING -o wlan1 -j MASQUERADE
sudo iptables -A FORWARD -i wlan1 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i wlan0 -o wlan1 -j ACCEPT
解释参考
设置iptables:

    # iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

    // 将局域网内地址通过eth0接口伪装后转发出去

    # iptables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISH -j ACCEPT 

    //对于已经建立连接的包以及该连接相关的包都允许通过！

    # iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT 

    // 配置数据包转发时候的接口 

    echo "1" >/proc/sys/net/ipv4/ip_forward 打开系统数据转发   

注意：连不上外网时也许是没有配置网关，使用如下命令配置网关：

    route add default gw  192.168.0.1
保存以上防火墙规则

sudo sh -c "iptables-save> /etc/iptables.ipv4.nat"
在/etc/network/interfaces 末尾增加一行，设置为开机启动

up iptables-restore < /etc/iptables.ipv4.nat
编辑 /etc/network/if-pre-up.d/iptables
添加下面两行代码：

#!/bin/bash
/sbin/iptables-restore < /etc/iptables.ipv4.nat
保存退出，然后修改 iptables 权限：

sudo chmod 755 /etc/network/if-pre-up.d/iptables
通过获取DHCPCD来运行NAT需要创建一个新文件

sudo nano /lib/dhcpcd/dhcpcd-hooks/70-ipv4-nat
sudo iptables-restore < /etc/iptables.ipv4.nat
重启服务及树莓派

sudo service hostapd start
sudo service dnsmasq start
sudo reboot
其他配置
设置 wlan1 自动连接区域内WIFI

sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
在文件的末尾添加WIFI网络的名称以及密码，将要连接的wifi名称和密码替换即可。

network={
    ssid="SSID"
    psk="wifi_password"
}
使用sudo wpa_cli reconfigure命令启动连接

pi@raspberrypi:~ $ sudo wpa_cli reconfigure
Selected interface 'wlan0'
OK
转自小莱沃
```
# 简便方法在此（最终采取的方法）：<https://zhuanlan.zhihu.com/p/101089893>
购买的5块多的usb无线网卡不支持发射，只能将它设置为station模式，将树莓派设置为ap模式，流程记录如下：
```
1.安装create_ap
git clone https://github.com/oblique/create_ap 
cd create_ap 
make install
2.安装依赖
apt install util-linux procps hostapd iproute2 iw haveged dnsmasq
3.开启热点(我的树莓派插上usb网卡后默认名字为wlan0，板载无线网卡变为wlan1)
sudo create_ap wlan1 wlan0 awtrix 12345678
如果上述命令报错，没有开启的话，有可能是你的wifi已经开启并做了从机，导致开启热点失败；可以先 'sudo ifconfig wlan0 down'，然后再输入上述命令。（第一次）
4.设置wlan0为 DHCP 并自动连接WIFI
sudo nano /etc/network/interfaces 
# wlan1 自动获取IP
auto wlan0
iface wlan0 inet dhcp
pre-up wpa_supplicant -Dwext -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf -B
5.使用sudo wpa_cli reconfigure命令启动连接
pi@raspberrypi:~ $ sudo wpa_cli reconfigure
Selected interface 'wlan0'
OK
6.设置开机开启热点
sudo nano /etc/rc.local
添加在exit 0
sudo create_ap wlan1 wlan0 awtrix 12345678 &

```
双网卡配置<https://blog.csdn.net/weixin_48191138/article/details/107615067>
<https://shumeipai.nxez.com/2022/06/11/raspberry-pi-internal-and-external-network-routing-configuration.html>
## 命令简记
```
iwconfig  //用于查看无线网络，如果你设备上有无线网卡此时可用此命令来查看
lsusb   //查看usb,USB无线网卡是否已经被系统识别
lsmod  //查看内核模块是否支持你的无线网卡
sudo ifdown wlan0  //关闭wlan0
sudo ifup wlan0  //启用wlan0
sudo iwlist wlan0 scan | grep ESSID //检索附近的无线网络名称
```

<div style="position: relative; padding: 30% 45%;">
<iframe style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;" src="https://www.raspberrypi.com/documentation/computers/configuration.html#setting-up-a-routed-wireless-access-point.html" frameborder="1" scrolling="yes" width="320" height="240"
</iframe>
</div>