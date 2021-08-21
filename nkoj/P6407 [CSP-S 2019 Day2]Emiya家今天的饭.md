```cpp
#include <cstdio>
#include <iostream>
#include <cstring>
using namespace std;
typedef long long ll;
const ll mod = 998244353;

ll a[105][2005], sum[105], f[105][205], g[105][105];

int main() {
	int n, m;
	scanf("%d %d", &n, &m);
	for (int i = 1; i <= n; i++)
		for (int j = 1; j <= m; j++) scanf("%lld", &a[i][j]), sum[i] = (sum[i] + a[i][j]) % mod;
	ll tot = 0, ans = 0;
	for (int w = 1; w <= m; w++) {
		memset(f, 0, sizeof(f));
		f[0][n] = 1;
		for (int i = 1; i <= n; i++)
			for (int j = n - i; j <= n + i; j++) {
				ll tmp1, tmp2, tmp3;
				tmp1 = f[i - 1][j];
				tmp2 = f[i - 1][j - 1] * a[i][w] % mod;
				tmp3 = f[i - 1][j + 1] * (((sum[i] - a[i][w]) % mod + mod) % mod) % mod;
				f[i][j] = ((tmp1 + tmp2) % mod + tmp3) % mod;
			}
		for (int i = n + 1; i <= n << 1; i++) tot = (tot + f[n][i]) % mod;
	}
	g[0][0] = 1;
	for (int i = 1; i <= n; i++)
		for (int j = 0; j <= n; j++) {
			if (!j) {g[i][j] = 1; continue;}
			g[i][j] = (g[i - 1][j] + g[i - 1][j - 1] * sum[i] % mod) % mod;
		}
	for (int i = 1; i <= n; i++) ans = (ans + g[n][i]) % mod;
	printf("%lld", ((ans - tot) % mod + mod) % mod);
	return 0;
}
```
