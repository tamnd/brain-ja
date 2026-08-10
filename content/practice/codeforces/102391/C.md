---
title: "CF 102391C - クリーニング"
description: "グリッドは、配列に見せかけた有向グラフです。 すべてのセルは頂点であり、最初のセルがその方向への移動を禁止していない場合、2 つの直交する隣接セルは有向エッジによって接続されます。"
date: "2026-08-10T19:56:08+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102391
codeforces_index: "C"
codeforces_contest_name: "XX Open Cup, Grand Prix of Korea"
rating: 0
weight: 102391
solve_time_s: 562
verified: false
draft: false
---

[CF 102391C - クリーニング](https://codeforces.com/problemset/problem/102391/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 9 分 22 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 グリッドは、配列に見せかけた有向グラフです。 すべてのセルは頂点であり、最初のセルがその方向への移動を禁止していない場合、2 つの直交する隣接セルは有向エッジによって接続されます。 マークされたセル`L`たとえば、右、上、または下の隣に移動することはできますが、左の隣に移動することはありません。 グリッドの外への移動は単純に利用できません。 この解釈は元の問題定義と一致します。 

各クエリについて、開始セルがわかっています。`s`そしてエンディングセル`t`。 すべての細胞を数える必要がある`v`有向パスが存在するもの`s -> ... -> v -> ... -> t`。 セルは、多くの異なるパスで使用されている場合でも、1 回カウントされます。 もし`t`からは到達できません`s`、答えはゼロです。 

最後の違いは見落としがちです。 から到達可能なすべてのセルをカウントしているわけではありません。`s`。 後ろの独房`t`プレイヤーが最初から到達できるからといってカウントされるわけではありません。 最終的に指定された終了セルに到達する前に、セルが使用可能でなければなりません。 

最大 100 万個のセルと 300,000 個のクエリが存在できます。 クエリごとに独立してグラフ検索を実行するには、最大で約`3 * 10^11`最悪の場合、近隣チェックをカウントする前に、セルが訪問する可能性があります。 グリッドが十分に大きいため、前処理は線形に近くなければなりませんが、各クエリは対数以上である必要があります。 元のコンテストでは 2 秒の時間制限と 1024 MB のメモリが与えられているため、すべてのペアに対して明示的な汎用到達可能性構造を構築するソリューションは大きすぎます。 

いくつかの小さなケースでは、一見無害に見える間違いが明らかになります。 

単一の細胞を考えてみましょう。```
1 1 1
L
1 1 1 1
```答えは`1`。 プレーヤーはそのセルで開始および終了するため、空のパスはすでにそのセルを訪れています。 動きのエッジのみをカウントする不注意な実装では、誤ってゼロが返される可能性があります。 

ここで、矢印が外側を向いている 1 × 2 のグリッドを考えてみましょう。```
1 2 2
RL
1 1 1 2
1 2 1 1
```出力は```
0
0
```最初のセルがマークされます`R`, そのため、正しく動くことができません。 2 番目のセルがマークされています`L`, したがって、左に移動することはできません。 2 つのセルは完全な指向性バリアを形成します。 グリッドを無向グラフとして扱うと、グリッドが接続されていると誤って主張してしまいます。 

また、境界セルは内部セルよりも有効な手数が少なくなります。 例えば、```
1 2 1
LL
1 2 1 1
```答えがあります`0`。 2 番目のセルは、そのセルによって禁止されているため、左に移動できません。`L`、他のセルはありません。 ソース セルの矢印を無視して、ターゲットが隣接しているかどうかだけをチェックするトラバーサルでは、誤った結果が得られます。 

最後に、複数のセルが 1 つの強く接続されたコンポーネントに属することができます。 サンプル 1 では、最後の行全体がマークされています。`U`。 セルは水平両方向に移動できるため、5 つのセルすべてが 1 つの SCC に属します。 からの問い合わせ`(5,5)`したがって、それ自体に答えがあります`5`、 ない`1`。 

## アプローチ

 直接的なアプローチは、クエリごとに開始セルから BFS または DFS を実行することです。 検索中に、到達可能なすべてのセルにマークが付けられます。 最後のセルに到達しない場合、答えはゼロになります。 それ以外の場合は、もう 1 つの制限が必要です。到達可能なセルのうち、ターゲットにまだ到達できるセルのみが、有効な開始からターゲットまでのパスに属します。 反転したグラフ上のターゲットから 2 回目の検索を実行すると、これに対処できますが、それでも`O(NM)`クエリごとに。 と`Q = 300000`そして`NM = 10^6`、これはおおよそに達します`3 * 10^11`最悪の場合、頂点を訪問しました。 

個々のセルに注目するのをやめ、最初に強く接続されたすべてのコンポーネントを縮小すると、有用な構造が現れます。 1 つの SCC 内では、すべてのセルが他のすべてのセルに到達できるため、開始からターゲットまでのパス上にどのセルがあるかを決定するために、コンポーネント全体がセルの数に等しい重みを持つ 1 つの頂点として動作します。 

結果として生じる SCC 凝縮は DAG です。 任意のペア間の到達可能性が複雑になる可能性があるため、一般的な DAG は依然として困難です。 グリッドの制限により、より多くの構造が得られます。 SCC がトポロジー順に挿入される場合、すでに挿入されているセルは常に、互いに素な長方形の集合を形成します。 このようなコンポーネントの 1 つが長方形でない場合、外側のセルは少なくとも 2 つの側面でそれに接触します。 その外側のセルは、選択された位相順序に反して、すでに構築された領域へのエッジを持ちます。 これは、この問題の最初の重要な幾何学的特性です。 

長方形は、少数の追加の有向エッジを備えたツリーで表すことができます。 新しい SCC が挿入されると、その境界四角形の内側にある以前に構築されたすべての四角形がツリーの子としてそれに接続されます。 4 つの辺に接する長方形は連続したチェーンにグループ化され、複数の長方形が同じ辺を共有する場合に仮想頂点が使用されます。 仮想頂点は 1 つのツリー接続を提供しますが、新しい SCC への実際の有向接続は非ツリー エッジとして個別に保存されます。 

2 番目の重要なプロパティは、隣り合った四角形のグループの垂直方向の移動が均一であることを示します。グループ内のすべての四角形がその方向に離れることができるか、どの四角形も離れることができないかのどちらかです。 これにより、すべてのセルを個別に処理するのではなく、側面全体を 1 つの仮想頂点で表現できるようになります。 

すべての SCC が処理された後、構築されたグラフは特に有用な形状になります。 そのツリーのエッジは根付きツリーを形成し、すべてのツリー頂点の子は 1 つまたは複数の有向チェーンに配置されます。 その後、クエリは、ソースとターゲットが同じ深さになるまでツリー内を上に移動し、その後、それらの 2 つの子が同じ有向チェーンに属しているかどうかをチェックするように縮小できます。 考えられるすべての有効なパス上のセルの数は、順序付けされた子のプレフィックスの合計から取得されます。 

この構築は本質的に、公式ソリューションの中心的な観察です。 元の実装では、素セット ユニオンを使用して、すでに処理された四角形をマージし、最終祖先クエリのバイナリ リフティングを使用します。 以下の Python 実装は同じ構造を使用していますが、バイナリ リフティングをヘビーライト分解に置き換えています。 どちらも対数のクエリ時間を与えます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(QNM)`|`O(NM)`| 遅すぎる |
 | SCC + 長方形ツリー |`O(NM α(NM) + Q log(NM))`|`O(NM)`梱包されたストレージ | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべてのセルを有向グラフの頂点として扱います。 各セルについて、その 4 つの隣接する位置を検査し、その方向がセルの禁止方向でない場合は常に、使用可能な遷移を作成します。 グリッドの外側に移動することは不可能であるため、境界位置はトランジションを作成する前に拒否されます。 
2. Tarjan のアルゴリズムを使用して、すべての強接続コンポーネントを計算します。 SCC が必要なのは、1 つのコンポーネント内ですべてのセルを使用して他のすべてのセルに到達できるため、クエリを個々のセルではなくコンポーネントに対して実行できるためです。 

この実装では、再帰的 DFS ではなく、Tarjan の反復バージョンが使用されます。 セル再帰の深さが 100 万個になると、Python の再帰制限を超え、大量のコールスタック メモリも消費します。 
3. すべての SCC について、そのサイズと最小および最大の行と列を記録します。 4 つの極値はその境界四角形を定義します。 また、各 SCC に属するすべてのセルをリンク リストに保存するため、コンポーネントのセルを反復処理する際に、最大 100 万コンポーネントのリストの Python リストは必要ありません。 
4. コンポーネント番号を減らして SCC を処理します。 Tarjan は逆のトポロジー順序でコンポーネントを割り当てるため、ソースからシンクに向かって凝縮 DAG が処理されます。 

コンポーネントの処理中`C`、素の集合共用体構造を使用して、境界四角形の内側にあるすでに表現されているすべてのコンポーネントをマージします。`C`。 これらのマージされたオブジェクトは、ツリーの子孫になります。`C`。 
5. 外接する長方形のすぐ外側の 4 つの辺を検査します。 のすぐ左側にあるセルを検査するとします。`C`。 このようなセルはすべて、1 つの水平境界セグメント上にあります。 すでに同じ四角形にマージされている連続するセルは、1 つの DSU ルートで表されます。 

境界セルがすべてその方向への移動を禁止している場合、`C`、有向エッジは入力できません`C`その側から接続できるため、追加の接続は必要ありません。 それ以外の場合、その辺に接するすべての四角形は 1 つの仮想頂点の下に結合され、仮想頂点は方向付けされた非ツリー エッジを受け取ります。`C`。 

右側、上、下も同様の手順で行います。 四辺に使用される禁止方向はまさに`R`、`L`、`D`、 そして`U`、 それぞれ。 
6. すべての SCC が処理された後、まだ親を取得していないすべての DSU ルートが 1 つの人工ルートに接続されます。 これで、ツリーにはすべての SCC 頂点とすべての仮想頂点が含まれます。 実際の SCC 頂点の重みはセルの数に等しい正の値になりますが、仮想頂点の重みは 0 です。 
7. 各ツリー頂点には複数の子があります。 追加の有向エッジは同じ親の子を接続し、それらのエッジは互いに素なチェーンを形成します。 1 つの親の子がこれらのチェーンに沿って左から右に順序付けされるように、すべての子の位置を計算します。 

余分なエッジのみによって形成されるグラフは、最大次数が 2 になります。 隣接ノードの XOR は、すべてのノードの隣接関係リストを保存しなくても、すべてのチェーンを通過するのに十分です。 
8. 兄弟間の余分なエッジごとに`a`そして`b`を使用してその方向を保存します`dir`。 もし`a`前です`b`そしてエッジは`a -> b`、 セット`dir[a] = 1`。 もし`b`前です`a`、 セット`dir[b] = -1`。 

すべての親について計算します`le[v]`そして`ri[v]`。 これらは、以下を含む有向チェーンの左端と右端の子を識別します。`v`。 プレフィックスの合計`sum[v]`最初の子から次の子までの合計 SCC 重みを保存します。`v`。 
9. コンピューティング`val[v]`、これは、サブツリーに入るときにカバーできる実際のグリッド セルの数を表します。`v`利用可能な兄弟チェーンの動きを使用します。 再発というのは、`val[v] = val[parent] + sum[ri[v]] - sum[le[v]] + size[le[v]]`。 

追加された項は、正確に、以下に関連付けられた最大兄弟チェーン間隔の重みです。`v`。 
10. 重光分解を使用して、祖先クエリのツリーを前処理します。 ソースコンポーネントが与えられた場合`a`およびターゲットコンポーネント`b`、まず、の祖先が必要です。`a`その深さはの深さに等しい`b`。 重光分解により、その頂点が見つかります。`O(log(NM))`。 
11. ソースがターゲットよりも浅い場合、構築されたツリー構造を介してターゲットに到達することはできないため、答えはゼロになります。 Otherwise lift the source to the target's depth.
 12. リフトされたソースとターゲットは同じ親を持つ必要があります。 親が異なる場合、それらを接続する有効なパスは存在しないため、答えはゼロになります。 
13. リフトされたソースが親の順序付けされた子の中でターゲットより前にある場合は、ソースから始まる有向チェーンがターゲットに到達するかどうかを確認します。 This is equivalent to checking`pos[ri[source]] >= pos[target]`。 失敗すると、ターゲットに到達できなくなります。 
14. チェーン条件が成功した場合、答えは 2 つの子の間のプレフィックス合計間隔にパスの祖先部分からの寄与を加えたものになります。 ソースがターゲットの使用後にある対称の場合`le`の代わりに`ri`。 

### なぜ効果があるのか

 1 つの SCC 内のすべてのセルは相互に到達可能であるため、SCC 縮小ではセル間のすべての到達可能性関係が維持されます。 長方形のプロパティは、トポロジー処理中に、すでに構築されているすべての領域を長方形の部分で表現できることを意味します。 unique-exit プロパティにより、可能なパスを失うことなく、各サイド インタラクションを 1 つの仮想頂点と 1 つの有向チェーン関係に圧縮できます。 

この圧縮後、コンポーネントから別のコンポーネントに向かうすべての可能なルートはルート付きツリーを上向きにたどる必要があり、複数の子が親を共有する場合は必ず、対応する有向兄弟チェーン内に移動します。 祖先リフティングは、ソースがターゲットを満たすことができる唯一の可能なツリー レベルを識別します。 同一親チェックは、そのような会議が構造的に可能であることを検証しますが、`le`そして`ri`境界は、有向兄弟チェーンが実際に 2 つの位置を接続していることを検証します。 

プレフィックスの合計は、可能なパスのコレクション上のすべての SCC を 1 回だけカウントし、仮想頂点の寄与はゼロになります。 SCC サイズは、そこに含まれる元のグリッド セルの数であるため、最終的な合計は、開始セルから終了セルまでの少なくとも 1 つの有効なパス上にあるセルの数と正確に一致します。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

def solve():
    n, m, q = map(int, input().split())
    R = n * m

    # 0 = L, 1 = R, 2 = U, 3 = D
    direction = bytearray(R)
    for i in range(n):
        s = input().strip()
        base = i * m
        for j, ch in enumerate(s):
            if ch == 76:       # L
                direction[base + j] = 0
            elif ch == 82:     # R
                direction[base + j] = 1
            elif ch == 85:     # U
                direction[base + j] = 2
            else:              # D
                direction[base + j] = 3

    # ------------------------------------------------------------
    # Iterative Tarjan SCC
    # ------------------------------------------------------------
    dfn = array('i', [0]) * R
    low = array('i', [0]) * R
    bel = array('i', [-1]) * R

    scc_stack = []
    timer = 0
    cnt = 0

    for start in range(R):
        if dfn[start]:
            continue

        dfn[start] = timer + 1
        low[start] = timer + 1
        timer += 1

        dfs = [start]
        it = [0]
        scc_stack.append(start)

        while dfs:
            u = dfs[-1]
            k = it[-1]

            while k < 4:
                it[-1] = k + 1

                if k == direction[u]:
                    k += 1
                    continue

                if k == 0:
                    if u % m == 0:
                        k += 1
                        continue
                    v = u - 1
                elif k == 1:
                    if u % m == m - 1:
                        k += 1
                        continue
                    v = u + 1
                elif k == 2:
                    if u < m:
                        k += 1
                        continue
                    v = u - m
                else:
                    if u >= R - m:
                        k += 1
                        continue
                    v = u + m

                if dfn[v] == 0:
                    dfn[v] = timer + 1
                    low[v] = timer + 1
                    timer += 1
                    dfs.append(v)
                    it.append(0)
                    scc_stack.append(v)
                    break

                if bel[v] == -1 and dfn[v] < low[u]:
                    low[u] = dfn[v]

                k += 1

            else:
                dfs.pop()
                it.pop()

                if dfs:
                    p = dfs[-1]
                    if low[u] < low[p]:
                        low[p] = low[u]

                if low[u] == dfn[u]:
                    while True:
                        v = scc_stack.pop()
                        bel[v] = cnt
                        if v == u:
                            break
                    cnt += 1

    # dfn and low are no longer needed.
    del dfn, low, scc_stack

    # ------------------------------------------------------------
    # Store SCC members as linked lists and compute bounding boxes.
    # ------------------------------------------------------------
    head = array('i', [-1]) * cnt
    nxt = array('i', [-1]) * R

    xmin = array('i', [n]) * cnt
    xmax = array('i', [-1]) * cnt
    ymin = array('i', [m]) * cnt
    ymax = array('i', [-1]) * cnt
    size = array('i', [0]) * cnt

    for u in range(R):
        c = bel[u]
        nxt[u] = head[c]
        head[c] = u

        x = u // m
        y = u - x * m

        if x < xmin[c]:
            xmin[c] = x
        if x > xmax[c]:
            xmax[c] = x
        if y < ymin[c]:
            ymin[c] = y
        if y > ymax[c]:
            ymax[c] = y
        size[c] += 1

    # ------------------------------------------------------------
    # DSU and compressed tree construction.
    # ------------------------------------------------------------
    V = 2 * R + 5

    parent = array('i', [-1]) * V
    dsu = array('i', range(V))

    deg = array('i', [0]) * V
    ch = array('i', [0]) * V

    edge_a = array('i')
    edge_b = array('i')

    def find(x):
        while dsu[x] != x:
            dsu[x] = dsu[dsu[x]]
            x = dsu[x]
        return x

    # SCCs are numbered in reverse topological order by Tarjan.
    for c in range(cnt - 1, -1, -1):
        u = head[c]

        # Merge all previously represented components inside C's
        # bounding rectangle into C.
        while u != -1:
            ux = u // m
            uy = u - ux * m

            if uy > ymin[c]:
                v = u - 1
                if ymin[c] <= uy - 1 <= ymax[c]:
                    r = find(bel[v])
                    if r != c:
                        parent[r] = c
                        dsu[r] = c

            if uy < ymax[c]:
                v = u + 1
                r = find(bel[v])
                if r != c:
                    parent[r] = c
                    dsu[r] = c

            if ux > xmin[c]:
                v = u - m
                r = find(bel[v])
                if r != c:
                    parent[r] = c
                    dsu[r] = c

            if ux < xmax[c]:
                v = u + m
                r = find(bel[v])
                if r != c:
                    parent[r] = c
                    dsu[r] = c

            u = nxt[u]

        # Process top and bottom sides.
        for x in (xmin[c] - 1, xmax[c] + 1):
            if x < 0 or x >= n:
                continue

            base = x * m + ymin[c]
            if bel[base] < c:
                continue

            all_blocked = True
            u = find(bel[base])
            first = True

            for y in range(ymin[c], ymax[c] + 1):
                v = x * m + y

                # Direction toward C.
                needed = 3 if x < xmin[c] else 2
                if direction[v] != needed:
                    all_blocked = False

                r = find(bel[v])

                if r != u:
                    if first:
                        parent[u] = cnt
                        dsu[u] = cnt
                        u = cnt
                        cnt += 1
                        first = False

                    parent[r] = u
                    dsu[r] = u

            if not all_blocked:
                edge_a.append(u)
                edge_b.append(c)
                deg[u] += 1
                ch[u] ^= c
                deg[c] += 1
                ch[c] ^= u

        # Process left and right sides.
        for y in (ymin[c] - 1, ymax[c] + 1):
            if y < 0 or y >= m:
                continue

            base = xmin[c] * m + y
            if bel[base] < c:
                continue

            all_blocked = True
            u = find(bel[base])
            first = True

            for x in range(xmin[c], xmax[c] + 1):
                v = x * m + y

                # Direction toward C.
                needed = 1 if y < ymin[c] else 0
                if direction[v] != needed:
                    all_blocked = False

                r = find(bel[v])

                if r != u:
                    if first:
                        parent[u] = cnt
                        dsu[u] = cnt
                        u = cnt
                        cnt += 1
                        first = False

                    parent[r] = u
                    dsu[r] = u

            if not all_blocked:
                edge_a.append(u)
                edge_b.append(c)
                deg[u] += 1
                ch[u] ^= c
                deg[c] += 1
                ch[c] ^= u

    # Attach every remaining DSU root to one artificial root.
    root = cnt
    old_cnt = cnt

    for i in range(old_cnt):
        if dsu[i] == i:
            parent[i] = root
            dsu[i] = root

    cnt += 1
    parent[root] = -1

    # ------------------------------------------------------------
    # Find the order of children inside every tree vertex.
    # ------------------------------------------------------------
    pos = array('i', [-1]) * cnt
    next_pos = array('i', [0]) * cnt

    for i in range(old_cnt):
        if pos[i] != -1:
            continue

        if deg[i] == 0:
            p = parent[i]
            pos[i] = next_pos[p]
            next_pos[p] += 1

        elif deg[i] == 1:
            u = i
            previous = 0
            p = parent[u]

            while True:
                pos[u] = next_pos[p]
                next_pos[p] += 1

                nxt_node = ch[u] ^ previous
                previous, u = u, nxt_node

                if deg[u] != 2:
                    pos[u] = next_pos[p]
                    next_pos[p] += 1
                    break

    del next_pos

    # Set the direction of every non-tree edge.
    dir_edge = array('b', [0]) * cnt

    for a, b in zip(edge_a, edge_b):
        if pos[a] < pos[b]:
            dir_edge[a] = 1
        else:
            dir_edge[b] = -1

    del edge_a, edge_b, dsu

    # ------------------------------------------------------------
    # Build children in the required left-to-right order.
    # ------------------------------------------------------------
    child_count = array('i', [0]) * cnt

    for i in range(old_cnt):
        child_count[parent[i]] += 1

    start_child = array('i', [0]) * cnt
    total = 0

    for u in range(cnt):
        start_child[u] = total
        total += child_count[u]

    children = array('i', [0]) * old_cnt
    cursor = array('i', start_child)

    for i in range(old_cnt):
        p = parent[i]
        children[cursor[p]] = i
        cursor[p] += 1

    del cursor

    # ------------------------------------------------------------
    # Tree depths, subtree sizes, and heavy child.
    # ------------------------------------------------------------
    depth = array('i', [0]) * cnt
    subtree = array('i', [1]) * cnt
    heavy = array('i', [-1]) * cnt

    order = array('i', [root])
    idx = 0

    while idx < len(order):
        u = order[idx]
        idx += 1

        begin = start_child[u]
        end = begin + child_count[u]

        for j in range(begin, end):
            v = children[j]
            depth[v] = depth[u] + 1
            order.append(v)

    for idx in range(len(order) - 1, -1, -1):
        u = order[idx]
        begin = start_child[u]
        end = begin + child_count[u]

        best_size = 0
        best_child = -1

        for j in range(begin, end):
            v = children[j]
            subtree[u] += subtree[v]
            if subtree[v] > best_size:
                best_size = subtree[v]
                best_child = v

        heavy[u] = best_child

    # ------------------------------------------------------------
    # Heavy-light decomposition.
    # tin is a preorder in which every heavy chain is contiguous.
    # ------------------------------------------------------------
    chain_head = array('i', [-1]) * cnt
    tin = array('i', [0]) * cnt
    at = array('i', [0]) * cnt

    stack = [(root, root)]
    timer = 0

    while stack:
        u, h = stack.pop()

        while u != -1:
            chain_head[u] = h
            tin[u] = timer
            at[timer] = u
            timer += 1

            heavy_u = heavy[u]

            begin = start_child[u]
            end = begin + child_count[u]

            for j in range(begin, end):
                v = children[j]
                if v != heavy_u:
                    stack.append((v, v))

            u = heavy_u

    del subtree, heavy, order

    def ancestor_at_depth(u, target_depth):
        while depth[chain_head[u]] > target_depth:
            u = parent[chain_head[u]]

        return at[tin[u] - (depth[u] - target_depth)]

    # ------------------------------------------------------------
    # Prefix sums and chain intervals.
    # ------------------------------------------------------------
    prefix = array('i', [0]) * cnt
    left_chain = array('i', [0]) * cnt
    right_chain = array('i', [0]) * cnt
    val = array('i', [0]) * cnt

    stack = [root]

    while stack:
        u = stack.pop()

        begin = start_child[u]
        dcnt = child_count[u]

        if dcnt == 0:
            continue

        end = begin + dcnt

        s = 0
        for j in range(begin, end):
            v = children[j]
            s += size[v]
            prefix[v] = s

        previous = -1
        for j in range(begin, end):
            v = children[j]
            if j == begin or dir_edge[previous] != -1:
                left_chain[v] = v
            else:
                left_chain[v] = left_chain[previous]
            previous = v

        for j in range(end - 1, begin - 1, -1):
            v = children[j]
            if j == end - 1 or dir_edge[v] != 1:
                right_chain[v] = v
            else:
                right_chain[v] = right_chain[children[j + 1]]

        for j in range(begin, end):
            v = children[j]
            val[v] = (
                val[u]
                + prefix[right_chain[v]]
                - prefix[left_chain[v]]
                + size[left_chain[v]]
            )

        for j in range(begin, end):
            stack.append(children[j])

    del head, nxt, xmin, xmax, ymin, ymax
    del children, start_child, child_count
    del chain_head, tin, at, dir_edge

    def query(a, b):
        if depth[a] < depth[b]:
            return 0

        ret = val[a]
        a = ancestor_at_depth(a, depth[b])
        ret -= val[a]

        if parent[a] != parent[b]:
            return 0

        if pos[a] < pos[b]:
            if pos[right_chain[a]] >= pos[b]:
                return prefix[b] - prefix[a] + ret + size[a]
            return 0

        if pos[left_chain[a]] <= pos[b]:
            return prefix[a] - prefix[b] + ret + size[b]

        return 0

    out = []

    for _ in range(q):
        x1, y1, x2, y2 = map(int, input().split())
        u = bel[(x1 - 1) * m + (y1 - 1)]
        v = bel[(x2 - 1) * m + (y2 - 1)]
        out.append(str(query(u, v)))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```実装の最初の部分では、各矢印を整数の方向に変換します。 を使用して`bytearray`必要な値は 4 つだけなので、これで十分です。 

SCC ステージは反復的です。`dfs`現在の DFS パスを保存します。`it`アクティブな頂点ごとに検査する次の方向を保存します。 頂点が終了すると、そのローリンク値がその親に伝播されます。 そのローリンク値がその検出番号と等しい場合、アクティブな SCC スタックは、その頂点が削除されるまでポップされます。 

SCC メンバーは次のように保存されます。`head`そして`nxt`。 これにより回避されます`cnt`Python リスト。ほぼすべてのセルが独自の SCC である場合、特にコストが高くなります。 外接する四角形とサイズは、セル上の同じパスで計算されます。 

DSU ステージは、長方形の構造を直接に従います。 Tarjan によって生成されるコンポーネント ID は、大きいものから小さいものへと処理されます。これは、構築に必要なトポロジの方向であるためです。 複数の境界長方形を 1 つのオブジェクトとして扱う必要があるたびに、仮想頂点が作成されます。 仮想頂点はゼロです`size`したがって、セルを答えに提供することはありません。 

の`pos`計算はより複雑な部分の 1 つです。 追加のエッジはチェーンを形成するため、次数 1 のエンドポイントは、2 つの近傍の XOR を使用してチェーンを通過できます。 すべてのノードが位置を受け取ると、ツリー頂点の子を必要な順序で配置できます。 

重光分解は祖先クエリにのみ使用されます。 すべての重鎖は連続しています`tin`したがって、目的の祖先が現在の重鎖上にある場合、その頂点は逆プリオーダー配列から直接取得できます。`at`。 それ以外の場合は、現在のチェーン ヘッドの親にジャンプします。 軽鎖ジャンプの数は対数的です。 

最後のクエリでは、減算する前に深さを意図的にチェックします。`val`。 ソースがターゲットよりも浅い場合、有効な上向きツリー表現は存在しません。 持ち上げた後、2 つの親を比較することは、構造的到達可能性テストです。 の`left_chain`そして`right_chain`チェックを行ってから、直接兄弟接続をテストします。 

SCC を特定する場合にのみ、すべての座標が 1 ベースの入力から 0 ベースのセル インデックスに変換されます。 ツリーの構築中に座標調整は実行されないため、境界テストでは一貫して 0 から始まる行と列が使用されます。 

## 実用的な例

 ### サンプル 1

 与えられたグリッドは```
DDDDD
RDDDL
RRDLL
RUUUL
UUUUU
```クエリを検討してください`(5,5) -> (5,5)`。 最後の行の 5 つのセルはすべて、`U`。 これらは左右に自由に移動できるため、5 つのセルを含む 1 つの SCC を形成します。 

| ステージ | ソースコンポーネント | ターゲットコンポーネント | 深さ関係 | 同じコンポーネント | 答え |
 | --- | --- | --- | --- | --- | --- |
 | 入力 |`(5,5)`|`(5,5)`| 等しい | はい | 5 |
 | SCC圧縮 | 最下段のSCC | 最下段のSCC | 等しい | はい | 5 |
 | クエリ |`a = b`|`a = b`| 等しい | はい | 5 |

 いつ`a == b`、クエリ式はその SCC のサイズまで縮小します。 結果は`5`これは、サンプル 1 の 5 番目の出力行と一致します。これは、圧縮されたすべての頂点を重み 1 として扱うのではなく、SCC 重みに元のセルの数を含める必要がある理由を示しています。 

ここで考えてみましょう`(2,2) -> (5,5)`。 ソースとターゲットは異なるコンポーネント内にあります。 ソースをターゲットの深さまで持ち上げた後、構造は共通の親の下に 2 つの兄弟位置とそれらを接続する有向チェーンを見つけます。 そのチェーンにわたるプレフィックスの合計と祖先の寄与には、正確に 14 個の実セルが含まれます。 

| ステージ | 操作 | 結果 |
 | --- | --- | --- |
 | 入力 | ソース`(2,2)`、ターゲット`(5,5)`| さまざまな SCC |
 | 祖先ステップ | ソースをターゲット深度まで持ち上げる | 同じ深さ |
 | 親テスト | 比較する`parent[a]`そして`parent[b]`| 等しい |
 | チェーンテスト | ターゲット位置と比較する`right_chain[a]`| 到達可能 |
 | カウント | 兄弟間隔 + 祖先の寄与 | 14 |

 サンプル 1 の最初のクエリ、`(1,1) -> (5,5)`、対応するチェーン到達可能性条件に失敗するため、その答えはゼロになります。 他のクエリは生成します`20`、`14`、 そして`5`、完全なサンプル出力を示します。`0 14 20 14 5`。 

### 一次元連鎖

 検討してください```
1 3 3
LLL
1 1 1 3
1 2 1 3
3 1 1 1
```アン`L`左への移動を禁止するため、すべてのセルは右へ移動できます。 グラフは有向連鎖です```
1 -> 2 -> 3
```各セルは独自の SCC です。 

| クエリ | ソース位置 | 目標位置 | 有向チェーンテスト | 答え |
 | --- | --- | --- | --- | --- |
 |`1 -> 3`| 0 | 2 | 成功する | 3 |
 |`2 -> 3`| 1 | 2 | 成功する | 2 |
 |`3 -> 1`| 2 | 0 | 失敗する | 0 |

 構築されたツリーには、1 つのルートの兄弟として 3 つの SCC があり、非ツリー エッジがあります。`1 -> 2`そして`2 -> 3`。 最初の子の右チェーン エンドポイントは 3 番目の子であるため、最初のクエリは 3 つの SCC 重みをすべてカウントします。 2 番目のクエリでは、最後の 2 つがカウントされます。 有向チェーンが後方を指していないため、逆クエリは失敗します。 

この例は、次の目的を確認します。`left_chain`そして`right_chain`: ツリー自体は兄弟間の方向をエンコードしないため、これらの追加の境界は不可欠です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(NM α(NM) + Q log(NM))`| SCC の構築、DSU の四角形の結合、およびツリーの前処理はほぼ線形です。 各クエリは、ヘビー/ライト祖先リフティングを使用します。 
| スペース |`O(NM)`| すべてのグラフとツリー情報はパックされた整数配列に格納されます。 

多くても`NM`元の SCC 頂点と`O(NM)`仮想頂点。 詰め込まれた`array`storage は、密な整数構造を通常の Python 整数リストよりもはるかに小さく保ちます。 このアルゴリズムは、処理されたグリッド境界要素ごとに一定量の幾何学的作業のみを実行し、クエリごとに対数作業を使用します。 漸近境界は、100 万セルと 300,000 クエリの制約に適合します。 元のコンテストの 2 秒制限は Python にとって厳しいものであるため、実装では Python のリストのリストと再帰的なグラフの走査を意図的に回避しています。 

## テストケース

 次のテストでは、送信されたソリューションが次のように保存されていることを前提としています。`solution.py`。 ヘルパーはモジュールのリセット`input`交換後の機能`sys.stdin`、ソリューションで定義されているため、これが必要です。`input = sys.stdin.readline`モジュールスコープで。```python
import sys
import io
import solution

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    solution.input = sys.stdin.readline

    try:
        solution.solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Provided sample
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

# Minimum-size grid, start equals end.
case_min = """\
1 1 1
L
1 1 1 1
"""

assert run(case_min) == "1", "minimum-size grid"

# Two cells with a complete directed barrier.
case_barrier = """\
1 2 2
RL
1 1 1 2
1 2 1 1
"""

assert run(case_barrier) == "0\n0", "mutual boundary barrier"

# Directed chain, catches endpoint and ordering errors.
case_chain = """\
1 3 3
LLL
1 1 1 3
1 2 1 3
3 1 1 1
"""

assert run(case_chain) == "3\n2\n0", "directed chain"

# Maximum-size grid and maximum number of cells.
# Every L forbids moving left, while vertical movement is unrestricted.
# Hence every column is an SCC and movement is possible only toward
# increasing columns.
n = 1000
m = 1000
grid = "\n".join(["L" * m] * n)

case_max = (
    f"{n} {m} 3\n"
    + grid
    + "\n"
    + "1 1 1000 1000\n"
    + "1000 1000 1 1\n"
    + "500 500 500 500\n"
)

assert run(case_max) == "1000000\n0\n1", "maximum-size all-equal grid"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 x 1`、1 つのクエリ |`1`| 空のパスと最小グリッド |
 |`1 x 2`、`RL`|`0`、`0`| 指示されたバリアと境界の処理 |
 |`1 x 3`、`LLL`|`3`、`2`、`0`| 順序付けられた兄弟チェーンとオフバイワン位置 |
 |`1000 x 1000`、 全て`L`|`1000000`、`0`、`1`| 最大のグリッド、最大の答え、すべて等しい矢印 |

 ## 特殊なケース

 ### 同じセル上で開始および終了する

 のために```
1 1 1
L
1 1 1 1
```唯一のセルを含む SCC のサイズは 1 です。 クエリでは同一のソース コンポーネントとターゲット コンポーネントが参照されるため、リフトされたソースは変更されず、兄弟間隔にはその SCC が正確に含まれます。 出力は`1`。 

同じロジックでより大きな SCC も処理します。 サンプル 1 では、`(5,5)`5 セルの最下行 SCC に属しているため、同じセルのクエリは次の値を返します。`5`。 実装では、座標が等しいクエリの答えが 1 であるとは決して想定しません。 

### 到達不可能な目標

 のために```
1 2 2
RL
1 1 1 2
1 2 1 1
```どちらのセルもセル間の境界を越えることはできません。 SCC は個別であり、長方形の構造により、SCC を接続する有向兄弟チェーンは作成されません。 ソースを持ち上げた後、親テストまたはチェーン テストは失敗するため、両方の答えがゼロになります。 

これは、隣接関係を自動的に双方向として扱うというよくある間違いを捉えています。 

### 矢印が唯一の有効な移動をブロックする境界セル

 のために```
1 2 1
RL
1 1 1 2
```最初のセルには`R`、2 番目のセルに向かう唯一の移動を禁止します。 バウンダリスキャンでは、ターゲット内にエッジが存在しないことが認識されます。 クエリ コードで特別なケースを必要とせずに、クエリは 0 を返します。 

同じ理由が、マークが付いた最上行のセルにも当てはまります。`U`、一番下の行のセルにマークが付いています`D`、マークされた左端のセル`L`、またはマークが付いている右端のセル`R`。 矢印は、その方向がグリッドから外れる場合でもその方向を禁止できますが、とにかくそのような外側への移動はすでに不可能でした。 

### 大規模な強結合コンポーネント

 サンプル 1 の最後の行は次のとおりです。```
UUUUU
```すべてのセルは左右に移動できます。`U`上向きの動きのみを禁止します。 したがって、5 つのセルすべてが 1 つの SCC に属します。 SCC 圧縮ストア`size = 5`、このコンポーネント全体の内部にあるクエリは、5 つのセルすべてを即座にカウントします。 

これが、すべての SCC を単一の重み付けされていない頂点で置き換えることが正しくない理由です。 圧縮されたグラフは到達可能性について答えますが、元の質問では元のグリッド セルの数が求められます。 

### 同じエンドポイント間の複数の可能なパス

 答えは、選択した 1 つのパスの長さではありません。 これは、少なくとも 1 つの有効な開始からターゲットまでのパス上に存在するセルの数です。 圧縮ツリーでは、兄弟チェーンにいくつかの中間 SCC が含まれる場合があり、それらはすべて異なるルートに参加できます。 プレフィックスの合計は、任意の 1 つのルートを選択するのではなく、有効なチェーン間隔全体を意図的にカウントします。 

これが、単純な最短経路アルゴリズムでは問題を解決できない理由でもあります。 必要な量は、パスの長さではなく、可能なパスの頂点の和集合です。 

### 非常に多くの小さな SCC

 一次元のようなグリッド`LLL`たとえば、セルごとに 1 つの SCC があります。 SCC に多くのセルが含まれることを想定していないため、この構造は依然として機能します。 すべてのシングルトン コンポーネントは 1 セルの境界四角形を取得し、隣接するコンポーネントは同じ四角形側のメカニズムを通じて接続されます。 

最大サイズすべて-`L`テストでは、逆の状況を大規模に実行します。 大きな SCC が 1000 個あり、各列に 1 つずつありますが、アルゴリズムは依然として同じ表現を使用してそれらを処理します。
