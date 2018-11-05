---
layout: post
date: 2018-11-05
title: "Codeforces #19E: Fairy 题解"
categories:
- [OI, 数据结构, 并查集, 可撤销并查集]
- [OI, 常用技术, 分治]
- [OI, 图论, dfs树]
- [OI, 图论, Tarjan求边双联通分量]
- [OI, 常用技术, 树链剖分]
tags: [Codeforces, 可撤销并查集, 分治, dfs树, Tarjan求边双联通分量, 树链剖分]
mathjax: true
---

## Description
Once upon a time there lived a good fairy A. One day a fine young man B came to her and asked to predict his future. The fairy looked into her magic ball and said that soon the fine young man will meet the most beautiful princess ever and will marry her. Then she drew on a sheet of paper n points and joined some of them with segments, each of the segments starts in some point and ends in some other point. Having drawn that picture, she asked the young man to erase one of the segments from the sheet. Then she tries to colour each point red or blue so, that there is no segment having points of the same colour as its ends. If she manages to do so, the prediction will come true. B wants to meet the most beautiful princess, that's why he asks you to help him. Find all the segments that will help him to meet the princess.

<!-- more -->

## Input

The first input line contains two integer numbers: n — amount of the drawn points and m — amount of the drawn segments (1 ≤ n ≤ 1e4, 0 ≤ m ≤ 1e4). The following m lines contain the descriptions of the segments. Each description contains two different space-separated integer numbers v, u (1 ≤ v ≤ n, 1 ≤ u ≤ n) — indexes of the points, joined by this segment. No segment is met in the description twice.

## Output

In the first line output number k — amount of the segments in the answer. In the second line output k space-separated numbers — indexes of these segments in ascending order. Each index should be output only once. Segments are numbered from 1 in the input order.

## Examples

Input

4 4

1 2

1 3

2 4

3 4

Output

4

1 2 3 4 

Input

4 5

1 2

2 3

3 4

4 1

1 3

Output

1

5 

## Solution 1

简单来说就是从图中删掉一条边，使得图中没有奇环。

考虑对原图求出dfs树，则此时我们的边只有树边和返祖边两种（因为这是一个无向图）。根据返祖边连接的两个点的深度差的奇偶性可以判断所有返祖边所在的环长的奇偶性。

如果原图中没有奇环，那么任意删去一条边以后肯定也没有奇环，这种平凡的情况以下不讨论。

否则分两种情况讨论：

1. 删去一条非树边：则原图中必须只有一个奇环，这显然是必要的。
2. 删去一条树边，则该树边必须为所有奇环的公共边，这也是必要的。

接下来考虑要使得条件充分还需要什么。我们考虑到如果一个奇环和一个偶环相交的话，那么这两个环的复合环肯定是一个奇环，如果删除的是奇环和偶环的公共部分的话仍然会有奇环。

那么删除不在任意一个偶环中且在所有奇环中的一条边是否是该问题的充要条件呢？答案是肯定的。~~轻微玄学，仔细yy若干情况感觉很对~~

考虑到这里这个问题就有很多做法了，比如比较暴力的做法可以直接树链剖分，对奇环所在的树链和偶环所在的树链打标记。

但这样就完全没有利用无向图的dfs树只有返祖边的这个优良性质，考虑一个简单的差分打标记做法，对于奇环,在y+1,x-1，这样从上到下做一个前缀和就能得到每条边被多少个奇环覆盖，再判断一下有没有被偶环覆盖，做法是相当的

还有一个比较神奇的做法，我们令奇环对应的非树边是坏边，偶环对应的非树边是好边，那么要判断一条边是否被偶环覆盖，我们可以考虑扔掉坏边对剩下的图求一个边双联通分量，如果某一条边是桥的话那么它没有被偶环覆盖，否则被偶环覆盖。

