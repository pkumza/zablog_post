title: Thread-Level Paralellism 线程级并行
date: 2015-12-27 20:06:52
categories: ['学习笔记']
description: 随着单核计算机的计算难以有成倍的提升，并行计算在计算机科学中越来越重要。本篇文章将从硬件和软件两个角度来分析线程级并行的内容。硬件方面包括内存一致性、SNOOPY、乱序执行等，软件方面会分析数字加总和快速排序两个例子。本文同时会有阿姆达尔定律（Amdahl's Law）的相关内容。
tags: [ICS, C]
---
# Thread-Level Paralellism (线程级并行)

# 目录（Page1~2）
- 并行计算(硬件)(Page3~12)
 - 多核心
   - 在一个芯片上有多个分离的处理器
   - 多个处理器如何保持主存一致性

 - 超线程
   - 有效地让多个线程在同一个核心上执行

- 线程级并行(软件)(Page13~结束)

 - 把程序切分为独立的任务块
   - 譬如并行求和

 - 分治并行
   - 譬如并行快排

# 并行计算(硬件)

## 利用并行计算(Page3)
- 迄今为止，在我们已经讲过的内容中，我们使用多线程来处理I/O延迟。
 - 譬如：在Web应用中，给每一个client都开一个线程，那么就可以避免一个client被另一个client阻塞。

- 多核心的CPU提供了额外的机遇
 - 把工作分发到多个线程上工作，在N个核心上并行执行。
 - 如果有很多独立的工作的话，那么这些可以自动并行。
   - 例如在电脑上运行多个应用，或者服务器同时服务多个client。这些独立的（互相之间不会影响的）工作，是可以自动并行的。
 - 当然，我们也可以专门写出并行的代码，让一个单独的大型的任务运行地更快。
   - 这需要把这个任务分解成可并行的子任务。
- Shark machine 可以同时运行16个线程
 - 8核心，每个核心有两个超线程
 - 理论上限是16倍的加速。
   - 当然这是“理论上限”，实际运行中是难以达到的。

## 多核处理器的硬件形态(Page4~12)
（主要是《高等计算机体系结构》讲的东西，给学生一个大概行的了解即可）
### 多核心处理器（Page4）
图示为多核心处理器的形态，一共有n个处理器核心，编号分别是0~n-1。
每个核心都有自己独立的L1缓存和L2缓存，其中L1缓存又分为数据缓存和指令缓存。
所有的核心共享L3缓存，再由L3缓存连接主存。

三级缓存的出现，是为了弥补存储访问速度跟不上处理器运行速度的不足。
利用计算机科学的“局部性原理”，给处理器架设多级缓存，可以提升处理器的执行效率。

多级缓存是有很大的好处，但是也带来了重大的问题：“一致性”。
所谓的一致性，就是说对于不同的CPU核心，需要它们看到的是同样的主存内容。
缓存的本质还是对主存内容的一个映射，对于同一个地址，在CPU0的cache中的值如果和CPU1的cache中的值不一样，那么程序就会出现问题。
### 主存的一致性（Page5~7）
这里有一段代码的例子。thread1写入a并输出b，thread2写入b并输出a。
最终输出的内容，取决于代码的执行顺序，但是对于线程内部来说，绝不能乱序。即代码Wa一定要在Rb之前执行，同时Wb一定要在Ra之前执行。
Page6列举了所有的执行的可能性。
如果输出100，1或者1，100那么就是错误的结果，是不应该出现的。
但是如Page7所示。
首先main函数设置a为1，b为100，thread1和thread2都把a、b放到了自己的cache中。
对于thread1来说，它把a写成2，然后thread2把b写成了200.
这个时候，它们只是保存了自己cache的内容，在没有确保一致性的机制的情况下，他们认为cache中的a、b的值都是正确的，于是使用print输出，就会输出1、100。
这就是采用Cache机制有可能出现的错误的结果。这个结果是不满足“主存一致性”的。它的解决方法就是SNOOPY Caches
### Snoopy Caches（Page8~9）
把每个cache block标注一个状态。
有三种状态可以标注
1. Invalid		不可用的值
2. Shared		只读的拷贝
3. Exclusive	可写的拷贝

这一页的执行顺序，动画可以表述地很清楚。
首先假设cache中是空的，thread1写入了a，那么就把a设为exclusive，（如果其他的cache中a的状态是shared，那么就强制把它们的状态设置为invalid，这个是动画所没有展示出来的）说明cache1中的a是独占的，是只有这里的a的值是正确的；同样地，thread2写入了b，那么b在cache2中也是独占的，只有这里是正确的。
thread1要想读取b，自己的cache中是没有（或者Invalid），那么就会通知其他的cache查找，找到之后，存入b的值的信息，然后status都变成shared。同理thread2也得到了shared的a的值。因此最终可以获得符合一致性的结果。

