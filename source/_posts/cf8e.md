---
layout: post
date: 2018-10-08
title: "Codeforces #8E: Beads 题解"
categories:
- [OI, 思维]
tags: [Codeforces, 思维]
mathjax: true
---

## Description

One Martian boy called Zorg wants to present a string of beads to his friend from the Earth — Masha. He knows that Masha likes two colours: blue and red, — and right in the shop where he has come, there is a variety of adornments with beads of these two colours. All the strings of beads have a small fastener, and if one unfastens it, one might notice that all the strings of beads in the shop are of the same length. Because of the peculiarities of the Martian eyesight, if Zorg sees one blue-and-red string of beads first, and then the other with red beads instead of blue ones, and blue — instead of red, he regards these two strings of beads as identical. In other words, Zorg regards as identical not only those strings of beads that can be derived from each other by the string turnover, but as well those that can be derived from each other by a mutual replacement of colours and/or by the string turnover.

<!-- more -->

It is known that all Martians are very orderly, and if a Martian sees some amount of objects, he tries to put them in good order. Zorg thinks that a red bead is smaller than a blue one. Let's put 0 for a red bead, and 1 — for a blue one. From two strings the Martian puts earlier the string with a red bead in the i-th position, providing that the second string has a blue bead in the i-th position, and the first two beads i - 1 are identical.

At first Zorg unfastens all the strings of beads, and puts them into small heaps so, that in each heap strings are identical, in his opinion. Then he sorts out the heaps and chooses the minimum string in each heap, in his opinion. He gives the unnecassary strings back to the shop assistant and says he doesn't need them any more. Then Zorg sorts out the remaining strings of beads and buys the string with index k.

All these manupulations will take Zorg a lot of time, that's why he asks you to help and find the string of beads for Masha.

## Input

The input file contains two integers n and k (2 ≤ n ≤ 50;1 ≤ k ≤ 1016) —the length of a string of beads, and the index of the string, chosen by Zorg.

## Output

Output the k-th string of beads, putting 0 for a red bead, and 1 — for a blue one. If it s impossible to find the required string, output the only number -1.

## Examples

Input

4 4

Output

0101

## Note

Let's consider the example of strings of length 4 — 0001, 0010, 0011, 0100, 0101, 0110, 0111, 1000, 1001, 1010, 1011, 1100, 1101, 1110. Zorg will divide them into heaps: {0001, 0111, 1000, 1110}, {0010, 0100, 1011, 1101}, {0011, 1100}, {0101, 1010}, {0110, 1001}. Then he will choose the minimum strings of beads in each heap: 0001, 0010, 0011, 0101, 0110. The forth string — 0101.

## Solution

考虑按位确定答案，于是我们要能算出对于一个确定的前缀，有多少个合法的数是以这个前缀开头的。

首先我们可以观察到一个性质：所有的答案的第一位肯定是0，否则它的取反串肯定比他小。在确定了第一位是0之后，我们之后在计算个数的时候就不用考虑取反串和它本身的大小问题了

第二我们可以发现当前串的最后一位如果是0，那么原串的取反串的倒串就肯定比原串小；最后一位如果是1，那么原串的倒串肯定比原串小，所以一旦确定了最后一位，我们就又可以少考虑一种情况。

所以在计算个数的时候，我们先枚举最后一位是0还是1，这样我们自始至终就只要考虑原串和其倒串的大小关系或者是原串和其取反倒串的关系。

先讨论最后一位是0的情况，我们考虑按位枚举后缀，后缀的每一位和前缀的一位相对应。如果前缀的对应位是0，那么后缀的对应位放1就肯定是合法的了，中间可以2的若干次方直接计算答案，当然也可以放0继续和前缀匹配，如果前缀的对应位是1，就只能放1和其匹配。

设当前前缀长度是$len$,我们匹配的总位数是$Len=min(len,n-len)$,全部匹配完之后，我们又要分两种情况讨论：

1. $len\geq \frac{n}{2}$,此时整个串已经完全确定，我们还需要检验一下中间的这些位是否满足条件，这个部分比较简单。

