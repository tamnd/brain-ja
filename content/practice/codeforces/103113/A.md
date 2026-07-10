---
title: "CF 103113A - \u0423\u0440\u0430\u0432\u043d\u0435\u043d\u0438\u0435 \u0441 \u041d\u041e\u041a"
description: "$q$ を 1 の原始 $m$ 乗根とすると、$1le j<m$ の場合、$q^m=1$ および $q^jneq 1$ になります。 $n=am+r,quad k=bm+s,$ と書きます。ここで $0le r,s<m$ および $a=lfloor n/mrfloor$、$b=lfloor k/mrfloor$ とします。 ガウス二項係数は $binom{n}{k}q=frac{[n]q!}{[k]q!,[n-k]q!},qquad [t]q! です。"
date: "2026-07-03T22:35:47+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103113
codeforces_index: "A"
codeforces_contest_name: "\u0428\u0435\u0441\u0442\u0430\u044f \u041b\u0438\u043f\u0435\u0446\u043a\u0430\u044f \u043a\u043e\u043c\u0430\u043d\u0434\u043d\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430 \u0448\u043a\u043e\u043b\u044c\u043d\u0438\u043a\u043e\u0432 \u043f\u043e \u043f\u0440\u043e\u0433\u0440\u0430\u043c\u043c\u0438\u0440\u043e\u0432\u0430\u043d\u0438\u044e. \u0424\u0438\u043d\u0430\u043b. 8-11 \u043a\u043b\u0430\u0441\u0441\u044b"
rating: 0
weight: 103113
solve_time_s: 153
verified: false
draft: false
---

[CF 103113A - \u0423\u0440\u0430\u0432\u043d\u0435\u043d\u0438\u0435 \u0441 \u041d\u041e\u041a](https://codeforces.com/problemset/problem/103113/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 33 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$q$原始的になる$m$統一の根なので、$q^m=1$そして$q^j\neq 1$のために$1\le j<m$。 書く$n=am+r,\quad k=bm+s,$どこ$0\le r,s<m$そして$a=\lfloor n/m\rfloor$、$b=\lfloor k/m\rfloor$。 

ガウス二項係数は次のとおりです。$\binom{n}{k}_q=\frac{[n]_q!}{[k]_q!\,[n-k]_q!},\qquad [t]_q!=\prod_{i=1}^t \frac{1-q^i}{1-q}.$一定の要素$(1-q)^{-t}$商ではキャンセルされるため、$\binom{n}{k}_q=\prod_{i=1}^k \frac{1-q^{n-k+i}}{1-q^i}.$インデックスセット${1,2,\dots,k}$剰余クラスに分割されるモジュロ$m$。 それぞれ書いてください$i$ユニークに$i=jm+t,\quad j\ge 0,\quad 1\le t\le m,$どこ$t=m$の倍数を表します$m$。 分解すると因子が 2 つのタイプに分類されます。 

非倍数の場合$m$、つまり$t\in{1,\dots,m-1}$、 我々は持っています$q^{jm+t}=q^t$以来$q^m=1$。 したがって、そのような各要素は次の要素のみに依存します。$t$そしてオンではない$j$:$\frac{1-q^{n-k+jm+t}}{1-q^{jm+t}}=\frac{1-q^{(a-b)m+(r-s)+t}}{1-q^t}.$したがって、すべての非倍数因子は次の要素のみに依存します。$(r,s)$そして多数発生する$b$完全なブロックと次のように決定される長さの残りのブロック$s$; 彼らの製品はまさに$\binom{r}{s}_q$と同じ商品ですので、$\binom{r}{s}_q$全額キャンセル後$m$-定期的な繰り返し。 

の倍数の場合$m$、 取る$i=jm$と$1\le j\le b$。 次に、分子と分母の両方が消えます。$1-q^{jm}=0,\qquad 1-q^{n-k+jm}=1-q^{(a-b)m+r-s+jm}=1-q^{jm+r-s}.$以来$q^{jm}=1$、どちらも円分因子の一次ゼロとして動作します。$(1-x^m)$で$x=q^j$。 標準のローカル拡張の使用$1-x^m=(1-x)(1+x+\cdots+x^{m-1}),$での評価$x=q^j$対応する消失係数の比率が、$t$- シフト、および全員の貢献$b$このようなインデックスは通常の二項係数に等しい$\binom{a}{b}.$これを製品レベルで直接確認するには、グループ インデックスを使用します。$i=jm$分子と分母で。 の倍数によって寄与される要因$m$形状$\prod_{j=1}^b \frac{1-q^{(a-b)m+r-s+jm}}{1-q^{jm}}.$因数分解後$q^{jm}=1$そして、共通の消失線形項をキャンセルします。$(1-x^m)$、残りの非ゼロ定数は正確に組み立てられます。$\prod_{j=1}^b \frac{a-b+j}{j}=\binom{a}{b},$これは標準限界です$q$-Unity の原始根にある整数。 

倍数と非倍数の寄与を組み合わせると、$\binom{n}{k}_q=\binom{a}{b}\binom{r}{s}_q.$置き換える$a=\lfloor n/m\rfloor$、$b=\lfloor k/m\rfloor$、$r=n\bmod m$、$s=k\bmod m$収量$\binom{n}{k}_q=\binom{\lfloor n/m\rfloor}{\lfloor k/m\rfloor}\binom{n\bmod m}{k\bmod m}_q.$これで証明は完了です。 ∎
