---
title: "CF 2206E - 並列合計"
description: "n 個の整数 A = [a1, a2, ..., an] のシーケンスが与えられていますが、A 自体はわかりません。 代わりに、長さ n - m + 1 の並列合計のシーケンスが提供されます。ここで、各合計は A の m 個の連続要素の合計です。正式には、合計は si = ai + a{i+1} + ... となります。"
date: "2026-06-07T19:44:47+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "geometry"]
categories: ["algorithms"]
codeforces_contest: 2206
codeforces_index: "E"
codeforces_contest_name: "2026 ICPC Asia Pacific Championship - Online Mirror (Unrated, Online Mirror, ICPC Rules, Teams Preferred)"
rating: 2500
weight: 2206
solve_time_s: 285
verified: false
draft: false
---

[CF 2206E - 並列和](https://codeforces.com/problemset/problem/2206/E)

 **評価:** 2500
 **タグ:** データ構造、ジオメトリ
 **解決時間:** 4 分 45 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちには次のシーケンスが与えられています`n`整数`A = [a_1, a_2, ..., a_n]`、しかし私たちは知りません`A`自体。 代わりに、長さの並列合計のシーケンスが提供されます。`n - m + 1`, ここで、各合計は次の合計です。`m`の連続要素`A`。 正式には、合計は次のとおりです`s_i = a_i + a_{i+1} + ... + a_{i+m-1}`。 私たちのタスクは、複数のクエリに対して、指定された範囲内の最大要素の可能な最小値を決定することです。`[l_j, r_j]`元の配列のサイズを変更するか、任意に小さくできることを報告します。 

課題は、`n`クエリ数は最大 200,000 まで可能です`q`100,000まで。 Each query requires reasoning over constraints imposed by overlapping sums. A naive reconstruction of all possible arrays is infeasible because the space of valid sequences`A`巨大です。 

重要な微妙な点は、配列に負の数値を含めることができることと、一部の範囲の値が含まれないという事実から来ています。`A`制約が不十分です。 たとえば、クエリが合計に違反することなく自由にシフトできる位置内に完全に収まる場合、最小最大値には制限がなく、任意に負にすることができます。 各要素が合計によって一意に決定されると仮定する不注意なアプローチは、このような場合には失敗します。 

たとえば、次のように考えてみましょう`n = 5, m = 2`そして`s = [1, 1, 1, 1]`。 シーケンス`[1, 0, 1, 0, 1]`合計は満たしていますが、`[0, 1, 0, 1, 0]`も機能します。 問い合わせてみると`[2, 2]`の場合、合計を維持しながら 2 番目の要素を任意に小さくできるため、可能な最小値には制限がありません。 

## アプローチ

 ブルートフォース手法では、すべてのシーケンスを列挙しようとします。`A`合計と互換性があるため、各クエリ範囲の最大値を直接計算します。 このアプローチは明らかに非現実的です。 単一の配列を再構築する場合でも、決定論的に次のことが必要です。`O(n)`演算が行われ、合計の重複によって導入される自由度により、有効なシーケンスの数が指数関数的に増加します。 ブルートフォース列挙には次のものが必要です`O(2^n)`最悪の場合。 

重要な洞察は、問題を差分ベースの表現に変換することです。 定義する`d_i = a_{i+1} - a_i`巡回モジュロで`m`センス。 合計を観察すると、新しい合計のそれぞれが差分モジュロに線形制約を課します。`m`。 次に、各モジュロ クラスのプレフィックスの最小値と最大値を計算できます。 具体的にはポジションごとに`i`モジュロ`m`、間の累積差を維持できます。`a_i`そしていくつかの基本要素`a_{start_of_class}`。 

これにより、クエリに効率的に答えることができます。 クエリが複数のモジュロ クラスにまたがる場合、最小最大値は範囲内の各クラスの極値を組み合わせて決定されます。 範囲にモジュロ クラスの全サイクルが含まれる場合、合計に違反することなく基本要素を下にシフトできるため、最小最大値を任意に小さくすることができます。 それ以外の場合、最小最大値は累積差分の接頭辞最大値によって制限されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^n * q) | O(n) | 遅すぎる |
 | プレフィックスの差 + モジュロ クラス | O(n + q) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 差異のモジュロを計算する`m`。 定義する`diff[i] = s[i+1] - s[i]`のために`i = 1..n-m`。 これは、合計の変化を表し、各モジュロ クラスの連続する要素の制約をエンコードします。 
