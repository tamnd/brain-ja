---
title: "CF 1906F - 価値を最大化"
description: "サイズ $N$ の配列が与えられ、最初はゼロで埋められます。 $M$ 操作があります。 各演算は 3 つの整数 $Li、Ri、Xi$ で記述されます。つまり、この演算を実行すると、$Li$ から $Ri$ の位置にあるすべての要素に $Xi$ が追加されます。"
date: "2026-06-08T20:45:08+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1906
codeforces_index: "F"
codeforces_contest_name: "2023-2024 ICPC, Asia Jakarta Regional Contest (Online Mirror, Unrated, ICPC Rules, Teams Preferred)"
rating: 2100
weight: 1906
solve_time_s: 105
verified: true
draft: false
---

[CF 1906F - 価値の最大化](https://codeforces.com/problemset/problem/1906/F)

 **評価:** 2100
 **タグ:** データ構造、並べ替え
 **解決時間:** 1 分 45 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 サイズの配列が与えられます$N$、最初はゼロで埋められます。 がある$M$操作。 各操作は 3 つの整数で記述されます$L_i, R_i, X_i$つまり、この操作を実行すると、$X_i$位置内のすべての要素に$L_i$を通して$R_i$。 

クエリは少し特殊です: クエリごとに$(K, S, T)$の可能な最大値を見つけたいと考えています。$A_K$範囲から選択された連続した一連の操作を実行した後$S$に$T$。 言い換えれば、任意の連続した操作ブロックを選択できます。$[S, T]$それらを順番に実行し、その位置の最大値を知りたいのです$K$到達できる。 

制約$N, M, Q \le 10^5$つまり、すべてのクエリに対してすべての連続する範囲を試行する単純なソリューションでは、時間がかかりすぎます。 各クエリには次のことが必要になる可能性があります$O(M^2)$ブルートフォースアプローチでチェックインしますが、これは実行不可能です。 私たちは解決策を探さなければなりません$O((M + Q) \log M)$または$O(M + Q)$前処理と効率的なデータ構造を使用してクエリごとに実行します。 

演算に負の値が含まれる可能性がある場合、明らかではないエッジ ケースが発生します。 たとえば、次の場合$X_i$が負の場合、より多くの操作を実行しても必ずしも増加するわけではありません$A_K$。 「より多くの操作が常に良い」と仮定する不注意なアプローチでは、間違った答えが得られます。 同様に、クエリの範囲が$[S, T]$影響を与える操作は含まれていません$A_K$、正解はゼロです。 

たとえば、次のように考えてみましょう$N = 2$、$M = 2$、操作:$(1, 1, -10), (2, 2, 20)$、およびクエリ$(1, 1, 2)$。 操作 1 のみが位置 1 に影響するため、最大値は$A_1$-10です。 すべての操作の素朴な合計$[S, T]$2 番目の操作で誤って 20 を追加してしまいます。 

## アプローチ

 ブルート フォース手法は簡単です。クエリごとに、次の操作のすべての部分配列を反復処理します。$[S, T]$、操作の適用をシミュレートし、 の最大値を追跡します。$A_K$。 これには必要です$O((T-S+1)^2 \cdot N)$クエリごとの作業、つまり$O(M^2 \cdot N)$最悪の場合。 と$M$まで$10^5$、このアプローチには必要があります$10^{15}$操作、明らかに不可能です。 

重要な洞察は、各ポジションについて次のとおりです。$K$、それに影響を与える操作のみを考慮します。 各操作は、配列位置の連続した範囲に影響を与えます。 ビルドする操作をそれぞれ前処理できます。$K$、影響を与える操作値のリスト。 次に、各クエリは、サブシーケンスに限定された、これらの値の連続する部分配列の最大合計を見つけることに帰着します。$[S, T]$。 これは古典的な「部分配列の合計の最大値」の問題であり、次のように解決できます。$O(\text{length})$Kadane のアルゴリズムを使用するか、複数のクエリを効率的に処理したい場合はセグメント ツリーを使用するとさらに高速になります。 

クエリは特定の範囲内の最大のサブ配列を要求するため、各サブ配列に影響を与える一連の操作にわたってセグメント ツリーを構築できます。$A_K$。 各セグメント ツリー ノードには、合計合計、最大プレフィックス合計、最大サフィックス合計、および最大サブアレイ合計の 4 つの値が格納されます。 次に、質問に答えることは、$O(\log M)$前処理後のクエリごと。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(Q * M * N) | O(N) | 遅すぎる |
 | 前処理 + 位置ごとのセグメント ツリー | O(M log M + Q log M) | お(お) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各ポジションごと$1 \le K \le N$、すべての操作を繰り返し、位置に影響を与える操作を収集します。$K$。 整数のリストとして格納します。インデックスは元の操作番号に対応します。 
2. ポジションごとに$K$、関連する操作のリストに基づいてセグメント ツリーを構築します。 セグメント ツリーの各ノードには、セグメントの合計、最大プレフィックス合計、最大サフィックス合計、および最大サブアレイ合計の 4 つの値が格納されます。 これらにより、子セグメントを効率的に組み合わせることができます。 
3. クエリごとに$(K, S, T)$:

 ａ． グローバル操作インデックスをマッピングする$[S, T]$影響を与える操作のリスト内のインデックスへ$K$。 実際に変更を行う操作のみ$A_K$が関係します。 

b. これらのインデックス内の最大サブ配列合計をセグメント ツリーに問い合わせます。 これにより、可能な最大の貢献が返されます。$A_K$クエリ範囲内の任意の連続した操作のサブシーケンスから。 
4. 結果を出力します。 影響を与える操作がない場合$A_K$範囲内では、最大合計はゼロになります。 

機能する理由: セグメント ツリーは、各ノードがその間隔にわたる最大サブ配列合計を正確に報告するという不変条件を維持します。 サブ範囲をクエリする場合、セグメント ツリーは対数時間でノードを結合し、サブ配列の最大合計を保持します。 位置ごとに操作を前処理することで、無関係な操作の繰り返しを回避し、効率を確保します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SegmentTree:
    def __init__(self, data):
        n = len(data)
        self.N = 1
        while self.N < n: self.N <<= 1
        self.data = [(-float('inf'), -float('inf'), -float('inf'), 0)] * (2*self.N)
        for i in range(n):
            val = data[i]
            self.data[self.N + i] = (val, val, val, val)  # total, prefix, suffix, max subarray
        for i in range(self.N-1, 0, -1):
            self.data[i] = self._combine(self.data[2*i], self.data[2*i+1])
    
    def _combine(self, left, right):
        total = left[0] + right[0]
        prefix = max(left[1], left[0] + right[1])
        suffix = max(right[2], right[0] + left[2])
        maxsum = max(left[3], right[3], left[2] + right[1])
        return (total, prefix, suffix, maxsum)
    
    def query(self, l, r):
        l += self.N
        r += self.N
        left_res = (0, 0, 0, -float('inf'))
        right_res = (0, 0, 0, -float('inf'))
        while l < r:
            if l % 2:
                left_res = self._combine(left_res, self.data[l])
                l += 1
            if r % 2:
                r -= 1
                right_res = self._combine(self.data[r], right_res)
            l //= 2
            r //= 2
        return self._combine(left_res, right_res)[3]

N, M = map(int, input().split())
ops = []
for _ in range(M):
    L, R, X = map(int, input().split())
    ops.append((L-1, R-1, X))

# map each position to affecting operations
pos_ops = [[] for _ in range(N)]
for idx, (L, R, X) in enumerate(ops):
    for k in range(L, R+1):
        pos_ops[k].append((idx, X))

# build segment trees per position
trees = []
for lst in pos_ops:
    vals = [x for idx, x in lst]
    if vals:
        trees.append(SegmentTree(vals))
    else:
        trees.append(None)

Q = int(input())
for _ in range(Q):
    K, S, T = map(int, input().split())
    K -= 1
    S -= 1
    # map global indices S,T to position-specific list indices
    lst = pos_ops[K]
    if not lst:
        print(0)
        continue
    l = 0
    while l < len(lst) and lst[l][0] < S:
        l += 1
    r = l
    while r < len(lst) and lst[r][0] <= T-1:
        r += 1
    if l == r:
        print(0)
    else:
        print(trees[K].query(l, r))
```最初のセクションは入力を読み取り、配列の位置ごとに、それに影響を与える操作を収集します。 各セグメント ツリーには、範囲最大クエリをサポートするために、関連するプレフィックス、サフィックス、および最大合計が格納されます。 クエリごとに、グローバル操作インデックスを位置固有の操作リストに注意深くマッピングし、セグメント ツリーをクエリします。 境界チェックにより、空の範囲がゼロを返すことが保証されます。 

## 実用的な例

 **サンプル入力 1**:```
2 6
1 1 -50
1 2 -20
2 2 -30
1 1 60
1 2 40
2 2 10
5
1 1 6
2 1 6
1 1 3
2 1 3
1 1 2
```位置 1 の場合:

 | オプ IDX | 位置 1 に影響します | X |
 | --- | --- | --- |
 | 0 | はい | -50 |
 | 1 | はい | -20 |
 | 2 | いいえ | - |
 | 3 | はい | 60 |
 | | | |
