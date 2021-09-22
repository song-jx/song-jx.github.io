---
title: tmp
date: 2021-09-22 15:35:30
tags:
---

```cpp
#include <bits/stdc++.h>
#define rep(i, l, r) for(int i = (l); i <= (r); i++)
#define per(i, r, l) for(int i = (r); i >= (l); i--)
#define mem(a, b) memset(a, b, sizeof a)
#define For(i, l, r) for(int i = (l), i##e = (r); i < i##e; i++)
#define pb push_back

using namespace std;
using ll = long long;

const int N = 1e6 + 5;

int n;
char S[N], T[N]; bool s[N * 4], t[N * 4];
ll t0, t1, ts;

void get(char S[], bool s[]) {
	n = 0;
	for(auto i = S; *i; i++) {
		int j = *i > 57 ? *i - 55 : *i - 48;
		per(k, 3, 0) s[++n] = j >> k & 1;
	}
};
int main() {
	int test;
	for(cin >> test; test--;) {
		scanf("%*d%lld%lld%lld%s%s", &t0, &t1, &ts, S, T);
		get(S, s), get(T, t);
		stack<pair<int, ll>> stk[2];
		ll as = 0;
		rep(i, 1, n) if(s[i] ^ t[i]) {
			ll mi = s[i] ? t1 : t0;
			if(stk[!s[i]].size()) {
				auto [j, w] = stk[!s[i]].top();
				ll tmp = ts * (i - j) - w;
				if(tmp < mi) mi = tmp, stk[!s[i]].pop();
			}
			stk[s[i]].emplace(i, mi), as += mi;
		}
		printf("%lld\n", as);
	}
}
```

