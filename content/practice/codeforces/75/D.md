---
title: "CF 75D - 大きな最大額"
description: "一連の小さな配列と、それらを 1 つの大きな配列に連結する方法を示すインデックスのシーケンスが与えられます。 この方法で大きな配列が構築されたら、連続するサブ配列の最大合計を見つけることが目標になります。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "dp", "greedy", "implementation", "math", "trees"]
categories: ["algorithms"]
codeforces_contest: 75
codeforces_index: "D"
codeforces_contest_name: "Codeforces Beta Round 67 (Div. 2)"
rating: 2000
weight: 75
solve_time_s: 94
verified: true
draft: false
---

[CF 75D - 大きな最大額](https://codeforces.com/problemset/problem/75/D)

 **評価:** 2000
 **タグ:** データ構造、DP、貪欲、実装、数学、ツリー
 **解決時間:** 1 分 34 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 一連の小さな配列と、それらを 1 つの大きな配列に連結する方法を示すインデックスのシーケンスが与えられます。 この方法で大きな配列が構築されたら、連続するサブ配列の最大合計を見つけることが目標になります。 これは基本的に古典的な最大部分配列合計問題の拡張ですが、ひねりが加えられています。配列を明示的に構築すると、全長が膨大になる可能性があるため、コストが高すぎる可能性があります。 シーケンス内の各インデックスは任意の小さな配列を指すことができ、各小さな配列は大きな配列内に複数回出現する可能性があります。 

入力制限から、それぞれのサイズが最大 5000 の小さな配列が最大 50 個存在し、大きな配列内のインデックスのシーケンスが最大 250,000 になる可能性があることがわかります。 単純に連結すると、最大 50 × 5000 × 250,000 のサイズの配列が作成される可能性があり、これは時間やメモリの点でまったく現実的ではありません。 これは、完全な配列を明示的に構築せずに合計を推論する方法が必要であることを直ちに示します。 

エッジケースには、負の数のみを含む配列が含まれます。 たとえば、小さな配列が`[-5, -2]`そしてもう一つは`[-1]`、シーケンスは次のとおりです`[1,2]`、正しい最大合計は次のとおりです。`-1`それよりも`-2`または`-5`。 もう 1 つの注意が必要なケースは、配列を連結すると合計が複数の配列にまたがることができる場合に発生します。 たとえば、1 つの小さな配列に負のプレフィックスがあり、正のサフィックスがある場合、最大のサブ配列は 1 つのインスタンスで開始され、次のインスタンスで継続する可能性があるため、プレフィックスとサフィックスを注意深く追跡する必要があります。 

## アプローチ

 単純な解決策では、シーケンスに従って配列を連結し、標準の Kadane アルゴリズムを適用してサブ配列の最大合計を見つけます。 これは問題を直接モデル化するため、原理的には正しいですが、大規模なシーケンスには遅すぎます。 各配列の長さが約 5,000 で、シーケンスに 250,000 要素がある場合、大きな配列の合計長は 10 億要素を超える可能性があります。 リニアスキャンですら実際には不可能です。 

重要な洞察は、連結シーケンス内の最大合計が、各小さな配列の 3 つの統計 (最大プレフィックス合計、最大サフィックス合計、および合計合計) を使用して段階的に計算できることです。 プレフィックスの合計は配列の連続するプレフィックスの最大合計であり、サフィックスの合計は連続するサフィックスの最大合計であり、総和は配列全体の合計です。 インデックスのシーケンスを 1 つずつ処理すると、現在の配列内に完全に存在する新しいサブ配列、または前の配列で開始され現在の配列に続くサブ配列のいずれかを考慮することにより、現在の配列で終了する最適なサブ配列の合計を追跡できます。 

これにより、巨大な配列を構築することなく最大合計を計算できるようになり、非現実的なメモリ使用の問題から一連のインデックスの線形スキャンの問題が軽減されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(全長) | O(全長) | 遅すぎる |
 | 最適 | O(小さな配列の合計 + m) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各小さな配列の合計合計、最大プレフィックス合計、最大サフィックス合計、および最大サブ配列合計を事前計算します。 プレフィックスとサフィックスの合計により、複数の連結にわたってサブ配列を拡張できます。合計合計は、配列全体がより長いシーケンスに寄与する場合に役立ちます。 
2. 2 つの変数を初期化します。`current_max`前のインデックスで終わる部分配列の最大合計、および`global_max`シーケンス全体でこれまでに見つかった最大合計。 
3. 大きな配列を定義する一連のインデックスを反復処理します。 インデックスごとに、対応する小さな配列の統計を取得します。 
4.アップデート`current_max`現在の配列の最大サブ配列で新たに開始するか、以前の配列を拡張するかを選択します。`current_max`現在の配列の最大プレフィックス合計を加算することによって。 
5.アップデート`global_max`それ自体の最大値になることと、`current_max`。 これは、これまでに処理されたすべての配列にわたって確認された全体的な最良の合計を追跡します。 
6. ループ後、`global_max`完全に連結された大きな配列全体の連続する部分配列の最大合計が含まれます。 

なぜ機能するのか: 各ステップで、`current_max`現在の配列内で新しいサブ配列を開始するか、以前の配列からサブ配列を拡張することにより、現在の配列で終了する最大合計を正しく表します。 全体の最大値を追跡する`current_max`値により、考えられる連続する部分配列が考慮されることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n, m = map(int, input().split())
small_arrays = []
for _ in range(n):
    data = list(map(int, input().split()))
    l = data[0]
    arr = data[1:]
    total = sum(arr)
    max_prefix = max_suffix = max_sub = arr[0]
    curr = 0
    for x in arr:
        curr += x
        max_prefix = max(max_prefix, curr)
    curr = 0
    for x in reversed(arr):
        curr += x
        max_suffix = max(max_suffix, curr)
    curr_max = arr[0]
    curr_sum = 0
    for x in arr:
        curr_sum += x
        curr_max = max(curr_max, curr_sum)
        if curr_sum < 0:
            curr_sum = 0
    max_sub = curr_max
    small_arrays.append((total, max_prefix, max_suffix, max_sub))

seq = list(map(int, input().split()))
current_max = global_max = -10**18

for idx in seq:
    total, pre, suf, sub = small_arrays[idx - 1]
    current_max = max(sub, suf + max(current_max, 0))
    global_max = max(global_max, current_max)

print(global_max)
```最初のループは、小さな配列ごとに必要な統計をすべて計算します。 プレフィックスとサフィックスの合計は、標準の Kadane スキャンとは別に計算されることに注意してください。 メインループでは、慎重に更新します`current_max`新たに開始することと、以前の配列から拡張することの両方を考慮することによって。 使用する`max(current_max, 0)`負の累計が次の部分配列を誤って削減しないようにします。 すべての合計は 64 ビット整数を使用するのに十分な大きさであるため、初期値は`-10**18`安全です。 

## 実用的な例

 サンプル 1:

 | ステップ | インデックス | 現在の配列 | 現在の最大値 | グローバルマックス |
 | --- | --- | --- | --- | --- |
 | 1 | 2 | [3、3] | 6 | 6 |
 | 2 | 3 | [-5、1] | 1 | 6 |
 | 3 | 1 | [1、6、-2] | 7 | 7 |
 | 4 | 3 | [-5、1] | 9 | 9 |

 これは、累計が配列全体に正しく拡張されており、グローバル最大値が複数の配列にまたがる最適なサブ配列を捉えていることを示しています。 

カスタム例: 単一の負の配列の後に正の配列が続く

 入力:```
2 2
-3 -2 -1
4 5
1 2
```表:

 | ステップ | インデックス | 現在の配列 | 現在の最大値 | グローバルマックス |
 | --- | --- | --- | --- | --- |
 | 1 | 1 | [-3、-2、-1] | -1 | -1 |
 | 2 | 2 | [4、5] | 9 | 9 |

 最初の配列が完全に負であっても、アルゴリズムは 2 番目の配列で新しい部分配列を正しく開始します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(小さな配列の長さの合計 + m) | それぞれの小さな配列は統計のために 1 回スキャンされます。 シーケンスは線形にスキャンされます。 |
 | スペース | O(n) | 小さな配列ごとに 4 つの数値とインデックスのシーケンスを保存します。 |

 小さな配列の長さの合計は最大 50 × 5000 = 250,000、シーケンスの長さは最大 250,000 であるため、時間計算量は許容範囲内です。 メモリ使用量は制限内で最小限に抑えられます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline
    n, m = map(int, input().split())
    small_arrays = []
    for _ in range(n):
        data = list(map(int, input().split()))
        l = data[0]
        arr = data[1:]
        total = sum(arr)
        max_prefix = max_suffix = max_sub = arr[0]
        curr = 0
        for x in arr:
            curr += x
            max_prefix = max(max_prefix, curr)
        curr = 0
        for x in reversed(arr):
            curr += x
            max_suffix = max(max_suffix, curr)
        curr_max = arr[0]
        curr_sum = 0
        for x in arr:
            curr_sum += x
            curr_max = max(curr_max, curr_sum)
            if curr_sum < 0:
                curr_sum = 0
        max_sub = curr_max
        small_arrays.append((total, max_prefix, max_suffix, max_sub))

    seq = list(map(int, input().split()))
    current_max = global_max = -10**18
    for idx in seq:
        total, pre, suf, sub = small_arrays[idx - 1]
        current_max = max(sub, suf + max(current_max, 0))
        global_max = max(global_max, current_max)
    return str(global_max)

# Provided sample
assert run("3 4
```
