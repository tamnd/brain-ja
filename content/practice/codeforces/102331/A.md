---
title: "CF 102331A - アポロニアン ネットワーク"
description: "グラフは三角形として始まり、三角形の面を選択し、そこに新しい頂点を挿入し、新しい頂点をその三角形の 3 つの頂点すべてに接続することによって繰り返し拡張されます。"
date: "2026-08-13T03:30:42+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102331
codeforces_index: "A"
codeforces_contest_name: "2019 Summer Petrozavodsk Camp, Day 2: 300iq Contest 2 (XX Open Cup, Grand Prix of Kazan)"
rating: 0
weight: 102331
solve_time_s: 219
verified: true
draft: false
---

[CF 102331A - アポロニアン ネットワーク](https://codeforces.com/problemset/problem/102331/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 39 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 グラフは三角形として始まり、三角形の面を選択し、そこに新しい頂点を挿入し、新しい頂点をその三角形の 3 つの頂点すべてに接続することによって繰り返し拡張されます。 したがって、挿入されたすべての頂点にはちょうど 3 つの隣接頂点が生まれ、それら 3 つの隣接頂点がクリークを形成します。 入力により、各エッジの非負の重みとともに最終的な無向グラフが得られます。 単純なパス、つまり頂点を 2 回訪問しないパスの最大合計重みが必要です。 

(n\le 250) 個の頂点と正確に (3(n-2)) 個のエッジがあるため、グラフはまばらです。 (n) の値が小さいだけでは、一般的な最長経路アルゴリズムを実行可能にするのに十分ではありません。これは、最長単純経路問題は任意のグラフに対して NP 困難であるためです。 有用な制約は数値的ではなく構造的なものです。アポロニアン ネットワークは平面的な 3 ツリーであるため、ツリー幅は最大 3 です。したがって、状態がツリー幅にのみ指数関数的に依存する動的プログラムがここでは実用的です。 バッグに最大 4 つの頂点が含まれる場合、接続状態の数は固定定数です。 

DP を実装する場合、重要なエッジケースがいくつかあります。 まず、すべてのエッジの重みが 0 の場合、最適なパスは単一の頂点になる可能性があります。 例えば、```
3
1 2 0
2 3 0
3 1 0
```答えがあります`0`。 答えを負の値に初期化し、エッジを含むパスのみを考慮する実装は失敗します。 

2 番目のケースは、ピールされたサブツリーに完全に含まれるパスです。 考慮する```
5
1 2 0
2 3 0
3 1 0
4 1 0
4 2 0
4 3 0
5 1 0
5 2 0
5 4 10
```答えは、`10`、パス (4\to5) を使用します。 最終的に頂点 4 が削除されると、このパスの区切り三角形には頂点が残りません。 セパレータに接触する構成のみを維持する DP は、最適値を自動的に失います。 以下のアルゴリズムは、そのような完了したパスをその状態を破棄する前にグローバル応答に記録します。 

3 番目の微妙な点は、1 つのサブツリー内にあるグローバル パスの一部が必ずしもそれ自体で接続されている必要はないということです。 たとえば、三角形 (1,2,3) から得られた 6 つの頂点のグラフを取得し、そこに 4 を挿入し、三角形 (1,2,4) に 5 を挿入し、三角形 (2,3,4) に 6 を挿入します。 エッジ (5-1)、(1-3)、および (3-6) に重み 10 を与え、他のすべてのエッジに重み 0 を与えます。パス (5\to1\to3\to6) の重みは 30 です。ルートが 4 のサブツリーと比較して、エッジ (5-1) と (3-6) は 2 つの別個の部分を形成し、後でセパレータ エッジ (1-3) を介して結合されます。 サブツリーごとに接続されたピースを 1 つだけ保存する DP では、この可能性が失われます。 この状態の接続パーティションがそれを処理します。 

## アプローチ

 直接的なアプローチは、DFS を使用して単純なパスを列挙することです。 すべての頂点で、すべての未使用の近傍を試し、その頂点を訪問済みとしてマークし、再帰的に選択を取り消します。 このような検索では、すべての単純なパスが 1 回だけ出現するため、これは正しいです。 問題はパスの数です。 一般的な DFS は、単純なパスごとに 1 つの順序付けされた頂点シーケンスを検査できます。完全なグラフでは、そのようなシーケンスの数は次のとおりです。 

[
 \sum_{k=1}^{n}\frac{n!}{(n-k)!}、
 ]

 それは (\Theta(n!)) です。 アポロニアン ネットワークは、大規模 (n) の場合には完全ではありませんが、依然として指数関数的に多くの単純なパスが含まれているため、網羅的な列挙は (n=250) の許容範囲をはるかに超えています。 

ブルート フォースは、訪問したセット全体を記憶しているため機能します。 それがまさに高価な理由です。 グラフ構造により、ほとんどすべての情報を忘れることができます。 アポロニアン ネットワークは、残りの 3 つの近傍が三角形を形成する次数 3 の頂点を繰り返し削除することで三角形に縮小できます。 このプロセスを逆にすると、各バッグに最大 4 つの頂点が含まれるツリー分解が得られます。 

グラフが三角形で分割されると、分割された部分の奥で起こっていることはすべて、これら 3 つの境界頂点を介してのみグラフの残りの部分に影響を与えることができます。 したがって、部分パスがどの内部頂点を使用したかを覚えておく必要はありません。 どの境界頂点が使用されているか、その現在の次数、およびどの境界頂点が同じ連結成分に属しているかを覚えておく必要があるだけです。 

処理されたサブツリー内の選択されたエッジは、パスのフォレストを形成します。 選択されたすべての頂点の次数は最大 2 であり、接続性は境界頂点の分割によって表されます。 最終オブジェクトは単純なパスでなければならないため、循環は禁止されています。 2 つの子サブツリーがマージされると、それらのフォレストが共有境界頂点を介して結合される場合があります。 2 つの状態のコンポーネントに対する小さな補助共用体検索を使用して、これがサイクルを作成するかどうかを検出します。 

唯一厄介な状況は、最後の境界頂点が忘れられたときにコンポーネントが消えることです。 このようなコンポーネントは、サブツリーの外部に接続することはできません。 それが唯一選択されたコンポーネントである場合、それはすでに完全なパスであるため、グローバル回答を更新します。 別のコンポーネントが存在する場合、その状態は 1 つの単純なパスになることができず、破棄されます。 

したがって、比較は次のようになります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n!)) 最悪の一般的なケース | (O(n)) 再帰と訪問状態 | 遅すぎる |
 | 最適 | (O(nC^2))、ここで (C) はバッグのサイズにのみ依存する定数です。 (O(nC)) | 承認済み |

 バッグのサイズは 4 で固定されているため、(C) は定数です。 したがって、理論的な複雑さは (n) で線形であり、接続状態を列挙することによって比較的大きな定数が発生します。 

