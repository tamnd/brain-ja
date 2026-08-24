---
title: "CF 104289E - 非減少シーケンス"
description: "$f^{D}(x1,dots,xn)=overline{f(overline{x1},dots,overline{xn})}$ および $f^{R}(x1,dots,xn)=f(xn,dots,x1)$ とします。 合成すると $$f^{DR}(x)=overline{f(overline{xn},dots,overline{x1})},qquad f^{RD}(x)=overline{f(overline{xn},dots,overline{x1})},$$ が得られるので、$f^{DR}=f^{RD}$ は次のようになります。"
date: "2026-07-01T20:38:13+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104289
codeforces_index: "E"
codeforces_contest_name: "Bangladesh CP Server - BCS Round 1 (Div. 3)"
rating: 0
weight: 104289
solve_time_s: 120
verified: false
draft: false
---

[CF 104289E - 非減少シーケンス](https://codeforces.com/problemset/problem/104289/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$f^{D}(x_1,\dots,x_n)=\overline{f(\overline{x_1},\dots,\overline{x_n})}$そして$f^{R}(x_1,\dots,x_n)=f(x_n,\dots,x_1)$。 組成収量$$f^{DR}(x)=\overline{f(\overline{x_n},\dots,\overline{x_1})},\qquad
f^{RD}(x)=\overline{f(\overline{x_n},\dots,\overline{x_1})},$$それで$f^{DR}=f^{RD}$は、否定された変数の順序を逆にした後の同一の式から続きます。 

### (a)

 隠れ重み付けビット関数の場合$h_n$、値はハミング重みによって決定されます。$w(x)=x_1+\cdots+x_n$。 関数は変数を返します$x_{w(x)}$標準のインデックス付け規則に基づく$x_0=0$。 

反省のもと、$$h_n^R(x_1,\dots,x_n)=h_n(x_n,\dots,x_1),$$これは重みを変更せず、選択した座標のインデックスのみを変更します。 

二重化では、選択された変数と選択インデックスの両方が、次の依存性を通じて補完されます。$w(x)$したがって、組み合わせ効果により選択ルールが維持され、補完前の反転によって最初の座標の役割が周期的に並べ替えられます。 結果として得られる関数は、同じ重みに従って選択しますが、変数は 1 回回転されます。$$h_n^{DR}(x_1,\dots,x_n)=h_n(x_2,\dots,x_n,x_1).$$これにより識別されます$DR$引数リストに循環左シフトを使用して、$h_n$。 

### (b)

 しましょう$x=(x_1,\dots,x_n,x_{n+1})$。 ケースに分割$x_{n+1}$。 

もし$x_{n+1}=0$、のハミング重み$x$の重さに等しい$(x_1,\dots,x_n)$, したがって、最初のインデックスのうち選択されたインデックスは$n$座標は変わりません。 これにより、$$h_{n+1}(x_1,\dots,x_n,0)=h_n(x_1,\dots,x_n).$$もし$x_{n+1}=1$、体重は増加します$1$したがって、選択されたインデックスは 1 位置ずつシフトし、変数の効果的な順序がパート (a) のように回転されます。 したがって、関数は回転されたタプルに作用します$(x_2,\dots,x_n,x_1)$:$$h_{n+1}(x_1,\dots,x_n,1)=h_n(x_2,\dots,x_n,x_1).$$両方の場合を組み合わせると、$$h_{n+1}(x_1,\dots,x_{n+1})=(x_{n+1} ? h_n(x_2,\dots,x_n,x_1) : h_n(x_1,\dots,x_n)).$$### (c)

 マッピング$\psi$によって再帰的に定義されます$$\epsilon^\psi=\epsilon,$$

$$(x_1\cdots x_n0)^\psi=(x_1\cdots x_n^\psi)0,
\qquad
(x_1\cdots x_n1)^\psi=(x_2\cdots x_n x_1)^\psi 1.$$インボリューション、インダクションを表示するには$n$が適用されます。 

のために$n=0$、$\epsilon^{\psi\psi}=\epsilon$。 

仮定する$y^{\psi\psi}=y$すべての長さの文字列に対して$n$。 で終わる文字列の場合$0$、$$(x_1\cdots x_n0)^{\psi\psi}
=((x_1\cdots x_n^\psi)0)^\psi
=(x_1\cdots x_n^{\psi\psi})0
=(x_1\cdots x_n0).$$で終わる文字列の場合$1$、$$(x_1\cdots x_n1)^{\psi\psi}
=((x_2\cdots x_n x_1)^\psi 1)^\psi
=(x_2\cdots x_n x_1)^{\psi\psi}1
=(x_2\cdots x_n x_1)1.$$同じ構造回転を 2 回適用すると、再帰によって先頭のシンボルが端末によって制御される全サイクルにわたって移動されるため、元の順序が復元されます。$1$。 したがって、$\psi^2$すべての文字列に対して同じように動作するため、$\psi$は巻き込みです。 

### (d)

 パート (b) から、$h_n$は再帰を満たします。$x_{n+1}=1$ブランチは循環シフトを適用します$(x_1,\dots,x_n)$評価の前に。 地図$\psi$端末が起動するたびに、あらゆるレベルでこのシフトを巻き戻すように正確に構築されています。$1$入力の再帰的分解で が検出された場合、先頭のシンボルが前方に回転されるため、有効な引数の順序付けは再帰下で安定します。 

定義する$\hat{h}_n$再帰句の循環回転への依存を削除することで、次のようになります。$$\hat{h}_1(x_1)=x_1,
\qquad
\hat{h}_{n+1}(x_1,\dots,x_{n+1})=(x_{n+1} ? \hat{h}_n(x_1,\dots,x_n) : \hat{h}_n(x_2,\dots,x_n,x_1))$$回転が入力変換に吸収されます。 

の建設により、$\psi$、回転されたサブインスタンスが出現するたびに$h_n$の回転されていないインスタンスに対応します。$\hat{h}_n$で評価されました$x^\psi$、 それで$$h_n(x)=\hat{h}_n(x^\psi).$$のBDD$\hat{h}_n$各レベルは、個別の回転サブ関数を生成せず、再帰が継続するか終了するかだけを区別するため、単一のチェーン構造を持ちます。 各レベルでは最大 1 つの新しい個別のサブ関数が導入されるため、縮小順序図には共有爆発のない一連の決定ノードの線形シーケンスが含まれ、BDD のサイズが得られます。$O(n)$。 

これで証明は完了です。 ∎
