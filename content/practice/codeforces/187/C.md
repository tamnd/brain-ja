---
title: "CF 187C - メモリが弱い"
description: "公園内の交差点と道路を表す無向グラフがあります。 一部の交差点にはボランティアがいます。 PMP は交差点 s から始まり、バス停は交差点 t にあります。 PMPは記憶力が弱いです。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "dsu"]
categories: ["algorithms"]
codeforces_contest: 187
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 119 (Div. 1)"
rating: 2000
weight: 187
solve_time_s: 132
verified: true
draft: false
---

[CF 187C - 弱いメモリ](https://codeforces.com/problemset/problem/187/C)

 **評価:** 2000
 **タグ:** dfs および類似の、dsu
 **解決時間:** 2 分 12 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 公園内の交差点と道路を表す無向グラフがあります。 一部の交差点にはボランティアがいます。 PMP は交差点から始まります`s`、バス停は交差点にあります`t`。 

PMPは記憶力が弱いです。 ボランティアと話した後、彼はせいぜい次の内容を含むパスしか思い出せません。`q`現在の交差点を除く交差点。 道路では経路の長さが測定されるため、ボランティアは最大でも経路に沿ってしか案内できないことを意味します。`q`エッジ。 

ボランティアは PMP を直接案内することもできます。`t`、または別のボランティアに案内します。 ボランティアは常に最適に協力します。 最小限のものを見つけなければなりません`q`PMP が確実に到達するようにする`t`。 

グラフには最大で`10^5`頂点と`2 * 10^5`エッジ。 すべてのボランティアから独立して BFS を実行するソリューションは、すでに危険になっています。 もし`k`も近いです`10^5`、その後、`O(k * (n + m))`アプローチは限界をはるかに超えています。 

問題の構造は微妙です。 PMP はルート全体を記憶する必要はありません。`s`に`t`。 彼はボランティア間を移動するだけでよく、最終的にはあるボランティアから別のボランティアに移動するだけです。`t`。 これにより、問題は距離のしきい値以下の特別なノード間の接続に変わります。 

いくつかの特殊なケースは、誤って処理されやすいものです。 

次のグラフを考えてみましょう。```
1 -- 2 -- 3
```ボランティアのみ`1`、 始める`s = 1`、ターゲット`t = 3`。 

からの最短経路`1`に`3`長さがある`2`、したがって、答えは次のとおりです`2`。 ボランティアからボランティアへの移行のみをチェックする不用意なソリューションでは、ボランティアへの移行が達成されたと誤って結論付けることになります。`t`不可能だから`t`ボランティアではありません。 

もう 1 つの注意が必要なケースは、グラフが切断されていることです。 

入力：```
4 2 1
1
1 2
3 4
1 4
```正しい出力:```
-1
```任意に大きくても`q`からの道がないので助けられません`1`に`4`。 

もう 1 つの微妙なケースは、中級ボランティアを使用すると必要なメモリが減少する場合に現れます。 

入力：```
5 4 2
1 3
1 2
2 3
3 4
4 5
1 5
```からの最短経路`1`に`5`長さがある`4`、しかし答えは`2`。 

PMP は以下を移動できます。`1 -> 3 -> 5`最初のセグメントには長さがあります`2`、2番目のセグメントにも長さがあります`2`。 

からの最短パスのみを計算するソリューション`s`に`t`問題の実際の構造を見逃しています。 

## アプローチ

 ブルートフォース解釈は簡単です。 いくつかの候補値を修正するとします`q`。 ボランティアとターゲットを頂点とする新しいグラフを構築できます。`t`。 

2 つの頂点は、元のグラフでの最短経路距離が最大以下の場合に接続されます。`q`。 

PMP はまさに次の場合に成功します。`s`到達できる`t`この圧縮されたグラフ内で。 

単一のものをテストするには`q`、すべてのボランティアから BFS を実行できます。`t`、までのすべてのペアごとの距離を計算します。`q`。 あれば`k`ボランティアの場合、おおよその費用がかかります`O((k + 1)(n + m))`。 

あらゆる可能性を試す`q`二分探索を使用した値を使用すると、事態はさらに悪化します。 最悪の場合:```
O(log n * k * (n + m))
```すべての限界が近づいている状態で`10^5`、これは完全に不可能になります。 

重要な観察は、徐々に距離を広げていくと、その答えはグラフのコンポーネントがどのようにマージされるかによってのみ決まるということです。 

最短経路距離が最大である頂点のペアをすべて知っていると仮定します。`d`。 次に、そのようなすべてのペアは、次の場合に効果的に PMP に接続されます。`q = d`。 

これは、距離を昇順に処理し、現在のしきい値内で到達可能になるとグラフ領域を結合することを示唆しています。 

この種の問題に対する古典的な手法は、マルチソース BFS と DSU を組み合わせることです。 

すべてのボランティアが同時に拡大することを想像してください。 すべての頂点は、どのボランティアが最初に到達したかを記憶します。 2 人の異なるボランティアからの BFS 波がエッジを越えて交わるとき、それら 2 人のボランティア間の最短距離がわかります。 

より正確には、次の場合:```
dist[u] + dist[v] + 1 = D
```そして`u`そして`v`異なるボランティア ソースに属している場合、それらのボランティア間の最短距離は正確に`D`。 

これにより、ボランティア コンポーネント間に重み付けされたエッジが生成されます。 これらのエッジを距離で並べ替えた後、昇順に結合できます。 コンポーネントが含まれる最初の瞬間`s`直接到達できるコンポーネントに接続されるようになります。`t`答えを出します。 

この問題は、ボランティアの暗黙的なグラフに対するクラスカルのアルゴリズムに非常に似ています。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(log n * k * (n + m)) | O(n + m) | 遅すぎる |
 | 最適 | O((n + m) log (n + m)) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべてのボランティア頂点から同時にマルチソース BFS を実行します。 

すべての頂点には以下が格納されます。`owner[v]`、最初に到達したボランティアの情報源。`dist[v]`、そのソースからの距離。 

これにより、グラフがボランティアの周りのボロノイのような領域に分割されます。 
2. BFS 中にエッジを検査するたびに`(u, v)`エンドポイントが異なる所有者に属している場合、2 つのボランティア ソース間の接続候補を発見します。 

これら 2 つのソース間の最短パスは次のとおりです。```
dist[u] + dist[v] + 1
```パスは 1 つのソースから`u`、エッジを越えてから他のソースに到達します。`v`。 
3. ボランティア ソース間でそのようなすべての候補エッジを保存します。 

複数のエッジが同じソースのペアに接続する場合があります。 クラスカル スタイルの処理では、より悪質な重複は自然に無視されるため、これは問題ありません。 
4. ボランティアを対象者に結び付ける必要もあります`t`。 

通常の BFS を実行します。`t`すべての頂点からの最短距離を計算します。`t`。 
5. すべてのボランティアソースに対して`x`、 記録`dt[x]`、そのボランティアからの最短距離`t`。 

PMPにメモリがある場合`q >= dt[x]`、その後ボランティア`x`バスに直接案内できます。 
6. すべてのボランティア接続エッジを距離によって並べ替えます。 
7. ボランティア ソースとターゲットを表す 1 つの追加ノード上に DSU を作成します。 
8. 距離の小さい順に候補エッジを処理します。 

重みのあるエッジを加工する場合`w`、2 つのボランティア コンポーネントを結合します。 
9. それとは別に、ボランティアを距離に応じて並べ替えます。`t`。 

いつでも`dt[x] <= current_weight`、組合ボランティア`x`特別なターゲットノードを使用します。 
10. 開始ボランティアとターゲットノードが接続される最初の重みは、有効な最小値です。`q`。 
11. 接続が行われない場合は、出力します。`-1`。 

### なぜ効果があるのか

 マルチソース BFS は、2 つの BFS 領域が会合するたびに、対応するボランティア ソース間の真の最短距離が生成されることを保証します。 これは、最短経路ボロノイ分解で使用されるのと同じプロパティです。 

エッジを昇順に処理すると、許容されるメモリ制限が徐々に増加することをシミュレートします。`q`。 

処理中のどの時点でも、PMP が現在のしきい値以下の長さのセグメントを使用して 2 つのボランティア間を移動できる場合、2 つのボランティアは同じ DSU コンポーネントに属します。 

同様に、ボランティアが特別なターゲット ノードに接続されると、PMP はそのボランティアから直接ターゲット ノードに移動できます。`t`。 

最初のしきい値は、`s`につながるようになる`t`is precisely the smallest feasible memory limit.

 ## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n

    def find(self, x):
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]
            x = self.parent[x]
        return x

    def union(self, a, b):
        a = self.find(a)
        b = self.find(b)

        if a == b:
            return

        if self.size[a] < self.size[b]:
            a, b = b, a

        self.parent[b] = a
        self.size[a] += self.size[b]

