---
layout: post
date: 2018-12-18
title: "[UOJ #3][NOI2014]魔法森林 题解"
categories:
- [OI, 图论, 最小生成树]
- [OI, 数据结构, LCT]
tags: [UOJ, NOI, 最小生成树, LCT]
mathjax: true
---

## Description
为了得到书法大家的真传，小E同学下定决心去拜访住在魔法森林中的隐士。魔法森林可以被看成一个包含个$N$节点$M$条边的无向图，节点标号为 $1…n$，边标号为$1…m$。初始时小E同学在 $1$ 号节点，隐士则住在 $n$ 号节点。小E需要通过这一片魔法森林，才能够拜访到隐士。

<!-- more -->

魔法森林中居住了一些妖怪。每当有人经过一条边的时候，这条边上的妖怪就会对其发起攻击。幸运的是，在 $1$ 号节点住着两种守护精灵：A型守护精灵与B型守护精灵。小E可以借助它们的力量，达到自己的目的。

只要小E带上足够多的守护精灵，妖怪们就不会发起攻击了。具体来说，无向图中的每一条边 $e_i$ 包含两个权值 $a_i$ 与 $b_i$。若身上携带的A型守护精灵个数不少于 $a_i$，且B型守护精灵个数不少于 $b_i$，这条边上的妖怪就不会对通过这条边的人发起攻击。当且仅当通过这片魔法森林的过程中没有任意一条边的妖怪向小E发起攻击，他才能成功找到隐士。

由于携带守护精灵是一件非常麻烦的事，小E想要知道，要能够成功拜访到隐士，最少需要携带守护精灵的总个数。守护精灵的总个数为A型守护精灵的个数与B型守护精灵的个数之和。

## Input Format

第$1$行包含两个整数 $n$,$m$，表示无向图共有 $n$ 个节点，$m$ 条边。

接下来 $m$ 行，第 $i+1$ 行包含4个正整数 $x_i$,$y_i$,$a_i$,$b_i$，描述第 $i$ 条无向边。其中 $x_i$ 与 $y_i$ 为该边两个端点的标号，$a_i$ 与 $b_i$的含义如题所述。

注意数据中可能包含重边与自环。

## Output Format

输出一行一个整数：如果小E可以成功拜访到隐士，输出小E最少需要携带的守护精灵的总个数；如果无论如何小E都无法拜访到隐士，输出“$-1$”（不含引号）。

## Sample Input 1
    4 5
    1 2 19 1
    2 3 8 12
    2 4 12 15
    1 3 17 8
    3 4 1 17

## Sample Output 1
    32

## Explanation 1

如果小E走路径1→2→4，需要携带 $19+15=34$ 个守护精灵；

如果小E走路径1→3→4，需要携带 $17+17=34$ 个守护精灵；

如果小E走路径1→2→3→4，需要携带 $19+17=36$ 个守护精灵；

如果小E走路径1→3→2→4，需要携带 $17+15=32$ 个守护精灵。

综上所述，小E最少需要携带 $32$ 个守护精灵。

## Sample Input 2
    3 1
    1 2 1 1

## Sample Output 2
    -1

## Explanation 2

小E无法从$1$号节点到达$3$号节点，故输出$-1$。

## Constraints
- 对于$100\%$的数据，$2\leq n\leq 50000$,$0\leq m\leq 100000$,$1\leq a_i,b_i\leq 50000$

## Solution

将所有的边按照$a_i$为关键字升序从小到大排序，这样我们之后可以从小到大枚举最后的$A$，随着$A$的增大，我们可用的边也就逐渐增多，要在这个不断加边的图中找到让$B$最小的方案。

不难发现，对于图中的任意两点，使得他们之间的路径的最大值最小，这条路径一定在图的最小生成树上，因此我们只要动态的维护以$b_i$为边权的图的最小生成树即可。

考虑用LCT维护最小生成树。对于当前的一条新边$(x,y)$，如果$x$,$y$不连通就直接link。否则我们找到$x$到$y$的路径上的最大边，如果最大边的权值大于当前边，就cut掉最大边，link上当前边，否则直接丢掉当前边。

PS: 注意LCT判断两个点是否连通的条件:应该都find_root之后判断根是否相同。

## Code
这份代码在UOJ上97pts,可能常数大了点，~~洛谷是能过的2333~~
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

int n,m;

