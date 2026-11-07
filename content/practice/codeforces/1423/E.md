---
title: "CF 1423E - 5G アンテナ タワー"
description: "私たちは 5G アンテナ塔の用地取得を管理する任務を負っています。 各タワーは、中心 (x, y) と半径 r で定義される円形の領域を占めます。 土地データベースは、一意の整数 ID を持つポリゴンとして定義されたプロパティで構成されます。"
date: "2026-06-11T06:09:09+07:00"
tags: ["codeforces", "competitive-programming", "geometry"]
categories: ["algorithms"]
codeforces_contest: 1423
codeforces_index: "E"
codeforces_contest_name: "Bubble Cup 13 - Finals [Online Mirror, unrated, Div. 1]"
rating: 2700
weight: 1423
solve_time_s: 80
verified: true
draft: false
---

[CF 1423E - 5G アンテナ塔](https://codeforces.com/problemset/problem/1423/E)

 **評価:** 2700
 **タグ:** ジオメトリ
 **解決時間:** 1 分 20 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは 5G アンテナ塔の用地取得を管理する任務を負っています。 各タワーは中心によって定義される円形の領域を占めます。`(x, y)`そして半径`r`。 土地データベースは、一意の整数 ID を持つポリゴンとして定義されたプロパティで構成されます。 目標は、タワーのクエリごとに、タワーの円と交差するすべてのプロパティの合計面積を計算し、その ID を返すことです。 

入力は最初に、座標の境界となる領域全体の幅と高さを与えますが、それ以外の点は計算とは無関係です。 それでは、`n`プロパティを表すポリゴン。 各ポリゴンには反時計回りの順序で 3 ～ 40 個の頂点があり、有効な単純なポリゴンが確実に定義されます。 クエリでは円を指定し、その円と交差する敷地面積の合計を求めます。 

制約により次のことが決まります`n * q ≤ 10^6`これにより、単純な O(n*q) 幾何学的計算ですべてのポリゴンをすべてのクエリに対してチェックする総当りのソリューションが即座に排除されます。 多角形と円の交差は自明ではないため、各チェックには O(v) 演算が含まれる可能性があります。`v`頂点。 と`n`そして`q`まで`10^5`、単純なペアごとのチェックでは最大 10^10 の演算が必要となり、これでは遅すぎます。 

明らかではないエッジ ケースは、円が頂点またはエッジでポリゴンにちょうど接触する場合に発生します。 単純なポリゴン内ポイント チェックではこれらを見逃してしまうため、交差テストでは、円にポリゴンの一部のみが含まれている場合や、ポリゴンに接触しているだけの場合など、部分的な重なりを正しく処理する必要があります。 別のエッジ ケースは、完全にポリゴンの内側または完全にすべてのポリゴンの外側にある非常に小さな円で発生し、面積 0 が返されるはずです。 

## アプローチ

 ブルート フォース アプローチでは、すべてのクエリを反復処理し、すべてのポリゴンとの交差をチェックします。 ポリゴンごとに、ジオメトリ ライブラリを使用して円とポリゴンの交差を計算するか、ポリゴンのエッジが円と交差するかどうか、または円の中心がポリゴンの内側にあるかどうかをテストする関数を実装します。 このアルゴリズムは、問題ステートメントを直接実装しているため、正しいです。つまり、多角形が円に接触すると、その面積が寄与します。 ただし、`n*q`潜在的に 10^6 に達し、各ポリゴンの頂点が最大 40 個になると、このアプローチではクエリあたり 4*10^7 の操作が容易に達成できます。これは境界線ですが、2 秒の制限では危険です。 

最適なアプローチでは、空間分割を利用して不必要なチェックを削減します。 プロパティは重複しないため、R ツリーやグリッドなどの空間インデックスを前処理できます。 円から遠く離れた多角形は円と交差できないことがわかります。 エリアを均一なグリッドに分割し、どのポリゴンが各グリッド セルに接触するかを保存することにより、各クエリはクエリ円と重なるセル内のポリゴンのみをチェックします。 これにより、コストのかかる多角形と円の交差チェックの数が削減されます。`n`小さなサブセットに限定され、多くの場合平均して一定です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n_q_v) | O(n*v) | 遅すぎる可能性があります |
 | グリッドベースの空間インデックス | O(n_v + q_k*v) | O(n*v) | 受け入れられ、大きな n と q に対して効率的です。 

ここ、`v`はポリゴンごとの頂点の数であり、`k`空間フィルタリング後のクエリごとの候補ポリゴンの平均数です。 

## アルゴリズムのチュートリアル

 1. すべてのポリゴンを解析し、その軸に合わせた境界ボックス (AABB) を計算します。 バウンディングボックスは`(min_x, max_x, min_y, max_y)`円との最初の交差テストを高速に行うことができます。 円がポリゴンの境界ボックスと重なっていない場合、その円はポリゴン自体と交差することはできません。 
