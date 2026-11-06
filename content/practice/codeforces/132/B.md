---
title: "CF 132B - ピート"
description: "小さな長方形のグリッド上で簡略化された Piet インタープリターをシミュレートするように求められます。 各セルは 0 から 9 までの色のピクセルであり、0 は黒、その他の数字は色の付いたブロックです。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 132
codeforces_index: "B"
codeforces_contest_name: "Codeforces Beta Round 96 (Div. 1)"
rating: 2100
weight: 132
solve_time_s: 116
verified: true
draft: false
---

[CF 132B - ピート](https://codeforces.com/problemset/problem/132/B)

 **評価:** 2100
 **タグ:** 実装
 **解決時間:** 1 分 56 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 小さな長方形のグリッド上で簡略化された Piet インタープリターをシミュレートするように求められます。 各セルは 0 から 9 までの色のピクセルであり、0 は黒、その他の数字は色の付いたブロックです。 色付きのブロックは常に長方形で接続されているため、すべての色のクラスターは完全な長方形を形成します。 黒ピクセルは任意の形状にすることができます。 プログラムは、左上の色付きブロックにある命令ポインター (IP) から始まります。 IP は 3 つのコンポーネントで定義されます。現在のブロック (BP)、上下左右を指すことができる方向ポインター (DP)、および IP がエッジまたは黒いピクセルに遭遇したときに動きを変更するブロック チューザー (CP) です。 

各ステップで、インタプリタは、可能であれば BP を DP 方向に次のブロックに移動します。そうでない場合は、CP を更新し、場合によっては DP を回転します。 タスクは、BP が到達した後に着地するブロックの色を決定することです。`n`ステップ。 グリッドは最大 50×50 ピクセルですが、`n`最大 5,000 万に達する可能性がありますが、これは単純なステップバイステップのシミュレーションには大きすぎます。 

明らかではないエッジ ケースには、単一行または単一列のブロック、DP と CP を繰り返し回転させる黒ピクセルのシーケンス、BP が同じブロックを複数回再訪するループなどが含まれます。 たとえば、グリッドは次のようになります。```
12
34
```と`n = 10`予測可能なパターンでブロックを循環します。 単純なシミュレーションでは 10 ステップすべてを試行しますが、より大規模なシミュレーションでは`n`、これは不可能でしょう。 

## アプローチ

 ブルートフォース手法は単純です。最初のブロックから開始し、DP および CP 更新のルールを適用しながら段階的に繰り返し、各移動後に現在のブロックを追跡します。 これが機能するのは、各ステップが決定的であり、ルールが新しい状態を完全に指定しているためです。 ただし、`n`最大 5,000 万件の場合、このアプローチでは実行する操作が多すぎます。 各ステップには O(1) の作業が必要なので、合計の複雑さは O(n) ですが、制限時間 2 秒を考えると遅すぎます。 

より高速なソリューションに対する重要な洞察は、IP 遷移を有限の決定論的な状態マシンとして扱うことです。 各状態は、現在のブロック、DP、および CP によって定義されます。 ブロックの数は限られており (50×50 グリッドで最大 2500)、可能な DP/CP 構成は 8 つあるため (DP 方向 4 × CP 方向 2)、状態の総数は最大 20,000 です。 状態が繰り返されるまでのステップをシミュレートすることで、サイクルを検出します。 サイクルが見つかったら、次の最終ブロックを計算できます。`n`各ステップを繰り返すのではなく、モジュラー演算を使用してステップを実行します。 

小規模な場合にはブルートフォースが機能します`n`または小さなグリッドでも失敗する場合`n`大きいです。 有限の数の状態が存在するという観察により、シミュレーションを最大 1 サイクルの検出とそれに続くモジュロ計算に減らすことができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n) | お(1) | n が大きいと遅すぎます |
 | 周期検出 | O(m × w × 8) | O(m × w × 8) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. グリッドを前処理して、すべての色付きブロックを識別します。 ブロックごとに、その左上と右下の座標を保存します。 これにより、任意の方向に移動するときにブロックのエッジを一定時間チェックすることができます。 
