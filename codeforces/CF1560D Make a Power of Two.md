```cpp
#include <cstdio>
#include <iostream>
#include <cstring>
#include <vector>
#define int long long
using namespace std;
inline int Min(int x, int y) {return x < y ? x : y;}
inline int Max(int x, int y) {return x > y ? x : y;}

vector<int> vec;
inline void init(int n) {
	for (int i = 1; i <= n; i <<= 1) vec.push_back(i);
}

int a[15], b[15], c[15], d[15];
inline int calc(int s, int e) {
	a[0] = b[0] = 0;
	while (s) a[++a[0]] = s % 10, s /= 10;
	while (e) b[++b[0]] = e % 10, e /= 10;
	memset(c, 0, sizeof(c));
	memset(d, 0, sizeof(d));
	for (int i = 1; i <= a[0]; i++) c[i] = a[a[0] - i + 1];
	for (int i = 1; i <= b[0]; i++) d[i] = b[b[0] - i + 1];
	int p = 1, q = 1;
	while (p <= a[0] && q <= b[0]) {
	    if (c[p] == d[q]) p++, q++;
	    else p++;
	}
	q--;
	return a[0] - q + b[0] - q;
}

signed main() {
	init(1e18);
	int T;
	scanf("%lld", &T);
	while (T--) {
		int n, ans = 1e18;
		scanf("%lld", &n);
		for (auto x : vec) ans = Min(ans, calc(n, x));
		printf("%lld\n", ans);
	}
	return 0;
}
```
