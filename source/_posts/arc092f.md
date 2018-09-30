---
layout: post
date: 2018-9-30
title: "AtCoder Regular Contest 092F: Two Faced Edges 题解"
categories:
- [OI, 图论]
- [OI, 思维]
tags: [AtCoder, 思维，图论]
mathjax: true
---

## Problem Statement

You are given a directed graph with N vertices and M edges. The vertices are numbered 1,2,…,N, and the edges are numbered 1,2,…,M. Edge i points from Vertex ai to Vertex bi.

<!-- more -->

For each edge, determine whether the reversion of that edge would change the number of the strongly connected components in the graph.

Here, the reversion of Edge i means deleting Edge i and then adding a new edge that points from Vertex bi to Vertex ai.

## Constraints
- 2≤N≤1000
- 1≤M≤200,000
- 1≤ai,bi≤N
- ai≠bi
- If i≠j, then ai≠aj or bi≠bj.

## Input

Input is given from Standard Input in the following format:

	N M
	a1 b1
	a2 b2
	:
	aM bM
	
## Output

Print M lines. In the i-th line, if the reversion of Edge i would change the number of the strongly connected components in the graph, print diff; if it would not, print same.

## Sample Input 1

	3 3
	1 2
	1 3
	2 3

## Sample Output 1

	same
	diff
	same

The number of the strongly connected components is 3 without reversion of edges, but it will become 1 if Edge 2 is reversed.

## Sample Input 2

	2 2
	1 2
	2 1

## Sample Output 2

	diff
	diff

Reversion of an edge may result in multiple edges in the graph.

## Sample Input 3

	5 9
	3 2
	3 1
	4 1
	4 2
	3 5
	5 3
	3 4
	1 2
	2 5

## Sample Output 3

	same
	same
	same
	same
	same
	diff
	diff
	diff
	diff

## Solution

朴素的做法是$O(m^2)$的，考虑优化到$O(nm)$

我们发现翻转一条边$u\rightarrow v$后连通块个数是否改变主要取决于两点：

- $v$能否到$u$
- $u$能否不经过$u\rightarrow v$这条边到达$v$

稍微计算一下可以发现，如果上面两条都不满足，连通块个数减一，满足其一连通块个数不变，都满足连通块个数加一

我们枚举每个端点$u$，考虑$u$的所有出边，$v$能否到$u$这件事情是比较容易判断的，我们只要将所有边反向，从$u$做一遍dfs,能到的所有的点就是原图中能到$u$的点，这个部分是$O(m)$的

$u$能否不经过$u\rightarrow v$到$v$比较麻烦，考虑如下做法：

我们将$u$所有指向的点$v_1,v_2...v_k$找出来，然后从$v_1$出发做dfs,要求不经过$u$，找出所有能到的点，然后依次考察$v_2,v_3...v_k$，如果能从$v_1$到的话说明要求的路径存在

再从$v_2$出发做相同的事情，对$v_3,v_4...v_k$做考察，以此类推。这样做看似是$O(m^2)$的，但是注意到如果我们搜到一个已经搜过的点，是不用向下继续搜的，因为“前人”一定已经继续向下搜过了，所以只要每次的visited数组不清空，所有点的搜索复杂度总和就是$O(m)$的

以上我们只考虑了从编号小的点出发走到编号大的点的情况，所以还要再反过来做一次，考虑从编号大的点走到编号小的点的情况

这样，每个点的一轮的总复杂度是$O(m)$，一共$n$个点，总时间复杂度$O(nm)$

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

const int MAXN=1000;
const int MAXM=2e5;

int n,m;
vector<Pair> v[MAXN+48];
vector<int> vv[MAXN+48];
bool visited1[MAXN+48],visited2[MAXN+48],ok[MAXN+48];
int ans[MAXM+48];

inline void dfs1(int cur)
{
	visited1[cur]=true;
	for (register int i=0;i<int(vv[cur].size());i++)
	{
		register int y=vv[cur][i];
		if (!visited1[y]) dfs1(y);
	}
}

inline void dfs2(int cur)
{
	visited2[cur]=true;
	for (register int i=0;i<int(v[cur].size());i++)
	{
		register int y=v[cur][i].x;
		if (!visited2[y]) dfs2(y);
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
	io.Get(n);io.Get(m);int x,y;
	for (register int i=1;i<=m;i++)
	{
		io.Get(x);io.Get(y);
		v[x].pb(mp(y,i));vv[y].pb(x);
	}
	for (register int i=1;i<=n;i++)
	{
		memset(visited1,false,sizeof(visited1));
		memset(visited2,false,sizeof(visited2));
		memset(ok,false,sizeof(ok));
		dfs1(i);visited2[i]=true;
		for (register int j=0;j<int(v[i].size())-1;j++)
		{
			dfs2(v[i][j].x);
			for (register int k=j+1;k<int(v[i].size());k++)
				if (visited2[v[i][k].x]) ok[v[i][k].x]=true;
		}
		memset(visited2,false,sizeof(visited2));
		visited2[i]=true;
		for (register int j=int(v[i].size())-1;j>=1;j--)
		{
			dfs2(v[i][j].x);
			for (register int k=j-1;k>=0;k--)
				if (visited2[v[i][k].x]) ok[v[i][k].x]=true;
		}
		for (register int j=0;j<int(v[i].size());j++) ans[v[i][j].y]=(visited1[v[i][j].x]^ok[v[i][j].x]);
	}
	for (register int i=1;i<=m;i++) printf(ans[i]?"diff\n":"same\n");
	io.flush();
#ifdef LOCAL
	cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
	return 0;
}
```