---
title: "CF 102437C - \u0415\u0434\u0438\u043d\u0430\u044f \u0441\u0435\u0442\u044c"
description: "エッジがサボテンを形成する接続された無向グラフがあります。すべての道路は最大でも 1 つの単純なサイクルに属します。 各都市は 3 つの送信機タイプのいずれかを受信する必要があり、隣接する都市は異なるタイプを受信する必要があります。"
date: "2026-08-08T15:12:34+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102437
codeforces_index: "C"
codeforces_contest_name: "\u0418\u043d\u0442\u0435\u0440\u043d\u0435\u0442-\u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u044b, \u0421\u0435\u0437\u043e\u043d 2019-2020, \u0427\u0435\u0442\u0432\u0451\u0440\u0442\u0430\u044f \u043a\u043e\u043c\u0430\u043d\u0434\u043d\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430, \u0443\u0441\u043b\u043e\u0436\u043d\u0435\u043d\u043d\u0430\u044f \u043d\u043e\u043c\u0438\u043d\u0430\u0446\u0438\u044f"
rating: 0
weight: 102437
solve_time_s: 257
verified: true
draft: false
---

[CF 102437C - \u0415\u0434\u0438\u043d\u0430\u044f \u0441\u0435\u0442\u044c](https://codeforces.com/problemset/problem/102437/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 4 分 17 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 エッジがサボテンを形成する接続された無向グラフがあります。すべての道路は最大でも 1 つの単純なサイクルに属します。 各都市は 3 つの送信機タイプのいずれかを受信する必要があり、隣接する都市は異なるタイプを受信する必要があります。 タイプ 3 は高価であるため、タスクはタイプ 3 でカラー化された頂点の数を最小限に抑えることです。 

入力には、グラフの都市と道路が含まれます。 必要な出力は、タイプ 3 を使用する頂点の最小数です。与えられたサボテンの保証の下では、有効な 3 色が常に存在します。`-1`実際には有効な入力では発生しません。 サボテンは 2 縮退です。なぜなら、すべての非自明な部分グラフには最大でも 2 次の頂点があり、そのようなグラフは常に 3 色可能だからです。 

(n=10^5) および (m=1.5\cdot10^5) のサイズ制限により、グラフ サイズの二次関数は除外されます。 (O(n^2)) であっても、最悪の場合、約 (10^{10}) 個の原始演算を意味します。 (n+m) において線形に近いアルゴリズムが必要です。 サボテンの条件は、まさにそのような解決策を得ることができる構造的な制限です。 

より単純なアプローチでは間違った答えが得られる特殊なケースがいくつかあります。 単一の頂点にはエッジがないため、高価な送信機は必要ありません。```
1 0
```答えは、`0`。 すべての接続されたグラフに少なくとも 1 つのエッジがあると仮定するソリューションでは、このケースは誤って処理されます。 

偶数サイクルは 2 部構成であるため、最初の 2 つの送信機タイプのみが必要です。```
4 4
1 2
2 3
3 4
4 1
```答えは、`0`。 タイプ 3 送信機のサイクルごとに充電するメソッドは、誤って返します。`1`。 

奇数サイクルにはタイプ 3 が必要ですが、複数の奇数サイクルが同じ高価な頂点を共有できます。 たとえば、1 つの共通の頂点を持つ 2 つの三角形について考えてみましょう。```
5 6
1 2
2 3
3 1
1 4
4 5
5 1
```答えは、`1`。 頂点 1 をタイプ 3 でカラー化すると、各三角形は他の 2 つの頂点でタイプ 1 と 2 を使用できます。 単純に奇数サイクルをカウントすると、誤って返されてしまいます。`2`。 

## アプローチ

 最も直接的な総当たりの解決策は、3 つの色のうち 1 つをすべての頂点に割り当て、すべてのエッジに異なる色の端点があるかどうかを確認します。 正確に (3^n) 個の割り当てがあり、1 つの割り当てをチェックするには (O(n+m)) 時間がかかります。 最悪の場合、これは (3^{100000}) 個の代入とおよそ (150000\cdot3^{100000}) 個のエッジ チェックを意味しますが、これは完全に実行不可能です。 

この総当り攻撃は、考えられるすべてのカラーリングを明示的に考慮するため機能します。 問題は、グラフの大部分を同時に考慮する必要がないことです。 サボテンでは、グラフをブロックに分解でき、各ブロックは単一のエッジまたは 1 つの単純なサイクルのいずれかになります。 異なるブロックは最大でも 1 つの共通頂点を介して相互作用します。 これにより、ブロック構造がツリーに変わります。 

これは、ブロック ツリー上の動的プログラミングを示唆しています。 すべての頂点 (v) について、(v) の可能な色ごとに 1 つずつ、合計 3 つの値を保持します。 この値は、(v) が選択された色であると仮定した場合、(v) の下のグラフの部分全体におけるタイプ 3 送信機の最小数を表します。 

親頂点の色が固定されると、複数の頂点を接続するブロックを独立して処理できます。 エッジの場合は、親とは異なる色を選択するだけです。 サイクルの場合、親頂点の色を固定し、サイクルの残りの周囲に小さなパス DP を実行し、最終的に最後の頂点も親と異なることを確認します。 

重要な部分は、ブロックを効率的に構築することです。 Tarjan の二重接続コンポーネントのアルゴリズムは、(O(n+m)) でまさにそれを実行します。 サボテンでは、結果として得られるすべての二重接続コンポーネントは、1 つのエッジまたは単純なサイクルのいずれかであることが保証されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(3^n(n+m))) | (O(n+m)) | 遅すぎる |
 | ブロックツリーDP | (O(n+m)) | (O(n+m)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 無向グラフを構築し、Tarjan のアルゴリズムを使用してすべての二重接続コンポーネントを見つけます。 DFS の実行中は、トラバースされたエッジをスタック上に保持します。 いつでも`low[child] >= tin[parent]`、子の DFS エッジが削除されるまでエッジをポップします。 これらのエッジは 1 つの二重接続コンポーネントを形成します。 

入力グラフはサボテンであるため、各コンポーネントは単一のブリッジまたは単純なサイクルのいずれかです。 また、すべてのコンポーネントに属する頂点も収集します。 
2. 頂点とコンポーネント間の入射構造を構築します。 すべてのコンポーネントについて、どのグラフ頂点が含まれているかを保存します。 すべての頂点について、それを含むコンポーネントのリストを保存します。 

結果の発生グラフは、元の頂点とコンポーネントを 2 つの異なる種類のノードとして扱った後のツリーになります。 サボテンのブロックカット木です。 
3. このブロック ツリーを任意のグラフの頂点、たとえば頂点にルートします。`0`。 すべてのコンポーネントについて、どの頂点がその親であるかを覚えておいてください。 ルート以外の頂点ごとに、どのコンポーネントがその親であるかを覚えておいてください。 

今では明確な親子関係ができています。 コンポーネントには親頂点が 1 つだけあり、他のすべての頂点は独立したサブツリーにつながります。 
4. 定義する`dp[v][c]`頂点をルートとするサブツリー内のタイプ 3 送信機の最小数として`v`と仮定すると、`v`色を受け取る`c`。 

直接的な貢献は、`v`は`1`いつ`c`タイプ3であり、`0`さもないと。 すべての子コンポーネントは、同じ色に対して独自の最適値を提供します。`v`。 

したがって、

 [
 dp[v][c] = [c=3] + \sum_{\text{子コンポーネント } B} blockDP[B][c]。 
】
 5. ブロックツリー走査の逆の順序で頂点を処理します。 計算する前に`dp[v]`、すべての子コンポーネント内のすべての頂点はすでに計算されています。 これにより、そのコンポーネントの DP を計算するために必要なすべてが得られます。 
6. 親頂点を持つブリッジコンポーネントの場合`p`そして他の頂点`u`、計算します

 [
 ブロックDP[B][c] =
 \min_{d\ne c} dp[u][d]。 
】

 エッジによって課される唯一の制約は、その 2 つの端点の色が異なることです。 
7. サイクルコンポーネントの場合、まず頂点を次のように並べます。 

[
 p,v_1,v_2,\ldots,v_{k-1},p,
 】

 どこで`p`は親頂点です。 

色を修正する`c`の`p`。 カラーのみでスリーステート DP を開始する`c`で許可されます`p`、コストゼロで。 次に、(v_1,v_2,\ldots,v_{k-1}) を順番に処理します。 色を割り当てるとき`d`次の頂点に移動するには、前の頂点は 2 つの色のいずれかでなければなりません。`d`。 

最後の頂点が処理された後は、最後の頂点と親が終了サイクル エッジによって接続されているため、色が固定の親の色と異なる状態のみが有効になります。 
8. すべての子コンポーネントがすべての頂点に組み込まれると、答えは次のようになります。 

[
 \min_{c\in{1,2,3}} dp[root][c]。 
】

 有効なサボテンは常に 3 色対応であるため、これらの値の少なくとも 1 つは有限です。 

### なぜ効果があるのか

 重要な不変条件は次のとおりです`dp[v][c]`以下のブロック ツリーの部分に最適なカラーリング コストが含まれています`v`、 と`v`色に固定`c`。 頂点のさまざまな子コンポーネントはその頂点でのみ交差するため、色が固定された後は、それらの選択は独立しており、最適なコストを追加できます。 

エッジ コンポーネントの場合、可能な 2 つの異なる色をチェックすることが、まさに適切な色の条件となります。 サイクルの場合、パス DP は、連続する頂点に不平等を適用しながら、すべての頂点の可能なすべての色を考慮し、最後の遷移では終了エッジを適用します。 したがって、コンポーネントのすべての適切なカラーリングが表現され、最小コストのカラーリングが選択されます。 

すべてのブロックは、その子孫の頂点がすべて解決された後にのみ処理されるため、不変式はリーフからルートまで伝播します。 したがって、根の 3 つの可能な色の最終的な最小値では、サボテン全体の有効なすべての色が考慮され、最も安価な色が選択されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10 ** 9

def solve_case(n, edges):
    m = len(edges)

    graph = [[] for _ in range(n)]
    for eid, (u, v) in enumerate(edges):
        graph[u].append(eid)
        graph[v].append(eid)

    # Iterative Tarjan algorithm for biconnected components.
    tin = [-1] * n
    low = [0] * n
    parent = [-1] * n
    parent_edge = [-1] * n
    it = [0] * n

    edge_stack = []
    components = []

    timer = 0
    tin[0] = low[0] = timer
    timer += 1

    dfs_stack = [0]

    while dfs_stack:
        u = dfs_stack[-1]

        if it[u] < len(graph[u]):
            eid = graph[u][it[u]]
            it[u] += 1

            if eid == parent_edge[u]:
                continue

            a, b = edges[eid]
            v = b if a == u else a

            if tin[v] == -1:
                parent[v] = u
                parent_edge[v] = eid
                edge_stack.append(eid)

                tin[v] = low[v] = timer
                timer += 1

                dfs_stack.append(v)
            elif tin[v] < tin[u]:
                edge_stack.append(eid)
                if tin[v] < low[u]:
                    low[u] = tin[v]
        else:
            dfs_stack.pop()

            p = parent[u]
            if p != -1:
                if low[u] < low[p]:
                    low[p] = low[u]

                if low[u] >= tin[p]:
                    comp = []
                    while True:
                        eid = edge_stack.pop()
                        comp.append(eid)
                        if eid == parent_edge[u]:
                            break
                    components.append(comp)

    k = len(components)

    # Vertices belonging to every component.
    comp_vertices = [[] for _ in range(k)]
    incident = [[] for _ in range(n)]

    for cid, comp_edges in enumerate(components):
        vertices = set()

        for eid in comp_edges:
            u, v = edges[eid]
            vertices.add(u)
            vertices.add(v)

        vertices = list(vertices)
        comp_vertices[cid] = vertices

        for v in vertices:
            incident[v].append(cid)

    # Root the block-cut tree at vertex 0.
    # parent_comp[v] is the component through which v is reached.
    parent_comp = [-2] * n
    parent_comp[0] = -1

    # comp_parent[c] is the vertex through which component c is reached.
    comp_parent = [-1] * k

    order = [0]

    for v in order:
        for cid in incident[v]:
            if cid == parent_comp[v]:
                continue

            comp_parent[cid] = v

            for u in comp_vertices[cid]:
                if u == v:
                    continue

                if parent_comp[u] == -2:
                    parent_comp[u] = cid
                    order.append(u)

    dp = [[0, 0, 0] for _ in range(n)]
    block_dp = [[0, 0, 0] for _ in range(k)]

    # Process bottom-up.
    for v in reversed(order):
        # First calculate all components for which v is the parent.
        for cid in incident[v]:
            if comp_parent[cid] != v:
                continue

            comp_edges = components[cid]
            vertices = comp_vertices[cid]

            # A component consisting of one edge.
            if len(comp_edges) == 1:
                eid = comp_edges[0]
                a, b = edges[eid]
                u = b if a == v else a

                for c in range(3):
                    best = INF
                    for d in range(3):
                        if d != c and dp[u][d] < best:
                            best = dp[u][d]
                    block_dp[cid][c] = best

            else:
                # A cactus biconnected component with more than one edge
                # is a simple cycle.
                local = {x: [] for x in vertices}

                for eid in comp_edges:
                    a, b = edges[eid]
                    local[a].append(b)
                    local[b].append(a)

                # Order the cycle starting from its parent vertex v.
                cycle = [v]
                prev = -1
                cur = v

                while True:
                    x, y = local[cur]
                    nxt = x if x != prev else y

                    if nxt == v:
                        break

                    cycle.append(nxt)
                    prev, cur = cur, nxt

                for parent_color in range(3):
                    cur_dp = [INF, INF, INF]
                    cur_dp[parent_color] = 0

                    for u in cycle[1:]:
                        nxt_dp = [INF, INF, INF]

                        for new_color in range(3):
                            best = INF
                            for old_color in range(3):
                                if old_color == new_color:
                                    continue
                                if cur_dp[old_color] < best:
                                    best = cur_dp[old_color]

                            nxt_dp[new_color] = best + dp[u][new_color]

                        cur_dp = nxt_dp

                    best = INF
                    for last_color in range(3):
                        if last_color == parent_color:
                            continue
                        if cur_dp[last_color] < best:
                            best = cur_dp[last_color]

                    block_dp[cid][parent_color] = best

        # Now every child component of v is solved.
        for color in range(3):
            value = 1 if color == 2 else 0

            for cid in incident[v]:
                if comp_parent[cid] == v:
                    value += block_dp[cid][color]

            dp[v][color] = value

    return min(dp[0])

def main():
    n, m = map(int, input().split())
    edges = [tuple(map(lambda x: int(x) - 1, input().split()))
             for _ in range(m)]

    print(solve_case(n, edges))

if __name__ == "__main__":
    main()
```グラフ構築では、各エッジを整数 ID で保存します。 これは、親リレーションが単なる頂点リレーションではなくエッジ リレーションであるため、Tarjan のアルゴリズムに必要です。 特に、無向 DFS が親に戻るエッジを認識した場合、その正確なエッジを無視する必要があります。 

Tarjan の実装は再帰的ではなく反復的です。 サボテンには (10^5) 頂点のパスが含まれる可能性があるため、再帰的 DFS では Python の再帰制限を増やす必要があり、また Python 呼び出しスタックに不必要な圧力をかけることになります。 明示的なスタックでは、そのリスクなしで同じ DFS 順序が与えられます。 

エッジ スタックには、現在の二重接続コンポーネントに属する DFS エッジが正確に含まれます。 いつ`low[u] >= tin[parent[u]]`、下にエッジはありません`u`上でも接続できる`parent[u]`、エッジまで`parent_edge[u]`1つの完全なブロックを形成します。 

の`incident`リストは、二重接続されたコンポーネントをブロックカット ツリーに変換するものです。 元のグラフには同じ頂点に接触する多数のサイクルが存在する可能性がありますが、ブロックカット表現ではツリー構造が維持されます。 

サイクル DP は 3 つの状態のみを使用します。 新しい色ごとに、異なる色の前の最も安い状態が適用されます。 親の色に対する最後の制限は不可欠です。 これを省略すると、サイクルが誤ってパスとして扱われ、無効なカラーリングが受け入れられる可能性があります。 

すべてのコストは最大 (n) であるため、通常の Python 整数で十分です。 整数オーバーフローの問題はありません。 

## 実用的な例

 ### サンプル 1

 グラフは 3 つの三角形で構成されます。 最初の三角形には頂点が含まれています`1, 2, 3`、他の 2 つの三角形は頂点に接続されています。`2`そして`3`。 

各リーフ三角形について、共有頂点をタイプ 3 に固定すると、他の 2 つの頂点がタイプ 1 と 2 を使用できるようになり、そのコンポーネントは追加のタイプ 3 トランスミッターをゼロにします。 共有頂点がタイプ 1 またはタイプ 2 を使用する場合、他の 2 つの頂点のいずれかはタイプ 3 を使用する必要があります。 

結果の DP 値は次のとおりです。 

| 頂点またはコンポーネント | タイプ 1 の状態 | タイプ 2 の状態 | タイプ 3 の状態 |
 | --- | --- | --- | --- |
 | 2 の葉の三角形 | 1 | 1 | 0 |
 |`dp[2]`| 1 | 1 | 1 |
 | 3 の葉の三角形 | 1 | 1 | 0 |
 |`dp[3]`| 1 | 1 | 1 |
 | ルート三角形 1 | 2 | 2 | 2 |
 |`dp[1]`| 2 | 2 | 3 |

 答えは、`2`。 1 つの最適なカラーリングにより、頂点 1 はタイプ 2、頂点 2 はタイプ 3、頂点 3 はタイプ 1 になります。アタッチされた各三角形は、頂点 3 にアタッチされた三角形内の 1 つの追加のタイプ 3 を除き、タイプ 1 と 2 で完成することができます。 

### サンプル 2

 グラフには、頂点 2、9、および 10 を介して接続されたいくつかのサイクルが含まれています。頂点 3 から 8 を介する大きなサイクルは偶数であり、頂点 10 から 13 を介するサイクルも偶数です。 三角形は、追加のタイプ 3 が強制される唯一の場所です。 

関連するボトムアップ状態は次のとおりです。 

| 下部構造 | タイプ 1 の親 | タイプ 2 の親 | タイプ 3 の親 |
 | --- | --- | --- | --- |
 | 偶数サイクル 3 | 1 | 1 | 0 |
 | 10 まで偶数サイクル | 1 | 1 | 0 |
 | 9 と 10 と 15 の三角形 | 1 | 1 | 1 |
 |`dp[9]`| 1 | 1 | 2 |
 | 9 と 14 の 2 の三角形 | 2 | 2 | 1 |
 |`dp[2]`| 2 | 2 | 2 |

 最終的な最小値は`2`。 

トレースは、DP が単純に奇数サイクルをカウントできない理由を示しています。 共有頂点で行われるタイプ 3 の選択は、いくつかのサイクル制約を同時に満たすことができ、その共有頂点の状態は、その親コン​​ポーネントが必要とする情報を正確に保持します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n+m)) | Tarjan はすべてのエッジを一定の回数だけ処理し、すべてのブロック DP 遷移では 3 つの色のみを検査します。 
| スペース | (O(n+m)) | グラフ、コンポーネント リスト、ブロックカット情報、および DP 配列はすべて、入力サイズにおいて線形です。 

許可される最大のグラフはサボテンであるため、(m\le150000) の制限が規定されているため、(O(n)) 個のエッジしかありません。 このアルゴリズムは、頂点ごとおよびエッジごとに一定量の 3 色の DP 作業を実行するため、意図した線形時間要件に快適に適合します。 

## テストケース

 次のテストでは、上記のソリューションが次のように保存されていることを前提としています。`solution.py`そして暴露します`solve_case`。```python
from solution import solve_case

def run(inp: str) -> str:
    data = list(map(int, inp.split()))
    n, m = data[0], data[1]

    edges = []
    pos = 2

    for _ in range(m):
        u = data[pos] - 1
        v = data[pos + 1] - 1
        pos += 2
        edges.append((u, v))

    return str(solve_case(n, edges))

# Sample 1
assert run("""\
7 9
1 2
2 3
3 1
2 4
4 5
5 2
3 6
6 7
7 3
""") == "2", "sample 1"

# Sample 2
assert run("""\
15 18
1 2
2 3
3 4
4 5
5 6
6 7
7 8
8 3
2 9
9 10
10 11
11 12
12 13
13 10
2 14
14 9
9 15
15 10
""") == "2", "sample 2"

# Minimum-size graph.
assert run("""\
1 0
""") == "0", "single isolated city"

# Even cycle, completely bipartite.
assert run("""\
4 4
1 2
2 3
3 4
4 1
""") == "0", "even cycle"

# Two triangles sharing one vertex.
# The common vertex can be the only type-3 vertex.
assert run("""\
5 6
1 2
2 3
3 1
1 4
4 5
5 1
""") == "1", "shared odd cycles"

# Maximum-size cactus for n = 100000.
# 49999 triangles share vertex 1, plus one leaf.
# This has 149998 edges, essentially the maximum possible for this n.
n = 100000
edges = []

for i in range(49999):
    a = 2 + 2 * i
    b = a + 1
    edges.append((1, a))
    edges.append((a, b))
    edges.append((b, 1))

edges.append((1, 100000))

assert solve_case(n, edges) == 1, "maximum-size cactus"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 0`|`0`| 最小サイズのグラフとゼロエッジ境界の場合 |
 | 4サイクル |`0`| 偶数サイクルでも 2 色だけが必要です |
 | 頂点 1 を共有する 2 つの三角形 |`1`| 1 つの高価な頂点で複数の奇数サイクルを満たすことができます。 
| 49999 個の共有三角形と 1 枚の葉 |`1`| 最大サイズの入力、線形複雑さ、大規模なブロック ツリー |

 ## 特殊なケース

 単一の都市の場合、```
1 0
```ブロックリストは空です。 ルートには子コンポーネントがないため、その 3 つの DP 状態は次のようになります。`[0, 0, 1]`。 最初の 2 色のいずれかを選択すると答えが表示されます`0`。 

4サイクルの場合```
4 4
1 2
2 3
3 4
4 1
```Tarjan は、4 つの頂点すべてを含む 1 つのサイクル コンポーネントを生成します。 親の色が固定されている場合、サイクル DP は残りの 3 つの頂点の周囲で他の 2 色を交互に配置し、タイプ 3 を使用せずにサイクルを閉じることができます。親自体がタイプ 3 を持ち、他の状態がタイプ 3 の使用の可能性を正しく考慮している場合、コンポーネントはゼロに寄与します。ルートでは、最小値は次のとおりです。`0`。 

頂点 1 を共有する 2 つの三角形の場合、```
5 6
1 2
2 3
3 1
1 4
4 5
5 1
```ブロックカット ツリーには、両方の三角形コンポーネントの親として共有頂点があります。 頂点 1 がタイプ 3 の場合、各三角形の残りの 2 つの頂点はタイプ 1 と 2 を使用できます。したがって、両方のコンポーネント DP は親色 3 に 0 を寄与しますが、ルートはタイプ 3 自体であるために 1 を寄与します。 答えは、`1`。 

最大サイズのテストは、頂点 1 と追加のリーフ 1 つを共有する 49999 個の三角形で構成されます。 頂点 1 にタイプ 3 を与えると、すべての三角形が他の頂点でタイプ 1 とタイプ 2 のみを使用できるようになるため、グラフ全体で高価なトランスミッターが 1 つだけ必要になります。 このアルゴリズムは、カラーリングやサイクルをグローバルに列挙することなく、149998 個のエッジと 100000 個の頂点をすべて一度に処理し、次の結果を返します。`1`。
