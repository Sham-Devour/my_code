```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;

int siz, root[500005], a[500005], b[500005];

struct Chairman_tree {
	struct node {
		int lson, rson, cnt;
	} t[20000005];
	
	inline void pushup(int p) {
		t[p].cnt = t[t[p].lson].cnt + t[t[p].rson].cnt;
	}
	
	inline int build(int p, int l, int r) {
		if (!p) p = ++siz;
		if (l == r) return p;
		int mid = l + r >> 1;
		t[p].lson = build(t[p].lson, l, mid);
		t[p].rson = build(t[p].rson, mid + 1, r);
		return p;
	}
	
	inline int update(int p, int l, int r, int pre, int x) {
		if (!p) p = ++siz;
		if (l == r) {t[p].cnt = t[pre].cnt + 1; return p;}
		int mid = l + r >> 1;
		if (x <= mid) {
			t[p].lson = update(t[p].lson, l, mid, t[pre].lson, x);
			t[p].rson = t[pre].rson;
		}
		else {
			t[p].rson = update(t[p].rson, mid + 1, r, t[pre].rson, x);
			t[p].lson = t[pre].lson;
		}
		pushup(p);
		return p;
	}
	
	inline int query(int p, int q, int l, int r, int rnk) {
		if (l == r) return l;
		int mid = l + r >> 1;
		if (t[t[q].lson].cnt - t[t[p].lson].cnt > rnk)
			return query(t[p].lson, t[q].lson, l, mid, rnk);
		if (t[t[q].rson].cnt - t[t[p].rson].cnt > rnk)
			return query(t[p].rson, t[q].rson, mid + 1, r, rnk);
		return 0;
	}
} cmt;

int main() {
	int n, q;
	scanf("%d %d", &n, &q);
	for (int i = 1; i <= n; i++) scanf("%d", &a[i]), b[i] = a[i];
	sort(b + 1, b + 1 + n);
	root[0] = cmt.build(root[0], 1, n);
	for (int i = 1; i <= n; i++) {
		a[i] = lower_bound(b + 1, b + 1 + n, a[i]) - b;
		root[i] = cmt.update(root[i], 1, n, root[i - 1], a[i]);
	}
	while (q--) {
		int l, r, k;
		scanf("%d %d", &l, &r);
		k = r - l + 1 >> 1;
		printf("%d\n", b[cmt.query(root[l - 1], root[r], 1, n, k)]);
	}
	return 0;
}
```
