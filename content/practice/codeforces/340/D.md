---
title: "CF 340D - バブルソートグラフ"
description: "問題理解の順列が与えられる 1 から n までの数字の順列が与えられます。 1 から n までの数字。 想像してみてください。この順列に対してバブル ソートを実行し、これに対して通常のバブル ソートを実行すると想像してください。"
date: "2026-06-06T17:17:43+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "dp"]
categories: ["algorithms"]
codeforces_contest: 340
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 198 (Div. 2)"
rating: 1500
weight: 340
solve_time_s: 125
verified: true
draft: false
---

[CF 340D - バブルソートグラフ](https://codeforces.com/problemset/problem/340/D)

 **評価:** 1500
 **タグ:** 二分探索、データ構造、dp
 **解決時間:** 2 分 5 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解の順列を考えると

 からの数値の順列が与えられます。`1`に`n`。 

1 から n までの数字。 想像してみてください。この順列に対してバブル ソートを実行し、これに対して通常のバブル ソートを実行すると想像してください。 バブルソートは毎回、順列内で隣接する 2 つの要素を見つけます。 バブル ソートが 2 つの隣接する間違った順序の間でスワップを実行し、それらを入れ替えるたびに、要素間に無向エッジを追加し、スワップされる 2 つの値の間に無向エッジを追加します。 グラフの値が交換されます。 

バブルソートが完了すると、頂点が値になります。`1...n`。 

バブルソート後、頂点が 1 ～ n の数字を持つグラフを取得します。 タスクは終了しました。グラフ自体をシミュレートするのではなく、発生したスワップを見つけるために、エッジがすべてを記録するグラフを取得します。 タスクは、その最大独立集合の最大のサイズを見つけることです。これは、そのグラフ内の最大の集合独立集合を意味します。これは、エッジのない頂点のペアのコレクション間でエッジのない最大の頂点を意味します。 

彼らのペア間の最初の挑戦。 

1 つ目は、バブル ソートが実際にどのようなグラフを作成するかを理解することです。 直接的な課題は、バブル ソートのシミュレーションでバブル ソートが実際にどのようなグラフ シミュレーションによって作成されるかを理解することです。 最大 O(n²) スワップまでのバブル ソートを直接シミュレートします。 必要に応じて n を使用`O(n² large as 100000, that would be roughly 10¹⁰ operations in)`交換します。 と`n`まで` the worst case, which is completely impossible within a100000`、制限時間は約 1 秒です。 

という制約が`10^10`入力が順列である最悪の場合の演算は非常に重要です。 すべてを1秒以内に行うことは完全に不可能です。 

制約は強い値を 1 回だけ表示するため、グラフに何らかの隠れた構造があることが示唆されます。 スワップの強力な構造。 

一般的なアルゴリズムが必要です`O(n log n)`。 

よくある間違いは、すべての反転がエッジになると考えることです。 考慮する：```text
3
3 1 2
```反転は (3、エッジです。次のように考えます。```text
3
3 1 2
```反転は次のとおりです`(3,1)`そして`(3,1) and (3,2). Bubble sort indeed2)`、実際、両方ともエッジ (3,1) と (3,2) を作成するため、エッジになります。 しかし、この例は違いが明らかになっていないため、真実ではありません。 

より明らかな例は次のとおりです。``` of inversions in general,text
4
4 1 3  it is true because bubble sort eventually2
```反転により、これらのペアが直接交換されます。 

別の魅力的なペア (4,2) も存在しますが、これらの間違いは、値が直接交換されないことを明示的にグラフを構築することです。 バブル ソートでは、隣接する要素のみが交換され、最大独立集合アルゴリズムが実行されます。 最大の独立セット。 すべての反転をエッジとして扱うと、任意のグラフに対して NP が困難になるため、間違ったグラフが構築されてしまいます。 

すぐに間違いに見えるもう 1 つの簡単なアプローチは、グラフを明示的に構築することですが、その後は絶望的です。 重要な観察は、このグラフが最大独立集合問題を解いているということです。 一般的な最大独立セットは非常に特殊です。 

考慮する：```text
4
 is NP-hard, so if the graph were arbitrary this4 1 2 3
```バブルは絶望的だろう。 隠れた構造がエッジを生み出す`(4,1)`、`(4,2)`、`(4,3)`。 グラフは全体です 最大の独立集合はサイズがあります`3`、つまり問題の点です。 

最後の微妙な`{1,2,3}`。 一般的なグラフのケースは、すでにソートされた順列です。 

アルゴリズムはこのグラフが来るという事実を見逃します```text
5
1 2 3 4 5
 from a very structured process.

A useful edge case is an```スワップは発生しないため、グラフはすでに並べ替えられています。```text
5
1 2 3 4  has no edges. The answer is 55
```スワップは発生しないため、グラフにはエッジがありません。 すべての頂点が独立セットに属することができるため、最大独立セットにはすべての頂点が含まれます。 を前提とした解決策、その答えは次のとおりです。`5`。 

反対の極端な場合:```text
5
 at least one edge exists will fail here.

## Approaches

5 4 3 2 1
```すべてのペアの最も直接的なアイデアは、バブル形成の反転をシミュレートすることです。 説明どおりのバブル ソートは、最終的にすべての反転ペアを正確に入れ替えます。 毎回 2 回、完全なグラフが生成されます。 独立したセットの最大サイズは次のとおりです。`1`。 

隣接する値が交換されることを理解して、それらの間にエッジを追加します。 これら 2 つの極端な値がすべてのエッジが空である理由を収集した後、主要な洞察に直接つながるグラフを計算して完成させることができます。 

## 最大の独立集合に近づきます。 

シミュレーション

 ブルートフォース解釈は簡単です。 バブル ソート自体は説明どおりにすでに O(n²) のコストがかかり、すべてのエッジを追加します。最悪の場合、バブル ソートは結果のグラフを作成し、その (n²) スワップを計算する可能性があるためです。 n = 100000 の場合、最大独立セット。 

これではグラ​​フを構築することすら不可能です。 

さらに悪い方法は遅すぎます。 バブルソートが実行され、一般的なグラフ内で最大の独立集合を見つけるのは計算によるものです。`O(n²)`比較するのは非常に困難です。 したがって、グラフが入れ替わるため、ブルート フォースは両方とも失敗します。 のために`n =  construction is too expensive and because the resulting optimization problem appears in100000`、操作数は計算可能です。 

重要な観察は、バブルによって生成されたグラフが実用的な限界を超えていることです。 

次の観察は、ソートが非常に特殊であり、重要なものである in です。 

x > y の 2 つの値 x と y を考えます。 

バブル ソート。元の順列で反転を形成する場合にのみ、x が y の前にある場合、2 つの値が交換されます。 

順列を取ると、反転が形成されます。 2つの値`x > y`。 もし`x`バブルソート中、x は最終的に y を超えなければなりません。 前にも登場してるので`y`、それらは反転を形成します。 バブル スワップは隣接しており、x と y が隣接してスワップされる瞬間が 1 つだけあり、最終的にソートが移動します。`x`右。 その瞬間、エッジ(x,y)方向と`y`隣接するまで左に移動すると、at が追加されます。 

最初に x が y の後に表示される場合、それらはすでに正確に 1 回交換されたポイントになります。 それらが正しい相対順序にあり、入れ替わらない場合。 

これは最初は反転ではなく、相対的な順序は決して変化しません。これは、次のことを意味します。 

2 つの頂点は接続されており、交換されることはありません。 

これは、それらが元の順列で反転を形成する場合に限り、ということを意味します。 

グラフには、反転ペアごとに 1 つのエッジが含まれています。 

ここで、独立したセットを形成する頂点に関して反転条件を selectNow で書き直すとします。 ポジションはありません。 させて`pos[v]`価値のある立場になる`v`順列の中で。 

値について`u < two selected vertices may form an inversion. Thus, among the selected v`、それらはグラフ値内で隣接しており、順列での順序はすでに正確に一致している必要があります。```text
pos[u] > pos[v]
`` increasing.

That is exactly the definition of an`

In other words, the graph is a increasing subsequence.

Conversely, every increasing subsequence contains no permutation graph.

An independent set cannot inversion pair, so its vertices form an independent set.

We have established a bijection:

Maximum independent set contain two vertices connected by an edge. Therefore, if we choose values size = Longest Increasing Subsequence length.

Once

```テキスト
 x1 < x2 < ... < x 問題は LIS に帰着します、週```

inside an independent set, we must have

```テキスト
 標準 pos[x1] < pos[x2] < ... < pos[xk] を使用すると、O(n log n) で解くことができます。```

Otherwise patience-sorting technique with binary search.

| Approach | Time Complexity some pair would form an inversion and create an edge.

So | Space Complexity | Verdict |
|---|---|---|---|
| Brute Force an independent set corresponds exactly to a subsequence of values whose positions are bubble-sort simulation + graph reasoning increasing.

If we list the positions in | O(n²) or worse | O(n² value order:

```テキスト
 pos[1]、pos[2]、...、) | 遅すぎる |
 | LIS削減pos[n]```

then the | O(n log n) | O(n) | Accepted |

## Algorithm answer becomes the length of the Longest Increasing Subsequence Walkthrough

1. Read the permutation.

2. Maintain an of this sequence.

Computing LIS in ` array `tails`.

   `tails[i]`O(n log n)` is a standard technique will store the smallest possible ending value of and easily fits the constraints.

| Approach | Time Complexity | an increasing subsequence of length `i + 1`.

3. Process Space Complexity | Verdict |
|---|---|---|---|
| Brute Force | the permutation from left to right.

4. O(n²) or worse | O(n² For the current value `x`,) | Too slow |
| Optimal (LIS) | O(n log n) | find the first position in `tails` whose O(n) | Accepted |

## Algorithm Walkthrough value is greater than or equal to `x`.

   This

1. Read the permutation.

2. Build an array ` position can be found with binary search.

5. If nopos` where `pos[v]` stores the position such position exists, append `x` to `tails`.

   This of value `v` in the permutation.

3. Construct the sequence:

   means we have found a longer increasing subsequence than any ```テキスト
 pos[1]、pos[2]、前に見たもの。 

6. それ以外の場合は、..., pos[n] を置き換えます```

   This sequence describes position with `x`.

   A smaller ending value is where each value appears.

4. Compute the Long always better because it leaves more room forest Increasing Subsequence of this sequence using the standard patience future elements to extend the subsequence.

7. After-sorting method.

5. Maintain an all values are processed, the array `tails`.

   `tails[len-1]` stores length of `tails` equals the length of the the smallest possible ending value of Longest Increasing Subsequence.

8. Output that length.

### Why it works an increasing subsequence of length `len`.

6. Process the

An edge exists exactly between pairs of position sequence from left to right.

   For each position value ` values that form inversions in the original permutation.

x`, find the first element in `tails` thatAn independent set cannot contain two adjacent vertices of the graph is greater than or equal to `x`.

7. If. Therefore it cannot contain an inversion pair. The no such element exists, append `x` to `tails`.

   This selected values must appear in increasing order within the permutation.

Convers means we found a longer increasing subsequence.

8. Otherwise replace that element with `x`.

ely, every increasing subsequence contains no inversion pair. Since   A smaller tail is always no inversion pair exists, no edge exists between any two preferable because it leaves more room for future extensions.

9. After processing all elements, the chosen vertices, making it an independent set.

Thus independent sets and increasing subsequences length of `tails` is the LIS length.

10. Output are exactly the same objects viewed that length.

### Why it works

Every graph from two different perspectives. Maximizing one is edge corresponds to an inversion pair.

Take any independent set and equivalent to maximizing the other.

The patience-sorting LIS algorithm is sort its vertices by value:

```テキスト
 v 1 < v2 < ... < vk であるため、増加するサブシーケンスの最大長を計算することが知られています。```

Since no pair `tails[k]` always stores the smallest achievable ending value is connected, none of these pairs forms of a subsequence of length `k + 1`. Binary an inversion. Hence

```テキスト
 pos[v1] < pos[v2] < ... < search は、更新のたびにこの不変式を保持します。 

## Python ソリューション``` pos[vk]
```位置は増加するサブシーケンスを形成しますPython
 インポートシステム
 bisect インポートから bisect_left

 入力=。 

逆に、値を仮定します。```text
 sys.stdin.readline

def solve():
    n = int(input())
    a = list(map(int,v1 < v2 < ... < vk
```input().split())) が増加しています

 尾 = []

 a の x の場合:
 pos = bisect_left 位置。 その場合、反転を形成するペアは存在しないため、どのペアの間にもエッジは存在しません。(tails, x)

 pos == len(尾部)の場合:
 尾部.append(x)
 それらは独立した集合を形成します。 

したがって、もう 1 つあります。 
尾[位置] = x

 print(長さ(尾))

 __name__ == "__main__"の場合:
 独立集合と増加部分列間の 1 対 1 対応```text
pos[1], solve()
```解決策は pos[2], ..., pos[n] ではありません。```

The maximum independent set size is constructs the graph.

The crucial observation is the equivalence between exactly the LIS length of that sequence.

## Python Solution

```パイソン
 グラフの独立したセットと増加するサブシーケンスをインポートします。 その削減システムが完了したら
 bisect インポートから bisect_left

 input = が確立され、残りは標準 LIS sys.stdin.readline

 defsolve():
 n = int(入力())
 a = list(map(int, input().split()))

 計算。 

配列`tails`ソートされた順序で維持されます。 pos = [0] * (n + 1)
 使用する`bisect_left`enumerate(a) で for i, v を見つけます。 
pos[v] = i

 尾 = []

 現在の値が既存の末尾を置き換えることができる最初の位置。 range(1, n + 1) の v の場合:
 x = pos[v]
 入力は順列であり、すべての値は個別ですが、 idx = bisect_left(tails, x)

 idx == len(尾部)の場合:`bisect_left`これは依然として tails.append(x) の正しい選択です
 それ以外の場合:
 尾[idx] = x

 標準の厳密に増加する LIS アルゴリズム。 

`pos == print(len(tails)) の場合

 __name__ == "__main__"の場合:
 len(tails)` を解決します。現在の値は、これまでに見つかった最長のサブシーケンスを拡張します()```

The first part builds the inverse. Otherwise we replace an existing tail. permutation `pos`. Instead of asking which value sits at each position, The replacement does not reduce any achievable subsequence length, it we ask where each value sits.

The sequence `pos[1], pos[ only improves future extension opportunities by making the ending2], ..., pos[n]` is exactly value smaller.

No overflow concerns the object whose LIS we need.

The ` exist because all values are at most 100000.

## Worked Examples

###tails` array implements the standard ` Example 1

Input:

```テキスト
 3
 3 1 2
 O(n log n)` LIS algorithm. Using `二等分_左````

| Current value | is the correct choice because LIS requires strictly increasing values tails before | Position. Replacing the first tail | tails after |
|---|---|---|---|
| 3 | [] greater than or equal to the current value | 0 | [3] |
| 1 | [ preserves the optimal invariant that3] | 0 | [1] |
| 2 | [ each subsequence length keeps the smallest possible ending position1] | 1 | [1, 2] |

Final.

No integer-overflow issues exist because all values are at LIS length = 2.

Output:

```テキスト
 ほとんどの`100000`。 

## 実用的な例

 ### 例 1

 入力:```text
3
3 2
```これは独立集合 {1 2 に対応します。```

Positions:

| Value | Position1,2}. The graph contains edges (3,1) and (3,2), |
|---|---|
| 1 | 1 |
|  so vertex 3 cannot coexist with either2 | 2 |
| 3 | 0 |

Position sequence:

```彼らのうち。 

### 例 2

 入力:```text
5
1 2 3 text
[1, 2, 0]
```LIS トレース:

 | 現在の x | 前の尾 | 後の尾 |
 |---|4 5```

| Current value | tails before | Position | tails after |
|---|---|---|---|
| 1 | [] | 0 |---|---|
| 1 | [] | [1] |
| [1] |
| 2 | [1] | 1 | [ 2 | [1] | [1, 2] |
| 0 | [1, 2] |
| 3 | [1, 2] | 2 | [1, 2] | [0, 2] |

Final LIS1, 2, 3] |
| 4 | [1, 2, length = 2.

Answer:

```テキスト
 2```

This corresponds 3] | 3 | [1, 2, 3, 4] |
| 5 | [1, 2, 3, 4] | 4 | [ to choosing values `{1,2}`. Their1, 2, 3, 4, 5] |

Final LIS positions are increasing, so no inversion exists between them.

### Example 2

Input:

```テキスト
 5
 5 4 3 2 1```

 length = 5.

Output:

```テキスト
 5```

No inversPositions:

| Value | Position |
|---|---|
| 1 | 4 |
ions exist, so bubble sort performs no swaps and the graph has no edges. Every| 2 | 3 |
| 3 | 2 |
| 4 | 1 |
|  vertex belongs to the maximum independent set.

## Complexity Analysis

| Measure | Complexity | Explanation |
|---5 | 0 |

Position sequence:

```テキスト
 [4、3、|---|---|
 | 時間 | O(n log n) | ワン2、1、0]```

LIS trace:

| Current binary search for each of n elements |
| Space | x | tails before | tails after |
|---|---|---|
|  O(n) | The `tails` array may contain up to n values |

With n4 | [] | [4] |
| 3 | [4] | [3] |
| ≤ 100000, O(n log n) performs roughly a 2 | [3] | [2] |
| 1 | [2] | few million primitive operations, which easily fits within the time [1] |
| 0 | [1] | [0] |

Final LIS limit. The memory usage is linear and well below length = 1.

Answer:

```テキスト 1```

This the 256 MB limit.

## Test Cases

```パイソン
 # は完全なグラフの場合を示します。 すべてのペア ヘルパー: 入力文字列に対してソリューションを実行し、出力文字列を返します。 
インポートシステム
 は反転であるため、選択できる頂点は 1 つだけですimport io
 bisect インポートから bisect_left

 間違いない。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 |--- run(inp: str) -> str:
 システム|---|---|
 | 時間 | O(n log n) | One.stdin = io.StringIO(inp)

 n = int(sys.stdin.readline())
 a = 値ごとのリスト LIS 挿入 |
 |(map(int, sys.stdin.readline().split()))

 尾 = []

 スペース | O(n) | 位置配列と LIS 構造体 |

 a の x の場合:
 pos = bisect_left(尾部,あり`n = 100000`、`n x)
 pos == len(尾部)の場合:
 尾部.append(x)
 log n` はおよそ数百万のプリミティブな else です。 
尾[位置] = x

 return str(len(t 操作、制限時間内で快適に。Theails))

 # 提供されたサンプル
 assert run("3\n3 1 のメモリ使用量は線形であり、2 を大幅に下回っています\n") == "2", "サンプル 1"

 # 最小サイズ、すでにソート済み
 利用可能な 256 MB をアサートします。 

## テストケース```python
# run("2\n1 2\n") == "2", "minimum sorted helper: run solution on input string, return output string
import sys, io
"

# minimum size, reversed
assert run("2\n2 1\n") == "1", "from bisect import bisect_left

def run(inp: str) -> str:
    sys.stdinminimum reversed"

# fully increasing
assert run("5\n1 2 3  = io.StringIO(inp)

    n = int(sys.stdin.readline())
    a = list(map(int,4 5\n") == "5", "all vertices independent"

# fully sys.stdin.readline().split()))

    pos = [0] * (n + 1)
    decreasing
assert run("5\n5 4 3 2 1\n") for i, v in enumerate(a):
        pos[v] = i

    tails = []

    == "1", "maximum inversion case"

# common for v in range(1, n + 1):
        x = pos[v]
        idx = bisect LIS example
assert run("6\n3 1 _left(tails, x)

        if idx == len(tails):
            tails5 2 6 4\n") == "3",.append(x)
        else:
            tails[idx] = x

    return str(len(tails))

# "general case"
```| テスト入力 | 提供されるサンプルで期待される出力
 assert run("3\n3 1 2\n") == "2", " | 検証内容 |
 |---|---|---|
 | `2/サンプル1"

 # 最小サイズ、ソート済み
 アサート run("2\n1 21 2` | `2` | 最小\n") == "2"、"最小ソート"

 # 最小サイズ、反転
 assert run(" 並べ替えられた順列 |
 |`2 / 2 1`|`1`| 最小の非自明な反転 |
 | `5 / 2\n2 1\n") == "1"、"最小値が反転"

 # すでにソート済み
 アサート1 2 3 4 5` | `5` | グラフ run("5\n1 2 3 4 5\n") == "5", "空のグラフ"

 # エッジなし |
 |`5 / 5 4 3 2 1`| ` 完全に逆転した
 assert run("5\n5 4 3 2 1\n") == "1", "完全なグラフ"

 # 大文字と小文字が混在する
 assert run("4\n1` | 最大反転密度 |
 |`6 / 3 1 5 2 6 4`|`2 1 4 3\n") == "2", "two independent3`| LIS の一般的な動作 |

 ## 特殊なケース

 ペアを検討してください」```

| Test input | Expected output | the already sorted permutation:

```テキスト
 5
 1 検証内容 |
 |---|---|---|
 | `2 / 2 3 4 5```

Bubble sort performs no1 2` | `2` | Minimum swaps. The graph contains five size, no inversions |
| `2 / 2  isolated vertices. The LIS algorithm1` | `1` | Minimum size, one inversion |
 produces length 5 because every| `5 / 1 2 3 4 5` element extends the current subsequence. | `5` | Empty graph case |
| `5 / 5  The answer is correctly4 3 2 1` | `1` | 5.

Consider the reverse permutation:

```テキスト
 5
 完全なグラフのケース |
 | `4 / 2 1 4 5 4 3 2 1```

Every3` | `2` | Mixed inversion structure |

## Edge Cases

 pair forms an inversion. The graph becomes complete, meaningConsider the sorted permutation:

```テキスト
 5
 1 2 すべての頂点のペアは隣接しています。3 4 5```

The Any independent set can contain only position sequence is:

```テキスト
 [0,1、1 つの頂点。 LISの長さも2,3,4です]```

Its LIS 1, which the algorithm returns length is `5`..

 Bubble sort performs no swaps, so theConsider a permutation where not every inversion corresponds to graph has no edges. Every vertex belongs to the an obvious adjacent pair:

```テキスト
 4
 4 1 3 2
 `` maximum independent set. The algorithm returns`LISは`[1, 3] `5`。 

次に、逆順列を考えてみましょう` or `[1, 2]`、両方:```text
5
5 4 3 2 1
 of length 2. The algorithm computes```位置の順序は次のとおりです。```text
[4,3, 2. This demonstrates why the reduction is2,1,0]
```その LIS の長さは`1`。 むしろ反転ペアに基づいて、値のすべてのペアが反転を形成し、個々のバブルソートパスを追跡しようとするのではなく、完全なグラフを生成します。 独立したセットには最大でも 1 つの頂点が含まれます。 すべての反転が正確に作成されます。 アルゴリズムは正しく返します`1`。 

より微妙な例は、スワップがいつ発生するかに関係なく、1 つのエッジです。 

は：```text
4
4 1 2 3
```考慮してください:```text
6
2 1 4 3 6 5
```位置の順序は次のとおりです。```text
[1,2,3,0]
```反転は独立したローカルペアです。 LISの長さは`3`。 

バブルソートによりエッジが作成される`(4,1)`、たとえば、LIS の長さは 3 です。`[2,4,6]`または`[ `(4,2)`, `(4,3)` and nothing else. Values `{1,2,3}`1,3,5]`。 アルゴリズムは 3 を返し、それらの間にエッジはなく、独立したサイズのセットを与える最大値と一致します。`3`。 アルゴリズムに依存しないセット サイズ。 これは、複数の切断された反転構造が、増加を通じて LIS サブシーケンスによって自然に処理されることを正確に発見していることを確認します。`[1,2,3]`。 

これらの例では主な落とし穴をカバーし、定式化の理由を説明します。 グラフ構造は LIS 問題と同等です。
