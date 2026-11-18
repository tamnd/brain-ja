---
title: "CF 2080A - 強力な接続性の逆襲"
description: "n 個の頂点と m 個のエッジを持つ有向グラフが与えられます。 タスクは、グラフを強く接続するために追加する必要があるエッジの最小数を決定することです。 すべての頂点から他のすべての頂点への有向パスが存在する場合、グラフは強く接続されています。"
date: "2026-06-08T06:25:39+07:00"
tags: ["codeforces", "competitive-programming", "*special", "constructive-algorithms", "graphs"]
categories: ["algorithms"]
codeforces_contest: 2080
codeforces_index: "A"
codeforces_contest_name: "XIX Open Olympiad in Informatics - Final Stage, Day 2 (Unrated, Online Mirror, IOI rules)"
rating: 3200
weight: 2080
solve_time_s: 56
verified: true
draft: false
---

[CF 2080A - 強力な接続性の逆襲](https://codeforces.com/problemset/problem/2080/A)

 **評価:** 3200
 **タグ:** *特別で建設的なアルゴリズム、グラフ
 **解決時間:** 56 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 次のような有向グラフが与えられます。`n`頂点と`m`エッジ。 タスクは、グラフを**強く接続**するために追加する必要があるエッジの最小数を決定することです。 すべての頂点から他のすべての頂点への有向パスが存在する場合、グラフは強く接続されています。 

入力は次のもので構成されます`n`そして`m`、 に続く`m`頂点間の有向エッジを表す整数のペア。 出力は単一の整数、つまり強力な接続を実現するために必要なエッジの最小数です。 

制約が意味するのは、`n`最大 10^5 にすることができ、`m`up to 10^5. これにより、各頂点からの到達可能性を繰り返しチェックするなど、二次的な複雑さを伴うアルゴリズムが除外されます。 線形または線形に近い解決策が必要です。 

Edge cases include:

 - すでに強く接続されたグラフ。 たとえば、エッジのある三角形`1→2`、`2→3`、`3→1`。 答えは次のようになります。`0`。 
- 孤立した頂点を持つグラフ。 例えば、`n=3`そしてエッジはありません。 各頂点は独自の強く接続されたコンポーネントであるため、複数のエッジを追加する必要があります。 
- 単一の頂点を持つグラフ。 答えはいつも`0`エッジ関係なく。 

## アプローチ

 ブルート フォース アプローチでは、頂点の各ペアを考慮し、それらの間にパスが存在するかどうかを確認します。 グラフの場合`n`頂点、これには必要になる場合があります`O(n^3)`Floyd-Warshall を使用した操作、または各頂点からの BFS/DFS の繰り返しを使用します。 これでは遅すぎます`n=10^5`。 

重要な洞察は、**強接続コンポーネント (SCC)** から得られます。 各 SCC は内部で強力に接続されています。 SCC のグラフは、有向非巡回グラフ (DAG) を形成します。 元のグラフを強く接続するには、DAG 内の SCC を接続して単一の SCC にするだけです。 

させて`sources`は入力エッジのない SCC の数であり、`sinks`は、発信エッジのない SCC の数になります。 この場合、必要なエッジの最小数は次のようになります。`max(sources, sinks)`。 これが機能するのは、各ソースには入力エッジが必要であり、各シンクには出力エッジが必要であり、DAG はサイクル内で接続する必要があるためです。 

SCC を効率的に計算する標準アルゴリズムは **Kosaraju のアルゴリズム** または **Tarjan のアルゴリズム** で、どちらも次の環境で実行されます。`O(n + m)`時間。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^3) | O(n^2) | 遅すぎる |
 | SCC + DAG 分析 | O(n + m) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 頂点数を読み取る`n`そしてエッジ`m`、グラフの隣接リストを構築します。 
