---
title: "CF 246D - カラフルなグラフ"
description: "各頂点が整数で表される色を持つ無向グラフが与えられます。 目標は、どの色が最も多様な隣接色のセットを持っているかを判断することです。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "dfs-and-similar", "graphs"]
categories: ["algorithms"]
codeforces_contest: 246
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 151 (Div. 2)"
rating: 1600
weight: 246
solve_time_s: 90
verified: true
draft: false
---

[CF 246D - カラフルなグラフ](https://codeforces.com/problemset/problem/246/D)

 **評価:** 1600
 **タグ:** ブルート フォース、DFS および同様のもの、グラフ
 **解決時間:** 1 分 30 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各頂点が整数で表される色を持つ無向グラフが与えられます。 目標は、どの色が最も多様な隣接色のセットを持っているかを判断することです。 正式には各色ごとに$k$、その色のすべての頂点を調べ、異なるすべての隣接する頂点の色を収集します。$k$。 隣接する色のセットのサイズが最も大きい色が答えです。 複数の色が同点の場合は、最小の色番号が選択されます。 

グラフには最大で次のものを含めることができます$10^5$頂点とエッジがあるため、より複雑なアルゴリズム$O(n + m)$または$O((n + m)\log n)$タイムアウトになる可能性があります。 また、次のような高い色も管理する必要があります。$10^5$したがって、色によって直接インデックス付けされた配列を作成すると、単純に実行するとメモリを大量に消費する可能性があります。 

いくつかの明白ではないエッジケースが重要です。 まず、すべての頂点が同じ色を持つグラフです。 たとえば、3 つの頂点がすべて 1 の色で、エッジが 1-2、2-3、1-3 の場合、隣接する明確な色がないため、出力は依然として唯一の色である 1 でなければなりません。 第 2 に、複数の色が同じ最大多様性を持っている場合、最初に遭遇した色ではなく、最小の色を選択する必要があります。 最後に、切断された頂点や孤立した色のコンポーネントは近傍に寄与しませんが、色が存在する場合には考慮の対象となります。 

## アプローチ

 強引な方法は各色を考慮することです$k$独立して。 For every vertex of color$k$、その隣接リストをスキャンし、すべての異なる隣接色をセットに追加します。 すべての頂点を処理すると、$k$、私たちは濃度を知っています$Q(k)$。 これをすべての色に対して繰り返すことで正確さが保証されますが、各色のすべての頂点に対して繰り返すと、次のような問題が発生する可能性があるため、遅すぎます。$O(n \cdot m)$密なグラフでの操作。 

最適化するための重要な洞察は、すべての色のすべての頂点を処理する必要はないということです。 各エッジは 2 つの頂点のみを接続し、最大 2 つの色の多様性カウントに寄与します (両端の色が異なる場合はその端点の色)。 したがって、すべてのエッジを 1 回反復し、エッジごとに頂点を接続することができます。$u$そして$v$さまざまな色を追加します$c[v]$隣接する色のセットに$c[u]$そして$c[u]$隣接する色のセットに$c[v]$。 これにより、すべての隣接リストを繰り返しスキャンする問題が、エッジ上の 1 回のパスに軽減されます。 残りは、これらのセットを維持し、最大のセット サイズを持つ色を見つけるための簿記です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n・m) | 色ごとに O(n) | 遅すぎる |
 | 最適 | O(n + m) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. グラフの頂点、色、エッジを読み取ります。 頂点インデックスから色へのマッピングを維持します。 
2. 空の辞書を初期化して、色ごとに隣接する色のセットを保存します。 
3. 各エッジについて$u, v$の色があるかどうかを確認します。$u$そして$v$異なる。 その場合は、次の色を挿入します。$v$の隣接セットに$c[u]$、の色$u$の隣接セットに$c[v]$。 これにより、各色のセットが異なる隣接する色のみをカウントするようになります。 
4. すべてのエッジを処理した後、グラフに存在するすべての色を繰り返し処理します。 隣接セットの最大サイズで色を追跡します。 複数の色の最大サイズが同じ場合は、最も小さい色を選択します。 
5. 選択した色を出力します。 

機能する理由: 2 つの異なる色を接続するすべてのエッジは、各色の隣接多様性カウントに 1 回だけ寄与します。 不変条件は、各色の隣接色のセットが、その色の任意の頂点に隣接する頂点のすべての色の和集合であるということです。 セットを使用することで、重複を自動的に回避します。 したがって、最大カーディナリティは正しく計算されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from collections import defaultdict