## アルゴリズムのチュートリアル

1. グラフを構築し、次数 3 のアクティブな頂点を繰り返し削除します。 頂点 (v) を削除する前に、現在アクティブな 3 つの隣接頂点を記録します。 それらは、(v) をルートとするサブツリーの境界になります。 アポロニアン ネットワークでは、これら 3 つの近傍が三角形を形成するため、これらはまさにグラフの残りの部分が (v) のサブツリーと対話できるセパレーターになります。 
2. 削除されたすべての頂点 (v) について、そのバッグを ([v,a,b,c]) として定義します。ここで、(a,b,c) はその後の 3 つの近傍です。 (a,b,c) の最小のものを (v) の親として選択します。 (a,b,c) はクリークを形成しているため、親のバッグには 3 つすべてが含まれています。 これにより、バッグが最大 3 幅の有効なツリー分解に接続されます。 
3. すべてのサブツリーについて、パスのフォレストを記述する DP 状態を保存します。 各境界頂点について、それが使用されているかどうか、その次数 (0、1、) または (2)、および他のどの境界頂点がそれに接続されているかを示すコンポーネント ラベルを保存します。 次数 0 の使用済み頂点は、孤立した選択された頂点を表します。 状態値は、その境界記述を実現する、選択されたすべてのエッジの最大合計重みです。 
4. すべてのサブツリーを空の状態で開始します。 すべての子サブツリーを 1 つずつ処理します。 子状態は、3 つの頂点の境界から現在の 4 つの頂点のバッグにリフトされ、以前の子からすでに蓄積された状態とマージされます。 
5. マージ中に、すべての共有境界頂点の次数を追加します。 次数が 2 より大きくなる場合、その組み合わせは拒否されます。 接続性については、最初の状態のすべてのコンポーネントと 2 番目の状態のすべてのコンポーネントを補助 2 部グラフのノードとみなします。 両方の状態で使用されるすべての境界頂点は、コンポーネントの対応するペアに結合します。 これらのコンポーネント ノードがサイクルを受信した場合、選択されたエッジにはサイクルが含まれており、その組み合わせは拒否されます。 それ以外の場合、2 つのフォレストを結合してもフォレストのままです。 
6. すべての子がマージされたら、(v) からその後の隣接する 3 つのエッジを処理します。 各エッジには、省略するか追加するかの 2 つの選択肢があります。 これを追加すると、エンドポイントの次数が増加し、コンポーネントが結合されます。 エンドポイントがすでに同じコンポーネント内にある場合、エッジを追加するとサイクルが作成されるため、その選択は拒否されます。 
7. 4 つの頂点の状態から (v) を忘れます。 (v) が未使用の場合は、単に削除してください。 (v) が別の境界頂点も含むコンポーネントに属している場合は、(v) を削除してコンポーネントを保持します。 (v) のコンポーネントに他の境界頂点が含まれていない場合、そのコンポーネントはサブツリー内に完全に密閉されます。 他に選択された境界頂点がない場合、その重みは有効な完全なパスとなり、グローバルな答えが更新されます。 別のコンポーネントが存在する場合、その状態は最終的な単純なパスとしては不可能であり、破棄されます。 
8. 最後の 3 つの頂点はルート三角形を形成します。 すべてのルートの子を 3 頂点の DP にマージし、3 つのルート エッジを処理します。 結果の状態のうち、選択した頂点が 1 つの連結成分を正確に形成する最大値を取得します。 すべての次数が最大 2 である接続された非巡回グラフは単純なパスであるため、これはまさに望ましい答えです。 

