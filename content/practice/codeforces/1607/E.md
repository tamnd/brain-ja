---
title: "CF 1607E - ボード上のロボット 1"
description: "ロボットを長方形のグリッド上に配置し、一連の方向コマンドを実行するように求められます。 ロボットは上下左右に移動でき、開始セルを選択できます。"
date: "2026-06-10T07:43:29+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 1607
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 753 (Div. 3)"
rating: 1600
weight: 1607
solve_time_s: 92
verified: false
draft: false
---

[CF 1607E - ボード上のロボット 1](https://codeforces.com/problemset/problem/1607/E)

 **評価:** 1600
 **タグ:** 実装
 **解決時間:** 1 分 32 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 ロボットを長方形のグリッド上に配置し、一連の方向コマンドを実行するように求められます。 ロボットは上下左右に移動でき、開始セルを選択できます。 目標は、ロボットがボードから降りる前に最大数のコマンドを実行できるように開始セルを選択することです。 コマンドがロボットをグリッドの外に押し出す場合、そのコマンドは失敗し、カウントできません。 出力は、最適な開始位置の行と列です。 

問題のサイズは大きく、ボードの各寸法は最大 1,000,000 であり、すべてのテスト ケースにわたるコマンドの合計長は最大 1,000,000 です。 これにより、可能性のあるすべての開始セルからロボットをシミュレートすることは即座に除外されます。$n \times m$開始位置には最大で$10^{12}$これは実行可能な実行時間をはるかに超えています。 

エッジケースは、ロボットのシーケンスが非常に短いか極端な場合に発生します。 たとえば、ボードが$1 \times 1$とロボットが左に動くと、ロボットはまったく動けなくなります。 もう 1 つの微妙なケースは、長いシーケンスにバランスの取れた動きが含まれる場合 (例: 「LRLRLR」) に発生します。開始位置に応じて、ロボットが落ちる場合と落ちない場合があり、累積変位を無視する単純なシミュレーションでは次善の開始セルが選択される可能性があります。 

## アプローチ

 ブルートフォースアプローチを説明するのは簡単です。 ボード内の各セルについて、ロボットが外側に移動しようとするまでのロボットの動きを段階的にシミュレートします。 正常に実行されたコマンドの数を追跡し、最大の実行を許可する開始セルを選択します。 これは正しいですが、遅すぎます。 最悪の場合のサイズのボードの場合$10^6 \times 10^6$および長さのシーケンス$10^6$、おおよそ必要になります$10^{12}$テストケースごとの操作。 

重要な点は、すべての開始セルをシミュレートする必要がないということです。 本当に重要なのは、行と列の累積変位です。 シーケンスを処理するときにロボットが垂直方向と水平方向にどれだけ移動するかを追跡しましょう。 定義する`dx`水平変位として、`dy`開始点からの垂直変位として。 コマンドを実行する際に、各方向でこれまでに確認された最小変位と最大変位を追跡します。 これらは、開始セルに対してロボットが移動する最も遠い左、右、上、下を表します。 

取得したら`min_dx`、`max_dx`、`min_dy`、 そして`max_dy`、安全な開始位置は、ロボットがボードの境界を超えないセルです。 この制約に違反する最初のコマンドは、まさに蓄積された変位がロボットを外側に押し出すときです。 コマンドを段階的に処理して、許可される開始範囲を更新し、それ以上の動きがオーバーフローする場合に停止することができます。 これにより、各シーケンスをその長さに比例して線形時間で処理できるようになり、ボード全体のシミュレーションが回避されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * m * | s | ) |
 | 最適 | O( | s | ) |

 ## アルゴリズムのチュートリアル

 1. 開始セルに対するロボットの変位を追跡するために 4 つの変数を初期化します。`min_row_offset`、`max_row_offset`、`min_col_offset`、`max_col_offset`。 すべてはゼロから始まります。 これらは、これまでに見られた各方向の極端な偏差を表しています。 
2. さらに 2 つの変数を維持します。`current_row_offset`そして`current_col_offset`、コマンドを反復処理する際に、仮想の開始セルからの累積変位を追跡します。 どちらもゼロから始めてください。 
3. シーケンス内の各コマンドについて、累積変位を更新します。 「L」はデクリメントします`current_col_offset`、「R」は増加し、「U」は減少します`current_row_offset`、「D」はそれをインクリメントします。 
4. 累積変位を更新した後、この動きを含めることでボード サイズを超える極端な値が発生するかどうかを確認します。 具体的には、`max_row_offset - min_row_offset`≧ n になる、または`max_col_offset - min_col_offset`≥ m になると、この動きを安全に含めることはできません。 この時点で、コマンドの処理を停止します。 
5. 停止する前に、新しい累積変位で極端なオフセットを更新します。 これは、開始点に対してロボットが到達した最も遠い位置を記録します。 
6. 反復を終了した後 (最後に到達するか、早期に停止するか)、最適な開始セルを計算します。 その行は`1 - min_row_offset`そしてそのコラムは`1 - min_col_offset`。 これにより、上方向または左方向への最大偏差がボードの境界を超えないようにロボットが配置されます。 調整により、1 から始まるインデックスが保証されます。 
7. このセルを返します。 

