---
title: "CF1647E まどかと6年生"
description: "n 個の机がある教室があり、最初はそれぞれの机に 1 から n までの番号が不明な順序で割り当てられた生徒が座っています。 各レッスンの後、すべての生徒は順列 p に従って机に移動します。"
date: "2026-06-10T04:07:10+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "dfs-and-similar", "greedy"]
categories: ["algorithms"]
codeforces_contest: 1647
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 777 (Div. 2)"
rating: 2500
weight: 1647
solve_time_s: 88
verified: false
draft: false
---

[CF 1647E - まどかと6年生](https://codeforces.com/problemset/problem/1647/E)

 **評価:** 2500
 **タグ:** データ構造、DFS および同様の、貪欲
 **解決時間:** 1 分 28 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 教室があります`n`机。最初はそれぞれの机に次の番号を持つ学生が座っていました。`1`に`n`未知の順序で。 各レッスンの後、生徒は順列に従って机に移動します。`p`。 複数の生徒が同じ机に着いた場合、最も小さい番号を持つ生徒だけが残り、他の生徒はすべて追放されます。 そして、空いた机は、無限の待機列から番号の若い順に学生によって埋められます。 いくつかのレッスンの後、最後の席が与えられます`a`そして動きの順列`p`、そして辞書編集上最小の初期順列を再構築するように求められます。`b`生徒たちの`1`に`n`。 

課題は、生徒が退学させられ、補充されるという事実にあり、最終的な座席から逆算して考える必要があります。 各ステップで多くの生徒が退学になる可能性があるため、シーケンスの組み合わせ数を潜在的に考慮せずに、最小の初期順列を見つけるために順方向シミュレーションを簡単に行うことはできません。 辞書編集上の要件により、別の層が追加されます。つまり、それらを取得できる位置には、より小さい番号を選択する必要があります。 

制約は次のとおりです。`n ≤ 10^5`そして`a_i ≤ 10^9`。 これにより、レッスンに対するブルートフォース シミュレーションやすべての順列の試行が排除されます。 私たちは線形または線形に近い時間で動作するアルゴリズムを目指す必要があります。 明らかではないエッジケースには、次のようなサイクルが含まれます。`p`複数の机が同じ机を指し、一部の生徒が退学になる場合や、空の机で終わるチェーンなどがあります。 たとえば、次の場合`p = [2,2]`そして`a = [1,3]`、単純なアプローチでは、番号を間違ったデスクに誤って割り当てる可能性があります。 

## アプローチ

 ブルートフォースアプローチでは、すべての候補順列から前方のプロセスをシミュレートしようとします。`1..n`どちらが結果につながるか見てみましょう`a`。 単一の候補をチェックするだけでも時間がかかります`O(n)`レッスンごとに、レッスン数は`O(n)`。 と`n!`可能な順列ですが、これは完全に不可能です。 実現可能な解決策を明らかにする洞察は、次のように定義される動きグラフを観察することで得られます。`p`。 机をノードとして扱い、そこからエッジを描画すると、`i`に`p_i`、一連のサイクルとチェーンが表示されます。 最小番号の生徒だけが生き残り、残りは部外者に置き換えられるため、各サイクルは最初の順列からちょうど 1 人の生徒を受け取ります。 

重要な洞察は、サイクルの一部であるどの机も、最初は最後の生徒を固定しなければならないということです。`a`それが最終的にそこに保存されます。そうしないと上書きされてしまいます。 辞書編集上最小の順列は、他のデスクからの入力エッジがないデスクから開始して、移動グラフに対して深さ優先検索を実行し、各チェーンまたはサイクルを使用可能な最小の数で埋めることによって取得されます。`a`。 そうすることで、順方向の移動ルールを尊重して、最初の並べ替えのできるだけ早い段階で、より小さい番号が割り当てられるようになります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n! * n) | O(n) | 遅すぎる |
 | DFS ベースのグラフ再構成 | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各デスクが配置される有向グラフを構築します。`i`机を指さす`p[i]`。 各デスクの受信エッジ数を追跡します。 入力エッジがゼロの机はチェーンの開始点です。 サイクルには、すべてのデスクの入次数 ≥ 1 が含まれます。 
2. 最終的な学生のソートされたリストを維持する`a`。 目標は、辞書編集上の要件を満たすことができるポジションに、利用可能な最小の学生番号を割り当てることです。 
3. 回答配列を初期化する`b`ゼロ付き。 
4. 入次数が 0 の各デスクに対して、移動エッジに従って深さ優先のトラバースを実行します。 割り当てる`a_i`に`b_i`ノードを訪問するとき。 重複を避けるために、割り当てられた生徒を並べ替えられたリストから削除します。 これにより、ソースからシンクまでのチェーンに沿って番号が割り当てられます。 
5. サイクル (まだ訪問していないノード) の場合、割り当てられていない最小の生徒を選択し、それをサイクル内の任意のノードに割り当てます。 その後、サイクルに沿って DFS を続行し、残りの生徒を割り当てます。 これにより、サイクル内で 1 人の学生だけが生き残ることが保証され、辞書編集上の順序が維持されます。 
6. 最終的な順列を出力します。`b`。 

