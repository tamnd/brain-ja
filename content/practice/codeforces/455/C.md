---
title: "CF 455C - 文明"
description: "グラフは道路で結ばれた都市で構成されています。 接続されている 2 つの都市間に単純なパスが 1 つだけ存在するという条件は、接続されているすべてのコンポーネントがツリーであることを意味します。 グラフが途切れることもあるので、グラフ全体が森になっています。"
date: "2026-05-30T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "dp", "dsu", "ternary-search", "trees"]
categories: ["algorithms"]
codeforces_contest: 455
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 260 (Div. 1)"
rating: 2100
weight: 455
solve_time_s: 121
verified: true
draft: false
---

[CF 455C - 文明](https://codeforces.com/problemset/problem/455/C)

 **評価:** 2100
 **タグ:** dfs および類似の、dp、dsu、三分探索、ツリー
 **解決時間:** 2 分 1 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 グラフは道路で結ばれた都市で構成されています。 接続されている 2 つの都市間に単純なパスが 1 つだけ存在するという条件は、接続されているすべてのコンポーネントがツリーであることを意味します。 グラフが途切れることもあるので、グラフ全体が森になっています。 

2 種類の操作をオンラインで処理する必要があります。 

最初の操作では、特定の都市を含む接続コンポーネントの直径を求めます。 ここで、直径はそのツリー内の最長のパスの長さです。 

2 番目の操作では、最初のコンポーネントのある都市と 2 番目のコンポーネントのある都市の間に新しいエッジを 1 つだけ追加することにより、2 つの接続されたコンポーネントをマージします。 この新しいエッジの端点は自由に選択できますが、結果として得られるツリーの直径ができるだけ小さくなるように選択する必要があります。 

グラフには最初は以下が含まれています$3 \cdot 10^5$頂点まで$3 \cdot 10^5$クエリ。 マージのたびに直径を最初から再計算するソリューションは、すぐに遅すぎます。 コンポーネント全体にわたる単一の DFS であってもコストがかかります$O(n)$、多くのクエリに対してこれを行うと、最悪の場合、数百億回の操作が発生する可能性があります。 

重要な課題は、コンポーネントが時間の経過とともに変化することです。 コンポーネントが結合されるときに、接続された各コンポーネントの直径を効率的に維持する方法が必要です。 

いくつかの特殊なケースは、誤って処理されやすいものです。 

2 つの孤立した頂点を考えてみましょう。```
2 0 2
2 1 2
1 1
```マージ後のツリーにはエッジが 1 つあるため、直径は 1 になります。直径が 0 から増加しないと仮定する解決策は間違っています。 

すでに同じツリーに属しているコンポーネントをマージすることを検討してください。```
3 2 2
1 2
2 3
2 1 3
1 1
```マージ操作では何も行われません。 答えは 2 のままです。いずれにせよ、誤って直径更新式を適用すると、保存されている値が破損します。 

もう 1 つの微妙なケースは、最適な新しいエッジが直径の端点に接続されていない場合です。 

長さ 4 の 2 つのパスをマージするとします。直径の端点を接続すると直径 9 が生成されますが、これはひどいことです。 最適な選択は、両方のツリーの中心を接続して、直径 5 を生成することです。直径の端点のみを調べるアプローチは失敗します。 

## アプローチ

 ブルート フォース ソリューションでは、接続されているすべてのコンポーネントが明示的に維持されます。 クエリで直径が要求されるたびに、2 回の DFS トラバーサルを実行して直径を計算できます。 2 つのコンポーネントが結合するたびに、エッジを追加して続行します。 

標準のツリー直径トリックは、任意のノードから DFS を実行すると 1 つの直径エンドポイントが見つかり、そのエンドポイントから再度 DFS を実行すると直径の長さが見つかるため、機能します。 問題はコストです。 単一の直径を計算するには、コンポーネント全体を走査する必要があります。 まで$3 \cdot 10^5$クエリ、最悪のケースはおおよそ次のようになります$O(nq)$、限界をはるかに超えています。 

すべてを変える観察は、マージ操作では実際の最適なエッジを構築する必要がないということです。 2 つのツリーを可能な限り最善の方法で接続した後、直径を維持することだけが求められます。 

2 本の木に直径があると仮定します。$d_1$そして$d_2$。 

直径のある木の場合$d$、その半径は次のとおりです。$$r = \left\lceil \frac{d}{2} \right\rceil$$半径は、選択した中心からすべての頂点までの最小可能な最大距離です。 

2 つのツリーを最適に結合するときは、それらの中心を接続します。 それ以外の選択をすると、最長パスが大きくなるだけです。 

結果の直径は次のようになります。$$\max\left(d_1,\ d_2,\ r_1 + r_2 + 1\right)$$最初の 2 つの項は、1 つの元のツリー内のパスを完全にカバーします。 3 番目の項は、新しいエッジを横切るパスをカバーします。 

これは、接続コンポーネントごとに必要な情報は 1 つだけ、つまりその直径だけであることを意味します。 

接続されたコンポーネントは動的にマージされるため、Disjoint Set Union は完全に適合します。 各 DSU ルートには、そのコンポーネントの直径が保存されます。 マージ操作では 2 つの DSU セットを結合し、上記の式を使用して保存された直径を更新します。 

残りのタスクは、すべての木の初期直径を計算することです。 グラフはフォレストであるため、標準の 2 つの DFS 直径計算を使用して各連結成分を 1 回処理できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nq) | O(n) | 遅すぎる |
 | 最適 | O((n + q) α(n)) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力エッジからフォレストを構築します。 
