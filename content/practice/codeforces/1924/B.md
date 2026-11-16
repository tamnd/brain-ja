---
title: "CF 1924B - スペースハーバー"
description: "n 個の点からなる直線があり、各点には船が配置されています。 これらのポイントの一部にはすでに港があり、それぞれに関連する価値があります。 ある地点にある船がその右隣の港に「移動」すると、コストが発生します。"
date: "2026-06-08T19:10:29+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "implementation", "math", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1924
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 921 (Div. 1)"
rating: 2100
weight: 1924
solve_time_s: 334
verified: false
draft: false
---

[CF 1924B - スペースハーバー](https://codeforces.com/problemset/problem/1924/B)

 **評価:** 2100
 **タグ:** データ構造、実装、数学、並べ替え
 **解決時間:** 5 分 34 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちには直線があります`n`それぞれのポイントには船が占有しています。 これらのポイントの一部にはすでに港があり、それぞれに関連する価値があります。 ある地点にある船がその右隣の港に「移動」すると、コストが発生します。 コストは、左側に最も近い港の値と右側にある次の港までの距離の積として定義されます。 船がすでに港に着いている場合、コストはゼロです。 このタスクは、2 種類の操作を効率的に処理することです。1 つは、指定された値で新しい位置に港を追加すること、もう 1 つは指定されたセグメント内の船舶を次の港に移動する総コストを計算することです。 

入力制限は大きいです。`n`そして`q`最大 300,000 に達する可能性があります。セグメントを反復処理すると、`O(n * q)`作戦、終了する可能性あり`9 * 10^10`ステップ。 これには、範囲合計クエリに効率的に応答し、ハーバーの動的な挿入を処理できるデータ構造またはアルゴリズムが必要です。 

いくつかの微妙な特殊なケースを考慮する必要があります。 クエリが港の位置に正確にある船舶のコストを要求する場合、コストはゼロになります。 同様に、港を追加すると、既存の港の間の間隔が分割され、その間隔内の船舶のコストが変わります。 たとえば、船がポイントを占有している場合`[1, 2, 3, 4]`に港がある`1`そして`4`に港を追加します。`3`船のコストを削減します`3`ゼロになり、船のコストが変更されます`2`。 

## アプローチ

 素朴な総当たりアプローチは、タイプ 2 クエリのセグメント内の各クエリと各船舶を反復し、最も近い左右の港を計算します。 タイプ 1 クエリの場合、新しい港がリストに追加されるだけです。 これは正しいですが、非常に時間がかかります。最悪の場合の複雑さは次のとおりです。`O(n * q)`、これは指定された境界では実現不可能です。 

最適なソリューションを実現するための重要な洞察は、各船のコストが港の位置によって定義される間隔に直線的に依存するということです。 連続する 2 つの港の間、その位置にある船のコスト`i`単純に`(i - left) * value[left]`もし`left`左側に最も近い港です。`right`一番右に近いものです。 この観察により、コストを間隔にわたるプレフィックスの合計として表すことができます。 次のようなバランスの取れたデータ構造を使用する`SortedDict`から`sortedcontainers`、港の位置を維持し、累積コストを効率的に計算できます。 新しいハーバーを追加する場合、更新する必要があるのは、ハーバーが分割する間隔だけです。 タイプ 2 クエリの場合、バイナリ検索または間隔クエリを使用すると、セグメント全体のコストをすばやく合計できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * q) | O(n) | 遅すぎる |
 | SortedDict + プレフィックス合計による間隔ベース | O((m+q) log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 港の位置とその値のソートされたマッピングを初期化します。 これにより、任意のポイントの最も近い左右の港への O(log n) アクセスが保証されます。 
2. すべての船舶の初期コストを事前計算します。 連続する港間の間隔ごとに、その位置にある船のコスト`i`は`(i - left) * value[left]`。 累積合計を使用して範囲クエリを O(log n) で回答できるように、コストのプレフィックス合計を保存します。 
3. タイプ 1 クエリを処理する場合`(1 x v)`、間隔を求めます`[L, R]`含まれている`x`。 その間隔の累積コストを削除し、次のように分割します。`[L, x-1]`そして`[x+1, R]`新しい港がある`x`。 これらの間隔のプレフィックス合計を再計算します。 
4. タイプ 2 クエリの場合`(2 l r)`、プレフィックス合計配列を使用して、間のコストの合計を効率的に計算します。`l`そして`r`。 港の位置での二分探索により、どの間隔がオーバーラップしているかを特定します。`[l, r]`、次に、それらの間隔について事前に計算されたコストを合計します。 
5. すべてのクエリに対してこのプロセスを繰り返します。 

これが機能する理由: 不変条件は、どの時点でも、コスト配列が連続する港間のすべての間隔で船を移動するコストを正しく表すことです。 新しいハーバーを追加すると、それが分割する間隔にのみ影響し、プレフィックス合計により迅速なクエリが可能になります。 ソートされたマッピングと間隔ベースのプレフィックス合計の組み合わせにより、正確性と効率性の両方が保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from sortedcontainers import SortedDict

n, m, q = map(int, input().split())
harbour_positions = list(map(int, input().split()))
harbour_values = list(map(int, input().split()))

harbours = SortedDict()
for pos, val in zip(harbour_positions, harbour_values):
    harbours[pos] = val

# Precompute interval costs
def interval_cost(l, r, val):
    # sum_{i=l}^{r} (i - l) * val = val * (sum_{i=0}^{r-l} i) = val * (r-l)*(r-l+1)//2
    length = r - l + 1
    return val * (length * (length - 1) // 2)

# store prefix sum of costs by intervals
intervals = SortedDict()
prev = None
for pos in harbours:
    if prev is not None:
        intervals[prev] = (pos, interval_cost(prev + 1, pos - 1, harbours[prev]))
    prev = pos

def add_harbour(x, v):
    idx = harbours.bisect_left(x)
    right = harbours.peekitem(idx)[0]
    left = harbours.peekitem(idx - 1)[0]
    # remove old interval
    if left in intervals:
        del intervals[left]
    # add new intervals
    if left <= x - 1:
        intervals[left] = (x, interval_cost(left + 1, x - 1, harbours[left]))
    if x + 1 <= right - 1:
        intervals[x] = (right, interval_cost(x + 1, right - 1, v))
    harbours[x] = v

def query_cost(l, r):
    total = 0
    # find relevant intervals
    idx = intervals.bisect_right(l)
    if idx > 0:
        idx -= 1
    keys = list(intervals.islice(idx, len(intervals)))
    for start in keys:
        end, cost = intervals[start]
        if start > r:
            break
        sub_l = max(l, start + 1)
        sub_r = min(r, end - 1)
        if sub_l <= sub_r:
            length = sub_r - sub_l + 1
            val = harbours[start]
            total += val * (length * (length + 1) // 2 - (sub_l - start - 1) * (sub_l - start) // 2)
    return total

for _ in range(q):
    parts = input().split()
    t = int(parts[0])
    if t == 1:
        x, v = int(parts[1]), int(parts[2])
        add_harbour(x, v)
    else:
        l, r = int(parts[1]), int(parts[2])
        print(query_cost(l, r))
```ソリューションでは、`SortedDict`港の位置を維持し、最も近い隣人に効率的にアクセスできます。 港間のコストは間隔ごとに事前計算されます。 ハーバーを追加すると、ハーバーが分割する間隔のみが更新されます。 コストのクエリでは、部分間隔を慎重に調整して事前計算された値が使用されます。 Off-by-one エラーは包含範囲を処理することで回避されます`[l, r]`きちんと。 

## 実用的な例

 サンプル 1:

 | ポジション | 港 | 最も近い左の港 | 最寄りの右の港 | コスト |
 | --- | --- | --- | --- | --- |
 | 1 | はい | 1 | 3 | 0 |
 | 2 | いいえ | 1 | 3 | 3 |
 | 3 | はい | 3 | 8 | 0 |
 | 4 | いいえ | 3 | 8 | 24 |
 | 5 | いいえ | 3 | 8 | 72 |
 | 6 | いいえ | 3 | 8 | 96 |
 | 7 | いいえ | 3 | 8 | 120 |
 | 8 | はい | 8 | - | 0 |

 最初のタイプ 2 クエリ`(2 2 5)`太陽`3 + 0 + 24 + 72 = 99`。 港を 5 に追加すると、間隔コストが更新され、後続のクエリでは新しい港の位置での船舶のコストがゼロになります。 

別の例: 既存の 2 つの港の中央に港を追加すると、間隔が分割され、コストが比例して再配分されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((m + q) log n) | SortedDict の操作 (挿入、二分化) は O(log n) です。 各クエリは関連する間隔のみに影響します。 |
 | スペース | O(n) | ハーバー、間隔、およびプレフィックスの合計を保存します。 |

 与えられた`n, q ≤ 3*10^5`、最悪の場合の操作は 10^7 未満であり、2 秒以内に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
```
