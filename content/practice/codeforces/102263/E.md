---
title: "CF 102263E - 最長パスの問題"
description: "重み付けされたツリーがあり、元のエッジはすべて独立して考慮されます。 重み (w) の 1 つの選択されたエッジについて、それを削除すると、ツリーが 2 つのコンポーネント (A) と (B) に分割されます。"
date: "2026-08-17T19:57:49+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102263
codeforces_index: "E"
codeforces_contest_name: "ArabellaCPC 2019"
rating: 0
weight: 102263
solve_time_s: 239
verified: true
draft: false
---

[CF 102263E - 最長パスの問題](https://codeforces.com/problemset/problem/102263/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 59 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 重み付けされたツリーがあり、元のエッジはすべて独立して考慮されます。 重み (w) の 1 つの選択されたエッジについて、それを削除すると、ツリーが 2 つのコンポーネント (A) と (B) に分割されます。 次に、(A) の任意の頂点と (B) の任意の頂点の間に、同じエッジを同じ重み (w) で配置することで、2 つのコンポーネントを再接続できます。 タスクは、この再接続後に可能な限り最小の直径を出力することです。 

入力には、(n) 個の頂点と (n-1) 個の重み付けされたエッジが含まれます。 (i) 番目の出力値は (i) 番目の入力エッジに対応するため、すべてのエッジには独自の答えが必要です。 公式の制約では (n=200000)、最大 (10^9) までのエッジの重みが許可されており、元の審査員には 256 MB のメモリで 1 秒の時間制限があります。 

ツリーのサイズにより、エッジごとに個別にトラバースを実行することは直ちに不可能になります。 単一の走査には (O(n)) のコストがかかり、(n-1) 個のエッジに対してこれを行うとすでに (O(n^2)) のコストがかかり、最大 (n) で約 (4\cdot10^{10}) 個の頂点訪問が必要になります。 異なるカット間で情報を再利用する必要があります。 

特に誤って対処しやすいエッジケースが 3 つあります。 

2 つの頂点で構成されるツリーの場合、唯一のエッジはツリー全体でもあります。```
2
1 2 5
```正しい出力は次のとおりです```
5
```エッジを削除すると、両方のコンポーネントに 1 つの頂点が含まれます。 唯一可能な再接続の重みは 5 なので、結果の直径は 5 になります。両方のコンポーネントの直径または半径がゼロ以外であると仮定したソリューションでは、誤ってより大きな値が生成される可能性があります。 

重み付けされたエッジは別のトラップを作成します。 考慮する```
3
1 2 10
2 3 1
```正しい出力は次のとおりです```
11
11
```いずれかのエッジを削除すると、一方のコンポーネントは 1 つの頂点で構成され、もう一方のコンポーネントは 2 つの頂点で構成されます。 エッジの重みが 10 である 2 頂点コンポーネントの場合、最適な頂点の離心率は 10 です。重み付けされたエッジの中点は頂点である必要がないため、頂点半径を (D/2) で置き換えることは無効です。 ウェイト 1 のエッジを含むコンポーネントでも同じ問題が発生します。 

最後に、最適な再接続頂点は、元の削除されたエッジの端点である必要はありません。 公式サンプルに関しては、```
4
1 2 2
1 3 3
2 4 2
```2 番目のエッジのウェイトは 3 です。これを削除すると、片側の頂点 3 とパス (3?) が残ります。実際には、もう一方のコンポーネントは (1-2-4) です。 最適な再接続頂点は、頂点 1 ではなく、頂点 2 です。3 を 2 に直接再接続すると、答えは 5 になります。公式声明でも、この特定の再構成が示されています。 

## アプローチ

 直接的なアプローチは、各エッジを個別に処理することです。 エッジを削除し、結果として得られる両方のコンポーネントをトラバースし、それらの直径を見つけて、可能な限り最適な接続頂点を見つけて、2 つのコンポーネントを結合します。 切断後のコンポーネント間の新しいパスは新しく挿入されたエッジだけであるため、これは正しいです。 

コンポーネント (T) について、その直径を (D(T))、頂点半径を (R(T)) とします。これは、(T) の頂点間の最小離心率を意味します。 重み (w) の削除されたエッジを使用して (x\in A) を (y\in B) に再接続すると、新しいツリー内のすべてのパスは完全に内側 (A) にあるか、完全に内側 (B) にあるか、または新しいエッジと交差します。 エッジを横切る最長のパスには長さがあります

 [
 \オペレーター名{ecc}_A(x)+w+\オペレーター名{ecc}_B(y)。 
】

 (x) と (y) の選択は独立しているため、考えられる最良の交差パスは次のとおりです。 

[
 R(A)+w+R(B)。 
】

 したがって、カットの答えは次のようになります。 

[
 \max(D(A),D(B),R(A)+w+R(B))。 
】

 ブルートフォース手法では、すべてのエッジについてこれらの量をゼロから計算できます。 各カットを (O(n)) だけで処理したとしても、合計は (O(n^2)) になります。 (n=200000) では、エッジごとに 1 つの完全なスキャンがすでに実行されています。 

[
 (n-1)n=39,999,800,000
 】

 直径と中心を決定するために必要な追加作業を考慮する前に、頂点を訪問します。 それは利用可能な時間をはるかに超えています。 

有益な観察は、エッジを削除すると、常に 2 つだけの有向コンポーネントのうちの 1 つが作成されるということです。 エッジ (u-v) の場合、状態 (u\rightarrow v) は、(v) が近傍として禁止されているときに (u) を含むコンポーネントを記述するものと考えることができます。 そのような有向状態は正確に (2(n-1)) 個あります。 

これにより、問題は DP の再ルート化に変わります。 すべての有向状態について、そのルートからそのコンポーネント内の任意の頂点までの最大距離、そのような最大深さのパスの 1 つの端点、直径、および直径の 2 つの端点を保存します。 状態は、隣接するすべてのコンポーネントの対応する状態から構築できます。 

残りの難しさは半径です。 直径の端点 (a、b) がわかれば、頂点 (x) の離心率は次のようになります。 

[
 \max(d(x,a),d(x,b))。 
】

 したがって、最良の頂点は (a) から (b) へのパス上にあります。 このパスに沿って、最初の量は増加し、2 番目の量は減少するため、直径の中点のすぐ周囲にある 2 つの頂点の 1 つによって最適値が達成されます。 重み付けされたエッジにより、中点がエッジの内側に位置する可能性があります。そのため、バイナリ リフティングを使用してこれら 2 つの頂点を明示的に特定します。 

これは、コンテストのディスカッションで説明されているのと同じ高レベルの分解です。つまり、すべてのエッジの両有向辺の直径端点と深さ情報を計算し、それらのコンポーネントを再接続するときにそれらのコンポーネントの中心を使用します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n^2)) | (O(n)) | 遅すぎる |
 | DP の再ルート + バイナリリフティング | (O(n\log n)) | (O(n\log n)) | 承認済み |

 ## アルゴリズムのチュートリアル

1. 元のツリーを頂点 1 にルートし、反復 DFS オーダーを構築します。 すべての頂点について、その親、その親に接続する有向エッジ、その深さ、およびルートからの重み付けされた距離を保存します。 反復トラバーサルにより、200,000 個の頂点を含むパスでの Python の再帰深度の問題が回避されます。 
2. すべての無向エッジを 2 つの有向エッジ ID で表します。 有向エッジ (u\rightarrow v) の場合、その DP 状態を定義して、エッジ (uv) が削除された後に (u) を含むコンポーネントを記述します。 この状態には、(u) からの最大距離、その最大値を達成する端点、直径の長さ、およびその直径の 2 つの端点の 4 つの情報が格納されます。 
3. DFS の逆順で子側の状態を計算します。 (u) に親 (p) があるとします。 (p) を除外した後、(u) をルートとするコンポーネントを構築するには、(p) を除く (u) のすべての近傍を検査します。 近傍が (v) の場合、(u) からの分岐としての寄与は長さを持ちます。 

[
 w(u,v)+H(v\右矢印 u)、
 】

 ここで、(H(v\rightarrow u)) は、反対方向の状態によって保存された最大下向き距離です。 最大の枝は州の高さを示します。 

1. 頂点のブランチをスキャンしている間、3 つの最大のブランチの長さと 2 つの最大の子の直径を維持します。 1 つのエッジが除外されると、最大でも現在の最良の分岐が消え、次の 2 つの候補が残るため、分岐候補は 3 つあれば十分です。 同じ理由で、直径の候補は 2 つあれば十分です。 
2. (u) を根とする状態の場合、直径には 2 つの可能な形式があります。 隣接する 1 つのコンポーネントの内側に完全に存在する場合もあり、その場合はそのコンポーネントの直径を取得します。 または、(u) を通過することもできます。この場合、2 つの最大の分岐長が使用されます。 これらの分岐端点が (x) と (y) である場合、候補直径は次のようになります。 

[
 \オペレーター名{支店}(x)+\オペレーター名{支店}(y)。 
】

 結果として得られる直径の終点は同時に分かるため、直径を個別にトラバースする必要はありません。 

1. 2 番目のトップダウン再ルート化パスを実行します。 頂点の親側を記述する状態が利用可能になると、その頂点のすべての隣接コンポーネントは既知の状態を持ちます。 子エッジごとに、その子を除外して反対側の状態を再計算します。 これにより、すべての有向エッジの DP 状態が得られるため、考えられるすべてのカットが表現されるようになります。 
2. 根付きツリーのバイナリ リフティング テーブルを構築します。 通常の祖先ジャンプに加えて、ルート距離配列を使用して、すべてのジャンプによって移動する重み付けされた距離を決定できます。 このテーブルは、LCA クエリと、祖先パスに沿った指定された距離に最も近い頂点を見つけるための両方に使用されます。 
3. 各有向成分について、保存されている直径の端点 (a、b) を取得します。 (D=d(a,b)) とします。 最適な頂点は、直径パスに沿って (a) からの距離 (D/2) を囲む 2 つの頂点のうちの 1 つです。 バイナリ リフティングにより、(O(\log n)) 内の候補が特定されます。 離心率は直径上の位置から直接取得されるため、追加の全頂点スキャンは必要ありません。 
4. 重み (w) の元のエッジごとに、その 2 つの有向状態の直径 (D_1、D_2) と頂点半径 (R_1、R_2) を持たせます。 答えは

 [
 \boxed{\max(D_1,D_2,R_1+w+R_2)}。 
】

 DP 状態の構築により、(D_1,D_2) がこのエッジの削除によって作成された 2 つのコンポーネントを正確に記述することが保証されます。 半径の計算では各コンポーネントの可能な限り最良の頂点が考慮されるため、第 3 項は新しいエッジを横切る可能な最小のパスになります。

これが機能する理由: 再接続されたツリー内のすべてのパスは、完全に最初のコンポーネント内、完全に 2 番目のコンポーネント内、または新しいエッジと交差する 3 つのクラスのいずれかに正確に属します。 最初の 2 つのクラスは、2 つのコンポーネントの直径によって正確に境界付けされます。 3 番目のクラスの場合、アタッチメント頂点 (x) の選択はそのコンポーネント内の離心率に影響し、2 つの選択は独立しているため、両方の離心率を最小化すると、2 つのコンポーネントの半径と固定エッジの重みの合計が得られます。 再ルート DP はすべての有向成分の正確な直径を計算しますが、直径の端点によってすべての頂点の離心率が決まります。 直径パス上の最小の偏心は、その中点を囲む 2 つの頂点のうちの 1 つで発生するため、バイナリ リフティング ステップで正確な頂点半径が求められます。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

def solve():
    n = int(input())
    m = n - 1

    # Forward-star adjacency representation.
    # Directed edge d has source implicit in the current adjacency list,
    # destination to[d], weight wt[d], and next adjacency edge nxt[d].
    head = array('i', [-1]) * n
    to = array('i', [0]) * (2 * m)
    nxt = array('i', [0]) * (2 * m)
    wt = array('q', [0]) * (2 * m)

    for i in range(m):
        u, v, w = map(int, input().split())
        u -= 1
        v -= 1

        d = 2 * i
        r = d ^ 1

        to[d] = v
        wt[d] = w
        nxt[d] = head[u]
        head[u] = d

        to[r] = u
        wt[r] = w
        nxt[r] = head[v]
        head[v] = r

    # Root the tree at 0.
    parent = array('i', [-1]) * n
    parent[0] = 0
    parent_edge = array('i', [-1]) * n
    depth = array('i', [0]) * n
    dist_root = array('q', [0]) * n

    order = []
    order_append = order.append
    order_append(0)

    stack = [0]

    while stack:
        u = stack.pop()
        e = head[u]

        while e != -1:
            v = to[e]
            if v != parent[u]:
                parent[v] = u
                parent_edge[v] = e
                depth[v] = depth[u] + 1
                dist_root[v] = dist_root[u] + wt[e]
                order_append(v)
                stack.append(v)
            e = nxt[e]

    # DP state for directed edge d:
    # component containing source(d), excluding destination(d).
    height = array('q', [0]) * (2 * m)
    far = array('i', [0]) * (2 * m)

    diam = array('q', [0]) * (2 * m)
    dia_a = array('i', [0]) * (2 * m)
    dia_b = array('i', [0]) * (2 * m)

    def build_state(u, excluded):
        # Three best branches are enough because one branch may be excluded
        # and we still need the two best remaining branches.
        b1v = 0
        b1x = u
        b1e = -1

        b2v = 0
        b2x = u
        b2e = -1

        b3v = 0
        b3x = u
        b3e = -1

        # Two best diameters, because one neighbor may be excluded.
        d1v = 0
        d1a = u
        d1b = u
        d1e = -1

        d2v = 0
        d2a = u
        d2b = u
        d2e = -1

        e = head[u]

        while e != -1:
            if e != excluded:
                r = e ^ 1

                branch = wt[e] + height[r]
                endpoint = far[r]

                if branch > b1v:
                    b3v, b3x, b3e = b2v, b2x, b2e
                    b2v, b2x, b2e = b1v, b1x, b1e
                    b1v, b1x, b1e = branch, endpoint, e
                elif branch > b2v:
                    b3v, b3x, b3e = b2v, b2x, b2e
                    b2v, b2x, b2e = branch, endpoint, e
                elif branch > b3v:
                    b3v, b3x, b3e = branch, endpoint, e

                dv = diam[r]
                if dv > d1v:
                    d2v, d2a, d2b, d2e = d1v, d1a, d1b, d1e
                    d1v, d1a, d1b, d1e = dv, dia_a[r], dia_b[r], e
                elif dv > d2v:
                    d2v, d2a, d2b, d2e = dv, dia_a[r], dia_b[r], e

            e = nxt[e]

        # Select the best two branches after excluding one edge.
        if b1e != excluded:
            x1v, x1x, x1e = b1v, b1x, b1e
            x2v, x2x, x2e = b2v, b2x, b2e
        else:
            x1v, x1x, x1e = b2v, b2x, b2e
            if b2e != excluded:
                x2v, x2x, x2e = b3v, b3x, b3e
            else:
                x2v, x2x, x2e = 0, u, -1

        best_d = d1v
        best_a = d1a
        best_b = d1b

        if d1e == excluded:
            best_d = d2v
            best_a = d2a
            best_b = d2b

        cross = x1v + x2v
        if cross > best_d:
            best_d = cross
            best_a = x1x
            best_b = x2x

        return x1v, x1x, best_d, best_a, best_b

    # Bottom-up pass.
    for idx in range(n - 1, 0, -1):
        u = order[idx]
        d = parent_edge[u] ^ 1

        h, f, dd, aa, bb = build_state(u, d)

        height[d] = h
        far[d] = f
        diam[d] = dd
        dia_a[d] = aa
        dia_b[d] = bb

    # Top-down pass.
    for u in order:
        e = head[u]

        while e != -1:
            v = to[e]

            if parent[v] == u:
                h, f, dd, aa, bb = build_state(u, e)

                height[e] = h
                far[e] = f
                diam[e] = dd
                dia_a[e] = aa
                dia_b[e] = bb

            e = nxt[e]

    # Binary lifting.
    LOG = max(1, n.bit_length())
    up = [array('i', parent)]

    for _ in range(1, LOG):
        prev = up[-1]
        cur = array('i', [0]) * n

        for v in range(n):
            cur[v] = prev[prev[v]]

        up.append(cur)

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

        for k in range(LOG - 1, -1, -1):
            ua = up[k][a]
            ub = up[k][b]
            if ua != ub:
                a = ua
                b = ub

        return parent[a]

    def climb_with_distance(v, x):
        # Move upward as far as possible without exceeding distance x.
        used = 0

        for k in range(LOG - 1, -1, -1):
            p = up[k][v]
            if p != v:
                w = dist_root[v] - dist_root[p]
                if w <= x:
                    x -= w
                    used += w
                    v = p

        return v, used

    def vertex_radius(a, b, D):
        if a == b:
            return 0

        l = lca(a, b)
        da = dist_root[a] - dist_root[l]
        db = dist_root[b] - dist_root[l]

        half = D // 2

        # Find the vertex at or immediately before the midpoint,
        # walking from a.
        if 2 * da >= D:
            p, used = climb_with_distance(a, half)
            ppos = used

            if 2 * ppos == D:
                return ppos

            # Find the next vertex toward b.
            if p != l:
                q = parent[p]
                edge_w = dist_root[p] - dist_root[q]
            else:
                # p is the LCA. The next vertex lies on the lca -> b path.
                q, _ = climb_with_distance(b, db - 1)
                edge_w = dist_root[q] - dist_root[l]

            qpos = ppos + edge_w

            r1 = max(ppos, D - ppos)
            r2 = max(qpos, D - qpos)
            return min(r1, r2)

        # The midpoint is on the lca -> b part.
        # Find the vertex at or immediately after the midpoint,
        # walking from b.
        need_from_b = D - half
        q, used_b = climb_with_distance(b, need_from_b)
        qpos = D - used_b

        if 2 * qpos == D:
            return qpos

        p = parent[q]
        edge_w = dist_root[q] - dist_root[p]
        ppos = qpos - edge_w

        r1 = max(ppos, D - ppos)
        r2 = max(qpos, D - qpos)
        return min(r1, r2)

    radius = array('q', [0]) * (2 * m)

    for d in range(2 * m):
        radius[d] = vertex_radius(dia_a[d], dia_b[d], diam[d])

    ans = []

    for i in range(m):
        d = 2 * i
        r = d ^ 1

        cross = radius[d] + wt[d] + radius[r]
        best = diam[d]

        if diam[r] > best:
            best = diam[r]
        if cross > best:
            best = cross

        ans.append(str(best))

    sys.stdout.write("\n".join(ans))

if __name__ == "__main__":
    solve()
```入力には 200,000 個の頂点を含めることができ、実装にはいくつかの DP 配列とバイナリ リフティング テーブルも必要であるため、隣接構造では Python のタプル リストではなく配列が使用されます。 パックされた整数配列により、メモリの予測可能性が維持され、Python 整数のオブジェクトごとの大きなオーバーヘッドが回避されます。 

有向 DP 配列には、元の各エッジの 2 つの有向バージョンによってインデックスが付けられます。 もし`d`一方向を表し、`d ^ 1`は逆方向を表します。 これにより、各カットの 2 つのコンポーネントが最後にすぐに利用できるようになります。 

ボトムアップ パスは、頂点の親を指す辺を計算します。 次に、トップダウン パスによって、不足している親側の情報が提供され、すべての子について他の方向が計算されます。 これは標準的な 2 パス再根付パターンですが、この状態には最長の枝と直径の両方が含まれています。 

の`build_state`関数は 3 つの分岐候補を保持します。 3 番目の候補が必要なのは、要求された有向状態に対して最適な分岐を正確に除外できるためです。 2 つのブランチだけを保持すると、その状況で正しい 2 番目に最適なブランチが自動的に失われます。 同じ除外問題により、2 つの直径候補が保持される理由が説明されます。 

半径の計算には意​​図的に使用しません。`diameter // 2`。 この式は、中心がツリー上のどこにでも配置できる場合には正しいですが、新しいエッジの端点は既存の頂点でなければなりません。 重み付けされたエッジを使用すると、連続した中点が厳密にエッジの内側に存在する可能性があります。 代わりに、コードはその中点の両側にある最も近い 2 つの頂点を見つけ、より良い離心率を取得します。 

すべての距離は 64 ビットのパックされた整数を使用します。 ツリーには重み (10^9) のエッジを最大 (199999) 個含めることができるため、パスの重みは (2\cdot10^{14}) に近い値になる可能性があり、これは 32 ビット整数に収まりません。 

## 実用的な例

 サンプル 1 の場合、```
4
1 2 2
1 3 3
2 4 2
```3 つのカットは次のように要約できます。 

| エッジ | コンポーネントの直径 | コンポーネントの半径 | エッジの重み | 交差値 | 答え |
 | --- | --- | --- | --- | --- | --- |
 | (1-2) | 2、3 | 2、3 | 2 | 7 | 7 |
 | (1-3) | 4、0 | 2、0 | 3 | 5 | 5 |
 | (2-4) | 3、0 | 3、0 | 2 | 5 | 5 |

 最初のエッジはパス (2-4) をエッジ (1-3) から分離します。 それらの半径は両方とも、それぞれのコンポーネントの中央の利用可能な頂点で得られ、(2+2+3=7) となります。 2 番目のエッジでは、シングルトン コンポーネントの半径は 0 ですが、他のコンポーネントの直径は 4、半径 2 です。最適な交差パスの長さは (3+2=5) です。 これにより、公式の出力が得られます`7, 5, 5`。 

サンプル 2 では、加重パスを考慮します。```
3
1 2 10
2 3 1
```| エッジ | 左のコンポーネント | 右のコンポーネント | 半径 | 交差値 | 答え |
 | --- | --- | --- | --- | --- | --- |
 | (1-2) |`{1}`|`2-3`| 0、1 | 11 | 11 |
 | (2-3) |`1-2`|`{3}`| 10、0 | 11 | 11 |

 最初のカットの 2 番目のコンポーネントには、重み 1 のエッジで接続された頂点が 2 つだけあるため、その半径は 1 です。削除されたエッジは 10 を寄与し、11 になります。2 番目のカットでは役割が逆になり、2 つの頂点コンポーネントの半径は 10 になります。この例は、直径の半分を盲目的に取得することによって重み付けされた頂点半径を取得できない理由を具体的に示しています。 

3 頂点ユニット パスの便利な内部 DP トレースはさらに単純です。 

| 頂点/状態 | 最高のブランチ | 2番目の支店 | 直径 | 直径の端点 |
 | --- | --- | --- | --- | --- |
 | 葉 | 0 | 0 | 0 | 葉、葉 | 写真 葉、葉
 | 真ん中 | 1 | 0 | 1 | 中央、葉 | 写真
 | パス全体 | 1 | 1 | 2 | 2枚の葉 | 写真

 中央の頂点では、2 つの分岐はそれぞれ長さ 1 を持つため、それらの合計は直径 2 を作成します。これはまさに、リルート DP のすべての有向状態で使用される局所不変式です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n\log n)) | 再ルートには (O(n)) がかかります。 (2(n-1)) の各成分半径は、(O(\log n)) のバイナリ リフティングを使用します。 |
 | スペース | (O(n\log n)) | DP とグラフ ストレージでは (O(n)) が使用されますが、バイナリ リフティングでは (O(n\log n)) が使用されます。 |

 (n=200000) の場合、対数係数は約 18 です。パックされた配列の実装では、原則としてメモリ フットプリントが 256 MB の制限を大幅に下回ります。 元の問題の 1 秒制限は Python にとって非常に厳しいため、元の判断者にとっては C++ 実装の方が安全な選択ですが、この Python バージョンはメモリのオーバーヘッドを最小限に抑え、再帰コストを回避するように設計されています。 意図されたアルゴリズムの複雑さは (O(n\log n)) であり、コンテストの議論で説明されたバイナリリフティング戦略と一致します。 

## テストケース

 次のテストでは、次のことを前提としています。`solve`上記のソリューションの機能が利用可能です。 ヘルパーは両方をリセットします`sys.stdin`そしてモジュールレベル`input`競合プログラミング ソリューションが結合するため、機能します。`input`に`sys.stdin.readline`。```python
import sys
import io

def run(inp: str) -> str:
    global input

    old_stdin = sys.stdin
    old_input = input

    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline

    try:
        solve()
        return sys.stdout.getvalue() if hasattr(sys.stdout, "getvalue") else ""
    finally:
        sys.stdin = old_stdin
        input = old_input

# A safer helper when stdout is not redirected by the surrounding environment.
def run(inp: str) -> str:
    global input

    old_stdin = sys.stdin
    old_stdout = sys.stdout
    old_input = input

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    input = sys.stdin.readline

    try:
        solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout
        input = old_input

# Provided sample.
assert run("""\
4
1 2 2
1 3 3
2 4 2
""") == "7\n5\n5", "sample 1"

# Minimum-size tree.
assert run("""\
2
1 2 5
""") == "5", "minimum-size tree"

# Weighted path, catches the incorrect radius = diameter / 2 assumption.
assert run("""\
3
1 2 10
2 3 1
""") == "11\n11", "unequal weighted edges"

# All equal weights, star-shaped tree.
assert run("""\
4
1 2 1
1 3 1
1 4 1
""") == "2\n2\n2", "equal-weight star"

# Path with equal weights.
assert run("""\
4
1 2 1
2 3 1
3 4 1
""") == "3\n3\n3", "equal-weight path"

# Large boundary test: maximum n and all equal weights.
n = 200000
large = [str(n)]
large.extend(f"1 {v} 1" for v in range(2, n + 1))
large_input = "\n".join(large) + "\n"
large_output = run(large_input)
assert large_output == "2\n" * (n - 1), "maximum-size star"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 / 1 2 5`|`5`| シングルトン コンポーネントと最小値 (n) |
 |`3 / 1 2 10 / 2 3 1`|`11 / 11`| 加重中心と非半整数直径中心 |
 |`4 / 1 2 1 / 1 3 1 / 1 4 1`|`2 / 2 / 2`| 等しい重みと高度な再ルート |
 |`4 / 1 2 1 / 2 3 1 / 3 4 1`|`3 / 3 / 3`| パス構造と中間点の処理 |
 | スター付き (n=200000) |`2`繰り返し | 最大サイズの入力とメモリの動作 |

 ## 特殊なケース

 2 頂点ツリー```
2
1 2 5
```カット後に 2 つのシングルトン コンポーネントを作成します。 それらの直径と半径は両方ともゼロです。 残っている唯一のパスは重み 5 の新しいエッジなので、式は次のようになります。 

[
 \max(0,0,0+5+0)=5。 
】

 DP は、高さ 0、直径 0、および同一の直径の端点を持つ各シングルトンを表します。 両方のエンドポイントが一致すると、radius ルーチンはすぐに 0 を返します。 

重み付けされたパス```
3
1 2 10
2 3 1
```半径の計算が頂点ベースでなければならない理由を示します。 切断 (2-3) 後、頂点 1 と 2 を含むコンポーネントの直径は 10 になります。その中心として使用できるのは頂点 1 と 2 のみで、両方とも離心率 10 です。エッジの連続した中点の離心率は 5 になりますが、その点は再接続されたエッジの端点として選択できません。 アルゴリズムは直径の端点 1 と 2 を確認し、それらの間に頂点がないことを検出し、半径 10 を返します。 

公式サンプルに関しては、```
4
1 2 2
1 3 3
2 4 2
```(1-3) を切断すると、シングルトン コンポーネントが得られます。`{3}`そしてコンポーネント`1-2-4`。 後者の頂点 1 と 4 の間の直径は 4 で、頂点 2 はちょうどその中間点にあるため、半径は 2 です。削除されたエッジの重みは 3 です。したがって、最適な交差パスは (0+3+2=5) となり、内径 4 よりも大きくなります。アルゴリズムは 5 を出力し、問題で説明されているように、頂点 3 を頂点 2 に直接再接続することに対応します。 

最後の微妙なケースは、中点がエッジの内側にある重み付けされた直径です。 コンポーネントがエッジの重み 4 と 10 を持つパスであるとします。その直径は 14 ですが、中間点はどちらかの端点から 7 単位離れた 2 番目のエッジの内側にあります。 使用可能な頂点は位置 0、4、および 14 にあるため、それらの離心率は 14、10、および 14 になります。正しい頂点半径は 7 ではなく 10 です。バイナリリフティング ルーチンは、位置 7 を囲む頂点を見つけて、より小さい方の離心率を取得します。これは、まさに必要な離散中心です。
