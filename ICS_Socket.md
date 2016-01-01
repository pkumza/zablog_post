title: Socket的一些Guide
date: 2015-12-8 23:44:52
categories: ['学习笔记']
toc: true
description: 套接字接口（Socket Interface）是一组用来创建网络应用的函数。它的最初实现是和Unix I/O相结合的，而如今绝大部分现代操作系统都实现了套接字接口，包括所有的Unix/Linux、Windows、OS X等。Socket是众多网络应用服务的基础，学习Socket编程对于理解互联网基础有非常重要的作用。
tags: [ICS, C, Socket]
---

# Internet, Connection & Port.

从编程的角度看，如何把数据从一个地方传送到另一个地方呢？
在现实生活中，想要邮寄一个物品，那么重要的无非是完整正确地写清楚对方的地址。一般的邮寄单、快递单上也都注明了接收地址和发出地址。那么在计算机网络中，地址是什么概念呢？这里就出现了IP地址的概念。
全球IP因特网是最著名和最成功的互联网实现，这种实现基本上直接就是当代互联网的代名词，如果没出意外的话，我们平时所说的互联网，就是指这一套协议所组织的全球因特网。几乎每个现代计算机系统都支持[IP/TCP]()协议。

- 在IP地址（[IPv4](https://en.wikipedia.org/wiki/IPv4)）中，主机Hosts被映射为32bit的一个地址。在书写的时候，经常记为十进制，把32个bit分为四组，然后成为用点分隔的四个0~255的数字。
- 为了防止记不住数字，IP地址一般还被映射到称为因特网域名（Domain Name）的标识符。
- 因特网主机上的进程可以通过连接，来和其他任意一个主机进行通信。（Sometimes it is not true.）

互联网连接仅仅靠IP是不够的，因为要组成双向可靠传输通道，而且一个机器在某一个时刻可能需要和多个主机通信。这就产生了端口（port）的概念。
Socket就是这样的终端，可以认为Socket Addresss就是一个IPaddress:port对。
端口port是一个16-bit的正整数，所以它的理论范围是0~65535。当然这只是理论范围，因为有好多端口是专用的，已经限制了它的具体目的。
譬如80端口用于Web Server页面，即HTTP；25号端口用于SMTP；22端口用于SSH；21端口用于FTP等等。

好了，上面都是一些只是普通的知识，这些知识感觉太多了写不完，而且别的地方都写得很具体，我就不再这里浪费时间了。关于Socket最基础的Client-Server Model，查查别的地方应该也就有答案了吧。


# Socket Interface, getaddrinfo/ getnameinfo

这是一个奇怪的稍微困难的部分，也是回到主题的部分！！
Socket Interface究竟是什么？前面说了，是接口，是系统提供的编程接口，让编程者可以用这个来进行网络访问。创建时间呢是上世纪80年代，在Berkeley的那个Unix上最早实现的。

最早怎么设计比较好呢？我们的应用程序一般都是有服务器来提供支持的，比如浏览网页，比如登陆QQ。那么就是服务器一直在那里等着，然后客户端发送连接请求，然后相互发送信息接受信息就好了罢。大概其实也就是这个样子，然后具体实现后面会说。

现在Unix、Windows、Linux、OS X上都有。结构如下图：

![socket client-server 结构](http://imglf0.nosdn.127.net/img/MGpGUW9CdGlzcDVPWHF3ZE91M2RycG5CZVRCaWFHTHNVTjVzNFJGUVdlTzVvc0kySmRiWUhnPT0.png)

## 套接字地址结构

套接字的地址结构是什么样子呢？其实很简单，之前说了要记录IP地址和Port，那么就把这两个给记录下来就成了呗。

![socket结构](http://imglf.nosdn.127.net/img/MGpGUW9CdGlzcDVPWHF3ZE91M2Rya1BUTHRFMFBrTFJjN0FIWCt2VU9YOUgzQzJ5b2tCYU5nPT0.png)

问，AF_INET是什么？AF是什么？

这个AF是Address Family，那个INET就是指Internet，而且Protocal family永远都是AF\_INET。既然永远都是，那你写个啥，这个主要是协议初期想要为新世代保留无限可能，方便扩展用的。事实上IPv6就不是这个了，但是本文还是只讨论旧的所以，永远都是这个。现在需要知道的是“AF\_INET表示32位的IPv4互联网”

而且因为以前的C没有(void)*，所以为了通用性，除了sockaddr\_in之外，还弄了一个无聊的sockaddr，这个东西有个别名叫做SA，在使用的时候还需要强制转换来转换去，比较麻烦。不过这个也是不得已而为之。其实看一下SA的结构，前面的AF\_INET是一样的，只不过后面它都留空了而已。

回去看第一个图，这就是整个过程了。
首先Server调用getaddrinfo，得到相关的信息，然后建立一个socket（这个并非完全建立，还需要做一些事情），bind函数把自己这个socket和一个端口进行绑定，绑定之后就可以监听了，使用listen。listen之后还不行，要用while套着一个accept来等待客户端的链接。如果没有人链接的话accept就阻塞在这里了。
现在轮到客户端出场了，客户端也是getaddrinfo，然后就要简单一些，直接建立socket就connect，来！发送的东西被accept接收到，两者就可以通过rio的读写进行通讯了。
RIO是什么？RIO是Robust Unix I/O的缩写，是可靠的IO，不那么容易丢，也不怕被中断等问题打扰，是适合于Internet的IO。
为什么网络还是用write、read什么的？！别忘了，在Unix，一切外设都是文件，连网络都不例外。访问网络，在程序眼里，就相当于去读写文件，文件描述符就是网络套接字所返回的描述符。

读写完毕之后，客户端不玩儿了，close了，在自己写写操作最后放了一个EOF，服务端收到之后也就close了，等待下一个accept。

具体地，首先是socket函数，它的原型是：

```c
int socket(int domain, int type, int protocol)

// Example
int clientfd = Socket(AF_INET, SOCK_STREAM, 0);
```

AF\_INET前面已经解释过了，SOCK\_STREAM表示这个套接字是因特网连接的一个端点。

## getaddrinfo

这个函数用来获取地址信息非常方便，输入是hostname或者address、service或者port，返回值是是否成功，同时会把结果放到struct addrinfo ** result中。
函数原型：

```c
int getaddrinfo(const char *host,            /* Hostname or address */
                const char *service,         /* Port or service name */
                const struct addrinfo *hints,/* Input parameters */
                struct addrinfo **result);   /* Output linked list */

void freeaddrinfo(struct addrinfo *result);  /* Free linked list */

const char *gai_strerror(int errcode);       /* Return error msg */

```

这个结果是这样一个结构：

![Result 链表结构](http://imglf.nosdn.127.net/img/MGpGUW9CdGlzcDVPWHF3ZE91M2RybHpHeHVLSzN3ZzlnREFwelZQejJoM1dhNkxtL3JPdjl3PT0.png)

因为这个是不定长的，有可能只有一个信息，也有可能有很多。所以需要用链表组织的；因为是用链表组织的，所以当结束的时候，就容易出现内存泄露，所以就必须要有相应的free函数，这就是freeaddrinfo，用处是delete这个链表，在getaddrinfo的结果用过之后调用。

为了形成图3的结构，addrinfo的结构中必然有指向自己类型的指针：

```c
struct addrinfo {
    int              ai_flags;     /* Hints argument flags */
    int              ai_family;    /* First arg to socket function */
    int              ai_socktype;  /* Second arg to socket function */
    int              ai_protocol;  /* Third arg to socket function  */
    char            *ai_canonname; /* Canonical host name */
    size_t           ai_addrlen;   /* Size of ai_addr struct */
    struct sockaddr *ai_addr;      /* Ptr to socket address structure */
    struct addrinfo *ai_next;      /* Ptr to next item in linked list */
};

```

ai\_next就是指向下一个内容的指针。

当然，还有一个对应的函数，叫做getnameinfo。
刚才的函数叫做getaddrinfo，输入是hostaddr/hostname、port/service，返回是socket的addr的一些信息。
现在这个getnameinfo函数，输入的是socket addr，输出是host和service。

```c
// 函数getaddrinfo原型
int getnameinfo(const SA *sa, socklen_t salen, /* In: socket addr */
                char *host, size_t hostlen,    /* Out: host */
                char *serv, size_t servlen,    /* Out: service */
                int flags);                    /* optional flags */

```

这函数的转化实例：

```c
#include "csapp.h"

int main(int argc, char **argv)
{
    struct addrinfo *p, *listp, hints;
    char buf[MAXLINE];
    int rc, flags;

    /* Get a list of addrinfo records */
    memset(&hints, 0, sizeof(struct addrinfo));
    hints.ai_family = AF_INET;       /* IPv4 only */
    hints.ai_socktype = SOCK_STREAM; /* Connections only */
    if ((rc = getaddrinfo(argv[1], NULL, &hints, &listp)) != 0) {
        fprintf(stderr, "getaddrinfo error: %s\n", gai_strerror(rc));
        exit(1);
    }
    /* Walk the list and display each IP address */
    flags = NI_NUMERICHOST; /* Display address instead of name */
    for (p = listp; p; p = p->ai_next) {
        Getnameinfo(p->ai_addr, p->ai_addrlen, 
                    buf, MAXLINE, NULL, 0, flags);
        printf("%s\n", buf);
    }

    /* Clean up */
    Freeaddrinfo(listp);

    exit(0);
}

```

解释：
这个函数的内容是输入域名，返回IP列表。
譬如

```bash
$ ./hostinfo zablog.me
23.235.37.133
23.235.33.133
$ ./hostinfo www.pku.edu.cn
162.105.131.196
$ ./hostinfo twitter.com
1.2.3.4        # Funny in Mainland China!
```

首先定义用于getaddrinfo函数相关的数据和结构。
p listp hints 用于指向addrinfo数据结构
buf用来缓存即将命令行输出的IP信息。
rc是错误信息、flag是输出的类型。
默认情况下，flag代表“展示IP地址”。

获得输入域名的相关信息，形成结构传入hints、listp。如果成功rc = 0，继续，如果出错，rc是错误码。
p作为指针依次遍历链表中的每一个结构，输出相应的地址。

程序结束前，清理这些数据结构。
必须使用Freeaddrinfo因为链表的数据结构不易随着程序的结束而自动析构，会产生内存泄漏。

# Client Side

Open_clientfd 是socket和connect函数的包装。客户端用这个函数建立和服务器的连接
所以在图一的过程中，就会简化很多。
现在要实现一个最简单的例子，需要Server和Client联合合作。

```c
// echoclient.c
#include "csapp.h"

int main(int argc, char **argv)
{
    int clientfd;
    char *host, *port, buf[MAXLINE];
    rio_t rio;

    host = argv[1];
    port = argv[2];

    clientfd = Open_clientfd(host, port);   // 用open_clientfd通过host和port获得clientfd

    Rio_readinitb(&rio, clientfd); // Rio_readinitb是把clientfd包装到rio中。


    while (Fgets(buf, MAXLINE, stdin) != NULL) {  // 从标准输入STDIN（命令行中）读取内容，存到buf中，直到遇到新行。

	Rio_writen(clientfd, buf, strlen(buf));  // 用Unix I/O写到clientfd所指向的地方。

	Rio_readlineb(&rio, buf, MAXLINE);  // Rio_readlineb从clientfd中读取line

	Fputs(buf, stdout);  // 把获得的信息输出到命令行

    }
    Close(clientfd); 
    exit(0);
}

```

rio是一个RIO的struct，本质仍是clientfd，唯一区别：rio是robust的。
rio\_readlineb读取到的是从Server端接收到的内容。


# Server Side

```c
#include "csapp.h”
void echo(int connfd);

int main(int argc, char **argv)
{
    int listenfd, connfd;
    socklen_t clientlen;
    struct sockaddr_storage clientaddr; /* Enough room for any addr */                                                                                                               
    char client_hostname[MAXLINE], client_port[MAXLINE];

    listenfd = Open_listenfd(argv[1]);      // 首先使用open_listenfd获得监听描述符

    while (1) {
	clientlen = sizeof(struct sockaddr_storage);    
	connfd = Accept(listenfd, (SA *)&clientaddr, &clientlen);       //使用accept获得连接描述符

	Getnameinfo((SA *) &clientaddr, clientlen, 
                    client_hostname, MAXLINE, client_port, MAXLINE, 0);  // 获取client的hostname、port等信息，输出。

	printf("Connected to (%s, %s)\n", client_hostname, client_port);
	echo(connfd);
	Close(connfd);
    }
    exit(0);
}
```

问题1：既然有了open_listenfd了，那么accept是否多余？
答案：并不。
Accept函数等待来自客户端的连接请求到达侦听描述符，listenfd，然后在addr中填写了客户端的套接字地址，并且返回一个“已连接描述符”，这个描述符可以用来利用Unix I/O和客户端通信。

问题2：为什么有了监听描述符，还需要已连接描述符？！
答案：监听描述符是作为客户端连接请求的一个端点，贯穿整个服务器的服务周期。已连接描述符是客户端和服务器之间已经建立起来的连接的一个端点。服务器每次接受连接请求时都会创见一次，它只存在于与服务器为一个客户端服务的过程中。更多理由另见49页图及50页解释

答案Extend：
Listening Descriptor 一直存在。存在时间等于服务器在线时间。
Connected Descriptor 有限时间存在。存在时间等于客户端服务时间。

这样，服务端才有机会去支持多个客户端。否则，总不能有个人访问了网页，别人都看不到了罢。

## open_listenfd的具体实现

```c
/*  
 * open_listenfd - open and return a listening socket on port
 *     Returns -1 and sets errno on Unix error.
 */
/* $begin open_listenfd */
int open_listenfd(int port) 
{
    int listenfd, optval=1;
    struct sockaddr_in serveraddr;
  
    /* Create a socket descriptor */
    if ((listenfd = socket(AF_INET, SOCK_STREAM, 0)) < 0)
	return -1;
 
    /* Eliminates "Address already in use" error from bind */
    if (setsockopt(listenfd, SOL_SOCKET, SO_REUSEADDR, 
		   (const void *)&optval , sizeof(int)) < 0)
	return -1;

    /* Listenfd will be an endpoint for all requests to port
       on any IP address for this host */
    bzero((char *) &serveraddr, sizeof(serveraddr));
    serveraddr.sin_family = AF_INET; 
    serveraddr.sin_addr.s_addr = htonl(INADDR_ANY); 
    serveraddr.sin_port = htons((unsigned short)port); 
    if (bind(listenfd, (SA *)&serveraddr, sizeof(serveraddr)) < 0)
	return -1;

    /* Make it a listening socket ready to accept connection requests */
    if (listen(listenfd, LISTENQ) < 0)
	return -1;
    return listenfd;
}
```
open\_listenfd就像前面所说的，把socket、bind、listen包装在了一起。然后具体的实现，比如SOL\_SOCKET是什么，SO\_REUSEADDR是什么，我稍微讲一下：
SOL_SOCKET表示在socket的层次。除此之外还可以在TCP、IP、IPv6等层次上操作。
而SO\_REUSEADDR呢？这个有一篇文章讲得很详细。[setsockopt中参数之SO_REUSEADDR的意义](http://www.cnblogs.com/mydomain/archive/2011/08/23/2150567.html)，领教了。
概括说就是，在普通情况下啊，当一个端口被释放后，要经过一段时间才能继续使用。
使用了SO_REUSEADDR之后，可以让端口释放后立即可被再次使用，因此这个参数在进行服务器调试的时候非常有用。

好，这一节就讲这么多了。
