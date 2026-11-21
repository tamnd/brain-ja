---
title: "CF 293D - クシャとスクエア"
description: "頂点が整数の座標を持つ凸多角形が与えられます。 多角形の内部または境界上にあるすべての格子点、つまり整数座標を持つすべての点を考慮します。"
date: "2026-06-05T17:22:49+07:00"
tags: ["codeforces", "competitive-programming", "geometry", "math", "probabilities", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 293
codeforces_index: "D"
codeforces_contest_name: "Croc Champ 2013 - Round 2"
rating: 2700
weight: 293
solve_time_s: 43
verified: false
draft: false
---

[CF 293D - クシュシャとスクエア](https://codeforces.com/problemset/problem/293/D)

 **評価:** 2700
 **タグ:** 幾何学、数学、確率、2 つのポインター
 **解決時間:** 43 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 頂点が整数の座標を持つ凸多角形が与えられます。 多角形の内部または境界上にあるすべての格子点、つまり整数座標を持つすべての点を考慮します。 

個別の格子点のすべての順序付けされていないペアの中から、一様にランダムに 1 つのペアを選択します。 これら 2 つの点は正方形の向かい合う角になります。 タスクは、その正方形の予想面積を計算することです。 

正方形の幾何学は実際には簡単な部分です。 2 つの点が正方形の向かい合う頂点にあり、対角線の長さが$d$、すると辺の長さは$d/\sqrt 2$, したがって、面積は$$\frac{d^2}{2}.$$問題全体は次のようになります。 

\mathbb E[\text{面積}] = \frac12 \cdot \mathbb E[\text{二乗距離]()
