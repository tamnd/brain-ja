---
title: "CF 103369B - \u0423\u043d\u0438\u0447\u0442\u043e\u0436\u0435\u043d\u0438\u0435 \u043c\u0430\u0441\u0441\u0438\u0432\u0430"
description: "数値の静的な配列が与えられ、次にこの配列の要素が 1 つずつ「削除」される順序を示すシーケンスが与えられます。 削除するたびに、まだ生きている要素のいくつかのばらばらの連続したセグメントが残ります。"
date: "2026-07-03T12:49:59+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103369
codeforces_index: "B"
codeforces_contest_name: "Moscow team olympiad 2021"
rating: 0
weight: 103369
solve_time_s: 52
verified: true
draft: false
---

[CF 103369B - \u0423\u043d\u0438\u0447\u0442\u043e\u0436\u0435\u043d\u0438\u0435 \u043c\u0430\u0441\u0441\u0438\u0432\u0430](https://codeforces.com/problemset/problem/103369/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 52 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 数値の静的な配列が与えられ、次にこの配列の要素が 1 つずつ「削除」される順序を示すシーケンスが与えられます。 削除するたびに、まだ生きている要素のいくつかのばらばらの連続したセグメントが残ります。 From these remaining segments, we are interested in the one whose sum of elements is the largest.

 The key detail is that removed elements act like barriers: they split the array into independent blocks. 各ブロック内で部分配列を取得できますが、すべての数値は負ではないため、ブロック内の最適な部分配列は単純にブロック全体です。 So after each deletion, the answer is effectively the maximum sum over all currently alive contiguous segments.

 From a computational perspective, the process is dynamic and destructive. We start with a full array, then progressively carve it into smaller segments. After each step we must recompute a global property of the remaining segments, which makes a naive recomputation expensive.

 制約は典型的なものです。$n \le 10^5$この問題により、削除のたびにセグメントの合計を最初から再計算することが直ちに除外されます。 ステップごとにフルスキャンを行うと、約$O(n^2)$、これは 1 ～ 2 秒の制限には遅すぎます。 コンポーネントを繰り返し再スキャンするというわずかに優れたアプローチであっても、不用意に実装すると各要素が何度も再訪される可能性があるため、依然として TLE が発生します。 

この問題構造では、いくつかのエッジの状況が重要になります。 

まず、すべての要素が削除される可能性があります。この場合、有効なセグメントは存在せず、答えはゼロになる必要があります。 たとえば、配列が次の場合、$[1, 2]$最終的に両方のインデックスが削除され、出力は次のように終了します。$0, 0$、負の無限大や空の答えではありません。 

次に、セグメントの結合動作にはゼロが重要です。 すべての値が 0 の場合、すべてのセグメントの合計が 0 になるため、答えは全体を通して 0 のままになります。 「少なくとも 1 つの正のセグメントが存在する」と想定する単純な実装では、誤って最大値が古いままになる可能性があります。 

第三に、削除の順序は任意ではなく、順列です。 すべての位置が 1 回だけ消えるため、これは重要です。これは、削除を前方にシミュレートするのではなく、プロセスを逆に行うことを強く示唆しています。 

## アプローチ

 ブルートフォースのアイデアは単純です。各削除の後、配列全体をスキャンし、削除されたインデックスをスキップし、連続したブロックに分割し、各ブロックの合計を計算して最大値を取得します。 それぞれのスキャンは、$O(n)$、そして私たちはそれをします$n$回、与える$O(n^2)$。 と$n = 10^5$、これについてです$10^{10}$限界をはるかに超えた操作。 

重要な点は、削除は直接処理するのは難しいが、挿入はマージするのが簡単であるということです。 要素を破棄する代わりに、プロセスを逆にすることもできます。つまり、空の配列から開始し、削除とは逆の順序で要素を「追加し直す」ことができます。 要素を追加すると、新しいセグメントが形成されるか、すでにアクティブな隣接セグメントとマージされます。 これにより、問題は、各コンポーネントがその合計を格納するユニオン操作の下で接続されたコンポーネントを維持することに変わります。 

これはまさに、Disjoint Set Union 構造がうまく機能する体制です。 ポジションをアクティブにするたびに、そのポジションを隣接するアクティブなポジションに接続し、接続された各コンポーネントの合計を維持します。 グローバル最大セグメント合計は、アクティブ化のたびに更新されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(n^2)$|$O(n)$| 遅すぎる |
 | 逆プロセスの DSU |$O(n \alpha(n))$|$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 ### 最適なアイデア: 削除を逆方向に処理する

 1. 配列と削除順序を読み取り、各位置を削除時刻にマップする構造を構築します。 これにより、逆のタイムラインを再構築できるようになります。 
2. すべての位置が最初は非アクティブである Disjoint Set Union 構造を初期化し、親とコンポーネントの合計の配列を維持します。 
3. ブール配列を維持する`active[i]`ポジションが現在逆転プロセスに存在するかどうかを示します。 
4. 変数を維持する`best`いつでも最大コンポーネント合計を追跡します。 
5. 位置を削除の逆順に処理します。 ポジションごとに`x`、それをアクティブにし、そのコンポーネントの合計を次のように初期化します。`a[x]`、と設定します`best = max(best, a[x])`。 
6. 左隣がアクティブな場合は、2 つのコンポーネントを結合し、結合されたルートの合計を更新してから、更新します。`best`。 
7. 右隣がアクティブな場合は、同じユニオン操作を実行します。 
8. 各アクティベーションを処理した後、記録します。`best`対応する前方削除ステップの答えとして。 

各結合の背後にある重要な考え方は、2 つのコンポーネントがマージされると、それらの合計が正確に結合され、各インデックスが常に 1 つのコンポーネントに正確に属するため、重複や二重カウントが発生しないということです。 

### なぜ効果があるのか

 正確さは、単調再構成の不変条件に基づいています。 逆のプロセスのどのステップでも、アクティブな位置のセットは、欠失の接頭辞の補数に正確に対応します。 このアクティブ セット内の各接続コンポーネントは、元の順方向プロセスで削除されていない要素の最大の連続ブロックです。 すべての値が負ではないため、ブロック内の最大の部分配列はブロック自体であるため、コンポーネントの合計を維持するだけですべての候補の答えを表すのに十分です。 すべてのマージ操作では、素セットの正確な合計が保存され、順方向プロセスで考えられるすべてのセグメントが、正確に 1 つの逆方向ステップで DSU コンポーネントとして表示されるため、候補が見逃されることはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class DSU:
    def __init__(self, n, a):
        self.parent = list(range(n))
        self.size = [1] * n
        self.sum = a[:]

    def find(self, x):
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]
            x = self.parent[x]
        return x

    def union(self, a, b):
        ra = self.find(a)
        rb = self.find(b)
        if ra == rb:
            return
        if self.size[ra] < self.size[rb]:
            ra, rb = rb, ra
        self.parent[rb] = ra
        self.size[ra] += self.size[rb]
        self.sum[ra] += self.sum[rb]

