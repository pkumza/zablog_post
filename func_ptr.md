title: 如果一个C++函数的返回值是一个函数指针。
date: 2016-03-03 09:39:39
categories: ['学习笔记']
tags: ['C++']
---

函数指针，顾名思义，是指向函数入口的指针。
C++的指针非常灵活，同时也非常让人头疼，没有Python的函数那么容易理解（虽然好像Python的高阶函数也不好理解）。函数指针作为形参来使用还算相对简单，而函数指针作为返回值就更混乱的，这篇文章只着重说明函数指针作为返回值。

<!--more-->

如果返回值返回指向函数的指针，那么就必须把返回类型写成指针的形式，这最简单的方法是使用类型别名。

```C++
using Func = bool(const string &, const string &);        // 函数类型
using FuncPtr = bool(*)(const string &, const string &);  // 函数指针
```
话说上面两个表达式还是有一点点区别的，那就是前者是类型，后者是指针。前者并不是可以任意转换为指针来使用的。
我们定义一个返回值为函数指针的函数的时候，要注意看清楚。

```C++
Func getFunction1(double);        // 错误，返回值应该是函数指针，而不能是函数类型
Func *getFuntion2(double);        // 正确，返回值加了指针符号，是指针
FuncPtr getFunction3(double);     // 正确，FuncPtr本身就是函数指针类型。
```
上述是明白的写法，返回值类型在左边，函数名在中间，形参列表在右边，很容易理解。
如果不想要using进行别名的声明的话，也不是不可以，那样就非常繁琐了，如下：
```C++
bool (*getFunction4(double))(const string &, const string &);
```
这样就非常奇怪了，但是也是可以解释。从里往外看，首先getFunction4有形参double，所以getFunction4是一个函数。它前面是*，所以返回的是一个指针，因为*也有参数列表，所以是一个函数指针，返回值是bool。

当然了，还有尾置返回类型的方法。
```C++
auto getFunction5(double) -> bool (*)(const string &, const string &);
```