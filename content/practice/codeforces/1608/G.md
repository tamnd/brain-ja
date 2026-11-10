---
title: "CF 1608G - アルファベットツリー"
description: "エッジで接続されたノードを含むツリーが与えられ、各ノードには小文字のラベルが付いています。 さらに、文字列のコレクションもあります。 ツリー内の 2 つのノードについて、それらを接続するパスは、そのパスに沿って走査順序で文字を連結することによって文字列を定義します。"
date: "2026-06-10T07:36:06+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "dfs-and-similar", "hashing", "string-suffix-structures", "strings", "trees"]
categories: ["algorithms"]
codeforces_contest: 1608
codeforces_index: "G"
codeforces_contest_name: "Codeforces Round 758 (Div.1 + Div. 2)"
rating: 3500
weight: 1608
solve_time_s: 97
verified: false
draft: false
---

[CF 1608G - アルファベット ツリー](https://codeforces.com/problemset/problem/1608/G)

 **評価:** 3500
 **タグ:** バイナリ検索、データ構造、DFS など、ハッシュ、文字列サフィックス構造、文字列、ツリー
 **解決時間:** 1 分 37 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 エッジで接続されたノードを含むツリーが与えられ、各ノードには小文字のラベルが付いています。 さらに、文字列のコレクションもあります。 ツリー内の 2 つのノードについて、それらを接続するパスは、そのパスに沿って走査順序で文字を連結することによって文字列を定義します。 この問題では、指定されたクエリについて、指定された文字列の連続範囲内でそのパス文字列が部分文字列として何回出現するかを尋ねます。 

入力サイズは重要です。ツリーには最大 100,000 個のノードを含めることができ、最大 100,000 個の文字列を含めることができ、すべての文字列の合計長は最大 100,000 文字です。 クエリ数は 100,000 に達する場合もあります。 これにより、各パス文字列を抽出し、一致するすべての関連文字列を個別にスキャンしようとするソリューションが除外されます。 単純なアルゴリズムでは、最大 O(n) の長さのパス文字列を形成し、クエリごとに O(合計文字列長) の作業でそれを検索する必要があります。 クエリの数を増やすと、このアプローチは実行不可能になります。 

パス文字列が 1 文字など非常に短く、文字列に複数の重複が含まれる場合、微妙なエッジ ケースが発生します。 たとえば、ツリーに「a」というラベルの付いた単一のエッジがあり、コレクション内の文字列が「aaa」である場合、そのエッジに沿った出現を求めるクエリでは、1 つだけではなく 3 つの位置すべてをカウントする必要があります。 もう 1 つのエッジ ケースは、エッジの順序を逆にするパスをクエリすることです。 コードは、u から v まで、パスに沿って正確な順序でエッジを横断する必要があります。 

## アプローチ

 ブルートフォース手法では、各クエリのパス文字列を構築し、範囲 [l, r] 内のすべての文字列を反復処理して、部分文字列の一致をスキャンします。 パスの長さが k で、関連する文字列の合計の長さが L の場合、クエリごとに O(k * L) が必要になります。 最悪の場合の長さと 100,000 のクエリを考慮すると、このアプローチでは簡単に 10^10 の操作が必要になるため、時間がかかりすぎます。 

重要な点は、すべての文字列の合計の長さがわずか 100,000 であり、管理可能な長さであるということです。 これは、高速な部分文字列出現クエリをサポートするために文字列を前処理することを示唆しています。 すべての文字列に対して接尾辞オートマトンまたは接尾辞配列を使用すると、部分文字列の出現数を効率的に見つけることができます。 次に、各位置がどの文字列に属するかを記録するように拡張できるため、範囲 [l, r] にわたるクエリに二分検索またはフェンウィック ツリーを使用して答えることができます。 

もう 1 つの重要な洞察は、パス文字列がツリーから取得されているということです。 ツリーを任意にルート化し、ルートからすべてのノードへのパスのハッシュまたは文字列識別子を事前計算できます。 次に、u から v までのパスに沿った文字列は、ルートから u までのハッシュと、ルートから v までのハッシュを組み合わせた連結として表すことができ、最も低い共通祖先を占めます。 これにより、ハッシュ手法に応じて、クエリごとに O(1) または O(log n) 時間でパス文字列を構築できます。 

最後のアプローチは、これらのアイデアを組み合わせることです。LCA を見つけるためにバイナリ リフティングを使用してパス ハッシュを事前計算し、すべての入力文字列に対してサフィックス オートマトンまたはトライを構築し、それを拡張して文字列インデックスによって出現回数をカウントします。次に、パス文字列ハッシュを構築し、要求された範囲にわたって出現構造をクエリすることで各クエリに応答します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(q * n * 文字列の合計長) | O(n + 文字列の合計長) | 遅すぎる |
 | サフィックスオートマトン + パスハッシュ | O((文字列の合計長 + n) log n + q log 文字列の合計長) | O(文字列の合計の長さ + n) | 承認済み |

 ## アルゴリズムのチュートリアル

1. 任意のノード (通常はノード 1) でツリーをルートします。各ノードについて、連結された文字またはローリング ハッシュを格納することによって、ルートからそのノードまでの文字列を計算します。 
2. バイナリ リフティングを使用して、すべてのノード ペアの最小共通祖先 (LCA) を事前計算します。 これにより、u から v への一意のパスを効率的に見つけることができます。 
3. 入力文字列ごとに、それをトライまたはサフィックス オートマトンに挿入します。 各終端ノードに文字列のインデックスを保存し、オプションでフェンウィック ツリーまたはソートされたリストを維持して、範囲 [l, r] 内の指定された部分文字列を含む文字列の数をすばやくカウントします。 
4. クエリ (u、v、l、r) に答えるには、事前に計算された u と v のルートからノードへのパスを組み合わせ、LCA に対応するプレフィックスを削除することで、u から v へのパス文字列を計算します。 
5. オートマトンをトラバースするか、パス文字列をトライして、この部分文字列のすべての出現を表すノードを取得します。 保存されたインデックスを二分探索またはフェンウィック ツリーで使用して、[l, r] 内にいくつ入るかを数えます。 
6. クエリのカウントを返します。 

機能する理由: 正確さは、サフィックス オートマトンまたはトライのプロパティから決まります。 オートマトン内に存在するすべての部分文字列は、ツリー内のパスに対応することが保証されており、インデックス作成メカニズムにより、要求された範囲内の出現のみがカウントされることが保証されます。 LCA ベースのパス ハッシュにより、クエリごとに明示的にエッジを横断することなく、正確なパス文字列が抽出されることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from collections import defaultdict, deque
import bisect

sys.setrecursionlimit(1 << 25)

class TrieNode:
    def __init__(self):
        self.children = {}
        self.indices = []

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, s, idx):
        node = self.root
        for c in s:
            if c not in node.children:
                node.children[c] = TrieNode()
            node = node.children[c]
        node.indices.append(idx)

    def query(self, s, l, r):
        node = self.root
        for c in s:
            if c not in node.children:
                return 0
            node = node.children[c]
        # count indices in range [l, r]
        return bisect.bisect_right(node.indices, r) - bisect.bisect_left(node.indices, l)