### 超线程
#### 乱序执行处理器架构（Page10）
指令译码器从指令cache中读取内容后，动态地生成一个执行序列，然后再映射到不同的功能单元上。
乱序执行的目的是为了最大化处理单元的利用率。不同的功能单元的处理速度不一样，浮点慢，整数很快。浮点计算需要的时间有时是整数计算的几十倍。

```c
double a = 1.3;
int sum = 0, i;
a += 1.32426 * 4.2343;
for (i = 0; i < 100; i++) {
    sum += i;
}
```
在这段代码的例子中，sum+=1的指令不必去等待浮点乘法计算完成，这可以让功能单元最大程度地利用，同时节约大量的时间。
#### 超线程的实现（Page11）
一个核心内部，有两个PC，两个执行序列和两个寄存器堆，采用同样一套执行单元，通过指令译码器进行分配。这样可以更好地利用执行单元，提高执行效率。

## 一些机器（Page12）
Shark Machines 和 GHC Cluster Machines
这里只是说使用了这两种机器作为实验机器。
对于Shark Machine，它是八核心十六线程，相当于有十六个可以并行的核心，这也是为什么Parallel Sums #1~#4这些图的横坐标轴Threads的数量最大是16。

------------------------------

# 线程级并行（软件）
## 求和的例子（Page13~27）
这个例子的目标是从0加到N-1。

```bash
Sum = 0 + 1 + 2 + 3 + 4 + ... + (N-1)
```

最终Sum应该等于(N-1)*N/2
这里为了说明“线程级并行”，所以才使用这些数字一个一个加总。
加总并行：为了让整个加总过程可以并行，把N个数字划分为K个区间，每个区间N/K个数字（向下取整），最后再把余数加起来。
### Page13~27总览：

1. 求和的方法 #1：划分为K块，分给K个线程，每个线程都去不断更新一个全局变量Sum。
 1. 1-A （Race）不使用同步机制——(结果错误)
 2. 1-B （Semaphore）使用信号量——(特别慢)
 3. 1-C （Mutex）使用互斥锁——(特别慢)
2. 求和的方法 #2：划分为K块，分给K个线程，每个线程只去计算自己的sum，最后汇总。
 1. 2-A （Adjacent memory acc）加总到一个连续的数组元素——(效果还可以)
 2. 2-B （Spaced memory acc）加总到有一定间隔的数组元素——(效果更好)
 3. 2-C （Register acc）加总到自己的寄存器——（效果最好）

### Page14（1-A、1-B、1-C）
```c
typedef unsigned long data_t;    // 设置data_t类型为无符号的long
/* Single accumulator | 唯一的加总器*/
volatile data_t global_sum;      // global_sum就是那个唯一的全局变量。所有的数字都加总到这一个数上。

/* Mutex & semaphore for global sum | 互斥锁和信号量*/
sem_t semaphore;
pthread_mutex_t mutex;

/* Number of elements summed by each thread | 线程数目*/
size_t nelems_per_thread;

/* Keep track of thread IDs | 线程列表*/
pthread_t tid[MAXTHREADS];

/* Identify each thread */
int myid[MAXTHREADS];

```

### Page15（1-A、1-B、1-C）
```c

    nelems_per_thread = nelems / nthreads;

    /* Set global value | 把这个全局变量总和初始化为0*/
    global_sum = 0;

    /* Create threads and wait for them to finish | 把任务分给多个 线程*/
    for (i = 0; i < nthreads; i++) {
	    myid[i] = i;
	    Pthread_create(&tid[i], NULL, thread_fun, &myid[i]);
    }
    for (i = 0; i < nthreads; i++)
	Pthread_join(tid[i], NULL);
    result = global_sum;

    /* Add leftover elements | 处理余数*/
    for (e = nthreads * nelems_per_thread; e < nelems; e++)
        result += e;

```
### Page16~17（1-A）

1-A不加同步保护的代码：

```c
void *sum_race(void *vargp)
{
    int myid = *((int *)vargp);
    size_t start = myid * nelems_per_thread;
    size_t end = start + nelems_per_thread;
    size_t i;

    for (i = start; i < end; i++) {
	    global_sum += i;
    }
    return NULL;
}
```
多核心性能如17页图示，最优加速2.86倍，但是当thread数量大于1的时候，就会出错！
### Page18~19
加入同步的内容，信号量or互斥锁。代码很简单，只是在1-A的基础上，在global_sum上增加了互斥而已。
运行性能如19所示。非常差的性能！对于信号量法1-B来说，本来单线程2.5秒结束的工作，信号量法会需要10分钟，耗费时间变成了原来的几百倍。
互斥锁比信号量好三倍，运行速度只慢了一百多倍。
但是显然这些方法都是失败的！

