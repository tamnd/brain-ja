---
title: "CF 1133D - ゼロ数量最大化"
description: "それぞれ n 個の要素を持つ 2 つの整数配列 a と b が与えられます。 単一の実数 d を使用して、各要素 ci が d ai + bi と等しくなるように新しい配列 c を構築するように求められます。 私たちの目標は、c のゼロの数を最大化するように d を選択することです。"
date: "2026-06-12T04:05:05+07:00"
tags: ["codeforces", "competitive-programming", "hashing", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 1133
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 544 (Div. 3)"
rating: 1500
weight: 1133
solve_time_s: 73
verified: true
draft: false
---

[CF 1133D - ゼロ数量最大化](https://codeforces.com/problemset/problem/1133/D)

 **評価:** 1500
 **タグ:** ハッシュ、数学、数論
 **解決時間:** 1 分 13 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 2 つの整数配列が与えられています。`a`そして`b`、それぞれ`n`要素。 新しい配列を構築するように求められます`c`単一の実数を使用する`d`各要素が`c_i`等しい`d * a_i + b_i`。 私たちの目標は選択することです`d`ゼロの数を最大化するには`c`。 出力は、で達成可能なゼロ要素の最大数です。`c`。 

制約は重要です。`n`最大 200,000 まで可能で、`a_i`そして`b_i`それぞれの絶対値は 10^9 まで大きくなります。 これにより、考えられるすべての解決策が排除されます。`d`明示的に値を指定するか、ネストされたループ内のすべてのペアを試行します。 アルゴリズムは、相対的にほぼ線形または線形演算時間で実行する必要があります。`n`。 

微妙なエッジケースが発生するのは、`a_i`値はゼロです。 その場合、`c_i = b_i`から独立しています`d`したがって、次の場合にのみその位置のゼロを取得できます。`b_i`それ自体はゼロです。 もう 1 つの重要なケースは、複数のペアが存在する場合です。`(a_i, b_i)`同じ比率が得られます`-b_i / a_i`。 これらは同じ最適化に貢献します`d`。 精度を無視したり、分数を誤って切り捨てたりする不注意な実装では、これらを誤ってカウントする可能性があります。 

小さな例:`a = [0, 2], b = [0, 4]`。 ここで、最初の要素は、`d`。 2 番目の要素は次の場合にゼロになります。`d = -2`。 最適な`d`は`-2`そして 2 つのゼロが得られます。 ゼロを無視する単純なアプローチでは、`a_i = 0`ゼロは 1 つだけカウントされます。 

## アプローチ

 ブルートフォースアプローチは、あらゆる現実的な問題を考慮することです。`d`それはもしかしたら何かを作るかもしれない`c_i`ゼロ、それからゼロを数えます`d`。 それを観察すると`c_i = 0`暗示する`d = -b_i / a_i`（いつ`a_i ≠ 0`）、単純なアルゴリズムでは、これらすべての候補をテストしようとする可能性があります。`d`値とカウントゼロ`O(n^2)`時間。 これは原理的には正しいですが、遅すぎます。`n = 2 * 10^5`、4 * 10^10 の演算が必要になる可能性があります。 

重要な観察は次のとおりです`d`比率によって一意に決定されます`-b_i / a_i`。 複数のインデックスが同じ比率を生成する場合は、それを選択します`d`すべてを対応させます`c_i`同時にゼロになります。 したがって、それぞれの一意の比率が何回発生するかを数えるだけで済みます。 浮動小数点の不正確さを避けるために、分数は縮小形式で保存する必要があります。 ゼロペア`(a_i = 0, b_i = 0)`どれもゼロなので別々に数えることができます。`d`。 

これにより、問題はこれらの端数のハッシュ化と発生回数のカウントに軽減されます。 各分数は次のように表すことができます。`(numerator, denominator)`一貫性を保つために分母が正であることを保証しながら、最大公約数を使用した縮小形式で。 カウントが得られたら、最大周波数に次の数を加えたものになります。`a_i = 0, b_i = 0`答えを出します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^2) | O(n) | 遅すぎる |
 | 最適 | O(n log(max( | a_i | , |

 ## アルゴリズムのチュートリアル

 1. カウンタを初期化する`ratio_count`を表す各換算分数の頻度を保存します。`-b_i / a_i`。 初期化もする`zero_count`位置を数える`a_i = 0`そして`b_i = 0`。 
2. 各ペアを反復処理します。`(a_i, b_i)`。 もし`a_i`ゼロであり、`b_i`ゼロ、増分`zero_count`。 もし`a_i`ゼロであり、`b_i`はゼロではありません。いいえなのでスキップします`d`これをゼロにすることができます。 
3. 非ゼロの場合`a_i`、分数を計算します`-b_i / a_i`。 この分数を最大公約数を使用して最も単純な形に換算します。 分母が正になるように符号を正規化し、一貫したハッシュを確保します。 
4.タプルを使用する`(numerator, denominator)`カウンターの鍵として`ratio_count`そしてそのキーのカウントをインクリメントします。 
5. 最終的な答えは、次の最大数です。`ratio_count`プラス`zero_count`。 もし`ratio_count`空です（すべて`a_i = 0`）、答えは次のとおりです。`zero_count`。 

なぜ機能するのか: 各分数は 1 つの候補を正確に表します`d`それは対応するものを作ります`c_i`ゼロ。 各候補者が何回かを数えることにより、`d`が発生すると、`d`ゼロを最大化します。 独立したゼロ要素`d`別途追加されます。 候補が見逃されることはなく、正規化によって端数が二重にカウントされることもありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from math import gcd
from collections import Counter

n = int(input())
a = list(map(int, input().split()))
b = list(map(int, input().split()))

ratio_count = Counter()
zero_count = 0

for ai, bi in zip(a, b):
    if ai == 0:
        if bi == 0:
            zero_count += 1
        continue
    num = -bi
    den = ai
    g = gcd(num, den)
    num //= g
    den //= g
    if den < 0:
        num = -num
        den = -den
    ratio_count[(num, den)] += 1

max_ratio = max(ratio_count.values(), default=0)
print(max_ratio + zero_count)
```このソリューションは最初にゼロを処理します。`a_i = 0`。 次に、慎重に分数を減らし、符号を正規化してキーの一貫性を確保します。 を使用して`Counter`効率的に周波数を追跡します。 決勝戦`max`呼び出しは最適な候補を見つけます`d`。 

## 実用的な例

 サンプル 1:

 入力:```
5
1 2 3 4 5
2 4 7 11 3
```| 私 | あ_い | b_i | 番号=-b_i | デン=a_i | 削減 | 比率カウント |
 | --- | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | 2 | -2 | 1 | (-2,1) | 1 |
 | 2 | 2 | 4 | -4 | 2 | (-2,1) | 2 |
 | 3 | 3 | 7 | -7 | 3 | (-7,3) | 1 |
 | 4 | 4 | 11 | -11 | 4 | (-11,4) | 1 |
 | 5 | 5 | 3 | -3 | 5 | (-3,5) | 1 |`zero_count = 0`、`max_ratio = 2`、出力`2`。 これにより、選択することが確認されます。`d = -2`最初の 2 つの位置をゼロにします。 

サンプル 2:

 入力:```
3
0 1 2
0 3 6
```| 私 | あ_い | b_i | ゼロカウント | 比率カウント |
 | --- | --- | --- | --- | --- |
 | 1 | 0 | 0 | 1 | - |
 | 2 | 1 | 3 | 0 | (-3,1)=1 |
 | 3 | 2 | 6 | 0 | (-3,1)=2 |`zero_count=1`、`max_ratio=2`、出力`3`。 分数カウントとゼロの処理は正しく機能します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log(max( | a_i |
 | スペース | O(n) | 最大で n 個の一意の分数をカウンターに保存します。 |

 と`n ≤ 2 * 10^5`、時間計算量は 2 秒以内で許容されます。 メモリ使用量は直線的であり、256 MB の制限内に十分収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n = int(input())
    a = list(map(int, input().split()))
    b = list(map(int, input().split()))
    from math import gcd
    from collections import Counter
    ratio_count = Counter()
    zero_count = 0
    for ai, bi in zip(a, b):
        if ai == 0:
            if bi == 0:
                zero_count += 1
            continue
        num, den = -bi, ai
        g = gcd(num, den)
        num //= g
        den //= g
        if den < 0:
            num = -num
            den = -den
        ratio_count[(num, den)] += 1
    max_ratio = max(ratio_count.values(), default=0)
    return str(max_ratio + zero_count)

# provided samples
assert run("5\n1 2 3 4 5\n2 4 7 11 3\n") == "2", "sample 1"
assert run("3\n0 1 2\n0 3 6\n") == "3", "custom zero + fraction overlap"
# minimum-size input
assert run("1\n0\n0\n") == "1", "single zero"
assert run("1\n1\n2\n") == "1", "single non
```