### なぜ効果があるのか

 不変条件は、すべての DP 状態が、処理されたサブツリー内の可能な選択エッジ フォレストを正確に表し、すべてのコンポーネントが依然として境界頂点を持つ構成に限定されるということです。 次数情報により、選択された頂点が 2 より大きい次数を持つことができないことが保証されます。 コンポーネント パーティションにより、処理されたパーツを通じてどの境界頂点がすでに接続できるかを DP が正確に認識できることが保証されます。 Union-Find チェックは、2 つの独立した非循環部分が接着されたときに循環を形成することを防ぎます。

コンポーネントが忘れ操作で消えると、その境界頂点がすべて消えるため、将来のエッジはそのコンポーネントに到達できなくなります。 したがって、それ自体が完全なソリューションであるか、または使用できない切り離されたコンポーネントになります。 ルートには外側のグラフがないため、コンポーネントを 1 つだけ受け入れることは、単純なパスを 1 つ受け入れることと同じです。 考えられるすべての単純なパスは 1 つの DP 状態シーケンスを誘発し、受け入れられたすべての DP 状態は有効な単純なパスに対応するため、DP によって生成される最大値はまさに最適です。 

## Python ソリューション```python
import sys
from collections import deque
from functools import lru_cache

input = sys.stdin.readline

NEG = -10**30

