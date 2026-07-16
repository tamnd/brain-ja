---
title: "CF 103428C - 代入または乗算"
description: "Let $q$ be a primitive $m$th root of unity. $1 le i le t$ を持つ各 $i$ に対して、$$ni = m ai + bi, qquad 0 le bi < m,$$ と書き、$N = mA + B$ となるように $$N = n1 + cdots + nt、qquad A = a1 + cdots + at、qquad B = b1 + cdots + bt,$$ を設定します。"
date: "2026-07-03T09:41:14+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103428
codeforces_index: "C"
codeforces_contest_name: "The 2021 CCPC Weihai Onsite"
rating: 0
weight: 103428
solve_time_s: 62
verified: false
draft: false
---

[CF 103428C - Assign or Multiply](https://codeforces.com/problemset/problem/103428/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 1分2秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$q$原始的になる$m$統一の根。 それぞれについて$i$と$1 \le i \le t$、 書く$$n_i = m a_i + b_i, \qquad 0 \le b_i < m,$$そしてセット$$N = n_1 + \cdots + n_t, \qquad A = a_1 + \cdots + a_t, \qquad B = b_1 + \cdots + b_t,$$となることによって$N = mA + B$。 

の$q$-多項係数は次のように定義されます。$$\binom{N}{n_1,\ldots,n_t}_q = \frac{[N]!_q}{[n_1]!_q \cdots [n_t]!_q}.$$演習 49 より、すべてのペアについて$(n,k)$1つは因数分解を持っています$$\binom{n}{k}_q = \binom{\lfloor n/m \rfloor}{\lfloor k/m \rfloor}\binom{n \bmod m}{k \bmod m}_q.$$多項係数は伸縮分解を認めます$$\binom{N}{n_1,\ldots,n_t}_q
=
\binom{N}{n_1}_q
\binom{N-n_1}{n_2}_q
\cdots
\binom{n_t}{n_t}_q,$$キャンセルが相次ぎましたので、$q$-階乗収量$$\frac{[N]!_q}{[n_1]!_q \cdots [n_t]!_q}
=
\frac{[N]!_q}{[n_1]!_q [N-n_1]!_q}
\cdot
\frac{[N-n_1]!_q}{[n_2]!_q [N-n_1-n_2]!_q}
\cdots
\frac{[n_t]!_q}{[n_t]!_q}.$$各因子について、演習 49 の二項結果を適用します。最初の因子については、次のようになります。$$\binom{N}{n_1}_q
=
\binom{A}{a_1}
\binom{B}{b_1}_q,$$以来$N = mA + B$そして$n_1 = ma_1 + b_1$。 

取り外し後$n_1$、残りのパラメータは次のとおりです。$$N^{(1)} = N - n_1 = m(A-a_1) + (B-b_1),$$同じ分解を繰り返すと、それぞれについて次のようになります。$j$、$$\binom{N - (n_1+\cdots+n_{j-1})}{n_j}_q
=
\binom{A - (a_1+\cdots+a_{j-1})}{a_j}
\binom{B - (b_1+\cdots+b_{j-1})}{b_j}_q.$$これらの恒等式を掛け合わせると、$j = 1,\ldots,t$整数多項式部分と$q$-多項部分。 整数因数は次のように伸縮します。$$\binom{A}{a_1}\binom{A-a_1}{a_2}\cdots\binom{a_t}{a_t}
=
\binom{A}{a_1,\ldots,a_t},$$一方$q$-ファクターは望遠鏡を適用します$$\binom{B}{b_1}_q\binom{B-b_1}{b_2}_q\cdots\binom{b_t}{b_t}_q
=
\binom{B}{b_1,\ldots,b_t}_q.$$両方の部分を組み合わせると、$$\binom{N}{n_1,\ldots,n_t}_q
=
\binom{A}{a_1,\ldots,a_t}
\binom{B}{b_1,\ldots,b_t}_q.$$交代で戻る$A = \sum_{i=1}^t \lfloor n_i/m \rfloor$そして$B = \sum_{i=1}^t (n_i \bmod m)$指定された拡張子が生成されます。$$\boxed{
\binom{n_1+\cdots+n_t}{n_1,\ldots,n_t}_q
=
\binom{\sum_{i=1}^t \lfloor n_i/m \rfloor}{\lfloor n_1/m \rfloor,\ldots,\lfloor n_t/m \rfloor}
\binom{\sum_{i=1}^t (n_i \bmod m)}{n_1 \bmod m,\ldots,n_t \bmod m}_q
}.$$これで証明は完了です。 ∎
