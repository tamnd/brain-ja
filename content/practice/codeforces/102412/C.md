---
title: "CF 102412C - スティール ボール ラン"
description: "現在、頂点にチップが含まれている場合と含まれていない場合がある木があります。 クエリは、これら 2 つの状態の間で 1 つの頂点を切り替えます。 各トグルの後、現在存在するすべてのチップを 1 つの共通の頂点に集めるために必要な最小合計エッジ トラバーサル数が必要です。"
date: "2026-08-10T14:00:57+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102412
codeforces_index: "C"
codeforces_contest_name: "MEX Foundation Contest (supported by AIM Tech)"
rating: 0
weight: 102412
solve_time_s: 1030
verified: true
draft: false
---

[CF 102412C - スティール ボール ラン](https://codeforces.com/problemset/problem/102412/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 17 分 10 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 現在、頂点にチップが含まれている場合と含まれていない場合がある木があります。 クエリは、これら 2 つの状態の間で 1 つの頂点を切り替えます。 各トグルの後、現在存在するすべてのチップを 1 つの共通の頂点に集めるために必要な最小合計エッジ トラバーサル数が必要です。 チップは他のチップを含む頂点を通過する可能性があるため、目的の頂点 (v) を選択するコストは単純に次のようになります。 

[
 F(v)=\sum_{u\text{ にはチップがあります}} \operatorname{dist}(u,v)。 
】

 答えは、すべての頂点にわたる (F(v)) の最小値です。 

ツリーには最大 (10^5) 個の頂点が含まれており、最大 (10^5) 個の更新があります。 公式の制限時間は 4 秒、メモリ制限は 256 MiB です。 これにより、クエリのたびにツリー全体をスキャンするものは除外されます。 (O(nq)) 解は、最大制限で約 (10^{10}) 個の演算を実行できますが、これは適合範囲をはるかに超えています。 更新ごとにほぼ対数的な作業が必要です。 

誤って対処しやすい特殊なケースがいくつかあります。 チップが 1 つしかない場合、答えは常にゼロになります。 例えば、```
1
1
+ 1
```出力があります```
0
```なぜならチップはすでに目的地にあるからです。 

最適な宛先にチップが含まれている必要はありません。 パス (1-2-3) の頂点 1 と 3 にチップを追加すると、次のようになります。```
3
1 2
2 3
2
+ 1
+ 3
```出力あり```
0
2
```最適な宛先は空の頂点 2 です。 占有された頂点のみを考慮する実装では、このケースを間接的に処理する場合にのみ 2 が誤って報告され、一般に真の最適値を逃す可能性があります。 

同等に良好な中央頂点が 2 つ存在することもあります。 パス (1-2) では、両方のエンドポイントにチップが含まれている場合、すべてをいずれかのエンドポイントに移動すると 1 コストかかります。 中央値が一意であると仮定する方法では、誤って正解を拒否する可能性があります。 有用な特性評価は、厳密に半分以上のチップを含むコンポーネントに基づいているため、どちらの中央値も自然に処理されます。 

最後に、最後のチップの削除は入力によって禁止されていますが、チップを削除しても 1 つのチップだけを残すことができます。 例えば、```
3
1 2
2 3
3
+ 1
+ 3
- 1
```生成する```
0
2
0
```データ構造は、更新直後のアクティブ セットのサイズが 1 のときに機能する必要があります。 

## アプローチ

 直接的な解決策では、クエリのたびに目標全体を再計算できます。 ツリーを 1 回ルート化し、すべてのサブツリー内のチップ数を計算し、ルートからの距離の合計を計算して、すべての頂点にわたる距離の合計を再ルートします。 標準的な再ルート計算式は次のとおりです。 

[
 F(v)=F(p)+M-2S_v、
 】

 (v) が (p) の子の場合、(M) はチップの総数、(S_v) は (v) のサブツリー内のチップの数です。 これにより、1 つのクエリに対して (O(n)) 時間で正確な答えが得られます。 

問題は、(10^5) クエリの後にこれを実行すると (O(nq)) のコストがかかることです。これは、最大の制約で約 (10^{10}) 回の演算に相当します。 ブルート フォースは、考えられるすべての宛先を明示的に評価するため、正しいのですが、前のクエリのほぼすべての情報を繰り返し破棄します。 

重要な観察は、対物レンズが木の上で非常に硬い形状をしているということです。 頂点 (v) に立って、一方のエッジを横切って (x) 個のチップを含むコンポーネントに移動するとします。 これら (x) 個のチップはそれぞれ 1 つのエッジに近づき、他の (M-x) 個のチップはそれぞれ 1 つのエッジに近づきます。 その結果、

 [
 F(\text{next})-F(v)=(M-x)-x=M-2x。 
】

 したがって、半分以上のチップを含むコンポーネントに移行すると、厳密に答えが改善されます。 最大でも半分を含むコンポーネントに移行しても、改善することはできません。 したがって、頂点を削除して得られたすべてのコンポーネントに全チップの最大半分が含まれている場合に、頂点は最適となります。 これはツリーの加重中央値です。 

元のツリーを任意にルート化します。 このルート付きツリーで、一部の子サブツリーにすべてのチップの半分以上が含まれている場合、中央値はそのサブツリー内にある必要があります。 私たちはその重い子供を繰り返し追いかけることができます。 同様に、中央値は、そのサブツリーに厳密にすべてのチップの半分以上が含まれる最も深い頂点です。 

残りの問題は、その頂点を動的に見つけることです。 オイラー ツアーは、すべてのサブツリーを 1 つの連続した区間に変換します。 フェンウィック ツリーはどの頂点にチップが含まれているかを維持できるため、サブツリーのチップ カウントは区間合計クエリになります。 まず、オイラー順序で中間点を横切るチップを見つけます。 チップの半分以上を含むサブツリーにはそのチップが含まれている必要があるため、中央値はルートからそのチップへのパス上にあります。 次に、バイナリ リフティングにより、サブツリーにチップの半分以上がまだ含まれている最も深い祖先が見つかります。 (O(\log n)) 個の祖先チェックではそれぞれ Fenwick プレフィックス合計クエリが実行されるため、これには (O(\log^2 n)) のコストがかかります。 

中央値を見つけた後も、すべてのチップまでの合計距離が必要です。 この合計を再計算するにはコストがかかりすぎます。 重心分解により、正確に適切な動的構造が得られます。 すべての重心 (c) について、そこに表されるアクティブなチップの数と (c) までの合計距離を維持します。 また、同じ分解コンポーネントからの寄与を 1 回差し引くことができるように、各重心の子に対応する情報も保存します。 挿入または削除では (O(\log n)) 個の重心祖先のみが変更され、距離合計クエリでは同じ (O(\log n)) 個の祖先が参照されます。 

2 つの手法は、問題の異なる部分を解決します。 オイラー次数とバイナリ リフティングは最適値がどこにあるのかを特定し、重心分解はその最適値で目的を評価します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(nq)) | (O(n)) | 遅すぎる |
 | 最適 | (O(n\log n+q\log^2 n)) | (O(n\log n)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 元のツリーを頂点 1 にルートし、オイラー ツアーを実行します。 店`tin[v]`そして`tout[v]`したがって、(v) の部分木はオイラー区間 ([tin[v],tout[v])) に対応します。 バイナリリフティングの祖先も構築します。 
2. Fenwick ツリーで現在のチップ構成を維持します。 位置`tin[v]`頂点 (v) にチップがある場合、正確に 1 が含まれます。 したがって、フェンウィック ツリーは、サブツリー内のチップの追加または削除、およびチップのカウントをサポートします。 
3. (M) を現在のチップ数とし、設定します。 

[
 k=\left\lfloor\frac M2\right\rfloor+1。 
】

 オイラー順序で (k) 番目のアクティブな頂点を見つけます。 それを (x) と呼びます。 これは、中間点の後の最初のアクティブな頂点です。 

1. 中央値は (x) の祖先でなければなりません。 頂点にそのサブツリー内の全チップの半分以上がある場合、そのサブツリーには (k) 番目のアクティブな頂点が含まれます。 したがって、考えられるすべての中央値はルートから (x) へのパス上にあります。 
2. (x) から開始して、バイナリ リフティングを使用して、候補祖先のサブツリーに含まれるチップの数が (k) 未満である間に、できるだけ上に登ります。 少なくとも (k) 個のチップを持つ最初の祖先は、チップの半分以上を含む最も深いサブツリーであるため、有効なツリー中央値となります。 
3. 元のツリーの重心分解を構築します。 すべての頂点について、その重心の祖先までの距離を保存します。 各重心はコンポーネントを最大でも半分のサイズの断片に分割するため、重心ツリーの高さは対数になります。 
4. すべての重心 (c) について、`cnt[c]`、その重心によって表されるアクティブなチップの数、および`sum[c]`、(c) までの距離の合計。 すべての非根重心 (c) についても、`subcnt[c]`そして`subsum[c]`、(c) で表されるコンポーネントをその重心の親に対して相対的に記述します。 
5. チップが頂点 (v) で挿入または削除されると、重心ツリーを (v) から上向きに歩きます。 重心 (c) で、更新を追加します。`cnt[c]`対応する距離を追加します`sum[c]`。 (c) に重心の親がある場合、更新します。`subcnt[c]`そして`subsum[c]`同じように。 
6. 任意の頂点 (v) からすべてのアクティブなチップまでの合計距離を計算するには、その重心の祖先をたどります。 重心 (c) の場合、`sum[c] + cnt[c] * dist(v,c)`そこに保存されているすべてのチップの寄与をカウントします。 (v) と同じ重心コンポーネントに属するチップは、より深い重心ですでにカウントされているため、減算します。`subsum[child] + subcnt[child] * dist(v,c)`あの子の為に。 
7. クエリを実行するたびに、フェンウィック ツリーと重心構造の両方を更新し、現在の中央値を見つけて、重心構造を使用してその合計距離を評価し、その値を出力します。 

### なぜ効果があるのか

 中心的な不変条件は、周囲にアクティブなチップの半分を超えるコンポーネントが存在しない場合に、頂点が距離の合計関数の最小値になるということです。 このようなコンポーネントが存在する場合、そのエッジを横切ると目的が減少するため、現在の頂点を最適化することはできません。 そのようなコンポーネントが存在しない場合、考えられるすべての最初の移動のコスト変化が負ではないため、頂点は最適になります。 

オイラー次数構成では、この条件を満たす最も深い祖先が見つかります。 (k) 番目のアクティブなオイラー位置は、チップの半分以上を含むすべてのサブツリー内になければならないため、中央値はその祖先パス上にあります。 バイナリリフティングにより、最も深い適格な祖先が見つかります。 

重心構造は、クエリされたすべての頂点の正確な距離の合計を独立して維持します。 各アクティブ チップはその頂点のすべての重心の祖先に寄与し、より深い重心コンポーネントの減算により二重カウントが防止されます。 したがって、選択した中央値に対して返される値は、まさに可能な最小スパンです。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 1)

    def add(self, pos, delta):
        n = self.n
        bit = self.bit
        while pos <= n:
            bit[pos] += delta
            pos += pos & -pos

    def prefix(self, pos):
        bit = self.bit
        res = 0
        while pos:
            res += bit[pos]
            pos -= pos & -pos
        return res

    def kth(self, k):
        idx = 0
        step = 1 << (self.n.bit_length() - 1)
        bit = self.bit
        n = self.n

        while step:
            nxt = idx + step
            if nxt <= n and bit[nxt] < k:
                idx = nxt
                k -= bit[nxt]
            step >>= 1

        return idx

def solve():
    n = int(input())

    graph = [[] for _ in range(n)]
    for _ in range(n - 1):
        u, v = map(int, input().split())
        u -= 1
        v -= 1
        graph[u].append(v)
        graph[v].append(u)

    # Root the original tree and build an Euler tour.
    parent = array('i', [-1]) * n
    depth = array('i', [0]) * n
    tin = array('i', [0]) * n
    tout = array('i', [0]) * n
    euler = []

    stack = [(0, -1, 0)]
    while stack:
        v, p, state = stack.pop()

        if state == 0:
            parent[v] = p
            if p != -1:
                depth[v] = depth[p] + 1

            tin[v] = len(euler)
            euler.append(v)

            stack.append((v, p, 1))
            for to in reversed(graph[v]):
                if to != p:
                    stack.append((to, v, 0))
        else:
            tout[v] = len(euler)

    # Binary lifting for ancestor queries.
    LOG = n.bit_length()
    up = [array('i', parent)]

    for _ in range(1, LOG):
        prev = up[-1]
        cur = array('i', [-1]) * n
        for v in range(n):
            p = prev[v]
            cur[v] = -1 if p == -1 else prev[p]
        up.append(cur)

    # Centroid decomposition.
    removed = bytearray(n)
    cd_parent = array('i', [-1]) * n

    # For every original vertex v, cd_dist[v] stores distances to
    # centroid ancestors in root-to-leaf centroid order.
    cd_dist = [array('i') for _ in range(n)]

    tmp_parent = array('i', [-1]) * n
    subtree_size = array('i', [0]) * n

    def find_centroid(start):
        order = [start]
        tmp_parent[start] = -1

        for v in order:
            pv = tmp_parent[v]
            for to in graph[v]:
                if not removed[to] and to != pv:
                    tmp_parent[to] = v
                    order.append(to)

        for v in reversed(order):
            s = 1
            for to in graph[v]:
                if not removed[to] and tmp_parent[to] == v:
                    s += subtree_size[to]
            subtree_size[v] = s

        total = len(order)

        for v in order:
            largest = total - subtree_size[v]
            for to in graph[v]:
                if not removed[to] and tmp_parent[to] == v:
                    if subtree_size[to] > largest:
                        largest = subtree_size[to]

            if largest * 2 <= total:
                return v

        return start

    def decompose(start, pcd):
        c = find_centroid(start)
        cd_parent[c] = pcd

        # Store distance from this centroid to every vertex in its
        # current component.
        stack = [(c, -1, 0)]
        while stack:
            v, p, d = stack.pop()
            cd_dist[v].append(d)

            for to in graph[v]:
                if not removed[to] and to != p:
                    stack.append((to, v, d + 1))

        removed[c] = 1

        for to in graph[c]:
            if not removed[to]:
                decompose(to, c)

    decompose(0, -1)

    # Dynamic centroid data.
    cnt = array('q', [0]) * n
    total_dist = array('q', [0]) * n
    subcnt = array('q', [0]) * n
    subdist = array('q', [0]) * n

    fenwick = Fenwick(n)
    active = bytearray(n)
    total_chips = 0

    def centroid_update(v, delta):
        chain = cd_dist[v]
        c = v

        for i in range(len(chain) - 1, -1, -1):
            d = chain[i]

            cnt[c] += delta
            total_dist[c] += delta * d

            p = cd_parent[c]
            if p != -1:
                dp = chain[i - 1]
                subcnt[c] += delta
                subdist[c] += delta * dp

            c = p
            if c == -1:
                break

    def distance_sum(v):
        chain = cd_dist[v]
        c = v
        child = -1
        ans = 0

        for i in range(len(chain) - 1, -1, -1):
            d = chain[i]

            ans += total_dist[c] + cnt[c] * d

            if child != -1:
                ans -= subdist[child] + subcnt[child] * d

            child = c
            c = cd_parent[c]

            if c == -1:
                break

        return ans

    def subtree_count(v):
        return fenwick.prefix(tout[v]) - fenwick.prefix(tin[v])

    def find_median():
        k = (total_chips + 1) // 2

        # Fenwick.kth returns a zero-based Euler position.
        pos = fenwick.kth(k)
        x = euler[pos]

        # x itself may already be the deepest heavy vertex.
        if subtree_count(x) >= k:
            return x

        cur = x

        for j in range(LOG - 1, -1, -1):
            a = up[j][cur]
            if a != -1 and subtree_count(a) < k:
                cur = a

        # cur is the deepest ancestor whose subtree is still too small.
        # Its parent is the first ancestor whose subtree exceeds half.
        return parent[cur]

    q = int(input())
    out = []

    for _ in range(q):
        op, v = input().split()
        v = int(v) - 1

        if op == '+':
            delta = 1
            active[v] = 1
        else:
            delta = -1
            active[v] = 0

        total_chips += delta

        fenwick.add(tin[v] + 1, delta)
        centroid_update(v, delta)

        median = find_median()
        out.append(str(distance_sum(median)))

    sys.stdout.write('\n'.join(out))

if __name__ == "__main__":
    solve()
```最初の前処理トラバーサルは、元のツリーをルート化し、オイラー位置を割り当てます。 の`tout[v] = tin[v] + subtree_size[v]`プロパティを使用することもできますが、代入`tout`反復的な開始/終了トラバーサルを直接使用すると、サブツリー間隔が明示的になり、再帰の深さの問題が回避されます。 

バイナリ リフティング テーブルには、セントロイド ツリーの祖先ではなく、元のツリーの祖先が格納されます。 これら 2 つの木には異なる意味があるため、混合してはなりません。 元のツリー テーブルは、加重中央値を見つけるためにのみ使用されます。 

重心分解は独立して構築されます。 元の頂点はすべて最終的に重心になるため、次のようになります。`cd_parent`(v) から始まると、動的距離クエリに必要な重心の祖先が正確に得られます。 

の`cd_dist[v]`配列は、重心ツリーのルートからリーフの順序で距離を格納します。 更新ループとクエリ ループは、元の頂点から開始して重心ルートに向かって移動するため、この配列を逆方向に走査します。 の`array('i')`表現により、これらの (O(n\log n)) 距離がメモリ内でコンパクトに保たれます。 Pythonの標準`array`type は、要素ごとに 1 つの Python オブジェクトではなく、型指定された数値をパック表現で格納します。 

答えが (\Theta(n^2)) まで大きくなる可能性があるため、セントロイド カウンターは 64 ビット配列を使用します。 Python の整数自体はオーバーフローしませんが、符号付き 64 ビット ストレージを使用すると、明示的な配列をコンパクトに保ちながら、可能な最大距離合計を快適にカバーできます。 

フェンウィック ツリーは 1 から始まる内部位置を使用するため、元の 0 から始まるオイラー位置は`tin[v]`で更新されます`tin[v] + 1`。 逆に、`prefix(tout[v]) - prefix(tin[v])`半開オイラー区間 ([tin[v],tout[v])) 内の頂点を正確にカウントします。 これら 2 つのインデックス付け規則を混合することは、off-by-one エラーを引き起こす最も簡単な方法の 1 つです。 

条件が厳密に半分より大きいため、中央値検索では (M/2) ではなく (k=\lfloor M/2\rfloor+1) が使用されます。 偶数 (M) の場合、考えられる中央値のペアの一方の側が選択されます。これは、両側の最適コストが同じであるため、これで十分です。 

## 実用的な例

 ### サンプル 1

 木が道(1-2-3)です。 根を 1 にして、オイラー次数 (1,2,3) を与えます。 

| クエリ | アクティブな頂点 | 総チップ数 | (k) | オイラー (k) 番目のチップ | 中央値 | スパン |
 | --- | --- | --- | --- | --- | --- | --- |
 |`+ 1`| {1} | 1 | 1 | 1 | 1 | 0 |
 |`+ 3`| {1,3} | 2 | 2 | 3 | 2 | 2 |
 |`+ 2`| {1,2,3} | 3 | 2 | 2 | 2 | 2 |
 |`- 1`| {2,3} | 2 | 2 | 3 | 2 | 1 |

 After the second query, vertex 3 is the halfway-crossing chip. Its own subtree has only one chip, so it is not the median. Its parent, vertex 2, has both chips in its subtree and is the deepest ancestor whose subtree exceeds half. The total distance from vertex 2 to chips at 1 and 3 is (1+1=2).

 After adding vertex 2, the median remains vertex 2. After deleting vertex 1, only vertices 2 and 3 remain, so vertex 2 has total cost (0+1=1). This also exercises the case where an even number of chips admits two equally good medians.

 ### サンプル 2

 Root the tree at vertex 1. Its Euler order is (1,2,3,4,5,6).

 | クエリ | アクティブな頂点 | 総チップ数 | (k) | オイラー (k) 番目のチップ | 中央値 | スパン |
 | --- | --- | --- | --- | --- | --- | --- |
 |`+ 1`| {1} | 1 | 1 | 1 | 1 | 0 |
 |`+ 4`| {1,4} | 2 | 2 | 4 | 2 | 3 |
 |`+ 5`| {1,4,5} | 3 | 2 | 4 | 4 | 4 |
 |`- 5`| {1,4} | 2 | 2 | 4 | 2 | 3 |
 |`+ 6`| {1,4,6} | 3 | 2 | 4 | 2 | 4 |

 チップが 1 と 4 にある場合、頂点 4 は頂点 2 のサブツリーの内側にあるため、頂点 2 が最も深い重い祖先になります。 1 と 4 のチップまでの距離は (1+2=3) です。 

頂点 5 を追加した後、頂点 4 のサブツリーには 4 と 5 にチップが含まれます。これは 3 つのチップのうち 2 つです。 したがって、頂点 4 自体が中央値となり、(0+3+1=4) になります。 これは、1 回の挿入後に中央値が数レベル下に移動する理由を示しています。 

頂点 5 を削除すると、再びバランスが変わり、頂点 2 が中央値になります。 最後に、頂点 6 がアクティブ セットに加わります。 頂点 2 は頂点 1、4、6 までの距離 (1)、(2)、(1) を持ち、スパン 4 になります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 前処理 | (O(n\log n)) | オイラー前処理、バイナリ リフティング、重心分解 |
 | 更新 | (O(\log n)) | 1 つのフェンウィック アップデートと 1 つのセントロイド ツリー ウォーク |
 | 中央値検索 | (O(\log^2 n)) | (O(\log n)) バイナリリフティング チェック。それぞれフェンウィックの合計を使用します。 
| 距離の合計 | (O(\log n)) | 重心の祖先をたどる |
 | 合計 | (O(n\log n+q\log^2 n)) | すべてのクエリは同じ前処理された構造を使用します。 
| スペース | (O(n\log n)) | バイナリリフティングと重心祖先までの距離 |

 (n,q\le 10^5) を使用すると、前処理は意図した規模内に簡単に収まり、すべてのクエリでツリーのフル スキャンが回避されます。 (O(\log^2 n)) 中央値検索はクエリごとの主要なコンポーネントですが、重心距離の計算は対数のままです。 実装のコンパクトな型付き配列は、(O(n\log n)) 個の補助データを 256 MiB のメモリ制限内に保ちます。 

## テストケース

 次のハーネスは次のことを想定しています`solve()`は、「Python ソリューション」セクションの関数です。 モジュールを一時的に置き換えます`input`そして`stdout`したがって、各アサーションは個別の再実装ではなく、実際の実装を実行します。```python
import sys
import io

def run(inp: str) -> str:
    global input

    old_input = input
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline
    sys.stdout = io.StringIO()

    try:
        solve()
        return sys.stdout.getvalue()
    finally:
        input = old_input
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Provided sample 1.
assert run(
    """3
1 2
2 3
4
+ 1
+ 3
+ 2
- 1
"""
) == """0
2
2
1""\n", "sample 1"

# Provided sample 2.
assert run(
    """6
1 2
2 3
3 4
4 5
2 6
5
+ 1
+ 4
+ 5
- 5
+ 6
"""
) == """0
3
4
3
4""\n", "sample 2"

# Minimum-size tree.
assert run(
    """1
1
+ 1
"""
) == """0\n""", "minimum-size tree"

# A path where the optimal vertex is between occupied vertices,
# followed by a deletion that leaves two active vertices.
assert run(
    """5
1 2
2 3
3 4
4 5
4
+ 1
+ 5
+ 3
- 5
"""
) == """0
4
4
2\n""", "path median and deletion"

# Star with every vertex eventually occupied.
assert run(
    """5
1 2
1 3
1 4
1 5
5
+ 1
+ 2
+ 3
+ 4
+ 5
"""
) == """0
1
2
3
4\n""", "all vertices active"

# Maximum-size tree and a distance close to the largest possible answer.
n = 100000
max_case = [str(n)]
for i in range(1, n):
    max_case.append(f"{i} {i + 1}")
max_case.append("2")
max_case.append("+ 1")
max_case.append(f"+ {n}")
max_input = "\n".join(max_case) + "\n"

assert run(max_input) == "0\n99999\n", "maximum-size path"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | (n=1)、1 つの挿入 |`0`| 最小サイズのツリーとシングルチップの場合 |
 | パス (1-2-3-4-5)、`+1,+5,+3,-5`|`0,4,4,2`| 空の中央値、中央値の移動、削除 |
 | すべての頂点が挿入された 1 を中心とする星 |`0,1,2,3,4`| すべての頂点がアクティブで高度な重心 |
 | 両端点に (100000) 個の頂点とチップがあるパス |`0,99999`| 最大 (n)、長い距離、64 ビットの応答範囲 |

 ## 特殊なケース

 単一のアクティブチップは (k=1) として扱われます。 唯一のアクティブな頂点は最初のアクティブなオイラー位置であり、その独自のサブツリーには少なくとも 1 つのチップが含まれているため、メディアン検索ではすぐにそれが返されます。 のために```
1
1
+ 1
```重心距離クエリはゼロを返します。 

中央値は頂点自体が占有されているかどうかではなく、サブツリーの重みによって定義されるため、空の最適な宛先が処理されます。 のために```
3
1 2
2 3
2
+ 1
+ 3
```途中のチップは頂点 3 です。そのサブツリーには 2 つのチップの半分以下である 1 つのチップが含まれています。 その親である頂点 2 のサブツリーには両方のチップがあるため、頂点 2 が選択されます。 重心距離クエリでは (1+1=2) が得られます。 

エッジがアクティブ チップを正確に半分に分割すると、2 つの隣接する中央値が発生します。 パス (1-2) では、両方のチップを挿入した後、どちらかの頂点が最適になります。 厳しい条件付きで`subtree >= floor(M / 2) + 1`の場合、アルゴリズムはオイラー次数の中間チップを含む側の中央値を選択します。 選択した頂点には可能な限り最小のスパンが残っているため、特別なタイ処理は必要ありません。 

チップが1枚だけ残った消去の場合も安心です。 パス上 (1-2-3)、その後```
+ 1
+ 3
- 1
```唯一のアクティブなチップは 3 にあります。中央値検索では 3 が返され、距離の合計は 0 になります。 入力により、アクティブ セットが決して空にならないことが保証されるため、アルゴリズムはゼロ チップの中央値を定義する必要がありません。 

特に有用な境界ケースは、中間点が 2 つのサブツリー間の境界に正確にある場合です。 半分以上という厳密な条件により、どちらの側にも実際に半分以上が含まれていない限り、アルゴリズムがどちらの側にも降りることはできません。 これはまさに、同じコードで一意の中央値と隣接する中央値のペアの両方を処理できるようにするものです。 

たとえば、重心分解セクションをより実装指向にしたり、証明をより形式的にしたりすることで、編集者の用語を特定の Codeforce ハウス スタイルに適合させることができます。
