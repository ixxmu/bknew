---
title: 软路由_树莓派3B设置
author: oknet
date: '2020-07-14'
slug: how to setup a boundless network in raspberry 3B
categories:
  - 折腾
tags:
  - 软路由
  - 树莓派
draft: no
---
树莓派3B充当旁路由，吃灰未拆封的3B感觉要派上用场了^_^

主要参考[*这篇文章*](https://mlapp.cn/376.html)作者编译的有树莓派全系以及x86_64镜像文件，可以自主下载刷机或者作为docker导入使用；

以下内容针对树莓派3b
### 1 刷写树莓派3b的固件；
[固件地址](https://github.com/openfans-community-offical/Debian-Pi-Aarch64/blob/master/README_zh.md),用的入树莓派爱好者基地的固件；

刷入过程中非常**不推荐** 那个垃圾的*Etcher*，任何时候包括但不限于装Windows或者MacOS镜像以及树莓派，Windows用Win32 Disk Imager 完美点亮，安装docker的过程中请使用**大于2A**的充电器给树莓派供电，不然你会遇到安装下载一旦持续几十秒以上会自动关机重启的问题；
### 2 安装设置docker
[参考文章](https://mlapp.cn/376.html)
先开启混杂模式，鉴别你的网卡id
```{r}
sudo ip link set **eth0** promisc on
ip addr 
```
查看ip设置,如 10.211.55.3/24，根据自己的ip进行更改
```{r}
docker network create -d macvlan --subnet=10.211.55.3/24 --gateway=10.211.55.1 -o parent=eth0 macnet 
```
获取docker的名称 sulinggg/openwrt:x86_64 ，运行docker
```{r}
docker run --restart always --name openwrt -d --network macnet --privileged sulinggg/openwrt:x86_64 /sbin/init 
```
进入容器，修改相应参数，注意识别不同docker镜像命令可能不一样
```{r}
docker exec -it openwrt bash
#sudo docker exec -it openwrt /bin/sh
```
```{r}
cat /etc/config/network
vi /etc/config/network
```
具体打开看参照美丽应用的设置即可
```{r}
/etc/init.d/network restart
```
这一步结束之后，你的旁路由大概离已经可以运行了，但是你会发现打开国内网站速度的时候慢的不行，包括淘宝京东优酷等，具体设置接着往下看；

### 3 旁路由设置
这一步包括3个点
+ 在接口设置中关闭DHCP，在DHCP/DNS中设置添加国内DNS，具体步骤看[这里](https://blog.sillyson.com/archives/7.html),不用像文章一样修改树莓派本机地址；
+ 在lan口设置中关闭桥接，并保存；
+ 最后在防火墙设置中添加语句即可，
> iptables -t nat -I POSTROUTING -o eth0 -j MASQUERADE

一切保存之后重启路由器docker；再次打开国内APP，完美解决内网图片加载慢闲鱼淘宝慢的问题

### 4 其他非必须
安装 portainer，非必须，树莓派爱好者镜像核一些openwrt是自带docker和portainer管理工具的；
docker run -d --name portainer-01 --restart unless-stopped  -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer

节点用瓦工黑五DC6，移动夜间高峰11W到19W没有任何问题；Trojan西雅图年付小鸡三网直连稳定11W，已经比某些x86软路由香了，宽带跑满没啥意义，主要网络连接表现，周六晚高峰Netflix 秒开 The Old Guard 1080p（主要是灭有4k设备），塞隆姐真帅！

```{r}
其他补遗：
#!/bin/bash
# 停止所有的容器
docker stop $(docker ps -a -q)
# 删除所有的容器
docker rm $(docker ps -a -q)
# 删除所有的镜像
docker rmi $(docker images 
#添加docker控制台
https://www.centlinux.com/2019/03/configure-portainer-web-ui-docker-centos-7.html
#添加aria2支持，拥有完美配置的aria2即可开启；
https://p3terx.com/archives/docker-aria2-pro.html
https://hub.docker.com/r/p3terx/aria2-pro
https://igeeker.me/posts/docker-openwrt.html
```
红米AC2100迟早会入的毕竟也是千兆。