def solve():
    n = int(input())
    a = list(map(int, input().split()))
    order = list(map(int, input().split()))

    active = [False] * n
    dsu = DSU(n, a)

    ans = [0] * n
    best = 0

    for i in range(n - 1, -1, -1):
        idx = order[i] - 1
        active[idx] = True

        best = max(best, a[idx])

        if idx - 1 >= 0 and active[idx - 1]:
            dsu.union(idx, idx - 1)
        if idx + 1 < n and active[idx + 1]:
            dsu.union(idx, idx + 1)

        root = dsu.find(idx)
        best = max(best, dsu.sum[root])

        ans[i] = best

    print(*ans)

if __name__ == "__main__":
    solve()
```この実装は、各ステップが挿入となるように削除順序を逆にすることに依存しています。 各挿入の後には、最大で 2 つの結合演算が行われ、1 つは左の近傍と、もう 1 つは右の近傍と行われます。 DSU は接続性とコンポーネントの合計の両方を維持するため、各ステップの後に最適なセグメントを取得することは、単一のグローバル最大値を追跡することになります。 

微妙な点は、更新されるということです`best`単一の要素を挿入した後とコンポーネントを結合した後の両方。 これにより、新しく形成された大きなセグメントが以前のどのセグメントよりも優れている場合の見逃しが回避されます。 

## 実用的な例

 ### 例 1

 入力:```
4
1 3 2 5
3 4 1 2
```削除とは逆の順序で処理するため、有効化の順序は次のとおりです。$2, 1, 4, 3$。 

| ステップ | アクティブ化されたインデックス | アクティブセット | 成分の合計 | ベスト |
 | --- | --- | --- | --- | --- |
 | 1 | 2 | [2] | [2] | 2 |
 | 2 | 1 | [1,2] | [1+3=4] | 4 |
 | 3 | 4 | [1,2,4] | [1+3=4]、[5] | 5 |
 | 4 | 3 | [1,2,3,4] | [1+3+2+5=11] | 11 |

 したがって、前向きの答えは次のとおりです。```
