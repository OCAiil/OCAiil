---
title: Vjudge 练习 2
image: "https://i.postimg.cc/Y942qFhH/124778496-p0-master1200.jpg"
published: 2025-02-22
description: Practice 2
tags: [Algorithm, Parctice]
category: Algorithm Parctices
draft: false
---

# [A - 排队接水](https://vjudge.net/contest/696445#problem/A)

```c++
# include <bits/stdc++.h>
using namespace std;

int cmp(pair<int, int>a, pair<int, int>b) {
	if (a.second != b.second) return a.second < b.second;
	else return a.first < b.first;
}

int main() {
	int n;
	cin >> n;
	vector<pair<int, int> > T;
	int t;
	for (int i = 1; i <= n; ++i) {
		cin >> t;
		T.push_back(make_pair(i, t));
	}
	sort(T.begin(), T.end(), cmp);
	long long sum = 0, total_sum = 0;
	for (auto i : T) {
		cout << i.first << " ";
		total_sum += sum;
		sum += i.second;
	}
	cout << "\n";
	double res = 1.0 * total_sum / n;
	printf("%.2f", res);
	return 0;
}


```
<br/>

# [B - 陶陶摘苹果（升级版）](https://vjudge.net/contest/696445#problem/B)

```c++
# include <bits/stdc++.h>
using namespace std;

int cmp(pair<int, int>a, pair<int, int>b) {
	return a.second < b.second;
}

int main() {
	int n, s, a, b;
	cin >> n >> s >> a >> b;
	vector<pair<int, int>> apples;
	int x, y;
	for (int i = 0; i < n; ++i) {
		cin >> x >> y;
		if (x > a + b) continue;
		apples.push_back(make_pair(x, y));
	}
	sort(apples.begin(), apples.end(), cmp);
	int cnt = 0;
	for (auto i : apples) {
		if (s - i.second < 0) break;
		cnt++;
		s -= i.second;
	}
	cout << cnt;
	return 0;
}

```
<br/>

# [C - 合并果子](https://vjudge.net/contest/696445#problem/C)

```c++
# include <bits/stdc++.h>
using namespace std;

int main() {
	int n;
	cin >> n;
	priority_queue<unsigned long long, vector<unsigned long long>, greater<unsigned long long>> a;
	int m;
	for (int i = 0; i < n; ++i) {
		cin >> m;
		a.push(m);
	}
	unsigned long long sum = 0;
	for (int i = 0; i < n - 1; ++i) {
		unsigned long long t1 = a.top();
		a.pop();
		unsigned long long t2 = a.top();
		a.pop();
		unsigned long long tmp_sum = t1 + t2;
		a.push(tmp_sum);
		sum += tmp_sum;
	}
	cout << sum;
	return 0;
}

```
<br/>

# [D - Remove the Ends ](https://vjudge.net/contest/696445#problem/D)

```c++
# include <bits/stdc++.h>
using namespace std;

int main() {
	int t, n;
	cin >> t;
	while (t--) {
		int n;
		cin >> n;
		int x;
		long long ans = 0, ansr = 0;
		for (int i = 0; i < n; ++i) {
			cin >> x;
			if (x < 0) ans = max(ans - x, ansr - x);
			else ansr += x;
		}
		cout << max(ans, ansr) << endl;
	}
	return 0;
}

```
<br/>

