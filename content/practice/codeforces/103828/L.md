---
title: "CF 103828L - 算術サブシーケンスがありません"
description: "$f(x1,ldots,xn)$ をブール関数とし、前の演習で定義したように、$$G(z)=sum{x1=0}^1 cdots sum{xn=0}^1 z^{x1+cdots+xn} f(x1,ldots,xn)$$ をその生成関数とします。"
date: "2026-07-02T08:16:58+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103828
codeforces_index: "L"
codeforces_contest_name: "(DCPC + TCPC + BCPC) 2022"
rating: 0
weight: 103828
solve_time_s: 129
verified: false
draft: false
---

[CF 103828L - 算術サブシーケンスがありません](https://codeforces.com/problemset/problem/103828/L)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 9 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$f(x_1,\ldots,x_n)$ブール関数にして、$$G(z)=\sum_{x_1=0}^1 \cdots \sum_{x_n=0}^1 z^{x_1+\cdots+x_n} f(x_1,\ldots,x_n)$$は、前の演習で定義した生成関数になります。 

させて$$F(p_1,\ldots,p_n)=\sum_{x_1=0}^1 \cdots \sum_{x_n=0}^1
\prod_{k=1}^n (1-p_k)^{1-x_k} p_k^{x_k}\, f(x_1,\ldots,x_n)$$は信頼性多項式になります。 

案件に特化した$p_1=\cdots=p_n=p$。 すると、積内のすべての用語は次のようになります。$(1-p)^{1-x_k}p^{x_k}$、つまりベクトルの重み$x=(x_1,\ldots,x_n)$のみに依存する$s=x_1+\cdots+x_n$。 製品を簡略化すると、$$\prod_{k=1}^n (1-p)^{1-x_k}p^{x_k} = (1-p)^{n-s} p^s.$$定義する$$A_s = \sum_{x_1=0}^1 \cdots \sum_{x_n=0}^1 [x_1+\cdots+x_n=s]\; f(x_1,\ldots,x_n),$$となることによって$A_s$カウント（重み付き）$f$) すべての代入は正確に$s$もの。 

この場合、信頼性多項式は次のようになります。$$F(p)=\sum_{s=0}^n A_s p^s (1-p)^{n-s}.$$母関数は次の条件を満たす$$G(z)=\sum_{s=0}^n A_s z^s,$$ハミング重みで項をグループ化しているため$s$まったく同じ係数を生成します$A_s$。 

今書き直してください$F(p)$因数分解によって$(1-p)^n$:$$F(p)=(1-p)^n \sum_{s=0}^n A_s \left(\frac{p}{1-p}\right)^s.$$内和は$G!\left(\frac{p}{1-p}\right)$母関数に代入することによって。 

したがって$$F(p)=(1-p)^n G\!\left(\frac{p}{1-p}\right).$$これは、母関数の観点から、等しいパラメータを持つ信頼性多項式を直接表現します。$$\boxed{F(p)=(1-p)^n\, G\!\left(\frac{p}{1-p}\right)}$$これで解決策は完了です。 ∎
