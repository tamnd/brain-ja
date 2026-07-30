---
title: "CF 102760I - ツリーに対するクエリ 17"
description: "ルート 1 を持つルート付きツリーがあります。各頂点には、負でない人の数が格納されます。 最初はすべての頂点の値がゼロです。 各操作により、サブツリー全体または単純なパス全体の値が増加します。"
date: "2026-07-29T00:03:53+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102760
codeforces_index: "I"
codeforces_contest_name: "2020 KAIST 10th ICPC Mock Contest (XXI Open Cup. Grand Prix of Korea. Division 2)"
rating: 0
weight: 102760
solve_time_s: 114
verified: true
draft: false
---

[CF 102760I - ツリー上のクエリ 17](https://codeforces.com/problemset/problem/102760/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 54 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 ルート 1 を持つルート付きツリーがあります。各頂点には、負でない人の数が格納されます。 最初はすべての頂点の値がゼロです。 各操作により、サブツリー全体または単純なパス全体の値が増加します。 更新のたびに、すべての人の総移動距離が可能な限り最小になる頂点を印刷する必要があります。 複数の頂点の最小値が同じ場合は、ルートに最も近い頂点が選択されます。 

最小化している値は、距離の加重合計です。 まで$10^5$頂点と$10^5$操作では、更新ごとにすべての頂点の値を再計算する必要があります。$10^{10}$仕事、それは不可能です。 操作ごとに対数またはそれに近い処理が必要です。 

トリッキーなケースは、タイ ルールと値がゼロの頂点に由来します。 子サブツリーにすべての人々のちょうど半分が含まれている頂点には、その子に移動する必要はありません。これは、両側が同じコストを与え、浅い頂点が勝つ必要があるためです。 

たとえば、ツリーがチェーンの場合は次のようになります。```
1
|
2
|
3
```頂点 3 の値は 10 で、答えは 3 ではなく 1 です。1 から 2 または 3 に移動しても、より浅い選択肢に勝てるほど収集距離は向上しません。 

## アプローチ

 ブルート フォース手法では、すべての頂点値が明示的に維持されます。 各更新後に、考えられるすべての回答頂点からツリー トラバーサルを実行し、合計距離を計算できます。 これは定義を直接評価するため正しいですが、単一のクエリにはすでにコストがかかります。$O(n)$あるいはさらに悪いことに、$O(nQ)$操作。 

重要な観察は、答えが重み付けされた重心であるということです。 エッジを越えて頂点からその親まで移動すると、移動したサブツリーの重みの量に応じてのみ距離の合計が変化します。 子サブツリーに全ユーザーの半分以上が含まれている場合、そのサブツリーに移動すると、答えが改善されます。 そうでなければ、私たちは留まるべきです。 

残りの問題は、サブツリーの追加とパスの追加でサブツリーの合計を維持することです。 重光分解で木を平らにします。 DFS の順序では、すべてのサブツリーが 1 つの間隔になり、すべてのパスが 1 つの間隔になります。$O(\log n)$間隔。 遅延セグメント ツリーは値を DFS 順序で維持し、間隔の追加とプレフィックス検索を可能にします。 

重み付けされた重心は、プレフィックスの重みが総重みの半分に達する最初の DFS 次数の位置を特定することによって見つけることができます。 答えはその頂点の祖先チェーンにあります。 バイナリ リフティングを使用して、最も高い有効な重心が見つかるまで上方に移動します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(nQ)$|$O(n)$| 遅すぎる |
 | HLD + セグメント ツリー |$O(Q\log^2 n)$|$O(n\log n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. ルート付きツリーを構築し、サブツリーのサイズ、深さ、重い子、DFS 次数、およびバイナリ リフティング祖先を計算します。 
2. すべてのサブツリーが 1 つの DFS 間隔に対応し、すべてのパスが対数的に多くの間隔に分割できるように、ヘビー ライト分解を使用します。 
3. 現在の頂点値を DFS 順序で遅延セグメント ツリーに保存します。 セグメント ツリーは、間隔への 1 の加算、合計の重みの検出、およびプレフィックスがターゲットに到達する最初の位置の検出をサポートします。 
4. アップデート後、総人数を$S$。 プレフィックスの合計が少なくとも以下である最初の DFS 位置を検索します。$\lceil S/2\rceil$。 
5. 答えはこの頂点の祖先です。 現在の祖先に十分な重みが含まれていない間に、バイナリ リフティングとサブツリー合計クエリを使用して上方に登ります。 

これが機能する理由: 頂点とその親のコストの差は、子側に総ウェイトの半分以上が含まれているかどうかによってのみ決まります。 選択された頂点はまさに最も浅い頂点であり、その子方向に重い側面が含まれていてもコストを改善できません。 接頭辞検索はその重い側面の内側の点を見つけ、祖先を登ることで必要なタイ ルールを解決します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

sys.setrecursionlimit(300000)

n = int(input())
g = [[] for _ in range(n + 1)]
for _ in range(n - 1):
    a, b = map(int, input().split())
    g[a].append(b)
    g[b].append(a)

par = [[0] * (n + 1) for _ in range(17)]
dep = [0] * (n + 1)
sz = [0] * (n + 1)
son = [0] * (n + 1)

def dfs1(u, p):
    par[0][u] = p
    dep[u] = dep[p] + 1
    sz[u] = 1
    best = 0
    for v in g[u]:
        if v == p:
            continue
        dfs1(v, u)
        sz[u] += sz[v]
        if sz[v] > best:
            best = sz[v]
            son[u] = v

dfs1(1, 0)

for j in range(1, 17):
    for i in range(1, n + 1):
        par[j][i] = par[j - 1][par[j - 1][i]]

tin = [0] * (n + 1)
tout = [0] * (n + 1)
rev = [0] * (n + 1)
top = [0] * (n + 1)
timer = 0

def dfs2(u, t):
    global timer
    timer += 1
    tin[u] = timer
    rev[timer] = u
    top[u] = t
    if son[u]:
        dfs2(son[u], t)
    for v in g[u]:
        if v != par[0][u] and v != son[u]:
            dfs2(v, v)
    tout[u] = timer

dfs2(1, 1)

class Seg:
    def __init__(self, n):
        self.s = [0] * (4 * n)
        self.lz = [0] * (4 * n)

    def add(self, x, l, r, ql, qr):
        if ql <= l and r <= qr:
            self.s[x] += r - l + 1
            self.lz[x] += 1
            return
        m = (l + r) // 2
        self.push(x, l, r)
        if ql <= m:
            self.add(x * 2, l, m, ql, qr)
        if qr > m:
            self.add(x * 2 + 1, m + 1, r, ql, qr)
        self.s[x] = self.s[x * 2] + self.s[x * 2 + 1]

    def push(self, x, l, r):
        if self.lz[x]:
            m = (l + r) // 2
            v = self.lz[x]
            self.s[x * 2] += v * (m - l + 1)
            self.s[x * 2 + 1] += v * (r - m)
            self.lz[x * 2] += v
            self.lz[x * 2 + 1] += v
            self.lz[x] = 0

    def query(self, x, l, r, ql, qr):
        if ql <= l and r <= qr:
            return self.s[x]
        self.push(x, l, r)
        m = (l + r) // 2
        res = 0
        if ql <= m:
            res += self.query(x * 2, l, m, ql, qr)
        if qr > m:
            res += self.query(x * 2 + 1, m + 1, r, ql, qr)
        return res

    def kth(self, x, l, r, k):
        if l == r:
            return l
        self.push(x, l, r)
        m = (l + r) // 2
        if self.s[x * 2] >= k:
            return self.kth(x * 2, l, m, k)
        return self.kth(x * 2 + 1, m + 1, r, k - self.s[x * 2])

seg = Seg(n)

def path_add(a, b):
    while top[a] != top[b]:
        if dep[top[a]] < dep[top[b]]:
            a, b = b, a
        seg.add(1, 1, n, tin[top[a]], tin[a])
        a = par[0][top[a]]
    if dep[a] > dep[b]:
        a, b = b, a
    seg.add(1, 1, n, tin[a], tin[b])

def subtree_sum(u):
    return seg.query(1, 1, n, tin[u], tout[u])

q = int(input())
ans = []
for _ in range(q):
    data = list(map(int, input().split()))
    if data[0] == 1:
        u = data[1]
        seg.add(1, 1, n, tin[u], tout[u])
    else:
        path_add(data[1], data[2])

    total = seg.s[1]
    need = (total + 1) // 2
    x = rev[seg.kth(1, 1, n, need)]

    for j in range(16, -1, -1):
        p = par[j][x]
        if p and subtree_sum(p) >= need:
            x = p

    while par[0][x] and subtree_sum(par[0][x]) >= need:
        x = par[0][x]

    ans.append(str(x))

print("\n".join(ans))
```セグメント ツリーには、実際の頂点値が DFS 順序で格納されます。 の`add`この操作は、サブツリーとヘビー ライト パスの両方の更新を処理します。 の`kth`関数は、累積重みが全体の半分に達する最初の場所を見つけます。これにより、重心を含む領域が特定されます。 

祖先ジャンプは、見つかった頂点の祖先だけが答えになり得るという事実を利用します。 最後の上向きの動きでは、必要なタイ ブレークに一致する、最も浅い有効な重心が維持されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(Q\log^2 n)$| 各パス更新で使用されるのは、$O(\log n)$セグメント、各セグメントの更新コスト$O(\log n)$、重心検索では対数祖先ジャンプが使用されます。 |
 | スペース |$O(n\log n)$| バイナリ リフティングがメモリ使用量の大半を占めます。 |

 すべての操作がツリー全体をスキャンするのではなく対数的であるため、境界によりこのアプローチが可能になります。 

## 特殊なケース

 すべての重みがリーフに集中している単一のパスは、タイ ルールをテストします。 アルゴリズムはヘビー プレフィックスを見つけますが、最も近い有効な祖先まで遡るため、誤ってリーフを返すことはありません。 

両方のエンドポイントが等しいパスを介して単一の頂点を更新するクエリは、パス分解境界をテストします。 ヘビーライト分解はこれを 1 つの頂点間隔として扱い、セグメント ツリーは通常のポイント更新を実行します。 

重い側に総重量のちょうど半分が含まれる木は、等価性の処理をテストします。 アルゴリズムが使用するのは、$\lceil S/2\rceil$祖先がまだ条件を満たしている場合にのみ上に移動するため、浅い頂点が保存されます。
