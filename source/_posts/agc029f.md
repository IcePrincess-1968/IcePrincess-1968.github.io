---
layout: post
date: 2018-12-19
title: "[AtCoder Grand Contest 029F]Construction of a tree 题解"
categories:
- [OI, 思维]
- [OI, 网络流, 最大流]
tags: [AtCoder, 最大流, 思维]
mathjax: true
---

## Problem Statement
You are given $N−1$ subsets of $\{1,2,3,...,N\}$. Let the $i$-th set be $E_i$.

Let us choose two distinct elements $u_i$ and $v_i$ from each set $E_i$, and consider a graph $T$ with $N$ vertices and $N−1$ edges, whose vertex set is $\{1,2,3,...,N\}$ and whose edge set is $(u_1,v_1),(u_2,v_2),...,(u_{N-1},v_{N-1})$. Determine if $T$ can be a tree by properly deciding $u_i$ and $v_i$. If it can,additionally find one instance of $(u_1,v_1),(u_2,v_2),...,(u_{N-1},v_{N-1})$ such that $T$ is actually a tree.

<!-- more -->

## Constraints

- $2\leq N\leq 10^5$
- $E_i$ is a subset of $\{1,2,3,...,N\}$.
- $\mid E_i\mid \geq 2$
- The sum of $\mid E_i\mid$ is at most $2\times 10^5$.

## Input

Input is given from Standard Input in the following format:
    
    N
    c1 w(1,1) w(1,2) ... w(1,c1)
    .
    .
    cn-1 w(n-1,1) w(n-1,2) ... w(n-1,cn-1)

Here, $c_i$ stands for the number of elements in $E_i$, and $w_{i,1}...w_{i,c_i}$ are the $c_i$ elements in $c_i$. Here, $2\leq c_i\leq N,1\leq w_{i,j}\leq N$, and $w_{i,j}\neq w_{i,k}(1\leq j<k\leq c_i)$ hold. 

## Output
If $T$ cannot be a tree, print -1; otherwise, print the choices of $(u_i,v_i)$ that satisfy the condition, in the following format:

    u1 v1
    u2 v2
    .
    .
    un-1 vn-1

## Sample Input 1 
    5
    2 1 2
    3 1 2 3
    3 3 4 5
    2 4 5

## Sample Output 1 
    1 2
    1 3
    3 4
    4 5

## Sample Input 2 
    6
    3 1 2 3
    3 2 3 4
    3 1 3 4
    3 1 2 4
    3 4 5 6

## Sample Output 2 
    -1

## Sample Input 3 
    10
    5 1 2 3 4 5
    5 2 3 4 5 6
    5 3 4 5 6 7
    5 4 5 6 7 8
    5 5 6 7 8 9
    5 6 7 8 9 10
    5 7 8 9 10 1
    5 8 9 10 1 2
    5 9 10 1 2 3

## Sample Output 3 
    1 2
    2 3
    3 4
    4 5
    5 6
    6 7
    7 8
    8 9
    9 10

## Solution

膜了wxh的博客，并没有怎么搞懂，准备以后再填坑。

考虑到每条边都连接着一个父亲和儿子，而除了根节点每个节点都有且仅有一个父亲，我们不妨令所有的$u_i$为儿子，$v_i$为父亲，那么$u_i$一定是一个2~n的排列。我们可以先用最大流来判断一下是否能分配出一个排列，只要像二分图匹配那样建图即可。

如果能给出一个排列，考虑怎么构造答案。考虑如下贪心：刚开始我们已经确定了1，然后我们把所有包含1的集合加入一个队列，给这些点都认领$1$作为父亲。之后每次从队列中取出一个集合编号$i$，就把包含$u_i$的集合加入队列中。

这个贪心显然是对的。但wxh说初始的$u_i$的选择不会影响结果，如果有解就能构造出来，如果无解选择任意一组$u_i$均无解，这个就不懂了（菜的真实

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

int n;
vector<int> a[MAXN+48];
vector<int> v[MAXN+48];
int u[MAXN+48];

bool inq[MAXN+48];
int head,tail,q[MAXN+48];
int ans[MAXN+48];

namespace flow
{
    int head[800048],cur[800048],to[800048],nxt[800048],f[800048],tot,t;
    inline void init() {memset(head,0,sizeof(head));tot=1;}
    inline void addedge(int s,int t,int cap)
    {
        to[++tot]=t;nxt[tot]=head[s];head[s]=tot;f[tot]=cap;
        to[++tot]=s;nxt[tot]=head[t];head[t]=tot;f[tot]=0;
    }
    int q[800048],Head,Tail;int depth[800048];
    inline bool bfs()
    {
        for (register int i=0;i<=t;i++) depth[i]=-1;
        depth[0]=0;q[1]=0;Head=Tail=1;
        while (Head<=Tail)
        {
            int x=q[Head++];
            for (register int i=head[x];i;i=nxt[i])
            {
                int y=to[i];
                if (depth[y]==-1 && f[i])
                {
                    depth[y]=depth[x]+1;
                    q[++Tail]=y;
                }
            }
        }
        if (depth[t]==-1) return false; else return true;
    }
    inline int dfs(int x,int maxf)
    {
        if (x==t || !maxf) return maxf;
        int y,minf,now,ans=0;
        for (register int &i=cur[x];i;i=nxt[i])
        {
            y=to[i];
            if (depth[y]==depth[x]+1 && f[i])
            {
                minf=min(maxf-ans,f[i]);
                now=dfs(y,minf);
                f[i]-=now;f[i^1]+=now;ans+=now;
            }
            if (ans>=maxf) return ans;
        }
        if (!ans) depth[x]=0;
        return ans;
    }
    inline int dinic()
    {
        int ans=0;
        while (bfs())
        {
            for (register int i=0;i<=t;i++) cur[i]=head[i];
            ans+=dfs(0,INF);
        }
        return ans;
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
    scanf("%d",&n);int num,x;
    for (register int i=1;i<=n-1;i++)
    {
        scanf("%d",&num);
        while (num--)
        {
            scanf("%d",&x);
            a[i].pb(x);v[x].pb(i);
        }
    }
    flow::init();flow::t=n+n+1;
    for (register int i=1;i<=n-1;i++) flow::addedge(0,i,1);
    for (register int i=2;i<=n;i++) flow::addedge(n+i,flow::t,1);
    for (register int i=1;i<=n-1;i++)
        for (auto to : a[i]) if (to!=1) flow::addedge(i,n+to,1);
    int res=flow::dinic();
    if (res!=n-1) {puts("-1");return 0;}
    for (register int i=1;i<=n-1;i++)
        for (register int j=flow::head[i];j;j=flow::nxt[j])
        {
            int y=flow::to[j];
            if (n+2<=y && y<=n+n && flow::f[j]==0) {u[i]=y-n;break;}
        }
    memset(inq,false,sizeof(inq));head=1;tail=0;
    for (auto item : v[1]) q[++tail]=item,q[++tail]=1,inq[item]=true;
    while (head<=tail)
    {
        int cur=q[head++],father=q[head++];
        ans[cur]=father;
        for (auto item : v[u[cur]]) if (!inq[item]) inq[item]=true,q[++tail]=item,q[++tail]=u[cur];
    }
    for (register int i=1;i<=n-1;i++) if (!ans[i]) {puts("-1");return 0;}
    for (register int i=1;i<=n-1;i++) printf("%d %d\n",u[i],ans[i]);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```