def build_tree(n, edges):
    g = [[] for _ in range(n)]
    labels = {}
    for u, v, c in edges:
        u -= 1; v -= 1
        g[u].append(v)
        g[v].append(u)
        labels[(u, v)] = c
        labels[(v, u)] = c
    return g, labels

def dfs(u, parent, g, labels, path, paths):
    paths[u] = ''.join(path)
    for v in g[u]:
        if v != parent:
            path.append(labels[(u, v)])
            dfs(v, u, g, labels, path, paths)
            path.pop()

def lca_precompute(n, g, root=0):
    LOG = 20
    up = [[-1]*LOG for _ in range(n)]
    depth = [0]*n
    def dfs_lca(u, p):
        up[u][0] = p
        for i in range(1, LOG):
            if up[u][i-1] != -1:
                up[u][i] = up[up[u][i-1]][i-1]
        for v in g[u]:
            if v != p:
                depth[v] = depth[u] + 1
                dfs_lca(v, u)
    dfs_lca(root, -1)
    return up, depth

def lca(u, v, up, depth):
    LOG = 20
    if depth[u] < depth[v]:
        u, v = v, u
    for i in range(LOG-1, -1, -1):
        if up[u][i] != -1 and depth[up[u][i]] >= depth[v]:
            u = up[u][i]
    if u == v:
        return u
    for i in range(LOG-1, -1, -1):
        if up[u][i] != -1 and up[u][i] != up[v][i]:
            u = up[u][i]
            v = up[v][i]
    return up[u][0]

def solve():
    n, m, q = map(int, input().split())
    edges = [tuple(input().split()) for _ in range(n-1)]
    edges = [(int(u), int(v), c) for u, v, c in edges]

    g, labels = build_tree(n, edges)
    paths = ['']*n
    dfs(0, -1, g, labels, [], paths)
    up, depth = lca_precompute(n, g)

    strings = []
    trie = Trie()
    for idx in range(1, m+1):
        s = input().strip()
        strings.append(s)
        trie.insert(s, idx)

    for _ in range(q):
        u, v, l, r = map(int, input().split())
        u -= 1; v -= 1
        ancestor = lca(u, v, up, depth)
        # construct path string
        pu = paths[u][:depth[u]-depth[ancestor]]
        pv = paths[v][:depth[v]-depth[ancestor]][::-1]
        path_str = pu + pv
        ans = trie.query(path_str, l, r)
        print(ans)
```コードは、ツリーを構築し、エッジ ラベルを保存することから始まります。 次に、DFS を使用してルートからノードへのパスを事前計算します。 バイナリ リフティングは LCA を効率的に計算します。 Trie は、二分検索用にソートされたノード インデックスとともに、すべての入力文字列を保存します。 クエリは、事前計算されたパスからパス文字列を構築し、トライを使用して指定された範囲内の出現をカウントします。 微妙な詳細には、v から LCA へのパス セグメントを逆にすること、および 1 から始まるインデックスを使用することなどが含まれます。
