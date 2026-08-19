---
title: "CF 102191K - サボテンポータル"
description: "グラフは、単純なサイクルの重み付けされたチェーンです。 頂点 1 から開始して頂点 n に向かって移動する各サイクルは、同じ 2 つのアーティキュレーション頂点を接続する 2 つの円弧の間で選択するように動作します。 これらのサイクルとは別に、グラフには通常のチェーン エッジが含まれています。"
date: "2026-08-18T09:41:43+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102191
codeforces_index: "K"
codeforces_contest_name: "PSUT Coding Marathon 2019"
rating: 0
weight: 102191
solve_time_s: 1129
verified: false
draft: false
---

[CF 102191K - カクタス ポータル](https://codeforces.com/problemset/problem/102191/K)

 **評価:** -
 **タグ:** -
 **解決時間:** 18 分 49 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 グラフは、単純なサイクルの重み付けされたチェーンです。 頂点 1 から開始して頂点 n に向かって移動する各サイクルは、同じ 2 つのアーティキュレーション頂点を接続する 2 つの円弧の間で選択するように動作します。 これらのサイクルとは別に、グラフには通常のチェーン エッジが含まれています。 頂点を共有する 2 つのサイクルはないため、これらの選択は順番に独立して発生します。 

1 から n に往復し、1 に戻る必要があります。往路のどこかの頂点 u で、ポータル タイマーを開始する場合があります。 その後、別の頂点 v に到達してポータルをアクティブにするまで、実際に歩くのは最大でも k 秒です。 一度有効にすると、u と v 間の移動のコストはゼロになります。 ポータルは往復中に使用できるため、n に到達した後は通常どおり v に戻り、v から u にテレポートできます。 

選択した 1 から n までの単純なパスに v の前に u が含まれているとします。このパスに沿った 1 から u までの距離を P、u から v までの距離を D、v から n までの距離を Q とします。 旅行費用の全額

 [
 P+D+Q+Q+0+P=2P+D+2Q。 
】

 ポータルの唯一の制限は D <= k です。 

入力には n 個の頂点と e 個の重み付けされたエッジが含まれます。 n が 300000 と大きく、制限が 2 秒であるため、頂点のすべてのペアを検査するアルゴリズムは遅すぎます。 Python では O(n sqrt n) ですら疑わしいため、目的の解決策は対数データ構造の演算とは別に、本質的に線形である必要があります。 エッジの重みは正で最大 1000 ですが、k は 10^8 まで大きくなる可能性があるため、k に対する有界状態動的計画法ではなく、正確な整数距離を使用する必要があります。 

実装を騙すことができる簡単なケースがいくつかあります。 

1 つ目は、v が n である必要はないということです。 例えば、```
2 1 4
1 2 2
```答えは 2 です。ポータルを頂点 1 で開始し、頂点 2 まで歩いてアクティブにし、ポータルを通って戻り、頂点 1 で終了します。より一般的には、より長いチェーンでは、ポータルをアクティブにした後に n に到達し、最後に戻ったときにのみポータルを使用できます。 n で終わるペアのみを考慮する実装では、有効な解決策が失われます。 

2 つ目は、最適なペアがサイクル内に存在し、その 2 つのアークのいずれかを使用できることです。 考慮する```
5 5 4
1 2 1
2 3 4
3 4 4
4 2 7
4 5 1
```通常の最短パスはウェイト 7 のエッジ 2 ～ 4 を使用し、往復は 18 になります。2 から 4 までのもう一方のアークにはウェイト 4 のエッジが 2 つあります。2 から開始し、4 秒で 3 まで歩き、3 から 4 に進み、次に 5 に進み、4 と 3 に戻り、最後に 3 から 2 のポータルを使用できます。合計は 16 です。すべてのサイクルをその最短のアークのみで置き換えるソリューションでは、これを見逃してしまいます。 可能性。 

3 番目はタイムアウト境界です。 と```
2 1 4
1 2 5
```答えは 5 ではなく 10 です。ポータルをアクティブにするには 5 秒歩く必要があり、k はわずか 4 であるためです。`<= k`の代わりに`< k`も不可欠です。 kが5の場合、答えは5になります。 

## アプローチ

 直接的な解決策では、最初のエンドポイント u を選択し、2 番目のエンドポイント v を選択し、1 から u および v から n への単純なパスを見つけ、対応する往復を評価します。 O(n^2) 個のペアがあり、サボテン内の距離は効率的に計算できますが、すべてのペアを明示的にチェックすると O(n^2) 回の作業が必要になります。 n = 300000 の場合、約 9 * 10^10 のペア チェックが行われることになり、これはまったく実行不可能です。 

有益な観察は、グラフが任意のサボテンではないということです。 そのサイクルが連鎖を形成します。 1 から n まで歩き、グラフをブロックのシーケンスとみなすことができます。各ブロックは 1 つの通常のエッジまたは 1 つのサイクルのいずれかです。 サイクルごとに、左のアーティキュレーション頂点から右のアーティキュレーション頂点までのパスは、2 つの円弧のうちの 1 つを正確に選択します。 

パスを固定的に選択する場合、ポータルのエンドポイントが u と v であるとします。コストは次のとおりです。 

[
 2P+D+2Q。 
】

 グラフ構造により、この式を u に属する寄与、v に属する寄与、およびそれらの間の距離制約に分割できます。 

u が前のブロックにあり、v が後のブロックにあると仮定します。 v のブロックの左アーティキュレーション頂点に到達したとき、選択したパスに沿った u からそのアーティキュレーションまでの距離を d とします。 この場合、アクティベーション距離は d+a になります。ここで、a はそのアーティキュレーションから現在のブロック内の v までの距離です。 費用は

 [
 (2P+d)+(a+2Q+2b)、
 】

 ここで、 b は v からそのブロックの右アーティキュレーションまでの残りの距離です。 

最初の括弧で囲まれた項は完全に前のエンドポイントに属します。 あるブロックから次のブロックに移動すると、すべての古い候補から現在のフロンティアまでの距離は、通過したばかりのブロックの最短の長さだけ増加します。 つまり、現在のフロンティアはすべての候補に同じ加算オフセットを与えながら、すべての候補を固定の変換座標で表すことができます。 

これにより、問題はプレフィックス最小クエリに変わります。 考えられるすべての最初のエンドポイント状態について、次の座標を保存します。`base`と呼ばれる値`value`。 現在の 2 番目のエンドポイントについては、しきい値を取得します。`base`、座標がそのしきい値以下である最小の保存値が必要です。 プレフィックス最小値を格納するフェンウィック ツリーは、まさにその操作を提供します。 

サイクルには追加の詳細が 1 つ必要です。 内部頂点は、そのサイクルを通る 2 つの可能な単純なパスのいずれかに属することができるため、各円弧に 1 つずつ、合計 2 つの状態があります。 2 つのアーティキュレーション頂点には、ブロック間の遷移の最短パス状態のみが必要です。 2 つの端点が同じブロック内にあるペアは、各円弧のエッジ上のスライディング ウィンドウによって個別に処理されます。 

すべてのペアを独立して評価できるため、ブルート フォースが機能します。 二次関数的に多くのペアがあるため、失敗します。 グラフが独立したエッジ ブロックとサイクル ブロックのシーケンスであるという観察により、左から右にスイープし、以前のすべてのエンドポイントを 1 つのプレフィックス最小構造に維持できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n²) | O(n + e) | 遅すぎる |
 | 最適 | O(n log n + e) | O(n + e) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 頂点 1 から頂点 n に向かって歩きながらグラフを分解します。 通常の次数 2 セクションはすべてエッジ ブロックです。 次数 3 の頂点に到達すると、入力チェーン エッジではない 2 つのエッジが 1 サイクルの 2 方向になります。 次の 3 度の関節頂点で交わるまで両方向に移動します。 これにより、サイクルの 2 つの円弧が得られます。 

このグラフ ファミリでは、サイクル アーティキュレーション頂点の次数は 3、通常のチェーン頂点とサイクル内部頂点の次数は 2、頂点 1 と n の次数は 1 です。これにより、汎用のブロック カット ツリーを使用せずに分解が可能になります。 
2. すべてのブロックについて、その左から右への最短の長さを計算します。 通常のエッジの場合、これは単にその重量です。 円弧長 L1 および L2 のサイクルの場合、最短のブロック長は min(L1,L2) です。 これらの長さを S0、S1、...、S(m-1) とします。 
3. 可能な最短の 1 対 n 距離を計算します。 

[
 T=\sum_i S_i.
 】

 有用なポータルがなければ、答えは 2T です。 この値で答えを初期化します。 
4. ブロック内の各頂点に 1 つまたは 2 つのパス状態を与えます。 状態は次のように表されます。`(a, b)`ここで、 a は選択した円弧に沿ったブロックの左アーティキュレーションから頂点までの距離、 b は同じ円弧に沿った頂点からブロックの右アーティキュレーションまでの距離です。 

長さ S の通常のエッジの場合、2 つの関節状態は次のようになります。`(0,S)`そして`(S,0)`。 サイクルの場合は、もう一度含めます。`(0,S)`そして`(S,0)`関節頂点の場合、S は最短のサイクル長です。 すべての内部頂点は、2 つの円弧のそれぞれから 1 つの状態を取得します。 
5. F を頂点 1 から現在のブロックの左アーティキュレーションまでの最短距離とする。 現在のブロックを最短ルートで横断した後の新しいフロンティアの距離は、`F + S`。 

州の場合`(a,b)`最初のポータル エンドポイントとして使用される場合、そのプレフィックス距離は 1 です。`F+a`。 選択した円弧を通ってブロックを離れると、現在のフロンティアまでの距離は次のようになります。`b`すべての最も短い中間ブロックを加えます。 
6. 最初のエンドポイント状態がそれ自身のブロックから次のフロンティアに移動するとき、

 [
 ベース=b-(F+S)。 
】

 フロンティア距離が F' である後続のブロックでは、このエンドポイントからフロンティアまでの実際の距離は次のようになります。 

[
 ベース+F'。 
】

 それに関連するコストの寄与は次のとおりです。 

[
 2(F+a)+(塩基+F')。 
】

 並べ替えると

 [
 \left(2(F+a)+base\right)+F'。 
】

 第 2 期は、現在の最前線にいるすべての候補者に共通です。 したがって、私たちは保管します`2(F+a)+base`によってインデックス付けされたフェンウィック ツリー内`base`。 
7. 現在の 2 番目のエンドポイントの状態の場合`(a,b)`、以前の最初の終点からその終点までの距離は、`base + F + a`。 ポータルは次の場合に使用できます。 

[
 ベース\le k-F-a。 
】

 この不等式を満たすすべての座標にわたって保存されている最小値をフェンウィック ツリーにクエリします。 その最小値が M の場合、完全な往復にはコストがかかります

 [
 M+F+a+2(b+Q)、
 】

 ここで、Q は現在のブロックの右アーティキュレーションから n までの最短距離です。 
8. 現在のブロックの状態をフェンウィック ツリーに挿入する前に、現在のブロックのすべての第 2 エンドポイント状態を処理します。 この順序により、両方のエンドポイントが実際に同じブロックに属している場合に、エンドポイントが厳密に以前のものであるかのように使用されることが防止されます。 
9. 同じブロック内のペアを個別に処理します。 通常のエッジの場合、可能な正のセグメントのみがそのエッジと同じ長さになります。 

サイクルの場合、その 2 つの円弧をそれぞれ独立して処理します。 1 つの円弧に沿った 2 つの頂点間の距離は、エッジ ウェイトの連続した間隔の合計です。 すべての重みが正であるため、2 ポインター ウィンドウは合計が最大でも k になる最大間隔を見つけます。 使用可能な最大セグメントの長さが D で、選択した円弧の長さが L の場合、このブロックを通る完全なパスの長さは次のようになります。`F + L + Q`したがって、結果の往復は次のようになります。 

[
 2(F+L+Q)-D.
 】
 10. 座標圧縮間隔`base`スイープ前の値。 フェンウィック ツリーは合計ではなく最小値を格納するため、新しい値が小さい場合にのみ更新によって位置が置き換えられます。 

### なぜ効果があるのか

 有効な単純な 1 対 n ルートはすべて、サイクルごとに 1 つのアークを正確に選択します。 2 つのポータル エンドポイントが異なるブロック内にある場合、それらの間のルートは、最初のエンドポイントが選択した円弧の残りの部分、完全に交差したすべてのブロックを通る最短ルート、および 2 番目のエンドポイントが選択した円弧の始まりで構成されます。 変身した`base`値は、スイープが進行する間固定されたままとなるこの距離の部分を正確に捕捉します。 Fenwick クエリは、アクティブ化距離の合計が最大でも k である以前の状態を正確に考慮し、その中から可能な最小の往復寄与を選択します。 

両方の端点が同じブロック内にある場合は、そのブロックの同じ選択された円弧上にある必要があります。 スライディング ウィンドウは、各円弧上のすべての連続する頂点間隔を検査し、長さが最大でも k である最長間隔を保持します。 ポータルは通常の往復からその間隔の長さを正確に保存するため、次の式は`2(F+L+Q)-D`同一ブロックの可能性をすべて評価します。 

したがって、すべての有効なポータル配置は、クロスブロックのフェンウィック クエリまたは同一ブロックのスライディング ウィンドウによって考慮され、生成されたすべての候補は有効な単純なパスに対応します。 最小値とポータルなしのベースラインを組み合わせると、最適値が得られます。 

## Python ソリューション```python
import sys
from bisect import bisect_left, bisect_right
from array import array

input = sys.stdin.readline

def solve():
    n, e, k = map(int, input().split())
    n0 = n - 1

    # Edge data. Using arrays keeps the graph representation compact.
    eu = array('i')
    ev = array('i')
    ew = array('i')

    # Each adjacency entry is an edge id.
    adj = [[] for _ in range(n)]

    for eid in range(e):
        u, v, w = map(int, input().split())
        u -= 1
        v -= 1
        eu.append(u)
        ev.append(v)
        ew.append(w)
        adj[u].append(eid)
        adj[v].append(eid)

    def other(eid, x):
        u = eu[eid]
        v = ev[eid]
        return v if u == x else u

    # Walk one direction around a cycle starting with first_eid.
    # The cycle is guaranteed to meet the chain again at a degree-3 vertex.
    def walk_cycle(start, first_eid):
        arc = []
        cur = start
        pe = first_eid

        while True:
            arc.append(pe)

            if eu[pe] == cur:
                nxt = ev[pe]
            else:
                nxt = eu[pe]

            if nxt != start and len(adj[nxt]) == 3:
                return arc, nxt

            # Every non-terminal vertex inside a cycle has degree 2.
            e0 = adj[nxt][0]
            e1 = adj[nxt][1]
            ne = e1 if e0 == pe else e0

            cur = nxt
            pe = ne

    # Blocks are:
    # (0, edge_id, edge_length)
    # (1, arc1_edge_ids, arc2_edge_ids, arc1_length, arc2_length)
    blocks = []

    cur = 0
    prev_eid = -1

    while cur != n0:
        deg = len(adj[cur])

        if deg == 2:
            e0 = adj[cur][0]
            e1 = adj[cur][1]
            eid = e1 if e0 == prev_eid else e0

            nxt = other(eid, cur)
            blocks.append((0, eid, ew[eid]))

            prev_eid = eid
            cur = nxt
            continue

        # At a degree-3 vertex, prev_eid is the incoming chain edge.
        starts = []
        for eid in adj[cur]:
            if eid != prev_eid:
                starts.append(eid)

        arc1, end1 = walk_cycle(cur, starts[0])
        arc2, end2 = walk_cycle(cur, starts[1])

        # Both arcs must reach the same right articulation vertex.
        end = end1

        len1 = 0
        for eid in arc1:
            len1 += ew[eid]

        len2 = 0
        for eid in arc2:
            len2 += ew[eid]

        blocks.append((1, arc1, arc2, len1, len2))

        # Leave the cycle through its unique non-cycle edge.
        cycle_edges = set(arc1)
        cycle_edges.update(arc2)

        out_eid = -1
        for eid in adj[end]:
            if eid not in cycle_edges:
                out_eid = eid
                break

        # The chain edge after the cycle is a separate block.
        nxt = other(out_eid, end)
        blocks.append((0, out_eid, ew[out_eid]))

        prev_eid = out_eid
        cur = nxt

    m = len(blocks)

    # Shortest left-to-right length of every block.
    shortest = [0] * m
    total = 0

    for i, block in enumerate(blocks):
        if block[0] == 0:
            s = block[2]
        else:
            s = block[3]
            if block[4] < s:
                s = block[4]

        shortest[i] = s
        total += s

    # Yield all path states (a, b) for a block.
    # a = distance from left articulation to endpoint
    # b = distance from endpoint to right articulation
    def states(block, s):
        if block[0] == 0:
            yield 0, s
            yield s, 0
            return

        arc1, arc2, len1, len2 = block[1], block[2], block[3], block[4]

        # Articulation states use the shortest way through the cycle.
        yield 0, s
        yield s, 0

        cur_dist = 0
        for j in range(len(arc1) - 1):
            cur_dist += ew[arc1[j]]
            yield cur_dist, len1 - cur_dist

        cur_dist = 0
        for j in range(len(arc2) - 1):
            cur_dist += ew[arc2[j]]
            yield cur_dist, len2 - cur_dist

    # Collect all transformed coordinates for coordinate compression.
    bases = []
    F = 0

    for i, block in enumerate(blocks):
        s = shortest[i]
        after = F + s

        for a, b in states(block, s):
            bases.append(b - after)

        F = after

    bases.sort()

    INF = 10**30
    size = len(bases)
    bit = [INF] * (size + 1)

    def update(pos, value):
        while pos <= size:
            if value < bit[pos]:
                bit[pos] = value
            pos += pos & -pos

    def query(pos):
        result = INF
        while pos > 0:
            value = bit[pos]
            if value < result:
                result = value
            pos -= pos & -pos
        return result

    answer = 2 * total
    F = 0

    for i, block in enumerate(blocks):
        s = shortest[i]
        after = F + s
        Q = total - after

        # First handle both endpoints inside this block.
        if block[0] == 0:
            w = block[2]
            if w <= k:
                candidate = 2 * (F + w + Q) - w
                if candidate < answer:
                    answer = candidate
        else:
            arc1 = block[1]
            arc2 = block[2]
            len1 = block[3]
            len2 = block[4]

            for arc, length in ((arc1, len1), (arc2, len2)):
                left = 0
                window = 0
                best = 0

                for right in range(len(arc)):
                    window += ew[arc[right]]

                    while window > k:
                        window -= ew[arc[left]]
                        left += 1

                    if window > best:
                        best = window

                if best > 0:
                    candidate = 2 * (F + length + Q) - best
                    if candidate < answer:
                        answer = candidate

        # Query all previous blocks as possible first endpoints.
        for a, b in states(block, s):
            threshold = k - F - a
            pos = bisect_right(bases, threshold)

            if pos == 0:
                continue

            best = query(pos)
            if best == INF:
                continue

            candidate = best + F + a + 2 * (b + Q)
            if candidate < answer:
                answer = candidate

        # Only after all queries do current states become previous states.
        for a, b in states(block, s):
            base = b - after
            pos = bisect_left(bases, base) + 1
            value = 2 * (F + a) + base
            update(pos, value)

        F = after

    print(answer)

if __name__ == "__main__":
    solve()
```The graph representation stores every undirected edge once in three compact arrays and keeps only edge IDs in the adjacency lists. This avoids storing two complete endpoint-weight tuples for every adjacency entry.

 The first traversal constructs the block chain directly from the degree pattern. At a degree-2 vertex there is only one edge that does not lead back to the previous vertex, so that edge is the next chain block. At a degree-3 vertex, the incoming chain edge is known, leaving exactly two cycle edges. Traversing those two edges independently recovers the two cycle arcs.

 の`states`ジェネレーターは、考えられるポータル エンドポイントの中心的な表現です。 内部サイクル頂点は各円弧上に 1 回発生するため、2 つの状態を受け取ります。 アーティキュレーション頂点は、ブロック間遷移の最短ブロック状態のみを受け取ります。 同じブロックのトランジションは個別に処理されるため、そこでは長いアーティキュレーション状態が不要になります。 

変換された座標`base = b - after`フェンウィックスイープの鍵となる。 At a later block with frontier distance F, the actual distance from that endpoint to the frontier is`base + F`。 したがって、タイムアウト条件は単純なプレフィックス条件になります。`base`。 

The Fenwick tree stores minima. その更新操作は標準的な点の更新を実行しますが、そのクエリは指定されたしきい値までのすべての圧縮座標の最小値を返します。 の使用`bisect_right`k に正確に等しいポータル距離が正当であるため、これは意図的なものです。 

すべての距離はおよそ 3 * 10^8 に達する可能性があり、中間式も Python 整数内に収まります。 特別なオーバーフロー処理は必要ありません。 

## 実用的な例

 ### サンプル 1

 グラフは 1 から 12 に移動すると次のブロックに分解されます。 

| ブロック | 構造 | 最短の長さ |
 | --- | --- | --- |
 | 1 | エッジ 1-2 | 2 |
 | 2 | サイクル 2 ～ 4、アーク 2-5-4 および 2-3-4 | 6 |
 | 3 | エッジ4-6 | 2 |
 | 4 | エッジ6-10 | 3 |
 | 5 | サイクル 10 から 9、アーク 10-9 および 10-11-7-8-9 | 2 |
 | 6 | エッジ9-12 | 4 |

 したがって、最短の 1 から 12 のパスには長さがあります。 

[
 2+6+2+3+2+4=19、
 】

 したがって、ポータルがなければコストは 38 です。 

最適な最初の終点は頂点 5 です。最初のサイクルでは円弧 2-5-4 が使用されるため、1 からの距離は 2+3=5 になります。 頂点 5 から最後のブロックの左側のアーティキュレーションである頂点 9 までの距離は、3+2+3+2=10 です。 9 から 12 への最後のエッジはさらに 4 秒貢献し、ポータルのアクティブ化距離は 14 になります。 

| 変数 | 値 |
 | --- | --- |
 | 最初のエンドポイント u | 5 |
 | u までの距離 1 | 5 |
 | フロンティア 9 までの距離 u | 10 |
 | 9 から v までの距離 = 12 | 4 |
 | 起動距離 | 14 |
 | 合計 | 5 + 14 + 5 = 24 |

 Fenwick クエリは、14 <= k = 14 であるため、この候補を受け入れます。結果の答えは 24 で、サンプルと一致します。 

### カスタムサイクルの例

 検討してください```
5 5 4
1 2 1
2 3 4
3 4 4
4 2 7
4 5 1
```2 と 4 の間のサイクルには、長さ 8 と 7 の 2 つの円弧があります。通常の最短パスは、長さ 7 のエッジ 2-4 を選択するため、1-5 の最短距離は 9 で、ベースライン往復は 18 です。 

ダイレクト サイクル エッジの重みは 7 で、k が 4 であるためポータルのアクティベーションには使用できません。ただし、他のアークでは、各エッジの重みは 4 です。u = 2 および v = 3 を選択できます。 

| 変数 | 値 |
 | --- | --- |
 | 最初のエンドポイント u | 2 |
 | u の接頭語 | 1 |
 | 選択されたアーク | 2-3-4 |
 | u から v | 4 |
 | v から n | 4 + 1 = 5 |
 | 合計 | 2(1) + 4 + 2(5) = 16 |

 同じブロックのスライディング ウィンドウは、長い円弧上で長さ 4 の最大使用可能なセグメントを見つけます。 答えは 16 となり、ポータルなしの値 18 よりも改善されます。 

この例は、サイクルを単純にその最短の円弧に置き換えることができない理由を示しています。 より長い円弧には、距離がポータル タイムアウト内にある頂点のペアのみが含まれる場合があります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + e) + n log n) | ブロックの走査は線形で、すべての状態が一定の回数生成され、すべてのフェンウィック操作のコストは O(log n) です。 |
 | スペース | O(n + e) | グラフ、ブロック表現、圧縮座標、およびフェンウィック ツリーはすべて、入力サイズにおいて線形です。 |

 グラフの構造上の制限により、e = O(n) が与えられます。これは、各サイクルが頂点の数よりも 1 つ多いエッジのみを提供し、サイクルが頂点に共通していないためです。 最大約 2n のエンドポイント状態では、フェンウィック ツリーは O(n) 個の更新とクエリのみを処理します。 これにより、解は n = 300000 の意図した漸近限界内に保たれます。 

## テストケース```python
import sys
import io

from solution import solve

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out

    try:
        solve()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

    return out.getvalue().strip()

# Provided sample
sample1 = """\
12 13 14
1 2 2
4 3 5
10 9 2
9 8 7
2 5 3
6 4 2
2 3 2
10 11 5
11 7 6
9 12 4
5 4 3
8 7 1
10 6 3
"""
assert run(sample1) == "24", "sample 1"

# Minimum-size graph, portal can be activated exactly at the timeout.
assert run("""\
2 1 5
1 2 5
""") == "5", "minimum size and k equality"

# Boundary case, the only edge is longer than k, so no portal can activate.
assert run("""\
2 1 4
1 2 5
""") == "10", "portal timeout boundary"

# Maximum-size path, all edge weights are equal.
# The whole path has length 299999 and can be used as the portal segment.
n = 300000
lines = [f"{n} {n - 1} 100000000"]
for i in range(1, n):
    lines.append(f"{i} {i + 1} 1")
large_case = "\n".join(lines) + "\n"

assert run(large_case) == "299999", "maximum-size all-equal chain"

# A longer cycle arc contains the only usable portal segment.
assert run("""\
5 5 4
1 2 1
2 3 4
3 4 4
4 2 7
4 5 1
""") == "16", "cycle arc and same-block portal"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 | 24 | クロスブロックポータルの配置とサンプルの最適なサイクルの選択 |
 |`2 1 5 / 1 2 5`| 5 | 最小のグラフとそれを含む`distance <= k`境界 |
 |`2 1 4 / 1 2 5`| 10 | 起動距離が k を超えた場合の正しい拒否 |
 | 300000 頂点ユニット チェーン | 299999 | 最大入力サイズ、すべて等しい重み、および長いブロック間ウィンドウ |
 | サイクルのある 5 つの頂点のグラフ | 16 | 同一ブロックの処理と非最短サイクル アーク内のポータル エンドポイント |

 ## 特殊なケース

 最小のグラフの場合```
2 1 5
1 2 5
```ブロックは 1 つだけです。 その最短の長さは 5 であるため、ポータルなしのベースラインは 10 です。スライディング ウィンドウでは k と等しいことが受け入れられるため、同一ブロックの計算では長さ 5 のセグメントが見つかります。 結果の値は次のとおりです。`2 * 5 - 5 = 5`。 

のために```
2 1 4
1 2 5
```同じスライディング ウィンドウでは、唯一のエッジの重みが 5 > 4 であることがすぐにわかります。その使用可能なセグメント長はゼロであるため、ベースラインを改善するポータル候補はありません。 アルゴリズムは 10 を返します。 

チェーン用```
4 3 4
1 2 2
2 3 2
3 4 2
```1 対 4 の最短距離は 6 で、ベースラインは 12 になります。最初のエンドポイントは頂点 1 にすることができ、2 番目のエンドポイントは頂点 3 にすることができます。それらの距離は 4 であるため、ポータルはタイムアウト境界で正確にアクティブ化されます。 結果として生じるコストは、

 [
 2\cdot0+4+2\cdot2=8。 
】

 フェンウィックのスイープにより、複数のブロックにわたってこのペアが検出されます。 これは、2 番目のエンドポイントが n であることを誤って要求する実装を捕捉する場合にも当てはまります。 

サイクルケースの場合```
5 5 4
1 2 1
2 3 4
3 4 4
4 2 7
4 5 1
```サイクル全体の最短ルートでは長さ 7 の 2-4 が使用されますが、そのエッジはポータルに対して長すぎます。 もう 1 つの円弧は、長さ 4 の 2 つのエッジで構成されます。同一ブロック スライディング ウィンドウは、長さ 4 のセグメント 2-3 を見つけます。そのプレフィックスは 1、サフィックス 3 から 5 は 5 です。 

[
 2\cdot1+4+2\cdot5=16。 
】

 通常の最短往復は 18 ですが、答えは 16 になります。これは、アルゴリズムが最短のものだけを保持するのではなく、すべてのサイクルの両方のアークを保持する必要があることを確認します。
