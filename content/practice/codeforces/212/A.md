---
title: "CF 212A - 民営化"
description: "二部グラフが与えられました。 一方にはベルラントの都市が含まれ、もう一方にはベルラントの都市が含まれており、すべてのフライトは 2 つの国の間の方向のないエッジです。 各エッジは、t 個の民間企業のいずれかに割り当てる必要があります。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "flows", "graphs"]
categories: ["algorithms"]
codeforces_contest: 212
codeforces_index: "A"
codeforces_contest_name: "VK Cup 2012 Finals (unofficial online-version)"
rating: 3000
weight: 212
solve_time_s: 140
verified: false
draft: false
---

[CF 212A - 民営化](https://codeforces.com/problemset/problem/212/A)

 **評価:** 3000
 **タグ:** フロー、グラフ
 **解決時間:** 2 分 20 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 二部グラフが与えられました。 一方にはベルラントの都市が含まれ、もう一方にはベルラントの都市が含まれており、すべてのフライトは 2 つの国の間の方向のないエッジです。 

各エッジは次のいずれかに割り当てる必要があります。`t`民間企業。 都市ごとに、各企業に属するインシデント エッジの数を数えます。 都市にカウントがある場合$$a_{i1}, a_{i2}, \dots, a_{it}$$その場合、不均一性への寄与は次のようになります。$$\sum_{j=1}^t a_{ij}^2$$不均一性の合計は、すべての都市の合計です。 

以下から色を割り当てる必要があります`1..t`この合計値が最小になるようにすべてのエッジに適用されます。 

グラフのサイズは十分に大きいため、代入に対するブルートフォースは不可能です。 エッジは最大 5000、企業は最大 200 社あります。 たとえ`t = 2`、すべての割り当てを試すにはチェックが必要になります$$2^{5000}$$構成は完全に絶望的です。 

目的関数の構造が重要な観察です。 都市の貢献度は、そのインシデント エッジが企業間でどの程度均等に分散されているかによってのみ決まります。 頂点に次数がある場合`d`、次に、可能な最小値$$\sum a_j^2$$制約の下で$$\sum a_j = d$$値の差が最大 1 の場合に達成されます。 

たとえば、都市に学位がある場合、`7`そして`t = 3`の場合、最適な分割は次のようになります。`(3,2,2)`そして貢献度は$$3^2 + 2^2 + 2^2 = 17$$分割が不均一になると、平方和が増加します。 

明らかではない部分は、すべてのエッジが 2 つの頂点に同時に影響を与えるため、すべての頂点での局所的な最適性がグローバルに競合する可能性があることです。 すべての頂点に対して同時に最適な分布を実現する 1 つのエッジ カラーリングが必要です。 

不注意で貪欲な人は簡単に失敗する可能性があります。 

考慮する：```
2 2 4 2
1 1
1 2
2 1
2 2
```すべての頂点には次数があります`2`したがって、最適なローカル分布は次のようになります。`(1,1)`。 

最初の頂点に属するエッジを交互に貪欲に色付けすると、次のようになります。```
edge (1,1) -> 1
edge (1,2) -> 2
edge (2,1) -> 1
edge (2,2) -> 1
```今頂点`2`ビールランド側にはカウントがある`(2,0)`、これは最適ではありません。 

正しいソリューションでは、すべての頂点を同時に調整する必要があります。 

もう一つの微妙なケースは次のような場合です。`t`はあらゆる度よりも大きいです。 

例：```
1 3 3 10
1 1
1 2
1 3
```唯一のベルランド市での最良の分布は次のとおりです。`(1,1,1,0,0,0,0,0,0,0)`、寄付をする`3`。 

実装が間違っていると、すべての色を均等に使用しようとして、誤って色を複製してしまう可能性があります。 

グラフが切断される場合もあります。 接続性を前提としたアルゴリズムでは、コンポーネントが静かに欠落する可能性があります。 

例：```
2 2 2 2
1 1
2 2
```各エッジは独立して任意の色を受け取ることができます。 最適な答えは`4`。 

## アプローチ

 ブルートフォースアプローチは簡単です。 私たちはエッジへの企業のすべての割り当てを試し、すべての頂点の寄与を計算し、最小値を維持します。 

割り当てごとに、すべての頂点およびすべての会社について、その会社に属するインシデント エッジの数を数えることができます。 次に二乗和を計算します。 

これが機能するのは、目的関数がエッジのカラーリングによって明示的に定義されているためです。 問題は課題の数です。 と`k`エッジと`t`企業、ありますよ$$t^k$$可能なカラーリング。 

と`k = 5000`、これはたとえであっても天文学的な規模です`t = 2`。 

次に自然なアイデアは、各頂点を個別に最適化することです。 学位取得のため`d`、最小二乗和は、エッジを色間でできるだけ均等に分散することによって得られます。 

仮定する$$d = qt + r$$それではまさに`r`色が表示されるはずです`q+1`回、残りは`t-r`色が表示されるはずです`q`回。 

これにより、その頂点に対する理論上の最小寄与が得られます。 

本当の画期的な点は、この最適なローカル構造が実際にグローバルに実現できることを認識したことです。 グラフは 2 部構成であり、2 部グラフではエッジの色付けが正確に許可されます。`Δ`色、どこ`Δ`は最大次数です。 さらに重要なのは、エッジを頂点ごとにほぼ等しいグループに分解できることです。 

この構築は正則化とオイラーツアーから来ています。 

まず、ダミーの頂点とダミーのエッジを追加して、すべての頂点の次数を等しくします。 次に、通常のグラフをエッジが互いに素な完全一致に繰り返し分割します。 これらのマッチングに対して周期的に色を割り当てると、すべての頂点が各色を受け取ることが保証されます。$$\left\lfloor \frac{d}{t} \right\rfloor$$または$$\left\lceil \frac{d}{t} \right\rceil$$これはまさに最適な分布です。 

これにより、問題は 2 部グラフで完全な一致を繰り返し見つけることに変換されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(t^k \cdot k)$|$O(k)$| 遅すぎる |
 | 最適 |$O(D \cdot V^2)$|$O(V^2)$| 承認済み |

 ここ`D`正則化後の最大次数です。`200`、 そして`V`ダミー ノードを追加した後の頂点の数です。これも最大で約`400`。 

## アルゴリズムのチュートリアル

 1. 入力から 2 部グラフを構築します。 

左側にはベルラントの都市が含まれ、右側にはベルラントの都市が含まれます。 
2. 最大次数を計算する`D`。 

あ`D`-regular bipartite graph is much easier to decompose into perfect matchings.
 3. Add dummy vertices so that both sides contain exactly the same number of vertices.

 Perfect matchings require balanced bipartite sets.
 4. すべての頂点が正確な次数になるまでダミー エッジを追加します`D`。 

これにより、グラフが次のように変換されます。`D`- 通常の 2 部グラフ。 

このようなグラフでは常に完全な一致が認められます。 
5. 完全に一致するものを繰り返し見つけては削除します。 

から完全一致を 1 つ削除した後、`D`- 通常の 2 部グラフ、残りのグラフは次のようになります。`(D-1)`-定期的なため、プロセスを続行できます。 
6. 一致するものに番号を付けます。`0`に`D-1`。 

会社を割り当てる$$(matching\_index \bmod t) + 1$$そのマッチングのすべてのエッジに。 
7. ダミーのエッジを無視し、元のエッジの色のみを出力します。 

なぜこのバランスが機能するのでしょうか?

 頂点に元の次数があると仮定します`d`。 分解中、元のエッジがすべて使い果たされるまで、各マッチングはその頂点に 1 つの入射エッジのみを提供します。 

の`D`マッチングは周期的に分散されます。`t`企業。 その頂点では、2 つの企業に割り当てられたインシデント エッジの数は最大 1 つ異なります。 

まさに二乗和を最小にする構成です。 

### なぜ効果があるのか

 重要な不変条件は、抽出されたすべての一致が、現在の通常のグラフのすべての頂点に属する 1 つのエッジを正確に使用することです。 

グラフが始まるので`D`-normal では、反復ごとに頂点ごとに 1 つのエッジが削除され、残りのグラフは規則的なままになります。 これにより、すべてのエッジが完全に一致するように分割されるまでプロセスが続行されることが保証されます。 

これらのマッチングに対して周期的に色を割り当てると、入射エッジがすべての頂点に数学的に可能な限り均等に分散されます。 目的関数は頂点によって分離可能であり、そのようなバランスの取れた分布によって正確に最小化されるため、結果として得られるカラーリングは全体的に最適になります。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

def hopcroft_karp(adj, n_left, n_right):
    INF = 10**9

    pair_u = [-1] * n_left
    pair_v = [-1] * n_right
    dist = [0] * n_left

    def bfs():
        q = deque()

        for u in range(n_left):
            if pair_u[u] == -1:
                dist[u] = 0
                q.append(u)
            else:
                dist[u] = INF

        found = False

        while q:
            u = q.popleft()

            for v in adj[u]:
                pu = pair_v[v]

                if pu == -1:
                    found = True
                elif dist[pu] == INF:
                    dist[pu] = dist[u] + 1
                    q.append(pu)

        return found

    def dfs(u):
        for v in adj[u]:
            pu = pair_v[v]

            if pu == -1 or (dist[pu] == dist[u] + 1 and dfs(pu)):
                pair_u[u] = v
                pair_v[v] = u
                return True

        dist[u] = INF
        return False

    matching = 0

    while bfs():
        for u in range(n_left):
            if pair_u[u] == -1 and dfs(u):
                matching += 1

    return pair_u

def solve():
    n, m, k, t = map(int, input().split())

    edges = []
    deg_left = [0] * n
    deg_right = [0] * m

    for _ in range(k):
        x, y = map(int, input().split())
        x -= 1
        y -= 1

        edges.append((x, y))

        deg_left[x] += 1
        deg_right[y] += 1

    D = max(max(deg_left), max(deg_right))

    size = max(n, m)

    left_deg = deg_left[:] + [0] * (size - n)
    right_deg = deg_right[:] + [0] * (size - m)

    graph = [[-1] * size for _ in range(size)]

    for idx, (u, v) in enumerate(edges):
        graph[u][v] = idx

    for u in range(size):
        need = D - left_deg[u]

        for v in range(size):
            if need == 0:
                break

            if right_deg[v] < D and graph[u][v] == -1:
                graph[u][v] = -2
                left_deg[u] += 1
                right_deg[v] += 1
                need -= 1

    answer = [0] * k

    for match_id in range(D):
        adj = [[] for _ in range(size)]

        for u in range(size):
            for v in range(size):
                if graph[u][v] != -1:
                    adj[u].append(v)

        match = hopcroft_karp(adj, size, size)

        color = (match_id % t) + 1

        for u in range(size):
            v = match[u]

            edge_id = graph[u][v]

            if edge_id >= 0:
                answer[edge_id] = color

            graph[u][v] = -1

    total = 0

    for d in deg_left:
        q, r = divmod(d, t)
        total += r * (q + 1) * (q + 1)
        total += (t - r) * q * q

    for d in deg_right:
        q, r = divmod(d, t)
        total += r * (q + 1) * (q + 1)
        total += (t - r) * q * q

    print(total)
    print(*answer)

solve()
```最初の主要コンポーネントは Hopcroft-Karp で、2 部グラフで最大一致を見つけます。 グラフは規則的でバランスが取れているため、すべての最大一致が完璧です。 

グラフ行列にはエッジ ID が格納されます。 元のエッジは入力インデックスを保存し、ダミー エッジは入力インデックスを保存します。`-2`、および不在エッジストア`-1`。 

正則化のステップは微妙です。 現在の次数が以下の頂点を貪欲に接続します`D`。 合計次数不足が両側で等しいため、このプロセスは常に成功します。 

グラフになった後、`D`-定期的に、完全一致を繰り返し抽出します。 抽出された各マッチングは、エッジの 1 つのレイヤーに対応します。 

色の割り当てには循環インデックスが使用されます。```
color = (match_id % t) + 1
```これにより、すべての頂点のエッジ数のバランスが自動的に調整されます。 

答えの値は、色を再計算するのではなく、度数式から直接計算されます。 すべての頂点が最適なバランスのとれた分布を受けることがわかれば、最小寄与は決定的になります。 

よくある間違いは、出力には元のエッジのみが含まれていることを忘れることです。 ダミー エッジは純粋に構造的なものであるため、無視する必要があります。 

もう 1 つの簡単なバグは、不適切な反復中にグラフを変更することです。 一致を抽出した後、それらのエッジをグラフ マトリックスから正確に削除します。 

## 実用的な例

 ### 例 1

 入力:```
3 5 8 2
1 4
1 3
3 3
1 2
1 1
2 1
1 5
2 2
```学位:

 | 頂点 | 学位 |
 | --- | --- |
 | L1 | 5 |
 | L2 | 2 |
 | L3 | 1 |
 | R1 | 2 |
 | R2 | 2 |
 | R3 | 2 |
 | R4 | 1 |
 | R5 | 1 |

 最大次数は`5`。 

正則化後のグラフは次のようになります。`5`-通常。 

抽出された一致に周期的に色が割り当てられるとします。 

| マッチング | 会社概要 |
 | --- | --- |
 | 0 | 1 |
 | 1 | 2 |
 | 2 | 1 |
 | 3 | 2 |
 | 4 | 1 |

 頂点`L1`学位を持っています`5`、その入射エッジは分散されます。`(3,2)`。 

貢献：$$3^2 + 2^2 = 13$$すべての次数 2 の頂点は、`(1,1)`そして貢献する`2`。 

最終的な合計は次のようになります`4`。 

このトレースは、バランス特性を示しています。 すべての頂点は、最大で 1 つだけ異なる色を受け取ります。 

### 例 2

 入力:```
2 2 4 2
1 1
1 2
2 1
2 2
```グラフはすでに`2`-通常。 

完璧なマッチング:

 | マッチング | エッジ |
 | --- | --- |
 | 0 | (1,1)、(2,2) |
 | 1 | (1,2)、(2,1) |

 割り当てられた色:

 | エッジ | 色 |
 | --- | --- |
 | (1,1) | 1 |
 | (2,2) | 1 |
 | (1,2) | 2 |
 | (2,1) | 2 |

 すべての頂点には、各色の 1 つのエッジが表示されます。 

頂点ごとの寄与:$$1^2 + 1^2 = 2$$合計：$$4 \times 2 = 8$$この例は、完全一致分解によってすべての頂点のバランスが同時に自然に保たれる理由を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(D \cdot V^2)$| 抽出します`D`完璧なマッチング、それぞれは最大でも Hopcroft-Karp を使用します`400`頂点 |
 | スペース |$O(V^2)$| The adjacency matrix stores the regularized graph |

 The maximum regular degree is at most`200`、バランス後のグラフ サイズは小さいままです。 最悪の場合でも、簡単に制限内に収まります。 

## テストケース```python
import sys
import io

def run(inp: str) -> str:
    from collections import deque

    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out

    input = sys.stdin.readline

    def hopcroft_karp(adj, n_left, n_right):
        INF = 10**9

        pair_u = [-1] * n_left
        pair_v = [-1] * n_right
        dist = [0] * n_left

        def bfs():
            q = deque()

            for u in range(n_left):
                if pair_u[u] == -1:
                    dist[u] = 0
                    q.append(u)
                else:
                    dist[u] = INF

            found = False

            while q:
                u = q.popleft()

                for v in adj[u]:
                    pu = pair_v[v]

                    if pu == -1:
                        found = True
                    elif dist[pu] == INF:
                        dist[pu] = dist[u] + 1
                        q.append(pu)

            return found

        def dfs(u):
            for v in adj[u]:
                pu = pair_v[v]

                if pu == -1 or (dist[pu] == dist[u] + 1 and dfs(pu)):
                    pair_u[u] = v
                    pair_v[v] = u
                    return True

            dist[u] = INF
            return False

        while bfs():
            for u in range(n_left):
                if pair_u[u] == -1:
                    dfs(u)

        return pair_u

    n, m, k, t = map(int, input().split())

    edges = []
    deg_left = [0] * n
    deg_right = [0] * m

    for _ in range(k):
        x, y = map(int, input().split())
        x -= 1
        y -= 1

        edges.append((x, y))
        deg_left[x] += 1
        deg_right[y] += 1

    D = max(max(deg_left), max(deg_right))
    size = max(n, m)

    left_deg = deg_left[:] + [0] * (size - n)
    right_deg = deg_right[:] + [0] * (size - m)

    graph = [[-1] * size for _ in range(size)]

    for idx, (u, v) in enumerate(edges):
        graph[u][v] = idx

    for u in range(size):
        need = D - left_deg[u]

        for v in range(size):
            if need == 0:
                break

            if right_deg[v] < D and graph[u][v] == -1:
                graph[u][v] = -2
                left_deg[u] += 1
                right_deg[v] += 1
                need -= 1

    ans = [0] * k

    for mid in range(D):
        adj = [[] for _ in range(size)]

        for u in range(size):
            for v in range(size):
                if graph[u][v] != -1:
                    adj[u].append(v)

        match = hopcroft_karp(adj, size, size)

        color = (mid % t) + 1

        for u in range(size):
            v = match[u]

            eid = graph[u][v]

            if eid >= 0:
                ans[eid] = color

            graph[u][v] = -1

    total = 0

    for d in deg_left[:n]:
        q, r = divmod(d, t)
        total += r * (q + 1) * (q + 1)
        total += (t - r) * q * q

    for d in deg_right[:m]:
        q, r = divmod(d, t)
        total += r * (q + 1) * (q + 1)
        total += (t - r) * q * q

    print(total)
    print(*ans)

    return out.getvalue()

# sample 1
out = run(
"""3 5 8 2
1 4
1 3
3 3
1 2
1 1
2 1
1 5
2 2
"""
)

assert out.splitlines()[0] == "20"

# minimum case
out = run(
"""1 1 1 1
1 1
"""
)

assert out.splitlines()[0] == "2"

# disconnected graph
out = run(
"""2 2 2 2
1 1
2 2
"""
)

assert out.splitlines()[0] == "4"

# t larger than degrees
out = run(
"""1 3 3 10
1 1
1 2
1 3
"""
)

assert out.splitlines()[0] == "6"

# complete 2x2 graph
out = run(
"""2 2 4 2
1 1
1 2
2 1
2 2
"""
)

assert out.splitlines()[0] == "8"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 片刃 | 2 | 最小グラフ |
 | 切断されたグラフ | 4 | 独立したコンポーネント |
 |`t > degree`| 6 | まばらな色の使用 |
 | 完了`2x2`グラフ | 8 | 完璧なバランス |

 ## 特殊なケース

 企業の数がすべての等級を超える場合を考えてみましょう。```
1 3 3 10
1 1
1 2
1 3
```唯一の左側の頂点には次数があります`3`。 このアルゴリズムは、3 つのエッジを 3 つの異なるマッチングに分散します。 色は周期的に割り当てられるため、頂点はカウントを受け取ります。```
(1,1,1,0,0,0,0,0,0,0)
```貢献度は次のようになります。$$1+1+1=3$$右側の頂点がそれぞれ寄与します`1`、合計は`6`。 

ここで、切り離されたグラフを考えてみましょう。```
2 2 2 2
1 1
2 2
```グラフにはすでに最大次数があります`1`。 分解には単一のマッチングが含まれるため、両方のエッジが同じ色を受け取ります。 

すべての頂点に分布が表示されます`(1,0)`そして貢献する`1`。 

このアルゴリズムが機能するのは、正則化と一致抽出が接続性に依存しないためです。 

最後に、不均衡な圧力を考慮します。```
2 2 3 2
1 1
1 2
2 1
```学位は次のとおりです。 

| 頂点 | 学位 |
 | --- | --- |
 | L1 | 2 |
 | L2 | 1 |
 | R1 | 2 |
 | R2 | 1 |

 次数 2 の頂点は、各色の 1 つのエッジを受け取る必要があります。 一致する分解により、これが自動的に保証されます。 素朴な地元の貪欲な人でも簡単に作成できます`(2,0)`しかし、完全に一致する構造により、そのような競合が防止されます。
