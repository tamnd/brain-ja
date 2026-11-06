---
title: "CF 1311A - 奇数の加算または偶数の減算"
description: "はい、不等式 $nu(n) le 2^{l(n) - lambda(n)}$ はすべての正の整数 $n$ に当てはまります。 最小長 $l(n)$ の加算チェーンを考え、$lambda(n)$ を 2 倍化ステップのみからなる最短チェーンの長さとします。"
date: "2026-06-11T17:16:21+07:00"
tags: ["codeforces", "competitive-programming", "greedy", "implementation", "math"]
categories: ["algorithms"]
codeforces_contest: 1311
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 624 (Div. 3)"
rating: 800
weight: 1311
solve_time_s: 98
verified: false
draft: false
---

[CF 1311A - 奇数の加算または偶数の減算](https://codeforces.com/problemset/problem/1311/A)

 **評価:** 800
 **タグ:** 貪欲、実装、数学
 **解決時間:** 1 分 38 秒
 **確認済み:** いいえ

 ## 解決策
 そう、不等号$\nu(n) \le 2^{l(n) - \lambda(n)}$すべての正の整数に当てはまります$n$。 最小の長さの加算チェーンを検討します。$l(n)$そしてさせてください$\lambda(n)$は、倍増ステップのみからなる最短のチェーンの長さになります。 各非倍増ステップでは、現在の指数の 2 進表現内の 1 の数を最大 2 倍にすることができます。 ですので、まずは1本から$1$、 後$l(n) - \lambda(n)$非倍増ステップ、1の数$\nu(n)$満足する$\nu(n) \le 2^{l(n) - \lambda(n)}$。 

これにより、境界が直接確立されます。 したがって、この不等式を次のように当てはめると、$2^n - 1$、そのバイナリ表現は次で構成されます。$n$下限を取得します$l(2^n - 1) \ge n - 1 + \lfloor \lg n \rfloor$、式 (17) および (49) と一致します。 これで議論は終わります。 ∎
