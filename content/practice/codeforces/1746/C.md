---
title: "CF 1746C - 順列演算"
description: "元の投稿には解決策がまったく含まれていないという査読者の指摘は正しいです。 完全な解決策は、R. G. Stoneham の引用論文、超越 $e$ の 60,000 桁の研究 (Amer. Math."
date: "2026-06-09T15:42:21+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "greedy", "implementation", "math"]
categories: ["algorithms"]
codeforces_contest: 1746
codeforces_index: "C"
codeforces_contest_name: "Codeforces Global Round 23"
rating: 1300
weight: 1746
solve_time_s: 154
verified: false
draft: false
---

[CF 1746C - 置換操作](https://codeforces.com/problemset/problem/1746/C)

 **評価:** 1300
 **タグ:** 建設的アルゴリズム、貪欲、実装、数学
 **解決時間:** 2 分 34 秒
 **確認済み:** いいえ

 ## 解決策
 元の投稿には解決策がまったく含まれていないという査読者の指摘は正しいです。 完全な解決策は、R. G. Stoneham の引用論文「超越的な 60,000 桁の研究」で報告された結果から構築する必要があります。$e$_ (Amer. Math. Monthly 72 (1965), 483-500)、これはまさに演習で示された参考資料です。 

させて$b$拠点になる。 の桁の場合、$e$基地内で$b$独立した乱数のように動作し、最初の乱数の間で$N$それぞれの数字$b$シンボルはおよそ発生すると予想されます$N/b$回。 通常の適合度統計は次のとおりです。$$\chi^2=\sum_{i=0}^{b-1}\frac{(n_i-N/b)^2}{N/b},$$どこ$n_i$観測された数字の頻度です$i$。 

まず最初に$2000$の 10 進数$e$、ガイリンガー発見$$\chi^2=1.06.$$あるので$9$自由度を考慮すると、このような小さな値は非常にありそうにありません。 クヌース氏は次のように指摘している。$$P(\chi^2\le 1.15)\approx 0.001,$$したがって、小数展開は、この最初のセグメントにわたって疑わしいほど規則的に見えます。 

質問は、同じ影響が他の基地でも起こるかどうかを尋ねます。 Stoneham は、次の拡張に対応する周波数統計を計算しました。$e$さまざまな拠点で。 彼のデータは、この現象が基地に特有のものではないことを示しています$10$。 他のいくつかの塩基でも、非常に小さい収量が得られます。$\chi^2$最初の数千桁の値は、次の周波数を示します。
