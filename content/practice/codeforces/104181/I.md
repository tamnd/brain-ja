---
title: "CF 104181I - 雨の配達"
description: "各ノードが友人の家を表し、各有向エッジが一方通行の道路を表す有向グラフが与えられます。 最初の家を選択し、指示された道路に沿って繰り返し移動し、場合によっては家や道路を複数回再訪問することができます。"
date: "2026-07-02T00:39:58+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104181
codeforces_index: "I"
codeforces_contest_name: "UTPC Contest 02-10-23 Div. 1 (Advanced)"
rating: 0
weight: 104181
solve_time_s: 66
verified: true
draft: false
---

[CF 104181I - 雨の配達](https://codeforces.com/problemset/problem/104181/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 6 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各ノードが友人の家を表し、各有向エッジが一方通行の道路を表す有向グラフが与えられます。 最初の家を選択し、指示された道路に沿って繰り返し移動し、場合によっては家や道路を複数回再訪問することができます。 

目標は、そのような散歩に沿って訪問できる別個の家をできるだけ多くすることです。 再訪問は許可されていますが、一意の家のみがカウントされるため、問題は開始ノードと、可能な限り多くの個別の到達可能なノードをカバーする有向ウォークを見つけることになります。 

重要な観察は、一度有向サイクルに入ると、そのサイクル内にとどまって無限に横断できるということです。これは、そのサイクル内のすべてのノードが相互に到達可能になることを意味します。 どのノードからでも、有向パスを介して到達可能なすべてのノードは事実上同じ到達可能なクロージャの一部ですが、サイクルによってこの構造が圧縮されます。 

制約はノード数が最大 1000 と小さく、エッジは最大 2N エッジでまばらです。 これは、O(N^2) または O(NM) アプローチは受け入れられるが、すべてのパスやノードのサブセットを列挙するようなアプローチは受け入れられないことを強く示唆しています。 

単純な解釈では、すべての開始ノードを試し、到達可能なノードをカウントする DFS/BFS を実行することになります。 多くの場合、これですでに正しい答えが得られますが、重要な点が抜け落ちています。サイクルによって再訪が可能であり、到達可能性は強く接続されたコンポーネントを通じて推移的であるため、実際の構造は SCC の DAG です。 SCC 構造が慎重に考慮されていない場合、答えは、ローカル BFS カウントだけでなく、その凝縮グラフ内の最長到達可能性によって決まります。 

よくある間違いは、各ノードからの単純な到達可能性が独立しているかのようにグラフを扱うことですが、循環グラフでは単純な到達可能性が 2 倍にカウントされるか、SCC 全体が単一ユニットとして動作することを利用できません。 

エッジケースには次のようなものがあります。 

1 → 2 → 3 → 1 などの単一の有向サイクル。どの開始でも、1 や 2 ではなく、3 という答えが得られるはずです。 

1 → 2 → 3 のような連鎖。1 から開始すると 3 が生成されますが、3 から開始すると 1 しか生成されません。答えは 3 です。 

複数の SCC パスが収束する、分岐および結合パスを含むグラフでは、SCC 圧縮が無視されると、単純な貪欲トラバーサルのカウントが過少カウントまたは過大カウントになる可能性があります。 

## アプローチ

 ブルート フォースのアイデアは簡単です。ノードごとに BFS または DFS を実行し、到達可能なノードの数を数えます。 答えは、すべての開始ノードの最大値です。 これは正しいです。すべての有効なウォークは開始時の到達可能なセット内に留まり、再訪問してもセットが到達可能を超えて増加することはないからです。 

ただし、このアプローチは、到達可能性がパス再利用構造に依存しないことを前提としています。 密なグラフや多くの重なり合うパスを含むグラフでは、これはまだ機能しますが、追加の状態を使用して単純な方法で到達可能性を繰り返し再計算しようとすると非効率的になります。 最悪のケースは O(N(N + M)) で、これは約 10^6 回の演算であり、まだ境界線にありますが許容範囲です。 

より深い問題は、SCC が実際の原子単位を形成していることを理解することです。 強く接続されたコンポーネントの内部では、すべてのノードが相互に到達可能であるため、内部のどのノードもコンポーネント全体に到達できます。 SCC が構築されると、グラフは DAG になります。 このタスクは、この DAG 内の任意の SCC から到達可能なノードの最大数を見つけることになります。これは、コンポーネント サイズで重み付けされた DAG ノード上の最長パスになります。 

この観察により、冗長な探索が削減され、クリーンな動的プログラミング構造が得られます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 各ノードからのブルート フォース DFS/BFS | O(N(N+M)) | O(N+M) | 最悪の場合遅すぎる |
 | DAG 上の SCC 凝縮 + DP | O(N+M) | O(N+M) | 承認済み |

 ## アルゴリズムのチュートリアル

 ## 1. 強結合成分を計算する

まず、Kosaraju または Tarjan を使用してグラフを SCC に分解します。 その理由は、SCC 内ではすべてのノードが相互に到達可能であるため、フレンドを収集するという点では単一のユニットとして動作するためです。 

## 2. 要約グラフを構築する

 各 SCC を単一のノードに圧縮します。 元のグラフのすべてのエッジ u → v について、u と v が異なる SCC に属している場合、それらのコンポーネント間に有向エッジを追加します。 SCC 凝縮によりサイクルが除去されるため、これにより DAG が生成されます。 

## 3. コンポーネントに重みを割り当てる

 各 SCC ノードには、その内部の元のノードの数に等しい重みが与えられます。 これは、そのコンポーネントに入ると自動的に収集される友達の数を反映します。 

## 4. DAG の到達可能性 DP を計算する

 各コンポーネントから到達可能な重みの最大合計を計算します。 グラフは DAG であるため、ノードをトポロジカルな順序で処理したり、メモ化された DFS を使用したりできます。 各コンポーネントの値は、それ自体の重みに、すべての発信隣接要素の中で最良のものを加えたものになります。 

## 5. 最適な出発点を取る

 出発点としてすべてのコンポーネントを試し、最大 DP 値を取得します。 

### なぜ効果があるのか

 重要な不変条件は、SCC 圧縮によって到達可能性が 1 対 1 で維持されるということです。元のグラフ内のパスはすべて SCC DAG 内のパスに対応し、SCC 内のいかなるウォークも、その SCC を超えて到達可能なコンポーネントのセットを増やすことはありません。 したがって、訪問した個別のノードを最大化することは、開始 SCC を選択し、DAG 内の到達可能なノードに対する合計の重みを最大化することと同じです。 DAG パスはサイクルを形成しないため、DP は二重にカウントすることなく、最適な到達可能な合計を正しく累積します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(10**7)

def solve():
    n, m = map(int, input().split())
    g = [[] for _ in range(n)]
    gr = [[] for _ in range(n)]
    
    for _ in range(m):
        a, b = map(int, input().split())
        a -= 1
        b -= 1
        g[a].append(b)
        gr[b].append(a)

    # Kosaraju: first pass order
    vis = [False] * n
    order = []

    def dfs1(u):
        vis[u] = True
        for v in g[u]:
            if not vis[v]:
                dfs1(v)
        order.append(u)

    for i in range(n):
        if not vis[i]:
            dfs1(i)

    # second pass assign components
    comp = [-1] * n
    comps = []

    def dfs2(u, cid):
        comp[u] = cid
        comps[cid].append(u)
        for v in gr[u]:
            if comp[v] == -1:
                dfs2(v, cid)

    for u in reversed(order):
        if comp[u] == -1:
            comps.append([])
            dfs2(u, len(comps) - 1)

    k = len(comps)

    # build condensed graph
    dag = [set() for _ in range(k)]
    weight = [0] * k

    for cid in range(k):
        weight[cid] = len(comps[cid])
        for u in comps[cid]:
            for v in g[u]:
                if comp[v] != cid:
                    dag[cid].add(comp[v])

    dag = [list(s) for s in dag]

    # DP on DAG
    dp = [-1] * k
    vis_dp = [False] * k

    def dfs(u):
        if vis_dp[u]:
            return dp[u]
        vis_dp[u] = True
        best = 0
        for v in dag[u]:
            best = max(best, dfs(v))
        dp[u] = weight[u] + best
        return dp[u]

    ans = 0
    for i in range(k):
        ans = max(ans, dfs(i))

    print(ans)

if __name__ == "__main__":
    solve()
```解決策は、元のグラフとその逆グラフの両方を構築することから始まります。これは、Kosaraju のアルゴリズムに必要です。 最初の DFS は終了順序を計算し、グラフの出口構造を考慮した方法でノードが処理されるようにします。 

2 番目の DFS は、反転グラフ上でコンポーネント ID を割り当て、相互に到達可能なノードをグループ化します。 各コンポーネントは明示的に保存されるため、そのサイズと出力エッジを計算できます。 

圧縮ステップでは、コンポーネントごとにセットを使用してエッジの重複を回避します。これは、複数の元のエッジが同じ SCC 遷移に崩壊する可能性があるため重要です。 次に、DP ステップでは、メモ化された DFS を使用して各 SCC から到達可能な最良の合計を計算します。 

微妙な点は再帰の深さです。 Python では、最大 1000 ノードの深さ、および場合によってはより深い DFS チェーンのため、再帰制限を増やす必要があります。 

## 実用的な例

 ### 例 1

 入力:```
3 2
1 2
2 3
```ここでは、すべてのノードがサイクルのない単純なチェーンを形成します。 

| ステップ | SCC の割り当て | 要約グラフ | DP値 |
 | --- | --- | --- | --- |
 | 1 | {1}、{2}、{3} | 1→2→3 | ボトムアップで計算された |
 | 2 | 各ノードのサイズ 1 | 同じチェーン | dp[3]=1 |
 | 3 | | | dp[2]=2 |
 | 4 | | | dp[1]=3 |

 これは、到達可能性がチェーンに沿って線形に累積することを示しています。 

### 例 2

 入力:```
3 1
1 2
```| ステップ | SCC の割り当て | 要約グラフ | DP値 |
 | --- | --- | --- | --- |
 | 1 | {1}、{2}、{3} | 1→2 | dp[2]=1 |
 | 2 | | | dp[1]=2 |
 | 3 | | | ノード 3 が分離 = 1 |

 最良の開始点はノード 1 から 2 を与えることです。 

これらの例では、DP が到達可能なコンポーネントを正しく集約していることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(N + M) | Kosaraju は線形時間で実行され、SCC DAG 構築と DP も線形です。 
| スペース | O(N + M) | 隣接リスト、コンポーネント ストレージ、および DP アレイ |

 この制約により、最大約 2000 のエッジが許可されるため、線形グラフの処理は 5 秒以内で簡単に十分高速になります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    n, m = map(int, sys.stdin.readline().split())
    g = [[] for _ in range(n)]
    gr = [[] for _ in range(n)]
    for _ in range(m):
        a, b = map(int, sys.stdin.readline().split())
        a -= 1
        b -= 1
        g[a].append(b)
        gr[b].append(a)

    sys.setrecursionlimit(10**7)

    vis = [False] * n
    order = []

    def dfs1(u):
        vis[u] = True
        for v in g[u]:
            if not vis[v]:
                dfs1(v)
        order.append(u)

    for i in range(n):
        if not vis[i]:
            dfs1(i)

    comp = [-1] * n
    comps = []

    def dfs2(u, cid):
        comp[u] = cid
        comps[cid].append(u)
        for v in gr[u]:
            if comp[v] == -1:
                dfs2(v, cid)

    for u in reversed(order):
        if comp[u] == -1:
            comps.append([])
            dfs2(u, len(comps) - 1)

    k = len(comps)
    dag = [set() for _ in range(k)]
    weight = [len(c) for c in comps]

    for cid in range(k):
        for u in comps[cid]:
            for v in g[u]:
                if comp[v] != cid:
                    dag[cid].add(comp[v])

    dag = [list(s) for s in dag]

    from functools import lru_cache

    @lru_cache(None)
    def dfs(u):
        best = 0
        for v in dag[u]:
            best = max(best, dfs(v))
        return weight[u] + best

    ans = 0
    for i in range(k):
        ans = max(ans, dfs(i))
    return str(ans) + "\n"

# provided samples
assert run("3 2\n1 2\n2 3\n") == "3\n", "sample 1"
assert run("3 1\n1 2\n") == "2\n", "sample 2"
assert run("5 5\n3 5\n3 2\n2 3\n4 5\n5 1\n") == "4\n", "sample 3"

# custom cases
assert run("1 0\n") == "1\n", "single node"
assert run("3 3\n1 2\n2 3\n3 1\n") == "3\n", "single cycle"
assert run("4 3\n1 2\n2 3\n3 4\n") == "4\n", "chain"
assert run("4 2\n1 2\n3 4\n") == "2\n", "disconnected components"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一ノード | 1 | 最小限のグラフ処理 |
 | シングルサイクル | 3 | SCC の崩壊の正しさ |
 | チェーン | 4 | 線形到達可能性の累積 |
 | 切断されたコンポーネント | 2 | 独立した部分グラフが正しく処理されました。 

## 特殊なケース

 次のような単一の強く接続されたサイクル`1 → 2 → 3 → 1`すべてのノードを 1 つの SCC にグループ化することで処理されます。 圧縮中、これは出力エッジのない重み 3 の単一ノードになり、DP は開始点に関係なくすぐに 3 を返します。 

次のような純粋な線形グラフ`1 → 2 → 3 → 4`それぞれサイズ 1 の SCC が 4 つ生成されます。 DAG はチェーンになり、DP は最後から逆方向に正しく累積され、最大の開始ノードがチェーンの先頭になることが保証され、4 が得られます。 

次のような切り離されたグラフ`1 → 2`そして`3 → 4`その結果、2 つの独立した DAG コンポーネントが生成されます。 DP はすべての SCC ルートにわたって評価されるため、最大値はより大きな到達可能なセグメント (この場合は 2) を正しく選択します。
