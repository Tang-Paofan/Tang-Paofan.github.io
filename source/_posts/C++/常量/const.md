---
title: const
mathjax: false
tags:
  - C++ 常量
categories:
  - C++
abbrlink: 6ac664ef
date: 2025-04-24 20:10:49
---

## 概述
1. `const`表示只读，但不能修改，值在运行时或编译时确定
2. 修饰变量、函数参数、类成员函数
<!-- less -->

## 概述
1. `const`表示只读，但不能修改，值在运行时或编译时确定
2. 修饰变量、函数参数、类成员函数
## const 修饰变量
#### const 修饰局部变量
```cpp
	const int a;	// 编译错误，未初始化
	const int a = 1;

	int& a = 10;	// 编译错误，非常量引用的初始值必须为左值
	const int& a = 10;
```
#### const 修饰指针变量（顶层const）
指针的指向不可变，**指针指向的值**可变
```cpp
	int* const a;	// 编译错误，未初始化
	int b = 3;
    int* const a = &b;
    int c = 4;
    a = &c;			// 编译错误，表达式必须是可修改的左值
```
#### const 修饰指向指针的变量（底层const）
**指针指向的值**不可变，指针的值向可变
// todo

// 引用
> const指针和指向const变量的指针，在写法上容易使人混淆。给大家介绍一种我自己用着比较顺手的区分方法：从右向左，依次结合，const就近结合。
> 比如，int * const p 可以这样进行解读：
> 1、int * ( const p )：变量p 经过 const 修饰，为只读变量。
> 2、int (* (const p))：（const p 现在作为一个整体） 只读变量p是一个指针。
> 3、(int (* (const p)))：（同样的 * const p 作为一个整体） 这个只读的指针p，指向一个int型变量。
## const 修饰函数
#### const 修饰函数参数
函数内部不能修改值
修饰形参指针也同上
```cpp
void foo(const int val);	// foo中不可修改val
```
#### const 修饰函数返回值
函数返回const返回值，主要用于**函数返回const引用。**
```cpp
const int foo()
{
	int a = 1;
	return a;
}
```
## const 修饰类
#### const 修饰类成员
同修饰变量
```cpp
class A
{
	const int a = 0;
};
```
#### const 修饰成员函数
const成员函数指的是，此函数不应该修改任何成员变量；但是`mutable `修饰的变量可以被修改
传给const成员函数的this指针，是指向 const 对象 的 const 指针。
```cpp
class A
{
	int a;

	int getA const()
	{
		return a;
	}
};
```
PS: 静态成员变量并不属于某个类对象，而是整个类共有的。静态成员变量可以不依附于某个实例化后的类对象进行访问。那么，静态成员变量的值，应该在任何实例化操作之前，就能够进行改变（否则，只有实例化至少一个对象，才能访问静态成员）。所以，静态成员变量不能够由构造函数进行内存分配，而应该在类外部单独定义，在实例化任何对象之前，就开辟好空间。又由于 const 成员变量 必须初始化，所以静态成员变量必须在定义的时候就初始化。
#### const 修饰类实例
const类对象指的是，此类对象不应该被改变。
类对象的 “改变” 定义：改变任何成员变量的值，调用任何非const成员函数

