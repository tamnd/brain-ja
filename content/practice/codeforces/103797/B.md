---
title: "CF 103797B - バスベット"
description: "$$G(z)=sum{xin{0,1}^n} f(x),z^{x1+cdots+xn}.$$ とすると、$$G(-1)=sum{x} f(x),(-1)^{ ここで $ f$ を $mathbb{R}$ に対する独自の多線形展開で書き、$$f(x)=sum{Ssubseteq [n]} aS prod{iin S} xi,$$ なので、$a{[n]}$ は完全単項式 $x1x2cdots xn$ の係数になります。"
date: "2026-07-02T08:47:06+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103797
codeforces_index: "B"
codeforces_contest_name: "IME++ Starters Try-outs 2022"
rating: 0
weight: 103797
solve_time_s: 52
verified: false
draft: false
---

[CF 103797B - バス ベット](https://codeforces.com/problemset/problem/103797/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 52 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$$G(z)=\sum_{x\in\{0,1\}^n} f(x)\,z^{x_1+\cdots+x_n}.$$それから$$G(-1)=\sum_{x} f(x)\,(-1)^{|x|},$$どこ$|x|=x_1+\cdots+x_n$。 

書く$f$独自の多重線形展開で$\mathbb{R}$、$$f(x)=\sum_{S\subseteq [n]} a_S \prod_{i\in S} x_i,$$となることによって$a_{[n]}$完全な単項式の係数です$x_1x_2\cdots x_n$。 

この展開を次のように置き換えると、$G(-1)$そして合計を交換すると、$$G(-1)=\sum_{S\subseteq[n]} a_S \sum_{x\in\{0,1\}^n} \left(\prod_{i\in S} x_i\right)(-1)^{|x|}.$$内和は次の場合を除き消滅します。$S=[n]$。 確かに、もし$j\notin S$次に、他の変数のすべての代入について、反転して得られる 2 つの代入$x_j$大きさが等しく、符号が反対であるため、$(-1)^{|x|}$因数の間に符号が変わります$\prod_{i\in S} x_i$は変わっていない。 これにより完全なキャンセルが行われます。 したがって、のみ$S=[n]$与えて生き残る$$G(-1)=a_{[n]}\sum_{x\in\{0,1\}^n} x_1x_2\cdots x_n(-1)^{|x|}.$$単一の課題のみ$x=(1,1,\dots,1)$貢献するので、$$G(-1)=a_{[n]}(-1)^n.$$したがって、$G(-1)\neq 0$暗示する$a_{[n]}\neq 0$、つまり、次の多線形多項式は$f$学位を持っています$n$。 

すべての FBDD は特にデシジョン ツリーであり、デシジョン ツリーの標準多項式手法は、あらゆる決定論的な意思決定構造の計算を意味します。$f$少なくとも表現する多線形多項式の次数の深さを持たなければなりません。 したがって、すべての FBDD は、$f$すべてをクエリするルートからリーフへのパスが含まれている必要があります。$n$変数。 

これはまさに回避の定義です。 

これで証明は完了です。 ∎
