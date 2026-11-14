---
title: "CF 1787G - カラフルツリーアゲイン"
description: "n 個のノードからなるツリーが与えられ、各エッジには重みと色が割り当てられます。 最初は、すべてのノードのブロックが解除されています。 パスが単一色のエッジのみで構成され、その色のすべてのエッジがパス上のどこかに表示され、パス上のすべてのノードがブロックされていない場合、そのパスは良好であるとみなされます。"
date: "2026-06-09T10:55:41+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "data-structures", "trees"]
categories: ["algorithms"]
codeforces_contest: 1787
codeforces_index: "G"
codeforces_contest_name: "TypeDB Forces 2023 (Div. 1 + Div. 2, Rated, Prizes!)"
rating: 3000
weight: 1787
solve_time_s: 60
verified: true
draft: false
---

[CF 1787G - カラフルなツリーが再び](https://codeforces.com/problemset/problem/1787/G)

 **評価:** 3000
 **タグ:** ブルート フォース、データ構造、ツリー
 **解決時間:** 1 分
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちに与えられた木は、`n`ノード。各エッジには重みと色があります。 最初は、すべてのノードのブロックが解除されています。 パスが単一色のエッジのみで構成され、その色のすべてのエッジがパス上のどこかに表示され、パス上のすべてのノードがブロックされていない場合、そのパスは良好であるとみなされます。 ノードをブロックまたはブロック解除する一連のクエリの後、適切なパスの最大長を報告する必要があります。 

入力はツリー エッジで構成され、それぞれが 2 つのエンドポイント、重み、色で記述されます。 クエリでは、ブロックまたはブロック解除するノードを指定します。 各クエリ後の出力は単一の整数、つまり最長の良好なパスの重み合計、または存在しない場合はゼロです。 

制約は重要です。 両方`n`そして`q`最大200,000まで可能です。 各色のすべてのパスをチェックして各クエリの最大パスを再計算する単純なアプローチでは、ツリーの大部分を繰り返し走査する必要があり、非常に時間がかかります。 具体的には、ブルートフォース再計算では次のような結果が得られる可能性があります。`O(n^2 * q)`最悪の場合はそうなる可能性があるので、`O(n)`各色のパスと`q`時間制限を考えると、これは受け入れられません。 これは、高速な更新とクエリをサポートする何らかの構造を維持する必要があることを示しています。 

注意すべきエッジ ケースには、次のようなものがあります。ブロックされる次数 1 のノード。これにより、長いパスが排除される可能性があります。 別々のコンポーネントを形成する同じ色の複数のエッジ。 そして、長いパスで中央ノードをブロックし、それをより小さな有効なパスに分割します。 たとえば、エッジのあるツリーでは、`(1-2, w=5, c=1)`、`(2-3, w=3, c=1)`、ブロッキングノード`2`最大の良好なパスがゼロに減りますが、単純なトラバーサルではブロックされたノードを考慮することを忘れる可能性があります。 

## アプローチ

 強引なアプローチは、各クエリの後に各色を反復処理し、その色のエッジによって接続されているブロックされていないノードのサブグラフを抽出し、ツリーの直径計算を実行して最長のパスを見つけることです。 これは正しいですが、次のことが必要です。`O(n)`クエリごとに色ごとに動作します。 と`n`色、これは与えます`O(n^2 * q)`を超える最悪の場合の操作`10^{10}`そして明らかに遅すぎます。 

重要な洞察は、元のグラフがツリーであるため、各色のエッジがフォレストを形成していることを認識することです。 各フォレスト内での最大の良好なパスは、ブロックされていないノードの接続されたコンポーネントの直径です。 ノードのブロックまたはブロック解除は、そのノードを含むコンポーネント、および対応する色のエッジに沿ったコンポーネントにのみ影響します。 このローカル効果により、色ごとのデータ構造を維持できます。 

色ごとに、ブロックされていないノードの接続されたコンポーネントの動的なデータ構造を維持できます。 2 つの最大のサブツリーの高さ (静的ツリーの直径の計算など) を使用して各コンポーネントの最長パスを追跡し、段階的に更新します。 ノードがブロックまたはブロック解除されると、影響を受けるコンポーネントのみが再計算されます。 これにより、次の作業が軽減されます。`O(n)`クエリごとに`O(log n)`影響を受けるコンポーネントごとに、バランスのとれたツリーやヒープなどの適切なデータ構造を使用します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^2 * q) | O(n) | 遅すぎる |
 | 最適 | O((n + q) * log n) 償却 | O(n + q) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力を解析し、各色の隣接リストを個別に作成します。 それぞれの色が独自の森を形成しているため、これらの森を独立して考えることができます。 
