---
title: QThread
mathjax: false
date: 2025-05-18 10:50:04
tags:
  - Qt线程
categories:
  - Qt
---

本文讲述了QThread 使用方式，以实际场景中使用使用为主，并不过过多讲解原理（前中期根本没必要）
<!-- less -->

## 成员函数

## 创建方式
### 方式一
`QThread` 静态成员`create`
```cpp
auto thd = QThread::create([]{});
```

### 方式二
继承`QThread`类，重写`run`

1. `run`函数它作为线程的入口，也就是线程从`run()`开始执行，我们打算在线程中完成的工作都要写在run()函数中，个人认为可以把run()函数理解为线程函数。这也就是子类覆写基类的虚函数，基类QThread的run()函数只是简单启动exec()消息循环。
2. `Qthread`中其他函数均不是在子线程中执行

### 方式三
`movemoveToThread`使用信号与槽方式来实现多线程

```cpp
class Work : public QObject
{
public slots:
    void doWorking()
    {
        for (size_t i = 0; i < 10; i++)
        {
            std::cout << i << ", thd ID=" << QThread::currentThread() << std::endl;
        }

        std::cout << "-----------------------------" << std::endl;
    }

    void doOtherWorking()
    {
        for (size_t i = 0; i < 10; i++)
        {
            std::cout << i << ", thd ID=" << QThread::currentThread() << std::endl;
        }

        std::cout << "doOtherWorking -----------------------------" << std::endl;
    }
};

auto pThd = new QThread;
Work work;
work.moveToThread(pThd);
QObject::connect(&startButton, &QPushButton::clicked, [&]
                 { pThd->start(); });

QObject::connect(&thdButton, &QPushButton::clicked, &work, &Work::doWorking);
QObject::connect(&thdButton, &QPushButton::clicked, &work, &Work::doOtherWorking);
    
std::cout << QThread::currentThread() << std::endl;
```

### 注意
1. `Work`实例化时不可以指定父对象
2. 当一个变量需要在多个线程间进行访问时，最好加上voliate关键字，以免读取到的是旧的值。当然，Qt中提供了线程同步的支持，比如互斥锁之类的玩意，使用这些方式来访问变量会更加安全
3. 一定要用信号槽机制，别想着直接调用，你会发现并没有在子线程中执行

## example
1. 非阻塞式线程运行
```cpp
QEventLoop loop;
QtConcurrent::run([&]()
{
    doSomething();
    loop.quit();
});
loop.exec();
```
上述写法存在两个问题
1. QtConcurrent::run()执行后线程立即开始执行，当doSomething()很快时，loop.quit()的执行时间可能早于loop.exec()，这样事件循环就再也不会退出了
2. 当doSomething因为某些原因crash时，如果没有导致程序崩溃，loop.quit无法被执行，事件循环同样无法退出

下面给出优化后的代码

```cpp
QEventLoop loop;
QThread* pThread = QThread::create([&]()
{
	doSomething();
});
connect(pThread, &QThread::finished, &loop, &QEventLoop::quit);
pThread->start(); // 线程开始执行
loop.exec();
```

## 总结
1. 第一种方法适合创建临时任务，用到了`lambda`表达式
2. 第二种方法适合在线程中处理单一事件，其逻辑简单（只需要新建一个继承自QThread类的对象，重写run函数，然后启动即可）
3. 第二种方法，我们可以自定义带参的子线程运行函数，代码简洁；随意修改需要在哪个线程中运行，代码灵活

## 参考文章
[QT从入门到入土（五（1））——多线程（QThread） - 唯有自己强大 - 博客园](https://www.cnblogs.com/xyf327/p/15032670.html)
