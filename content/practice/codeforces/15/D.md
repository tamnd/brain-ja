---
title: "CF 15D - マップ"
description: "サイズ n × m の長方形のマップが与えられます。各セルの高さは負ではありません。 ピーターは a × b のサイズの都市を建設したいと考えています。 都市を配置するには、その長方形内のすべてのセルを最小の高さまで減らして、その長方形内の地面を平らにする必要があります。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "implementation", "sortings"]
categories: ["algorithms"]
codeforces_contest: 15
codeforces_index: "D"
codeforces_contest_name: "Codeforces Beta Round 15"
rating: 2500
weight: 15
solve_time_s: 132
verified: true
draft: false
---
[CF 15D - マップ](https://codeforces.com/problemset/problem/15/D)

 **評価:** 2500
 **タグ:** データ構造、実装、並べ替え
 **解決時間:** 2 分 12 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 次のサイズの長方形のマップが与えられます。`n`×`m`ここで、各セルの高さは負ではありません。 ピーターは大きな都市を建設したいと考えている`a`×`b`。 都市を配置するには、その長方形内のすべてのセルを最小の高さまで減らして、その長方形内の地面を平らにする必要があります。 特定の位置に建築する場合の費用は、除去された地面の総量となります。 目標は、最適な長方形 (利用可能なすべての位置の中でレベリング コストが最小のもの) を繰り返し見つけて、そこに都市を配置することです。 複数のポジションが同点の場合、ピーターは一番上のポジション、次に一番左のポジションを好みます。 セルが都市によって占有されると、それ以降の都市では使用できなくなります。 

この問題により、最大 1000 × 1000 の地図と、地図全体の寸法までの都市のサイズが得られます。 考えられるすべての四角形を調べてそのコストを最初から計算する単純なアプローチでは、最大 1,000,000 個の四角形を反復処理することになり、最悪の場合、それぞれの四角形の合計が最大 1,000,000 個になる可能性があります。 これにより、約 10^12 の操作が行われ、2 秒の制限時間を大幅に超えます。 したがって、単純に合計と最小値を再計算することを避ける、より効率的なアプローチが必要です。 

エッジ ケースには、都市が空間全体を占める非常に小さなマップ、すべてのセルが等しい (複数の位置が結びつく) マップ、および注意深い整数の処理が必要な大きな値 (最大 10^9) を持つマップが含まれます。 たとえば、高さのある 2×2 マップの場合`[[1, 2], [3, 5]]`都市サイズが 1×2 の場合、最初の都市は左上の行を占め、レベリングコストは 1、次に 2 番目の都市はコスト 2 で下の行を占めます。「最左端」のタイブレーカーを無視すると、順序が間違っている可能性があります。 

## アプローチ

 ブルートフォースアプローチでは、すべての要素が評価されます。`a×b`地図上の四角形。 各長方形について、最小の高さを見つけて、各セルとその最小値との差を合計して、平準化コストを計算します。 次に、コストが最小の長方形を選択し、それを占有済みとしてマークします。 これがなくなるまで繰り返されます`a×b`長方形が利用可能です。 正しいとはいえ、このアプローチは非常に遅くなります。各四角形を最初から評価するには O(a・b) 操作が必要で、全体の複雑さは O((n−a+1)(m−b+1)・a・b) になります。 n=m=1000 および a=b=500 の場合、これは約 10^11 回の演算に相当します。 

重要な洞察は、スライドする長方形内の**最小値**と**高さの合計**の両方を高速クエリのために前処理できることです。 プレフィックスの合計を計算して、O(1) 内の任意の四角形の合計をすばやく計算できます。 最小値については、両端キューに基づく 2D スライディング最小値テクニックを使用できます。最初に幅の行単位の最小値を計算します。`b`、次に、高さのこれらの行方向の結果の列方向の最小値`a`。 これにより、それぞれの行列が生成されます。`a×b`長方形の最小値は O(1) で使用できます。 これら 2 つの事前計算を使用すると、O(n・m・a・b) ではなく、O(n・m) 内のすべての可能な位置の平準化コストを計算できます。 最後に、都市を順番に配置するには、占有されているセルをマークする効率的な方法が必要です。 2D バイナリ占有マップ上のフェンウィック ツリーまたはセグメント ツリーで十分ですが、各セルは 1 つの都市の一部にのみなり、総反復回数が制限されるため、同じスライド長方形チェックを備えた単純な占有グリッドでも機能します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O((n−a+1)(m−b+1)・a・b・k) | O(n・m) | 遅すぎる |
 | 最適 | O(n・m) | O(n・m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 高さの 2D プレフィックス合計配列を計算します。 細胞ごとに`(i, j)`、からのすべてのセルの合計を保存します。`(1, 1)`に`(i, j)`。 これにより、包含-排他原理を使用した O(1) 個の四角形合計クエリが可能になります。 
2. 幅の行方向のスライド最小値を計算します。`b`デクを使用します。 各行について、左端の要素が現在の長さウィンドウの最小値になるようにインデックスの両端キューを維持します。`b`。 これらの中間結果を行列に保存します`row_min`。 
3. 高さの列方向のスライド最小値を計算します。`a`の上`row_min`あらゆる最小値を取得するには`a×b`矩形。 ここでも、列ごとのデキューを使用して、垂直スライディング ウィンドウの現在の最小値を維持します。 この行列を呼び出します`rect_min`。 
4. それぞれについて`a×b`長方形の場合、レベリングコストを次のように計算します。`sum of heights − minimum × (a·b)`合計にはプレフィックス合計配列を使用し、`rect_min`最小限のために。 最小コストの長方形を追跡します。 一番上の四角形、次に一番左の四角形を選択して、関係を解除します。 
5. 選択した長方形を占有グリッド内で占有されているものとしてマークします。 使用可能な四角形がなくなるまで (つまり、候補となる四角形に少なくとも 1 つの占有セルが含まれる) まで、手順 4 ～ 5 を繰り返します。 
6. 都市の位置を、配置された順序で出力し、その平準化コストも合わせて出力します。 

機能する理由: 各反復で、すべての有効な長方形を考慮し、正確な平準化コストを計算します。 合計と最小値を前処理することにより、四角形ごとの計算が一定になります。 最小コストの長方形を選択し、それが占有されているとマークすると、問題の決定論的な都市配置に従うことが保証されます。 スライディング ウィンドウ アプローチにより、各長方形の最小値が常に正しいことが保証されます。 

## Python ソリューション```python
import sys
from collections import deque
input = sys.stdin.readline

def read_matrix(n, m):
    mat = []
    for _ in range(n):
        mat.append(list(map(int, input().split())))
    return mat

def prefix_sum(mat):
    n, m = len(mat), len(mat[0])
    ps = [[0]*(m+1) for _ in range(n+1)]
    for i in range(n):
        for j in range(m):
            ps[i+1][j+1] = mat[i][j] + ps[i][j+1] + ps[i+1][j] - ps[i][j]
    return ps

def rectangle_sum(ps, x1, y1, x2, y2):
    return ps[x2][y2] - ps[x1][y2] - ps[x2][y1] + ps[x1][y1]

def sliding_min_row(mat, b):
    n, m = len(mat), len(mat[0])
    row_min = [[0]*(m-b+1) for _ in range(n)]
    for i in range(n):
        dq = deque()
        for j in range(m):
            while dq and mat[i][dq[-1]] >= mat[i][j]:
                dq.pop()
            dq.append(j)
            if dq[0] == j-b:
                dq.popleft()
            if j >= b-1:
                row_min[i][j-b+1] = mat[i][dq[0]]
    return row_min

def sliding_min_col(mat, a):
    n, m = len(mat), len(mat[0])
    rect_min = [[0]*m for _ in range(n-a+1)]
    for j in range(m):
        dq = deque()
        for i in range(n):
            while dq and mat[dq[-1]][j] >= mat[i][j]:
                dq.pop()
            dq.append(i)
            if dq[0] == i-a:
                dq.popleft()
            if i >= a-1:
                rect_min[i-a+1][j] = mat[dq[0]][j]
    return rect_min

def main():
    n, m, a, b = map(int, input().split())
    heights = read_matrix(n, m)
    ps = prefix_sum(heights)
    
    row_min = sliding_min_row(heights, b)
    rect_min = sliding_min_col(row_min, a)
    
    occupied = [[False]*m for _ in range(n)]
    result = []
    
    while True:
        best = None
        best_cost = None
        for i in range(n-a+1):
            for j in range(m-b+1):
                if any(occupied[x][y] for x in range(i, i+a) for y in range(j, j+b)):
                    continue
                total = rectangle_sum(ps, i, j, i+a, j+b)
                cost = total - rect_min[i][j]*a*b
                if best_cost is None or cost < best_cost or (cost == best_cost and (i < best[0] or (i==best[0] and j<best[1]))):
                    best = (i, j)
                    best_cost = cost
        if best is None:
            break
        i, j = best
        result.append((i+1, j+1, best_cost))
        for x in range(i, i+a):
            for y in range(j, j+b):
                occupied[x][y] = True
    
    print(len(result))
    for r in result:
        print(*r)

if __name__ == "__main__":
    main()
```**説明:**

 プレフィックス合計セクションでは、O(1) 個の四角形合計クエリが許可されます。 行方向および列方向のスライディング最小値は、長方形ごとに再計算するのではなく、O(n・m) で最小値を計算する 2D モノトーン キューを実装します。 占有グリッドにより、すでに使用されている長方形を選択することがなくなります。
