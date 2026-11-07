---
title: "CF 140E - ニューイヤーガーランド"
description: "入力配列の差分は、未知の配列内の連続する値がある位置から次の位置にどのように変化するかを表します。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "combinatorics", "dp"]
categories: ["algorithms"]
codeforces_contest: 140
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 100"
rating: 2600
weight: 140
solve_time_s: 123
verified: false
draft: false
---

[CF 140E - 新年のガーランド](https://codeforces.com/problemset/problem/140/E)

 **評価:** 2600
 **タグ:** 組み合わせ論、dp
 **解決時間:** 2 分 3 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 入力配列`differences`は、未知の配列内の連続する値がある位置から次の位置にどのように変化するかを説明します。 隠しシーケンスが呼び出された場合`hidden`、 それから：$$hidden[i + 1] = hidden[i] + differences[i]$$タスクは、すべての要素が包含範囲内に収まる完全な非表示シーケンスを生成できる有効な開始値の数を決定することです。`[lower, upper]`。 

重要な観察は、最初の値が選択されるとシーケンス全体が固定されるということです。 以降のすべての値は、差分を繰り返し適用することによって決定されます。 これは、任意の配列を検索しているのではなく、可能な開始番号のみを検索していることを意味します。 

開始値が`x`。 その後、シーケンス内のすべての要素は次のように記述できます。$$x + prefixSum$$どこ`prefixSum`は、その時点までの差の累積和を表します。 

制約は十分に大きいため、すべての可能なシーケンスを強引に列挙することは不可能です。 配列の長さは次のとおりです`10^5`したがって、ソリューションは線形時間で実行する必要があります。 プレフィックスに対するネストされた反復やシーケンスの再構築を繰り返し行うアプローチでは、速度が非常に遅くなります。 

いくつかの特殊なケースが重要です。 

一部のプレフィックスが値を許容範囲外に押し出すため、シーケンスはすぐに無効になる可能性があります。 例えば：```
differences = [100]
lower = 0
upper = 1
```両方の要素を範囲内に維持できる開始値はありません。 

もう 1 つの重要なケースは、シーケンスが境界に正確に接触したときに発生します。 包含境界が重要です。 例えば：```
differences = [1]
lower = 0
upper = 1
```から開始`0`与える`[0,1]`、これは有効です。 

最小プレフィックスはシーケンスの先頭ではなく後から現れる可能性があるため、負の累積合計も重要です。 

## アプローチ

 ブルートフォースソリューションでは、次の範囲内のすべての可能な開始値を試します。`lower`そして`upper`。 開始値ごとに、隠れたシーケンス全体を再構築し、すべての要素が有効な範囲内にあるかどうかを確認します。 

最初の値が選択されると、シーケンスが一意に決定されるため、これが機能します。 アルゴリズムは単純です。 

1. 開始値を選択します。 
2. 違いを繰り返し適用します。 
3. 値が許容範囲から外れた場合は停止します。 
4. 成功した開始値の数を数えます。 

問題はパフォーマンスです。 まであるかもしれません`200001`可能な開始値、および各検証ではすべての値を調べる必要がある場合があります。`10^5`違い。 最悪の場合の複雑さは次のようになります。$$O((upper - lower + 1) \cdot n)$$それは大きすぎます。 

重要な洞察は、シーケンスのすべての要素が開始値と異なるのはプレフィックスの合計だけであるということです。 考えられるすべてのシーケンスを個別にチェックする代わりに、開始値の許容範囲を直接決定できます。 

累積プレフィックス合計の範囲が次の場合、`minPrefix`に`maxPrefix`の場合、すべてのシーケンス値は次のようになります。$$start + prefix$$すべての値を範囲内に保つには:$$lower \le start + minPrefix$$そして$$start + maxPrefix \le upper$$並べ替えると次のようになります。$$lower - minPrefix \le start \le upper - maxPrefix$$この間隔内の有効な整数の数が答えです。 

| アプローチ | 時間計算量 | 空間の複雑さ | メモ |
 | --- | --- | --- | --- |
 | ブルートフォース | O((上 - 下 + 1) * n) | お(1) | 考えられるすべての開始値をテストします。 
| 最適 | O(n) | お(1) | プレフィックス合計範囲のみをトラックします。 

## アルゴリズムのチュートリアル

 1. 3 つの変数を初期化します。 

-`prefixSum = 0`-`minPrefix = 0`-`maxPrefix = 0`プレフィックスの合計は、現在のシーケンス要素が開始値からどれだけ離れているかを表します。 
2.`differences`一度に 1 つの要素を配列します。 
3. 現在の差を加算します`prefixSum`。 

これは、開始値を基準にして非表示シーケンス内の移動をシミュレートします。 
4. 更新:

 -`minPrefix = min(minPrefix, prefixSum)`-`maxPrefix = max(maxPrefix, prefixSum)`これらの値は、シーケンス内の任意の場所で発生する最小および最大のオフセットを追跡します。 
5. すべての差異を処理した後、有効な開始間隔を計算します。 

すべてのシーケンス値は次と等しいため、$$start + prefix$$私たちに必要なのは：$$lower \le start + minPrefix$$そして$$start + maxPrefix \le upper$$6. 不等式を並べ替えます。$$start \ge lower - minPrefix$$

$$start \le upper - maxPrefix$$7. 間隔内の整数の数を計算します。$$(upper - maxPrefix) - (lower - minPrefix) + 1$$8. 間隔が無効な場合は、戻り値を返します。`0`。 

### なぜ効果があるのか

 すべての非表示シーケンスは、その開始値によって完全に決定されます。 累積差分は、その開始点に対して後の要素のみをシフトします。 

プレフィックスの合計の最小値によってシーケンスがどの程度まで下がることができるかが決まり、プレフィックスの合計の最大値によってシーケンスがどの程度まで上昇できるかが決まります。 両極端を内側に保つ任意の開始値`[lower, upper]`すべての中間値も自動的に範囲内に保持されます。 

アルゴリズムは開始値の正確な許容間隔を計算するため、その間隔内の整数をカウントすると、有効なシーケンスの正確な数が得られます。 

## Python ソリューション```
from typing import List

class Solution:
    def numberOfArrays(self, differences: List[int], lower: int, upper: int) -> int:
        prefix_sum = 0
        min_prefix = 0
        max_prefix = 0

        for diff in differences:
            prefix_sum += diff
            min_prefix = min(min_prefix, prefix_sum)
            max_prefix = max(max_prefix, prefix_sum)

        smallest_start = lower - min_prefix
        largest_start = upper - max_prefix

        return max(0, largest_start - smallest_start + 1)
```実装は数学的導出に直接続きます。 

変数`prefix_sum`これまでに処理されたすべての差分の累積効果を追跡します。 すべてのシーケンス値は開始値にこの累積オフセットを加えたものに等しいため、実際に必要な情報は、発生した最小オフセットと最大オフセットだけです。 

ループは配列を 1 回処理し、更新します。`min_prefix`そして`max_prefix`。 これらは、開始値からの極端な偏差を表します。 

トラバーサルが完了すると、コードは開始値の有効な間隔を計算します。 表現:```
largest_start - smallest_start + 1
```包含区間内に存在する整数の数をカウントします。 

外側`max(0, ...)`有効な間隔が存在しない場合を処理します。 

## ソリューションに進む```
func numberOfArrays(differences []int, lower int, upper int) int {
    prefixSum := 0
    minPrefix := 0
    maxPrefix := 0

    for _, diff := range differences {
        prefixSum += diff

        if prefixSum < minPrefix {
            minPrefix = prefixSum
        }

        if prefixSum > maxPrefix {
            maxPrefix = prefixSum
        }
    }

    smallestStart := lower - minPrefix
    largestStart := upper - maxPrefix

    result := largestStart - smallestStart + 1

    if result < 0 {
        return 0
    }

    return result
}
```Go の実装は Python バージョンを厳密に反映しています。 Go は組み込みを提供していないため、`min`そして`max`整数の関数では、明示的な比較が使用されます。 

制約によってすべての値が 32 ビット整数の範囲内に快適に保たれるため、ここでは整数のオーバーフローは問題になりません。 

## 実用的な例

 ### 例 1```
differences = [1, -3, 4]
lower = 1
upper = 6
```プレフィックスの合計を計算します。 

| ステップ | 違い | プレフィックスの合計 | 最小プレフィックス | 最大プレフィックス |
 | --- | --- | --- | --- | --- |
 | 開始 | - | 0 | 0 | 0 |
 | 1 | 1 | 1 | 0 | 1 |
 | 2 | -3 | -2 | -2 | 1 |
 | 3 | 4 | 2 | -2 | 2 |

 次に、有効な開始値を計算します。$$smallestStart = 1 - (-2) = 3$$

$$largestStart = 6 - 2 = 4$$有効な開始は次のとおりです。```
3, 4
```答え：```
2
```### 例 2```
differences = [3, -4, 5, 1, -2]
lower = -4
upper = 5
```| ステップ | 違い | プレフィックスの合計 | 最小プレフィックス | 最大プレフィックス |
 | --- | --- | --- | --- | --- |
 | 開始 | - | 0 | 0 | 0 |
 | 1 | 3 | 3 | 0 | 3 |
 | 2 | -4 | -1 | -1 | 3 |
 | 3 | 5 | 4 | -1 | 4 |
 | 4 | 1 | 5 | -1 | 5 |
 | 5 | -2 | 3 | -1 | 5 |

 境界を計算します。$$smallestStart = -4 - (-1) = -3$$

$$largestStart = 5 - 5 = 0$$可能な開始:```
-3, -2, -1, 0
```答え：```
4
```### 例 3```
differences = [4, -7, 2]
lower = 3
upper = 6
```| ステップ | 違い | プレフィックスの合計 | 最小プレフィックス | 最大プレフィックス |
 | --- | --- | --- | --- | --- |
 | 開始 | - | 0 | 0 | 0 |
 | 1 | 4 | 4 | 0 | 4 |
 | 2 | -7 | -3 | -3 | 4 |
 | 3 | 2 | -1 | -3 | 4 |

 境界を計算します。$$smallestStart = 3 - (-3) = 6$$

$$largestStart = 6 - 4 = 2$$以来：$$6 > 2$$有効な開始値はありません。 

答え：```
0
```## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | シングルパススルー`differences`|
 | スペース | お(1) | 少数の変数のみが保存されます。 

このアルゴリズムは各差異を 1 回だけ処理し、追加の配列やデータ構造を保存することはありません。 これは、次の問題の制約内に簡単に適合します。`10^5`要素。 

## テストケース```
from typing import List

class Solution:
    def numberOfArrays(self, differences: List[int], lower: int, upper: int) -> int:
        prefix_sum = 0
        min_prefix = 0
        max_prefix = 0

        for diff in differences:
            prefix_sum += diff
            min_prefix = min(min_prefix, prefix_sum)
            max_prefix = max(max_prefix, prefix_sum)

        smallest_start = lower - min_prefix
        largest_start = upper - max_prefix

        return max(0, largest_start - smallest_start + 1)

sol = Solution()

assert sol.numberOfArrays([1, -3, 4], 1, 6) == 2  # provided example 1
assert sol.numberOfArrays([3, -4, 5, 1, -2], -4, 5) == 4  # provided example 2
assert sol.numberOfArrays([4, -7, 2], 3, 6) == 0  # provided example 3

assert sol.numberOfArrays([1], 0, 1) == 1  # exact boundary fit
assert sol.numberOfArrays([0, 0, 0], 1, 1) == 1  # constant sequence
assert sol.numberOfArrays([100], 0, 1) == 0  # impossible due to large jump
assert sol.numberOfArrays([-1, -1, -1], -3, 0) == 1  # decreasing sequence
assert sol.numberOfArrays([1, 1, 1], 1, 4) == 1  # increasing sequence
assert sol.numberOfArrays([1, -1, 1, -1], 0, 0) == 1  # oscillating sequence
```| テスト | なぜ |
 | --- | --- |
 |`[1, -3, 4], 1, 6`| サンプルの動作を検証する |
 |`[3, -4, 5, 1, -2], -4, 5`| 正と負の接頭語が混在したテスト |
 |`[4, -7, 2], 3, 6`| テスト不可能な間隔 |
 |`[1], 0, 1`| 包含境界処理をテストします。 
|`[0, 0, 0], 1, 1`| テストは同じ値を繰り返します。 
|`[100], 0, 1`| 特大の違いをテストする |
 |`[-1, -1, -1], -3, 0`| 負の累積移動をテストします。 
|`[1, 1, 1], 1, 4`| 累積的な動きの正のテスト |
 |`[1, -1, 1, -1], 0, 0`| プレフィックスの合計を交互にテストします。 

## 特殊なケース

 重要なエッジ ケースの 1 つは、有効な間隔が単一の値に崩れるときに発生します。 例えば：```
differences = [1, 1, 1]
lower = 1
upper = 4
```プレフィックスの合計は次のとおりです。`0, 1, 2, 3`。 唯一の有効な開始値は、`1`。 不用意に実装すると、間隔エンドポイントが誤って排他的として扱われ、返される可能性があります。`0`の代わりに`1`。 実装では、包含的なカウントを使用するため、これが正しく処理されます。`+1`。 

すべての差がゼロの場合、別の厄介なケースが発生します。```
differences = [0, 0, 0]
lower = 5
upper = 5
```すべてのシーケンス要素は開始値と同一のままです。 プレフィックスの最小値と最大値の合計はどちらもゼロのままであるため、有効な間隔は正確に次のようになります。`[5,5]`。 プレフィックス範囲は決して変更されないため、アルゴリズムはこれを自然に処理します。 

3 番目の重要なエッジ ケースは、選択された開始値に関係なく、シーケンス範囲が許容範囲を超える場合に発生します。```
differences = [4, -7, 2]
lower = 3
upper = 6
```最小有効開始値が最大有効開始値を超えているため、必要な開始間隔は無効になります。 実装では、負の間隔の長さを計算して返すことでこれを検出します。`0`。 

最後の微妙なケースには、トラバーサルの後半で現れる負のプレフィックス合計が含まれます。```
differences = [5, -10, 3]
```最小プレフィックスは先頭には現れません。 グローバルな最小値と最大値のプレフィックスを追跡せずに、現在の値を境界に対してチェックするだけの実装では、誤った結果が生成される可能性があります。 アルゴリズムは、トラバーサル全体を通じて両方の極値を維持することでこれを回避します。
