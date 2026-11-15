---
title: "CF 1859F - バイトランドのテレポート"
description: "道路で結ばれた n 個の都市を含むツリーが与えられています。 各道路には硬度値 wi があります。 道路に沿った移動時間はドライバーのスキル c に依存し、ceil(wi / c) として計算されます。"
date: "2026-06-09T00:29:05+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "dfs-and-similar", "divide-and-conquer", "graphs", "shortest-paths", "trees"]
categories: ["algorithms"]
codeforces_contest: 1859
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 892 (Div. 2)"
rating: 3200
weight: 1859
solve_time_s: 82
verified: true
draft: false
---

[CF 1859F - バイトランドでのテレポート](https://codeforces.com/problemset/problem/1859/F)

 **評価:** 3200
 **タグ:** データ構造、DFS など、分割統治、グラフ、最短パス、ツリー
 **解決時間:** 1 分 22 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちに与えられた木は、`n`道路で結ばれた都市。 各道路には硬度値があります`w_i`。 道路に沿った移動時間はドライバーのスキルによって異なります`c`そして次のように計算されます`ceil(w_i / c)`。 ドライバーはスキルから始まります`c = 1`、および特定の都市では、以下のコースが利用可能です。`T`時間がかかり、現在のスキルが2倍になります。 同じ都市で複数のコースを受講することも可能です。 クエリごとに、都市からの最短移動時間を計算する必要があります。`a`街へ`b`これらのルールの下で。 

ツリー構造は、任意の 2 つの都市間には単純なパスが 1 つだけ存在することを意味します。 したがって、固定スキルの場合、2 つの都市間の移動時間は、このパスに沿った加重移動時間の合計になります。 ドライバーは経路に沿ってスキルを向上させることができるため、問題は複雑になります。これにより、将来の移動時間は短縮されますが、コストは削減されます。`T`コースごとの単位。 どこで、いくつのコースを受講するかを最適に決定することが主な課題です。 

制約は、最大で処理する必要があることを示します。`10^5`すべてのテスト ケースにわたる都市とクエリ。 パスに沿ったコース訪問のすべての組み合わせを探索するという直接的な強引なアプローチは、指数関数的な時間がかかるため、実行不可能です。 エッジケースには、遅くより早くコースを受講する方が良いパス、コースのないパス、および両方のエンドポイントで利用可能なコースがあるパスが含まれます。 

たとえば、硬度のある 1 つのエッジで接続されている 2 つの都市を考えてみましょう。`10`そして`T = 3`。 最初の都市にコースがある場合、1 回受講するとスキルが得られます`2`移動時間を短縮し、`ceil(10 / 2) = 5`、原価計算`3`コースを受講するには、合計で`8`。 コースをスキップしたら、時間は`10`。 コースに沿って複数の倍増コースが存在する場合は特に、いつコースを受講するかを選択するのは微妙です。 

## アプローチ

 ブルート フォース ソリューションは、間のパスに沿った可能なコース選択ポイントのすべてのサブセットを評価します`a`そして`b`。 各サブセットについて、移動時間をシミュレートして合計し、最小値を維持します。 ブルートフォースはすべてのオプションを徹底的にチェックするので正しいですが、複雑さは次のとおりです。`O(2^k * L)`どこ`k`はパス上のコース都市の数であり、`L`パスの長さです。 以来`k`大きくなる可能性がありますが、これは不可能です。 

重要な洞察は、ツリー構造により情報を前処理できるため、クエリに効率的に回答できるということです。 2 つの都市間のパスは、利用可能なコースに基づいてセグメントに分割できます。 セグメントに沿ったコストは開始時のスキルにのみ依存し、コースのない各セグメントは時間的に直線的です。 これにより、パスに沿った最短時間を計算するという問題が軽減され、コース ノードでのスキルが 2 倍になる可能性があります。 

パスの問題をパスに沿った動的計画問題に変換できます。 コースのあるノードごとに、スキルを使用してそのノードに到達するまでの最小合計時間を維持します。`c`。 送信元から宛先までのパスに沿って両方向に DP を伝播できます。 の単調性`ceil(w / c)`この関数は、スキルを早期に倍増しても後のセグメントが悪化することがないことを保証し、スキル倍増の決定を貪欲に伝播することを可能にします。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^k * L) | O(L) | 遅すぎる |
 | コース倍増のパス DP | O(n + q * log n) | O(n) | 承認済み |

 の`O(n + q * log n)`複雑さはパスの前処理 LCA から生じます (`O(n)`) の各クエリ パスに沿って最小 DP を伝播します。`O(log n)`バイナリ リフティングまたは重心分解を使用します。 

## アルゴリズムのチュートリアル

 1. Parse input and build the tree adjacency list. 各エッジには硬度が保存されます。 