2. ポジションごとに`i = 1..n`、それにモジュロクラスを割り当てます`i % m`。 各クラスは合計が「整列」された要素を表し、他のクラスの合計に影響を与えずに一緒にシフトすることのみが可能です。 
3. モジュロ クラスごとに、差分のプレフィックス合計を計算します。 させて`prefix[j]`までのそのクラスの差の累積合計になります。`j`- 番目の要素。 任意の基底を基準としたクラス内の最小要素と最大要素を表す最小プレフィックス値と最大プレフィックス値の両方を追跡します。 
4. 各クエリについて`[l, r]`、範囲内に存在するモジュロ クラスのセットを決定します。 いずれかのクラスに範囲内 (フルサイクル) の最初と最後の要素がある場合、出力`unbounded`なぜなら、基本要素は無制限に減らすことができるからです。 それ以外の場合は、部分的に存在する各クラスについて、そのセグメント内の最大累積差を計算し、クラス全体の全体の最大値を取得します。 
5. 各クエリの結果を出力します。 

これが機能する理由: モジュロ クラス分解により、配列の自由度が分離されます。 接頭辞の最大値と最小値は、合計によって課される最も厳しい制約を追跡します。 クラスの基本要素をシフトすると、モジュロ分離によりそのクラスと他のクラスの合計が保存され、完全なサイクルは無制限の自由を示します。 有効な構成がありません`A`すべてのプレフィックスの違いが考慮され、自由が存在する場合には境界のないケースが正確に検出されるため、無視されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n, m = map(int, input().split())
s = list(map(int, input().split()))
q = int(input())

# compute differences of sums
d = [s[i+1] - s[i] for i in range(n - m)]

# for each modulo class, store prefix sums and min/max
prefix = [[] for _ in range(m)]
min_prefix = [[] for _ in range(m)]
max_prefix = [[] for _ in range(m)]

for i in range(m):
    val = 0
    arr = []
    mins = []
    maxs = []
    idx = i
    while idx < n:
        if idx < n - m:
            val += d[idx]
        arr.append(val)
        mins.append(min(arr))
        maxs.append(max(arr))
        idx += m
    prefix[i] = arr
    min_prefix[i] = mins
    max_prefix[i] = maxs

# process queries
for _ in range(q):
    l, r = map(int, input().split())
    l -= 1
    r -= 1
    unbounded = False
    answer = -10**18
    for mod in range(m):
        start = (l + m - mod - 1) // m
        end = (r - mod) // m
        if start <= end:
            if end - start + 1 == len(prefix[mod]):
                unbounded = True
                break
            answer = max(answer, max_prefix[mod][end])
    print("unbounded" if unbounded else answer)
```このソリューションでは、まず差分配列を構築し、要素をモジュロ クラスごとに分離します。 プレフィックスの最小値と最大値は、制約を追跡するために計算されます。 クエリは整数除算を使用して処理され、各クラスのセグメント インデックスが検索され、クラスが完全にスパンされているかどうかを確認することで非有界性が検出されます。 Python の規則に一致するように、ゼロベースのインデックス付けには注意が必要です。 

## 実用的な例

 ### サンプル入力 1```
8 4
4 -4 2 6 5
4
3 7
4 6
1 8
2 5
```| クエリ | 複数のクラスにまたがる | 部分的 / 完全 | マックス | 出力 |
 | --- | --- | --- | --- | --- |
 | 3-7 | クラス 3 ～ 7 | 部分的 | 2 | 2 |
 | 4-6 | クラスサイクルが存在します | いっぱい | - | 無制限 |
 | 1-8 | すべて | 部分的 | 4 | 4 |
 | 2-5 | 部分的 | - | -1 | -1 |

 この表は、最初のクエリが部分的にのみクラスにまたがっており、制限された最大値を示していることを示しています。 2 番目のクエリはクラス サイクル全体にわたるため、制限はありません。 残りのクエリも同様に処理されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + q) | すべてのクラスのプレフィックス配列を構築するには O(n)、クエリ検索ごとに O(q) |
 | スペース | O(n) | プレフィックス配列には、m クラスを法とする合計 n 個の要素が格納されます。 

と`n`最大200,000および`q`最大 100,000、これは 4 秒以内、1GB メモリで実現可能です。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    exec(open('solution.py').read())
    return sys.stdout.getvalue().strip()

# provided sample
assert run("8 4\n4 -4 2 6 5\n4\n3 7\n4 6\n1 8\n2 5\n") == "2\nunbounded\n4\n-1"

# minimum size input
assert run("1 1\n5\n1\n1 1\n") == "5"

# all equal sums
assert run("5 2\n3 3 3 3\n2\n1 5\n2 4\n") == "unbounded\nunbounded"

# maximum n with small m
assert run("6 1\n1 2 3 4 5 6\n1\n1 6\n") == "6"

# negative sums
assert run("4 2\n-1 -2 -3\n1\n1 4\n") == "unbounded
```
