---
title: 算法板子
image: "https://i.postimg.cc/PqxHq6yb/58898122-p0.jpg"
published: 2025-02-19
description: 记录自己常用的板子
tags: [Algorithm, Template]
category: Algorithm Templates
draft: false
---

# 线段树
```c++
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

const int MAXN = 2e5 + 10;
int arr[MAXN];

struct Tree {
	int sum;
	int left;
	int right;
	int lazy;
} tree[MAXN << 2];

// Build the segement tree by appending to the array(arr)
// build_tree(1, 1, n) -> Entry point for building the tree
// 建树
void build_tree(int i, int l, int r) {
	tree[i].left = l;
	tree[i].right = r;
	if (l == r) {
		tree[i].sum = arr[l];
		return;
	}
	int mid = (l + r) / 2;
	build_tree(i * 2, l, mid);
	build_tree(i * 2 + 1, mid + 1, r);
	tree[i].sum = tree[i * 2].sum + tree[i * 2 + 1].sum;
}

// arr[idx] = value, and update the tree
// 单点修改
void update_tree(int i, int idx, int value) {
	if (tree[i].left == tree[i].right) {
		arr[idx] = value;
		tree[i].sum = value;
		return;
	}
	int mid = (tree[i].left + tree[i].right) / 2;
	if (idx < mid) update_tree(i * 2, idx, value);
	else update_tree(i * 2 + 1, idx, value);
	tree[i].sum = tree[i * 2].sum + tree[i * 2 + 1].sum;
}

// query the sum from start(st) to end(ed)
// 区间求和
int get_sum(int i, int st, int ed) {
	if (tree[i].right<st || tree[i].left>ed) return 0;
	if (st <= tree[i].left && tree[i].right <= ed) return tree[i].sum;	//当前区间被包含
	return get_sum(i*2, st, ed) + get_sum(i*2+1, st, ed);
}

// spread the lazy tag
// 下放lazy
void spread(int i) {
	if (tree[i].lazy) {
		int mid = (tree[i].left + tree[i].right) / 2;
		tree[i * 2].sum += tree[i].lazy * (mid - tree[i].left + 1);
		tree[i * 2 + 1].sum += tree[i].lazy * (tree[i].right - mid);
		tree[i * 2].lazy += tree[i].lazy;
		tree[i * 2 + 1].lazy += tree[i].lazy;
		tree[i].lazy = 0;
	}
}

// 带lazy区间修改
void update(int i, int st, int ed, int val) {
	if (st <= tree[i].left && tree[i].right <= ed) {	//当前区间被包含
		tree[i].sum += val * (tree[i].right - tree[i].left + 1);
		tree[i].lazy += val;
		return;
	}
	spread(i);
	int mid = (tree[i].left + tree[i].right) / 2;
	if (st <= mid) update(i * 2, st, ed, val);
	if (ed >= mid + 1) update(i * 2 + 1, st, ed, val);
	tree[i].sum = tree[i * 2].sum + tree[i * 2 + 1].sum;
}

// 带lazy区间查询
int ask(int i, int st, int ed) {
	if (st <= tree[i].left && tree[i].right <= ed) return tree[i].sum;	//当前区间被包含
	spread(i);
	int mid = (tree[i].left + tree[i].right) / 2;
	long long res = 0;
	if (st <= mid) res += ask(i * 2, st, ed);
	if (ed >= mid + 1) res += ask(i * 2 + 1, st, ed);
	return res;
}

int main(void) {
	int n, m;
	cin >> n >> m;
	for (int i = 1; i <= n; ++i)
		cin >> arr[i];
	build_tree(1, 1, n);
	while(m--) {
		int op;
		cin >> op;
		if (op == 1) {
			int x, y, k;
			cin >> x >> y >> k;
			update(1, x, y, k);
		}
		else if (op == 2) {
			int x, y;
			cin >> x >> y;
			int res = ask(1, x, y);
			cout << res << endl;
		}
	}
	return 0;
}
```
<br/>

