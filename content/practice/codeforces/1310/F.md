---
title: "CF 1310F - 不正な暗号化"
description: "はい、不等式 $nu(n) le 2^{l(n) - lambda(n)}$ はすべての正の整数 $n$ に当てはまります。 最小長 $l(n)$ の加算チェーンを考え、$lambda(n)$ を 2 倍化ステップのみからなる最短チェーンの長さとします。"
date: "2026-06-11T17:22:12+07:00"
tags: ["codeforces", "competitive-programming", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 1310
codeforces_index: "F"
codeforces_contest_name: "VK Cup 2019-2020 - Elimination Round (Engine)"
rating: 3400
weight: 1310
solve_time_s: 124
verified: false
draft: false
---

[CF 1310F - 不正な暗号化](https://codeforces.com/problemset/problem/1310/F)

 **評価:** 3400
 **タグ:** 数学、数論
 **解決時間:** 2 分 4 秒
 **確認済み:** いいえ

 ## 解決策
 そう、不等号$\nu(n) \le 2^{l(n) - \lambda(n)}$すべての正の整数に当てはまります$n$。 最小の長さの加算チェーンを検討します。$l(n)$そしてさせてください$\lambda(n)$は、倍増ステップのみからなる最短のチェーンの長さになります。 各非倍増ステップでは、現在の指数の 2 進表現内の 1 の数を最大 2 倍にすることができます。 ですので、まずは1本から$1$、 後$l(n) - \lambda(n)$非倍増ステップ、1の数$\nu(n)$満足する$\nu(n) \le 2^{l(n) - \lambda(n)}$。 

これにより、境界が直接確立されます。 したがって、この不等式を次のように当てはめると、$2^n - 1$、そのバイナリ表現は次で構成されます。$n$下限を取得します$l(2^n - 1) \ge n - 1 + \lfloor \lg n \rfloor$、式 (17) および (49) と一致します。 これで議論は終わります。 ∎
