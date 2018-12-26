---
layout: post
date: 2018-12-26
title: "[NFLSoj #346][Jiangsu Training Contest #8]01串 题解"
categories:
- [OI, 数学, 莫比乌斯函数]
tags: [NFLSoj, 数学, 莫比乌斯函数]
mathjax: true
---

## Description

给定 $S$ 和 $T$，它们都是仅包含字母 $A$ 和 $B$ 的字符串。

$A$ 和 $B$ 是两个仅包含数字 $0$ 和 $1$ 的非空字符串。现在，将 $S$ 和 $T$ 中的字母 $A$ 都替换成字符串 $A$，字母 $B$ 都替换成字符串 $B$，如果替换完成后，$S$ 和 $T$ 相同，那么就称这是一个完美的替换。问有多少组字符串是完美的替换，且满足 $A$ 和 $B$ 的长度都小于等于 $n$。两组字符串 $(A,B)$ 和 $(A',B')$ 不同当且仅当 $A\neq A'$ 或 $B\neq B'$。$S$ 和 $T$ 对应的完美替换的数量记作 $F(S,T)$。

例如当 $S=AAB$，$T=BB$ 时，$A=01$，$B=0101$ 时就是一个完美的替换，因为替换完成后 $S=T=010101$。

输入 $S'$,$T'$，$S'$ 和 $T'$ 中仅包含字母 $A$，$B$ 和 $?$。将每个问号都替换成 $A$ 或 $B$ 后你会得到 $2^t$ 个 $S$ 和 $T$ 的组合（其中 $t$ 是问号的数量），问这些 $S$ 和 $T$ 的组合的 $F$ 的和。输出答案模 $10^9+7$。

<!-- more -->

## Input Format

第一行一个字符串 $S'$。

第二行一个字符串 $T'$。

第三行一个正整数 $n$。

## Output Format

输出一行答案。

## Sample Input 1
    A
    B
    10

## Sample Output 1
    2046

## Sample Input 2
    A?
    ?
    3

## Sample Output 2
    2

## Constraints
- 对于 $100\%$ 的数据，$1\leq n,\mid S'\mid ,\mid T'\mid \leq 3\times 10^5$。

## Solution

~~伪装成字符串题的数学题（大雾~~

如果$S=T$,那么$A$,$B$取任意字符串都可行，简单计算即可。当$S$和$T$中有问号的时候，统计使得$S=T$的方案，这部分比较简单。

当$S\neq T$时，有一个结论，$A+B=B+A$，即$A$和$B$都有长度为$gcd(A,B)$的循环节，~~证明先鸽着~~

既然有$A+B=B+A$,我们就可以把$S$和$T$都变成前面全是$A$，后面全是$B$的形式，这样答案就只和$S$和$T$中$A$,$B$的个数有关了。

设最后$S$中$A$的个数为$a$,$B$的个数为$b$,$T$中$A$的个数为$c$,$B$的个数为$d$,有以下情况：
- $(a-c)(b-d)\geq 0$且$a=c,b=d$不同时成立，此时必然无解，方案数为$0$。
- $(a-c)(b-d)<0$，此时要使得$S$和$T$的长度相等，必然有$(a-c)\mid A\mid = (b-d)\mid B\mid$，如果$gcd(a-c,b-d)\neq 1$就除一下。考虑之前的结论，我们可以设$A=(b-d)X$，$B=(a-c)X$，其中字符串$X$可以任取。注意到$A$和$B$长度不超过$n$的限制，我们的答案应该是
$$\sum_{i=1}^{\frac{n}{max\{a-c,b-d\}}}2^i=2^{(\frac{n}{max\{a-c,b-d\}}+1)}-2$$
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 这个式子可以$O(1)$计算。
- $a=c$且$b=d$，此时我们只要满足$A+B=B+A$即可，只要考虑长度$n$的限制。根据之前的结论，只要保证长度为$gcd(\mid A\mid ,\mid B\mid)$的循环节即可，循环节可以任选。我们的答案应该是
$$\sum_{i=1}^n\sum_{j=1}^n2^{gcd(i,j)}$$

&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 这是一个比较套路的式子，枚举gcd，我们有
$$\sum_{g=1}^n2^g\sum_{i=1}^{\left \lfloor \frac{n}{g} \right \rfloor}\sum_{j=1}^{\left \lfloor \frac{n}{g} \right \rfloor}[gcd(i,j)=1]$$

&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 根据恒等式
$$\sum_{d\mid n}\mu(d)=[d=1]$$

&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 可以将原式改写为
$$\sum_{g=1}^n2^g\sum_{d=1}^n\mu(d)\sum_{i=1}^{\left \lfloor \frac{n}{gd} \right \rfloor}\sum_{j=1}^{\left \lfloor \frac{n}{gd} \right \rfloor}1=\sum_{g=1}^n2^g\sum_{d=1}^n\mu(d) \left \lfloor \frac{n}{gd} \right \rfloor^2$$
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 根据调和级数，算这个式子的复杂度是$O(nlogn)$

