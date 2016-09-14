---
layout: post
title: "检测最耗 CPU 的 Java 线程"
date: 2016-09-09 10:02:23 +0800
comments: true
categories: java
---

## 脚本

top 命令还可以监控 Java 线程，特此记录一段脚本:

``` bash
#!/bin/bash

if [ $# -eq 0 ]; then
    echo "please enter java pid"
    exit -1
fi

pid=$1
jstack_cmd=""

if [[ $JAVA_HOME != "" ]]; then
    jstack_cmd="$JAVA_HOME/bin/jstack"
else
    r=`which jstack 2>/dev/null`
    if [[ $r != "" ]]; then
	jstack_cmd=$r
    else
	echo "can not find jstack"
	exit -2
    fi
fi

line=`top -H -b -n 1 -p $pid | sed '1,/^$/d' | sed '1d;/^$/d' | grep -v $pid | sort -nrk9 | head -1`
echo "$line" | awk '{print "tid: "$1," cpu: %"$9}'

tid_0x=`printf "%0x" $(echo "$line" | awk '{print $1}')`
$jstack_cmd $pid | grep $tid_0x -A20 | sed -n '1,/^$/p'
```
[Reference](http://hongjiang.info/find-busiest-thread-of-java)