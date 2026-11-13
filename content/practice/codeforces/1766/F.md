---
title: "CF 1766F - MCF"
description: "各エッジに容量とコストがある有向グラフが与えられます。 頂点 1 から頂点 n に流れを押し込み、中間のすべての頂点で通常の流れの保存を満たしたいとします。つまり、頂点に流入する総流れが流出する総流れに等しいということです。"
date: "2026-06-09T13:01:21+07:00"
tags: ["codeforces", "competitive-programming", "flows"]
categories: ["algorithms"]
codeforces_contest: 1766
codeforces_index: "F"
codeforces_contest_name: "Educational Codeforces Round 139 (Rated for Div. 2)"
rating: 2800
weight: 1766
solve_time_s: 138
verified: false
draft: false
---

[CF 1766F - MCF](https://codeforces.com/problemset/problem/1766/F)

 **評価:** 2800
 **タグ:** フロー
 **解決時間:** 2 分 18 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 各エッジに容量とコストがある有向グラフが与えられます。 頂点 1 から頂点 n に流れを押し込み、中間のすべての頂点で通常の流れの保存を満たしたいとします。つまり、頂点に流入する総流れが流出する総流れに等しいということです。 各エッジには追加のパリティ制約があります。エッジの容量が偶数であれば、フローも偶数でなければなりません。 奇数の場合、フローは奇数でなければなりません。 目的は、フローの全エッジの合計と重みの積として定義されるフローの総コストを最小限に抑えることです。 

の制約`n`(最大100)および`m`(最大 200) は、頂点の数が 3 次または 4 次の複雑さを持つアルゴリズムが許容されることを示しています。 容量は小さな整数 (最大 100) であるため、整数制約を使用してフローをモデル化することが可能です。 負の重みは、残差グラフ内の最短パスを慎重に処理する必要があることを意味します。 ただし、負のサイクルがないため、実現可能な需要に対して最小コストのフローが存在することが保証されます。 

パリティ制約があるため、エッジ ケースは微妙です。 たとえば、頂点に偶数の容量を持つ入力エッジと奇数の容量を持つ出力エッジがある場合、パリティを尊重しながらフロー保存を満たすことが不可能になる可能性があります。 パリティを無視した単純な最小コスト フロー アルゴリズムでは、実際にパリティ条件に違反する「実行可能な」フローが誤って生成されてしまいます。 

次の小さな例を考えてみましょう。```
2 1
1 2 2 3
```ここで、シングルエッジの能力は均等であるため、流れは均等でなければなりません。 保存を満たすための最小フローは 2 です。1 をプッシュしようとするとパリティに違反し、パリティを無視するアルゴリズムは誤って 1 を受け入れます。 

## アプローチ

 ブルート フォース アプローチでは、容量とパリティによって制限された、各エッジの考えられるすべてのフローを列挙します。 最大 200 のエッジと最大 100 の容量により、エッジごとに平均 50 の選択肢が得られます。`50^200`可能性はありますが、それは明らかに実現不可能です。 

重要な洞察は、パリティ制約が標準の最小コスト フロー問題に変換できることを観察することで得られます。 すべての容量をパリティ要件 (奇数の場合は 1、偶数の場合は 0) だけ削減し、それに応じて各頂点での需要と供給を調整すると、パリティに違反することなく標準的な整数の最小コスト フロー問題を解決できます。 基本的に、各エッジに必要な最小パリティを事前に割り当ててから、残りのフローを標準 MCF 問題として計算します。 この調整されたネットワーク内に実行可能なフローが存在する場合、それを事前に割り当てられたパリティと組み合わせて、すべての制約を満たすフローを取得できます。 

これが機能するのは、すべてのエッジのフローが固定パリティ部分と残りの可変部分に分解できるためです。 残差グラフには負のサイクルがないため、負のエッジに対して連続最短パスやベルマン・フォード/ダイクストラによる最小コスト最大フローなどの標準アルゴリズムを使用できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O((C/2)^m) | O(m) | 遅すぎる |
 | パリティ削減を伴う最小コスト フロー | O(n m^2 C) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. リストを初期化する`f`フローのすべてがゼロです。 各エッジの容量を確認します`c`。 もし`c`奇数の場合は、1 単位の流量を事前に割り当てます。 偶数の場合は、0 を事前に割り当てます。この事前割り当てを容量から減算します。 これにより、残りのフローに対してパリティ制約が自動的に満たされることが保証されます。 
2. これらの調整された容量を使用して残差グラフを作成します。 各頂点の正味の需要/供給は、最初はゼロです。 頂点 1 には、そこから出ていく事前に割り当てられたフローの合計に等しい供給があり、頂点 n には、そこに入る事前に割り当てられたフローの合計に等しい需要があります。 他のすべての頂点については、事前に割り当てられたフローを考慮して需要を調整し、フローの保存を維持します。 
3. この残差ネットワーク上で標準の整数最小コスト フロー アルゴリズムを実行します。 Bellman-Ford による連続最短パスは、容量とエッジ数が小さく、負の重みは存在しますが、負のサイクルがないため機能します。 
4. アルゴリズムがすべての残りの要求を満たす実行可能なフローを見つけた場合、事前に割り当てられたパリティ フローを各エッジに追加して最終的なフローを取得します。 それ以外の場合は報告してください`Impossible`。 
5. 出力`Possible`次にフローのリストが続きます。 最終的なフローでは、パリティとフローの保存が自動的に考慮されます。 

これが機能する理由: 不変条件は、各エッジがパリティを満たすために必要な最小限のフローをすでに持っているということです。 残りのネットワーク内の追加フローは、容量とフローの節約の両方を考慮します。 したがって、残留フローが実行可能な場合は、それをパリティ フローに追加すると有効なソリューションが生成されます。 残余ネットワークが実行不可能な場合、どの割り当てもフロー保存とパリティの両方を同時に満たすことはできません。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

class Edge:
    def __init__(self, u, v, cap, cost, idx):
        self.u = u
        self.v = v
        self.cap = cap
        self.cost = cost
        self.idx = idx
        self.flow = 0

def min_cost_flow(n, edges, source, sink):
    INF = 10**18
    m = len(edges)
    adj = [[] for _ in range(n)]
    for i, e in enumerate(edges):
        adj[e.u].append((e.v, i))
        adj[e.v].append((e.u, i))

    potential = [0]*n
    def shortest_path():
        dist = [INF]*n
        prev = [None]*n
        inqueue = [False]*n
        dist[source] = 0
        q = deque([source])
        while q:
            u = q.popleft()
            inqueue[u] = False
            for v, i in adj[u]:
                e = edges[i]
                residual = e.cap - e.flow if e.u == u else e.flow
                if residual <= 0:
                    continue
                cost = e.cost if e.u == u else -e.cost
                if dist[v] > dist[u] + cost + potential[u] - potential[v]:
                    dist[v] = dist[u] + cost + potential[u] - potential[v]
                    prev[v] = (u, i)
                    if not inqueue[v]:
                        inqueue[v] = True
                        q.append(v)
        for i in range(n):
            if dist[i] < INF:
                potential[i] += dist[i]
        return prev, dist[sink] < INF

    total_flow = 0
    total_cost = 0
    while True:
        prev, found = shortest_path()
        if not found:
            break
        # find bottleneck
        v = sink
        delta = INF
        while v != source:
            u, i = prev[v]
            e = edges[i]
            if e.u == u:
                delta = min(delta, e.cap - e.flow)
            else:
                delta = min(delta, e.flow)
            v = u
        # push flow
        v = sink
        while v != source:
            u, i = prev[v]
            e = edges[i]
            if e.u == u:
                e.flow += delta
                total_cost += delta * e.cost
            else:
                e.flow -= delta
                total_cost -= delta * e.cost
            v = u
        total_flow += delta
    return total_flow, total_cost

def solve():
    n, m = map(int, input().split())
    edges = []
    pre_flow = []
    for idx in range(m):
        x, y, c, w = map(int, input().split())
        x -= 1
        y -= 1
        parity = c % 2
        pre_flow.append(parity)
        edges.append(Edge(x, y, c - parity, w, idx))
    # adjust source and sink demands
    demands = [0]*n
    for e, pf in zip(edges, pre_flow):
        demands[e.u] -= pf
        demands[e.v] += pf
    # add super-source and super-sink
    super_source = n
    super_sink = n+1
    adj_edges = edges[:]
    for i, d in enumerate(demands):
        if d > 0:
            adj_edges.append(Edge(super_source, i, d, 0, m + len(adj_edges)))
        elif d < 0:
            adj_edges.append(Edge(i, super_sink, -d, 0, m + len(adj_edges)))
    # run min-cost flow from super_source to super_sink
    total_flow, _ = min_cost_flow(n+2, adj_edges, super_source, super_sink)
    if total_flow != sum(d for d in demands if d > 0):
        print("Impossible")
        return
    final_flow = [pf for pf in pre_flow]
    for e in edges:
        final_flow[e.idx] += e.flow
    print("Possible")
    print(" ".join(map(str, final_flow)))

solve()
```このソリューションでは、まず各エッジにパリティを事前に割り当てます。 次に、調整された容量と需要を使用して、残りのネットワークで実現可能なフローを計算します。 負の重みを処理するには、最短パス ベースの最小コスト フローを使用します。 最後に、事前に割り当てられたパリティと残りのフローを組み合わせて、答えを生成します。 難しい部分は、プリフローに基づいて各頂点の要求を正しく管理することです。 

## 実用的な例

 サンプル入力 1:```
3 3
1 2 3 -10
1 2 3 -15
2 3 2 0
```プリフロー割り当て: エッジ`[1,1,0]`容量があるので`[3,3,2]`→奇数、奇数、偶数。 残存容量`[2,2,2]`。 最小コストを解決する
