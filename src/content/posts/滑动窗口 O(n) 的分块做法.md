---
title: 滑动窗口 O(n) 的分块做法
published: 2022-05-28
description: '搬运自我的 cnblogs'
image: ''
tags: ['算法']
category: '学术'
draft: false 
lang: ''
---

:::info

此为本人于 2022-05-28 初次发表在 cnblogs 的内容，迁移至此。[阅读原文](https://www.cnblogs.com/luogu-int64/p/16320845.html)

:::

一种复杂度为 $\mathcal{O}(n)$ 的分块做法。不需要开 O2。

显然，这题可以想出一种 $\mathcal{O}(n\sqrt{n})$ 的垃圾分块做法，过不了，实测只有 [70 分](https://www.luogu.com.cn/record/76536917)，但是开 O2 可过，但是这样就没有优化的乐趣了。

观察查询阶段：

同一个块内我们不考虑，因为这部分直接暴力即可。看两端点不在同一个块内的情况，朴素代码如下：

```cpp
for (int i = l; i <= b[bel[l]].ed; i++) {
	minv = min(minv, a[i]);
	maxv = max(maxv, a[i]);
}
for (int i = b[bel[r]].st; i <= r; i++) {
	minv = min(minv, a[i]);
	maxv = max(maxv, a[i]);
}
for (int i = bel[l] + 1; i < bel[r]; i++) {
	minv = min(minv, b[i].minv);
	maxv = max(maxv, b[i].maxv);
}
```

我们使用了比较大的时间在暴力计算零散块的值，这会花费很多时间。（之所以这样说，是因为如果注释了前两个暴力的 for 之后，我们发现[只有一个点超时了](https://www.luogu.com.cn/record/76537264)）

注意到，这里零散块的暴力都是从一个块的起点或终点到某个点之前的最值，我们可以使用前缀和后缀最值来实现 $\mathcal{O}(1)$ 查询块的起点、终点到一个点之间的最值。我的代码中使用 premin/max 和 sufmin/max 分别表示前缀最小最大值和后缀最小最大值。

那么恭喜，使用这种方法我们可以得到 [90 pts](https://www.luogu.com.cn/record/76490775)，正好对应了之前注释掉的评测记录。说明这种优化是大有作用的。

此刻，你发现你陷入了一个窘境——你想不出别的方法优化了。这里，我们就需要对块长入手。

我们要充分发挥 ~~人类的智慧~~ 前缀最值和后缀最值的作用，因为使用这两个数组可以 $\mathcal{O}(1)$ 找到最值。

能用到前缀最值和后缀最值的情况只有当查询的两个端点在不同的块内。为了达到这个目标，我们需要将块长设置为 $k-1$。（我的代码中 $k$ 与块的个数重复了，于是使用 $l$ 代替）可能说的不是很清楚，我画个图：

![](https://cdn.luogu.com.cn/upload/image_hosting/kpduogal.png)

上图中红色框表示块，绿色框表示查询区间。对于查询区间的 ①，可以看做是从 $l$ 到 $l$ 所在的块末尾的最值（使用后缀最值数组），② 同理可以看做是从 $r$ 所在的块的起始到 $r$ 的最值（使用前缀最值数组）。这样可以直接 $\mathcal{O}(1)$ 解决查询问题。

那么时间复杂度也就降到了 $\mathcal{O}(n)$，好像不能再低了。

### 代码

```cpp
#include<iostream>
#include<cmath>
#include<ctype.h>
#include<climits>
#include<vector>
using namespace std;

#define int long long

const int maxn = 1e6 + 10;
const int maxblocksize = 1010;

int n, l;
int a[maxn];

struct block {
    int st, ed;
    vector<int> premax, premin;
    vector<int> sufmax, sufmin;
};
vector<block> b;

int blocksize, k, bel[maxn];

void init() {
    blocksize = l - 1, k = n / blocksize;
    b.resize(k + 5);
    for (int i = 1; i <= k; i++) {
        b[i].st = b[i - 1].ed + 1;
        b[i].ed = b[i].st + blocksize - 1;
    }
    if (b[k].ed != n) {
        k++;
        b[k].st = b[k - 1].ed + 1;
        b[k].ed = n;
    }
    for (int i = 1; i <= k; i++) {
        b[i].premax.resize(b[i].ed - b[i].st + 20);
        b[i].premin.resize(b[i].ed - b[i].st + 20);
        b[i].sufmax.resize(b[i].ed - b[i].st + 20);
        b[i].sufmin.resize(b[i].ed - b[i].st + 20);
        b[i].premax[0] = b[i].sufmax[b[i].ed + 1 - b[i].st + 1] = LLONG_MIN;
        b[i].premin[0] = b[i].sufmin[b[i].ed + 1 - b[i].st + 1] = LLONG_MAX;
        for (int j = b[i].st; j <= b[i].ed; j++) {
            bel[j] = i;
            b[i].premax[j - b[i].st + 1] = max(b[i].premax[j - b[i].st], a[j]);
            b[i].premin[j - b[i].st + 1] = min(b[i].premin[j - b[i].st], a[j]);
        }
        for (int j = b[i].ed; j >= b[i].st; j--) {
            b[i].sufmax[j - b[i].st + 1] = max(b[i].sufmax[j - b[i].st + 2], a[j]);
            b[i].sufmin[j - b[i].st + 1] = min(b[i].sufmin[j - b[i].st + 2], a[j]);
        }
    }
    return;
}
pair<int, int> query(int l, int r) {
    int minv = LLONG_MAX, maxv = LLONG_MIN;
    minv = min(minv, b[bel[l]].sufmin[l - b[bel[l]].st + 1]);
    minv = min(minv, b[bel[r]].premin[r - b[bel[r]].st + 1]);
    maxv = max(maxv, b[bel[l]].sufmax[l - b[bel[l]].st + 1]);
    maxv = max(maxv, b[bel[r]].premax[r - b[bel[r]].st + 1]);
    return make_pair(minv, maxv);
}

pair<int, int> ans[maxn];

signed main() {
    cin >> n >> l;
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
    }
    if (l == 1) {
        for (int i = 1; i <= n; i++) {
            cout << a[i] << ' ';
        }
        cout << endl;
        for (int i = 1; i <= n; i++) {
            cout << a[i] << ' ';
        }
        cout << endl;
        return 0;
    }
    init();
    int cnt = 0;
    for (int i = 1; i + l - 1 <= n; i++) {
        ans[++cnt] = query(i, i + l - 1);
    }
    for (int i = 1; i <= cnt; i++) {
        cout << ans[i].first << ' ';
    }
    cout << endl;
    for (int i = 1; i <= cnt; i++) {
        cout << ans[i].second << ' ';
    }
    cout << endl;
    return 0;
}
```





