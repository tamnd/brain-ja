---
title: "CF 102174G - \u795e\u5723\u7684 F2 \u8fde\u63a5\u7740\u6211\u4eec"
description: "2 つの郡があり、それぞれに (1) から (n) までの番号が付けられたポジションがあります。 同じ郡内の役職自体にはつながりはありません。 プリズムは郡間を移動する唯一の方法です。 プリズムは、2 つの位置間隔と移動時間によって記述されます。"
date: "2026-08-19T07:06:36+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102174
codeforces_index: "G"
codeforces_contest_name: "The 14-th BIT Campus Programming Contest"
rating: 0
weight: 102174
solve_time_s: 182
verified: true
draft: false
---

[CF 102174G - \u795e\u5723\u7684 F2 \u8fde\u63a5\u7740\u6211\u4eec](https://codeforces.com/problemset/problem/102174/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 2 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 2 つの郡があり、それぞれに (1) から (n) までの番号が付けられたポジションがあります。 同じ郡内の役職自体にはつながりはありません。 プリズムは郡間を移動する唯一の方法です。 

プリズムは、2 つの位置間隔と移動時間によって記述されます。 ユニットが現在最初の間隔の任意の位置にある場合、正確に (w) 時間以内に 2 番目の間隔の任意の位置にジャンプできます。 ジャンプは双方向であるため、同じコストが逆方向にも適用されます。 

(p) 戦闘ユニットは郡 1 の位置 (x_1,\ldots,x_p) から開始し、(q) 敵の建物は郡 2 の位置 (y_1,\ldots,y_q) を占めます。 戦闘ユニットは建物に到着するとすぐに終了したとみなされます。 すべてのユニットが同時に移動できるため、各ユニットが敵の建物への最速ルートを個別に選択する場合、必要な答えはユニット間の最も遅い到着時間です。 

(d(x,y)) が戦闘位置 (x) と建物位置 (y) の間の最短経路距離を表す場合、答えは次のようになります。 

[
 \max_{i=1}^{p}\min_{j=1}^{q} d(x_i,y_j)。 
]

 戦闘ユニットが 1 つでも敵の建物に到達できない場合、答えは次のとおりです。`boring game`。 

入力には、最大 (10^5) 個の位置、(10^5) 個のプリズム、(10^5) 個の戦闘ユニット、および (10^5) 個の建物が含まれます。 (n,m) が両方とも (10^5) 付近である場合、すべてのプリズムを位置のすべてのペアに明示的に拡張することは不可能です。 すべての位置をカバーする 1 つのプリズムでも (n^2=10^{10}) ペアを表し、(10^5) プリズムでは、2 つの方向を考慮する前に最悪のケースで (10^{15}) ペアに達します。 解決策には、間隔サイズにおける線形または二次の仕事ではなく、プリズムごとにほぼ対数の仕事が必要です。 

見落としやすい境界ケースがいくつかあります。 まず、戦闘ユニットはすべての建物から完全に切り離される可能性があります。 例えば、```
2 1 1 1
1 1 1 1 4
2
1
```には各郡の位置 (1) のみを接続するプリズムがあり、戦闘ユニットは位置 (2) から開始します。 正しい出力は次のとおりです`boring game`。 到達不可能な距離をゼロに初期化する、または単純に到達可能な単位の最大値を取る不注意な実装では、誤ってゼロを返す可能性があります。 

第二に、プリズム間隔の両端が含まれる。 例えば、```
4 1 1 1
1 1 4 4 3
1
4
```は位置 (1) から位置 (4) まで直接ルートがあるため、答えは次のようになります。`3`。 セグメントツリーの分解中に間隔を半分開いたものとして扱うと、このルートは暗黙のうちに失われます。 

第三に、プリズムは双方向性です。 例えば、```
3 1 1 1
1 1 3 3 5
3
1
```答えもあります`5`たとえ入力プリズムの 2 番目のエンドポイントとして表示される間隔で戦闘ユニットが開始されたとしてもです。 リストされた方向を追加するだけの実装では、`boring game`。 

最後に、答えはユニットごとの最短距離の最大値であり、世界的な最短ルートではありません。 2 つのユニットが時間 (2) と時間 (7) を必要とする場合、それらは同時に移動するため、相手は時間 (9) や時間 (2) ではなく、時間 (7) で降伏します。 

## アプローチ

 ブルートフォースソリューションは、すべてのプリズムを通常のグラフエッジに変えることから始まります。 ([a,b]) と ([c,d]) を接続するプリズムの場合、すべての (x\in[a,b]) からすべての (y\in[c,d]) にエッジを追加し、反対方向に別のエッジを追加します。 このグラフは問題を正確に表しているため、複数ソースの最短パス アルゴリズムを実行するのは正しいことです。 

問題は辺の数です。 1 つのプリズムで各方向に ((b-a+1)(d-c+1)) 個のペアを作成できます。 両方の間隔の長さ (n) を使用すると、これは (2n^2) 個の有向エッジになります。 (n=10^5) では、1 つのプリズムのみに対して (2\cdot10^{10}) 個のエッジがあり、(10^5) 個のプリズムにわたる最悪のケースは (2\cdot10^{15}) です。 これは時間とメモリの制限をはるかに超えています。 

最初の有益な観察は、目的地が一連の建物であるため、戦闘ユニットごとに個別の最短経路を計算する必要がないということです。 反転したグラフ内のすべての敵の建物にゼロコストで接続された概念的なスーパー ソースを追加します。 次に、ダイクストラを 1 回実行すると、すべての位置から最も近い建物までの距離が得られます。 

2 番目の観測値は大きな間隔を処理します。 区間 (A) のすべての点を区間 (B) のすべての点に接続するとします。 セグメント ツリーは、(O(\log n)) 個の正規ノードのみを使用してどちらの区間も表すことができます。 

セグメント ツリーの 2 つの有向コピーを使用します。 最初のコピーでは、すべての子がコスト 0 の親を指します。 したがって、ポイントは、そのリーフから、そのポイントが含まれる区間にある任意のセグメント ツリー ノードまで登ることができます。 2 番目のコピーでは、すべての親がコスト 0 の子を指します。 したがって、セグメント ツリー ノードは、その区間内の任意の点に下降できます。 

1 つの有向プリズム (A\to B) に対して、仮想頂点 (v) を作成します。 上向きツリーの (A) をカバーするすべての正規ノードはコスト (w) で (v) に接続し、(v) は下向きツリーの (B) をカバーするすべての正規ノードにコスト 0 で接続します。 (A) の任意の点からのパスは、1 つの正規ノードに登り、(v) で 1 回だけ (w) を支払い、その後 (B) の任意の点に下降できます。 

プリズムは双方向であるため、(B\to A) についても同じ構造を作成します。 上向きのツリーと下向きのツリーは分離したままにする必要があります。 親子エッジが双方向にされた場合、ポイントは同じ木を登ったり降りたりすることによって自身の郡内を自由に移動でき、元の問題には存在しないパスが導入されてしまいます。 

以下の実装では、反転圧縮グラフ上でダイクストラを実行します。 グラフのエッジをすべて実体化するのではなく、プリズム接続を範囲イベントとしてコンパクトに保存します。 仮想頂点のターゲット間隔は、その正規セグメント ツリー ノードに関連付けられます。 ダイクストラがそのようなノードに到達すると、追加コストなしで仮想頂点に到達可能になります。 仮想頂点自体がポップされると、そのソース間隔が他のツリーの標準ノードに分解され、それらのノードがプリズム コストを受け取ります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | 最悪の場合の (O(mn^2)) エッジ構造 | (O(mn^2)) | 遅すぎる |
 | セグメントツリー圧縮 + ダイクストラ | (O((n+m)\log^2 n)) | (O(n+m\log n)) | 承認済み |

 ## アルゴリズムのチュートリアル

1. 両方の郡のすべての位置を概念グラフの頂点として扱います。 コスト (w) のプリズム ([a,b]\leftrightarrow[c,d]) の場合、2 つの仮想頂点を作成します。1 つは ([a,b]) から ([c,d]) への方向を表し、もう 1 つはその逆の方向を表します。 間隔方向のソース範囲と宛先範囲が異なるため、2 つの仮想頂点が必要です。 
2. すべて (2n) の位置にわたって 1 つのセグメント ツリーを構築します。 位置 (1) ～ (n) は郡 1 を表し、位置 (n+1) ～ (2n) は郡 2 を表します。 このツリーの論理コピーを 2 つ保持します。 上向きのコピーでは、すべての子はコスト 0 の親を指します。 下向きのコピーでは、すべての親がコスト 0 の子を指します。 
3. 上向きコピーと下向きコピーをすべての実位置で両方向のゼロコスト エッジで接続します。 これにより、同じ物理位置の両方の表現が交換可能になりますが、内部セグメント ツリー ノードの方向性は維持されます。 
4. 各有向プリズム (A\to B) について、(B) を下向きツリーの (O(\log n)) 個の正準ノードに分解します。 逆のグラフでは、これらの各ノードはプリズムの仮想頂点へのゼロコスト接続を取得します。 これらの接続は、対応するセグメント ツリー ノードに関連付けられたイベントとして保存されます。 
5. ソース間隔 (A) とコスト (w) を仮想頂点内に保存します。 逆ダイクストラ中に仮想頂点に到達したら、(A) を上向きツリーの正準ノード (O(\log n)) に分解し、それらすべてを (w) で緩和します。 これはまさにプリズムに入るときの支払い(w)の逆形式です。 
6. 距離ゼロにあるすべての敵の建物からディクストラを初期化します。 建物は下向きの木の葉で表されます。 下向きのツリーから開始すると、逆プリズム エッジが目的の区間を表す正準ノードから仮想頂点に入るので便利です。 
7. ダイクストラ中、元の上向きツリーには子から親へのエッジがあったため、上向きツリー ノードはその子に向かってエッジを反転します。 元の下向きツリーには親から子へのエッジがあったため、下向きツリー ノードには親に向かって反転したエッジがあります。 実際のリーフには、他のツリーの対応するリーフへのコストがゼロのエッジもあります。 
8. ディクストラが終了すると、戦闘位置 (x_i) に対応する上向きのツリーの葉に保存されている距離は、(x_i) から敵の建物までのまさに最短距離になります。 すべての戦闘ユニットの最大値を取得します。 そのような距離が無限である場合は、次のように出力します。`boring game`。 

### なぜ効果があるのか

 区間 (A) から区間 (B) までの 1 つの元のプリズムを考えます。 (A) のすべての点は、(A) をカバーする正準ノードの 1 つまで上向きのツリーを登ることができます。 そこからプリズム頂点に到達して支払います(w)。 次に、プリズム頂点は、下向きツリー内の (B) をカバーするすべての正準ノードに到達し、そのノードは (B) 内のすべての点に下降できます。 したがって、圧縮されたグラフには、許可されたすべてのエンドポイントのペア間の正確なコスト (w) のパスが含まれます。 

逆に、この構造によって導入される唯一の正のコストの遷移は、仮想プリズム頂点を通過する遷移です。 ゼロコストのセグメントツリーのエッジは、同じ区間のメンバーシップの表現を変更するだけであり、無関係な実際の位置間の移動は決して許可しません。 したがって、実際の位置間のすべての圧縮パスは、同じコストの元のプリズム横断の有効なシーケンスに対応します。 

すべての建物から反転グラフでダイクストラを実行すると、すべての実際の位置から最も近い建物までの距離が計算されます。 すべての戦闘ユニットは同時に独立して移動するため、すべての戦闘ユニットが到着する時間はそれらの最短距離の最大値になります。 到達不可能なソースには無限の距離があり、必要なソースと正確に一致します。`boring game`状態。 

## Python ソリューション```python
import sys
import heapq
from array import array

input = sys.stdin.readline

INF = 4_000_000_000_000_000_000

def solve():
    n, m, p, q = map(int, input().split())

    # There are 2*n real positions, the first n in county 1
    # and the next n in county 2.
    N = 2 * n

    # Iterative segment tree size.
    S = 1
    while S < N:
        S <<= 1

    # Segment-tree indices are 1 .. 2*S-1.
    # Tree 0: upward tree, child -> parent in the original graph.
    # Tree 1: downward tree, parent -> child in the original graph.
    OUT_BASE = 2 * S
    VBASE = 4 * S

    virtual_count = 2 * m
    total_nodes = VBASE + virtual_count

    # For each downward-tree canonical node, head[idx] is the first
    # virtual prism attached to it in the reversed graph.
    head = array('i', [-1]) * (2 * S)

    # Linked-list storage for prism events.
    event_v = array('i')
    event_next = array('i')

    # Information stored for every virtual vertex.
    # In the reversed graph, this is the interval reached from the
    # virtual vertex, plus the cost of the prism.
    source_l = array('i')
    source_r = array('i')
    weight = array('q')

    def add_event(seg_idx, vid):
        event_v.append(vid)
        event_next.append(head[seg_idx])
        head[seg_idx] = len(event_v) - 1

    def add_interval_events(l, r, vid):
        """Attach vid to canonical nodes covering inclusive [l, r]."""
        l += S
        r += S + 1

        while l < r:
            if l & 1:
                add_event(l, vid)
                l += 1
            if r & 1:
                r -= 1
                add_event(r, vid)
            l >>= 1
            r >>= 1

    # Create both directions of every prism.
    for i in range(m):
        a, b, c, d, w = map(int, input().split())

        # Convert to zero-based positions in the combined 2*n array.
        a -= 1
        b -= 1
        c = n + c - 1
        d = n + d - 1

        # Direction: county 1 [a,b] -> county 2 [c,d].
        vid = VBASE + 2 * i
        source_l.append(a)
        source_r.append(b)
        weight.append(w)

        # In the reversed graph, destination [c,d] reaches vid at cost 0.
        add_interval_events(c, d, vid)

        # Direction: county 2 [c,d] -> county 1 [a,b].
        vid = VBASE + 2 * i + 1
        source_l.append(c)
        source_r.append(d)
        weight.append(w)

        # In the reversed graph, destination [a,b] reaches vid at cost 0.
        add_interval_events(a, b, vid)

    sources = [x - 1 for x in map(int, input().split())]
    targets = [n + y - 1 for y in map(int, input().split())]

    dist = array('q', [INF]) * total_nodes
    heap = []

    # Start from every enemy building in the downward-tree representation.
    for pos in targets:
        node = OUT_BASE + S + pos
        if dist[node] != 0:
            dist[node] = 0
            heapq.heappush(heap, (0, node))

    while heap:
        dcur, u = heapq.heappop(heap)
        if dcur != dist[u]:
            continue

        # Virtual prism vertex.
        if u >= VBASE:
            k = u - VBASE
            l = source_l[k] + S
            r = source_r[k] + S + 1
            nd = dcur + weight[k]

            # In the reversed graph, a virtual vertex reaches
            # canonical nodes covering its source interval in the
            # upward tree.
            while l < r:
                if l & 1:
                    v = l
                    node = v
                    if nd < dist[node]:
                        dist[node] = nd
                        heapq.heappush(heap, (nd, node))
                    l += 1

                if r & 1:
                    r -= 1
                    v = r
                    node = v
                    if nd < dist[node]:
                        dist[node] = nd
                        heapq.heappush(heap, (nd, node))

                l >>= 1
                r >>= 1

            continue

        # Downward-tree node.
        if u >= OUT_BASE:
            idx = u - OUT_BASE

            # Reverse of parent -> child is child -> parent.
            if idx > 1:
                v = OUT_BASE + (idx >> 1)
                if dcur < dist[v]:
                    dist[v] = dcur
                    heapq.heappush(heap, (dcur, v))

            # A leaf representing a real position is connected to
            # the same position in the upward tree.
            if idx >= S and idx < S + N:
                v = idx
                if dcur < dist[v]:
                    dist[v] = dcur
                    heapq.heappush(heap, (dcur, v))

            # Reverse prism edges: this canonical destination node
            # can enter every prism whose destination interval contains it.
            e = head[idx]
            while e != -1:
                v = event_v[e]
                if dcur < dist[v]:
                    dist[v] = dcur
                    heapq.heappush(heap, (dcur, v))
                e = event_next[e]

        # Upward-tree node.
        else:
            idx = u

            # Reverse of child -> parent is parent -> child.
            if idx < S:
                v = idx << 1
                if dcur < dist[v]:
                    dist[v] = dcur
                    heapq.heappush(heap, (dcur, v))

                v += 1
                if dcur < dist[v]:
                    dist[v] = dcur
                    heapq.heappush(heap, (dcur, v))

            # Same physical position, other representation.
            if idx >= S and idx < S + N:
                v = OUT_BASE + idx
                if dcur < dist[v]:
                    dist[v] = dcur
                    heapq.heappush(heap, (dcur, v))

    answer = 0

    for pos in sources:
        node = S + pos
        if dist[node] >= INF // 2:
            print("boring game")
            return
        if dist[node] > answer:
            answer = dist[node]

    print(answer)

if __name__ == "__main__":
    solve()
```最初の構築セクションでは、2 のべき乗のセグメント ツリー サイズ (S) を選択します。これにより、セグメント ノードの親と子が単純になります。`idx >> 1`そして`idx << 1`。 結合されたツリーには両方の郡が含まれるため、単一のセグメント ツリーで十分です。 郡 2 の位置は (n) だけシフトされますが、郡 1 の位置は前半に残ります。 

2 つの配列`source_l`そして`source_r`すべての仮想プリズム頂点のソース側に間隔を保存します。 宛先間隔は、その正規ノードが入力処理中に直ちにイベントに変換されるため、個別に保存されません。 これにより、プリズムごとに 2 つの明示的な隣接リストを保存する場合に比べて、かなりの量のメモリが節約されます。 

イベント配列は、タプルの Python リストの代わりに整数配列を使用します。 Python タプルにはかなりのオブジェクト オーバーヘッドがあり、(10^5) 個のプリズムがそれぞれ (O(\log n)) 個のセグメント ツリー イベントを生成すると危険です。`head`、`event_v`、 そして`event_next`コンパクトなリンクリスト表現を形成します。 

このグラフは、従来の隣接リストとして具体化されることはありません。 セグメントツリーのエッジは、ダイクストラ中にノード インデックスから直接生成されます。 上向きノードが処理されると、その子が生成されます。 下位ノードが処理されると、その親が生成されます。 明示的なストレージを必要とする唯一のエッジは、プリズム イベントです。 

範囲分解では、内部的に半開区間 ([l,r)) が使用されます。 入力間隔 ([l,r]) は、セグメント ツリーのエンドポイントを次のように設定することによって変換されます。`l + S`そして`r + S + 1`。 それ`+1`問題の間隔には包括的な範囲が含まれるため、これは必須です。 

距離タイプは符号付き 64 ビット配列です。 パスには多くのプリズム トランジションが含まれる可能性があり、それぞれのコストは最大 (10^9) になるため、32 ビット整数では不十分です。 Python の整数は数値的には安全ですが、`array('q')`距離テーブルをコンパクトに保ちます。 

古いエントリのチェック`if dcur != dist[u]`別の訪問済み配列を置き換えます。 ノードが複数回改善されると、古いヒープ エントリがヒープ内に残り、現在の最適な距離に一致するエントリのみが処理されます。 

2 つの郡は、単に位置が同じ番号であるという理由だけでつながっているわけではありません。 郡を越える唯一の動きはプリズムの頂点から生じます。 2 つのセグメント ツリー表現間のゼロコスト エッジは、同じ物理的位置の 2 つの表現間にのみ存在します。 

## 実用的な例

 ### サンプル 1

 入力は```
5 3 2 2
2 4 1 3 1
1 1 4 5 3
1 2 3 4 2
2 3
4 5
```最初の戦闘ユニットは位置 (2) から開始し、2 番目の戦闘ユニットは位置 (3) から開始します。 敵の建物は(4)と(5)の位置にあります。 

最初のユニットの有用な直接ルートは、3 番目のプリズムを通る、郡 1 の位置 ([1,2]) から郡 2 の位置 ([3,4]) までで、コストは (2) です。 2 番目のユニットは、最初のプリズムを位置 (3) から郡 2 の位置 (3) まで使用し、コスト (1) をとります。その後、3 番目のプリズムを使用して郡 1 に戻り、もう一度横断して敵の建物に到達します。 最適な到着時間は(4)です。 

| ダイクストラ州 | 距離 | 意味 |
 | --- | --- | --- |
 | 4号館 | 0 | 最初のソース |
 | 5号館 | 0 | 最初のソース |
 | プリズム (1) 逆頂点 | 0 | その宛先間隔には建物 4 | が含まれます。 
| プリズム (3) 逆頂点 | 0 | その宛先間隔には建物 4 | が含まれます。 
| ソース位置 2 | 2 | 最初の戦闘ユニットが建物に到達 |
 | ソース位置 3 | 4 | 第 2 戦闘ユニットが建物に到達 |

 最大最短距離は (4) であるため、出力は次のようになります。`4`。 これは、最終的な演算が合計ではなく、個々の最短パスの最大値である理由を示しています。 

### サンプル 2

 小さな 2 番目の例は次のとおりです。```
3 1 1 1
1 2 2 3 5
2
3
```唯一のプリズムは、郡 1 の位置 (1) および (2) がコスト (5) で郡 2 の位置 (2) および (3) に到達することを許可します。 戦闘ユニットは位置 (2) から開始し、建物は位置 (3) にあります。 

| ステップ | 現在の表現 | 距離 | 操作 |
 | --- | --- | --- | --- |
 | 1 | 建物 3、下向きの葉 | 0 | ダイクストラの初期化 |
 | 2 | ([2,3]) の下向き正規ノード | 0 | 逆さまに下向きの木に登る | 写真 逆下向きの木に登る
 | 3 | プリズム仮想頂点 | 0 | 宛先間隔イベント |
 | 4 | ([1,2]) の上向き正規ノード | 5 | プリズムコストを支払う |
 | 5 | 位置 2 の上向きリーフ | 5 | 逆上がりの木を降りる | 写真

 唯一の戦闘ユニットは (5) 時間単位で建物に到着するため、答えは次のようになります。`5`。 このトレースは、いずれかの間隔を表すために必要なセグメント ツリー ノードの数に関係なく、プリズム コストが 1 回だけ支払われることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O((n+m)\log^2 n)) | 各プリズムは (O(\log n)) イベントを作成し、ダイクストラは結果として生じる (O((n+m)\log n)) 遷移を対数ヒープ係数 | で処理します。 
| スペース | (O(n+m\log n)) | 2 つのセグメント ツリーと距離テーブルは線形ですが、プリズム イベントには (O(m\log n)) のコンパクトなストレージが必要です。 

(10^5) 個の位置と (10^5) 個のプリズムの制約により、間隔の長さの積に比例する構造は除外されます。 セグメントツリー表現は、すべての間隔の相互作用を対数的に多くの構造演算に削減します。 この実装では、Python オブジェクトを大量に使用する隣接リストも回避されます。これは、256 MB のメモリ制限下で特に重要です。 

## テストケース

 次のテストでは、提出されたソリューションが次のように利用可能であることを前提としています。`solution.py`そして暴露します`solve()`上に示した関数。```python
# helper: run solution on input string, return output string
import sys
import io
from solution import solve

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    try:
        sys.stdin = io.StringIO(inp)
        sys.stdout = io.StringIO()
        solve()
        return sys.stdout.getvalue().strip()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Provided sample
assert run(
    """5 3 2 2
2 4 1 3 1
1 1 4 5 3
1 2 3 4 2
2 3
4 5
"""
) == "4", "provided sample"

# Custom 1: minimum-size input
assert run(
    """1 1 1 1
1 1 1 1 7
1
1
"""
) == "7", "minimum size"

# Custom 2: unreachable combat unit
assert run(
    """2 1 1 1
1 1 1 1 4
2
1
"""
) == "boring game", "unreachable source"

# Custom 3: both interval boundaries must be included
assert run(
    """4 1 1 1
1 1 4 4 3
1
4
"""
) == "3", "inclusive boundaries"

# Custom 4: duplicate positions and multiple prisms
assert run(
    """5 2 3 2
2 4 3 5 2
3 3 1 2 7
2 2 4
3 5
"""
) == "2", "duplicate positions and overlapping intervals"

# Custom 5: maximum n and m, while keeping every prism interval a singleton
m = 100000
lines = ["100000 100000 1 1"]
lines.extend(["1 1 1 1 1"] * m)
lines.append("1")
lines.append("1")
max_case = "\n".join(lines) + "\n"

assert run(max_case) == "1", "maximum n and m"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 提供されたサンプル |`4`| 複数のユニットとパス |
 | (n=1、m=1) |`7`| 可能な最小のグラフ |
 | 切断されたソース |`boring game`| 到達不可能な距離の処理 |
 | シングルトン境界間隔 |`3`| 包括的な間隔のエンドポイント |
 | 重複した位置と重なった位置 |`2`| 繰り返されるソースとターゲット |
 | (n=m=100000) シングルトンプリズムを使用 |`1`| 最大入力スケールとコンパクトな収納 |

 ## 特殊なケース

 切り離されたケース```
2 1 1 1
1 1 1 1 4
2
1
```唯一の戦闘ユニットを郡 1 の位置 (2) で開始します。 唯一のプリズムは郡 1 の位置 (1) のみを受け入れるため、パスは位置 (2) から出ることができません。 ダイクストラは、上向きの葉に有限の距離を割り当てることはありません。 最終スキャンで検出されるのは、`INF`そしてプリント`boring game`。 

包含境界の場合```
4 1 1 1
1 1 4 4 3
1
4
```は両方のプリズム間隔を持ち、それらの境界位置から正確に構成されます。 ([1,1]) と ([4,4]) の分解により、それぞれ 1 つのセグメント ツリー リーフが生成されます。 反転したダイクストラは、距離 0 で仮想プリズムに到達し、次に距離 (3) でソース リーフに到達します。 答えは`3`。 

逆方向の場合```
3 1 1 1
1 1 3 3 5
3
1
```リストされている 2 番目の間隔から最初の間隔までプリズムを使用する必要があります。 この構築では、この方向の 2 番目の仮想頂点を明示的に作成します。 郡 2 の位置 (1) から開始して、反転ダイクストラはその仮想頂点に到達し、次にコスト (5) で郡 1 のソースに到達します。 出力は`5`。 

重複位置の場合```
5 2 3 2
2 4 3 5 2
3 3 1 2 7
2 2 4
3 5
```位置 (2,2,4) に戦闘ユニットがあり、同じ建物のないターゲット インターバル エンドポイントのコピーが 2 つあります。 位置 (2) と位置 (4) の両方で、最初のプリズムを使用してコスト (2) の建物に到達できます。 複製された戦闘ユニットは、位置 (2) にある他のユニットと同じ距離を持ちます。 したがって最大値は`2`。 

大入力テストには (10^5) 個のプリズムが含まれていますが、すべての間隔はシングルトンです。 各プリズムは、方向ごとに 1 つの正準セグメント ツリー イベントのみを寄与するため、イベント配列は (m) で線形のままになります。 このケースでは、実装が潜在的なグラフ エッジごとに Python タプルまたはリスト オブジェクトを割り当てていないこと、および整数配列表現が最大の入力サイズに合わせてスケーリングされていることを確認します。
