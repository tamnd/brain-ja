---
title: "CF 103109A - ポク\u00e9mon の順列"
description: "$kappat$ をセクションで定義された関数とし、$$M ge mut Nquad Longleftrightarrowquad kappat(M) ge N,$$ for $t ge 2$ という意味で逆 $mut$ を使用します。"
date: "2026-07-03T21:13:47+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103109
codeforces_index: "A"
codeforces_contest_name: "mBIT Advanced Spring 2021"
rating: 0
weight: 103109
solve_time_s: 156
verified: false
draft: false
---

[CF 103109A - Pok\u00e9mon 順列](https://codeforces.com/problemset/problem/103109/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 36 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$\kappa_t$はセクションで定義された関数であり、inverse になります。$\mu_t$という意味で$$M \ge \mu_t N \quad \Longleftrightarrow \quad \kappa_t(M) \ge N,$$のために$t \ge 2$。 させて$\lambda_{t-1} M$を指します$(t-1)$の二項表現における -レベルの寄与$\kappa_t(M)$, そのため、次の構造からの定義的な分解が行われます。$\kappa_t$与える$$\kappa_t(M) = M + \lambda_{t-1} M.$$このアイデンティティは、次の表現から導き出されます。$\kappa_t(M)$二項寄与の合計として、最上位の項は$M$そして残りの貢献はまさに$(t-1)$- に適用される構造$M$。 

まず次のように仮定します$M \ge \mu_t N$。 の定義特性により、$\mu_t$、これは次と同等です$\kappa_t(M) \ge N$。 の分解を代入すると、$\kappa_t(M)$収量$$M + \lambda_{t-1} M \ge N.$$逆に仮定すると、$M + \lambda_{t-1} M \ge N$。 同じ分解を使用して左側を書き直すと、次のようになります。$\kappa_t(M) \ge N$したがって、次の定義的な等価性により、$\mu_t$、$$M \ge \mu_t N.$$各ステップは次の分解において等式を使用するため、両方の意味は可逆的です。$\kappa_t(M)$そして間の定義的な等価性$\mu_t$そして$\kappa_t$。 したがって、$$M \ge \mu_t N \quad \Longleftrightarrow \quad M + \lambda_{t-1} M \ge N.$$これで証明は完了です。 ∎
