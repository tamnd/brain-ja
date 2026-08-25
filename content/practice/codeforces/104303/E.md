---
title: "CF 104303E - \u8bfb\u4e2d\u56fd\u6570\u5b57"
description: "$x in [0,1)$ を 3 項展開 $x = 0.x1 x2 x3 cdots quad (xj in {0,1,2}),$ とし、非終端表現を使用します。 $omega = e^{2pi i/3}$ と定義すると、$omega^3 = 1$、$1 + omega + omega^2 = 0$ となります。"
date: "2026-07-01T20:11:53+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104303
codeforces_index: "E"
codeforces_contest_name: "2023 Xiangtan Unversity Freshman Conteset"
rating: 0
weight: 104303
solve_time_s: 135
verified: false
draft: false
---

[CF 104303E - \u8bfb\u4e2d\u56fd\u6570\u5b57](https://codeforces.com/problemset/problem/104303/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 15 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$x \in [0,1)$三項展開がある$x = 0.x_1 x_2 x_3 \cdots \quad (x_j \in \{0,1,2\}),$ここでは非終了表現が使用されます。 

定義する$\omega = e^{2\pi i/3}$、 それで$\omega^3 = 1$そして$1 + \omega + \omega^2 = 0$。 

それぞれについて$j \ge 1$、三項 Rademacher 関数を定義します。$r_j(x) = \omega^{x_j}.$それぞれ$r_j(x)$のみに依存します$j$3 番目の桁で値を受け取ります${1,\omega,\omega^2}$。 

させて$k$3 項表現の非負の整数であること$k = k_0 + 3k_1 + 3^2 k_2 + \cdots,$有限数だけが存在する場所$k_j$非ゼロであり、それぞれ$k_j \in {0,1,2}$。 

三項ウォルシュ関数を定義する$w_k(x)$による$w_k(x) = \prod_{j \ge 1} r_j(x)^{k_{j-1}}.$同様に、$w_k(x) = \omega^{\sum_{j \ge 1} k_{j-1} x_j} = \omega^{\sum_{j \ge 0} k_j x_{j+1}}.$指数はモジュロで計算されます$3$、 以来$\omega^m$のみに依存する$m \bmod 3$。 

固定ごとに$k$、積は有限であるため、$k_j = 0$すべて十分に大きい$j$。 

させて$m \ge 1$に限定します$\sigma$-最初に生成された代数$m$3 進数。 それから$w_k$のみに依存する$x_1,\dots,x_m$いつでも$k_j = 0$のために$j \ge m$。 

整数の場合$k,\ell$3進数の場合$(k_j)$そして$(\ell_j)$、製品は満足しています$w_k(x)\,\overline{w_\ell(x)} = \omega^{\sum_{j \ge 1} (k_{j-1}-\ell_{j-1})x_j}.$直交性は、桁ごとに積分することで計算されます。 固定ごとに$j$、$\int_0^1 \omega^{a x_j}\,dx = \frac{1}{3}(1 + \omega^a + \omega^{2a})$どこ$a \in {0,1,2}$。 これは等しい$1$いつ$a \equiv 0 \pmod 3$と等しい$0$さもないと。 

3進数なので$x_j$は独立しており、ルベーグ法に基づいて一様に分布しています。$[0,1)$、積分は次のように因数分解します。 

= \prod_{j \ge 1} \frac{1}{3}\left(1 + \omega^{k_{j-1}-\ell_{j-1}} + \omega^{2(k_{j-1}-\ell_{j-1})}\right)。$$ Each factor equals $1$ if $k_{j-1} = \ell_{j-1}$ and equals $0$ otherwise. The product is therefore $1$ when $k = \ell$ and $0$ when $k \ne \ell$, yielding $$\int_0^1 w_k(x)\,\overline{w_\ell(x)}\,dx = \delta_{k\ell}.$$ システム$\{w_k\}$で完了です$L^2[0,1)$それはコンパクト・アーベル群の文字体系と一致するためです。$\prod_{j \ge 1} \mathbb{Z}/3\mathbb{Z}$三項展開によって与えられた識別の下で、文字は対応する正規直交基底を形成します。$L^2$空間。 間の数字マップの下で$[0,1)$および 3 値シーケンスでは、このグループはルベーグ測度による単位区間と測度保存的に同型です。 したがって、3 値ウォルシュ系は 2 進数と符号群を置き換えることによって得られます。$\{\pm 1\}$3 進数と 1 の 3 乗根の乗法グループを使用し、数字ごとの独立性を通じて直交性と完全性を維持します。 これで、ウォルシュ関数の三項一般化の構築が完了しました。 ∎
