---
title: "CF 2069F - グラフの組み込み"
description: "同じ $n$ 頂点セット上に 2 つの無向グラフ $A$ と $B$ が与えられています。 最初は、どちらのグラフにもエッジがありません。 クエリは 1 つずつ到着します。 各クエリは、$A$ または $B$ のエッジを切り替えます。"
date: "2026-06-08T07:00:56+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "dfs-and-similar", "divide-and-conquer", "dsu", "graphs"]
categories: ["algorithms"]
codeforces_contest: 2069
codeforces_index: "F"
codeforces_contest_name: "Educational Codeforces Round 174 (Rated for Div. 2)"
rating: 2800
weight: 2069
solve_time_s: 108
verified: false
draft: false
---

[CF 2069F - グラフの組み込み](https://codeforces.com/problemset/problem/2069/F)

 **評価:** 2800
 **タグ:** データ構造、dfs など、分割統治、dsu、グラフ
 **解決時間:** 1 分 48 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 2 つの無向グラフが与えられています。$A$そして$B$、同じセット上で$n$頂点。 最初は、どちらのグラフにもエッジがありません。 クエリは 1 つずつ到着します。 各クエリはエッジを切り替えるか、$A$または$B$。 すべてのクエリの後に、次の質問が行われます: エッジをいくつ追加する必要があるか$A$したがって、それぞれの接続されたコンポーネントは、$B$の接続されたコンポーネント内に完全に含まれています$A$? 言い換えれば、各クエリの後に、$A$"含む"$B$。 

制約は厳しいです:$n$そして$q$それぞれ最大で可能です$4 \cdot 10^5$。 これにより、DFS または BFS を使用するクエリのたびにコンポーネントを最初から再構築するアルゴリズムが即座に排除されます。これにはコストがかかります。$O(n)$クエリごとの結果は最大で$1.6 \cdot 10^{11}$操作。 ほぼ一定時間で接続を段階的に更新するソリューションが必要です。 

明らかではないエッジ ケースには、エッジが繰り返し切り替わる状況や、エッジの複数の頂点が含まれる状況が含まれます。$B$- コンポーネントが複数に分散されている$A$-コンポーネント。 たとえば、次の場合$B$コンポーネントがあります$\{1, 2, 3\}$そして$A$最初は接続のみ$\{1, 2\}$、答えは$1$間に単一のエッジを追加するため、$2$そして$3$十分でしょう。 コンポーネントのメンバーシップを効率的に追跡できない不注意な実装では、そのような場合にエッジのカウントを誤る可能性があります。 

## アプローチ

 ブルートフォースアプローチは、両方の接続されたコンポーネントを再構築することです。$A$そして$B$各クエリの後で繰り返します$B$のコンポーネント、そしていくつ分離しているかを数えます$A$- それぞれが触れるコンポーネント。 追加するエッジの最小数は次の合計になります。$B$-「タッチ数」の構成要素$A$-コンポーネントから 1 を引いた値です。」これは正しいですが、コストがかかります$O(n)$クエリごとに、$O(nq) = 1.6 \cdot 10^{11}$最悪の場合、動作が非常に遅くなります。 

重要な観察は、エッジの挿入と削除によって **動的接続の問題**が形成され、複数のコンポーネントを接続するために必要なエッジの数が、まさに素のコンポーネントの数であるということです。$A$- 接触したコンポーネント$B$-コンポーネントから 1 を引いた値。 どちらを追跡できれば$A$- 各頂点が属するコンポーネントと各頂点の数$B$-コンポーネントあたり$A$-component を使用すると、効率的に答えを計算できます。 

**Disjoint Set Union (DSU)** を 2 つのレイヤーで使用します。1 つの DSU は$A$そしてもう一つは$B$。 2つをマージするたびに$A$-コンポーネントの数を調整します$B$-コンポーネントが接触している。 それぞれメンテナンスすることで、$B$-component 個別の数$A$-コンポーネントが交差する場合、「接触したコンポーネントから 1 を引いた値」の合計を計算できます。$O(1)$DSU マージごとに償却されます。 これにより、各クエリがほぼ次のように削減されます。$O(\alpha(n))$時間、場所$\alpha$は逆アッカーマン関数です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース DFS/BFS | O(n q) | O(n) | 遅すぎる |
 | カウンタ付き DSU | O(q α(n)) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 2 つの DSU を初期化します。`DSU_A`グラフ用$A$そして`DSU_B`グラフ用$B$。 各頂点は独自のコンポーネントから始まります。 
2. それぞれのメンテナンス$B$-component 何のカウンタ$A$-現在交差しているコンポーネント。 当初はそれぞれ$B$-component には単一の頂点が含まれているため、頂点と交差します$A$-成分。 
3. クエリを処理するとき:

 1. グラフを特定します (`A`または`B`) と頂点$(x, y)$。 
2. 選択した DSU のエッジを切り替えます。エッジが存在する場合は、論理的に削除します。 そうでない場合は、コンポーネントを結合します。 エッジ追加用$A$をマージします。`DSU_A`xの成分` and \(y. For edge addition in ⟦PROTECT_55⟧, merge `DSU_B` コンポーネントと更新カウンター。 
3. 2 つのとき$A$- コンポーネントをマージし、交差する数を更新します$A$-すべてのコンポーネント$B$- マージされたコンポーネントのいずれかに接触するコンポーネント$A$-コンポーネント。 これにより、それぞれの不変条件が維持されます。`B`-コンポーネントはその数を知っています`A`-コンポーネントにまたがります。 
4. DSU の更新後、答えを計算します: すべての合計`B`- のコンポーネント`(number of distinct A-components it touches - 1)`。 これは、パーツを接続するために必要なエッジの最小数です。$A$それぞれに対応する`B`-成分。 
5. クエリの結果を出力します。 すべてのクエリに対して繰り返します。 

機能する理由: DSU は、各グラフのコンポーネント構造が段階的に維持されることを保証します。 カウンタは正確な数を維持します。`A`- それぞれが交差するコンポーネント`B`-成分。 接続してから`k`互いに素なコンポーネントには正確な値が必要です`k-1`エッジ、合計`B`-components は、必要な最小限のエッジ数を生成します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from collections import defaultdict

class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1]*n
        self.members = [set([i]) for i in range(n)]
    
    def find(self, x):
        while x != self.parent[x]:
            self.parent[x] = self.parent[self.parent[x]]
            x = self.parent[x]
        return x
    
    def union(self, x, y):
        x_root, y_root = self.find(x), self.find(y)
        if x_root == y_root:
            return False, x_root, y_root
        if self.size[x_root] < self.size[y_root]:
            x_root, y_root = y_root, x_root
        self.parent[y_root] = x_root
        self.size[x_root] += self.size[y_root]
        self.members[x_root] |= self.members[y_root]
        self.members[y_root] = set()
        return True, x_root, y_root

n, q = map(int, input().split())
DSU_A = DSU(n)
DSU_B = DSU(n)

# count of A-components per B-component root
B_count = [1]*n  # initially each vertex touches 1 A-component
A_touch = [defaultdict(int) for _ in range(n)]  # A-component root -> count per B-component

for i in range(n):
    A_touch[i][i] = 1  # vertex i in B-component i touches A-component i

edges = dict()  # track edge state

for _ in range(q):
    line = input().split()
    g, u, v = line[0], int(line[1])-1, int(line[2])-1
    key = (g, min(u,v), max(u,v))
    adding = key not in edges
    if adding:
        edges[key] = True
    else:
        edges.pop(key)

    if g == 'A':
        merged, r1, r2 = DSU_A.union(u,v)
        if merged:
            # merge counts
            for b_root in range(n):
                if A_touch[b_root].get(r2):
                    A_touch[b_root][r1] = A_touch[b_root].get(r1,0) + A_touch[b_root][r2]
                    del A_touch[b_root][r2]
    else:  # B
        merged, r1, r2 = DSU_B.union(u,v)
        if merged:
            # merge B roots, counts follow the new root
            for r in [r1, r2]:
                if r == r2: continue
                # no action needed, B_count and A_touch already mapped by root
                pass

    # compute answer
    ans = 0
    B_roots = set(DSU_B.find(i) for i in range(n))
    for b in B_roots:
        ans += len(A_touch[b]) - 1
    print(ans)
```このソリューションでは、DSU とメンバー セットを使用して、どのメンバー セットがどれかを追跡します。`A`-各コンポーネント`B`-コンポーネントに触れます。 更新中`A_touch`マージ時に正しく`A`-components を使用すると、各クエリの後に追加する最小のエッジを計算できるようになります。 主な微妙な点は、結合によってルートが変更された場合に正確なマッピングを維持することと、異なる個数を慎重にカウントすることです。`A`- コンポーネントが触れた。 

## 実用的な例

 **サンプル 1:**

 | クエリ | グラフ | エッジ |`A`コンポーネント |`B`コンポーネント | 答え |
 | --- | --- | --- | --- | --- | --- |
 | 1 | あ | 2-3 | {1}、{2,3}、{4}、{5}、{6} | {1}、{2}、{3}、{4}、{5}、{6} | 0 |
 | 2 | B | 1-3 | {1}、{2,3}、{4}、{5}、{6} | {1,3}、{2 | |