2. $len<\frac{n}{2}$，此时我们中间还有一些空位要填，考虑如何快速计算有多少种填法符合条件，我们可以发现，如果中间填一个回文串肯定是符合条件的，回文串的个数很好计算，相当于将对应的位打包，个数是$2^{\left \lceil \frac{n-Len-Len}{2}\right \rceil}$。对于不是回文串的串，我们发现一个合法的填法取反肯定是不合法的，一个不合法的填法取反肯定是合法的，所以合法的和不合法的填法一一对应，我们用总填法数减去回文的填法数除以二即可

再讨论最后一位是1的情况，大部分和前面是一样的。我们可以一样的按位和前缀匹配，匹配完了之后$len\geq \frac{n}{2}$的做法也是一样的，但在$len<\frac{n}{2}$的时候，非回文的填法还是那样算，但我们发现回文的填法并不全是合法的，如果$n-Len-Len$是偶数是没有问题的，如果是奇数，我们会发现，最中间的那个单独的一位只能是0不能是1，所以这是回文的合法个数等于回文的总个数除以二。

写起来不长，但很容易错。时间复杂度$O(n^2)$

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
template<typename T> inline int quick_pow(int x,T y) {int res=1;while (y) {if (y&1) res=1ll*res*x%MOD;x=1ll*x*x%MOD;y>>=1;}return res;}
template<typename T> inline int quick_pow(int x,T y,int MO) {int res=1;while (y) {if (y&1) res=1ll*res*x%MO;x=1ll*x*x%MO;y>>=1;}return res;}

int n;LL k;
int a[148];LL pw[148];

inline LL Count(int len)
{
	int Len=min(len,n-len);LL res=0;
	//not reverse
	for (register int i=2;i<=Len;i++)
		if (a[i]==0) res+=pw[n-len-i];
	if ((len<<1)<n)
	{
		LL p=pw[(n-Len-Len+1)/2],np=pw[n-Len-Len]-p;
		res+=p+np/2;
	}
	else
	{
		bool f=true;
		for (register int i=Len+1,j=n-Len;i<=j;i++,j--)
		{
			if (a[i]==0 && a[j]==1) break;
			if (a[i]==1 && a[j]==0) {f=false;break;}
		}
		if (f) res++;
	}
	bool f=true;
	for (register int i=1;i<=len;i++) if (a[i]) {f=false;break;}
	if (f) res--; // 000...0 is not allowed
	if (len==n) return res;
	//reverse,a[n]=1;
	for (register int i=2;i<=Len;i++)
		if (a[i]==0) res+=pw[n-len-i];
	if ((len<<1)<n)
	{
		if ((n-Len-Len)%2==0)
		{
			LL p=pw[(n-Len-Len+1)/2],np=pw[n-Len-Len]-p;
			res+=p+np/2;
		}
		else
		{
			LL p=pw[(n-Len-Len+1)/2],np=pw[n-Len-Len]-p;
			p/=2;
			res+=p+np/2;
		}
	}
	else
	{
		bool f=true;
		for (register int i=Len+1,j=n-Len;i<=j;i++,j--)
		{
			if (a[i]==0 && a[j]==0) break;
			if (a[i]==1 && a[j]==1) {f=false;break;}
		}
		if (f) res++;
	}
	return res;
}

int main ()
{
#ifdef LOCAL
	double TIME=clock();
	freopen ("a.in","r",stdin);
	freopen ("a.out","w",stdout);
	cerr<<"Running..."<<endl;
#endif
	io.Get(n);io.Get(k);a[1]=0;
	pw[0]=1;for (register int i=1;i<=n;i++) pw[i]=pw[i-1]+pw[i-1]; 
	if (Count(1)<k) {printf("-1\n");return 0;}
	for (register int i=2;i<=n;i++)
	{
		a[i]=0;
		register LL res=Count(i);
		if (k>res) k-=res,a[i]=1;
	}
	for (register int i=1;i<=n;i++) printf("%d",a[i]);
	printf("\n");
#ifdef LOCAL
	cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
	return 0;
}
```