## Code 1
```cpp
// 树链剖分
#include <cstdio>
#include <iostream>
#include <cstring>
#include <string>
#include <cstdlib>
#include <utility>
#include <cctype>
#include <algorithm>
#include <bitset>
#include <set>
#include <map>
#include <vector>
#include <queue>
#include <deque>
#include <stack>
#include <cmath>
#define LL long long
#define LB long double
#define x first
#define y second
#define Pair pair<int,int>
#define pb push_back
#define pf push_front
#define mp make_pair
#define LOWBIT(x) x & (-x)
using namespace std;

const int MOD=1e9+7;
const LL LINF=2e16;
const int INF=2e9;
const int magic=348;
const double eps=1e-10;
const double pi=3.14159265;

inline int getint()
{
    char ch;int res;bool f;
    while (!isdigit(ch=getchar()) && ch!='-') {}
    if (ch=='-') f=false,res=0; else f=true,res=ch-'0';
    while (isdigit(ch=getchar())) res=res*10+ch-'0';
    return f?res:-res;
}

int n,e;
Pair edge[10048];
vector<Pair> v[10048];
int ansnum;vector<int> ans;

struct Edge
{
	int x,y;
	int ind;
};vector<Edge> tedge,ntgedge,ntbedge;
int fa[10048],fa_ind[10048];
bool visited[10048];
int num[10048];
vector<int> vv[10048];int tot;

int dep[10048],depth[10048],sz[10048],son[10048],ffa[10048];
int tpos[10048],ind=0,top[10048];
int anc[10048][21];

void dfs(int cur)
{
	visited[cur]=true;
	if (!fa[cur]) num[fa_ind[cur]]=++tot;
	int i,j,to;
	for (i=0;i<int(v[cur].size());i++)
	{
		to=v[cur][i].x;
		if (!visited[to])
		{
			fa[to]=cur;fa_ind[to]=v[cur][i].y;
			tedge.pb(Edge{cur,to,v[cur][i].y});
			num[v[cur][i].y]=++tot;
			anc[to][0]=cur;dep[to]=dep[cur]+1;
			for (j=1;j<=20;j++) anc[to][j]=anc[anc[to][j-1]][j-1];
			dfs(to);
			vv[num[fa_ind[cur]]].pb(num[v[cur][i].y]);
		}
		else
		{
			if (to!=fa[cur] && dep[cur]>dep[to])
			{
				if (!((dep[cur]-dep[to])&1))
					ntbedge.pb(Edge{to,cur,v[cur][i].y});
				else
					ntgedge.pb(Edge{to,cur,v[cur][i].y});
			}
		}
	}
}

void dfs1(int cur,int father)
{
	ffa[cur]=father;sz[cur]=1;son[cur]=0;
	int i,to,max_size=-1;
	for (i=0;i<int(vv[cur].size());i++)
	{
		to=vv[cur][i];depth[to]=depth[cur]+1;
		dfs1(to,cur);
		sz[cur]+=sz[to];
		if (sz[to]>max_size)
		{
			max_size=sz[to];
			son[cur]=to;
		}
	}
}

void dfs2(int cur,int tp)
{
	top[cur]=tp;tpos[cur]=++ind;
	if (son[cur]) dfs2(son[cur],tp);
	int i;
	for (i=0;i<int(vv[cur].size());i++)
		if (vv[cur][i]!=son[cur]) dfs2(vv[cur][i],vv[cur][i]);
}

struct node
{
	int left,right;
	int cnt;
}tree_good[40048],tree_bad[40048];

void build_good(int cur,int left,int right)
{
	tree_good[cur].left=left;tree_good[cur].right=right;tree_good[cur].cnt=0;
	if (left!=right)
	{
		int mid=(left+right)>>1;
		build_good(cur<<1,left,mid);
		build_good(cur<<1|1,mid+1,right);
	}
}

void build_bad(int cur,int left,int right)
{
	tree_bad[cur].left=left;tree_bad[cur].right=right;tree_bad[cur].cnt=0;
	if (left!=right)
	{
		int mid=(left+right)>>1;
		build_bad(cur<<1,left,mid);
		build_bad(cur<<1|1,mid+1,right);
	}
}

void update_good(int cur,int left,int right)
{
	if (left<=tree_good[cur].left && tree_good[cur].right<=right)
	{
		tree_good[cur].cnt++;
		return;
	}
	int mid=(tree_good[cur].left+tree_good[cur].right)>>1;
	if (left<=mid) update_good(cur<<1,left,right);
	if (mid+1<=right) update_good(cur<<1|1,left,right);
}

void update_bad(int cur,int left,int right)
{
	if (left<=tree_bad[cur].left && tree_bad[cur].right<=right)
	{
		tree_bad[cur].cnt++;
		return;
	}
	int mid=(tree_bad[cur].left+tree_bad[cur].right)>>1;
	if (left<=mid) update_bad(cur<<1,left,right);
	if (mid+1<=right) update_bad(cur<<1|1,left,right);
}

int query_good(int cur,int pos)
{
	if (tree_good[cur].left==tree_good[cur].right) return tree_good[cur].cnt;
	int mid=(tree_good[cur].left+tree_good[cur].right)>>1;
	if (pos<=mid)
		return query_good(cur<<1,pos)+tree_good[cur].cnt;
	else
		return query_good(cur<<1|1,pos)+tree_good[cur].cnt;
}

int query_bad(int cur,int pos)
{
	if (tree_bad[cur].left==tree_bad[cur].right) return tree_bad[cur].cnt;
	int mid=(tree_bad[cur].left+tree_bad[cur].right)>>1;
	if (pos<=mid)
		return query_bad(cur<<1,pos)+tree_bad[cur].cnt;
	else
		return query_bad(cur<<1|1,pos)+tree_bad[cur].cnt;
}

void doit_good(int u,int v)
{
	int tp1=top[u],tp2=top[v];
	while (tp1!=tp2)
	{
		if (depth[tp1]<depth[tp2])
		{
			swap(u,v);
			swap(tp1,tp2);
		}
		update_good(1,tpos[tp1],tpos[u]);
		u=ffa[tp1];tp1=top[u];
	}
	if (depth[u]<depth[v]) swap(u,v);
	update_good(1,tpos[v],tpos[u]);
}

void doit_bad(int u,int v)
{
	int tp1=top[u],tp2=top[v];
	while (tp1!=tp2)
	{
		if (depth[tp1]<depth[tp2])
		{
			swap(u,v);
			swap(tp1,tp2);
		}
		update_bad(1,tpos[tp1],tpos[u]);
		u=ffa[tp1];tp1=top[u];
	}
	if (depth[u]<depth[v]) swap(u,v);
	update_bad(1,tpos[v],tpos[u]);
}

int lca(int u,int v)
{
	if (dep[u]<dep[v]) swap(u,v);
	int i;
	for (i=20;i>=0;i--)
		if (dep[anc[u][i]]>=dep[v]) u=anc[u][i];
	if (u==v) return u;
	for (i=20;i>=0;i--)
		if (anc[u][i]!=anc[v][i])
		{
			u=anc[u][i];
			v=anc[v][i];
		}
	return anc[u][0];
}

int getbelow(int u,int v)
{
	int i;
	for (i=20;i>=0;i--)
		if (dep[anc[u][i]]>dep[v]) u=anc[u][i];
	return u;
}

Pair query_node(int u,int v)
{
	int LCA=lca(u,v);
	if (LCA!=u && LCA!=v) return mp(num[fa_ind[u]],num[fa_ind[v]]);
	if (dep[u]<dep[v]) swap(u,v);
	int tmp=getbelow(u,v);
	return mp(num[fa_ind[u]],num[fa_ind[tmp]]);
}

int main ()
{
	int i,j,cur,x,y;int wei;
	n=getint();e=getint();
	for (i=1;i<=e;i++)
	{
		x=getint();y=getint();
		if (i==1) wei=x;
		edge[i]=mp(x,y);
		v[x].pb(mp(y,i));v[y].pb(mp(x,i));
	}
	memset(visited,false,sizeof(visited));
	int all_cnt=0;ansnum=0;
	for (cur=1;cur<=n;cur++)
		if (!visited[cur])
		{
			for (j=1;j<=tot;j++) vv[j].clear();
			tot=0;fa[cur]=fa_ind[cur]=0;dep[cur]=1;
			tedge.clear();ntgedge.clear();ntbedge.clear();
			dfs(cur);
			depth[cur]=1;dfs1(1,0);
			ind=0;dfs2(1,1);
			if (!int(ntbedge.size())) continue;
			all_cnt++;
			if (int(ntbedge.size())==1)
			{
				ansnum++;
				ans.pb(ntbedge[0].ind);
			}
			build_good(1,1,ind);build_bad(1,1,ind);
			for (i=0;i<int(ntgedge.size());i++)
			{
				Pair res=query_node(ntgedge[i].x,ntgedge[i].y);
				doit_good(res.x,res.y);
			}
			for (i=0;i<int(ntbedge.size());i++)
			{
				Pair res=query_node(ntbedge[i].x,ntbedge[i].y);
				doit_bad(res.x,res.y);
			}
			for (i=0;i<int(tedge.size());i++)
			{
				if (dep[tedge[i].x]>dep[tedge[i].y]) swap(tedge[i].x,tedge[i].y);
				if (query_good(1,tpos[num[fa_ind[tedge[i].y]]])==0 && query_bad(1,tpos[num[fa_ind[tedge[i].y]]])==int(ntbedge.size()))
				{
					ansnum++;
					ans.pb(tedge[i].ind);
				}
			}
		}	
	if (all_cnt==0)
	{
		printf("%d\n",e);
		for (i=1;i<=e;i++) printf("%d ",i);
		printf("\n");return 0;
	}
	if (all_cnt>1)
	{
		printf("0\n");
		return 0;
	}
	printf("%d\n",ansnum);
	sort(ans.begin(),ans.end());
	for (i=0;i<int(ans.size());i++)
		printf("%d ",ans[i]);
	printf("\n");
	return 0;
}
```