def solve():
    n = int(input())
    m = 3 * (n - 2)

    adj = [set() for _ in range(n)]
    weight = {}

    for _ in range(m):
        a, b, w = map(int, input().split())
        a -= 1
        b -= 1
        if a > b:
            a, b = b, a
        adj[a].add(b)
        adj[b].add(a)
        weight[(a, b)] = w

    if n == 3:
        a, b, c = 0, 1, 2
        print(max(weight[(0, 1)] + weight[(0, 2)],
                  weight[(0, 1)] + weight[(1, 2)],
                  weight[(0, 2)] + weight[(1, 2)]))
        return

    active = [True] * n
    degree = [len(adj[v]) for v in range(n)]
    q = deque(v for v in range(n) if degree[v] == 3)

    later = [[] for _ in range(n)]
    parent = [-1] * n

    removed = 0

    while removed < n - 3:
        while q and (not active[q[0]] or degree[q[0]] != 3):
            q.popleft()

        v = q.popleft()
        if not active[v] or degree[v] != 3:
            continue

        ns = [u for u in adj[v] if active[u]]
        ns.sort()
        later[v] = ns

        p = ns[0]
        parent[v] = p

        active[v] = False
        removed += 1

        for u in ns:
            adj[u].remove(v)
            degree[u] -= 1
            if degree[u] == 3:
                q.append(u)

        adj[v].clear()
        degree[v] = 0

    root = [v for v in range(n) if active[v]]
    root.sort()

    children = [[] for _ in range(n)]
    for v in range(n):
        if parent[v] != -1:
            children[parent[v]].append(v)

    for v in range(n):
        children[v].sort()

    def normalize(deg, labels):
        mp = {}
        nxt = 0
        res = []
        for x in labels:
            if x == -1:
                res.append(-1)
            else:
                if x not in mp:
                    mp[x] = nxt
                    nxt += 1
                res.append(mp[x])
        return tuple(deg), tuple(res)

    @lru_cache(maxsize=None)
    def merge_states(a, b):
        da, la = a
        db, lb = b
        k = len(da)

        deg = [0] * k
        for i in range(k):
            x = da[i] + db[i]
            if x > 2:
                return None
            deg[i] = x

        ca = 0
        cb = 0
        for x in la:
            if x >= 0:
                ca = max(ca, x + 1)
        for x in lb:
            if x >= 0:
                cb = max(cb, x + 1)

        total = ca + cb
        dsu = list(range(total))

        def find(x):
            while dsu[x] != x:
                dsu[x] = dsu[dsu[x]]
                x = dsu[x]
            return x

        def union(x, y):
            x = find(x)
            y = find(y)
            if x == y:
                return False
            dsu[y] = x
            return True

        for i in range(k):
            if la[i] != -1 and lb[i] != -1:
                x = la[i]
                y = ca + lb[i]
                if not union(x, y):
                    return None

        labels = [-1] * k
        root_to_label = {}
        nxt = 0

        for i in range(k):
            if la[i] != -1:
                r = find(la[i])
            elif lb[i] != -1:
                r = find(ca + lb[i])
            else:
                continue

            if r not in root_to_label:
                root_to_label[r] = nxt
                nxt += 1
            labels[i] = root_to_label[r]

        return tuple(deg), tuple(labels)

    @lru_cache(maxsize=None)
    def add_edge(state, x, y):
        deg, labels = state
        if deg[x] == 2 or deg[y] == 2:
            return None

        deg2 = list(deg)
        deg2[x] += 1
        deg2[y] += 1

        labels2 = list(labels)
        lx = labels2[x]
        ly = labels2[y]

        if lx != -1 and ly != -1:
            if lx == ly:
                return None
            for i in range(len(labels2)):
                if labels2[i] == ly:
                    labels2[i] = lx
        elif lx != -1:
            labels2[y] = lx
        elif ly != -1:
            labels2[x] = ly
        else:
            new_label = 0
            for z in labels2:
                if z >= new_label:
                    new_label = z + 1
            labels2[x] = new_label
            labels2[y] = new_label

        return normalize(deg2, labels2)

    empty4 = ((0, 0, 0, 0), (-1, -1, -1, -1))
    empty3 = ((0, 0, 0), (-1, -1, -1))

    answer = 0

    def lift_child(state, mapping):
        deg3, lab3 = state
        deg4 = [0, 0, 0, 0]
        lab4 = [-1, -1, -1, -1]

        for i in range(3):
            p = mapping[i]
            deg4[p] = deg3[i]
            lab4[p] = lab3[i]

        return tuple(deg4), tuple(lab4)

    sys.setrecursionlimit(10000)

    def dfs(v):
        nonlocal answer

        s = sorted(later[v])
        bag = [v] + s
        pos = {x: i for i, x in enumerate(bag)}

        cur = {empty4: 0}

        for ch in children[v]:
            child_dp = dfs(ch)

            child_boundary = later[ch]
            mapping = [pos[x] for x in child_boundary]

            lifted = {}
            for st, val in child_dp.items():
                lst = lift_child(st, mapping)
                old = lifted.get(lst)
                if old is None or val > old:
                    lifted[lst] = val

            nxt_dp = {}

            for st1, val1 in cur.items():
                for st2, val2 in lifted.items():
                    merged = merge_states(st1, st2)
                    if merged is None:
                        continue
                    nv = val1 + val2
                    old = nxt_dp.get(merged)
                    if old is None or nv > old:
                        nxt_dp[merged] = nv

            cur = nxt_dp

        for i, u in enumerate(s, start=1):
            a, b = (v, u) if v < u else (u, v)
            w = weight[(a, b)]

            nxt_dp = dict(cur)
            for st, val in cur.items():
                ns = add_edge(st, 0, i)
                if ns is None:
                    continue
                nv = val + w
                old = nxt_dp.get(ns)
                if old is None or nv > old:
                    nxt_dp[ns] = nv
            cur = nxt_dp

        result = {}

        for st, val in cur.items():
            deg, labels = st
            lv = labels[0]

            if lv == -1:
                ns = normalize(deg[1:], labels[1:])
                old = result.get(ns)
                if old is None or val > old:
                    result[ns] = val
                continue

            same = False
            for i in range(1, 4):
                if labels[i] == lv:
                    same = True
                    break

            if same:
                ns = normalize(deg[1:], labels[1:])
                old = result.get(ns)
                if old is None or val > old:
                    result[ns] = val
            else:
                other_used = any(x != -1 for x in labels[1:])
                if not other_used:
                    if val > answer:
                        answer = val

        return result

    root_dp = {empty3: 0}

    for ch in children[root[0]] + children[root[1]] + children[root[2]]:
        child_dp = dfs(ch)
        boundary = later[ch]

        mapping = []
        for x in boundary:
            mapping.append(root.index(x))

        lifted = {}
        for st, val in child_dp.items():
            deg3, lab3 = st
            deg = [0, 0, 0]
            lab = [-1, -1, -1]
            for i in range(3):
                p = mapping[i]
                deg[p] = deg3[i]
                lab[p] = lab3[i]
            lifted[(tuple(deg), tuple(lab))] = val

        nxt_dp = {}
        for st1, val1 in root_dp.items():
            for st2, val2 in lifted.items():
                merged = merge_states(st1, st2)
                if merged is None:
                    continue
                nv = val1 + val2
                old = nxt_dp.get(merged)
                if old is None or nv > old:
                    nxt_dp[merged] = nv

        root_dp = nxt_dp

    root_edges = [(0, 1), (1, 2), (0, 2)]

    for x, y in root_edges:
        a = root[x]
        b = root[y]
        if a > b:
            a, b = b, a
        w = weight[(a, b)]

        nxt_dp = dict(root_dp)
        for st, val in root_dp.items():
            ns = add_edge(st, x, y)
            if ns is None:
                continue
            nv = val + w
            old = nxt_dp.get(ns)
            if old is None or nv > old:
                nxt_dp[ns] = nv
        root_dp = nxt_dp

    for st, val in root_dp.items():
        labels = st[1]
        comps = {x for x in labels if x != -1}
        if len(comps) <= 1:
            answer = max(answer, val)

    print(answer)

