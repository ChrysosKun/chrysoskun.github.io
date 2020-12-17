---
title: Netfilter简介
categories: [linux,network]
comments: true
---

* TOC
{:toc}
## 什么是Netfilter

Netfilter是Linux第三代防火墙，在此之前还有Ipfwadm和[Ipchains](https://zh.wikipedia.org/wiki/Ipchains)。

## Netfilter与Linux的关系

这里的Linux不是指GNU/Linux。

[Netfilter](http://www.netfilter.org/)与[Linux](https://www.kernel.org/)是两个独立的组织。[Linux](https://www.kernel.org/)每隔一段时间就会到[Netfilter](http://www.netfilter.org/)下载最新的Netfilter程序代码。并将其加入到自己所开发的Linux系统中。

## wikipedia的定义

**Netfilter**，在[Linux内核](https://zh.wikipedia.org/wiki/Linux內核)中的一个[软件框架](https://zh.wikipedia.org/wiki/軟體框架)，用于管理网络数据包。不仅具有[网络地址转换](https://zh.wikipedia.org/wiki/网络地址转换)（NAT）的功能，也具备数据包内容修改、以及数据包过滤等[防火墙](https://zh.wikipedia.org/wiki/防火墙)功能。利用运作于[用户空间](https://zh.wikipedia.org/wiki/使用者空間)的应用软件，如[iptables](https://zh.wikipedia.org/wiki/Iptables)、[ebtables](https://zh.wikipedia.org/w/index.php?title=Ebtables&action=edit&redlink=1)和[arptables](https://zh.wikipedia.org/wiki/Arptables)等，来控制Netfilter，系统管理者可以管理通过Linux操作系统的各种网络数据包。1990年代，Netfilter在Linux 2.3.15版时进入Linux内核，正式应用于Linux 2.4版。

## Netfilter工作的位置

Netfilter是工作在Linux中的一个小功能，Netfilter是以模块的类型存在于Linux之中。每当Linux多一个Netfilter的模块就代表LInux防火墙的功能多了一项。

![image](https://cdn.jsdelivr.net/gh/chrysoskun/chrysoskun.github.io/assets/img/netfilter-iptables/image01.png)

**Netfilter模块存在的目录**

- /lib/modules/5.9.2-arch1-1/kernel/net/ipv4/netfilter/

- /lib/modules/5.9.2-arch1-1/kernel/net/ipv6/netfilter/

- /lib/modules/5.9.2-arch1-1/kernel/net/netfilter/

  (5.9.2-arch1-1为Kernel-version)

![image](https://cdn.jsdelivr.net/gh/chrysoskun/chrysoskun.github.io/assets/img/netfilter-iptables/netfilter-path.png)

`/lib/modules/5.9.2-arch1-1/kernel/net/ipv4/netfilter/`目录下存放的模块只能工作在IPv4的网络环境下；而`/lib/modules/5.9.2-arch1-1/kernel/net/ipv6/netfilter/`目录下存放的模块只能工作在IPv6的网络环境下。

`/lib/modules/5.9.2-arch1-1/kernel/net/netfilter/`目录下存放的模块可以同时应用于IPv4及IPv6的网络环境之中。

## Netfilter的命令结构

Netfilter的模块只是提供与之对应的功能，而使用Netfilter需要给Netfilter执行的规则，有了规则Netfilter才知道哪些包可以被接受，哪些包需要过滤。给予Netfilter规则的方式是将规则填入到一块有结构性的内存中。

下图为Netfilter存放规则的区块，从图中可以看到内存被分为5个Table（还有一个security表未列出），也是目前Netfilter提供的五大功能。

![image](https://cdn.jsdelivr.net/gh/chrysoskun/chrysoskun.github.io/assets/img/netfilter-iptables/image02.png)

Table

- Filter：Filter是Netfilter内最为重要的，实现包的过滤，也就是防火墙功能。
- Nat：网络地址转换，当无法直接访问网络时，通常用于将数据包路由到网络。
- Mangle：修改包的内容。
- Raw：用于处理异常，提供一种机制来标记数据包，以便选择退出连接跟踪。加快包穿越防火墙机制的速度，借此提高防火墙性能。
- Security：用于强制访问控制网络规则。

Chain

- INPUT：输入链。发往本机的数据包通过此链。
- FORWARD：转发链。本机转发的数据包通过此链。
- OUTPUT：输出链。从本机发出的数据包通过此链。
- PREROUTING：路由前链，在处理路由规则前通过此链，通常用于目的地址转换（DNAT）。
- POSTROUTING：路由后链，完成路由规则后通过此链，通常用于源地址转换（SNAT）。

[网络数据包通过Netfilter时的工作流向](https://zh.wikipedia.org/wiki/File:Netfilter-packet-flow.svg)

![image](https://cdn.jsdelivr.net/gh/chrysoskun/chrysoskun.github.io/assets/img/netfilter-iptables/image03.png)