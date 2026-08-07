---
title: "CF 103993G - スコアリング"
description: "$G = (V,E)$ が (18) の連続 USA グラフを表すものとし、$U を V$ のサブセットとします。 誘導された部分グラフ $G Mid U$ は、奇数長のサイクルを含まない場合にのみ、つまり $G Mid U$ のすべての連結成分が 2 色付けを許容する場合に限り、二部になります。"
date: "2026-07-02T06:03:31+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103993
codeforces_index: "G"
codeforces_contest_name: "ICPC 2022-2023 NERC (NEERC), Southern and Volga Russia Qualifier"
rating: 0
weight: 103993
solve_time_s: 123
verified: false
draft: false
---

[CF 103993G - スコアリング](https://codeforces.com/problemset/problem/103993/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 3 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$G = (V,E)$は、(18) の隣接する米国のグラフを示し、$U \subseteq V$。 誘導された部分グラフ$G \mid U$奇数の長さのサイクルが含まれていない場合に限り、二部構成になります。同様に、$G \mid U$2色塗りを認めます。 

セット$U$は、次の場合に限り、最大誘導二部部分グラフです。$G \mid U$二者構成であり、すべての人にとって$v \in V \setminus U$、誘導された部分グラフ$G \mid (U \cup {v})$奇数サイクルが含まれています。 同様に、除外されたすべての頂点は、二部性を維持するために不可欠です。 

家族を紹介する$$\mathcal{B} = \{U \subseteq V \mid G \mid U \text{ is bipartite}\}.$$求める対象は最大限の要素である$\mathcal{B}^\uparrow$演習 236 の ZDD 族代数の意味で。 

二部性制約は、すべての奇数サイクルの除外として表現できます。$C \subseteq V$:$$U \in \mathcal{B} \quad \Longleftrightarrow \quad \forall C \in \mathcal{C}_{\mathrm{odd}},\; C \nsubseteq U,$$どこ$\mathcal{C}_{\mathrm{odd}}$のすべての奇数サイクルの頂点セットのファミリーです。$G$。 

したがって、$$\mathcal{B} = \mathcal{C}_{\mathrm{odd}}^{\nearrow},$$通訳する$\mathcal{C}_{\mathrm{odd}}^{\nearrow}$ZDD 演算の意味で、奇数サイクルのスーパーセットを回避するすべてのセットのファミリーとして$f \nearrow g$練習問題 236 より。最大誘導二部部分グラフは、この族の最大要素になります。$$\mathcal{M} = \mathcal{B}^\uparrow.$$したがって、計算は次の ZDD 評価に帰着します。$$\mathcal{M} = (\mathcal{C}_{\mathrm{odd}}^{\nearrow})^\uparrow.$$この構造はファミリーを一意に決定し、ZDD 実装は演習 237 の再帰的削減ルールを適用し、頂点の固定変数順序に沿って包含制約を伝播します。$G$。 各奇数サイクルは、そのすべての頂点を同時に含めることを禁止する制約を与え、最大化により、そのような制約をすべて維持しながら拡張できるセットが削除されます。 

したがって、許容されるセットの数は次のようになります。$$|\mathcal{M}| = \text{number of maximal elements of } \mathcal{B}.$$明示的な数値は、両方の奇数サイクルのセットであるため、グラフ (18) の完全な隣接構造に依存します。$\mathcal{C}_{\mathrm{odd}}$そしてその結果生じる ZDD の減少は、頂点間の正確な出現関係に依存します。 提供された抜粋ではそのグラフが特定されていないため、入手可能な情報だけからクローズ数値カウントを導き出すことはできません。 

極端なケースの構造的特徴付けは、欠損データには依存しません。 

最小の最大誘導二部部分グラフは、任意の包含最小集合です$U \in \mathcal{B}^\uparrow$。 このようなセットには、頂点をすべて削除するという特性があります。$U$拡張が可能になり、頂点を追加すると、誘導されたサブグラフに奇数のサイクルが導入されます。 その正確なカーディナリティは、ローカルな奇数サイクル構造に依存します。$G$。 

最大の最大誘導二部部分グラフは、$U \in \mathcal{B}^\uparrow$最大カーディナリティの。 このような各セットは、最小の奇数サイクル トランスバーサルの削除に対応します。$V \setminus U$、しかし、そのような横断のサイズは、の詳細なサイクル構造に依存します。$G$。 

同じフレームワークは、置き換えることによって最大誘導三部部分グラフに拡張されます。$\mathcal{B}$誘導されたサブグラフに 3 色性を妨げるサイクルがない頂点セットのファミリーを使用すると、同等に 4 色を必要とするサブグラフがなく、これもまた、禁止された構成とその最大要素に対する ZDD 制約システムに帰着します。 

これで、演習 236 の族代数に基づく ZDD 評価への問題の還元が完了しました。
