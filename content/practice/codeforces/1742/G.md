---
title: "CF 1742G - オーレー"
description: "タスクは、接頭辞 OR 配列が辞書編集的にできるだけ大きくなるように、指定された非負整数の配列を再配置することです。"
date: "2026-06-09T16:18:34+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "brute-force", "greedy", "math", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1742
codeforces_index: "G"
codeforces_contest_name: "Codeforces Round 827 (Div. 4)"
rating: 1500
weight: 1742
solve_time_s: 120
verified: false
draft: false
---

[CF 1742G - オーレイ](https://codeforces.com/problemset/problem/1742/G)

 **評価:** 1500
 **タグ:** ビットマスク、ブルート フォース、貪欲、数学、ソート
 **解決時間:** 2 分
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 タスクは、接頭辞 OR 配列が辞書編集的にできるだけ大きくなるように、指定された非負整数の配列を再配置することです。 プレフィックス OR 配列は次のように定義されます。$b_i = a_1 ,\mathsf{OR}, a_2 ,\mathsf{OR}, \dots ,\mathsf{OR}, a_i$、各要素の意味$b_i$配列の先頭から位置までの累積的なビット単位の OR を表します。$i$。 目的は並べ替えることです$a$最大化する$b$辞書編集順。つまり、の最初の要素を意味します。$b$はできるだけ大きくする必要があり、同点の場合は 2 番目の要素もできるだけ大きくする必要があります。以下同様です。 

入力には複数のテスト ケースが含まれており、すべての配列の合計サイズが次を超えません。$2 \cdot 10^5$。 この制約は、アルゴリズムが$O(n^2)$複雑なため効率的に実行されませんが、$O(n \log n)$または$O(n \cdot 30)$各数値は最大で次のとおりであるため、操作は許容されます。$10^9$、せいぜい意味