### Page20
这里就是上述方法2的2-A、 2-B、 2-C。

```c
/* Partial sum computed by each thread | 每个线程求出的部分的总和*/
data_t psum[MAXTHREADS*MAXSPACING];

/* Spacing between accumulators | 间隔大小*/
size_t spacing = 1;

```

### Page21

```c
	nelems_per_thread = nelems / nthreads;    // 每个线程需要处理的长度

    /* Create threads and wait for them to finish */
    for (i = 0; i < nthreads; i++) {
	    myid[i] = i;                           // 该线程的id
	    psum[i*spacing] = 0;                   // 这个线程的partial sum放置的地方
	    Pthread_create(&tid[i], NULL, thread_fun, &myid[i]);
    }
    for (i = 0; i < nthreads; i++)
	Pthread_join(tid[i], NULL);                // 等待子线程计算完毕

    result = 0;

    /* Add up the partial sums computed by each thread */
    /* 加总子线程的计算结果 */
    for (i = 0; i < nthreads; i++)
	result += psum[i*spacing];

    /* Add leftover elements | 加总余数*/
    for (e = nthreads * nelems_per_thread; e < nelems; e++)
        result += e;

```

### Page22（子线程的代码）
```c
void *sum_global(void *vargp)
{
    int myid = *((int *)vargp);
    size_t start = myid * nelems_per_thread;   // 计算起始点和结束点
    size_t end = start + nelems_per_thread;
    size_t i;

    size_t index = myid*spacing;
    psum[index] = 0;
    for (i = start; i < end; i++) {
	    psum[index] += i;                      // 加总到partial sum数组特定的位置
    }
    return NULL;
}

```
### Page23
性能比较。2-A相邻数组存储的方法比一个一个相加，快了5倍，但是2-B间隔数组方法快乐13.3倍。
从程序语言的方法上没有任何区别，操作次数也一样，为什么这两者有这么大的差距？答案在Page24
### Page24
因为错误的分享False Sharing。
Cache的一致性不是以字节作为单位，而是以cache block作为单位。
当更新psum[i]的时候，那么整个block就是独占的。当访问psum[0]的时候psum[1]、psum[2]、...、psum[7]就不能同时访问了。

### Page24
当间隔拉大，false sharing的影响就越来越小。
在本例子中，机器的缓存block大小是64，而变量的长度是8，因此一个block中有8个。
当spacing是1的时候，thread1会影响thread2~thread8；
当spacing是2的时候，thread1会影响thread2~thread4；
当spacing是4的时候，thread1会影响thread2；
当spacing >= 8 就再也没有区别了。
这也是为什么图中S8和S16基本上是重合的。

### Page26
```c
void *sum_local(void *vargp)
{
    int myid = *((int *)vargp);
    size_t start = myid * nelems_per_thread;
    size_t end = start + nelems_per_thread;
    size_t i;
    size_t index = myid*spacing;
    data_t sum = 0;
    for (i = start; i < end; i++) {
	    sum += i;
    }
    psum[index] = sum;
    return NULL;
}
```
这就是2-C的代码。这个sum是一个局部变量，因此在加总过程中线程之间不会有“任何”影响！
因为sum非常常用，所以sum很可能常驻寄存器，所以最终可以达到7.5倍的加速比，比2-B还要快两倍。

## 快速排序（Page28~45）

###Page 28~31
目标：排序N个数字
方法：使用快排的并行版本
算法：
- 选择轴值pivot
- 把X个数字分为两个L、R部分，L代表小于等于p的数字的集合，R表示大于等于p的数字的集合。
- 递归地排序L，得到排序后的L'。
- 递归地排序R，得到排序后的R'。
- 返回L' : p : R'

算法的演示如图29~30所示。

```c
void qsort_serial(data_t *base, size_t nele) {
  if (nele <= 1)			// 如果需要排序的长度小于等于1，那么直接返回即可。
    return;
  if (nele == 2) {			// 如果需要排序的长度是2，那么把小的放在前面。
    if (base[0] > base[1])
      swap(base, base+1);
    return;
  }

  /* Partition returns index of pivot */
  size_t m = partition(base, nele);  // 获得轴值pivot的index
  if (m > 1)                         // 如果pivot左边的元素个数大于一，也就是L的大小大于1，递归地排序L
    qsort_serial(base, m);
  if (nele-1 > m+1)                  // 如果pivot右侧的元素个数大于一，也就是R的大小大于1，递归地排序R
    qsort_serial(base+m+1, nele-m-1);
}
```
以上代码是最基础的快排代码，采用了“分治法”的思想。
### Page32 并行快排
进行并行快排的算法：
- 如果数组长度小于等于阈值，那么就采用普通的顺序快速排序。
- 否则，首先找到轴值pivot，得到L和R，然后启用两个线程，并行地排序L和R。

