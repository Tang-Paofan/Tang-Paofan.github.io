---
title: 02 Review of Linear Algebra
mathjax: true
tags:
  - games 101
categories:
  - 图形学
abbrlink: d597840a
date: 2025-04-12 23:23:23
---

Games 101 笔记
<!-- less -->

## 概述
1. 线性代数回顾
2. A Swift and Brutal  Introduction to Linear Algebra!

## 向量
1. 图形学中均为列向量
2. 表示方向和长度
3. 没有绝对的起点（任意两个方向和长度相等的向量可认为是同一个向量）
4. $\overrightarrow{AB} = B - A$

### 单位向量
1. 大小为1的向量
2. 被用来表示方向

## 点乘（Dot）
1. 结果为一个数
$$ \vec{a}\cdot\vec{b}=\lVert\vec{a}\rVert\lVert\vec{b}\rVert \cos\theta $$
**=>**
$$\cos \theta = \frac{\vec{a} \cdot \vec{b}}{\lVert \vec{a} \rVert \lVert \vec{b} \rVert}$$
$$\cos \theta = \hat{a} \cdot \hat{b} ,\text { $\hat{a}$ is unit vector, $\hat{a}=\frac{\vec{a}}{\lVert \vec{a} \rVert}$}$$

### 属性
$$\vec{a} \cdot \vec{b} = \vec{b} \cdot \vec{a}$$
$$\vec{a}(\vec{b} + \vec{c}) = \vec{a} \cdot \vec{b} + \vec{a} \cdot \vec{c}$$
$$(k\vec{a})\cdot \vec{b}= \vec{a} \cdot (k\vec{b}) = k{\vec{a} \cdot \vec{b}}$$


### 点乘-笛卡尔坐标系
二维
$$\vec{a} \cdot \vec{b}=\begin{pmatrix} x_a \\ y_a \\ \end{pmatrix} \cdot \begin{pmatrix} x_b \\ y_b \\\end{pmatrix} = x_ax_b + y_ay_b$$
三维
$$\vec{a} \cdot \vec{b}=\begin{pmatrix} x_a \\ y_a \\ z_a \end{pmatrix} \cdot \begin{pmatrix} x_b \\ y_b \\ z_b \end{pmatrix} = x_ax_b + y_ay_b + z_az_b$$

### 点乘在图形学中应用
1. 向量投影![](./02%20Review%20of%20Linear%20Algebra/Pasted%20image%2020250112113614.png)
2. 分解向量（计算投影后可根据平行四边形法则得到垂直向量）![](./02%20Review%20of%20Linear%20Algebra/Pasted%20image%2020250112113625.png)
3. 计算两个向量是**同向还是反向**![](./02%20Review%20of%20Linear%20Algebra/Pasted%20image%2020250112113701.png)
## 叉乘（Cross）
1. 结果为一个向量
2. 叉积向量与初始向量正交
3. 方向符合右手螺旋法则
4. 可以用来构建坐标系
5. 表示以这两个向量为边的平行四边形的面积（除以2就是三角形面积）

$$\vec{a} \times \vec{b} = -\vec{b} \times \vec{a}$$
$$\lVert \vec{a} \times \vec{b} \rVert = \lVert \vec{a} \rVert \lVert \vec{b} \rVert \sin \theta$$
### 属性
$$\vec{x} \times \vec{y} = +\vec{z}$$
$$\vec{y} \times \vec{x} = -\vec{z}$$

$$\vec{a} \times \vec{b} = -\vec{b} \times \vec{a}$$
$$\vec{a} \times (\vec{b} + \vec{c}) = \vec{a} \times \vec{b} + \vec{a} \times \vec{c}$$
$$\vec{a} \times (k\vec{b}) = k(\vec{a} \times \vec{b})$$
### 叉乘-笛卡尔坐标系
$$\vec{a} \times \vec{b} = 
\begin{pmatrix}
    y_az_b-y_bz_a \\
    z_ax_b-x_az_b \\
    x_ay_b-y_ax_b \\
\end{pmatrix}$$
$$\vec{a} \times \vec{b} = A^*b
\begin{pmatrix}
    0 & -z_a & y_a \\
    z_a & 0 & -x_a \\
    -y_a & x_a & 0 \\
\end{pmatrix}
\begin{pmatrix}
    x_b \\
    y_b \\
    z_b \\
\end{pmatrix}$$

### 叉乘在图形学中应用
1. 判断向量在左还是右（相对而言）![](./02%20Review%20of%20Linear%20Algebra/Pasted%20image%2020250112172643.png)
2. 判断向量在内还是外，判断p点在三角形内![](./02%20Review%20of%20Linear%20Algebra/Pasted%20image%2020250112172701.png)
3. ![](./02%20Review%20of%20Linear%20Algebra/Pasted%20image%2020250112172916.png)
## 矩阵
### 属性
1. 矩阵往往没有交换律
2. 具有分配律
3. 正交矩阵的转置等于其逆矩阵
4. 一般来说，旋转矩阵是正交矩阵

### 矩阵转置
$$(AB)^T=B^TA^T$$
### 逆矩阵
![](./02%20Review%20of%20Linear%20Algebra/Pasted%20image%2020250112173256.png)
### 向量形式乘法
![](./02%20Review%20of%20Linear%20Algebra/Pasted%20image%2020250112173402.png)
