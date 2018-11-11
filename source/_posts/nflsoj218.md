---
layout: post
date: 2018-9-28
title: "[NFLSoj #228][McfXH AK Contest]比赛 题解"
categories:
- [OI, Dynamic Programming, 斜率优化]
- [OI, 常用技术, cdq分治]
tags: [NFLSoj, dp, 斜率优化, cdq分治]
mathjax: true
---

## Problem statement

陈太阳非常喜欢打比赛。

最近有 n 个比赛，陈太阳参加第 i 个比赛能获得的快乐值是 ai，他准备选择某些比赛去虐场。

他选择的比赛的快乐值必须满足快乐值不下降，即如果陈太阳参加了第 $i$,$j$ 个比赛，且 $i<j$，则必须有 $a_i
\leq a_j$。

<!-- more -->

陈太阳如果不去打某个比赛他的心里就会难受，并且不打的时间越长他越难受。如果陈太阳选择不打某个比赛，并且他已经在这之前连续选择不打 x 个比赛了，那他就会损失 x+1 的快乐值。

请你计算陈太阳能获得的快乐值的最大值。注意他可以选择不参加所有比赛。

## Input

第一行一个整数 n。

接下来一行 n 个整数，表示 ai。

## Output

输出一个整数，表示答案。

## Sample Input 1

7

1 3 2 7 3 2 4

## Sample Output 1

7

## Sample Input 2

7

-3 -4 -2 -2 -6 -8 -1

## Sample Output 2

-11

## Constraints

- Subtask 11（30 pts）: 1≤n≤5000,1≤n≤5000；
- Subtask 22（70 pts）: 1≤n≤100000,1≤n≤100000。

## Solution

如果我们先不考虑不下降子序列的要求，那么这是一个比较显然的dp+斜率优化，推一波式子：

$dp[i]$表示以i结尾的最大收益,有状态转移方程:
$$dp[i]=a[i]+\max_{j=1}^{i-1}(dp[j]+\frac{(i-j)(i-j-1)}{2})$$

考虑决策$j$比决策$k$优的条件：
$$dp[j]+\frac{(i-j)(i-j-1)}{2}<dp[k]+\frac{(i-k)(i-k-1)}{2}$$

化简，按照斜率优化的套路移项，我们有
$$i<-\frac{(dp[j]-\frac{j(j+1)}{2})-(dp[k]-\frac{k(k+1)}{2})}{j-k}$$

考虑到$i$是递增的，我们应该把$(j,dp[j]-\frac{j(j+1)}{2})$看做点，用单调队列维护斜率的相反数的一个下凸壳

然而现在还有一个不下降子序列的要求，相当于转移要在一个二维偏序上进行

考虑cdq分治，定义函数$solve(l,r)$表示我们当前正在计算$l$到$r$的dp状态，刚开始我们将数组按照$a[i]$排序，然后我们预处理每个区间的点按照$id$排序的结果$pos$数组，然后先$solve(l,mid)$(注意这里的区间是按照$a[i]$排序的),接着考虑$l$到$mid$对$mid+1$到$r$的贡献,我们按照$pos$数组从$l$到$r$扫描，如果这个点来自$l$就把它加到下凸壳里面，如果这个点来自$r$就在单调队列里面找一个最优决策点，想想可以发现的确是按照二维偏序进行的更新。

时间复杂度$O(nlogn)$

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
inline int add(int x) {if (x>=MOD) x-=MOD;return x;}
inline int sub(int x) {if (x<0) x+=MOD;return x;}
inline void add(int &x,int y) {x=add(x+y);}
inline void sub(int &x,int y) {x=sub(x-y);}

const int MAXN=1e5;

int n;
int a[MAXN+48],id[MAXN+48];
int pos[21][MAXN+48];bool isleft[MAXN+48];LL dp[MAXN+48];
int q[MAXN+48];

inline void mergesort(int l,int r,int d)
{
	if (l==r) {pos[d][l]=id[l];return;}
	int mid=(l+r)>>1;
	mergesort(l,mid,d+1);mergesort(mid+1,r,d+1);
	for (register int k1=l,k2=mid+1,pt=l;k1<=mid || k2<=r;pt++)
	{
		if (k1>mid) {pos[d][pt]=pos[d+1][k2++];continue;}
		if (k2>r) {pos[d][pt]=pos[d+1][k1++];continue;}
		if (pos[d+1][k1]<pos[d+1][k2]) pos[d][pt]=pos[d+1][k1++]; else pos[d][pt]=pos[d+1][k2++];
	}
}

inline double calc(int id1,int id2)
{
	LL x1=id1,x2=id2,y1=-dp[id1]+1ll*id1*(id1+1)/2,y2=-dp[id2]+1ll*id2*(id2+1)/2;
	return double(y2-y1)/double(x2-x1);
}

inline void solve(int l,int r,int d)
{
	if (l==r) {dp[pos[d][l]]+=a[pos[d][l]];return;}
	int mid=(l+r)>>1,head=1,tail=0;
	solve(l,mid,d+1);
	for (register int i=l;i<=mid;i++) isleft[pos[d+1][i]]=true;
	for (register int i=l;i<=r;i++)
	{
		int cur=pos[d][i];
		if (isleft[cur])
		{
			while (head<tail && calc(q[tail-1],q[tail])>=calc(q[tail],cur)) tail--;
			q[++tail]=cur;
		}
		else
		{
			while (head<tail && calc(q[head],q[head+1])<cur) head++;
			if (head<=tail) check_max(dp[cur],dp[q[head]]-1ll*(cur-q[head]-1)*(cur-q[head])/2);
		}
	}
	for (register int i=l;i<=mid;i++) isleft[pos[d+1][i]]=false;
	solve(mid+1,r,d+1);
}

int main ()
{
#ifdef LOCAL
	double TIME=clock();
	freopen ("a.in","r",stdin);
	freopen ("a.out","w",stdout);
	cerr<<"Running..."<<endl;
#endif
	io.Get(n);
	for (register int i=1;i<=n;i++) io.Get(a[i]),id[i]=i;
	sort(id+1,id+n+1,[&](const int &x,const int &y) {if (a[x]!=a[y]) return a[x]<a[y];return x<y;});
	for (register int i=1;i<=n;i++) dp[i]=-1ll*i*(i-1)/2;
	mergesort(1,n,1);solve(1,n,1);
	LL ans=-1ll*n*(n+1)/2;
	for (register int i=1;i<=n;i++) check_max(ans,dp[i]-1ll*(n-i+1)*(n-i)/2);
	io.Print(ans,'\n');
	io.flush();
#ifdef LOCAL
	cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
	return 0;
}
```