上述算法如图Page33所示。
### Page34
任务：排序数据的一个子集
表示方式：使用base和nele来表示。base代表数组起始位置，nele代表元素数目。
特点：作为分离的线程运行。

### Page35
对于很短的小任务，采用顺序快排即可。对于很小的片段内容，如果使用多线程，反而造成资源浪费。

### Page36
把一个任务切分为两个任务的示意图。
原本一个thread来处理，得到轴值应在的位置以后，递归地排序L和R，会分离为两个新的threads，分别处理。

### Page37 顶级函数（简化版）
```c
void tqsort(data_t *base, size_t nele) {
    init_task(nele);
    global_base = base;                      // 待排序数组位置
    global_end = global_base + nele - 1;     // 待排序的数组长度
    task_queue_ptr tq = new_task_queue();    // 建立线程池
    tqsort_helper(base, nele, tq);           // 给出第一条任务指令：数组位置和长度
    join_tasks(tq);                          // 等待所有任务完成
    free_task_queue(tq);                     // 析构，释放空间
}

```
### Page38
```c
/* Multi-threaded quicksort | 多线程快排*/
static void tqsort_helper(data_t *base, size_t nele,
                          task_queue_ptr tq) {
    if (nele <= nele_max_sort_serial) {      // 如果长度低于阈值，就使用顺序快排，否则使用多线程。
        /* Use sequential sort */
        qsort_serial(base, nele);
    }
    sort_task_t *t = new_task(base, nele, tq);
    spawn_task(tq, sort_thread, (void *) t);
}
```
### Page39
```c
/* Thread routine for many-threaded quicksort */
static void *sort_thread(void *vargp) {
    sort_task_t *t = (sort_task_t *) vargp; // 从参数指针获取task内容
    data_t *base = t->base;                 // 得到数组起始地址
    size_t nele = t->nele;                  // 待排序数组长度
    task_queue_ptr tq = t->tq;
    free(vargp);
    size_t m = partition(base, nele);       // 分割数组，得到L、pivot和R，返回轴值的位置
    if (m > 1)                              // 如果L的长度大于1，排序L
        tqsort_helper(base, m, tq);
    if (nele-1 > m+1)                       // 如果R的长度大于1，排序R
        tqsort_helper(base+m+1, nele-m-1, tq);
    return NULL;
}
```

### Page40~41 并行快排的性能
Serial Fraction是指进行顺序排序的部分所占输入的比例。
Serail Fraction = 1 意味着100%的输入，都是用qsort_serial，即不使用多线程。
排序2^37个数字，大约134M个。
最佳的加速是6.84倍。

大多数的fraction都有着比较好的performance，极端情况除外。
当F太小的时候，并行度不够，所以比较慢。
当F太大的时候，Thread太多产生很多的负载，而且内存吃紧，造成运行缓慢。

## 阿姆达尔定律（Page42）
### 描述一个问题：
T 表示顺序执行总时间
p 代表可以加速部分的比例（p介于0~1之间）
k 代表加速比
### 那么公式为：
Tk = pT/k + (1-p)T
其中Tk表示在加速比为k的时候，新的顺序执行时间T。
### 极值
当k等于无穷大，Tk = (1-p)T
### Example（Page43）
计算一下加速效果。
### 阿姆达尔定律和并行快排
- 顺序执行瓶颈
  - 最顶层的分割环节，不会有任何加速
  - 第二层，最多最多只有两倍加速
  - 第k层，最多可以有2^k-1次方的加速
- 意义
  - 小规模的并行效果好
  - 大规模的如果想要并行，就必须让partition（分割）阶段并行

Page45是分割阶段并行的例子。
首先选取轴值p，然后划分的四个部分同时划分为L和R两个部分。
最后把所有的L聚集在左边，R聚集在右边。
这看起来是可以并行的，但是实际上在计算机中是不能加速的。
把四个部分划分为L和R可以，但是把多个L都放在数组左边，把多个R都放在数组右边，这意味着大量的拷贝操作。
而且很难直接在source array中直接操作，还需要额外的临时空间。

# 总结（Page47）
- 必须要有并行的策略
  - 分割成k个独立的部分
  - 使用分治法
- 循环内部不要使用同步机制
  - 使用同步机制的代价很高
- 关心硬件
  - 需要了解处理器和存储的架构
  - 关心全局数据的sharing和false sharing
- 关心阿姆达尔定律
  - 顺序代码会成为瓶颈
- 你可以做到的！
  - 进行温和的并行并不难
  - 建立试验框架，测试多种策略。