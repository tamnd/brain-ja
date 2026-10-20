---
title: "CF 105809C - 3D のチェス"
description: "$A x B x C$ の寸法を持つ 3 次元のチェス盤があります。 一部のセルはブロックされており、ナイトを含めることができません。 残りのすべてのセルが 3D ナイトの潜在的な位置になります。"
date: "2026-06-25T15:28:37+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105809
codeforces_index: "C"
codeforces_contest_name: "Code Rush 2025"
rating: 0
weight: 105809
solve_time_s: 54
verified: true
draft: false
---

[CF 105809C - 3D のチェス](https://codeforces.com/problemset/problem/105809/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 54 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 寸法のある 3 次元のチェス盤があります$A \times B \times C$。 一部のセルはブロックされており、ナイトを含めることができません。 残りのすべてのセルが 3D ナイトの潜在的な位置になります。 

騎士は、三次元に拡張された通常のチェスのパターンによって記述されたとおりに正確に動きます。 2 つの軸を選択し、一方に沿って 2 移動し、もう一方に沿って 1 移動しますが、3 番目の座標は変更されません。 ボードから離れる手は無効です。 目標は、2 人の騎士が一度の動きで互いに攻撃しないように、できるだけ多くの騎士を配置することです。 

ボードの寸法は各方向で最大 10 であるため、ボード全体に含まれるのは最大でも 10 です。$10^3 = 1000$細胞。 ブロックされたセルを削除する前でも、使用できる位置の数は十分に少ないため、グラフの頂点の観点から考えることができます。 課題はセルの数ではなく、最大の非攻撃サブセットを選択するという組み合わせの性質です。 

すべてのサブセットを単純に検索するには、最大で次の点までを調べる必要があります。$2^{1000}$完全に不可能な構成です。 ナイトムーブグラフの構造を活用する必要があります。 

微妙なケースが 1 つありますが、ボードが非常に小さくてナイトの動きが存在しない場合に発生します。 

例：```
1 1 1
1
1 1 1
```使用可能なセルがないため、答えは 0 です。少なくとも 1 人の騎士を配置できることを前提とした解決策は失敗します。 

もう 1 つの興味深いケースは、利用可能なセルはあるものの、攻撃ペアが存在しないボードです。```
1 1 2
1
1 1 1
```使用可能なセルは 1 つだけ残ります。 答えは 1 です。グラフベースのソリューションは、孤立した頂点を正しく処理する必要があります。 

さらに危険な間違いは、ブロックされたセルをグラフから削除し忘れることです。 考慮する：```
3 3 3
1
2 2 2
```中央のセルは使用できません。 それでもその頂点を通るエッジを作成したり、候補位置としてカウントしたりすると、実装に応じて最終的な答えが大きすぎたり小さすぎたりします。 正解は14です。 

## アプローチ

 最も直接的な解釈は、使用可能なすべてのセルが頂点であり、そこに配置された騎士が互いに攻撃する場合にエッジが 2 つのセルを接続するグラフを構築することです。 次に、タスクは、どのペア間にもエッジがない頂点の最大のセットを見つけることになります。 グラフ理論では、これは最大独立集合です。 

ブルートフォース手法では、使用可能なセルのすべてのサブセットを試し、攻撃ペアが存在するかどうかを確認します。 あれば$N$使用可能なセル、これには必要です$2^N$サブセット。 たとえ$N = 50$、これはすでに絶望的ですが、実際の制限は最大 1000 セルです。 

重要な観察は、座標のパリティから得られます。 

ナイトの移動により座標が変化します。$(\pm2,\pm1,0)$ある順序で。 合計の変化は、$x+y+z$は常に奇数であるため、$2+1=3$。 つまり、騎士が動くたびに、次の等価性が反転します。$x+y+z$。 

すべてのセルをパリティに従って色付けすると、$x+y+z$、すべてのエッジが反対の色を接続します。 攻撃グラフは 2 部構成です。 

古典的な定理のおかげで、この問題ははるかに簡単になります。 どのグラフでも次のようになります。$$\text{Maximum Independent Set}
=
|V|
-
\text{Minimum Vertex Cover}$$二部グラフの場合、ケーニッヒの定理は次のように述べています。$$\text{Minimum Vertex Cover}
=
\text{Maximum Matching}$$両方の事実を組み合わせると、次のようになります。$$\text{Answer}
=
|V|
-
\text{Maximum Matching}$$したがって、最大の独立セットを直接検索する代わりに、ナイト攻撃の 2 部グラフを構築し、最大一致を計算し、使用可能なセルの数からそのサイズを差し引きます。 

ボードには最大 1000 個の使用可能なセルがあります。 各セルには一定数のナイトの動きしか含まれていないため、グラフはまばらなままです。 Hopcroft-Karp はこのサイズを簡単に処理できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(2^N)$|$O(N)$| 遅すぎる |
 | 最適 |$O(E\sqrt{V})$|$O(V+E)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. ボードの寸法を読み、ブロックされているすべてのセルにマークを付けます。 
2. 使用可能なすべてのセルに整数 ID を割り当てます。 これらの ID がグラフの頂点になります。 
3. パリティに従って頂点を 2 つのパーティションに分割します。$x+y+z$。 
4. 偶数パーティション内の使用可能なすべてのセルに対して、可能なすべてのナイトの動きを生成します。 
5. 宛先セルがボード内にあり、ブロックされていない場合は、対応する 2 つの頂点の間にエッジを追加します。 
6. 結果の 2 部グラフに対して Hopcroft-Karp を実行して、最大一致を計算します。 
7. しましょう$N$は使用可能なセルの数であり、$M$一致する最大サイズになります。 
8. 出力$N-M$。 

ステップ 8 が正しい理由は、一連の等式にあります。$$\text{Maximum Independent Set}
=
N-\text{Minimum Vertex Cover}
=
N-\text{Maximum Matching}$$### なぜ効果があるのか

 ナイトの動きごとにパリティが変化するため、攻撃グラフは 2 つに分かれます。 選択された 2 つの頂点が攻撃エッジを共有できないため、有効なナイトの配置はすべて、このグラフの独立したセットに正確に対応します。 

2 部グラフの場合、ケーニッヒの定理は最小頂点カバー問題を最大マッチング問題に変換します。 最小の頂点カバーの補数は最大の独立したセットであるため、頂点の総数から一致するサイズを減算すると、相互に攻撃しないナイトの可能な最大のセットが得られます。 有効な配置にはこの値を超える頂点を含めることはできず、定理によりそのような配置が存在することが保証されます。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

def solve():
    A, B, C = map(int, input().split())
    K = int(input())

    blocked = set()
    for _ in range(K):
        x, y, z = map(int, input().split())
        blocked.add((x - 1, y - 1, z - 1))

    cell_id = {}
    cells = []

    for x in range(A):
        for y in range(B):
            for z in range(C):
                if (x, y, z) not in blocked:
                    cell_id[(x, y, z)] = len(cells)
                    cells.append((x, y, z))

    n = len(cells)

    moves = []
    for axis2 in [2, -2]:
        for axis1 in [1, -1]:
            moves.extend([
                (axis2, axis1, 0),
                (axis2, 0, axis1),
                (axis1, axis2, 0),
                (0, axis2, axis1),
                (axis1, 0, axis2),
                (0, axis1, axis2),
            ])

    left_vertices = []
    for idx, (x, y, z) in enumerate(cells):
        if (x + y + z) % 2 == 0:
            left_vertices.append(idx)

    adj = [[] for _ in range(n)]

    for idx in left_vertices:
        x, y, z = cells[idx]

        for dx, dy, dz in moves:
            nx, ny, nz = x + dx, y + dy, z + dz

            if not (0 <= nx < A and 0 <= ny < B and 0 <= nz < C):
                continue

            if (nx, ny, nz) not in cell_id:
                continue

            adj[idx].append(cell_id[(nx, ny, nz)])

    INF = 10 ** 18

    pair_u = [-1] * n
    pair_v = [-1] * n
    dist = [0] * n

    def bfs():
        q = deque()

        for u in left_vertices:
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
        for u in left_vertices:
            if pair_u[u] == -1 and dfs(u):
                matching += 1

    print(n - matching)

solve()
```最初の部分では、使用可能なセルのセットを構築し、それぞれにコンパクトな頂点 ID を割り当てます。 整数 ID を使用すると、照合の実装がはるかに簡単になります。 

動きの生成では、24 のナイトの動きすべてが 3 次元で明示的に作成されます。 いくつかの実装では、誤って一部の座標の順列が欠落し、エッジが欠落し、大きすぎる答えを持つグラフが生成されます。 

偶数パリティの頂点のみが出力エッジを作成します。 これにより、すべてのエッジを 2 回保存することがなくなり、自然に 2 部グラフの左側が形成されます。 

Hopcroft-Karp 実装では、標準の BFS レイヤ化フェーズとそれに続く DFS 拡張が使用されます。 グラフはまばらなので、制限内で快適に実行されます。 

バグの一般的な原因は、ブロックされたセルが頂点として存在しないことを忘れることです。 すべての宛先をチェックする必要があります`cell_id`エッジを追加する前。 

## 実用的な例

 ### 例 1```
1 1 2
1
1 1 1
```使用可能なセル:

 | セル | パリティ |
 | --- | --- |
 | (1,1,2) | 奇数 |

 騎士の動きはありません。 

| 数量 | 値 |
 | --- | --- |
 | 使用可能な頂点 | 1 |
 | 適合サイズ | 0 |
 | 答え | 1 |

 これは、孤立した頂点が自動的に最大独立セットに属することを示しています。 

### 例 2```
3 3 3
1
2 2 2
```合計 26 個のセルと 1 つのブロックされたセルがあります。 

| 数量 | 値 |
 | --- | --- |
 | セルの総数 | 27 |
 | ブロックされたセル | 1 |
 | 使用可能な頂点 | 26 |

 Hopcroft-Karp を実行すると、次のことがわかります。 

| 数量 | 値 |
 | --- | --- |
 | 最大一致 | 12 |
 | 答え | 14 |

 結果はサンプル出力と一致します。 

この例は、ナイト配置から二部マッチングへの完全な削減を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(E\sqrt{V})$| Hopcroft-Karp 最大一致 |
 | スペース |$O(V+E)$| グラフの保存と配列の照合 |

 ボードには最大 1000 個の使用可能なセルが含まれます。 各セルには最大 24 個のナイトの動きがあります。$E$ほんの数万です。 このサイズのグラフ上の Hopcroft-Karp は、1 秒の制限を十分に上回る速度です。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

def run(inp: str) -> str:
    from collections import deque

    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline

    A, B, C = map(int, input().split())
    K = int(input())

    blocked = set()
    for _ in range(K):
        x, y, z = map(int, input().split())
        blocked.add((x - 1, y - 1, z - 1))

    cell_id = {}
    cells = []

    for x in range(A):
        for y in range(B):
            for z in range(C):
                if (x, y, z) not in blocked:
                    cell_id[(x, y, z)] = len(cells)
                    cells.append((x, y, z))

    n = len(cells)

    moves = []
    for a in [2, -2]:
        for b in [1, -1]:
            moves.extend([
                (a, b, 0),
                (a, 0, b),
                (b, a, 0),
                (0, a, b),
                (b, 0, a),
                (0, b, a),
            ])

    left = [i for i, (x, y, z) in enumerate(cells)
            if (x + y + z) % 2 == 0]

    adj = [[] for _ in range(n)]

    for u in left:
        x, y, z = cells[u]
        for dx, dy, dz in moves:
            nx, ny, nz = x + dx, y + dy, z + dz
            if (nx, ny, nz) in cell_id:
                adj[u].append(cell_id[(nx, ny, nz)])

    INF = 10**18
    pair_u = [-1] * n
    pair_v = [-1] * n
    dist = [0] * n

    def bfs():
        q = deque()
        found = False

        for u in left:
            if pair_u[u] == -1:
                dist[u] = 0
                q.append(u)
            else:
                dist[u] = INF

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
        for u in left:
            if pair_u[u] == -1 and dfs(u):
                matching += 1

    return str(n - matching) + "\n"

# sample
assert run("3 3 3\n1\n2 2 2\n") == "14\n"

# minimum usable board
assert run("1 1 1\n1\n1 1 1\n") == "0\n"

# single available cell
assert run("1 1 2\n1\n1 1 1\n") == "1\n"

# no knight moves anywhere
assert run("2 2 2\n1\n1 1 1\n") == "7\n"

# one-dimensional line
assert run("1 1 3\n1\n1 1 1\n") == "2\n"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1×1×1 完全ブロック | 0 | 空のグラフ |
 | 1×1×2、1つのブロックされたセル | 1 | 単一の孤立した頂点 |
 | 2×2×2 | 7 | 有効なナイトの動きが存在しません |
 | 1×1×3 | 2 | 縮退ディメンションの処理 |
 | サンプルケース | 14 | 完全一致ロジック |

 ## 特殊なケース

 完全にブロックされたボードを考えてみましょう。```
1 1 1
1
1 1 1
```頂点は作成されません。 一致するサイズは 0 であり、アルゴリズムは次のように出力します。$0 - 0 = 0$。 空の配置が唯一の有効な配置です。 

使用可能なセルが 1 つあるボードを考えてみましょう。```
1 1 2
1
1 1 1
```グラフには頂点が 1 つあり、エッジはありません。 Hopcroft-Karp は一致するものを見つけませんでした。 答えは次のようになります$1 - 0 = 1$、単独のセルには常にナイトを含めることができるため、これは最適です。 

次のサンプルを考えてみましょう。```
3 3 3
1
2 2 2
```ブロックされた中央セルはグラフに挿入されません。 すべての攻撃エッジは、使用可能なセル間でのみ生成されます。 一致するサイズは 12 であるため、独立したセットの最大サイズは次のとおりです。$26 - 12 = 14$。 これにより、ブロックされたセルが正しく処理され、グラフにも最終的なカウントにも関与していないことが確認されます。
