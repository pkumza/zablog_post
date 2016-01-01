title: Linking 链接
date: 2015-10-23 14:44:52
categories: ['学习笔记']
description: 链接（Linking）是把各种代码和数据部分收集起来并组合成为一个单一文件的过程。链接又可以分为静态链接和动态链接。静态链接一般发生在编译的最后一步，会把解析好的符号进行重定位。而动态链接的使用就更加灵活了，在程序执行的过程中，就可以动态链接一些代码。常见的概念包括.so、.dll等，都是动态链接库。
tags: [ICS, C]
---

# Linking链接初步

链接（Linking）是把各种代码和数据部分收集起来并组合成为一个单一文件的过程。

一个简单的C语言的例子：

main.c

```c
#include<stdio.h>
int sum, a, b;

int main()
{
    a = 2;
    b = 7;
    op_add();
    printf("%d\n", sum);
    return 0;
}
```

add.c

```c
extern int sum, a, b;

void op_add()
{
    sum = a + b;
}
```

编译：

```bash
$ gcc -O2 -o m add.c main.c 
$ ./m
9
```

这个方法就是使用了__静态链接__。
![过程示意图](http://i5.tietuku.com/1e68db464b96d3ec.png)

首先，把源代码分别使用Translators(cpp)进行预处理。

```bash
cpp [other arguments] main.c main.i
```

接下来，驱动程序运行C编译器(cc1)，把main.i翻译为ASCII汇编语言文件main.s

```bash
cc1 main.i main.c -O2 [other arguments] -o main.s
```

然后，运行汇编器(as)，把main.s翻译成一个可重定位目标文件(relocatable object file) main.o

```bash
as [other arguments] -o main.o main.s
```

最后，运行连接器程序(ld)，把main.o和add.o以及一些必要的系统文件结合起来，创建一个可执行目标文件(executable object file)m。

```bash
ld -o m [system object files and args] main.o add.o
```

要需要运行可执行文件m，只需要在shell中输入它的名字就可以运行了。

```bash
$ ./m
```

shell会调用一个叫做__加载器__的函数，它拷贝可执行文件m中的代码和数据到存储器，然后转移控制到这个程序的开头。

# 为什么要用链接


## 模块化

模块化是大型程序开发的必要部分。如果一个程序没有办法分解为不同的模块的话，那么它将会很难做大，即使大了也难以调试。
当然，C语言的模块化还是比较原始的模块化，是文件级别上的支持，具体的模块组合需要程序员自己实现，不像某些语言有丰富的模块管理工具。

## 效率考虑

当程序很小的时候，效率就不会成为一个重要的问题。但是当一个程序大到包含千千万万个模块，那么每次编译都需要把所有的代码都编译一遍吗？这未免太夸张了。对于一个操作系统级别的程序，整体编译一遍会特别耗时间。使用了链接之后，就不必重新编译所有的文件。

# 链接做了什么

1. 符号解析，维护了一个符号表(Symbol Table)。上述例子中，main之所以可以找到op_add函数，就得益于这个符号表。对于ELF文件来说，这个符号表存在.symtab中。
2. 重定位。需要重定位的变量也好、函数也好，具体的地址会是暂时空着的，只有从符号表里找到这个内容之后，才会填写进去。

| ELF header        | 
| ------------- |
| Segment header table     | 
| .text      | 
| .rodata | 
| .data | 
| .bss | 
| .symtab | 
| .rel.txt | 
| .rel.data | 
| .debug | 
| Section header table | 
