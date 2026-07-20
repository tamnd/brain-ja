---
title: "CF 103643K - カード"
description: "$x を [0,1)$ に置き、その二項展開 $$x = 0.x1 x2 x3 ldots,qquad xj in {0,1} を書きます。$$ $rj(x)$ が $j$ 番目の Rademacher 関数、$$rj(x) = (-1)^{xj} を表すものとします。"
date: "2026-07-02T22:12:50+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103643
codeforces_index: "K"
codeforces_contest_name: "TeamsCode Spring 2022 Contest"
rating: 0
weight: 103643
solve_time_s: 135
verified: false
draft: false
---

[CF 103643K - カード](https://codeforces.com/problemset/problem/103643/K)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 15 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$x \in [0,1)$そしてその二項展開を書きます$$x = 0.x_1 x_2 x_3 \ldots,\qquad x_j \in \{0,1\}.$$させて$r_j(x)$を指します$j$-th Rademacher 関数、$$r_j(x) = (-1)^{x_j}.$$させて$k$バイナリ展開あり$$k = (b_m \cdots b_1 b_0)_2,\qquad b_j \in \{0,1\},$$Walsh 関数を次のように定義します。$$w_k(x) = \prod_{j \ge 0} r_{j+1}(x)^{b_j}.$$### 下の変換$x \mapsto 1-x$二項展開の場合は、$x$による$1-x$各 Rademacher 関数の符号が変わるという意味で、すべての 2 進数を反転します。$$r_j(1-x) = -r_j(x),$$以来$j$-番目の 2 進数は以下で補われます$x \mapsto 1-x$二項体系では、したがって$(-1)^{(1-x_j)} = -(-1)^{x_j}$。 

これを応用すると、$w_k$、$$w_k(1-x)
= \prod_{j \ge 0} r_{j+1}(1-x)^{b_j}
= \prod_{j \ge 0} (-r_{j+1}(x))^{b_j}.$$各要素は符号に寄与します$-1$まさにいつ$b_j = 1$したがって、$$w_k(1-x)
= (-1)^{\sum_{j \ge 0} b_j} \prod_{j \ge 0} r_{j+1}(x)^{b_j}.$$させて$\nu(k) = \sum_{j \ge 0} b_j$の数になります$1$-bits のバイナリ展開$k$。 それから$$w_k(1-x) = (-1)^{\nu(k)} w_k(x).$$### 主張されている身元との比較

 テストするステートメントは次のとおりです$$w_k(-x) = (-1)^k w_k(x).$$Walsh 関数は通常、$[0,1)$そして定期的に延長され、$-x$に対応する$1-x$この設定では。 導出された恒等式は、正しい指数がハミング重みに依存することを示しています。$\nu(k)$、オンではありません$k$自体。 

この主張に反証するには、次のようにします。$k=2$。 それから$k=(10)_2$、 それで$\nu(k)=1$。 上記のアイデンティティにより、$$w_2(1-x) = -w_2(x),$$その間$$(-1)^k = (-1)^2 = 1.$$したがって、$$w_2(1-x) \ne (-1)^k w_2(x)$$みんなのために$x$どこ$w_2(x) \ne 0$。 

主張されたアイデンティティは失敗します。$$\boxed{\text{False}}$$
