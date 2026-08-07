---
title: "CF 103990C - 正解"
description: "(18) のグラフ $G$ の頂点セットを $U$ とし、練習問題 236(e) のようにエンコードされたエッジのファミリーを $g$ とします。したがって、g$ の各 $e は $U$ の 2 要素のサブセットになります。 $C$ 内の個別の頂点のすべてのペアが $G$ のエッジである場合、頂点のセット $C subseteq U$ は $G$ 内のクリークです。"
date: "2026-07-02T06:04:28+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103990
codeforces_index: "C"
codeforces_contest_name: "2022 ICPC Asia Taiwan Online Programming Contest"
rating: 0
weight: 103990
solve_time_s: 44
verified: false
draft: false
---

[CF 103990C - 正解](https://codeforces.com/problemset/problem/103990/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 44 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$U$グラフの頂点セットになる$G$(18) で、$g$は、演習 236(e) のようにエンコードされたエッジのファミリーであるため、それぞれ$e \in g$の 2 要素のサブセットです$U$。 

頂点のセット$C \subseteq U$の派閥です$G$異なる頂点のすべてのペアが$C$の端です$G$。 同様に、$C$の派閥です$G$もし、そしてその場合に限り$C$補グラフ内の独立した集合です$\overline{G}$。 させて$\overline{g}$のエッジ族を示します$\overline{G}$。 

演習 236(e) と独立集合の族代数符号化から、エッジ族を持つグラフの独立集合の族$h$によって与えられます$$\mathrm{Ind}(h) = \mathcal{P}(U) \; \↘ \; h,$$という状態なので、$\alpha$どのエッジのスーパーセットでもない$e \in h$」はまさに、「エッジが完全に含まれていない」という言葉です。$\alpha$。 

これを応用すると、$\overline{G}$、派閥の家族$G$は$$\mathrm{Cliq}(G) = \mathcal{P}(U) \; \↘ \; \overline{g}.$$徒党$C$包含されるこのファミリーの最小要素に属する場合、 は最大になります。 したがって、最大クリークの族は次のようになります。$$\mathrm{MaxCliq}(G) = \bigl(\mathcal{P}(U) \; \↘ \; \overline{g}\bigr)^{\downarrow}.$$この ZDD 式は、次のすべての最大クリークのセットを決定します。$G$かつてはエッジファミリーだった$\overline{g}$が置き換えられ、削減ルールが適用されます。 

カバーできる集合を計算するには$k$派閥$G$、頂点サブセットを考えます$X \subseteq U$。 セット$X$でカバーできる$k$派閥$G$派閥が存在する場合にのみ$C_1, \dots, C_k \in \mathrm{Cliq}(G)$そのような$$X \subseteq C_1 \cup \cdots \cup C_k.$$同様に、次のすべての頂点は、$X$のいずれかに割り当てられます$k$派閥、だから$X$～への分割を許可します$k$それぞれがクリークであるサブセット。 補数グラフへの変換$\overline{G}$の各派閥$G$独立したセットです$\overline{G}$したがって、この条件はそれを要求することと同じです。$X$最大でも次の和集合です$k$独立したセット$\overline{G}$、これはまさに、誘導されたサブグラフが$\overline{G}[X]$は$k$- 着色可能。 

したがって、以下でカバーできる頂点セットのファミリーは、$k$派閥$G$は$$F_k = \{X \subseteq U \mid \chi(\overline{G}[X]) \le k\}.$$ZDD 形式では、これはすべての適切なファミリーを構築することによって取得されます。$k$-着色料$\overline{G}$独立セットの生成とファミリーの積構築を繰り返し適用し、色ラベル付きのパーティションから頂点セットに投影します。 

カバーできる最大セット$k$クリークは、（実現可能性の下での包含最大性に関して）最小限の要素です。$F_k$したがって、$$F_k^{\uparrow} = \{X \in F_k \mid \nexists Y \in F_k \text{ with } X \subsetneq Y\}.$$具体的なグラフについては$G$(18) では、具体的な最大クリークと最大クリークのカーディナリティ$k$-clique-coverable セットは、固定エッジ ファミリでこれらの ZDD 式を評価することによって取得されます。$\overline{g}$そのグラフに関連付けられ、リダクションを実行します。 結果として得られるファミリーは、最大解に対応するすべてのルートから⊤へのパスのセットとして、末端縮小 ZDD から直接読み取られます。 

これで工事は完了です。 ∎