def solve():
    n, m, k = map(int, input().split())

    volunteers = list(map(int, input().split()))

    graph = [[] for _ in range(n + 1)]

    for _ in range(m):
        u, v = map(int, input().split())
        graph[u].append(v)
        graph[v].append(u)

    s, t = map(int, input().split())

    volunteer_id = {}
    for i, v in enumerate(volunteers):
        volunteer_id[v] = i

    # multi-source BFS from volunteers
    owner = [-1] * (n + 1)
    dist = [-1] * (n + 1)

    q = deque()

    for i, v in enumerate(volunteers):
        owner[v] = i
        dist[v] = 0
        q.append(v)

    edges = []

    while q:
        u = q.popleft()

        for v in graph[u]:
            if dist[v] == -1:
                dist[v] = dist[u] + 1
                owner[v] = owner[u]
                q.append(v)
            elif owner[v] != owner[u]:
                w = dist[u] + dist[v] + 1
                edges.append((w, owner[u], owner[v]))

    # BFS from target
    dt = [-1] * (n + 1)

    q = deque([t])
    dt[t] = 0

    while q:
        u = q.popleft()

        for v in graph[u]:
            if dt[v] == -1:
                dt[v] = dt[u] + 1
                q.append(v)

    start_id = volunteer_id[s]

    if dt[s] == -1:
        print(-1)
        return

    target_node = k

    volunteer_to_t = []

    for i, v in enumerate(volunteers):
        volunteer_to_t.append((dt[v], i))

    volunteer_to_t.sort()
    edges.sort()

    dsu = DSU(k + 1)

    ptr = 0
    ans = -1

    all_weights = []

    for w, _, _ in edges:
        all_weights.append(w)

    for w, _ in volunteer_to_t:
        all_weights.append(w)

    all_weights = sorted(set(all_weights))

    edge_ptr = 0

    for current in all_weights:
        while edge_ptr < len(edges) and edges[edge_ptr][0] <= current:
            _, a, b = edges[edge_ptr]
            dsu.union(a, b)
            edge_ptr += 1

        while ptr < len(volunteer_to_t) and volunteer_to_t[ptr][0] <= current:
            _, x = volunteer_to_t[ptr]
            dsu.union(x, target_node)
            ptr += 1

        if dsu.find(start_id) == dsu.find(target_node):
            ans = current
            break

    print(ans)

