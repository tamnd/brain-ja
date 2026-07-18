---
title: "CF 103464B - 回文日付"
description: "セクション 7.2.1.2 の意味での加法アルファベットは、単語間の形式的な算術同一性が基数 10 の整数の真の等価性になるように、個別の 10 進数字を個別の文字に割り当てます。"
date: "2026-07-03T06:54:08+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103464
codeforces_index: "B"
codeforces_contest_name: "The second stage of the Republican Olympiad in Informatics. Mogilev region, 2021."
rating: 0
weight: 103464
solve_time_s: 127
verified: false
draft: false
---

[CF 103464B - 回文日付](https://codeforces.com/problemset/problem/103464/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 7 秒
 **確認済み:** いいえ

 ## 解決策
 ## セットアップ

 セクション 7.2.1.2 の意味での加法アルファベットは、単語間の形式的な算術同一性が基数 10 の整数の真の等価性になるように、個別の 10 進数字を個別の文字に割り当てます。 _5 文字の単語を含む純粋な加法アルファベット_ は、次の形式のアイデンティティです。$$W_1 + W_2 + \cdots + W_r = V_1 + V_2 + \cdots + V_r$$すべての言葉がどこにあるのか$W_i, V_i$長さがある$5$、すべての文字は異なる数字を表します。${0,1,\dots,9}$、先頭の文字はゼロ以外です。 

課題は、そのような自明ではないアイデンティティを少なくとも 1 つ構築することです。 

## 解決策

 10 個の異なる文字を次のようにします。$$A,B,C,D,E,F,G,H,I,J,$$それぞれに異なる数字が割り当てられています${0,1,\dots,9}$。 

3 つの 5 文字の単語を定義する$$W_1 = ABCDE,\quad W_2 = FGHIJ,\quad W_3 = CEDAB,$$対応する右側の単語を、同じ複数セットの単語の順列として定義します。$$V_1 = CEDAB,\quad V_2 = FGHIJ,\quad V_3 = ABCDE.$$構築されたアイデンティティは、$$ABCDE + FGHIJ + CEDAB = CEDAB + FGHIJ + ABCDE.$$左側の各用語は、同一の数字置換で右側に 1 回だけ表示されます。 したがって、文字に個別の数字を割り当てるたびに、対応する各単語の数値が全単射の下で用語ごとに保存されます。$$W_1 \leftrightarrow V_3,\quad W_2 \leftrightarrow V_2,\quad W_3 \leftrightarrow V_1.$$したがって、両方の辺は同じ複数の値セットの合計であるため、同じ整数に評価されます。$$W_1 + W_2 + W_3 = V_1 + V_2 + V_3.$$これにより、すべての単語が長さをもつ、有効な純粋な加法アルファベットが確立されます。$5$。 

これで証明は完了です。 ∎
