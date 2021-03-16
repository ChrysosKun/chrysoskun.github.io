---
title: 记录一次Arch安装
categories: [arch]
comments: true
---

(最后更新于2020-10-27)

* TOC
{:toc}
------



![image](https://cdn.jsdelivr.net/gh/chrysoskun/chrysoskun.github.io/assets/img/arch/image01.png)

## 验证

```shell
ls /sys/firmware/efi/efivars
```

## 联网

- 手机usb共享网络

- [iwctl](https://wiki.archlinux.org/index.php/Iwd_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

## 更新系统时间

```shell
timedatectl set-ntp true
```

## 分区

```shell
fdisk
或
cfdisk 
```

## 格式化

引导分区

```shell
mkfs.fat -F32 /dev/xxxxx
```

根分区

```
mkfs.ext4 /dev/xxxxx
```

## 挂载

挂载根分区

```
mount /dev/XXXX /mnt
```

建立boot文件夹

```
mkdir /mnt/boot
```

挂载EFI分区

```shell
mount /dev/XXXX /mnt/boot
```

------

## 选择镜像

```
reflector -c China -a 5 --sort rate --save /etc/pacman.d/mirrorlist
```

## 安装必须的软件包

```
pacstrap /mnt base linux linux-firmware
```

------

## Fstab

生成 [fstab](https://wiki.archlinux.org/index.php/Fstab) 文件

```
genfstab -U /mnt >> /mnt/etc/fstab
```

检查一下生成的 /mnt/etc/fstab 文件是否正确

```
cat /mnt/etc/fstab
```

## Chroot

[Change root](https://wiki.archlinux.org/index.php/Change_root_(简体中文)) 到新安装的系统

```
arch-chroot /mnt
```

## 时区

```
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

运行 [hwclock(8)](https://jlk.fjfi.cvut.cz/arch/manpages/man/hwclock.8) 以生成 /etc/adjtime

```
hwclock --systohc
```

## 本地化

```
vim /etc/locale.gen
```

 找到zh_CN.UTF-8 UTF-8 删掉前面的#

 找到en_US.UTF-8 UTF-8 删掉前面的#

 接着执行 locale-gen 以生成 locale 信息

```
locale-gen
```

创建并写入/etc/locale.conf文件

```
vim /etc/locale.conf
```

填入以下内容

```
LANG=en_US.UTF-8
```

## 网络配置

```
vim /etc/hostname
```

设定的一个myhostname

添加对应的信息到 [hosts(5)](https://jlk.fjfi.cvut.cz/arch/manpages/man/hosts.5):

```
vim /etc/hosts
```

填入以下内容

```
127.0.0.1	localhost
::1		localhost
127.0.1.1	myhostname.localdomain	myhostname
```

Root 密码

```
passwd
```

## 安装基本的包

```
pacman -S grub efibootmgr networkmanager network-manager-applet dialog wireless_tools wpa_supplicant os-prober mtools dosfstools ntfs-3g base-devel linux-headers reflector git intel-ucode vim
```

## grub-install

```
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=Arch
```

## 双系统

创建文件夹

```
mkdir /mnt/windows10
```

挂载Windows系统的EFI

```
mount /dev/XXXX /mnt/windows10
```

生成grub.cfg

```
grub-mkconfig -o /boot/grub/grub.cfg
```

## 退出新系统并取消挂载

```
exit
```

```
umount -a
```

```
reboot
```

------

## 创建交换文件

```
dd if=/dev/zero of=/swapfile bs=1M count=512
```

更改权限

```
chmod 600 /swapfile
```

设置交换文件

```
mkswap /swapfile
```

启用

```
swapon /swapfile
```

修改/etc/fstab文件

```
vim /etc/fstab
```

到文件末尾输入

```
/swapfile none swap defaults 0 0
```

## 新建用户

```shell
useradd -m -G wheel username
```

```shell
passwd username
```

授权

```shell
EDITOR=vim visudo
```

找到 # %wheel ALL=(ALL)ALL  取消注释

## 安装显卡驱动

安装AMD集显驱动

```shell
pacman -S xf86-video-amdgpu
```

安装intel集显驱动

```shell
sudo pacman -S xf86-video-intel
```

安装NVIDIA独显驱动

```shell
pacman -S nvidia nvidia-utils
```

## 安装Xorg

```shell
sudo pacman -S xorg
```

## 安装桌面管理器

KDE

```shell
pacman -S sddm
```

## 安装桌面环境

KDE

```shell
pacman -S plasma kde-applications packagekit-qt5
```

安装中文的字体

```shell
pacman -S noto-fonts-cjk
```

## 配置网络

```shell
sudo systemctl enable NetworkManager
```

```shell
sudo pacman -S network-manager-applet
```

## 重启

```shell
reboot
```

## 参考资料

[[1]](https://wiki.archlinux.org/index.php/Main_page)

[[2]](https://t.me/archlinuxcn_group)

