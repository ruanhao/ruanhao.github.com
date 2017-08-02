---
layout: post
title: "Setup Python Env for Emacs"
date: 2016-08-24 17:40:28 +0800
description: python, emacs
comments: true
categories: PYTHON
---

## 安装 elpy

`M-x package-install elpy`

## 配置文件

``` lisp
(elpy-enable)
(add-hook 'elpy-mode-hook
          (lambda ()
            (define-key elpy-mode-map (kbd "M-.") 'elpy-goto-definition)
            (define-key elpy-mode-map (kbd "C-x M-.") 'elpy-goto-definition-other-window)
            (define-key elpy-mode-map (kbd "M-,") 'pop-tag-mark)
            (define-key elpy-mode-map (kbd "M-RET") 'elpy-company-backend)
            ))
```

## Happy Hacking

{% img /images/examples/emacs_py_env.png %}
