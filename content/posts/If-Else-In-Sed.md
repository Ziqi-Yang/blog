+++
title = "If Else In Sed"
author = ["Zarkli Leonardo"]
description = "If-else statement in linux command sed"
date = 2022-12-21T00:00:00+08:00
tags = ["shell", "sed"]
categories = ["shell"]
draft = false
+++

use `t` or `T` to control the sed workflow.[(more](https://getdocs.org/Sed/Branching-and-flow-control))

```bash
sed -i "s/animations = true/animations = false/;t;\
    s/animations = false/animations = true/" "$picom_config"
```

## Reference {#reference}

1.  [How to toggle a comment in a line using sed](https://stackoverflow.com/questions/68320533/how-to-toggle-a-comment-in-a-line-using-sed#answer-72615471)
2.  [Sed/Branching-and-flow-control](https://getdocs.org/Sed/Branching-and-flow-control)
