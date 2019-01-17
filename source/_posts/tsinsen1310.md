---
layout: post
date: 2019-01-17
title: "[Tsinsen A1310]矩形计算(钟沛林) 题解"
categories:
- [OI, 数据结构, BIT]
- [OI, 常用技术, 根号暴力]
tags: [Tsinsen, BIT, 根号暴力]
mathjax: true
---

## Original Description
- [Click to open](http://www.tsinsen.com/A1310)

## Summary
- 对于一个矩阵，我们这样定义它的权值：一个数如果在该矩阵中出现了$p$次，则权值加上$p^2$。
- 现在给你一个$n\times m$的矩阵。有$q$次询问，每次问以$(x1,y1)$到$(x2,y2)$为对角线的子矩阵的权值。
- $n,q\leq 200,q \leq 200000$。
- 对于某$30\%$的数据，满足原矩阵中不同的数不超过$200$个。
<!-- more -->

## Solution
<details>
<summary>Click to view the solution</summary>
我们先考虑不同数字不超过$200$个的那个部分分。容易想到把询问离线下来，枚举每一种数字，判断它给所有询问带来的贡献。枚举到某种数字的时候，我们把该数字所在位置都填$1$，其他位置填$0$，然后维护二维前缀和就可以$O(1)$数一个区域内的$1$的个数，从而给每个询问计算贡献。时间复杂度$O(200\cdot (q+n^2))$。

当数字种类很多的时候这个方法就太慢了。不妨考虑类似根号暴力的做法。我们设定一个阈值$p$。如果一个数的出现次数大于$p$我们就使用这种方法，否则使用其他方法。因为出现次数大于$p$的数最多有$\frac{n^2}{p}$种，所以这一部分的复杂度为$O(\frac{n^2}{p}(n^2+q))$

接下来考虑如果有一种数出现次数不超过$p$怎么做。这里我们有一种很神奇的想法。我们要维护出现次数的平方，**这个等价于所有的出现位置两两匹配（包括自己和自己匹配）的方案数**。在$p$较小的时候，我们暴力枚举所有可能的点对，加入某种数据结构，最后对于每个询问计算一下它包含了多少个点对即可。

接下来思考如何维护这些点对。一个查询包含了一个点对相当于这个查询所对应的矩形区域包含了点对所对应的矩形区域。设查询为$(a,b),(c,d)$，我们要找的是满足$a\leq x_1,b\leq y_1,c\geq x_2,d\geq y_2$的点对$(x_1,y_1),(x_2,y_2)$的个数。可以想到用四维树状数组来维护这些点对，但这样空间会达到$n^4$，不能接受。考虑把所有点对和查询放在一起，先按照第一个坐标排序，这样我们只需要三维树状数组即可。点对的数量最多为$\frac{n^2}{p}\cdot p^2=n^2p$。这部分的时间复杂度为$O((n^2p+q)log^3n)$，忽略掉$q$,复杂度为$O(n^2plog^3n)$。

我们希望两部分复杂度平均，有方程$\frac{n^2}{p}(n^2+q)=n^2plog^3n$，解得$p=\sqrt{\frac{n^2+q}{log^3n}}$，约在$17$,$18$左右。此时总复杂度为$O(n^3log^{1.5}n+q(n\log^{1.5}n+log^3n))$。
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
#define x first
#define y second
#define pb push_back
#define pf push_front
#define mp make_pair
#define Pair pair<int,int>
#define pLL pair<LL,LL>
#define pii pair<double,double>
#define LOWBIT(x) x & (-x)
//#define LOCAL true

const int INF=2e9;
const LL LINF=2e16;
const int magic=18;
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

const int MAXN=200;
const int MAXQ=1e5;

int n,m,q;
int a[MAXN+48][MAXN+48];

int val[MAXN*MAXN+48],tot;
int cnt[MAXN*MAXN+48],sum[MAXN+1][MAXN+1];

inline int query(int X1,int Y1,int X2,int Y2) {return sum[X2][Y2]-sum[X2][Y1-1]-sum[X1-1][Y2]+sum[X1-1][Y1-1];}

int X1[MAXQ+48],Y1[MAXQ+48],X2[MAXQ+48],Y2[MAXQ+48];
int ans[MAXQ+48];

int head[MAXN*MAXN+48],tx[MAXN*MAXN+48],ty[MAXN*MAXN+48],nxt[MAXN*MAXN+48],t;
inline void add(int pos,int x,int y) {++t;tx[t]=x;ty[t]=y;nxt[t]=head[pos];head[pos]=t;}

namespace BIT
{
	int c[MAXN+48][MAXN+48][MAXN+48];
	inline void modify(int x,int y,int z)
	{
		int yy=y,zz=z;
		while (x)
		{
			y=yy;
			while (y<=n)
			{
				z=zz;
				while (z<=m)
				{
					c[x][y][z]++;
					z+=LOWBIT(z);
				}
				y+=LOWBIT(y);
			}
			x^=LOWBIT(x);
		}
	}
	inline int query(int x,int y,int z)
	{
		int res=0,yy=y,zz=z;
		while (x<=m)
		{
			y=yy;
			while (y)
			{
				z=zz;
				while (z)
				{
					res+=c[x][y][z];
					z^=LOWBIT(z);
				}
				y^=LOWBIT(y);
			}
			x+=LOWBIT(x);
		}
		return res;
	}
}

struct element
{
	int X1,Y1,X2,Y2,ind;
	element () {}
	inline element(int _X1,int _Y1,int _X2,int _Y2,int _i)
	{
		ind=_i;
		X1=min(_X1,_X2);X2=max(_X1,_X2);
		Y1=min(_Y1,_Y2);Y2=max(_Y1,_Y2);
	}
	inline void print()
	{
		cerr<<X1<<' '<<Y1<<' '<<X2<<' '<<Y2<<endl;
	}
	inline bool operator < (const element &other) const
	{
		if (X1!=other.X1) return X1>other.X1;
		return ind<other.ind;
	}
}ele[3000048];int etot;

int cx[MAXN*MAXN+48],cy[MAXN*MAXN+48],ctot;

int main ()
{
#ifdef LOCAL
	double TIME=clock();
	freopen ("a.in","r",stdin);
	freopen ("a.out","w",stdout);
	cerr<<"Running..."<<endl;
#endif
	scanf("%d%d",&n,&m);tot=0;
	for (register int i=1;i<=n;i++)
		for (register int j=1;j<=m;j++)
			scanf("%d",&a[i][j]),val[++tot]=a[i][j];
	sort(val+1,val+tot+1);tot=unique(val+1,val+tot+1)-(val+1);
	for (register int i=1;i<=n;i++)
		for (register int j=1;j<=m;j++)
			a[i][j]=lower_bound(val+1,val+tot+1,a[i][j])-val,cnt[a[i][j]]++,add(a[i][j],i,j);
	scanf("%d",&q);
	for (register int i=1;i<=q;i++)
	{
		int x,y,xx,yy;
		scanf("%d%d%d%d",&x,&y,&xx,&yy);
		X1[i]=min(x,xx);X2[i]=max(x,xx);
		Y1[i]=min(y,yy);Y2[i]=max(y,yy);
	}
	for (register int k=1;k<=tot;k++)
		if (cnt[k]>=magic)
		{
			memset(sum,0,sizeof(sum));
			for (register int i=head[k];i;i=nxt[i]) sum[tx[i]][ty[i]]=1;
			for (register int i=1;i<=n;i++)
				for (register int j=2;j<=m;j++)
					sum[i][j]+=sum[i][j-1];
			for (register int i=2;i<=n;i++)
				for (register int j=1;j<=m;j++)
					sum[i][j]+=sum[i-1][j];
			for (register int i=1;i<=q;i++)
			{
				int tmp=query(X1[i],Y1[i],X2[i],Y2[i]);
				ans[i]+=tmp*tmp;
			}
		}
		else
		{
			ctot=0;
			for (register int i=head[k];i;i=nxt[i]) ++ctot,cx[ctot]=tx[i],cy[ctot]=ty[i];
			for (register int i=1;i<=ctot;i++)
				for (register int j=1;j<=ctot;j++)
					ele[++etot]=element(cx[i],cy[i],cx[j],cy[j],0);
		}
	for (register int i=1;i<=q;i++) ele[++etot]=element(X1[i],Y1[i],X2[i],Y2[i],i);
	sort(ele+1,ele+etot+1);
	for (register int i=1;i<=etot;i++)
	{
		if (!ele[i].ind) BIT::modify(ele[i].Y1,ele[i].X2,ele[i].Y2); else ans[ele[i].ind]+=BIT::query(ele[i].Y1,ele[i].X2,ele[i].Y2);
	}
	for (register int i=1;i<=q;i++) printf("%d\n",ans[i]);
	io.flush();
#ifdef LOCAL
	cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
}
```
</details>