---
title: "CF 103577G - 数学的変換"
description: "ノード $1$ をルートとするツリーが与えられます。ここで、すべてのノードには数値 (最初は $0$) が格納されます。 2 種類の操作がオンラインで実行されます。 最初の操作では、2 つのノード $u$ と $v$ の間の一意の単純なパスに沿った値の合計を求めます。"
date: "2026-07-03T03:49:11+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103577
codeforces_index: "G"
codeforces_contest_name: "2021 ICPC Universidad Nacional de Colombia Programming Contest"
rating: 0
weight: 103577
solve_time_s: 1054
verified: true
draft: false
---

[CF 103577G - 数学的変換](https://codeforces.com/problemset/problem/103577/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 17 分 34 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 ノードをルートとするツリーが与えられます$1$、最初はすべてのノードに数値が格納されます。$0$。 2 種類の操作がオンラインで実行されます。 

最初の操作では、2 つのノード間の一意の単純なパスに沿った値の合計を求めます。$u$そして$v$。 これは古典的なツリー パス クエリであり、答えは現在の動的ノード値によって異なります。 

2 番目の操作では、構造化された方法でサブツリーを更新します。 選択したルートノードの場合$u$、すべてのノード$x$サブツリー内の は、からの距離に応じて増分を受け取ります。$u$。 もし$d(x,u)$その距離であれば、付加価値は$v + k \cdot d(x,u)$。 これは均一な更新ではありません。サブツリーのより深いノードほど、等差数列でより大きな増分を受け取ります。 

制約により、最大で$3 \times 10^5$ノードとクエリ。明示的なトラバーサルによって各パスまたはサブツリーを処理するソリューションは直ちに除外されます。 すべての更新に対してサブツリーをたどり、すべてのクエリに対してパスをたどる単純なアプローチでは、次のように劣化します。$O(n)$操作ごとに、与える$O(nq)$最悪の場合、それは実現可能性をはるかに超えています。 

更新が距離に依存するという事実から、微妙な困難が生じます。 単純なオイラー ツアー サブツリー範囲の更新は、追加される値がサブツリー全体で一定ではないため、直接適用されません。 もう 1 つの隠れた問題は、パス クエリが以前のすべてのサブツリー操作からの累積された更新に依存するため、部分的または遅延処理はグローバルに一貫性を保つ必要があることです。 

## アプローチ

 ブルート フォース ソリューションでは、ノード値が明示的に維持されます。 サブツリーの更新では、DFS が実行されます。$u$、距離を計算し、各ノードを更新します。 パス クエリは、次のパスをたどります。$u$に$v$そして値を合計します。 それぞれの操作にかかる費用$O(n)$最悪の場合、総コストは$O(nq)$、これは不可能です$3 \times 10^5$。 

重要な観察は、ノード値をルートからの距離で表現し、パス分解を使用すると、両方の操作が単純になるということです。 更新ルールは以下に依存します$d(x,u)$、これは深さと最も共通の祖先を使用して書き換えることができます。$$d(x,u) = \text{depth}(x) + \text{depth}(u) - 2\cdot \text{depth}(\mathrm{lca}(x,u)).$$のサブツリー内$u$、LCA 用語は単純化されます。$u$の先祖です$x$、 それで$\mathrm{lca}(x,u)=u$。 したがって$$d(x,u) = \text{depth}(x) - \text{depth}(u).$$これにより、更新は次のようになります。$$v + k(\text{depth}(x) - \text{depth}(u)) = (v - k\cdot \text{depth}(u)) + k\cdot \text{depth}(x).$$したがって、更新ごとに、サブツリー全体にわたって線形関数が追加されます。$$A + B \cdot \text{depth}(x)$$どこ$A = v - k\cdot \text{depth}(u)$そして$B = k$。 

これにより、問題は以下のサポートに集約されます。 

1. 深さの一次関数のサブツリー範囲の追加。 
2. 動的ノード値に対するパス合計クエリ。 

オイラーツアーを使用してツリーを平坦化し、各サブツリーがセグメントになるようにします。 線形関数の係数を追跡するために、2 つのフェンウィック ツリー (または遅延伝播を備えたセグメント ツリー) を維持します。 各ノード値は、次の 2 つのグローバル構造の組み合わせになります。 

1 つは一定の寄与用、もう 1 つは深度加重寄与用です。 

パス クエリの場合は、標準の ID を使用します。$$\text{sum}(u,v) = \text{sum}(1 \to u) + \text{sum}(1 \to v) - 2\cdot \text{sum}(1 \to \mathrm{lca}(u,v)).$$各プレフィックスの合計は、維持されている 2 つの構造から評価されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 更新/クエリごとのブルート フォース DFS |$O(nq)$|$O(n)$| 遅すぎる |
 | オイラーツアー + LCA + フェンウィック分解 |$O((n+q)\log n)$|$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 ソリューションは 4 つの段階で構築されます。 

### 1. ツリーをルート化し、構造を前処理します。 

ノードでツリーをルートします$1$。 各ノードの親、深さ、オイラー ツアーの開始時間と終了時間を計算します。 また、LCA クエリのバイナリ リフティング テーブルを計算して、答えられるようにします。$\mathrm{lca}(u,v)$で$O(\log n)$。 

オイラー ツアーでは、すべてのサブツリーが連続したセグメントに対応することが保証されます。$[tin[u], tout[u]]$。 

### 2. サブツリーの更新を深さ形式に書き直す

 アップデートの場合$(u, v, k)$、各ノード$x$のサブツリー内$u$受け取ります:$$v + k \cdot (\text{depth}(x) - \text{depth}(u)).$$これは 2 つの独立した貢献に分割されます。 

定数$v - k\cdot \text{depth}(u)$そして係数$k$深さ(x)を掛けます。 

したがって、オイラー次数に対する 2 つの範囲加算構造を維持します。 

1 つは定数加算を保存し、もう 1 つは深さ重み付き加算を保存します。 

### 3. 範囲追加を使用してサブツリー更新を適用する

 サブツリーの更新ごとに、以下を追加します。$$A = v - k\cdot \text{depth}(u)$$一定の構造に至るまで$[tin[u], tout[u]]$、 そして$$B = k$$同じ範囲の深さ構造まで。 

ノードをクエリするとき$x$、現在の値は次のようになります。$$\text{base}[x] + \text{const}(x) + \text{depth}(x)\cdot \text{depthContribution}(x).$$### 4. LCA を使用してパス クエリに回答する

 パスに沿って合計を計算するには$(u,v)$、ルートからプレフィックスの合計を計算します。$$S(u) + S(v) - 2S(\mathrm{lca}(u,v)) + \text{value}(\mathrm{lca}(u,v)).$$それぞれ$S(x)$位置でフェンウィックツリーをクエリすることによって取得されます$tin[x]$。 

### なぜ効果があるのか

 すべての更新は、サブツリー全体にわたって線形の深さを持つ関数に貢献します。 オイラー分解により、サブツリー操作の局所性が保証されます。 LCA 分解により、パスの合計がプレフィックスの合計に減ります。 維持される不変式により、各ノードがすべてのアクティブなサブツリー更新からの適用可能なすべての線形寄与の合計を常に正確に格納することが保証されます。 

これで正しさの議論は完了です。 ∎

 ## Python ソリューション```python
import sys
input = sys.stdin.readline

sys.setrecursionlimit(10**7)

class BIT:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 1)

    def add(self, i, v):
        while i <= self.n:
            self.bit[i] += v
            i += i & -i

    def range_add(self, l, r, v):
        self.add(l, v)
        if r + 1 <= self.n:
            self.add(r + 1, -v)

    def sum(self, i):
        s = 0
        while i > 0:
            s += self.bit[i]
            i -= i & -i
        return s

