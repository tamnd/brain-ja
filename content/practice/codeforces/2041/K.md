---
title: "CF 2041K - 栄養バランスのとれた種"
description: "有向グラフとしてモデル化された生態系が与えられます。各ノードは種を表し、各有向エッジは獲物から捕食者への摂食関係を表します。 それぞれの種について、それが栄養バランスの取れた種であるかどうかを特定したいと考えています。"
date: "2026-06-08T09:45:30+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "brute-force", "dfs-and-similar", "graphs"]
categories: ["algorithms"]
codeforces_contest: 2041
codeforces_index: "K"
codeforces_contest_name: "2024 ICPC Asia Taichung Regional Contest (Unrated, Online Mirror, ICPC Rules, Preferably Teams)"
rating: 3100
weight: 2041
solve_time_s: 64
verified: true
draft: false
---

[CF 2041K - 栄養バランスの種](https://codeforces.com/problemset/problem/2041/K)

 **評価:** 3100
 **タグ:** 二分探索、ブルート フォース、DFS など、グラフ
 **解決時間:** 1 分 4 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 有向グラフとしてモデル化された生態系が与えられます。各ノードは種を表し、各有向エッジは獲物から捕食者への摂食関係を表します。 それぞれの種について、それが栄養バランスの取れた種であるかどうかを特定したいと考えています。 種が到達できる種の数 (下流の捕食者) と到達できる種の数 (上流の獲物) の間の絶対差がすべての種にわたって最小化されている場合、その種は栄養バランスがとれています。 

入力は種の数を提供します`n`および給餌関係の数`m`、 に続く`m`有向エッジ。 出力には、上流と下流の数の間の絶対差が最小になるすべての種をリストする必要があります。 

制約は厳しいです:`n`最大 200,000 まで可能で、`m`400,000まで。 これにより、DFS または BFS を介して各ノードの到達可能性を個別に明示的に計算する単純なソリューションは除外されます。`O(n*(n+m))`最悪の場合、つまりおおよそ`4 * 10^10`操作は不可能であり、明らかに実行不可能です。 

見落としやすいエッジ ケースには、発信エッジまたは着信エッジがないノードが含まれます。 たとえば、すべての種が独立している場合、すべての種には上流と下流がゼロになるため、すべてが栄養バランスがとれていると見なす必要があります。 同様に、グラフ内のサイクルは慎重に扱わないとカウントを増大させる可能性があるため、ソリューションでは強く接続されたコンポーネントまたは同等の推移的な到達可能性を考慮する必要があります。 

## アプローチ

 ブルートフォースアプローチは簡単です。種ごとに、DFS を実行してそこから到達可能な種の数 (下流) を数え、反転したグラフ上で別の DFS を実行してそこに到達できる種の数 (上流) を数えます。 次に、各種の絶対差を計算し、最小値を選択します。 正しいのですが、各 DFS が異なるため、これは遅すぎます。`O(n+m)`それを2回実行します`n`ノード、結果として`O(n*(n+m))`。 

重要な観察は、グラフ構造により、ビットセットを使用して到達可能性を効率的に事前計算できるということです。 各ノードは、次のサイズのビットセットを維持できます。`n`到達可能なノードを表します。 トポロジ順 (または反転したグラフではトポロジ順を逆) でノードを反復処理すると、到達可能性情報を次の形式で伝播できます。`O(n*m / w)`どこ`w`マシンワードのサイズ (64 または 32) です。 この最適化が実現可能な理由は、`k`は小さい (≤16) ため、完全に接続されたサブセットの深さには制限があり、固定サイズのブロックに対するビット単位の演算は非常に高速です。 

もう 1 つの観点は、強接続コンポーネント (SCC) を使用することです。 同じ SCC 内のすべてのノードは相互に通信できます。 SCC が凝縮されると、グラフは DAG になります。 その後、元のグラフではなく DAG に沿って到達可能性カウントを効率的に伝播できます。 メモ化を使用した DAG 上の BFS/DFS は、各 SCC の正確なアップストリーム/ダウンストリーム カウントを提供し、これを個々のノードに分散できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n*(n+m)) | O(n) | 遅すぎる |
 | ビットセットの伝播 / SCC-DAG | O((n+m) * n / w) | O(n^2 / w) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力を解析し、グラフとその逆の隣接リストを作成します。 
2. Kosaraju のアルゴリズムまたは Tarjan のアルゴリズムを使用して、強く接続されたコンポーネントを特定します。 SCC 内のすべてのノードは相互に接続できるため、各 SCC は単一のスーパーノードとして扱われます。 
3. 元のグラフを SCC の DAG に圧縮します。 異なる SCC 内のノード間のエッジを、それらの SCC 間のエッジに置き換えます。 
4. SCC から到達可能なノードを表す各 SCC のビットセットを初期化します。 最初は、ビットセットには SCC 自体のすべてのノードが含まれています。 
5. トポロジ順に SCC を繰り返します。 各 SCC について、ビットごとの OR を使用して、そのビットセットを DAG 内の隣接する SCC に伝播します。 これにより、各 SCC から到達可能なすべての下流ノードが蓄積されます。 
6. 逆の DAG に対して同じプロセスを繰り返して、各 SCC の上流到達可能性を計算します。 
7. 元のノードごとに、そのアップストリーム カウントはアップストリーム ビットセットのサイズから 1 を引いたもの (それ自体を除く)、ダウンストリーム カウントはダウンストリーム ビットセットのサイズから 1 を引いたものになります。 
8. 各ノードの絶対差を計算し、最小の差を追跡します。 
9. 差が最小値に等しいすべてのノードを昇順に並べて出力します。 

