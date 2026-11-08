---
title: "CF 1530G - なんという逆転"
description: "同じ長さ n と整数 k の 2 つのバイナリ文字列 a と b が与えられます。 私たちの目標は、ちょうど k 個の値を含む a の部分文字列を繰り返し反転することによって、a を b に変換することです。 各反転には任意の数のゼロを含めることができます。"
date: "2026-06-10T16:55:35+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms"]
categories: ["algorithms"]
codeforces_contest: 1530
codeforces_index: "G"
codeforces_contest_name: "Codeforces Round 733 (Div. 1 + Div. 2, based on VK Cup 2021 - Elimination (Engine))"
rating: 3300
weight: 1530
solve_time_s: 49
verified: false
draft: false
---

[CF 1530G - なんという逆転](https://codeforces.com/problemset/problem/1530/G)

 **評価:** 3300
 **タグ:** 建設的アルゴリズム
 **解決時間:** 49 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 2 つのバイナリ文字列が与えられています。`a`そして`b`、同じ長さの`n`そして整数`k`。 私たちの目標は変革することです`a`の中へ`b`の部分文字列を繰り返し反転することで、`a`正確に含まれるもの`k`もの。 各反転には任意の数のゼロを含めることができます。 出力は一連の反転であるか、または`-1`指定された制限内で変換が不可能な場合。 

この問題は建設的ですが、微妙な制約があります。つまり、任意に移動することはできません。 正確に含むブロックのみを反転できます`k`もの。 これは、ものの位置の観点から考える必要があることを意味します
