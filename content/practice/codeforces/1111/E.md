---
title: "CF 1111E - ツリー"
description: "n 個のノードと複数のクエリを含むツリーが与えられています。 各クエリは、k ノードのサブセット、グループの最大数 m、およびルート r を提供します。"
date: "2026-06-12T04:59:45+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "dfs-and-similar", "dp", "graphs", "trees"]
categories: ["algorithms"]
codeforces_contest: 1111
codeforces_index: "E"
codeforces_contest_name: "CodeCraft-19 and Codeforces Round 537 (Div. 2)"
rating: 2500
weight: 1111
solve_time_s: 86
verified: false
draft: false
---

[CF 1111E - ツリー](https://codeforces.com/problemset/problem/1111/E)

 **評価:** 2500
 **タグ:** データ構造、dfs および同様のもの、dp、グラフ、ツリー
 **解決時間:** 1 分 26 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちに与えられた木は、`n`ノードと複数のクエリ。 各クエリは次のサブセットを提供します。`k`ノード、グループの最大数`m`、およびルート`r`。 タスクは、指定されたノードを最大で次のノードに分割することです。`m`グループには、ルート ツリー内のノードとその祖先が含まれるグループは存在しないという制限があります。 有効なパーティションの数を法で数えなければなりません$10^9 + 7$。 

木には最大で$10^5$ノードがあり、最大で$10^5$クエリ。 各クエリにはグループ内に最大 300 個のノードを含めることができますが、合計は`k`すべてのクエリで制限されるのは、$10^5$。 これは、アルゴリズムを導入する余裕がないことを示しています。$O(2^k)$クエリごとの複雑さはありますが、アルゴリズムは多項式です`k`（例えば。、$O(k^2)$または$O(k m)$) 動作する可能性があります。 

エッジ ケースは、ノードのサブセットが祖先のチェーンを形成するときに発生します。 たとえば、サブセットが次の場合、`[1, 2, 3]`ルートパスに沿って、そして`m = 1`、各ノードは次のノードの祖先であるため、有効なグループは存在しません。 祖先関係を無視する不注意な実装では、そのような構成が誤って有効としてカウントされてしまいます。 

## アプローチ

 ブルートフォースアプローチでは、考えられるすべてのパーティションが試行されます。`k`ノード数は最大でも`m`グループを作成し、祖先の状態を確認します。 これはベルの数に比例した複雑さを持ちます。$B_k$よりも早く成長します$2^k$。 たとえ`k = 20`、これは実行不可能であり、`k`300までは無理です。 

重要な観察は、祖先関係が推移的であり、ツリー構造になっているということです。 有効なグループには、**誘導されたサブツリー**内で独立したノードが含まれている必要があります。 これにより、選択したノードとそれらのペアごとの最小共通祖先 (LCA) によって形成されるツリー上で問題を動的プログラミングとしてモデル化することができます。 選択したセットに LCA を追加し、深さによってノードを並べ替えることにより、各ノードが祖先制限に違反することなくサブツリー ノードをグループに分散する方法の数を計算するボトムアップ DP を実行できます。 

これにより、問題は最大でもサイズの小さな「圧縮ツリー」にわたる DP に縮小されます。$2k$。 各ノードは DP アレイを維持します`dp[i]`どこ`dp[i]`サブツリーを分割する方法を数えます`i`グループ。 子を結合するときは、組み合わせたたみ込みを使用して DP 配列をマージし、範囲内に留まります。`O(k m)`クエリごとに。 制約を考慮すると、これは効率的です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(B_k) | O(B_k) | 遅すぎる |
 | 圧縮ツリー上の DP | O(km) | O(km) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. **LCA クエリ用にツリーを前処理します。** オイラー ツアー + セグメント ツリーまたはバイナリ リフティングを使用します。 これにより、任意の 2 つのノードの LCA を計算できます。$O(\log n)$。 選択したノード間の先祖関係はすべて LCA を通過する必要があるため、LCA は非常に重要です。 
2. **各クエリを個別に処理します。**`k`ノードとルート`r`。 追加`r`一貫性を保つためにノードのセットに追加し、クエリ内のすべてのノードのペアのすべての LCA を計算します。 これらの LCA をセットに挿入して、関連するノードの「圧縮ツリー」を構築します。 
3. **圧縮ツリーを構築します。** オイラー ツアーの開始時間によってノードを並べ替え、スタックを使用して LCA 階層に従って親子関係にあるノードを接続します。 圧縮されたツリーには、最大でも次のものが含まれます。`2k`ノード。 
4. **圧縮ツリーでの動的プログラミング。** 各リーフ ノードの DP を次のように初期化します。`dp[1] = 1`そして`dp[i] = 0`のために`i > 1`。 
5. **子から親に DP をマージします。** 子を持つ親の場合`c1, c2, ..., cn`、グループ数のすべての組み合わせを計算します: 親が貢献している場合`p`グループと子供たちが貢献する`c1_groups, c2_groups, ...`、グループの総数 ≤ となるすべての有効な組み合わせを合計します。`m`。 大きなカウントにはモジュラー演算を使用します。 
6. **考えられるグループ数を合計します。** 圧縮ツリーのルートを処理した後、合計します。`dp[1..m]`有効なパーティションの総数を取得します。 