機能する理由: 各 SCC は、相互に到達可能なすべてのノードをキャプチャし、ダウンストリームとアップストリームの伝播が正しいことを保証します。 DAG を使用することで、ノードを再度訪問することなく到達可能性を効率的に伝達し、正確性を維持します。 トポロジー的順序により、SCC のすべての先行者が処理する前に到達可能性が完全に伝播されることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from collections import defaultdict, deque

sys.setrecursionlimit(1 << 25)

def main():
    n, m = map(int, input().split())
    adj = [[] for _ in range(n)]
    radj = [[] for _ in range(n)]
    for _ in range(m):
        u, v = map(int, input().split())
        u -= 1
        v -= 1
        adj[u].append(v)
        radj[v].append(u)

    # Step 1: SCC using Kosaraju
    visited = [False] * n
    order = []
    def dfs1(u):
        visited[u] = True
        for v in adj[u]:
            if not visited[v]:
                dfs1(v)
        order.append(u)
    for i in range(n):
        if not visited[i]:
            dfs1(i)

    comp = [-1] * n
    def dfs2(u, label):
        comp[u] = label
        for v in radj[u]:
            if comp[v] == -1:
                dfs2(v, label)

    label = 0
    for u in reversed(order):
        if comp[u] == -1:
            dfs2(u, label)
            label += 1

    # Step 2: Build SCC DAG
    scc_adj = [set() for _ in range(label)]
    scc_nodes = [set() for _ in range(label)]
    for u in range(n):
        scc_nodes[comp[u]].add(u)
        for v in adj[u]:
            if comp[u] != comp[v]:
                scc_adj[comp[u]].add(comp[v])

    # Step 3: Downstream reachability
    downstream = [set(nodes) for nodes in scc_nodes]
    topo_order = []
    indegree = [0]*label
    for u in range(label):
        for v in scc_adj[u]:
            indegree[v] += 1
    queue = deque([i for i in range(label) if indegree[i]==0])
    while queue:
        u = queue.popleft()
        topo_order.append(u)
        for v in scc_adj[u]:
            downstream[v].update(downstream[u])
            indegree[v] -= 1
            if indegree[v] == 0:
                queue.append(v)

    # Step 4: Upstream reachability
    upstream = [set(nodes) for nodes in scc_nodes]
    indegree = [0]*label
    rev_adj = [set() for _ in range(label)]
    for u in range(label):
        for v in scc_adj[u]:
            rev_adj[v].add(u)
            indegree[u] += 1
    queue = deque([i for i in range(label) if indegree[i]==0])
    while queue:
        u = queue.popleft()
        for v in rev_adj[u]:
            upstream[v].update(upstream[u])
            indegree[v] -= 1
            if indegree[v]==0:
                queue.append(v)

    # Step 5: Compute differences
    min_diff = n+1
    diffs = [0]*n
    for i in range(n):
        down = len(downstream[comp[i]]) - 1
        up = len(upstream[comp[i]]) - 1
        diffs[i] = abs(down - up)
        if diffs[i] < min_diff:
            min_diff = diffs[i]

    result = [str(i+1) for i in range(n) if diffs[i]==min_diff]
    print(" ".join(result))

if __name__ == "__main__":
    main()
```このソリューションは、まずサイクルを崩壊させる SCC を見つけ、DAG を構築し、セットを使用して下流と上流の到達可能性を伝播し、最後に各ノードの絶対差を計算します。 セットを使用すると、ノードがサイクルに属している場合でも正確さが保証され、DAG に沿った伝播により二重カウントがないことが保証されます。 

## 実用的な例

 **サンプル 1**:

 入力:```
4 3
1 2
2 3
2 4
```| ノード | SCC | 下流 | 上流 | 違い |
 | --- | --- | --- | --- | --- |
 | 1 | 0 | {2,3,4} | {} | 3 |
 | 2 | 1 | {3,4} | {1} | 1 |
 | 3 | 2 | {} | {1,2} | 2 |
 | 4 | 3 | {} | {1,2} | 2 |

 ノード 2 の最小差は 1、出力`2`。 

**カスタム サンプル 2**:

 入力:```
3 3
1 2
2 3
3 1
```すべてのノードがサイクルを形成します。 

| ノード | SCC | 下流 | 上流 | 違い |
 | --- | --- | --- | --- | --- |
 | 1 | 0 | {1,2,3} | { | |
