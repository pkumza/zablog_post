title: IA64中的VHPT
date: 2016-4-21 15:44:52
categories: ['学习笔记']
description: 
tags: [ICS]
---

安腾的体系结构定义了一套虚拟散列页表 virtual hash page table（VHPT），同时定义了VHPT walker这样一个硬件机制。 VHPT是对TLB的扩展，来加强虚拟地址转换为物理地址的效率，可以显著减小当TLB失效的时候造成的延迟。

<!--more-->
本文的原文地址为[http://zablog.me/2016/04/21/VHPT-IA64/](http://zablog.me/2016/04/21/VHPT-IA64/)。

VHPT在虚拟地址空间中可以被设置为操作系统的一级页表，也可以设置为独一无二的大型转换缓存，如图4-9。当TLB失效产生的时候，会使用硬件的哈希函数进行查找。

PTA的意思是Page Table Address (Register)。

VHPT可以被操作系统当做一级系统页表使用，也可以用作倒排页表。其中的冲突查找链的实现和操作系统页表的实现都是由软件定义的，是可选的。

![Figure 4-9](http://imglf0.nosdn.127.net/img/MGpGUW9CdGlzcDZ0d3RiZWFqbDVnUDJWbFFnTk83aGg4NjBlL3JNUmJKTzZjU1RXMFovSy93PT0.png)

安腾体系结构提供了两种VHPT表项的结构，一种是Short格式，一种是Long格式。对于Short格式来说，VHPT就是一个线性的页表，它的PTE的表项大小是8个字节，即2个字的长度，64Bit。如图4-10。

![Figure 4-10](http://imglf0.nosdn.127.net/img/MGpGUW9CdGlzcDZ0d3RiZWFqbDVnQXZ3MDN0WnhaTHFoMXU2eDJ4TjBjSld3eEtFMFhzYlBRPT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ubG9mdGVyLmNvbQ==&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

当采用Long格式的时候，它的表项就增加为32个字节。这32个字节不仅包含了Short格式的所有内容，还增加了一些位。它是Short格式的超集。

![Figure 4-12](http://imglf1.nosdn.127.net/img/MGpGUW9CdGlzcDZ0d3RiZWFqbDVnQ3NNdkppaE9rR0EvSVpGL3N3c3k4TEo3WS9manJjVTd3PT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ubG9mdGVyLmNvbQ==&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

增加了主要内容为 ：

|bit|offset|Usage|
|------|----|:-------|
|tag| +16 |Tag是VHPT的hash index，是用来确认转换的独一无二性。|
|ti| +16 |Tag Invalid位，用来确定tag的有效性。|
|ig| +24 |操作系统可自定义，比如可以用来指示当hash冲突产生的时候，冲突链的下一个链接的地址。正好是8个字节，正好用来放置64bit的地址。|

附录1

Short Format & Long Format

The Itanium architecture defines a data structure that allows for the insertion of TLB entries by a hardware mechanism. The data structure is called the “virtual hash page table” (VHPT) and the hardware mechanism is called the VHPT walker. 
Unlike the IA-32 page tables, the Itanium VHPT itself is virtually mapped, i.e. VHPT walker references can take TLB faults themselves. Virtual mapping of the page tables is needed because the page tables for 264 address space are quite large and typically do not fit into physical memory. 
The Itanium architecture prescribes the format of a leaf-node page table entry (PTE) seen by the VHPT walker, but does not impose an OS page table data structure itself. As summarized in Table 5-1, the architecture support two different VHPT formats: 
• Short format uses 8-byte PTEs, and is a linear page table. The short format VHPT does not contain protection key information (there are not enough PTE bits for that). Short format is a per-region linear page table, i.e. the PTEs and hash function are independent of the RID. The short format prefers use of a self-mapped page table. The short format VHPT is an efficient representation for address spaces that contain only a few large clusters of pages, like the text, data, and stack segments of applications running on a MAS operating system. 
• Long format uses 32-byte PTEs, and is a hashed page table. The hash function embedded in hardware. The long format supports protection keys and the use of multiple page sizes in a region. The long format hash and tag functions incorporate the RID, and allows multiple address space translations to be present in the same VHPT. The long format is expected to be used either as a cache of the real OS page tables, or as a primary page table with collision chains. The long format VHPT is a much better representation for address spaces that are sparsely populated, since the short format VHPT has a linear layout and would consume a large amount of memory. Single address space operating systems may prefer the long format VHPT for this reason. 

|Attribute|Short Format|Long Format|
|------|----|:-------|
|Entry Size| 8 Byte |32 Byte|
|Lookup|Linear | Hashed|
|Protection Keys| No |Yes|
|Page Size| per region |per entry|

附录2：
What is RID?
The Itanium architecture provides a byte-addressable 64-bit virtual address space. The address space is divided into 8 equally-sized sections called regions. Each region is 261 bytes in size and is tagged with a unique region identifier (RID). As a result, the processor TLBs can hold translations from many different address spaces concurrently, and need not be flushed on address space switches. The regions provide the basic virtual memory architecture to support multiple address space (MAS) operating systems. 
Additionally, each translation in the TLB contains a protection key that is matched against a set of software maintained protection key registers. The protection keys are orthogonal to the region model and allow efficient object sharing between different address spaces. The protection key registers provide the basic virtual memory architecture to support single address space (SAS) operating systems. 
安腾的体系结构提供了一个64位的虚拟空间。这个虚拟空间被平分为8个区域（region），每一个区域有261字节的空间，被一个独一无二的区间标识符(RID)来标识……
 参考资料：
 
1 Intel 白皮书 itanium-architecture - http://www.intel.com/content/www/us/en/processors/itanium/itanium-architecture-vol-1-2-3-4-reference-set-manual.html 
2 Itanium Page Table and TLB - http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.14.3453&rep=rep1&type=pdf 


