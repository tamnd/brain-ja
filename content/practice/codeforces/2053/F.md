---
title: "CF 2053F - アーネスト マトリックス コンプリメント"
description: "行 $i$ については、 - $zi$ = その行内の -1 セルの数とします。 - $cnti(u)$ = 値 $u$ の既に修正された出現の数。 行 $i$ のすべての空白が同じ値 $pi$ で埋められると決定したとします。 これは制限ではありません。"
date: "2026-06-08T08:26:49+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "data-structures", "dp", "greedy", "implementation", "math"]
categories: ["algorithms"]
codeforces_contest: 2053
codeforces_index: "F"
codeforces_contest_name: "Good Bye 2024: 2025 is NEAR"
rating: 2600
weight: 2053
solve_time_s: 80
verified: true
draft: false
---

[CF 2053F - 本格的なマトリックス補完](https://codeforces.com/problemset/problem/2053/F)

 **評価:** 2600
 **タグ:** ブルート フォース、データ構造、DP、貪欲、実装、数学
 **解決時間:** 1 分 20 秒
 **確認済み:** はい

 ## 解決策
 ## 重要な観察

 一列に$i$、 させて：

-$z_i$= の数`-1`その行のセル。 
-$cnt_i(u)$= すでに固定されている値の出現数$u$。 

行内のすべての空白を次のように決定するとします。$i$同じ値で埋められます$p_i$。 

これは制限ではありません。 美しさの表現は行のカウントにおいて線形であるため、$z_i$空白は、最大の係数を与える単一の値に完全に移動できます。 

したがって、各行は選択された単一の値で表されます。$p_i$。 

値の最終的なカウント$u$並んでいる$i$になる$$c_{u,i}=cnt_i(u)+ \begin{cases} z_i,&u=p_i\\ 0,&u\neq p_i \end{cases}$$## 隣接する 2 つの行の寄与

 行の場合$i-1$そして$i$、$$\sum_u c_{u,i-1}c_{u,i}$$に拡大します$$C_i +z_{i-1}\,cnt_i(p_{i-1}) +z_i\,cnt_{i-1}(p_i) +z_{i-1}z_i[p_{i-1}=p_i]$$どこ$$C_i=\sum_u cnt_{i-1}(u)\,cnt_i(u)$$は選択肢に依存しない定数です。 

定義する$$dp_i(v)$$列を考慮した最大限の美しさとして$1\dots i$列のとき$i$価値を選ぶ$v$。 

それから$$dp_i(v) = C_i + z_i\,cnt_{i-1}(v) + \max_p \Big( dp_{i-1}(p) + z_{i-1}\,cnt_i(p) + z_{i-1}z_i[p=v] \Big)$$させて$$A(p)=dp_{i-1}(p)+z_{i-1}cnt_i(p).$$それから$$dp_i(v) = C_i + z_i cnt_{i-1}(v) + \max \Big( \max_p A(p), A(v)+z_{i-1}z_i \Big).$$これが重要な単純化です。 

## データ構造

 すべての行遷移には次のものが必要です。$$A(v)$$みんなのために$v$、 そして$$\max_v A(v).$$ほとんどの値は連続して出現することはありません。 現在の行または前の行に出現する値のみが重要です。 

標準的に受け入れられているソリューションは、すべての DP 値を保存します。$1\ldots k$以下をサポートする遅延セグメント ツリー内で:

 - 範囲の追加、
 - ポイント追加、
 - グローバル値を含む範囲 chmax、
 - グローバル最大値クエリ。 

行からの移行$i-1$漕ぐ$i$その後、受け入れられている Codeforces ソリューションとまったく同じように実装できます。$$O((\text{distinct values in row }i)+ (\text{distinct values in row }i-1)) \log k.$$すべてのテスト ケースにわたる行列セルの総数は最大でも$6\cdot10^5$、全体的な複雑さは次のとおりです。$$O((nm)\log k),$$制限内に快適に収まります。 

## 承認された Python 3 ソリューション```python
import sys
input = sys.stdin.readline

class SegTree:
    def __init__(self, n):
        self.n = n
        m = 4 * n + 5
        self.mx = [0] * m
        self.add = [0] * m
        self.tagmax = [0] * m

    def _apply(self, p, addv, maxv):
        self.mx[p] = max(self.mx[p] + addv, maxv)
        self.tagmax[p] = max(self.tagmax[p] + addv, maxv)
        self.add[p] += addv

    def _push(self, p):
        if self.add[p] != 0 or self.tagmax[p] != 0:
            a = self.add[p]
            m = self.tagmax[p]
            self._apply(p * 2, a, m)
            self._apply(p * 2 + 1, a, m)
            self.add[p] = 0
            self.tagmax[p] = 0

    def modify(self, p, l, r, ql, qr, addv, maxv):
        if ql <= l and r <= qr:
            self._apply(p, addv, maxv)
            return

        self._push(p)
        mid = (l + r) >> 1

        if ql <= mid:
            self.modify(p * 2, l, mid, ql, qr, addv, maxv)
        if qr > mid:
            self.modify(p * 2 + 1, mid + 1, r, ql, qr, addv, maxv)

        self.mx[p] = max(self.mx[p * 2], self.mx[p * 2 + 1])

    def clear(self):
        self.mx[:] = [0] * len(self.mx)
        self.add[:] = [0] * len(self.add)
        self.tagmax[:] = [0] * len(self.tagmax)

t = int(input())

for _ in range(t):
    n, m, k = map(int, input().split())
    a = [list(map(int, input().split())) for _ in range(n)]

    seg = SegTree(k)

    prev = {}

    for row in a:
        cur = {}

        for x in row:
            cur[x] = cur.get(x, 0) + 1

        z_prev = prev.get(-1, 0)
        z_cur = cur.get(-1, 0)

        for v, c in cur.items():
            if v != -1:
                seg.modify(1, 1, k, v, v, z_prev * c, 0)

        best = seg.mx[1]

        seg.modify(1, 1, k, 1, k, z_prev * z_cur, 0)
        seg.modify(1, 1, k, 1, k, 0, best)

        for v, c in cur.items():
            if v != -1:
                seg.modify(1, 1, k, 1, k, prev.get(v, 0) * c, 0)

        for v, c in prev.items():
            if v != -1:
                seg.modify(1, 1, k, v, v, z_cur * c, 0)

        prev = cur

    print(seg.mx[1])
```これは、上記の遷移から派生した、標準的に受け入れられている DP + 遅延セグメント ツリーの実装です。
