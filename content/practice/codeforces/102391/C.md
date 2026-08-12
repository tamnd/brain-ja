---
title: "CF 102391C - クリーニング"
description: "すべてのグリッド セルを有向グラフの頂点と考えてください。 辺を共有する 2 つのセルはエッジの候補ですが、セルはそこに書かれた方向に移動することを拒否します。"
date: "2026-08-12T02:01:21+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102391
codeforces_index: "C"
codeforces_contest_name: "XX Open Cup, Grand Prix of Korea"
rating: 0
weight: 102391
solve_time_s: 644
verified: true
draft: false
---

[CF 102391C - クリーニング](https://codeforces.com/problemset/problem/102391/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 10 分 44 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 すべてのグリッド セルを有向グラフの頂点と考えてください。 辺を共有する 2 つのセルはエッジの候補ですが、セルはそこに書かれた方向に移動することを拒否します。 したがって、すべてのセルには最大 3 つの出力エッジがあり、多くの有向サイクルが含まれている場合でも、グラフは高度に構造化されています。 

クエリの場合、プレーヤーはセル (s) から開始し、最終的にセル (t) に立たなければなりません。 セルを訪問する (s) から (t) への有向ウォークが存在する場合、セルは正確にクリーニングされる必要があります。 同様に、必要なセルは (s) から到達可能であり、それ自体 (t) に到達できる頂点です。 

グリッドには最大で (10^6) 個のセルが含まれますが、(3\cdot10^5) 個のクエリが存在する可能性があります。 クエリごとに個別にグラフを走査すると、クエリごとに最大 (10^6) 個のセルが検査され、最悪の場合でも約 (3\cdot10^{11}) 個の頂点が検査されます。 非常に最適化された BFS であっても、2 秒の制限をはるかに超えます。 前処理はグリッド サイズにおいて線形に近い必要があり、各クエリにはほぼ対数時間がかかる必要があります。 公式の制約では、2 秒の制限と 1024 MB のメモリが与えられています。 

不注意な実装によって間違った答えが得られる簡単なケースがいくつかあります。 (1\times1) グリッドはそのようなケースの 1 つです。```
1 1 1
U
1 1 1 1
```答えは`1`プレーヤーはすでに同じセルで開始および終了しているためです。 空ではない手のみを考慮する到達可能性テストでは、誤ってゼロが返される可能性があります。 

2 番目のトラップは、相互にブロックするセルのペアです。```
1 2 1
RL
1 1 1 2
```左のセルは右への移動を拒否し、右のセルは左への移動を拒否します。 道はない、だから答えは`0`。 隣接関係を無向接続として扱うと、ターゲットが到達可能であると誤って主張されてしまいます。 

逆の現象も考えられます。 考慮する```
1 2 1
LR
1 1 1 2
```左のセルは右に移動でき、右のセルは左に移動できるため、両方のセルが 1 つの強く接続されたコンポーネントを形成します。 答えは`2`。 強く接続されたコンポーネントを圧縮しても、コンポーネントに元のセルの数を与えるのを忘れると、間違った数が生成されます。 

最後に、到達可能なターゲットは、最初から到達可能なすべてのセルが答えに属することを意味するものではありません。 セルは、スタートからターゲットまでのウォーク上にある必要があります。 フォワード BFS だけでは大きすぎるセットを計算します。 この違いが、ソリューションでより具体的なグラフ表現を必要とする理由です。 

## アプローチ

 直接的なアプローチは、開始セルから検索を実行し、到達可能なすべてのセルを保持し、それらのセルのうちどのセルが最終的にターゲットに到達できるかを個別に決定することです。 2 番目の部分は、ターゲットから反転したグラフをたどることによって実行できます。 それらの交差は、少なくとも 1 回の (s) から (t) への歩行で発生するセルのセットであるため、この方法は正しいです。 

問題は繰り返し作業です。 1 つのクエリに (O(NM)) 個の作業が必要な場合があり、(Q) 個のクエリが存在します。 (N,M\le1000) と (Q\le300000) を使用すると、これは (O(QNM)) となり、およそ (3\cdot10^{11}) 回のセル訪問に達します。 すべてのクエリの到達可能なセット全体を保存することも不可能です。 

重要な観察は、このグリッド グラフが任意の有向グラフではないということです。 まず、強く結合したコンポーネントを圧縮します。 1 つのコンポーネント内では、すべてのセルが他のすべてのセルに到達できるため、コンポーネント間を移動する場合、コンポーネント全体が 1 つの頂点として動作します。 さらに重要なことは、これらのコンポーネントがトポロジカルな順序で処理される場合、すでに処理されたセルは常に分離された長方形のコレクションを形成することです。 この幾何学的特性により、巨大な有向グラフが圧縮可能になります。 

次の強結合成分が (C) であるとします。 (C) を含む最小の長方形を考えてみましょう。 その境界長方形の内側にある以前に処理された長方形は、補助構造の (C) にマージできます。 次に、外接する四角形の 4 つの辺を検査します。 一辺に直接接触する長方形は、仮想頂点を介してグループ化されます。 重要な方向特性は、並べて配置された長方形のグループの場合、グループ内のすべてのセルが並べて配置された配置に対して垂直な方向を通ってグループから離れる同じ能力を持っていることです。 したがって、1 つの仮想接続で、元のすべての有向エッジを表すのに十分です。 

結果として得られる補助グラフはツリーです。 元の強接続コンポーネントはそれぞれ、セルの数に等しい重みを持つ重み付きツリー頂点になります。 仮想頂点の重みはゼロです。 すべてのツリー頂点の子はチェーンに配置され、元の到達可能性関係はそれらのチェーンから復元できます。 

このツリーが存在すると、クエリはツリー クエリになります。 開始コンポーネントをターゲット コンポーネントと同じ深さになるまで上に移動します。 結果として得られる頂点が同じ親を持たない場合、ターゲットには到達できません。 それ以外の場合、2 つの頂点は 1 つの順序付けられたチェーン内の兄弟であり、一連の兄弟コンポーネントが答えに寄与します。 順序付けされた子のプレフィックス合計により、祖先ジャンプ後のこの範囲のカウントが一定時間になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(QNM)) | (O(NM)) | 遅すぎる |
 | SCC + 長方形ツリー + バイナリリフティング | (O(NM\アルファ(NM)+Q\log(NM))) | (O(NM\log(NM))) | 承認済み |

 この構築は基本的に公式ソリューションと同じ構造的な考え方であり、以下の実装では再帰的な C++ DFS 呼び出しを反復的な Python トラバーサルで置き換え、パックされた整数配列を使用してメモリを制御します。 

