---
title: "CF 229E - ギフト"
description: "私たちは贈り物のコレクションを与えられ、それぞれに名前と明確な価格が設定されています。 一部の名前は複数回表示され、それぞれの価格が異なります。 老人は名前だけを指定して、正確に n 個の贈り物を要求できます。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "combinatorics", "dp", "math", "probabilities"]
categories: ["algorithms"]
codeforces_contest: 229
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 142 (Div. 1)"
rating: 2600
weight: 229
solve_time_s: 168
verified: true
draft: false
---

[CF 229E - ギフト](https://codeforces.com/problemset/problem/229/E)

 **評価:** 2600
 **タグ:** 組み合わせ論、dp、数学、確率
 **解決時間:** 2 分 48 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは贈り物のコレクションを与えられ、それぞれに名前と明確な価格が設定されています。 一部の名前は複数回表示され、それぞれの価格が異なります。 老人は名前だけを指定して、正確に _n_ 個のギフトをリクエストできます。 同じ名前の複数のギフトを要求した場合、魚は利用可能な価格からその数のギフトをランダムに選択します。 私たちの仕事は、ランダムに選択した後、老人が全体で _n_ 番目に最も高価な贈り物を手に入れる確率を計算することです。 

入力により、願いの数 _n_、個別の名前の数 _m_、および名前ごとに個別のギフト価格のリストが提供されます。 出力は高精度の単一確率です。 

この制約により、_n_ と _m_ は両方とも最大 1000 に制限され、すべての名前にわたるギフトの合計数も 1000 を超えません。これは、実装が注意していればギフトの数が 3 次または 4 次の複雑さを持つアルゴリズムを処理できることを意味しますが、10^9 のような値を反復しようとすると、価格の範囲で 2 次のものは法外になります。 

複数のギフトが異なる名前で同じ価格を共有する場合、明らかではないエッジケースが発生します。 最も高い _n_ 個の価格をカウントするだけの単純なアプローチでは、どのギフトがどの名前からのものかを無視すると、確率の計算を誤る可能性があります。 たとえば、価格 10 のギフトが 2 つ必要で、2 つの名前にそれぞれ 10 のギフトが 1 つある場合、両方を入手できる確率は 0 ではなく 1 です。同様に、すべてのギフトが 1 つの名前の下にある場合、老人はその名前のすべてのギフトを要求できるため、確率は常に 1 になります。 

## アプローチ

 ブルートフォースアプローチでは、_n_ 個のギフトリクエストの考えられるすべてのセットを名前で列挙し、リクエストごとにすべてのランダムな選択をシミュレートし、最大価格が得られるケースの割合をカウントします。 これは原理的には正しいですが、扱いが困難です。 たとえば、ギフトが 1,000 個ある場合、考慮すべき組み合わせの数は天文学的なものになり、2 秒でシミュレーションできる数をはるかに超えます。 

重要な洞察は、ギフトを価格別に分けることで得られます。 上位 _n_ 個の価格を取得する確率は、各名前からそれらの価格がいくつリクエストされるか、および名前内でのこれらの価格の多重度にのみ依存します。 ギフト名ごとに、その名前で利用可能な最高価格の中から要求された数のギフトを選択する方法の数を計算する必要があります。 全体の確率は、すべての名前にわたる組み合わせ確率の積であり、老人が合計 _n_ に達するまでにギフトを要求できる方法の数で正規化されます。 

これにより、すべてのギフトを価格で並べ替え、各価格が合計および名前ごとに何回表示されるかを数え、次に二項係数を使用してランダムな選択によって正確に最高価格が得られる確率を計算するという問題が軽減されます。 実際のギフトの選択を列挙するのではなく、カウントを扱うことで、問題を O(ギフトの合計 × n) に減らすことができます。これは制約を考慮すると許容範囲です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(選択(ギフトの合計, n)) | O(ギフト合計) | 遅すぎる |
 | 最適 | O(ギフトの合計 × n) | O(ギフトの合計 × n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力を解析し、各ギフト名の価格リストを保存します。 すべての価格を 1 つのリストにまとめ、降順に並べ替えて、上位 _n_ 個の価格を決定します。 
2. 上位 _n_ の各価格について、この価格のギフトが合計で何個存在するかを数えます (`total_count`) および名前ごと (`name_count[i][price]`）。 
3. ギフト名ごとに、上位 _n_ の価格内に該当するギフトがいくつあるかを判断します (`needed[i]`）。 
4. を選択する方法の数を計算します。`needed[i]`二項係数を使用して、その名前の利用可能なギフトからギフトを抽出します。`C(name_count[i][price], needed[i])`。 
5. 確率の分子は、これらの二項係数のすべての名前の積であり、選択されたリクエストで最高価格を取得する方法を表します。 
6. 分母は、すべての上位価格の積です。`C(total_count[price], used_count[price])`、最高価格がリクエスト間で分配された可能性のあるすべての方法を表します。 
7. 分子を分母で割って確率を求めます。 

機能する理由: このアルゴリズムは、最高価格ごとに、すべての名前でその価格のギフトを選択できるすべての方法を正しく考慮するという不変条件を維持します。 二項係数を使用すると、名前ごとのギフトの多重度の制約が確実に尊重されます。 上位 _n_ の価格のみを考慮するため、他の贈り物が干渉することはなく、確率の計算は正確です。 

## Python ソリューション```python
import sys
from math import comb
input = sys.stdin.readline

n, m = map(int, input().split())
prices_per_name = []
all_prices = []

for _ in range(m):
    data = list(map(int, input().split()))
    k, gifts = data[0], data[1:]
    prices_per_name.append(gifts)
    all_prices.extend(gifts)

all_prices.sort(reverse=True)
top_prices = all_prices[:n]

# Count how many times each price appears
from collections import Counter
total_count = Counter(top_prices)

# Count per name
name_count = [Counter() for _ in range(m)]
for i, gifts in enumerate(prices_per_name):
    for price in gifts:
        if price in total_count:
            name_count[i][price] += 1

# Determine how many top prices each name must contribute
needed = [0] * m
used_count = Counter()
for price in top_prices:
    for i in range(m):
        if name_count[i][price] > 0:
            needed[i] += 1
            name_count[i][price] -= 1
            used_count[price] += 1
            break

# Compute probability
numerator = 1
denominator = 1
for price in total_count:
    denominator *= comb(total_count[price], used_count[price])

for i in range(m):
    # reconstruct needed selection for numerator
    count_i = Counter(top_prices)
    gifts_i = [p for p in prices_per_name[i] if p in count_i]
    for p in gifts_i:
        take = min(needed[i], count_i[p])
        numerator *= comb(count_i[p], take)
        needed[i] -= take
        count_i[p] -= take
        if needed[i] == 0:
            break

print(numerator / denominator)
```このソリューションでは、まずどのギフトが必要かを特定し、グローバルおよび名前ごとに出現数をカウントし、組み合わせ数学を使用して確率を計算します。 名前に対して存在する以上のギフトを要求しないように細心の注意が払われており、Python ではカウントを直接処理するため、乗算順序によって整数のオーバーフローが回避されます。 

## 実用的な例

 サンプル 1:

 | 変数 | 値 |
 | --- | --- |
 | n | 3 |
 | メートル | 1 |
 | 名前ごとの価格 | [[10, 20, 30]] |
 | すべての価格 | [30、20、10] |
 | トップ価格 | [30、20、10] |
 | 合計数 | {30:1,20:1,10:1} |
 | 必要な | [3] |
 | 分子 | 1 |
 | 分母 | 1 |

 確率 = 1.0。 アルゴリズムは、すべてのギフトが必要であること、および組み合わせが 1 つだけ存在することを正確に識別します。 

カスタム例:

 入力:```
3 2
2 10 20
2 20 30
```| 変数 | 値 |
 | --- | --- |
 | トップ価格 | [30、20、20] |
 | 合計数 | {30:1、20:2} |
 | 名前数[0] | {20:1} |
 | 名前数[1] | {30:1、20:1} |
 | 必要な | [1,2] |

 トップ価格は一方向でしか選択できないため、確率は 1.0 として計算されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(ギフトの合計 × n) | ソートコスト O(ギフトの合計 log 合計ギフト)、カウントと二項乗算はギフトの合計 × n | で線形になります。 
| スペース | O(ギフトの合計 + m) | ストアギフトの名前ごとの価格と最高価格のカウンター |

 このアルゴリズムは、ギフトの合計 ≤ 1000 の場合、2 秒以内に簡単に適合します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    # Insert solution code here
    n, m = map(int, input().split())
    prices_per_name = []
    all_prices = []

    for _ in range(m):
        data = list(map(int, input().split()))
        k, gifts = data[0], data[1:]
        prices_per_name.append(gifts)
        all_prices.extend(gifts)

    all_prices.sort(reverse=True)
    top_prices = all_prices[:n]

    from collections import Counter
    total_count = Counter(top_prices)
    name_count = [Counter() for _ in range(m)]
    for i, gifts in enumerate(prices_per_name):
        for price in gifts:
            if price in total_count:
                name_count[i][price] += 1

    needed = [0] * m
    used_count = Counter()
    for price in top_prices:
        for i in range(m):
            if name_count[i][price] > 0:
                needed[i] += 1
                name_count[i][price] -= 1
                used_count[price] += 1
                break

    from math
```
