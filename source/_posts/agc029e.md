---
layout: post
date: 2018-12-19
title: "[AtCoder Grand Contest 029E]Wandering TKHS 题解"
categories:
- [OI, 数据结构, 并查集]
- [OI, 数据结构, BIT]
- [OI, 常用技术, dfs序]
tags: [AtCoder, 并查集, BIT, dfs序]
mathjax: true
---

## Problem Statement
Takahashi's office has $N$ rooms. Each room has an ID from $1$ to $N$. There are also $N−1$ corridors, and the $i$-th corridor connects Room $a_i$ and Room $b_i$
. It is known that we can travel between any two rooms using only these corridors.

<!-- more -->

Takahashi has got lost in one of the rooms. Let this room be $r$. He decides to get back to his room, Room $1$, by repeatedly traveling in the following manner:

- Travel to the room with the smallest ID among the rooms that are adjacent to the rooms already visited, but not visited yet.
Let $c_r$ be the number of travels required to get back to Room $1$. Find all of $c_2,c_3,...,c_N$ . Note that, no matter how many corridors he passes through in a travel, it still counts as one travel.

## Constraints

- $2\leq N\leq 2\times 10^5$
- $1\leq a_i,b_i\leq N$
- $a_i\neq b_i$
- The graph given as input is a tree.

## Input

Input is given from Standard Input in the following format:

    N
    a1 b1
    .
    .
    .
    an-1 bn-1

## Output

Print $c_r$ for each $r$, in the following format:

    c2 c3 ... cn

## Sample Input 1 
    6
    1 5
    5 6
    6 2
    6 3
    6 4

## Sample Output 1 
    5 5 5 1 5

For example, if Takahashi was lost in Room $2$, he will travel as follows:

- Travel to Room $6$.
- Travel to Room $3$.
- Travel to Room $4$.
- Travel to Room $5$.
- Travel to Room $1$.

## Sample Input 2 
    6
    1 2
    2 3
    3 4
    4 5
    5 6

## Sample Output 2 
    1 2 3 4 5

## Sample Input 3 
    10
    1 5
    5 6
    6 10
    6 4
    10 3
    10 8
    8 2
    4 7
    4 9

## Sample Output 3 
    7 5 3 1 3 4 7 4 5

## Solution

首先一个显然的性质是我们最后经过的所有的点是树上的一个联通块。

考虑对于任意一个点的c值该怎么求。我们发现一个点要到达根节点，至少要做的事情是打通从它到1的这条路径。

然后考虑这条链上挂的若干棵子树。我们发现对于每棵子树，与子树根连通的所有小于从1到子树根祖父的max的节点会访问到，其他的不会访问到（有点拗口）。

如果我们提前求好了每个节点的上述东西的答案。那我们只要一边搜索一边把各个子树的答案加起来，就可以得到所有的c了。

接下来考虑怎么算之前的那个东西。因为是祖父，跨过了父亲，所以有些难算。考虑离线。我们把所有的点按照1~祖父的max从小到大排序。这样我们可以按照编号去依次“点亮”一些节点，然后每次来统计某棵子树内和根节点相连的“点亮”的节点的个数。一个显然的想法是每次点亮一个节点后用LCT来维护子树和，但太难写了，没有很好的运用树的结构是固定的这个性质。

考虑这样一个做法：事实上我们要支持的是一些link操作。每次link会影响的节点是从被link的节点向上的一条被点亮的链的子树和。这样我们可以在树上打差分标记，在链的最下端加，在链的最上端的父亲减，这样把树映射到dfs序上之后只要查一个子树在dfs序上的和就行了。至于怎么找最上端的点，可以考虑并查集。定义一个联通块的代表元是深度最小的节点即可。

因为有一个BIT，所以总时间复杂度$O(nlogn)$

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

const int MAXN=2e5;

int n,maxn[MAXN+48],fa[MAXN+48];vector<int> v[MAXN+48];
int dp[MAXN+48],ans[MAXN+48];

int L[MAXN+48],R[MAXN+48],ind;

int id[MAXN+48];
inline bool cmp(int x,int y)
{
    int xx=fa[fa[x]],yy=fa[fa[y]];
    return maxn[xx]<maxn[yy];
}

bool exist[MAXN+48];

namespace DSU
{
    int pre[MAXN+48];
    inline void init() {for (register int i=1;i<=n;i++) pre[i]=i;}
    inline int find_anc(int x) {if (pre[x]!=x) pre[x]=find_anc(pre[x]);return pre[x];}
    inline void update(int x,int y) {x=find_anc(x);y=find_anc(y);pre[x]=y;}
}

namespace BIT
{
    int c[MAXN+48];
    inline void init() {memset(c,0,sizeof(c));}
    inline void update(int x,int delta) {if (!x) return;while (x<=n) c[x]+=delta,x+=LOWBIT(x);}
    inline int query(int x) {int res=0;while (x) res+=c[x],x^=LOWBIT(x);return res;}
    inline int calc(int x,int y) {return query(y)-query(x-1);}
}

inline void dfs(int cur,int father)
{
    L[cur]=++ind;fa[cur]=father;
    for (auto y : v[cur]) if (y!=father) maxn[y]=max(maxn[cur],y),dfs(y,cur);
    R[cur]=ind;
}

inline void Dfs(int cur,int father,int curans)
{
    int cursum=0;
    for (auto y : v[cur]) if (y!=father) cursum+=dp[y];
    ans[cur]=curans+1+cursum;
    for (auto y : v[cur]) if (y!=father) Dfs(y,cur,curans+1+cursum-dp[y]);
}

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    scanf("%d",&n);
    for (register int i=1;i<=n-1;i++)
    {
        int x,y;scanf("%d%d",&x,&y);
        v[x].pb(y);v[y].pb(x);
    }
    maxn[1]=1;dfs(1,0);
    for (register int i=1;i<=n;i++) id[i]=i;
    maxn[0]=INF;sort(id+1,id+n+1,cmp);
    DSU::init();BIT::init();int curpt=0;
    for (register int i=1;i<=n;i++)
    {
        if (fa[fa[id[i]]]==0) continue;
        int need=maxn[fa[fa[id[i]]]];
        while (curpt<need-1)
        {
            ++curpt;exist[curpt]=true;
            BIT::update(L[curpt],1);BIT::update(L[fa[curpt]],-1);
            for (auto y : v[curpt])
                if (exist[y])
                {
                    int xx=curpt,yy=y;if (fa[xx]==yy) swap(xx,yy);
                    int ynum=BIT::calc(L[yy],R[yy]),anc=DSU::find_anc(xx);
                    BIT::update(L[xx],ynum);BIT::update(L[fa[anc]],-ynum);
                    DSU::update(yy,xx);
                }
        }
        dp[id[i]]=BIT::calc(L[id[i]],R[id[i]]);
    }
    Dfs(1,0,0);
    for (register int i=2;i<=n;i++) printf("%d ",ans[i]-1);
    puts("");
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```