---
title: "CF 104090F - ダ・ミ・ラオ・シー・アイ・カン・デ"
description: "ZDD では、各レベルが変数に対応し、$k$ というラベルの付いたノードは $xk$ に関する決定を表します。ここで、下位エッジは変数を除外し、上位エッジは表現された集合ファミリーに変数を含みます。"
date: "2026-07-02T02:32:33+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104090
codeforces_index: "F"
codeforces_contest_name: "The 2022 ICPC Asia Hangzhou Regional Programming Contest"
rating: 0
weight: 104090
solve_time_s: 133
verified: false
draft: false
---

[CF 104090F - Da Mi Lao Shi Ai Kan De](https://codeforces.com/problemset/problem/104090/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 13 秒
 **確認済み:** いいえ

 ## 解決策
 ZDD では、各レベルは変数とラベル付けされたノードに対応します。$k$～に関する決定を表します$x_k$ここで、下位エッジは変数を除外し、上位エッジは表現された集合ファミリーにその変数を含めます。 シンク$\perp$は空の家族を表しますが、$\top$は空のセットのみを含むファミリを表します。 

示されている ZDD は、ラベルが付けられた単一ノードです。$x_3$そのローエッジは$\perp$ハイエッジは次のとおりです$\top$。 これは、次のようなときを意味します。$x_3=0$、サブセットは受け入れられません。$x_3=1$、唯一受け入れられるサブセットは、選択後の空の継続です。$x_3$。 

したがって、表現されたファミリは正確に 1 つのセットで構成されます。${3}$。 他のすべての変数$x_1,x_2,x_4,x_5,x_6$どのノードにも表示されないため、強制的に表示する必要があります。$0$あらゆる満足のいく仕事に。 

したがって、ブール関数は、次のような単一の割り当ての指標となります。$x_3=1$他のすべての変数は$0$:$$f(x_1,x_2,x_3,x_4,x_5,x_6) = x_3 \cdot \overline{x_1}\,\overline{x_2}\,\overline{x_4}\,\overline{x_5}\,\overline{x_6}.$$同様に、それはシングルトン集合の特徴的な関数です${{3}}$サブセット表現で。
