---
title: "CF 102341I - インフェルナペ"
description: "すべてのクエリに対して、1 つの固定ツリーの頂点に複数の Infernape を配置します。 頂点 (vi) にあるパワー (ri) の Infernape は、(vi) からのツリー距離が最大 (ri) である頂点を正確に加熱します。 頂点は、(k) 個の Infernape のうち少なくとも (k-1) 個が加熱する場合に良好であるとみなされます。"
date: "2026-08-14T01:53:41+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102341
codeforces_index: "I"
codeforces_contest_name: "Radewoosh+mnbvmar Contest (supported by AIM Tech)"
rating: 0
weight: 102341
solve_time_s: 953
verified: true
draft: false
---

[CF 102341I - 地獄](https://codeforces.com/problemset/problem/102341/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 15 分 53 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 すべてのクエリに対して、1 つの固定ツリーの頂点に複数の Infernape を配置します。 電力 (r_i) を備えた頂点 (v_i) の Infernape は、(v_i) からのツリー距離が最大 (r_i) である頂点を正確に加熱します。 

頂点は、(k) 個の Infernape のうち少なくとも (k-1) 個が加熱する場合に良好であるとみなされます。 同様に、Infernape によって定義された (k 個の) ボールの中で、頂点は最大 1 つのボールの外側に存在することができます。 タスクは、クエリごとにすべての良好な頂点を個別にカウントすることです。 

ツリー自体には最大 (10^5) 個の頂点がありますが、すべてのクエリに対する Infernape の合計数は最大 (3\cdot10^5) です。 制限時間は 7 秒なので、すべての Infernape を見つけるためにツリー全体をスキャンするアルゴリズムはコストが高すぎます。 (10^5\cdot3\cdot10^5=3\cdot10^{10}) 演算などの境界は、(O(nk)) アプローチを除外します。 おおよそ (O((n+\sum k)\log n))、またはそれに近い値が必要です。 

ソリューションで正確に処理する必要がある境界ケースがいくつかあります。 まず、目的のセットはすべてのボールの交差ではありません。 たとえば、ツリー (1-2) で、(1) と (2) にある 2 つの Infernape を取得し、両方ともパワー (0) を持ちます。 各頂点は 1 つの Infernape によって加熱され、(k-1=1) されるため、正解は (2) です。 2 つのボールの交点は空であるため、共通の交点のみを計算する実装では誤って (0) が返されます。 

第 2 に、距離境界が包括的です。 パス (1-2-3) 上で、パワー (1) でインフェルナペを 1 つ (1) に置き、もう 1 つをパワー (1) で (3) に置きます。 すべての頂点は少なくとも 1 つの Infernape によって加熱されるため、答えは (3) になります。 距離 (r_i) を除外したものとして扱うと、誤って頂点 (2) のみが残ります。 

第三に、いくつかのボールの交差点の中心は、元の頂点ではなくエッジの中央にある可能性があります。 ツリー (1-2) 上で、パワー (1) の両方のエンドポイントに Infernape を配置します。 それらの共通の加熱領域には両方の頂点が含まれており、自然にエッジの中点が中心になります。 すべての中間ボールの中心を元の頂点に制限すると、交差操作が扱いにくくなり、オフバイワン エラーが発生する可能性があります。 

最後に、別のボールを追加すると交差点が空になることがあります。 このような交差は単純にゼロを寄与する必要があります。 このアルゴリズムは、この状態に人為的な中心を割り当てるのではなく、この状態を明示的に表します。 

## アプローチ

 直接的なアプローチは、クエリ内のすべての Infernape のすべてのツリー頂点を検査することです。 Infernape からの距離を計算し、そこに含まれるボールの数を数え、その数が (k-1) に達したときに答えを増分します。 これは文字通り定義に従っているので正しいです。 そのコストは、1 つのクエリの場合は (O(nk))、入力全体の場合は (O(n\sum k)) です。 最大合計 (k) では、これは約 (3\cdot10^{10}) 個の頂点チェックに相当しますが、これは決して実現可能ではありません。 

最初の有益な観察は、カバレッジを直接カウントするのではなく、交差を使用して答えを表現できることです。 (S_i) を番号 (i) を除くすべての Infernape によって加熱される頂点の集合とし、(S) をすべての (k) Infernape によって加熱される集合とします。 ちょうど (k-1) 個の Infernape によって加熱された頂点は、ちょうど 1 つ (S_i) に属します。 すべて (k) によって加熱された頂点はすべて (S_i) に属するため、それらの合計で (k) 回カウントされます。 (S) のコピー (k-1) を減算すると、その過剰数が正確に修正されます。 

[
 \text{answer}=\sum_{i=1}^{k}|S_i|-(k-1)|S|。 
]

 これにより、問題は木のボールの (k+1) 個の交差点のサイズを計算することになります。 

2 番目の観察は、ツリー上のボール交差の構造です。 ツリー内の 2 つの接続されたボールの交差点も、空であるか、またはその中心が元の 2 つの中心間のパス上にあるボールのいずれかになります。 操作を繰り返すと、任意の数のボールの交点を 1 つのペアで表現できることになります。 

[
 (c,R)、
 ]

 中心 (c) から最大 (R) の距離にあるすべての点を意味します。

中心がエッジの途中に位置する場合があるため、ツリーは細分化されます。 すべての元のエッジ (u-v) は (u-x-v) に置き換えられます。ここで (x) は新しい補助頂点です。 元の木の距離はすべて 2 倍になります。 また、すべての Infernape 半径を 2 倍にします。 これにより、元のエッジの中点が実際の頂点になるため、すべての交差点は整数の中心と整数の半径で表すことができます。 最終的な回答では、元の頂点のみがカウントされます。 

2 つのボール (U(a,A)) と (U(b,B)) の場合、(D=\operatorname{dist}(a,b)) とします。 (A+B<D) の場合、それらの交差は空になります。 一方の半径がもう一方のボールを含むのに十分な距離に達した場合は、小さい方のボールを変更せずに返します。 それ以外の場合、新しい半径は次のようになります。 

[
 R=\min(A,B)-\left\lfloor\frac{D-|A-B|}{2}\right\rloor,
 ]

 新しい中心は、(a) から (b) へのパス上の対応する点です。 バイナリ リフティングでは、パスに沿った指定された距離にある距離と点の両方が提供されます。 これがソリューションの幾何学的な核心です。 この問題に対する既知の解決策では、同じ表現と交差演算が使用されています。 

接頭辞と接尾辞の交差を使用して、線形多交差演算ですべての交差を構築できます。 させて`pre[i]`最初の (i) ボールと`suf[i]`(i)以降のボールの交点。 この場合、ボール (i) を除いた交点は単純に次のようになります。 

[
 \text{pre}[i-1]\cap\text{suf}[i+1]。 
]

 したがって、各クエリは (k+1) 個のボールカウントリクエストのみを作成します。 

残りの問題は、多くの任意のボールの中に元のツリーの頂点がいくつあるかを効率的に数えることです。 重心分解はこれをオフラインで解決します。 各重心について、現在のコンポーネントのすべての頂点からその重心までの距離がわかります。 (u) を中心とし半径 (R) のクエリは、次を使用して重心を通してテストできます。 

[
 \operatorname{dist}(u,c)+\operatorname{dist}(c,x)\le R.
 ]

 コンポーネント内のすべての頂点は、重心からの距離によって 2 番目の部分を満たします。 距離ごとのプレフィックス数により、可能な数 (x) がわかります。 (u) が重心の 1 つの子コンポーネントにある場合、その同じ子からの頂点は重心を通過しない短いパスを持つ可能性があります。 まず、重心を介してコンポーネント全体を数えてから、まったく同じ子の頂点を減算します。 すべての頂点とクエリのペアは、パスがその重心を通過する最も高い重心に割り当てられるため、正確に 1 回カウントされます。 

すべてのボールカウント要求は分解が開始される前にわかっているため、重心分解はオフラインで処理されます。 結果として得られるアプローチには、(O((n+K)\log n)) 時間 (K) は Infernape の合計数、および (O(n\log n+K)) 個のメモリが必要です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(nK)) | (O(n)) | 遅すぎる |
 | 最適 | (O((n+K)\log n)) | (O(n\log n+K)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 補助頂点を 1 つ挿入して、元のエッジをすべて再分割します。 結果として得られるツリーには (2n-1) 個の頂点があり、元の距離はすべて正確に 2 倍になります。 元の頂点を最初 (n) の頂点として保存すると、重心カウント フェーズで補助頂点を無視できます。 
2. 細分化されたツリーをルート化し、バイナリリフティングテーブルを構築します。 テーブルのサポート`lca(u,v)`そして`jump(u,d)`、 どこ`jump`(d) のエッジを (u) から上に移動します。 これらの操作は、2 つの中心間の距離を見つけ、それらの接続パス上に新しい中心を見つけるには十分です。 
3. 空ではない交差点を次のように表します。`(center, radius)`細分化されたツリー内。 空の交差点は無効な中心で表されます。 無限ボールと通常のボールを交差させると通常のボールが返されるため、単位要素として非常に大きな半径を使用します。 
4. 表現された 2 つのボールの交差を実装します。 中心を (a,b)、半径を (A,B)、距離を (D) とします。 (A+B<D) の場合は空を返します。 (A\ge D+B) の場合、2 番目のボールは最初のボールに含まれているため、2 番目のボールを返します。 対称の場合は最初の値を返します。 それ以外の場合は、次のように新しい半径を計算します。 
[
 R=\min(A,B)-\left\lfloor\frac{D-|A-B|}{2}\right\rloor。 
]
 新しい中心は、(a) から (b) までのパス上の点で、(a) から (A-R) の距離、または (b) からは (B-R) の距離にあります。 バイナリリフティングによりその点が特定されます。 
5. すべてのクエリに対して、プレフィックス交差を構築します`pre`と接尾辞の交差`suf`。 完全な交差点は、`suf[0]`。 省略可能なすべての Infernape (i) について、他のすべてのボールの交点は次のようになります。`pre[i] ∩ suf[i+1]`。 この交差点が空でない場合は、係数 (+1) を使用してボールカウント要求を作成します。 係数 (1-k) を使用して完全な交差に対して追加のリクエストを 1 つ作成します。 
6. 細分化されたツリーを重心によって分解します。 重心を削除すると、現在のコンポーネントがより小さなコンポーネントに分割され、それぞれのコンポーネントに含まれる頂点の数は最大で半分になります。 したがって、分解には (O(\log n)) レベルがあります。 
7. 1 つの重心 (c) で、現在のコンポーネント全体を横断してビルドします。`cnt[d]`、(c) からちょうど (d) の距離にある元の頂点の数。 それをプレフィックス配列に変換するので、`cnt[d]`最大距離にある元の頂点の数 (d) になります。 
8. コンポーネントを再度トラバースし、中心がこのコンポーネントの頂点であるすべてのボールカウント要求を処理します。 その中心 (u) が (c) からの距離 (du) にある場合、半径 (R) は (c) を通ってすべての頂点 (x) に到達できます。 
[
 du+\operatorname{dist}(c,x)\le R.
 ]
 結果として貢献度は`cnt[min(max_distance, R-du)]`。 
9. (c) のすべての子コンポーネントについて、その子の頂点のみを使用してカウント トラバーサルを繰り返しますが、そのカウントに係数 (-1) を与えます。 これにより、(c) で計算された寄与から同じ子の頂点が削除されます。 その後、子コンポーネントを再帰的に実行します。 (c) を通るパスは同じ子の内部の 2 つの頂点の実際のパスではないため、減算が必要です。 
10. すべての重心分解レベルが処理された後、各ボール リクエストには、元の頂点間で表現されたボールのサイズが含まれます。 クエリ (q) の場合、格納されている係数はすでに計算されています。 
[
 \sum_i |S_i|-(k-1)|S|,
 ]
 それはまさに必要な答えです。 

### なぜ効果があるのか

 正しさには 2 つの独立した部分があります。 まず、包含公式は正確です。正確に (k-1) 個のボールで覆われた頂点は 1 つの省略されたボール交差点に属し、すべての (k) 個のボールで覆われた頂点はすべての (k) 個の省略されたボール交差点に属し、その後 (k-1) 回減算されます。 

第 2 に、すべての交差点は 1 つの木のボールまたは空の状態によって正確に表現されます。 ツリー上では、2 つの交差するボールの境界制約がそれらの中心間の固有のパスに沿って交わるので、交差部分はそのパスの中心に配置され、上記の半径を持ちます。 細分割ではすべての可能な中点が実際の頂点になりますが、距離を 2 倍にすると元の頂点に関するすべてのステートメントが保持されます。 

固定重心の場合、最初のトラバースでは重心を通る距離に応じて頂点がカウントされます。 このようなパスは、中心の子コンポーネントの外側にある頂点に対しては正しいです。 同じ子の頂点の場合、重心を通る距離が実際の距離と異なる可能性があるため、2 番目の走査ではそれらの同じ子の候補が正確に減算されます。 したがって、この重心レベルは、パスが重心を通過するペアを正確にカウントします。 頂点のすべてのペアは、分解において正確に 1 つの最高重心によって分離されるため、ペアが見逃されたり、2 回カウントされたりすることはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())
    N = 2 * n - 1

    g = [[] for _ in range(N)]

    for i in range(n - 1):
        a, b = map(int, input().split())
        a -= 1
        b -= 1
        x = n + i
        g[a].append(x)
        g[x].append(a)
        g[b].append(x)
        g[x].append(b)

    # Root the subdivided tree and build binary lifting.
    parent = [0] * N
    depth = [0] * N
    order = [0]

    for u in order:
        pu = parent[u]
        for v in g[u]:
            if v == pu:
                continue
            parent[v] = u
            depth[v] = depth[u] + 1
            order.append(v)

    LOG = N.bit_length()
    up = [parent]

    for _ in range(1, LOG):
        prev = up[-1]
        cur = [0] * N
        for i in range(N):
            cur[i] = prev[prev[i]]
        up.append(cur)

    def jump(u, d):
        bit = 0
        while d:
            if d & 1:
                u = up[bit][u]
            d >>= 1
            bit += 1
        return u

    def lca(a, b):
        if depth[a] < depth[b]:
            a, b = b, a

        diff = depth[a] - depth[b]
        bit = 0
        while diff:
            if diff & 1:
                a = up[bit][a]
            diff >>= 1
            bit += 1

        if a == b:
            return a

        for j in range(LOG - 1, -1, -1):
            if up[j][a] != up[j][b]:
                a = up[j][a]
                b = up[j][b]

        return up[0][a]

    def distance(a, b):
        p = lca(a, b)
        return depth[a] + depth[b] - 2 * depth[p]

    INF = 10**9

    # A ball is (center, radius). (-1, -1) is empty.
    def intersect(A, B):
        a, ra = A
        b, rb = B

        if a < 0 or b < 0:
            return (-1, -1)

        p = lca(a, b)
        D = depth[a] + depth[b] - 2 * depth[p]

        if ra + rb < D:
            return (-1, -1)

        if ra >= D + rb:
            return B

        if rb >= D + ra:
            return A

        R = min(ra, rb) - (D - abs(ra - rb)) // 2

        da = depth[a] - depth[p]
        move_a = ra - R

        if da >= move_a:
            c = jump(a, move_a)
        else:
            c = jump(b, rb - R)

        return (c, R)

    q = int(input())

    answers = [0] * q

    # Offline ball-counting requests.
    qhead = [-1] * N
    qradius = []
    qid = []
    qcoef = []
    qnext = []

    def add_request(center, radius, idx, coef):
        pos = len(qradius)
        qradius.append(radius)
        qid.append(idx)
        qcoef.append(coef)
        qnext.append(qhead[center])
        qhead[center] = pos

    for qi in range(q):
        k = int(input())

        balls = [None] * k
        for i in range(k):
            v, r = map(int, input().split())
            balls[i] = (v - 1, 2 * r)

        pre = [None] * (k + 1)
        pre[0] = (0, INF)

        for i in range(k):
            pre[i + 1] = intersect(pre[i], balls[i])

        suf = [None] * (k + 1)
        suf[k] = (0, INF)

        for i in range(k - 1, -1, -1):
            suf[i] = intersect(balls[i], suf[i + 1])

        # Full intersection has coefficient 1-k.
        all_ball = suf[0]
        if all_ball[0] >= 0:
            add_request(all_ball[0], all_ball[1], qi, 1 - k)

        # Intersection of every ball except i has coefficient +1.
        for i in range(k):
            cur = intersect(pre[i], suf[i + 1])
            if cur[0] >= 0:
                add_request(cur[0], cur[1], qi, 1)

    # Centroid decomposition.
    dead = bytearray(N)
    temp_parent = [-1] * N
    subsize = [0] * N

    def find_centroid(start):
        comp = [start]
        temp_parent[start] = -1

        for u in comp:
            pu = temp_parent[u]
            for v in g[u]:
                if dead[v] or v == pu:
                    continue
                temp_parent[v] = u
                comp.append(v)

        total = len(comp)

        for u in comp:
            subsize[u] = 1

        for u in reversed(comp):
            p = temp_parent[u]
            if p != -1:
                subsize[p] += subsize[u]

        centroid = start
        best = total + 1

        for u in comp:
            largest = total - subsize[u]

            for v in g[u]:
                if dead[v]:
                    continue
                if temp_parent[v] == u and subsize[v] > largest:
                    largest = subsize[v]

            if largest < best:
                best = largest
                centroid = u

        return centroid, total

    def collect(start, parent_node, start_dist, cnt, sign):
        stack = [(start, parent_node, start_dist)]
        max_dist = start_dist

        while stack:
            u, p, d = stack.pop()

            if u < n:
                cnt[d] += sign

            if d > max_dist:
                max_dist = d

            for v in g[u]:
                if dead[v] or v == p:
                    continue
                stack.append((v, u, d + 1))

        return max_dist

    def process_requests(start, parent_node, start_dist, cnt, deg):
        stack = [(start, parent_node, start_dist)]

        while stack:
            u, p, d = stack.pop()

            e = qhead[u]
            while e != -1:
                r = qradius[e]

                if r >= d:
                    limit = r - d
                    if limit > deg:
                        limit = deg
                    answers[qid[e]] += qcoef[e] * cnt[limit]

                e = qnext[e]

            for v in g[u]:
                if dead[v] or v == p:
                    continue
                stack.append((v, u, d + 1))

    tasks = [0]

    while tasks:
        start = tasks.pop()

        centroid, total = find_centroid(start)
        dead[centroid] = 1

        cnt = [0] * (total + 1)

        # First count all vertices through the centroid.
        deg = collect(centroid, -1, 0, cnt, 1)

        for d in range(1, deg + 1):
            cnt[d] += cnt[d - 1]

        process_requests(centroid, -1, 0, cnt, deg)

        # Then subtract vertices belonging to the same child component.
        for v in g[centroid]:
            if dead[v]:
                continue

            # Only the prefix that will be used by this child needs clearing.
            child_deg = 0
            stack = [(v, centroid, 1)]
            while stack:
                u, p, d = stack.pop()
                if d > child_deg:
                    child_deg = d
                for w in g[u]:
                    if dead[w] or w == p:
                        continue
                    stack.append((w, u, d + 1))

            for d in range(child_deg + 1):
                cnt[d] = 0

            child_deg = collect(v, centroid, 1, cnt, -1)

            for d in range(1, child_deg + 1):
                cnt[d] += cnt[d - 1]

            process_requests(v, centroid, 1, cnt, child_deg)

        # The remaining neighbors are roots of independent components.
        for v in g[centroid]:
            if not dead[v]:
                tasks.append(v)

    sys.stdout.write("\n".join(map(str, answers)))

