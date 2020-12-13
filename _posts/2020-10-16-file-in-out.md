---
title: 文件基本I/O操作
categories: [linux]
comments: true
---

* TOC
{:toc}
## 打开/创建文件 open/creat

系统调用 open/creat

|  项目  | 描述                                                         |
| :----: | ------------------------------------------------------------ |
|  原型  | int open(const char * pathname, int flags);<br/>int open(const char * pathname, int flags, mode_t mode);<br/>int creat(const char * pathname, mode_t mode); |
|  功能  | 按照flags方式打开pathname指定的文件<br/>或者创建权限为mode的新文件pathname |
|  参数  | pathname:要打开或创建的文件名（包含路径）<br/>flags:打开方式<br/>mode:当创建一个新文件时（flags中包含O_CREAT）,mode指定<br/>新文件权限 |
| 返回值 | int型结果：当文件打开成功时，返回一个文件描述符<br/>当文件打开失败时，返回-1,并且errno为错误码 |

头文件

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
```

文件描述符

- 文件描述符（File descriptor）是计算机科学中的一个术语，是一个用于表述指向文件的引用的抽象化概念。
- 文件描述符在形式上是一个非负整数。实际上，它是一个索引值，指向内核为每一个进程所维护的该进程打开文件的记录表。当程序打开一个现有文件或者创建一个新文件时，内核向进程返回一个文件描述符。



## 读文件

通过open()打开或创建文件时获得的文件描述符，对打开的文件进行读操作。读文件使用系统调用函数read()

系统调用read()

|  项目  | 描述                                                         |
| :----: | ------------------------------------------------------------ |
|  原型  | ssize_t read(int fd, void * buf, size_t count);              |
|  功能  | 从文件描述符为fd的文件中读取count个字节的数据放入buf缓冲<br/>注：从当前文件读写指针处开始读数据，读完后，当前文件读写指针也将<br/>改变成功读出的字节数 |
|  参数  | fd:要读文件的文件描述符<br/>buf:指向接收数据缓冲区的指针，读取的数据将存放在该缓冲区中<br/>count:要读取的字节数 |
| 返回值 | 整型结果：执行成功时返回实际读回数据的字节数；当失败时，返回-1<br/>并errno为错误码 |

头文件  

```c
#include <unistd.h>
```

返回值为0表示从文件末尾读

下面的代码就示范了如何基于文件描述符来读取当前目录下的一个指定文件，并把文件内容打印至Console中

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int main (void){
    int fd;
    int numbytes;
    char path[] = "file";
    char buf[256];

    /*
     * O_CREAT:如果文件不存在则创建
     * O_RDONLY:以只读模式打开文件
     */
    fd = open(path, O_CREAT | O_RDONLY, 0644);
    if(fd == -1){
        perror("open()");
        exit(1);
    }
	printf("fd = %d\n", fd);
    memset(buf, 0x00, 256);
    /*
     *memset
     *void *memset(void *s, int ch, size_t n);
     *函数解释：将s中当前位置后面的n个字节（typedef unsigned int size_t ）用 ch 替换并返回 s
     *作用是在一段内存块中填充某个给定的值，它是对较大的结构体或数组进行清零操作的一种最快方法
     *memset()函数原型是extern void *memset(void *buffer, int c, int count)
     *buffer：为指针或是数组
     *c：是赋给buffer的值
     *count：是buffer的长度
     */
    while((numbytes = read(fd, buf, 255)) > 0){
        printf("%d bytes read: %s", numbytes, buf);
        memset(buf, 0x00, 256);
    }
    close (fd);
    exit(1);
}
```



## 写文件

通过open()打开或创建文件时获得的文件描述符，对打开的文件进行写操作。读文件使用系统调用函数write()

系统调用write()

