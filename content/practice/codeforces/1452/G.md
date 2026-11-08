---
title: "CF 1452G - ゲームオンツリー"
description: "以前の実装で何が問題だったかがわかります。 この問題は微妙です。「すべてが等しい」ケース [1, 1, 1] は、現在の合計がすでに maxa 条件を満たすのに十分である場合でも、数式が誤って合計を次の n-1 の倍数に丸めようとするため、失敗します。"
date: "2026-06-11T03:28:24+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "dfs-and-similar", "greedy", "trees"]
categories: ["algorithms"]
codeforces_contest: 1452
codeforces_index: "G"
codeforces_contest_name: "Educational Codeforces Round 98 (Rated for Div. 2)"
rating: 2700
weight: 1452
solve_time_s: 651
verified: false
draft: false
---

[CF 1452G - ゲーム オン ツリー](https://codeforces.com/problemset/problem/1452/G)

 **評価:** 2700
 **タグ:** データ構造、DFS および類似の、貪欲、ツリー
 **解決時間:** 10 分 51 秒
 **確認済み:** いいえ

 ## 解決策
 以前の実装で何が問題だったかがわかります。 問題は微妙です:「すべてが等しい」場合`[1, 1, 1]`数式が誤って合計を次の倍数に丸めようとするため、失敗します。`n-1`現在の合計がすでに条件を満たすのに十分である場合でも、`max_a`状態。 前のコードは、割り算を個別にチェックして切り上げようとしましたが、**必要な合計が少なくとも次の値である必要があることを考慮していませんでした。`max_a * (n-1)`**、** で割り切れる必要があります`n-1`**。 **四捨五入する前に**、これら 2 つの要件の最大値を計算し、最も近い倍数に切り上げる必要があります。`n-1`。 

修正されたシンプルなアプローチは次のとおりです。 

1. しましょう`sum_a`すべてのブロックの合計になります。 
2. しましょう`max_a`1 つのボックス内のブロックの最大数になります。 
3. コンピューティング`target = max(sum_a, max_a * (n-1))`。 
4. 次の倍数を計算します。`n-1`それは≧`target`:`target = ((target + n - 2) // (n-1)) * (n-1)`。 
5. 答えは次のとおりです。`target - sum_a`。 

これにより、「すべてが等しい」ケースと極端な不均衡が一貫して処理されるようになりました。 

修正された Python ソリューションは次のとおりです。```python
import sys
input = sys.stdin.readline

t = int(input())
for _ in range(t):
    n = int(input())
    a = list(map(int, input().split()))
    sum_a = sum(a)
    max_a = max(a)
    # minimal total sum that satisfies both constraints
    target = max(sum_a, max_a * (n - 1))
    # round up to nearest multiple of n-1
    if target % (n - 1) != 0:
        target = ((target + n - 2) // (n - 1)) * (n - 1)
    print(target - sum_a)
```### エッジケースの検証

 -`[3, 2, 2]`→ sum 7、max 3、n-1 = 2 → target = max(7, 3*2)=7 → 2 の次の倍数 ≥7 → 8 → extra = 8-7=1
 -`[2,2,3,2]`→ sum 9、max 3、n-1=3 → target=max(9,3*3)=9 → 9 3 で割り切れる → extra = 0
 -`[0,3,0]`→ sum 3、max 3、n-1=2 → target=max(3,3*2)=6 → 2 で割り切れる → extra = 6-3=3
 -`[1,1,1]`→ sum 3、max 1、n-1=2 → target=max(3,1*2)=3 → 2 の次の倍数 ≥3 → 4 → extra=4-3=1

 このソリューションはテスト ケースごとに O(n) で実行され、すべてのエッジ ケースを正しく処理します。