if __name__ == "__main__":
    solve()
```最初の部分では、細分化されたツリーを構築し、一度ルート化します。 補助頂点は意図的に元の頂点の後に配置されるため、テスト`u < n`後で、カウントする必要がある頂点を正確に特定します。 

バイナリ昇降テーブルは２回使用されます。`lca`は 2 つのボールの中心間の距離を示します。`jump`交差点の新しい中心を見つけます。 クエリが読み取られるとすべての半径が 2 倍になり、細分化されたツリー内の 2 倍のエッジの長さに一致します。 

プレフィックス配列とサフィックス配列により、各クエリは (O(k)) 個のボール交差のみを必要とします。 アイデンティティボール`(0, INF)`最初のプレフィックスまたはサフィックスの操作を、他のすべての交差部分とまったく同じように記述することができます。 

リクエスト配列は、ツリーの頂点ごとにリンクされたリストを形成します。 これにより、リクエスト リストを含む最大 (2n-1) 個の Python リスト オブジェクトの作成が回避され、多数の 4 要素タプルの保存も回避されます。 各リクエストは、その中心、半径、元のクエリ インデックス、係数、および次のリクエストを同じ中心に保存します。 

重心分解は反復的に書き込まれます。 (10^5) 個の頂点を持つパス上の再帰 DFS は Python の再帰制限を超えますが、重心分解自体には対数的な深さがありますが、そのコンポーネントの走査にはそれがありません。 コンポーネント ファインダーは、走査順序を明示的に構築し、サブツリーのサイズを逆方向に計算します。 

重心カウント フェーズでは、最初に重心全体の元の頂点をすべてカウントします。 重心からの距離 (d) を中心とするリクエストの場合、半径は`r - d`パスの後半でも引き続き利用できます。 プレフィックス配列は、そのカウントを (O(1)) で返します。 

子コンポーネントの 2 番目の走査には負の重みがあります。 最初のカウントから同じ子頂点を削除し、リクエスト センターからのパスが現在の重心を通過する頂点だけを残します。 この重心レベルが完了すると、重心は永久に削除され、残りのすべての近傍が独立した部分問題を開始します。 

すべての演算は整数演算です。 また、Python 整数を使用すると、蓄積された答えを考慮する必要がある 64 ビット オーバーフローに関する懸念も解消されます。 

## 実用的な例

 公式サンプルには、次のツリーと 2 つのクエリが含まれており、出力 (5) と (7) が含まれます。 

### サンプル 1

 最初のクエリには 3 つの Infernape があります。```