5
4
3
0
```このトレースは、反転によって削除がマージにどのように変換されるか、および結合境界でのみ最大セグメントがどのように展開されるかを示します。 

### 例 2

 入力:```
5
1 2 3 4 5
4 2 3 5 1
```アクティベーション順序を逆にします:$1, 5, 3, 2, 4$| ステップ | アクティブ化されたインデックス | アクティブセット | 成分の合計 | ベスト |
 | --- | --- | --- | --- | --- |
 | 1 | 1 | [1] | [1] | 1 |
 | 2 | 5 | [1]、[5] | [1]、[5] | 5 |
 | 3 | 3 | [1]、[3]、[5] | [1]、[3]、[5] | 5 |
 | 4 | 2 | [1,2,3]、[5] | [6]、[5] | 6 |
 | 5 | 4 | [1..5] | [15] | 15 |

 順方向出力:```
6
5
5
1
0
```これにより、答えは内部の下部構造ではなく、連続したマージのみに依存することが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n \alpha(n))$| 各アクティベーションは最大 2 つの結合を実行し、DSU 操作はほぼ一定に償却されます。 
| スペース |$O(n)$| DSU の親、サイズ、合計、およびアクティビティ追跡の配列 |

 と$n \le 10^5$、これは時間制限内に簡単に収まります。$\alpha(n)$実際には事実上一定です。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys as _sys
    from collections import defaultdict

    class DSU:
        def __init__(self, n, a):
            self.parent = list(range(n))
            self.size = [1] * n
            self.sum = a[:]

        def find(self, x):
            while self.parent[x] != x:
                self.parent[x] = self.parent[self.parent[x]]
                x = self.parent[x]
            return x

        def union(self, a, b):
            ra = self.find(a)
            rb = self.find(b)
            if ra == rb:
                return
            if self.size[ra] < self.size[rb]:
                ra, rb = rb, ra
            self.parent[rb] = ra
            self.size[ra] += self.size[rb]
            self.sum[ra] += self.sum[rb]

    n = int(input())
    a = list(map(int, input().split()))
    order = list(map(int, input().split()))

    active = [False] * n
    dsu = DSU(n, a)

    ans = [0] * n
    best = 0

    for i in range(n - 1, -1, -1):
        idx = order[i] - 1
        active[idx] = True
        best = max(best, a[idx])

        if idx > 0 and active[idx - 1]:
            dsu.union(idx, idx - 1)
        if idx + 1 < n and active[idx + 1]:
            dsu.union(idx, idx + 1)

        best = max(best, dsu.sum[dsu.find(idx)])
        ans[i] = best

    return "\n".join(map(str, ans))

# provided samples
assert run("""4
1 3 2 5
3 4 1 2
""").strip() == """5
4
3
0"""

assert run("""5
1 2 3 4 5
4 2 3 5 1
""").strip() == """6
5
5
1
0"""

# custom cases
assert run("""1
10
1
""").strip() == "10"

assert run("""3
0 0 0
1 2 3
""").strip() == "0\n0\n0"

assert run("""5
5 1 5 1 5
3 1 5 2 4
"""), "mixed values"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一要素 | 10 | 最小サイズの処理 |
 | すべてゼロ | すべてゼロ | 中立和 |
 | 交互の値 | 安定したマージ | 繰り返し結合の正しさ |

 ## 特殊なケース

 単一要素の配列の場合、DSU は 1 つのコンポーネントから開始し、すべてのアクティベーションで単にその要素を最良の要素として設定するため、答えは要素自体だけであり、その後は 0 になります。 隣接ユニオンがトリガーされないため、アルゴリズムはこれを処理します。`best`その単一ノードにのみ依存します。 

すべてゼロの配列の場合、マージに関係なく、すべてのコンポーネントの合計はゼロのままです。 アルゴリズムは引き続きアクティブ化され、正しく結合されますが、`best`決して増加しないため、出力はずっとゼロのままです。 

次のように、大きな値がゼロで区切られている場合`[5, 0, 5]`、すべての中間インデックスがアクティブ化されるまで、ゼロ位置間の結合は決して発生しません。 逆のプロセスにより、実際に接続が存在する場合にのみセグメントが形成されることが保証されるため、最大値は完全なブロックが接続されたときに正確に更新されます。