|  项目  | 描述                                                         |
| :----: | ------------------------------------------------------------ |
|  原型  | ssize_t write(int fd, const void * buf, size_t count);       |
|  功能  | 将buf缓冲区中count个字节数据写入到文件描述符为fd的文件中<br/>注：从当前文件读写指针处开始读数据，写完后，当前文件读写<br/>指针也将改变成功写入的字节数 |
|  参数  | fd:要写入文件的文件描述符<br/>buf:指向写数据缓冲区的指针，要写入的数据应先存放在该缓冲区中<br/>count:要读取的字节数 |
| 返回值 | 整型结果：执行成功时返回实际写入数据的字节数；当失败时<br/>返回-1并errno为错误码 |

**头文件**  

```c
#include <unistd.h>
```



## 文件定位

每一个打开的文件都有一个读写指针指向下一次读写操作位置。改变读写指针的位置可以用系统调用lseek()

系统调用lseek()

|  项目  | 描述                                                         |
| :----: | ------------------------------------------------------------ |
|  原型  | off_t lseek(int fd, off_t offset, int whence);               |
|  功能  | 将文件描述符为fd的文件读写指针按照whence方式<br/>移动offset字节偏移量 |
|  参数  | fd:要改变读写指针位置的文件的文件描述符<br/>offset:要改变的读写指针的偏移量(字节数)，可以是正值、负值或0<br/>whence:移动读写指针的方式，SEEK_SET、SEEK_CUR、SEEK_END |
| 返回值 | 整型结果：执行成功时返回的是定位后新的读写指针位置；当失败时<br/>返回-1并errno为错误码 |

头文件

```c
#include <unistd.h> 
#include <sys/types.h>
```

whence的取值

| whence参数 |  读写指针移动方式  |    读写指针移动结果     |
| :--------: | :----------------: | :---------------------: |
|  SEEK_SET  | 从文件开始位置移动 |     即为offset的值      |
|  SEEK_CUR  | 从文件当前位置移动 | 当前读写指针位置+offset |
|  SEEK_END  | 从文件末尾位置移动 |     文件长度+offset     |

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>

int main() {
    int fd1;
    int num;
    char buf[20];
    fd1 = open("f1", O_RDWR | O_CREAT);
    if (fd1 == -1) {
        perror("open");
        exit(1);
    }
    printf("fd1 is %d \n", fd1);
    num = write(fd1, "hello world!", 12);
    printf("fd1:write num=%d bytes into f1\n", num);
    lseek(fd1, 0, SEEK_SET);
    num = read(fd1, buf, 20);
    buf[num] = 0;
    printf("fd1:read  %d bytes from f1: %s\n", num, buf);
    close(fd1);
}

```



## 文件复制命令的实现

“文件复制命令的实现”项目将通过打开源文件、创建目标文件、从源文件中读出数据写入目标文件、关闭源目标文件这样的过程实现文件的复制。

```c
#include<stdio.h>
#include<stdlib.h>
#include<sys/stat.h>
#include<fcntl.h>
#include<unistd.h>

#define BUFSIZE 256

void copy(char *from, char *to) {
    int fromfd = -1, tofd = -1;
    int num;
    char buf[BUFSIZE];
    if ((fromfd = open(from, O_RDONLY)) == -1) {
        perror("open");
        exit(1);
    }
    if ((tofd = open(to, O_WRONLY | O_CREAT | O_TRUNC, S_IRUSR | S_IWUSR)) == -1) {
        perror("open to");
        exit(1);
    }
    num = read(fromfd, buf, sizeof(buf));
    while (num > 0) {
        if (write(tofd, buf, num) != num)
            printf("write %s error\n", to);
        num = read(fromfd, buf, sizeof(buf));
    }
    if (num == -1)
        printf("write %s error\n", to);
    close(fromfd);
    close(tofd);
}

int main(int argc, char **argv) {
    if (argc != 3) {
        printf("Usage:%s fromfilename tofilename\n", argv[0]);
        exit(1);
    }
    copy(argv[1], argv[2]);
}

```