solve()
```最初の BFS はすべてのボランティアから同時に成長します。 各ノードは、どのボランティアが最初に到達したか、どのくらいの距離で到達したかを記録します。 2 つの領域がエッジを越えて接触すると、対応するボランティア間の最短経路接続が得られます。 

2 番目の BFS は、ターゲットまでの通常の最短距離を計算します。 これにより、各ボランティアがいつ PMP を直接ガイドできるかがわかります。`t`。 

DSU ステージは、Kruskal のアルゴリズムのように動作します。 しきい値が増加すると、より多くのボランティア ペアが通過可能になります。 開始ボランティアが特別なターゲット ノードに接続されると、現在のしきい値が答えとなります。 

微妙な点の 1 つは、到達不可能なターゲットの処理です。 もし`dt[s] == -1`、その場合、無制限の動きでも到達できなくなります`t`ので、すぐに印刷します`-1`。 

もう 1 つの重要な実装詳細は、`<= current`エッジとボランティアからターゲットへのリンクを処理しながら。 接続をチェックする前に、現在のしきい値以下の重みを持つすべての接続がすでにアクティブになっている必要があります。 

## 実用的な例

 ### サンプル 1

 入力:```
6 6 3
1 3 6
1 2
2 3
4 2
5 6
4 5
3 4
1 6
```マルチソース BFS の結果:

 | 頂点 | オーナーボランティア | 距離 |
 | --- | --- | --- |
 | 1 | 1 | 0 |
 | 2 | 1 | 1 |
 | 3 | 3 | 0 |
 | 4 | 3 | 1 |
 | 5 | 6 | 1 |
 | 6 | 6 | 0 |

 生成されたボランティア エッジ:

 | ボランティア | 重量 |
 | --- | --- |
 | 1 と 3 | 2 |
 | 3と6 | 3 |

 ターゲットまでの距離`6`:

 | ボランティア | 6までの距離 |
 | --- | --- |
 | 1 | 5 |
 | 3 | 3 |
 | 6 | 0 |

 DSU 処理:

 | 現在の q | アクティブなボランティアエッジ | t | につながるボランティア 届くかな |
 | --- | --- | --- | --- |
 | 0 | なし | 6 | いいえ |
 | 2 | 1-3 | 6 | いいえ |
 | 3 | 3-6 | 3,6 | はい |

 答え：```
