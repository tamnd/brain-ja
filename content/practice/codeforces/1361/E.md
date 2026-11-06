---
title: "CF 1361E - ジェームズと追跡者"
description: "この演習では、一意の因数分解領域 $S$ 上の多項式の内容と原始部分を含む 2 つの表示された恒等式の直接代数的単純化を求めます。"
date: "2026-06-11T12:47:51+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "graphs", "probabilities", "trees"]
categories: ["algorithms"]
codeforces_contest: 1361
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 647 (Div. 1) - Thanks, Algo Muse!"
rating: 3000
weight: 1361
solve_time_s: 173
verified: false
draft: false
---

[CF 1361E - ジェームズと追跡者](https://codeforces.com/problemset/problem/1361/E)

 **評価:** 3000
 **タグ:** dfs および同様のもの、グラフ、確率、ツリー
 **解決時間:** 2 分 53 秒
 **確認済み:** いいえ

 ## 解決策
 ## TAOCP 4.6.1.24 の正しい解決策

 この演習では、固有の因数分解ドメイン上の多項式の内容と原始部分を含む 2 つの表示された恒等式の直接代数的単純化を求めます。$S$。 目標は、新しい構造定理を導入することではなく、このセクションの後半ですでに説明されているよりきれいな形式と一致するまで、指定された式を書き直すことです。 

全体を通して重要なポイントは、両方とも$\operatorname{cont}(\cdot)$そして$\operatorname{pp}(\cdot)$固定因数分解によって定義される$$u(x) = \operatorname{cont}(u)\,\operatorname{pp}(u(x)),$$と$\operatorname{cont}(u) \in S$そして$\operatorname{pp}(u)$セクション 4.6.1 の意味でのプリミティブ。 この分解は、このセクションの規約に基づいて標準的なものであるため、この定義を超える追加の曖昧さは導入されず、必要ありません。 

## (19) ～ (20) の簡略化

 式 (19) は、積多項式の内容の係数レベルの定義です。 それは定義します$\operatorname{cont}(uv)$最大公約数として ($S$) 積のすべての係数の$uv$。 

書く$$u(x) = \sum_i u_i x^i, \quad v(x) = \sum_j v_j x^j.$$次に、次の係数$uv$要素の有限和です$u_i v_j$。 のすべての係数$uv$したがって、$S$-係数の積の線形結合$u$そして$v$。 

重要な観察は、次のすべての係数が$uv$のすべての係数の公約数で割り切れます。$u$、および、のすべての係数のすべての公約数によっても計算されます。$v$。 もし$d$すべてを分割します$u_i$、次に各係数$u_i v_j$で割り切れます$d$、したがって、のすべての係数$uv$で割り切れます$d$。 の公約数にも同じことが当てはまります。$v_j$。 したがって、次の係数のすべての公約数は、$u$そしての$v$は、の係数の公約数でもあります。$uv$、これは割り算を与えます$$\operatorname{cont}(u)\operatorname{cont}(v) \mid \operatorname{cont}(uv).$$逆に、次のすべての係数は、$u$で割り切れます$\operatorname{cont}(u)$、および のすべての係数$v$で割り切れます$\operatorname{cont}(v)$, したがって、すべての係数$u_i v_j$で割り切れます$\operatorname{cont}(u)\operatorname{cont}(v)$。 の係数なので、$uv$はそのような項の合計であり、それらはすべて次の式で割り切れます。$\operatorname{cont}(u)\operatorname{cont}(v)$。 したがって、$$\operatorname{cont}(uv) \mid \operatorname{cont}(u)\operatorname{cont}(v).$$両方の割り算により、UFD 内で単位までの等価性が強制され、セクション 4.6.1 で使用される標準規則 (係数イデアルの正規生成器として取られる内容) の下では、これにより単位の曖昧さが除去されます。 結果はまさに単純化された恒等式 (20) になります。$$\operatorname{cont}(uv) = \operatorname{cont}(u)\operatorname{cont}(v).$$(19) から (20) への「単純化」は、まさに、積の係数ごとの gcd が係数ごとの gcd の積に因数分解されるという認識です。なぜなら、割り算は、すでに存在するものを超える新しい共通因数を導入することなく、乗算と加算を通じて伝播するからです。 

## (24) を (24) に簡略化

 式 (24) は積のプリミティブ部分に関するものです。 因数分解の定義から始めて、$$u(x) = \operatorname{cont}(u)\operatorname{pp}(u(x)), \quad v(x) = \operatorname{cont}(v)\operatorname{pp}(v(x)),$$乗算して取得します$$uv = \operatorname{cont}(u)\operatorname{cont}(v)\,\operatorname{pp}(u)\operatorname{pp}(v).$$次に、原始部分の定義を適用します。多項式は、その内容とその原始部分の積として一意に記述され、原始部分は次の内容に等しいという特徴があります。$1$セクションの正規化で。 

まず内容を特定します$uv$。 すでに確立されているアイデンティティ (20) から、$$\operatorname{cont}(uv) = \operatorname{cont}(u)\operatorname{cont}(v).$$分ける$uv$この内容要素によって次のようになります。$$\operatorname{pp}(uv) = \frac{uv}{\operatorname{cont}(uv)} = \frac{\operatorname{cont}(u)\operatorname{cont}(v)\,\operatorname{pp}(u)\operatorname{pp}(v)}{\operatorname{cont}(u)\operatorname{cont}(v)}.$$コンテンツ要素は正確にキャンセルされ、残ります。$$\operatorname{pp}(uv) = \operatorname{pp}(u)\operatorname{pp}(v).$$これは直接的な代数的単純化です。キャンセルは完全に定義された因数分解内で行われるため、単位の調整や正規化の選択は必要ありません。 

したがって、式 (24) は、原始部分のきれいな乗法恒等式に直ちに帰着します。これは、このセクションで述べた単純化された形式です。 

## これらの単純化が有効な理由

 どちらのステップも、係数 gcd としてのコンテンツの定義プロパティと、コンテンツとプリミティブ部分を掛けた多項式の因数分解の定義のみに依存します。 最初の恒等式は、乗算時の係数の割り算の構造的特性です。 2 番目のアイデンティティは、最初のアイデンティティの結果を使用して因数分解を置き換え、内容用語をキャンセルした直接の結果です。 

単位、正規化規則、または外部構造補題に関する追加の仮定は必要ありません。 この簡略化は、セクション 4.6.1 で既に定められている定義内での純粋に代数的な書き換えです。
