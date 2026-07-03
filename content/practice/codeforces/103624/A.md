---
title: "CF 103624A - アン女王の復讐"
description: "$omega = e^{2pi i/3}$ とすると、$omega^3 = 1$、$1 + omega + omega^2 = 0$ となります。 基底 $3$ の各非負整数 $k$ を $$k = sum{j ge 0} kj 3^j,quad kj in {0,1,2} として書き込みます。"
date: "2026-07-02T22:42:04+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103624
codeforces_index: "A"
codeforces_contest_name: "UTPC Contest 03-25-22 Div. 2 (Beginner)"
rating: 0
weight: 103624
solve_time_s: 127
verified: false
draft: false
---

[CF 103624A - アン女王の復讐](https://codeforces.com/problemset/problem/103624/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 7 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$\omega = e^{2\pi i/3}$、 それで$\omega^3 = 1$そして$1 + \omega + \omega^2 = 0$。 それぞれの非負の整数を書き込みます$k$基地内で$3$として$$k = \sum_{j \ge 0} k_j 3^j, \quad k_j \in \{0,1,2\}.$$のために$x \in [0,1)$その三項展開を定義する$$x = \sum_{j \ge 1} x_j 3^{-j}, \quad x_j \in \{0,1,2\},$$最終的にではない表現を選択する$2$。 

を定義します$j$3進数関数$$\tau_j(x) = \lfloor 3^j x \rfloor \bmod 3,$$それで$\tau_j(x) = x_j$そしてそれぞれ$\tau_j(x)$長さの 3 進区間のみに依存します$3^{-j}$含む$x$。 

それぞれについて$k \ge 0$、三項ウォルシュ関数を定義します$$w_k(x) = \omega^{\sum_{j \ge 0} k_j \tau_{j+1}(x)}.$$この式は、桁数が有限であるため、明確に定義されています。$k_j$はゼロではないので、指数は有限和になります。$\mathbb{Z}/3\mathbb{Z}$。 

固定用$j$、関数$\tau_j(x)$各間隔で一定です$$\left[\frac{m}{3^j}, \frac{m+1}{3^j}\right), \quad 0 \le m < 3^j,$$したがってそれぞれ$w_k(x)$長さの 3 項区間で一定です$3^{-m}$、 どこ$m = 1 + \max{j : k_j \ne 0}$。 

もし$k$そして$\ell$ベースがあります-$3$拡張$k_j$そして$\ell_j$、 それから$$w_k(x)\, w_\ell(x) = \omega^{\sum_{j \ge 0} (k_j + \ell_j)\tau_{j+1}(x)}
= w_{k \oplus_3 \ell}(x),$$どこ$\oplus_3$は桁ごとの加算モジュロを示します$3$。 これで家族が特定される${w_k}$加法群の文字を使って$\bigoplus_{j \ge 0} \mathbb{Z}/3\mathbb{Z}$座標関数で評価される$\tau_j(x)$。 

直交性は 3 進数の独立性から得られます。 のために$k \ne 0$、 選ぶ$m$そのような$k_m \ne 0$。 パーティション$[0,1)$一定の長さの間隔に$3^{-m}$、それぞれのすべての数字$\tau_j(x)$のために$j \le m$を除いて固定されています$\tau_m(x)$、値を受け取ります$0,1,2$長さの部分区間にわたって均等に$3^{-(m+1)}$。 このような部分間隔では、関数$w_k(x)$因子を取得します$\omega^{k_m \tau_m(x)}$一方、他のすべての要素は一定のままです。 の 3 つの値を合計すると、$\tau_m(x)$与える$$1 + \omega^{k_m} + \omega^{2k_m} = 0,$$以来$k_m \in {1,2}$暗示する$\omega^{k_m}$は単一の原始立方根です。 したがって$$\int_0^1 w_k(x)\,dx = 0 \quad \text{for } k \ne 0.$$のために$k = \ell$、同じ桁ごとの分解により次の結果が得られます。$w_k(x)\overline{w_k(x)} = 1$したがって、$$\int_0^1 w_k(x)\overline{w_k(x)}\,dx = 1.$$のために$k \ne \ell$に同じ引数を適用します。$w_k(x)\overline{w_\ell(x)} = w_{k \oplus_3 (-\ell)}(x)$桁算術剰余$3$、これはある桁の位置がゼロ以外であり、まったく同じ方法でキャンセルされます。 したがって、$$\int_0^1 w_k(x)\overline{w_\ell(x)}\,dx = 0 \quad (k \ne \ell).$$機能${w_k(x)}_{k \ge 0}$～で正規直交系を形成する$L^2[0,1]$、各関数はbase-によって決定される文字です。$3$Walsh 関数とまったく同じ数字が基数に対応します。$2$文字。 これで Walsh システムの 3 項一般化が完了しました。 ∎
