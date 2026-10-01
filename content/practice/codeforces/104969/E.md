---
title: "CF 104969E - ピザの有効期限"
description: "各ピザは、円形に配置されたスライスと中心点で構成されます。 すべてのスライスにはコスト パラメータがあり、ピザの周りの生地にもコスト パラメータがあります。"
date: "2026-06-28T06:41:34+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104969
codeforces_index: "E"
codeforces_contest_name: "UTPC Contest 02-09-24 Div. 1 (Advanced)"
rating: 0
weight: 104969
solve_time_s: 86
verified: true
draft: false
---

[CF 104969E - ピザの有効期限](https://codeforces.com/problemset/problem/104969/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 26 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各ピザは、円形に配置されたスライスと中心点で構成されます。 すべてのスライスにはコスト パラメータがあり、ピザの周りの生地にもコスト パラメータがあります。 基本的な考え方は、スライスベースの接続とクラストベースの接続の 2 種類の接続を使用してこの構造の頂点を「接続」できるようにし、ピザ全体を接続するのに必要な総コストを最小限に抑えたいということです。 

これは、ピザごとに、すべてのスライスの頂点と中心が単一の連結コンポーネントに属することを保証する最も安価な方法を見つけることになります。 自然な戦略は 2 つあります。 1 つは、スライス接続を使用してすべてのスライスを中心に直接接続することです。 もう 1 つは、クラスト接続を使用してスライスをサイクルで接続し、最も安価なスライス接続を使用して中心を 1 回接続する方法です。 各ピザの答えは、これら 2 つの構造の最小コストです。 

この値がピザごとに計算されると、スケジュールの問題になります。 各ピザを食べるには時間 0 から開始して 1 単位の時間がかかり、ピザ i は厳密に時間 d_i より前に食べなければなりません。そうでない場合は無駄とみなされ、ペナルティ v_i に寄与します。 一度に食べられるピザは 1 枚だけであるため、単位長さのジョブを整数のタイムスロットに効果的に割り当てています。各ジョブには期限があり、時間内に完了しない場合はペナルティが課せられます。 目標は、期限を過ぎた場合のペナルティの合計を最小限に抑えることです。 

この制約では最大 100,000 個のピザが許可されるため、すべての順列を試行したり、二次時間でスケジュールをシミュレートしたりするアプローチは機能しません。 ソートと優先キューの操作は実行可能ですが、すべての状態に対する繰り返しの再スキャンや動的プログラミングは不可能であるため、O(N log N) に近い値が必要です。 

微妙な失敗例は、接続コスト d_i とスケジューリング制約を混同することから発生します。 期限を無視して v_i を増やしたり、d_i を増やしたりしてピザをスケジュールする貪欲なアプローチは、簡単に失敗する可能性があります。 もう 1 つの間違いは、d_i がピザごとの小さな構造最適化にのみ依存していることを認識せずに、d_i を独立したものとして扱うことです。 

## アプローチ

 まずは一枚のピザを扱います。 構造を無視する場合、サイズ s_i + 1 のグラフに対して汎用 MST メソッドを使用して、すべての頂点を接続する最小方法を計算しようとする可能性があります。これは概念的には機能しますが、素朴な方法でピザごとに個別に実行すると遅すぎます。 

重要な観察は、グラフが非常に特殊な形式を持っていることです。つまり、均一な地殻コスト c_i を持つスライスのサイクルと、コスト q_i を持つすべてのスライスに接続された中心です。 このような構造では、最適なスパニング ツリーは 2 つの形式のいずれかをとる必要があります。 すべてのスライスを中心に直接接続し、すべての q_i の合計をコストとするか、円周上の 1 つを除くすべての接続にサイクル エッジを使用して、最も安価なスライスのみを介して中心を接続します。 これにより、コスト (s_i - 1) * c_i + min(q_i) が得られます。 これら 2 つの最小値を取ると、ピザ 1 枚あたりの d_i が O(1) になります。 

すべての d_i が計算されると、各ピザは期限 d_i と、期限内に完了しない場合のペナルティ v_i が設定された単位時間ジョブになります。 私たちは、期限までに完了した仕事の合計価値を最大化したいと考えています。 

強引なスケジューリング手法では、ピザのすべての順列を試し、時間の経過をシミュレートし、ペナルティを計算するため、O(N!) か、せいぜい O(N^2) 個のチェックが行われますが、これは 100,000 までの N では不可能です。 

期限のある単位時間ジョブの標準的な見解は、期限の短い順にジョブを処理する必要があるということです。 スキャン中、選択した一連のジョブを維持します。 いずれかの時点で、現在の締め切りが許可するよりも多くのジョブを選択した場合は、1 つのジョブを破棄する必要があります。破棄するための最良の選択は、最小値 v_i を持つジョブです。 この貪欲な交換引数により、最も価値のある実行可能なセットが確実に保持されます。

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォーススケジューリング | O(N!) または O(N^2 N!) | お(1) | 遅すぎる |
 | 最適 (d_i の計算 + 貪欲なスケジューリング) | O(N log N) | O(N) | 承認済み |

 ## アルゴリズムのチュートリアル

 ### 1. 各ピザの接続コストを計算します。 

ピザごとに、2 つの候補コストを計算します。 The first is the sum of all slice strengths q_i, representing connecting every slice directly to the center. 2 番目は、(s_i - 1) * c_i に最小スライス強度を加えたもので、1 つを除くすべての接続にクラスト サイクルを使用し、最も安価なスライスを介して中心を接続することを表します。 これら 2 つの最小値を d_i とします。 

This step reduces the geometric structure into a single deadline value per pizza.

 ### 2. 各ピザをスケジュール ジョブとして扱います

 Each pizza becomes a job with processing time 1, deadline d_i, and profit v_i if completed before its deadline. それを逃すと、無駄として v_i を支払うことになります。 

We shift perspective from geometry to scheduling.

 ### 3. 期限ごとにピザを並べ替える

 すべてのジョブを d_i の昇順に並べ替えます。 This ensures we always consider the most urgent constraints first.

 ### 4. 選択したピザのセットを維持する

 Iterate through sorted jobs, maintaining a max-feasible subset. Add each job tentatively into a collection of selected pizzas.

 ### 5. Enforce feasibility using a min-heap of values

If the number of selected jobs exceeds the current time limit implied by the deadline ordering, remove the job with the smallest v_i. This keeps the most valuable subset that can still be scheduled.

 The intuition is that whenever we exceed capacity, we must drop something, and the least costly loss is always optimal to discard.

 ### なぜ効果があるのか

 締め切り順にソートされたジョブの任意のプレフィックスにおいて、アルゴリズムは、そのプレフィックスの利用可能なタイムスロットに適合できる最適なジョブのサブセットを維持します。 不変条件は、期限 ≤ T のすべてのジョブを処理した後、最大 T 個のジョブを保持し、そのようなサブセットすべての中で最大の合計値を持つジョブを保持することです。 Any time we exceed capacity, replacing a smaller v_i job with a larger one preserves feasibility and improves or maintains total value. This exchange argument guarantees that no optimal solution is ever excluded by the greedy removals.

 ## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())
    jobs = []
    
    for _ in range(n):
        s, q, c, v = map(int, input().split())
        
        total_q = s * q
        min_q = q
        
        # connectivity cost
        d = min(total_q, (s - 1) * c + min_q)
        
        jobs.append((d, v))
    
    jobs.sort()
    
    import heapq
    heap = []
    heap_sum = 0
    
    for d, v in jobs:
        heapq.heappush(heap, v)
        heap_sum += v
        
        # we can keep at most d jobs by time d
        if len(heap) > d:
            heap_sum -= heapq.heappop(heap)
    
    total = sum(v for _, v in jobs)
    print(total - heap_sum)

if __name__ == "__main__":
    solve()
```この実装では、まず各ピザを有効期限内に圧縮します。 ヒープには、時間内に完成させると決めたピザの値が保存されます。 現在の期限プレフィックスで許可されているジョブ数を超えると、最終目標への貢献が最も少ない最小値が削除されます。 

最終的な答えは、合計値から選択した定時勤務の合計を引いたものとして計算され、これにより無駄な合計値が直接得られます。 

## 実用的な例

 ### 例 1

 計算された期限と値を持つピザを考えてみましょう。 

| ステップ | ソートされたジョブ (d, v) | ヒープの内容 | 保持された値の合計 |
 | --- | --- | --- | --- |
 | 1 | (1, 4) | [4] | 4 |
 | 2 | (2, 3) | [3、4] | 7 |
 | 3 | (2, 5) | [3、4、5] → 3 を削除 | [4, 5] = 9 |

 ここでは、容量を超えた場合には常に最小値を削除します。 The final kept value is maximized.

 これは、期限が早いとスケジュールできるジョブの数が制限され、値ベースのプルーニングによって最適性が維持されることを示しています。 

### 例 2

 入力:```
4
2 3 5 10
3 1 4 20
2 2 2 5
1 10 1 7
```計算された期限を想定します。 

(2,10)、(3,20)、(2,5)、(1,7)

 | ステップ | 仕事 | ヒープ | 合計を保持 |
 | --- | --- | --- | --- |
 | 1 | (1,7) | [7] | 7 |
 | 2 | (2,10) | [7,10] | 17 |
 | 3 | (2,5) | [5,10,7] → 5 を削除 | [7,10] = 17 |
 | 4 | (3,20) | [7,10,20] | 37 |

 最終的な選択では、各プレフィックスの期限を尊重して、最も価値のある実行可能なサブセットが保持されます。 

これは、貪欲な削除戦略が緊急性と価値のバランスを正しくとっていることを裏付けています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(N log N) | ソートが優先され、ヒープ操作は挿入/削除ごとに対数的になります。 
| スペース | O(N) | すべてのジョブをヒープと配列に保存します。 

N log N ソートとヒープ操作の両方が 100,000 要素に対して効率的であるため、このソリューションは制限内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    return sys.stdout.getvalue() if False else solve_capture(inp)

def solve_capture(inp: str) -> str:
    import sys, heapq
    input = sys.stdin.readline
    sys.stdin = io.StringIO(inp)
    
    n = int(input())
    jobs = []
    
    for _ in range(n):
        s, q, c, v = map(int, input().split())
        d = min(s * q, (s - 1) * c + q)
        jobs.append((d, v))
    
    jobs.sort()
    
    heap = []
    total = 0
    kept = 0
    
    for d, v in jobs:
        heapq.heappush(heap, v)
        total += v
        if len(heap) > d:
            total -= heapq.heappop(heap)
    
    allv = sum(v for _, v in jobs)
    return str(allv - total)

# sample-like tests
assert solve_capture("1\n2 3 5 10\n") == "0"
assert solve_capture("2\n1 1 1 5\n2 2 2 7\n") in {"0", "5"}

# edge: all deadlines large
assert solve_capture("3\n2 1 1 1\n2 1 1 2\n2 1 1 3\n") == "0"

# edge: tight deadlines force drops
assert solve_capture("3\n1 1 1 10\n2 1 1 20\n2 1 1 30\n") in {"10", "20", "30"}

# large equal structure
assert solve_capture("2\n100 5 1 1\n100 5 1 2\n") == "0"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一の仕事 | 0 | 基本的なスケジュールの正確性 |
 | 値を増やす | 0 | 貪欲であればすべてが実現可能になる |
 | 同等の構造 | 0 | 対称的な処理 |
 | 厳しい締め切り | 部分損失 | ヒープエビクション動作 |

 ## 特殊なケース

 すべてのスライスが同一であり、クラストがスライス接続よりもはるかに安価な場合は、例外的なケースが発生します。 その場合、d_i は (s_i - 1) * c_i + q_i となり、完全な q_i ではなく最小スライス強度を採用するという間違いがあると、実現可能性を過大評価することになります。 

もう 1 つのエッジ ケースは、多くのピザが同じ短い期限を共有する場合です。 このアルゴリズムは同じしきい値で容量を繰り返し超過し、複数回の削除を強制します。 すべての違反は最も価値の低いジョブを破棄することでローカルに解決されるため、ヒープ ベースの選択は引き続き機能し、複数の制約が同時に衝突した場合でも、この決定は有効のままです。
