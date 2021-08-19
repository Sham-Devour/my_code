```cpp
#include <cstdio>
#include <iostream>
using namespace std;
inline void Swap(int &x, int &y) {x ^= y ^= x ^= y;}

int main() {
	int T;
	scanf("%d", &T);
	while (T--) {
		int a, b, c, n;
		scanf("%d %d %d", &a, &b, &c);
		if (a > b) Swap(a, b);
		n = b - a << 1;
		if (a > n || b > n || c > n) {puts("-1"); continue;}
		if (c <= n >> 1) printf("%d\n", (n >> 1) + c);
		else printf("%d\n", c - (n >> 1));
	}
	return 0;
}
```