3
```この例は、中級ボランティアが重要である理由を示しています。 PMP は長さ 5 のパス全体を記憶することはありません。`1`に`6`。 

### カスタム例

 入力:```
5 4 2
1 3
1 2
2 3
3 4
4 5
1 5
```ボランティア地域:

 | 頂点 | オーナー | 距離 |
 | --- | --- | --- |
 | 1 | 1 | 0 |
 | 2 | 1 | 1 |
 | 3 | 3 | 0 |
 | 4 | 3 | 1 |
 | 5 | 3 | 2 |

 ボランティアエッジ:

 | ボランティア | 重量 |
 | --- | --- |
 | 1 と 3 | 2 |

 ターゲットまでの距離`5`:

 | ボランティア | 距離 |
 | --- | --- |
 | 1 | 4 |
 | 3 | 2 |

 DSU の進化:

 | 現在の q | アクティブエッジ | ターゲット接続 | 到達可能 |
 | --- | --- | --- | --- |
 | 2 | 1-3 | 3 | はい |

 答え：```
2
```このトレースにより、最適なソリューションでは必要なメモリを削減するためにボランティア ハンドオフが使用される可能性があることが確認されています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + m) log (n + m)) | BFS は線形であり、ソート候補エッジが優勢です。 
| スペース | O(n + m) | グラフ、BFS アレイ、DSU、エッジ ストレージ |

 グラフのサイズは最大です`2 * 10^5`したがって、線形 BFS トラバーサルは簡単に十分に高速になります。 大まかに分類する`O(m)`candidate edges also comfortably fits within the limits.

 ## テストケース```python
