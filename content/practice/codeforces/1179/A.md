---
title: "CF 1179A - ヴァレリーとデケ"
description: "n 個の整数を含む両端キューが与えられます。 Valeriy は、最初の 2 つの要素を削除し、比較して、再度挿入するという操作を繰り返し実行します。2 つの要素のうち大きい方が前面に配置され、小さい方が背面に配置されます。"
date: "2026-06-12T01:35:59+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "implementation"]
categories: ["algorithms"]
codeforces_contest: 1179
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 569 (Div. 1)"
rating: 1500
weight: 1179
solve_time_s: 184
verified: true
draft: false
---

[CF 1179A - ヴァレリーとデケ](https://codeforces.com/problemset/problem/1179/A)

 **評価:** 1500
 **タグ:** データ構造、実装
 **解決時間:** 3 分 4 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 を含む deque が与えられます。`n`整数。 Valeriy は、最初の 2 つの要素を削除し、比較して、再度挿入するという操作を繰り返し実行します。2 つの要素のうち大きい方が前面に配置され、小さい方が背面に配置されます。 私たちに求められているのは、`q`クエリを使用して、どの 2 つの要素が削除されるかを決定します。`m_j`各クエリの - 番目の操作。 

重要な制約は次のとおりです。`n`最大 10^5、クエリ数`q`can be up to 3×10^5. 各クエリでは、次のことについて尋ねることができます。`m_j`- 番目の操作、ここで`m_j`10^18 までの大きさになる可能性があります。 これにより、すべてのクエリに対して各操作を 1 つずつシミュレートするソリューションは即座に除外されます。`O(n)`クエリごとの処理が遅すぎるため、`O(m_j)`大型には無理`m_j`。 

単純な実装が失敗する可能性のあるエッジケースには、すべての要素が等しい、すでにソートされた配列、または最大の要素が先頭に到達した後の操作のクエリが含まれます。 たとえば、両端キューが次の場合、`[5, 5, 5]`そして 10 番目の操作を要求すると、不注意なシミュレーションでは、決して起こらない順序の変更が想定される可能性があります。 

## アプローチ

 ブルートフォースアプローチは簡単です。 指定された両端キューから開始し、操作ごとに最初の 2 つの要素をポップし、比較し、それに応じて再挿入します。 希望の動作回数に達するまでこれを繰り返します。 これは小規模な場合に機能します`m_j`、しかし、もし`m_j`は 10^18 のオーダーなので、実行不可能です。 たとえ`n = 10^5`そして`q = 10^5`、操作を 1 つずつシミュレートすると、最悪の場合、何兆もの操作が必要になります。 

重要な洞察は、プロセスには 2 つのフェーズがあるということです。 まず、両端キュー内の最大の要素が最終的に先頭に到達します。 このフェーズでは、各操作で 2 つの数値を比較し、大きい方を前に、小さい方を後ろに移動します。 最大値が前面に来ると、最初の要素は決して変更されません。 その後、すべての操作は前の要素 (最大値) と次の要素を組み合わせて、次の要素を後ろに送信するだけです。 この第 2 フェーズは完全に周期的です。残りのフェーズをローテーションします。`n-1`固定サイクルで最大値よりも遅れた要素。 

これを観察することで、最初の計算を事前に行うことができます。`n`最大値が手前に達するまで操作します。 この範囲内のクエリの場合は、事前計算された結果が返されます。 これを超えるクエリの場合は、残りの要素の循環シーケンスに対するモジュラー演算を使用して答えを計算できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(m_j) | O(n) | 遅すぎる |
 | 最適 | O(n + q) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. deque 要素とクエリを含む入力を読み取ります。 両端キュー内の最大の要素を特定します。 
2. 両端キュー構造を初期化して、最大値が先頭に達するまでの操作をシミュレートします。 リストを保管しておく`first_phase`各操作で削除された要素のペアを保存します。 
3.両端キューの先頭が最大値ではない場合、最初の 2 つの要素をポップします`A`そして`B`。 追加`(A, B)`に`first_phase`。 比較する`A`そして`B`、大きい方を前に、小さい方を後ろに押します。 これにより、最終的には最大値が先頭を占めるようになります。 
4. 最大値が先頭に来たら、その後ろの残りの要素を次のように順番に記録します。`cycle`。 これらの要素は、各操作後に最大値よりも後ろで回転します。 
5. 各クエリについて`m_j`、事前計算された範囲内にあるかどうかを確認します。`first_phase`。 その場合は、保存されているペアを出力するだけです。 それ以外の場合は、次の操作の数を減算します。`first_phase`循環フェーズでインデックスを取得します。 最大値とペアになる要素は次のとおりです。`cycle[(m_j - len(first_phase) - 1) % len(cycle)]`、最大値自体は常に他の要素です。 
6. すべてのクエリに対する回答を、与えられた順序で出力します。 

これが機能する理由: 不変条件は、一度最大値が前面に来ると、決して移動しないということです。 残りの要素はその後ろに固定シーケンスを形成し、操作ごとに次の要素が後ろに移動します。 これにより、事前に計算された第 1 フェーズのリストまたは循環フェーズでの単純なモジュラー ルックアップのいずれかを使用して、すべてのクエリに回答できることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from collections import deque

n, q = map(int, input().split())
a = list(map(int, input().split()))
queries = [int(input()) for _ in range(q)]

d = deque(a)
first_phase = []

max_val = max(a)

while d[0] != max_val:
    A = d.popleft()
    B = d.popleft()
    first_phase.append((A, B))
    if A > B:
        d.appendleft(A)
        d.append(B)
    else:
        d.appendleft(B)
        d.append(A)

# Now the maximum is at the front
d.popleft()  # remove max for cycle
cycle = list(d)

answers = []
for m in queries:
    if m <= len(first_phase):
        answers.append(first_phase[m - 1])
    else:
        idx = (m - len(first_phase) - 1) % len(cycle)
        answers.append((max_val, cycle[idx]))

for a, b in answers:
    print(a, b)
```このソリューションでは、まず最大値が先頭に達するまでのみ操作をシミュレートし、各操作の結果を保存します。 その後、両端キューは最大要素と残りの要素のサイクルに分割されます。 最初のフェーズ後のクエリでは、モジュラー演算を使用して、どの要素が最大値とペアになっているかを特定します。 これにより、すべての操作をシミュレートすることなく、非常に大きなクエリ数が効率的に処理されます。 

## 実用的な例

 サンプル入力のトレース:```
5 3
1 2 3 4 5
1
2
10
```### 最初のフェーズのシミュレーション

 | 操作 | デケ フロント 2 | 操作後のデキュー |
 | --- | --- | --- |
 | 1 | 1、2 | 2、3、4、5、1 |
 | 2 | 2、3 | 3、4、5、1、2 |
 | 3 | 3、4 | 4、5、1、2、3 |
 | 4 | 4、5 | 5、1、2、3、4 |

 最大`5`前に到達しました、サイクルは`[1,2,3,4]`。 

### クエリの処理

 クエリ1 → オペレーション1 →`(1,2)`第一段階から。 

クエリ 2 → 操作 2 →`(2,3)`第一段階から。 

クエリ 10 → 演算 10 → 10 - 4 = 6 → インデックス 6 % 4 = 2 → サイクル[2] = 3 → 答え`(5,3)`。 

このトレースは、最初のフェーズと循環フェーズの間の分離を示しており、モジュラー演算により最大値との正しいペアが得られることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + q) | せいぜいシミュレーションする`n`max を前面に持ってくる操作、その後モジュラー算術を使用してクエリごとに O(1) |
 | スペース | O(n) | デキュー操作と第 1 フェーズの操作、および最大 n-1 のサイクルを格納します。 

これは、最大の入力に対して十分効率的です。`n`≤ 10^5 および`q`≤ 3×10^5。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from collections import deque
    input = sys.stdin.readline
    
    n, q = map(int, input().split())
    a = list(map(int, input().split()))
    queries = [int(input()) for _ in range(q)]
    
    d = deque(a)
    first_phase = []

    max_val = max(a)
    while d[0] != max_val:
        A = d.popleft()
        B = d.popleft()
        first_phase.append((A, B))
        if A > B:
            d.appendleft(A)
            d.append(B)
        else:
            d.appendleft(B)
            d.append(A)
    
    d.popleft()
    cycle = list(d)
    
    answers = []
    for m in queries:
        if m <= len(first_phase):
            answers.append(first_phase[m - 1])
        else:
            idx = (m - len(first_phase) - 1) % len(cycle)
            answers.append((max_val, cycle[idx]))
    
    return "\n".join(f"{x[0]} {x[1]}" for x in answers)

# Provided samples
assert run("5 3\n1 2 3 4 5\n1\n2\n10\n") == "1 2\n2 3\n5 2"

# Minimum input
assert run("2 1\n0 0\n1\n") == "0 0"

# Maximum element at front initially
assert run("3 2\n9 1 2\n1\n4\n") == "9 1\n9 2"

# All equal elements
assert run("4 3\n7 7 7 7\n1\n5\n10\n") == "7 7\n7 7\n7 7"

# Large query beyond first phase
assert run("5 2\n1 2
```