(8, 1)
(3, 1)
(3, 2)
```関連する加熱セットは次のとおりです。 

[
 B_1={8,9,1,2,7},
 ]

 [
 B_2={3,1,4,10}、
 ]

 そして

 [
 B_3={3,1,4,10,5,8}。 
]

 交差の計算は次のように要約できます。 

| 操作 | 結果の交差点 | 元の頂点間のサイズ | 係数 |
 | --- | --- | --- | --- |
 | (B_2\キャップ B_3) | ({3,1,4,10}) | 4 | (+1) |
 | (B_1\キャップ B_3) | ({1,8}) | 2 | (+1) |
 | (B_1\キャップ B_2) | ({1}) | 1 | (+1) |
 | (B_1\キャップ B_2\キャップ B_3) | ({1}) | 1 | (1-3=-2) |

 積み上げた答えは、

 [
 4+2+1-2\cdot1=5。 
]

 頂点 (1) は 3 つの Infernape すべてによって加熱されるため、最初は 3 つの省略されたボール交差すべてに表示されます。 係数 (-2) は正確に 2 つのコピーを削除し、必要に応じて 1 つの出現を残します。 

### サンプル 2

 2 番目のクエリには 2 つの Infernape があります。```
(7, 3)
(6, 0)
```2 番目のボールには頂点 (6) のみが含まれています。 最初のボールには以下が入っています

 [
 {7、8、1、2、9、3}。 
]

 (k=2) なので、頂点は少なくとも 1 つの Infernape によって加熱される必要があるため、答えは単にこれら 2 つのセットの結合になります。 

| 操作 | 結果 | サイズ | 係数 |
 | --- | --- | --- | --- |
 | 初球のみ | ({7,8,1,2,9,3}) | 6 | (+1) |
 | セカンドボールのみ | ({6}) | 1 | (+1) |
 | 両方のボール | 空 | 0 | (-1) |

 結果は

 [
 6+1-0=7。 
]

 この例では、空の交差点状態も実行されます。 このアルゴリズムは空の交差点に対する重心リクエストを作成しないため、寄与する値は正確にゼロになります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O((n+K)\log n)) | バイナリ リフティングは各ボール交差を (O(\log n)) で処理し、重心分解はすべてのツリー頂点とすべてのリクエストを (O(\log n)) レベルで処理します。 
| スペース | (O(n\log n+K)) | バイナリ リフティングでは (O(n\log n)) が使用されますが、オフライン リクエストとツリーでは (O(n+K)) | が使用されます。 

ここで、(K) はすべてのクエリ サイズの合計であり、(K\le300000) となります。 細分化されたツリーの頂点の数は (200,000) 未満であるため、対数係数は小さいままです。 オフライン セントロイド処理は、ブルート フォース手法の (O(nK)) ボトルネックを回避し、指定された (10^5) および (3\cdot10^5) の制限を中心に設計されています。 

## テストケース

 次のハーネスは、送信されたソリューションが次のように保存されることを前提としています。`solution.py`。 サンプルは公式サンプルであり、その後に対象となる 4 つのケースが続きます。 最後のケースでは、許可される最大値 (n=100000) でパスが構築されます。```python
