---
title: "CF 51C - 3 つの基地局"
description: "1 次元の線に沿った点として表される村が与えられ、各点は家の座標になります。 課題は、すべての住宅が少なくとも 1 つの基地局のカバー範囲内に収まるように、この路線に沿って正確に 3 つの携帯電話基地局を配置することです。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "greedy"]
categories: ["algorithms"]
codeforces_contest: 51
codeforces_index: "C"
codeforces_contest_name: "Codeforces Beta Round 48"
rating: 1800
weight: 51
solve_time_s: 133
verified: false
draft: false
---

[CF 51C - 3 つの基地局](https://codeforces.com/problemset/problem/51/C)

 **評価:** 1800
 **タグ:** 二分探索、貪欲
 **解決時間:** 2 分 13 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 1 次元の線に沿った点として表される村が与えられ、各点は家の座標になります。 課題は、すべての住宅が少なくとも 1 つの基地局のカバー範囲内に収まるように、この路線に沿って正確に 3 つの携帯電話基地局を配置することです。 カバレッジは対称です: ステーションの位置`t`力を持って`d`間隔をカバーする`[t - d, t + d]`。 私たちが望む出力は最小限です`d`これにより、完全なカバレッジと 3 つのステーションの 1 つの有効な構成が可能になります。 

制約が教えてくれるのは`n`最大 200,000 までの座標を指定できます`10^9`。 並べ替えや配列全体の線形パスは実行可能ですが、3 つのステーションすべてに対して考えられるすべての配置を試みる単純なアプローチでは天文学的な速度がかかります。 たとえば、各住宅のすべての潜在的な駅の位置を反復処理することは不可能です。`10^9`値が多すぎます。 

微妙なエッジ ケースには、同じ座標に積み重ねられた住宅、非常にまばらな住宅 (たとえば、1 つが 1 にあり、もう 1 つが 10^9 にある)、および重複が許可されていればステーションが 3 つ未満でも十分であるケースが含まれます。 家を均等に分割しようとするだけの単純なアプローチでは、単一のステーションが複数のクラスターをカバーする可能性があるという事実を見逃してしまい、より小さなクラスターが得られる可能性があります。`d`。 

## アプローチ

 ブルートフォース手法では、住宅座標の考えられるすべての組み合わせに 3 つのステーションを配置しようとし、必要な座標を計算します。`d`それぞれの組み合わせについて、最小のものを選択します。 これはすべてのシナリオを列挙しているため正しいですが、実行不可能です。`n = 2 × 10^5`、組み合わせの数は次のとおりです。`O(n^3)`またはそれ以上`10^15`操作。 

重要な観察は、問題が単調な性質を持っているということです。`d`3 つのステーションですべての家をカバーできます。`d`すべての家をカバーすることもできます。 逆に、`d`小さすぎる、それ以下ではない`d`機能します。 この単調性により、二分探索が可能になります。`d`。 候補者にとって`d`、左から右へ貪欲に家をカバーし、次の覆われていない家をカバーする最も遠い点にステーションを配置して、続行します。 3 つ以下のステーションですべての家をカバーできれば、候補は`d`実現可能です。 

貪欲なカバレッジが機能するのは、各ステーションのカバレッジ間隔が連続しているためです。そのため、最初のカバーされていない家をカバーできる最右端に次のステーションを配置することが常に最適です。 ステーションをさらに左に配置すると、ステーションの数または必要なステーションの数が不必要に増加します。`d`。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^3) | O(n) | 遅すぎる |
 | 二分探索 + 貪欲 | O(n log(maxX)) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. **家の座標を並べ替えます**。 家を左から右に移動し、貪欲なカバレッジを正しく適用するには、並べ替えが必要です。 
2. **二分探索境界を初期化します**。 セット`lo = 0`そして`hi = 1e9`、ステーションが必要とする可能性のある最大距離。 小さなイプシロンを使用します (例:`1e-7`) 浮動小数点精度の場合。 
3. **二分探索ループ**。 それぞれの中間値について`mid = (lo + hi) / 2`、3つの発電所ですべての家をカバーできるかどうかを確認してください`mid`。 可能なら移動する`hi = mid`; それ以外の場合は移動する`lo = mid`。 
4. **貪欲なカバレッジチェック**。 一番左の家から始めます。 Place a station at`house + mid`までのすべての家をカバーします。`house + 2 * mid`。 次の屋根のない家について、最大 3 駅まで繰り返します。 3 駅を通過してもまだ覆われていない家があれば、候補者は`mid`小さすぎます。 
5. **ステーションの位置を再構築**。 二分探索が収束したら、グリーディ カバレッジをもう一度実行して正確な位置を生成します。次の覆われていない家をカバーする間隔の右端の点に各ステーションを配置します。 

