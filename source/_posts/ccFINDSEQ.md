---
layout: post
date: 2019-01-13
title: "[CodeChef FINDSEQ]Find a Subsequence 题解"
categories:
- [OI, 数据结构, BIT]
- [OI, 常用技术, 树状数组二分]
- [OI, 思维]
tags: [CodeChef, BIT, 树状数组二分]
mathjax: true
---

## Original Description
- [Click to open](https://www.codechef.com/problems/FINDSEQ)

## Summary
- 给定一个长度为$n$的数列$A_n$和由$12345$组成的排列$s$。要求从数列中选出五个数，使得其互不相同且第$i$个数在这五个数中是第$s_i$小的，即这五个数的相对大小关系和那个$s$排列是一样的。
- $n\leq 1000$，$-10^9\leq A_i\leq 10^9$

<!-- more -->

## Solution
<details>
<summary>Click to view the solution</summary>
我一开始的思路是枚举第二大和第四大的数是多少，这样剩下的三个数的范围就确定了。但是有一个问题：我们要实现这样一个询问：

> 在区间$[l,r]$内，第一个在$[L,R]$之内的数是多少？

这个问题好像没法单log做？（如果有会的哥哥可以联系我）一个比较显然的双log做法是二分+主席树，但这题撑不住双log。

我们又有一个办法：注意到第一大和第五大这两个数是只有一个边界的，这两个数的查询非常方便，只要用ST表维护最小值/最大值即可。这样我们最后再处理第三大的，问题就变成了这样：

> 在区间$[l,r]$内，是否存在一个数$x\in [L,R]$？

这个用主席树就可以做到单log了，总时间复杂度$O(Tn^2logn)$

这个做法未免太烦了，其实在刚开始的时候我掉入了思维陷阱。我们尝试另一种思考方法：我们枚举$s$排列中的第二个数和第四个数的位置，这样虽然我们没有确定每个数的值域范围，但好处是原序列被分成了三个区间，每个区间内我们只要取一个数。

接下来我们从小到大考虑最小的数，第二小的数...以此类推。显然的是，为了使后面的数的可选范围尽可能大，我们每一步都应该选尽可能小的数，但又要保证比上一个数大，于是我们要解决这样一个问题：

> 在区间$[l,r]$内，比$x$大的最小的数是多少？

只要我们有这个区间的线段树，就可以在线段树上二分以单log的解决这个问题。为了进一步减小常数，代码中使用的是树状数组二分。树状数组中维护的是一个前缀和，我们相当于要找一个最小的$x'$使得$sum(x')>sum(x)$。树状数组的本质是砍掉所有右儿子的线段树，而前缀和可以由若干个左儿子区间拼起来得到。具体细节可以参考代码。

我们实时维护三个区间的树状数组，在右端点滑动的时候，相当于第二个BIT加数字，第三个BIT减数字。这样总时间复杂度也是$O(Tn^2logn)$，但常数和代码难度都小很多。
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

const int MAXN=1000;

int n,a[MAXN+48];
char seq[48];int topos[48];
vector<int> pos[MAXN+48];int L[10];

int val[MAXN+48],tot;
int ans[10];

struct BIT
{
    int c[MAXN+48];
    inline void init() {memset(c,0,sizeof(c));}
    inline void modify(int x,int delta) {while (x<=tot) c[x]+=delta,x+=LOWBIT(x);}
    inline int query(int x) {int res=0;while (x) res+=c[x],x^=LOWBIT(x);return res;}
    inline int getpos(int x)
    {
        int cmp=query(x),res=0,tmp;
        for (register int i=10;i>=0;i--)
        {
            tmp=res+(1<<i);
            if (tmp<=tot && c[tmp]<=cmp) res+=(1<<i),cmp-=c[tmp];
        }
        if (res==tot) return -1; else return res+1;
    }
}tree[3];

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    int ca;scanf("%d",&ca);
    while (ca--)
    {
        scanf("%d%s",&n,seq+1);for (register int i=1;i<=n;i++) scanf("%d",a+i),val[i]=a[i];
        for (register int i=1;i<=5;i++) topos[seq[i]-'0']=i;
        tot=n;sort(val+1,val+tot+1);tot=unique(val+1,val+tot+1)-(val+1);
        for (register int i=1;i<=n;i++) a[i]=lower_bound(val+1,val+tot+1,a[i])-val;
        for (register int i=1;i<=tot;i++) pos[i].clear();
        for (register int i=1;i<=n;i++) pos[a[i]].pb(i);
        tree[0].init();tree[0].modify(a[1],1);bool f=false;
        for (register int i=2;i<=n-3;i++)
        {
            tree[1].init();tree[2].init();
            tree[1].modify(a[i+1],1);for (register int j=i+3;j<=n;j++) tree[2].modify(a[j],1);
            for (register int j=i+2;j<=n-1;j++)
            {
                L[0]=1;L[1]=i+1;L[2]=j+1;
                memset(ans,-1,sizeof(ans));ans[0]=0;
                ans[seq[2]-'0']=i;ans[seq[4]-'0']=j;bool ff=true;
                for (register int k=1;k<=5;k++)
                {
                    if (ans[k]!=-1)
                    {
                        if (k<5 && ans[k+1]!=-1 && a[ans[k]]>=a[ans[k+1]]) {ff=false;break;}
                        continue;
                    }
                    int p=tree[topos[k]/2].getpos(a[ans[k-1]]);
                    if (p==-1 || (k<5 && ans[k+1]!=-1 && p>=a[ans[k+1]])) {ff=false;break;}
                    int pp=lower_bound(pos[p].begin(),pos[p].end(),L[topos[k]/2])-pos[p].begin();
                    ans[k]=pos[p][pp];
                }
                if (ff) {f=true;break;}
                tree[1].modify(a[j],1);tree[2].modify(a[j+1],-1);
            }
            if (f) break;
            tree[0].modify(a[i],1);
        }
        if (!f) puts("-1"); else
        {
            sort(ans+1,ans+6);
            for (register int i=1;i<=5;i++) printf("%d ",ans[i]-1);
            puts("");
        }
    }
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
</details>