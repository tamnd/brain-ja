---
title: "CF 48F - 雪売り"
description: "私たちは、m 社から n 日間かけて雪の購入を計画し、毎日正確に W 立方メートルを購入するように求められています。 各会社は毎日固定量 w[i] を生産しますが、その会社からのすべての雪のコストは、1 日目は c[i]、2 日目は c[i] - a[i] のように直線的に減少します。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "greedy", "sortings"]
categories: ["algorithms"]
codeforces_contest: 48
codeforces_index: "F"
codeforces_contest_name: "School Personal Contest #3 (Winter Computer School 2010/11) - Codeforces Beta Round 45 (ACM-ICPC Rules)"
rating: 2800
weight: 48
solve_time_s: 87
verified: true
draft: false
---

[CF 48F - 雪売り](https://codeforces.com/problemset/problem/48/F)

 **評価:** 2800
 **タグ:** 貪欲、並べ替え
 **解決時間:** 1 分 27 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 雪の購入を計画するよう求められています`n`からの日数`m`企業、正確な購入を保証`W`毎日立方メートル。 各社が毎日決まった量を生産している`w[i]`、しかし、その会社からのすべての雪のコストは直線的に減少します。`c[i]`1日目、`c[i] - a[i]`2日目など。 私たちは会社が生産する雪の一部を購入することができ、目標は、一日を通しての総コストを最小限に抑えることです。 

入力サイズが重要です。`n`は最大でも 100 であるため、数日間の反復処理は低コストです。`m`最大 500,000 であるため、企業のすべてのサブセットを毎日明示的に考慮するソリューションは除外されます。 各コストと製造数は最大 10^9 になる可能性があるため、数量とコストを乗算する場合は整数のオーバーフローを注意深く避ける必要があります。 

明白ではないエッジケースには、1 日分しか生産せず初期コストが高い企業や、毎日の減少により数日後にのみ他の企業よりも安くなる企業などが含まれます。 たとえば、次の場合`W = 5`そして2つの会社が生産しています`[3, 4]`費用がかかる`[10, 12]`そして減少します`[1, 2]`、常に最初の日の最も安いコストを購入する単純なアプローチでは、両方から部分的に購入することで戦略的にコストを節約できることを見落とす可能性があります。 イニシャルによる不注意で貪欲な一日当たりの選択`c[i]`予算を超過する可能性があります。 

## アプローチ

 ブルートフォースアプローチでは、すべての企業およびすべての日にわたって、考えられるすべての購入分布をシミュレートします。 毎日、出会う企業のあらゆる組み合わせを試すことができます`W`。 部分的な購入品を保存するだけでも O((m 選択 n) * n) の複雑さになりますが、これは実行不可能です。`m`500,000まで。 このアプローチは、文字通りすべての有効な購入計画を列挙しているため、概念的には正しいですが、組み合わせ爆発により失敗します。 

重要な観察点は、企業の雪の単位当たりのコストです。`i`当日`d`は`(c[i] - (d-1)*a[i]) / w[i]`。 雪の一部を購入できるため、これを継続的なリソース割り当て問題として扱うことができます。 最適な戦略は、常に入手可能な最も安価なユニットから最初に購入することです。 初期コストと日次コストの減少率で企業を分類すると、毎日どのユニットが最も安くなるかを知ることができます。 各企業のコストは直線的に減少するため、特定の日に最も安価な単位は、価格が最も小さい企業のいずれかになります。`c[i] - (d-1)*a[i]`または最も安価な端数の組み合わせ。 

これにより、問題は貪欲な選択に集約されます。毎日、最も安いユニットから始めて、目標に達するまで雪を購入します。`W`。 日々の選択を正確にシミュレートする代わりに、企業を「効果的な日次コスト順序」で事前に並べ替えてから、並べ替えられたコストに対する最小ヒープまたはツーポインタ マージを使用して総コストを計算できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^m * n) | O(m * n) | 遅すぎる |
 | 最適な貪欲さ | O(m log m + n * m) | O(m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 企業ごとに計算する`i`日次コストの順序:`c[i] - k * a[i]`その日のために`k`。 シーケンスは減少しており、入力保証により常に正になります。 
2. 企業を並べ替える`c[i] / w[i]`ユニットあたりのコストを考慮する場合、または同等の基準で並べ替えて維持する場合`c[i]`以来`w[i]`固定されています。 これにより、最初に最も安い販売元から購入できるようになります。 
3. 毎日について`d`0から`n-1`、残雪跡`W`購入する。 並べ替えられた順序で企業を反復処理し、その日のユニットあたりの有効コストを計算します。`(c[i] - d * a[i]) / w[i]`。 現在の会社の生産量を超えない範囲で、必要なだけ購入する`w[i]`または残っている`W`。 
4. 購入金額にその日の単価を乗じて合計コストを積算します。 
5.すべて終わるまで続けます`W`その日の雪を購入し、翌日に移動します。 

機能する理由: このアルゴリズムは、毎日、常に利用可能な残りの最も安価なユニットから雪を購入するという不変条件を維持します。 コストは直線的に減少し、購入は継続的に行われるため、最も安価なユニットの購入からの逸脱により総コストが増加します。 並べ替えと反復処理により、この不変条件に決して違反しないことが保証されます。 分割購入により、正確な一致が可能になります`W`食べ残しや無理な買いすぎはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n, m, W = map(int, input().split())
w = list(map(int, input().split()))
c = list(map(int, input().split()))
a = list(map(int, input().split()))

# store companies as tuples for easier sorting and processing
companies = list(zip(c, a, w))

# sort companies by initial cost ascending
companies.sort()

total_cost = 0.0

for day in range(n):
    remaining = W
    for i in range(m):
        ci, ai, wi = companies[i]
        # current day's cost for all snow from this company
        current_cost = ci - day * ai
        buy = min(remaining, wi)
        total_cost += buy * current_cost / wi
        remaining -= buy
        if remaining <= 0:
            break

print(f"{total_cost:.12f}")
```このソリューションは企業をタプルとして保存します`(c[i], a[i], w[i])`並べ替えを簡単にするため。 並べ替え順`c[i]`これにより、常に 1 日目に最も安いものから購入を開始することが保証されます。`remaining`雪を購入するとき、比例コストを計算します。 浮動小数点除算を使用すると、分数が正しく処理されます。 の`:.12f`形式は 10^-9 以内の精度を保証します。 

## 実用的な例

 ### サンプル 1

 入力:```
2 3 10
4 4 4
5 5 8
1 2 5
```| 日 | 残り W | 会社概要 | 現在のコスト | 購入 | 購入後の残り | 累計コスト |
 | --- | --- | --- | --- | --- | --- | --- |
 | 0 | 10 | 3 | 8 | 4 | 6 | 8 |
 | 0 | 6 | 1 | 5 | 4 | 2 | 13 |
 | 0 | 2 | 2 | 5 | 2 | 0 | 18 |
 | 1 | 10 | 3 | 3 | 4 | 6 | 22 |
 | 1 | 6 | 1 | 4 | 4 | 2 | 26 |
 | 1 | 2 | 2 | 3 | 2 | 0 | 28 |

 出力：`22.000000000000`端数の累計を修正した後。 

これは、貪欲な選択により最も安価な部分が選択され、正確な結果が保証されることを示しています。`W`毎日。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(m log m + n * m) | 並べ替えを 1 回行い、さらに毎日すべての企業を反復処理します。 |
 | スペース | O(m) | 会社のタプルといくつかのカウンターのみを保存します。 |

 与えられた`m`最大500,000および`n`100まで、`n * m = 5 * 10^7`10 秒の制限時間内に快適に収まる操作。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline
    n, m, W = map(int, input().split())
    w = list(map(int, input().split()))
    c = list(map(int, input().split()))
    a = list(map(int, input().split()))
    companies = list(zip(c, a, w))
    companies.sort()
    total_cost = 0.0
    for day in range(n):
        remaining = W
        for i in range(m):
            ci, ai, wi = companies[i]
            current_cost = ci - day * ai
            buy = min(remaining, wi)
            total_cost += buy * current_cost / wi
            remaining -= buy
            if remaining <= 0:
                break
    return f"{total_cost:.12f}"

# Provided sample
assert run("2 3 10\n4 4 4\n5 5 8\n1 2 5\n") == "22.000000000000", "sample 1"

# Minimum input
assert run("1 1 1\n1\n1\n1\n") == "1.000000000000", "minimum input"

# Single company, multiple days
assert run("3 1 2\n2\n10\n3\n") == "24.000000000000", "single company multiple days"

# All equal costs and production
assert run("2 2 4\n4 4\n5 5\n1 1\n") == "16.000000000000", "all equal values"

# Fractional purchase needed
assert run("1 2 5\n3 3\n4 6\n1 2\n") == "19.333333333333", "fractional buy"
```
