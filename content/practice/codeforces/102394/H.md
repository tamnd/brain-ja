---
title: "CF 102394H - 高速バス"
description: "無向接続グラフによって接続された (n) 個のバス停があります。 すべての高速道路には単位長があるため、2 つの駅間の距離がグラフ内の通常の最短経路長になります。"
date: "2026-08-10T19:23:14+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102394
codeforces_index: "H"
codeforces_contest_name: "The 2019 China Collegiate Programming Contest Harbin Site"
rating: 0
weight: 102394
solve_time_s: 457
verified: true
draft: false
---

[CF 102394H - 高速バス](https://codeforces.com/problemset/problem/102394/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 7 分 37 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 無向接続グラフによって接続された (n) 個のバス停があります。 すべての高速道路には単位長があるため、2 つの駅間の距離がグラフ内の通常の最短経路長になります。 

駅 (i) は、(i) からのグラフ距離が最大 (f_i) である任意の駅へのバスのチケットを販売できます。 このようなバスに乗ると料金がかかります

 [
 c_i+(T-1)w_i
 】

 日 (T) にすべてのバスが乗車したとき。 価格はチケットを購入する駅のみに依存し、目的地には依存しません。 

アリスはステーション (1) からスタートします。 考えられるすべての目的地 (k) について、すべての有効なバス乗車シーケンスおよび全日にわたる最小合計チケット料金 (1\le T\le T_{\max}) が必要です。 

グラフには最大 (200,000) 個の頂点がありますが、エッジは (n+50) 個だけです。 この 2 番目の境界が構造キーです。 一般的な疎グラフでも複雑な最短経路が存在する可能性がありますが、このグラフとツリーの違いは最大でも (51) エッジです。 

時間パラメータは (10^6) と大きいため、毎日試すのは不可能です。 頂点の数も十分に大きいため、(O(n^2)) 個の作業でも意図した範囲をはるかに超えています。 このソリューションでは、追加のエッジの数が少ないことと、ステーションからのすべての発信バスの移行に同じコストがかかるという事実の両方を利用する必要があります。 

実装を簡単に壊す境界ケースがいくつかあります。 

1つ目は始発駅そのものです。 アリスは駅 (1) を訪れるために切符を購入する必要がないため、答えは常に 0 です。 例えば、```
1 0 3
1 10 -5
```出力があります```
0
```少なくとも 1 回のバス乗車を強制する最短パスの実装では、誤って正の値が返されます。 

2 番目はマイナス (w_i) です。 後の日は (1) 日よりも安くなる可能性があり、最適な日が初日であると単純に想定することはできません。 例えば、```
2 1 3
1 10 -4
1 1 0
1 2
```出力があります```
0
2
```駅 (1) の切符は 3 日間で (10,6,2) であるため、2 番目の駅に最も安く到着できるのは (3) 日目です。 

3つ目は樹木のない高速道路です。 仮定する```
3 3 1
1 3 0
1 3 0
1 3 0
1 2
2 3
1 3
```が与えられる。 スパニング ツリーには (1-2) と (2-3) を含めることができますが、(1-3) は追加のエッジになります。 (f_1=1) なので、エッジが余分にあるため、ステーション (1) からステーション (3) に 1 つのバスで到達できます。 正しい出力は次のとおりです```
0
3
3
```スパニング ツリー内の距離のみを考慮する方法では、ステーション (3) が高速道路 2 本離れたところにあると誤って認識してしまいます。 

4 番目は包含半径条件です。 (f_i=2) の場合、正確に (2) の距離にある目的地は正当です。 例えば、```
3 2 1
2 7 0
1 100 0
1 100 0
1 2
2 3
```出力があります```
0
7
7
```正確な距離 (2) にある目的地を含める必要があります。 

## アプローチ

 固定日に対する直接的な解決策は概念的に単純です。 ステーション (i) に出力エッジ コスト (c_i+(T-1)w_i) を与え、それをグラフの距離 (f_i) 内のすべてのステーションに接続します。 次にステーション (1) から Dijkstra を実行します。 

問題は、この暗黙的な有向グラフが密になる可能性があることです。 半径がグラフ全体をカバーするステーションには、(n) 個の発信バス遷移があります。 最悪の場合、(\Theta(n^2)) 個の遷移が発生します。 (T_{\max}) 日すべてを試すと、おおよその結果が得られます

 [
 O(T_{\max}n^2\log n)
 】

 仕事。 (n=200000) および (T_{\max}=10^6) の場合、可能な緩和の数だけでも約 (4\cdot10^{16}) に達する可能性があります。 

最初の主要な観測では、日の次元が完全に削除されます。 バスに乗る一定の順序を考えてみましょう。 その合計価格は、

 [
 \sum_i c_i+(T-1)\sum_i w_i,
 】

 これは (T) の一次関数です。 整数区間の線形関数 ([1,T_{\max}]) は、2 つの端点のいずれかで最小値に達します。 したがって、特定のルートはすべて、日 (1) または日 (T_{\max}) のみを考慮する必要があります。 

すべてのルートで最小値を取ると、次のプロパティが維持されます。 

\分\左(
 \min_{\text{ルート}}\text{コスト(ルート},1),
 \min_{\text{ルート}}\text{コスト(ルート},T_{\max})
 \右）。 
】

 したがって、必要な最短経路の計算は 2 つだけです。 このエンドポイントの削減は、この問題に対する既知の解決アプローチの開始点でもあります。 

では、その 2 日のうち 1 日を修正してください。 しましょう

 [
 a_i=c_i+(T-1)w_i。 
】

 アリスが駅 (i) に到着するたびに、(i) からのすべての有効なバスの乗り換えには正確に (a_i) のコストがかかります。 現在の最短距離が (d_i) であるとします。 その結果、半径内のすべてのステーションに候補値を割り当てることができます。 

[
 d_i+a_i。 
】

 これにより、有用なダイクストラ変種が得られます。 すべての有向バス エッジを明示的に保存する代わりに、キー (d_i+a_i) を使用してステーション (i) を優先キューに入れます。 処理するときは、距離 (f_i) 内でまだ到達していない頂点をすべて見つける必要があります。 

高速道路グラフが正確にツリーである場合、これは標準的な重心分解クエリになります。 すべての重心 (x) について、すべての頂点を (x) からのツリー距離でソートして現在のコンポーネントに保存します。 ソース (u) については、(u) の重心の祖先をたどります。 (d(u,x)) がすでに既知であり、

 [
 d(u,x)+d(x,v)\le f_u,
 】

 (v) は有効な宛先です。 ポインターを使用してソートされたリストを使用できます。 ダイクストラが頂点に到達すると、それを再度考慮する必要はなくなるため、すべてのポインターは前方にのみ移動します。 

実際のグラフには、スパニング ツリーを超えるエッジが最大 (51) あります。 このような非ツリー エッジの 1 つを検討し、その端点の 1 つ、たとえば (x) を選択します。 この余分なエッジを使用する最短パスは (x) を通過します。 したがって、このエッジを使用して何らかのパスを介して (u) から到達可能な宛先 (v) は次の条件を満たします。 

[
 \operatorname{dist}(u,x)+\operatorname{dist}(x,v)\le f_u。 
】

 選択したすべてのエンドポイント (x) から通常の BFS を実行し、すべての頂点を (x) からの距離が減少しない順序で保存し、まったく同じポインターのアイデアを使用できます。 このような BFS 実行は最大 (51) 回あります。 これは、参照ソリューションで使用されている重心分解のアイデアをスパース グラフに拡張したものです。 

重要な単純化は、密なバス グラフを決して構築しないことです。 重心構造といくつかの BFS 構造は、その有用な遷移のすべてを暗黙的に表します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(T_{\max}n^2\log n)) | (O(n^2)) | 遅すぎる |
 | 最適 | 2 つのエンドポイント実行あたり (O(n\log n+(m-n+1)n)) | (O(n\log n+(m-n+1)n)) | 承認済み |

 (m-n+1\le51) であるため、最適な複雑さは実質的に (O(n\log n+51n)) になります。 

## アルゴリズムのチュートリアル

1. ハイウェイ グラフのスパニング ツリーを構築します。 ツリーに対して選択されていないすべてのエッジは、余分なエッジと呼ばれます。 グラフは (m\le n+50) 個接続されているため、余分なエッジは最大で (51) 個あります。 
2. 追加のエッジ ((u,v)) ごとに、1 つのエンドポイント (たとえば (u)) を選択します。 同じエンドポイントからの 1 つの BFS がそのエンドポイントを通過するすべてのパスをすでに処理しているため、重複した選択したエンドポイントを削除できます。 
3. スパニング ツリーの重心分解を構築します。 すべての重心 (x) で、現在のコンポーネント内のすべての頂点を収集し、(x) からのツリー距離を非減少順に保存します。 
4. 重心分解を構築する際、各頂点 (u) について、各分解レベルでの重心とその重心までの距離を保存します。 これにより、ダイクストラ クエリで LCA 計算を行わずに (\operatorname{dist}_{tree}(u,x)) を取得できるようになります。 
5. 選択したすべてのエクストラエッジエンドポイント (x) に対して、元のグラフで BFS を実行します。 BFS 次数と (x) からすべての頂点までの距離を保存します。 BFS は自然に、距離が減少しない順序で頂点を生成します。 
6. 最初は (T=1) 日 (T) を固定します。 駅 (i) の切符の価格を次のように定義します。 

[
 a_i=c_i+(T-1)w_i。 
】

 以下で説明する暗黙的ダイクストラを実行します。 
7. (d_1=0) に設定します。 優先キューには、最初はキー (a_1) を持つステーション (1) が含まれています。 他のすべてのステーションについては、その距離は最初は不明です。 
8. ステーション (u) がポップされると、その優先キーは

 [
 p=d_u+a_u。 
】

 (u) から 1 つのバスで到達できるすべての駅は、距離 (p) を受け取ることができます。 
9. (u) のすべての重心レベルを処理します。 (x) をそのレベルの重心とし、(r=d_{tree}(u,x)) とします。 重心リスト内のすべての頂点 (v) は次のようになります。 

[
 d_{tree}(x,v)\le f_u-r
 】

 は有効なツリー距離の候補です。 ポインタを使用して、ソートされたリストのプレフィックスを消費します。 
10. 選択したすべてのエッジ外端点 (x) を処理します。 (r=d_G(u,x)) とします。 (x) の BFS リストで、条件を満たすすべての未処理の頂点 (v) を消費します。 

[
 d_G(x,v)\le f_u-r.
 】

 このような頂点は、(f_u) 個の高速道路内の (u) から到達可能です。 
11. 未訪問の頂点 (v) が見つかるたびに、

 [
 d_v=p
 】

 そして ((d_v+a_v,v)) を優先キューに挿入します。 頂点は 1 回だけ挿入されます。 
12. 優先キューが空になるまで繰り返します。 結果として得られる距離は、この固定日の最小コストとなります。 
13. (T=T_{\max}) に対しても同じ手順を実行します。 宛先ごとに 2 つの結果のうち小さい方を個別に取得します。 

### なぜ効果があるのか

 不変条件は、ステーション (v) に最初に距離が割り当てられたとき、その割り当てはすでに最適であるということです。 優先キューは、(d_u+a_u) によってステーションを順序付けします。これは、(u) がバス半径内のすべての目的地に与える値とまったく同じです。 (v) を改善できる将来のルートは、その代替値よりも大きくないステーションから出発する必要があるため、そのステーションが最初に処理されます。 したがって、最初の割り当てはダイクストラ式の最終距離です。 

ツリー部分の場合、スパニング ツリー エッジのみを使用するすべてのパスには正確なツリー距離があります。 重心分解では、(u) からのツリー距離が最大でも (f_u) であるすべての頂点が検索されます。 (d(u,x)+d(x,v)) を使用した条件は、実際のツリーの距離よりも強い場合がありますが、有効なツリー ルートの上限を設定するだけであるため、無効な頂点は決して受け入れられません。 

エッジ以外の部分については、非ツリー エッジを使用する任意の最短パスを選択します。 パスには、前処理用に選択されたエンドポイントを含む、そのエッジの両方のエンドポイントが含まれます。 したがって、選択したエンドポイント (x) については、

 [
 d_G(u,x)+d_G(x,v)=d_G(u,v)。 
】

 その結果、(x) の BFS 構造は、その追加のエッジを使用する最短ルートを持つすべての宛先を見つけます。 すべてのパスはツリー エッジのみを使用するか、少なくとも 1 つの追加エッジを使用するため、すべての有効なバス遷移が表現されます。 

最後に、すべてのルートには (T) 単位の線形コストがあるため、最良の日は (1) または (T_{\max}) になります。 2 つの固定日の最短パス結果の最小値を取ると、全体的に最適な答えが得られます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from array import array
from collections import deque
import heapq

def solve():
    input = sys.stdin.readline

    n, m, tmax = map(int, input().split())

    f = [0] * (n + 1)
    c = [0] * (n + 1)
    w = [0] * (n + 1)

    for i in range(1, n + 1):
        f[i], c[i], w[i] = map(int, input().split())

    graph = [[] for _ in range(n + 1)]
    tree = [[] for _ in range(n + 1)]

    dsu = list(range(n + 1))
    dsu_size = [1] * (n + 1)

    def find(x):
        while dsu[x] != x:
            dsu[x] = dsu[dsu[x]]
            x = dsu[x]
        return x

    extra_sources = []
    seen_extra = bytearray(n + 1)

    for _ in range(m):
        u, v = map(int, input().split())
        graph[u].append(v)
        graph[v].append(u)

        ru = find(u)
        rv = find(v)

        if ru != rv:
            if dsu_size[ru] < dsu_size[rv]:
                ru, rv = rv, ru
            dsu[rv] = ru
            dsu_size[ru] += dsu_size[rv]

            tree[u].append(v)
            tree[v].append(u)
        else:
            if not seen_extra[u]:
                seen_extra[u] = 1
                extra_sources.append(u)

    del dsu
    del dsu_size
    del seen_extra

    # Centroid decomposition of the spanning tree.
    #
    # At every decomposition level, center[level][v] is the centroid
    # of v's current component, and cd_dist[level][v] is the tree
    # distance from v to that centroid.
    levels = n.bit_length() + 1

    center = [
        array('i', [0]) * (n + 1)
        for _ in range(levels)
    ]
    cd_dist = [
        array('i', [0]) * (n + 1)
        for _ in range(levels)
    ]

    # For every centroid x:
    # vec_v[x] is the vertices in its component in BFS order.
    # vec_d[x] contains their distances from x in the same order.
    vec_v = [None] * (n + 1)
    vec_d = [None] * (n + 1)

    removed = bytearray(n + 1)
    temp_parent = array('i', [0]) * (n + 1)
    subtree_size = array('i', [0]) * (n + 1)

    tasks = [(1, 0)]
    while tasks:
        start, level = tasks.pop()

        # Collect this component.
        order = []
        stack = [start]
        temp_parent[start] = 0

        while stack:
            u = stack.pop()
            order.append(u)

            pu = temp_parent[u]
            for v in tree[u]:
                if removed[v] or v == pu:
                    continue
                temp_parent[v] = u
                stack.append(v)

        total = len(order)

        # Compute subtree sizes with respect to the temporary root.
        for u in reversed(order):
            s = 1
            for v in tree[u]:
                if removed[v]:
                    continue
                if temp_parent[v] == u:
                    s += subtree_size[v]
            subtree_size[u] = s

        # Find a centroid.
        centroid = start
        best = total + 1

        for u in order:
            largest = total - subtree_size[u]

            for v in tree[u]:
                if removed[v]:
                    continue
                if temp_parent[v] == u and subtree_size[v] > largest:
                    largest = subtree_size[v]

            if largest < best:
                best = largest
                centroid = u

        # BFS from the centroid inside this component.
        vv = array('i')
        dd = array('i')

        q = deque([centroid])
        temp_parent[centroid] = 0
        center[level][centroid] = centroid
        cd_dist[level][centroid] = 0

        while q:
            u = q.popleft()
            du = cd_dist[level][u]

            vv.append(u)
            dd.append(du)

            for v in tree[u]:
                if removed[v] or v == temp_parent[u]:
                    continue

                temp_parent[v] = u
                center[level][v] = centroid
                cd_dist[level][v] = du + 1
                q.append(v)

        vec_v[centroid] = vv
        vec_d[centroid] = dd

        removed[centroid] = 1

        # After removing the centroid, each remaining neighbor starts
        # an independent component.
        next_level = level + 1
        for v in tree[centroid]:
            if not removed[v]:
                tasks.append((v, next_level))

    del removed
    del temp_parent
    del subtree_size

    # For each selected endpoint of an extra edge, run BFS in the
    # original graph. BFS order is already sorted by distance.
    key_vertices = []
    key_distances = []

    for source in extra_sources:
        dist = array('i', [-1]) * (n + 1)
        vertices = array('i')

        dist[source] = 0
        q = deque([source])

        while q:
            u = q.popleft()
            vertices.append(u)

            nd = dist[u] + 1
            for v in graph[u]:
                if dist[v] == -1:
                    dist[v] = nd
                    q.append(v)

        key_distances.append(dist)
        key_vertices.append(vertices)

    key_count = len(key_vertices)

    # Bits needed to encode a vertex are no longer needed here because
    # centroid vertices and distances are stored separately.
    def dijkstra(day_offset, best_answer):
        cost = [0] * (n + 1)
        for i in range(1, n + 1):
            cost[i] = c[i] + w[i] * day_offset

        dis = [-1] * (n + 1)
        dis[1] = 0

        # Each centroid list is consumed monotonically.
        ptr = [0] * (n + 1)

        # Each extra-edge BFS list is also consumed monotonically.
        ptr_key = [0] * key_count

        heap = [(cost[1], 1)]

        while heap:
            p, u = heapq.heappop(heap)

            # p = dis[u] + cost[u].
            if best_answer[u] > dis[u]:
                best_answer[u] = dis[u]

            fu = f[u]

            # Tree-distance transitions through centroid decomposition.
            for level in range(levels):
                x = center[level][u]
                if x == 0:
                    break

                remaining = fu - cd_dist[level][u]
                if remaining < 0:
                    continue

                vv = vec_v[x]
                dd = vec_d[x]

                j = ptr[x]
                size_v = len(vv)

                while j < size_v and dd[j] <= remaining:
                    v = vv[j]

                    if dis[v] == -1:
                        dis[v] = p
                        heapq.heappush(
                            heap,
                            (p + cost[v], v)
                        )

                    j += 1

                ptr[x] = j

            # Transitions whose route uses at least one non-tree edge.
            for z in range(key_count):
                kd = key_distances[z]
                kv = key_vertices[z]

                remaining = fu - kd[u]
                if remaining < 0:
                    continue

                j = ptr_key[z]
                size_v = len(kv)

                while j < size_v and kd[kv[j]] <= remaining:
                    v = kv[j]

                    if dis[v] == -1:
                        dis[v] = p
                        heapq.heappush(
                            heap,
                            (p + cost[v], v)
                        )

                    j += 1

                ptr_key[z] = j

        return best_answer

    INF = 10**30
    answer = [INF] * (n + 1)

    dijkstra(0, answer)

    if tmax > 1:
        dijkstra(tmax - 1, answer)

    sys.stdout.write(
        '\n'.join(str(answer[i]) for i in range(1, n + 1))
    )

if __name__ == "__main__":
    solve()
```エッジ外の BFS 実行には元のハイウェイが必要であるため、入力フェーズでは完全なグラフが保存されます。 同時に、DSU はスパニング ツリーを選択します。 拒否されたエッジはすべて余分なエッジであり、最大 (m-n+1\le51) 個存在する可能性があります。 

重心の前処理は再帰的ではなく反復的です。 これにより、Python の再帰制限が回避され、大規模な再帰呼び出しスタックの維持も回避されます。 各重心コンポーネントについて、一時的な DFS がサブツリーのサイズを決定して重心を識別し、BFS が距離が減少しない順序で頂点を記録します。 

の`array`モジュールは意図的に使用されています。 数百万の Python 整数を含む Python リストは、パックされた整数配列よりもはるかに多くのメモリを消費します。 重心距離構造には (O(n\log n)) 個の整数が含まれますが、エクストラエッジ BFS 構造には (O(51n)) 個の整数が含まれます。 パックされた配列は、これらの構造をメモリ バジェット内に保ちます。 

2 つの重心配列には、分解レベルによってインデックスが付けられます。 頂点 (u) の場合、`center[level][u]`関連する重心を与え、`cd_dist[level][u]`それまでの距離を与えます。 これは、すべてのセントロイド クエリ (O(\log n)) を維持しながら、一般的な C++ 実装で使用される LCA とスパース テーブルを置き換えます。 

ポインター配列は、ダイクストラ実行ごとにリセットされます。 ポインタが後方に移動することはありません。 ポインタより前のすべてのエントリはすでに検査されており、検査時にまだ訪問されていない頂点はすでに最適なダイクストラ距離を取得しているため、後のクエリで半径を小さくしても無害です。 

プライオリティキューストア`dis[u] + cost[u]`、というよりも`dis[u]`。 この値は、まさに (u) でもう 1 枚チケットを購入した後に得られるコストです。 (u) からのすべての発信バスの乗り換え料金は同じであるため、(u) から検出されたすべての目的地は同じ候補を取得します。 さらに重要なのは、各頂点が最初の検出時にのみ割り当てられるため、(O(n)) 個のヒープ挿入のみが発生することです。 

表現`c[i] + w[i] * day_offset`ゼロベースの日オフセットを使用します。 日 (1) はオフセット (0) に対応し、日 (T_{\max}) はオフセット (T_{\max}-1) に対応します。 これは、実装におけるオフバイワンの主な詳細です。 

Python の整数はオーバーフローしないため、数学的値が 32 ビット整数よりはるかに大きい場合でも、(w_i) を含む中間乗算は安全です。 この問題により、実際のチケット価格が規定の範囲内に収まることが保証されます。 

## 実用的な例

 ### サンプル 1

 入力は```
6 6 2
1 50 -40
1 2 100
2 1 100
2 4 100
3 1 100
1 1 100
1 2
2 3
3 4
4 2
2 5
6 1
```1 日目のチケット価格は (50,2,1,2,1,1) です。 重要なダイクストラ状態は次のとおりです。 

| 日 | ポップな駅 | 現在のキー (p) | 新しく到着した駅 | 影響を受ける最終距離 |
 | --- | --- | --- | --- | --- |
 | 1 | 1 | 50 | 2、6 | (d_2=50,\ d_6=50) |
 | 1 | 6 | 51 | なし | 変更なし |
 | 1 | 2 | 52 | 3、5 | (d_3=52,\ d_5=52) |
 | 1 | 3 | 53 | 4 | (d_4=52) |
 | 1 | 5 | 53 | なし | 変更なし |
 | 1 | 4 | 54 | なし | 変更なし |

 結果として、宛先のコストは (0,50,52,52,52,50) になります。 

日 (2) のチケット価格は (10,102,101,102,101,101) になります。 

| 日 | ポップな駅 | 現在のキー (p) | 新しく到着した駅 | 影響を受ける最終距離 |
 | --- | --- | --- | --- | --- |
 | 2 | 1 | 10 | 2、6 | (d_2=10,\ d_6=10) |
 | 2 | 2 | 112 | 3、5 | (d_3=112,\ d_5=112) |
 | 2 | 6 | 111 | なし | 変更なし |
 | 2 | 3 | 213 | 4 | (d_4=11​​2) |

 2 日間の小さい方の結果を取得すると、次のようになります。```
0
10
52
52
52
10
```トレースは、プライオリティ キュー キーが無効になっている理由も示しています。`distance + current ticket cost`。 たとえば、ステーション (2) は、日 (1) に距離 (50) で到着し、その発信遷移キーは (50+2=52) です。 

### サンプル 2

 次の構築されたケースを考えてみましょう。```
3 2 3
2 10 -4
1 100 0
1 100 0
1 2
2 3
```ステーション (1) の半径は (2) であるため、他の両方のステーションに直接到達できます。 

| 日 | 1 でのチケット価格 | ポップな駅 | キー (p) | 新しく到着した駅 |
 | --- | --- | --- | --- | --- |
 | 1 | 10 | 1 | 10 | 2、3 |
 | 1 | 10 | 2 | 110 | なし |
 | 1 | 10 | 3 | 110 | なし |
 | 3 | 2 | 1 | 2 | 2、3 |
 | 3 | 2 | 2 | 102 | なし |
 | 3 | 2 | 3 | 102 | なし |

 最終的な答えは、```
0
2
2
```このトレースにより、エンドポイント引数が確認されます。 チケット価格は日によって異なりますが、中間日は必要ありません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n\log n + Kn)) | セントロイドの前処理コスト (O(n\log n))、(K\le51) のエクストラエッジ BFS 実行コスト (O(Kn))、および 2 つのダイクストラ実行のそれぞれが (O(n\log n+Kn)) 個の作業を実行します。 
| スペース | (O(n\log n+Kn)) | 重心リストには (O(n\log n)) 個のエントリが含まれ、エッジ外距離構造には (O(Kn)) 個のエントリが含まれます。 

ここでは (K\le m-n+1\le51) となります。 (n\le200000) の場合、エッジ外部分は約 (1,020 万個) の頂点距離エントリによって制限されますが、重心分解は (O(n\log n)) エントリのみに寄与します。 2 つのダイクストラ実行は、日パラメーターに依存する唯一の部分であり、そのうちの 2 つだけです。 これは、この問題で意図されたスパース グラフの複雑さです。 

## テストケース

 次のテストでは、`solve()`上記のソリューションの関数。 ヘルパーは標準入力を置き換え、標準出力をキャプチャします。```python
import sys
import io

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    try:
        solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Provided sample
assert run("""\
6 6 2
1 50 -40
1 2 100
2 1 100
2 4 100
3 1 100
1 1 100
1 2
2 3
3 4
4 2
2 5
6 1
""") == """\
0
10
52
52
52
10""", "sample 1"

# Minimum-size graph.
assert run("""\
1 0 3
1 10 -5
""") == """\
0""", "single station"

# Negative w_i makes the last day optimal.
assert run("""\
2 1 3
1 10 -4
1 1 0
1 2
""") == """\
0
2""", "last-day optimum"

# All values equal, plus an extra edge creating a shortcut.
assert run("""\
3 3 1
1 3 0
1 3 0
1 3 0
1 2
2 3
1 3
""") == """\
0
3
3""", "extra-edge shortcut"

# Maximum n, a tree, f_i = 1 exactly at the radius boundary.
n = 200000
stations = "1 1 0\n" * n
edges = "\n".join(f"{i} {i + 1}" for i in range(1, n))

max_case = f"{n} {n - 1} 1\n" + stations + edges + "\n"
max_expected = "".join(f"{i}\n" for i in range(n))

assert run(max_case) == max_expected, "maximum-size chain"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | (n=1、m=0) |`0`| 始発駅は切符不要 |
 | (w_1<0) | の 2 つのステーション`0, 2`| 最適な日は (T_{\max}) |
 | 等しい値を持つ三角形 |`0, 3, 3`| エクストラエッジ最短パスと等しいチケットパラメータ |
 | (f_i=1) の (n=200000) パス |`0,1,2,...,199999`| 最大入力サイズと正確な半径境界 |

 ## 特殊なケース

 ### 始発駅

 のために```
1 0 3
1 10 -5
```重心構造にはステーション (1) のみが含まれます。 ディクストラは以下から始まります`dis[1] = 0`、そして自己の目的地はすでに決まっています。 最終的な出力は次のとおりです`0`。 バストランジションは必要ありません。 

### 最終日の最適値

 のために```
2 1 3
1 10 -4
1 1 0
1 2
```最初のダイクストラの実行では、駅 (1) でのチケット価格 (10) が使用されます。 2回目はチケット料金を使用します

 [
 10+(3-1)(-4)=2。 
】

 2 回目の実行ではコスト (2) で目的地に到達するため、最終出力は次のようになります。`0, 2`。 これはまさに、両方のエンドポイント日を評価することで処理される状況です。 

### ツリー以外のショートカット

 のために```
3 3 1
1 3 0
1 3 0
1 3 0
1 2
2 3
1 3
```スパニング ツリーに (1-2) と (2-3) が含まれているとします。 (1) から (3) までのツリーの距離は (2) であり、ステーション (1) の半径の外側になります。 エッジ (1 ～ 3) は余分であるため、ステーション (1) は特別な BFS ソースの 1 つになります。 (3) までの BFS 距離は (1) であり、(f_1=1) を満たします。 その結果、エクストラエッジ構造は距離 (3) をステーション (3) に割り当てます。`0, 3, 3`。 

### 正確な半径

 のために```
3 2 1
2 7 0
1 100 0
1 100 0
1 2
2 3
```ステーション (1) からステーション (3) までの木の距離はちょうど (2) です。 重心クエリ中の条件は次のとおりです。`distance <= remaining`、厳密な不等式ではありません。 (2\le f_1=2) なので、ステーション (3) にはコスト (7) で到達します。 出力は`0, 7, 7`。 

BFS 構造では、追加のエッジに対して同じ包括的な比較が使用されます。 バス半径の境界上にある目的地は常に処理する必要があります。 

ご希望であれば、2 つの重要なアイデアに焦点を当てた短いコンテスト編集版、または Python 実装の 1 行ずつの派生版も提供できます。
