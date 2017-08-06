---
layout: post
title: "使用 Emacs 编写 Python 脚本"
date: 2016-08-24 17:40:28 +0800
description: python, emacs
comments: true
categories: emacs
---

## 前言

使用终端编辑器编写脚本类语言是一件很爽的事情，编辑速度快，Ctrl-Z 就能立马调试运行看到效果。
不过像 Python 这样的高级脚本语言，有着各式各样的库，要记住所有的模块名，函数名基本不可能，
因此需要添加代码提示功能，提高编辑效率。

{%highlight 说明 %}：并不是建议大家一定要使用 Emacs 或者 Vim ，使用 PyCharm 开发 Python 效率也很高，
如果是开发大型工程，或者是团队合作，请根据实际情况选择合适的开发工具。
不过，如果只是一些随写随用的脚本，何不尝试下 Emacs 呢？

## 插件配置

试了下几种常见 Emacs Python 插件，感觉 [Elpy](https://github.com/jorgenschaefer/elpy) 不论是配置还是使用，
都比较简单和轻量化。

在 {%highlight ~/.emacs.d/init.el %} 文件中添加一下配置代码：（需事先安装 use-package）

``` lisp
(use-package elpy
  :defer t
  :init (with-eval-after-load 'python (elpy-enable)))

(add-hook 'elpy-mode-hook
          (lambda ()
            (define-key elpy-mode-map (kbd "M-.")     'elpy-goto-definition)
            (define-key elpy-mode-map (kbd "C-x M-.") 'elpy-goto-definition-other-window)
            (define-key elpy-mode-map (kbd "M-,")     'pop-tag-mark)
            (define-key elpy-mode-map (kbd "M-RET")   'elpy-company-backend)))
```

这样就可以了，使用 {%highlight Meta-. %} 和 {%highlight Meta-, %} 查看函数定义和回退，
使用 {%highlight Ctrl-x Meta-. %} 在新窗口查看函数定义，
使用 {%highlight Meta-Enter %} 手动显示代码提示。（默认在句号后，自动显示代码提示）

## 效果

{% img /images/examples/emacs_py_env.gif %}