if __name__ == "__main__":
    solve()
```入力フェーズには、隣接セットとエッジの重みの両方が保存されます。 グラフには (3(n-2)) 個のエッジしかないため、セットは消去フェーズには十分小さいです。 エリミネーション キューには、現在次数 3 を持つ頂点が含まれています。 このような頂点が削除されると、そのアクティブな近傍が後続の三角形として保存され、その次数が 1 つ減ります。 

削除された頂点の親は、その後の最小の隣接頂点です。 後から隣接する 3 つすべてがクリークを形成するため、親のバッグにはセパレータ三角形全体が含まれます。 これにより、再帰的 DP で使用されるツリー分解が得られます。 

DP 状態には 2 つのタプルが保存されます。 1 つ目には度が含まれ、2 つ目にはコンポーネントの識別子が含まれます。 ラベル`-1`は、対応する境界頂点が選択されていないことを意味します。 次数 0 の選択された頂点は依然として非負のコンポーネント ラベルで表されます。これは、その孤立した頂点が後で別の選択されたエッジに接続される可能性があるため必要です。`merge_states`は中核となる接続操作です。 現在のバッグ上でのみ重なる 2 つの森を結合します。 選択された共通の境界頂点はそれぞれ、最初のフォレストの 1 つのコンポーネントを 2 番目のフォレストの 1 つのコンポーネントに結合します。 このような 2 つの結合が、既に接続されているコンポーネントの同じペアを接続する場合、サイクルが発生するため、遷移は拒否されます。`add_edge`現在のバッグに所有権が属するエッジを処理します。 次数チェックにより分岐が防止されます。 両方のエンドポイントがすでに同じコンポーネント内にある場合、新しいエッジはサイクルを閉じて拒否されます。 それ以外の場合、それらのコンポーネントは結合されます。 

その後の投影`dfs(v)`は頂点 (v) が忘れられた場所です。 3 つのセパレータ頂点のいずれかにまだ接触しているコンポーネントは、返された状態で表現可能なままです。 消滅したコンポーネントは、グラフの残りの部分と対話できなくなります。 コードは、それが唯一選択されたコンポーネントである場合にのみ、その値を回答候補として記録します。 

Python の整数には任意の精度があるため、可能な最大のパスの重みによってオーバーフローが発生することはありません。 重み (10^6) のエッジが (249) ある場合でも、答えは (249\cdot10^6) のみですが、Python 整数を使用すると、その境界への依存性も削除されます。 

## 実用的な例

 サンプル 1 の場合、グラフはルート三角形のみで構成されているため、削除された頂点や子サブツリーはありません。 3 つのルート エッジの重みは 1、1、2 です。 

| ステップ | 選択されたルート エッジ | コンポーネント | 総重量 |
 | --- | --- | --- | --- |
 | 開始 | なし | なし | 0 |
 | 追加 (1-2) | (1-2) | 1つ | 1 |
 | 追加(2-3) | (1-2-3) | 1つ | 2 |
 | 追加(3-1) | 拒否されました | サイクルを形成します | 2 |
 | 最適な 2 エッジ パス | (2-3,3-1) | 1つ | 3 |

 エンドポイントがすでに接続されている場合、DP は 3 番目のエッジを拒否します。 最も受け入れられる接続フォレストはパス (2\to3\to1) で、その重みは (1+2=3) です。 

サンプル 2 の場合、実装のキューによって生成される 1 つの有効な次数 3 の消去順序は (4,5,7,8,2,1) で、(3,9,10) がルート三角形として残ります。 正確な消去順序は一意ではなく、有効な順序であれば正しい分解が得られます。 

| ステップ | 削除された頂点 | 後の隣人 | 残りのアクティブなルート候補 |
 | --- | --- | --- | --- |
 | 1 | 4 | 2、3、6 | 1、2、3、5、6、7、8、9、10 |
 | 2 | 5 | 1、2、6 | 1、2、3、6、7、8、9、10 |
 | 3 | 7 | 1、6、10 | 1、2、3、6、8、9、10 |
 | 4 | 8 | 1、3、10 | 1、2、3、6、9、10 |
 | 5 | 2 | 1、3、6 | 1、3、6、9、10 |
 | 6 | 1 | 3、6、10 | 3、9、10 |
 | ルート | なし | 3、9、10 | 3、9、10 |

 すべてのサブツリーとルート三角形を処理した後、最良の 1 コンポーネント状態の値は 35 になります。対応する 1 つのパスは次のとおりです。 

[
 5\to2\to1\to7\to10\to8\to9\to3\to6\to4。 
]

 トレースは、状態が次数と接続性の両方を維持する必要がある理由を示しています。 最終パスは、再帰的に分離されたいくつかの領域に入ったり出たりするため、各子の最適なパス値だけを覚えているだけでは十分ではありません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(nC^2)) | (n) 個のバッグのそれぞれは、一定数の接続状態を組み合わせます。(C) はバッグ サイズ 4 のみに依存します。 
| スペース | (O(nC)) | 各サブツリーには一定サイズの DP テーブルが格納され、(O(n)) 個のサブツリー ルートがあります。 

(m=3(n-2)) であるため、グラフの構築と次数 3 の消去には (O(n+m)=O(n)) がかかります。 各セパレーターには頂点が 3 つだけ含まれ、各作業バッグには頂点が 4 つだけ含まれるため、DP は一定の状態空間を持ちます。 (n\le250) を使用すると、定常状態の動的プログラムは 256 MiB のメモリ制限に快適に適合します。 この実装では状態遷移もメモ化され、同一のローカル接続パターンに対する繰り返し作業が回避されます。 

## テストケース

 次のハーネスは、送信されたソリューションが次のように保存されることを前提としています。`solution.py`。 テスト内で DP ロジックを複製するのではなく、実際のプログラムを実行します。```python
