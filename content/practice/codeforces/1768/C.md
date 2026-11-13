---
title: "CF 1768C - エレメンタルデコンプレス"
description: "これはタイプ C ではなく、タイプ B (不等式の証明) 問題です。このタスクは、$abc=1$ を満たすすべての正の $a,b,c$ に対して $$frac1{a^3(b+c)}+frac1{b^3(c+a)}+frac1{c^3(a+b)}ge frac32$$ であることを証明することです。"
date: "2026-06-09T12:42:15+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "greedy", "implementation", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1768
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 842 (Div. 2)"
rating: 1300
weight: 1768
solve_time_s: 95
verified: false
draft: false
---

[CF 1768C - エレメンタル解凍](https://codeforces.com/problemset/problem/1768/C)

 **評価:** 1300
 **タグ:** 建設的アルゴリズム、貪欲、実装、ソート
 **解決時間:** 1 分 35 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の種類のチェック

 これは **タイプ B (不等式の証明)** の問題であり、タイプ C ではありません。 

課題はそれを証明することです$$\frac1{a^3(b+c)}+\frac1{b^3(c+a)}+\frac1{c^3(a+b)}\ge \frac32$$すべてのポジティブのために$a,b,c$満足のいく$abc=1$。 

有効な解決策は、許容されるすべてのトリプルに対して不等式を確立し、引数から生じた場合には等価の場合を正確に識別する必要があります。 

## 段階的な検証

 ### ステップ 1: 次を使用して式を書き換えます。$abc=1$解決策の主張$$\frac1{a^3(b+c)}=\frac{b^3c^3}{b+c},$$循環項についても同様です。 

**評価:** 有効です。 

以来$abc=1$、 我々は持っています$a=\frac1{bc}$したがって、$a^{-3}=b^3c^3$。 

したがって$$\sum \frac1{a^3(b+c)} = \sum \frac{b^3c^3}{b+c}.$$### ステップ 2: Engel-Cauchy を適用する

 解決策が適用される

 \sum \frac{(b^{3/2}c^{3/2})^2}{b+c} \ge \frac{\left(\sum b^{3/2}c^{3/2}\right)^2} {(a+b)+(b+c)+(