## アルゴリズムのチュートリアル

1. 暗黙的な有向グリッド グラフを構築し、Tarjan のアルゴリズムを使用してすべての強接続コンポーネントを計算します。 セルには、そのセルに書き込まれた方向を除く、すべての有効な隣接セルへの発信エッジがあります。 すべてのエッジは一定時間内にセルから再生成できるため、すべてのグラフ エッジを明示的に保存することはありません。 
2. 強結合成分を成分番号の逆順に処理します。 Tarjan はコンポーネントをそのルートが完了した順序で割り当てるため、この順序は構築に必要なトポロジー処理に対応します。 
3. すべてのコンポーネント (C) について、その境界四角形 ([x_{\min},x_{\max}]\times[y_{\min},y_{\max}]) と元のセルの数を保存します。 強く接続されたコンポーネント内のすべてのセルは、コンポーネント自体が使用可能なときはいつでも使用できるため、コンポーネントの重みはまさにその数値になります。 
4. すでに処理されたコンポーネントと仮想頂点上で素セットの結合構造を維持します。 (C) を処理する場合、(C) に属するすべてのセルを検査します。 (C) の境界四角形内で検出された処理済みコンポーネントはすべて (C) に向かってマージされます。 これは、それらの領域が内部の長方形構造を介して (C) に入ることができるという事実を表しています。 
5. 外接する四角形のすぐ上とすぐ下の行を調べます。 既存の各サイドについて、その列を左から右にスキャンします。 連続する処理領域は、複数の領域が発生するたびに、新しく作成された仮想頂点を介して結合されます。 長方形のすぐ左と右の列に対して同様の操作を実行します。 
6. 側面をスキャンしながら、すべての境界セルが (C) を指す方向を拒否しているかどうかを確認します。 そうなると、側面から (C) への実際のエッジは存在しなくなるため、非ツリー接続は記録されません。 それ以外の場合は、サイドグループと (C) の間の補助接続を 1 つ記録します。 ここで、元のグリッドの方向情報がツリー構造に入力されます。 
7. すべてのコンポーネントが処理された後、残りのすべての DSU ルートが 1 つの人工ルートに接続されます。 結果として生じる親関係はツリーを形成します。 仮想頂点の重みはゼロですが、元の SCC 頂点はコンポーネント サイズを保持します。 
8. ツリーの頂点ごとに順序付けられた子配列を構築します。 補助的な非ツリー接続により、すべての子に隣接する兄弟とのチェーン関係が与えられます。 これらの関係に従うことで、すべての子に位置を割り当てることができます。 次数 0 の子は 1 頂点チェーンを開始し、次数 1 の子は、隣接する 2 つのチェーンの XOR を使用してそのチェーンのトラバースを開始します。 
9. 各子に、そのチェーンが右に続くか左に続くかを示す方向フラグを付けます。 すべてのツリー頂点について、子の重みのプレフィックスの合計を計算します。 計算もする`le[v]`そして`ri[v]`、子を含むチェーンの端 (v)。 
10. コンピューティング`val[v]`、関連する兄弟チェーンの寄与を含む、ルートから (v) までの補助ツリーの部分によって表されるセルの数。 これらの値を使用すると、クエリでターゲット深度の祖先より上の部分を一定時間で削除できます。 
11. ツリー用のバイナリ リフティング テーブルを構築します。 クエリでは、開始コンポーネントの深さがターゲット コンポーネントの深さと等しくなるまで、開始コンポーネントを上に移動するだけでよいため、標準の祖先テーブルで十分です。 一般的な LCA 計算は必要ありません。 
12. クエリ ((s,t)) の場合、両方のグリッド セルを SCC にマップします。 スタートが目標より浅い場合は目標に到達できません。 それ以外の場合は、スタートをターゲットの深さまで上向きにジャンプします。 結果として得られる頂点とターゲットが同じ親を共有しない場合、ターゲットには到達できません。 それらが兄弟である場合は、チェーンのエンドポイントとプレフィックスの合計を使用して、(s)-to-(t) ウォークでセルが出現する可能性のあるコンポーネントを正確に数えます。 

