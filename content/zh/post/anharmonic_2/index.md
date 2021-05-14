---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "非谐声子模拟方法（二） 格林函数方法"
subtitle: ""
summary: ""
authors: []
tags: []
categories: []
date: 2021-05-14T17:55:08+02:00
lastmod: 2021-05-14T17:55:08+02:00
featured: false
draft: false
math: true

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

本文介绍用于计算非谐声子的格林函数方法，参考文献为

> Kong, L. T. Phonon dispersion measured directly from molecular dynamics simulations. Computer Physics Communications 182, 2201–2207 (2011).

首先，对于一个一维谐振子$H(x,p) = \frac {p^2} {2m} + \frac {1} {2} kx^2$，在一定温度$T$下有能均分定理（文献式(13)）：

$$ \frac {1} {2} k \left \langle x^2 \right \rangle = \frac {1} {2} k_B T $$

可得（文献式(14)）：
$$ k = k_B T / \left \langle x^2 \right \rangle $$

即通过位移平方的系综平均可以得到力常数。

文献中推导以上公式后，直接给出了晶体力常数矩阵和格林函数的关系。这里我将省略的推导步骤都写出来。

假设晶胞中有$N$个原子，简谐近似下的势能为$V=U^T\Phi U$，其中$\Phi$是$3N\times 3N$的力常数矩阵，$U$是长度为$3N$的位移矢量。力常数矩阵对角化后：

$$\Phi = Q^T \Lambda Q, \Lambda = diag(\dots,\lambda_i,\dots) $$
$$V = X^T \Lambda X, X = QU $$

系统变成$3N$个独立谐振子，第$i$个谐振子的力常数为$\lambda_i$，应用能均分定理有：

$$\frac {1} {2} \lambda_i \left \langle x_i^2 \right \rangle = \frac {1} {2} k_B T,  \left \langle x_i^2 \right \rangle = k_B T / \lambda_i $$

$$ G' = diag(\dots, \left \langle x_i^2 \right \rangle, \dots) = k_B T \Lambda^{-1} $$

由于$X=QU$，$G'$两边乘上$Q^{-1}$和$(Q^T)^{-1}$即是晶体的格林函数矩阵：

$$ G_{ij} = \left \langle u_i u_j \right \rangle $$
$$ G = Q^{-1} G' (Q^T)^{-1} = k_B T Q^{-1} \Lambda^{-1} (Q^T)^{-1} = k_B T \Phi^{-1} $$
$$ \Phi = k_B T G^{-1} $$

上式就是文献中的式(17)。

更进一步，可以把位移投影到倒空间中，得到在任意$q$点上力常数和格林函数的关系，其形式是一样的：

$$ \Phi(q) = k_B T G(q)^{-1}, G_{ij} = \left \langle u_i(q) u_j(q) \right \rangle . $$

我们得到了力常数与格林函数的关系，而格林函数可以通过分子动力学获得，从而可以计算有限温的声子谱。文献中比较了实验、晶格动力学（有限位移法）和格林函数法的声子谱和态密度，发现格林函数法的结果明显与实验更加接近。相关图片在文献中，也可以看知乎有人重复的实例(https://zhuanlan.zhihu.com/p/38010355)。

该方法已在LAMMPS中实现，文档链接为：(https://lammps.sandia.gov/doc/fix_phonon.html)，知乎上也有人翻译了中文版本并计算了例子：(https://zhuanlan.zhihu.com/p/38010355)。

与上一篇的Normal Mode Decomposition相比，格林函数方法不需要事先获得简正模，这是它的一个优势。此外，我们可以注意到格林函数法与Normal Mode Decomposition之间的关系。这里的格林函数，其实就是原子间的位移关联函数，而Normal Mode Decomposition使用的是速度自相关函数，是同一原子在不同时刻速度的关联函数。两种方法都通过关联函数来计算有限温的声子，它们可以统一到同一个理论框架下，具体内容下一篇再讲。


