```cpp
#include <cstdio>
#include <iostream>
#include <cstring>
using namespace std;
typedef long long ll;
inline int Max(int x, int y) {return x > y ? x : y;}

char a[500005], b[1000005];
int r[1000005], nr[500005];

int main() {
	int n, len;
	scanf("%d %s", &n, a + 1);
	len = n << 1 | 1;
	for (int i = 1; i <= n; i++) b[(i << 1) - 1] = '#', b[i << 1] = a[i];
	b[n << 1 | 1] = '#';
	int R = -1, C = 0;
	for (int i = 1; i <= len; i++) {
		if (i > R) {
			int j = i + 1;
			while (j <= len && (i << 1) - j >= 1 && ((b[(i << 1) - j] == b[j] && b[j] == '#') || (b[(i << 1) - j] != b[j] && b[j] == '1' || b[j] == '0'))) j++;
			j--;
			r[i] = j - i + 1;
			R = j;
			C = i;
		}
		else {
			int cl = (C << 1) - R, p2 = (C << 1) - i, pl = p2 - r[p2] + 1;
			if (pl > cl) r[i] = r[p2];
			if (pl < cl) r[i] = R - i + 1;
			if (pl == cl) {
				int j = R + 1;
				while (j <= len && (i << 1) - j >= 1 && ((b[(i << 1) - j] == b[j] && b[j] == '#') || (b[(i << 1) - j] != b[j] && b[j] == '1' || b[j] == '0'))) j++;
				j--;
				r[i] = j - i + 1;
				R = j;
				C = i;
			}
		}
	}
	ll ans = 0;
	for (int i = 1; i <= len; i++) {
		ll t = 1ll * r[i] << 1 - 1 >> 1 >> 1;
		ans += t * (t + 1) >> 1;
	}
	printf("%lld", ans);
	puts("\n-----");
	printf("%s\n", b + 1);
	for (int i = 1; i <= len; i++) printf("%d", r[i]);
	return 0;
}
```