import sys
import io
import solution

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    old_input = solution.input

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    solution.input = sys.stdin.readline

    try:
        solution.solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout
        solution.input = old_input

# Official sample
sample = """\
10
1 3
6 4
9 8
1 8
3 4
2 8
10 3
4 5
8 7
2
3
8 1
3 1
3 2
2
7 3
6 0
"""

assert run(sample) == "5\n7", "official sample"

# Minimum-size tree, zero-radius balls.
# Each vertex is heated by exactly one Infernape.
case_min = """\
2
1 2
1
2
1 0
2 0
"""

assert run(case_min) == "2", "minimum-size tree"

# Boundary distance is inclusive.
# On 1-2-3, each endpoint reaches vertex 2.
# The union contains all three vertices.
case_boundary = """\
3
1 2
2 3
1
2
1 1
3 1
"""

assert run(case_boundary) == "3", "inclusive radius boundary"

# Three identical zero-radius balls.
# Only vertex 2 is heated, and it is heated by all three.
case_equal = """\
3
1 2
2 3
1
3
2 0
2 0
2 0
"""

assert run(case_equal) == "1", "identical balls"

# Maximum-size path.
# Both radius-(n-1) balls cover the whole tree.
n = 100000
edges = "\n".join(f"{i} {i + 1}" for i in range(1, n))
case_max = (
    f"{n}\n"
    f"{edges}\n"
    "1\n"
    "2\n"
    f"1 {n - 1}\n"
    f"{n} {n - 1}\n"
)

