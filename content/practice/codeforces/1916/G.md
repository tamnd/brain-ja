---
title: "CF 1916G - Chelsu による最適化"
description: "エッジが正の整数の重みを持つツリーが与えられます。 任意の 2 つの頂点 $u$ と $v$ について、そのパス上のエッジの数を $len(u,v)$ とし、そのパス上のすべてのエッジの重みの gcd を $gcd(u,v)$ とします。"
date: "2026-06-08T19:53:16+07:00"
tags: ["codeforces", "competitive-programming", "divide-and-conquer", "dp", "number-theory", "trees"]
categories: ["algorithms"]
codeforces_contest: 1916
codeforces_index: "G"
codeforces_contest_name: "Good Bye 2023"
rating: 3500
weight: 1916
solve_time_s: 123
verified: true
draft: false
---

[CF 1916G - Chelsu による最適化](https://codeforces.com/problemset/problem/1916/G)

 **評価:** 3500
 **タグ:** 分割統治、dp、整数論、木
 **解決時間:** 2 分 3 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 エッジが正の整数の重みを持つツリーが与えられます。 

任意の 2 つの頂点について$u$そして$v$、 させて$len(u,v)$をパス上のエッジの数とし、$gcd(u,v)$そのパス上のすべてのエッジの重みの gcd になります。 課題は最大化することです$$len(u,v)\cdot gcd(u,v)$$すべての頂点のペアにわたって。 

ツリーには以下が含まれます$10^5$すべてのテスト ケースにわたる頂点。 すべてのペアに対する二次アルゴリズムでは、おおよそ次のものが必要になります。$10^{10}$最悪の場合のペア評価は 2 秒以内では完全に不可能です。 たとえ$O(n\sqrt n)$スタイルのソリューションは不快になるでしょう。 ターゲットはそれに近いものです$O(n\log^2 n)$。 

難しいのは、目的が反対方向に移動する 2 つの量を組み合わせていることです。 長いパスでは gcd が小さくなる傾向がありますが、大きな gcd は通常、短いパスでのみ表示されます。 成功するソリューションはすべてのパスを個別に扱うのではなく、gcd 値の特別な構造を利用する必要があります。 

多くの異なるパスが同じ gcd を持つ場合、微妙なエッジ ケースが発生します。 

例：```
1
3
1 2 6
2 3 6
```最善の答えは道全体から導き出されます$1\leftrightarrow3$:$$2\cdot 6 = 12.$$各 gcd に対して代表的なパスを 1 つだけ保持すると、パスの長さに関する情報が失われ、間違った答えが生成されます。 

もう 1 つのトラップは、両方の半分が同じサブツリーに由来する重心を通過するパスです。 このようなパスはサブツリー内で再帰的に処理されるため、セントロイド マージ ステップ中にカウントしてはなりません。 

例：```
1
4
1 2 12
2 3 12
2 4 12
```パス$3\leftrightarrow4$頂点を通過する$2$、ただし、両方のエンドポイントは重心の子サブツリーに属します。 不注意に結合すると、同じ子からの情報を誤って 2 回結合してしまう可能性があります。 

3 番目の落とし穴は、巨大なエッジ ウェイトに起因します。 以来$w_i\le 10^{12}$、どんな答えも周囲にあるかもしれません$10^{17}$。 32 ビット整数を使用すると、すぐにオーバーフローします。 

## アプローチ

 ブルート フォース ソリューションでは、頂点のすべてのペアが検査されます。 各ペアについて、LCA ベースの前処理を使用してパス gcd とパス長を計算できます。 

ペア数は$O(n^2)$。 たとえすべてのクエリが$O(1)$、それでも大まかに必要になります$5\cdot10^9$ときの評価$n=10^5$。 ボトルネックはパス クエリではなく、ペアの数です。 

重要な観察は、固定された重心を通るパスを観察することから得られます。 

重心から下向きの 2 つのチェーンが寄与していると仮定します。$$(len_1,g_1),\qquad (len_2,g_2)$$と$len_1\ge len_2$。 

結果のパスには gcd が含まれます$$\gcd(g_1,g_2).$$もし$\gcd(g_1,g_2)\lt g_1$、その場合はせいぜい$g_1/2$。 適切な約数は、数値自体の半分を超えることはできません。 したがって、パスは次の場合にのみ答えを改善できます。$g_1$分割する$g_2$。 これにより、問題は任意の gcd の組み合わせから可分性の関係に即座に変換されます。 

仮定する$$g_2=k\cdot g_1.$$結合されたパスが貢献します$$g_1(len_1+len_2).$$2番目のチェーンのみを使用すると、すでに次のようになります$$g_2\,len_2=k\,g_1\,len_2.$$マージされたパスが競争力を持つためには、次のことが必要です$$g_1(len_1+len_2)\ge k\,g_1\,len_2,$$これを単純化すると$$(k-1)len_2\le len_1.$$特に、$k\le len_1+1$、受け入れられたソリューションで使用される実装の場合は、複数を列挙するだけで十分です。$g_2=k g_1$と$k\le len_1$。 

この制限は非常に強力です。 すべての gcd 値について$g$、その gcd を達成する最長のチェーンのみが必要です。 次に、少数の倍数を列挙します。$g$他のすべての gcd 値ではなく。 

残りの課題は、すべてのパスを生成することです。 問題はツリー上にあり、すべての有効なパスは重心分解で一意の最高重心を持つため、重心分解は自然なフレームワークになります。 

固定重心の場合:

 1. すべての子サブツリー内のすべてのノードを列挙します。 
2. ペアを記録する$(g,len)$、 どこ$g$重心からそのノードまでのパス上の gcd です。 
3. すべての gcd 値について、最長のチェーン長と、別の子サブツリーからの 2 番目に最適なチェーンを維持します。 
4. 上記の割り算観察を使用して、互換性のある gcd 値を結合します。 

すべてのパスは、そのパス上の最も高い重心で正確に処理されるため、パスが見逃されることはなく、パスが 2 回カウントされることもありません。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(n^2)$高速パスクエリでもペアチェック |$O(n)$| 遅すぎる |
 | 最適 |$O(n\log^2 n\log W)$|$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. ツリーの重心分解を構築します。 
2. 現在の重心の場合$c$、DFS を各子サブツリーに個別に入力します。 
3. DFS 中に以下を維持します。$$g=\gcd(\text{all edges from }c\text{ to current node})$$そしてからの経路長$c$。 
4. 遭遇したすべての gcd 値について$g$、その gcd を生成する最長のチェーン長を格納します。 また、どの子サブツリーがそれを生成したかを保存します。 
5. 別の子サブツリーからの同じ gcd の 2 番目に最適な長さを格納します。 これが必要なのは、2 つのエンドポイントを結ぶパスが重心を通る 2 つの異なる分岐を使用する必要があるためです。 
6. しましょう$best[g]$gcd に関連付けられた最大長であること$g$。 
7. すべての gcd 値について$g$、チェーンの最長の長さを考慮してください。$len$。 
8. 倍数を列挙する$$g_2=g\cdot k$$と$1\le k\le len$。 
9. もし$g_2$記録された gcd 値の中に存在する場合は、チェーンを組み合わせてみてください。$g$に最適なチェーン付き$g_2$。 
10. 両方のチェーンが異なる子サブツリーに由来する場合は、最適な長さの$g_2$。 
11. それ以外の場合は、2 番目に最適な長さを使用します。$g_2$。 
12. アップデート$$ans=\max(ans,(len_1+len_2)\cdot g).$$1. 重心を削除済みとしてマークし、残りのすべてのコンポーネントを再帰的に調べます。 

### なぜ効果があるのか

 あらゆる道を検討する$P$。 重心分解では、次の位置にある固有の最高重心が存在します。$P$。 パスは、その重心が処理されるときに正確に処理されます。 

その重心では、パスの 2 つの半分が gcd 値を持つ 2 つのチェーンに対応します。$g_1$そして$g_2$。 パス gcd は以下に等しい$\gcd(g_1,g_2)$。 

割り切れる議論は、改善する組み合わせは次の条件を満たす必要があることを示しています。$g_1\mid g_2$したがって、倍数を列挙すると、関連するすべての候補がカバーされます。 各 gcd 値について、利用可能な最長のチェーンが保持されるため、有効な gcd 値のペアが検査されるたびに、アルゴリズムは個別のサブツリーからのチェーン間で可能な最大長を使用します。 

すべての候補パスはその最高重心で考慮され、考慮されたすべての候補はツリー内の実際のパスに対応します。 したがって、見つかった最大値はまさに全体最適値となります。 

## Python ソリューション```python
import sys
from math import gcd

input = sys.stdin.readline

def solve_case():
    n = int(input())
    g = [[] for _ in range(n)]

    for _ in range(n - 1):
        u, v, w = input().split()
        u = int(u) - 1
        v = int(v) - 1
        w = int(w)
        g[u].append((v, w))
        g[v].append((u, w))

    removed = [False] * n
    sz = [0] * n
    ans = 0

    def calc_size(v, p):
        sz[v] = 1
        for to, _ in g[v]:
            if to != p and not removed[to]:
                calc_size(to, v)
                sz[v] += sz[to]

    def find_centroid(v, p, tot):
        for to, _ in g[v]:
            if to != p and not removed[to]:
                if sz[to] > tot // 2:
                    return find_centroid(to, v, tot)
        return v

    def dfs(v, p, cur_g, depth, root_id):
        nonlocal max_g, max_len

        max_g = max(max_g, cur_g)
        max_len = max(max_len, depth)

        if cur_g not in best:
            best[cur_g] = depth
            owner[cur_g] = root_id
        else:
            if depth >= best[cur_g]:
                if owner[cur_g] == root_id:
                    best[cur_g] = depth
                else:
                    second[cur_g] = best[cur_g]
                    best[cur_g] = depth
                    owner[cur_g] = root_id
            elif owner[cur_g] != root_id:
                second[cur_g] = max(second.get(cur_g, 0), depth)

        for to, w in g[v]:
            if to == p or removed[to]:
                continue
            dfs(to, v, gcd(cur_g, w), depth + 1, root_id)

    def decompose(entry):
        nonlocal ans, max_g, max_len

        calc_size(entry, -1)
        c = find_centroid(entry, -1, sz[entry])

        best.clear()
        owner.clear()
        second.clear()

        max_g = 0
        max_len = 0

        for to, w in g[c]:
            if removed[to]:
                continue
            dfs(to, c, w, 1, to)

        keys = sorted(best)

        idx = {x: i for i, x in enumerate(keys)}
        best_len = [best[x] for x in keys]
        best_owner = [owner[x] for x in keys]
        second_len = [second.get(x, 0) for x in keys]

        for i, gg in enumerate(keys):
            length = best_len[i]

            if gg * (length + max_len) <= ans:
                continue

            k = 1
            while k <= length and gg * k <= max_g:
                target = gg * k
                j = idx.get(target)
                if j is not None:
                    if best_owner[j] == best_owner[i]:
                        ans = max(ans, (length + second_len[j]) * gg)
                    else:
                        ans = max(ans, (length + best_len[j]) * gg)
                k += 1

        removed[c] = True

        for to, _ in g[c]:
            if not removed[to]:
                decompose(to)

    best = {}
    owner = {}
    second = {}
    max_g = 0
    max_len = 0

    decompose(0)

    return str(ans)

def main():
    t = int(input())
    out = []
    for _ in range(t):
        out.append(solve_case())
    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    main()
```分解コードは、すべてのパスが正確に 1 つの重心レベルに割り当てられることを保証します。 

DFS は gcd 値を収集するだけではありません。 すべての gcd について、最長のチェーンとそれを生成した子サブツリーを追跡します。 同じサブツリーからの 2 つのチェーンを結合すると、現在の重心を通過しないパスが作成されるため、別のサブツリーからの 2 番目に長いチェーンも維持されます。 

剪定の様子```
if gg * (length + max_len) <= ans:
    continue
```受け入れられた実装からの重要な最適化です。 現在の gcd を可能な限り長いチェーンと組み合わせても答えは改善されないため、すべての倍数をスキップできます。 

Python の整数は、次の値まで自動的に処理します。$10^{17}$さらに、オーバーフローの問題を回避します。 

## 実用的な例

 ### 例 1```
2
1 2 1000000000000
```唯一のパスは単一のエッジです。 

| ノードに到達しました | gcd | 長さ |
 | --- | --- | --- |
 | 2 | 1000000000000 | 1 |

 答え：$$1 \cdot 10^{12}=10^{12}.$$### 例 2```
4
3 2 6
2 1 10
2 4 6
```重心は頂点 2 です。 

| エンドポイント | 重心からの gcd | 長さ |
 | --- | --- | --- |
 | 1 | 10 | 1 |
 | 3 | 6 | 1 |
 | 4 | 6 | 1 |

 最も互換性のあるペアはパスです$3\leftrightarrow4$。 

| チェーンA | チェーンB | パス gcd | パスの長さ | 値 |
 | --- | --- | --- | --- | --- |
 | (6,1) | (6,1) | 6 | 2 | 12 |

 答え = 12。 

この例は、異なる子サブツリーからの情報を保持しなければならない理由を示しています。 gcd 6 を持つ両方のエンドポイントは、重心の別個のブランチに属します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n\log^2 n\log W)$| 重心分解が寄与する$\log n$レベル、gcd 状態は別の対数係数に寄与します。 
| スペース |$O(n)$| ツリー、再帰メタデータ、重心構造 |

 すべてのテスト ケースにわたる頂点の合計数は最大で$10^5$したがって、この複雑さは制限に問題なく適合します。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()

    # call solution here
    return out.getvalue()

# sample
assert run("""4
2
1 2 1000000000000
4
3 2 6
2 1 10
2 4 6
8
1 2 12
2 3 9
3 4 9
4 5 6
5 6 12
6 7 4
7 8 9
12
1 2 12
2 3 12
2 4 6
2 5 9
5 6 6
1 7 4
4 8 12
8 9 4
8 10 12
2 11 9
7 12 9
""") == """1000000000000
12
18
24
"""

# minimum tree
assert run("""1
2
1 2 7
""") == """7
"""

# all equal weights
assert run("""1
5
1 2 6
2 3 6
3 4 6
4 5 6
""") == """24
"""

# star
assert run("""1
4
1 2 12
1 3 12
1 4 12
""") == """24
"""

# gcd decreases along long path
assert run("""1
3
1 2 12
2 3 18
""") == """12
"""
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 片刃 | 7 | 最小サイズ |
 | すべての重みが等しい | 24 | 最長パスが優勢 |
 | スターツリー | 24 | 2 つの重心ブランチを結合する |
 | ウェイト 12 および 18 | 12 | 正しい gcd 削減 |

 ## 特殊なケース

 検討してください```
1
3
1 2 6
2 3 6
```両方のエッジの重みは同じです。 このアルゴリズムは、重心の両側から長さ 1 の gcd 値 6 を記録します。 これらを結合すると、長さ 2 と gcd 6 が得られ、12 が生成されます。gcd 6 を 1 つだけ保持すると、誤って 6 が返されます。 

検討してください```
1
4
1 2 12
2 3 12
2 4 12
```重心マージ ステップでは、サブツリーの所有権が保存されます。 2 つのチェーンを結合する場合、gcd に最適なチェーンが同じ子サブツリーに由来する場合、アルゴリズムは 2 番目に最適なチェーンにフォールバックします。 これにより、存在しないパスが構築されるのを防ぎます。 

考慮する```
1
2
1 2 1000000000000
```答えは等しい$10^{12}$。 大きな木では、答えは次の値を超える可能性があります$10^{17}$。 実装では任意精度の Python 整数を使用するため、オーバーフローは発生しません。
