---
title: If Else Statement in Sed
description: If Else Statement in Sed
date: 2022-11-29 00:00:00+0000
categories:
    - shell
tags:
    - shell
    - sed
---

use `t` or `T` to control the sed workflow.([more](https://getdocs.org/Sed/Branching-and-flow-control))
```bash
sed -i "s/animations = true/animations = false/;t;\
    s/animations = false/animations = true/" "$picom_config"
```

# Reference

1. [How to toggle a comment in a line using sed](https://stackoverflow.com/questions/68320533/how-to-toggle-a-comment-in-a-line-using-sed#answer-72615471)
2. [Sed/Branching-and-flow-control](https://getdocs.org/Sed/Branching-and-flow-control)