assert run(case_max) == str(n), "maximum-size path"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 公式 10 頂点サンプル |`5`そして`7`| 完全交差式と一般的な重心カウント |
 | 2 つの頂点、両方とも半径 0 |`2`| 最小ツリーと (k-1=1) は交差ではなく結合を意味するという事実 |
 | パス (1-2-3)、終点半径 1 |`3`| 包含距離境界 |
 | 3 つの同一の半径 0 ボール |`1`| 繰り返される中心、等しいボール、および係数 (1-k) |
 | (100000) 個の頂点を持つ生成されたパス |`100000`| 最大 (n)、最大半径、および大規模ツリー分解 |

 ## 特殊なケース

 最小ツリーの場合```
2
1 2
1
2
1 0
2 0
```2 つのボールは ({1}) と ({2}) です。 それらの完全な交点は空ですが、1 つのボールを省略して得られる 2 つの交点のサイズは (1) と (1) です。 式は (1+1-1\cdot0=2) を与えます。 重心フェーズは空の交差点のリクエストを決して受信しないため、誤って無効な中心をカウントすることはありません。 

包含半径の場合```
3
1 2
2 3
1
2
1 1
3 1
```2 倍になったツリーにはエッジ長 (1) のパスが含まれており、2 つの元のボールの半径は 2 倍になっています (2)。 それらの境界は両方とも頂点 (2) に正確に到達します。 省略されたボールの交点は単に 2 つの元のボールですが、それらの共通の交点は頂点 (2) です。 式は (2+2-1=3) で、2 つのボールの結合と一致します。 

