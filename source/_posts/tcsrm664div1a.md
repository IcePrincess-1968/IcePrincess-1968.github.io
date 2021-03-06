---
layout: post
data: 2018-9-19
title: "[TopCoder SRM664DIV1A]BearPlays 题解"
categories:
- [OI, 数学]
tags: [TopCoder, 数学]
mathjax: true
---

## Problem Statement

Limak is a little bear who loves to play. Today he is playing by moving some stones between two piles of stones. Initially, one of the piles has A and the other has B stones in it.

<!-- more -->

Limak has decided to perform a sequence of K operations. In each operation he will double the size of the currently smaller pile. Formally, if the current pile sizes are labeled X and Y in such a way that X <= Y, he will move X stones from the second pile to the first one. After this move the new pile sizes will be X+X and Y-X.

You are given the ints A, B, and K. Determine the pile sizes after Limak finishes all his operations. Return the size of the smaller of those piles.

Formally, suppose that the final pile sizes are labeled P and Q in such a way that P <= Q. Return P.

## Definition

Class: BearPlays

Method: pileSize

Parameters: int, int, int

Returns: int

Method signature: int pileSize(int A, int B, int K)
(be sure your method is public)

## Notes
- Pay attention to the unusual time limit.
 
## Constraints
- A and B will be between 1 and 1,000,000,000, inclusive.
- K will be between 1 and 2,000,000,000, inclusive.

## Examples

0) 	
     	

4

7

2

Returns: 5

The process will look as follows:

Initially, the pile sizes are 4 and 7.

First operation: Limak doubles the pile of size 4 by moving 4 stones from the other pile to this pile. The new pile sizes are 8 and 3.

Second operation: Limak doubles the pile of size 3. The final pile sizes are 5 and 6.

As 5 <= 6, the correct return value is 5.

1) 	   	

5

5

3

Returns: 0

The initial pile sizes are 5 and 5. In the first operation Limak will double one of them, so after the operation the new pile sizes will be 10 and 0. The second and third operation do nothing: in each of them Limak doubles the size of an empty pile.

As 0 ≤ 10, the correct return value is 0.

2)

2

6

1

Returns: 4

After the only operation the pile sizes will be 4 and 4, hence the correct return value is 4.

3) 		

2

8

2000000000

Returns: 2

4) 		

900000000

350000000

3

Returns: 300000000

This problem statement is the exclusive and proprietary property of TopCoder, Inc. Any unauthorized use or reproduction of this information without the prior written consent of TopCoder, Inc. is strictly prohibited. (c)2010, TopCoder, Inc. All rights reserved. 

## Solution

数学神思维题

首先的性质是$x+y$的和不会变，设$x+y=n$

考虑如果当前$x\leq y$,则$x=2x$

如果当前$x\ge y$,则$x=x-y=2x-(x+y)=2x-n\equiv 2x(mod$ &nbsp; $n)$

综上，进行$k$轮之后,$x\equiv x*2^k(mod$ &nbsp; $n)$

所以算出这个，返回$min(x,n-x)$即可

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
// #define LOCAL true

const int INF=2e9;
const LL LINF=2e16;
const int magic=348;
const int MOD=998244353;
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

class BearPlays
{
	int n;
	inline int quick_pow(int x,int y)
	{
		int res=1;
		while (y)
		{
			if (y&1) res=(1ll*res*x)%n,y--;
			x=(1ll*x*x)%n;y>>=1;
		}
		return res;
	}
	public:
		inline int pileSize(int x,int y,int k)
		{
			n=x+y;
			x=(1ll*x*quick_pow(2,k))%n;
			return min(x,n-x);
		}
};

#ifdef LOCAL
int main ()
{
	freopen ("a.in","r",stdin);
	freopen ("a.out","w",stdout);
	BearPlays A;
	int nn,mm,kk;
	while (cin>>nn>>mm>>kk) cout<<A.pileSize(nn,mm,kk)<<endl;
	return 0;
}
#endif
```