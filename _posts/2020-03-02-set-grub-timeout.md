---
title: 设置grub的timeout
categories: linux
comments: true
---

1. 打开*/etc/default/grub*,将*`GRUB_TIMEOUT`*的值改为0，*GRUB_TIMEOUT=0*
2. 打开终端执行*sudo update-grub*
3. 打开*/boot/grub/grub.cfg*,注释掉以下代码

```
if [ "${timeout}" = 0 ]; then
    set timeout=10
fi
```

重启，完成。

