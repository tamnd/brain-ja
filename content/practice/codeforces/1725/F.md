---
title: "CF 1725F - フィールド写真"
description: "各行には、最初は非常に大きな整数行の列に配置された出場者の連続ブロックが含まれています。 行 $i$ は $Li$ から $Ri$ までのすべての位置を占めるため、幾何学的には各行は単なる閉じた間隔になります。"
date: "2026-06-16T16:52:35+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "data-structures", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1725
codeforces_index: "F"
codeforces_contest_name: "COMPFEST 14 - Preliminary Online Mirror (Unrated, ICPC Rules, Teams Preferred)"
rating: 2100
weight: 1725
solve_time_s: 175
verified: false
draft: false
---

[CF 1725F - フィールド写真](https://codeforces.com/problemset/problem/1725/F)

 **評価:** 2100
 **タグ:** ビットマスク、データ構造、並べ替え
 **解決時間:** 2 分 55 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 各行には、最初は非常に大きな整数行の列に配置された出場者の連続ブロックが含まれています。 行$i$～からのあらゆるポジションを占める$L_i$に$R_i$したがって、幾何学的には、各行は単なる閉区間です。 

行全体を任意の正の整数だけ左または右にシフトできます。$k$。 そのような動きのたびに、$k$ビットごとの OR を使用してグローバル アキュムレータに変換します。 クエリ値の場合$W$、シーケンスを選択する必要があります
