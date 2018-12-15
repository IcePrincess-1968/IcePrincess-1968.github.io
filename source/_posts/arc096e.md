---
layout: post
date: 2018-12-09
title: "[AtCoder Regular Contest #096E]Everything on It 题解"
categories:
- [OI, 数学, 容斥原理]
- [OI, 数学, 斯特林数, 第二类斯特林数]
tags: [AtCoder, 数学, 容斥原理, 第二类斯特林数]
mathjax: true
---

## Problem Statement
In "Takahashi-ya", a ramen restaurant, basically they have one menu: "ramen", but N kinds of toppings are also offered. When a customer orders a bowl of ramen, for each kind of topping, he/she can choose whether to put it on top of his/her ramen or not. There is no limit on the number of toppings, and it is allowed to have all kinds of toppings or no topping at all. That is, considering the combination of the toppings, $2^N$ types of ramen can be ordered.

<!-- more -->

Akaki entered Takahashi-ya. She is thinking of ordering some bowls of ramen that satisfy both of the following two conditions:

Do not order multiple bowls of ramen with the exactly same set of toppings.
Each of the N kinds of toppings is on two or more bowls of ramen ordered.
You are given N and a prime number M. Find the number of the sets of bowls of ramen that satisfy these conditions, disregarding order, modulo M. Since she is in extreme hunger, ordering any number of bowls of ramen is fine.

## Constraints

- 2≤N≤3000

- 1e8≤M≤1e9+9

- N is an integer.

- M is a prime number.

## Subscores

600 points will be awarded for passing the test set satisfying N≤50.

## Input
Input is given from Standard Input in the following format:

    N M

## Output
Print the number of the sets of bowls of ramen that satisfy the conditions, disregarding order, modulo M.

## Sample Input 1

    2 1000000007

## Sample Output 1

    2

Let the two kinds of toppings be A and B. Four types of ramen can be ordered: "no toppings", "with A", "with B" and "with A, B". There are two sets of ramen that satisfy the conditions:

- The following three ramen: "with A", "with B", "with A, B".
- Four ramen, one for each type.

## Sample Input 2

    3 1000000009

## Sample Output 2

    118

Let the three kinds of toppings be A, B and C. In addition to the four types of ramen above, four more types of ramen can be ordered, where C is added to the above four. There are 118 sets of ramen that satisfy the conditions, and here are some of them:

- The following three ramen: "with A, B", "with A, C", "with B, C".
- The following five ramen: "no toppings", "with A", "with A, B", "with B, C", "with A, B, C".
- Eight ramen, one for each type.
  
Note that the set of the following three does not satisfy the condition: "'with A', 'with B', 'with A, B'", because C is not on any of them.

## Sample Input 3

    50 111111113

## Sample Output 3

    1456748

Remember to print the number of the sets modulo M. Note that these three sample inputs above are included in the test set for the partial score.

## Sample Input 4
    
    3000 123456791

## Sample Output 4

    16369789

This sample input is not included in the test set for the partial score.

## Solution

注意到拉面的碗数是不固定的，所以从拉面的碗数的角度考虑不好做。

考虑根据每种配料容斥。由题意得，一种配料是坏的，当且仅当它只出现了0或1次，我们枚举有几种配料是坏的，则
$$ans=\sum_{i=0}^n(-1)^iC_n^if(i)$$
其中$f(i)$表示有$i$种配料是坏的，其他的随意的方案数。

考虑如何计算$f(i)$。枚举这$i$种配料一共加到了$j$碗面中，则
$$f(i)=2^{2^{n-i}}\sum_{j=0}^{i}g[i][j]*(2^{j})^{n-i}$$
其中$g[i][j]$表示将$i$中坏的配料加入$j$碗面的方案数,考虑剩下的$n-i$中配料如何加入面中，对于每种配料来说，每一碗面都可以放或者不放，是$2^j$种方案，$(n-i)$种配料就是$(2^j)^{n-i}$,再考虑这$j$碗面以外的面，这些面中除了$j$中坏的配料其他的配料都可以放或不放，所以一共有$2^{n-i}$种可行的面，每一种面都可以选择加入或者不加入我的集合当中，所以共有$2^{2^{n-i}}$种情况。

最后考虑$g[i][j]$怎么求，我们发现是将$i$种配料放入$j$碗面中且要求没有面非空，这个定义非常像第二类斯特林数，但这里每种配料还可以选择不在任意一碗中出现，所以稍稍魔改一下第二类斯特林数的递推式，得到
$$g[i][j]=g[i-1][j-1]+g[i-1][j]*(j+1)$$
注意和第二类斯特林数不一样的是最后乘的是$(j+1)$而不是$j$，因为如果$j$碗面中都有配料了，最后一种配料可以选择不加，多一种情况。

总时间复杂度$O(n^2)$

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
// #define LOCAL true

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
inline int quick_pow(int x,int y) {int res=1;while (y) {if (y&1) res=1ll*res*x%MOD;x=1ll*x*x%MOD;y>>=1;}return res;}
inline int quick_pow(int x,int y,int MO) {int res=1;while (y) {if (y&1) res=1ll*res*x%MO;x=1ll*x*x%MO;y>>=1;}return res;}

const int MAXN=3000;

int n,M;

int s[MAXN+48][MAXN+48];
inline void init_stirling()
{
	s[0][0]=1;
	for (register int i=1;i<=n;i++)
		for (register int j=1;j<=i;j++)
			s[i][j]=add(s[i-1][j-1]+1ll*j*s[i-1][j]%M,M);
}

int c[MAXN+48][MAXN+48];
inline void init_c()
{
	c[0][0]=1;
	for (register int i=1;i<=n;i++)
	{
		c[i][0]=c[i][i]=1;
		for (register int j=1;j<=i-1;j++)
			c[i][j]=add(c[i-1][j-1]+c[i-1][j],M);
	}
}

int main ()
{
#ifdef LOCAL
	double TIME=clock();
	freopen ("a.in","r",stdin);
	freopen ("a.out","w",stdout);
	cerr<<"Running..."<<endl;
#endif
	io.Get(n);io.Get(M);
	init_stirling();init_c();int ans=0;
	for (register int i=0;i<=n;i++)
	{
		int pw1=quick_pow(2,quick_pow(2,n-i,M-1),M);
		int pw2=quick_pow(2,n-i,M),pw=1;
		for (register int j=0;j<=i;j++)
		{
			int res=1ll*add(1ll*(j+1)*s[i][j+1]%M+s[i][j],M)*pw%M*pw1%M*c[n][i]%M;
			if (i&1) Sub(ans,res,M); else Add(ans,res,M);
			pw=1ll*pw*pw2%M;
		}
	}
	io.Print(ans,'\n');
	io.flush();
#ifdef LOCAL
	cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
	return 0;
}
```