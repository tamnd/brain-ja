---
title: "CF 104536E - LIS の最大化"
description: "この障害はツリー ロジック自体から発生しているのではなく、提供されたソリューションのハーネス動作のテストから発生しています。 主な症状は次の行です。そして、出力が計算された値ではなく、テスト ランナー内の空の文字列であるという事実です。"
date: "2026-06-30T09:43:31+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104536
codeforces_index: "E"
codeforces_contest_name: "SashaT9 Contest 1"
rating: 0
weight: 104536
solve_time_s: 198
verified: false
draft: false
---

[CF 104536E - LIS の最大化](https://codeforces.com/problemset/problem/104536/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 18 秒
 **確認済み:** いいえ

 ## 解決策
 ## 診断

 この障害はツリー ロジック自体から発生しているのではなく、提供されたソリューションのハーネス動作のテストから発生しています。 

重要な症状は次の行です。```
AssertionError
```そして、テスト ランナー内の出力は計算された値ではなく空の文字列であるという事実です。 

これは、前の「ソリューション」で次のように定義されているために発生します。```
return ""
```の中で`run()`実際にソルバー ロジックを呼び出すのではなく、ヘルパーを呼び出します。 したがって、テストではアルゴリズムはまったく評価されません。 黙って空の文字列を返しており、明らかに失敗します。`"5"`。 

それとは別に、たとえそれが修正されたとしても、半径の計算には 2 つ目の正確性の問題があります。 

使用された式は次のとおりです。```
radius = min(max(dist_u[i], dist_v[i]))
```これは直径の端点に対して有効ですが、それは次のことを確認した後でのみ有効です。`dist_u`そして`dist_v`直径の真の反対側の端点に対応します。 その部分は大丈夫です。 

ただし、実際の堅牢性の問題は構造的なものです。実装では、ノードのインデックスが 1 であることを前提としています。`len(adj)-1`隣接サイズが正しく再利用されない場合、または入力解析が誤って処理された場合、この問題は壊れます。 

しかし、即座にクラッシュしたのは純粋に通話の不在によるものです。 

## 正しいソリューションのアイデア

 私たちは次のことを行う必要があります。 

1. 両方のツリーを構築します。 
2. BFS をツリーごとに 2 回実行して、直径のエンドポイントを取得します。 
3. 両方の端点からの距離を使用して半径を計算します。 
4. 以下を使用して結合します。```
answer = max(d1, d2, r1 + r2 + 1)
```テスト ハーネス ロジックが実行を妨げてはなりません。 

## 正しい Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

def bfs(start, adj):
    n = len(adj) - 1
    dist = [-1] * (n + 1)
    q = deque([start])
    dist[start] = 0

    far = start

    while q:
        v = q.popleft()
        for to in adj[v]:
            if dist[to] == -1:
                dist[to] = dist[v] + 1
                q.append(to)
                if dist[to] > dist[far]:
                    far = to

    return far, dist

def tree_info(adj):
    u, _ = bfs(1, adj)
    v, dist_u = bfs(u, adj)
    _, dist_v = bfs(v, adj)

    diameter = dist_u[v]

    radius = 10**18
    for i in range(1, len(adj)):
        radius = min(radius, max(dist_u[i], dist_v[i]))

    return diameter, radius

def solve():
    n = int(input())
    adj1 = [[] for _ in range(n + 1)]
    for _ in range(n - 1):
        u, v = map(int, input().split())
        adj1[u].append(v)
        adj1[v].append(u)

    m = int(input())
    adj2 = [[] for _ in range(m + 1)]
    for _ in range(m - 1):
        u, v = map(int, input().split())
        adj2[u].append(v)
        adj2[v].append(u)

    d1, r1 = tree_info(adj1)
    d2, r2 = tree_info(adj2)

    print(max(d1, d2, r1 + r2 + 1))

if __name__ == "__main__":
    solve()
```## 実際何が間違っていたのか

 元の失敗はアルゴリズムによるものではありませんでした。 この回帰は、「単体テスト ラッパー コード」と実際のソリューションを混合することで発生しました。 

テストに使用された関数はソルバーを呼び出す代わりに空の文字列を返したので、すべてのアサーションが正確さに関係なく失敗しました。 

これが削除されると、BFS 直径 + 半径の組み合わせアプローチは問題の制約と完全に一致し、ツリーごとに線形時間で実行されます。 

ご希望であれば、ソルバーがテスト ハーネスから分離されているため、この種のバグが再び発生しないようにする、競技プログラミング用のよりクリーンなパターンもお見せできます。
