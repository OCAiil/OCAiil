---
title: Vjudge 练习 1
image: "https://i.postimg.cc/7LxvWKj8/bb92ecb76c9a361d942ada0105bf2e12.jpg"
published: 2025-02-21
description: Practice 1
tags: [Algorithm, Parctice]
category: Algorithm Parctices
draft: false
---

# [A - Japanese Cursed Doll](https://vjudge.net/contest/694751#problem/A)

```c++
# include <bits/stdc++.h>
using namespace std;

int main() {
	int n, t, p;
	cin >> n >> t >> p;
	vector<int> l(n + 1);
	for (int i = 1; i <= n; ++i)
		cin >> l[i];
	sort(l.begin() + 1, l.end());
	cout << max(0, t - l[n- p + 1]);
	return 0;
}
```
<br/>

# [B - Rotate Colored Subsequence](https://vjudge.net/contest/694751#problem/B)

```c++
# include <bits/stdc++.h>
using namespace std;

int main() {
	int n, m, c;
	string s;
	cin >> n >> m >> s;
	vector<int> idx(n);
	vector<int> mm(m + 1, -1), firsts(m + 1, 0);
	for (int i = 0; i < n; ++i)
		idx[i] = i;
	for (int i = 0; i < n; ++i) {
		cin >> c;
		if (mm[c] == -1) {
			mm[c] = i;
			firsts[c] = i;
		}
		else {
			idx[i] = mm[c];
			mm[c] = i;
		}
	}
	for (int i = 1; i <= m; ++i) 
		idx[firsts[i]] = mm[i];
	for (int i : idx) cout << s[i];
	return 0;
}
```
<br/>

# [C - 玩具谜题](https://vjudge.net/contest/694751#problem/C)
```c++
# include <bits/stdc++.h>
using namespace std;

int main() {
	int n, m;
	cin >> n >> m;
	vector<int> face(n);
	vector<string> name(n);
	int tmp;
	for (int i = 0; i < n; ++i) {
		cin >> tmp;
		face[i] = (tmp == 0 ? 1 : -1);
		cin >> name[i];
	}
	int a, s;
	int idx = 0;
	for (int i = 0; i < m; ++i) {
		cin >> tmp;
		a = (tmp == 0 ? -1 : 1);
		cin >> s;
		idx += a * face[idx] * s;
		idx %= n;
		idx = idx + (idx < 0 ? n : 0);
	}
	cout << name[idx];
	return 0;
}
```
<br/>

# [D - 两只塔姆沃斯牛 The Tamworth Two](https://vjudge.net/contest/694751#problem/D)
```c++
# include <bits/stdc++.h>
using namespace std;

typedef struct {
	int x, y, face; // 0: N, 1: E, 2: S, 3: W
} Statu;

int dx[4] = {0, 1, 0, -1}, dy[4] = {-1, 0, 1, 0};

int main() {
	char m[10][10];
	Statu C, F;
	for (int i = 0; i < 10; ++i) {
		for (int j = 0; j < 10; ++j) {
			cin >> m[i][j];
			if (m[i][j] == 'C') { C = { j, i, 0 }; m[i][j] = '.'; }
			if (m[i][j] == 'F') { F = { j, i, 0 }; m[i][j] = '.'; }
		}
	}
	int t = 0;
	for (t = 1; t < 10000; ++t) {
		int new_cx = C.x + dx[C.face], new_cy = C.y + dy[C.face];
		if (new_cx < 0 || new_cx>9 || new_cy < 0 || new_cy>9 || m[new_cy][new_cx] == '*') C.face = (C.face + 1) % 4;
		else C = { new_cx, new_cy, C.face };

		int new_fx = F.x + dx[F.face], new_fy = F.y + dy[F.face];
		if (new_fx < 0 || new_fx>9 || new_fy < 0 || new_fy>9 || m[new_fy][new_fx] == '*') F.face = (F.face + 1) % 4;
		else F = { new_fx, new_fy, F.face };

		if (C.x == F.x && C.y == F.y) break;
	}
	cout << (t == 10000 ? 0: t);
	return 0;
}
```
<br/>

# [E - The Fool](https://vjudge.net/contest/694751#problem/E)
```c++
# include <bits/stdc++.h>
using namespace std;

int main() {
	int n, m, k;
	cin >> n >> m >> k;
	vector<string> lines(n);

	for (int i = 0; i < n; ++i) cin >> lines[i];
	string word1 = lines[0].substr(0, k);
	string word2 = lines[0].substr(k, k);
	string word3 = lines[1].substr(0, k);

	string word;
	if (word1 == word2) word = word1;
	if (word1 == word3) word = word1;
	if (word2 == word3) word = word2;

	int same = -1;
	for (int i = 0; i < n; ++i) {
		for (int j = 0; j < m; ++j) {
			string tmp_word = lines[i].substr(j * k, k);
			if (tmp_word != word) cout << i + 1 << " " << j + 1;
		}
	}
	return 0;
}
```
<br/>

# [F - LOWER](https://vjudge.net/contest/694751#problem/F)
```c++
# include <bits/stdc++.h>
using namespace std;

int main() {
	int n, q; 
	string s;
	cin >> n >> s >> q;
	vector<int[3]> ops(q);
	int do_op = 0;
	for (int i = 0; i < q; ++i) {
		scanf("%d %d %c", &ops[i][0], &ops[i][1], &ops[i][2]);
		if (ops[i][0] == 2 || ops[i][0] == 3) do_op = i;
	}
	for (int i = 0; i < q; ++i) {
		if (ops[i][0] == 1) 
			s[ops[i][1] - 1] = ops[i][2];
		if (i == do_op) {
			if (ops[i][0] == 2)
				transform(s.begin(), s.end(), s.begin(), ::tolower);
			if (ops[i][0] == 3)
				transform(s.begin(), s.end(), s.begin(), ::toupper);
		}
	}
	cout << s;
	return 0;
}
```
<br/>

# [G - 文件压缩](https://vjudge.net/contest/694751#problem/G)
```c++
# include <bits/stdc++.h>
using namespace std;

int main() {
	int n, p;
	cin >> n;
	vector<int> flag(n, 0);
	vector<char> l(n), r(n), ans(n);
	for (int i = 0; i < n; ++i) { cin >> r[i]; l[i] = r[i]; }
	cin >> p;
	sort(l.begin(), l.end());
	ans[0] = r[p - 1];
	flag[p - 1] = 1;
	char now = r[p - 1];
	for (int i = 0; i < n; ++i)
		if (l[i] == r[p - 1]) {
			ans[n - 1] = r[i];
			now = r[i];
			flag[i] = 1;
			break;
		}
	for (int i = 2; i < n; ++i) {
		for (int j = n - 1; j >= 0; --j) {
			if (l[j] == now && !flag[j]) {
				ans[n - i] = r[j];
				now = r[j];
				flag[j] = 1;
				break;
			}
		}
	}
	for (int i = 0; i < n; ++i) cout << ans[i];
	return 0;
}

```
<br/>

