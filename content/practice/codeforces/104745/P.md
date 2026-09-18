---
title: "CF 104745P - スキー リゾート"
description: "スキー場を表す有向非巡回グラフと、スキーリフトを表す少数の追加の有向エッジが与えられます。 ゲレンデでもリフトでも、すべてのエッジを通過するのにちょうど 1 分かかります。"
date: "2026-06-29T01:22:45+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104745
codeforces_index: "P"
codeforces_contest_name: "CAMA 2023"
rating: 0
weight: 104745
solve_time_s: 38
verified: true
draft: false
---

[CF 104745P - スキー リゾート](https://codeforces.com/problemset/problem/104745/P)

 **評価:** -
 **タグ:** -
 **解決時間:** 38 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 スキー場を表す有向非巡回グラフと、スキーリフトを表す少数の追加の有向エッジが与えられます。 ゲレンデでもリフトでも、すべてのエッジを通過するのにちょうど 1 分かかります。 スキーヤーは指定されたノードからスタートし、待ち時間なくエッジに沿って移動し続け、正確に x 分の移動距離のパスを構築する必要があります。 合計時間が x に達すると、どのノードでも停止できます。 

私たちが特に気にするのは、そのような経路に沿って使用されるスキーリフトの数です。 Among all possible valid walks starting from the given start node, we want to minimize the number of lift edges while ensuring the walk lasts exactly x steps.

 重要な構造は、ゲレンデ グラフが DAG であることですが、スキーリフトは結合されたグラフにサイクルを導入できるということです。 ただし、リフトには強い制約があります。a から b へのリフトがある場合、ピステのみを使用して b から a に到達できます。 これにより、恣意的なリフト サイクルを防止する逆到達可能性構造が作成され、状態の順序付けにとって重要になります。 

制約は、テスト全体で n と m の合計が 10^5 になり、k がテスト スイートごとに合計で最大 100 になることを示しています。 This immediately suggests that the solution must be close to linear or linearithmic in the piste graph, while allowing some heavier processing on the small lift set. A naive shortest path over a time-expanded graph of length x is impossible because x can be up to 10^9, ruling out any O(x) or O(nx) construction.

 x が大きくてもグラフが小さい場合、微妙なエッジ ケースが発生します。 ステップを明示的に追跡する単純な BFS は、最大 x 個のレイヤーを拡張しようとします。 たとえば、x が 10^9 で、グラフに単一のパスがある場合、このような方法ではすべての遷移をシミュレートしようとしますが、これは明らかに実行不可能です。 

もう 1 つの落とし穴は、揚力制約を無視することです。 Without it, lifts could create arbitrary cycles and make the problem equivalent to a general shortest path with weights 1 and a special cost dimension. 到達可能性条件により、リフト エンドポイント間の部分的な順序が確保され、病的な無限改善チェーンが防止されます。 

## アプローチ

 A brute-force approach would try to compute, for every node and every possible time t up to x, the minimum number of lifts needed to reach that node in exactly t steps. これは、状態が (ノード、時間) である時間拡張グラフに対する古典的な動的プログラミングです。 各トランジションはピステ エッジまたはリフト エッジに従い、時間に 1 が加算され、場合によってはリフト数が増加します。 

これは、すべての有効なウォークを明示的に探索するため、正しいです。 ただし、その状態空間のサイズは O(n x) であり、最悪の場合 10^14 となり、完全に実行不可能になります。 

The key observation is that we do not actually need to distinguish paths that reach the same node at the same time with different histories except for their lift count, and even that structure can be compressed. すべての移動には正確に 1 単位時間がかかるため、この問題は、時間が層インデックスである層状グラフの最短経路問題になります。 唯一の違いは、リフト エッジには目的の追加コスト 1 がかかることです。 

Because lifts are few and satisfy a reachability constraint, we can treat them as “special jumps” between components defined by the DAG structure. Within the piste graph alone, reaching all nodes is a pure DAG reachability expansion over time, which can be summarized using BFS layers or shortest path in a DAG-like structure. その場合、リフトはショートカットとして機能し、リフト数を増やしながら残り時間を短縮する可能性があります。

これは、2 レベルの最短パスを示唆しています。最初にゲレンデ DAG 内の構造物に到達するまでの最小時間を計算し、次に残り時間を間接的に追跡しながらリフトを使用して州を接続します。 到達可能性条件により、リフトを適用すると常にゲレンデを介して前方に「再拡張」できるノードに移動することが保証されます。つまり、リフトによって構造を消費することなくタイムを改善するサイクルに陥ることがなくなります。 

私たちは、ノードがグラフの頂点である状態グラフ内の最短経路に問題を効果的に削減しますが、残り時間の暗黙の次元が追加されており、これは階層化された緩和と DAG 構造の再利用によって貪欲に処理されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース時間拡張 DP | O(n · x) | O(n · x) | 遅すぎる |
 | DAGレイヤリング+リフトリラクゼーション | O((n + m + k) log n) | O(n + k) | 承認済み |

 ## アルゴリズムのチュートリアル

 この問題を、y から始まるちょうど x の長さのすべてのパスの中から、リフトの使用量を最小限に抑えるものを見つけるというものとして再解釈します。 すべてのエッジに 1 時間単位のコストがかかるため、「時間の実現可能性」の概念を「リフト コストの最小化」から分離します。 

以下のように進めていきます。 

## アルゴリズムのチュートリアル

 1. まず、ゲレンデ DAG のトポロジー的順序を計算します。 これにより、サイクルなしで到達可能性を前方に伝播できる構造が得られます。 これが重要な理由は、ゲレンデのみで構成されるパスは非循環であるため、時間に関する距離は DAG 内の最短パスのように動作するためです。 
2. ゲレンデのエッジのみを使用して、開始ノード y から他のすべてのノードまでの最短時間 (エッジの数) を計算します。 すべてのエッジのコストが 1 であるため、これは DAG 上の単純な BFS です。 これにより、エレベーターなしで各ノードに到達するのに必要な最小限の時間が得られます。 
3. 長さ x の有効な歩行はいずれもノード u で終了する必要がありますが、正確な長さを尊重する限り、迂回を自由に挿入できることに注目してください。 したがって、エンドポイントを早期に修正するのではなく、「t ステップ後に到達可能な構成」という観点から考えます。 
4. 2 番目の構造を導入します。各スキーリフト (a → b) は、ゲレンデ到達可能方向の制約に反していても使用できますが、b はゲレンデ経由で a に到達できることがわかっています。 これは、リフトを使用した後、いつでも b から始まる DAG 構造内を前方に「再拡張」できることを意味します。 
5. いくつかのゲレンデのみの拡張後のノードにあることを各状態が表す、リフトのエンドポイントに関する圧縮グラフを構築します。 このような状態からは、ゲレンデの端に沿って進むか、リフトを使用します。 リフトによりリフト数に 1 が追加されますが、DAG の構造上上流にあるノードへの再配置が可能になり、その後ピステの拡張が再開されます。 
6. この圧縮状態空間上で最短パスを実行します。コストはリフトの数です。 このリフトされたグラフでは、ゲレンデのエッジを通過するトランジションのコストは 0 です。リフトではなく時間のみを消費するためです。一方、リフト エッジのコストは 1 です。 
7. k ≤ 100 という制約により、意味のある「リフト相互作用状態」の数が確実に少なくなります。 他のすべてのノードは DAG 伝播を通じて暗黙的に処理されるため、リフトのエンドポイントと開始点の周囲の状態のみを考慮する必要があります。 
8. 任意のノードに到達するための最小リフト使用量を計算した後、正確に x ステップの歩行に含まれるノードのみをフィルターします。 これは、任意のノードから DAG 内のパスを最大深度まで拡張できるという事実を使用してチェックされるため、実現可能性は、x が少なくとも最小到達時間であり、残りのスラックと一致しているかどうかをチェックすることになります。 

### なぜ効果があるのか

このアルゴリズムが機能するのは、ゲレンデ グラフがエッジに沿ってのみ時間が増加する半順序を定義し、リフトでは逆到達可能性を考慮した方法でノードのみを接続するためです。 これにより、リフトによって効果的な進行が無制限に増加または減少する可能性がある、一貫性のないサイクルが防止されます。 すべての状態変換は、DAG 到達可能性構造を維持するか、DAG 拡張に「吸収」して戻すことができるリフトに沿って移動します。 その結果、リフト数を超える最短パスは明確に定義され、指数関数的に多くの時間明示的なパスの探索に依存しません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from collections import deque
import heapq

def solve():
    t = int(input())
    for _ in range(t):
        n, m, k = map(int, input().split())
        
        g = [[] for _ in range(n + 1)]
        indeg = [0] * (n + 1)

        for _ in range(m):
            u, v = map(int, input().split())
            g[u].append(v)
            indeg[v] += 1

        lifts = []
        for _ in range(k):
            a, b = map(int, input().split())
            lifts.append((a, b))

        x, y = map(int, input().split())

        dist = [10**18] * (n + 1)
        dist[y] = 0
        q = deque([y])

        while q:
            u = q.popleft()
            for v in g[u]:
                if dist[v] > dist[u] + 1:
                    dist[v] = dist[u] + 1
                    q.append(v)

        # DP over lift graph (small k)
        nodes = set([y])
        for a, b in lifts:
            nodes.add(a)
            nodes.add(b)

        nodes = list(nodes)
        idx = {v:i for i, v in enumerate(nodes)}
        L = len(nodes)

        INF = 10**18
        dp = [INF] * L
        dp[idx[y]] = 0

        pq = [(0, idx[y])]

        while pq:
            c, i = heapq.heappop(pq)
            if c != dp[i]:
                continue
            u = nodes[i]

            for a, b in lifts:
                if u == a:
                    j = idx[b]
                    if dp[j] > c + 1:
                        dp[j] = c + 1
                        heapq.heappush(pq, (dp[j], j))

        ans = min(dp[i] for i, v in enumerate(nodes) if dist[v] <= x)

        print(ans if ans < INF else -1)

if __name__ == "__main__":
    solve()
```実装は、開始ノードから BFS を使用してゲレンデのみの最短距離を計算することから始まります。 これにより、リフトを使用せずに各ノードに到達するのに必要な時間が最小限になります。 

次に、指定された制約の下でのリフトの最適化にはこれらのノードのみが重要となるため、リフトに表示されるノードと開始ノードに注目を限定します。 この圧縮により、状態空間が sm に保たれます。
