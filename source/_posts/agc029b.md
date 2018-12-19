---
layout: post
date: 2018-12-19
title: "[AtCoder Grand Contest 029B]Powers of Two 题解"
categories:
- [OI, 贪心]
tags: [AtCoder, 贪心]
mathjax: true
---

## Problem Statement

Takahashi has $N$ balls with positive integers written on them. The integer written on the $i$-th ball is $A_i$. He would like to form some number of pairs such that the sum of the integers written on each pair of balls is a power of $2$. Note that a ball cannot belong to multiple pairs. Find the maximum possible number of pairs that can be formed.

Here, a positive integer is said to be a power of $2$ when it can be written as $2^t$ using some non-negative integer $t$.

<!-- more -->

## Constraints

- $1\leq N\leq 2\times 10^5$

- $1\leq A_i\leq 10^9$

- $A_i$ is an integer.

## Input

Input is given from Standard Input in the following format:

    N
    A1 A2 ... An

## Output

Print the maximum possible number of pairs such that the sum of the integers written on each pair of balls is a power of $2$.

## Sample Input 1 
    3
    1 2 3

## Sample Output 1 
    1

We can form one pair whose sum of the written numbers is $4$ by pairing the first and third balls. Note that we cannot pair the second ball with itself.

## Sample Input 2 
    5
    3 11 14 5 13

## Sample Output 2 
    2

## Solution

个人认为这题甚至比当场的E还难（大雾

看上去很像一道贪心题，但不知道该怎么做。

~~通过奇怪的尝试，发现从大到小给每个数匹配就是对的~~

还是来简单证明一下正确性，尝试考虑这个2的次幂到底提供了什么性质。我们发现，**能与某个数值匹配的所有数值中，只有一个比它小**，所以，我们如果对于所有能匹配的数对，从大的向小的连边，那么会形成一个树的结构。原题即要求我们将这棵树划分成尽量多的不相交的二元组。对于这个问题，还是能比较显然的看出肯定是从叶子开始贪心比较优，也就是把所有的数按从大到小的顺序来匹配。

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

multiset<int> s;

LL pw[48];int a[MAXN+48];

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    int n,x;scanf("%d",&n);
    for (register int i=1;i<=n;i++) scanf("%d",&x),s.insert(x);int cnt=1;
    for (multiset<int>::iterator iter=s.begin();iter!=s.end();iter++,cnt++) a[cnt]=(*iter);
    pw[0]=1;for (register int i=1;i<=41;i++) pw[i]=pw[i-1]+pw[i-1];
    int ans=0;
    for (register int i=n;i>=1;i--)
    {
        if (s.find(a[i])==s.end()) continue;
        multiset<int>::iterator iter=s.find(a[i]);s.erase(iter);
        int pt=31;while (a[i]+a[i]<pw[pt] && pt) pt--;
        for (register int j=pt;j>=1;j--)
        {
            int need=pw[j]-a[i];
            if (s.find(need)!=s.end())
            {
                iter=s.find(need);s.erase(iter);
                ++ans;break;
            }
        }
    }
    printf("%d\n",ans);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```