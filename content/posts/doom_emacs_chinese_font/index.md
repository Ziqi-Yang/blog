---
title: Doom Emacs Set Chinese Font
description: It is extraordinarily simple, but I missed it a lot
date: 2022-11-14 00:00:00+0000
categories:
    - emacs
tags:
    - emacs
    - doom emacs
---

```elisp
(setq 
  doom-font (font-spec :family "FiraCode Nerd Font" :size 34) ;; :wight light
    ;; don't set size for doom-unicode-font, or the size won't be changed
    doom-unicode-font (font-spec :family "LXGW WenKai")) ;; CJK font
```
