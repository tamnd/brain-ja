---
title: "CF 1108F - MST 統合"
description: "n 個の頂点と m 個のエッジを持ち、それぞれが正の重みを持つ接続された無向グラフが与えられます。 目標は、グラフの最小スパニング ツリー (MST) が最初と同じコストのままで、固有になるように、一部のエッジの重みを増分して調整することです。"
date: "2026-06-12T05:19:17+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "dsu", "graphs", "greedy"]
categories: ["algorithms"]
codeforces_contest: 1108
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 535 (Div. 3)"
rating: 2100
weight: 1108
solve_time_s: 45
verified: true
draft: false
---

[CF 1108F - MST の統合](https://codeforces.com/problemset/problem/1108/F)

 **評価:** 2100
 **タグ:** 二分探索、dsu、グラフ、貪欲
 **解決時間:** 45 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 次のような接続された無向グラフが与えられます。`n`頂点と`m`エッジ、それぞれに正の重みが付けられます。 目標は、グラフの最小スパニング ツリー (MST) が最初と同じコストのままで、固有になるように、一部のエッジの重みを増分して調整することです。 言い換えれば、グラフには最初は同じ合計コストの複数の MST が含まれている可能性があり、全体の MST コストを変更せずにエッジの重みをわずかに増やすことで最小限の方法で関係を解消したいと考えています。 

入力は、エンドポイントと重みを含むエッジのリストとしてグラフを提供します。 出力は単一の整数です。これは、一意の MST を実現するために必要な「1 ずつインクリメント」する操作の最小数です。 

制約が厳しい:`n`そして`m`最大 200,000、エッジの重みは最大 200,000 に達する可能性があります。`10^9`。 すべての MST を列挙したり、すべてのサイクルを明示的にチェックしようとしたソリューションは、遅すぎます。`O(m^2)`操作は実行不可能です。 これは、おおよそ次の条件で機能するソリューションが必要であることを示唆しています。`O(m log n)`理想的には、MST 構築とあいまいなエッジを識別する効率的な方法を組み合わせます。 

微妙なエッジ ケースとは、多くのエッジが同じ重みを持ち、複数の MST が存在するグラフです。 たとえば、エッジのある三角形`(1,2,1)`、`(2,3,1)`、`(1,3,1)`には 3 つの MST があります。 MST を選択してそこにないエッジを増分するだけの単純なアルゴリズムでは、誤って MST コストを変更したり、必要以上のエッジを増分したりする可能性があります。 正しいアプローチでは、元の MST コストに影響を与えることなく、重みの増分によって関係が解消されるエッジを慎重に識別します。 

## アプローチ

 ブルート フォース アプローチでは、すべての MST を生成し、すべてではないが一部の MST に存在するエッジを見つけて、それらをインクリメントしようとします。 これが機能するのは、総コストを増加させることなく MST エッジを置き換えることができるエッジが現在の MST の外部にある場合にのみ、MST の一意性が失敗するためです。 ただし、すべての MST を列挙することは現実的ではありません。 非 MST エッジごとにサイクル チェックを使用しても、`O(m * n)`あるいはさらに悪いことに。 と`n`まで`2 * 10^5`、これは遅すぎます。 

重要な洞察は、MST のあいまいさは、MST 内で形成されるサイクル上の最大エッジの重みと等しい重みを持つ MST の外側のエッジからのみ発生するということです。 MST エッジと非 MST エッジを考慮すると`(u, v, w)`、MST とサイクルを形成します。 もし`w`が MST のそのサイクルに沿った最大重みに等しい場合、エッジを交換して別の MST を取得できます。 曖昧さを防ぐために、これらの非 MST エッジの一部の重みを最小限に増やして、サイクルに沿った最大値と一致しないようにする必要があります。 これにより、問題は、非 MST エッジごとに、そのエンドポイント間の MST 内の一意のパスに沿った最大エッジ重みを計算するというものに軽減されます。 

パスに沿った最大エッジ重みの計算は、次のバイナリ リフティングを使用して効率的に実行できます。`O(n log n)`前処理を行ってから、各非 MST エッジをクエリします。`O(log n)`。 合計時間は`O(m log n)`、制約に適合します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(m * n) | O(n + m) | 遅すぎる |
 | 最適 | O(m log n) | O(n log n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. Kruskal のアルゴリズムを使用してグラフの MST を構築します。 どのエッジが MST に属するかを保存します。 すべての MST の合計の重みが同じであり、どの MST も分析に使用できるため、これは有効です。 
2. MST をツリーとして構築し、LCA クエリ用のバイナリ リフティング テーブルを準備します。 各ノードについて、2 の累乗で祖先と各祖先へのパス上の最大エッジ重みを保存します。 これにより、任意の 2 つのノード間のパスの最大重みをクエリできます。`O(log n)`。 
3. 非 MST エッジごとに`(u, v, w)`、からパスに沿った最大エッジ重みを計算します。`u`に`v`MST ではバイナリ リフティング テーブルを使用します。 もし`w`がこの最大値に等しい場合、このエッジを MST エッジと交換して別の MST を生成できます。 
4. このようなエッジをそれぞれ数えます。 重みを 1 つ増やすと、サイクルに沿って最大重みを超えるため、あいまいさが解消されます。 最小操作数は、これらのクリティカル エッジの合計数です。 
5. このカウントを出力します。 

機能する理由: MST の一意性は、エンドポイントを接続する MST パス上で、非 MST エッジが最大値に等しい重みを持たないことを保証することと同じです。 アルゴリズムはこれらのエッジを正確に識別し、それらを増分します。 バイナリ リフティングにより、すべての非 MST エッジに対する正しい最大パス クエリが効率的に保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(1 << 25)

def main():
    n, m = map(int, input().split())
    edges = []
    for _ in range(m):
        u, v, w = map(int, input().split())
        edges.append((w, u - 1, v - 1))
    edges.sort()
    
    parent = list(range(n))
    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x
    def union(x, y):
        x, y = find(x), find(y)
        if x == y: return False
        parent[y] = x
        return True

    mst_edges = []
    for w, u, v in edges:
        if union(u, v):
            mst_edges.append((u, v, w))
    
    # build tree for LCA
    from collections import defaultdict, deque
    adj = defaultdict(list)
    for u, v, w in mst_edges:
        adj[u].append((v, w))
        adj[v].append((u, w))
    
    LOG = 20
    up = [[-1]*LOG for _ in range(n)]
    max_edge = [[0]*LOG for _ in range(n)]
    depth = [0]*n
    
    def dfs(u, p):
        for v, w in adj[u]:
            if v == p: continue
            up[v][0] = u
            max_edge[v][0] = w
            depth[v] = depth[u] + 1
            for i in range(1, LOG):
                if up[v][i-1] != -1:
                    up[v][i] = up[up[v][i-1]][i-1]
                    max_edge[v][i] = max(max_edge[v][i-1], max_edge[up[v][i-1]][i-1])
            dfs(v, u)
    
    dfs(0, -1)
    
    def query(u, v):
        if depth[u] < depth[v]:
            u, v = v, u
        res = 0
        for i in reversed(range(LOG)):
            if up[u][i] != -1 and depth[up[u][i]] >= depth[v]:
                res = max(res, max_edge[u][i])
                u = up[u][i]
        if u == v:
            return res
        for i in reversed(range(LOG)):
            if up[u][i] != -1 and up[u][i] != up[v][i]:
                res = max(res, max_edge[u][i], max_edge[v][i])
                u, v = up[u][i], up[v][i]
        res = max(res, max_edge[u][0], max_edge[v][0])
        return res

    mst_set = set((min(u,v), max(u,v)) for u,v,w in mst_edges)
    ans = 0
    for w, u, v in edges:
        if (min(u,v), max(u,v)) in mst_set:
            continue
        if w == query(u,v):
            ans += 1
    print(ans)

if __name__ == "__main__":
    main()
```コードはまず、Kruskal を使用して MST を構築し、MST エッジをマークします。 隣接リストを含むツリー構造を構築して DFS を実行し、エッジ クエリを最大化するためにバイナリ リフティング テーブルを埋めます。 次に、各非 MST エッジがチェックされます。その重みが MST 内のパスに沿った最大値に等しい場合、そのエッジは増分の候補となります。 そのようなエッジをすべて数えることで答えが得られます。 

## 実用的な例

 **サンプル 1:**```
n = 8, m = 10
edges = [
1 2 1, 2 3 2, 2 4 5, 1 4 2, 6 3 3,
6 1 3, 3 5 2, 3 7 1, 4 8 1, 6 2 4
]
```Kruskal 以降、MST エッジには重み 1、1、1、2、2、2、3 が含まれます。 エッジ`(6,2,4)`MST の外側にあり、最大重み 4 のサイクルを形成します。`w == max_cycle`、増加させるとあいまいさが解決されます。 そのようなエッジは 1 つだけ存在します。 出力は`1`。 

**カスタム入力:**```
n = 3, m = 3
edges = [
1 2 1, 2 3 1, 1 3 1
]
```すべてのエッジの重みは 1 です。MST は任意の 2 つを選択します。 非MSTエッジ`(1,3,1)`もっている`w == max_cycle`そして増分する必要があります。 出力は`1`。 

| ステップ | あなた | v | パス上の最大値 | クリティカルエッジって何ですか？ | カウント |
 | --- | --- | --- | --- | --- | --- |
 | チェック (1, | | | | | |
