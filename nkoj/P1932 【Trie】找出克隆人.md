```cpp
#include <cstdio>
#include <iostream>
#include <cstring>
using namespace std;

char s[25];
int bac[20005];

struct Trie {
	int siz, go[400005][26], val[400005];
	bool flag[400005];
	
	inline void init() {
		siz = 1;
		memset(go[0], 0, sizeof(go[0]));
		memset(val, 0, sizeof(val));
		memset(flag, false, sizeof(flag));
	}
	
	inline void ins(char *s) {
		int u = 0, len = strlen(s + 1);
		for (int i = 1; i <= len; i++) {
			int c = s[i] - 'A';
			if (!go[u][c]) memset(go[siz], 0, sizeof(go[siz])), go[u][c] = siz++;
			u = go[u][c];
		}
		val[u]++;
		flag[u] = true;
	}
} trie;

int main() {
	int n, m;
	for (;;) {
		scanf("%d %d", &n, &m);
		if (!n && !m) break;
		trie.init();
		for (int i = 1; i <= n; i++) scanf("%s", s + 1), trie.ins(s);
		memset(bac, 0, sizeof(bac));
		for (int i = 1; i <= trie.siz; i++)
			if (trie.flag[i]) bac[trie.val[i] - 1]++;
		for (int i = 0; i < n; i++) printf("%d\n", bac[i]);
	}
	return 0;
}
```
