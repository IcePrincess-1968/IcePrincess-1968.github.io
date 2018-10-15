---
layout: post
date: 2018-10-15
title: "[NOIP模拟赛]number 题解"
categories:
- [OI, 思维]
- [OI, Dynamic Programming]
tags: [NOIP模拟赛, dp, 思维]
mathjax: true
---

## Description

对于所有长度为$k$的满足$\sum_{i=1}^na_i=n$的不下降数列{$a_k$},其权值为$\sum_{i=1}^na_i^m$，求所有满足条件的数列的权值和。

数据范围: $n,m,k<=4096$

<!-- more -->

## Solution

非常好的dp题

这题显然的一个思路是用类似背包的dp,令$dp[i][j][k]$表示当前考虑到第$i$个数，所有数的和是$j$，最大的数是$k$的方案数,转移的时候还要考虑下一个数是多少，所以这个dp是$O(n^4)$的，不能接受。

给出一个比较难想的dp思路:令$dp[i][j]$表示当前数列有$i$个数，和为j的方案数，有两种转移：

1. 所有的数都加1，$dp[i][j]\rightarrow dp[i][j+i]$

2. 在数列的最前面添加一个1，$dp[i][j]\rightarrow dp[i+1]][j+1]$

可以发现按照这个dp构造数列的方法，所有的满足条件的数列都能不重不漏的构造出来。

现在我们已经知道了方案数，考虑如何计算最后的权值。我们考虑对于一个数$x$，$dp[k-1][n-x]$对应的是序列中至少有一个$x$的方案数，$dp[k-2][n-2x]$对应的是序列中至少有两个$x$的方案数，以此类推。我们发现一个有$num$个$x$的序列会正好在$num$个“至少”的方案数中出现一次，所以我们只要枚举$x$和$num$，累加$dp[k-num][n-x\cdot num] *x^m$即可

## Code
```cpp
#include <bits/stdc++.h>
using namespace std;

#define LL long long
#define LB long double
#define ull unsigned long long
#define x first
#define y second
#define pb push_back
#define pf push_front
#define mp make_pair
#define Pair pair<int,int>
#define pLL pair<LL,LL>
#define pii pair<double,double>
#define LOWBIT(x) x & (-x)
#define LOCAL true

const int INF=2e9;
const LL LINF=2e16;
const int magic=348;
const int MOD=1e9+7;
const double eps=1e-10;
const double pi=acos(-1);

struct fastio
{
	static const int S=1e7;
	char rbuf[S+48],wbuf[S+48];int rpos,wpos,len;
	fastio() {rpos=len=wpos=0;}
	inline char Getchar()
	{
		if (rpos==len) rpos=0,len=fread(rbuf,1,S,stdin);
		if (!len) return EOF;
		return rbuf[rpos++];
	}
	template <class T> inline void Get(T &x)
	{
		char ch;bool f;T res;
		while (!isdigit(ch=Getchar()) && ch!='-') {}
		if (ch=='-') f=false,res=0; else f=true,res=ch-'0';
		while (isdigit(ch=Getchar())) res=res*10+ch-'0';
		x=(f?res:-res);
	}
	inline void getstring(char *s)
	{
		char ch;
		while ((ch=Getchar())<=32) {}
		for (;ch>32;ch=Getchar()) *s++=ch;
		*s='\0';
	}
	inline void flush() {fwrite(wbuf,1,wpos,stdout);fflush(stdout);wpos=0;}
	inline void Writechar(char ch)
	{
		if (wpos==S) flush();
		wbuf[wpos++]=ch;
	}
	template <class T> inline void Print(T x,char ch)
	{
		char s[20];int pt=0;
		if (x==0) s[++pt]='0';
		else
		{
			if (x<0) Writechar('-'),x=-x;
			while (x) s[++pt]='0'+x%10,x/=10;
		}
		while (pt) Writechar(s[pt--]);
		Writechar(ch);
	}
	inline void printstring(char *s)
	{
		int pt=1;
		while (s[pt]!='\0') Writechar(s[pt++]);
	}
}io;

template<typename T> inline void check_max(T &x,T cmp) {x=max(x,cmp);}
template<typename T> inline void check_min(T &x,T cmp) {x=min(x,cmp);}
template<typename T> inline T myabs(T x) {return x>=0?x:-x;}
template<typename T> inline T gcd(T x,T y) {return y==0?x:gcd(y,x%y);}
inline int add(int x) {if (x>=MOD) x-=MOD;return x;}
inline int add(int x,int MO) {if (x>=MO) x-=MO;return x;}
inline int sub(int x) {if (x<0) x+=MOD;return x;}
inline int sub(int x,int MO) {if (x<0) x+=MO;return x;}
inline void Add(int &x,int y) {x=add(x+y);}
inline void Add(int &x,int y,int MO) {x=add(x+y,MO);}
inline void Sub(int &x,int y) {x=sub(x-y);}
inline void Sub(int &x,int y,int MO) {x=sub(x-y,MO);}
template<typename T> inline int quick_pow(int x,T y) {int res=1;while (y) {if (y&1) res=1ll*res*x%MOD;x=1ll*x*x%MOD;y>>=1;}return res;}
template<typename T> inline int quick_pow(int x,T y,int MO) {int res=1;while (y) {if (y&1) res=1ll*res*x%MO;x=1ll*x*x%MO;y>>=1;}return res;}

const int MAXN=4096;

int n,k,m;
int dp[MAXN+48][MAXN+48];

int main ()
{
#ifdef LOCAL
	double TIME=clock();
	freopen ("number.in","r",stdin);
	freopen ("number.out","w",stdout);
	cerr<<"Running..."<<endl;
#endif
	io.Get(n);io.Get(k);io.Get(m);
	dp[0][0]=1;
	for (register int i=0;i<=k-1;i++)
		for (register int j=0;j<=n;j++)
			if (dp[i][j])
			{
				Add(dp[i+1][j+1],dp[i][j]);
				if (j+i<=n) Add(dp[i][j+i],dp[i][j]);
			}
	int ans=0;
	for (register int i=1;i<=n;i++)
	{
		int base=quick_pow(i,m);
		for (register int j=1;j<=k && i*j<=n;j++)
			Add(ans,1ll*base*dp[k-j][n-i*j]%MOD);
	}
	io.Print(ans,'\n');
	io.flush();
#ifdef LOCAL
	cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
	return 0;
}
```