2. DFS を使用して、接続されているすべてのコンポーネントを識別します。 
3. 各コンポーネントについて、2 つのトラバースを使用してその直径を計算します。 

まず、任意の開始点から最も遠い頂点を見つけます。 次に、最も遠い頂点から開始して、最大距離を再度見つけます。 その距離が木の直径になります。 
4. DSU 構造を初期化します。 

接続された各コンポーネントは 1 つの DSU セットとなり、ルートにはそのコンポーネントの直径が格納されます。 
5. タイプ 1 のクエリの場合、要求された都市を表す DSU を見つけて、保存されている直径を出力します。 
6. タイプ 2 のクエリの場合は、両方の都市の DSU 代表者を見つけます。 
7. 両方の代表者が同一の場合は、何もしません。 

都市はすでに同じコンポーネントに属しています。 
8. それ以外の場合は、次のように計算します。$$r_1=\left\lceil\frac{d_1}{2}\right\rceil,\quad r_2=\left\lceil\frac{d_2}{2}\right\rceil$$9. 新しい直径を計算します。$$d=\max(d_1,d_2,r_1+r_2+1)$$10. 2 つの DSU セットを結合し、この新しい直径を結果のルートに保存します。 

### なぜ効果があるのか

 各 DSU セットは常に、現在のフォレストの接続されたコンポーネントを 1 つだけ表します。 

最初に、直径は標準の木の直径アルゴリズムによって正しく計算されます。 マージ操作の場合、中心を接続すると、新しいエッジを通過できる最大距離が最小限に抑えられます。 結合されたツリー内の最長のパスは、最初のツリーの内側に完全に留まるか、2 番目のツリーの内側に完全に留まるか、新しいエッジを横切るかのいずれかでなければなりません。 これら 3 つの可能性は、次のものに正確に対応します。$$d_1,\quad d_2,\quad r_1+r_2+1$$それらの最大値を取ると、最適に結合されたツリーの真の直径が得られます。 すべての共用体がこの値を格納するため、各 DSU ルートが正しい直径を格納するという不変条件は、すべての操作の後でも真のままになります。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

