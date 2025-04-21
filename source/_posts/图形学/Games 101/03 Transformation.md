---
title: 03 Transformation
mathjax: true
abbrlink: 4e658e2f
date: 2025-04-17 23:05:40
tags: Games 101
categories: 图形学
---

Games 101 笔记 03 Transformation
<!-- less -->

## 概述
1. 变换（二维、三维）
2. 为什么学习旋转
3. 2维 变换
4. Homogeneous coordinates（齐次坐标）
5. **旋转、平移默认从原点开始**

## 旋转分类
1. 模型旋转
2. 视图旋转（摄像机旋转）

## 二维线性变换
**Scale（缩放）=>**
$$ x^,=sx $$
$$ y^,=sy $$
**缩放矩阵=>**
$$\begin{bmatrix} x^, \\ y^, \end{bmatrix} = \begin{bmatrix} s & 0 \\ 0 & s\\ \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix}$$
**翻转矩阵=>**
$$\begin{bmatrix} x^, \\ y^, \end{bmatrix} = \begin{bmatrix} -1 & 0 \\ 0 & 1\\ \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix}$$
**错切矩阵=>**
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/Pasted%20image%2020250116235703.png)
**旋转矩阵=>**
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/Pasted%20image%2020250116235735.png)
推导：
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/微信图片_20250117000234.jpg)
### 线性变换 Linear Transforms
$$\begin{bmatrix} x^, \\ y^, \end{bmatrix} = \begin{bmatrix} a & b \\ c & d\\ \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix}$$
## 二维平移变换
$$x^, = x + t$$
$$y^, = y + t$$
=>
$$\begin{bmatrix} x^, \\ y^, \end{bmatrix}=\begin{bmatrix} x \\ y \end{bmatrix} + \begin{bmatrix} t_x \\ t_y \end{bmatrix}$$

但$$\begin{bmatrix} x^, \\ y^, \end{bmatrix}=\begin{bmatrix} a & b \\ c & d\\  \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix} + \begin{bmatrix} t_x \\ t_y \end{bmatrix}$$不是线性变换，Is there a unified way to represent all transformations?->齐次坐标

## 齐次坐标（Homogenous Coordinates）
规定
2D point = $(x, y, 1)^T$
2D vector = $(x, y, 0)^T$
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/Pasted%20image%2020250117001413.png)
1. 为什么2D point 齐次坐标最后是1
    1. 其实更加通用的形式为$(x, y, w)^T = (x/w, y/w, 1)^T$
2. 为什么2D vector齐次坐标最后是0
    1. 向量具有平移不变性，即平移后，仍表示同一个向量

当w是1or0时，如下有效
1. vector + vector = vector
2. point - point = vector
3. point + vector = point
4. ponit + point = point（中点）

## 仿射变换（Affine Transformations）
**Affine map = linear map + translation**
1. 先线性变换
2. 再平移
$$\begin{bmatrix} x^, \\ y^, \end{bmatrix}=\begin{bmatrix} a & b \\ c & d\\  \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix} + \begin{bmatrix} t_x \\ t_y \end{bmatrix}$$
**齐次坐标表示=>**
$$\begin{bmatrix} x^, \\ y^, \\ 1 \end{bmatrix}=\begin{bmatrix} a & b & t_x \\ c & d & t_y\\ 0 & 0 & 1\\ \end{bmatrix} \begin{bmatrix} x \\ y \\ 1 \end{bmatrix}$$
## 逆变换（Inverse Transform）
$M^{-1}$逆矩阵表示了逆变换
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/Pasted%20image%2020250117002342.png)

## 转换顺序
1. **先旋转在平移≠先平移再旋转**
2. 矩阵应用顺序为从右到左![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/Pasted%20image%2020250117002604.png)
3. 多个仿射变换时可合并为一个![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/Pasted%20image%2020250117002749.png)
## 分解复杂变换
1. 平移到圆心
2. 旋转
3. 平移回去
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/Pasted%20image%2020250117002940.png)
