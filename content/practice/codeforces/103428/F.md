---
title: "CF 103428F - ストーン"
description: "$q$ を 1 の原始 $m$ 乗根とし、$$N = n1 + cdots + nt.$$ とします。各インデックスを基底 $m$ の形式 $$ni = m ai + ri,qquad 0 le ri < m,$$ で書き、$N = mA + R$ となるように $$A = a1 + cdots + at,qquad R = r1 + cdots + rt,$$ を定義します。"
date: "2026-07-03T09:43:28+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103428
codeforces_index: "F"
codeforces_contest_name: "The 2021 CCPC Weihai Onsite"
rating: 0
weight: 103428
solve_time_s: 133
verified: false
draft: false
---

[CF 103428F - 石](https://codeforces.com/problemset/problem/103428/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 13 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$q$原始的になる$m$統一の根元とレット$$N = n_1 + \cdots + n_t.$$各インデックスをbaseに書き込みます$m$形状$$n_i = m a_i + r_i,\qquad 0 \le r_i < m,$$そして定義します$$A = a_1 + \cdots + a_t,\qquad R = r_1 + \cdots + r_t,$$となることによって$N = mA + R$。 

の$q$-多項係数は$$\binom{N}{n_1,\dots,n_t}_q
=
\frac{(q;q)_N}{(q;q)_{n_1}\cdots (q;q)_{n_t}},
\qquad (q;q)_n = \prod_{j=1}^n (1-q^j).$$任意の整数の場合$L \ge 0$を分解します。$q$-指数を剰余クラスにグループ化することによる階乗法$m$:$$(q;q)_{mL+R}
=
\left(\prod_{j=1}^{mL} (1-q^j)\right)
\left(\prod_{j=mL+1}^{mL+R} (1-q^j)\right).$$最初の製品に次のように書きます。$j = ms + u$と$1 \le u \le m$そして$0 \le s \le L-1$。 それから$$1 - q^{ms+u} = 1 - q^u$$以来$q^{ms}=1$。 したがって、$$\prod_{j=1}^{mL} (1-q^j)
=
\prod_{s=0}^{L-1}\prod_{u=1}^{m} (1-q^{ms+u})
=
\prod_{s=0}^{L-1}\left(\prod_{u=1}^{m}(1-q^u)\right)
=
\left(\prod_{u=1}^{m}(1-q^u)\right)^L.$$要因$u=m$は$1-q^m = 0$、したがって、各ブロック全体が消失係数に寄与します。 多項比では、これらの因子は分子と分母で同一の多重度で表示されます。$N,n_1,\dots,n_t$正確に含まれています$A$サイズの完全なブロック$m$。 このようなゼロ因数はすべて、円分法特殊化の商で相殺されます。$q^m=1$、非ゼロ剰余からの減少した寄与のみを残します。$1,\dots,m-1$不完全な最終ブロックと一緒に。 

完全キャンセル後$m$-blocks、各整数からの残りの寄与$n_i$その分解のみに依存します$n_i = m a_i + r_i$2 つの独立した部分に分割されます。1 つは$a_i$フルブロックと残差からのブロック$r_i$。 同じ分離が合計にも当てはまります$N = mA + R$。 

したがって、階乗比は「ブロック部分」と「剰余部分」の積に因数分解されます。$$\frac{(q;q)_N}{(q;q)_{n_1}\cdots(q;q)_{n_t}}
=
\left(\frac{(q;q)_A}{(q;q)_{a_1}\cdots(q;q)_{a_t}}\right)
\left(\frac{(q;q)_R}{(q;q)_{r_1}\cdots(q;q)_{r_t}}\right),$$ここで、両方の因子が同じ円分別特殊化で評価されます。$q^m=1$。 それぞれの要因はまた、$q$-多項係数。 

最初の要因は$$\binom{A}{a_1,\dots,a_t}_q,$$そして2番目の要素は$$\binom{R}{r_1,\dots,r_t}_q.$$したがって、$$\binom{n_1+\cdots+n_t}{n_1,\dots,n_t}_q
=
\binom{A}{a_1,\dots,a_t}_q
\binom{R}{r_1,\dots,r_t}_q.$$これで証明は完了です。 ∎$$\boxed{
\binom{n_1+\cdots+n_t}{n_1,\dots,n_t}_q
=
\binom{a_1+\cdots+a_t}{a_1,\dots,a_t}_q
\binom{r_1+\cdots+r_t}{r_1,\dots,r_t}_q
\quad (q^m=1,\ q\ \text{primitive})
}$$