def solve():
    n, m, q = map(int, input().split())

    g = [[] for _ in range(n)]

    parent = list(range(n))
    size = [1] * n

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x

    def union_initial(a, b):
        ra = find(a)
        rb = find(b)

        if ra == rb:
            return

        if size[ra] < size[rb]:
            ra, rb = rb, ra

        parent[rb] = ra
        size[ra] += size[rb]

    for _ in range(m):
        a, b = map(int, input().split())
        a -= 1
        b -= 1

        g[a].append(b)
        g[b].append(a)

        union_initial(a, b)

    diameter = [0] * n

    visited = [False] * n

    def bfs(start):
        dist = [-1] * n
        dist[start] = 0

        dq = deque([start])
        farthest = start

        while dq:
            v = dq.popleft()

            if dist[v] > dist[farthest]:
                farthest = v

            for to in g[v]:
                if dist[to] == -1:
                    dist[to] = dist[v] + 1
                    dq.append(to)

        return farthest, dist[farthest]

    for v in range(n):
        if visited[v]:
            continue

        stack = [v]
        component = []
        visited[v] = True

        while stack:
            cur = stack.pop()
            component.append(cur)

            for to in g[cur]:
                if not visited[to]:
                    visited[to] = True
                    stack.append(to)

        start = component[0]
        endpoint, _ = bfs(start)
        _, diam = bfs(endpoint)

        root = find(start)
        diameter[root] = diam

    def merge(a, b):
        ra = find(a)
        rb = find(b)

        if ra == rb:
            return

        d1 = diameter[ra]
        d2 = diameter[rb]

        new_d = max(
            d1,
            d2,
            (d1 + 1) // 2 + (d2 + 1) // 2 + 1
        )

        if size[ra] < size[rb]:
            ra, rb = rb, ra

        parent[rb] = ra
        size[ra] += size[rb]
        diameter[ra] = new_d

    ans = []

    for _ in range(q):
        query = list(map(int, input().split()))

        if query[0] == 1:
            x = query[1] - 1
            ans.append(str(diameter[find(x)]))
        else:
            x = query[1] - 1
            y = query[2] - 1
            merge(x, y)

    sys.stdout.write("\n".join(ans))

solve()
```マージ操作中に選択される実際のエンドポイントは重要ではないため、グラフは最初に 1 回構築されます。 結果として得られる直径のみが重要です。 

最初の DSU 構築では、初期フォレストに従って頂点をグループ化します。 その後、接続されているすべてのコンポーネントの直径が、古典的なダブル BFS 手法を使用して 1 回だけ計算されます。 

直径配列は DSU ルートによってインデックス付けされます。 2 つのコンポーネントが結合されるたびに、半径の公式を使用して古い直径から新しい直径が直接計算されます。 グラフを走査する必要はありません。 

よくある間違いは、`d // 2`の代わりに`(d + 1) // 2`。 直径 5 の木の半径は 2 ではなく 3 です。天井分割は必須です。 

もう 1 つの簡単な間違いは、2 つの頂点が既に同じ DSU セットに属しているかどうかを確認する前に直径を更新してしまうことです。 マージ式は、個別のコンポーネントに対してのみ有効です。 

## 実用的な例

 ### 例 1

 入力:```
6 0 6
2 1 2
2 3 4
2 5 6
2 3 2
2 5 3
1 1
```初期状態:

 | コンポーネント | 直径 |
 | --- | --- |
 | {1} | 0 |
 | {2} | 0 |
 | {3} | 0 |
 | {4} | 0 |
 | {5} | 0 |
 | {6} | 0 |

 クエリの処理:

 | クエリ | 結合されたコンポーネント | 新しい直径 |
 | --- | --- | --- |
 | 2 1 2 | {1} + {2} | 1 |
 | 2 3 4 | {3} + {4} | 1 |
 | 2 5 6 | {5} + {6} | 1 |
 | 2 3 2 | 直径 1 + 直径 1 | 3 |
 | 2 5 3 | 直径 1 + 直径 3 | 4 |
 | 1 1 | 答え | 4 |

 出力：```
4
```この例は、小さなツリーの繰り返しのマージを示しています。 直径更新式だけで十分であり、初期化後のグラフの走査は必要ありません。 

### 例 2

 入力:```
5 4 3
1 2
2 3
3 4
4 5
1 1
2 2 5
1 3
```初期コンポーネント:

 | コンポーネント | 直径 |
 | --- | --- |
 | {1,2,3,4,5} | 4 |

 処理:

 | クエリ | 結果 |
 | --- | --- |
 | 1 1 | 4 |
 | 2 2 5 | 同じコンポーネント |
 | 1 3 | 4 |

 出力：```
4
4
```これは、同じコンポーネント内のマージ リクエストは無視する必要があることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + q) α(n)) | 初期のグラフ処理は線形であり、DSU 操作は逆償却されます。 
| スペース | O(n) | グラフ、DSU アレイ、補助記憶域 |

 森には多くても$3 \cdot 10^5$頂点とエッジ。 線形前処理とほぼ一定時間の DSU 操作は、制限内に簡単に収まります。 

