---
title: "CF 103987C - 一列に並べる"
description: "$G = (V,E)$ とし、$g$ が演習 236(e) の意味で符号化されたエッジの族を表すものとすると、$g = bigcup{u-v in E}(eu sqcup ev)$ となり、独立集合の族はその演習と同様に拡張族代数の式で表現されます。"
date: "2026-07-02T06:09:24+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103987
codeforces_index: "C"
codeforces_contest_name: "2021 Huazhong University of Science and Technology Freshmen Cup"
rating: 0
weight: 103987
solve_time_s: 120
verified: false
draft: false
---

[CF 103987C - 一行で作成](https://codeforces.com/problemset/problem/103987/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$G = (V,E)$そしてさせてください$g$は、演習 236(e) の意味でエンコードされたエッジのファミリーを示します。$g = \bigcup_{u-v \in E}(e_u \sqcup e_v)$そして、独立集合の族は、その演習と同様に、拡張族代数の式で表現されます。 

派閥$G$頂点の集合です$C \subseteq V$異なる頂点のすべてのペアが$C$のエッジで接続されています$G$。 させて$G^c$エッジファミリーを持つ同じ頂点セット上の補数グラフを示します$g^c$すべての順序付けされていないペアで構成されます$u-v$入っていない$E$。 それからセット$C$の派閥です$G$それが独立したセットである場合に限り、$G^c$。 これにより、クリーク列挙が次のように変換されます。$G$の独立したセット列挙に$G^c$。 

させて$f_{\mathrm{ind}}(g)$エッジファミリーを持つグラフの独立したセットのファミリーを示します$g$、演習 236(e) に​​記載されているとおり。 それから、派閥の家族は、$G$は$$f_{\mathrm{clique}}(G) = f_{\mathrm{ind}}(g^c).$$最大クリーク$G$したがって、 はこの族の最大要素であるため、$$f_{\max\text{-clique}}(G) = \bigl(f_{\mathrm{ind}}(g^c)\bigr)^\uparrow.$$この式はすでに族代数の言語になっており、一度 ZDD 演算によって直接実装できます。$g^c$利用可能です。 補エッジ ファミリは次のように取得されます。$$g^c = \binom{V}{2} \setminus g,$$したがって、拡張族代数では、2 要素サブセットのレベルでの普遍族減算によって構築されます。 

頂点セット$U \subseteq V$でカバーできる$k$クリークが存在する場合に限り、クリーク$C_1, \dots, C_k$で$G$そのような$$U \subseteq C_1 \cup \cdots \cup C_k.$$同等に、それぞれ$C_i$独立したセットです$G^c$、 それで$U$でカバーできる$k$派閥$G$もし、そしてその場合に限り$U$でカバーできる$k$独立したセット$G^c$。 これは次のように述べているのと同じです$U$誘導された部分グラフの適切な色付けを許可します$(G^c \mid U)$せいぜい$k$色。各色クラスは独立したセットです。$G^c$。 

させて$F_k$は、以下でカバーできる頂点セットのファミリーを示します。$k$派閥$G$。 それから$$F_k = \{ U \subseteq V \mid U \text{ is $k$-colorable in } G^c \}.$$対象となる最大セット$k$その場合、派閥は$$F_k^\uparrow.$$この定式化は、問題を族代数における独立集合構成の繰り返し適用に帰着させます。$G^c$と組み合わせると、$k$- の素結合に対応する折り積構造$k$独立集合の家族。 具体的に言うと、$f = f_{\mathrm{ind}}(g^c)$は補数グラフの独立集合族であり、次の式でカバーできる集合族です。$k$クリークは次のように取得されます。$k$-フォールドユニオンクロージャ$$F_k = \underbrace{f \sqcup f \sqcup \cdots \sqcup f}_{k\ \text{times}},$$どこ$\sqcup$は、演習 236 の ZDD 代数で使用されるファミリーの素結合を示します。 

最大の$k$-clique-coverable セットは、最大値演算子を適用することで取得されます。$$F_k^\uparrow = \bigl(\underbrace{f \sqcup \cdots \sqcup f}_{k\ \text{times}}\bigr)^\uparrow.$$具体的なケースとしては、$G$は隣接する USA グラフ (18) であり、計算は次の ZDD を構築することによって進められます。$f_{\mathrm{ind}}(g^c)$補数グラフのエッジ ファミリを使用して、$\uparrow$最大要素を抽出する操作、そして最後に ZDD 共用体の構築を繰り返す$k$増加する回数$k$。 カーディナリティと極値要素を含む結果として得られるファミリーは、グラフ (18) の明示的な隣接構造に依存します。 与えられたコンテキストに (18) のエッジ リストが存在しない場合、最大クリークと最大クリークの最終的な列挙は、$k$-clique-coverable 頂点セットはインスタンス化できません。 

これで、ZDD 操作への族代数還元の導出と、必要な族の構造的特徴付けが完了します。 ∎
