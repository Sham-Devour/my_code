```cpp
#include <cstdio>
#include <iostream>
#include <queue>
#define lson (p << 1)
#define rson (p << 1 | 1)
using namespace std;
typedef long long ll;

int n, q, s, leaf_in[100005], leaf_out[100005];
ll dis[800005];
bool vis[800005];

int cnt, elst[800005];
struct edge {
	int to, nxt;
	ll len;
} e[3500005];
inline void add(int u, int v, ll w) {
	e[++cnt].to = v;
	e[cnt].len = w;
	e[cnt].nxt = elst[u];
	elst[u] = cnt;
}

struct Segment_tree_in {
	struct node {
		int l, r;
	} t[400005];
	
	inline void build(int p, int l, int r) {
		t[p].l = l;
		t[p].r = r;
		if (l == r) {leaf_in[l] = p; return;}
		int mid = l + r >> 1;
		build(lson, l, mid);
		build(rson, mid + 1, r);
		add(p, lson, 0);
		add(p, rson, 0);
	}
	
	inline void update(int p, int l, int r, int u, ll w) {
		if (l <= t[p].l && r >= t[p].r) {add(leaf_out[u], p, w); return;}
		int mid = t[p].l + t[p].r >> 1;
		if (l <= mid) update(lson, l, r, u, w);
		if (r > mid) update(rson, l, r, u, w);
	}
} seg_in;

struct Segment_tree_out {
	struct node {
		int l, r;
	} t[400005];
	
	inline void build(int p, int l, int r) {
		t[p].l = l;
		t[p].r = r;
		if (l == r) {leaf_out[l] = p + 400000; return;}
		int mid = l + r >> 1;
		build(lson, l, mid);
		build(rson, mid + 1, r);
		add(lson + 400000, p + 400000, 0);
		add(rson + 400000, p + 400000, 0);
	}
	
	inline void update(int p, int l, int r, int v, ll w) {
		if (l <= t[p].l && r >= t[p].r) {add(p + 400000, leaf_in[v], w); return;}
		int mid = t[p].l + t[p].r >> 1;
		if (l <= mid) update(lson, l, r, v, w);
		if (r > mid) update(rson, l, r, v, w);
	}
} seg_out;

struct node {
	ll dis;
	int id;
	
	inline node() {}
	inline node(ll _dis, int _id) : dis(_dis), id(_id) {}
};
inline bool operator < (node x, node y) {
	return x.dis > y.dis;
}
priority_queue<node> pq;
inline void dijkstra(int s) {
	for (int i = 1; i <= 8e5; i++) dis[i] = 1e18;
	dis[s] = 0;
	pq.push(node(0, s));
	while (!pq.empty()) {
		node u = pq.top();
		pq.pop();
		if (vis[u.id]) continue;
		vis[u.id] = true;
		for (int i = elst[u.id]; i; i = e[i].nxt) {
			int v = e[i].to;
			if (dis[v] > dis[u.id] + e[i].len)
				dis[v] = dis[u.id] + e[i].len, pq.push(node(dis[v], v));
		}
	}
}

int main() {
	scanf("%d %d %d", &n, &q, &s);
	seg_in.build(1, 1, n);
	seg_out.build(1, 1, n);
	for (int i = 1; i <= n; i++) add(leaf_in[i], leaf_out[i], 0), add(leaf_out[i], leaf_in[i], 0);
	while (q--) {
		int op, v;
		scanf("%d %d", &op, &v);
		if (op == 1) {
			int u;
			ll w;
			scanf("%d %lld", &u, &w);
			add(leaf_out[v], leaf_in[u], w);
		}
		if (op == 2) {
			int l, r;
			ll w;
			scanf("%d %d %lld", &l, &r, &w);
			seg_in.update(1, l, r, v, w);
		}
		if (op == 3) {
			int l, r;
			ll w;
			scanf("%d %d %lld", &l, &r, &w);
			seg_out.update(1, l, r, v, w);
		}
	}
	dijkstra(leaf_out[s]);
	for (int i = 1; i <= n; i++) printf("%lld ", dis[leaf_in[i]] == 1e18 ? -1 : dis[leaf_in[i]]);
	return 0;
}
```
