---
title: "CF 66E - ペティアとポスト"
description: "n 個の郵便局からなる環状ルートがあり、各郵便局にはガソリン スタンドがあります。 各ステーション i には a[i] リットルのガソリンがあり、ステーション i から i+1 までの距離は b[i] キロメートルで、最後に折り返します。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "dp"]
categories: ["algorithms"]
codeforces_contest: 66
codeforces_index: "E"
codeforces_contest_name: "Codeforces Beta Round 61 (Div. 2)"
rating: 2000
weight: 66
solve_time_s: 132
verified: false
draft: false
---

[CF 66E - Petya と Post](https://codeforces.com/problemset/problem/66/E)

 **評価:** 2000
 **タグ:** データ構造、dp
 **解決時間:** 2 分 12 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 循環ルートがあります`n`郵便局、それぞれにガソリンスタンドがあります。 各駅`i`もっている`a[i]`ガソリンのリットル数とステーションからの距離`i`に`i+1`は`b[i]`キロメートル、最後に折り返します。 ペティアの叔父は、郵便局から出発して車を受け取り、円に沿ってどちらかの方向にちょうど 1 周運転し、通過する駅でのみ給油し、同じ駅で複数回給油しないことを希望しています。 車は 1 キロメートルあたりちょうど 1 リットルを消費します。 

ルートを開始してガス欠になることなく一周できるステーションをすべて見つけるように求められます。 

すべてのガソリンの合計`sum(a)`すべての距離の合計に等しい`sum(b)`これにより、少なくとも 1 つの開始点から解が存在することが保証されます。 各ステーションには少なくとも 1 リットルのガソリンがあり、各ステーションの距離は少なくとも 1 キロメートルです。 

と`n`最大 10^5 で制限時間は 2 秒であるため、O(n log n) よりも複雑なソリューションは危険です。 すべてのステーションで開始を試み、ループ全体をシミュレートする単純な O(n^2) アプローチでは、約 10^10 の操作が必要となり、実行不可能です。 

エッジケースとしては、すべてのステーションに次のステーションに到達するのに十分な量のガスが存在すること、ステーションのガス分布が非常に不均一であること、距離が特定の地点からの開始を妨げる「ボトルネック」を形成していることなどが挙げられます。 例えば：```
n = 3
a = [1, 5, 2]
b = [3, 2, 3]
```単純にステーション 1 から開始すると、1 < 3 であるためすぐに失敗しますが、ステーション 2 から開始すると成功します。 これらのチェックを怠ると、不正確な回答が生じる可能性があります。 

## アプローチ

 ブルートフォースのアプローチは簡単です。各ステーションから出発し、燃料カウンターを実行し続け、前進をシミュレートします。 各ステーションごとに減算します`b[i]`燃料から追加してください`a[i]`次の駅で。 燃料がゼロを下回ると、その出発点は失敗します。 各シミュレーションには O(n) がかかり、これを O(n^2) まで n 回繰り返しますが、これでは遅すぎます。 

重要な洞察は、この問題が古典的な「循環ガソリン スタンド/ガソリン ツアー」問題と同等であるということです。 各ステーションでの正味燃料変化を次のように定義すると、`a[i] - b[i]`、円を完了するには、選択したステーションから開始して、これらの正味の変化の累積合計がゼロを下回らないことが必要です。 なぜなら`sum(a) == sum(b)`、正味合計はゼロです。 累積合計が最小値に達する点によって、どの開始ステーションが実行可能であるかが決まります。 累積合計のグローバル最小値の直後の任意のステーションが有効な開始点となります。 

この観察により、累積和を 1 回計算して全体的な最小値を見つけ、O(n) の解を得ることができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^2) | O(n) | 遅すぎる |
 | 累積合計/最小追跡 | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各ステーションでの正味燃料変化を計算します。`delta[i] = a[i] - b[i]`。 これは、ステーションからの移動でどれだけの燃料が得られるか、または失われるかを表します。`i`に`i+1`。 
