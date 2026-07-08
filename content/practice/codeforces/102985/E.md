---
title: "CF 102985E - 食料の寄付"
description: "$alpha$ を $t$ の組み合わせとすると、$alpha$ は ${0,1,dots,n-1}$ の $t$ 要素のサブセットになります。 演算子 $partialt alpha$ は、$alpha$ の 1 つの要素を削除することによって得られるすべての $(t-1)$ の組み合わせを生成します。 $alpha={ct,dots,c1}$ の場合、$$partialt alpha={alphasetminus{cj}mid 1le jle t} となります。"
date: "2026-07-04T02:58:23+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102985
codeforces_index: "E"
codeforces_contest_name: "UTPC Contest 03-05-21 Div. 1 (Advanced)"
rating: 0
weight: 102985
solve_time_s: 112
verified: false
draft: false
---

[CF 102985E - 食料の寄付](https://codeforces.com/problemset/problem/102985/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 52 秒
 **確認済み:** いいえ

 ## 解決策
 しましょう$\alpha$になる$t$-組み合わせなので、$\alpha$です$t$-要素のサブセット${0,1,\dots,n-1}$。 

オペレーター$\partial_t \alpha$すべてを生み出す$(t-1)$- の要素を 1 つ削除した組み合わせ$\alpha$。 もし$\alpha={c_t,\dots,c_1}$、 それから$$\partial_t \alpha=\{\alpha\setminus\{c_j\}\mid 1\le j\le t\}.$$の各要素$\partial_t \alpha$したがって、$(t-1)$-の組み合わせ${0,1,\dots,n-1}$。 

オペレーター$\partial_{t+1} \alpha$すべてを生み出す$(t+1)$-を含む組み合わせ$\alpha$、まだ存在しない 1 つの新しい要素を隣接させることによって取得されます。$\alpha$。 もし$\overline{\alpha}={0,1,\dots,n-1}\setminus \alpha$、 それから$$\partial_{t+1} \alpha=\{\alpha\cup\{x\}\mid x\in \overline{\alpha}\}.$$の各要素$\partial_{t+1} \alpha$したがって、$(t+1)$-を含む組み合わせ$\alpha$。