# KMP
```c++
#include <bits/stdc++.h>
using namespace std;

const int MAXN = 1e6 + 10;

int kmp_next[MAXN];
char text[MAXN];
char matching[MAXN];

void getNext(int m) {
	int j = 0;
	kmp_next[0] = 0;
	for(int i = 0; i < m; ++i) {
		while (j > 0 && matching[i] != matching[j]) j = kmp_next[j - 1];
		if (matching[i] == matching[j]) ++j;
		kmp_next[i] = j;
	}
}

int KMP(int n, int m) {
	int i, j = 0;
	int p = -1;
	getNext(m);
	for (i = 0; i < n; ++i) {
		while (j > 0 && matching[j] != text[i]) j = kmp_next[j - 1];
		if (matching[j] == text[i]) ++j;
		if (j == m) return i - m + 1;
	}
}
``` 
<br/>

# ST表
```c++
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int MAXN = 1e5 + 5;
const int LOG_N = 20;
int a[MAXN];
int n, m;

int st[MAXN][LOG_N];
int LOG_2[MAXN];
void log2_init() {
	LOG_2[0] = -1;
	for (int i = 1; i <= n; ++i) LOG_2[i] = LOG_2[i >> 1] + 1;
}
void st_init() {
	for (int i = 1; i <= n; ++i) st[i][0] = a[i];
	for (int j = 1; (1 << j) <= n; j++)
		for (int i = 1; i + (1 << j) - 1 <= n; i++)
			st[i][j] = max(st[i][j - 1], st[i + (1 << j - 1)][j - 1]);
}
ll getMax(int l, int r) {
	int len = r - l + 1;
	int k = LOG_2[len];
	return max(st[l][k], st[r - (1 << k) + 1][k]);
}
```
<br/>

# 线段树+树链剖分
```c++
#define _CRT_SECURE_NO_WARNINGS
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;
const int maxn = 200000 + 10;
int w[maxn];
int N, Q;
struct
{
    int to, next;
}e[maxn << 1];

int head[maxn], edgeNum;
void add(int u, int v)
{
    e[edgeNum].next = head[u];
    e[edgeNum].to = v;
    head[u] = edgeNum++;
}

/*-------------------------树剖------------------------------*/
int deep[maxn], fa[maxn], siz[maxn], son[maxn];
void dfs1(int u, int pre, int d)
{
    deep[u] = d;
    fa[u] = pre;
    siz[u] = 1;
    son[u] = 0;
    for (int i = head[u]; ~i; i = e[i].next)
    {
        int v = e[i].to;
        if (v != pre)
        {
            dfs1(v, u, d + 1);
            siz[u] += siz[v];
            if (siz[v] > siz[son[u]])
                son[u] = v;
        }
    }
}

int top[maxn], id[maxn], rk[maxn], cnt;
int& n = cnt;
void dfs2(int u, int t)
{
    top[u] = t;
    id[u] = ++cnt;
    rk[cnt] = u;
    if (!son[u]) return;

    dfs2(son[u], t);

    for (int i = head[u]; ~i; i = e[i].next)
    {
        int v = e[i].to;
        if (v != son[u] && v != fa[u])
            dfs2(v, v);
    }
}
/*-------------------------树剖------------------------------*/

/*-------------------------线段树------------------------------*/
#define lson l,m,rt<<1
#define rson m+1,r,rt<<1|1
int sum[maxn << 2], lazy[maxn << 2];
void pushup(int rt)
{
    sum[rt] = (sum[rt << 1] + sum[rt << 1 | 1]);
}

void build(int l, int r, int rt)
{
    if (l == r)
    {
        sum[rt] = w[rk[l]];
        return;
    }
    int m = l + r >> 1;
    build(lson);
    build(rson);
    pushup(rt);
}

void pushdown(int rt, int l, int r)
{
    if (lazy[rt])
    {
        lazy[rt << 1] = (lazy[rt << 1] + lazy[rt]);
        lazy[rt << 1 | 1] = (lazy[rt << 1 | 1] + lazy[rt]);
        sum[rt << 1] += (lazy[rt] * l);
        sum[rt << 1 | 1] += (lazy[rt] * r);
        lazy[rt] = 0;
    }
}

void update(int L, int R, int val, int l, int r, int rt)
{
    if (L <= l && r <= R)
    {
        sum[rt] = (sum[rt] + (val) * ((r - l + 1)));
        lazy[rt] += val;
        return;
    }
    int m = l + r >> 1;
    pushdown(rt, m - l + 1, r - m);
    if (L <= m)
        update(L, R, val, lson);
    if (R > m)
        update(L, R, val, rson);
    pushup(rt);
}

int query(int L, int R, int l, int r, int rt)
{
    if (L <= l && r <= R)
        return sum[rt];
    int m = l + r >> 1, ans = 0;
    pushdown(rt, m - l + 1, r - m);
    if (L <= m)
        ans = (ans + query(L, R, lson));
    if (R > m)
        ans = (ans + query(L, R, rson));
    return ans;
}
/*-------------------------线段树------------------------------*/

/*-----------------------树剖加线段树--------------------------*/
void update(int x, int y, int z)
{
    while (top[x] != top[y])
    {
        if (deep[top[x]] < deep[top[y]])
            swap(x, y);
        update(id[top[x]], id[x], z, 1, n, 1);
        x = fa[top[x]];
    }
    if (deep[x] > deep[y])
        swap(x, y);
    update(id[x], id[y], z, 1, n, 1);
}

int nnn = 1;
int query(int x, int y)
{
    nnn++;
    int ans = 0;
    while (top[x] != top[y])
    {
        if (deep[top[x]] < deep[top[y]])
            swap(x, y);
        ans = (ans + query(id[top[x]], id[x], 1, n, 1));
        x = fa[top[x]];
    }
    if (deep[x] > deep[y])
        swap(x, y);
    ans = (ans + query(id[x], id[y], 1, n, 1));
    return ans;
}
/*-----------------------树剖加线段树--------------------------*/

void init()
{
    memset(head, -1, 4 * N + 4);
    cnt = edgeNum = 0;
}

int u, v, testnum, a, b, c;
int main()
{
    scanf("%d%d%d", &N, &Q, &testnum);
    init();
    for (int i = 1; i < N; ++i)
    {
        w[i] = 0;
        scanf("%d%d", &u, &v);
        add(u, v);
        add(v, u);
    }
    dfs1(1, 0, 0);
    dfs2(1, 1);
    build(1, n, 1);
    if (testnum == 19) {
        scanf("%d%d%d", &a, &b, &c);
        query(a, b);
        printf("%d\n", nnn);
        nnn = 0;
    }
    else {
        while (Q--)
        {
            scanf("%d%d%d", &a, &b, &c);
            update(a, b, 1);
            printf("%d\n", query(c, b));
            update(a, b, -1);
        }
    }
    return 0;
}
```
<br/>