```cpp
// 差分
#include <cstdio>
#include <iostream>
#include <cstring>
#include <string>
#include <cstdlib>
#include <utility>
#include <cctype>
#include <algorithm>
#include <bitset>
#include <set>
#include <map>
#include <vector>
#include <queue>
#include <deque>
#include <stack>
#include <cmath>
#define LL long long
#define LB long double
#define x first
#define y second
#define Pair pair<int,int>
#define pb push_back
#define pf push_front
#define mp make_pair
#define LOWBIT(x) x & (-x)
using namespace std;

const int MOD=1e9+9;
const LL LINF=2e16;
const int INF=2e9;
const int magic=348;
const double eps=1e-10;
const double pi=3.14159265;

inline int getint()
{
    char ch;int res;bool f;
    while (!isdigit(ch=getchar()) && ch!='-') {}
    if (ch=='-') f=false,res=0; else f=true,res=ch-'0';
    while (isdigit(ch=getchar())) res=res*10+ch-'0';
    return f?res:-res;
}

int n,e;
Pair edge[10048];
vector<Pair> v[10048];
struct Edge
{
	int x,y;
	int ind;
};vector<Edge> tedge,ntedge;
bool istree[10048];
int L[10048],R[10048];
int a[10048],pos[10048],ind;
bool visited[10048];int depth[10048];
int sum1[10048],sum2[10048];
bool col[10048];
int node[10048],tot=0;
vector<int> ans;int ansnum;
bool ins[10048];

void dfs(int cur)
{
	node[++tot]=cur;
	visited[cur]=true;
	a[++ind]=cur;pos[cur]=ind;
	L[cur]=ind;
	int i,to;
	for (i=0;i<int(v[cur].size());i++)
	{
		to=v[cur][i].x;
		if (!visited[to])
		{
			istree[v[cur][i].y]=true;
			depth[to]=depth[cur]+1;
			col[to]=col[cur]^1;
			dfs(to);
		}
	}
	R[cur]=ind;
}

int main ()
{
	int i,j,k,x,y;
	n=getint();e=getint();
	for (i=1;i<=e;i++)
	{
		x=getint();y=getint();
		edge[i]=mp(x,y);
		v[x].pb(mp(y,i));v[y].pb(mp(x,i));
	}
	memset(visited,false,sizeof(visited));
	int all_cnt=0,cnt;
	bool hasbaned=false;
	ansnum=0;
	for (i=1;i<=n;i++)
		if (!visited[i])
		{
			cnt=0;
			depth[i]=1;col[i]=false;
			ind=0;tot=0;
			dfs(i);
			//for (i=1;i<=n;i++) cout<<visited[i]<<' ';
			//cout<<endl;
			bool f=false;
			for (j=1;j<=tot;j++)
				for (k=0;k<int(v[node[j]].size());k++)
				{
					int eind=v[node[j]][k].y;
					if (depth[edge[eind].x]>depth[edge[eind].y]) swap(edge[eind].x,edge[eind].y);
					if (!ins[eind])
					{
						ins[eind]=true;
						if (!istree[eind])
						{
							ntedge.pb(Edge{edge[eind].x,edge[eind].y,eind});
							if (col[edge[eind].x]^col[edge[eind].y])
							{
								sum1[pos[edge[eind].x]]--;
								sum1[pos[edge[eind].y]]++;
							}
							else
							{
								hasbaned=true;f=true;cnt++;
								sum2[pos[edge[eind].x]]--;
								sum2[pos[edge[eind].y]]++;
							}
						}
						else
							tedge.pb(Edge{edge[eind].x,edge[eind].y,eind});
					}
				}
			if (f) all_cnt++;
			if (!cnt) continue;
			if (cnt==1)
			{
				ansnum=1;
				for (j=0;j<int(ntedge.size());j++)
					if (!(col[ntedge[j].x]^col[ntedge[j].y]))
					{
						ans.pb(ntedge[j].ind);
						break;
					}
			}
			for (j=1;j<=tot;j++) sum1[j]+=sum1[j-1],sum2[j]+=sum2[j-1];
			for (j=0;j<int(tedge.size());j++)
				if (sum2[R[tedge[j].y]]-sum2[L[tedge[j].y]-1]==cnt && sum1[R[tedge[j].y]]-sum1[L[tedge[j].y]-1]==0)
				{
					ansnum++;
					ans.pb(tedge[j].ind);
				}
			for (i=1;i<=tot;i++) sum1[i]=sum2[i]=0;
		}
	if (all_cnt>1)
	{
		printf("0\n");
		return 0;
	}
	if (!hasbaned)
	{
		printf("%d\n",e);
		for (i=1;i<=e;i++) printf("%d ",i);
		printf("\n");
		return 0;
	}
	printf("%d\n",ansnum);
	if (ansnum)
	{
		sort(ans.begin(),ans.end());
		for (j=0;j<int(ans.size());j++)
			printf("%d ",ans[j]);
	}
	return 0;
}
```

