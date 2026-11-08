---
title: "CF 1530C - 追跡"
description: "複数のステージからなるコンテストがあり、各ステージで 0 ～ 100 ポイントが与えられます。 あなたとイリヤはすでに n ステージを完了しており、私たちはあなたたち二人のスコアを知っています。"
date: "2026-06-10T16:52:07+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "brute-force", "greedy", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1530
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 733 (Div. 1 + Div. 2, based on VK Cup 2021 - Elimination (Engine))"
rating: 1200
weight: 1530
solve_time_s: 45
verified: false
draft: false
---

[CF 1530C - 追跡](https://codeforces.com/problemset/problem/1530/C)

 **評価:** 1200
 **タグ:** 二分探索、ブルート フォース、貪欲、ソート
 **解決時間:** 45 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 複数のステージからなるコンテストがあり、各ステージで 0 ～ 100 ポイントが与えられます。 あなたとイリヤはすでに完了しています`n`stages, and we know the scores for both of you. このコンテストでは、最高位のみを獲得して出場者をランク付けします。`k - floor(k / 4)`最初から得点する`k`ステージを合計して全体のスコアを取得します。 

この問題は、あなたの現在のスコアとイリヤのスコアを考慮して、イリヤがこれらの追加ステージで何も得点しないと仮定して、理論的にイリヤの合計と少なくとも等しい合計に達するために必要な追加の満点スコア ステージ (100 ポイント) の最小数はいくつですか?

 入力により、次の合計が保証されます。`n`すべてのテスト ケースで最大でも`10^5`。 以来`n`到達できる`10^5`、追加ステージのあらゆる可能性を素朴に試すアルゴリズムは遅すぎます。 `O(n よりも高速なものが必要です
