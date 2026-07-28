---
title: "CF 102786H - NP-\u0421\u043b\u043e\u043d"
description: "私は社説を書くことはできますが、声明のデータは矛盾しているように見えます。入力 2 1 に対して表示されたサンプルは出力 3 を示していますが、記載されたグラフの数学的特性は、n = 2 の場合、可能な最小サイクル コストが 2 であることを示唆しています。"
date: "2026-07-27T19:29:20+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102786
codeforces_index: "H"
codeforces_contest_name: "\u041e\u0442\u043a\u0440\u044b\u0442\u044b\u0439 \u0447\u0435\u043c\u043f\u0438\u043e\u043d\u0430\u0442 \u042f\u0440\u0413\u0423 \u0438\u043c. \u041f.\u0413. \u0414\u0435\u043c\u0438\u0434\u043e\u0432\u0430 Demidov Open IT Cup 2019"
rating: 0
weight: 102786
solve_time_s: 58
verified: false
draft: false
---

[CF 102786H - NP-\u0421\u043b\u043e\u043d](https://codeforces.com/problemset/problem/102786/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 58 秒
 **確認済み:** いいえ

 ## 解決策
 社説を書くことはできますが、声明データに一貫性がないように見えます: 入力用に表示されたサンプル`2 1`出力を示します`3`一方、上記のグラフの数学的特性は、可能な最小サイクル コストが次のとおりであることを示しています。`2`のために`n = 2`。 以下の解決策は、指定された距離の定義に従います。`(i - j) mod n`、答えが得られます`n`そして有効な構造。 元の裁判官がモジュロまたはルートの長さについて異なる解釈をしている場合は、編集を調整する必要があります。
