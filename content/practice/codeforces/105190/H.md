---
title: "CF 105190H - アリの黒の見方"
description: "dp[n] を初期セット {1,2,...,n} に必要な演算の最小数とする。 最初の操作で x が選択されたとします。 要素 x はすぐに消えます。 x より小さい値はすべて変更されませんが、x より大きい値はすべて x だけ減らされます。"
date: "2026-06-27T04:20:35+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105190
codeforces_index: "H"
codeforces_contest_name: "Al-Baath Collegiate Programming Contest 2024"
rating: 0
weight: 105190
solve_time_s: 36
verified: true
draft: false
---

[CF 105190H - アリの黒の見方](https://codeforces.com/problemset/problem/105190/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 36 秒
 **確認済み:** はい

 ## 解決策
 しましょう`dp[n]`初期セットに必要な操作の最小数になります。`{1,2,...,n}`。 

最初の操作で選択したとします。`x`。 

要素`x`すぐに消えます。 以下のすべての値`x`より大きい値はすべて変更されません。`x`によって減少します`x`。 ゼロを削除した後、残りの値は正確に次の和集合になります。`{1,2,...,x-1}`そして`{1,2,...,n-x}`。 

2 つの部分は完全に独立しています。 値が重複しても問題が難しくなることはないため、残りの作業はこれら 2 つの範囲の大きい方によってのみ決定されます。 大きいサイズは、`max(x-1, n-x)`。 

したがって、`dp[n] = 1 + min(max(dp[x-1], dp[n-x]))`。 

最良の選択は常に範囲を可能な限り均等に分割することです。`max(x-1, n-x) = floor(n/2)`、

 より単純な反復を与える`dp[0] = 0`

`dp[n] = dp[n//2] + 1`。 

のために`n ≤ 100`、小さなDPで十分です。```python
import sys
input = sys.stdin.readline

n = int(input())

dp = [0] * (n + 1)
for i in range(1, n + 1):
    dp[i] = dp[i // 2] + 1

print(dp[n])
```この再帰は閉じた形式と同等です`dp[n] = floor(log2(n)) + 1`のために`n ≥ 1`。 したがって、たとえば、`n = 47`与える`5 + 1 = 6`、サンプルと一致します。
