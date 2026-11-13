---
title: "CF 1736A - A を B と等しくする"
description: "これはタイプ B (証明) の問題です。 このステートメントは、すべての自然数 $n$ に対する普遍的主張、つまり $S(7n)$ または $S(9n)$ の少なくとも 1 つが $S(n)/5$ を超えることを要求します。"
date: "2026-06-09T18:00:22+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "greedy", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1736
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 825 (Div. 2)"
rating: 800
weight: 1736
solve_time_s: 117
verified: false
draft: false
---

[CF 1736A - A を B と等しくする](https://codeforces.com/problemset/problem/1736/A)

 **評価:** 800
 **タグ:** ブルートフォース、貪欲、ソート
 **解決時間:** 1 分 57 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の種類のチェック

 これは **タイプ B (証明)** の問題です。 このステートメントには、すべての自然数に対する普遍的主張が必要です$n$、つまり、次の少なくとも 1 つが$S(7n)$または$S(9n)$を超える$S(n)/5$。 有効な解決策は、すべての人にとって不等式を厳密に確立する必要があります。$n$小さな例や直感によるものだけではありません。 適切な根拠を伴う段階的な論理的推論が必要です。 

## 段階的な検証

 **ステップ 1: 小さな値を使用した探索$n$** - 有効。 の計算$n=1,2,10,111$不等式の妥当性を正しく示していますが、証明にはなりません。 

**ステップ 2: 補題 1、主張$S(9k) \ge S(k)$みんなのために$k$**
