---
title: "CF 1167C - ニュース配信"
description: "私たちには、n 人のユーザーと m 人のグループの友人がいるソーシャル ネットワークが与えられています。 各グループには、相互に友達であるユーザーのリストが含まれています。"
date: "2026-06-12T02:11:02+07:00"
tags: ["codeforces", "competitive-programming", "dfs-and-similar", "dsu", "graphs"]
categories: ["algorithms"]
codeforces_contest: 1167
codeforces_index: "C"
codeforces_contest_name: "Educational Codeforces Round 65 (Rated for Div. 2)"
rating: 1400
weight: 1167
solve_time_s: 198
verified: true
draft: false
---

[CF 1167C - ニュース配信](https://codeforces.com/problemset/problem/1167/C)

 **評価:** 1400
 **タグ:** dfs および類似の、dsu、グラフ
 **解決時間:** 3 分 18 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちにはソーシャルネットワークが与えられています`n`ユーザーと`m`友達のグループ。 各グループには、相互に友達であるユーザーのリストが含まれています。 友情はこれらのグループを通じて推移的です。2 人のユーザーが同じグループに属している場合、彼らは友達であり、ニュースは友情の連鎖を通じて広がります。 ユーザーごとに、そのユーザーが唯一の最初の情報源である場合に、最終的にニュースを知ることになるユーザーの数を計算するように求められます。 

概念的には、これはグラフの問題です。 各ユーザーはノードであり、同じグループに属している 2 人のユーザー間にはエッジが存在します。 ニュースは、このグラフの接続されたコンポーネントに沿って広がります。 したがって、ユーザーから始まって最終的にニュースを知るユーザーの数は、`x`を含む連結コンポーネントのサイズに等しい`x`。 

制約は高いです:`n`そして`m`まで行くことができます`5 * 10^5`、グループメンバーシップの合計数も最大です。`5 * 10^5`。 各ユーザーの近隣ノードを明示的に計算し、すべてのノードから BFS または DFS を実行するソリューションは、低速になりすぎます。`O(n*(n+m))`最悪の場合の運用。 このため、同じコンポーネントの繰り返しの走査を回避するアプローチを見つける必要があります。 

明らかではないエッジ ケースには、空のグループやシングル ユーザー グループが含まれます。 とのグループ`0`誰も接続しないため、ユーザーは無視する必要があります。 ユーザーが 1 人のグループでは接続性は向上しませんが、ユニオン ロジックを壊してはなりません。 たとえば、すべてのグループが空の入力では、次の出力が生成されます。`1`各ユーザーがシングルトンコンポーネントを形成するため、ユーザーごとに。 

## アプローチ

 単純なアプローチは、すべてのユーザーの隣接関係リストを作成し、すべてのユーザーから BFS または DFS を実行して、接続されているコンポーネントのサイズをカウントすることです。 これは正しいですが、非効率的です。 のために`n = 5*10^5`、BFS を実行中`n`累積操作が超過する可能性があるため、時間が法外に遅くなります。`10^{11}`最悪の場合。 

重要な洞察は、接続が対称的かつ推移的であるということです。 同じ接続コンポーネント内のユーザーは、常に同じ最終回答を共有します。 これは、各接続コンポーネントのサイズを 1 回計算するだけでよいことを示唆しています。 このための自然な構造は Disjoint Set Union (DSU) であり、Union-Find とも呼ばれます。 各グループを反復処理して、そのすべてのメンバーを 1 つのセットにマージできます。 すべてのグループを処理した後、各セットは連結コンポーネントを表し、各セットのサイズによってそのすべてのメンバーの答えが得られます。 これにより、時間の複雑さがグループ メンバーシップの総数にほぼ線形に減少します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | すべてのユーザーからのブルート フォース BFS/DFS | O(n*(n+m)) | O(n+m) | 遅すぎる |
 | DSU / Union-グループの検索 | O(n + m + グループの合計サイズ * α(n)) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 次のコマンドを使用して DSU を初期化します。`n`要素。各ユーザーは最初は独自のセットに属します。 各セットはそのサイズを追跡します。 
2. 各グループを繰り返し処理します。 一定のサイズのグループの場合`k`、すべてをマージします`k`ユーザーを 1 つのセットにまとめます。 これを行うには、最初のユーザーを残りの各ユーザーと順番にマージします。 
3. すべてのグループが処理された後、各接続コンポーネントは 1 つのセットにマージされ、セットのサイズは任意のメンバーから到達可能なユーザーの数を表します。 
4. 各ユーザーごとに、`1`に`n`、それらが属するセットのサイズを出力します。 これは、そのユーザーがニュースの配信を開始した場合に、そのニュースを知ることになるユーザーの数です。 

機能する理由: Union-Find 構造により、グループの任意のシーケンスを通じて相互に連絡できるすべてのユーザーが 1 つのセットにマージされます。 セットのサイズを追跡することで、各初期ユーザーが何人のユーザーに到達できるかを正確に知ることができます。 パス圧縮により次のことが保証されます。`find`操作は高速であり、全体的な複雑さは線形に近いものになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(10**6)

class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        x_root = self.find(x)
        y_root = self.find(y)
        if x_root == y_root:
            return
        if self.size[x_root] < self.size[y_root]:
            x_root, y_root = y_root, x_root
        self.parent[y_root] = x_root
        self.size[x_root] += self.size[y_root]

n, m = map(int, input().split())
dsu = DSU(n)

for _ in range(m):
    line = list(map(int, input().split()))
    k = line[0]
    if k == 0:
        continue
    users = [x-1 for x in line[1:]]
    first = users[0]
    for u in users[1:]:
        dsu.union(first, u)

result = [dsu.size[dsu.find(i)] for i in range(n)]
print(*result)
```このコードは、パス圧縮を備えた標準 DSU を定義します。 グループごとに、ユーザー ID を 0 から始まるインデックスに変換し、グループのすべてのメンバーを結合します。 すべての結合の後、各ユーザーを含むセットのサイズが出力されます。 パス圧縮を使用すると、確実に繰り返される`find`大量の入力でも呼び出しは高速です。 

## 実用的な例

 **サンプル入力 1**```
7 5
3 2 5 4
0
2 1 2
1 1
2 6 7
```| ステップ | グループが処理されました | DSU 状態 (親) | コンポーネントのサイズ |
 | --- | --- | --- | --- |
 | イニシャル | - | 0 1 2 3 4 5 6 | 1 1 1 1 1 1 1 |
 | グループ1 2,5,4 | マージ(2,5)、マージ(2,4) | 0 1 2 2 2 2 6 | 1 1 3 3 3 3 1 |
 | グループ 2 が空 | - | 変更なし | 変更なし |
 | グループ3 1,2 | マージ(1,2) | 0 2 2 2 2 2 6 | 4 4 4 4 3 3 1 |
 | グループ4 1 | シングル | 変更なし | 変更なし |
 | グループ5 6,7 | マージ(5,6) | 0 2 2 2 2 5 5 | 4 4 4 4 4 2 2 |

 出力：`4 4 1 4 4 2 2`トレースにより、各ユーザーが正しいサイズの接続コンポーネントに属していることが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m + グループの合計サイズ * α(n)) | 各結合と検索はほぼ一定です。 各グループ メンバーを 1 回ずつ処理します。 
| スペース | O(n) | DSU の親配列とサイズ配列には、ユーザーごとに 1 つの整数が格納されます。 

と`n, m`まで`5*10^5`グループの合計サイズは最大`5*10^5`、アルゴリズムは 2 秒の制限内に簡単に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline
    
    class DSU:
        def __init__(self, n):
            self.parent = list(range(n))
            self.size = [1]*n
        def find(self, x):
            if self.parent[x] != x:
                self.parent[x] = self.find(self.parent[x])
            return self.parent[x]
        def union(self, x, y):
            x_root = self.find(x)
            y_root = self.find(y)
            if x_root == y_root: return
            if self.size[x_root] < self.size[y_root]:
                x_root, y_root = y_root, x_root
            self.parent[y_root] = x_root
            self.size[x_root] += self.size[y_root]

    n, m = map(int, input().split())
    dsu = DSU(n)
    for _ in range(m):
        line = list(map(int, input().split()))
        k = line[0]
        if k == 0: continue
        users = [x-1 for x in line[1:]]
        first = users[0]
        for u in users[1:]:
            dsu.union(first, u)

    return ' '.join(str(dsu.size[dsu.find(i)]) for i in range(n))

# Provided sample
assert run("7 5\n3 2 5 4\n0\n2 1 2\n1 1\n2 6 7\n") == "4 4 1 4 4 2 2", "sample 1"

# Custom cases
assert run("5 0\n0\n0\n0\n0\n0\n") == "1 1 1 1 1", "all empty groups"
assert run("3 3\n1 1\n1 2\n1 3\n") == "1 1 1", "singletons"
assert run("4 2\n2 1 2\n2 3
```