### なぜ効果があるのか

 中心的な不変条件は、SCC トポロジー順序のプレフィックスを処理した後、処理された領域が互いに素な長方形として表現可能であり、場合によっては並列チェーンにグループ化されることです。 これらの長方形の方向特性により、そのような 1 つのグループ内のすべてのセルが垂直方向に離れる同じ能力を持っていることが保証されます。 したがって、すべての境界接続を単一の仮想接続に置き換えても、到達可能性は変わりません。 

すべての SCC が処理された後、補助構造はツリーになります。 ある SCC から別の SCC への有向パスは、対応する一意のツリー ルートに従う必要がありますが、兄弟間の移動は記録されたチェーン内で正確に可能です。 クエリ プロシージャは、まずターゲットの深さより上のツリー部分を削除し、次に、結果として得られる 2 つの兄弟が同じ到達可能なチェーン上にあるかどうかを確認します。 プレフィックス合計の式は、そのチェーン上の重み付けされた SCC を正確にカウントします。`val`その上のすでに固定されている部分を説明します。 したがって、カウントされたすべてのセルは、何らかの有効な開始からターゲットまでのウォークに属し、そのようなウォークに属するすべてのセルがカウントされます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from array import array

def solve():
    read = sys.stdin.readline
    n, m, q = map(int, read().split())
    cells = n * m
    MAX = 2 * cells + 5

    # Directions:
    # 0 = L, 1 = R, 2 = U, 3 = D
    direction = bytearray(cells)

    for i in range(n):
        s = read().strip()
        base = i * m
        for j, ch in enumerate(s):
            if ch == 'L':
                direction[base + j] = 0
            elif ch == 'R':
                direction[base + j] = 1
            elif ch == 'U':
                direction[base + j] = 2
            else:
                direction[base + j] = 3

    dx = (-0, 0, -1, 1)
    dy = (-1, 1, 0, 0)

    def iarr(length, value=0):
        return array('i', [value]) * length

    # ------------------------------------------------------------
    # Iterative Tarjan SCC
    # ------------------------------------------------------------

    dfn = iarr(cells, -1)
    low = iarr(cells, -1)
    bel = iarr(cells, -1)
    nxt_cell = iarr(cells, -1)

    # SCC member linked lists.
    member_head = iarr(MAX, -1)
    sz = iarr(MAX, 0)
    xmi = iarr(MAX, n)
    xma = iarr(MAX, -1)
    ymi = iarr(MAX, m)
    yma = iarr(MAX, -1)

    tarjan_stack = []
    dfs_stack = []
    it = bytearray(cells)

    timer = 0
    cnt = 0

    for root in range(cells):
        if dfn[root] != -1:
            continue

        dfn[root] = timer
        low[root] = timer
        timer += 1
        tarjan_stack.append(root)
        dfs_stack.append(root)

        while dfs_stack:
            u = dfs_stack[-1]
            k = it[u]

            while k < 4:
                it[u] = k + 1

                if k == direction[u]:
                    k += 1
                    continue

                ux = u // m
                uy = u - ux * m
                vx = ux + dx[k]
                vy = uy + dy[k]

                if vx < 0 or vx >= n or vy < 0 or vy >= m:
                    k += 1
                    continue

                v = vx * m + vy

                if dfn[v] == -1:
                    dfn[v] = timer
                    low[v] = timer
                    timer += 1
                    tarjan_stack.append(v)
                    dfs_stack.append(v)
                    break

                if bel[v] == -1 and dfn[v] < low[u]:
                    low[u] = dfn[v]

                k = it[u]

            else:
                dfs_stack.pop()

                if dfs_stack:
                    p = dfs_stack[-1]
                    if low[u] < low[p]:
                        low[p] = low[u]

                if low[u] == dfn[u]:
                    while True:
                        v = tarjan_stack.pop()
                        bel[v] = cnt

                        x = v // m
                        y = v - x * m

                        nxt_cell[v] = member_head[cnt]
                        member_head[cnt] = v
                        sz[cnt] += 1

                        if x < xmi[cnt]:
                            xmi[cnt] = x
                        if x > xma[cnt]:
                            xma[cnt] = x
                        if y < ymi[cnt]:
                            ymi[cnt] = y
                        if y > yma[cnt]:
                            yma[cnt] = y

                        if v == u:
                            break

                    cnt += 1

    # ------------------------------------------------------------
    # Auxiliary tree construction
    # ------------------------------------------------------------

    parent = iarr(MAX, -1)
    dsu = array('i', range(MAX))

    deg = iarr(MAX, 0)
    chain_xor = iarr(MAX, 0)

    # Non-tree edges are kept as packed integer arrays.
    edge_a = array('i')
    edge_b = array('i')

    def find(x):
        while dsu[x] != x:
            dsu[x] = dsu[dsu[x]]
            x = dsu[x]
        return x

    for c in range(cnt - 1, -1, -1):
        # First merge processed components inside the bounding rectangle.
        u = member_head[c]
        while u != -1:
            ux = u // m
            uy = u - ux * m

            for k in range(4):
                vx = ux + dx[k]
                vy = uy + dy[k]

                if vx < xmi[c] or vx > xma[c] or vy < ymi[c] or vy > yma[c]:
                    continue

                v = vx * m + vy
                r = find(bel[v])

                if r != c:
                    parent[r] = c
                    dsu[r] = c

            u = nxt_cell[u]

        # Scan horizontal sides.
        for x in (xmi[c] - 1, xma[c] + 1):
            if x < 0 or x >= n:
                continue

            first_bel = bel[x * m + ymi[c]]
            if first_bel < c:
                continue

            all_blocked = True
            group = find(first_bel)
            first = True

            for y in range(ymi[c], yma[c] + 1):
                vcell = x * m + y

                forbidden = 3 if x < xmi[c] else 2
                if direction[vcell] != forbidden:
                    all_blocked = False

                r = find(bel[vcell])

                if r != group:
                    if first:
                        parent[group] = cnt
                        dsu[group] = cnt
                        group = cnt
                        cnt += 1
                        first = False

                    parent[r] = group
                    dsu[r] = group

            if not all_blocked:
                edge_a.append(group)
                edge_b.append(c)
                deg[group] += 1
                chain_xor[group] ^= c
                deg[c] += 1
                chain_xor[c] ^= group

        # Scan vertical sides.
        for y in (ymi[c] - 1, yma[c] + 1):
            if y < 0 or y >= m:
                continue

            first_bel = bel[xmi[c] * m + y]
            if first_bel < c:
                continue

            all_blocked = True
            group = find(first_bel)
            first = True

            for x in range(xmi[c], xma[c] + 1):
                vcell = x * m + y

                forbidden = 1 if y < ymi[c] else 0
                if direction[vcell] != forbidden:
                    all_blocked = False

                r = find(bel[vcell])

                if r != group:
                    if first:
                        parent[group] = cnt
                        dsu[group] = cnt
                        group = cnt
                        cnt += 1
                        first = False

                    parent[r] = group
                    dsu[r] = group

            if not all_blocked:
                edge_a.append(group)
                edge_b.append(c)
                deg[group] += 1
                chain_xor[group] ^= c
                deg[c] += 1
                chain_xor[c] ^= group

    # Add one root above all remaining DSU roots.
    root = cnt

    for i in range(cnt):
        if dsu[i] == i:
            parent[i] = root
            dsu[i] = root

    cnt += 1
    parent[root] = root

    nodes = cnt

    # ------------------------------------------------------------
    # Store children in contiguous ranges.
    # ------------------------------------------------------------

    child_count = iarr(nodes, 0)

    for i in range(nodes - 1):
        child_count[parent[i]] += 1

    start = iarr(nodes, 0)
    total = 0
    for u in range(nodes):
        start[u] = total
        total += child_count[u]

    ordered = iarr(nodes - 1, 0)
    used = iarr(nodes, 0)

    for i in range(nodes - 1):
        p = parent[i]
        idx = start[p] + used[p]
        ordered[idx] = i
        used[p] += 1

    # ------------------------------------------------------------
    # Depth and binary lifting.
    # ------------------------------------------------------------

    depth = iarr(nodes, 0)
    p0 = iarr(nodes, 0)
    p0[root] = root

    stack = [root]

    while stack:
        u = stack.pop()
        begin = start[u]
        end = begin + child_count[u]

        for idx in range(begin, end):
            v = ordered[idx]
            depth[v] = depth[u] + 1
            p0[v] = u
            stack.append(v)

    LOG = max(1, (nodes - 1).bit_length())
    up = [p0]

    for _ in range(1, LOG):
        prev = up[-1]
        cur = iarr(nodes, 0)
        for i in range(nodes):
            cur[i] = prev[prev[i]]
        up.append(cur)

    # ------------------------------------------------------------
    # Order children by chain structure.
    # ------------------------------------------------------------

    pos = iarr(nodes, -1)
    cp = iarr(nodes, 0)

    for i in range(nodes - 1):
        if pos[i] != -1:
            continue

        if deg[i] == 0:
            p = parent[i]
            pos[i] = cp[p]
            cp[p] += 1

        elif deg[i] == 1:
            u = i
            previous = 0
            p = parent[u]

            while True:
                pos[u] = cp[p]
                cp[p] += 1

                nxt = previous ^ chain_xor[u]
                previous, u = u, nxt

                if deg[u] != 2:
                    pos[u] = cp[p]
                    cp[p] += 1
                    break

    # Rebuild children according to their final positions.
    for i in range(nodes - 1):
        p = parent[i]
        ordered[start[p] + pos[i]] = i

    # Direction of the auxiliary chain edges.
    chain_dir = iarr(nodes, 0)

    for i in range(len(edge_a)):
        a = edge_a[i]
        b = edge_b[i]

        if pos[a] < pos[b]:
            chain_dir[a] = 1
        else:
            chain_dir[b] = -1

    # ------------------------------------------------------------
    # Prefix sums and val/le/ri.
    # ------------------------------------------------------------

    prefix = iarr(nodes, 0)
    le = iarr(nodes, 0)
    ri = iarr(nodes, 0)
    val = iarr(nodes, 0)

    # Process parents before children.
    stack = [root]

    while stack:
        u = stack.pop()
        begin = start[u]
        end = begin + child_count[u]

        if begin == end:
            continue

        running = 0
        for idx in range(begin, end):
            v = ordered[idx]
            running += sz[v]
            prefix[v] = running

        for idx in range(begin, end):
            v = ordered[idx]

            if idx == begin or chain_dir[ordered[idx - 1]] != -1:
                le[v] = v
            else:
                le[v] = le[ordered[idx - 1]]

        for idx in range(end - 1, begin - 1, -1):
            v = ordered[idx]

            if chain_dir[v] != 1:
                ri[v] = v
            else:
                ri[v] = ri[ordered[idx + 1]]

        for idx in range(begin, end):
            v = ordered[idx]
            val[v] = prefix[ri[v]] - prefix[le[v]] + sz[le[v]]
            val[v] += val[u]

        for idx in range(begin, end):
            stack.append(ordered[idx])

    def query(a, b):
        if depth[a] < depth[b]:
            return 0

        ret = val[a]

        diff = depth[a] - depth[b]

        bit = 0
        while diff:
            if diff & 1:
                a = up[bit][a]
            diff >>= 1
            bit += 1

        ret -= val[a]

        if parent[a] != parent[b]:
            return 0

        if pos[a] < pos[b]:
            if pos[ri[a]] >= pos[b]:
                return prefix[b] - prefix[a] + ret + sz[a]
            return 0

        if pos[le[a]] <= pos[b]:
            return prefix[a] - prefix[b] + ret + sz[b]

        return 0

    # ------------------------------------------------------------
    # Queries.
    # ------------------------------------------------------------

    out = []

    for _ in range(q):
        x1, y1, x2, y2 = map(int, read().split())
        x1 -= 1
        y1 -= 1
        x2 -= 1
        y2 -= 1

        a = bel[x1 * m + y1]
        b = bel[x2 * m + y2]

        out.append(str(query(a, b)))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```Python の再帰制限では最大 (10^6) 個のセルを含むパスを安全に処理できないため、SCC フェーズでは明示的な DFS スタックが使用されます。 からの配列`array('i')`も意図的です。 数百万の Python 整数からなる通常の Python リストは、同等の C++ 整数配列よりも大幅に多くのメモリを消費します。 

方向エンコーディングは、隣接する順序と正確に一致する必要があります。`0,1,2,3`はそれぞれ左、右、上、下を意味するため、使用可能な 3 つの近傍をチェックする前に、禁止された方向はスキップされます。 

コンポーネントのメンバーリストは次のように表されます。`member_head`そして`nxt_cell`。 これは C++ を置き換えます`vector<int> vr[N]`構造を変更し、最大 (10^6) 個の個別の Python リスト オブジェクトの作成を回避します。 

長方形の処理中に、DSU は、すでに処理されたすべての領域の現在の代表を保存します。 条件`bel[v] < c`長方形の辺も意図的です。 必要なトポロジー順序ですでに出現しているコンポーネントのみがそのスキャンに参加できます。 

人工根はそれ自体を持ち上げる親として持ちます。 これにより、親が存在しない元の C++ ルートの論理的意味を保持しながら、Python の配列アクセスにおける負のインデックスが回避されます。 

順序付けられた子配列は、次の後に再構築されます。`pos`が割り当てられています。 これが必要なのは、プレフィックス合計の式が、ノードがたまたま作成された順序ではなく、最終的なチェーンの順序に依存するためです。 

クエリは最初に開始頂点のみを変更します。 ターゲットのツリーの深さは保存されるため、このジャンプによって到達した祖先が、ターゲットと同じ兄弟チェーンに参加できる唯一の候補になります。 親が異なる場合、ターゲットへの方向付けられたルートは存在せず、答えは即座にゼロになります。 

すべての答えの値は最大 (NM\le10^6) であるため、保存されるグラフ量には 32 ビットの符号付き整数で十分です。 Python の整数は中間算術に自動的に使用されるため、最終計算ではオーバーフローの問題は発生しません。 

## 実用的な例

 公式サンプルは 1 つだけ提供されているため、2 番目のトレースは構築された小さなグリッドを使用します。 

### サンプル 1

 グリッドは```
