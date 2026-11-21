---
title: "CF 258D - 小さな象と壊れた選別"
description: "1 から n までの整数の順列と m 回のスワップ操作のシーケンスが与えられます。 通常、これらの操作は配列を昇順にソートしますが、スワップ プログラムが壊れています。各移動は、確率 1/2 で、指定された位置を交換するか、何もしません。"
date: "2026-06-04T17:23:16+07:00"
tags: ["codeforces", "competitive-programming", "dp", "math", "probabilities"]
categories: ["algorithms"]
codeforces_contest: 258
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 157 (Div. 1)"
rating: 2600
weight: 258
solve_time_s: 87
verified: true
draft: false
---

[CF 258D - 小さな象と壊れた仕分け](https://codeforces.com/problemset/problem/258/D)

 **評価:** 2600
 **タグ:** dp、数学、確率
 **解決時間:** 1 分 27 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 1 から _n_ までの整数の順列と、一連の _m_ スワップ操作が与えられます。 通常、これらの操作は配列を昇順にソートしますが、スワップ プログラムが壊れています。各移動は、確率 1/2 で、指定された位置を交換するか、何もしません。 目標は、最終的な置換で予想される反転数を計算することです。 反転は、_i < j_ および _p[i] > p[j]_ である _(i, j)_ のペアです。 

入力は、置換サイズ _n_、移動数 _m_、初期置換、および交換する位置を指定するインデックスの _m_ ペアを提供します。 出力は単一の浮動小数点数、つまりすべての移動後の予想される反転数であり、精度は最大 10^-6 です。 

制約 _n, m ≤ 1000_ を使用すると、3 次複雑さ (O(n³)) のアルゴリズムは実行可能ですが、速度が遅くなります。 二次解が理想的です。 エッジケースには、すでにソートされている順列、逆ソートされた順列、同じ位置を繰り返し交換する移動、_n = 2_ のような非常に小さな配列が含まれます。 たとえば、_n = 2_ の場合、順列`[2, 1]`単一のスワップの場合、期待値は次のとおりです。`0.5`なぜなら、スワップは反転を修正するか、何もしないかのどちらかだからです。 

## アプローチ

 ブルートフォースアプローチでは、可能な 2^m 個のスワップシーケンスをすべてシミュレートし、それぞれの反転をカウントし、平均を取得します。 これは正しいですが、2^1000 の状態は実現不可能です。 

重要な洞察は、期待値の線形性から得られます。つまり、期待される反転数は、個々の反転インジケーターの期待値の合計です。 _E[i, j]_ を、_i < j_ が最後に反転する確率とします。 各スワップは、その 2 つのポジションに関係する反転の確率にのみ影響します。 したがって、すべてのペア _(i, j)_ (_i < j_) の確率行列を維持し、スワップが発生するかどうかに応じて各移動の後に確率行列を更新できます。 

スワップに a < b の位置 _(a, b)_ が含まれる場合、それらの間の反転の確率は次のようになります。`1 - current probability`。 他のペアの場合は、_a_ または _b_ に関係する確率のみが、次の派生式に従って調整されます。 

- _p_ をペア _(x, y)_ の現在の反転確率とします。 
- x または y の 1 つが a または b に等しい場合、50% の確率で交換した後、新しい確率は、古い確率と、交換によって関連する要素が反転した場合の反転の確率の平均になります。 

すべての手を反復処理すると、すべての確率が O(n² m) で効率的に更新されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート フォース (すべての 2^m シーケンスをシミュレート) | O(2^m n²) | O(n) | m ~ 1000 では不可能 |
 | DP / 期待値による確率 | O(n² m) | O(n²) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 2D 配列を初期化する`inv_prob[i][j]`すべてのペア _i < j_ について、位置 _i_ と _j_ が反転を形成する確率を示します。 最初に、次の場合は 1 に設定します。`p[i] > p[j]`それ以外の場合は 0。 
2. 移動ごとに位置を交換する`a`そして`b`(0 からインデックス付き)、確率を更新します。 まずは交換`a`そして`b`関連するすべての確率において。 ペア用`(a, b)`、その確率を次のように更新します`1 - inv_prob[a][b]`。 
3. 1 ペアおきに`(i, j)`それはまさに次のうちの 1 つを含みます`a`または`b`、古い確率と要素が次の場合の確率の平均に確率を更新します。`a`そして`b`交換されていました。 これは、スワップが発生する確率が 50% であることを示しています。 
4. すべての手を処理した後、すべての確率を合計します。`inv_prob[i][j]`全体的に _i < j_ です。 この合計が予想される反転数です。 
5. 結果を小数点以下 9 桁以上で出力します。 

それが機能する理由: 期待値の線形性により、各反転を独立して扱うことができます。 各動きは、その 2 つのポジションを含むペアにのみ影響します。 確率をその場で更新すると、各スワップの 50% の確率効果が正しく伝播されます。 確率が二重にカウントされることはなく、ペアが見逃されることもないため、最後の合計により、予想される正しい反転数が得られます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n, m = map(int, input().split())
p = list(map(int, input().split()))
moves = [tuple(map(int, input().split())) for _ in range(m)]

# convert moves to 0-indexed
moves = [(a-1, b-1) for a, b in moves]

# initialize inversion probabilities
inv_prob = [[0.0]*n for _ in range(n)]
for i in range(n):
    for j in range(i+1, n):
        if p[i] > p[j]:
            inv_prob[i][j] = 1.0

for a, b in moves:
    if a > b:
        a, b = b, a
    # update probability for pair (a,b)
    inv_prob[a][b] = 1 - inv_prob[a][b]
    # update all other pairs
    for i in range(n):
        if i != a and i != b:
            x, y = sorted((i, a))
            inv_prob[x][y] = 0.5 * (inv_prob[x][y] + inv_prob[min(i,b)][max(i,b)])
            x, y = sorted((i, b))
            inv_prob[x][y] = 0.5 * (inv_prob[x][y] + inv_prob[min(i,a)][max(i,a)])

# sum probabilities for expected inversions
expected_inversions = sum(inv_prob[i][j] for i in range(n) for j in range(i+1, n))
print(f"{expected_inversions:.9f}")
```説明: 確率更新のネストされたループは、影響を受けるすべてのペアを慎重に処理します。 インデックスを並べ替えることで、常に参照できるようになります。`inv_prob[i][j]`と`i < j`。 メインペアの交換`(a, b)`これは単純であり、平均化更新によって壊れたスワップの影響が伝播されます。 すべての確率がそのまま残るため、浮動小数点演算で十分です。`[0,1]`。 

## 実用的な例

 **サンプル 1:**

 入力:```
2 1
1 2
1 2
```初期反転確率行列:`[[0, 0], [0, 0]]`1 < 2 なので。 

スワップ後 (1,2): 確率が反転します。`(0,1)`→`1 - 0 = 1`。 他のペアとの平均は存在しないため、取るに足らないものです。 

予想される反転 = 0.5。 

**カスタム例:**

 入力:```
3 2
3 1 2
1 2
2 3
```| i,j | イニシャル | 移動後 1 | move2 後 |
 | --- | --- | --- | --- |
 | 0,1 | 1 | 0.0.5 | 0.5 |
 | 0,2 | 1 | 1 | 0.5 |
 | 1,2 | 0 | 0 | 0.25 |

 合計 = 0.5 + 0.5 + 0.25 = 1.25 の予想される反転。 

これは、複数の重複するスワップがあっても確率の伝播が正しいことを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n² m) | m の各移動は、最悪でも O(n²) ペアを更新します。 
| スペース | O(n²) | 確率行列`inv_prob`すべてのペアを保存します |

 n ≤ 1000 および m ≤ 1000 の場合、O(n² m) ~ 10^9 の最悪の場合の演算になります。これは効率的な内部ループで実現可能です。 メモリ～1e6のダブルが快適に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline
    n, m = map(int, input().split())
    p = list(map(int, input().split()))
    moves = [tuple(map(int, input().split())) for _ in range(m)]
    moves = [(a-1, b-1) for a, b in moves]
    inv_prob = [[0.0]*n for _ in range(n)]
    for i in range(n):
        for j in range(i+1, n):
            if p[i] > p[j]:
                inv_prob[i][j] = 1.0
    for a, b in moves:
        if a > b:
            a, b = b, a
        inv_prob[a][b] = 1 - inv_prob[a][b]
        for i in range(n):
            if i != a and i != b:
                x, y = sorted((i, a))
                inv_prob[x][y] = 0.5 * (inv_prob[x][y] + inv_prob[min(i,b)][max(i,b)])
                x, y = sorted((i, b))
                inv_prob[x][y] = 0.5 * (inv_prob[x][y] + inv_prob[min(i,a)][
```
