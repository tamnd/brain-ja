---
title: "CF 105018J - 乗算配列"
description: "特別な乗算構造を備えた 1 から n までのインデックスが付けられた配列が与えられますが、その構造は直接計算するものではありません。 代わりに、プロセスでは除数集計を使用して配列を繰り返し変換します。"
date: "2026-06-28T02:05:59+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105018
codeforces_index: "J"
codeforces_contest_name: "Winter Cup 5.0 Online Mirror Contest"
rating: 0
weight: 105018
solve_time_s: 30
verified: false
draft: false
---

[CF 105018J - 乗法配列](https://codeforces.com/problemset/problem/105018/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 30 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 特別な乗算構造を備えた 1 から n までのインデックスが付けられた配列が与えられますが、その構造は直接計算するものではありません。 代わりに、プロセスでは除数集計を使用して配列を繰り返し変換します。 

各ステップで、すべての位置 k が、k を分割するインデックスにあるすべての値の合計に置き換えられます。 この操作を m 回実行した後、最終的な配列を出力するように求められます。 

したがって、配列 A の関数 T を T(A)[k] = k を除算するすべての d にわたる A[d] の合計として定義すると、タスクは初期配列に m 回適用される T を計算することになります。 

入力の乗算特性は、直接シミュレーションにとっては危険な要素です。 The key effect comes entirely from repeated divisor convolution.

 制約は実際の信号です。 配列サイズは 100 万に達する可能性があり、反復回数は 10^18 に達する可能性があります。 最悪の場合、最大 10^24 の演算が必要となるため、反復ごとに O(n) 個の作業を実行するソリューションは直ちに不可能です。 Even O(n log n) per iteration is still far too large. This forces a solution where the transformation is understood as an algebraic operator that can be exponentiated or precomputed once.

 n が大きく、ほとんどの値が 0 である場合、微妙なエッジ ケースが発生します。 A naive simulation might skip zeros and assume sparsity helps, but divisor relationships still create dense propagation. たとえば、A[1] = 1、その他すべて 0 から開始すると、1 回の反復の後、すべての位置が 1 になります。これは、1 がすべてを除算するためです。 スパースシミュレーションは依然としてすべてのインデックスに影響します。 

もう 1 つの落とし穴は、乗算条件によって反復のダイナミクスが単純化されると仮定していることです。 It does not help reduce the divisor sum structure; 反復は値ではなくインデックスのみに依存します。 

## アプローチ

 直接的なアプローチは、文字通り定義に従います。 m 回の反復ごとに、k ごとに、k のすべての約数 d を反復し、A[d] を累積します。 n までのすべての数値の約数を事前計算すると、各反復コストは、k の約数の k のおおよその合計になり、約 O(n log n) になります。 With m up to 10^18, this is clearly infeasible because the transformation must be applied repeatedly, not just once.

 重要な点は、操作が線形でインデックスベースであるということです。 更新はインデックス間の約数関係にのみ依存するため、プロセス全体は、ベクトル A に固定の n × n 行列 M を乗算するものとみなすことができます。ここで、d が k を除算する場合は M[k][d] = 1、そうでない場合は 0 になります。問題は、M^m 倍の A を計算することになります。 

行列の直接累乗はサイズの関係で不可能ですが、M の構造は特殊です。これはディリクレ畳み込み代数の乗算となる除数畳み込みに対応します。 This means the operation is diagonalizable in the space of arithmetic functions using the Möbius transform framework.

 具体的には、整数に対する関数を定義すると、除数和の繰り返しは、除数ポセットに対する「ゼータ変換」の繰り返し適用に対応します。 The m-th application corresponds to applying a known combinational coefficient on each chain of divisibility. これは組み合わせの解釈につながります。k における各最終値は、k の約数における初期値の重み付けされた合計であり、重みは長さ m の約数チェーンを何回選択できるかによってのみ決まります。 

これにより、問題は、m ステップで d から k まで除数チェーンを拡張する方法の数に応じて係数を乗算した k のすべての約数 d からの寄与を k ごとに計算することになります。 n は最大 10^6 であるため、その係数は、約数と累乗に対する数論的 DP を使用して事前に計算できます。 

最終的な解決策は、除数遷移のふるいスタイルの前処理と、除数格子の深さの累乗を組み合わせたものとなり、m にわたる明示的な反復を回避します。

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォースシミュレーション | O(m · n log n) | O(n) | 遅すぎる |
 | 除数構造 DP + べき乗 | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 この演算を除数和変換の繰り返し適用として再解釈します。 反復をシミュレートする代わりに、m 層の除数拡張後に元の各位置が各最終位置に何回寄与するかを計算します。 

1. n までの k ごとに、en
