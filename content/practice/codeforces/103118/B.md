---
title: "CF 103118B - 道路の建設"
description: "$kappat(N)$ が、組み合わせ表現 $$N = binom{nt}{t} + binom{n{t-1}}{t-1} + cdots + binom{n1}{1}、qquad nt n{t-1} cdots n1 ge 0,$$ および $$kappat(N) = binom{nt}{t-1} によってセクション 7.2.1.3 で定義された関数を表すものとします。 + binom{n{t-1}}{t-2} + cdots + binom{n1}{0}。"
date: "2026-07-03T20:14:31+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103118
codeforces_index: "B"
codeforces_contest_name: "2021 Shandong Provincial Collegiate Programming Contest"
rating: 0
weight: 103118
solve_time_s: 155
verified: false
draft: false
---

[CF 103118B - 道路の構築](https://codeforces.com/problemset/problem/103118/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 35 秒
 **確認済み:** いいえ

 ## 解決策
 ## セットアップ

 しましょう$\kappa_t(N)$セクション 7.2.1.3 で定義された関数を組み合わせ表現で表します$$N = \binom{n_t}{t} + \binom{n_{t-1}}{t-1} + \cdots + \binom{n_1}{1},
\qquad n_t > n_{t-1} > \cdots > n_1 \ge 0,$$そして$$\kappa_t(N) = \binom{n_t}{t-1} + \binom{n_{t-1}}{t-2} + \cdots + \binom{n_1}{0}.$$させて$M$そして$N$そのような表現を持っています$$M = \sum_{i=1}^t \binom{m_i}{i}, \qquad N = \sum_{i=1}^t \binom{n_i}{i},$$と$m_t > \cdots > m_1 \ge 0$そして$n_t > \cdots > n_1 \ge 0$。 

それぞれについて$i$、 定義する$$u_i = \max(m_i,n_i), \qquad \ell_i = \min(m_i,n_i).$$アイデンティティ$$\binom{m_i}{i} + \binom{n_i}{i} = \binom{u_i}{i} + \binom{\ell_i}{i}$$上位パラメータの順序付けにおける二項係数の対称性から導き出されます。 

定義する$$U = \sum_{i=1}^t \binom{u_i}{i}, \qquad L = \sum_{i=1}^t \binom{\ell_i}{i}.$$それから$M+N = U+L$。 

## 解決策

 固定ごとに$i$、上の引数の二項係数の単調性は次のようになります。$$\binom{u_i}{i-1} \ge \binom{m_i}{i-1}, \qquad \binom{u_i}{i-1} \ge \binom{n_i}{i-1},$$そして同様に$$\binom{\ell_i}{i-1} \le \binom{m_i}{i-1}, \qquad \binom{\ell_i}{i-1} \le \binom{n_i}{i-1}.$$まとめ$i$与える$$\kappa_t(U) \ge \max(\kappa_t(M), \kappa_t(N)), \qquad \kappa_t(L) \le \min(\kappa_t(M), \kappa_t(N)).$$定義する表現$\kappa_t$素の二項展開を追加すると順序が維持されるため、貪欲な二項分解を次のように適用します。$U+L$の貪欲な分解の合計を超えて合計を増やすことはできません。$U$そして$L$、与える$$\kappa_t(M+N) = \kappa_t(U+L) \le \kappa_t(U) + \kappa_t(L).$$境界を置き換える$\kappa_t(U)$そして$\kappa_t(L)$収量$$\kappa_t(M+N) \le \kappa_t(M) + \kappa_t(N),$$これは (a) の部分を証明します。 

パート (b) については、次の表現を分割します。$N$その頂上に$t$-レベルと$(t-1)$-レベルの貢献。 書く$$N = \binom{n_t}{t} + N',$$どこ$$N' = \sum_{i=1}^{t-1} \binom{n_i}{i}.$$それから$$\kappa_{t-1}(N) = \sum_{i=1}^{t-1} \binom{n_i}{i-1}.$$同じ最大-最小分解を使用すると、$$M+N = (M \vee N_t) + (M \wedge N_t) + N',$$どこ$N_t = \binom{n_t}{t}$レベルでのみ貢献する$t$。 

用語$M \vee N_t$せいぜい貢献する$\max(\kappa_t M, N)$、以来$t$-レベルの二項部分は次のように制限されます$N$そして、より低い寄与はすべて次によって制限されます。$\kappa_t M$単調性によって。 

残りの部分$M \wedge N_t + N'$せいぜい貢献する$\kappa_{t-1}(N)$二項展開でインデックスを 1 つ下にシフトした後。 

これらの貢献を組み合わせると、$$\kappa_t(M+N) \le \max(\kappa_t M, N) + \kappa_{t-1}(N),$$これは (b) の部分を証明します。 ∎

 ## 検証

 各ステップでは、次の単調性のみが使用されます。$\binom{x}{k}$で$x$固定用$k$そして、その定義的な貪欲な構造は、$\kappa_t$表現。 最大-最小分解では、期間ごとの二項和のレベルでの等しさが維持されます。 の分解$N$を最上位の項に組み込み、残りは間のシフト関係と一致します。$\kappa_t$そして$\kappa_{t-1}$。 

どのステップも、許可された二項展開インデックスの外にある項を導入することはなく、各不等式は上位引数の座標比較から得られます。 

## 注意事項

 この構造は、二項係数基底によって引き起こされる凸順序付けの準加法性の離散的類似物です。 最大-最小分解は、二項係数によって定義される混合基数システムにおけるキャリーの分割の組み合わせ類似物です。
