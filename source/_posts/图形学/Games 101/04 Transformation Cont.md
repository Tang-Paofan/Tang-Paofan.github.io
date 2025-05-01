---
title: 04 Transformation Cont
mathjax: true
tags: Games 101
categories: 图形学
abbrlink: 66c9c41e
date: 2025-04-28 20:40:51
---

Games 101 笔记 04 Transformation Cont
<!-- less -->

## 概述
1. 变换续（模型、视图、投影）
2. 三维变换
3. Viewing (观测) transformation
    1. View (视图) / Camera transformation（摄像机和视图变换）
    2. Projection (投影) transformation（3D→2D）
        1. Orthographic (正交) projection
        2. Perspective (透视) projection

## 三维变换
1. 使用齐次坐标表示
    1. 3D point = $(x, y, x, 1)^T$
    2. 3D vector = $(x, y, z, 0)^T$
2. 使用4x4矩阵表示仿射变换$$\begin{pmatrix} x'\\ y'\\ z'\\ 1 \end{pmatrix} = \begin{pmatrix} a & b & c & t_x\\ d & e & f & t_y\\ g & h & i & t_z\\ 0 & 0 & 0 & 1 \end{pmatrix} \cdot \begin{pmatrix} x\\ y\\ z\\ 1 \end{pmatrix}$$**线性变换在前，平移变换在后（同2D变换）**
3. 缩放$$\mathbf{S}(s_x, s_y, s_z) = \begin{pmatrix} s_x & 0 & 0 & 0\\ 0 & s_y & 0 & 0\\ 0 & 0 & s_z & 0\\ 0 & 0 & 0 & 1 \end{pmatrix}$$
4. 平移$$\mathbf{T}(s_x, s_y, s_z) = \begin{pmatrix} 1 & 0 & 0 & t_x\\ 0 & 1 & 0 & t_y\\ 0 & 0 & 1 & t_z\\ 0 & 0 & 0 & 1 \end{pmatrix}$$
5. 绕轴旋转$$\mathbf{R}_x(\theta) = \begin{pmatrix} 1 & 0 & 0 & 0\\ 0 & \cos\theta & -\sin\theta & 0\\ 0 & \sin\theta & \cos\theta & 0\\ 0 & 0 & 0 & 1 \end{pmatrix}\text{ ,右手螺旋法则y-z-x}$$$$\mathbf{R}_y(\theta) = \begin{pmatrix} \cos\theta & 0 & \sin\theta & 0\\ 0 & 1 & 0 & 0\\ -\sin\theta & 0 &  \cos\theta & 0\\ 0 & 0 & 0 & 1 \end{pmatrix}\text{ ,右手螺旋法则z-x-y}$$$$\mathbf{R}_y(\theta) = \begin{pmatrix} \cos\theta & -\sin\theta & 0 & 0\\ \sin\theta & \cos\theta & 0 & 0\\ 0 & 0 &  1 & 0\\ 0 & 0 & 0 & 1 \end{pmatrix}\text{ ,右手螺旋法则x-y-z}$$
6. 任意的3D旋转都可以转化为$R_x、R_y、R_z$$$\mathbf{R}_{xyz}(\alpha,\beta,\gamma) = \mathbf{R}_{x}(\alpha) \mathbf{R}_{y}(\beta) \mathbf{R}_{z}(\gamma)$$
7. 齐次坐标并不适合做插值，而**四元数适合**
8. Rodrigues’ Rotation Formula（罗德里格斯旋转公式 ）推导过程![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/微信图片_20250118181717.jpg)
## 视图变换
### 摄像机位置-what
1. 定义摄像机位置![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/20250428204545.png)

2. 总是要将摄像机移动到原点，Y轴为向上，朝-Z观看
3. 物体随着摄像机旋转
### 怎么旋转-how
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/20250428204629.png)
### 怎么旋转-do
1. 先平移到原点，写平移矩阵坐标$-\vec{e}$
2. 写旋转矩阵
    1. 直接写旋转矩阵不好写，可以先写逆矩阵
    2. 逆矩阵转置就是旋转矩阵（旋转矩阵为正交矩阵，旋转矩阵的逆矩阵就是转置矩阵）

#### 视图变换矩阵*
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/20250428204659.png)
## 投影变换（3D→2D）
1. 正交投影
2. 透视投影
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/20250428204731.png)
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/20250428204759.png)
### 正交投影
1. 将摄像机变换到原点，Y轴朝上，观测方向为-Z
2. 去除Z坐标
3. 将结果缩放到[-1, 1]中
#### 正交投影矩阵*
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/20250428204833.png)
### 透视投影
#### waht
1. 近大远小
2. 齐次坐标中，$(x, y, z, 1)$ ，$(kx, ky, kz, k != 0)$，$(xz, yz, z^2, z != 0)$都代表了相同的点$(x, y, z)$
    1. $(1, 0, 0, 1)$与$(2, 0, 0, 2)$都是同一个点
#### how
1. 先做透视投影到正交投影
    1. 将透视Frustum（锥台）挤压为长方体
2. 然后再做透视投影
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/20250428204906.png)
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/20250428204914.png)
![](https://cdn.jsdelivr.net/gh/Tang-Paofan/Asset/BlogPicture/微信图片_20250118193220.jpg)
#### 透视投影矩阵*
$$\begin{bmatrix} n & 0 & 0 & 0 \\ 0 & n & 0 & 0 \\ 0 & 0 & n+f & -nf \\ 0 & 0 & 1 & 0 \\ \end{bmatrix}$$

## 总结
1. 模型变换矩阵、视图变换矩阵、投影变换矩阵均应用在了模型上
2. 视图变换矩阵是将相机移动到原点，look at Z轴负方向，up Y轴正方向，此变换没有修改模型与相机的相对位置
3. 