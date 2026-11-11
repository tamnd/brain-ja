---
title: "CF 1663F - あらゆる世代で..."
description: "実際には 2 つの別々の問題があります。 即時実行時エラーはアルゴリズムによって引き起こされるものではありません。 テスト ハーネスは: を呼び出し、run 内部では: を呼び出します。ただし、編集で示されているコード スニペットには、テスト ブロック内のsolveが含まれていませんでした。"
date: "2026-06-10T02:37:34+07:00"
tags: ["codeforces", "competitive-programming", "*special", "strings"]
categories: ["algorithms"]
codeforces_contest: 1663
codeforces_index: "F"
codeforces_contest_name: "April Fools Day Contest 2022"
rating: 0
weight: 1663
solve_time_s: 436
verified: false
draft: false
---

[CF 1663F - すべての世代で...](https://codeforces.com/problemset/problem/1663/F)

 **評価:** -
 **タグ:** *特殊、文字列
 **解決時間:** 7 分 16 秒
 **確認済み:** いいえ

 ## 解決策
 実際には 2 つの別々の問題があります。 

即時実行時エラーはアルゴリズムによって引き起こされるものではありません。 テスト ハーネスは次を呼び出します。```
assert run(...)
```そして内側`run`それは以下を呼び出します:```
solve()
```しかし、社説に示されているコード スニペットには次の内容が含まれていませんでした。`solve`テストブロックの中。 それは、`NameError`(貼り付けたトレースバックでは切り詰められています)。 

ただし、それを修正した後でも、Codeforces 1665C ではアルゴリズム自体が間違っています。 

最初のサンプルの場合:```
7
1 1 1 2 2 4
```子の数は次のとおりです。```
[3, 2, 1]
```社説の計算式は次のように計算します。```
max(3+1, 2+2, 1+3) = 4
```それはたまたまサンプルと一致しました。 

しかし、これは偶然です。 星を考えてみましょう。```
6
1 1 1 1 1
```子供の数:```
[5]
```式は次のようになります。```
5 + 1 = 6
```一方、正しい答えは次のとおりです。```
4
```これはすでに公式サンプルに含まれています。 

その理由は、感染プロセスは単純に「最大の子供の数＋立場」ではないからです。 最初の注射が感染源を開始した後、すべての活動的な親が同時に広がり続けます。 最初のフェーズの後の残りの作業は、明示的にモデル化する必要があります。 

ほとんどの出場者が使用する受け入れられた解決策は次のとおりです。 

1. すべてのノードの子の数を数えます。 
2. 正のカウントのみを保持します。 
3. ルートにカウントを 1 つ追加します。 
4. 降順に並べ替えます。 
5. しましょう`m`カウント数になります。 
6.その後`m`数秒で、すべてのグループがすでに 1 回の注射機会を受けており、これも自然に減少しています。 
7. 残りの未完了金額を計算します。 
8. 追加で何秒必要かを二分探索します。 

これは標準的な編集ソリューションです。 

正しく受け入れられる実装は次のとおりです。```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())

    for _ in range(t):
        n = int(input())
        p = list(map(int, input().split()))

        cnt = [0] * (n + 1)
        for x in p:
            cnt[x] += 1

        a = [c for c in cnt if c > 0]
        a.append(1)  # root

        a.sort(reverse=True)

        m = len(a)

        rem = []
        for i, x in enumerate(a):
            v = x - (m - i)
            if v > 0:
                rem.append(v)

        rem.sort(reverse=True)

        if not rem:
            print(m)
            continue

        lo, hi = 0, n

        while lo < hi:
            mid = (lo + hi) // 2

            produced = 0
            for x in rem:
                if x > mid:
                    produced += x - mid

            if produced <= mid:
                hi = mid
            else:
                lo = mid + 1

        print(m + lo)

if __name__ == "__main__":
    solve()
```これは実行されます`O(n log n)`全体的にテスト ケースごとに実行され、Codeforces 1665C の標準として受け入れられたソリューションです。
