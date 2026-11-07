---
title: "CF 139B - 壁紙"
description: "各部屋が直方体であるアパートのすべての壁に紙を貼るための最小コストを計算するように求められました。 各部屋には、長さ、幅、高さが与えられます。 部屋の周囲の長さによって、必要な壁紙の数が決まります。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation", "math"]
categories: ["algorithms"]
codeforces_contest: 139
codeforces_index: "B"
codeforces_contest_name: "Codeforces Beta Round 99 (Div. 2)"
rating: 1600
weight: 139
solve_time_s: 87
verified: true
draft: false
---

[CF 139B - 壁紙](https://codeforces.com/problemset/problem/139/B)

 **評価:** 1600
 **タグ:** 実装、数学
 **解決時間:** 1 分 27 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各部屋が直方体であるアパートのすべての壁に紙を貼るための最小コストを計算するように求められました。 各部屋には、長さ、幅、高さが与えられます。 部屋の周囲の長さによって、必要な壁紙の数が決まります。 各タイプの壁紙は固定幅と長さのロールで提供され、ロールを回転させることはできません。 ストリップはロールに沿って垂直にカットされ、各部屋に 1 種類の壁紙を貼る必要があります。 部分的なロールは許可されますが、部屋間でロールを共有することはできません。 私たちの目標は、部屋ごとに、総コストを最小限に抑える壁紙の種類とロールの数を選択することです。 

制約は控えめです。部屋の数と壁紙の種類は最大 500 です。寸法と価格は 500 に制限されています。これにより、おおよその最大のアルゴリズムが許可されます。$O(n \cdot m)$複雑。 すべての入力数値は小さな整数であるため、浮動小数点の精度については心配ありませんが、ロールごとのストリップの数を計算するときにオフバイ 1 エラーを回避するには、慎重な整数演算が必要です。 

注意すべきエッジケースは、ロールの幅が部屋の周囲よりも大きいため、1 つのロールで十分な場合です。 もう 1 つのケースは、ロールの長さによって少数のストリップしか許容されず、複数のロールが必要な場合です。 たとえば、周囲が 6 メートル、高さが 5 メートルの部屋で、長さ 4 メートル、幅 2 メートルのロールを使用する場合、間違って 1 つではなく 2 つのロールを購入できるように、慎重に計算する必要があります。 

## アプローチ

 強引なアプローチは簡単です。部屋ごとに、壁紙の種類ごとに反復処理を行い、部屋を完全に覆うのに必要なロールの数を計算します。 ロールの数と価格を掛けて、その組み合わせのコストを求めます。 次に、部屋に最も安価な壁紙の種類を選択します。 すべての部屋にわたるこれらの最小コストを合計します。 これが機能するのは、各部屋が独立しているためです。 ただし、ロールごとのストリップの数と必要なロールの数を慎重に計算する必要があります。 と$n \le 500$部屋と$m \le 500$壁紙の種類に応じて、このアプローチでは最大でも$500 \cdot 500 = 250,000$操作は 2 秒の制限時間内で許容されます。 

計算を簡素化する重要な観察点は、1 つのロールで提供できるストリップの数は、ロールの長さを部屋の高さで割った整数であり、必要なストリップの数は、部屋の周囲をロールの幅で除算した整数で切り上げたものであるということです。 これら 2 つの整数除算を使用して、壁紙の種類と部屋の組み合わせに必要なロールの最小数を計算できます。 部屋は相互作用しないため、動的プログラミングやグラフ アルゴリズムは必要ありません。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * m) | お(1) | 承認済み |
 | 最適 | O(n * m) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力値を読み取ります: 部屋の数、部屋の寸法、壁紙の種類の数、および価格を含むロール仕様。 
2. 変数を初期化する`total_cost`ゼロに。 
3. 各部屋について、周囲を次のように計算します。`2 * (length + width)`そして高さを保存します。 
4.初期化`min_cost`現在の部屋を無限に拡張します。 
5. 壁紙の種類ごとに、ロールの長さを部屋の高さで整数で割ることにより、1 つのロールで提供される垂直ストリップの数を決定します。 
6. 周囲をロール幅で割って切り上げて、部屋に必要なストリップの総数を決定します。 
7. 上限として必要なロール数を計算します。`required_strips / strips_per_roll`。 
8. ロールの数と壁紙の種類の価格を掛けて、この壁紙の種類の合計コストを求めます。 
9. アップデート`min_cost`このコストが現在よりも低い場合`min_cost`。 
10. 部屋の壁紙の種類をすべて検討した後、次の項目を追加します。`min_cost`に`total_cost`。 
11. 印刷`total_cost`。 