DDDDD
RDDDL
RRDLL
RUUUL
UUUUU
```5 つの質問には答えがあります`0, 14, 20, 14, 5`。 

次のトレースは、SCC と補助ツリーの前処理後のクエリ ステージを要約しています。 

| クエリ | SCC を開始する | ターゲット SCC | 深さ関係 | ジャンプ後の同じ親 | 結果 |
 | --- | --- | --- | --- | --- | --- |
 |`(1,1) -> (5,5)`| ソース領域 | 対象地域 | 有効な深度ジャンプ | いいえ |`0`|
 |`(2,2) -> (5,5)`| SCCA | SCC B | 上に移動し始めます | はい |`14`|
 |`(3,3) -> (5,5)`| SCCC | SCC B | 上に移動し始めます | はい |`20`|
 |`(4,4) -> (5,5)`| ＳＣＣＤ | SCC B | 上に移動し始めます | はい |`14`|
 |`(5,5) -> (5,5)`| SCC B | SCC B | ゼロジャンプ | はい |`5`|

 最初のクエリは、この構造が幾何学的近接性と到達可能性を混同していないことを示しています。 最後のクエリは、SCC の重みの不変性を示しています。両方のエンドポイントが同じ SCC 内にある場合、答えはその SCC のサイズです。`5`ここ。 

### 構築例

 検討してください```
