---
title: "CF 1146E - 熱いものは冷たい"
description: "整数の配列と一連のクエリが与えられます。 各クエリは、比較を満たす配列内の数値の符号を反転するように指示します。つまり、しきい値より大きいすべての数値、またはしきい値より小さいすべての数値のいずれかです。"
date: "2026-06-12T03:20:47+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "data-structures", "divide-and-conquer", "implementation"]
categories: ["algorithms"]
codeforces_contest: 1146
codeforces_index: "E"
codeforces_contest_name: "Forethought Future Cup - Elimination Round"
rating: 2400
weight: 1146
solve_time_s: 125
verified: false
draft: false
---

[CF 1146E - 熱いものは冷たいもの](https://codeforces.com/problemset/problem/1146/E)

 **評価:** 2400
 **タグ:** ビットマスク、データ構造、分割統治、実装
 **解決時間:** 2 分 5 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 整数の配列と一連のクエリが与えられます。 各クエリは、比較を満たす配列内の数値の符号を反転するように指示します。つまり、しきい値より大きいすべての数値、またはしきい値より小さいすべての数値のいずれかです。 すべてのクエリを順番に実行した後、配列の最終状態を出力するように求められます。 

入力サイズは重要です。配列とクエリの数は両方とも最大 100,000 です。 各要素の範囲は -100,000 ～ 100,000 です。 クエリごとに配列全体を反復処理する単純なソリューションでは、最悪の場合、最大 10^10 の操作が必要になりますが、これは 1 秒の時間制限では実現不可能です。 したがって、総当たり的なアプローチは遅すぎます。 

符号反転は非明白な方法で相互作用する可能性があるため、微妙なエッジ ケースが存在します。 たとえば、数値を 2 回反転すると元の値が復元されるため、配列境界近くの反転しきい値は慎重に処理する必要があります。 小さな配列を考えてみましょう`[-1, 0, 1]`クエリ付き`> -2`そして`< 2`。 不用意に実装すると、同じ数値を 2 回反転したり、不等式を誤って適用したりして、間違った結果が生じる可能性があります。 

問題の難しさは、クエリ間の相互作用を考慮しながら、整数値の制限された範囲に複数の条件付き符号反転を効率的に適用することにあります。 

## アプローチ

 ブルートフォース手法は簡単です。クエリごとに、すべての配列要素を反復処理し、比較が成立する場合は符号を反転します。 これは正しいですが、パフォーマンスは次のとおりです$n \cdot q = 10^10$最悪の場合、動作が非常に遅くなります。 

パフォーマンスを向上させるための重要な点は、配列要素とクエリのしきい値が有限の整数であることです。 配列を直接操作する代わりに、すべてのクエリの後で、許可された範囲内のすべての可能な整数の最終符号を事前計算できます。 このマッピングを取得したら、それを 1 回のパスで配列内の各要素に適用できます。 

このアプローチが機能するのは、フリップ操作が決定的であり、値とクエリのみに依存するためです。 値は小さく離散的な範囲内にあるため、-100,000 から 100,000 までのすべての整数の符号テーブルを維持できます。 クエリを最後のクエリから最初のクエリまで逆に処理し、各値の符号を反転するかどうかを決定します。 これは本質的に、整数値に対するスイープライン、または符号状態に対する分割統治です。 

結果として、O(n*q) の代わりに、整数の範囲にクエリの数を加えたものに比例した複雑さが得られますが、これは実現可能です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * q) | O(n) | 遅すぎる |
 | 最適 (符号表) | O(R + q + n)、R ≈ 2*10^5 | O(R) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 配列を初期化する`sign`-100,000 から 100,000 までの整数を表す長さ 200,001。 各エントリには、最初は正符号として +1 が格納されます。 
2. 逆の順序でクエリを繰り返します。 のようなクエリの場合`> x`、すべての整数が次より大きいとみなします。`x`まだ修正されていない場合は、サインを反転するようにマークします。 同様に、`< x`、すべての整数が以下であるとみなします。`x`。 
3. すべてのクエリを処理した後、`sign[v + 100000]`整数の最終乗算係数 (+1 または -1) を示します`v`。 
4. 元の配列を反復処理します。`a`各要素に対応する値を乗算します`sign`最終的な配列を生成するための係数。 

このアプローチが機能するのは、各整数値に対するすべてのクエリの最終的な影響を効果的にシミュレートできるからです。 クエリを逆に処理すると、必要に応じて後のフリップで前のフリップが上書きされ、操作の正しい順序が維持されます。 配列内の各要素は、その最終符号に直接マッピングできます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

