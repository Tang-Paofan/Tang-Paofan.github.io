---
title: QFutureWatcher
mathjax: false
date: 2025-04-22 23:15:01
tags:
 - Qt线程
categories:
 - Qt
---

## 概述
1. 用于监视QFuture对象的状态和进度
2. `QFuture`的部分函数与`QFutureWatcher`重复
    1. `QFutureWatcher`侧重于信号槽
    2. `QFuture`直接查询任务状态
<!-- less -->

## 概述
1. 用于监视QFuture对象的状态和进度
2. `QFuture`的部分函数与`QFutureWatcher`重复
    1. `QFutureWatcher`侧重于信号槽
    2. `QFuture`直接查询任务状态

## 原文
>QFutureWatcher provides information and notifications about a QFuture. Use the setFuture() function to start watching a particular QFuture. The future() function returns the future set with setFuture().

>For convenience, several of QFuture's functions are also available in QFutureWatcher: progressValue(), progressMinimum(), progressMaximum(), progressText(), isStarted(), isFinished(), isRunning(), isCanceled(), isPaused(), waitForFinished(), result(), and resultAt(). The cancel(), setPaused(), pause(), resume(), and togglePaused() functions are slots in QFutureWatcher.

>Status changes are reported via the started(), finished(), canceled(), paused(), resumed(), resultReadyAt(), and resultsReadyAt() signals. Progress information is provided from the progressRangeChanged(), void progressValueChanged(), and progressTextChanged() signals.

>Throttling control is provided by the setPendingResultsLimit() function. When the number of pending resultReadyAt() or resultsReadyAt() signals exceeds the limit, the computation represented by the future will be throttled automatically. The computation will resume once the number of pending signals drops below the limit.


> `QFutureWatcher`提供关于`QFuture`的信息和通知，使用`setFuture()`开始监视一个`QFuture`，`future()`函数则返回通过`setFuture()`函数所设置的那个`QFuture`对象
> 为方便起见，`QFuture`的若干函数在`QFutureWatcher`中同样可用，这些函数包括：`progressValue()`（进度值）、`progressMinimum()`（进度最小值）、`progressMaximum()`（进度最大值）、`progressText()`（进度文本）、`isStarted()`（是否已启动）、`isFinished()`（是否已完成）、`isRunning()`（是否正在运行）、`isCanceled()`（是否已取消）、`isPaused()`（是否已暂停）、`waitForFinished()`（等待完成）、`result()`（结果）以及`resultAt()`（指定位置的结果）。 而`cancel()`（取消）、`setPaused()`（设置暂停）、`pause()`（暂停）、`resume()`（恢复）和`togglePaused()`（切换暂停状态）这些函数在`QFutureWatcher`中是槽函数。
> 状态变化会通过`started()`（已启动）、`finished()`（已完成）、`canceled()`（已取消）、`paused()`（已暂停）、`resumed()`（已恢复）、`resultReadyAt()`（指定索引处结果已准备好）以及`resultsReadyAt()`（多个结果已准备好）这些信号来进行报告。 进度信息则由`progressRangeChanged()`（进度范围已改变）、`progressValueChanged()`（进度值已改变）和`progressTextChanged()`（进度文本已改变）这些信号来提供。
> 节流控制由`setPendingResultsLimit()`函数提供。当待处理的`resultReadyAt()`或`resultsReadyAt()`信号的数量超过限定值时，该`QFuture`所代表的计算将会自动受到节流限制。一旦待处理信号的数量下降到限定值以下，计算将会恢复进行。


## example
```cpp
 // Instantiate the objects and connect to the finished signal.
 MyClass myObject;
 QFutureWatcher<int> watcher;
 connect(&watcher, &QFutureWatcher<int>::finished, &myObject, &MyClass::handleFinished);

 // Start the computation.
 QFuture<int> future = QtConcurrent::run(...);
 watcher.setFuture(future);
```
1. QFuture 表示异步计算的结果，QFutureWatcher 则允许使用信号和槽监视 QFuture，也就是说，QFutureWatcher 是为 QFuture 而生的