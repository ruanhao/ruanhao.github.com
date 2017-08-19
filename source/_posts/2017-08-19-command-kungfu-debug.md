---
layout: post
title: "有趣的命令行（之调试）"
date: 2017-08-19 23:28:13 +0800
comments: true
categories: utils
---


- 截获进程的输入输出

```
strace -ff -e trace=write -e write=1,2 -p <pid>
```

- 查看共享库依赖

```
ldd <executable_file
```

- 查看待写入磁盘数据大小

```
grep ^Dirty /proc/meminfo
```

- 查看内存中数据

```
strings /dev/mem
```