2 3 2
UUU
UUU
1 1 2 3
2 3 1 1
```のために`U`セルの上方向への移動は禁止されていますが、対応する隣接セルが存在する場合は下方向および水平方向への移動が許可されます。 から`(1,1)`に`(2,3)`、すべてのセルは有効なパス上に存在する可能性があります。 

| クエリ | 開始 | ターゲット | 到達可能ですか？ | あるパス上のセル | 答え |
 | --- | --- | --- | --- | --- | --- |
 | 1 |`(1,1)`|`(2,3)`| はい | 6 つのセルすべて |`6`|
 | 2 |`(2,3)`|`(1,1)`| いいえ | なし |`0`|

 2 番目のクエリは方向性を示します。 ターゲットは開始点の上にありますが、上に移動することはできないため、補助ツリーの深さテストは最終的にクエリを拒否します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(NM\アルファ(NM)+Q\log(NM))) | SCC の計算、DSU の四角形の構築、およびツリーの前処理はほぼ線形です。 各クエリは 1 つのバイナリリフティング祖先ジャンプを実行します。 
| スペース | (O(NM\log(NM))) | 主な追加コストはバイナリリフティングテーブルです。 すべてのグリッド配列とツリー配列は線形です。 

(NM\le10^6) を使用すると、前処理は DSU 操作とは別に一定の回数だけグリッドに触れます。 (3\cdot10^5) クエリはそれぞれ (O(\log(NM))) の作業のみを必要とします。 これは制約によって要求される複雑さであり、一般に認められている C++ アプローチの構造と一致します。その複雑さは (O(NM\alpha(NM)+Q\log(NM))) です。 

100 万頂点では漸近複雑さだけでは十分ではないため、Python 実装ではパックされた整数配列と反復走査が使用されます。 ネストされたリストと再帰的 DFS を使用した単純な Python 変換は、より多くのメモリを消費し、深い DFS ツリーでも失敗します。 

## テストケース```python
# This test block assumes the solve() function from the solution above
# is available in the same file.

