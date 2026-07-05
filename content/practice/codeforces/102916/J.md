---
title: "CF 102916J - ロストアイランド"
description: "$a1 ge a2 ge cdots ge am ge 1$ を、最適にバランスがとれた $m$ 部分への $n$ の分割であるとします。つまり $t$ を $x$ に等しい部分の数、$m-t$ を $x-1$ に等しい部分の数とします。 パーティションの合計は $$n = tx + (m-t)(x-1) = mx - (m-t) になります。"
date: "2026-07-04T08:03:44+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102916
codeforces_index: "J"
codeforces_contest_name: "Samara Farewell Contest 2020 (XXI Open Cup, Grand Prix of Samara)"
rating: 0
weight: 102916
solve_time_s: 152
verified: false
draft: false
---

[CF 102916J - ロストアイランド](https://codeforces.com/problemset/problem/102916/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 32 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$a_1 \ge a_2 \ge \cdots \ge a_m \ge 1$～のパーティションになる$n$の中へ$m$パーツが最適なバランスで配置されているということは、$|a_i-a_j|\le 1$みんなのために$1\le i,j\le m$。 この条件は、最大部分と最小部分の差が最大でも異なることを要求するのと同じです。$1$、だから、もし$a_1 = x$、すべての部分が満たされます$a_m \in {x, x-1}$他の値はあり得ません。 

させて$t$に等しい部分の数になる$x$そして$m-t$に等しい部品の数$x-1$。 パーティションには合計があります$$n = tx + (m-t)(x-1) = mx - (m-t).$$を解決する$x$与える$$mx = n + m - t,\quad x = \frac{n+m-t}{m}.$$以来$x$は整数です、$n+m-t \equiv 0 \pmod m$したがって、$t \equiv n \pmod m$。 書く$$n = mq + r,\quad 0 \le r < m,$$それで$q = \lfloor n/m \rfloor$そして$r = n \bmod m$。 

の式に代入すると、$n$、$$n = m q + r.$$最適にバランスの取れたパーティションでは、最大でも異なる部分を使用する必要があります。$1$したがって、可能な値は次のとおりです。$q$そして$q+1$。 させて$t$に等しい部分の数になる$q+1$、 そして$m-t$に等しい部品の数$q$。 この場合、合計制約は次のようになります。$$n = t(q+1) + (m-t)q = mq + t.$$と比較して$n = mq + r$与える$t = r$。 したがって、まさに$r$部品は$q+1$そして残りの$m-r$部品は$q$。 

これにより、シーケンスが強制的に非増加となり、より大きな部分がすべて最初に配置されるため、一意のパーティションが決定されます。$$a_1 = \cdots = a_r = q+1,\quad a_{r+1} = \cdots = a_m = q.$$最適なバランスを確認するために、パーツのペアは次のいずれかで異なります。$0$または$1$存在する唯一の値は$q$そして$q+1$、 それで$|a_i-a_j|\le 1$が成立する。 他のパーティションは$m$部分はこの商と余りの分布から逸脱する必要があり、その場合、一部の部分は少なくとも$q+2$またはせいぜい$q-1$、合計制約または最小スプレッドの非増加条件のいずれかに矛盾します。 

したがって、最適にバランスされたパーティションが 1 つだけ存在します。 

の$j$したがって、 番目の部分は$$a_j =
\begin{cases}
\left\lfloor \frac{n}{m} \right\rfloor + 1 & \text{if } 1 \le j \le n \bmod m,\\[6pt]
\left\lfloor \frac{n}{m} \right\rfloor & \text{if } n \bmod m < j \le m.
\end{cases}$$これで証明は完了です。 ∎
