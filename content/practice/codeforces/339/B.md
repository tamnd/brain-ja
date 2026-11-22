---
title: "CF 339B - クセニアと環状道路"
description: "環状道路に沿って時計回りに n 軒の家が配置された円形の都市があります。 各家には 1 から n までの固有の番号があり、交通は時計回りにのみ流れます。 Xenia はハウス 1 から開始し、特定の順序で完了する m 個のタスクがあり、各タスクはハウス番号 a[i] に配置されます。"
date: "2026-06-06T17:08:15+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 339
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 197 (Div. 2)"
rating: 1000
weight: 339
solve_time_s: 115
verified: false
draft: false
---

[CF 339B - クセニアと環状道路](https://codeforces.com/problemset/problem/339/B)

 **評価:** 1000
 **タグ:** 実装
 **解決時間:** 1 分 55 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちは円形の都市を持っています`n`環状道路に沿って時計回りに住宅が並んでいます。 各家には 1 から 1 までの固有の番号が付いています。`n`、トラフィックは時計回りにのみ流れます。 Xenia はハウス 1 から開始し、`m`特定の順序で完了するタスク。各タスクは番地にあります`a[i]`。 ある家から時計回りにすぐ隣の家まで移動するのにかかる時間は 1 ユニットです。 目標は、Xenia がすべてのタスクを指定された順序で完了するために移動に費やす合計時間を計算することです。 

入力は 2 つの整数で構成されます`n`そして`m`、 に続く`m`Xenia が訪問しなければならない家の順序を表す整数。 出力は単一の整数、つまりすべてのタスクを完了するまでの合計時間です。 

制約を考慮すると (`2 ≤ n ≤ 10^5`そして`1 ≤ m ≤ 10^5`) と 2 秒の時間制限があるため、アルゴリズムはほぼ直線的な時間内で実行する必要があります。 ネストされたループまたは二次アプローチは、最大で次のパフォーマンスを発揮します。`10^10`最悪の場合、動作が遅すぎます。 それぞれの動きは一定時間で計算できるため、`O(m)`解決。 

明らかではないエッジケースには、次のタスクが現在のタスクよりも小さい番号の家で行われるシナリオが含まれます。 たとえば、Xenia が 4 つのハウスからなるリングのハウス 3 にいて、次のタスクがハウス 2 にある場合、彼女は 2 に到達する前にハウス 4、次にハウス 1 を通過する必要があります。インデックスを減算するだけの単純なアプローチでは、誤ってマイナスの移動時間が与えられてしまいます。 もう 1 つのエッジ ケースは、同じ家での連続したタスクであり、追加時間はかかりません。 たとえば、`n = 5`、タスク`[3, 3, 2]`, the time to reach the second task at 3 is zero, then she moves from 3 to 2 along the ring, taking 4 units, not 2.

 ## アプローチ

 The brute-force approach would simulate Xenia moving from house to house step by step along the ringroad. For each task, we would increment a counter for each house traversed. While this is conceptually correct, the worst-case scenario occurs when Xenia has to traverse almost the entire ring for each task, leading to`O(m * n)`オペレーション、それは周りにあります`10^10`最大限の制約があり、遅すぎます。 

重要な観察は、一方通行の環状道路に沿って移動すると、各ステップをシミュレートするのではなく、単純な算術を使用して 2 つの家の間の移動時間を計算できることです。 ゼニアが家にいたら`current`そして到達する必要がある`next`、リングに沿った距離は`next - current`もし`next >= current`、 または`n - (current - next)`もし`next < current`。 これはリングの周りに巻き付けるためのものです。 これにより、シミュレーションがタスク リスト上の単一の線形パスに変換され、タスクごとに一定時間で距離が計算されます。 

これらの距離を合計すると、合計時間を計算できます。`O(m)`現在の家と合計時間を追跡する変数以外に余分なスペースはありません。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート フォース (段階的にシミュレーション) | O(m * n) | お(1) | 遅すぎる |
 | 最適 (距離を算術的に計算) | O(m) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 1.初期化`current`1 に、Xenia の開始ハウスを表し、`total_time`0まで。追跡します`current`なぜなら、次の仕事はクセニアが現在いる家によって決まるからです。 
2. 各タスクを反復処理する`task`指定された順序で。 それぞれについて`task`、から移動する時間を決定します。`current`に`task`。 もし`task >= current`、距離は`task - current`。 それ以外の場合、彼女はリングを一周する必要があるため、距離は次のようになります。`n - (current - task)`。 この距離を加算すると、`total_time`。 
3.アップデート`current`に`task`Xenia の新しい場所を反映します。 
4. すべてのタスクを処理した後、印刷します。`total_time`。 

なぜ機能するのか: 維持される不変条件は次のとおりです`current`次のタスクに進む前に、Xenia の現在の家を常に正確に反映します。 距離の計算では、次のタスクが家屋の番号付けに関して先である場合と後である場合を区別することにより、道路の円形の性質が正しく処理されます。 これらの距離を順番に合計すると、すべての移動時間が正確に 1 回だけ計算されることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n, m = map(int, input().split())
tasks = list(map(int, input().split()))

current = 1
total_time = 0

for task in tasks:
    if task >= current:
        total_time += task - current
    else:
        total_time += n - (current - task)
    current = task

print(total_time)
```最初の行には都市の規模とタスクの数が表示されます。 2 行目は一連のタスクを読み取ります。 初期化する`current`1、開始ハウス、および`total_time`ループはタスクを繰り返し、タスク ハウス番号が先行しているか、ラップアラウンドが必要かに応じて距離を計算します。 更新中`current`次の反復で正しい開始点が使用されるようにします。 このアプローチでは、家番号が 1 から始まるため、off-by-one エラーが回避されます。 

## 実用的な例

 **サンプル 1**

 入力:```
4 3
3 2 3
```| タスク | 現在 | 距離 | 合計時間 |
 | --- | --- | --- | --- |
 | 3 | 1 | 2 | 2 |
 | 2 | 3 | 3 | 5 |
 | 3 | 2 | 1 | 6 |

 クセニアは1→2→3（2ユニット）と移動し、3→4→1→2（3ユニット）を周回し、2→3（1ユニット）となります。 合計時間は6です。 

**サンプル 2**

 入力:```
5 4
2 2 4 1
```| タスク | 現在 | 距離 | 合計時間 |
 | --- | --- | --- | --- |
 | 2 | 1 | 1 | 1 |
 | 2 | 2 | 0 | 1 |
 | 4 | 2 | 2 | 3 |
 | 1 | 4 | 2 | 5 |

 これは、同じハウス内の連続タスクに時間がかからず、4→1 のラップアラウンド ロジックが正しく処理されることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(m) | 各タスクは定数時間演算で 1 回処理されます。 |
 | スペース | O(m) | タスクリストを保存します。 追加の構造は必要ありません。 |

 これは制約内に快適に適合します。`m`そして`n`最大 10^5 であり、それぞれの算術演算は簡単です。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n, m = map(int, input().split())
    tasks = list(map(int, input().split()))
    current = 1
    total_time = 0
    for task in tasks:
        if task >= current:
            total_time += task - current
        else:
            total_time += n - (current - task)
        current = task
    return str(total_time)

# provided sample
assert run("4 3\n3 2 3\n") == "6", "sample 1"

# custom tests
assert run("5 4\n2 2 4 1\n") == "5", "consecutive tasks & wrap"
assert run("2 5\n1 2 1 2 1\n") == "5", "small n, alternating"
assert run("10 1\n10\n") == "9", "single task at last house"
assert run("3 3\n3 3 3\n") == "2", "all tasks same house"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 5 4\n2 2 4 1 | 5 | 連続タスクとラップアラウンド ロジック |
 | 2 5\n1 2 1 2 1 | 5 | 小さなリング、交互のタスク、完全なトラバース |
 | 10 1\n10 | 9 | 最後の家のシングルタスク |
 | 3 3\n3 3 3 | 2 | 同じ家の中で繰り返されるタスク、繰り返しの距離はゼロ |

 ## 特殊なケース

 リング全体でタスクが降順に並んでいる場合、アルゴリズムは正しくラップされます。 入力`n=4, tasks=[4,1,2]`結果として`3+1+1=5`単位: 1→2→3→4 (3 単位)、ラップ 4→1 (1 単位)、1→2 (1 単位)。 同じ家の中で繰り返されるタスクの場合、距離はゼロになります。例:`n=5, tasks=[2,2]`収量`1`ユニット。 どちらの場合も、算術距離計算により、各家を段階的にシミュレートすることなく、正確な移動時間が保証されます。