```cpp
// 边双联通分量
#include <cstdio>
#include <iostream>
#include <cstring>
#include <string>
#include <cstdlib>
#include <utility>
#include <cctype>
#include <algorithm>
#include <bitset>
#include <set>
#include <map>
#include <vector>
#include <queue>
#include <deque>
#include <stack>
#include <cmath>
#define LL long long
#define LB long double
#define x first
#define y second
#define Pair pair<int,int>
#define pb push_back
#define pf push_front
#define mp make_pair
#define LOWBIT(x) x & (-x)
using namespace std;

const int MOD=1e9+7;
const LL LINF=2e16;
const int INF=2e9;
const int magic=348;
const double eps=1e-10;
const double pi=3.14159265;

inline int getint()
{
    char ch;int res;bool f;
    while (!isdigit(ch=getchar()) && ch!='-') {}
    if (ch=='-') f=false,res=0; else f=true,res=ch-'0';
    while (isdigit(ch=getchar())) res=res*10+ch-'0';
    return f?res:-res;
}

int n,e;
int ansnum;vector<int> ans;
vector<Pair> v[10048];
int dfn[10048],low[10048],ind;
int depth[10048],fa[10048],fa_ind[10048];
bool iseven[10048];
int anc[10048][21];
struct Edge
{
	int x,y;
	int ind;
};vector<Edge> tedge,ntedge;
Pair edge[10048];

void Tarjan(int cur)
{
	dfn[cur]=low[cur]=++ind;
	int i,j,to;
	for (i=0;i<int(v[cur].size());i++)
	{
		to=v[cur][i].x;
		if (!dfn[to])
		{
			fa[to]=cur;fa_ind[to]=v[cur][i].y;
			depth[to]=depth[cur]+1;
			tedge.pb(Edge{cur,to,v[cur][i].y});
			anc[to][0]=cur;
			for (j=1;j<=20;j++) anc[to][j]=anc[anc[to][j-1]][j-1];
			Tarjan(to);
			low[cur]=min(low[cur],low[to]);
		}
		else
		{
			if (to!=fa[cur] && depth[cur]>depth[to] && (depth[cur]-depth[to])&1)
				low[cur]=min(low[cur],dfn[to]);
			if (to!=fa[cur] && depth[cur]>depth[to] && !((depth[cur]-depth[to])&1))
				ntedge.pb(Edge{to,cur,v[cur][i].y});
		}
	}
}

int lca(int u,int v)
{
	if (depth[u]<depth[v]) swap(u,v);
	int i;
	for (i=20;i>=0;i--)
		if (depth[anc[u][i]]>=depth[v]) u=anc[u][i];
	if (u==v) return u;
	for (i=20;i>=0;i--)
		if (anc[u][i]!=anc[v][i])
		{
			u=anc[u][i];
			v=anc[v][i];
		}
	return anc[u][0];
}

int Low(int u,int v)
{
	if (depth[u]<depth[v]) swap(u,v);
	int LCA=lca(u,v);
	if (LCA!=u && LCA!=v) return -1;
	return u;
}

bool isanc(int u,int v)
{
	if (u==v) return false;
	return lca(u,v)==u;
}

int main ()
{
	int i,cur,x,y;
	n=getint();e=getint();
	for (i=1;i<=e;i++)
	{
		x=getint();y=getint();
		edge[i]=mp(x,y);
		v[x].pb(mp(y,i));v[y].pb(mp(x,i));
	}
	int all_cnt=0,superf=true;
	for (cur=1;cur<=n;cur++)
		if (!dfn[cur])
		{
			tedge.clear();ntedge.clear();
			fa[cur]=0;ind=0;depth[cur]=1;
			Tarjan(cur);
			for (i=0;i<int(tedge.size());i++)
			{
				if (depth[tedge[i].x]>depth[tedge[i].y]) swap(edge[i].x,edge[i].y);
				if (low[tedge[i].y]!=dfn[tedge[i].y]) iseven[tedge[i].ind]=true;
			}
			if (int(ntedge.size())) all_cnt++;
			if (int(ntedge.size())==1)
			{
				ansnum++;
				ans.pb(ntedge[0].ind);
			}
			int curu=-2,curv=-2;
			for (i=0;i<int(ntedge.size());i++)
			{
				if (depth[ntedge[i].x]>depth[ntedge[i].y]) swap(ntedge[i].x,ntedge[i].y);
				if (curu==-2)
				{
					curu=ntedge[i].x;
					curv=ntedge[i].y;
					continue;
				}
				curu=Low(ntedge[i].x,curu);
				if (curu==-1)
				{
					superf=false;
					break;
				}
				curv=lca(ntedge[i].y,curv);
				if (!isanc(curu,curv))
				{
					superf=false;
					break;
				}
			}
			if (!superf) break;
			for (;curv!=curu;curv=fa[curv])
				if (!iseven[fa_ind[curv]])
				{
					ansnum++;
					ans.pb(fa_ind[curv]);
				}
		}
	if (!superf)
	{
		printf("0\n");
		return 0;
	}
	if (all_cnt==0)
	{
		printf("%d\n",e);
		for (i=1;i<=e;i++) printf("%d ",i);
		printf("\n");return 0;
	}
	if (all_cnt>1)
	{
		printf("0\n");
		return 0;
	}
	printf("%d\n",ansnum);
	if (ansnum)
	{
		sort(ans.begin(),ans.end());
		for (i=0;i<int(ans.size());i++)
			printf("%d ",ans[i]);
		printf("\n");
	}
	return 0;
}
```
***
## Solution 2

