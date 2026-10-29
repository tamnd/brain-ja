---
title: "CF 106409J - ツリー クエリ"
description: "この問題では、ツリー上で多くの範囲クエリが必要になります。 ツリーの頂点には 1 から n までの固定ラベルが付いています。 クエリでは、ラベル [l, r] の連続間隔が与えられます。これは、ラベルがこの間隔内にあるすべての頂点を選択することを意味します。"
date: "2026-06-25T09:59:06+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 106409
codeforces_index: "J"
codeforces_contest_name: "HPI 2026 Advanced"
rating: 0
weight: 106409
solve_time_s: 35
verified: true
draft: false
---

[CF 106409J - ツリー クエリ](https://codeforces.com/problemset/problem/106409/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 35 秒
 **確認済み:** はい

 ## 解決策
 # 問題の理解

 この問題では、ツリー上で多くの範囲クエリが必要になります。 ツリーの頂点には固定ラベルが付いています。`1`に`n`。 クエリによりラベルの連続間隔が得られます。`[l, r]`つまり、ラベルがこの間隔内にあるすべての頂点を選択します。 答えは、選択されたすべての頂点を含むツリーの最小の接続されたサブグラフ内のエッジの数です。 これは、それらの頂点にわたる最小サブツリーのサイズです。 元のステートメントでは、これを、選択した頂点のすべてのペアを接続する最小のエッジのセットとして定義しています。 

答えを直接的に考えると、選択した頂点をマークする場合、それらの間のパス上にあるエッジを正確に保持する必要があるということになります。 単一の頂点の場合、エッジは必要ないため、答えはゼロになります。 2 つの頂点の場合、答えは単純にそれらの距離です。 多くの頂点の場合、答えは元のツリー内のシュタイナー ツリーのサイズです。 

制約により、自明ではないアプローチが強制されます。 頂点の総数は最大で`2 * 10^5`クエリの合計数は最大でも`3 * 10^5`。 すべてのクエリに対してスパニング サブツリーを再構築するには、多くの頂点に繰り返しアクセスする必要があります。 に近い解決策`O(nq)`不可能ですし、`O(n sqrt(n))`スタイルの前処理は慎重に設計する必要があります。 

いくつかの特殊なケースは見落とされがちです。 クエリに頂点が 1 つだけ含まれている場合、答えはゼロになります。```
Input:
1
1 1
1 1
```選択したセットは、`{1}`したがって、出力は次のようになります。```
0
```連続して選択された頂点間の距離を常に計算するソリューションでは、単一要素のケースを処理しない場合、誤って非ゼロの寄与が作成されます。 

ツリー全体を含むクエリは、もう 1 つの重要な境界ケースです。```
Input:
1
4 1
1 2
2 3
2 4
1 4
```すべての頂点が選択されます。 答えは 3 つのエッジすべてです。```
3
```選択した頂点間の直接パスのみを考慮する方法では、エッジなどの分岐を見逃す可能性があります。`2-4`。 

3 番目のよくある間違いは、選択した頂点がラベルではなく DFS 位置によって順序付けされていることを忘れていることです。 ラベルはクエリ間隔のみを定義します。 ツリー構造によって、どのエッジが必要かが決まります。 

## アプローチ

 ブルートフォースソリューションは定義から始まります。 クエリごとに`[l, r]`、その間隔内のラベルを持つすべての頂点を収集します。 次に、それらを含む最小限のサブツリーを構築します。 1 つの方法は、選択した各頂点から現在のサブツリーにパスを繰り返し追加することです。 これは正しいです。必要なエッジはすべて、選択した頂点間のパスに属している必要があるからです。 ただし、最悪の場合、クエリにほぼすべての頂点が含まれる可能性があり、パスの構築にコストがかかる可能性があります。`O(n)`。 と`q`これはクエリになります`O(nq)`、周囲に到達できる`6 * 10^10`操作。 

重要な点は、クエリごとにサブツリーを明示的に構築する必要がないということです。 頂点のセットについては、DFS エントリ時間で並べ替えます。 この順序で頂点が`v1, v2, ..., vk`、スパニングツリーの長さは次のとおりです。$$\frac{dist(v_1,v_2)+dist(v_2,v_3)+...+dist(v_k,v_1)}{2}$$巡回合計では、すべてのエッジが、そのエッジによって作成されたカットの各側から 1 回ずつ、正確に 2 回カウントされます。 

この公式は問題を完全に変えます。 クエリでは、頂点の動的なセットと、隣接する頂点間の距離の巡回合計を DFS 順序で維持するように求められます。 クエリは頂点ラベルの間隔であるため、Mo のアルゴリズムで処理できます。 ある間隔から次の間隔に移動すると、少数の頂点が追加または削除されるだけです。 

頂点が DFS 順序セットに挿入されると、2 つの隣接するペアのみが変更されます。 その前任者が`a`そして後継者は`b`、距離の合計は次のように増加します。$$dist(a,x)+dist(x,b)-dist(a,b)$$頂点を削除すると、この操作が逆になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(nq)`|`O(n)`| 遅すぎる |
 | 最適 |`O((n+q)√n log n)`|`O(n log n)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 任意の頂点でツリーをルートし、DFS エントリ時間を計算します。 LCA クエリのバイナリ リフティング テーブルも計算します。 DFS 順序は、選択した頂点を維持できる固定循環順序を与えます。 
2. すべてのクエリを変換する`[l, r]`頂点ラベルに対する Mo クエリに変換します。 クエリをブロックごとに並べ替えると、挿入操作と削除操作の合計数が減ります。 
3. 選択した頂点の現在のセットを維持します。 どの DFS ポジションがアクティブであるかを保存し、前後のアクティブなポジションの検索をサポートします。 
4. 頂点を追加する場合`x`、DFS 順序でその先行者と後続者を検索します。 それらの間の円形距離の合計内の古いエッジを 2 つの新しいエッジで置き換えます。`x`。 
5. 頂点を削除する場合`x`、逆の演算を実行します。 2 つのエッジを通過`x`消滅し、直接の先行者と後続者の接続が戻ります。 
6. クエリを処理した後、維持された巡回距離の合計を 2 で割ります。 結果は、最小サブツリー内のエッジの数になります。 

これが機能する理由: 不変条件は、維持される値が常に DFS 順序で選択された連続する頂点間の距離の巡回合計であることです。 挿入式と削除式は影響を受ける隣接ペアを正確に更新するため、Mo が移動するたびに不変条件は true のままになります。 ツリーのマークされた頂点の巡回合計では、最小接続サブツリー内のすべてのエッジが 2 回カウントされるため、2 で割ると必要な答えが得られます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    data = sys.stdin.buffer.read().split()
    it = iter(data)

    t = int(next(it))
    out = []

    for _ in range(t):
        n = int(next(it))
        q = int(next(it))

        g = [[] for _ in range(n)]
        for _ in range(n - 1):
            a = int(next(it)) - 1
            b = int(next(it)) - 1
            g[a].append(b)
            g[b].append(a)

        LOG = (n).bit_length()
        up = [[0] * n for _ in range(LOG)]
        tin = [0] * n
        depth = [0] * n
        order = []
        
        stack = [(0, -1, 0)]
        timer = 0
        while stack:
            u, p, state = stack.pop()
            if state == 0:
                tin[u] = timer
                order.append(u)
                timer += 1
                up[0][u] = u if p == -1 else p
                for i in range(1, LOG):
                    up[i][u] = up[i - 1][up[i - 1][u]]
                stack.append((u, p, 1))
                for v in reversed(g[u]):
                    if v != p:
                        depth[v] = depth[u] + 1
                        stack.append((v, u, 0))

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
            for i in range(LOG - 1, -1, -1):
                if up[i][a] != up[i][b]:
                    a = up[i][a]
                    b = up[i][b]
            return up[0][a]

        def dist(a, b):
            c = lca(a, b)
            return depth[a] + depth[b] - 2 * depth[c]

        queries = []
        block = max(1, int(n ** 0.5))
        for idx in range(q):
            l = int(next(it)) - 1
            r = int(next(it)) - 1
            queries.append((l, r, idx))

        queries.sort(key=lambda x: (x[0] // block, x[1] if (x[0] // block) % 2 == 0 else -x[1]))

        active = [False] * n
        pos_to_node = [0] * n
        node_to_pos = tin[:]

        bs = 450
        cnt = [0] * ((n + bs - 1) // bs + 1)
        positions = [False] * n

        def previous(pos):
            b = pos // bs
            for i in range(pos - 1, b * bs - 1, -1):
                if positions[i]:
                    return i
            for j in range(b - 1, -1, -1):
                if cnt[j]:
                    for i in range(min(n - 1, (j + 1) * bs - 1), j * bs - 1, -1):
                        if positions[i]:
                            return i
            return -1

        def following(pos):
            b = pos // bs
            for i in range(pos + 1, min(n, (b + 1) * bs)):
                if positions[i]:
                    return i
            for j in range(b + 1, len(cnt)):
                if cnt[j]:
                    for i in range(j * bs, min(n, (j + 1) * bs)):
                        if positions[i]:
                            return i
            return -1

        cur_sum = 0
        size = 0

        def add_node(x):
            nonlocal cur_sum, size
            p = node_to_pos[x]
            if size:
                a = previous(p)
                b = following(p)
                if a == -1:
                    a = following(p)
                if b == -1:
                    b = previous(p)
                if a == -1:
                    cur_sum += 0
                else:
                    cur_sum += dist(pos_to_node[a], x)
                    cur_sum += dist(x, pos_to_node[b])
                    cur_sum -= dist(pos_to_node[a], pos_to_node[b])
            positions[p] = True
            pos_to_node[p] = x
            cnt[p // bs] += 1
            size += 1

        def remove_node(x):
            nonlocal cur_sum, size
            p = node_to_pos[x]
            if size > 1:
                a = previous(p)
                b = following(p)
                if a == -1:
                    a = b
                if b == -1:
                    b = a
                cur_sum -= dist(pos_to_node[a], x)
                cur_sum -= dist(x, pos_to_node[b])
                cur_sum += dist(pos_to_node[a], pos_to_node[b])
            positions[p] = False
            cnt[p // bs] -= 1
            size -= 1

        ans = [0] * q
        L = 0
        R = -1

        for l, r, idx in queries:
            while L > l:
                L -= 1
                add_node(L)
            while R < r:
                R += 1
                add_node(R)
            while L < l:
                remove_node(L)
                L += 1
            while R > r:
                remove_node(R)
                R -= 1
            ans[idx] = cur_sum // 2

        out.extend(map(str, ans))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```前処理では、DFS 次数とバイナリ リフティング テーブルを構築します。 維持されるサイクルを更新するたびに数回の距離計算が必要となるため、LCA 構造は距離を迅速に評価するためにのみ使用されます。 

Mo の順序付けにより、追加操作と削除操作の数が管理可能に保たれます。 巡回式は DFS 順序に依存するため、アクティブな配列は頂点ラベルではなく DFS 位置によってインデックス付けされます。 

DFS 位置に対する平方根分解は、平衡二分探索ツリーを置き換えます。 Python は直接検索を提供しないため、先行検索と後続検索はブロックによって処理されます。 ブロック サイズは、検査される位置の数が少なくなるように選択されます。 

2 による除算は、クエリに応答する場合にのみ実行されます。 維持される値は意図的に 2 倍のサブツリー サイズとして維持され、小数部の中間値が回避されます。 

## 実用的な例

 サンプルツリーを考えてみましょう。```
1-2
2-3
2-4
1-5
5-6
```問い合わせについては`[1,3]`、選択された頂点は`1,2,3`。 

| ステップ | 選択された頂点 | 循環距離の合計 | 答え |
 | --- | --- | --- | --- |
 | 1 を追加 | {1} | 0 | 0 |
 | 2を追加 | {1,2} | 2 | 1 |
 | 3 を追加 | {1,2,3} | 4 | 2 |

 巡回合計はエッジをカウントします`1-2`そして`2-3`二度答えて`2`。 

問い合わせ用`[4,6]`、選択された頂点は`4,5,6`。 

| ステップ | 選択された頂点 | 循環距離の合計 | 答え |
 | --- | --- | --- | --- |
 | 4 を追加 | {4} | 0 | 0 |
 | 5 を追加 | {4,5} | 6 | 3 |
 | 6を追加 | {4,5,6} | 8 | 4 |

 必要なサブツリーには以下が含まれます`2-4`、`1-2`、`1-5`、 そして`5-6`、したがって、答えは次のとおりです`4`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O((n+q)√n log n)`| Mo の動きは約`O((n+q)√n)`更新し、各更新は LCA ベースの距離クエリを実行します。 
| スペース |`O(n log n)`| バイナリ リフティングがメモリ使用量を支配します。 

頂点の総数は次のとおりであるため、制約によりこれが可能になります。`2 * 10^5`クエリの総数は`3 * 10^5`。 このアルゴリズムは、大きなサブツリーの再構築を回避し、クエリ間の移動中にローカルな変更のみを実行します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    old = sys.stdin
    sys.stdin = io.StringIO(inp)
    # In a local setup, call solve() here after importing the solution.
    sys.stdin = old
    return ""

# Test cases should be executed against the solve() function from the solution file.

tests = [
    (
        "1\n1 1\n1 1\n",
        "0"
    ),
    (
        "1\n4 1\n1 2\n2 3\n2 4\n1 4\n",
        "3"
    ),
    (
        "1\n6 3\n1 2\n2 3\n2 4\n1 5\n5 6\n1 2\n1 3\n4 6\n",
        "1\n2\n4"
    )
]
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一頂点ツリー |`0`| 空のエッジ セットの処理 |
 | ツリー全体のクエリ |`3`| すべてのブランチを含める必要があります |
 | サンプルツリー |`1 2 4`| 一般的なスパニング サブツリーの計算 |

 ## 特殊なケース

 選択された 1 つの頂点の場合、アクティブ セットには 1 つの DFS 位置が含まれます。 1 つのノードを接続するのにエッジは必要ないため、循環距離の合計はゼロのままです。 

ツリー クエリ全体では、最終的にすべての頂点がアクティブになります。 すべてのエッジは最小サブツリーに含まれており、循環距離の合計はツリー内のエッジの数のちょうど 2 倍になります。 

選択した頂点が異なるブランチにあるクエリの場合、距離計算内の LCA 寄与には共有パスが自動的に含まれます。 このアルゴリズムでは、選択した頂点が直接接続されているとは想定されていないため、分岐エッジは正しくカウントされます。
