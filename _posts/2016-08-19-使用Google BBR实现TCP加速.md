---
layout: post
title: 使用Google BBR实现TCP加速
category: shadowsocks
tags: [shadowsocks]
---

## 引入

Google BBR是什么就不赘述了，可以理解为和锐速差不多的一个东西。但是，锐速过高的价格并且不在低端售卖，导致了我们并无法实现一个免费好用的单边加速功能。所以，可以选择BBR（新的 TCP 拥塞控制算法 - Bottleneck Bandwidth and RTT）来对 shadowsocks 进行加速。

Ubuntu开启BBR的前提是内核必须等于高于4.9，所以想要使用先看内核是否是4.9或者以上。

## 内核版本

### 查看

```shell
uname -a
```

如果是4.9或者以上，那么恭喜你，升级内核这一步你就可以跳过了，如果在4.9以下，那就需要更新一下内核了；很遗憾GCE官方默认搭载的镜像，内核是4.4的，所以我必须要做一波内核升级了。

### 下载

内核下载地址：[Ubuntu Kernel](http://kernel.ubuntu.com/~kernel-ppa/mainline/)

比如当前我使用的是i386架构的CPU，使用wget获取资源包。(请根据系统环境自行选择下载)

```shell
wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.12.8/linux-image-4.12.8-041208-generic_4.12.8-041208.201708161815_i386.deb
```

### 安装

```shell
dpkg -i linux-image-4.12.8-041208-generic_4.12.8-041208.201708161815_i386.deb
```

### 更新 grub 系统引导文件并重启

```shell
update-grub
reboot
```

## 开启Google BBR

### 查看

再次查看内核版本，确认升级成功

```shell
uname -a
```


### 修改系统配置

```shell
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
```


### 生效配置

```shell
sysctl -p
```

### 确认生效

执行下面的命令

```shell
sysctl net.ipv4.tcp_available_congestion_control
```

返回结果，如果结果中有bbr，则证明内核已开启BBR。

```shell
net.ipv4.tcp_available_congestion_control = bbr cubic reno
```

也可以执行下面的命令，看到有 tcp_bbr 模块即说明BBR已启动。

```shell
lsmod | grep bbr
```