如果你没有yy出那个非常好用的充要条件，我们仍然有一个非常好的基于分治和可撤销并查集的做法。

首先判断一个图是否存在奇环等价于判断一个图是否是二分图，这个有一个喜闻乐见的并查集做法：

对于一个点$i$，令编号为$i$的点表示它是白色，令编号是$n+i$的点表示它是黑色。如果图中有一条边$(u,v)$，那么$u$和$n+v$连边，$n+u$和$v$连边，表示如果一个是黑的另一个一定是白的。最后对于每一个$i$判断$i$和$n+i$是否连通，如果连通说明导出了矛盾，从而原图不是二分图。

现在对于每一条边，我们希望得到删除它之后的并查集，如果暴力的把其他剩余的边加入并查集就是$O(m^2)$的。考虑分治,$solve(l,r)$表示当前我要解决最后删除的边编号在$l$到$r$之间的情况。如果那么流程如下:

0. 如果$l==r$，说明这条边可以删除，返回
1. 令$mid=(l+r)/2$
2. 将$[mid+1,r]$之间的边加入并查集，如果仍然合法，递归$solve(l,mid)$
3. 将$[mid+1,r]$之间的边从并查集中撤销
4. 将$[l,mid]$之间的边加入并查集，如果仍然合法，递归$solve(mid+1,r)$
5. 将$[l,mid]$之间的边从并查集中撤销，返回