import sys
import io
from contextlib import redirect_stdout

def run(inp: str) -> str:
    old_stdin = sys.stdin
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()

    try:
        with redirect_stdout(out):
            solve()
    finally:
        sys.stdin = old_stdin

    return out.getvalue()

# Official sample.
sample1 = """\
5 5 5
DDDDD
RDDDL
RRDLL
RUUUL
UUUUU
1 1 5 5
2 2 5 5
3 3 5 5
4 4 5 5
5 5 5 5
"""

assert run(sample1) == "0\n14\n20\n14\n5", "sample 1"

# Minimum-size grid. The only cell is both the start and target.
case_min = """\
1 1 1
U
1 1 1 1
"""

assert run(case_min) == "1", "minimum-size grid"

# Two cells block each other.
case_blocked = """\
1 2 2
RL
1 1 1 2
1 2 1 2
"""

assert run(case_blocked) == "0\n1", "mutually blocked boundary cells"

# Two cells form one strongly connected component.
case_scc = """\
1 2 1
LR
1 1 1 2
"""

assert run(case_scc) == "2", "same SCC must count both cells"

# All equal directions. Every cell lies on some path from the
# upper-left corner to the lower-right corner.
case_all_equal = """\
2 2 1
UU
UU
1 1 2 2
"""