n, m = map(int, input().split())
colors = list(map(int, input().split()))
edges = [tuple(map(int, input().split())) for _ in range(m)]

# Map color to its set of neighboring colors
neighbor_colors = defaultdict(set)

for u, v in edges:
    u -= 1
    v -= 1
    cu, cv = colors[u], colors[v]
    if cu != cv:
        neighbor_colors[cu].add(cv)
        neighbor_colors[cv].add(cu)

# Ensure every color present in graph has an entry (even isolated)
unique_colors = set(colors)
for color in unique_colors:
    neighbor_colors[color]  # touch entry to ensure existence

# Find color with maximum neighbor diversity, tie-breaking by smallest color
max_diversity = -1
best_color = None
for color in neighbor_colors:
    diversity = len(neighbor_colors[color])
    if diversity > max_diversity or (diversity == max_diversity and color < best_color):
        max_diversity = diversity
        best_color = color

print(best_color)
```コードは最初に近傍セットを構築し、次に存在するすべての色に孤立した色を処理するための対応するエントリがあることを確認します。 最後に、タイブレーク ルールを尊重しながら、最大限の多様性を探索します。 1 から始まる入力のインデックスをデクリメントするように注意が必要です。 

## 実用的な例

 **サンプル 1:**

 入力:```
6 6
1 1 2 3 5 8
1 2
3 2
1 4
4 3
4 5
4 6
```| 頂点 | 色 | 隣接する色 (すべてのエッジを処理した後) |
 | --- | --- | --- |
 | 1 | 1 | {2、3} |
 | 2 | 1 | {2、3} |
 | 3 | 2 | {1、3} |
 | 4 | 3 | {1、2、5、8} |
 | 5 | 5 | {3} |
 | 6 | 8 | {3} |

 色ごとのセットサイズ:

 | 色 | 隣人の多様性 |
 | --- | --- |
 | 1 | 2 |
 | 2 | 2 |
 | 3 | 4 |
 | 5 | 1 |
 | 8 | 1 |

 印刷されるカラー 3 の最大多様性は 4 です。 

**カスタム例 2: すべての頂点を同じ色**

 入力:```
3 2
1 1 1
1 2
2 3
```各頂点は色 1 を持ち、すべてのエッジは同じ色の頂点を接続します。 隣接するセットは空です。 最大多様性は 0 で、存在する唯一の色は 1 です。 出力:```
1
```これにより、孤立した色または多様でない色が正しく処理されることが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m) | 1 回のパスで入力を読み取り、1 回のパスでエッジを通過して隣接するセットを塗りつぶし、1 回のパスで色を最大値を選択します。 |
 | スペース | O(n + m) | 色ごとの隣接セットとエッジのストレージ。 |

 制約を考慮すると、$n, m \le 10^5$、これは 2 秒の制限内で効率的に実行されます。 

## テストケース```python
import sys, io
from collections import defaultdict

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n, m = map(int, input().split())
    colors = list(map(int, input().split()))
    edges = [tuple(map(int, input().split())) for _ in range(m)]
    neighbor_colors = defaultdict(set)
    for u, v in edges:
        u -= 1
        v -= 1
        cu, cv = colors[u], colors[v]
        if cu != cv:
            neighbor_colors[cu].add(cv)
            neighbor_colors[cv].add(cu)
    for color in set(colors):
        neighbor_colors[color]
    max_div = -1
    best = None
    for color in neighbor_colors:
        div = len(neighbor_colors[color])
        if div > max_div or (div == max_div and color < best):
            max_div = div
            best = color
    return str(best)

# provided sample
assert run("6 6\n1 1 2 3 5 8\n1 2\n3 2\n1 4\n4 3\n4 5\n4 6\n") == "3", "sample 1"

# custom cases
assert run("3 2\n1 1 1\n1 2\n2 3\n") == "1", "all same color"
assert run("4 2\n1 2 3 4\n1 2\n3 4\n") == "1", "tie between 1 and 3, pick smaller"
assert run("5 0\n10 20 30 40 50\n") == "10", "no edges, smallest color"
assert run("5 4\n1 2 3 2 1\n1 2\n2 3\n3 4\n4 5\n") == "2", "diversity across middle color"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 3 2、すべて 1 | 1 | すべての同じ色の頂点を処理します。 
| 4 2、色 1 ～ 4 | 1 | タイブレークは最小のものを選択します |
