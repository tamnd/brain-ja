---
title: "CF 105139C - リリはポリゴンが好き"
description: "入力は、無限グリッド上の軸に揃えられた一連の長方形を記述します。 これらをすべて適用すると、少なくとも 1 つの長方形で覆われたすべてのグリッド セルが「裸」になります。"
date: "2026-06-27T18:46:22+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105139
codeforces_index: "C"
codeforces_contest_name: "The 2024 International Collegiate Programming Contest in Hubei Province, China"
rating: 0
weight: 105139
solve_time_s: 62
verified: true
draft: false
---

[CF 105139C - リリはポリゴンが好き](https://codeforces.com/problemset/problem/105139/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 1分2秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 入力は、無限グリッド上の軸に揃えられた一連の長方形を記述します。 これらをすべて適用すると、少なくとも 1 つの長方形で覆われたすべてのグリッド セルが「裸」になります。 結果として得られる形状は、直交領域を形成する単位正方形の結合です。エッジは水平または垂直であり、形状には穴や複数の切断された部分が含まれる場合があります。 

タスクは、このおそらく複雑な領域を、すべての裸の単位セルが正確に 1 つの選択された長方形に属するように、重ならない軸に沿った長方形へのパーティションに置き換えることです。 目標は、使用する長方形の数を最小限に抑えることです。 

出力を言い換える便利な方法は、バイナリ グリッド (結合後にセルが覆われているか覆われていない) を取得し、すべての 1 セルを最小数の完全な部分四角形に分割するように求められることです。ここで、各部分四角形は 1 セルのみで構成され、四角形は互いに素になっている必要があります。 

座標は大きいですが、結合境界の全体的な幾何学的複雑さは小さく、約 2000 の端点で囲まれています。 これは、座標圧縮後の結果のグリッドには数千個の個別の x 境界と y 境界しかないため、個別のセルの総数は管理可能であることを意味します。 圧縮されたグリッドに比例する構造を構築する解決策はどれも実現可能ですが、元の座標の大きさに依存する解決策は不可能です。 

素朴なアイデアは、各セルを独立して扱い、それらを貪欲に長方形にマージしようとすることです。 これは、ローカルな選択が全体的な最適性を妨げる単純な構成では失敗します。 たとえば、5 つの単位セルで構成されるプラス型の領域を考えてみましょう。構造に応じて最適な答えが明らかに 5 以下である場合でも、貪欲な水平方向または垂直方向の結合により、スキャン順序に応じて余分な長方形が強制的に作成される可能性があります。 重要な問題は、長方形は軸を揃えたままにする必要があり、重なり合うことができないため、長方形を一方向に拡張するかどうかの決定が、将来の多くの配置に影響することです。 

もう 1 つの微妙な失敗例は、リージョンが単一の接続された塊のように見えても、分割を強制する「細いブリッジ」がある場合に発生します。 接続されたセルをグループ化する単純なフラッドフィルは、接続性が無関係であるため役に立ちません。単一の長方形には、接続された形状だけでなく、完全なデカルト積を形成する場合にのみ、接続されていないように見える部分が含まれる可能性があります。 

## アプローチ

 直接的な総当たりアプローチでは、グリッドを長方形に分割するすべての方法を列挙しようとします。 各セルから始まる最大の長方形のみを考慮するように制限したとしても、各セルは多くの可能な高さと幅を持つ長方形を開始することができ、これらは組み合わせ的に相互作用するため、選択肢の数は爆発的に増加します。 N 個のセルを含むグリッドでは、これはすぐに指数関数的になります。 

重要な構造的観察は、長方形がグリッドの複数の隣接する垂直スライスにわたって持続する「一貫した水平ストリップ」に対応するということです。 X 座標を圧縮すると、グリッドを一連の垂直スラブとして表示できます。 各スラブの内部では、占有されているセルが連続した垂直間隔に分割されます。 このような各間隔は、構成要素の候補となります。 

ここで重要な単純化は、すべての有効な長方形が、スラブ内のそのような垂直間隔を 1 つ取得し、それをまったく同じ間隔が変化せずに持続する複数の連続するスラブにわたって拡張することに対応するということです。 これにより、問題は隣接する列にわたる同一間隔のセグメントの接続に変換されます。

これを階層化グラフとしてモデル化できます。 各ノードは、特定の X スラブ内の垂直間隔のセグメントです。 2 つのノードがまったく同じ y 間隔を表す場合、連続するスラブで 2 つのノードを接続します。 この場合、長方形は、y 間隔を変更せずに左から右に移動する、まさにこのグラフ内のパスになります。 すべてのユニットセルは一度カバーされる必要があるため、すべてのノードはそのようなパスの 1 つだけに属している必要があります。 

これは、有向非巡回グラフにおける古典的な最小パス カバー問題になります。 エッジはスラブ i から i+1 にのみ進むため、グラフは隣接するレイヤー間で 2 部構成となり、最小パス カバーはノード数から互換性のある間隔ノード間の最大一致サイズを引いたものに等しくなります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォースパーティショニング | 指数 | 高 | 遅すぎる |
 | 間隔グラフ + 最大一致 | O(M √M) | お(お) | 承認済み |

 ここで、M は圧縮後の間隔セグメントの数であり、合計境界サイズによって制限されます。 

## アルゴリズムのチュートリアル

 1. 圧縮されたグリッド上の包括的なカバレッジを正しく表現するために、両方の端点と「右 + 1」境界および「上 + 1」境界を含む、長方形の境界として表示されるすべての x 座標と y 座標を収集します。 このステップにより、すべての単位セルが圧縮表現でクリーンなセルになります。 
2. 元のすべてのセルが小さなグリッド内のインデックスのペアにマップされるように、座標を並べ替えて圧縮します。 圧縮後、元の各四角形は、この縮小されたグリッド内の塗りつぶされたセルのブロックになります。 
3. 各圧縮セルが少なくとも 1 つの入力四角形で覆われているかどうかをマークするバイナリ グリッドを構築します。 これは、差分グリッド内の各四角形をマークするか、圧縮された範囲を直接反復することによって行われます。 
4. 連続する圧縮された x 座標間の各 x スラブについて、y に沿って垂直にスキャンし、スラブを塗りつぶされたセルの最大の連続セグメントに分割します。 このような各セグメントが、「縦縞」の候補を表すノードとなる。 
5. 各ノードに、スラブ インデックスと y 間隔で構成されるラベルを割り当てます。 ノードはスラブ インデックスによって自然にグループ化され、レイヤーを形成します。 
6. 2 つのノードの y 間隔がまったく同じである場合は常に、スラブ i とスラブ i+1 のノード間にエッジを構築します。 これは、垂直方向の範囲を変更せずに長方形を水平方向に拡張できることを表します。 
7. これらのエッジで最大二部マッチングを実行し、偶数スラブのノードを一方の側として扱い、奇​​数スラブをもう一方の側として扱います。 一致した各エッジは、2 つのノードを同じ四角形のパスにマージします。 
8. ノードの数から最大一致のサイズを引いたものとして最終的な答えを計算します。 一致しない各ノードは新しい四角形のパスを開始し、一致した各エッジは連続性をマージすることでパスの数を減らします。 

### なぜ効果があるのか

 各ノードは、塗りつぶされた領域を離れることなく垂直に拡張できない最大の垂直セグメントに対応します。 すべての長方形は、各スラブ内のこれらの最大垂直境界を尊重する必要があるため、同一のセグメント内に留まることによってのみ水平方向に移動できます。 したがって、長方形はスラブ間の同一セグメントを通るパスに正確に対応します。 すべてのノードをカバーするために必要なこのようなパスの最小数は、正確に最小数の四角形であり、標準のパス カバー削減により、最大のマッチングによって正確さが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from collections import defaultdict, deque

def hopcroft_karp(adj, n_left, n_right):
    INF = 10**18
    pair_u = [-1] * n_left
    pair_v = [-1] * n_right
    dist = [0] * n_left

    def bfs():
        q = deque()
        for u in range(n_left):
            if pair_u[u] == -1:
                dist[u] = 0
                q.append(u)
            else:
                dist[u] = INF

        found = False

        while q:
            u = q.popleft()
            for v in adj[u]:
                pu = pair_v[v]
                if pu != -1 and dist[pu] == INF:
                    dist[pu] = dist[u] + 1
                    q.append(pu)
                elif pu == -1:
                    found = True

        return found

    def dfs(u):
        for v in adj[u]:
            pu = pair_v[v]
            if pu == -1 or (dist[pu] == dist[u] + 1 and dfs(pu)):
                pair_u[u] = v
                pair_v[v] = u
                return True
        dist[u] = float('inf')
        return False

    match = 0
    while bfs():
        for u in range(n_left):
            if pair_u[u] == -1:
                if dfs(u):
                    match += 1
    return match

n = int(input())
rects = []
xs, ys = set(), set()

for _ in range(n):
    l, b, r, t = map(int, input().split())
    rects.append((l, b, r, t))
    xs.add(l); xs.add(r + 1)
    ys.add(b); ys.add(t + 1)

xs = sorted(xs)
ys = sorted(ys)

x_id = {x:i for i, x in enumerate(xs)}
y_id = {y:i for i, y in enumerate(ys)}

H = len(ys)
W = len(xs)

grid = [[0] * (H - 1) for _ in range(W - 1)]

for l, b, r, t in rects:
    xl = x_id[l]
    xr = x_id[r + 1]
    yb = y_id[b]
    yt = y_id[t + 1]
    for i in range(xl, xr):
        for j in range(yb, yt):
            grid[i][j] = 1

nodes = []
node_id = {}
slab_nodes = [[] for _ in range(W - 1)]

for i in range(W - 1):
    j = 0
    while j < H - 1:
        if grid[i][j] == 0:
            j += 1
            continue
        start = j
        while j < H - 1 and grid[i][j] == 1:
            j += 1
        nodes.append((i, start, j - 1))
        node_id[(i, start, j - 1)] = len(nodes) - 1
        slab_nodes[i].append(len(nodes) - 1)

adj = defaultdict(list)

for i in range(W - 2):
    for u in slab_nodes[i]:
        x, y1, y2 = nodes[u]
        for v in slab_nodes[i + 1]:
            x2, z1, z2 = nodes[v]
            if y1 == z1 and y2 == z2:
                adj[u].append(v)

# bipartite: split by slab parity
left = [i for i, (x, _, _) in enumerate(nodes) if x % 2 == 0]
right = [i for i, (x, _, _) in enumerate(nodes) if x % 2 == 1]

right_index = {v:i for i, v in enumerate(right)}

adj_bip = [[] for _ in range(len(left))]

for i, u in enumerate(left):
    for v in adj[u]:
        if v in right_index:
            adj_bip[i].append(right_index[v])

match = hopcroft_karp(adj_bip, len(left), len(right))

print(len(nodes) - match)
```この実装では、まず座標が圧縮されるため、ジオメトリは有限グリッドになります。 次に、各 X スラブ内に垂直間隔ノードを構築します。 隣接関係の構築は意図的に厳密です。不一致があると四角形の有効性が損なわれるため、同一の y 間隔のみが接続されます。 

マッチングは、スラブ パリティによって分割された 2 部に適用されます。これは、エッジが隣接するスラブのみを接続するため有効であり、部内競合がないことが保証されます。 

最後に、ノードの数から最大一致サイズを減算すると、最小数の四角形が生成されます。 

## 実用的な例

 ### 例 1 (十字を形成する単一単位ブロック)

 | ステップ | 作成されたノード | 一致するエッジ | 現在の答え |
 | --- | --- | --- | --- |
 | 圧縮後 | 各セルは独自のノードです。 なし | 8 |

 隣接する 2 つのスラブが同じ垂直間隔を共有しないため、すべてのノードは分離されます。 アルゴリズムでは一致が生成されないため、すべてのノードが独自の長方形になり、孤立した各セルを個別にカバーするという直感的なニーズに一致します。 

これにより、切断されたユニット コンポーネントをより大きな長方形にマージできないことが確認されます。 

### 例 2 (分離された 2 つの大きな長方形)

 | ステップ | 作成されたノード | 一致するエッジ | 現在の答え |
 | --- | --- | --- | --- |
 | 圧縮後 | 2 つの長い間隔のチェーン | 各長方形内のチェーンエッジ | 2 |

 各長方形は、スラブにわたる同一の垂直セグメントの連続チェーンを形成します。 チェーン内のすべてのノードがその隣接ノードと一致し、各チェーンが単一のパスに折りたたまれます。 パスの数は、独立した長方形の数と同じです。 

これは、長い安定領域が単一の長方形に最適に折りたたまれることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(M √M) | M ノードの間隔隣接グラフにおける Hopcroft-Karp |
 | スペース | お(お) | グリッド セル、ノード、および隣接関係のストレージ |

 ノードの総数 M は圧縮された境界サイズによって制限され、最大でも数千であるため、制約の下でグリッドの構築とマッチングの両方が十分に高速になります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    from collections import defaultdict, deque

    def hopcroft_karp(adj, n_left, n_right):
        INF = 10**18
        pair_u = [-1] * n_left
        pair_v = [-1] * n_right
        dist = [0] * n_left

        def bfs():
            q = deque()
            for u in range(n_left):
                if pair_u[u] == -1:
                    dist[u] = 0
                    q.append(u)
                else:
                    dist[u] = INF
            found = False
            while q:
                u = q.popleft()
                for v in adj[u]:
                    pu = pair_v[v]
                    if pu != -1 and dist[pu] == INF:
                        dist[pu] = dist[u] + 1
                        q.append(pu)
                    elif pu == -1:
                        found = True
            return found

        def dfs(u):
            for v in adj[u]:
                pu = pair_v[v]
                if pu == -1 or (dist[pu] == dist[u] + 1 and dfs(pu)):
                    pair_u[u] = v
                    pair_v[v] = u
                    return True
            dist[u] = float('inf')
            return False

        match = 0
        while bfs():
            for u in range(n_left):
                if pair_u[u] == -1:
                    if dfs(u):
                        match += 1
        return match

    n = int(input())
    rects = []
    xs, ys = set(), set()

    for _ in range(n):
        l, b, r, t = map(int, input().split())
        rects.append((l, b, r, t))
        xs.add(l); xs.add(r + 1)
        ys.add(b); ys.add(t + 1)

    xs = sorted(xs)
    ys = sorted(ys)

    x_id = {x:i for i, x in enumerate(xs)}
    y_id = {y:i for i, y in enumerate(ys)}

    W, H = len(xs), len(ys)

    grid = [[0] * (H - 1) for _ in range(W - 1)]

    for l, b, r, t in rects:
        xl, xr = x_id[l], x_id[r + 1]
        yb, yt = y_id[b], y_id[t + 1]
        for i in range(xl, xr):
            for j in range(yb, yt):
                grid[i][j] = 1

    nodes = []
    slab_nodes = [[] for _ in range(W - 1)]

    for i in range(W - 1):
        j = 0
        while j < H - 1:
            if grid[i][j] == 0:
                j += 1
                continue
            s = j
            while j < H - 1 and grid[i][j]:
                j += 1
            nodes.append((i, s, j - 1))
            slab_nodes[i].append(len(nodes) - 1)

    adj = defaultdict(list)
    for i in range(W - 2):
        for u in slab_nodes[i]:
            x, y1, y2 = nodes[u]
            for v in slab_nodes[i + 1]:
                x
```
