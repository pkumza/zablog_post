title: System Level I/O 系统级输入输出
date: 2015-11-25 14:44:52
categories: ['学习笔记']
description: System Level I/O
tags: [ICS, Unix]
---

I/O (输入/输出) 是主存和外部设备（包括磁盘驱动、终端、网络以及其他设备）之间传递数据的过程。输入操作是从外部设备传递数据到主存，而输出操作是从主存传递数据到外部设备。

所有的程序语言在实现的时候，都提供了更高层级的输入输出函数。譬如ANSI C提供了标准的I/O库，可以使用printf、scanf这样带有缓冲区的I/O函数，C++提供了<<和>>重载操作符来实现类似的功能，Python中有功能完备的print函数，Java中使用System.in/out类等等。在一般情形下，这些高层及的I/O函数可以良好地工作，没有必要直接使用Unix I/O。

然而，Unix I/O可以让人更好地理解胸膛你改概念。例如I/O在进程创建的时候发挥了重要的角色，反过来，进城创建又在不同进程间的文件共享中扮演了重要的角色。此外，有些时候Unix I/O可以完成高层I/O所无法完成的任务，例如标准I/O库没有提供读取文件元数据的方式，比如文件大小或者文件创建时间。所以，一定程度上了解Unix I/O是非常有必要的。

# Unix I/O Unix系统IO

## Unix 文件

- Unix文件是字节的序列。譬如，当一个文件大小为m字节的时候，它就是从B<sub>0</sub>到B<sub>m-1</sub>的序列。

- 所有的I/O设备，都被表示为文件。

 - /dev/sda2
 - /dev/tty2

- 甚至内核也表示为文件

 - /dev/kmem
 - /proc
 
## Unix 文件类型

Unix文件有以下几种基本类型。

1. 一般文件
1. 目录文件
1. 字符设备文件和块设备文件
1. 符号链接文件
1. 管道文件（FIFO）
1. Socket

## Unix I/O 

设计原理：把所有的设备映射成文件，可以让系统内核使用简单的Unix I/O来管理所有设备。所有的输入和输出的设计，都是一致的。

基本 Unix I/O 操作采用系统调用的形式完成，包括以下几类：

1. 打开、关闭文件 open() close()
2. 读取、写入文件 read() write()
3. 改变当前文件指针的位置 lseek()

### 打开文件

使用open函数来打开文件

```c
int fd; /*file descriptor*/

if ((fd = open("/etc/hosts", O_RDONLY)) < 0>) {
    perror("open");
    exit(1);
}
```
返回值是一个叫做文件描述符的int类型，当返回的描述符大于等于0的时候，就是对应的文件描述符，如果等于-1则说明文件打开失败。
有几个特殊的文件描述符是系统所固定的：

- 0:标准输入
- 1:标准输出
- 2:标准错误

O_RDONLY 是 oflag，还有其他的标识符，包括O\_WRONLY和O\_RDWR，分别是只写和读写方式。

### 关闭文件

关闭文件会告知系统内核，本进程已经不再使用这个文件了。
样例代码：

```c
int fd;     /* file descriptor */
int retval; /* return value */

if ((retval = close(fd)) < 0) {
    perror("close");
    exit(1);
}
```

内核会释放文件打开时，所维护的数据结构，然后把文件描述符恢复到可用的描述符池中。如果没有调用close，进程就因为某种原因终止，那么内核也会关闭它打开的所有文件，并且释放它们的存储器资源。虚拟目录/proc/self/fd 记录了当前进程所打开的文件描述符。