---
layout: post
title: "有趣的命令行（之网络）"
date: 2017-08-19 23:33:32 +0800
comments: true
categories: utils
---



- 扫描网段中的主机

```
nmap -sP 192.168.1.0/24  ## list alive hosts in specific subnet
```

- 设置网卡速率

```
ethtool --change eth0 autoneg off speed 100 duplex full
```

- 设置无线网卡速率

```
iwconfig eth1 rate 1Mb/s fixed
```