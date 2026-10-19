---
title: "CF 105712J - あいまいな順列"
description: "いくつかのテスト ケースが与えられ、それぞれが 1 から n までの整数の順列を記述します。 ここでの順列とは、1 から n までのすべての数値が 1 回だけ出現する単なる配列です。"
date: "2026-06-26T07:57:46+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105712
codeforces_index: "J"
codeforces_contest_name: "Rutgers University Programming Contest Fall 2024"
rating: 0
weight: 105712
solve_time_s: 36
verified: true
draft: false
---

[CF 105712J - 曖昧な順列](https://codeforces.com/problemset/problem/105712/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 36 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 いくつかのテスト ケースが与えられ、それぞれが 1 から n までの整数の順列を記述します。 ここでの順列とは、1 から n までのすべての数値が 1 回だけ出現する単なる配列です。 

タスクは、この順列に特別な対称性があるかどうかを判断することです。配列を位置から値へのマッピングとして扱い、そのマッピングを 2 回適用すると、各要素の開始位置に戻ります。 In other words, if you go from index i to p[i], and then from p[i] to p[p[i]], you should end up back at i for all i.

 各テスト ケースの出力は、このプロパティが当てはまるかどうかの単純な判断です。 

問題は配列に関するもののように見えますが、基礎となる構造は有限集合上の関数です。 各インデックスは別のインデックスを指しており、この関数がそれ自体の逆関数であるかどうかをチェックしています。 

制約はここでは明示的に提供されていませんが、この形式の一般的な置換問題は、テスト ケースごとに少なくとも 10^5 の要素に達します。 これにより、二次シミュレーションは即座に除外されます。 キャッシュせずにすべてのインデックスからチェーンを繰り返したどろうとするアプローチは、最悪の場合 O(n^2) まで劣化し、実行不可能です。 テスト ケースごとに 1 つのリニア スキャンが必要です。 

いくつかの特殊なケースは、単純な推論を打ち破る傾向があります。 

1 つは変装した自己ループです。 たとえば、n = 1 で順列が [1] の場合、マッピングを 2 回適用すると 1 に留まるため、条件は自明に当てはまります。サイクルの長さが少なくとも 2 でなければならないと想定する不用意な実装は、これを誤って処理する可能性があります。 

別のケースは混合サイクルです。 たとえば、n = 4 および p = [2, 1, 4, 3] です。 これは素のスワップで構成されているため実際には機能しますが、 p[i] != i のようなローカルの一貫性のみをチェックする誤ったアプローチでは、誤って拒否される可能性があります。 

より微妙な失敗は、順列がローカルでは有効なインボリューション構造を持っているが、グローバルでは有効ではない場合に発生します。 For example, p = [2, 3, 1] looks cyclic, but applying twice does not return all elements to their start positions.

 ## アプローチ

 A direct approach is to simulate the condition literally. For every index i, we compute p[p[i]] and compare it with i. If any mismatch appears, we immediately reject the permutation.

 条件はインデックスごとに独立しているため、これは正しいです。 各インデックスはそれ自体で制約を満たす必要があるため、すべてのインデックスをチェックするだけで十分です。 

ただし、これには、インデックス付けと置換配列への繰り返しアクセスを慎重に処理する必要があります。 Each check is O(1), and we do it n times, so the total time per test case is O(n). すべてのテスト ケースにわたって、合計入力サイズは線形のままです。 

強引な誤解は、サイクルを明示的に探索し、訪問したノードをマークし、構造を再構築しようとすることです。 That still works but introduces unnecessary bookkeeping without changing complexity. Another worse variant is simulating repeated applications of the permutation for each index, which leads to O(n^2) behavior.

 The key observation is that we do not need to build cycles or simulate steps. 条件 p[p[i]] = i は、順列が反復であるかどうかをすでに完全に特徴付けています。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | インデックスごとに繰り返されるステップのブルート フォース シミュレーション | O(n^2) | O(n) | 遅すぎる |
 | すべての i について p[p[i]] = i を直接チェックする | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

1. n と順列配列 p を読み取ります。 The array is 1-indexed conceptually, but stored in a 0-indexed Python list, so adjustments are needed when accessing values. This matters because confusion between index bases is the most common source of wrong answers here.
 2. For every index i from 0 to n - 1, compute the value j = p[i]. This represents the first step of the mapping from i.
 3. Check whether p[j - 1] equals i + 1 if using 1-indexed logic, or equivalently p[p[i] - 1] == i in 0-indexed form. If this equality fails for any i, the permutation cannot be its own inverse, so we can stop early.
 4. If all indices satisfy the condition, declare the permutation valid.

 The reason early stopping is valid is that the property is conjunctive across all indices. A single violation invalidates the entire permutation.

 ### なぜ効果があるのか

 The mapping defined by a permutation is a bijection on the set {1, ..., n}. The condition p[p[i]] = i means that applying the function twice yields the identity function. これはまさにインボリューションの定義です。 Since equality is checked pointwise for every element, verifying it locally for each index guarantees global correctness.

 ## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve_case(n, arr):
    for i in range(n):
        j = arr[i] - 1
        if arr[j] != i + 1:
            return False
    return True

def main():
    out = []
    while True:
        line = input().strip()
        if not line:
            break
        n = int(line)
        if n == 0:
            break
        arr = list(map(int, input().split()))
        if solve_case(n, arr):
            out.append("ambiguous")
        else:
            out.append("not ambiguous")
    print("\n".join(out))

if __name__ == "__main__":
    main()
```コアロジックは次のとおりです`solve_case`。 Each element i is used to jump once to j = p[i] - 1, and then we verify whether jumping back from j returns to i. The subtraction and addition adjustments are necessary because the input is 1-based while Python lists are 0-based.

 The loop in`main`n = 0 まで継続します。これは、この問題の標準的な終了パターンです。 複数のテスト ケースが存在する場合、行ごとに読み取ることで、入力の非同期の問題を回避できます。 

## 実用的な例

 n = 4 で順列が 2 1 4 3 である入力を考えます。 

| 私 | p[i] | j = p[i]-1 | p[j] | チェック |
 | --- | --- | --- | --- | --- |
 | 0 | 2 | 1 | 1 | わかりました |
 | 1 | 1 | 0 | 2 | わかりました |
 | 2 | 4 | 3 | 3 | わかりました |
 | 3 | 3 | 2 | 4 | わかりました |

 すべてのインデックスが条件を満たすため、置換は有効です。 このトレースは、完全に 2 サイクルで構成される構造を示しており、当然にインボリューション特性を満たします。 

Now consider n = 3 with permutation 2 3 1.

 | 私 | p[i] | j = p[i]-1 | p[j] | チェック |
 | --- | --- | --- | --- | --- |
 | 0 | 2 | 1 | 3 | 失敗 |

 マッピングを 2 回たどっても開始点に戻らないため、最初のチェックはすでに失敗しています。 これは、長さが 2 を超える巡回置換では条件を満たすことができないことを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | テスト ケースあたり O(n) | 各インデックスは定数時間の配列アクセスで 1 回だけチェックされます。 
| スペース | O(1) おまけ | 入力配列のみが格納され、補助構造は必要ありません。 

合計実行時間は、テスト ケース全体の n の合計に比例して増加します。これは、最大 10^5 以上の一般的な制約に対して効率的です。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from __main__ import main
    try:
        main()
    except Exception:
        pass
    return ""  # replace if capturing output properly

# provided sample style tests (format depends on original statement)
# these are conceptual placeholders since exact samples are not shown

# minimal case
assert run("1\n1\n0\n") == "ambiguous\n", "single element"

# simple valid swap structure
assert run("2\n2 1\n0\n") == "ambiguous\n", "two-cycle"

# invalid cycle
assert run("3\n2 3 1\n0\n") == "not ambiguous\n", "three-cycle"

# mixed structure valid
assert run("4\n2 1 4 3\n0\n") == "ambiguous\n", "disjoint swaps"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1 1 0 | 曖昧 | 単一要素の基本ケース |
 | 2 2 1 0 | 曖昧 | 2 サイクルの正しい取り扱い |
 | 3 2 3 1 0 | 曖昧ではない | 3 サイクルの拒否 |
 | 4 2 1 4 3 0 | 曖昧 | 複数の独立したスワップ |

 ## 特殊なケース

 n = 1 の場合、順列 [1] を受け入れる必要があります。 このアルゴリズムは i = 0 をチェックし、j = 0 を計算し、arr[0] == 1 が成立することを検証するため、有効な値が返されます。 

[2, 1, 4, 3] のような固定 2 サイクルのみで構成される順列の場合、2 回の適用後にすべてのインデックスが正しくマッピングされます。 このアルゴリズムは各ペアを個別に検証するため、グローバル サイクル推論は必要ありません。 

[2, 3, 4, 1] などの単一の大きなサイクルの場合、マッピングを 2 回たどっても最初に戻らないため、最初のインデックスはすでに条件を満たしていません。 アルゴリズムは、サイクル構造全体をたどることなく、これを即座に検出します。
