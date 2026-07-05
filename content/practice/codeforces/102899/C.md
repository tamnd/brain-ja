---
title: "CF 102899C - KK \u7b97\u65e5\u671f"
description: "$n ge m ge 1$ と $a1 ge a2 ge cdots ge am ge 1$ を $n$ の分割とし、$ 実際、$a1$ が最大部分で $am$ が最小部分であれば、条件は $a1 - am le 1$ を与え、したがって $am in {a1, a1 - 1}$ になります。 したがって、すべての部分は $a1$ または $a1 - 1$ のいずれかに等しくなります。"
date: "2026-07-04T08:21:56+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102899
codeforces_index: "C"
codeforces_contest_name: "The 2nd Hangzhou Normal University Freshman Programming Contest"
rating: 0
weight: 102899
solve_time_s: 177
verified: false
draft: false
---

[CF 102899C - KK \u7b97\u65e5\u671f](https://codeforces.com/problemset/problem/102899/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 57 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$n \ge m \ge 1$そしてさせてください$a_1 \ge a_2 \ge \cdots \ge a_m \ge 1$～のパーティションになる$n$そのような$|a_i - a_j| \le 1$みんなのために$i,j$。 この場合、個別の部分値のセットには最大 2 つの連続する整数が含まれます。 

確かに、もし$a_1$は最大部分であり、$a_m$は最小部分であり、条件は次のとおりです$a_1 - a_m \le 1$したがって、$a_m \in {a_1, a_1 - 1}$。 したがって、すべての部分は次のいずれかに等しい$a_1$または$a_1 - 1$。 

させて$k$に等しい部分の数を示します$a_1$。 それから$m-k$等しい部分$a_1 - 1$、和の条件は次のようになります。$$n = k a_1 + (m-k)(a_1 - 1).$$展開すると得られる$$n = k a_1 + m a_1 - k a_1 - m + k = m(a_1 - 1) + k.$$したがって、$$k = n - m(a_1 - 1).$$以来$0 \le k \le m$、整数$a_1$によって制約されています$$m(a_1 - 1) \le n \le m(a_1 - 1) + m,$$これは以下と同等です$$a_1 - 1 \le \frac{n}{m} \le a_1.$$したがって$a_1 = \left\lceil \frac{n}{m} \right\rceil$。 書き込み$n = qm + r$と$0 \le r < m$、これは次の結果をもたらします$q = \left\lfloor \frac{n}{m} \right\rfloor$そして$$a_1 =
\begin{cases}
q, & r = 0,\\
q+1, & r > 0.
\end{cases}$$もし$r = 0$、 それから$k = n - m(q-1) = mq - m(q-1) = m$、つまりすべての部分が等しい$q$。 

もし$r > 0$、 それから$a_1 = q+1$、 そして$$k = n - m q = r.$$したがって、まさに$r$等しい部分$q+1$、残りは$m-r$等しい部分$q$。 

パーティションは増加しないため、最初の$r$部品は$q+1$そして残りの$m-r$部品は$q$、それで、$j$番目の部分は$$a_j =
\begin{cases}
q+1, & 1 \le j \le r,\\
q, & r < j \le m.
\end{cases}$$より大きな値に等しい部分の数は合計制約によって一意に決定され、最大でも異なる値の他の選択肢はないため、最適にバランスされたパーティションはすべてこの形式でなければなりません。$1$必要な合計を満たすことができます。 これで証明は完了です。 ∎