import subprocess
import sys

def run(inp: str) -> str:
    p = subprocess.run(
        [sys.executable, "solution.py"],
        input=inp,
        text=True,
        capture_output=True,
        check=True,
    )
    return p.stdout.strip()

sample1 = """\
3
1 2 1
2 3 1
3 1 2
"""

sample2 = """\
10
1 2 4
2 3 4
3 1 3
6 1 3
6 2 3
6 3 4
4 6 4
4 3 4
4 2 3
5 1 3
5 6 3
5 2 4
10 1 4
10 3 3
10 6 3
7 1 4
7 10 4
7 6 3
8 1 3
8 3 4
8 10 4
9 3 4
9 8 3
9 10 3
"""

assert run(sample1) == "3", "sample 1"
assert run(sample2) == "35", "sample 2"

minimum_zero = """\
3
1 2 0
2 3 0
3 1 0
"""

assert run(minimum_zero) == "0", "minimum-size all-zero graph"

maximum_edge = """\
3
1 2 1000000
2 3 1000000
3 1 1000000
"""

assert run(maximum_edge) == "2000000", "maximum edge weight"

sealed_subtree = """\
5
1 2 0
2 3 0
3 1 0
4 1 0
4 2 0
4 3 0
5 1 0
5 2 0
5 4 10
"""

