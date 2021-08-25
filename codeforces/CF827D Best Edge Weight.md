```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#define lson p << 1
#define rson p << 1 | 1
using namespace std;
typedef long long ll;
inline ll Min(ll x, ll y) {return x < y ? x : y;}
inline ll Max(ll x, ll y) {return x > y ? x : y;}
inline void Swap(int &x, int &y) {x ^= y ^= x ^= y;}

int n, m;
ll ans[200005];
bool is_tree_edge[200005];

struct edge {
	int u, v, id;
	ll w;
} e[200005];
inline bool cmp1(edge x, edge y) {
	return x.w < y.w;
}
inline bool cmp2(edge x, edge y) {
	return x.id < y.id;
}

struct find_union {
	int fa[200005];
	
	inline void init(int n) {
		for (int i = 1; i <= n; i++) fa[i] = i;
	}
	
	inline int find(int u) {return fa[u] == u ? u : fa[u] = find(fa[u]);}
	
	inline void merge(int u, int v) {
		int fx = find(u), fy = find(v);
		if (fx != fy) fa[fx] = fy;
	}
} fu;

struct Graph {
	int cnt, elst[200005];
	
	struct edge {
		int to, nxt;
		ll len;
	} e[400005];
	
	inline void add(int u, int v, ll w) {
		e[++cnt].to = v;
		e[cnt].len = w;
		e[cnt].nxt = elst[u];
		elst[u] = cnt;
	}
} G;

inline void kruskal() {
	fu.init(n);
	sort(e + 1, e + 1 + m, cmp1);
	int cnt = 0;
	for (int i = 1; i <= m && cnt < n - 1; i++) {
		int fx = fu.find(e[i].u), fy = fu.find(e[i].v);
		if (fx != fy) {
			cnt++;
			fu.merge(e[i].u, e[i].v);
			G.add(e[i].u, e[i].v, e[i].w);
			G.add(e[i].v, e[i].u, e[i].w);
			is_tree_edge[e[i].id] = true;
		}
	}
}

struct HLD {
	int tim, fa[200005][20], dep[200005], siz[200005], hson[200005], nid[200005], tp[200005];
	ll d[200005][20];
	
	inline void dfs(int u) {
		dep[u] = dep[fa[u][0]] + 1;
		siz[u] = 1;
		int mx = 0;
		for (int i = G.elst[u]; i; i = G.e[i].nxt) {
			int v = G.e[i].to;
			if (fa[u][0] == v) continue;
			fa[v][0] = u;
			d[v][0] = G.e[i].len;
			dfs(v);
			siz[u] += siz[v];
			if (siz[v] > mx) mx = siz[v], hson[u] = v;
		}
	}
	
	inline void dfs2(int u, int fath) {
		nid[u] = ++tim;
		tp[u] = fath;
		if (hson[u]) dfs2(hson[u], fath);
		for (int i = G.elst[u]; i; i = G.e[i].nxt) {
			int v = G.e[i].to;
			if (fa[u][0] == v || hson[u] == v) continue;
			dfs2(v, v);
		}
	}
	
	inline void init() {
		for (int j = 1; j <= 18; j++)
			for (int i = 1; i <= n; i++) fa[i][j] = fa[fa[i][j - 1]][j - 1];
		for (int j = 1; j <= 18; j++)
			for (int i = 1; i <= n; i++) d[i][j] = Max(d[i][j - 1], d[fa[i][j - 1]][j - 1]);
	}
	
	inline int LCA(int u, int v) {
		if (dep[u] < dep[v]) Swap(u, v);
		for (int i = 18; i >= 0; i--)
			if (dep[u] - dep[v] & 1 << i) u = fa[u][i];
		if (u == v) return u;
		for (int i = 18; i >= 0; i--)
			if (fa[u][i] != fa[v][i]) u = fa[u][i], v = fa[v][i];
		return fa[u][0];
	}
	
	struct Segment_tree {
		struct node {
			int l, r;
			ll mn, lazy_mn;
		} t[800005];
		
		inline void init() {
			for (int i = 1; i <= 8e5; i++) t[i].mn = t[i].lazy_mn = 1e18;
		}
		
		inline void pushup(int p) {t[p].mn = Min(t[lson].mn, t[rson].mn);}
		
		inline void build(int p, int l, int r) {
			t[p].l = l;
			t[p].r = r;
			if (l == r) return;
			int mid = l + r >> 1;
			build(lson, l, mid);
			build(rson, mid + 1, r);
		}
		
		inline void pushdown(int p) {
			if (t[p].lazy_mn == 1e18) return;
			t[lson].mn = Min(t[lson].mn, t[p].lazy_mn);
			t[rson].mn = Min(t[rson].mn, t[p].lazy_mn);
			t[lson].lazy_mn = Min(t[lson].lazy_mn, t[p].lazy_mn);
			t[rson].lazy_mn = Min(t[rson].lazy_mn, t[p].lazy_mn);
			t[p].lazy_mn = 1e18;
		}
		
		inline void update(int p, int l, int r, ll d) {
			if (l <= t[p].l && r >= t[p].r) {
				t[p].mn = Min(t[p].mn, d);
				t[p].lazy_mn = Min(t[p].lazy_mn, d);
				return;
			}
			pushdown(p);
			int mid = t[p].l + t[p].r >> 1;
			if (l <= mid) update(lson, l, r, d);
			if (r > mid) update(rson, l, r, d);
			pushup(p);
		}
		
		inline ll query(int p, int l, int r) {
			if (l <= t[p].l && r >= t[p].r) return t[p].mn;
			pushdown(p);
			int mid = t[p].l + t[p].r >> 1;
			if (l <= mid) return query(lson, l, r);
			if (r > mid) return query(rson, l, r);
		}
	} seg;
} hld;

int main() {
	scanf("%d %d", &n, &m);
	for (int i = 1; i <= m; i++) scanf("%d %d %lld", &e[i].u, &e[i].v, &e[i].w), e[i].id = i;
	kruskal();
	hld.dfs(1);
	hld.dfs2(1, 1);
	hld.init();
	sort(e + 1, e + 1 + m, cmp2);
	for (int i = 1; i <= m; i++) {
		if (is_tree_edge[i]) continue;
		int u = e[i].u, v = e[i].v, lca = hld.LCA(u, v);
		for (int j = 18; j >= 0; j--)
			if (hld.dep[u] - hld.dep[lca] & 1 << j) ans[i] = Max(ans[i], hld.d[u][j]), u = hld.fa[u][j];
		for (int j = 18; j >= 0; j--)
			if (hld.dep[v] - hld.dep[lca] & 1 << j) ans[i] = Max(ans[i], hld.d[v][j]), v = hld.fa[v][j];
		ans[i]--;
	}
	hld.seg.build(1, 1, n);
	hld.seg.init();
	for (int i = 1; i <= m; i++) {
		if (is_tree_edge[i]) continue;
		int u = e[i].u, v = e[i].v;
		while (hld.tp[u] != hld.tp[v]) {
			if (hld.dep[hld.tp[u]] < hld.dep[hld.tp[v]]) Swap(u, v);
			hld.seg.update(1, hld.nid[hld.tp[u]], hld.nid[u], e[i].w);
			u = hld.fa[hld.tp[u]][0];
		}
		if (hld.dep[u] > hld.dep[v]) Swap(u, v);
		if (u == v) continue;
		hld.seg.update(1, hld.nid[u] + 1, hld.nid[v], e[i].w);
	}
	for (int i = 1; i <= m; i++)
		if (is_tree_edge[i]) {
			int u = e[i].u, v = e[i].v;
			if (hld.dep[u] > hld.dep[v]) Swap(u, v);
			ll tmp = hld.seg.query(1, hld.nid[v], hld.nid[v]);
			ans[i] = tmp == 1e18 ? 0 : tmp;
			ans[i]--;
		}
	for (int i = 1; i <= m; i++) printf("%lld ", ans[i]);
	return 0;
}
```
