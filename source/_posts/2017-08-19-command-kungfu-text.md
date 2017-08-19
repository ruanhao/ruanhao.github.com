---
layout: post
title: "有趣的命令行（之文本处理）"
date: 2017-08-19 18:21:13 +0800
comments: true
categories: utils
---


- 打印水平分隔符

```
printf '%100s\n' | tr ' ' =
```

- 打印文件某一行

```
sed -n '1000p;1000q' <filename>
```

- 打印文件某几行

```
sed -n '10,20p;20q' <filename>
```

- 生成随机数

```
head -c4 /dev/urandom | od -An -tu4
```

- 重复生成单词

```
echo hello{,,,}
```

- 大小写转换

```
echo ${var^^}
echo ${var,,}
tr '[:lower:]' '[:upper:]'
```

- 过滤不可打印字符

```
tr -dc '[:print:]' </dev/urandom
```

- 查看字符的二进制表示

```
echo ★  | hexdump -C

showkey -a
```

- 删除空行

```
grep .
awk NF
```

- 删除前导空格

```
awk '{$1=$1}1' FILE
echo $line | xargs
```

- 跳过文件首尾行

``` 
head file.txt               # 开始 10 行
tail file.txt               # 最后 10 行
head -n 20 file.txt         # 开始 20 行
tail -n 20 file.txt         # 最后 20 行
head -20 file.txt           # 开始 20 行
tail -20 file.txt           # 最后 20 行
head -n -5 file.txt         # 排除最后 5 行
tail -n +5 file.txt         # 从第 5 行开始到最后
```

- 为 man 建立 pdf

```
man -t <command> | ps2pdf - > filename.pdf
```