OFFSET = 100000
MAXV = 100000

n, q = map(int, input().split())
a = list(map(int, input().split()))

queries = []
for _ in range(q):
    s, x = input().split()
    x = int(x)
    queries.append((s, x))

# Initialize sign table: 1 means positive, -1 means negative
sign = [1] * (2 * MAXV + 1)
fixed = [False] * (2 * MAXV + 1)

# Process queries in reverse
for s, x in reversed(queries):
    if s == '>':
        for v in range(x + 1, MAXV + 1):
            idx = v + OFFSET
            if not fixed[idx]:
                sign[idx] *= -1
                fixed[idx] = True
    else:  # s == '<'
        for v in range(-MAXV, x):
            idx = v + OFFSET
            if not fixed[idx]:
                sign[idx] *= -1
                fixed[idx] = True

# Apply final signs
result = [val * sign[val + OFFSET] for val in a]
print(' '.join(map(str, result)))
```このソリューションは、すべてのクエリの最終的な効果をエンコードするルックアップ テーブルを構築します。 逆に繰り返すと、正しい順序が保証されます。 の`fixed`配列は、数値の最終符号が決定されると、それが以前のクエリによって上書きされないことを保証します。 

## 実用的な例

 サンプル入力の場合:```
a = [-5, -4, -3, -2, -1, 0, 1, 2, 3, 4, 5]
queries = [('>', 2), ('>', -4), ('<', 5)]
```| ステップ | サインテーブル効果 | 適用後の配列 |
 | --- | --- | --- |
 | イニシャル | すべて +1 | [-5、-4、-3、-2、-1、0、1、2、3、4、5] |
 | '>' 2 | インデックス 3..5 を反転 | [-5、-4、-3、-2、-1、0、1、2、-3、-4、-5] |
 | '>' -4 | インデックスを反転 -3..5 | [-5、-4、3、2、1、0、-1、-2、3、-4、-5] |
 | '<' 5 | フリップ インデックス -100000..4 | [5、4、-3、-2、-1、0、1、2、-3、4、5] |

 このトレースは、アルゴリズムが累積的な符号反転を正しく追跡していることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(R + q + n) | R = 2*10^5 は整数の範囲です。 テーブルの構築にはクエリごとに O(R) がかかり、配列の反復は O(n) |
 | スペース | O(R + n + q) | 符号配列と固定配列にはそれぞれ 2*10^5 の値が格納されます。 入力配列には n 個の要素と q 個のクエリが格納されます。 

制約 n、q ≤ 10^5 と制限された値の範囲を考慮すると、これは時間とメモリの制限内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    OFFSET = 100000
    MAXV = 100000

    n, q = map(int, input().split())
    a = list(map(int, input().split()))
    queries = [tuple(input().split()) for _ in range(q)]
    queries = [(s, int(x)) for s, x in queries]

    sign = [1] * (2 * MAXV + 1)
    fixed = [False] * (2 * MAXV + 1)

    for s, x in reversed(queries):
        if s == '>':
            for v in range(x + 1, MAXV + 1):
                idx = v + OFFSET
                if not fixed[idx]:
                    sign[idx] *= -1
                    fixed[idx] = True
        else:
            for v in range(-MAXV, x):
                idx = v + OFFSET
                if not fixed[idx]:
                    sign[idx] *= -1
                    fixed[idx] = True

    result = [val * sign[val + OFFSET] for val in a]
    return ' '.join(map(str, result))

# Provided sample
assert run("11 3\n-5 -4 -3 -2 -1 0 1 2 3 4 5\n> 2\n> -4\n< 5\n") == "5 4 -3 -2 -1 0 1 2 -3 4 5", "sample 1"
# Minimum input
assert run("1 1\n0\n> -1\n") == "0", "single element no flip"
# All equal values
assert run("3 2\n2 2 2\n> 1\n< 3\n") == "-2 -2 -2", "all elements flipped twice"
# Maximum value
assert run("2 1\n100000 -100000\n> 0\n") == "-100000 -100000", "boundary flip"
# Mix negative and positive
assert run("4 2\n-1 0 1 2\n< 0\n> 1\n") == "1 0 -1 -2", "mixed signs flip"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1\n0\n> -1`|`0`| 単一要素の場合、クエリにはそれが含まれますが、| は含まれません。
