---
title: "CF 105710H - ロボトミー手術"
description: "私たちにはニューロンのネットワークが与えられており、各ニューロンはノードであり、各シナプスは 2 つのニューロン間の無向接続です。 標準的な単純なグラフとは異なり、複数のシナプスが同じニューロンのペアに接続でき、シナプスはニューロンをそれ自体に接続することもできます。"
date: "2026-06-26T08:01:06+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105710
codeforces_index: "H"
codeforces_contest_name: "UTPC Contest 2-12-25 Div. 1 (Advanced)"
rating: 0
weight: 105710
solve_time_s: 70
verified: true
draft: false
---

[CF 105710H - Lobotomy](https://codeforces.com/problemset/problem/105710/H)

 **評価:** -
 **タグ:** -
 **Solve time:** 1m 10s
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちにはニューロンのネットワークが与えられており、各ニューロンはノードであり、各シナプスは 2 つのニューロン間の無向接続です。 標準的な単純なグラフとは異なり、複数のシナプスが同じニューロンのペアに接続でき、シナプスはニューロンをそれ自体に接続することもできます。 

The doctor wants to perform exactly one cut, meaning we remove a single synapse. シナプスを除去すると脳が少なくとも 2 つの切断されたコンポーネントに分割され、一部のニューロンが他のニューロンに到達できなくなる場合、シナプスは危険であると考えられます。 The task is to identify all synapses whose removal would disconnect the graph, and output their identifiers in sorted order. そのようなシナプスが存在しない場合は、-1 を出力します。 

Each synapse has a unique integer ID, and we must reason in terms of these IDs, not just endpoints.

 The constraint on the number of neurons and synapses goes up to a few hundred thousand. This immediately rules out any quadratic or per-edge re-simulation approach. Any solution that tries to remove each edge and run a BFS or DFS would cost O(m(n + m)), which is far too large.

 The key subtlety is that the graph is not simple. Parallel edges completely change the definition of a “critical” connection. たとえば、2 つのシナプスが同じニューロンのペアに接続している場合、基礎となる構造が脆弱であっても、そのうちの 1 つを削除してもそのペアが切断されることはありません。 

A naive approach that ignores this will fail on cases like:

 入力:```
3 2
1 0 1
2 0 1
```ここでは、同じノードのペアの間に 2 つの並列シナプスがあります。 単純なグラフではエンドポイントがブリッジのように見えますが、もう一方のエッジはまだ接続を維持しているため、どちらのエッジも削除する必要はありません。 単純な橋発見アルゴリズムでは、誤って橋としてラベル付けされてしまいます。 

自己ループでは別の障害モードが発生します。 ニューロンからそれ自体へのシナプスは、異なるコンポーネント間の接続には決して寄与しないため、シナプスを削除してもグラフが切断されることはありません。 通常のエッジのように扱うと、誤った組み込みが発生します。 

## アプローチ

 ブルート フォース ソリューションでは、すべてのシナプスを反復処理して削除し、DFS を実行してグラフがまだ接続されているかどうかを確認します。 各接続チェックには O(n + m) のコストがかかるため、合計の複雑さは O(m(n + m)) になります。 最大 200,000 のエッジでは、これは実現不可能です。 

問題の構造は、実際に無向グラフ内の橋を見つけるように求められていることを示唆しています。 ブリッジは、削除すると接続されるコンポーネントの数が増加するエッジです。 古典的な解決策は、DFS タイムスタンプとローリンク値を使用する Tarjan のアルゴリズムで、すべてのブリッジを線形時間 O(n + m) で計算します。 

ただし、複数のエッジが存在すると、複雑さが生じます。 Tarjan のアルゴリズムは単純なグラフを前提としています。 2 つのノードが複数のエッジで接続されている場合、少なくとも 1 つの代替直接ルートが常に残るため、それらのエッジはいずれもブリッジとみなされません。 

これは重要な改良点につながります。まず、順序のないノードの各ペア間に存在するシナプスの数を追跡することによって、マルチグラフ構造を圧縮します。 次に、標準的なブリッジ検索 DFS を実行しますが、エッジが DFS の意味でブリッジであり、エンドポイント間の唯一のシナプスである場合にのみ、エッジを有効なブリッジとして受け入れます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 各エッジを削除 + DFS | O(m(n + m)) | O(n + m) | 遅すぎる |
 | 多重度フィルタリングを備えた Tarjan ブリッジ | O(n + m) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 各シナプスを ID を持つ個別のエッジとして扱いますが、エンドポイントのペアごとにエッジをグループ化することもできます。 

1. ニューロンごとに、すべてのインシデント シナプスとその宛先および ID を格納する隣接リストを構築します。 同時に、順序のないニューロンの各ペアを接続するシナプスの数を数えます。 これにより、後で一意のエッジと重複エッジを区別できるようになります。 
2. グラフに対して DFS を実行して、検出時間と低リンク値を計算します。 これらの値は、各サブツリーから最も早く到達可能な祖先を取得します。これは、ブリッジを検出するための標準ツールです。 
3. DFS 中に、u から v にエッジをトラバースするとき、リバース ツリー エッジをすぐにスキップしますが、それ以外の場合は標準の Tarjan ロジックを適用します。つまり、v にアクセスした後、low[v] > disc[u] かどうかを確認します。 この不等式が成り立つ場合、エッジ (u, v) がブリッジ候補になります。 
4. そのエッジをブリッジとして最終決定する前に、ペア (u, v) に複数のシナプスがあるかどうかを確認します。 存在する場合、冗長性により削除後でも接続が保証されるため、u と v の間のすべてのエッジをブリッジから破棄します。 
5. 両方の条件を満たすすべてのシナプス ID を収集します。それらは DFS ブリッジであり、一意のエッジ ペアに属しています。 
6. 最後に、結果の ID をソートして出力します。 存在しない場合は、-1 を出力します。 

なぜそれが機能するのかは、2 つの不変条件に関係しています。 まず、DFS ローリンク値は、サブツリーに祖先に対する代替バック エッジがあるかどうかを正確に特徴付けます。これはまさに、エッジが接続に必須ではないための条件です。 第 2 に、エッジの多重度は独立した構造保証として機能します。つまり、2 つの頂点が少なくとも 2 つのエッジを共有している場合、DFS 構造に関係なく、1 つを削除してもそれらを切断することはできません。 これら 2 つの事実を組み合わせると、報告されたすべてのシナプスが、グラフの 2 つの部分間の真に固有の重要な接続であることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(10**7)

n, m = map(int, input().split())

edges = []
adj = [[] for _ in range(n)]
pair_count = {}

for i in range(m):
    eid, u, v = map(int, input().split())
    edges.append((u, v, eid))
    adj[u].append((v, i))
    adj[v].append((u, i))

    a, b = (u, v) if u <= v else (v, u)
    pair_count[(a, b)] = pair_count.get((a, b), 0) + 1

disc = [-1] * n
low = [0] * n
timer = 0
is_bridge = [False] * m

def dfs(u, pe):
    global timer
    disc[u] = low[u] = timer
    timer += 1

    for v, eid in adj[u]:
        if eid == pe:
            continue

        if disc[v] == -1:
            dfs(v, eid)
            low[u] = min(low[u], low[v])

            if low[v] > disc[u]:
                is_bridge[eid] = True
        else:
            low[u] = min(low[u], disc[v])

for i in range(n):
    if disc[i] == -1:
        dfs(i, -1)

res = []

for i, (u, v, eid) in enumerate(edges):
    a, b = (u, v) if u <= v else (v, u)
    if is_bridge[i] and pair_count[(a, b)] == 1:
        res.append(eid)

if not res:
    print(-1)
else:
    res.sort()
    print(*res)
```DFS は、標準ブリッジ アルゴリズムとまったく同じように、検出アレイとローリンク アレイを維持します。 唯一の違いは、同じエンドポイント間に複数のシナプスが存在する可能性があるため、エッジのアイデンティティを明示的に追跡することです。 の`pe`このパラメータは、元のエッジをすぐに再訪問することを防ぎます。これは、無向 DFS で誤ったサイクルを回避するために必要です。 

の`pair_count`map enforces the multigraph correction. Tarjan がエッジを構造的にブリッジとしてマークしたとしても、それがエンドポイント間の唯一の接続である場合にのみ受け入れられます。 

実装の微妙な詳細は、複数の個別のシナプスが同じノードに接続する可能性があるため、ブリッジ情報をノード ペアごとではなくエッジ インデックスごとに保存することです。 

## 実用的な例

 ### 例 1

 入力:```
4 4
212 3 0
238 1 2
394 2 0
281 0 1
```| ステップ | 現在のノード | DFS アクション | 更新頻度が低い | 橋が見つかりました |
 | --- | --- | --- | --- | --- |
 | 1 | 0 | DFS を開始する | 低[0]=0 | なし |
 | 2 | 1 | 0からの訪問 | 低[1]=1 | なし |
 | 3 | 2 | 1からの訪問 | 低[2]=2 | エッジ238候補 |
 | 4 | 0 | 次のエッジを探索 | バックエッジ経由で低値を更新 | なし |
 | 5 | 仕上げ | エッジをチェックする | 一意性を検証する | 212 を保持 |

 クリティカルになる唯一のエッジはシナプス２１２である。これは、シナプス２１２の除去により、代替のバックエッジまたは並列接続を介して再接続できないサブツリーが分離されるためである。 

このトレースは、上位祖先へのバック エッジが存在しない場合にのみ、単一の DFS ツリー エッジがどのようにブリッジになるかを示しています。 

### 例 2

 入力:```
3 3
101 0 1
102 0 2
103 2 1
```| ステップ | DFS ツリー | 低い値 | ブリッジの状態 |
 | --- | --- | --- | --- |
 | 0 | 0-1-2 構造はサイクルを形成します | すべてのノードが到達可能 | いいえ low[v] > disc[u] |
 | 1 | サイクルが検出されました | リンクが低いと崩壊する | 橋がない |

 ここでは、すべてのエッジがサイクルに参加しているため、各ノードには代替ルートがあります。 ローリンク値は常にルートに伝播し、ブリッジ状態がトリガーされるのを防ぎます。 

最終出力は -1 で、グラフ全体が単一カットの下で接続されたままであることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m) | DFS は各ノードとエッジに 1 回アクセスし、一定時間のブリッジ チェックを行います。 
| スペース | O(n + m) | 隣接リスト、再帰スタック、および補助配列 |

 最大 200,000 のシナプスを考慮すると、線形の複雑さが必要になります。 繰り返しのグラフ走査に依存するソリューションは制限時間を超過しますが、このアプローチでは構造が単一パスで処理されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys as _sys
    input = _sys.stdin.readline

    n, m = map(int, input().split())
    edges = []
    adj = [[] for _ in range(n)]
    pair_count = {}

    for i in range(m):
        eid, u, v = map(int, input().split())
        edges.append((u, v, eid))
        adj[u].append((v, i))
        adj[v].append((u, i))
        a, b = (u, v) if u <= v else (v, u)
        pair_count[(a, b)] = pair_count.get((a, b), 0) + 1

    sys.setrecursionlimit(10**7)
    disc = [-1]*n
    low = [0]*n
    timer = 0
    is_bridge = [False]*m

    def dfs(u, pe):
        nonlocal timer
        disc[u] = low[u] = timer
        timer += 1
        for v, eid in adj[u]:
            if eid == pe:
                continue
            if disc[v] == -1:
                dfs(v, eid)
                low[u] = min(low[u], low[v])
                if low[v] > disc[u]:
                    is_bridge[eid] = True
            else:
                low[u] = min(low[u], disc[v])

    for i in range(n):
        if disc[i] == -1:
            dfs(i, -1)

    res = []
    for i, (u, v, eid) in enumerate(edges):
        a, b = (u, v) if u <= v else (v, u)
        if is_bridge[i] and pair_count[(a, b)] == 1:
            res.append(eid)

    return " ".join(map(str, sorted(res))) if res else "-1"

# custom cases

# single edge is bridge
assert run("2 1\n5 0 1\n") == "5", "single edge"

# cycle has no bridges
assert run("3 3\n1 0 1\n2 1 2\n3 2 0\n") == "-1", "cycle"

# parallel edges block bridges
assert run("2 2\n10 0 1\n11 0 1\n") == "-1", "parallel edges"

# chain
assert run("4 3\n1 0 1\n2 1 2\n3 2 3\n") == "1 2 3", "chain"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 片刃 | 5 | 基本的なブリッジ検出 |
 | サイクル | -1 | サイクル内に橋はありません |
 | 平行エッジ | -1 | 多重度の処理 |
 | チェーン | 1 2 3 | すべてのエッジはブリッジです |

 ## 特殊なケース

 自己ループは、最も単純で自明ではないケースです。 次のような入力の場合:```
2 1
100 0 0
```DFS はノードからそれ自身へのエッジを認識します。 そもそも異なるコンポーネントを接続していないため、ブリッジ条件を満たすことはありません。 新しいノードにつながるツリー エッジとして表示されず、 low[v] > disc[u] も満たさないため、アルゴリズムはこれを正しく無視します。 

同じノード間の平行エッジは、構造 DFS だけでは不十分な理由を示しています。```
2 2
1 0 1
2 0 1
```単純なトラバーサルでは各エッジが個別に候補ブリッジのように見えても、pair_count チェックによってそれらが除外されます。 いずれかのシナプスを削除した後も両方のエンドポイントは接続されたままとなるため、どちらも出力されません。 

ノードの線形チェーンは、意図したポジティブなケースを示しています。```
4 3
1 0 1
2 1 2
3 2 3
```すべてのエッジはエンドポイント間の唯一の接続であり、バックエッジは存在しないため、それぞれがブリッジ条件を満たし、正しく報告されます。
