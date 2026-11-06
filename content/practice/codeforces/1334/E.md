---
title: "CF 1334E - 除数パス"
description: "$D$ という数値が与えられており、そのすべての約数について推論する必要があります。 この問題は、頂点がすべて $D$ の約数であるグラフを定義します。 $x$ が $y$ で割り切れ、商 $x / y$ が素数の場合、約数 $y$ からより大きな約数 $x$ までの辺が存在します。"
date: "2026-06-11T15:59:29+07:00"
tags: ["codeforces", "competitive-programming", "combinatorics", "graphs", "greedy", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 1334
codeforces_index: "E"
codeforces_contest_name: "Educational Codeforces Round 85 (Rated for Div. 2)"
rating: 2200
weight: 1334
solve_time_s: 49
verified: false
draft: false
---

[CF 1334E - 除数パス](https://codeforces.com/problemset/problem/1334/E)

 **評価:** 2200
 **タグ:** 組み合わせ論、グラフ、貪欲、数学、数論
 **解決時間:** 49 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちには番号が与えられています$D$そして、そのすべての約数について推論する必要があります。 この問題は、頂点がすべて次の約数であるグラフを定義します。$D$。 約数からエッジが存在します$y$より大きな約数に$x$もし$x$で割り切れます$y$そして商$x / y$プライムです。 このようなエッジの重みは、次の約数の数です。$x$約数ではないもの$y$。 このタスクは、それぞれが約数のペアを求める複数のクエリに対して行われます。$v$そして$u$, このグラフ内のそれらの間の最短パスの数を数えます。 

制約は重要です。$D$と同じ大きさにすることができます$10^{15}$まであります$3 \cdot 10^5$クエリ。 すべての約数を列挙すると、$D$または、完全なグラフを明示的に構築することは、数値が大きい場合には実行不可能です。