最后我们只要处理问号的问题就可以了。如果我们暴力枚举$S$中有多少个问号变成$A$，$T$中有多少个问号变成$A$，再来计算两者$A$的差值，可以得到一个$O(\mid S\mid \mid T\mid)$的算法。

但我们注意到我们至始至终只关注$S$和$T$中$A$的个数的差值，所以我们只要枚举$S$中变成$A$的问号个数比$T$中变成$A$的问号个数多多少，然后计算即可。

至于乘多少系数，只要用一点点组合知识。假设$S$中有$s$个问号，$T$中有$t$个问号，则$S$中变成$A$的问号个数比$T$中变成$A$的问号个数多$x$个的方案数应该是
$$\sum_{i=1}^{min\{s-x,t\}}\binom{s}{i+x}\binom{t}{i}$$
根据$\binom{n}{m}=\binom{n}{n-m}$，可以将原式化为
$$\sum_{i=1}^{min\{s-x,t\}}\binom{s}{s-x-i}\binom{t}{i}$$

这个可以看做要在$s+t$个物品中选出$s-x$个物品，我们枚举这$s-x$个物品中有多少个来自$t$，就有上面的式子，所以答案就是$\binom{s+t}{s-x}$。

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

const int MAXN=1e6;

char S[MAXN+48],T[MAXN+48];
int len1,len2,n,s,t,cnta,cntb,cntc,cntd;

int fac[MAXN+48],ifac[MAXN+48],pw[MAXN+48];
int mu[MAXN+48],prime[MAXN+48],tot;bool isprime[MAXN+48];
inline void init()
{
    fac[0]=1;for (register int i=1;i<=MAXN;i++) fac[i]=1ll*fac[i-1]*i%MOD;
    ifac[MAXN]=quick_pow(fac[MAXN],MOD-2);
    for (register int i=MAXN-1;i>=0;i--) ifac[i]=1ll*ifac[i+1]*(i+1)%MOD;
    pw[0]=1;for (register int i=1;i<=MAXN;i++) pw[i]=add(pw[i-1]+pw[i-1]);
    memset(isprime,true,sizeof(isprime));tot=0;mu[1]=1;
    for (register int i=2;i<=MAXN;i++)
    {
        if (isprime[i]) prime[++tot]=i,mu[i]=-1;
        for (register int j=1;j<=tot && 1ll*i*prime[j]<=MAXN;j++)
        {
            isprime[i*prime[j]]=false;
            if (i%prime[j]==0) {mu[i*prime[j]]=0;break;} else mu[i*prime[j]]=-mu[i];
        }
    }
    for (register int i=1;i<=MAXN;i++) if (mu[i]<0) mu[i]+=MOD;
}

inline int C(int x,int y)
{
    if (x<y || x<0) return 0;
    return 1ll*fac[x]*ifac[y]%MOD*ifac[x-y]%MOD;
}

int ans=0,valueg=0;
inline void doit(int coef)
{
    valueg=0;
    for (register int g=1;g<=n;g++)
    {
        int curans=0;
        for (register int d=1;d*g<=n;d++) Add(curans,1ll*(n/g/d)*(n/g/d)%MOD*mu[d]%MOD);
        Add(valueg,1ll*curans*pw[g]%MOD);
    }
    Add(ans,1ll*valueg*coef%MOD);
}

inline void doit2()
{
    int coef=1;
    for (register int i=1;i<=len1;i++)
    {
        if (S[i]!='?' && T[i]!='?' && S[i]!=T[i]) return;
        if (S[i]=='?' && T[i]=='?') coef=1ll*coef*2%MOD;
    }
    int sum=0;
    for (register int i=1;i<=n;i++) Add(sum,pw[i]);
    sum=1ll*sum*sum%MOD;
    Add(ans,1ll*coef*sum%MOD);
    Sub(ans,1ll*coef*valueg);
}

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    scanf("%s%s%d",S+1,T+1,&n);len1=strlen(S+1);len2=strlen(T+1);init();
    s=cnta=cntb=0;for (register int i=1;i<=len1;i++) s+=(S[i]=='?'),cnta+=(S[i]=='A'),cntb+=(S[i]=='B');
    t=cntc=cntd=0;for (register int i=1;i<=len2;i++) t+=(T[i]=='?'),cntc+=(T[i]=='A'),cntd+=(T[i]=='B');
    for (register int i=-t;i<=s;i++)
    {
        int delta1=cnta-cntc+i,delta2=cntb-cntd+(s-i-t);
        if (delta1==0 && delta2==0) {doit(C(s+t,s-i));continue;}
        if (1ll*delta1*delta2>=0) continue;
        int coef=C(s+t,s-i);
        delta1=(delta1<0?-delta1:delta1);delta2=(delta2<0?-delta2:delta2);
        int g=gcd(delta1,delta2);delta1/=g;delta2/=g;
        Add(ans,1ll*coef*(pw[n/max(delta1,delta2)+1]-2)%MOD);
    }
    if (cnta+cntb+s==cntc+cntd+t) doit2();
    printf("%d\n",ans);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```