2. Kosaraju のアルゴリズムを実行して、強く接続されたコンポーネントをすべて見つけます。 これには、終了時刻を記録するための DFS パス、グラフの転置、および終了時刻の逆順の DFS が含まれます。 
3. SCC の要約グラフを構築します。ここで、各 SCC はノードであり、元のグラフの 2 つの SCC 間にエッジがある場合はエッジが存在します。 
4. 入力エッジのない SCC の数を数えます (`sources`) であり、出力エッジはありません (`sinks`）。 
5. SCC の数が 1 の場合、グラフはすでに強く接続されています。 それ以外の場合は戻ります`max(sources, sinks)`。 

**機能する理由:** SCC は、グラフをそれ以上分割できないコンポーネントに分割します。 SCC DAG でソースとシンクを接続すると、すべてのコンポーネントが他のコンポーネントに到達できるようになります。 ソースとシンクの最大数により、いずれの方法でもコンポーネントが切断されたままにならないことが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(10**6)

def kosaraju_scc(n, adj):
    visited = [False] * n
    order = []

    def dfs(u):
        visited[u] = True
        for v in adj[u]:
            if not visited[v]:
                dfs(v)
        order.append(u)

    for u in range(n):
        if not visited[u]:
            dfs(u)

    transpose = [[] for _ in range(n)]
    for u in range(n):
        for v in adj[u]:
            transpose[v].append(u)

    comp_id = [-1] * n
    def dfs_rev(u, label):
        comp_id[u] = label
        for v in transpose[u]:
            if comp_id[v] == -1:
                dfs_rev(v, label)

    label = 0
    for u in reversed(order):
        if comp_id[u] == -1:
            dfs_rev(u, label)
            label += 1

    return comp_id, label

def main():
    n, m = map(int, input().split())
    adj = [[] for _ in range(n)]
    for _ in range(m):
        u, v = map(int, input().split())
        adj[u - 1].append(v - 1)

    comp_id, num_scc = kosaraju_scc(n, adj)
    if num_scc == 1:
        print(0)
        return

    in_deg = [0] * num_scc
    out_deg = [0] * num_scc
    for u in range(n):
        for v in adj[u]:
            if comp_id[u] != comp_id[v]:
                out_deg[comp_id[u]] += 1
                in_deg[comp_id[v]] += 1

    sources = sum(1 for deg in in_deg if deg == 0)
    sinks = sum(1 for deg in out_deg if deg == 0)
    print(max(sources, sinks))

if __name__ == "__main__":
    main()
```最初の DFS は終了時間を計算し、転置グラフ上の 2 番目の DFS は SCC にラベルを付けます。 各 SCC の入次数と出次数をカウントすることで、ソースとシンクを特定します。 1 から始まる入力には注意してください。 それを 0 から始まるインデックスに変換します。 

## 実用的な例

 **サンプル入力 1:**```
3 2
1 2
2 3
```| ステップ | 注文 | 移調 | comp_id | ソース | シンク |
 | --- | --- | --- | --- | --- | --- |
 | DFS | [0、1、2] | [[ ]、[0]、[1]] | [0,1,2] | 1 | 1 |

 説明: 各ノードは独自の SCC です。 ノード 1 には入力エッジ (ソース) がなく、ノード 3 には出力エッジ (シンク) がありません。 最小エッジ = max(1, 1) = 1。 

**サンプル入力 2:**```
3 3
1 2
2 3
3 1
```| ステップ | 注文 | 移調 | comp_id | ソース | シンク |
 | --- | --- | --- | --- | --- | --- |
 | DFS | [0,1,2] | [[2]、[0]、[1]] | [0,0,0] | 0 | 0 |

 すべてのノードは 1 つの SCC 内にあります。 エッジは必要ありません。 出力は0です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m) | Kosaraju のアルゴリズムは、すべての頂点とエッジを 2 回走査します。 |
 | スペース | O(n + m) | Visited、order、comp_id の隣接リストと補助配列。 |

 n ≤ 10^5 および m ≤ 10^5 の場合、これは 1 ～ 2 秒で快適に適合します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from contextlib import redirect_stdout
    out = io.StringIO()
    with redirect_stdout(out):
        main()
    return out.getvalue().strip()

# provided samples
assert run("3 2\n1 2\n2 3\n") == "1", "sample 1"
assert run("3 3\n1 2\n2 3\n3 1\n") == "0", "sample 2"

# custom cases
assert run("1 0\n") == "0", "single node"
assert run("4 0\n") == "4", "all isolated"
assert run("4 3\n1 2\n2 3\n3 4\n") == "1", "chain of nodes"
assert run("4 4\n1 2\n2 3\n3 4\n4 1\n") == "0", "already cyclic"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1 0 | 0 | 単一頂点の場合 |
 | 4 0 | 4 | 複数の孤立した頂点 |
 | 4 3 チェーン | 1 | チェーンを接続するための最小エッジ |
 | 4 4 サイクル | 0 | すでに強くつながっています |

 ## 特殊なケース

 複数の孤立した頂点を持つグラフの場合、`n=4`、`m=0`、各頂点は独自の SCC です。 ソースとシンクが 4 つあります。 最大 = 4。これは、すべての頂点を接続するサイクルを形成するために必要なエッジの数を正確に示します。 

自己ループのある単一の頂点の場合、`n=1`、`m=1`, コサラジュは 1 つの SCC を特定します。 ソースとシンクは両方とも 0 であり、期待どおり 0 を返します。 

このソリューションは、すべての境界条件と異常なトポロジを正しく処理します。