**機能する理由:** 圧縮ツリーにより、クエリ ノード間の祖先と子孫の関係が明示的に表現されることが保証されます。 ボトムアップ DP により、各サブツリーは有効なグループ化を独立して計算し、マージでは祖先の制限が尊重されます。 すべての組み合わせが DP の不変条件に従うため、アルゴリズムは有効な構成のみをカウントします。つまり、グループには祖先とその子孫が含まれません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(1 << 25)

MOD = 10**9 + 7

def main():
    n, q = map(int, input().split())
    tree = [[] for _ in range(n+1)]
    for _ in range(n-1):
        u, v = map(int, input().split())
        tree[u].append(v)
        tree[v].append(u)

    # Binary lifting for LCA
    LOG = 17
    up = [[-1]*(LOG+1) for _ in range(n+1)]
    depth = [0]*(n+1)
    tin = [0]*(n+1)
    tout = [0]*(n+1)
    timer = 0

    def dfs(u, p):
        nonlocal timer
        tin[u] = timer
        timer += 1
        up[u][0] = p
        for i in range(1, LOG+1):
            if up[u][i-1] != -1:
                up[u][i] = up[up[u][i-1]][i-1]
        for v in tree[u]:
            if v != p:
                depth[v] = depth[u]+1
                dfs(v, u)
        tout[u] = timer
        timer += 1

    dfs(1, -1)

    def lca(u, v):
        if depth[u] < depth[v]:
            u, v = v, u
        for i in reversed(range(LOG+1)):
            if up[u][i] != -1 and depth[up[u][i]] >= depth[v]:
                u = up[u][i]
        if u == v:
            return u
        for i in reversed(range(LOG+1)):
            if up[u][i] != -1 and up[u][i] != up[v][i]:
                u = up[u][i]
                v = up[v][i]
        return up[u][0]

    def is_ancestor(u, v):
        return tin[u] <= tin[v] and tout[v] <= tout[u]

    for _ in range(q):
        parts = list(map(int, input().split()))
        k, m, r = parts[:3]
        nodes = parts[3:]
        nodes.append(r)
        nodes = list(set(nodes))
        # add LCAs
        nodes.sort(key=lambda x: tin[x])
        stack = []
        compressed = []
        for node in nodes:
            if stack:
                l = lca(stack[-1], node)
                if l not in nodes:
                    nodes.append(l)
        nodes = list(set(nodes))
        nodes.sort(key=lambda x: tin[x])
        # build compressed tree
        parent = {nodes[0]: None}
        stack = [nodes[0]]
        for node in nodes[1:]:
            while not is_ancestor(stack[-1], node):
                stack.pop()
            parent[node] = stack[-1]
            stack.append(node)

        # DP
        children = {u: [] for u in nodes}
        for u in nodes:
            if parent[u] is not None:
                children[parent[u]].append(u)

        dp = {}
        def dfs_dp(u):
            dp_u = [0]*(m+1)
            dp_u[1] = 1 if u in parts[3:] else 0
            for v in children[u]:
                dfs_dp(v)
                ndp = [0]*(m+1)
                for i in range(1, m+1):
                    if dp_u[i] == 0:
                        continue
                    for j in range(1, m+1-i+1):
                        ndp[i+j] = (ndp[i+j] + dp_u[i]*dp[v][j])%MOD
                for i in range(1, m+1):
                    dp_u[i] = (dp_u[i]+ndp[i])%MOD
            dp[u] = dp_u
        dfs_dp(nodes[0])
        print(sum(dp[nodes[0]][1:m+1])%MOD)

if __name__ == "__main__":
    main()
```**説明:** このソリューションでは、まず、効率的な LCA 計算のためにバイナリ リフティングを設定します。 各クエリは、LCA を含むノードの圧縮ツリーを構築します。 DP 配列は、グループの最大数と祖先の制限を尊重しながら、ボトムアップでマージされます。 モジュラー演算が全体に適用されます。 

## 実用的な例

 **サンプル 1**

 入力クエリ:`3 3 2 7 4 3`ノード`[7,4,3]`ルート付き`2`:

 | ノード | 深さ | セット内の先祖 | DP結果 |
 | --- | --- | --- | --- |
 | 7 | 3 | 4 | [0 |
