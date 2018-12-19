---
layout: post
date: 2018-12-19
title: "[AtCoder Grand Contest 029C]Lexicographic constraints 题解"
categories:
- [OI, 思维]
- [OI, 常用技术, 二分答案]
tags: [AtCoder, 思维]
mathjax: true
---

## Problem Statement
There are $N$ strings arranged in a row. It is known that, for any two adjacent strings, the string to the left is lexicographically smaller than the string to the right. That is, $S_1<S_2<...<S_N$ holds lexicographically, where $S_i$ is the $i$-th string from the left.

At least how many different characters are contained in $S_1,S_2,...,S_N$, if the length of $S_i$ is known to be $A_i$?

<!-- more -->

## Constraints

- $1\leq N\leq 2\times 10^5$

- $1\leq A_i\leq 10^9$

- $A_i$ is an integer.

## Note

The strings do not necessarily consist of English alphabet; there can be arbitrarily many different characters (and the lexicographic order is defined for those characters).

## Input

Input is given from Standard Input in the following format:

    N
    A1 A2 .. An

## Output

Print the minimum possible number of different characters contained in the strings.

## Sample Input 1 
    3
    3 2 1

## Sample Output 1 
    2

The number of different characters contained in $S_1,S_2,...,S_N$ would be $3$ when, for example, $S_1=$'`abc`',$S_2=$'`bb`' and $S_3=$'`c`'.

However, if we choose the strings properly, the number of different characters can be 
$2$.

## Sample Input 2 
    5
    2 3 2 1 2

## Sample Output 2 
    2

## Solutions

显然答案具有单调性，考虑二分答案。

接下来是怎么check，我们的目标是尽可能最小化每个串的字典序。每次考虑相邻的两个字符串，如果后一个比前一个长，那么只要copy前一个串并在后面添上一串最小字符即可。如果后一个比前一个短，那么就要将前一个长出来的部分先截掉copy过来，然后使得最后一个字符比上一个大一。那么如果上一个的最后字符已经是最大字符了呢？那么就使得倒数第二个字符大一，最后一个字符设为最小字符，以此类推。可以发现这个很像加法的进位。如果某一次进位的时候，我们“加爆了”，就是首位也要进位，那么说明答案不合法，要寻求更大的答案，否则就可以尝试更小的答案。

分析复杂度。如果我们暴力的维护进位的话，均摊到每次只会进位$O(1)$次，复杂度可以保证。因为数位个数可能达到1e9个而可能产生的进位数量是1e5级别，所以可以用map来维护当前有进位的那些位。加上二分答案的一个log,总时间复杂度$O(nlog^2n)$。

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

int n,a[MAXN+48];
map<int,int> cur;int maxbit;

inline bool addone(int pt,int N)
{
    while (pt<=maxbit && cur[pt]==N-1) cur[pt]=0,pt++;
    if (pt>maxbit) return false;
    cur[pt]++;return true;
}

inline bool check(int N)
{
    cur.clear();
    if (N==1)
    {
        for (register int i=2;i<=n;i++) if (a[i]<=a[i-1]) return false;
        return true;
    }
    for (register int i=2;i<=n;i++)
    {
        if (a[i]>a[i-1]) continue;
        int low=maxbit-a[i]+1;
        while (!cur.empty() && cur.begin()->x<low) cur.erase(cur.begin());
        bool sta=addone(low,N);if (!sta) return false;
    }
    return true;
}

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    scanf("%d",&n);for (register int i=1;i<=n;i++) scanf("%d",a+i),check_max(maxbit,a[i]);
    int l=1,r=n,mid,ans;
    while (l<=r)
    {
        mid=(l+r)>>1;
        if (check(mid)) ans=mid,r=mid-1; else l=mid+1;
    }
    printf("%d\n",ans);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```