同一のボールの場合```
3
1 2
2 3
1
3
2 0
2 0
2 0
```すべてのボールはシングルトン ({2}) です。 各省略ボール交差点はサイズ (1) を持ち、完全交差点もサイズ (1) を持ちます。 累積値は、

 [
 1+1+1-2\cdot1=1。 
]

 これは、頂点がすべての (k) 個の Infernape でカバーされている場合に、(k) 個の省略された交差を単純に合計することが間違っている理由を示しています。 

中間点のケースは次の日に発生します。```
2
1 2
1
2
1 1
2 1
```細分割後のツリーは (1-x-2) となり、両方の半径は (2) になります。 2 つのボールの交点には、細分化されたメトリックの中心 (x)、挿入された中点、および半径 (1) があります。 元の頂点はどちらも (x) から (1) の距離にあるため、カウントされるサイズは (2) になります。 これがまさに、エッジの細分化が単なる実装の便宜ではなく表現の一部である理由です。 

最後に、最大サイズのパスには、(100000) 個の元の頂点と (99999) 個のエッジが含まれます。 半径 (99999) の端点に配置された 2 つの Infernape がそれぞれパス全体をカバーします。 交差点はパス全体でもあるため、答えは (100000) になります。 重心分解は繰り返しパスをほぼ半分にカットし、元のツリーが非常に不均衡であるにもかかわらず、レベルの数を対数に保ちます。
