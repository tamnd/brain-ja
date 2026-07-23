---
title: "CF 103828E - ナシームはどこですか？"
description: "$G(z)=sum{x1=0}^{1}cdotssum{xn=0}^{1} z^{x1+cdots+xn} f(x1,ldots,xn)$ を演習 25 で定義した生成関数とし、$F(p)$ を $p1=cdots=pn=p$ のときの信頼性多項式とします。 $$F(p)=sum{x1=0}^{1}cdotssum{xn=0}^{1} (1-p)^{1-x1}p^{x1}cdots…"
date: "2026-07-02T08:14:48+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103828
codeforces_index: "E"
codeforces_contest_name: "(DCPC + TCPC + BCPC) 2022"
rating: 0
weight: 103828
solve_time_s: 95
verified: false
draft: false
---

[CF 103828E - ナシームはどこですか?](https://codeforces.com/problemset/problem/103828/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 35 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$G(z)=\sum_{x_1=0}^{1}\cdots\sum_{x_n=0}^{1} z^{x_1+\cdots+x_n} f(x_1,\ldots,x_n)$を演習 25 で定義した母関数とし、$F(p)$は、次の信頼性多項式を示します。$p_1=\cdots=p_n=p$、 となることによって$$F(p)=\sum_{x_1=0}^{1}\cdots\sum_{x_n=0}^{1} (1-p)^{1-x_1}p^{x_1}\cdots (1-p)^{1-x_n}p^{x_n} f(x_1,\ldots,x_n).$$固定ベクトルの場合$x=(x_1,\ldots,x_n)$、ハミング重みに応じた積係数$w(x)=x_1+\cdots+x_n$、与える$$(1-p)^{1-x_1}p^{x_1}\cdots (1-p)^{1-x_n}p^{x_n}=(1-p)^{n-w(x)}p^{w(x)}.$$代替収量$$F(p)=\sum_{x} f(x)\, (1-p)^{n-w(x)} p^{w(x)}.$$因数分解$(1-p)^n$生成する$$F(p)=(1-p)^n \sum_{x} f(x)\left(\frac{p}{1-p}\right)^{w(x)}.$$残りの合計は次の定義に一致します。$G(z)$で評価されました$z=\frac{p}{1-p}$、 以来$z^{w(x)}$同じ指数構造で表示されます。 したがって、$$F(p)=(1-p)^n G\!\left(\frac{p}{1-p}\right).$$これは表現します$F(p)$母関数の単純な再スケーリングとして、単一の置換とそれに続く単項係数の乗算によって得られます。 これで解決策は完了です。 ∎