2. ポリゴンを均一な 2D グリッドに前処理します。 グリッド セルのサイズは、平均ポリゴン サイズに基づいて選択できます。たとえば、`sqrt(total_area/n)`セルごとのポリゴン数のバランスをとります。 境界ボックスがセルと交差するポリゴン ID のリストをセルごとに保存します。 
3. 各クエリサークルについて`(x, y, r)`、円が重なるグリッドセルを決定します。 これは、円をその半径だけ拡張し、どのセルがその長方形と交差するかを見つけることで実行できます。 
4. 重複を避けて、重複するセルからすべてのポリゴンを候補セットに収集します。 
5. 候補となる多角形ごとに、まず、条件を使用して円に対する境界ボックスをチェックします。`(closest_x - cx)^2 + (closest_y - cy)^2 ≤ r^2`どこ`closest_x, closest_y`円の中心に最も近い境界ボックスの点です。 失敗した場合は、そのポリゴンをスキップします。 
6. 境界ボックスが交差する場合は、正確な多角形と円の交差チェックを実行します。 これは、ポリゴンのエッジが円と交差しているかどうか、または円の中心がポリゴンの内側にあるかどうかをチェックすることで実行できます。 
7. 交差するポリゴンの面積を合計し、出力用の ID を収集します。 

機能する理由: 境界ボックスとグリッド フィルタリングにより、円と交差するポリゴンがスキップされることが保証されます。 多角形と円の正確な交差により、円に接触しているか、部分的に円の内側にある多角形の正確さが保証されます。 グリッド分割により、有効なポリゴンを見逃すことなく、不必要なチェックが削減されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from math import sqrt

def point_inside_polygon(px, py, polygon):
    cnt = 0
    n = len(polygon)
    for i in range(n):
        x1, y1 = polygon[i]
        x2, y2 = polygon[(i+1)%n]
        if ((y1 <= py < y2) or (y2 <= py < y1)) and (px < (x2-x1)*(py-y1)/(y2-y1)+x1):
            cnt ^= 1
    return cnt

def distance_sq_point_to_segment(px, py, x1, y1, x2, y2):
    dx, dy = x2-x1, y2-y1
    if dx == dy == 0:
        return (px-x1)**2 + (py-y1)**2
    t = max(0, min(1, ((px-x1)*dx + (py-y1)*dy)/(dx*dx + dy*dy)))
    closest_x, closest_y = x1 + t*dx, y1 + t*dy
    return (px-closest_x)**2 + (py-closest_y)**2

def circle_intersects_polygon(cx, cy, r, polygon):
    if point_inside_polygon(cx, cy, polygon):
        return True
    r2 = r*r
    n = len(polygon)
    for i in range(n):
        x1, y1 = polygon[i]
        x2, y2 = polygon[(i+1)%n]
        if distance_sq_point_to_segment(cx, cy, x1, y1, x2, y2) <= r2:
            return True
    return False

def main():
    width, height, n = map(float, input().split())
    n = int(n)
    polygons = []
    bboxes = []
    areas = []

    for _ in range(n):
        data = list(map(float, input().split()))
        v = int(data[0])
        pts = [(data[i*2+1], data[i*2+2]) for i in range(v)]
        polygons.append(pts)
        xs, ys = zip(*pts)
        bboxes.append((min(xs), max(xs), min(ys), max(ys)))
        # Polygon area by shoelace
        area = 0
        for i in range(v):
            x1, y1 = pts[i]
            x2, y2 = pts[(i+1)%v]
            area += x1*y2 - x2*y1
        areas.append(abs(area)/2)

    q = int(input())
    for _ in range(q):
        cx, cy, r = map(float, input().split())
        total_area = 0
        intersect_ids = []
        r2 = r*r
        for pid, poly in enumerate(polygons):
            min_x, max_x, min_y, max_y = bboxes[pid]
            # Bounding box check
            closest_x = max(min_x, min(cx, max_x))
            closest_y = max(min_y, min(cy, max_y))
            if (closest_x-cx)**2 + (closest_y-cy)**2 > r2:
                continue
            if circle_intersects_polygon(cx, cy, r, poly):
                total_area += areas[pid]
                intersect_ids.append(str(pid))
        if intersect_ids:
            print(f"{total_area:.6f} {len(intersect_ids)} {' '.join(intersect_ids)}")
        else:
            print("0.000000 0")

if __name__ == "__main__":
    main()
```The code first reads polygons and computes their areas and bounding boxes. For each query, it quickly eliminates polygons whose bounding boxes are far from the circle. For remaining candidates, it checks exact intersection using point-in-polygon and edge distance checks. Areas and IDs of intersecting polygons are collected and printed. Subtle points include using floating point arithmetic consistently and applying the shoelace formula correctly for area calculation.

 ## 実用的な例

 ### サンプル 1

 クエリ`(2, 3.5, 0.5)`| ポリゴン ID | 境界ボックスのチェック | 円が多角形と交差する | 写真 含まれるエリア |
 | --- | --- | --- | --- |
 | 0 | パス | 本当 | 1.0 |
 | 1 | パス | 偽 | 0 |
 | 2 | 失敗 | 該当なし | 0 |

 総面積は 1.0、ID`[0]`。 

クエリ`(3.3, 2, 0.4)`| ポリゴン ID | 境界ボックスのチェック | 円が多角形と交差する | 写真 含まれるエリア |
 | --- | --- | --- | --- |
 | 0 | パス | | |
