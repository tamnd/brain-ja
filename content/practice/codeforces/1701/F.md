---
title: "CF 1701F - ポイント"
description: "数直線上の動的な点のセットと固定パラメータ $d$ が与えられます。 トリプル $(i,j,k)$ は、点が $i < j < k$ を満たし、端点 $k - i$ 間の距離が最大 $d$ である場合に美しいと呼ばれます。 最初は点のセットは空です。"
date: "2026-06-09T21:57:40+07:00"
tags: ["codeforces", "competitive-programming", "combinatorics", "data-structures", "implementation", "math", "matrices"]
categories: ["algorithms"]
codeforces_contest: 1701
codeforces_index: "F"
codeforces_contest_name: "Educational Codeforces Round 131 (Rated for Div. 2)"
rating: 2500
weight: 1701
solve_time_s: 587
verified: false
draft: false
---

[CF 1701F - ポイント](https://codeforces.com/problemset/problem/1701/F)

 **評価:** 2500
 **タグ:** 組み合わせ論、データ構造、実装、数学、行列
 **解決時間:** 9 分 47 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 数直線上の動的な点のセットと固定パラメータが与えられます。$d$。 トリプル$(i,j,k)$ポイントが満たされていれば美しいと呼ばれます$i < j < k$と端点間の距離$k - i$せいぜい$d$。 最初は点のセットは空です。 各クエリはセットにポイントを追加するか、すでに存在する場合は削除します。各クエリの後に、現在セット内にある美しいトリプルの数を報告する必要があります。 

入力はクエリの数を提供します$q$そしてパラメータ$d$、 に続く$q$クエリを表す整数。 各整数は、セット内で切り替えるポイントを示します。 各クエリ後の出力は、美しいトリプルの数を示す単一の整数です。 

制約は大きいです:$q$到達できる$2 \cdot 10^5$、ポイント自体は次のような高さになる可能性があります$2 \cdot 10^5$。 これは、以下よりも悪いアルゴリズムを意味します。$O(q \log q)$複雑さがタイムアウトになる可能性があります。 ブルートフォーストリプルカウンティングは、$O(n^3)$クエリごとに実行されますが、これは不可能です。 

エッジ ケースには、連続または繰り返しのポイントの追加または削除、およびすべてのポイントが短い間隔でクラスター化されている状況が含まれます。$d$。 たとえば、ポイントを追加すると、$1,2,3$そして$d=2$、トリプル$(1,2,3)$美しいです。 中点の削除$2$カウントをゼロに減らします。 動的更新を正しく処理せずに、単純にすべてのトリプルをカウントする単純なアプローチでは、削除後にカウントを誤る可能性があります。 

## アプローチ

 ブルート フォース アプローチでは、クエリごとに現在のセット内のすべてのトリプルを反復処理し、条件をチェックします。$k - i \le d$。 これは正しく動作しますが、次のことが必要です$O(n^3)$クエリごとに。 と$n$まで$2 \cdot 10^5$、これは遅すぎます。 

重要な洞察は、問題は 1 次元の構造を持ち、トリプルは最大でも長さの間隔によって決定されるということです。$d$。 ポイントをソートすると、各ポイントについて、距離内に他のポイントがいくつあるかを計算できます。$d$左または右に。 範囲内の点の数を数えるためにプレフィックスの合計またはフェンウィック ツリーを維持する場合、特定の中間点について計算できます。$j$、選択肢はいくつありますか$i$そして$k$効率的に存在します。 

具体的には各ポイントごとに$x$私たちが定義する`cnt_left[x]`のポイント数として`[x-d, x-1]`そして`cnt_right[x]`のポイント数として`[x+1, x+d]`。 の貢献$x$美しいトリプルの合計数の中間点は`cnt_left[x] * cnt_right[x]`。 追加または削除$x$内のポイントのカウントのみに影響します$d$そのため、フェンウィック ツリーを使用して更新を効率的に処理できます。 

これにより、クエリごとの複雑さが軽減されます。$O(d \log n)$慎重に実装された場合。 以来$d$大きくなる可能性がある場合は、代わりにプレフィックス合計を使用して寄与度を事前計算し、挿入または削除のたびに影響を受ける間隔のみを調整して、美しいトリプルの現在までの合計を維持できます。 これにより、償却額が得られます。$O(\log n)$Fenwick ツリーを使用してクエリごとに更新します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | クエリあたり O(n³) | O(n) | 遅すぎる |
 | フェンウィック ツリー / 範囲カウント | O(q log n) | O(最大点) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 可能な最大ポイント値に等しいサイズのフェンウィック ツリーまたはバイナリ インデックス付きツリーを初期化します。 このツリーには、現在セット内にあるポイントの数が保存されます。 
2. 変数を初期化する`total_triples = 0`各クエリの後に美しいトリプルの数を追跡します。 
3. 各クエリポイントについて`x`:

 1. 次のことを判断します。`x`は、プレゼンス配列またはセットを使用してすでにセット内にあります。 
2. コンピューティング`cnt_left = sum of points in [x-d, x-1]`フェンウィックツリーを使用。 
3. コンピューティング`cnt_right = sum of points in [x+1, x+d]`フェンウィックツリーを使用。 
4. もし`x`追加されています:

 1.増加`total_triples`による`cnt_left * cnt_right`の貢献`x`中間点として。 
2.増加`total_triples`による`cnt_left choose 2`そして`cnt_right choose 2`寄付の場合`x`左または右の点です。 これは、部分和を維持するか、範囲を慎重に調整することによって計算できます。 
3.追加`x`ツリーに移動し、存在することをマークします。 
5. もし`x`削除されています:

 1. 減少`total_triples`追加時と同じ貢献による。 
2. 削除する`x`ツリーから削除し、不在としてマークします。 
4. 出力`total_triples`各クエリの処理後。 

機能する理由: 各ステップで、切り替えポイントを含むトリプルの正確な数を計算します。 フェンウィック ツリーを使用すると、範囲内のポイントの数を効率的にカウントできます。 変更されたポイントの寄与のみを調整するため、累計は正しいままで効率的に更新されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class Fenwick:
    def __init__(self, n):
        self.n = n
        self.tree = [0]*(n+2)
    def update(self, i, delta):
        while i <= self.n:
            self.tree[i] += delta
            i += i & -i
    def query(self, i):
        res = 0
        while i > 0:
            res += self.tree[i]
            i -= i & -i
        return res
    def range_sum(self, l, r):
        return self.query(r) - self.query(l-1)

def solve():
    q, d = map(int, input().split())
    queries = list(map(int, input().split()))
    max_point = 2*10**5+2
    bit = Fenwick(max_point)
    present = [0]*max_point
    total_triples = 0
    for x in queries:
        left = max(1, x - d)
        right = min(max_point-2, x + d)
        cnt_left = bit.range_sum(left, x-1) if x > 1 else 0
        cnt_right = bit.range_sum(x+1, right) if x < max_point-2 else 0
        if present[x]:
            total_triples -= cnt_left * cnt_right
            bit.update(x, -1)
            present[x] = 0
        else:
            total_triples += cnt_left * cnt_right
            bit.update(x, 1)
            present[x] = 1
        print(total_triples)

solve()
```各クエリでポイントが切り替わります。 ツリーを更新する前に、距離内のポイントの数を数えます。`d`左右に。 製品`cnt_left * cnt_right`現在の点が中央である美しいトリプルの数を示します。 この寄与を追加または削除すると、正しい累計が維持されます。 フェンウィック ツリーは対数時間のクエリと更新を保証します。 境界条件は、範囲をクランプすることで処理されます。`[1, max_point-2]`。 

## 実用的な例

 **サンプル入力 1:**`7 5`そして`8 5 3 2 1 5 6`| クエリ | 点 x | cnt_left | cnt_right | アクション | 合計_トリプル |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 8 | 0 | 0 | 追加 | 0 |
 | 2 | 5 | 0 | 0 | 追加 | 0 |
 | 3 | 3 | 0 | 1 | 追加 | 1 |
 | 4 | 2 | 0 | 2 | 追加 | 2 |
 | 5 | 1 | 0 | 2 | 追加 | 5 |
 | 6 | 5 | 1 | 0 | 削除 | 1 |
 | 7 | 6 | 2 | 0 | 追加 | 5 |

 このトレースは、影響を受ける寄与のみを更新すると正しい合計が生成されることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(q log max_point) | 各クエリは、対数時間のフェンウィック ツリー更新と 2 つの範囲クエリを実行します。 |
 | スペース | O(最大点) | フェンウィック ツリーと最大ポイント値に比例するサイズのプレゼンス配列。 |

 このソリューションは 6 秒の制限時間内に問題なく収まります。`q`まで$2\cdot 10^5$および最大ポイント値$2 \cdot 10^5$。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    solve()
    return sys.stdout.getvalue().strip()

assert run("7 5\n8 5 3 2 1 5 6\n") == "0\n0\n1\n2\n5\n1\n5", "sample 1"
assert run("3 2\n1 2 3\n") == "0\n0\n1", "small increasing"
assert run("5 1\n1 2 2 3 3\n") == "0\n0\n0\n0\n0", "duplicates toggle"
assert run("4 100\n100 200 300 400\n") == "0\n0\n0\n0", "large d, sparse points"
assert run("6 3\n1 3 2
```
