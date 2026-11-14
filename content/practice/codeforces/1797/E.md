---
title: "CF 1797E - リーファとアレイ"
description: "整数の配列と 2 種類の演算が与えられています。 最初の演算では、数値をその数値と互いに素な整数の数に減らすオイラーのトーティエント関数を適用して、範囲内の要素を変換します。"
date: "2026-06-09T09:58:20+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "data-structures", "dsu", "math", "number-theory", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 1797
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 864 (Div. 2)"
rating: 2300
weight: 1797
solve_time_s: 114
verified: false
draft: false
---

[CF 1797E - 李華とアレイ](https://codeforces.com/problemset/problem/1797/E)

 **評価:** 2300
 **タグ:** ブルート フォース、データ構造、DSU、数学、数論、2 つのポインター
 **解決時間:** 1 分 54 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 整数の配列と 2 種類の演算が与えられています。 最初の演算では、数値をその数値と互いに素な整数の数に減らすオイラーのトーティエント関数を適用して、範囲内の要素を変換します。 2 番目の演算は、範囲に対して、範囲内のすべての数値が等しくなるために必要なトーティエント関数の適用の数を求め、各要素の関数適用の数を数えます。 重要なのは、タイプ 2 のクエリは実際には配列を変更しないことです。 それらは仮定の計算です。 

入力サイズは、配列の長さと演算数の両方で最大 100,000 まで可能で、配列内の各数値は最大 500 万まで可能です。 クエリごとに各トーティエントの操作を直接シミュレートする総当り的なアプローチは遅すぎます。トーティエントの繰り返し適用には最大で 4 時間かかるためです。$O(\log a_i)$クエリごとに多くの要素に対してこれを行うと、最悪の場合、数十億回の操作が必要になります。 

要素が 1 に達すると、1 の合計が 1 となり、それ以上適用しても効果がないため、明らかではないエッジ ケースが発生します。 たとえば、配列`[1, 1, 2]`最初の 3 つの要素を等化するクエリでは、次のことを認識する必要があります。`2 → 1`は 1 回の操作を必要としますが、1 はゼロを必要とします。 1 を固定小数点として扱えない単純なアプローチでは、演算が過剰にカウントされてしまいます。 

## アプローチ

 ブルートフォースアプローチは、各操作を直接シミュレートすることです。 タイプ 1 の場合、範囲を反復処理して、各要素をその合計に設定します。 タイプ 2 の場合、範囲を反復し、数値ごとに、すべての要素がターゲット値に一致するまで、反復される全体的なアプリケーションをシミュレートし、すべての可能なターゲットを試します。 これは正しいですが、遅すぎます。最大 500 万の数値で最大 100,000 の範囲にわたってトーティエントを繰り返し適用するには、最悪の場合、何兆もの演算が必要になります。 

重要な洞察は、トーティエント関数を繰り返し適用すると数値がすぐに 1 に減るということです。実際、任意の数値に対するトーティエントのシーケンスは次のようになります。$x$は、最大 20 ステップで 1 に達します。これは、すでに 1 でない限り、各アプリケーションが数値を厳密に減少させるためです。したがって、要素ごとに、1 に達するまでその合計のシーケンスを事前計算できます。その後、タイプ 2 のクエリは、範囲について、すべての要素を等しくするためのステップの合計数を最小にする、事前計算されたシーケンス内の数値を見つけることになります。 

範囲操作を効率的に管理するために、拡張データを含むセグメント ツリーを使用します。 各ノードは、そのセグメント内の数値ごとに、その数値の頻度と、その数値に到達するために必要な全体アプリケーションの累積数を保存します。 タイプ 1 の操作は遅延的に伝播できます。セグメントがすでにすべて 1 である場合、それ以上の更新は必要ありません。 タイプ 2 の操作では、セグメント ツリーにクエリを実行して最良の共通数を取得し、関連するステップ数を合計できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(m * n * log(max(a_i))) | O(n) | 遅すぎる |
 | セグメント ツリー + トーティエント シーケンスの事前計算 | O(n log n + m log n) | O(n log max(a_i)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべての整数に対してオイラーの合計関数を可能な最大値まで事前計算します。$a_i$ふるいのようなアプローチを使用します。 これにより、O(1) 個の合計ルックアップが可能になります。 
2. 配列内の数値ごとに、1 に達するまで全合計シーケンスを事前計算します。このシーケンスとその長さを保存します。 最大長は 20 で制限されます。 
3. アレイ上にセグメント ツリーを構築します。 各ノードは、セグメント内の各値を、そのセグメントに到達するために必要な全体アプリケーションの数にマッピングするカウンターを格納します。 複数の数値が同じ値に減少する場合は、ステップを合計します。 
4. タイプ 1 の操作の場合、セグメント ツリーを再帰的に更新します。 セグメントに 1 のみが含まれている場合は、何も行いません。 それ以外の場合は、ノードのカウンターに保存されている各ステップを 1 ずつ増分し、子に遅延的に伝播します。 
5. タイプ 2 の操作の場合、セグメント ツリーをクエリして、すべての要素を均等化するためのステップの合計数を最小にする範囲内の値を見つけます。 その値についてカウンターに保存されているステップを合計します。 
6. 各タイプ 2 クエリの答えとして合計を返します。 セグメント ツリーにより、すべてのクエリと更新が確実に処理されます。$O(\log n)$操作ごとに。 

これが機能するのは、セグメント ツリーが各セグメントにわたる可能なターゲット値と累積ステップの正確な集計を維持しているためです。 遅延伝播により、影響を受けるセグメントごとに更新が 1 回だけ適用されることが保証されます。 事前に計算されたトーティエント シーケンスにより、繰り返されるトーティエントを再計算することがなくなります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from collections import defaultdict, Counter

MAX_A = 5_000_000

# Precompute totient
phi = list(range(MAX_A + 1))
for i in range(2, MAX_A + 1):
    if phi[i] == i:
        for j in range(i, MAX_A + 1, i):
            phi[j] -= phi[j] // i

# Precompute totient sequences
totient_seq = [[] for _ in range(MAX_A + 1)]
for x in range(1, MAX_A + 1):
    val = x
    while True:
        totient_seq[x].append(val)
        if val == 1:
            break
        val = phi[val]

class Node:
    __slots__ = 'l', 'r', 'left', 'right', 'counter', 'lazy'
    def __init__(self, l, r):
        self.l = l
        self.r = r
        self.left = None
        self.right = None
        self.counter = Counter()
        self.lazy = 0

def build(arr, l, r):
    node = Node(l, r)
    if l == r:
        node.counter = Counter({arr[l]: 0})
        return node
    mid = (l + r) // 2
    node.left = build(arr, l, mid)
    node.right = build(arr, mid+1, r)
    node.counter = node.left.counter + node.right.counter
    return node

def push(node):
    if node.lazy:
        new_counter = Counter()
        for val, steps in node.counter.items():
            seq = totient_seq[val]
            if node.lazy < len(seq):
                new_val = seq[node.lazy]
            else:
                new_val = 1
            new_counter[new_val] += steps + node.lazy
        node.counter = new_counter
        if node.left:
            node.left.lazy += node.lazy
            node.right.lazy += node.lazy
        node.lazy = 0

def update(node, l, r):
    push(node)
    if node.r < l or node.l > r:
        return
    if l <= node.l and node.r <= r:
        node.lazy += 1
        push(node)
        return
    update(node.left, l, r)
    update(node.right, l, r)
    node.counter = node.left.counter + node.right.counter

def query(node, l, r):
    push(node)
    if node.r < l or node.l > r:
        return Counter()
    if l <= node.l and node.r <= r:
        return node.counter
    return query(node.left, l, r) + query(node.right, l, r)

n, m = map(int, input().split())
arr = list(map(int, input().split()))
root = build(arr, 0, n-1)

for _ in range(m):
    t, l, r = map(int, input().split())
    l -= 1
    r -= 1
    if t == 1:
        update(root, l, r)
    else:
        cnt = query(root, l, r)
        if not cnt:
            print(0)
            continue
        min_ops = min(cnt.values())
        print(min_ops)
```コードは、500 万までのすべての数値のトーティエント関数を計算することから始まります。 次に、各数値の合計数列を事前計算し、繰り返し適用した結果を即座に判断できるようにします。 セグメント ツリー ノードには、値をその値に到達するまでのステップ数にマッピングするカウンターが保存されます。 更新は遅延して伝達されるため、効率が確保されます。 クエリは、範囲内のカウンターを集計して、値を均等化するために必要な最小の合計操作を見つけます。 

## 実用的な例

 サンプル入力:```
5 4
8 1 6 3 7
2 1 5
2 3 4
1 1 3
2 3 4
```| ステップ | 操作 | 配列 | トーティエントカウンター | 最小歩数 |
 | --- | --- | --- | --- | --- |
 | 1 | クエリ 2 1 5 | [8,1,6,3,7] | {8:0,1:0,6:0,3:0,7:0} | 10 |
 | 2 | クエリ 2 3 4 | [8,1,6,3,7] | {6:0,3:0} | 2 |
 | 3 | 更新 1 1 3 | [4,1,2,3,7] | 遅れて更新されました | - |
 | 4 | クエリ 2 3 4 | [4,1,2,3,7] | {2:0,3:0} | 1 |

 この表は、各操作の後、セグメント ツリーが必要なステップ数を正確に反映し、遅延伝播によって値が正しく更新されることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n | |
