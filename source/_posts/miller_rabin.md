---
layout: post
date: 2018-8-24
title: "[学习笔记]Miller_Rabin强伪素性测试"
categories: [OI, 数学, Miller_Rabin强伪素性测试]
tags: [学习笔记, 数学, Miller_Rabin强伪素性测试]
mathjax: true
---

Miller_Rabin强伪素性测试主要基于下面两个定理

1. 费尔马小定理：若$gcd(a,p)=1$,则$a^{p-1}\equiv 1(mod$ &nbsp; $p)$

2. 二次探测定理：若p是质数且$a^2\equiv 1(mod$ &nbsp; $p)$,则$a\equiv \pm 1(mod$ &nbsp; $p)$

<!-- more -->

(对于2给出一个简单的证明: 若$a^2\equiv 1(mod$ &nbsp; $p)$,那么$p\mid a^2-1$,即$p\mid (a+1)(a-1)$,这里我们不讨论$p=2$的情况，因为p是质数，所以p不可能同时整除$a-1$和$a+1$,所以要么$p\mid a-1$,要么$p\mid a+1$,即$a\equiv \pm 1(mod$ &nbsp; $p)$)

也就是说，一旦找到任何一个$a$不满足上述条件，那么$p$一定不是质数

为什么定理1不够用还要定理2呢，因为有一种数叫Carmichael数，它们都是合数，且均满足对于$\forall a$,有$a^{p-1}\equiv 1(mod$ &nbsp; $p)$,这种数在1e8范围内只有255个，但是也足够多了

所以我们考虑下面的方法：

1. 对于$p-1$,把它写成$p-1=2^kt$的形式,其中$p\equiv 1(mod$ &nbsp; $2)$

2. 随机一个小于p的数a, 快速幂算出$a^t$

3. 当前数是$a^t$,自乘的结果是$a^{2t}$,再自乘的结果是$a^{2^2t}$,依次类推，自乘k次后这个数就会变成$a^{2^kt}$,即$a^{p-1}$

在3的过程中，参考上面的两条定理，我们可以得到：

- 如果最后这个数不等于1，那么p不是质数

- 如果在某一个时刻，这个数在模$p$意义下等于1,而上一时刻不等于$\pm 1$，那么$p$不是质数

我们称2和3为一次测试，一个数$p$,不是质数且通过一次测试的概率约为25%,所以多随机几次失败的概率就微乎其微