正確さは 2 つの事実に基づいています。1 つは各部屋が 1 種類の壁紙で覆われているということ、もう 1 つはロールまたは部屋間でストリップを共有できないということです。 ストリップとロールの数を整数演算で計算することで、必要なロールを過小評価することがなくなります。 すべての壁紙タイプを考慮すると、各部屋の最小コストが個別に見つかることが保証されるため、部屋全体を合計すると全体的な最小コストが得られます。 

## Python ソリューション```python
import sys
import math
input = sys.stdin.readline

n = int(input())
rooms = []
for _ in range(n):
    l, w, h = map(int, input().split())
    rooms.append((l, w, h))

m = int(input())
wallpapers = []
for _ in range(m):
    roll_length, roll_width, price = map(int, input().split())
    wallpapers.append((roll_length, roll_width, price))

total_cost = 0

for length, width, height in rooms:
    perimeter = 2 * (length + width)
    min_cost = float('inf')
    for roll_length, roll_width, price in wallpapers:
        strips_per_roll = roll_length // height
        if strips_per_roll == 0:
            continue
        strips_needed = (perimeter + roll_width - 1) // roll_width
        rolls_needed = (strips_needed + strips_per_roll - 1) // strips_per_roll
        cost = rolls_needed * price
        min_cost = min(min_cost, cost)
    total_cost += min_cost

print(total_cost)
```このコードは、入力処理と計算のインターリーブを避けるために、最初にすべての部屋と壁紙を読み取ります。 整数除算により、ロールごとのストリップと必要なロールを正確に計算できます。 の追加`roll_width - 1`そして`strips_per_roll - 1`before 整数除算は、浮動小数点演算を回避し、正確さを保証する上限演算をシミュレートします。 

## 実用的な例

 ### 例 1

 入力:```
1
5 5 3
3
10 1 100
15 2 320
3 19 500
```| 部屋 | 周囲 | 壁紙 | ストリップ/ロール | 必要なストリップ | ロール | コスト | 最小コスト |
 | --- | --- | --- | --- | --- | --- | --- | --- |
 | 5x5x3 | 20 | 10x1,100 | 3 | 20 | 7 | 700 | 640 |
 | | | 15x2,320 | 5 | 10 | 2 | 640 | 640 |
 | | | 3x19,500 | 1 | 20 | 2 | 1000 | 640 |

 アルゴリズムは、合計コスト 640 の 2 番目の壁紙タイプを正しく選択します。 

### 例 2

 入力:```
2
3 4 2
6 5 3
2
10 2 200
6 1 100
```| 部屋 | 周囲 | 壁紙 | ストリップ/ロール | 必要なストリップ | ロール | コスト | 最小コスト |
 | --- | --- | --- | --- | --- | --- | --- | --- |
 | 3x4x2 | 14 | 10x2,200 | 5 | 7 | 2 | 400 | 400 |
 | | | 6x1,100 | 3 | 14 | 5 | 500 | 400 |
 | 6x5x3 | 22 | 10x2,200 | 3 | 11 | 4 | 800 | 800 |
 | | | 6x1,100 | 2 | 22 | 11 | 1100 | 800 |

 合計コスト = 400 + 800 = 1200。 

これは、アルゴリズムがロールごとのストリップと複数の部屋に必要なロールを正しく計算していることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n * m) | n 個の部屋のそれぞれについて、m 種類の壁紙を調べ、反復ごとにいくつかの整数の除算を計算します。 |
 | スペース | O(n + m) | 部屋の寸法と壁紙の仕様を保存します。 |

 と$n, m \le 500$、合計操作数は最大 250,000 で、2 秒の制限時間と 256 MB のメモリに簡単に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n = int(input())
    rooms = [tuple(map(int, input().split())) for _ in range(n)]
    m = int(input())
    wallpapers = [tuple(map(int, input().split())) for _ in range(m)]
    
    total_cost = 0
    for length, width, height in rooms:
        perimeter = 2 * (length + width)
        min_cost = float('inf')
        for roll_length, roll_width, price in wallpapers:
            strips_per_roll = roll_length // height
            if strips_per_roll == 0:
                continue
            strips_needed = (perimeter + roll_width - 1) // roll_width
            rolls_needed = (strips_needed + strips_per_roll - 1) // strips_per_roll
            cost = rolls_needed * price
            min_cost = min(min_cost, cost)
        total_cost += min_cost
    return str(total_cost)

# provided sample
assert run("1\n5 5 3\n3\n10 1 100\n15 2 320\n3 19 500\n") == "640", "sample 1"

# minimum input
assert run("1\n1 1 1\n1\n1 1 1\n") == "1", "minimum input"

# equal dimensions and prices
assert run("2\n2 2 2\n2 2 2\n2\n4 1 2\n4 2 3\n") ==
```
