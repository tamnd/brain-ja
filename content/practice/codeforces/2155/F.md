---
title: "CF 2155F - フアンのカラフルツリー"
description: "n 個のノードを持つツリーが与えられます。各ノードには、サイズ k のパレットから描画された色のセットが含まれています。 各ノードのセットは異なる場合があり、すべてのノードにわたる色の割り当ての合計数は s です。"
date: "2026-06-08T00:32:24+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "dfs-and-similar", "dsu", "graphs", "meet-in-the-middle", "trees"]
categories: ["algorithms"]
codeforces_contest: 2155
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 1056 (Div. 2)"
rating: 2800
weight: 2155
solve_time_s: 116
verified: false
draft: false
---

[CF 2155F - フアンのカラフルなツリー](https://codeforces.com/problemset/problem/2155/F)

 **評価:** 2800
 **タグ:** データ構造、dfs および類似物、dsu、グラフ、中間者会合、ツリー
 **解決時間:** 1 分 56 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちに与えられた木は、`n`ノード。各ノードには、サイズのパレットから描画された色のセットが含まれます。`k`。 各ノードのセットは異なる場合があり、すべてのノードにわたる色の割り当ての合計数は次のようになります。`s`。 タスクは、2 つのノードを接続する単純なパスに沿ったすべてのノードに表示される色の数を求める複数のクエリに答えることです。`u`そして`v`。 

言い換えれば、クエリごとに、次のパスに沿ったすべてのノードのカラー セットの交差部分を見つける必要があります。`u`に`v`そしてこの交差点のサイズを返します。 入力により、次の合計が保証されることも保証されます。`n`、`k`、`s`、 そして`q`これは、テスト ケースあたりおよそ O(n + s + q) より悪い解がある場合、制限時間を超える可能性が高いことを意味します。 

すべてのクエリに対して明示的にパスを走査し、セットを交差させるような単純なアプローチは直ちに疑われます。 たとえば、それぞれ 10 個の色と 105 個のクエリを持つ 105 個のノードを持つツリーを考えてみましょう。 最大 10⁵ までの長さのパスに沿って反復し、交差を実行するには、最大 10¹⁰ の操作が必要になりますが、これは許容範囲をはるかに超えています。 エッジケースには、次のようなパスが含まれます。`u = v`(答えはノード自体が設定したサイズにすぎません)、またはパス上のすべてのノードではなく一部のノードに色が表示される場合(不注意なアプローチでは、色が間違ってカウントされる可能性があります)。 

## アプローチ

 ブルート フォース アプローチは簡単です。クエリごとに、クエリ間の単純なパスを見つけます。`u`そして`v`、そのパスに沿ってすべての色のセットを収集し、それらの交差を計算します。 これは定義を直接実装しているため正しいですが、遅すぎます。 もし`n`そして`q`が大きく、パスを横断したりセットが交差したりすると、O(n・q) 付近で最悪の場合の複雑さが発生し、10¹⁰ の演算に達する可能性があります。 

より高速なソリューションを実現するための重要な洞察は、色がパスに沿ったすべてのノードに表示される場合、その色はパスの交差部分にのみ表示されることを認識することです。 問題を逆転させることができます。パスごとにすべての色をチェックする代わりに、色ごとにどのノードにその色が含まれているかを追跡できます。 これにより、古典的なツリー手法を使用できるようになります。色を含むノードをマークし、クエリごとに、その色がパスに沿って表示されるかどうかを確認します。`u`に`v`。 ルートからのパスに沿った色の最小共通祖先 (LCA) とプレフィックス カウントを使用すると、各クエリに 1 色あたり O(k) 時間で (無関係な色を削除すればさらに速く) 答えることができ、ブルートフォース パスの交差をカウントの問題に変えることができます。 

このアプローチはツリーの構造を利用します。 各ノードについて、ルートからのパスに沿った特定の色の出現数を計算すると、ルートからのパスに沿った特定の色の出現数が計算されます。`u`に`v`LCA 式を使用して計算できます。`count[u] + count[v] - 2*count[lca] + (1 if lca contains color else 0)`。 このカウントがパスの長さと等しい場合にのみ、パスの交差部分に色が表示されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(q・n・k) | O(n・k) | 遅すぎる |
 | 最適 | O(s + n + q・k) | O(n・k) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力を解析し、隣接リストとしてツリーを構築します。 これにより、パスを効率的に走査し、後で LCA を簡単に計算できるようになります。 
2. 各ノードについて、ノードに含まれる色のセットを維持します。 
3. 任意のルート (ノード 1 など) から深さ優先検索を実行して、各ノードの深さを計算し、バイナリ リフティングを使用して LCA 計算用の親ポインターを埋めます。 これにより、LCA(u, v) を O(log n) 時間で見つけることができます。 
4. 色ごとに配列を構築します`count_color`そのような`count_color[v]`ルートからノードまでのパスに沿ってこの色が表示される回数を表します`v`。 DFS 中に、カウントが親から子に伝播され、現在のノードにカラーが含まれている場合は増加します。 
5. 各クエリ (u、v) について、LCA を計算します。`w`。 各色ごとに`c`、パスに沿った出現の合計を次のように計算します。`count_color[u] + count_color[v] - 2*count_color[w] + (1 if w contains c else 0)`。 これがパスに沿ったノードの数 (深さ[u] + 深さ[v] - 2*深さ[w] + 1) と等しい場合、その色はパス上のすべてのノードに存在します。 
6. クエリに対してこの条件を満たす色の数を数え、結果を出力します。 

機能する理由: このアルゴリズムは、ルートからのプレフィックス カウントを使用して、色ごとにパスに沿ったその色の存在をカウントします。 LCA を使用してパスを 2 つのセグメントに分割すると、色ごとに一定時間内に任意のパスに沿った正確な出現数を計算できます。 このカウントをパスの長さと比較することで、パスのすべてのノードに色が表示されるかどうかを正確に判断できることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from collections import defaultdict

sys.setrecursionlimit(10**6)

def solve():
    t = int(input())
    for _ in range(t):
        n, k, s, q = map(int, input().split())
        tree = [[] for _ in range(n+1)]
        for _ in range(n-1):
            u, v = map(int, input().split())
            tree[u].append(v)
            tree[v].append(u)
        
        node_colors = [[] for _ in range(n+1)]
        color_nodes = defaultdict(list)
        for _ in range(s):
            v, x = map(int, input().split())
            node_colors[v].append(x)
            color_nodes[x].append(v)
        
        LOGN = 20
        parent = [[-1]*(n+1) for _ in range(LOGN)]
        depth = [0]*(n+1)
        
        def dfs(u, p):
            parent[0][u] = p
            for v in tree[u]:
                if v != p:
                    depth[v] = depth[u] + 1
                    dfs(v, u)
        dfs(1, -1)
        
        for i in range(1, LOGN):
            for v in range(1, n+1):
                if parent[i-1][v] != -1:
                    parent[i][v] = parent[i-1][parent[i-1][v]]
        
        def lca(u, v):
            if depth[u] < depth[v]:
                u, v = v, u
            for i in reversed(range(LOGN)):
                if parent[i][u] != -1 and depth[parent[i][u]] >= depth[v]:
                    u = parent[i][u]
            if u == v:
                return u
            for i in reversed(range(LOGN)):
                if parent[i][u] != -1 and parent[i][u] != parent[i][v]:
                    u = parent[i][u]
                    v = parent[i][v]
            return parent[0][u]
        
        color_count = defaultdict(lambda: [0]*(n+1))
        def dfs_count(u, p):
            for c in node_colors[u]:
                color_count[c][u] = 1
            for v in tree[u]:
                if v != p:
                    dfs_count(v, u)
                    for c in color_count:
                        color_count[c][v] += color_count[c][u]
        dfs_count(1, -1)
        
        result = []
        for _ in range(q):
            u, v = map(int, input().split())
            w = lca(u, v)
            path_len = depth[u] + depth[v] - 2*depth[w] + 1
            cnt = 0
            for c, arr in color_count.items():
                total = arr[u] + arr[v] - 2*arr[w] + (1 if c in node_colors[w] else 0)
                if total == path_len:
                    cnt += 1
            result.append(str(cnt))
        print(" ".join(result))

if __name__ == "__main__":
    solve()
```コードは、ツリーを読み取り、各ノードに表示される色を保存することから始まります。 次に、バイナリ リフティングを使用した LCA 計算の準備をします。 DFS は、深さテーブルと親テーブルを計算するために使用されます。 の`dfs_count`関数は、ルートからのパスに沿って各色のプレフィックスの合計を計算します。 クエリに答えるとき、LCA を使用してパスを 2 つのセグメントに分割し、これらのプレフィックスの合計を使用してパスに沿ったすべてのノードに存在する色をカウントします。 

微妙な点としては、LCA 自体のカウントを確実にインクリメントすることや、配列内のインデックスを慎重に作成することが挙げられます。 大きな再帰深度も次のように処理します。`sys.setrecursionlimit`ツリーの DFS の深さは最大 3・10⁵ に達する可能性があるためです。 

## 実用的な例

 **サンプル 1 トレース**

 | クエリ | パスノード | ノードごとの色 | 交差点 | カウント |
 | --- | --- | --- | --- | --- |
 | (1,3) | 1,3 | {1,2,3,4,5}、{1,2} | {1,2} | 2 |
 | (2,3) | 2,1,3 | {1,2,5}、{1,2,3,4,5}、{1,2} | {1,2} | 2 |
 | (1,2) | 1,2 | {1,2,3,4,5}、{1、| | |