assert run(case_all_equal) == "4", "all-equal directions"

# Boundary and directionality.
case_direction = """\
2 3 2
UUU
UUU
1 1 2 3
2 3 1 1
"""

assert run(case_direction) == "6\n0", "boundary directionality"

# Maximum-size grid. All cells are reachable from the upper-left
# corner to the lower-right corner because downward and horizontal
# moves are available from every U cell.
n = 1000
m = 1000
grid = "\n".join(["U" * m for _ in range(n)])
case_max = f"""\
{n} {m} 1
{grid}
1 1 1000 1000
"""

assert run(case_max) == "1000000", "maximum-size grid"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 1 / U / 1 1 1 1`|`1`| 最小グリッドと長さゼロのウォーク |
 |`1 2 2 / RL / ...`|`0`、`1`| 相互にブロックされたセルと境界処理 |
 |`1 2 1 / LR / 1 1 1 2`|`2`| 強結合コンポーネントの重量 |
 |`2 2 1 / UU / 1 1 2 2`|`4`| すべて等しい方向と完全なパス カバレッジ |
 |`2 3 2 / UUU / UUU / ...`|`6`、`0`| 方向性と境界到達可能性 |
 |`1000 1000 1 / all U`|`1000000`| 最大グリッド サイズと大きな答え |

 ## 特殊なケース

 (1\times1) の場合は、意味のある動きが必要になる前に処理されます。 のために```
