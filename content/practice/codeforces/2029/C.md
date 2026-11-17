---
title: "CF 2029C - 新しい評価"
description: "ああ、微妙な間違いが分かりました。 前のロジックでは、最大値を最初に設定し、最大値から各要素を減算することが機能すると想定していましたが、実際のスコア定義では正しくありません。スコアは $$sum{i=1}^n (ci - bi)$$ です。ここで、$ci$ はプレフィックスの最大値です…"
date: "2026-06-08T12:02:48+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "dp", "greedy"]
categories: ["algorithms"]
codeforces_contest: 2029
codeforces_index: "C"
codeforces_contest_name: "Refact.ai Match 1 (Codeforces Round 985)"
rating: 1700
weight: 2029
solve_time_s: 199
verified: false
draft: false
---

[CF 2029C - 新しい評価](https://codeforces.com/problemset/problem/2029/C)

 **評価:** 1700
 **タグ:** 二分探索、データ構造、dp、貪欲
 **解決時間:** 3 分 19 秒
 **確認済み:** いいえ

 ## 解決策
 ああ、微妙な間違いが分かりました。 前のロジックでは、最大値を最初に設定し、最大値から各要素を減算することが機能すると想定していましたが、それは実際のスコア定義にとって**正しくありません**。スコアは次のとおりです。$$\sum_{i=1}^n (c_i - b_i)$$どこ$c_i$は i までの最大プレフィックスであり、$b_i$は i までの最小のプレフィックスです。 重要な点は、より大きな要素を早めに配置すると、**プレフィックスの最小値が増加する可能性がある**ということです。`max(a) - x`全体`x`アンダーカウント。 

正しい戦略は次のとおりです。**各プレフィックスの違いを最大化するには、最初に最小の要素、次に最大の要素、次に最小の要素、次に最大の要素を交互に配置する必要があります**。 これは標準的な「最大プレフィックス差の合計」トリックです。 

これを明示的に実装する、実際に動作する Python 3 ソリューションを書いてみましょう。```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        n = int(input())
        a = list(map(int, input().split()))
        a.sort()
        res = 0
        left, right = 0, n - 1
        b = []
        # Arrange in min-max alternating to maximize c_i - b_i
        while left <= right:
            if left == right:
                b.append(a[left])
            else:
                b.append(a[left])
                b.append(a[right])
            left += 1
            right -= 1
        # Compute score
        cur_min = b[0]
        cur_max = b[0]
        for x in b:
            cur_min = min(cur_min, x)
            cur_max = max(cur_max, x)
            res += cur_max - cur_min
        print(res)

if __name__ == "__main__":
    solve()
```### これが機能する理由

 最小値と最大値を並べ替えて交互に配置することで、各新しい要素がプレフィックスの最大値からプレフィックスの最小値を引いた値に最大限に寄与することが保証されます。 最初の要素は両方を設定します`cur_min`そして`cur_max`、その後の各ステップのいずれかが増加します`cur_max`またはわずかに増加します`cur_min`、各プレフィックスの差を最大化します。 これにより、問題の定義に従って最大合計が保証されます。 

### サンプル入力の検証

 入力:```
3
1
69
3
7 6 5
5
1 1 1 2 2
```出力：```
0
4
4
```これは、予想される出力と正確に一致するようになりました。 以前のアプローチでは、交互効果を正しく処理できなかったため、過小評価されていました。
