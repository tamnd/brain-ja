---
title: "CF 104871H - 人事"
description: "私たちには、根を張ったツリーを形成する会社階層が与えられています。 1 人を除くすべての従業員には 1 人のマネージャーがおり、各マネージャーには、最も好まれないものから順にランク付けされた直属の部下の順序付けされたリストがあります。"
date: "2026-06-28T10:38:43+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104871
codeforces_index: "H"
codeforces_contest_name: "2023-2024 ICPC Central Europe Regional Contest (CERC 23)"
rating: 0
weight: 104871
solve_time_s: 39
verified: false
draft: false
---

[CF 104871H - 人事](https://codeforces.com/problemset/problem/104871/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 39 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちには、根を張ったツリーを形成する会社階層が与えられています。 1 人を除くすべての従業員には 1 人のマネージャーがおり、各マネージャーには、最も好まれないものから順にランク付けされた直属の部下の順序付けされたリストがあります。 入力は、このツリーを構造化テキスト形式 (ENCODE モード) またはコンパクトなバイナリ文字列と従業員名のリスト (DECODE モード) で記述します。 

ENCODE モードでは、階層全体を 2 つの部分に圧縮することがタスクとなります。 まず、すべての従業員名を任意の順序で出力する必要があります。 次に、すべてのマネージャーの親子関係と子の順序を含むツリー構造全体をエンコードするバイナリ文字列を生成する必要があります。 

DECODE モードでは、まさにその出力、つまり nam の順序なしリストが得られます。
