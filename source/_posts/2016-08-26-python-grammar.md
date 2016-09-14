---
layout: post
title: "Python 语法拾遗"
date: 2016-08-26 13:27:31 +0800
comments: true
description: python
categories: python
---

## 变量命名惯例

* 单一下划线变量名( _x )不会被 from module import * 语句导入
* 前后有双下划线变量( __x__ )是系统定义变量名，对 python 解释器有特殊意义
* 两个下划线开头但结尾没有双下划线的变量( __x )是类的本地变量
* 交互模式下，变量名( _ )用于保存最后表达式的结果

