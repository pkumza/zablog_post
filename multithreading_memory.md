title: 多线程的内存排布
date: 2016-12-26 12:46:23
categories: ['技术指南']
tags: [Linux]
---

写了一小段python代码来测试Linux对于多线程的内存排布。


```python
import threading
import time

class T(threading.Thread):
    def __init__(self, t_name):
        threading.Thread.__init__(self, name=t_name)
        self.name = t_name

    def run(self):
        print "%s Runs" % self.name
        # sleep for 1 minutes.
        time.sleep(60)

if __name__ == "__main__":
    t1 = T("t1")
    t2 = T("t2")
    t3 = T("t3")
    t1.start()
    t2.start()
    t3.start()
    t1.join()
    t2.join()
    t3.join()
    print "T1 & T2 reaped."
```

查看一下maps文件，

```bash
$cat /proc/.../maps
```

内容如下：
```text
zachary@LABCAT:/mnt/c/Projects/test$ cat /proc/309/maps
00400000-006bc000 r-x- 00000000 00:00 249355                     /usr/bin/python2.7
008bb000-008bc000 r--- 002bb000 00:00 249355                     /usr/bin/python2.7
008bc000-00931000 rw-- 002bc000 00:00 249355                     /usr/bin/python2.7
00931000-00943000 rw-- 00000000 00:00 0
020cb000-0219c000 rw-- 00000000 00:00 0                          [heap]
7f0a4c000000-7f0a4c021000 rw-- 00000000 00:00 0
7f0a4c021000-7f0a50000000 ---- 00000000 00:00 0
7f0a50000000-7f0a50021000 rw-- 00000000 00:00 0
7f0a50021000-7f0a54000000 ---- 00000000 00:00 0
7f0a54000000-7f0a54021000 rw-- 00000000 00:00 0
7f0a54021000-7f0a58000000 ---- 00000000 00:00 0
7f0a58910000-7f0a58911000 ---- 00000000 00:00 0
7f0a58911000-7f0a59111000 rw-- 00000000 00:00 0
7f0a59120000-7f0a59121000 ---- 00000000 00:00 0
7f0a59121000-7f0a59921000 rw-- 00000000 00:00 0
7f0a59930000-7f0a59931000 ---- 00000000 00:00 0
7f0a59931000-7f0a5a131000 rw-- 00000000 00:00 0
7f0a5a137000-7f0a5a2c0000 r--- 00000000 00:00 119790             /usr/lib/locale/locale-archive
7f0a5a2c0000-7f0a5a3c5000 r-x- 00000000 00:00 34505              /lib/x86_64-linux-gnu/libm-2.19.so
7f0a5a3c5000-7f0a5a3c6000 ---- 00105000 00:00 34505              /lib/x86_64-linux-gnu/libm-2.19.so
7f0a5a3c6000-7f0a5a5c4000 ---- 00000000 00:00 0
7f0a5a5c4000-7f0a5a5c5000 r--- 00104000 00:00 34505              /lib/x86_64-linux-gnu/libm-2.19.so
7f0a5a5c5000-7f0a5a5c6000 rw-- 00105000 00:00 34505              /lib/x86_64-linux-gnu/libm-2.19.so
7f0a5a5d0000-7f0a5a5e8000 r-x- 00000000 00:00 34714              /lib/x86_64-linux-gnu/libz.so.1.2.8
7f0a5a5e8000-7f0a5a5e9000 ---- 00018000 00:00 34714              /lib/x86_64-linux-gnu/libz.so.1.2.8
7f0a5a5e9000-7f0a5a7e7000 ---- 00000000 00:00 0
7f0a5a7e7000-7f0a5a7e8000 r--- 00017000 00:00 34714              /lib/x86_64-linux-gnu/libz.so.1.2.8
7f0a5a7e8000-7f0a5a7e9000 rw-- 00018000 00:00 34714              /lib/x86_64-linux-gnu/libz.so.1.2.8
7f0a5a7f0000-7f0a5a7f2000 r-x- 00000000 00:00 34384              /lib/x86_64-linux-gnu/libutil-2.19.so
7f0a5a7f2000-7f0a5a7f3000 ---- 00002000 00:00 34384              /lib/x86_64-linux-gnu/libutil-2.19.so
7f0a5a7f3000-7f0a5a9f1000 ---- 00000000 00:00 0
7f0a5a9f1000-7f0a5a9f2000 r--- 00001000 00:00 34384              /lib/x86_64-linux-gnu/libutil-2.19.so
7f0a5a9f2000-7f0a5a9f3000 rw-- 00002000 00:00 34384              /lib/x86_64-linux-gnu/libutil-2.19.so
7f0a5aa00000-7f0a5aa03000 r-x- 00000000 00:00 34478              /lib/x86_64-linux-gnu/libdl-2.19.so
7f0a5aa03000-7f0a5aa04000 ---- 00003000 00:00 34478              /lib/x86_64-linux-gnu/libdl-2.19.so
7f0a5aa04000-7f0a5ac02000 ---- 00000000 00:00 0
7f0a5ac02000-7f0a5ac03000 r--- 00002000 00:00 34478              /lib/x86_64-linux-gnu/libdl-2.19.so
7f0a5ac03000-7f0a5ac04000 rw-- 00003000 00:00 34478              /lib/x86_64-linux-gnu/libdl-2.19.so
7f0a5ac10000-7f0a5adca000 r-x- 00000000 00:00 34759              /lib/x86_64-linux-gnu/libc-2.19.so
7f0a5adca000-7f0a5add2000 ---- 001ba000 00:00 34759              /lib/x86_64-linux-gnu/libc-2.19.so
7f0a5add2000-7f0a5afca000 ---- 00000000 00:00 0
7f0a5afca000-7f0a5afce000 r--- 001ba000 00:00 34759              /lib/x86_64-linux-gnu/libc-2.19.so
7f0a5afce000-7f0a5afd0000 rw-- 001be000 00:00 34759              /lib/x86_64-linux-gnu/libc-2.19.so
7f0a5afd0000-7f0a5afd5000 rw-- 00000000 00:00 0
7f0a5afe0000-7f0a5aff9000 r-x- 00000000 00:00 34736              /lib/x86_64-linux-gnu/libpthread-2.19.so
7f0a5aff9000-7f0a5b003000 ---- 00019000 00:00 34736              /lib/x86_64-linux-gnu/libpthread-2.19.so
7f0a5b003000-7f0a5b1f8000 ---- 00000000 00:00 0
7f0a5b1f8000-7f0a5b1f9000 r--- 00018000 00:00 34736              /lib/x86_64-linux-gnu/libpthread-2.19.so
7f0a5b1f9000-7f0a5b1fa000 rw-- 00019000 00:00 34736              /lib/x86_64-linux-gnu/libpthread-2.19.so
7f0a5b1fa000-7f0a5b1fe000 rw-- 00000000 00:00 0
7f0a5b200000-7f0a5b223000 r-x- 00000000 00:00 34451              /lib/x86_64-linux-gnu/ld-2.19.so
7f0a5b2e0000-7f0a5b3d1000 rw-- 00000000 00:00 0
7f0a5b3e0000-7f0a5b421000 rw-- 00000000 00:00 0
7f0a5b422000-7f0a5b423000 r--- 00022000 00:00 34451              /lib/x86_64-linux-gnu/ld-2.19.so
7f0a5b423000-7f0a5b424000 rw-- 00023000 00:00 34451              /lib/x86_64-linux-gnu/ld-2.19.so
7f0a5b424000-7f0a5b425000 rw-- 00000000 00:00 0
7f0a5b430000-7f0a5b4b2000 rw-- 00000000 00:00 0
7f0a5b4c0000-7f0a5b4c1000 rw-- 00000000 00:00 0
7f0a5b4d0000-7f0a5b4d1000 rw-- 00000000 00:00 0
7f0a5b4e0000-7f0a5b4e1000 rw-- 00000000 00:00 0
7f0a5b4f0000-7f0a5b4f2000 rw-- 00000000 00:00 0
7ffff2573000-7ffff2d73000 rw-- 00000000 00:00 0                  [stack]
7ffff2f6b000-7ffff2f6c000 r-x- 00000000 00:00 0                  [vdso]
```

每一个子线程的stack都是放在一起的，而且默认分配好了一块比较大的空间。
当然，用python来测试这个内容是不太合理的，最好还是用C来测试。推荐看一下参考内容1。

由上面的内容可以推断出大致的虚存结构：
![图片](http://imglf0.nosdn.127.net/img/MGpGUW9CdGlzcDd1cm1TOXdxeWIwWGpxSHFVb0RIdUd2NnBndGRVMElhTnRmYXVkaG1Ca253PT0.png?imageView&thumbnail=1680x0&quality=96&stripmeta=0&type=jpg%7Cwatermark&type=2&text=wqkgWmFjaGFyeSAvIG1hcmNob24ubG9mdGVyLmNvbQ==&font=bXN5aA==&gravity=southwest&dissolve=30&fontsize=340&dx=16&dy=20&stripmeta=0)

参考内容：
 - [参考内容1_多线程程序在内存中的分布](http://blog.csdn.net/high_high/article/details/7204097)
 - [参考内容2_StackOverflow](http://stackoverflow.com/questions/18149218/the-memory-layout-of-a-multithreaded-process)
