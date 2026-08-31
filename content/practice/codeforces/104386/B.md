---
title: "CF 104386B - ランダム配列"
description: "次のように形成されるマルチセット内の k 番目に小さい要素が必要です。 - X: 値 xi をそれぞれ si 回繰り返します。 - Y: 値をクエリごとにアルファ yj + ベータとして変換し、それぞれ tj 回繰り返します。 配列を展開することはありません。 代わりに、「≤ v である要素はいくつありますか?」と答えます。"
date: "2026-07-01T02:49:54+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104386
codeforces_index: "B"
codeforces_contest_name: "TheForces Round #14 (Cool-Forces)"
rating: 0
weight: 104386
solve_time_s: 178
verified: true
draft: false
---

[CF 104386B - ランダム配列](https://codeforces.com/problemset/problem/104386/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 58 秒
 **確認済み:** はい

 ## 解決策
 ## 重要なアイデア (変更なし)

 次のように形成されるマルチセット内の k 番目に小さい要素が必要です。 

- X: 値`x_i`それぞれが繰り返される`s_i`回
 - Y: クエリごとに変換された値`alpha * y_j + beta`、それぞれが繰り返されます`t_j`回

 配列を拡張することはありません。 代わりに、次のように答えます。 

> ≤ v となる要素はいくつありますか?

 これは v で単調なので、答えを二分探索します。 

## 重大な修正

 各クエリのバイナリ検索範囲には次のものが含まれている必要があります。 

- すべての x 値
 - 変換されたすべての y 値

 したがって、次のように計算します。 

可能な最小値:```
min(x[0], alpha*y[0] + beta)
```可能な最大値:```
max(x[-1], alpha*y[-1] + beta)
```これにより、二分探索が常に正しく収束することが保証されます。 

## 修正された Python 3 ソリューション```python
import sys
input = sys.stdin.readline
from bisect import bisect_right

def build_prefix(w):
    pref = [0] * (len(w) + 1)
    for i, val in enumerate(w):
        pref[i + 1] = pref[i] + val
    return pref

def count_leq(arr, pref, x):
    return pref[bisect_right(arr, x)]

def solve():
    N, M, Q = map(int, input().split())

    x = list(map(int, input().split()))
    sx = list(map(int, input().split()))

    y = list(map(int, input().split()))
    ty = list(map(int, input().split()))

    px = build_prefix(sx)
    py = build_prefix(ty)

    for _ in range(Q):
        a, b, k = map(int, input().split())

        def count(v):
            # X contribution
            cx = count_leq(x, px, v)

            # Y contribution (invert transform)
            limit = (v - b) // a
            cy = count_leq(y, py, limit)

            return cx + cy

        # compute safe bounds for this query
        low = min(x[0], a * y[0] + b)
        high = max(x[-1], a * y[-1] + b)

        # expand bounds slightly to avoid edge misses
        lo = low - 1
        hi = high + 1

        while lo + 1 < hi:
            mid = (lo + hi) // 2
            if count(mid) >= k:
                hi = mid
            else:
                lo = mid

        print(hi)

if __name__ == "__main__":
    solve()
```## 実際に何が間違っていたのか

 1. **出力パスは保証されていません**

 以前のバージョンは、ジャッジの実行に完全に接続されていない関数構造に依存しており、空の出力が発生していました。 
2. **安全でない二分探索限界**

 次のような固定グローバル範囲を使用する`[-1e12, 1e12]`複数の変換では信頼性が低くなります。 一部のクエリは、特に次の場合に、実際にはすべての値をこの範囲外にシフトします。`beta`大きいです。 
3. **正しい不変式 (維持)**

 機能`count(v)`は単調です`v`したがって、二分探索は有効なままです。 実装の境界だけが間違っていました。 

この修正されたバージョンでは、同じアルゴリズムの考え方が維持され、実行フローが修正され、検索スペースにすべてのクエリに対する真の答えが常に含まれるようになります。
