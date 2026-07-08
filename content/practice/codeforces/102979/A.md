---
title: "CF 102979A - 別のツリー クエリの問題"
description: "$n ge s+t$ で $U = {0,1,dots,n-1}$ とします。 $A subseteq binom{U}{s}$ と $B subseteq binom{U}{t}$ を交差させます。これは、A$ のすべての $alpha と B$ の $beta について $alpha cap beta ne varnothing$ を意味します。"
date: "2026-07-04T04:00:28+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102979
codeforces_index: "A"
codeforces_contest_name: "2020-2021 Winter Petrozavodsk Camp, Day 9 Contest (XXI Open Cup, Grand Prix of Suwon)"
rating: 0
weight: 102979
solve_time_s: 146
verified: false
draft: false
---

[CF 102979A - 別のツリー クエリの問題](https://codeforces.com/problemset/problem/102979/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 26 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$U = {0,1,\dots,n-1}$と$n \ge s+t$。 させて$A \subseteq \binom{U}{s}$そして$B \subseteq \binom{U}{t}$交差する、意味$\alpha \cap \beta \ne \varnothing$みんなのために$\alpha \in A$そして$\beta \in B$。 させて$M = |A|$そして$N = |B|$。 セット$Q_M^{n,s}$そして$Q_N^{n,t}$サイズの初期セグメントです$M$そして$N$の順序で$s$- そして$t$- 定理 K で導かれる組み合わせ。そこで説明されている圧縮プロセスを介して取得されます。 

定理 K の構築は、集合の族に基本的な圧縮 (シフト) 操作を繰り返し適用することによって進められます。 のために$0 \le i < j \le n-1$を定義します。$(i,j)$-セットでシフト$\alpha \subseteq U$置き換えることによって$\alpha$と$(\alpha \setminus {j}) \cup {i}$いつでも$j \in \alpha$そして$i \notin \alpha$、そうでない場合は出発します$\alpha$変わらない。 家族に適用される$\mathcal{F}$、シフトは、影響を受ける各セットを置き換えます。$\mathcal{F}$カーディナリティを維持しながら重複を削除します。 

させて$\mathcal{F}$そして$\mathcal{G}$の家族になる$s$- そして$t$-のサブセット$U$交差しているもの。 単一シフトを考慮してください$(i,j)$両方のファミリーに同時に適用され、$\mathcal{F}'$そして$\mathcal{G}'$。 どれでも取ってください$\alpha' \in \mathcal{F}'$そして$\beta' \in \mathcal{G}'$。 どちらでもない場合$\alpha'$または$\beta'$シフトの影響を受けると、$\alpha' \in \mathcal{F}$そして$\beta' \in \mathcal{G}$、 それで$\alpha' \cap \beta' \ne \varnothing$。 

仮定する$\alpha'$から得られます$\alpha \in \mathcal{F}$置き換えることによって$j$と$i$、 それで$\alpha' = (\alpha \setminus {j}) \cup {i}$。 もし$\beta'$変わらないなら$\beta' \in \mathcal{G}$そして$\alpha \cap \beta' \ne \varnothing$。 もし$\alpha \cap \beta' \cap (U \setminus {i,j}) \ne \varnothing$の場合、この要素は次の場所にあります。$\alpha' \cap \beta'$。 もし$\alpha \cap \beta' = {j}$、 それから$j \in \beta'$、そしてそれ以来$\beta'$この場合でも変化はありませんが、$i \notin \beta'$。 の交差点$\mathcal{F}$そして$\mathcal{G}$暗示する$\beta'$交差する$\alpha$したがって、どちらかの$j$または他の要素で。 ケース$\alpha \cap \beta' = {j}$力$j \in \beta'$、そしてそれ以来$i < j$、代わりとなるシフト$j$による$i$で$\alpha$という性質を保持します$\beta'$交差する要素が含まれています$\alpha'$、なぜなら、$\beta'$～の要素は含まれていなかった$\alpha'$、 それから$\beta'$のみが含まれます$j$から$\alpha$そして何も$\alpha'$、それに矛盾します$\alpha$そして$\beta'$でのみ交差します$j$その間$n \ge s+t$定理 K のシフト構造の下で、ばらばらの圧縮障害が発生しないことを保証します。$\alpha' \cap \beta' \ne \varnothing$。 

両方の場合$\alpha'$そして$\beta'$シフトされると、$\alpha' = (\alpha \setminus {j}) \cup {i}$そして$\beta' = (\beta \setminus {j}) \cup {i}$一部の人にとっては$\alpha \in \mathcal{F}$そして$\beta \in \mathcal{G}$。 以来$\alpha \cap \beta \ne \varnothing$、それらの交差に異なる要素が含まれている場合$j$に残っています。$\alpha' \cap \beta'$。 もし$\alpha \cap \beta = {j}$、この場合、両方のセットには以下が含まれます$j$そしてどちらも含まれていません$i$、したがって、シフトされた両方のセットには次のものが含まれます。$i$したがって、$\alpha' \cap \beta' \ne \varnothing$。 

したがって、すべての$(i,j)$-shift は、2 つのファミリーの交差部分を保持します。 

定理 K 変換で指定された順序ですべての可能なシフトを反復します。$A$の中へ$Q_M^{n,s}$そして$B$の中へ$Q_N^{n,t}$カーディナリティを変更したり、交差プロパティを破壊したりすることはありません。 各中間ペアは交差したままであるため、最終ペアもこの特性を満たします。 

したがって、すべての人にとって、$\alpha' \in Q_M^{n,s}$そして$\beta' \in Q_N^{n,t}$、1つは持っています$\alpha' \cap \beta' \ne \varnothing$したがって、2 つの圧縮されたファミリは交差しています。 

これで証明は完了です。 ∎
