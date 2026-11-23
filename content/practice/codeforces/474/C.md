---
title: "CF 474C - キャプテン・マーモット"
description: "私たちは、マーモット船長が 4 人ずつのグループが平面上で完全な正方形を形成するようにモグラの連隊を組織するのを手伝ってほしいと頼まれました。 各モルは位置 (xi, yi) から始まり、(ai, bi) に「ホーム」を持ちます。"
date: "2026-05-30T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "geometry"]
categories: ["algorithms"]
codeforces_contest: 474
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 271 (Div. 2)"
rating: 2000
weight: 474
solve_time_s: 72
verified: true
draft: false
---

[CF 474C - キャプテン マーモット](https://codeforces.com/problemset/problem/474/C)

 **評価:** 2000
 **タグ:** ブルート フォース、ジオメトリ
 **解決時間:** 1 分 12 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは、マーモット船長が 4 人ずつのグループが平面上で完全な正方形を形成するようにモグラの連隊を組織するのを手伝ってほしいと頼まれました。 各ほくろはある位置から始まります`(xi, yi)`そしてそこに「家」がある`(ai, bi)`。 唯一許可されている操作は、ほくろをそのホームを中心に反時計回りに 90 度回転させることです。 各回転は 1 つの移動としてカウントされ、目標は、各連隊の正方形を形成するために必要な移動の合計を最小限に抑えることです。 

入力は次のもので構成されます`n`連隊にはそれぞれ 4 つのほくろがあるので、合計すると次のようになります。`4*n`ほくろ。 座標の範囲は次のとおりです。`-10^4`に`10^4`。 与えられた`n`最大 100 まで可能ですが、モルは最大 400 個です。 出力は連隊ごとの最小移動数、または正方形を形成することが不可能な場合は -1 です。 

エッジケースには、すべてのほくろが同じ位置にあり、非縮退正方形を形成できない状況、または同じほくろで複数の回転が必要な状況が含まれます。 モルごとに 1 つの固定回転のみをチェックする単純なアプローチは失敗する可能性があります。これは、各モルの 0 ～ 3 の回転をすべて調査し、最小の動きで正方形を形成する組み合わせを見つける必要があるためです。 

## アプローチ

 強引な方法は簡単です。連隊ごとに、4 つのほくろすべてに対して可能なすべての回転 (0、90、180、270 度) を試します。 各モールには 4 つのオプションがあるため、連隊ごとに 4^4 = 256 通りの組み合わせがあります。 組み合わせごとに、結果として得られる 4 つの点が正方形を形成しているかどうかを確認します。 これは、制約 n ≤ 100 を考慮すると、合計 256 * 100 = 25,600 回のチェックが実行されるため、実現可能です。 

最適化のための重要な洞察は、連隊あたり 256 の組み合わせが十分に小さいため、それ以上の枝刈りが厳密には必要ないことを認識することです。 主な作業は、4 つの点が正方形を形成しているかどうかを効率的にチェックすることです。 正方形は辺の長さと対角線が等しいです。 角度を計算したり浮動小数点を使用したりする代わりに、二乗距離を使用して精度誤差を回避できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * 4^4) | お(1) | 承認済み |
 | 最適 | O(n * 4^4) | お(1) | 承認済み |

 最適なアプローチは、本質的には、平方をチェックするために整数演算を慎重に実装した総当りアプローチです。 

## アルゴリズムのチュートリアル

 1. 連隊の数を読み取る`n`。 
2. 連隊ごとに、4 つのほくろの位置とホーム座標を読み取ります。 
3. ほくろごとに、ホームの周りを 0、1、2、または 3 回転した後の 4 つの可能な位置を事前計算します。 回転の公式は、`(x, y)`ぐるぐる回った`(a, b)`反時計回りに 90 度:`(a - (y-b), b + (x-a))`。 
4. 4^4 = 256 通りの回転の組み合わせをすべて、モルごとに 1 つずつ生成します。 各組み合わせの合計移動数 (使用された回転の合計) を追跡します。 
5. 各組み合わせについて、結果の 4 つの点が縮退していない正方形を形成しているかどうかを確認します。 

- すべてのペアごとの二乗距離を計算します。 正確に 2 つの一意の距離が存在する必要があります。辺が小さいほど、対角線が大きいほどです。 
- 対角線の距離が正確に 2 回発生し、側面の距離が正確に 4 回発生し、側面の距離 > 0 であることを確認します。 
6. すべての有効な正方形の組み合わせにおける最小の合計移動数を追跡します。 有効な組み合わせが存在しない場合は、その連隊に対して -1 を出力します。 
7. すべての連隊の結果を印刷します。 

