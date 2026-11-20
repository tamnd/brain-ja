---
title: "CF 2197C - 分数を使ったゲーム"
description: "値 $V = 7frac{1}{16}$ は、式 1 のように、$k = 11$ カテゴリから計算されたカイ 2 乗統計量に対応します。 (5)。 したがって、自由度の数は $nu = k - 1 = 10$ となります。"
date: "2026-06-09T04:44:55+07:00"
tags: ["codeforces", "competitive-programming", "games", "greedy", "math"]
categories: ["algorithms"]
codeforces_contest: 2197
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 1079 (Div. 2)"
rating: 1200
weight: 2197
solve_time_s: 130
verified: false
draft: false
---

[CF 2197C - 分数を使ったゲーム](https://codeforces.com/problemset/problem/2197/C)

 **評価:** 1200
 **タグ:** ゲーム、貪欲、数学
 **解決時間:** 2 分 10 秒
 **確認済み:** いいえ

 ## 解決策
 値$V = 7\frac{1}{16}$次から計算されるカイ二乗統計量に対応します。$k = 11$式のようにカテゴリ。 (5)。 したがって、自由度の数は次のようになります。$\nu = k - 1 = 10$。 かどうかを判断するには$V$ありそうもないほど高いので、表 1 の次の行を参照してください。$\nu = 10$、10 自由度のカイ 2 乗分布のパーセンテージ ポイントをリストします。 この行は、次の値に対するおおよそのしきい値を示します。$V$比較してその極限を評価することができます。 

以来$7\frac{1}{16} = 7.0625$の 50 パーセントと 75 パーセントのエントリの間にあります。$\nu = 10$、著しく高いとは考えられません。 それにもかかわらず、表で使用する適切な行は次の行です。$\nu = 10$。 

∎
