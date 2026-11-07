---
title: "CF 144B - ミーティング"
description: "私たちは、無限のデカルト平面上に置かれた長方形のテーブルで暖かい毛布を必要としている将軍の数を判断するように求められます。 テーブルの角は整数座標を持つ 2 つの点によって与えられ、長方形の周囲に沿った各整数点は将軍をホストします。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 144
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 103 (Div. 2)"
rating: 1300
weight: 144
solve_time_s: 75
verified: true
draft: false
---

[CF 144B - 会議](https://codeforces.com/problemset/problem/144/B)

 **評価:** 1300
 **タグ:** 実装
 **解決時間:** 1 分 15 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは、無限のデカルト平面上に置かれた長方形のテーブルで暖かい毛布を必要としている将軍の数を判断するように求められます。 テーブルの角は整数座標を持つ 2 つの点によって与えられ、長方形の周囲に沿った各整数点は将軍をホストします。 多数のラジエーターがあり、それぞれに位置と加熱半径があります。 少なくとも 1 つのラジエーターまでのユークリッド距離がそのラジエーターの半径以下であれば、将軍は快適です。 出力は、すべてのラジエーター範囲外の将軍の数です。 

制約は控えめです。座標は ±1000 で制限されているため、長方形の周囲には最大でも数千の点があり、ラジエーターの数は最大 1000 です。これは、各将軍をすべてのラジエーターに対してブルートフォース チェックすることが可能であることを意味します。$O(\text{generals} \times n)$オペレーションは依然として大幅に下回っている$10^7$、2秒の制限時間に快適に適合します。 

明らかではないエッジ ケースには、負の座標に位置合わせされた長方形、幅または高さが 1 に等しい長方形 (将軍の行のみを生成)、および将軍の位置に正確に配置されたラジエーターが含まれます。 たとえば、長方形が (0,0) と (0,3) で定義され、(0,1) に半径 0 のラジエーターがある場合、その点のみが加熱されます。 他の人には毛布が必要です。 不用意に実装すると、長方形の座標に対して誤って反復処理を行ったり、加熱条件を厳密な不等式でチェックしたりして、これらのケースが失敗する可能性があります。 

## アプローチ

 ブルートフォースアプローチは、長方形のすべての周囲点を列挙し、すべてのラジエーターまでのユークリッド距離をチェックすることによって機能します。 将軍ごとに、各ラジエーターまでの距離を計算し、それをラジエーターの半径と比較します。 一般をカバーするラジエーターがない場合は、ブランケットカウンターをインクリメントします。 この方法は問題の説明を直接実装しているため正しいですが、次のことが必要です。$O(\text{perimeter} \times n)$操作。 最大周長が約 8000 ポイント、ラジエーターが 1000 個である場合、これはおおよそ次のようになります。$8 \cdot 10^6$距離チェック。許容範囲内ですが、最大の入力に対しては若干厳しい可能性があります。 

問題のサイズが小さいため、大規模な最適化は必要ありませんが、小さな改善の 1 つは、半径の 2 乗を事前に計算し、それを距離の 2 乗と比較することです。 これにより、コストのかかる平方根計算が回避され、距離を比較するだけで済むため、相対比較は正しいままになります。 ここでは、空間分割などの他の最適化は過剰です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(P × n)、P = 周囲の点 | O(n) | 承認済み |
 | 最適 (距離の 2 乗) | O(P × n) | O(n) | 承認済み |

 最適なアプローチは、距離の 2 乗比較を行う同じブルートフォースです。これにより、浮動小数点演算が回避され、実際にはわずかに高速になります。 

## アルゴリズムのチュートリアル

 1. 長方形の角を解析します$(x_a, y_a)$そして$(x_b, y_b)$入力順序に関係なく、長方形のエッジを正しく反復するための最小および最大の x 座標と y 座標を決定します。 
2. すべてのラジエーターの位置とその半径を収集します。 各ラジエーターについて、二乗半径を事前計算します。$r_i^2$距離チェックで平方根を避けるため。 
3. 長方形の周囲に沿ったすべての整数点を列挙します。 上端と下端の x 座標と、左端と右端の y 座標を反復します。 端を慎重に扱って、角を二重に数えないようにしてください。 
4. 各周囲点（一般）に対して、フラグを初期化します。`warm`偽として。 すべてのラジエーターを反復処理し、一般的なラジエーターまでのユークリッド距離の 2 乗を計算します。 いずれかのラジエーターが満たしていれば$dx^2 + dy^2 \le r_i^2$、 セット`warm = True`そして休憩する。 
5. すべてのラジエーターをチェックした後、次の場合は`warm`False のままの場合、ブランケット カウンタをインクリメントします。 
6. 総ブランケット数を出力します。 

アルゴリズムの正しさは、すべてのラジエーターに対して各境界点をチェックし、二乗距離を使用して将軍が暖地または寒地として誤って分類されていないことを確認することで決まります。 各周囲点は 1 回だけ処理され、距離の比較は整数演算で正確に行われます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

xa, ya, xb, yb = map(int, input().split())
n = int(input())
radiators = []
for _ in range(n):
    x, y, r = map(int, input().split())
    radiators.append((x, y, r*r))  # store squared radius

# normalize rectangle coordinates
x_min, x_max = min(xa, xb), max(xa, xb)
y_min, y_max = min(ya, yb), max(ya, yb)

blankets = 0

# generate perimeter points
for x in range(x_min, x_max+1):
    for y in [y_min, y_max]:
        warm = False
        for rx, ry, r2 in radiators:
            dx = x - rx
            dy = y - ry
            if dx*dx + dy*dy <= r2:
                warm = True
                break
        if not warm:
            blankets += 1

for y in range(y_min+1, y_max):  # avoid double-counting corners
    for x in [x_min, x_max]:
        warm = False
        for rx, ry, r2 in radiators:
            dx = x - rx
            dy = y - ry
            if dx*dx + dy*dy <= r2:
                warm = True
                break
        if not warm:
            blankets += 1

print(blankets)
```最初のループは水平エッジを処理し、2 番目のループは既にカウントされている角を除く垂直エッジを処理します。 平方半径を保存すると、浮動小数点精度エラーが回避され、パフォーマンスが向上します。 各距離チェックでは整数演算のみが使用されます。 

## 実用的な例

 **サンプル 1**

 入力長方形: (2,5) ～ (4,2)

 ラジエーター: (3,1,2)、(5,3,1)、(1,3,2)

 | 一般 | ラジエーターまでの二乗距離 | 暖かい？ |
 | --- | --- | --- |
 | (2,2) | 2->10->2 | 本当 |
 | (2,3) | 5->5->0 | 本当 |
 | (2,4) | 10->5->2 | 本当 |
 | (2,5) | 18->5->8 | 偽 |
 | (3,2) | 1->4->4 | 本当 |
 | (3,5) | 10->4->8 | 偽 |
 | (4,2) | 2->1->8 | 本当 |
 | (4,3) | 5->1->2 | 本当 |
 | (4,4) | 10->1->5 | 偽 |
 | (4,5) | 18->1->10 | 偽 |

 必要なブランケット = 4、予想される出力と一致します。 

**サンプル 2**

 入力長方形: (5,2) ～ (6,3)

 ラジエーター: (5,2,1)、(5,3,1)、(6,2,1)、(6,3,1)

 すべての周囲の点はラジエーターの適用範囲と一致するため、必要なブランケットは = 0 となります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(P × n) | 各周囲点は、n 個のラジエーターすべてに対してチェックされます。 最悪の場合、P ≤ 4000。 |
 | スペース | O(n) | ラジエーター座標と二乗半径を保存します。 |

 このソリューションは、2 秒の制限と 256 MB のメモリ制限内に簡単に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline
    xa, ya, xb, yb = map(int, input().split())
    n = int(input())
    radiators = []
    for _ in range(n):
        x, y, r = map(int, input().split())
        radiators.append((x, y, r*r))
    x_min, x_max = min(xa, xb), max(xa, xb)
    y_min, y_max = min(ya, yb), max(ya, yb)
    blankets = 0
    for x in range(x_min, x_max+1):
        for y in [y_min, y_max]:
            warm = False
            for rx, ry, r2 in radiators:
                dx = x - rx
                dy = y - ry
                if dx*dx + dy*dy <= r2:
                    warm = True
                    break
            if not warm:
                blankets += 1
    for y in range(y_min+1, y_max):
        for x in [x_min, x_max]:
            warm = False
            for rx, ry, r2 in radiators:
                dx = x - rx
                dy = y - ry
                if dx*dx + dy*dy <= r2:
                    warm = True
                    break
            if not warm:
                blankets += 1
    return str(blankets)

# provided samples
assert run("2 5 4 2\n3\n3 1 2\n5 3 1\n1 3 2\n") == "4", "sample 1"
assert run("5 2 6 3\n4\n5 2 1\n5 3 1\n6 2 1\n6 3 1\n") == "0", "sample 2"

# custom cases
assert run("0 0 0 0\n1
```
