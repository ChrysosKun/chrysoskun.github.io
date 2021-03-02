---
title: linux终端代理
categories: [linux]
comments: true
---

(最后更新于2020-11-02)

* TOC
{:toc}
### 方法一：编写shell文件

```shell
# 开启代理
function proxyon(){
    export ALL_PROXY=socks5://localhost:1088
#     export HTTP_PROXY=http://localhost:8888
#     export HTTPS_PROXY=https://localhost:8888
    echo -e "PROXY IS ENABLED"
}
# 关闭代理
function proxyoff(){
    unset ALL_PROXY
#     unset HTTP_PROXY
#     unset HTTPS_PROXY
    echo -e "PROXY IS DISABLED"
}

```

端口、协议根据实际环境改变

打开.zshrc文件在结尾添加 source 路径

```
# source后跟shell文件路径

source /home/user/Documents/teminal_proxy.sh
```

重启终端或source ~/.zshrc 使配置文件生效

效果

![image](https://cdn.jsdelivr.net/gh/langyaya/langyaya.github.io/assets/img/proxy/image.png)

------

### 方法二：使用*proxychains-ng*

安装

```shell
sudo pacman -S proxychains-ng
```

或

```shell
git clone https://github.com/rofl0r/proxychains-ng.git
cd proxychains-ng
./configure
make && sudo make install && sudo make install-config
```

配置proxychains.conf 

```shell
vim /etc/proxychains.conf 
```

注释掉原有的 socks4 127.0.0.1 9050

添加 socks5 127.0.0.1 1080 (根据自己实际代理端口设置)

![image](https://cdn.jsdelivr.net/gh/langyaya/langyaya.github.io/assets/img/proxy/image01.png)

测试

```
proxychains4 curl cip.cc
```

![image](https://cdn.jsdelivr.net/gh/langyaya/langyaya.github.io/assets/img/proxy/image02.png)

完成。

------

(2020-11-02更新)

### 方法三：使用cgproxy(推荐)

cgproxy将透明代理在特定cgroup中运行的任何内容

> 能够按照你设置的[cgroup](https://zh.wikipedia.org/wiki/Cgroups)，代理几乎所有应用的流量，包括不理会“全局代理”设置的那些终端命令行工具（如curl，wget，git等）。支持IPv4/6，支持tcp和udp，支持作为网关共享代理给其他设备。--by麻鸭忙戟(@flyawaytheazure)

详情见：[springzfx](https://github.com/springzfx)/**[cgproxy](https://github.com/springzfx/cgproxy)**

