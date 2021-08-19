```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

int n, m, q, lst, h[100005], b[100005];

int siz, root[2000005];
struct Chairman_tree {
	struct node {
		int lson, rson, cnt;
	} t[8000005];

	inline void pushup (int p) {
		t[p].cnt = t[t[p].lson].cnt + t[t[p].rson].cnt;
	}

	inline int build (int p, int l, int r) {
		if (!p) p = ++siz;
		if (l == r) return p;
		int mid = l + r >> 1;
		t[p].lson = build(t[p].lson, l, mid);
		t[p].rson = build(t[p].rson, mid + 1, r);
		return p;
	}

	inline int update (int p, int l, int r, int pre, int x) {
		if (!p) p = ++siz;
		if (l == r) {
			t[p].cnt = t[pre].cnt + 1;
			return p;
		}
		int mid = l + r >> 1;
		if (x <= mid) t[p].lson = update(t[p].lson, l, mid, t[pre].lson, x), t[p].rson = t[pre].rson;
		else t[p].rson = update(t[p].rson, mid + 1, r, t[pre].rson, x), t[p].lson = t[pre].lson;
		pushup(p);
		return p;
	}

	inline int query (int p, int q, int l, int r, int rnk) {
		if (l == r) return l;
		int mid = l + r >> 1;
		if (t[t[q].lson].cnt - t[t[p].lson].cnt >= rnk) return query(t[p].lson, t[q].lson, l, mid, rnk);
		else return query(t[p].rson, t[q].rson, mid + 1, r, rnk - (t[t[q].lson].cnt - t[t[p].lson].cnt));
	}
} cmt;

struct edge {
	int u, v, w;
} e[500005];
inline bool operator < (edge x, edge y) {
	return x.w < y.w;
}

struct find_union {
	int fa[200005];

	inline void init(int n) {
		for (int i = 1; i <= n; i++) fa[i] = i;
	}

	inline int find(int u) {
		return fa[u] == u ? u : fa[u] = find(fa[u]);
	}

	inline void merge(int u, int v) {
		int fx = find(u), fy = find(v);
		if (fx != fy) fa[fx] = fy;
	}
} fu;

int cnt_nt, tim, fa[200005][20], val[200005], in[200005], out[200005], cnt_leaf[200005], mp[200005];
vector<int> G[200005];
inline void ex_kruskal() {
	fu.init(n * 2 - 1);
	sort(e + 1, e + 1 + m);
	int cnt = 0;
	for (int i = 1; i <= m && cnt < n - 1; i++) {
		int fx = fu.find(e[i].u), fy = fu.find(e[i].v);
		if (fx != fy) {
			cnt++;
			val[++cnt_nt] = e[i].w;
			fa[fx][0] = cnt_nt;
			fa[fy][0] = cnt_nt;
			G[cnt_nt].push_back(fx);
			G[cnt_nt].push_back(fy);
			fu.merge(fx, cnt_nt);
			fu.merge(fy, cnt_nt);
		}
	}
}
inline void dfs(int u) {
	if (G[u].empty()) cnt_leaf[u] = 1;
	in[u] = ++tim;
	mp[tim] = u;
	for (auto v : G[u]) dfs(v), cnt_leaf[u] += cnt_leaf[v];
	out[u] = tim;
}
inline void init() {
	for (int j = 1; j <= 18; j++)
		for (int i = 1; i < n * 2; i++) fa[i][j] = fa[fa[i][j - 1]][j - 1];
}

int main() {
	scanf("%d %d %d", &n, &m, &q);
	cnt_nt = n;
	for (int i = 1; i <= n; i++) scanf("%d", &h[i]), b[i] = h[i];
	sort(b + 1, b + 1 + n);
	for (int i = 1; i <= n; i++) h[i] = lower_bound(b + 1, b + 1 + n, h[i]) - b;
	for (int i = 1; i <= m; i++) scanf("%d %d %d", &e[i].u, &e[i].v, &e[i].w);
	ex_kruskal();
	for (int i = 1; i <= n * 2 - 1; i++)
		if (fu.find(i) == i) dfs(i);
	root[0] = cmt.build(root[0], 1, n);
	for (int i = 1; i <= n * 2 - 1; i++) {
		if (mp[i] <= n) root[i] = cmt.update(root[i], 1, n, root[i - 1], h[mp[i]]);
		else root[i] = root[i - 1];
	}
	init();
	val[0] = 2147483647;
	while (q--) {
		int u, x, k;
		scanf("%d %d %d", &u, &x, &k);
		u = (u ^ lst) % n + 1;
		x ^= lst;
		k = (k ^ lst) % n + 1;
		for (int i = 18; i >= 0; i--)
			if (val[fa[u][i]] <= x) u = fa[u][i];
		if (cnt_leaf[u] < k) lst = -1;
		else lst = b[cmt.query(root[in[u] - 1], root[out[u]], 1, n, cnt_leaf[u] - k + 1)];
		printf("%d\n", lst);
		lst = lst == -1 ? 0 : lst;
	}
	return 0;
}
```
