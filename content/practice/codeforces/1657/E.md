---
title: "CF 1657E - スター MST"
description: "n 個のラベル付き頂点に関する完全なグラフができました。 すべてのエッジは 1 から k までの整数の重みを受け取ります。 頂点 1 は特別な役割を果たします。 v 1 のすべてのエッジ (1, v) で構成される、頂点 1 を中心とする星形について考えます。"
date: "2026-06-10T03:29:55+07:00"
tags: ["codeforces", "competitive-programming", "combinatorics", "dp", "graph-matchings", "math"]
categories: ["algorithms"]
codeforces_contest: 1657
codeforces_index: "E"
codeforces_contest_name: "Educational Codeforces Round 125 (Rated for Div. 2)"
rating: 2200
weight: 1657
solve_time_s: 124
verified: true
draft: false
---

[CF 1657E - スター MST](https://codeforces.com/problemset/problem/1657/E)

 **評価:** 2200
 **タグ:** 組み合わせ論、dp、グラフマッチング、数学
 **解決時間:** 2 分 4 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 完全なグラフがあります`n`ラベル付きの頂点。 すべてのエッジは、次の間の整数の重みを受け取ります。`1`そして`k`。 

頂点`1` plays a special role. Consider the star centered at vertex `1`、すべてのエッジで構成されます`(1, v)`のために`v > 1`。 この星の総重量はこれらの合計です。`n - 1` edge weights.

A graph is called beautiful when the weight of its minimum spanning tree is exactly equal to the weight of that star.

The task is to count how many complete weighted graphs satisfy this condition, modulo `998244353`.

The constraints are the first hint that the solution must be heavily combinatorial. A complete graph on 250 vertices contains more than 31,000 edges. Even writing down all edge assignments is impossible. The number of possible weighted graphs is

$$k^{\binom n2},$$

which is astronomically large.

Since both `n` and `k` are at most `250`, a dynamic programming solution with roughly `O(n^2 k)` or `O(n^3)` states is realistic. Anything that tries to reason about individual graphs or MST computations directly is completely out of reach.

The tricky part is understanding what the MST condition actually means.

Consider three vertices `1, u, v`。 仮定する$$w(u,v) < \max(w(1,u), w(1,v)).$$次に、星型では、2 つの端のうち重い方になります。`(1,u)`そして`(1,v)`で置き換えることができます`(u,v)`, producing a spanning tree with strictly smaller weight. That would make the star non-optimal.

For example, if

$$w(1,u)=5,\quad w(1,v)=3,\quad w(u,v)=2,$$スターには貢献がある`5 + 3`これら 2 つの頂点からですが、エッジを置き換えます`(1,u)`による`(u,v)`総重量を減らす`3`。 

不注意なアプローチでは、星自体が MST であるかどうかを確認するだけになる可能性があります。 実際の条件はさらに強力です。MST の重みはスターの重みと等しくなければなりません。 スターはすでにスパニング ツリーであるため、等しいということは、スターが最小のスパニング ツリーでなければならないことを意味します。 

役に立つ小さな例は次のとおりです。`n = 3`、`k = 2`。 

させて$$a=w(1,2),\quad b=w(1,3),\quad c=w(2,3).$$スターはまさにそのときMSTです$$c \ge \max(a,b).$$有効な割り当ては次のとおりです。 

| | b | 有効な c |
 | --- | --- | --- |
 | 1 | 1 | 1,2 |
 | 1 | 2 | 2 |
 | 2 | 1 | 2 |
 | 2 | 2 | 2 |

 合計 = 5、サンプル出力と一致します。 

## アプローチ

 ブルートフォースのアイデアは単純です。 すべてのエッジに重みを割り当て、MST アルゴリズムを実行し、スターの重みを計算して、2 つの値を比較します。 

問題は課題の数です。 完全なグラフには以下が含まれます$$\binom n2$$エッジなので、重み付きグラフの数は次のようになります。$$k^{\binom n2}.$$のために`n = 250`、これはまったく不可能です。 

重要な観察は、MST 条件がすべての非スター エッジに対するローカル制約として書き換えられるということです。 

させて$$a_i = w(1,i).$$これらはスターエッジの重みです。 

非ルート頂点間のすべてのエッジが次の条件を満たす場合に限り、スターは MST です。$$w(i,j) \ge \max(a_i,a_j).$$なぜ？ 

一部のエッジがその最大値よりも小さい場合は、重いスター エッジを置き換えて、より軽いスパニング ツリーを取得できます。 

逆に、すべての非スターエッジが少なくともその最大値である場合、すべてのエッジに対して`(i,j)`スター内の固有のパスには重みが含まれています`a_i`そして`a_j`、そのパス上の最大の重みは正確に`max(a_i,a_j)`。 エッジ`(i,j)`パス上の最大のエッジよりも安くなることはありません。これはまさに、サイクル プロパティによって与えられる MST の特性です。 スターは MST になります。 

ここで、問題は値の代入を数えるということになります。`a_i`。 

星の重みが固定されると、すべてのエッジが`(i,j)`もっている$$k-\max(a_i,a_j)+1$$有効な選択肢。 

したがって、答えは次のようになります$$\sum_{a_2,\dots,a_n} \prod_{i<j} \bigl(k-\max(a_i,a_j)+1\bigr).$$直接評価することはまだ不可能です。`k^(n-1)`スターの割り当て。 

残りの洞察は、各星の重みを持つ頂点の数のみが重要であるということです。 

以下の重みを処理します`1`に`k`そして、スターエッジの重みがすでに決定されている頂点のセットを構築します。 これは、ウェイト レベルと頂点数に対する DP につながります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(k^{\binom n2})$| 巨大な | 遅すぎる |
 | 最適なDP |$O(k n^2)$|$O(kn)$| 承認済み |

 ## アルゴリズムのチュートリアル

 しましょう`N = n - 1`。 頂点に星の重みを割り当てるだけで済みます。`2...n`。 

定義する：$$dp[w][t]$$重量値を処理した後のウェイの数として`1...w`、正確にはどこに`t`頂点にはすでにスター ウェイトが最大で与えられています`w`。 

当初は、$$dp[0][0]=1.$$重量を処理しているとします。`w`。 

仮定する`t`頂点にはすでに小さい重みが割り当てられています。 

選ぶ`x`星の重みが正確になる新しい頂点`w`。 

新しいカウントは次のようになります$$t+x.$$### エッジ寄与のカウント

 新しく追加されたすべての頂点には正確なスター ウェイトが設定されています`w`。 

スターの最大重みが等しいペアを考慮します。`w`。 

次の 2 種類があります。 

1. 古いエンドポイントの中の 1 つのエンドポイント`t`頂点とそのうちの 1 つ`x`新しい頂点。 

カウント：$$t \cdot x.$$1. 両方のエンドポイント`x`新しい頂点。 

カウント：$$\frac{x(x-1)}2.$$合計：$$E=tx+\frac{x(x-1)}2.$$このようなすべてのペアについて、対応するエッジ`(i,j)`任意の値を取ることができます$$[w,k].$$したがって、各ペアが貢献します$$k-w+1$$選択肢。 

寄付総額は、$$(k-w+1)^E.$$### どの頂点が重みを受けるかを数える`w`残った中では`N-t`頂点、選択`x`そのうち:$$\binom{N-t}{x}.$$したがって、移行は次のようになります$$dp[w][t+x] += dp[w-1][t] \cdot \binom{N-t}{x} \cdot (k-w+1)^E.$$すべてのウェイト値を処理した後、すべての頂点が割り当てられている必要があります。$$\text{answer}=dp[k][N].$$### なぜ効果があるのか

 DP は、スターエッジ重みのマルチセットをレベルごとに構築します。 

体重のとき`w`より大きなエンドポイントの重みが正確に一致するすべての頂点ペアが処理されます。`w`完全に決意するようになる。 このようなペアはすべて独立して正確に寄与します`k-w+1`非スターエッジの選択肢。 

各ペアは 1 回、つまり、より大きなエンドポイントの重みが最初に出現したときにカウントされます。 指数$$tx+\frac{x(x-1)}2$$スターの最大重みが等しいペアの正確な数です`w`。 

これらすべての独立した寄与を乗算すると、すべての美しいグラフが 1 回だけカウントされ、カウントされたすべてのグラフは上記の MST 特性を満たします。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 998244353

def main():
    n, k = map(int, input().split())

    N = n - 1

    C = [[0] * (N + 1) for _ in range(N + 1)]
    C[0][0] = 1
    for i in range(1, N + 1):
        C[i][0] = C[i][i] = 1
        for j in range(1, i):
            C[i][j] = (C[i - 1][j - 1] + C[i - 1][j]) % MOD

    max_pairs = N * (N - 1) // 2

    pw = [[1] * (max_pairs + 1) for _ in range(k + 1)]
    for w in range(1, k + 1):
        base = k - w + 1
        for e in range(1, max_pairs + 1):
            pw[w][e] = pw[w][e - 1] * base % MOD

    dp = [[0] * (N + 1) for _ in range(k + 1)]
    dp[0][0] = 1

    for w in range(1, k + 1):
        for t in range(N + 1):
            cur = dp[w - 1][t]
            if cur == 0:
                continue

            rem = N - t

            for x in range(rem + 1):
                nt = t + x

                pairs = t * x + x * (x - 1) // 2

                ways = cur
                ways = ways * C[rem][x] % MOD
                ways = ways * pw[w][pairs] % MOD

                dp[w][nt] = (dp[w][nt] + ways) % MOD

    print(dp[k][N])

if __name__ == "__main__":
    main()
```組み合わせテーブルは、どの頂点が特定の星の重みを受け取るかをカウントします。 以来`N ≤ 249`、単純なパスカル三角形で十分です。 

遷移指数$$t x + \frac{x(x-1)}2$$がソリューションの核心です。 これは、割り当てられた最大スター ウェイトが現在のウェイトとなる頂点ペアを正確にカウントします。 

同じ塩基が何度も出現するため、検出力の値は事前に計算されます。 この前処理がないと、DP 内で累乗剰余演算が繰り返されるため、不必要にコストがかかることになります。 

国が使用しているのは、`N = n - 1`ルート以外の頂点のみにスターウェイトの割り当てが必要になるためです。 このわずかな削減により、フォーミュラはよりクリーンな状態に保たれます。 

## 実用的な例

 ### サンプル 1

 入力:```
3 2
```ここ`N = 2`。 

| 重量 w | 前 | x が選択されました | nt | ペア | 係数 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 0 | 0 | 0 | 0 | 1 |
 | 1 | 0 | 1 | 1 | 0 | 2 |
 | 1 | 0 | 2 | 2 | 1 | 2 |
 | 2 | DP を続ける | | | | |

 すべてのトランジションが完了したら、次のようにします。 

| 状態 | 値 |
 | --- | --- |
 | dp[2][2] | 5 |

 答え：```
5
```この例は、DP がスター重み割り当てと有効な非スター エッジ割り当てを同時にカウントしていることを示しています。 

### 例 2

 入力:```
2 3
```スターエッジは 1 つだけです。 

| 重量 w | 前 | × | nt |
 | --- | --- | --- | --- |
 | 1 | 0 | 1 | 1 |
 | 2 | 0 | 1 | 1 |
 | 3 | 0 | 1 | 1 |

 スター状でないエッジは存在しないため、単一エッジの重みの選択はすべて有効です。 

| 最終状態 | 値 |
 | --- | --- |
 | dp[3][1] | 3 |

 答え：```
3
```これにより、数式が最小のグラフを正しく処理していることが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(kn^2)$| 重みごとに、現在割り当てられているカウントと新しく追加されたカウントを繰り返します。 
| スペース |$O(kn)$| DP テーブルと組み合わせ事前計算 |

 と`n, k ≤ 250`、DP 遷移の数はおよそ$$250 \times 250 \times 250 \approx 1.6\times10^7,$$これは、最適化された Python の 6 秒制限内に快適に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    MOD = 998244353

    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline

    n, k = map(int, input().split())
    N = n - 1

    C = [[0] * (N + 1) for _ in range(N + 1)]
    C[0][0] = 1
    for i in range(1, N + 1):
        C[i][0] = C[i][i] = 1
        for j in range(1, i):
            C[i][j] = (C[i - 1][j - 1] + C[i - 1][j]) % MOD

    max_pairs = N * (N - 1) // 2

    pw = [[1] * (max_pairs + 1) for _ in range(k + 1)]
    for w in range(1, k + 1):
        base = k - w + 1
        for e in range(1, max_pairs + 1):
            pw[w][e] = pw[w][e - 1] * base % MOD

    dp = [[0] * (N + 1) for _ in range(k + 1)]
    dp[0][0] = 1

    for w in range(1, k + 1):
        for t in range(N + 1):
            cur = dp[w - 1][t]
            if cur == 0:
                continue

            rem = N - t
            for x in range(rem + 1):
                pairs = t * x + x * (x - 1) // 2
                dp[w][t + x] = (
                    dp[w][t + x]
                    + cur * C[rem][x] * pw[w][pairs]
                ) % MOD

    return str(dp[k][N])

# provided sample
assert run("3 2\n") == "5"

# minimum graph
assert run("2 1\n") == "1"

# one edge, three possible weights
assert run("2 3\n") == "3"

# official sample
assert run("4 4\n") == "571"

# larger official sample
assert run("6 9\n") == "310640163"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 1`|`1`| 可能な最小のグラフ |
 |`2 3`|`3`| 星状でないエッジは存在しません |
 |`3 2`|`5`| 基本的な MST 制約のカウント |
 |`4 4`|`571`| 公式サンプル |
 |`6 9`|`310640163`| 大きな公式サンプル |

 ## 特殊なケース

 次のことを考慮してください。```
2 1
```グラフにはエッジが 1 つだけあります。 スターはグラフ全体であり、固有のスパニング ツリーでもあります。 DPは、`N = 1`、唯一の頂点をウェイトに割り当てます`1`、そして戻ります`1`。 

考慮する：```
2 5
```ここでも、非スターエッジはありません。 からの任意の重量`1`に`5`動作します。 トランジションでは、単独の頂点に対して 5 つのレベルの 1 つを選択するだけで、答えが生成されます。`5`。 

考慮する：```
3 2
```危険な構成は$$w(1,2)=2,\quad w(1,3)=1,\quad w(2,3)=1.$$すべての重みが範囲内にあるため、単純なカウント方法では受け入れられる可能性があります。 私たちの特徴付けはそれを拒否します。$$1 < \max(2,1).$$スターの最大重みが次のエッジに等しいため、DP はこのグラフをカウントしません。`2`からの値のみが許可されます`[2,2]`。 

考慮する：```
3 1
```すべてのエッジの重みは強制的に`1`。 星には重さがある`2`、すべてのスパニング ツリーには重みがあります。`2`、その答えはまさに`1`。 DP はこれを自然に処理します。なぜなら、すべての乗算係数は`1`。
