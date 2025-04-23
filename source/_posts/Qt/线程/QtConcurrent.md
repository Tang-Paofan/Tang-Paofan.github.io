---
title: QtConcurrent
mathjax: false
date: 2025-04-23 23:16:46
tags:
 - Qt线程
categories:
 - Qt
---

## 概述
1. `QtConcurrent`用于多任务并发的高级API，无需显式管理线程的创建和同步
2. `QtConcurrent`没有事件循环
3. `QtConcurrent`没有任务优先级
4. 相比于`QThread`，`QtConcurrent`更方便
5. `QtConcurrent`没有事件循环->关联，**创建事件循环运行任务**
6. Cmake添加`QtConcurrent`模块
<!-- less -->

## 概述
1. `QtConcurrent`用于多任务并发的高级API，无需显式管理线程的创建和同步
2. `QtConcurrent`没有事件循环
3. `QtConcurrent`没有任务优先级
4. 相比于`QThread`，`QtConcurrent`更方便
5. `QtConcurrent`没有事件循环->关联，**创建事件循环运行任务**
6. Cmake添加`QtConcurrent`模块

## 成员函数
### `run`
#### 原型
```cpp
template <typename T>
QFuture<T> QtConcurrent::run(Function function, ...)
```
Equivalent to
```cpp
QtConcurrent::run(QThreadPool::globalInstance(), function, ...);
```

#### 解释
>Runs function in a separate thread. The thread is taken from the global QThreadPool. Note that function may not run immediately; function will only be run once a thread becomes available.
>
>T is the same type as the return value of function. Non-void return values can be accessed via the QFuture::result() function.
>
>Note: The QFuture returned can only be used to query for the running/finished status and the return value of the function. In particular, canceling or pausing can be issued only if the computations behind the future has not been started.

>在单独的线程运行，这个线程取自global QThreadPool，该函数可能不会立即运行，只有在线程可用时才会执行
>T 与函数返回值相同
>返回的`QFuture`对象只能用于查询函数的运行 / 完成状态以及返回值。尤其要注意的是，只有在`QFuture`背后对应的计算尚未开始时，才能发出取消或暂停操作（通过QtConcurrent::run()返回的QFuture不支持取消、暂停）

#### 细节
~~使用`QtConcurrent::run()`来运行自定义类的成员函数时一定要确保自定义类继承了`QObject`，不然线程池的初始化会有问题
默认第一个参数传递的this~~

why
~~`QtConcurrent::run`的第一个参数使用了`QThreadPool`，而`QThreadPool`是由`QObject`派生而来~~
**有待考究**
#### 使用
> QtConcurrent::run() also accepts pointers to member functions. The first argument must be either a const reference or a pointer to an instance of the class. Passing by const reference is useful when calling const member functions; passing by pointer is useful for calling non-const member functions that modify the instance

> `QtConcurrent::run()`函数也接受指向成员函数的指针。第一个参数必须是const 引用或类实例的指针

#### Qt Help的例子
For example, calling QByteArray::split() (a const member function) in a separate thread is done like this:
```cpp
 // call 'QList<QByteArray>  QByteArray::split(char sep) const' in a separate thread
 QByteArray bytearray = "hello world";
 QFuture<QList<QByteArray> > future = QtConcurrent::run(bytearray, &QByteArray::split, ',');
 ...
 QList<QByteArray> result = future.result();
```

Calling a non-const member function is done like this:
```cpp
 // call 'void QImage::invertPixels(InvertMode mode)' in a separate thread
 QImage image = ...;
 QFuture<void> future = QtConcurrent::run(&image, &QImage::invertPixels, QImage::InvertRgba);
 ...
 future.waitForFinished();
 // At this point, the pixels in 'image' have been inverted
```

Calling a lambda function is done like this:
```cpp
 QFuture<void> future = QtConcurrent::run([=]() {
     // Code in this block will run in another thread
 });
```

## 总结
1. QThread、QtConcurrent、QRunnable区别

| 特性               | QThread | QRunnable | QtConcurrent |
| ------------------ | ------- | --------- | ------------ |
| 高级API            | 否      | 否        | 是           |
| 面向任务           | 否      | 是        | 是           |
| 暂停/恢复/取消支持 | 否      | 否        | 是           |
| 具有优先级         | 是      | 否        | 否           |
| 可运行事件循环     | 是      | 否        | 否           |