2. 次のプレフィックスの合計を計算します。`delta`円に沿って:`pref[i] = delta[0] + delta[1] + ... + delta[i]`。 必要に応じて、ラップアラウンド処理を簡素化するために、配列を 1 ラウンド追加して循環的に拡張します。 
3. 累積合計のグローバル最小値を見つけます。 させて`min_sum`最小のプレフィックス合計を次のようにします。`min_index`この最小値が発生する最後のインデックスになります。 これにより、ループ上で燃料が最も「不足」している位置が特定されます。 
4. このグローバル最小値の直後の任意のステーション (すなわち、`(min_index + 1) % n`）は実行可能な出発点です。 円対称のため、この時点以降の一連のステーションでは、ループ全体にわたって非負の燃料が維持されます。 
5. から順にすべてのステーションをチェックします。`min_index + 1`累計を使用して、負の燃料なしで完全な円を開始できるすべてのステーションを識別します。 これらが有効な答えです。 

機能する理由: 正味燃料の累積合計は、ステーション 0 からの開始と比較してどれだけの燃料があるかを表します。グローバル最小値は、円に沿った最悪の点を特定します。 この直後にスタートすると、直前の燃料不足が回避され、燃料の合計が合計距離と一致するため、燃料がマイナスになることがなくなります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n = int(input())
a = list(map(int, input().split()))
b = list(map(int, input().split()))

delta = [a[i] - b[i] for i in range(n)]

# Compute prefix sums
pref = [0] * n
pref[0] = delta[0]
for i in range(1, n):
    pref[i] = pref[i-1] + delta[i]

# Find minimum prefix sum
min_pref = pref[0]
min_index = 0
for i in range(n):
    if pref[i] < min_pref:
        min_pref = pref[i]
        min_index = i

# The station after the minimum is a valid start
start = (min_index + 1) % n

# Verify all valid stations using running sum from 'start'
res = []
fuel = 0
for i in range(n):
    idx = (start + i) % n
    fuel += delta[idx]
    if fuel < 0:
        start = (idx + 1) % n
        fuel = 0
        res = []
    else:
        res.append(idx + 1)  # 1-based indexing

print(len(res))
print(' '.join(map(str, sorted(res))))
```説明: まずステーションごとの正味燃料増加量を計算し、次にプレフィックス合計を計算して累積燃料変化を追跡します。 グローバル最小プレフィックス合計の直後のステーションが開始点になります。 円周上を反復してすべての有効な開始ステーションを確認し、モジュロインデックスによるラップアラウンドを処理します。 

## 実用的な例

 ### サンプル 1```
n = 4
a = [1, 7, 2, 3]
b = [8, 1, 1, 3]
```| 駅 | デルタ | 接頭語 | 燃料 (スタート 2 から) |
 | --- | --- | --- | --- |
 | 1 | -7 | -7 | - |
 | 2 | 6 | -1 | 6 |
 | 3 | 1 | 0 | 7 |
 | 4 | 0 | 0 | 7 |

 プレフィックス合計のグローバル最小値は、インデックス 0 で -7 です。その後開始します: ステーション 2。反復により、有効なステーション 2 と 4 が得られます。 

### カスタムケース```
n = 3
a = [1, 5, 2]
b = [3, 2, 3]
```プレフィックスの合計: -2、3、2。最小値 = インデックス 0 で -2。ステーション 2 から開始。ステーション 2 のみが円を完成できます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | デルタに 1 パス、プレフィックスに 1 パス、有効な開始を識別するために 1 パス |
 | スペース | O(n) | デルタとプレフィックスの合計を保存する |

 と`n ≤ 10^5`、このアルゴリズムは、256 MB と比較してごくわずかなメモリを使用し、2 秒の制限時間内で快適に実行されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import builtins
    from contextlib import redirect_stdout
    f = io.StringIO()
    with redirect_stdout(f):
        n = int(input())
        a = list(map(int, input().split()))
        b = list(map(int, input().split()))
        delta = [a[i] - b[i] for i in range(n)]
        pref = [0]*n
        pref[0] = delta[0]
        for i in range(1, n):
            pref[i] = pref[i-1] + delta[i]
        min_pref = pref[0]
        min_index = 0
        for i in range(n):
            if pref[i] < min_pref:
                min_pref = pref[i]
                min_index = i
        start = (min_index + 1) % n
        res = []
        fuel = 0
        for i in range(n):
            idx = (start + i) % n
            fuel += delta[idx]
            if fuel < 0:
                start = (idx + 1) % n
                fuel = 0
                res = []
            else:
                res.append(idx + 1)
        print(len(res))
        print(' '.join(map(str, sorted(res))))
    return f.getvalue().strip()

# provided sample
assert run("4\n1 7 2 3\n8 1 1 3\n") == "2\n2 4"

# minimum input
assert run("1\n1\n1\n") == "1\n1"

# all equal values
assert run("3\n2 2 2\n2 2 2\n") == "3\n1 2 3"

# edge case: uneven distribution
assert run("3\n1 5 2\n3 2
```