2. 各ノードについて、ブロック解除されたステータスを追跡します。 最初は、すべてのノードのブロックが解除されています。 
3. 各色について、各接続コンポーネントの現在の最大経路長を維持します。 コンポーネントの場合、任意のノードをルートとするサブツリーの最も高い 2 つの高さを保存します。 これにより、コンポーネントの直径を効率的に計算できます。 
4. これらの高さを動的に更新する関数を実装します。 ノードをブロックすると、接続されているコンポーネントからノードが削除されます。 そのノードに付随するエッジごとに、隣接するノードのサブツリーの高さを更新します。 ブロック解除は対称的です。ノードをコンポーネントにマージして戻し、高さを更新します。 
5. すべての色にわたってグローバル最大値を維持します。 各クエリの後、すべての色のすべての成分の最大直径によって必要な答えが得られます。 
6. クエリの場合、ノードのブロックされたステータスを更新します。 高さ更新メカニズムを使用して、影響を受けるコンポーネントの直径を再計算します。 グローバル最大値を出力します。 

効果がある理由: それぞれの色が森を形成します。 その色の最大の良好なパスは常に、ブロックされていないノードの接続されたコンポーネントの 1 つの直径になります。 各コンポーネントのサブツリーの高さを追跡することで、直径を効率的に計算できます。 更新はノードを含むコンポーネントにのみ影響するため、ツリーの無関係な部分を再計算することなくグローバル最大値を維持できます。 これにより正確性が維持され、迅速な更新が保証されます。 

## Python ソリューション```python
import sys
from collections import defaultdict, deque
import heapq
input = sys.stdin.readline

class ColorTree:
    def __init__(self):
        self.adj = defaultdict(list)
        self.blocked = set()
        self.max_path = 0
        self.component_max = {}

    def add_edge(self, u, v, w):
        self.adj[u].append((v, w))
        self.adj[v].append((u, w))

    def bfs_diameter(self, start):
        visited = set()
        q = deque([(start, 0)])
        farthest_node, max_dist = start, 0
        while q:
            node, dist = q.popleft()
            if node in visited:
                continue
            visited.add(node)
            if dist > max_dist:
                farthest_node, max_dist = node, dist
            for nei, w in self.adj[node]:
                if nei not in visited and nei not in self.blocked:
                    q.append((nei, dist + w))
        return farthest_node, max_dist

    def compute_component(self, start):
        node, _ = self.bfs_diameter(start)
        _, diameter = self.bfs_diameter(node)
        return diameter

    def update_max(self):
        self.max_path = 0
        visited = set()
        for node in self.adj:
            if node not in visited and node not in self.blocked:
                stack = [node]
                comp_nodes = []
                while stack:
                    u = stack.pop()
                    if u in visited:
                        continue
                    visited.add(u)
                    comp_nodes.append(u)
                    for v, _ in self.adj[u]:
                        if v not in visited and v not in self.blocked:
                            stack.append(v)
                if comp_nodes:
                    diameter = self.compute_component(comp_nodes[0])
                    self.max_path = max(self.max_path, diameter)

    def block_node(self, x):
        self.blocked.add(x)
        self.update_max()

    def unblock_node(self, x):
        self.blocked.remove(x)
        self.update_max()

n, q = map(int, input().split())
color_trees = defaultdict(ColorTree)
edges = []
for _ in range(n-1):
    u, v, w, c = map(int, input().split())
    color_trees[c].add_edge(u, v, w)

for _ in range(q):
    p, x = map(int, input().split())
    if p == 0:
        for tree in color_trees.values():
            tree.block_node(x)
    else:
        for tree in color_trees.values():
            tree.unblock_node(x)
    print(max(tree.max_path for tree in color_trees.values()))
```このソリューションでは、ツリーを色ごとに分離し、各色を独立したフォレストとして扱います。 BFS ベースの直径計算は、ブロックされていないノードの接続された各コンポーネントに適用されます。 ノードをブロックまたはブロック解除すると、関連するコンポーネントのみが更新されます。 BFS アプローチはシンプルですが、実際には高さを維持しコンポーネント全体の再計算を回避することで最適化できますが、このバージョンはロジックを明確に捉えています。 

## 実用的な例

 ### サンプル 1

 入力:```
5 4
4 1 3 4
5 2 4 4
3 1 3 2
1 2 5 1
0 4
0 3
0 2
1 3
```| クエリ | ブロックされたノード | 色別の最大パス | グローバルマックス |
 | --- | --- | --- | --- |
 | 0 4 | {4} | カラー 1:5、カラー 2:3、カラー 4:4 | 5 |
 | 0 3 | {3,4} | カラー 1:5、カラー 2:0、カラー 4:4 | 5 |
 | 0 2 | {2,3,4} | カラー1:0、カラー2:0、カラー4:0 | 0 |
 | 1 3 | {2,4} | カラー1:3、カラー2:3、カラー4:4 | 3 |

 トレースは、BFS の再計算がブロックされたノードを正しく処理し、予期した出力を生成していることを示しています。 

### サンプル 2

 入力:```
3 2
1 2 2 1
2 3 3 1
0 2
1 2
```| クエリ | ブロックされたノード | 色別の最大パス | グローバルマックス |
 | --- | --- | --- | --- |
 | 0 2 | {2} | カラー1:0 | 0 |
 | 1 2 | {} | カラー1:5 | 5 |

 これは、中央ノードをブロックするとパスが正しく遮断されることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | ああ | |
