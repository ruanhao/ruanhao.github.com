---
layout: post
title: "有趣的命令行（之系统信息）"
date: 2017-08-19 23:30:35 +0800
comments: true
categories: utils
---


- 挂载信息

```
mount | column -t
```

- 块设备信息

```
lsblk -f
```

- 内核模块目录

```
/lib/modules/$(uname -r)/kernel
```

- 终端信息

```
tput longname
```
- 内核模块依赖关系

```
lsmod | perl -e 'print "digraph \"lsmod\" {";<>;while(<>){@_=split/\s+/; print "\"$_[0]\" -> \"$_\"\n" for split/,/,$_[3]}print "}"' | dot -Tpng | display -
```
- 磁盘信息

```
file -s /dev/sd*
```

- 操作系统位数

```
getconf LONG_BIT
getconf -a ## 显示所有信息
```
