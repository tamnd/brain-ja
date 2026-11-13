---
title: "CF 1770B - コキアと順列"
description: "これは、タイプ C ではなく、タイプ B (不等式の証明) 問題です。このタスクは、$abc=1$ を満たすすべての正の $a,b,c$ に対して、$$frac1{a^3(b+c)}+frac1{b^3(c+a)}+frac1{c^3(a+b)}ge frac32$$ であることを証明することです。"
date: "2026-06-09T12:27:59+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms"]
categories: ["algorithms"]
codeforces_contest: 1770
codeforces_index: "B"
codeforces_contest_name: "Good Bye 2022: 2023 is NEAR"
rating: 1000
weight: 1770
solve_time_s: 149
verified: false
draft: false
---

[CF 1770B - コクシアと順列](https://codeforces.com/problemset/problem/1770/B)

 **評価:** 1000
 **タグ:** 建設的アルゴリズム
 **解決時間:** 2 分 29 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の種類のチェック

 これは **タイプ B (不等式の証明)** の問題であり、タイプ C ではありません。 

課題はそれを証明することです$$\frac1{a^3(b+c)}+\frac1{b^3(c+a)}+\frac1{c^3(a+b)}\ge \frac32$$すべてのポジティブのために$a,b,c$満足のいく$abc=1$。 

有効な解決策は、許容されるすべてのトリプルに対して不等式を確立し、引数から生じた等価のケースを正しく識別する必要があります。 

## 段階的な検証

 ### ステップ 1: 次を使用して式を書き換えます。$abc=1$解決策の主張$$\frac1{a^3(b+c)}=\frac{b^3c^3}{b+c},$$循環項についても同様です。 

**評価:** 有効です。 

以来$abc=1$、 我々は持っています$a=\frac1{bc}$したがって、$a^{-3}=b^3c^3$。 

したがって$$\sum \frac1{a^3(b+c)} = \sum \frac{b^3c^3}{b+c}.$$### ステップ 2: Engel-Cauchy を適用する

 解決策は適用されます

 \sum \frac{(b^{3/2}c^{3/2})^2}{b+c} \ge \frac{\left(\sum b^{3/2}c^{3/2}\right)^2} {(a+b)+(b+c)+(