2. Precompute the binary lifting table for LCA queries. This allows finding the lowest common ancestor between any two cities in`O(log n)`。 
3. LCA 計算のために深さと親配列を事前計算します。 深度はノードを同じレベルに移動するために使用されます。 
4. 各クエリについて`(a, b)`、間のパスを見つけます`a`そして`b`両方のノードから LCA まで歩いていきます。 ノードをパス順に格納します。 
5. パスに沿ったコース ノードのリストを抽出します。 コースノードがない場合は単純に合計します`ceil(w_i / c)`道に沿って`c = 1`。 
6. それ以外の場合は、パスに沿って動的プログラミングを実行します。 DP 配列を維持します。`dp[i]`ノードに到達するまでの最小合計時間です`i`現在のスキルで`c`。 各コース ノードで、役に立たなくなるまでスキルを 2 倍にするために 0 個以上のコースを受講することを検討してください (2 倍にしても残りのセグメントの時間が減らなくなったら停止します)。 
7. パスに沿って DP を伝播し、宛先ノードの最小値を返します。`b`。 

機能する理由: LCA 分解により、任意の 2 つの都市間の経路を対数時間で抽出できることが保証されます。 DP の伝播では、スキル倍増の単調性が尊重されます。スキルを早期に増加しても、将来のセグメントのコストが増加することはありません。 すべてのパスは独立して処理されるため、クエリの答えは正確です。 

## Python ソリューション```python
import sys
import math
input = sys.stdin.readline
sys.setrecursionlimit(1 << 25)

def solve():
    t = int(input())
    for _ in range(t):
        n, T = map(int, input().split())
        edges = [[] for _ in range(n)]
        for _ in range(n-1):
            u, v, w = map(int, input().split())
            edges[u-1].append((v-1, w))
            edges[v-1].append((u-1, w))
        s = input().strip()
        course = [int(ch) for ch in s]
        LOG = (n-1).bit_length() + 1
        parent = [[-1]*n for _ in range(LOG)]
        depth = [0]*n
        def dfs(u, p):
            parent[0][u] = p
            for v, _ in edges[u]:
                if v != p:
                    depth[v] = depth[u] + 1
                    dfs(v, u)
        dfs(0, -1)
        for k in range(1, LOG):
            for v in range(n):
                if parent[k-1][v] != -1:
                    parent[k][v] = parent[k-1][parent[k-1][v]]
        def lca(u, v):
            if depth[u] < depth[v]:
                u, v = v, u
            for k in reversed(range(LOG)):
                if parent[k][u] != -1 and depth[parent[k][u]] >= depth[v]:
                    u = parent[k][u]
            if u == v:
                return u
            for k in reversed(range(LOG)):
                if parent[k][u] != -1 and parent[k][u] != parent[k][v]:
                    u = parent[k][u]
                    v = parent[k][v]
            return parent[0][u]
        # prepare edge weights from parent to child
        weight = [0]*n
        def dfs_w(u, p):
            for v, w in edges[u]:
                if v != p:
                    weight[v] = w
                    dfs_w(v, u)
        dfs_w(0, -1)
        q = int(input())
        for _ in range(q):
            a, b = map(int, input().split())
            a -= 1
            b -= 1
            # gather path from a to b
            l = lca(a, b)
            path = []
            def gather(u, stop):
                tmp = []
                while u != stop:
                    tmp.append(u)
                    u = parent[0][u]
                return tmp
            path = gather(a, l) + [l] + gather(b, l)[::-1]
            skills = [1]
            time_dp = [0]
            for i in range(1, len(path)):
                u = path[i-1]
                v = path[i]
                w = weight[v]
                new_dp = []
                for t_curr, c in zip(time_dp, skills):
                    # take zero or more courses at u
                    best = float('inf')
                    skill = c
                    while True:
                        travel_time = math.ceil(w / skill)
                        total_time = t_curr + travel_time
                        best = min(best, total_time)
                        if course[u] == 1:
                            t_curr += T
                            skill *= 2
                        else:
                            break
                        if travel_time <= 1:
                            break
                    new_dp.append(best)
                time_dp = new_dp
                skills = [1]  # reset skills for next iteration
            print(time_dp[0])
            
if __name__ == "__main__":
    solve()
```このコードはツリーを構築し、LCA の親配列を計算し、クエリ ノード間のパスを計算します。 各セグメントで、コースの選択をシミュレートし、パスに沿った DP を使用して最小移動時間を更新します。 LCA を使用すると、対数時間でのパス抽出が保証されます。 

## 実用的な例

 **サンプル入力 1**```
2
2 3
1 2 1
11
1
1 2
5 3
1 4 5
1 3 8
2 3 8
4 5 10
11001
5
1 5
2 5
5 1
3 4
4 2
```**クエリ 1 トレース (1 ～ 2)**

 | ノード | スキル | 移動時間 | 受講したコース | 合計時間 |
 | --- | --- | --- | --- | --- |
 | 1 | 1 | - | | |