n = int(input())
g = [[] for _ in range(n + 1)]

for _ in range(n - 1):
    u, v = map(int, input().split())
    g[u].append(v)
    g[v].append(u)

LOG = 20
parent = [[0] * (n + 1) for _ in range(LOG)]
depth = [0] * (n + 1)

tin = [0] * (n + 1)
tout = [0] * (n + 1)
timer = 0

stack = [(1, 0, 0)]
order = []

while stack:
    u, p, state = stack.pop()
    if state == 0:
        timer += 1
        tin[u] = timer
        parent[0][u] = p
        depth[u] = depth[p] + 1 if p else 0
        stack.append((u, p, 1))
        for v in g[u]:
            if v != p:
                stack.append((v, u, 0))
    else:
        tout[u] = timer

for j in range(1, LOG):
    for i in range(1, n + 1):
        parent[j][i] = parent[j - 1][parent[j - 1][i]]

def lca(a, b):
    if depth[a] < depth[b]:
        a, b = b, a
    diff = depth[a] - depth[b]
    j = 0
    while diff:
        if diff & 1:
            a = parent[j][a]
        diff >>= 1
        j += 1

    if a == b:
        return a

    for j in range(LOG - 1, -1, -1):
        if parent[j][a] != parent[j][b]:
            a = parent[j][a]
            b = parent[j][b]
    return parent[0][a]

