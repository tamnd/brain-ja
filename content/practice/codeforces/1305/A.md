---
title: "CF 1305A - クロニと贈り物"
description: "はい、不等式 $nu(n) le 2^{l(n) - lambda(n)}$ はすべての正の整数 $n$ に当てはまります。 最小長 $l(n)$ の加算チェーンを考え、$lambda(n)$ を 2 倍化ステップのみからなる最短チェーンの長さとします。"
date: "2026-06-11T17:39:01+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "constructive-algorithms", "greedy", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1305
codeforces_index: "A"
codeforces_contest_name: "Ozon Tech Challenge 2020 (Div.1 + Div.2, Rated, T-shirts + prizes!)"
rating: 800
weight: 1305
solve_time_s: 108
verified: false
draft: false
---

[CF 1305A - クロニと贈り物](https://codeforces.com/problemset/problem/1305/A)

 **評価:** 800
 **タグ:** ブルート フォース、構築的アルゴリズム、貪欲、ソート
 **解決時間:** 1 分 48 秒
 **確認済み:** いいえ

 ## 解決策
 そう、不等号$\nu(n) \le 2^{l(n) - \lambda(n)}$すべての正の整数に当てはまります$n$。 最小の長さの加算チェーンを検討します。$l(n)$そしてさせてください$\lambda(n)$は、倍増ステップのみからなる最短のチェーンの長さになります。 各非倍増ステップでは、現在の指数の 2 進表現内の 1 の数を最大 2 倍にすることができます。 ですので、まずは1本から$1$、 後$l(n) - \lambda(n)$非倍増ステップ、1の数$\nu(n)$満足する$\nu(n) \le 2^{l(n) - \lambda(n)}$。 

これにより、境界が直接確立されます。 したがって、この不等式を次のように当てはめると、$2^n - 1$、そのバイナリ表現は次で構成されます。$n$下限を取得します$l(2^n - 1) \ge n - 1 + \lfloor \lg n \rfloor$、式 (17) および (49) と一致します。 これで議論は終わります。 ∎
