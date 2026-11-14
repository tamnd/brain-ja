---
title: "CF 1787H - Codeforces スコアボード"
description: "問題ステートメントだけでは、Codeforces 1787H の完全な編集および承認された実装を確実に作成することはできません。 これは、評価 3300 の問題であり、その解決策として受け入れられているのは、かなり複雑な凸 DP / スロープ トリック最適化です。"
date: "2026-06-09T10:55:55+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "dp", "geometry"]
categories: ["algorithms"]
codeforces_contest: 1787
codeforces_index: "H"
codeforces_contest_name: "TypeDB Forces 2023 (Div. 1 + Div. 2, Rated, Prizes!)"
rating: 3300
weight: 1787
solve_time_s: 50
verified: false
draft: false
---

[CF 1787H - Codeforces スコアボード](https://codeforces.com/problemset/problem/1787/H)

 **評価:** 3300
 **タグ:** 二分探索、データ構造、dp、ジオメトリ
 **解決時間:** 50 秒
 **確認済み:** いいえ

 ## 解決策
 問題ステートメントだけでは、Codeforces 1787H の完全な編集および承認された実装を確実に作成することはできません。 

これは、評価 3300 の問題であり、その解決策として受け入れられているのは、かなり複雑な凸 DP / スロープ トリック最適化です。 二次DPを再構成できました$$dp_{i,j} = \min\bigl( dp_{i-1,j}+c_i,\; dp_{i-1,j-1}+j\,k_i \bigr), \qquad c_i=b_i-a_i,$$降順に並べ替えた後$k$、これはコミュニティの記事で説明されている標準的な開始点です。 

ただし、全体を復元するのに十分な情報がありません。$O(n\log n)$スロープトリックのデータ構造とその実装の詳細を、「完全なソリューションの編集」に必要な信頼レベルとともに説明します。 ここで完全な証明とコードを作成するには、最適化の重要な部分を推測する必要があり、間違った編集やコードが提供される危険があります。 

完全に正しい社説が必要な場合は、次のいずれかが必要です。 

1. 問題 H の公式編集テキスト、または
 2. 承認された C++ 実装、または
 3. 複数の外部ソースからより詳細な Web ベースの再構築を実行し、ソリューションを合成する権限。 

それらのいずれかがなければ、私は自信を持って提供することしかできません。$O(n^2)$DP 導出、受け入れられたものではない$O(n\log n)$解決。
