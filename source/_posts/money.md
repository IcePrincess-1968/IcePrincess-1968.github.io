---
layout: post
date: 2018-10-08
title: "[NOIP2018模拟赛]money 题解"
categories:
- [OI, 常用技术, 启发式合并]
- [OI, 常用技术, 倍增]
- [OI, 数据结构, 链表]
tags: [NOIP模拟赛, 启发式合并, 倍增, 链表]
mathjax: true
---

## Description

给定$n$个节点，有$q$次操作:

1. $0$ $a$ $b$ $c$，表示$a$向$b$连一条有向边，并给$a$赋值$c$

2. $1$ $a$ $b$，如果$b$是$a$的祖先，输出$a$到$b$路径上的权值最小值（包括$a$的权值，不包括$b$的权值）

保证整张图在任意时刻都是一个森林

$n\leq 100000$,$q\leq 1000000$

<!-- more -->

## Solution

非常好的思维题

如果整棵树是静态的，我们肯定希望维护祖先和最小值的倍增表，从而在$log$的时间查询

现在是动态的，但我们发现每个点的祖先和最小值倍增表的值只会添加不会删除，因为每个点的祖先是越来越多的，所以我们只要不重复的修改所有节点的祖先和最小值倍增表，最后的时间复杂度就会是数组的大小$O(nlogn)$

考虑如何实现，我们对当前的每一颗树维护一个deque,deque里的每个节点又是一个单向链表，表示这棵树这一层的所有节点。

在更新祖先和最小值倍增表的过程中，考虑启发式合并，如果$a$所在的树的深度小于$b$所在的树的深度，我们抓着$a$里面的每个点去更新表格，随着深度的增加，更新的$2^k$的祖先的那个$k$也会增加，有点像two pointers；如果$a$所在的树的深度大于$b$所在的树的深度，我们抓着$b$里面的每个节点去找可能能更新到的$a$的节点，这个也是一个类似two pointers的东西。

在合并树的过程中，我们会发现相同深度的点是很好合并的，只要把第一个链表的结尾接上第二个链表的头就行了，这就是我们用链表存储信息的原因。合并的时候也要启发式，不然复杂度不对。

总时间复杂度$O(nlogn+mlogn)$，注意启发式合并的$log$和处理倍增表的$log$不是叠加的，所以不是$log^2n$

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

const int MAXN=1e5;

int n,m;

struct List
{
	int bg,ed;
	List () {}
	inline List(int b,int e) {bg=b;ed=e;}
};

struct node
{
	int nxt;
}nodes[MAXN+48];

inline List join(List x,List y)
{
	nodes[x.ed].nxt=y.bg;
	return List(x.bg,y.ed);
}

int root[MAXN+48];

struct tree
{
	vector<List> L,R;
	int sz,szl,szr;
	inline void clear() {L.clear();R.clear();sz=szl=szr=0;}
	inline void push_back(List nw) {R.pb(nw);szr++;sz++;}
	inline void push_front(List nw) {L.pb(nw);szl++;sz++;}
	inline List getind(int id) {return (id<=szl)?L[szl-id]:R[id-szl-1];}
	inline void modify(int id,List nw)
	{
		if (id<=szl) L[szl-id]=nw;
		else R[id-szl-1]=nw;
	}
}tree[MAXN+48];

inline void init()
{
	for (register int i=1;i<=n;i++) nodes[i].nxt=0,root[i]=i;
	for (register int i=1;i<=n;i++) tree[i].clear(),tree[i].pb(List(i,i));
}

int anc[MAXN+48][21],minn[MAXN+48][21];

inline int getdepth(int u)
{
	int res=0;
	for (register int i=20;i>=0;i--)
		if (anc[u][i]) res+=(1<<i),u=anc[u][i];
	return res+1;
}

inline int jump(int u,int depth)
{
	for (register int i=20;i>=0;i--)
		if (depth>=(1<<i)) depth-=(1<<i),u=anc[u][i];
	return u;
}

inline int getmin(int u,int depth)
{
	int res=INF;
	for (register int i=20;i>=0;i--)
		if (depth>=(1<<i)) depth-=(1<<i),res=min(res,minn[u][i]),u=anc[u][i];
	return res;
}

inline int query(int a,int b)
{
	int da=getdepth(a),db=getdepth(b);
	if (da<=db) return 0;
	if (jump(a,da-db)!=b) return 0;
	return getmin(a,da-db);
}

inline void update(int a,int b,int c)
{
	anc[a][0]=b;minn[a][0]=c;
	int A=root[a];
	int da=tree[A].sz,db=getdepth(b);
	//update info
	if (da<=db)
	{
		int curdepth=0;
		for (register int i=1;i<=da;i++)
		{
			while ((1<<curdepth)<i) curdepth++;
			List cur=tree[A].getind(i);
			for (register int j=max(curdepth,1);(1<<j)<=i-1+db;j++)
				for (register int nd=cur.bg;nd;nd=nodes[nd].nxt)
				{
					anc[nd][j]=anc[anc[nd][j-1]][j-1];
					minn[nd][j]=min(minn[nd][j-1],minn[anc[nd][j-1]][j-1]);
				}
		}
	}
	else
	{
		int curdepth=0;
		for (register int i=db;i>=1;i--)
		{
			while ((1<<curdepth)<(db-i+1)) curdepth++;
			for (register int j=max(curdepth,1);(1<<j)<=da-1+(db-i+1);j++)
			{
				List cur=tree[A].getind((1<<j)-(db-i+1)+1);
				for (register int nd=cur.bg;nd;nd=nodes[nd].nxt)
				{
					anc[nd][j]=anc[anc[nd][j-1]][j-1];
					minn[nd][j]=min(minn[nd][j-1],minn[anc[nd][j-1]][j-1]);
				}
			}
		}
	}
	//merge
	int Root=root[jump(b,db-1)],dep=tree[Root].sz;
	if (da<=db)
	{
		for (register int i=db+1,j=1;i<=dep && j<=da;i++,j++)
		{
			List cur=tree[Root].getind(i);
			List need=join(tree[A].getind(j),cur);
			tree[Root].modify(i,need);
		}
		for (register int i=dep-db+1;i<=da;i++) tree[Root].pb(tree[A].getind(i));
		tree[A].clear();
	}
	else
	{
		for (register int i=db+1,j=1;i<=dep && j<=da;i++,j++)
		{
			List cur=tree[A].getind(j);
			List need=join(cur,tree[Root].getind(i));
			tree[A].modify(j,need);
		}
		for (register int i=db+da+1;i<=dep;i++) tree[A].pb(tree[Root].getind(i));
		for (register int i=db;i>=1;i--) tree[A].pf(tree[Root].getind(i));
		tree[Root].clear();root[jump(b,db-1)]=A;
	}
}

int lastans=0;
inline int getreal(int x) {return (x+lastans)%n+1;}

int main ()
{
#ifdef LOCAL
	double TIME=clock();
	freopen ("money.in","r",stdin);
	freopen ("money.out","w",stdout);
	cerr<<"Running..."<<endl;
#endif
	io.Get(n);io.Get(m);
	init();int op,a,b,c;
	while (m--)
	{
		io.Get(op);io.Get(a);io.Get(b);
		a=getreal(a);b=getreal(b);
		if (!op) io.Get(c),c=getreal(c);
		if (!op) update(a,b,c);
		else
		{
			lastans=query(a,b);
			printf("%d\n",lastans);
		}
	}
	io.flush();
#ifdef LOCAL
	cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
	return 0;
}
```