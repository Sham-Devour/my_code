```cpp
#include <cstdio>
#include <iostream>
using namespace std;

int a[1005];
inline void init(int n) {
	int tot = 1;
	while (a[0] < n) {
		while (tot % 3 == 0 || tot % 10 == 3) tot++;
		a[++a[0]] = tot;
		tot++;
	}
}

int main() {
	init(1e3);
	int T;
	scanf("%d", &T);
	while (T--) {
		int k;
		scanf("%d", &k);
		printf("%d\n", a[k]);
	}
	return 0;
}
```
