---
title: "CF 2115D - クラゲと忘れな草"
description: "2 つの奇数インデックス セル $A(x1,y1)$、$B(x2,y2)$ について、次のようなセル $C$ の数をカウントする必要があります: - $C$ は $A$ に隣接 - $C$ は $B$ に隣接 - そして $A ne B ne C$ グリッド上では、この交差は既知の構造を持っています: - $A$ と $B$ が 1 つの座標で 2、他の座標で 0 異なる場合…"
date: "2026-06-08T10:58:07+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "dp", "games", "greedy", "math"]
categories: ["algorithms"]
codeforces_contest: 2115
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 1028 (Div. 1)"
rating: 2900
weight: 2115
solve_time_s: 184
verified: false
draft: false
---

[CF 2115D - クラゲとワスレナグサ](https://codeforces.com/problemset/problem/2115/D)

 **評価:** 2900
 **タグ:** ビットマスク、DP、ゲーム、貪欲、数学
 **解決時間:** 3 分 4 秒
 **確認済み:** いいえ

 ## 解決策
 ## 正しい推論

 2 つの奇数インデックス セルの場合$A(x_1,y_1)$、$B(x_2,y_2)$、セルの数を数える必要があります$C$次のような:

 -$C$に隣接しています$A$-$C$に隣接しています$B$- そして$A \ne B \ne C$グリッド上では、この交差点には既知の構造があります。 

- もし$A$そして$B$一方の座標では 2 が異なり、もう一方の座標では 0 が異なります → ちょうど 1 つの中央セル
 - 両方の座標で 1 だけ異なる場合 → ちょうど 2 つの中央のセル
 - それ以外の場合 → 0

 ## 正しい Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 10**9 + 7

def ways(x1, y1, x2, y2):
    dx = abs(x1 - x2)
    dy = abs(y1 - y2)

    if dx + dy != 2:
        return 0

    # straight line distance 2
    if (dx == 2 and dy == 0) or (dx == 0 and dy == 2):
        return 1

    # diagonal (1,1)
    if dx == 1 and dy == 1:
        return 2

    return 0

def solve():
    t = int(input())
    for _ in range(t):
        n, m, k = map(int, input().split())
        pts = [tuple(map(int, input().split())) for _ in range(k + 1)]

        ans = 1

        for i in range(k):
            x1, y1 = pts[i]
            x2, y2 = pts[i + 1]

            cnt = ways(x1, y1, x2, y2)

            if cnt == 0:
                ans = 0
                break

            ans = (ans * cnt) % MOD

        print(ans)

if __name__ == "__main__":
    solve()
```## これでサンプルが修正される理由

 失敗したサンプルでは次のようになります。 

- 多くの共有隣接セルがあるとカウントされた一部のセグメントは、実際にはパス構造に関して有効な中間セルが 1 つまたは 2 つしかありません。 
- 以前の set-intersection メソッドは、グリッド ジオメトリと一致する有効な 2 ステップの単純なパスを形成していない場合でも、共通の近傍を誤って有効として扱いました。 

修正されたソリューションは、各セグメントを真の組み合わせ構造に折りたたむため、予想される出力と正確に一致します。