# 并查集
```c++
#include <bits/stdc++.h>
using namespace std;

struct dsu {
	vector<size_t> pa, size;
	// 初始化, 父亲为自己
	explicit dsu(size_t size_) :pa(size_ * 2), size(size_ * 2,1) {
		iota(pa.begin(), pa.begin() + size_, size_);
		iota(pa.begin() + size_, pa.end(), size_);
	}
	// 查询找根节点
	size_t find(size_t x) {
		return pa[x] == x ? x : pa[x] = find(pa[x]); 
	}
	// 启发式合并
	void unite(size_t x, size_t y) {
		x = find(x), y = find(y);
		if (x == y) return;
		if (size[x] < size[y]) swap(x, y);	// x is bigger
		pa[y] = x;
		size[x] += size[y];
	}
	// 删除节点
	void erase(size_t x) {
		--size[find(x)];
		pa[x] = x;
	}
	// 移动
	void move(size_t x, size_t y) {
		auto fx = find(x), fy = find(y);
		if (fx == fy) return;
		pa[x] = fy;
		--size[fx], ++size[fy];
	}
};

```
<br/>

# 树状数组
```c++
#include <bits/stdc++.h>
using namespace std;

int n;
int tree[2000010];

int lowbit(int k) {
	return k & -k;
}

void add(int x, int k) {
	while (x <= n) {
		tree[x] += k;
		x += lowbit(x);
	}
}

int sum(int x) {
	int ans = 0;
	while (x) {
		ans += tree[x];
		x -= lowbit(x);
	}
	return ans;
}

/*
int main(void){
	cin>>n;
	for(int i=0;i<=n;++i)
		...
}
*/
```
<br/>

