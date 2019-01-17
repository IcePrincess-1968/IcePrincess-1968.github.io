---
layout: post
date: 2019-01-15
title: "[TopCoder SRM570DIV1C]CurvyonRails 题解"
categories:
- [OI, 网络流, 费用流]
tags: [TopCoder, 网络流, 费用流]
mathjax: true
---

## Original Description
- [Click to open](https://community.topcoder.com/stat?c=problem_statement&pm=12432)

## Summary
- 有一幅$n\times m$的地图。有一些格子是废弃的，这些格子上不能建铁轨。其他的格子都要建铁轨。铁轨有两种：第一种是直的，即连接上下或左右两个格子；第二种是弯的，即连接上下左右中相邻的两个格子。要求铺完铁轨后所有的铁轨形成若干个环，即不能有地方是断的。有些能建铁轨的格子上有地鼠，如果地鼠所在的格子建的是一条直的铁轨它就会不高兴。求最少让几只地鼠不高兴。如果没有满足题意的建铁轨方式输出$-1$。
- $n,m\leq 25$
<!-- more -->

## Solution
<details>
<summary>Click to view the solution</summary>
~~这道题和清华集训的无限之环有点像~~

要在铁轨满足要求的基础上最小化不高兴的地鼠数量，考虑费用流。我们将地图黑白染色之后，让黑格子出发，白格子接收，这样只要判断一下最大流即可知道是否可行。关键是如何最小化不高兴的地鼠数量。

这题设计费用的方式还是非常神的。我们这里考虑一个从源点出发的黑格子，白格子的做法以此类推。首先从汇点向黑格子连流量为$2$费用为$0$的边。然后从这个黑格子向两个点连边，这两个点分别管理上下的两个接口和左右的两个接口。

怎么连边呢？接下来是最精彩的一步：**黑格子向两个点各连两条边，一条流量为$1$费用为$0$，一条流量为$1$费用为$1$。**（当然这是针对有地鼠的情况，没有地鼠的话费用都是0）这样的目的是：如果这个格子最终建弯的铁轨，那么它会向两个点各流$1$的流量，那么跑最小费用流一定能流两条费用为$0$的边，从而对应没有怒气值。否则如果建直的铁轨，则两单位流量会同时到第一个点或者第二个点，那么就必然要走一条费用是$1$的边，从而对应产生怒气值。

接下来就比较简单了，我们假设每两个格子之间有一个虚点，刚才建出的两个点往对应的虚点上连流量为$1$，费用为$0$的点即可。
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

namespace flow
{
    const int flow_maxn=1e5;
    int head[flow_maxn+48],cur[flow_maxn+48],to[flow_maxn+48],nxt[flow_maxn+48],f[flow_maxn+48],cost[flow_maxn+48],tot,t,mincost,maxflow;
    bool visited[flow_maxn+48];int D[flow_maxn+48];
    inline void init() {tot=1;memset(head,0,sizeof(head));memset(D,0,sizeof(D));mincost=maxflow;}
    inline void addedge(int s,int t,int cap,int cc)
    {
        to[++tot]=t;nxt[tot]=head[s];head[s]=tot;f[tot]=cap;cost[tot]=cc;
        to[++tot]=s;nxt[tot]=head[t];head[t]=tot;f[tot]=0;cost[tot]=-cc;
    }
    inline int aug(int x,int maxf)
    {
        if (x==t) {mincost+=(-D[0])*maxf;maxflow+=maxf;return maxf;}
        visited[x]=true;
        int y,minf,now,ans=0;
        for (register int &i=cur[x];i;i=nxt[i])
        {
            y=to[i];
            if (f[i] && !visited[y] && D[x]+cost[i]-D[y]==0)
            {
                minf=min(maxf-ans,f[i]);
                now=aug(y,minf);
                f[i]-=now;f[i^1]+=now;ans+=now;
            }
            if (ans>=maxf) return ans;
        }
        return ans;
    }
    inline bool relabel()
    {
        int d=INF;
        for (register int cur=0;cur<=t;cur++)
            if (visited[cur])
                for (register int i=head[cur];i;i=nxt[i])
                {
                    int y=to[i];
                    if (!visited[y] && f[i]) check_min(d,D[cur]+cost[i]-D[y]);
                }
        if (d>=INF) return false;
        for (register int cur=0;cur<=t;cur++) if (visited[cur]) D[cur]-=d;
        return true;
    }
    inline Pair zkw()
    {
        mincost=0;maxflow=0;
        do
        {
            for (register int i=0;i<=t;i++) cur[i]=head[i];
            do
                for (register int i=0;i<=t;i++) visited[i]=false;
            while (aug(0,INF));
        }
        while (relabel());
        return mp(maxflow,mincost);
    }
}

class CurvyonRails
{
    int n,m;
    inline int getind(int x,int y) {return (x-1)*m+y;}
    inline int getrow(int x,int y) {return n*m*3+(x-1)*m+y;}
    inline int getcol(int x,int y) {return n*m*4+(x-1)*m+y;}
    public:
        inline int getmin(vector<string> field)
        {
            n=int(field.size());m=int(field[0].size());
            flow::init();flow::t=n*m*5+1;int cnt1=0,cnt2=0;
            for (register int i=1;i<=n;i++)
                for (register int j=1;j<=m;j++)
                    if (field[i-1][j-1]!='w')
                        if ((i+j)&1)
                        {
                            cnt1++;
                            flow::addedge(0,getind(i,j),2,0);
                            int tc=(field[i-1][j-1]=='C'?1:0);
                            flow::addedge(getind(i,j),n*m+getind(i,j),1,0);
                            flow::addedge(getind(i,j),n*m+getind(i,j),1,tc);
                            flow::addedge(getind(i,j),n*m*2+getind(i,j),1,0);
                            flow::addedge(getind(i,j),n*m*2+getind(i,j),1,tc);
                            if (j!=1) flow::addedge(n*m+getind(i,j),getrow(i,j-1),1,0);
                            if (j!=m) flow::addedge(n*m+getind(i,j),getrow(i,j),1,0);
                            if (i!=1) flow::addedge(n*m*2+getind(i,j),getcol(i-1,j),1,0);
                            if (i!=n) flow::addedge(n*m*2+getind(i,j),getcol(i,j),1,0);
                        }
                        else
                        {
                            cnt2++;
                            flow::addedge(getind(i,j),flow::t,2,0);
                            int tc=(field[i-1][j-1]=='C'?1:0);
                            flow::addedge(n*m+getind(i,j),getind(i,j),1,0);
                            flow::addedge(n*m+getind(i,j),getind(i,j),1,tc);
                            flow::addedge(n*m*2+getind(i,j),getind(i,j),1,0);
                            flow::addedge(n*m*2+getind(i,j),getind(i,j),1,tc);
                            if (j!=1) flow::addedge(getrow(i,j-1),n*m+getind(i,j),1,0);
                            if (j!=m) flow::addedge(getrow(i,j),n*m+getind(i,j),1,0);
                            if (i!=1) flow::addedge(getcol(i-1,j),n*m*2+getind(i,j),1,0);
                            if (i!=n) flow::addedge(getcol(i,j),n*m*2+getind(i,j),1,0);
                        }
            if (cnt1!=cnt2) return -1;
            Pair res=flow::zkw();
            if (res.x!=cnt1*2) return -1;
            return res.y;
        }
};

#ifdef LOCAL
int main ()
{
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
    CurvyonRails A;vector<string> ss;string s;int n;
    while (cin>>n)
    {
        ss.clear();
        for (register int i=1;i<=n;i++) cin>>s,ss.pb(s);
        cout<<A.getmin(ss)<<endl;
    }
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
#endif
```
</details>