---
layout: post
date: 2019-01-18
title: "[学习笔记]中国剩余定理"
categories:
- [OI, 数学, 中国剩余定理]
tags: [学习笔记, 中国剩余定理]
mathjax: true
---

## 普通中国剩余定理

若已知如下同余方程组
$$
\begin{cases}
x\equiv a_1(mod \ m_1) \\\\
x\equiv a_2(mod \ m_2) \\\\
\ldots \\\\
x\equiv a_n(mod \ m_n) \\\\
\end{cases}
$$
其中$m_1,m_2,\ldots ,m_n$两两互质，则$x$的通解可以这样计算：

<!-- more -->

令$M=\Pi_{i=1}^nm_i$，$M_i=\frac{M}{m_i}$，$M_i^{-1}$为$M_i$在模$m_i$意义下的逆元，则
$$x=\sum_{i=1}^na_iM_iM_i^{-1} (mod \ M)$$

> 证明：对于第$p$个方程式，因为除了$M_p$，其他的$M_i$都是$m_p$的倍数，所以其他的项在模$m_p$意义下都为$0$。考虑$a_pM_pM_p^{-1}$这一项，在模$m_p$意义下$M_pM_p^{-1}\equiv 1(mod \ m_p)$，所以$x$满足第$p$个方程式的限制。

## 扩展中国剩余定理

若已知如下同余方程组
$$
\begin{cases}
x\equiv a_1(mod \ m_1) \\\\
x\equiv a_2(mod \ m_2) \\\\
\ldots \\\\
x\equiv a_n(mod \ m_n) \\\\
\end{cases}
$$
但不保证$m_1,m_2,\ldots,m_n$两两互质，我们可以用两两合并的方法求出$x$的通解。

设当前已经合并完了前$i-1$个同余方程，正准备去合并第$i$个同余方程，我们现在有方程组
$$
\begin{cases}
x\equiv A(mod \ M) \\\\
x\equiv a_i(mod \ m_i) \\\\
\end{cases}
$$
则可以得到如下等式
$$A+Mx=a_i+m_iy$$
移项，得
$$Mx+m_i(-y)=a_i-A$$
如果$gcd(M,m_i) \nmid a_i-A$就可以直接判断原方程组无解。否则我们可以通过扩展欧几里得算法求出一个特解$x_0$，然后调整这个解使得$A+Mx$是满足限制的最小非负整数即可。

需要注意的是，这个调整的过程中做乘法会爆long long，所以对于$x$的调整要在模$lcm(M,m_i)$的意义下进行。具体的，我们写一个快速加来代替乘法即可。

最后我们解出来的答案是在模$lcm(m_1,m_2,\ldots m_n)$意义下的。通过加减模数的最小公倍数来获得通解。