1 1 1
U
1 1 1 1
```Tarjan は、次のサイズの SCC を 1 つ生成します`1`。 両方のクエリ エンドポイントがその SCC にマップされるため、深さの差はゼロになり、チェーン計算はその重みを返します。`1`。 

相互にブロックされている近隣の場合、```
1 2 1
RL
1 1 1 2
```最初のセルには使用可能な移動がありません。その唯一の隣接セルは右側にあり、これは禁止されています。 その結果、2 つのセルは、必要な方向にそれらを接続する補助到達可能性チェーンのない個別の SCC になります。 クエリは親またはチェーンのテストに失敗し、返されます。`0`。 

強く結びついたペアの場合、```
1 2 1
LR
1 1 1 2
```左のセルは右に移動でき、右のセルは左に移動できます。 Tarjan は両方のセルを 1 つの SCC に入れます。その重みは次のとおりです。`2`。 開始コンポーネントとターゲット コンポーネントが同一であるため、クエリは単にターゲット セルをカウントするのではなく、完全なコンポーネントの重みを返します。 

すべて等しい (2\times2) グリッドの場合```
2 2 1
UU
UU
1 1 2 2
```プレイヤーは左上のセルから下または右に移動し、結果として得られるセルから右下のセルに向かって進みます。 4 つのセルはすべて有効なパス上にあるため、答えは次のようになります。`4`。 これにより、考えられるすべてのパスの和集合ではなく、特定の 1 つの最短パスのみを計算するソリューションが捕捉されます。 

より大きな方向の例としては```
2 3 2
UUU
UUU
1 1 2 3
2 3 1 1
```最初のクエリはすべてのセルにアクセスできます。 パスは早くても遅くても下に移動でき、どちらの行でも水平に移動できるため、6 つのセルのすべてが少なくとも 1 つの開始からターゲットまでのパス上にあります。 逆引きクエリは上に移動できないため、その答えは次のようになります。`0`。 補助ツリーは、基礎となるグリッドが完全に均一に見えても、この非対称性を維持します。 

最大サイズのテストでは、(10^6) 個のセルが使用されます。 前処理ではすべてのセルが一定の回数だけ処理されますが、単一のクエリでは事前計算されたツリーのみが使用されます。 期待される答えは正確に (10^6) であり、実装が小さな次元に依存せずに最大のグリッドと可能な最大の答えの両方を処理していることを示しています。 

上記の論説は、受け入れられている構造的解決策に厳密に従っていますが、Python バージョンでは、再帰と C++ ベクトルが反復走査とパック配列に置き換えられています。
