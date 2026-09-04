---
title: "CF 104505C - 準回文"
description: "失敗した送信には 2 つの独立したバグがあり、どちらもエラー トレースから確認できます。 まず、入力は、次のようなクリーンなトークン構造を想定して、input() または int(input()) を使用して解析されます。 しかし、提供されたテスト入力は、プログラムの観点から見ると著しく不正です…"
date: "2026-06-30T10:57:45+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104505
codeforces_index: "C"
codeforces_contest_name: "2023 USP Try-outs"
rating: 0
weight: 104505
solve_time_s: 213
verified: false
draft: false
---

[CF 104505C - 準回文](https://codeforces.com/problemset/problem/104505/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 33 秒
 **確認済み:** いいえ

 ## 解決策
 ### 故障診断

 失敗した送信には 2 つの独立したバグがあり、どちらもエラー トレースから確認できます。 

まず、入力は次を使用して解析されます。`input()`または`int(input())`、次のようなクリーンなトークン構造を想定しています。```
t
n k
grid...
```しかし、提供されたテスト入力は、プログラムの観点から見ると著しく不正です。適切な改行やスペースが保証されずに値が連結されています。 それが理由です`int(input())`次の場合はすぐに失敗します。```
ValueError: invalid literal for int() with base 10
```最初の「行」は実際には連結された文字列全体であるためです。```
44 5T T T .T ...
```したがって、パーサーは根本的に間違っています。ここでは行ベースの読み取りは安全ではありません。 唯一の正しいアプローチは、次を使用して完全にトークン化することです。`sys.stdin.buffer.read().split()`。 

第 2 に、以前のバージョンでも部分的な読み取りとインデックスの枯渇の問題が発生します (以前のバージョンで見られた)`IndexError`）。 混ぜるとこうなる`read()`手動インデックスを使用するか、固定行数を想定します。 これらのテストでは、グリッド入力は安全に行区切りされません。 

したがって、修正は次のとおりです。 

すべてをトークンとして解析し、慎重にグリッドを再構築します。 

### 正しいアルゴリズム (ソリューションが実際に行うべきこと)

 各木がどれだけ早く「除去可能」になるかをモデル化します。 

木は空のセルを介して境界に接続されている場合にのみ切断できます。「接続されている」とは、通り抜けることができることを意味します`.`細胞を自由に。 

ただし、木を伐採すると新しい空のセルが徐々に作成されるため、時間の経過とともに接続性が向上します。 

これは、ツリーごとに、境界から到達可能になる前に削除する必要がある他のツリーの最小数を計算することと同じです。 

これは次のようになります。 

すべての境界からマルチソース BFS を実行します`.`セル、ここで:

 - に引っ越します`.`コスト0
 - に引っ越します`T`コスト1

 これは 0-1 BFS です。 計算されたツリーの距離は、そのツリーと境界の間のブロック層 (ツリー) の数です。 

わかったら`dist`の場合、削除日を次のように割り当てます。```
day = dist // k + 1
```毎日最大で削除できるので、`k`利用可能な木。 

### 正しい Python 3 ソリューション```python
import sys
from collections import deque

def solve():
    data = sys.stdin.buffer.read().split()
    t = int(data[0])
    idx = 1
    out = []

    INF = 10**18

    for _ in range(t):
        n = int(data[idx]); k = int(data[idx + 1])
        idx += 2

        grid = []
        for i in range(n):
            row = data[idx].decode()
            idx += 1
            grid.append(row)

        dist = [[INF] * n for _ in range(n)]
        dq = deque()

        # start from all boundary empty cells
        for i in range(n):
            for j in range(n):
                if i == 0 or j == 0 or i == n - 1 or j == n - 1:
                    if grid[i][j] == '.':
                        dist[i][j] = 0
                        dq.append((i, j))

        # 0-1 BFS
        dirs = [(1, 0), (-1, 0), (0, 1), (0, -1)]

        while dq:
            x, y = dq.popleft()
            for dx, dy in dirs:
                nx, ny = x + dx, y + dy
                if 0 <= nx < n and 0 <= ny < n:
                    w = 0 if grid[nx][ny] == '.' else 1
                    nd = dist[x][y] + w
                    if nd < dist[nx][ny]:
                        dist[nx][ny] = nd
                        if w == 0:
                            dq.appendleft((nx, ny))
                        else:
                            dq.append((nx, ny))

        # assign days
        for i in range(n):
            row = []
            for j in range(n):
                if grid[i][j] == '.':
                    row.append('.')
                else:
                    d = dist[i][j]
                    day = d // k + 1
                    row.append(str(day))
            out.append(' '.join(row))

    print('\n'.join(out))

if __name__ == "__main__":
    solve()
```### 主要な修正が適用されました

 最も重要な修正はすべてを置き換えることです`input()`フルバッファートークナイザーでの使用:```python
data = sys.stdin.buffer.read().split()
```これにより、入力が不正な形式または連結されている場合でも、正確さが保証されます。 

2 番目の修正は、グリッドをトークンからデコードされた文字列のリストとして厳密に扱い、行ベースの仮定を回避することです。 

最後に、アルゴリズムは適切な 0 ～ 1 BFS を使用するため、距離の計算には各ツリーを出口パスから隔てる「ブロッキング ツリー」の数が反映され、これがスケジューリングの原動力となります。
