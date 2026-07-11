---
title: "CF 103176J - たったの \\$10 メモ"
description: "$[n]={1,2,dots,n}$ とし、$mathcal{A}$ を、すべての $alpha,betainmathcal{A}$ に対して $alphacapbetaneqvarnothing$ が含まれるような $[n]$ の $r$-部分集合の族とする。 $rle n/2$ と仮定します。 目標は、$$ Let $mathcal{B}={[n]setminus alpha : alphainmathcal{A}}$ を証明することです。"
date: "2026-07-03T16:44:09+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103176
codeforces_index: "J"
codeforces_contest_name: "La Salle-Pui Ching Programming Challenge 2019"
rating: 0
weight: 103176
solve_time_s: 131
verified: false
draft: false
---

[CF 103176J - 単なる \\$10 のメモ](https://codeforces.com/problemset/problem/103176/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 11 秒
 **確認済み:** いいえ

 ## 解決策
 ## セットアップ

 しましょう$[n]={1,2,\dots,n}$そしてさせてください$\mathcal{A}$の家族になる$r$-のサブセット$[n]$全員にとってそうなる$\alpha,\beta\in\mathcal{A}$1つは持っています$\alpha\cap\beta\neq\varnothing$。 仮定する$r\le n/2$。 目標は証明することです$$|\mathcal{A}|\le \binom{n-1}{r-1}.$$させて$\mathcal{B}={[n]\setminus \alpha : \alpha\in\mathcal{A}}$。 のあらゆる要素$\mathcal{B}$サイズがあります$n-r$。 

セットの場合$X\subseteq[n]$、表記$\partial_k \mathcal{B}$を示します$k$-の影$\mathcal{B}$、全員の家族を意味します$k$- の一部のメンバーに含まれるサブセット$\mathcal{B}$。 

設定しました$k=n-2r$、これは非負であるため、$r\le n/2$。 

## 解決策

 それぞれ$B\in\mathcal{B}$サイズがあります$n-r$したがって、正確に含まれています$$\binom{n-r}{n-2r}=\binom{n-r}{r}$$サイズのサブセット$n-2r$。 全体をまとめると$B\in\mathcal{B}$合計発生数を与える$$I=\sum_{B\in\mathcal{B}} \binom{n-r}{r}=|\mathcal{A}|\binom{n-r}{r}.$$今修正してください$(n-2r)$-サブセット$X\subseteq[n]$。 させて$$\mathcal{B}(X)=\{B\in\mathcal{B}: X\subseteq B\}.$$そういったものごとに$B$に相当する$r$-サブセット$\alpha=[n]\setminus B$に含まれる$X^c$、サイズがあります$2r$。 したがって$\mathcal{B}(X)$家族と困惑している$$\mathcal{A}(X)=\{\alpha\in\mathcal{A} : \alpha\subseteq X^c\},$$それぞれどこで$\alpha$です$r$-固定のサブセット$2r$-セット$X^c$。 

家族$\mathcal{A}(X)$交差点は制限下で保存されるため、交差したままになります。 したがって$\mathcal{A}(X)$交差する家族です$r$- のサブセット$2r$-要素セット。 

極端な場合のエルデシュ・コ・ラド定理による$n=2r$、の交差するファミリー$r$- のサブセット$2r$-set には最大のサイズがあります$$\binom{2r-1}{r-1}.$$したがって、$$|\mathcal{B}(X)| \le \binom{2r-1}{r-1}.$$すべてを合計してください$(n-2r)$-サブセット$X$。 そのようなサブセットの数は$\binom{n}{n-2r}=\binom{n}{2r}$。 それぞれ$B\in\mathcal{B}$まさに貢献する$\binom{n-r}{r}$そのようなサブセット$X$その中に含まれています。 したがって、発生数は同じです$I$も満たす$$I \le \binom{n}{2r}\binom{2r-1}{r-1}.$$両方の式を組み合わせると、$I$収量$$|\mathcal{A}|\binom{n-r}{r} \le \binom{n}{2r}\binom{2r-1}{r-1}.$$二項係数を階乗形式で書き直すと、$$\binom{n}{2r}=\frac{n!}{(2r)!(n-2r)!},\quad
\binom{2r-1}{r-1}=\frac{(2r-1)!}{(r-1)!r!},\quad
\binom{n-r}{r}=\frac{(n-r)!}{r!(n-2r)!}.$$置換により得られるもの$$|\mathcal{A}| \le
\frac{n!}{(2r)!(n-2r)!}\cdot \frac{(2r-1)!}{(r-1)!r!}\cdot \frac{r!(n-2r)!}{(n-r)!}.$$キャンセル$(n-2r)!$そして$r!$これを単純化すると$$|\mathcal{A}| \le \frac{n!}{(2r)!}\cdot \frac{(2r-1)!}{(r-1)!}\cdot \frac{1}{(n-r)!}.$$使用する$\frac{(2r-1)!}{(2r)!}=\frac{1}{2r}$を取得します。$$|\mathcal{A}| \le \frac{n!}{(n-r)!}\cdot \frac{1}{2r(r-1)!}.$$以来$\frac{n!}{(n-r)!}=n(n-1)\cdots(n-r+1)$、再グループ化すると、$$|\mathcal{A}| \le \frac{(n-1)!}{(r-1)!(n-r)!}=\binom{n-1}{r-1}.$$これで証明は完了です。 ∎

 ## 検証

 すべてのペアがあるため、二重カウントは一貫しています。$(X,B)$と$|X|=n-2r$そして$X\subseteq B$両方向で 1 回カウントされます: 固定で 1 回$B$そして選択する$X\subseteq B$、そして一度修正することで$X$そして選択する$B\supseteq X$。 

の場合への還元$2r$-element グラウンド セットは有効です。$X^c$サイズがあります$2r$そしてすべての$\alpha\in\mathcal{A}(X)$です$r$-のサブセット$X^c$。 

バウンド$|\mathcal{A}(X)|\le \binom{2r-1}{r-1}$シャープなEKR極限ケースを採用$n=2r$、すべての交差するファミリーが 1 つの星に含まれるため、サイズは最大でも次の数になります。$r$- 固定要素を含むサブセット。 

すべての項が正であり、かつ正であるため、すべての要因キャンセルは平等を維持します$r\le n/2$すべての二項係数が定義されていることを確認します。 

## 注意事項

 引数は、$2r$- スター構造を強制するほど交差条件が厳しくなるポイント制限。 シャドウパラメータ$n-2r$はまさにそれぞれを可能にする寸法です$B\in\mathcal{B}$極値を埋め込みながら、均一な数の低次元証人を生成する$2r$- 境界を管理する構成。