## example
1. QtConcurrent
```cpp
class Threadtest : public QMainWindow
{
    Q_OBJECT

public:
    Threadtest(QWidget *parent = Q_NULLPTR);  
private:
    Ui::ThreadtestClass ui;
    void work();//成员函数
};
Threadtest::Threadtest(QWidget* parent)
    : QMainWindow(parent)
{
    ui.setupUi(this);
    connect(ui.btn_start, &QPushButton::clicked, [=]()
        {  
        //将成员函数放入参数中（成员函数需要引用指针）
            QtConcurrent::run(this, &Threadtest::work);
        });
}
void Threadtest::work()
{
    qDebug() << "子线程运行：" << QThread::currentThreadId();
    QThread::sleep(5);
    qDebug() << "子线程结束：" << QThread::currentThreadId();
}
```


```cpp
// A time-consuming function
int heavyTask(int value)
{
    QThread::sleep(1); // Simulate a long computation
    qDebug() << "thread id=" << QThread::currentThreadId();
    return value * value;
}

int main(int argc, char *argv[])
{
    QCoreApplication app(argc, argv);

    // List of integers to process
    QList<int> values = {1, 2, 3, 4, 5};

    // Use QtConcurrent to run the heavyTask function on each value in the list
    QFuture<int> future = QtConcurrent::mapped(values, heavyTask);

    // Create a QFutureWatcher to monitor the progress of the future
    QFutureWatcher<int> watcher;

    // Connect signals to monitor the progress
    QObject::connect(&watcher, &QFutureWatcher<int>::started, []()
                     { qDebug() << "Task started"; });

    QObject::connect(&watcher, &QFutureWatcher<int>::finished, [&future]()
                     {
        qDebug() << "Task finished";

        // Retrieve and display the results
        QList<int> results = future.results();
        for (int result : results) {
            qDebug() << "Result:" << result;
        } });

    QObject::connect(&watcher, &QFutureWatcher<int>::progressValueChanged, [](int progress)
                     { qDebug() << "Progress:" << progress; });

    // Set the future to the watcher
    watcher.setFuture(future);

    return app.exec();
}

// 输出
Task started
Progress: 0
thread id= 0x3954
thread id= 0x3f7c
thread id= 0x233c
thread id= 0x568
thread id= 0x4768
Progress: 1
Progress: 5
Task finished
Result: 1
Result: 4
Result: 9
Result: 16
Result: 25
```

2. QThreadPool多任务
```cpp
#include <QCoreApplication>
#include <QThreadPool>
#include <QRunnable>
#include <QDebug>
#include <QThread>

// 一个自定义的 QRunnable 任务
class HeavyTask : public QRunnable {
public:
    HeavyTask(int value) : m_value(value) {}

    void run() override {
        QThread::sleep(1); // 模拟长时间计算
        int result = m_value * m_value;
        qDebug() << "结果:" << result;
    }

private:
    int m_value;
};

int main(int argc, char *argv[]) {
    QCoreApplication app(argc, argv);

    QThreadPool *threadPool = QThreadPool::globalInstance();

    QList<int> values = {1, 2, 3, 4, 5};
    for (int value : values) {
        HeavyTask *task = new HeavyTask(value);
        threadPool->start(task);
    }

    threadPool->waitForDone(); // 等待所有任务完成

    return app.exec();
}
```

3. 与QEventLoop结合
```cpp
template<typename Func>
static void runInEventLoop(Func&& task)
{
    QEventLoop loop;
    QFutureWatcher<void> watcher;

    // 连接任务完成信号到事件循环的退出槽
    QObject::connect(&watcher, &QFutureWatcher<void>::finished, &loop, &QEventLoop::quit);

    // 启动异步任务
    QFuture<void> future = QtConcurrent::run(std::forward<Func>(task));

    // 设置FutureWatcher来监视任务
    watcher.setFuture(future);

    // 执行事件循环，等待任务完成
    loop.exec();

    // 确保任务在事件循环退出后已完成
    watcher.waitForFinished();
    }
```

## 参考文章
[QT从入门到入土（五（2））——多线程(QtConcurrent::run())和线程池 - 唯有自己强大 - 博客园](https://www.cnblogs.com/xyf327/p/15104043.html)
[QtConcurrent和QFuture的使用-CSDN博客](https://blog.csdn.net/Sakuya__/article/details/132500677)
[Qt并发模块Qt Concurrent的使用_qtconcurrent 用法-CSDN博客](https://blog.csdn.net/amnesiagreen/article/details/116560543)
[Qt多线程：QtConcurrent + QFuture + QFutureWatcher_51CTO博客_Qt多线程通信](https://blog.51cto.com/xiaohaiwa/5379232)
[最详细的Qt多线程的三种方法之三QtConcurrent::run()+QThreadPool_qtconcurrent::run()](https://blog.csdn.net/weixin_41761608/article/details/119328018)