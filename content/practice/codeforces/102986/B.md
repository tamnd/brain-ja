---
title: "CF 102986B - チェリオの共有"
description: "$alpha$ を $t$ の組み合わせとすると、$alpha$ は ${0,1,dots,n-1}$ の $t$ 要素のサブセットになります。 演算子 $partialt alpha$ は、$alpha$ の 1 つの要素を削除することによって得られるすべての $(t-1)$ の組み合わせを生成します。 $alpha={ct,dots,c1}$ の場合、$$partialt alpha={alphasetminus{cj}mid 1le jle t} となります。"
date: "2026-07-04T02:56:17+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102986
codeforces_index: "B"
codeforces_contest_name: "UTPC Contest 03-05-21 Div. 2 (Beginner)"
rating: 0
weight: 102986
solve_time_s: 151
verified: false
draft: false
---

[CF 102986B - Cheerios の共有](https://codeforces.com/problemset/problem/102986/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 31 秒
 **確認済み:** いいえ

 ## 解決策
 しましょう$\alpha$になる$t$-組み合わせなので、$\alpha$です$t$-要素のサブセット${0,1,\dots,n-1}$。 

オペレーター$\partial_t \alpha$すべてを生み出す$(t-1)$- の要素を 1 つ削除した組み合わせ$\alpha$。 もし$\alpha={c_t,\dots,c_1}$、 それから$$\partial_t \alpha=\{\alpha\setminus\{c_j\}\mid 1\le j\le t\}.$$の各要素$\partial_t \alpha$したがって、$(t-1)$-の組み合わせ${0,1,\dots,n-1}$。 

オペレーター$\partial_{t+1} \alpha$すべてを生み出す$(t+1)$-を含む組み合わせ$\alpha$、まだ存在しない 1 つの新しい要素を隣接させることによって取得されます。$\alpha$。 もし$\overline{\alpha}={0,1,\dots,n-1}\setminus \alpha$、 それから$$\partial_{t+1} \alpha=\{\alpha\cup\{x\}\mid x\in \overline{\alpha}\}.$$の各要素$\partial_{t+1} \alpha$したがって、$(t+1)$-を含む組み合わせ$\alpha$。
