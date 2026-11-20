---
title: "CF 2216B - THU パッキングパズル"
description: "値 $V = 7frac{1}{16}$ は、式 1 のように、$k = 11$ カテゴリから計算されたカイ 2 乗統計量に対応します。 (5)。 したがって、自由度の数は $nu = k - 1 = 10$ となります。"
date: "2026-06-09T04:52:32+07:00"
tags: ["codeforces", "competitive-programming", "greedy"]
categories: ["algorithms"]
codeforces_contest: 2216
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 1092 (Unrated, Div. 2, Based on THUPC 2026 \u2014 Finals)"
rating: 1300
weight: 2216
solve_time_s: 40
verified: false
draft: false
---

[CF 2216B - THU パッキング パズル](https://codeforces.com/problemset/problem/2216/B)

 **評価:** 1300
 **タグ:** 貪欲
 **解決時間:** 40 秒
 **確認済み:** いいえ

 ## 解決策
 値$V = 7\frac{1}{16}$次から計算されるカイ二乗統計量に対応します。$k = 11$式のようにカテゴリ。 (5)。 したがって、自由度の数は次のようになります。$\nu = k - 1 = 10$。 かどうかを判断するには$V$ありそうもないほど高いので、表 1 の次の行を参照してください。$\nu = 10$、10 自由度のカイ 2 乗分布のパーセンテージ ポイントをリストします。 この行は、次の値に対するおおよそのしきい値を示します。$V$比較してその極限を評価することができます。 

以来$7\frac{1}{16} = 7.0625$の 50 パーセントと 75 パーセントのエントリの間にあります。$\nu = 10$、著しく高いとは考えられません。 それにもかかわらず、表で使用する適切な行は次の行です。$\nu = 10$。 

∎