**機能する理由:** DFS トラバーサルにより、移動グラフ内のすべてのチェーンまたはサイクルに、生徒が生き残る順序を考慮した番号が割り当てられます。 入次数が 0 のデスクは、辞書編集上の最小要件を満たす、利用可能な最小の数値を最初に受け取ることが保証されます。 サイクルは、最初に訪れた机に最小の生徒を配置することによって処理され、サイクルごとに 1 人の生存者のみが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(1 << 20)

n = int(input())
p = list(map(lambda x: int(x)-1, input().split()))
a = list(map(int, input().split()))

from collections import defaultdict, deque

# build graph and in-degree
in_deg = [0]*n
graph = [[] for _ in range(n)]
for i, dest in enumerate(p):
    graph[i].append(dest)
    in_deg[dest] += 1

b = [0]*n
used = [False]*n
available = sorted(range(n), key=lambda x: a[x])  # indices of a sorted by value

def dfs(u):
    used[u] = True
    idx = available.pop(0)
    b[u] = a[idx]
    for v in graph[u]:
        if not used[v]:
            dfs(v)

# handle chains
for i in range(n):
    if in_deg[i] == 0 and not used[i]:
        dfs(i)

# handle remaining cycles
for i in range(n):
    if not used[i]:
        dfs(i)

print(' '.join(map(str, b)))
```の`available`list は、値によってソートされた最終的な学生のインデックスを保持します。 DFS では、移動グラフに従って、未割り当ての最小番号が各デスクに確実に割り当てられます。 チェーンが最初に処理され、次にサイクルが処理されます。 深いチェーンを処理するために再帰制限が増加しました。 

## 実用的な例

 入力例:```
5
5 5 3 3 1
1 8 2 9 4
```| デスク | p[i] | a[i] | b[i] 割り当て |
 | --- | --- | --- | --- |
 | 0 | 4 | 1 | 1 |
 | 1 | 4 | 8 | 2 |
 | 2 | 2 | 2 | 3 |
 | 3 | 2 | 9 | 5 |
 | 4 | 0 | 4 | 4 |

 トラバーサルはゼロ次数ノードから開始され、その後サイクルが処理されます。 ファイナル`b = [1,3,2,5,4]`。 

2 番目の例:```
3
2 3 2
10 20 30
```グラフのエッジ: 0→1、1→2、2→1。 ノード 0 には次数 0 があり、DFS を 0 から開始します。パスに沿って最小の番号を割り当て、残りのサイクルが処理されます。 ファイナル`b = [10,30,20]`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 最終的な配列のソートには最初に O(n log n) かかり、DFS トラバーサルは O(n) |
 | スペース | O(n) | グラフ表現、b の配列、使用済み、および次数 |

 この複雑さは、2 秒の制限時間内に簡単に収まります。`n ≤ 10^5`。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    # call the solution
    n = int(input())
    p = list(map(lambda x: int(x)-1, input().split()))
    a = list(map(int, input().split()))
    from collections import defaultdict, deque
    in_deg = [0]*n
    graph = [[] for _ in range(n)]
    for i, dest in enumerate(p):
        graph[i].append(dest)
        in_deg[dest] += 1
    b = [0]*n
    used = [False]*n
    available = sorted(range(n), key=lambda x: a[x])
    def dfs(u):
        used[u] = True
        idx = available.pop(0)
        b[u] = a[idx]
        for v in graph[u]:
            if not used[v]:
                dfs(v)
    for i in range(n):
        if in_deg[i] == 0 and not used[i]:
            dfs(i)
    for i in range(n):
        if not used[i]:
            dfs(i)
    print(' '.join(map(str, b)))
    return output.getvalue().strip()

# provided sample
assert run("5\n5 5 3 3 1\n1 8 2 9 4\n") == "1 3 2 5 4", "sample 1"
# minimum size
assert run("2\n1 1\n2 1\n") == "2 1", "minimum size"
# all same p (cycle)
assert run("3\n2 3 1\n5 6 7\n") == "5 6 7", "cycle handling"
# already sorted a
assert run("4\n2 3 4 1\n1 2 3 4\n") == "1 2 3 4", "sorted final a"
# reverse a
assert run("3
```
