---
layout: post
title: "有趣的命令行（之文件处理）"
date: 2017-08-19 23:17:14 +0800
comments: true
categories: utils
---


- 列出系统中打开的大文件

```
lsof / | awk '{ if($7 > 1048576) print $7/1048576 "MB "$9 }' | sort -n -u | tail
```

- 反向删除文件

```
rm !(*.foo|*.bar|*.baz)
```

- 遍历文件

```
find -type f -name "*.txt" | while read f; do echo "i have $f"; done
```

- 打包目录并传至远端服务器

```
tar -c <dir> | gzip | ssh user@remote 'dd of=dir.tar.gz'
tar -c <dir> | ssh -C user@remote 'cd /path && tar -x -p'
```

- 打包离散文件

```
find . -name '*.txt' | tar -c --files-from=- | bzip2 > dir_txt.tar.bz2
```

- 备份整个硬盘至远端服务器

```
dd bs=1M if=/dev/sda | gzip | ssh user@remote 'dd of=sda.gz'
```