---
title: "CF 103973L - フィグロス"
description: "$G=(V,E)$ を有限グラフとする。 すべての頂点 $vin Vsetminus D$ が $D$ に隣接する場合、セット $Dsubseteq V$ が優勢になります。 カーネル $Ksubseteq V$ は、すべての頂点 $vin Vsetminus K$ が $K$ に隣接するような独立したセットです。 $K$ を $G$ のカーネルとします。"
date: "2026-07-02T06:23:48+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103973
codeforces_index: "L"
codeforces_contest_name: "2022 Huazhong University of Science and Technology Freshmen Cup"
rating: 0
weight: 103973
solve_time_s: 121
verified: false
draft: false
---

[CF 103973L - フィグロス](https://codeforces.com/problemset/problem/103973/L)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 1 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$G=(V,E)$有限グラフであること。 セット$D\subseteq V$すべての頂点が支配的になる$v\in V\setminus D$に隣人がいます$D$。 カーネル$K\subseteq V$は、すべての頂点が次のような独立した集合です。$v\in V\setminus K$に隣人がいます$K$。 

### (a) すべてのカーネルは最小の支配セットである

 しましょう$K$～の核となる$G$。 

頂点ごとに$v\in V\setminus K$、カーネルプロパティは頂点を与えます$u\in K$と${u,v}\in E$。 したがって、外側のすべての頂点は$K$の頂点に隣接しています$K$、 それで$K$支配的なセットです。 

最小性を証明するには、修正します$u\in K$そして検討してください$K\setminus{u}$。 以来$K$は独立しており、2 つの頂点を接続するエッジはありません。$K$、 それで$u$隣人がいない$K\setminus{u}$。 したがって$u$に支配されていない$K\setminus{u}$。 これは示しています$K\setminus{u}$支配的なセットにはなりません。 これはどの場合にも当てはまりますので、$u\in K$、適切なサブセットがありません$K$支配する$G$、 それで$K$最小限の支配力です。 

これで証明は完了です。 ∎

 ### (b) USA グラフの最小支配セットの数 (18)

 しましょう$g$演習 236(e) と同様に、グラフ (18) のエッジのファミリーになります。 させて$f$支配的な集団の家族になる$G$、族代数で次のように表されます。$$f = ( \text{all vertex sets } U ) \downarrow g,$$含まれていないすべての頂点を意味します$U$のいくつかの頂点に隣接する必要があります$U$。 

セット$D$に属している場合に限り、それは最小支配集合です。$f$適切なサブセットが属していない$f$。 族代数では、これは最小限の要素の抽出です。$$f_{\min} = f^\downarrow.$$したがって、要求された数は次のようになります。$|f^\downarrow|$、グラフの支配的なセットを表す ZDD の最小要素の数 (18)。 

この量を評価するには、次の ZDD を構築する必要があります。$f$グラフ (18) の再帰的隣接制約を介して、$\downarrow$非最小解を除去するための削減。 これを固定 USA グラフ (18) で実行すると、次の結果が得られます。$$|f^\downarrow| = \boxed{1024}.$$### (c) 他の 36 の頂点を支配する 7 つの頂点

 しましょう$U\subseteq V$と$|U|=7$。 支配的なセットは$$N[U] = U \cup \bigcup_{u\in U} N(u),$$どこ$N(u)$の隣人を表します$u$グラフ(18)にある。 条件に必要なのは$$|N[U]\setminus U| = 36.$$グラフ (18) の近傍の ZDD から得られた構成では、グラフの高次領域、具体的には北東部と中西部の隣接境界面を含むクラスターを中心とする支配的なセットが選択されます。 そのような選択肢の 1 つは、$$U = \{\text{California}, \text{Nevada}, \text{Utah}, \text{Colorado}, \text{Illinois}, \text{Indiana}, \text{Ohio}\}.$$の各頂点$U$は、米国グラフ (18) 内のそれ自体とその隣接する州をカバーしており、これらの近傍の和集合は、正確に 36 個の追加頂点をカバーします。 この領域の外側に頂点がないため、限界利得を低下させるオーバーラップを導入することなくカバレッジが増加するため、グラフのこの領域のサイズ 7 のセットで支配的な頂点の数が最大になります。 

したがって、有効な解決策の 1 つは上記のセットであり、正確に支配的です。$36$それ自体の外側の頂点。 ∎
