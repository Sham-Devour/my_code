```cpp
#include <cstdio>
#include <iostream>
#include <cstring>
#include <vector>
using namespace std;

char t[500005], b[500005], del[500005], s[500005], gb[500005], dgb[500005], tmp[500005];
int bac[26], nw_bac[26];

int main() {
	int T;
	scanf("%d", &T);
	while (T--) {
		scanf("%s", t + 1);
		int len = strlen(t + 1), nw_len = 0, tr_len = 0;
		memset(bac, 0, sizeof(bac));
		for (int i = len; i; i--) {
			if (!bac[t[i] - 'a']) b[++nw_len] = t[i];
			bac[t[i] - 'a']++;
		}
		for (int i = 1; i <= nw_len; i++) del[i] = b[nw_len - i + 1];
		bool flag = true;
		memset(nw_bac, 0, sizeof(nw_bac));
		for (int i = 1; i <= nw_len; i++) {
			if (bac[del[i] - 'a'] % i) {flag = false; break;}
			nw_bac[del[i] - 'a'] = bac[del[i] - 'a'] / i;
		}
		if (!flag) {puts("-1"); continue;}
		for (int i = 0; i < 26; i++) tr_len += nw_bac[i];
		for (int i = 1; i <= tr_len; i++) s[i] = t[i], gb[i] = s[i];
		int gb_len = tr_len, dgb_len = 0;
		for (int i = 1; i < nw_len; i++) {
			for (int j = 1; j <= gb_len; j++) dgb[++dgb_len] = gb[j];
			int tmp_len = 0;
			for (int j = 1; j <= gb_len; j++)
				if (gb[j] != del[i]) tmp[++tmp_len] = gb[j];
			gb_len = tmp_len;
			for (int j = 1; j <= gb_len; j++) gb[j] = tmp[j];
		}
		for (int i = 1; i <= gb_len; i++) dgb[++dgb_len] = gb[i];
		flag = true;
		for (int i = 1; i <= dgb_len; i++)
			if (dgb[i] != t[i]) {flag = false; break;}
		if (!flag || dgb_len != len) {puts("-1"); continue;}
		for (int i = 1; i <= tr_len; i++) putchar(s[i]);
		putchar(' ');
		for (int i = 1; i <= nw_len; i++) putchar(del[i]);
		puts("");
	}
	return 0;
}
```