## テストケース```python
import sys
import io
from collections import deque

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    n, m, q = map(int, input().split())

    g = [[] for _ in range(n)]

    parent = list(range(n))
    size = [1] * n

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x

    def unite(a, b):
        ra = find(a)
        rb = find(b)
        if ra == rb:
            return
        if size[ra] < size[rb]:
            ra, rb = rb, ra
        parent[rb] = ra
        size[ra] += size[rb]

    for _ in range(m):
        a, b = map(int, input().split())
        a -= 1
        b -= 1
        g[a].append(b)
        g[b].append(a)
        unite(a, b)

    diameter = [0] * n
    vis = [False] * n

    def bfs(s):
        dist = [-1] * n
        dist[s] = 0
        dq = deque([s])
        far = s

        while dq:
            v = dq.popleft()
            if dist[v] > dist[far]:
                far = v

            for to in g[v]:
                if dist[to] == -1:
                    dist[to] = dist[v] + 1
                    dq.append(to)

        return far, dist[far]

    for i in range(n):
        if vis[i]:
            continue

        stack = [i]
        vis[i] = True
        comp = []

        while stack:
            v = stack.pop()
            comp.append(v)

            for to in g[v]:
                if not vis[to]:
                    vis[to] = True
                    stack.append(to)

        a, _ = bfs(comp[0])
        _, d = bfs(a)

        diameter[find(comp[0])] = d

    def merge(a, b):
        ra = find(a)
        rb = find(b)

        if ra == rb:
            return

        nd = max(
            diameter[ra],
            diameter[rb],
            (diameter[ra] + 1) // 2 + (diameter[rb] + 1) // 2 + 1
        )

        if size[ra] < size[rb]:
            ra, rb = rb, ra

        parent[rb] = ra
        size[ra] += size[rb]
        diameter[ra] = nd

    out = []

    for _ in range(q):
        qry = list(map(int, input().split()))

        if qry[0] == 1:
            out.append(str(diameter[find(qry[1] - 1)]))
        else:
            merge(qry[1] - 1, qry[2] - 1)

    return "\n".join(out)

# provided sample
assert run(
"""6 0 6
2 1 2
2 3 4
2 5 6
2 3 2
2 5 3
1 1
"""
) == "4"

# single isolated node
assert run(
"""1 0 1
1 1
"""
) == "0"

# merge two isolated vertices
assert run(
"""2 0 2
2 1 2
1 1
"""
) == "1"

# same component merge should do nothing
assert run(
"""3 2 2
1 2
2 3
2 1 3
1 2
"""
) == "2"

# chain of length four
assert run(
"""5 4 1
1 2
2 3
3 4
4 5
1 3
"""
) == "4"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一の孤立した頂点 | 0 | グラフの最小サイズ |
 | 2 つの孤立した頂点が結合されました | 1 | 0 からの直径の増加 |
 | 同じコンポーネント内でマージ | 2 | 正しい DSU の処理 |
 | 長さ 4 のパス | 4 | 初期直径の計算 |
 | サンプルケース | 4 | 完全なワークフロー |

 ## 特殊なケース

 2 つの孤立した頂点を考えてみましょう。```
2 0 2
2 1 2
1 1
```どちらのコンポーネントも直径 0 から始まります。マージ式は次のようになります。$$\max(0,0,0+0+1)=1$$クエリは正しく次の値を返します。```
1
```同じコンポーネント内でのマージを考えてみましょう。```
3 2 2
1 2
2 3
2 1 3
1 2
```DSU 代表は等しいため、マージ操作は直ちに終了します。 保存された直径は 2 のままで、答えは次のようになります。```
2
```長さ 4 の 2 つのパスがマージされているとします。 各ツリーの直径は 4、半径は 2 です。 

式は次のようになります。$$\max(4,4,2+2+1)=5$$中心を接続すると直径 5 になります。端点を接続すると直径 9 が作成されます。アルゴリズムは特定のエッジの選択ではなく半径ベースの式に依存するため、常に最適な値を保存します。 

最後に、孤立した頂点は自然に処理されます。 直径は 0、半径も 0 で、すべての式は特別な場合を除いて有効です。