struct Edge
{
    int u,v,a,b;
    inline void input() {scanf("%d%d%d%d",&u,&v,&a,&b);}
    inline bool operator < (const Edge &other) const {return a<other.a;}
}edge[MAXN+48];

namespace LCT
{
    struct node
    {
        int ch[2],val,maxn,maxind,flip,father;
    }tree[MAXN+48];
    inline void update(int x,int y)
    {
        if (!y) return;
        if (tree[y].maxn>tree[x].maxn) tree[x].maxn=tree[y].maxn,tree[x].maxind=tree[y].maxind;
    }
    inline void pushup(int cur)
    {
        tree[cur].maxn=tree[cur].val;tree[cur].maxind=cur;
        update(cur,tree[cur].ch[0]);update(cur,tree[cur].ch[1]);
    }
    inline void pushdown(int cur)
    {
        if (tree[cur].flip)
        {
            tree[tree[cur].ch[0]].flip^=1;
            tree[tree[cur].ch[1]].flip^=1;
            tree[cur].flip=0;swap(tree[cur].ch[0],tree[cur].ch[1]);
        }
    }
    inline void Create(int cur,int val)
    {
        tree[cur].val=val;tree[cur].ch[0]=tree[cur].ch[1]=tree[cur].flip=tree[cur].father=0;
        pushup(cur);
    }
    inline bool isroot(int cur) {return tree[tree[cur].father].ch[0]!=cur && tree[tree[cur].father].ch[1]!=cur;}
    inline void rotate(int x)
    {
        int y=tree[x].father,z=tree[y].father;
        pushdown(y);pushdown(x);
        int k=(tree[y].ch[1]==x);
        if (!isroot(y)) tree[z].ch[tree[z].ch[1]==y]=x;
        tree[y].ch[k]=tree[x].ch[k^1];tree[x].ch[k^1]=y;
        tree[tree[y].ch[k]].father=y;tree[y].father=x;tree[x].father=z;
        pushup(y);pushup(x);
    }
    inline void splay(int x)
    {
        pushdown(x);
        while (!isroot(x))
        {
            int y=tree[x].father,z=tree[y].father;
            if (!isroot(y)) ((tree[y].ch[1]==x)^(tree[z].ch[1]==y))?rotate(x):rotate(y);
            rotate(x);
        }
    }
    inline void access(int cur)
    {
        for (register int pre=0;cur;pre=cur,cur=tree[cur].father)
        {
            splay(cur);
            tree[cur].ch[1]=pre;pushup(cur);
        }
    }
    inline void makeroot(int cur)
    {
        access(cur);splay(cur);
        tree[cur].flip^=1;
    }
    inline int find_root(int x)
    {
        access(x);splay(x);
        while (tree[x].ch[0]) x=tree[x].ch[0];
        return x;
    }
    inline bool issame(int x,int y)
    {
        return find_root(x)==find_root(y);
    }
    inline void link(int x,int y)
    {
        makeroot(x);
        tree[x].father=y;
    }
    inline void cut(int x,int y)
    {
        makeroot(x);access(y);splay(y);
        tree[x].father=0;tree[y].ch[0]=0;pushup(y);
    }
    inline Pair query(int x,int y)
    {
        makeroot(x);access(y);splay(y);
        return mp(tree[y].maxn,tree[y].maxind);
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
    scanf("%d%d",&n,&m);
    for (register int i=1;i<=m;i++) edge[i].input();
    sort(edge+1,edge+m+1);
    for (register int i=1;i<=n;i++) LCT::Create(i,0);
    for (register int i=1;i<=m;i++) LCT::Create(n+i,edge[i].b);
    int ans=INF;
    for (register int i=1;i<=m;i++)
    {
        if (edge[i].a>=ans) break;
        if (edge[i].u!=edge[i].v)
        {
            if (!LCT::issame(edge[i].u,edge[i].v))
                LCT::link(n+i,edge[i].u),LCT::link(n+i,edge[i].v);
            else
            {
                Pair res=LCT::query(edge[i].u,edge[i].v);
                if (res.x>edge[i].b)
                {
                    LCT::cut(res.y,edge[res.y-n].u);LCT::cut(res.y,edge[res.y-n].v);
                    LCT::link(n+i,edge[i].u);LCT::link(n+i,edge[i].v);
                }
            }
        }
        if (LCT::issame(1,n)) check_min(ans,edge[i].a+LCT::query(1,n).x);
    }
    if (ans>=INF) ans=-1;
    printf("%d\n",ans);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```