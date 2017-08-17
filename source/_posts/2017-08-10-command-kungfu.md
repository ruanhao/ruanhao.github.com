---
layout: post
title: "有趣的命令行"
date: 2017-08-10 16:52:15 +0800
comments: true
categories: utils
---

工作中经常会遇到简单，有趣且使用的命令行，现将这些命令行记录与此。

## 文本处理

- 打印水平行\\
`printf '%100s\n' | tr ' ' =`
- 打印文件某一行\\
`sed -n '1000p;1000q' <filename>`
- 打印文件某几行\\
`sed -n '10,20p;20q' <filename>`
- 生成随机数\\
`head -c4 /dev/urandom | od -An -tu4`
- 重复生成单词\\
`echo hello{,,,}`
- 大小写转换\\
`echo ${var^^}`\\
`echo ${var,,}`\\
`tr '[:lower:]' '[:upper:]'`
- 过滤不可打印字符\\
`tr -dc '[:print:]' </dev/urandom`
- 查看字符的二进制表示\\
`echo ★ | hexdump -C`\\
`showkey -a`
- 删除空行\\
`grep .`\\
`awk NF`
- 删除前导空格\\
`awk '{$1=$1}1' FILE`\\
`echo $line | xargs`
- 过滤文件收尾行
``` sh
head file.txt               # first 10 lines
tail file.txt               # last 10 lines
head -n 20 file.txt         # first 20 lines
tail -n 20 file.txt         # last 20 lines
head -20 file.txt           # first 20 lines
tail -20 file.txt           # last 20 lines
head -n -5 file.txt         # all lines except the 5 last
tail -n +5 file.txt         # all lines except the 4 first, starts at line 5
```



## 文件处理
- 列出系统中打开的大文件\\
`lsof / | awk '{ if($7 > 1048576) print $7/1048576 "MB "$9 }' | sort -n -u | tail`
- 反向删除文件\\
`rm !(*.foo|*.bar|*.baz)`
- 遍历文件\\
`find -type f -name "*.txt" | while read f; do echo "i have $f"; done`
- 打包目录并传至远端服务器\\
`tar -c <dir> | gzip | ssh user@remote 'dd of=dir.tar.gz'`\\
`tar -c <dir> | ssh -C user@remote 'cd /path && tar -x -p'`
- 打包离散文件\\
`find . -name '*.txt' | tar -c --files-from=- | bzip2 > dir_txt.tar.bz2`
- 备份整个硬盘至远端服务器\\
`dd bs=1M if=/dev/sda | gzip | ssh user@remote 'dd of=sda.gz'`


## Debug
- 截获进程的输入输出\\
`strace -ff -e trace=write -e write=1,2 -p <pid>`
- 查看共享库依赖\\
`ldd <executable_file`
- 查看待写入磁盘数据大小\\
`grep ^Dirty /proc/meminfo`
- 查看内存中数据\\
`strings /dev/mem`



## 操作系统信息
- 查看挂载信息\\
`mount | column -t`
- 查看块设备\\
`lsblk -f`
- 内核模块目录\\
`/lib/modules/$(uname -r)/kernel`
- 查看终端信息\\
`tput longname`
- 查看内核模块依赖关系\\
`lsmod | perl -e 'print "digraph \"lsmod\" {";<>;while(<>){@_=split/\s+/; print "\"$_[0]\" -> \"$_\"\n" for split/,/,$_[3]}print "}"' | dot -Tpng | display -`
- 查看磁盘信息\\
`file -s /dev/sd*`
- 查看操作系统位数\\
`getconf LONG_BIT`\\
`getconf -a ## 显示所有信息`


## 网络
- 扫描网段中的主机\\
`nmap -sP 192.168.1.0/24  ## list alive hosts in specific subnet`
- 设置网卡速率\\
`ethtool --change eth0 autoneg off speed 100 duplex full`
- 设置无线网卡速率\\
`iwconfig eth1 rate 1Mb/s fixed`


## 小技巧
- 显示相关的命令\\
`apropos <command>`
- 为 man 建立 pdf\\
`man -t <command> | ps2pdf - > filename.pdf`
- 倒计时\\
`time cat`
