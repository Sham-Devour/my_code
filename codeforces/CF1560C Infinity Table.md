```cpp
#include <cstdio>
#include <iostream>
using namespace std;

int main() {
	int T;
	scanf("%d", &T);
	while (T--) {
		int k, sum = 0, tot, cnt = 0, d;
		scanf("%d", &k);
		for (int i = 1;; i += 2) {
			if (sum + i >= k) {
				tot = sum;
				sum += i;
				cnt++;
				break;
			}
			sum += i;
			cnt++;
		}
		d = k - tot;
		if (d <= sum - tot >> 1) printf("%d %d\n", d, cnt);
		else printf("%d %d\n", cnt, cnt - (d - (sum - tot >> 1)) + 1);
	}
	return 0;
}
```
