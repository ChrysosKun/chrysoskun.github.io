---
title:  grub rescue
categories: [arch]
comments: true
---

##  error: unknown filesystem. Entering rescue mode… grub rescue>

输入set指令，可以看到当前grub的位置，如：

```shell
grub rescue> set
cmdpath=(hd0,gtp5)/EFI/XXXX/
prefix=(hd0,gpt8)/boot/grub
root=(hd0,gpt8)
```

ls 查看列出的分区情况

```shell
grub rescue> ls
(hd0)(hd0,gpt8)(hd0,gpt7)(hd0,gpt6) (hd0,gpt5)(hd0,gpt4) ...
```

设置正确的gpt分区 假如gpt7是正确的

(这里若不知道正确分区，需要一个一个尝试，建议从编号大的开始)

```shell
set prefix=(hd0,gpt7)/boot/grub 
root = (hd0,gpt7)
```

启动 normal 模块

```shell
grub rescue> insmod normal
grub rescue> normal
```

现在就可以进入系统了，但还没有结束

1. 打开终端
2. 更新grub
3. df确认位置

```shell
sudo update-grub
df
sudo grup-install /dev/nvme0n1p5
```

reboot正常

完成