これが機能する理由は、最小オフセットと最大オフセットのみを追跡することで、開始セルを基準としたロボットのパスの「境界ボックス」を効果的にキャプチャしているためです。 これらの境界を尊重する開始セルは、安全な実行を保証します。 境界に達するまで貪欲にパスを拡張するため、実行されるコマンドの数は自然に最大になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        n, m = map(int, input().split())
        s = input().strip()
        
        min_row_offset = max_row_offset = 0
        min_col_offset = max_col_offset = 0
        current_row_offset = current_col_offset = 0
        
        for c in s:
            if c == 'L':
                current_col_offset -= 1
            elif c == 'R':
                current_col_offset += 1
            elif c == 'U':
                current_row_offset -= 1
            elif c == 'D':
                current_row_offset += 1
            
            min_row_offset = min(min_row_offset, current_row_offset)
            max_row_offset = max(max_row_offset, current_row_offset)
            min_col_offset = min(min_col_offset, current_col_offset)
            max_col_offset = max(max_col_offset, current_col_offset)
            
            if max_row_offset - min_row_offset >= n or max_col_offset - min_col_offset >= m:
                # Undo last move; cannot fit
                if c == 'L':
                    current_col_offset += 1
                elif c == 'R':
                    current_col_offset -= 1
                elif c == 'U':
                    current_row_offset += 1
                elif c == 'D':
                    current_row_offset -= 1
                break
        
        start_row = 1 - min_row_offset
        start_col = 1 - min_col_offset
        print(start_row, start_col)
```コードでは、実行中のオフセットと両方の次元の極値を維持します。 小切手`max_row_offset - min_row_offset >= n`ボードの高さを決して超えないようにします。 ロボットはそのコマンドを安全に実行できないため、ボードの境界に違反する場合は、最後の移動を取り消します。 最終的な計算では、最小オフセットによってロボットがボードの外に押し出されないように開始セルが配置されます。 

## 実用的な例

 ### 例 1

 入力:`3 3`ボード、シーケンス`RRDLUU`。 

| ステップ | コマンド | 行オフセット | 列オフセット | 最小行 | 最大行 | min_col | 最大列 |
 | --- | --- | --- | --- | --- | --- | --- | --- |
 | 1 | R | 0 | 1 | 0 | 0 | 0 | 1 |
 | 2 | R | 0 | 2 | 0 | 0 | 0 | 2 |
 | 3 | D | 1 | 2 | 0 | 1 | 0 | 2 |
 | 4 | Ｌ | 1 | 1 | 0 | 1 | 0 | 2 |
 | 5 | う | 0 | 1 | 0 | 1 | 0 | 2 |
 | 6 | う | -1 | 1 | -1 | 1 | 0 | 2 |`max_row_offset - min_row_offset = 1 - (-1) = 2 < n = 3`

`max_col_offset - min_col_offset = 2 - 0 = 2 < m = 3`すべての動きがフィットします。 計算開始:`1 - min_row_offset = 1 - (-1) = 2`、`1 - min_col_offset = 1 - 0 = 1`。 答え：`(2, 1)`。 

### 例 2

 入力:`1 2`ボード、シーケンス`L`。 

- 指示`L`→col_offset = -1、min_col_offset = -1、max_col_offset = 0
 -`max_col_offset - min_col_offset = 0 - (-1) = 1 < m = 2`→ぴったりです。 
- 開始: 行 = 1 - 0 = 1、列 = 1 - (-1) = 2 →`(1, 2)`これらのトレースは、アルゴリズムが最大のコマンドに対してロボットを正しく配置していることを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O( | s |
 | スペース | お(1) | n、m、または | に関係なく、固定数のカウンターのみが維持されます。 

合計 |s| が与えられると、 すべてのテスト ケースで ≤ 10^6 であるため、アルゴリズムは 2 秒の制限と 256 MB のメモリ制限内に簡単に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    solve()
    return output.getvalue().strip()

#
```
