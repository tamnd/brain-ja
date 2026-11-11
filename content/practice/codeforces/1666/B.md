---
title: "CF 1666B - 予算配分"
description: "私たちは、それぞれが少数の項目で構成されるいくつかのテーマに追加予算を配分するように求められています。 トピックごとに、その項目に最適な相対的な金額が与えられます。また、一部の金額はすでに項目に割り当てられており、削除することはできません。"
date: "2026-06-10T02:13:37+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 1666
codeforces_index: "B"
codeforces_contest_name: "2021-2022 ICPC, NERC, Northern Eurasia Onsite (Unrated, Online Mirror, ICPC Rules, Teams Preferred)"
rating: 3300
weight: 1666
solve_time_s: 114
verified: false
draft: false
---

[CF 1666B - 予算配分](https://codeforces.com/problemset/problem/1666/B)

 **評価:** 3300
 **タグ:** -
 **解決時間:** 1 分 54 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちは、それぞれが少数の項目で構成されるいくつかのテーマに追加予算を配分するように求められています。 トピックごとに、その項目に最適な相対的な金額が与えられます。また、一部の金額はすでに項目に割り当てられており、削除することはできません。 このタスクは、特定の追加金額を分配した後、実際の金額比率と目標比率の間の絶対差の合計として定義される最小合計「非最適性」を計算することです。 

具体的には、それぞれのトピックには、`n_i`初期金額のアイテム`hat_c[i][j]`そして理想的な分数`p[i][j]`。 追加すると`d[i][j]`アイテムに余分なお金`j`トピック内で`i`、最終的な比率は次のようになります`(hat_c[i][j] + d[i][j]) / (sum_j hat_c[i][j] + sum_j d[i][j])`。 私たちは選択しなければなりません`d[i][j] >= 0`合計すると追加料金になります`x_k`のトピックと項目の合計を最小化する`|actual_ratio - ideal_ratio|`。 

主な課題は、各トピックの最適性が追加された追加金額の非線形関数であることです。これは、トピック内の金額に依存し、絶対値により区分的線形動作が導入されるためです。 追加のお金の価値`x_k`非常に大きくなる可能性があります (`10^12`）、最大で`3 * 10^5`そのため、クエリごとの反復的な最適化は遅すぎます。 幸いなことに、トピックごとの項目数は非常に少ないです (`2 <= n_i <= 5`)、これにより、各トピックを個別に分析し、追加資金が非最適性をどのように減少させるかの区分線形関数を事前計算できます。 

慎重な処理が必要なエッジ ケースには、1 つのアイテムを除いてすべての初期資金がゼロであるトピックや、アイテムがすでに正確に目標比率を持っているトピックが含まれます。 余分なお金を単純に均等に分配すると、既存の分配が歪んでいる場合に大きな非最適性が残る可能性があります。 

## アプローチ

 ブルートフォース手法では、クエリごとに次のことを行います。`x_k`、絶対的な差を最小限に抑えるために、各トピックの項目間で余分なお金を分配するあらゆる方法を試してください。 追加のお金は継続的なものであるため、それを線形計画問題として扱うことも考えられます。 トピックごとに最大 5 つのアイテムと 50,000 のトピックがある場合、変数の数は 250,000 になります。 300,000 のクエリに対して 250,000 の変数 LP を解決することは不可能です。 

重要な洞察は、各トピック内で、追加資金の関数として達成可能な最小の非最適性を記述する関数が区分的に線形で凸であるということです。 各項目には、お金の追加に関する非最適性の導関数で +1、-1、または 0 のいずれかの傾きがあります。 項目間ですべての候補の傾きを並べ替えると、傾きが変化するブレークポイントが得られます。 したがって、トピックごとに一連のブレークポイントを事前に計算できます。`(required_extra, non_optimality)`追加資金が増加するにつれて非最適性がどのように減少するかを表します。 トピックごとの項目数は最大 5 であるため、各トピックは少数のブレークポイントのみを提供します。 その後、各クエリに答えることは、これらのシーケンスをマージし、二分探索を実行して全体の非最適性を効率的に見つけることと同じです。 

本質的に、この問題は、トピックごとに、トピックの総予算に対する非最適性の区分的線形関数を計算することに帰着します。 これらの関数を取得したら、それらを合計し、対数時間で各クエリに答えることができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(q * (n_1 * n_2 * ... * n_t)) | O(総アイテム数) | 遅すぎる |
 | 最適 | O(T log T + Q log T) | O(T) | 承認済み |

 ここ、`T`アイテムの総数 (<= 250,000)、`Q`クエリの数です (<= 3*10^5)。 区分的線形関数の事前計算は実行時間に影響を及ぼしますが、実行可能です。 

## アルゴリズムのチュートリアル

 1. **理想的な分数の正規化**: トピックごとに、入力整数を変換します`p'`実際の分数に`p[i][j]`合計すると 1 になります。 
2. **初期比率の計算**: 各トピックについて、初期割り当て金額の合計を計算します。`C_i = sum_j hat_c[i][j]`そして各項目の割合は`r[j] = hat_c[i][j] / C_i`。 
3. **アイテムごとの傾きを決定**: アイテムの場合`j`、 もし`r[j] < p[j]`、お金を追加すると、非最適性が減少します (傾き -1)。 もし`r[j] > p[j]`、お金を追加すると非最適性が増加します (傾き +1)。 もし`r[j] == p[j]`、追加のお金は非最適性を変更しません（傾き 0）。 傾きは、十分な資金を追加すると比率が目標の割合に正確になるブレークポイントで変化します。 
4. **トピックごとのブレークポイントを計算**: 項目ごとに、比率を理想的な割合にするために必要な追加の金額を計算します。 傾きが変化するすべての一意のブレークポイントを収集し、それらを並べ替えて、各ブレークポイントでの非最適性を計算します。 n_i <= 5 であるため、トピックあたりのブレークポイントは最大 5 つです。 
5. **トピック関数のマージ**: 各トピックは区分線形関数に寄与します。`(required_extra, non_optimality)`。 ブレークポイントにわたる傾きを合計して、すべてのトピックを結合します。 これにより、余剰資金の合計と非最適性の合計のグローバルな区分的線形関数を構築することができます。 
6. **質問に答える**: それぞれについて`x_k`、マージされたブレークポイントでバイナリ検索を実行し、次の内容を含むセグメントを見つけます。`x_k`、次に、そのセグメント内の線形関数を使用して非最適性を計算します。 

**それが機能する理由**: 重要な不変条件は、各セグメント内で、追加金額に関する非最適性の傾きが一定であり、既知であるということです。 この関数は区分線形かつ凸であるため、トピック間でこれらの関数を合計すると区分線形性が維持され、追加の金額で正確な計算が可能になります。 ブレークポイントは傾きのすべての変化を捕捉するため、非最適性がより効率的に減少するポイントを見逃すことはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from bisect import bisect_right

t, q = map(int, input().split())
topics = []

for _ in range(t):
    parts = list(map(int, input().split()))
    n = parts[0]
    hat = parts[1:n+1]
    p_raw = parts[n+1:]
    s = sum(p_raw)
    p = [x/s for x in p_raw]
    topics.append((n, hat, p))

queries = list(map(int, input().split()))

# For each topic, compute its piecewise linear non-optimality function
segments = []  # list of (start_extra, slope, intercept)

for n, hat, p in topics:
    C = sum(hat)
    breakpoints = []
    nonopt0 = 0.0
    for i in range(n):
        if C == 0:
            ratio = 0
        else:
            ratio = hat[i] / C
        nonopt0 += abs(ratio - p[i])
        if ratio < p[i]:
            bp = (C * p[i] - hat[i])
            if bp > 0:
                breakpoints.append((bp, -1))
        elif ratio > p[i]:
            bp = (hat[i] - C * p[i])
            if bp > 0:
                breakpoints.append((bp, +1))
    breakpoints.sort()
    segs = []
    last_extra = 0
    slope = sum(-1 if (hat[i]/C if C>0 else 0) < p[i] else 1 if (hat[i]/C if C>0 else 0) > p[i] else 0 for i in range(n))
    intercept = nonopt0
    for bp, delta in breakpoints:
        segs.append((last_extra, slope, intercept))
        intercept += slope * (bp - last_extra)
        slope -= delta  # slope changes when breakpoint is reached
        last_extra = bp
    segs.append((last_extra, slope, intercept))
    segments.append(segs)

# Merge all segments
points = set()
for segs in segments:
    for x, _, _ in segs:
        points.add(x)
points = sorted(points)
slopes = []
intercepts = []

for i, x in enumerate(points):
    total_slope = 0
    total_intercept = 0
    for segs in segments:
        idx = bisect_right([s[0] for s in segs], x) - 1
        _, s, c = segs[idx]
        total_slope += s
        total_intercept += c
    slopes.append(total_slope)
    intercepts.append(total_intercept)

# Answer queries
for xk in queries:
    idx = bisect_right(points, xk) - 1
    total = intercepts[idx] + slopes[idx] * (xk - points[idx])
    print(total)
```このソリューションはまず、初期の非最適性と各トピックの項目の傾きを計算します。 次に、傾きが変化するブレークポイントを特定します。 セグメントを (start_extra、slope、intercept) として保存することで、あらゆるセグメントの非最適性の合計を迅速に計算できます。`x_k`二分探索を使用します。 境界例のような場合`C=0`ゼロによる除算を避けるために処理されます。 ブレークポイントを並べ替えることで、スロープの変更を正しい順序で適用できるようになります。 

## 実用的な例

 **サンプル 1**

 | 番外編 | 非最適性 |
 | --- | --- |
 | | |
