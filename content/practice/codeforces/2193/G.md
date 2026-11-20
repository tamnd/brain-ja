---
title: "CF 2193G - ツリー内のパス"
description: "$n$ 個の頂点の無向接続非循環グラフであるツリーが与えられます。 ツリーには頂点のペアの間に 1 つの単純なパスがあるため、2 つの非表示の頂点 $x$ と $y$ によって一意のパスが定義されます。"
date: "2026-06-07T20:53:34+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "interactive", "sortings", "trees"]
categories: ["algorithms"]
codeforces_contest: 2193
codeforces_index: "G"
codeforces_contest_name: "Codeforces Round 1076 (Div. 3)"
rating: 2100
weight: 2193
solve_time_s: 185
verified: false
draft: false
---

[CF 2193G - ツリー内のパス](https://codeforces.com/problemset/problem/2193/G)

 **評価:** 2100
 **タグ:** dfs および類似の、インタラクティブ、並べ替え、ツリー
 **解決時間:** 3 分 5 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 与えられたツリーは、無向で接続された非循環グラフです。$n$頂点。 2 つの隠れた頂点、$x$そして$y$、ツリーは頂点のペア間に単純なパスを 1 つだけ持つため、一意のパスを定義します。 私たちはその正体を知りません$x$そして$y$ですが、選択した 2 つの頂点間のパスが正しいかどうかを尋ねることによって、ツリーと対話することができます。$a$そして$b$の間の隠しパスと交差します$x$そして$y$。 私たちの目標は、最大でも次のコマンドを使用して、隠れたパス上で少なくとも 1 つの頂点を見つけることです。
