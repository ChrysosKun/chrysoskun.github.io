---
title: Arch,Manjaro配置Optimus
categories: [arch]
comments: ture
---

Nvidia卡的GPU方案和电源管理在Linux上没有得到适当的支持，这可能会使你的笔记本电脑很难在全性能下使用。 optimus-manager为这个问题提供了一个变通的方法，允许你在Nvidia GPU上运行你的整个桌面会话，而英特尔/AMD GPU只作为Nvidia GPU和你的屏幕之间的 "中继"。

* TOC
{:toc}
**注：**

- 请将系统备份，避免误操作或未知因素造成的数据丢失、无法进入桌面。
- 只适用于arch和安装时选 non-free drivers的manjaro kde
- 本文翻译自：[Askannz](https://github.com/Askannz)/**[optimus-manager](https://github.com/Askannz/optimus-manager)**并做了部分修改

### 安装optimus-manager及相关组件

```shell
yay pacman -S optimus-manager optimus-manager-qt
```

安装完成后，optimus-manager守护进程应该是自动启动的，可以用systemctl status optimus-manager.service检查它的状态。

```shell
systemctl status optimus-manager.service
```

若没自动启动则手动启动

```shell
systemctl enable --now optimus-manager.service
```

**Arch转到最后一步配置bbswitch然后重启一下就好了，Manjaro还要更改一下配置**

### 编辑文件/etc/sddm.conf

Manjaro提供了SDDM的默认配置，它覆盖了optimus-manager需要的一些配置。

编辑文件/etc/sddm.conf

在以 DisplayCommand 与 DisplayStopCommand 开头的行前面加上 #。

```shell
sudo vim /etc/sddm.conf
```

若文件下没有这两行内容，就不用修改。

### 自定义 Xorg 配置：

Manjaro有自己的驱动工具MHWD，它也会自动生成一个Xorg配置文件，在/etc/X11/xorg.conf.d/90-mhwd.conf，optimus-manager会自动删除这个文件以避免问题。如果未自动删除则手动删除或备份。

optimus-manager 通过自动生成 Xorg 配置文件并将其放入 /etc/X11/xorg.conf.d/。

备份已有的配置文件。

最后文件夹内容如下（已备份）

![image](https://cdn.jsdelivr.net/gh/chrysoskun/chrysoskun.github.io/assets/img/optimus/bak.png)

10-optimus-manager.conf就是optimus-manager自动生成的

### 给optimus-manager-qt 配置自动启动

optimus-manager-qt一个用于Optimus Manager的Qt接口，允许Optimus在笔记本上使用托盘菜单配置和切换GPU。

![image](https://cdn.jsdelivr.net/gh/chrysoskun/chrysoskun.github.io/assets/img/optimus/opqt.png)

系统设置 ->开机和关机 -> 自动启动（若已经自动添加自启动，则不用配置）

### 重启

已完成所有配置，重启系统

检查 Optimus 是否正常运行：

```shell
systemctl status optimus-manager.service
```

若重启黑屏进不去桌面，按Ctrl+Alt+F3进入tty卸载optimus相关软件包和组件

```shell
sudo pacman -Rs optimus-manager optimus-manager-qt
```

然后reboot即可重新进入桌面

```
reboot
```

若还想配置optimus请参考[Askannz](https://github.com/Askannz)/**[optimus-manager](https://github.com/Askannz/optimus-manager)**或其它文章。

### bbswitch

内核模块允许在Optimus笔记本上切换专用显卡，使用bbswitch可以解决风扇过快和切换失灵问题。

安装

```shell
sudo pacman -S bbswitch
```

设置

![image](https://cdn.jsdelivr.net/gh/chrysoskun/chrysoskun.github.io/assets/img/optimus/bbswitch.png)

右键托盘图标，更改后重启。

