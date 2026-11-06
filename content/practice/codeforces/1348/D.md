---
title: "CF 1348D - フェニックスと科学"
description: "$u(x)$ を $mathbb{Z}$ 上で自由平方である整数係数を持つ多項式とします。 これは、$u(x)$ が $mathbb{C}$ に反復根を持たないことを意味します。これは、$mathbb{Z}[x]$ に $gcd(u(x), u'(x)) = 1$ が含まれることを意味します。ここで、$u'(x)$ は $u(x)$ の導関数です。"
date: "2026-06-11T14:48:27+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "constructive-algorithms", "greedy", "implementation", "math"]
categories: ["algorithms"]
codeforces_contest: 1348
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 638 (Div. 2)"
rating: 1900
weight: 1348
solve_time_s: 173
verified: false
draft: false
---

[CF 1348D - フェニックスと科学](https://codeforces.com/problemset/problem/1348/D)

 **評価:** 1900
 **タグ:** 二分探索、構築的アルゴリズム、貪欲、実装、数学
 **解決時間:** 2 分 53 秒
 **確認済み:** いいえ

 ## 解決策
 ## 演習 4.6.2.23 の解決策を修正

 しましょう$u(x)$整数係数をもつ多項式であること。$\mathbb{Z}$。 これはつまり、$u(x)$～に繰り返されるルーツがない$\mathbb{C}$、同等に、$\gcd(u(x), u'(x)) = 1$で$\mathbb{Z}[x]$、 どこ$u'(x)$の派生です$u(x)$。 

素数の数が有限であることを示したい$p$そのような$u(x)$自由平方モジュロではありません$p$、つまり、次のような減少$\overline{u}(x) \in \mathbb{F}_p[x]$繰り返しのルートがあります。 

### ステップ 1: 平方自由度モジュロの特性評価$p$多項式$v(x)$野原の上に$F$スクエアフリーであるのは、次の場合に限ります。$\gcd(v(x), v'(x)) = 1$で$F[x]$。 したがって、$\overline{u}(x)$**は自由平方モジュロではありません$p$** もし、そしてその場合に限り$$\gcd\big(\overline{u}(x), \overline{u}'(x)\big) \neq 1 \quad \text{in } \mathbb{F}_p[x].$$同様に、非定数多項式が存在します。$d(x) \in \mathbb{F}_p[x]$両方を分割する$\overline{u}(x)$そして$\overline{u}'(x)$。 

### ステップ 2: 結果を使用する

 しましょう$R = \operatorname{Res}(u, u')$は**結果**を示します$u(x)$そして$u'(x)$。 結果は以下を満たします。$$R = 0 \quad \text{if and only if} \quad \gcd(u, u') \neq 1 \text{ in } \mathbb{C}[x].$$以来$u(x)$スクエアフリーは終わりました$\mathbb{Z}$、 我々は持っています$\gcd(u, u') = 1$以上$\mathbb{Z}[x]$、 それで$$R = \operatorname{Res}(u, u') \neq 0.$$結果は整数になります。 さらに、どの素数に対しても、$p$、の削減$u$モジュロ$p$満足する$$\overline{u}(x) \text{ is not squarefree over } \mathbb{F}_p \quad \iff \quad p \text{ divides } R.$$これは以下の理由から続きます。$\gcd(\overline{u}, \overline{u}') \neq 1$以上$\mathbb{F}_p[x]$結果がモジュロで消える場合にのみ$p$。 

### ステップ 3: 有限個の素数のみがゼロ以外の整数を割ります

 以来$R \in \mathbb{Z}$がゼロではない場合、素約数は有限個しかありません。 させて$p_1, p_2, \dots, p_k$はこれらの素数を表します。 次に、任意の素数について$p \notin \{p_1, \dots, p_k\}$、多項式$\overline{u}(x)$平方自由モジュロです$p$。 

### ステップ 4: 結論

 したがって、素数の集合は、$p$そのような$u(x)$**は自由平方モジュロではありません$p$** は、次の素数の有限集合に含まれます。$\operatorname{Res}(u, u')$。 したがって、そのような素数は有限個しか存在しません。$$\boxed{\text{Only finitely many primes } p \text{ make } u(x) \text{ not squarefree modulo } p.}$$### ステップ 5: オプションのコメント

 この引数は、次の根の明示的な計算を必要としません。$u(x)$または$u'(x)$。 これは、結果の代数的性質と、ゼロ以外の整数には有限の数の素約数があるという事実に純粋に依存しています。 

**解決策は終わりです。**