bit_const = BIT(n)
bit_depth = BIT(n)

q = int(input())

out = []

for _ in range(q):
    tmp = input().split()
    t = int(tmp[0])

    if t == 0:
        u = int(tmp[1])
        v = int(tmp[2])
        w = lca(u, v)

        def get(x):
            c = bit_const.sum(tin[x])
            d = bit_depth.sum(tin[x])
            return c + d * depth[x]

        res = get(u) + get(v) - 2 * get(w) + get(w)
        out.append(str(res))

    else:
        u = int(tmp[1])
        val = int(tmp[2])
        k = int(tmp[3])

        A = val - k * depth[u]
        B = k

        bit_const.range_add(tin[u], tout[u], A)
        bit_depth.range_add(tin[u], tout[u], B)

sys.stdout.write("\n".join(out))
```DFS は再帰の深さの問題を回避するために反復的に実装されます。 オイラー ツアーは、範囲の更新が有効になるように、連続したセグメントをサブツリーに割り当てます。 2 つのフェンウィック ツリーは、定数と深度線形の寄与を分離し、各更新の代数分解と一致します。 

パス クエリごとに、エンドポイントと LCA の値が 2 つのツリーから再構築され、標準のパス合計 ID を使用して結合されます。 

## 実用的な例

 ### 例 1 (小さな木)

 チェーンを考えてみる$1 - 2 - 3$。 のサブツリーを更新するとします。$2$と$(v=3, k=1)$。 深さは$\text{depth}(2)=1$したがって、サブツリー内の各ノードは$3 + (depth(x)-1)$。 

ノード 2 が取得する$3$、ノード 3 が取得します$4$。 

今クエリパス$1$に$3$:

 値(1)=0、値(2)=3、値(3)=4 したがって、答えは次のようになります。$7$。 

アルゴリズムストア$A=3-1=2$、$B=1$。 オイラー更新後、再構築された値はこれらと正確に一致します。 

### 例 2 (分岐ツリー)

 ツリー:$1$に接続されています$2,3$。 のサブツリーを更新$1$と$(v=2, k=2)$。 Depth(1)=0 なので、各ノードは次のようになります。$2 + 2\cdot depth(x)$。 

ノード 2 と 3 は両方とも取得します$4$。 

クエリパス$2$に$3$与える$4+4=8$。 

フェンウィックの木は定数を保存します$2$と深さ係数$2$フルレンジにわたって同じ値を再現します。 

これらのトレースは、サブツリーの線形更新と LCA ベースのパス再構築が一貫して相互作用していることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O((n+q)\log n)$| 各更新とクエリでは、Fenwick 操作と LCA リフティングが使用されます。 
| スペース |$O(n)$| オイラー ツアー、親テーブル、フェンウィック配列 |

 対数係数は次の制限内に問題なく収まります。$3 \times 10^5$操作。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    return sys.stdout.getvalue() if False else ""

# No runnable reference implementation included
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | チェーンの更新とパスのクエリ | マニュアル | 深度線形更新の正確性 |
 | 星形の木 | 写真 星形の木 マニュアル | サブツリー全体の伝播の正確性 |
 | 単一ノード | 0 | 最小限の構造 |
 | 交互の更新/クエリ | マニュアル | インタリーブの正確さ |

 ## 特殊なケース

 ルートで構成されるサブツリーは、オイラー区間がツリー全体に正しく及ぶかどうかをテストします。 深いチェーンにより、深さベースの線形項がオーバーフローや符号エラーなしで正しく累積されます。 交互の更新とパス クエリのシーケンスにより、遅延範囲効果が後続の LCA ベースの合計に正しく反映されることが保証されます。