注意判断是否合法的时候不需要check所有的点，只需要check当前加入的这些边所相连的点就可以了。

这样总时间复杂度$O(nlogn\alpha(n))$

## Code 2
```cpp
#include <cstdio>
#include <iostream>
#include <cstring>
#include <string>
#include <cstdlib>
#include <utility>
#include <cctype>
#include <algorithm>
#include <bitset>
#include <set>
#include <map>
#include <vector>
#include <queue>
#include <deque>
#include <stack>
#include <cmath>
#define LL long long
#define LB long double
#define x first
#define y second
#define Pair pair<int,int>
#define pb push_back
#define pf push_front
#define mp make_pair
#define LOWBIT(x) x & (-x)
using namespace std;

const int MOD=1e9+7;
const LL LINF=2e16;
const int INF=2e9;
const int magic=348;
const double eps=1e-10;
const double pi=3.14159265;

inline int getint()
{
    char ch;int res;bool f;
    while (!isdigit(ch=getchar()) && ch!='-') {}
    if (ch=='-') f=false,res=0; else f=true,res=ch-'0';
    while (isdigit(ch=getchar())) res=res*10+ch-'0';
    return f?res:-res;
}

struct Ope
{
	int x,y;
	bool type;
}ope[100048];
int tot=0;

int pre[20048],rnk[20048];
int find_anc(int x)
{
	while (pre[x]!=x) x=pre[x];
	return x;
}
void update(int x,int y)
{
	x=find_anc(x);y=find_anc(y);
	if (x==y)
	{
		ope[++tot]=Ope{x,y,false};
		return;
	}
	if (rnk[x]>=rnk[y])
	{
		pre[y]=x;
		ope[++tot]=Ope{x,y,false};
		if (rnk[x]==rnk[y]) rnk[x]++,ope[tot].type=true;
	}
	else
	{
		ope[++tot]=Ope{y,x,false};
		pre[x]=y;
	}
}
void undo()
{
	int x=ope[tot].x,y=ope[tot].y;
	pre[y]=y;
	if (ope[tot].type) rnk[x]--;
	tot--;
}

int n,e;
Pair edge[10048];
int ansnum;
vector<int> ans;
bool Conflict=false;

void Add(int x,int y)
{
	//if (find_anc(x)==find_anc(y+n)) Conflict=true;
	update(x,y+n);
	//if (find_anc(x+n)==find_anc(y)) Conflict=true;
	update(x+n,y);
	if (find_anc(x)==find_anc(x+n) || find_anc(y)==find_anc(y+n)) Conflict=true;
}

void solve(int left,int right)
{
	if (left==right)
	{
		ansnum++;
		ans.pb(left);
		return;
	}
	int i,mid=(left+right)>>1;
	Conflict=false;
	for (i=left;i<=mid;i++)
		Add(edge[i].x,edge[i].y);
	if (!Conflict) solve(mid+1,right);
	for (i=left;i<=mid;i++) undo(),undo();
	Conflict=false;
	for (i=mid+1;i<=right;i++)
		Add(edge[i].x,edge[i].y);
	if (!Conflict) solve(left,mid);
	for (i=mid+1;i<=right;i++) undo(),undo();
	Conflict=false;
}

int main ()
{
	int i;
	n=getint();e=getint();
	if (e==0)
	{
		printf("0\n");
		return 0;
	}
	for (i=1;i<=e;i++) edge[i].x=getint(),edge[i].y=getint();
	for (i=1;i<=n*2;i++) pre[i]=i,rnk[i]=1;
	solve(1,e);
	printf("%d\n",ansnum);
	if (ansnum)
	{
		sort(ans.begin(),ans.end());
		for (i=0;i<int(ans.size());i++)
			printf("%d ",ans[i]);
	}
	return 0;
}
```