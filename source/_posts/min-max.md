---
layout: post
date: 2018-11-19
title: "Min-Max容斥"
categories: [OI, 数学, 容斥原理, Min-Max容斥]
tags: [讲稿, Min-Max容斥, 数学]
mathjax: true
---

规定$Max(S)$表示集合$S$中最大的元素，$Min(S)$表示集合$S$中最小的元素，$\mid S\mid$表示集合$S$的大小。

Min-Max容斥的公式如下：
$$Max(S)=\sum_{T\subseteq S}(-1)^{\mid T\mid +1}Min(T)$$

<!-- more -->

证明：

首先证明一个引理：对于$\forall n>0$，$\sum_{i=0}^n(-1)^iC_n^i=0$

证明：$\sum_{i=0}^n(-1)^iC_n^i=C_n^0+C_n^n+\sum_{i=1}^{n-1}(-1)^i(C_{n-1}^{i-1}+C_{n-1}^i)=C_n^0+C_n^n-C_{n-1}^0-C_{n-1}^{n-1}=0$ &nbsp; $\blacktriangle$

不妨设$S$中的元素是有序的，即对于$\forall i<j$,有$a_i<=a_j$

考虑每个元素作为最小值的贡献，可以将Min-Max容斥的结果写成
$$\sum_{i=1}^na_i\sum_{j=0}^{n-i}(-1)^jC_{n-i}^j$$

注意$j$和$\mid T\mid$正好相差2(最小值$a_i$被单独拎了出来),所以直接写$(-1)^j$是对的。

根据引理，只有当$n-i=0$，即$i=n$时后面一半不是0,所以原式等于$a_n$ &nbsp; $\blacktriangle$