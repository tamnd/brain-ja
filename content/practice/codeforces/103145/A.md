---
title: "CF 103145A - マトリックス"
description: "κt(N) を $N$ の次数 $t$ の組み合わせ表現における主要なパラメータとし、κt(N) は $$binom{nt}{t} le N < binom{nt+1}{t} を満たす一意の整数 $nt$ とします。"
date: "2026-07-03T19:52:40+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103145
codeforces_index: "A"
codeforces_contest_name: "The 15th Chinese Northeast Collegiate Programming Contest"
rating: 0
weight: 103145
solve_time_s: 148
verified: false
draft: false
---

[CF 103145A - マトリックス](https://codeforces.com/problemset/problem/103145/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 28 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 κ_t(N) を次数の主要パラメータとします。$t$の組み合わせ表現$N$、したがって、κ_t(N) は一意の整数になります。$n_t$満足のいく$$\binom{n_t}{t} \le N < \binom{n_t+1}{t}.$$この特徴付けは、定理 K に暗黙的に含まれる貪欲な構築と演習 75 の表現から得られます。ここで、κ_t(N) は、$t$-の表現$N$。 

書く$n_t = \kappa_t(N)$。 すると余りが存在する$R$そのような$$N = \binom{n_t}{t} + R, \qquad 0 \le R < \binom{n_t+1}{t} - \binom{n_t}{t}.$$特に、定義上の不等式は次のことを意味します。$$0 \le R < \binom{n_t+1}{t} - \binom{n_t}{t},$$そしてそれ以来$\binom{n_t+1}{t} > \binom{n_t}{t}$、増分$N \mapsto N+1$変化$n_t$そのときだけ$R$次の二項しきい値へのキャリーが発生する前に、可能な最大値に達します。 

のために$N+1$、2つのケースがあります。 

もし$R+1 < \binom{n_t+1}{t} - \binom{n_t}{t}$、 それから$N+1$まだ同じ間隔にある$$\binom{n_t}{t} \le N+1 < \binom{n_t+1}{t},$$したがって、κ_t(N+1) = n_t、したがって κ_t(N+1) - κ_t(N) = 0 となります。 

もし$R$と互換性のある最大値に達します$n_t$、 それから$N+1$次の二項境界に到達する、つまり$$N+1 = \binom{n_t+1}{t}.$$この場合、κ_t の最大性プロパティによって次のことが強制されます。$$\kappa_t(N+1) = n_t+1,$$以来$\binom{n_t+1}{t}$より大きい先行インデックスを必要とする最初の値です$n_t$組み合わせ表現で。 

κ_t の定義上の不等式は増加することを示しているため、これより大きなジャンプは発生しません。$N$による$1$フォームの二項しきい値を最大 1 つ超えることができます$\binom{m}{t}$。 

したがって、$$\kappa_t(N+1) - \kappa_t(N) =
\begin{cases}
1, & \text{if } N+1 = \binom{m}{t} \text{ for some } m,\\[4pt]
0, & \text{otherwise}.
\end{cases}$$同様に、増分はまさに次のときに発生します。$N+1$です$t$パスカル三角形列挙の - 番目の二項係数。その他すべての場合、先頭の組み合わせインデックスは変更されません。$$\boxed{\kappa_t(N+1)-\kappa_t(N)\in\{0,1\},\ \text{equal to }1 \text{ iff } N+1=\binom{m}{t}\text{ for some }m.}$$これで解決策は完了です。 ∎
