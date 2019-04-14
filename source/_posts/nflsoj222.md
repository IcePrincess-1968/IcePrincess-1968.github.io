---
layout: post
date: 2019-03-31
title: "[NFLSoj #222]雨 题解"
categories:
- [OI, 常用技术, 分块]
- [OI, Dynamic Programming, 背包, 完全背包]
tags: [NFLSoj, dp, 分块]
mathjax: true
---

## Original Description
- [Click to open](https://acm.nflsoj.com/problem/222)

## Summary
- 求第一个数大于等于$x$小于等于$y$且所有数和为$n$的不下降序列个数，模$10^9+7$。
- $n\leq 300000$
<!-- more -->

## Solution
<details>
<summary>Click to view the solution</summary>
第一个数上下界都有限制比较麻烦，我们只要用第一个数大于等于$x$的序列个数减去大于等于$y+1$的序列个数即可。

接下来考虑如何计算第一个数仅有下界的情况。考虑分块。如果我们能预处理仅用不超过$\sqrt{n}$的数对每种和能构造多少种序列，和仅用大于$\sqrt{n}$的数对每种和能构造出多少种序列，那么最后只要把这两部分卷积一下就能得到答案（注意卷积只要知道最后一个数的答案，说白了就是枚举不超过$\sqrt{n}$的数的和）。

下面考虑这两部分分别怎么算。对于第一部分，可选的数只有$\sqrt{n}$个，令$dp[i][j]$表示考虑到第$i$种数，和为$j$的方案有多少，只要跑一个完全背包即可。

对于第二部分，数的种类比较多，无法直接跑普通的完全背包。这里有一个喜闻乐见的$dp$套路：令$dp[i][j]$表示当前有$i$个数，和为$j$的方案数，我们有两种操作：第一种是在序列最前端添加一个$\sqrt{n}+1$，$dp[i][j]$转移到$dp[i+1][j+\sqrt{n}+1]$，第二种是序列内所有数加一，$dp[i][j]$转移到$dp[i][i+j]$，可以证明任意合法的序列与这样的操作是一一对应的。

这样总复杂度$O(n\sqrt{n})$。写的时候注意一下块的分界和当前第一个数下界的大小关系。
</details>

## Code
<details>
<summary>Click to view the code</summary>
```cpp
#include <bits/stdc++.h>
using namespace std;

#define LL long long
#define LB long double
#define uint unsigned int
#define ull unsigned long long
#define pb push_back
#define pf push_front
#define mp make_pair
#define x first
#define y second
#define Pair pair<int,int>
#define pii pair<double,double>
#define pLL pair<LL,LL>
#define LOWBIT(x) ((x) & (-x))

const int INF=2e9;
const LL LINF=2e16;
const int magic=548;
const int MOD=1e9+7;
const double eps=1e-10;
const double pi=acos(-1);

template<typename T> inline void Get(T &x)
{
	bool f;char ch;T res;
	while (!isdigit(ch=getchar()) && ch!='-') {}
	if (ch=='-') f=false,res=0; else f=true,res=ch-'0';
	while (isdigit(ch=getchar())) res=res*10+ch-'0';
	x=(f?res:(-res));
}

template<typename T1,typename ...T2> inline void Get(T1 &x,T2 &...y) {Get(x);Get(y...);}

inline int add(int x) {if (x>=MOD) x-=MOD;return x;}
inline int sub(int x) {if (x<0) x+=MOD;return x;}
inline void Add(int &x,int y) {x=add(x+y);}
inline void Sub(int &x,int y) {x=sub(x-y);}

const int MAXN=3e5;

int n,X,Y;
int dp1[MAXN+48],dp2[2][MAXN+48],sum[MAXN+48];

inline int calc(int x)
{
	memset(dp1,0,sizeof(dp1));dp1[0]=1;
	for (register int i=x;i<=magic;i++)
		for (register int j=1;j<=n;j++)
		{
			if (i==j) Add(dp1[j],1);
			if (j-i>0) Add(dp1[j],dp1[j-i]);
		}
	memset(sum,0,sizeof(sum));memset(dp2,0,sizeof(dp2));
	dp2[0][0]=1;sum[0]=1;int cur=1,pre=0,minn=max(magic+1,x);
	for (register int i=1;i<=n/minn;i++)
	{
		memset(dp2[cur],0,sizeof(dp2[cur]));
		for (register int j=1;j<=n;j++)
		{
			if (j-i>=0) Add(dp2[cur][j],dp2[cur][j-i]);
			if (j-minn>=0) Add(dp2[cur][j],dp2[pre][j-minn]);
			Add(sum[j],dp2[cur][j]);
		}
		cur^=1;pre^=1;
	}
	int res=0;
	for (register int i=0;i<=n;i++) Add(res,1ll*dp1[i]*sum[n-i]%MOD);
	return res;
}

int main ()
{
#ifndef ONLINE_JUDGE
	freopen ("a.in","r",stdin);
	freopen ("a.out","w",stdout);
#endif
	Get(X,Y,n);
	printf("%d\n",sub(calc(X)-calc(Y+1)));
	return 0;
}
```
</details>