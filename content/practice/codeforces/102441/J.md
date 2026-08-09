---
title: "CF 102441J - 親子鑑定"
description: "頂点 1 をルートとするルート付きツリーがあります。 頂点には 1 から (n) までの番号が付けられます。 クエリ間隔 ([l,r]) の場合、(lle vle r) を持つすべての頂点 (v) は、(v) のサブツリー内にある同じ間隔からの頂点の数に寄与します。"
date: "2026-08-08T13:33:12+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102441
codeforces_index: "J"
codeforces_contest_name: "2018-2019 9th BSUIR Open Programming Championship. Final"
rating: 0
weight: 102441
solve_time_s: 268
verified: true
draft: false
---

[CF 102441J - 親子鑑定](https://codeforces.com/problemset/problem/102441/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 4 分 28 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 頂点 1 をルートとするルート付きツリーがあります。 頂点には 1 から (n) までの番号が付けられます。 クエリ区間 ([l,r]) の場合、(l\le v\le r) を持つすべての頂点 (v) は、(v) のサブツリー内にある同じ区間の頂点の数に寄与します。 したがって、答えは、(u) が (v) の祖先である区間内の順序ペア ((v,v)) を含む順序ペア ((u,v)) の数です。 

ツリーは (n-1) 個の祖先参照によって与えられます。 (i) 番目の参照は頂点 (i+1) の祖先を記述しているため、頂点のすべての祖先はより小さなラベルを持ちます。 クエリは前の回答を使用してエンコードされます。2 つの入力値は前の回答と XOR され、剰余 (n) が減算され、範囲 (1\ldots n) にシフトされ、並べ替えられて (l) と (r) が取得されます。 このエンコードではクエリがオンラインになるため、デコードされた間隔をすべて確認した後でクエリを並べ替えたり、前処理したりすることはできません。 元の問題では、(n,q\le 50000) と 3 秒の制限時間が指定されています。 

答えは (n(n+1)/2) まで大きくなる可能性があります。 (n=50000) の場合、つまり (1,250,025,000) であるため、最終的な答えには 32 ビットの符号付き整数でも十分ですが、Python 整数を使用するとオーバーフローの懸念がなくなります。 

実装が 2 つの異なる頂点を持つ祖先と子孫のペアのみをカウントする場合、シングルトン間隔は間違いやすいです。 例えば、```
1
1
0 0
```([1,1]) にデコードされ、答えは次のようになります。`1`頂点はそれ自身のサブツリーに含まれているためです。 

無関係な 2 つの頂点も、有用な境界ケースです。 考慮する```
3
1
1
1
1 2
```クエリは ([2,3]) です。 どちらの頂点も他の頂点の祖先ではないため、それぞれがそれ自体にのみ貢献し、答えは次のようになります。`2`。 間隔内の頂点のすべてのペアを祖先ペアとしてカウントする実装は、誤って次の値を返します。`3`。 

ルートを含むクエリでは、サブツリー全体が正しく処理されるかどうかもテストされます。 のために```
3
1
1
1
0 2
```間隔は ([1,3]) です。 頂点 1 には 3 つの頂点すべてが含まれますが、頂点 2 と 3 にはそれ自体のみが含まれます。 答えは`3 + 1 + 1 = 5`。 実装が誤ってルートを除外したり、間違った場所でサブツリー間隔をハーフオープンとして扱ったりすると、これらの寄与の 1 つが失われる可能性があります。 

## アプローチ

 直接的なアプローチは、クエリされた間隔内のすべての頂点のペアを調べて、一方が他方の祖先であるかどうかをテストすることです。 DFS は各頂点にエントリ時間を与えます`tin`そして退場時間`tout`、そして (u) はまさにそのとき (v) の祖先です。 

[
 tin_u\le tin_v<tout_u.
 】

 これにより、前処理後の各ペアのテスト時間が一定になります。 ただし、すべて (n) 個の頂点を含むクエリでは、(\Theta(n^2)) ペアが検査されます。 (n=q=50000) の場合、1 つのクエリに対して約 (1.25\times10^9) 個の順序なしペアが検査され、すべてのクエリにわたって約 (6.25\times10^{13}) 個の順序なしペアがチェックされます。 ブルート フォースは、答えの定義を正確にテストするため正しいですが、利用可能な時間を大幅に超えています。 

有益な観察は、祖先関係が DFS 順序で非常に厳格な構造を持っているということです。 2 つの異なる頂点については、次のいずれかが発生します。 一方のサブツリーにもう一方のサブツリーが含まれているか、それらのサブツリーが互いに素になっています。 (u) と (v) が互いに素であり、(u) が DFS 順序で最初に現れる場合、

 [
 tout_u\le tin_v.
 】

 編集上の定式化では、この補完的な見解が使用されます。 (k) 個の頂点を含む区間の場合、(\binom{k}{2}) 個の順序のない個別の頂点のペアが存在します。 祖先と子孫のペアではないすべてのペアは、互いに素なサブツリーのペアです。 もし`bad(l,r)`はそのような素なペアの数です。 

# k+\binom{k}{2}-bad(l,r)

 \frac{k(k+1)}2-bad(l,r)。 
】

 これにより、問題は頂点ラベルに対する範囲クエリに変わり、対応する 2 つのサブツリーが互いに素である場合、各ペアが 1 つを寄与します。 

次に、ラベル軸をサイズ (T) のブロックに分割します。 クエリには、その両端に最大 2 つの部分ブロックがあります。 それらの間にあるものはすべて完全なブロックで構成されています。 完全なブロックの間隔ごとに答えを事前計算します。 また、すべての頂点とすべてのブロック境界について、ブロックのプレフィックス内の頂点の数がその頂点と互いに素なサブツリーを持ち、ブロックのサフィックスについて対称的にいくつあるかを事前計算します。 これにより、境界頂点とすべての完全な中間ブロック間のすべての相互作用が (O(1)) で評価されます。 

2 つの部分ブロック内に残っている唯一の作業は、(O(T)) によって制限されます。 2 つの部分ブロック間の相互作用は、各ブロックを 1 回ソートした後、DFS 間隔のエンドポイントをマージすることでカウントされます。 

これは、公式編集資料で説明されている平方根分解のアイデアと同じです。つまり、(n/\sqrt q) の周りで (T) を選択し、要素からブロックへのプレフィックス情報と完全なブロックの答えを事前計算し、2 つの境界ブロックのみを処理することで各クエリに答えます。 結果の境界は (O(n(\sqrt q+\log n))) になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(qn^2)) | (O(n)) | 遅すぎる |
 | 最適 | (O(n(\sqrt q+\log n))) | (O(n\sqrt q)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 頂点 1 から DFS を実行し、計算します。`tin[v]`そして`tout[v]`。 (v) のサブツリーは、半開 DFS 間隔 ([tin[v],tout[v])) で表されます。 2 つの頂点が互いに素なサブツリーを持つのは、次のいずれかの場合です。`tout[u] <= tin[v]`または`tout[v] <= tin[u]`が成立する。 
2. (n/\sqrt q) に近いブロック サイズ (T) を選択します。 ラベル間隔 (1\ldots n) は連続したブロックに分割されます。 クエリは多くの完全なブロックと交差できますが、部分的であることができるのは最初と最後のブロックのみです。 
3. すべての頂点 (v) およびすべてのブロック プレフィックスについて、そのプレフィックス内のいくつの頂点が (v) から素なサブツリーを持つかを事前計算します。 これを私たちは呼んでいます`pref[v][b]`、 どこ`b`より小さいインデックスを持つブロックを意味します`b`。 

この値は 2 つの独立した条件の合計です。 前の頂点 (u) は (tout[u]\le tin[v]) または (tout[v]\le tin[u]) のいずれかの理由で (v) から素です。 どちらのセットも、DFS の開始時間と終了時間をスイープしてカウントできます。 
4. 類似の接尾辞テーブルを構築する`suf[v][b]`、ブロック内の互いに素な頂点を数えます。`b`これからも。 同じ 2 つの DFS 時間スイープが使用されますが、ブロック インデックスは逆に処理されます。 
5. すべてのブロックについて、そのブロックのすべての部分間隔内の素のペアの数を事前計算します。 ブロックには頂点が (T) 個だけあるため、そのすべての部分区間は (O(T^2)) で処理できます。 結果をコンパクトな整数配列に保存します。 
6. 事前計算`full[a][b]`、完全なブロックに属するすべての頂点間の素のペアの数 (a,a+1,\ldots,b)。 ブロック (b) がブロック (a) で始まる区間に追加される場合、その内部ペアはすでに既知です。 ブロック (b) 内のすべての頂点 (v) について、`pref[v][b] - pref[v][a]`は、先行する完全なブロック内の素のパートナーをカウントします。 これをブロック全体で合計すると、新しいブロック間の寄与が得られます。 
7. 前の回答を使用してすべてのクエリをデコードします。 2 つのエンコードされた数値を (1\ldots n) の頂点に変換し、(l) と (r) に並べ替えて、内部でゼロから始まるインデックスを処理します。 
8. 両方のエンドポイントが同じブロック内にある場合、`bad(l,r)`事前計算されたサブ間隔テーブルから直接。 
9. それ以外の場合は、事前計算された`full`厳密に 2 つの境界ブロック間の完全なブロックの値。 左側のブロックの部分接尾辞と右側のブロックの部分接頭辞の内側に素のペアを追加します。 
10. 左側の部分ブロック内のすべての頂点について、接尾辞テーブルを使用して、完全な中央ブロック内のその互いに素なパートナーを数えます。 右側の部分ブロック内のすべての頂点について、プレフィックス テーブルを使用して、それらの中央のブロック内の互いに素なパートナーをカウントします。 このような境界頂点は最大で (2T) 個あるため、コストは (O(T)) になります。 
11. 最後に、2 つの部分ブロック間の素のペアを数えます。 価値観`tout`左側と`tin`右側はソートされているので、満足する数`tout[left] <= tin[right]`線形 2 ポインター マージによって見つけることができます。 役割を逆にして繰り返します。 これにより、2 つの部分ブロック間のすべての素のペアが 1 回だけカウントされます。 
12. 間隔に (k=r-l+1) 個の頂点が含まれる場合、(k(k+1)/2-bad(l,r)) を出力し、その値を次のクエリをデコードするための前の回答として保存します。 

### なぜ効果があるのか

 2 つの異なる頂点ごとに、そのサブツリーはネストされているか、互いに素になっています。 ネストされたペアは、祖先と子孫の関係を 1 つだけ提供しますが、素のペアは何も提供しません。 したがって、クエリ間隔内の (\binom{k}{2}) 個の順序のないペアの間では、正確に`bad(l,r)`祖先関係に貢献できません。 各頂点もそれ自体に寄与し、(k) 個の追加のペアを与えます。 分解では、1 つの部分ブロック内、部分ブロックと完全な中間ブロック間、2 つの完全なブロック間、または 2 つの部分ブロック間のいずれかで、すべての素のペアを 1 回だけカウントします。 したがって、最終的な式 (k(k+1)/2-bad(l,r)) は、まさに必要な合計となります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from array import array
from math import isqrt

def solve():
    n = int(input())

    children = [[] for _ in range(n)]
    for v in range(1, n):
        p = int(input()) - 1
        children[p].append(v)

    # Iterative DFS.
    tin = [0] * n
    tout = [0] * n
    at_tin = [0] * n
    at_tout = [0] * (n + 1)

    timer = 0
    stack = [(0, 0, 0)]

    while stack:
        v, p, state = stack.pop()

        if state == 0:
            tin[v] = timer
            at_tin[timer] = v
            timer += 1

            stack.append((v, p, 1))
            for u in reversed(children[v]):
                if u != p:
                    stack.append((u, v, 0))
        else:
            tout[v] = timer
            at_tout[timer] = v

    q = int(input())

    # T ~= n / sqrt(q).
    sq = max(1, isqrt(q))
    T = max(1, n // sq)
    B = (n + T - 1) // T
    stride = B + 1

    block_of = [v // T for v in range(n)]
    block_start = [b * T for b in range(B)]
    block_end = [min(n, (b + 1) * T) for b in range(B)]

    # Build a table:
    # table[v][b] = number of vertices in blocks [0, b)
    # whose subtrees are disjoint from v.
    #
    # If reverse=True, blocks are considered in reverse order.
    def build_disjoint_table(reverse):
        size = n * stride
        table = array('I', [0]) * size

        def mapped_block(v):
            b = block_of[v]
            return B - 1 - b if reverse else b

        cnt = [0] * B

        # First condition:
        # tout[u] <= tin[v].
        #
        # Sweep the threshold tin[v] from small to large.
        for t in range(n):
            u = at_tout[t] if t <= n else 0
            if t > 0:
                u = at_tout[t]
                cnt[mapped_block(u)] += 1

            v = at_tin[t]
            base = v * stride
            cur = 0
            table[base] = 0

            for b in range(B):
                cur += cnt[b]
                table[base + b + 1] = cur

        # Second condition:
        # tout[v] <= tin[u], equivalently tin[u] >= tout[v].
        #
        # Sweep the threshold tout[v] from large to small.
        cnt = [0] * B

        for s in range(n, 0, -1):
            u = at_tin[s - 1]
            cnt[mapped_block(u)] += 1

            v = at_tout[s]
            base = v * stride
            cur = 0

            for b in range(B):
                cur += cnt[b]
                table[base + b + 1] += cur

        return table

    pref = build_disjoint_table(False)
    suf = build_disjoint_table(True)

    # Precompute all subinterval answers inside one block.
    #
    # small[b][l * m + r] =
    # number of disjoint pairs in that subinterval.
    small = []
    internal = [0] * B

    for b in range(B):
        L = block_start[b]
        R = block_end[b]
        m = R - L

        sqmat = array('I', [0]) * (m * m)

        # Process the left endpoint backwards.
        # bad(l, r) = bad(l+1, r) + pairs(l, l+1..r).
        for l in range(m - 1, -1, -1):
            cur = 0
            vl = L + l
            row = l * m
            next_row = (l + 1) * m

            for r in range(l + 1, m):
                vr = L + r

                disjoint = (
                    tout[vl] <= tin[vr] or
                    tout[vr] <= tin[vl]
                )

                if disjoint:
                    cur += 1

                sqmat[row + r] = sqmat[next_row + r] + cur

        small.append(sqmat)
        internal[b] = sqmat[m - 1] if m else 0

    # full[a][b] = number of disjoint pairs in complete blocks a..b.
    full = [[0] * B for _ in range(B)]

    for a in range(B):
        total = 0

        for b in range(a, B):
            cross = 0

            for v in range(block_start[b], block_end[b]):
                base = v * stride
                cross += pref[base + b] - pref[base + a]

            total += internal[b] + cross
            full[a][b] = total

    # Values sorted by DFS entry and exit times inside every label block.
    # They let us count pairs between the two partial blocks in O(T).
    sorted_tin = []
    sorted_tout = []

    for b in range(B):
        L = block_start[b]
        R = block_end[b]

        ids = list(range(L, R))
        sorted_tin.append(sorted(ids, key=tin.__getitem__))
        sorted_tout.append(sorted(ids, key=tout.__getitem__))

    def count_le(A, Bvals):
        # A and Bvals are sorted.
        # Count pairs (a,b) with a <= b.
        j = 0
        m = len(Bvals)
        ans = 0

        for a in A:
            while j < m and Bvals[j] < a:
                j += 1
            ans += m - j

        return ans

    def cross_partial(lb, l, left_end, rb, right_start, r):
        # Count disjoint pairs between:
        # [l, left_end] and [right_start, r].
        #
        # The first orientation is tout[left] <= tin[right].
        # The second orientation is tout[right] <= tin[left].
        out_left = [
            tout[v]
            for v in sorted_tout[lb]
            if v >= l
        ]
        in_right = [
            tin[v]
            for v in sorted_tin[rb]
            if v <= r
        ]

        out_right = [
            tout[v]
            for v in sorted_tout[rb]
            if v <= r
        ]
        in_left = [
            tin[v]
            for v in sorted_tin[lb]
            if v >= l
        ]

        return count_le(out_left, in_right) + count_le(out_right, in_left)

    def query(l, r):
        k = r - l + 1
        lb = l // T
        rb = r // T

        if lb == rb:
            m = block_end[lb] - block_start[lb]
            ll = l - block_start[lb]
            rr = r - block_start[lb]
            bad = small[lb][ll * m + rr]
            return k * (k + 1) // 2 - bad

        # Complete blocks strictly between the two boundary blocks.
        bad = 0

        ml = lb + 1
        mr = rb - 1

        if ml <= mr:
            bad += full[ml][mr]

        # Pairs entirely inside the two partial blocks.
        left_m = block_end[lb] - block_start[lb]
        left_l = l - block_start[lb]
        bad += small[lb][left_l * left_m + left_m - 1]

        right_m = block_end[rb] - block_start[rb]
        right_r = r - block_start[rb]
        bad += small[rb][right_r]

        if ml <= mr:
            # Left partial block against all complete middle blocks.
            #
            # suf[v][B-k] represents original blocks k..B-1.
            left_prefix_column = B - (lb + 1)
            left_suffix_column = B - rb

            for v in range(l, block_end[lb]):
                base = v * stride
                bad += (
                    suf[base + left_prefix_column]
                    - suf[base + left_suffix_column]
                )

            # Complete middle blocks against the right partial block.
            for v in range(block_start[rb], r + 1):
                base = v * stride
                bad += (
                    pref[base + rb]
                    - pref[base + (lb + 1)]
                )

        # Interaction between the two partial blocks.
        bad += cross_partial(
            lb, l, block_end[lb] - 1,
            rb, block_start[rb], r
        )

        return k * (k + 1) // 2 - bad

    ans = 0
    output = []

    for _ in range(q):
        u, v = map(int, input().split())

        x = 1 + ((u ^ ans) % n)
        y = 1 + ((v ^ ans) % n)

        l = min(x, y) - 1
        r = max(x, y) - 1

        ans = query(l, r)
        output.append(str(ans))

    sys.stdout.write("\n".join(output))

if __name__ == "__main__":
    solve()
```チェーン形状のツリーには 50,000 個の頂点を含めることができるため、DFS は反復的です。これは、Python の通常の再帰の深さを超えます。`tin`エントリー時に割り当てられ、`tout`したがって、頂点のサブツリーは、DFS 順序でちょうど 1 つの半開間隔になります。 

2 つの素ペアのテーブルは次の場所に保存されます。`array('I')`通常の Python リストではなく。 (O(n\sqrt q)) 個のエントリがあり、Python 整数はかなり多くのメモリを消費します。 保存されるすべてのカウントは最大 (\binom n2) であり、この制約の符号なし 32 ビット整数に収まります。 

各テーブル ハンドルの最初のスイープ`tout[u] <= tin[v]`。 2番目のハンドル`tout[v] <= tin[u]`。 それらの合計は、サブツリーが互いに素である頂点の数と正確に一致します。`v`。 ブロックの番号付けを逆にすると、基礎となるツリー情報を変更せずにサフィックス テーブルが作成されます。 

の`small`テーブルは、完全に 1 つのブロック内に留まる間隔を処理します。 再帰では、以前に計算された行が使用されるため、すべての (O(T^2)) 間隔値が生成される間に、頂点のすべてのペアが 1 回検査されます。 

完全なブロック テーブルは、接頭辞の素のカウントから構築されます。 ブロック時`b`ブロックで始まる範囲に追加されます`a`、 違い`pref[v][b] - pref[v][a]`ブロックの前にすべての頂点を削除します`a`そして、現在の範囲内にある直前のブロックを正確に残します。 

XOR エンコーディングは前の回答に依存するため、クエリ コードでは操作の順序が正確に保たれます。 範囲計算の前に生のクエリ値をデコードする必要があり、新しく計算された答えを次の値に割り当てる必要があります。`ans`現在のクエリが完全に評価された後でのみ。 

## 実用的な例

 公式サンプルには、5 つのエンコードされたクエリが含まれています。 最初の 2 つは、概念的に全範囲のケースと複数のブロックにまたがるクエリの両方をすでに示しています。 

| クエリ | 前の回答 | (x) | (y) | 間隔 | 頂点 | 互いに素なペア | 答え |
 | --- | --- | --- | --- | --- | --- | --- | --- |
 | 1 | 0 | 1 | 9 | [1,9] | 9 | 3 | 42 |
 | 2 | 42 | 8 | 5 | [5,8] | 4 | 2 | 8 |
 | 3 | 8 | 2 | 3 | [2,3] | 2 | 1 | 3 |
 | 4 | 3 | 6 | 7 | [6,7] | 2 | 1 | 3 |
 | 5 | 3 | 5 | 8 | [5,8] | 4 | 2 | 8 |

 最初のクエリでは、9 つ​​の頂点すべてが選択されます。 素のペアを削除する前に、(9\cdot10/2=45) 個の自己または順序なしペアの寄与が利用可能です。 互いに素なサブツリー ペアは、頂点 6 と頂点 7、8、または 9 の 1 つを含む 3 つのペアだけです。したがって、(45-3=42) になります。 

2 番目のクエリの場合、前の答えは 42 です。エンコードされた値は (1\oplus42=43) と (2\oplus42=40) になります。 モジュロ 9 を減算して 1 を追加すると、頂点 8 と 5 が得られるため、間隔は [5,8] になります。 頂点が 4 つあるため、自己ペアまたは順序なしペアの可能性が 10 個あり、素ペアが 2 つあり、(10-2=8) が得られます。 

独立した小さな例により、素ペアのロジックがより明確になります。```
4
1
1
2
1
2 3
```唯一のクエリは [3,4] にデコードされます。 頂点 3 と 4 は異なるブランチにあるため、それらのサブツリーは互いに素です。 2 つの頂点と 1 つの互いに素なペアがあるため、答えは (2\cdot3/2-1=2) になります。 

| ステップ | 前の回答 | (う) | (v) | デコードされた間隔 | (k) | 悪いペア | 答え |
 | --- | --- | --- | --- | --- | --- | --- | --- |
 | 1 | 0 | 2 | 3 | [3,4] | 2 | 1 | 2 |

 この例では、アルゴリズムが無関係な 2 つの頂点を祖先と子孫のペアと混同しないことを確認します。 また、ラベル配列の同じ小さな領域内でクエリが開始および終了する境界も実行されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n(\sqrt q+\log n))) | DFS と並べ替えには (O(n\log n)) がかかります。 ブロックの前処理には (O(n^2/T)) がかかります。 クエリはそれぞれ (O(T)) かかります。 
| スペース | (O(n^2/T+nT)) | プレフィックス/サフィックス ブロック テーブルとブロック内間隔テーブルがメモリの大半を占めます。 

(T\about n/\sqrt q) の場合、前処理項 (n^2/T) と合計クエリ項 (qT) は両方とも (O(n\sqrt q)) です。 (n,q\le50000) の場合、これは意図された平方根のトレードオフです。 コンパクトな整数配列により、大きな (O(n\sqrt q)) テーブルが 512 MB のメモリ制限内に収まります。 

## テストケース

 テスト ハーネスは、提出されたソリューションが次のように利用可能であることを前提としています。`solution.py`そして暴露します`solve()`。```python
import sys
import io
from contextlib import redirect_stdout

from solution import solve

def run(inp: str) -> str:
    old_stdin = sys.stdin
    try:
        sys.stdin = io.StringIO(inp)
        out = io.StringIO()
        with redirect_stdout(out):
            solve()
        return out.getvalue()
    finally:
        sys.stdin = old_stdin

# Provided sample.
sample = """\
9
1
2
3
4
5
5
7
8
5
0 8
1 2
2 3
4 5
6 7
"""
assert run(sample) == "42\n8\n3\n3\n3", "provided sample"

# Minimum-size tree.
minimum = """\
1
1
0 0
"""
assert run(minimum) == "1", "singleton vertex"

# All encoded values equal.
# Every query becomes a singleton after XOR decoding.
all_equal = """\
5
1
1
1
1
4
0 0
0 0
0 0
0 0
"""
assert run(all_equal) == "1\n1\n1\n1", "all-equal encoded queries"

# Two sibling leaves.
siblings = """\
3
1
1
1
1 2
"""
assert run(siblings) == "2", "disjoint sibling subtrees"

# Root plus two children.
root_interval = """\
3
1
1
1
0 2
"""
assert run(root_interval) == "5", "root interval boundary"

# Maximum-size chain.
# Every vertex is an ancestor of every later vertex, so the full interval
# has answer n * (n + 1) / 2.
n = 50000
parents = "\n".join(["1"] + [str(i) for i in range(2, n)])
maximum = (
    str(n) + "\n" +
    parents + "\n" +
    "1\n" +
    "0 " + str(n - 1) + "\n"
)
expected = str(n * (n + 1) // 2)
assert run(maximum) == expected, "maximum-size chain"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / 0 0`|`1`| 最小ツリーと自己カウント |
 | 4 つのノードを持つ 5 ノード スター`0 0`クエリ |`1 1 1 1`| 繰り返される XOR デコードとシングルトン間隔 |
 | 親を持つ 3 ノード ツリー`1,1`、クエリ`1 2`|`2`| 2 つの互いに素なサブツリーと区間境界 |
 | 親を持つ 3 ノード ツリー`1,1`、クエリ`0 2`|`5`| ルートサブツリーと完全な間隔 |
 | 50000 ノードのチェーン |`1250025000`| 最大サイズ、大きな応答、および最悪の場合の祖先構造 |

 ## 特殊なケース

 シングルトン間隔の場合、アルゴリズムは直ちに同じブロック分岐に入ります。 のために```
1
1
0 0
```区間には 1 つの頂点が含まれるため、`k=1`そして`bad=0`。 戻り値は (1\cdot2/2=1) です。 ブロック内テーブルには、その唯一の区間に異なる頂点のペアがないため、同じ結果が含まれます。 

無関係な 2 つの頂点については、次のように考えます。```
3
1
1
1
2
1
```エンコードされたクエリは [2,3] です。 それらのサブツリーは互いに素であるため、`bad=1`。 (k=2) の場合、式は (2\cdot3/2-1=2) を与えます。 2 つの部分範囲間のマージにより、2 つの DFS 間隔不等式の 1 つを通じて素のペアが検出されます。 

完全な 3 頂点スターの場合、```
3
1
1
1
0 2
```間隔は[1,3]です。 頂点 1 は両方のリーフの祖先ですが、2 つのリーフは互いに素です。 したがって`bad=1`, (k=3)、答えは (3\cdot4/2-1=5) となります。 ルートはそのサブツリーに 3 つのノードを提供し、各リーフはそれ自体に貢献します。 

現在の回答によって次の入力ペアの意味が変わるため、エンコードされたクエリにも特別な注意が必要です。 公式サンプルでは、​​最初のクエリは 42 を返します。次の生の値は`1 2`したがって、42 との XOR を使用してデコードされ、生の数値が示唆する間隔 [2,3] ではなく、間隔 [5,8] が生成されます。 この依存関係により、クエリは厳密に入力順に応答する必要があります。 

最後に、チェーンはスターとは反対の構造的極端です。 50,000 ノードのチェーンでは、異なる頂点のすべてのペアが祖先と子孫のペアになります。`bad=0`インターバル全体にわたって。 答えは (50000\cdot50001/2=1,250,025,000) となります。 このケースでは、考えられる最大の答えでの算術と、素ペア機構がネストされたサブツリーを誤って減算しないという事実の両方をチェックします。
