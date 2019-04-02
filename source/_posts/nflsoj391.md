---
layout: post
date: 2019-03-27
title: "[NFLSoj #391][六校联合训练 #12]修(elephant) 题解"
categories:
- [OI, 博弈论, SG函数]
- [OI, 数据结构, 线段树, 主席树]
- [OI, 常用技术, 线段树二分]
tags: [NFLSoj, SG函数, 主席树, 线段树二分]
mathjax: true
---

## Original Description
- [Click to open](https://acm.nflsoj.com/problem/391)

## Summary
- 有一张有$n$个点的有向图, 第$i$个节点向$[L_i,R_i]$中的每一个点连一条有向边。
- 有$q$次游戏，每次在图上放置$m_i$个棋子，并放置一个零食堆。每次操作可以将一个棋子沿出边移动一次，或者拿走任意数量的零食。
- 作为后手，你需要安排每次游戏的零食堆零食个数，以保证自己必胜。
- $n,q,\sum m_i\leq 200000$
<!-- more -->

## Solution
<details>
<summary>Click to view the solution</summary>
对于一个询问而言，每个棋子相当于一个独立的游戏，零食堆也相当于一个独立的游戏。根据Nim游戏的性质，要使得后手必胜，即先手必败，则这些游戏的异或和要是$0$。
则我们要算出这些棋子所在位置的$SG$函数的值。显然$1$号点是必败态,$SG(1)=0$，根据这张DAG的性质，我们的问题转化为了一个求区间$mex$的问题。

求区间$mex$可以考虑用主席树维护。对于主席树的第$i$棵线段树的第$j$个叶子，我们存储的是在$1$到$i$这些点中$SG$值为$j$的编号最大节点的编号。这样对于一个查询$[L,R]$，我们在$R$所在的线段树内进行二分，如果左边区间的$min$大于等于$L$说明左边区间内的$SG$值都存在，往右走，否则往左走。

总时间复杂度$O(nlogn+\sum m_i)$
</details>

## Code
<details>
<summary>Click to view the code</summary>
```cpp
#include <bits/stdc++.h>
using namespace std;

#define LL long long
#define LB long double
#define ull unsigned long long
#define uint unsigned int
#define pb push_back
#define pf push_front
#define mp make_pair
#define x first
#define y second
#define Pair pair<int,int>
#define pLL pair<LL,LL>
#define pii pair<double,double>
#define LOWBIT(x) ((x) & (-x))

const int INF=2e9;
const LL LIBNF=2e16;
const int magic=348;
const int MOD=998244353;
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

const int MAXN=2e5;

int n,q,L[MAXN+48],R[MAXN+48];
int mex[MAXN+48];

int root[MAXN+48];
namespace SegmentTree
{
	int lson[MAXN*20],rson[MAXN*20],minn[MAXN*20],tot;
	inline void pushup(int cur) {minn[cur]=min(minn[lson[cur]],minn[rson[cur]]);}
	inline void build(int &cur,int l,int r)
	{
		if (l==r) return;
		if (!cur) cur=++tot;
		int mid=(l+r)>>1;
		build(lson[cur],l,mid);build(rson[cur],mid+1,r);
	}
	inline void insert(int cur,int last,int pos,int nv,int l,int r)
	{
		lson[cur]=lson[last];rson[cur]=rson[last];
		if (l==r) {minn[cur]=nv;return;}
		int mid=(l+r)>>1;
		if (pos<=mid) lson[cur]=++tot,insert(lson[cur],lson[last],pos,nv,l,mid);
		else rson[cur]=++tot,insert(rson[cur],rson[last],pos,nv,mid+1,r);
		pushup(cur);
	}
	inline int query(int cur,int pos,int l,int r)
	{
		if (l==r) return l;
		int mid=(l+r)>>1;
		if (minn[lson[cur]]>=pos) return query(rson[cur],pos,mid+1,r); else return query(lson[cur],pos,l,mid);
	}
}

int main ()
{
	Get(n);for (register int i=2;i<=n;i++) Get(L[i]),Get(R[i]);
	SegmentTree::build(root[0],0,n);
	root[1]=++SegmentTree::tot;SegmentTree::insert(root[1],root[0],0,1,0,n);mex[1]=0;
	for (register int i=2;i<=n;i++)
	{
		mex[i]=SegmentTree::query(root[R[i]],L[i],0,n);
		root[i]=++SegmentTree::tot;SegmentTree::insert(root[i],root[i-1],mex[i],i,0,n);
	}
	Get(q);int num,x;
	while (q--)
	{
		Get(num);int xsum=0;
		while (num--) Get(x),xsum^=mex[x];
		printf("%d\n",xsum);
	}
	return 0;
}
```
</details>