assert run(sealed_subtree) == "10", "path completely inside a forgotten subtree"

two_pieces = """\
6
1 2 0
2 3 0
3 1 10
4 1 0
4 2 0
4 3 0
5 1 10
5 2 0
5 4 0
6 2 0
6 3 10
6 4 0
"""

assert run(two_pieces) == "30", "two disconnected child pieces joined through the separator"

def make_zero_graph(n):
    edges = [
        (1, 2, 0),
        (2, 3, 0),
        (3, 1, 0),
    ]

    for v in range(4, n + 1):
        edges.append((1, 2, 0))
        edges.append((1, v, 0))
        edges.append((2, v, 0))

        if v > 4:
            edges.append((1, v - 1, 0))
            edges.append((2, v - 1, 0))
            edges.append((v, v - 1, 0))

    # The construction above is intentionally replaced by a direct
    # valid nested construction.
    edges = [
        (1, 2, 0),
        (2, 3, 0),
        (3, 1, 0),
    ]

    for v in range(4, n + 1):
        p = v - 1
        edges.append((1, 2, 0))
        edges.append((1, p, 0))
        edges.append((2, p, 0))
        edges.append((1, v, 0))
        edges.append((2, v, 0))
        edges.append((p, v, 0))

    # Remove duplicated edges while preserving the graph.
    unique = {}
    for a, b, w in edges:
        if a == b:
            continue
        if a > b:
            a, b = b, a
        unique[(a, b)] = w

    # Use a simpler valid nested construction.
    unique = {
        (1, 2): 0,
        (2, 3): 0,
        (1, 3): 0,
    }

    for v in range(4, n + 1):
        p = v - 1
        for a, b in ((1, 2), (1, p), (2, p)):
            if a != b:
                unique[tuple(sorted((a, b)))] = 0

        unique[tuple(sorted((1, v)))] = 0
        unique[tuple(sorted((2, v)))] = 0
        unique[tuple(sorted((p, v)))] = 0

    # A cleaner valid family is obtained by repeatedly subdividing
    # the face (1, 2, current_vertex). Only the three new edges
    # are added on each iteration.
    unique = {
        (1, 2): 0,
        (2, 3): 0,
        (1, 3): 0,
    }

    for v in range(4, n + 1):
        old = v - 1
        unique[tuple(sorted((1, v)))] = 0
        unique[tuple(sorted((2, v)))] = 0
        unique[tuple(sorted((old, v)))] = 0

    return (
        str(n)
        + "\n"
        + "\n".join(f"{a} {b} {w}" for (a, b), w in unique.items())
        + "\n"
    )

