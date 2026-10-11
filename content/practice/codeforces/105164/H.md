---
title: "CF 105164H - 最高スコア APPQ"
description: "私たちは、非常に構造化された方法で生成された数値の世界を与えられています。 各数値は、最初の $n$ 素数の指数のベクトルに対応します。"
date: "2026-06-27T10:45:46+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105164
codeforces_index: "H"
codeforces_contest_name: "2024 ICPC Gran Premio de Mexico 1ra Fecha"
rating: 0
weight: 105164
solve_time_s: 42
verified: false
draft: false
---

[CF 105164H - 最高スコア APPQ](https://codeforces.com/problemset/problem/105164/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 42 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちは、非常に構造化された方法で生成された数値の世界を与えられています。 各数値は、最初の数値の指数のベクトルに対応します。$n$素数。 の$i$-番目の座標$e_i$の回数を教えてください$i$-番目の素数は因数分解で出現し、各座標は次のように制限されます。$0 \le e_i \le a_i$。 したがって、入力全体は指数ベクトルの有限グリッドを表し、すべての有効な数値はこのグリッド内の 1 つの点になります。 

ペアの構造上の制限の下で、これらの数値のサブセットを選択する必要があります。 2つの数字を取ると$x < y$、それならそれは禁止されています