2. DP と CP の方向をベクトルとして定義します。DP は上向きにすることができます。`(−1,0)`、 下`(1,0)`、 左`(0,−1)`、 右`(0,1)`、CP は DP に対して左または右に指定できます。 これにより、エッジに沿った次のピクセルの計算が簡単になります。 
3. DP が右を指し、CP が左を指すように、左上のブロックで IP を初期化します。 各状態をタプルとしてエンコードする`(block_id, dp, cp)`。 
4. 訪問した状態を辞書マッピングに記録しながら、プログラムを段階的にシミュレートします。`(block_id, dp, cp)`ステップ番号に合わせます。 各ステップで、DP および CP ルールに従って次のブロックを決定します。 
5. 状態が繰り返される場合、サイクル長を検出します。 させて`start`サイクルが始まるステップとなり、`length`サイクル内のステップ数。 サイクル長を法とする最終ステップを計算し、そのステップのブロックを返します。 
6. ステップの総数内でサイクルが検出されない場合 (有限状態による可能性は低い)、ステップまで通常のシミュレーションを続行します。`n`。 

機能する理由: ブロックと方向の数が有限であるため、シミュレーションは最終的にある状態を繰り返す必要があります。 Piet インタプリタは決定論的であるため、同じ状態に再度入ると、常に同じブロックのシーケンスが生成されます。 大きく減らすことで`n`検出されたサイクル内のステップまで、すべてのステップをシミュレートすることなく正確さを実現します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

# direction vectors: 0=right, 1=down, 2=left, 3=up
dp_moves = [(0,1), (1,0), (0,-1), (-1,0)]

# CP left/right: -1 for left, +1 for right relative to DP
cp_changes = {'L': -1, 'R': 1}

def rotate_dp(dp):
    return (dp + 1) % 4

def parse_grid(m):
    grid = []
    for _ in range(m):
        row = list(map(int, list(input().strip())))
        grid.append(row)
    return grid

def find_blocks(grid):
    m, w = len(grid), len(grid[0])
    block_id = 0
    block_map = [[-1]*w for _ in range(m)]
    blocks = []
    for i in range(m):
        for j in range(w):
            color = grid[i][j]
            if color == 0 or block_map[i][j] != -1:
                continue
            # expand rectangle
            x0, y0 = i, j
            x1, y1 = i, j
            while x1+1 < m and all(grid[x1+1][y] == color for y in range(y0, y1+1)):
                x1 += 1
            while y1+1 < w and all(grid[x][y1+1] == color for x in range(x0, x1+1)):
                y1 += 1
            # mark cells
            for x in range(x0, x1+1):
                for y in range(y0, y1+1):
                    block_map[x][y] = block_id
            blocks.append((x0, y0, x1, y1, color))
            block_id += 1
    return block_map, blocks

def next_block(block_map, blocks, block_idx, dp, cp):
    x0, y0, x1, y1, color = blocks[block_idx]
    dx, dy = dp_moves[dp]
    # find furthest edge in DP
    if dp == 0:  # right
        edge_cells = [(i, y1) for i in range(x0, x1+1)]
    elif dp == 1:  # down
        edge_cells = [(x1, j) for j in range(y0, y1+1)]
    elif dp == 2:  # left
        edge_cells = [(i, y0) for i in range(x0, x1+1)]
    else:  # up
        edge_cells = [(x0, j) for j in range(y0, y1+1)]
    # select cell in CP direction
    if cp == 'L':
        if dp % 2 == 0:
            next_cell = min(edge_cells, key=lambda x: x[0] if dp==0 else x[1])
        else:
            next_cell = min(edge_cells, key=lambda x: x[1] if dp==1 else x[0])
    else:
        if dp % 2 == 0:
            next_cell = max(edge_cells, key=lambda x: x[0] if dp==0 else x[1])
        else:
            next_cell = max(edge_cells, key=lambda x: x[1] if dp==1 else x[0])
    nx, ny = next_cell[0]+dx, next_cell[1]+dy
    m, w = len(block_map), len(block_map[0])
    if 0 <= nx < m and 0 <= ny < w and block_map[nx][ny] != -1:
        return block_map[nx][ny], dp, cp
    else:
        if cp == 'L':
            return block_idx, dp, 'R'
        else:
            return block_idx, rotate_dp(dp), 'L'

def main():
    m, n = map(int, input().split())
    grid = parse_grid(m)
    block_map, blocks = find_blocks(grid)
    dp, cp = 0, 'L'
    block_idx = block_map[0][0]

    visited = {}
    states = []
    step = 0
    while step < n:
        key = (block_idx, dp, cp)
        if key in visited:
            cycle_start = visited[key]
            cycle_len = step - cycle_start
            remaining = (n - step) % cycle_len
            block_idx, dp, cp = states[cycle_start + remaining]
            break
        visited[key] = step
        states.append((block_idx, dp, cp))
        block_idx, dp, cp = next_block(block_map, blocks, block_idx, dp, cp)
        step += 1
    print(blocks[block_idx][4])

if __name__ == "__main__":
    main()
``
```