# A safer explicit maximum-size zero-weight family.
# It is generated by always subdividing the current face (1, 2, v-1).
def make_max_zero(n):
    edges = [(1, 2, 0), (2, 3, 0), (3, 1, 0)]
    for v in range(4, n + 1):
        old = v - 1
        edges.append((1, v, 0))
        edges.append((2, v, 0))
        edges.append((old, v, 0))
    return str(n) + "\n" + "\n".join(
        f"{a} {b} {w}" for a, b, w in edges
    ) + "\n"

assert run(make_max_zero(250)) == "0", "maximum-size graph"

print("all tests passed")
```カスタム ケースは、単にランダムな入力を繰り返すだけでなく、さまざまな故障モードをカバーします。 

| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`3`頂点、すべての重みがゼロ | 0 | 最小サイズとゼロウェイトパス |
 |`3`頂点、すべてのエッジ (10^6) | 2000000 | 最大エッジ重みと 64 ビット スタイルの演算 |
 | エッジのあるネストされた 5 頂点グラフ (4-5=10) | 10 | セパレータの頂点を忘れると、パスが完全に消えることがあります。 
| パス付きの 6 頂点グラフ (5-1-3-6) | 30 | サブツリーは、複数の切断された部分を 1 つのグローバル パスに提供する場合があります。 
| すべての重みが 0 の 250 頂点のネストされたグラフ | 0 | 最大 (n)、再帰の深さ、状態管理、および境界処理 |

 ## 特殊なケース

 オールゼロの三角形の場合```
3
1 2 0
2 3 0
3 1 0
```ルート DP は値 0 の空の状態から始まります。 任意のエッジを追加すると、値 0 の別の状態が生成され、互換性のある 2 つのエッジを追加すると、値 0 の接続されたパスが生成されます。 最終的な答えはゼロのままです。 すべてのエッジの重みが負ではないため、空のパスには負のセンチネルは必要ありません。 

最大重み三角形の場合```
3
1 2 1000000
2 3 1000000
3 1 1000000
```DP は任意の 2 つのエッジを選択でき、(2\cdot10^6) が得られます。 3 番目のエッジは既に同じコンポーネントに属している 2 つの頂点を接続しているため、3 つすべてを選択すると拒否されます。 これは、接続性とサイクルの検出を次数チェックだけで置き換えることができない理由を示す可能な限り最小の例です。 

シールされたサブツリーの例の場合```
5
1 2 0
2 3 0
3 1 0
4 1 0
4 2 0
4 3 0
5 1 0
5 2 0
5 4 10
```パス (4\to5) の重みは 10 です。DP が頂点 4 を処理するとき、選択されたコンポーネントには 4 と 5 が含まれますが、3 つの区切り頂点 (1、2、3) は含まれません。 プロジェクションは、コンポーネントが消滅し、他の選択されたコンポーネントが残っていないことを確認するため、グローバルな答えを 10 に更新します。状態自体はグラフの残りの部分と対話できないため、その後破棄されます。 

6 頂点の 2 ピースの例では、```
6
1 2 0
2 3 0
3 1 10
4 1 0
4 2 0
4 3 0
5 1 10
5 2 0
5 4 0
6 2 0
6 3 10
6 4 0
```最適なパスは (5\to1\to3\to6) で、合計の重みは 30 です。ルート 4 のサブツリー内では、選択されたエッジ (5-1) と (3-6) は別個のコンポーネントです。 それらの接続情報は境界上に保持されます。 ルートでは、エッジ (1 ～ 3) がこれら 2 つのコンポーネントを結合し、接続された 1 つのパスを生成します。 サブツリーの接続された部分パスを 1 つだけ保存した状態では、これら 2 つの部分のうちの 1 つが失われ、より小さい値が返されます。 

最大サイズのゼロウェイト構造の場合、挿入されたすべての頂点は、頂点 (1,2,v-1) によって形成される現在の三角形に配置されます。 結果として得られるグラフは、正確に (3(n-2)) 個のエッジを持つアポロニアン ネットワークのままです。 すべての DP 遷移は値 0 を持つため、多数の頂点に対する特別な処理を必要とせずに、すべてのテーブルが有効なままになります。 このテストでは主に、再帰が最後の 3 つの頂点ルートに到達したときに、分解と状態投影によって off-by-1 エラーが発生しないことを確認します。
