---
title: "CF 102391I - 最小直径のスパニング ツリー"
description: "すべての頂点が到達可能であり、結果のグラフがツリーになるように、接続された重み付き無向グラフから正確に (N-1) 個のエッジを選択する必要があります。 このようなすべてのスパニング ツリーの中で、最も長い加重パスができるだけ短いスパニング ツリーが必要です。"
date: "2026-08-14T14:05:19+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102391
codeforces_index: "I"
codeforces_contest_name: "XX Open Cup, Grand Prix of Korea"
rating: 0
weight: 102391
solve_time_s: 413
verified: false
draft: false
---

[CF 102391I - 最小直径スパニング ツリー](https://codeforces.com/problemset/problem/102391/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 6 分 53 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 すべての頂点が到達可能であり、結果のグラフがツリーになるように、接続された重み付き無向グラフから正確に (N-1) 個のエッジを選択する必要があります。 このようなすべてのスパニング ツリーの中で、最も長い加重パスができるだけ短いスパニング ツリーが必要です。 出力には、可能な最小直径と、その直径に達する 1 本の木のエッジが含まれている必要があります。 このステートメントでは任意の最適ツリーが許可されるため、異なる正しい出力に異なるエッジが含まれる可能性があります。 

重要な問題は、直径を最小化することと、エッジの総重量を最小化することは同じではないことです。 元のグラフに直径がはるかに小さくなる短いルートが含まれている場合でも、最小スパニング ツリーには非常に長い分岐が含まれることがあります。 

(N\le 500) では、立方時間が自然な目標になります。 完全な全ペアの最短パスの計算には (O(N^3)) がかかり、これは最大サイズで約 (1 億 2,500 万) の基本距離更新になります。 (N) 個の頂点上の完全なグラフには既に (N^{N-2}) 個のスパニング ツリーがあるため、スパニング ツリーを列挙することは完全に不可能です。 限界 (M\le N(N-1)/2) は、密なグラフに慣れていなければならないことも意味するため、主項が (O(NM)) であるアルゴリズムは、最悪の場合でも (O(N^3)) です。 

不注意な解決策が見逃す可能性のあるケースが 3 つあります。 

まず、最適な中心は元の頂点である必要はありません。 考慮する```
3 2
1 2 2
2 3 4
```唯一のスパニング ツリーはグラフ自体であるため、エッジ (1\ 2) と (2\ 3) を使用した正しい直径は (6) です。 その中心は、頂点 (2) や頂点 (3) ではなく、エッジ (2\ 3) の 1 単位内側にあります。 頂点 (2) の離心率は (4) であるため、頂点の中心だけを見ると (8) が誤って得られます。 

第 2 に、元のグラフで使用されているグラフ エッジは、最適スパニング ツリーに属している必要はありません。 例えば、```
4 6
1 2 1
2 3 1
3 4 1
1 4 100
1 3 10
2 4 10
```ツリー (1-2-3-4) の直径は (3) です。これは、すべてのスパニング ツリーが頂点 (1) と (4) を接続する必要があり、グラフにはこのパスを形成する 3 つの単位エッジが含まれるため、最適です。 エッジ (1 ～ 4) がエンドポイントを直接接続しているため、エッジ (1 ～ 4) を優先する不注意な構築では、さらに悪いツリーが生成される可能性があります。 

第三に、いくつかの最短パスは同じ長さを持つことができます。 のために```
3 3
1 2 1
2 3 1
1 3 1
```正しい直径は (2) で、木 ({1-2,2-3})、({1-2,1-3})、および ({1-3,2-3}) は両方とも有効な答えです。 実装は特定のタイブレーク順序に依存してはなりません。 

## アプローチ

 ブルート フォース ソリューションでは、(N-1) 個のグラフ エッジのすべてのサブセットを列挙し、そのサブセットがスパニング ツリーであるかどうかをテストし、その直径を計算します。 あります

 [
 \binom{M}{N-1}
 】

 そのようなサブセット。 接続性をチェックするためにグラフ走査を使用する場合、1 つのサブセットのテストにはすでに少なくとも (O(N+M)) のコストがかかるため、最悪の場合の作業は次のようになります。 

[
 O\left(\binom{M}{N-1}(N+M)\right)。 
】

 (N=500) および完全なグラフ (M=124750) の場合、これは実行可能な操作数よりも天文学的に大きくなります。 ブルート フォースは文字通りすべての可能なスパニング ツリーをチェックするので正しいのですが、候補の数を列挙するのは間違った量です。 

有益な観察は、すべての木には中心があるということです。 木の最長の道をたどり、その中点を見てください。 中点は既存の頂点であるか、エッジの内側のどこかにあります。 中点が頂点 (c) の場合、すべての頂点は (c) から最大でも直径の半分の木の距離にあります。 グラフの最短パスはツリーのパスよりも短くすることしかできないため、(c) のグラフの偏心も最大でもツリーの直径の半分です。 

これにより、問題が最短パス ツリーに即座に関連付けられます。 頂点 (c) を選択し、(c) をルートとする最短パス ツリーを構築すると、ルートから頂点までのすべての距離がグラフの距離になります。 その直径は(c)の離心率の最大2倍です。 (c) が最適ツリーの中心である場合、この境界は最適に達します。 

複雑なのは 2 番目のケースで、中心がエッジの内側にある場合です。 標準的なソリューションでは、エッジ上の任意の点を一時的なダミー頂点として扱うことでこれを処理します。 このようなすべての点にわたる最小の離心率は、グラフの絶対的な 1 中心であり、その中心を根とする最短経路ツリーによって最小直径スパニング ツリーが得られます。 この等価性は、(O(NM+N^3)) 解の背後にある古典的な還元です。 

重み (w) のエッジ (e=(u,v)) について、ダミーの中心が (u) から (x) 単位離れていると仮定します。 任意の頂点 (z) について、中心からの距離は次のようになります。 

[
 \min(x+d(u,z),\ w-x+d(v,z))。 
】

 固定 (z) の場合、これは (x) の V 字型関数であり、傾き (+1) と (-1) があります。 すべての頂点の最大値は、これらの V 字型関数の上部エンベロープです。 そのエンベロープの最小値は、頂点中心のケースですでにカバーされている端点、または 2 つの連続する関連関数間の交差点で発生します。 

1 つのエッジについて、すべての頂点を (d(u,z)) で降順に並べ替えます。 この順序をスキャンしている間、(d(v,z)) が厳密に増加している頂点のみを保持します。 これらは、まさに上部エンベロープに表示される頂点です。 連続する保持頂点が (a) と (b) の場合、該当する 2 つの線分は次の点で交差します。 

[
 x=\frac{w-d(u,a)+d(v,b)}{2},
 】

 対応する離心率は

 [
 \frac{w+d(u,a)+d(v,b)}{2}。 
】

 したがって、候補直径は単純に次のようになります。 

[
 w+d(u,a)+d(v,b)。 
】

 これにより、エッジに沿った連続的な最適化が (N) 個の距離ペアの線形スキャンに変わります。 

必要なすべてのペアの距離は、Floyd-Warshall によって取得できます。 また、グラフのエッジごとに個別にソートするのではなく、考えられるすべてのエンドポイントの距離順序を一度に事前計算します。 合計複雑度は (O(N^3+NM+N^2\log N)) となり、指定された範囲では (O(N^3)) になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(\binom{M}{N-1}(N+M))) | (O(N+M)) | 遅すぎる |
 | 最適 | (O(N^3+NM+N^2\log N)) | (O(N^2+M)) | 承認済み |

 ## アルゴリズムのチュートリアル

1. グラフを隣接リストおよび距離行列として保存します。 マトリックスには、最初に指定されたエッジの重みと対角上のゼロが含まれています。 すべての重みが正であるため、負のサイクルによる複雑な問題は発生しません。 
2. Floyd-Warshall を実行して、元の頂点のすべてのペア間の最短距離 (d(u,v)) を計算します。 後の中心の計算は、元のエッジ構造ではなく、これらの最短経路距離のみに依存します。 
3. すべての頂点 (c) について、その離心率を行 (c) の最大値として計算します。 (c) に正確に位置する中心は、候補直径 (2\operatorname{ecc}(c)) を与えます。 最適な頂点候補を保存します。 
4. 重み (w) のすべてのグラフ エッジ (e=(u,v)) について、そのエッジの内側のどこかに中心の可能性があると考えます。 このエッジでは、頂点を (d(u,z)) で降順に並べ替えます。 並べ替え順序は、同じ (u) に属するすべてのエッジに対して再利用されるため、頂点ごとに 1 回だけ計算されます。 
5. その注文を最も遠いものから最も近いものまでスキャンします。 (v) からの距離が以前に保持されていた値より大きい最後の頂点を保持します。 現在の頂点が (a) で、以前に保持された頂点が (b) である場合、2 つの対応するエンベロープ セグメントは可能な極小値で一致します。 その候補直径は

 [
 d(u,a)+w+d(v,b)。 
】

 この値により答えが改善される場合は、(e)、(a)、(b) を保存します。 端数を避けるために 2 回測定された、対応する中心位置は次のとおりです。 

[
 2x=w+d(v,b)-d(u,a)。 
】

 1. すべての頂点とエッジが検査された後、保存された候補はグラフの絶対的な中心になります。 最小直径スパニング ツリーは、この中心をルートとする最短パス ツリーから取得できます。 これが問題の中心的な構造特性です。 
2. 中心が元の頂点の場合、その頂点からダイクストラを実行し、ルートを除くすべての頂点の先行エッジを保持します。 すべての先行処理は中心からの最短パスに沿って選択されるため、これらの (N-1) 個のエッジが必要な最短パス ツリーを形成します。 
3. 中心がエッジ ((u,v)) 上にある場合は、そのエッジを一時的に削除し、概念的にダミーの頂点 (c) に置き換えます。 2 つのダミー エッジの長さは (x) および (w-x) です。 (u) を距離 (x) で初期化し、(v) を距離 (w-x) で初期化することで、マルチソース ダイクストラを実行します。 すべての距離は 2 倍にできるため、浮動小数点演算は必要ありません。 したがって、初期距離は (2x) と (2(w-x)) で、どちらも整数です。 
4. 結果として得られる先行構造は、ダミー中心を持つグラフ内の最短パス ツリーです。 (u) と (v) の 1 つだけがダミーに直接アタッチされたままの場合、そのダミー アタッチメントを削除すると、元の頂点にスパニング ツリーがすでに残ります。 両方が接続されたままの場合、両方のダミー エッジを削除すると 2 つのコンポーネントが残るため、元のエッジ ((u,v)) を追加してそれらを再接続します。 
5. 保存されている最小直径と、先行ツリーから取得した (N-1) 個の元のグラフ エッジを出力します。 

アルゴリズム全体の背後にある不変条件は、選択された中心がグラフの頂点までの可能な最小の最大最短経路距離を持つということです。 直径 (D) のスパニング ツリーには、離心率が最大 (D/2) である中点があるため、絶対中心の離心率の 2 倍が、考えられるすべてのツリー直径の下限になります。 逆に、その中心をルートとする最短パス ツリーでは、すべての頂点が中心の離心率内にあるため、すべてのツリー パスは最大でもその値の 2 倍になります。 下限と上限は一致しており、最適性が証明されています。 エッジ スキャンは、考えられるすべてのエッジで正確な最小偏心を検出し、頂点スキャンは端点の中心を処理します。 

## Python ソリューション```python
import sys
import heapq

input = sys.stdin.readline

INF = 10**30

def dijkstra_vertex(n, adj, root):
    dist = [INF] * n
    parent = [-1] * n
    dist[root] = 0
    pq = [(0, root)]

    while pq:
        d, u = heapq.heappop(pq)
        if d != dist[u]:
            continue

        for v, w, eid in adj[u]:
            nd = d + w
            if nd < dist[v]:
                dist[v] = nd
                parent[v] = u
                heapq.heappush(pq, (nd, v))

    return parent

def dijkstra_edge_center(n, adj, u, v, banned_eid, x2, w):
    # All distances are doubled.
    # The dummy center has doubled distances x2 and 2*w-x2
    # to u and v respectively.
    dist = [INF] * n
    parent = [-2] * n  # -2 means directly attached to dummy
    pq = []

    dv = 2 * w - x2

    dist[u] = x2
    dist[v] = dv
    heapq.heappush(pq, (x2, u))
    if v != u:
        heapq.heappush(pq, (dv, v))

    while pq:
        d, cur = heapq.heappop(pq)
        if d != dist[cur]:
            continue

        for to, ew, eid in adj[cur]:
            if eid == banned_eid:
                continue

            nd = d + 2 * ew
            if nd < dist[to]:
                dist[to] = nd
                parent[to] = cur
                heapq.heappush(pq, (nd, to))

    result = []

    for node in range(n):
        if parent[node] >= 0:
            result.append((parent[node], node))

    # If both u and v are still attached to the dummy, the two
    # components must be joined by the original center edge.
    if parent[u] == -2 and parent[v] == -2:
        result.append((u, v))

    return result

def solve():
    n, m = map(int, input().split())

    adj = [[] for _ in range(n)]
    edges = []

    dist = [[INF] * n for _ in range(n)]
    for i in range(n):
        dist[i][i] = 0

    for eid in range(m):
        u, v, w = map(int, input().split())
        u -= 1
        v -= 1
        edges.append((u, v, w))
        adj[u].append((v, w, eid))
        adj[v].append((u, w, eid))

        if w < dist[u][v]:
            dist[u][v] = w
            dist[v][u] = w

    # Floyd-Warshall.
    for k in range(n):
        dk = dist[k]
        for i in range(n):
            di = dist[i]
            dik = di[k]
            if dik >= INF:
                continue

            # The explicit loop avoids creating a large temporary
            # matrix row and works well for n <= 500.
            for j in range(n):
                nd = dik + dk[j]
                if nd < di[j]:
                    di[j] = nd

    # Distance orders, reused for every edge.
    orders = []
    for u in range(n):
        row = dist[u]
        order = list(range(n))
        order.sort(key=row.__getitem__, reverse=True)
        orders.append(order)

    best_diameter = INF
    best_type = 0
    best_root = -1
    best_edge = -1
    best_a = -1
    best_b = -1

    # Centers at original vertices.
    for u in range(n):
        ecc = max(dist[u])
        candidate = 2 * ecc
        if candidate < best_diameter:
            best_diameter = candidate
            best_type = 0
            best_root = u

    # Centers inside graph edges.
    for eid, (u, v, w) in enumerate(edges):
        order = orders[u]

        last = order[0]
        b_last = dist[v][last]

        for now in order[1:]:
            b_now = dist[v][now]

            if b_now > b_last:
                candidate = dist[u][now] + w + b_last

                if candidate < best_diameter:
                    best_diameter = candidate
                    best_type = 1
                    best_edge = eid
                    best_a = now
                    best_b = last

                last = now
                b_last = b_now

    # Construct the shortest-path tree from the selected center.
    if best_type == 0:
        parent = dijkstra_vertex(n, adj, best_root)
        answer_edges = []

        for v in range(n):
            if v != best_root:
                answer_edges.append((parent[v], v))
    else:
        u, v, w = edges[best_edge]

        # If a is the vertex on the u-side and b is the vertex on
        # the v-side, the center position satisfies
        #
        # x = (w + d(v,b) - d(u,a)) / 2.
        #
        # We store 2*x.
        x2 = w + dist[v][best_b] - dist[u][best_a]

        answer_edges = dijkstra_edge_center(
            n, adj, u, v, best_edge, x2, w
        )

    out = [str(best_diameter)]
    out.extend(f"{u + 1} {v + 1}" for u, v in answer_edges)
    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```距離行列は元のエッジ長で初期化され、フロイド・ウォーシャルによって最短経路で閉じられます。 グラフは単純なので、各ペアの入力エッジは最大でも 1 つですが、初期化中に最小値を取ることにより、行列の構築も堅牢になります。 

の`orders`配列は実装の重要な詳細です。 各エンドポイント (u) について、すべての頂点を (d(u,\cdot)) の降順で格納します。 エッジ ((u,v)) は再利用できます`orders[u]`、(M)-edge ループ内のソートを回避します。 

頂点中心フェーズは意図的に単純になっています。 各頂点の離心率は距離行の最大値にすぎないため、候補直径はその値の 2 倍になります。 

エッジ中心位相は、上部エンベロープ スキャンに続きます。`last`は以前に保持されていた頂点であり、`now`は、(u) からの距離が大きい順に現在の頂点です。 条件`b_now > b_last`2 番目の座標が新しい上部エンベロープ セグメントに必要な方向に移動したことを意味します。 候補者`dist[u][now] + w + b_last`は、対応する 2 つの V 字型関数の極小値のちょうど 2 倍です。 

中心自体は、エッジに沿った半整数の位置にある場合があります。 浮動小数点演算は不必要であり、潜在的に危険であるため、構築では 2 倍の距離が使用されます。 直径を(D)とすると、ダミー中心から(u)側端点までの距離の2倍は

 [
 2x=D-2d(u,a)、
 】

 これは次のように計算された値と同じです`w + dist[v][best_b] - dist[u][best_a]`。 

エッジ中心の候補の場合、元の中心エッジがダイクストラから一時的に除外されます。 これが必要なのは、ダミーの中心が、中心を迂回できる別のルートではなく、エッジ内の点を表すためです。 2 つのエンドポイントは、ダミー中心からの距離にあるソースとして初期化されます。 

値`parent[node] == -2`は、頂点が概念的な最短パス ツリーのダミー中心に直接接続されていることを意味します。 両方のエンドポイントがそのステータスを保持している場合は、ダミーを削除した後に元のエッジを復元する必要があります。 1 つだけが接続されたままの場合、先行エッジはすでに元のグラフ上にスパニング ツリーを形成しています。 

Python の整数には任意の精度があるため、(10^9) までのエッジの重み、(499) までのエッジを含むパス、および 2 倍の距離はすべて、オーバーフロー処理なしで適合します。 

## 実用的な例

 ### サンプル 1

 グラフは、すべての辺の長さが (1) に等しい三角形です。 

| ステージ | キーの状態 | 値 |
 | --- | --- | --- |
 | APSP | (d(1,2),d(1,3),d(2,3)) | (1,1,1) |
 | 頂点 1 | 偏心 | (1) |
 | 頂点 2 | 偏心 | (1) |
 | 頂点 3 | 偏心 | (1) |
 | 最適な頂点候補 | (2\cdot 1) | (2) |
 | エッジ候補 | 最小 | (2) |
 | 選択されたセンター | 考えられる頂点の 1 つ | (1) |
 | 構築されたツリー | 2つのエッジ | (1-2、\ 1-3) |
 | 直径 | (1+1) | (2) |

 すべての頂点には離心率 (1) があるため、すべての頂点が最適な中心になります。 それらのいずれかからのダイクストラは、直径が (2) の両刃星を生成します。 これは、最短経路での結合と中心間の結合が無害であることを示しています。 

### サンプル 2

 グラフには、左側のクラスターと右側のクラスターの間に太い橋のような接続があります。 

| ステージ | キーの状態 | 値 |
 | --- | --- | --- |
 | 左クラスター | 短距離 | (10,20,30) スケール |
 | 右クラスター | 短距離 | (10,20,30) スケール |
 | 接続エッジ | (3-4) | (1000) |
 | 最高の頂点中心 | 直径候補 | (1060) より大きい |
 | エッジセンター | エッジ | (3-4) |
 | エッジ中心候補 | (d(3,a)+1000+d(4,b)) | (1060) |
 | 選択された直径 | 最小 | (1060) |
 | 構築されたツリー | クロスクラスターエッジ | (3-4) |
 | 最終ツリー | 5つのエッジ | (3-4,4-6,6-5,3-2,2-1) |
 | 直径 | 最長の木道 | (1060) |

 重要な特徴は、最適な中心がウェイト (1000) の端にあることです。 2 つの側面にはそれぞれ短い構造があるため、重いエッジの内側に中心を配置すると、どちらかの端点を中心として選択するよりも 2 つの側面のバランスが良くなります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(N^3+NM+N^2\log N)) | フロイド・ウォーシャルのコスト (O(N^3))、すべての距離の注文コストのソート (O(N^2\log N))、およびすべてのエッジの注文コストのスキャン (O(NM)) |
 | スペース | (O(N^2+M)) | 距離行列は (O(N^2)) を使用しますが、グラフとエッジ リストは (O(M)) | を使用します。 

(N\le500) の場合、(N^3) と (NM) はどちらも最大でも (10^8) 回の演算になります。 (500\times500) 距離行列はわずか (250000) エントリであるため、メモリ要件は控えめです。 アルゴリズムはこれらの境界に対して意図された多項式アプローチですが、フロイド・ウォーシャル部分は Python 実装のパフォーマンスが重要な部分です。 

## テストケース

 この問題ではあらゆる最適なスパニング ツリーが受け入れられるため、テストでは完全な出力文字列を 1 文字ずつ比較するのではなく、報告された直径とツリー自体を検証する必要があります。```python
# Paste the solve() implementation from the solution above before this test code.

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

def validate(inp: str, out: str, expected_diameter: int):
    data = list(map(int, inp.split()))
    it = iter(data)

    n = next(it)
    m = next(it)

    weights = {}
    for _ in range(m):
        u = next(it)
        v = next(it)
        w = next(it)
        if u > v:
            u, v = v, u
        weights[(u, v)] = w

    lines = out.strip().splitlines()
    assert len(lines) == n, "wrong number of output lines"

    diameter = int(lines[0])
    assert diameter == expected_diameter, (
        f"wrong diameter: got {diameter}, expected {expected_diameter}"
    )

    tree = [[] for _ in range(n)]
    used = set()

    for line in lines[1:]:
        u, v = map(int, line.split())
        assert 1 <= u <= n and 1 <= v <= n and u != v

        key = (min(u, v), max(u, v))
        assert key in weights, "output contains an edge not in the graph"
        assert key not in used, "duplicate tree edge"

        used.add(key)
        w = weights[key]
        tree[u - 1].append((v - 1, w))
        tree[v - 1].append((u - 1, w))

    assert len(used) == n - 1

    parent = [-1] * n
    parent[0] = 0
    stack = [0]
    order = []

    while stack:
        u = stack.pop()
        order.append(u)
        for v, _ in tree[u]:
            if v == parent[u]:
                continue
            assert parent[v] == -1, "cycle in output"
            parent[v] = u
            stack.append(v)

    assert len(order) == n, "output edges do not connect all vertices"

    def farthest(start):
        dist = [-1] * n
        dist[start] = 0
        stack = [start]
        best = start

        while stack:
            u = stack.pop()
            if dist[u] > dist[best]:
                best = u

            for v, w in tree[u]:
                if dist[v] != -1:
                    continue
                dist[v] = dist[u] + w
                stack.append(v)

        return best, dist[best]

    a, _ = farthest(0)
    _, tree_diameter = farthest(a)

    assert tree_diameter == expected_diameter, (
        f"constructed tree has diameter {tree_diameter}"
    )

# Sample 1.
sample1 = """\
3 3
1 2 1
2 3 1
3 1 1
"""
validate(sample1, run(sample1), 2)

# Sample 2.
sample2 = """\
6 7
1 2 10
2 3 20
1 3 30
3 4 1000
4 5 30
5 6 20
4 6 10
"""
validate(sample2, run(sample2), 1060)

# Minimum-size graph.
case_min = """\
2 1
1 2 7
"""
validate(case_min, run(case_min), 7)

# Center lies inside an edge.
case_edge_center = """\
3 2
1 2 2
2 3 4
"""
validate(case_edge_center, run(case_edge_center), 6)

# All edge weights equal.
case_equal = """\
4 6
1 2 5
1 3 5
1 4 5
2 3 5
2 4 5
3 4 5
"""
validate(case_equal, run(case_equal), 10)

# A very long direct edge should not be forced into the tree.
case_long = """\
4 6
1 2 1
2 3 1
3 4 1
1 4 100
1 3 10
2 4 10
"""
validate(case_long, run(case_long), 3)

# Maximum-size style test, 500 vertices.
# A unit-weight star is already optimal and has diameter 2.
n = 500
parts = [f"{n} {n - 1}"]
for v in range(2, n + 1):
    parts.append(f"1 {v} 1")
case_max = "\n".join(parts) + "\n"

validate(case_max, run(case_max), 2)

print("all tests passed")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 1 / 1 2 7`| 直径 (7) | 最小 (N)、単辺ツリー、境界処理 |
 |`3 2 / 1 2 2 / 2 3 4`| 直径 (6) | エッジの内側に厳密に中央を配置し、半整数セーフ構造 |
 | 4 つの頂点、各重みに関する完全なグラフ (5) | 直径 (10) | 等距離、多数の最適なツリー、タイの処理 |
 | 重み (100) エッジを持つ 4 頂点グラフ | 直径 (3) | 魅力的だが有害な長いエッジを避ける |
 | (500) 頂点を持つユニットスター | 直径 (2) | 最大 (N)、疎なグラフ、大きな入力サイズ |

 ## 特殊なケース

 最初のエッジ ケースは、最小サイズのグラフです。```
2 1
1 2 7
```スパニング ツリーは 1 つだけ、つまりシングル エッジ (1-2) だけであるため、その直径は (7) です。 頂点中心スキャンにより、いずれかの頂点の離心率 (7) と候補直径 (14) が得られますが、木の直径として解釈すると大きすぎるように見えます。 これが、一般的な中心の議論を注意深く理解する必要がある理由です。単一のエッジの中点はダミーの中心です。 エッジ スキャンではエッジ (1-2) が考慮され、いずれかの端点からの距離 (3.5) にある中心が検出され、候補直径 (7) が生成されます。 この構造では 2 倍のダミー距離が使用されるため、浮動小数点値 (3.5) は格納されません。 

2 番目のエッジケースは重み付けされたパスです```
3 2
1 2 2
2 3 4
```The only possible tree has diameter (6). 頂点 (2) には離心率 (4) があるため、頂点のみのアルゴリズムでは (8) が報告されます。 エッジ (2-3) の場合、関連する頂点は (1) と (3) です。 端点からの距離のペアは ((2,6)) と ((4,0)) です。 Their envelope intersection gives

 [
 D=2+4+0=6、
 】

 2 倍の中心座標は (4+0-2=2) で、中心が頂点 (2) から 1 単位であることを意味します。 エッジ中心のダイクストラ構造により、元の 2 つのエッジが回復されます。 

3 番目のエッジ ケースは、完全な等重みグラフです。```
4 6
1 2 5
1 3 5
1 4 5
2 3 5
2 4 5
3 4 5
```任意の頂点を中心とする星の直径は (10) です。これは、異なる頂点のすべてのペアがウェイト (5) の少なくとも 1 つのエッジによって分離されており、このグラフでは 4 つの頂点にあるツリーの直径が (10) 未満であることができないため、これが最適です。 すべての距離比較は一致する可能性があるため、実装では人間が期待する星とは異なる星が選択される場合があります。 バリデーターは、特定のエッジ順序を要求するのではなく、木のプロパティと直径をチェックします。 

4 番目のエッジ ケースには非常に長いエッジが含まれています。```
4 6
1 2 1
2 3 1
3 4 1
1 4 100
1 3 10
2 4 10
```フロイド-ウォーシャルは、頂点間の最短距離が 3 つのユニット エッジによって支配されることを最初に発見しました。 したがって、中央の計算では、グラフの短い中央領域が優先されます。 最終的なツリーでは (1-2)、(2-3)、および (3-4) を使用でき、直径は (3) になります。 重み (100) エッジは、単に存在するという理由だけで必要になることはありません。 

5 番目のエッジ ケースは、選択した中心エッジを削除するとグラフが切断されるグラフです。```
3 2
1 2 2
2 3 4
```エッジ (2-3) が中心エッジとして扱われる場合、一時グラフには 2 つのコンポーネントがあり、1 つは頂点 (1,2) を含み、もう 1 つは頂点 (3) を含みます。 マルチソースのダイクストラはダミーセンターの両サイドからスタートします。 両方のエンドポイントは概念的なダミーに直接接続されたままであるため、構築によりこれら 2 つのダミー接続が削除され、元のエッジが復元されます (2-3)。 ちょうど 2 つの元のエッジが残り、スパニング ツリーが得られます。 

最後の微妙なケースは、中央エッジがブリッジではないことです。 代替パスがそのエンドポイントに接続している場合、マルチソース ダイクストラは、反対側から 1 つのエンドポイントをより安価に検出する可能性があります。 その後、1 つのエンドポイントだけがダミー センターに直接接続されたままになります。 先行エッジはすでにすべての元の頂点を 1 つのツリーに接続しているため、元の中心エッジが再度追加されることはありません。 このため、構築では中央エッジをやみくもに追加するのではなく、両方のエンドポイントがまだダミー ルートであるかどうかをチェックします。