機能する理由: 考えられるすべての回転を生成し、それぞれの組み合わせをチェックすることで、正方形の構成が可能であればそれを見つけることが保証されます。 二乗距離を使用すると、浮動小数点エラーのない正確さが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from itertools import product
from collections import Counter

def rotate_90(x, y, a, b):
    # rotate (x, y) 90 degrees CCW around (a, b)
    return a - (y - b), b + (x - a)

def is_square(points):
    dists = []
    for i in range(4):
        for j in range(i+1, 4):
            dx = points[i][0] - points[j][0]
            dy = points[i][1] - points[j][1]
            dists.append(dx*dx + dy*dy)
    count = Counter(dists)
    if len(count) != 2:
        return False
    side, diag = sorted(count.keys())
    return count[side] == 4 and count[diag] == 2 and side > 0

n = int(input())
results = []
for _ in range(n):
    moles = [tuple(map(int, input().split())) for _ in range(4)]
    rotations = []
    for x, y, a, b in moles:
        r = [(x, y)]
        for _ in range(3):
            x, y = rotate_90(x, y, a, b)
            r.append((x, y))
        rotations.append(r)
    min_moves = float('inf')
    for choice in product(range(4), repeat=4):
        points = [rotations[i][choice[i]] for i in range(4)]
        moves = sum(choice)
        if is_square(points):
            min_moves = min(min_moves, moves)
    results.append(-1 if min_moves == float('inf') else min_moves)

for res in results:
    print(res)
```このコードは、まず各ほくろのすべての可能な位置を計算し、次に次を使用してすべての回転の組み合わせを反復します。`itertools.product`。 の`is_square`関数は、二乗距離を操作することで、精度エラーなしで正方形を正しく識別できるようにします。 最小の移動は連隊ごとに追跡され、構成が機能しない場合は -1 が返されます。 

## 実用的な例

 最初のサンプル入力では、最初の連隊の回転により可能な位置が生成されます。 

| ほくろ | 0° | 90° | 180° | 270° |
 | --- | --- | --- | --- | --- |
 | 1 | (1,1) | (-1,1) | (-1,-1) | (1,-1) |
 | 2 | (-1,1) | (-1,-1) | (1,-1) | (1,1) |
 | 3 | (-1,1) | (-1,-1) | (1,-1) | (1,1) |
 | 4 | (1,-1) | (1,1) | (-1,1) | (-1,-1) |

 組み合わせを試してみると`(0,1,0,0)`ポイントが得られます`(1,1)`、`(-1,-1)`、`(-1,1)`、`(1,-1)`、正方形を形成します。 手数の合計は 1 です。どの組み合わせでも手数が少なくなります。 

2 番目の連隊の場合、回転を組み合わせても正方形を形成する 4 つの点は生成されません。 すべての試行で共線点またはゼロエリア構成が生成されるため、出力は -1 になります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n * 4^4) | 各連隊ごとに、4 つのローテーション選択肢を持つ 4 つのモル → 256 の組み合わせ。 n ≤ 100 なので、256*100 = 25,600 チェックになります。 距離の確認には、組み合わせごとに O(6) かかります。 |
 | スペース | 連隊あたり O(4*4) | モルごとに 4 つの位置を 4 回転分保存します。 一定の追加メモリ。 |

 合計操作数が少なく (約 25k)、データ構造が小さいことを考慮すると、これは 1 秒の制限時間内に十分収まります。 

## テストケース```python
import sys, io
def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline
    from itertools import product
    from collections import Counter

    def rotate_90(x, y, a, b):
        return a - (y - b), b + (x - a)

    def is_square(points):
        dists = []
        for i in range(4):
            for j in range(i+1, 4):
                dx = points[i][0] - points[j][0]
                dy = points[i][1] - points[j][1]
                dists.append(dx*dx + dy*dy)
        count = Counter(dists)
        if len(count) != 2:
            return False
        side, diag = sorted(count.keys())
        return count[side] == 4 and count[diag] == 2 and side > 0

    n = int(input())
    results = []
    for _ in range(n):
        moles = [tuple(map(int, input().split())) for _ in range(4)]
        rotations = []
        for x, y, a, b in moles:
            r = [(x, y)]
            for _ in range(3):
                x, y = rotate_90(x, y, a, b)
                r.append((x, y))
            rotations.append(r)
        min_moves = float('inf')
        for choice in product(range(4), repeat=4):
            points = [rotations[i][choice[i]] for i in range(4)]
            moves = sum(choice)
            if is_square(points):
                min_moves = min(min_moves, moves)
        results.append(-1 if min_moves == float('inf') else min_moves)

    return "\n".join(map(str, results))

# Provided sample
assert run("""4
1 1 0 0
-1 1 0 0
-1 1 0
```