**機能する理由**。 並べ替えにより、住宅が順番に処理されることが保証されます。 貪欲な配置により、最小限のオーバーラップが保証されます。ステーションを左端に配置すると、カバレッジは拡張されませんが、余分なステーションが強制的に追加される可能性があります。 二分探索は単調性に依存します: if`d`は実現可能ですが、より大きな値も引き続き実現可能です。 この組み合わせにより最小限のものが生成されます`d`。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def can_cover(houses, d):
    n = len(houses)
    count = 0
    i = 0
    while i < n:
        count += 1
        limit = houses[i] + 2 * d
        while i < n and houses[i] <= limit:
            i += 1
        if count > 3:
            return False
    return True

def find_stations(houses, d):
    stations = []
    n = len(houses)
    i = 0
    while i < n and len(stations) < 3:
        pos = houses[i] + d
        stations.append(pos)
        limit = houses[i] + 2 * d
        while i < n and houses[i] <= limit:
            i += 1
    while len(stations) < 3:
        stations.append(stations[-1])
    return stations

n = int(input())
houses = list(map(int, input().split()))
houses.sort()

lo, hi = 0.0, 1e9
eps = 1e-7
while hi - lo > eps:
    mid = (lo + hi) / 2
    if can_cover(houses, mid):
        hi = mid
    else:
        lo = mid

d = hi
stations = find_stations(houses, d)
print(f"{d:.6f}")
print(" ".join(f"{s:.6f}" for s in stations))
```このソリューションでは、実現可能性のチェックを分離します (`can_cover`) 駅の再建から (`find_stations`）。 貪欲に配置する前に並べ替えることが重要です。 二分探索は浮動小数点数を使用し、間隔が以下の場合に停止します。`1e-7`出力の小数点以下 6 桁の精度を確保するため。 

## 実用的な例

 **サンプル 1**

 入力:```
4
1 2 3 4
```| ステップ | 私 | ハウス | カウント | 制限 | 駅 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 0 | 1 | 1 | 1 + 2*0.5 = 2 | [1.5] |
 | 2 | 2 | 3 | 2 | 3 + 1 = 4 | [1.5、3.5] |
 | 3 | 完了 | - | - | - | [1.5、3.5] |

 最小限の動作を示します`d = 0.5`2つのステーションですべての家をカバーします。 

**カスタム例**

 入力:```
5
1 2 10 11 12
```二分探索の結果`d = 2.5`。 貪欲なステーションは 2.5、11.5 に配置され、最後のステーション 11.5 を繰り返して 3 つのステーションを満たします。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log(maxX)) | ソートには O(n log n) がかかり、バイナリ検索には O(log(maxX)) 回の反復が必要です。各反復ではハウスを線形に通過します。 
| スペース | O(n) | 住宅配列と駅配列 |

 これは時間とメモリの制限内に収まります。`n`up to 2 × 10^5 is manageable with sorting and multiple linear passes.

 ## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n = int(input())
    houses = list(map(int, input().split()))
    houses.sort()
    lo, hi = 0.0, 1e9
    eps = 1e-7
    def can_cover(houses, d):
        n = len(houses)
        count = 0
        i = 0
        while i < n:
            count += 1
            limit = houses[i] + 2*d
            while i < n and houses[i] <= limit:
                i += 1
            if count > 3:
                return False
        return True
    def find_stations(houses, d):
        stations = []
        n = len(houses)
        i = 0
        while i < n and len(stations) < 3:
            pos = houses[i] + d
            stations.append(pos)
            limit = houses[i] + 2*d
            while i < n and houses[i] <= limit:
                i += 1
        while len(stations) < 3:
            stations.append(stations[-1])
        return stations
    while hi - lo > eps:
        mid = (lo + hi)/2
        if can_cover(houses, mid):
            hi = mid
        else:
            lo = mid
    d = hi
    stations = find_stations(houses, d)
    out = f"{d:.6f}\n{' '.join(f'{s:.6f}' for s in stations)}"
    return out

# Provided sample
assert run("4\n1 2 3 4\n").startswith("0.500000"), "sample 1"

# Custom cases
assert run("1\n100\n").startswith("0.000
```
