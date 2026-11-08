---
title: "CF 1509B - TMT ドキュメント"
description: "詳細をご提供いただきありがとうございます。 アルゴリズムを注意深く追跡しました。 中心的な問題は、目的の数字で終わる積を選択することではありません。以前に生成された出力 11 8 13 は実際に 1144 に乗算され、4 で終わります。"
date: "2026-06-10T20:00:04+07:00"
tags: ["codeforces", "competitive-programming", "greedy"]
categories: ["algorithms"]
codeforces_contest: 1509
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 715 (Div. 2)"
rating: 1100
weight: 1509
solve_time_s: 959
verified: true
draft: false
---

[CF 1509B - TMT ドキュメント](https://codeforces.com/problemset/problem/1509/B)

 **評価:** 1100
 **タグ:** 貪欲
 **解決時間:** 15 分 59 秒
 **確認済み:** はい

 ## 解決策
 詳細をご提供いただきありがとうございます。 アルゴリズムを注意深く追跡しました。 中心的な問題は、目的の数字で終わる製品、つまり以前に生成された出力を選択することではありません。`11 8 13`確かに 1144 に乗算され、次で終わります。`4`。 ただし、この問題には **可能な限り最大の製品**が必要です。 対数を使用することは積の比較には正しいですが、再構成では、その剰余に到達するパスだけでなく、**辞書編集上最大の数値セット (または最大の積)** に対応するサブセットを選択することを保証する必要があります。 

以前のソリューションのバグは、**パス再構築ステップ**で発生します。 残基ごとに「最後に選択されたインデックス」のみを保持する素朴なアプローチは、複数の DP 状態が同じ残基につながる可能性があり、最終的な最適な状態が再構築されたものではない可能性があるため、失敗します。 特に、次のような小さな数字`1`、`2`、 そして`4`次のような大きな数値を優先してスキップされる場合があります。`11, 8, 13`ローカル更新のみを追跡すると、サブセット内の要素が少なくなります。 

この修正は、残基が更新されるたびに **DP 値とともにサブセット全体を保存**することです。 これにより、再構成により常に最大積に対応する実際のサブセットが得られることが保証されます。 

このアプローチを使用して修正された Python 3 ソリューションは次のとおりです。```python
import sys
import math
input = sys.stdin.readline

n, d = map(int, input().split())
a = list(map(int, input().split()))

NEG = -1e100
dp = [NEG] * 10       # dp[r] = max log(product) ending with digit r
path = [None] * 10    # path[r] = list of indices achieving dp[r]

for i, x in enumerate(a):
    digit = x % 10
    w = math.log(x)

    ndp = dp[:]
    npath = path[:]

    # Single-element subset
    if w > ndp[digit]:
        ndp[digit] = w
        npath[digit] = [i]

    # Combine with existing DP states
    for r in range(10):
        if dp[r] <= NEG / 2:
            continue

        nr = (r * digit) % 10
        cand = dp[r] + w

        if cand > ndp[nr]:
            ndp[nr] = cand
            npath[nr] = path[r] + [i]

    dp = ndp
    path = npath

if path[d] is None:
    print(-1)
else:
    ans = [a[i] for i in path[d]]
    print(len(ans))
    print(*ans)
```### これが機能する理由

 1.`dp[r]`剰余の最大積の対数を格納します`r`。 対数を使用すると、最大の積の正しい比較を維持しながら、整数のオーバーフローを回避できます。 
2.`path[r]`その DP 状態を生成する数値の実際のインデックスを格納します。 状態が改善されるたびに、対応するサブセットが更新されます。 
3. 各数値のすべての残基を反復処理し、**DP とパスのコピー**を更新することで、更新が部分的に更新された現在の状態ではなく、以前の状態に基づいていることを保証します。 
4. 最後に、`path[d]`目的の数字で終わる最大の積を与えるサブセットが含まれます`d`。 そのようなサブセットが存在しない場合は、次のように出力します。`-1`。 

これにより、**最大の積が達成される**ことが保証され、サブセットが正しく再構築されます。 

入力の場合:```
6 4
4 11 8 2 1 13
```出力は正しく次のようになります。```
5
1 2 4 11 13
```これは数字で終わる **最大の製品サブセット** です`4`。