# helper: run solution on input string, return output string
import sys, io
from collections import deque

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    class DSU:
        def __init__(self, n):
            self.parent = list(range(n))
            self.size = [1] * n

        def find(self, x):
            while self.parent[x] != x:
                self.parent[x] = self.parent[self.parent[x]]
                x = self.parent[x]
            return x

        def union(self, a, b):
            a = self.find(a)
            b = self.find(b)

            if a == b:
                return

            if self.size[a] < self.size[b]:
                a, b = b, a

            self.parent[b] = a
            self.size[a] += self.size[b]

    n, m, k = map(int, input().split())
    volunteers = list(map(int, input().split()))

    graph = [[] for _ in range(n + 1)]

    for _ in range(m):
        u, v = map(int, input().split())
        graph[u].append(v)
        graph[v].append(u)

    s, t = map(int, input().split())

    volunteer_id = {}
    for i, v in enumerate(volunteers):
        volunteer_id[v] = i

    owner = [-1] * (n + 1)
    dist = [-1] * (n + 1)

    q = deque()

    for i, v in enumerate(volunteers):
        owner[v] = i
        dist[v] = 0
        q.append(v)

    edges = []

    while q:
        u = q.popleft()

        for v in graph[u]:
            if dist[v] == -1:
                dist[v] = dist[u] + 1
                owner[v] = owner[u]
                q.append(v)
            elif owner[v] != owner[u]:
                edges.append((dist[u] + dist[v] + 1, owner[u], owner[v]))

    dt = [-1] * (n + 1)

    q = deque([t])
    dt[t] = 0

    while q:
        u = q.popleft()

        for v in graph[u]:
            if dt[v] == -1:
                dt[v] = dt[u] + 1
                q.append(v)

    if dt[s] == -1:
        return "-1"

    volunteer_to_t = []

    for i, v in enumerate(volunteers):
        volunteer_to_t.append((dt[v], i))

    volunteer_to_t.sort()
    edges.sort()

    dsu = DSU(k + 1)

    target = k
    start = volunteer_id[s]

    vals = []

    for w, _, _ in edges:
        vals.append(w)

    for w, _ in volunteer_to_t:
        vals.append(w)

    vals = sorted(set(vals))

    ep = 0
    tp = 0

    for cur in vals:
        while ep < len(edges) and edges[ep][0] <= cur:
            _, a, b = edges[ep]
            dsu.union(a, b)
            ep += 1

        while tp < len(volunteer_to_t) and volunteer_to_t[tp][0] <= cur:
            _, x = volunteer_to_t[tp]
            dsu.union(x, target)
            tp += 1

        if dsu.find(start) == dsu.find(target):
            return str(cur)

    return "-1"

# provided sample
assert run(
"""6 6 3
1 3 6
1 2
2 3
4 2
5 6
4 5
3 4
1 6
"""
) == "3", "sample"

# disconnected graph
assert run(
"""4 2 1
1
1 2
3 4
1 4
"""
) == "-1", "unreachable target"

# direct short path
assert run(
"""3 2 1
1
1 2
2 3
1 3
"""
) == "2", "single volunteer"

# intermediate volunteer reduces answer
assert run(
"""5 4 2
1 3
1 2
2 3
3 4
4 5
1 5
"""
) == "2", "handoff between volunteers"

# already adjacent to target
assert run(
"""2 1 1
1
1 2
1 2
"""
) == "1", "minimum nonzero answer"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 切断されたグラフ |`-1`| 到達不可能なターゲットの適切な処理 |
 | 単一のボランティアの道 |`2`| ボランティアからターゲットへの直接的な指導 |
 | 中級ボランティア事例 |`2`| 複数ステップのボランティアルーティング |
 | 2 ノード グラフ |`1`| 可能な最小の有効な答え |

 ## 特殊なケース

 切り離されたグラフを考えてみましょう。```
4 2 1
1
1 2
3 4
1 4
```からのBFS`t = 4`ノードに到達しない`1`、 それで`dt[1] = -1`。 

アルゴリズムはすぐに戻ります`-1`。 無制限の移動でもコンポーネントを接続できないため、DSU 処理は必要ありません。 

次に、ターゲットがボランティアではない場合を考えてみましょう。```
3 2 1
1
1 2
2 3
1 3
```唯一のボランティアがノードにいる`1`。 からのBFS`t = 3`距離を与える`2`ボランティア用`1`。 

ボランティア間のエッジは存在しませんが、アルゴリズムはボランティアとボランティアを正しく結び付けます。`1`ターゲットノードに直接接続する場合`q = 2`。 

最後に、重要なリレーのケースを考えてみましょう。```
5 4 2
1 3
1 2
2 3
3 4
4 5
1 5
```からの最短経路`1`に`5`長さがある`4`、しかしボランティア`3`中間チェックポイントとして機能します。 

DSU はまずボランティア間のエッジを活性化します。`1`そして`3`体重で`2`。 ボランティア`3`重量のあるターゲットにも直接接続します`2`。 

この瞬間、`s`につながるようになる`t`、したがって、答えは次のとおりです`2`。
