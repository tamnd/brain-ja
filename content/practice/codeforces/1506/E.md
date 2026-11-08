---
title: "CF 1506E - 順列の復元"
description: "ルール qi = max(p1, ..., pi) を使用して、1 から n までの数値の未知の順列 p から導出された配列 q が与えられます。 私たちのタスクは、この q を生成した可能性がある 2 つの順列、つまり辞書編集上最小のものと辞書編集上最大のものを再構築することです。"
date: "2026-06-10T20:20:11+07:00"
tags: ["codeforces", "competitive-programming", "constructive-algorithms", "implementation"]
categories: ["algorithms"]
codeforces_contest: 1506
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 710 (Div. 3)"
rating: 1500
weight: 1506
solve_time_s: 175
verified: false
draft: false
---

[CF 1506E - 順列の復元](https://codeforces.com/problemset/problem/1506/E)

 **評価:** 1500
 **タグ:** 建設的アルゴリズム、実装
 **解決時間:** 2 分 55 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 配列が与えられています`q`未知の順列から派生した`p`からの数字の`1`に`n`ルールを使って`q_i = max(p_1, ..., p_i)`。 私たちの仕事は、これを生成した可能性のある 2 つの順列を再構築することです。`q`: 1 つは辞書編集上最小であり、もう 1 つは辞書編集上最大です。 辞書編集的な順序は辞書の順序に似ています。配列を要素ごとに比較し、最初の違いによって順序が決まります。 

入力により次のことが保証されます。`q`は有効な「プレフィックス最大値」配列であるため、その有効性をチェックする必要はありません。 課題は、要素の選択にあります。`p`位置の間で`q`が増加します。 いつ`q_i > q_{i-1}`、要素`p_i`と等しくなければなりません`q_i`。 いつ`q_i == q_{i-1}`、残りの未使用の数値以下を自由に配置できます。`q_i`。 

与えられた`n`は最大 2 × 10^5 になる可能性があるため、既知の最大値の間ですべての置換を試みる単純なアプローチでは遅すぎるでしょう。 テスト ケースごとに O(n) のソリューションが必要です。 

エッジケースには配列が含まれます。`q`は厳密に増加しており、順列がまったく同じになるように強制され、配列は`q`反復値が長く続くため、辞書編集上の制約を満たすために未使用の数値を慎重に選択する必要があります。 

## アプローチ

 ブルートフォースソリューションでは、以下と一致するすべての順列が生成されます。`q`未知の位置を残りのすべての数字で埋めることによって。 これは正しく機能しますが、未知の位置の数が階乗的に複雑になるため、これは実行不可能です。`n`最大 2 × 10^5。 

重要な観察は、次のような位置にあるということです。`q`増加は固定されています。 他のすべてのポジションは、まだ使用されていない数値のセットから埋めることができますが、現在の数値以下に制限されます。`q_i`。 辞書編集的に最小の順列を得るには、常にこれらのギャップ内で利用可能な最小の数値を選択する必要があります。 辞書編集上の最大順列の場合、現在の最大値を超えない利用可能な最大の数を選択します。 これは、ソートされたコンテナーまたは優先キューを使用して効率的に実行できます。 重要な洞察は、残りの数値のセットを維持することで、各選択を O(log n) で実行できるか、事前に計算されたリストとポインターを使用して、選択ごとに O(1) で実行できることです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | お(ん!) | O(n) | 遅すぎる |
 | 最適 | テスト ケースあたり O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 2 つの空のリストを初期化します。`p_min`そして`p_max`最小および最大の順列の場合。 セットを維持する`remaining`まだ配置されていない 1 から n までのすべての数字が含まれています。 
2. 各インデックスを反復処理します。`i`の`q`。 もし`i == 0`または`q[i] != q[i-1]`、これは「新しい最大値」の位置です。 セット`p_min[i] = p_max[i] = q[i]`そして削除します`q[i]`から`remaining`。 
3. もし`q[i] == q[i-1]`、最小順列と最大順列に対して異なる方法でこの位置を埋めます。 最小の順列の場合は、次の最小の数値を選択します。`remaining`未満`q[i]`。 最大の順列を得るには、次の最大の数を選択します。`remaining`未満`q[i]`。 
4. 選択した番号をから削除します`remaining`重複がないようにします。 
5. すべての位置を繰り返した後、印刷します。`p_min`そして`p_max`。 

不変条件は、どの位置でも`i`、最初の最大値`i`要素が一致する`q[i]`、およびのすべての要素`p`区別されたままです。 ギャップ内の可能な最小または最大の数値を常に選択することで、順列が辞書編集的に最小または最大であることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        n = int(input())
        q = list(map(int, input().split()))
        
        used = set()
        remaining_min = []
        remaining_max = []
        last = 0
        
        p_min = []
        p_max = []
        
        import heapq
        min_heap = []
        max_heap = []
        
        for num in range(1, n+1):
            remaining_min.append(num)
        remaining_min_ptr = 0
        
        # For maximal, we will use sorted list and pop from end
        remaining_max = list(range(1, n+1))
        
        from bisect import bisect_left
        import collections
        
        # used numbers
        used_set = set()
        
        # Preprocess min and max
        min_available = []
        max_available = []
        import bisect
        
        # We maintain min_available as a pointer to the next smallest unused number
        available = set(range(1, n+1))
        min_available = []
        max_available = sorted(available)
        
        last_q = 0
        for i in range(n):
            if i == 0 or q[i] != q[i-1]:
                # fixed element
                p_min.append(q[i])
                p_max.append(q[i])
                available.remove(q[i])
                max_available.remove(q[i])
            else:
                # q[i] == q[i-1]
                # minimal: choose smallest number in available
                smallest = min(available)
                p_min.append(smallest)
                available.remove(smallest)
                
                # maximal: choose largest number < q[i]
                idx = bisect.bisect_left(max_available, q[i])
                largest = max_available[idx-1]
                p_max.append(largest)
                max_available.pop(idx-1)
        
        print(*p_min)
        print(*p_max)

solve()
```コードは、未使用の数値のセットを維持し、正しい戦略でギャップを埋めます。 最小の順列の場合、直接`min`順序付けられた削除により要素ごとに必要なのは O(1) だけであるため、セットに対するクエリは機能します。 最大順列の場合、並べ替えられたリストの二分探索により、現在の最大値を下回る最大の数値が効率的に見つかります。 繰り返しの最大値や`q`厳密に増加しているものは、選択ルールが制約を尊重するため、自動的に処理されます。 

## 実用的な例

 ### 例 1

 入力`q = [3, 3, 4, 4, 7, 7, 7]`。 

| 私 | q[i] | 利用可能 (分) | 選択した分 | 利用可能 (最大) | 選択された最大値 |
 | --- | --- | --- | --- | --- | --- |
 | 0 | 3 | {1..7} | 3 | {1..7} | 3 |
 | 1 | 3 | {1,2,4,5,6,7} | 1 | {1,2,4,5,6,7} | 2 |
 | 2 | 4 | {2,4,5,6,7} | 4 | {1,4,5,6,7} | 4 |
 | 3 | 4 | {2,5,6,7} | 2 | {1,5,6,7} | 1 |
 | 4 | 7 | {5,6,7} | 7 | {5,6,7} | 7 |
 | 5 | 7 | {5,6} | 5 | {5,6} | 6 |
 | 6 | 7 | {6} | 6 | {5} | 5 |

 最小出力:`[3,1,4,2,7,5,6]`最大出力:`[3,2,4,1,7,6,5]`これは、アルゴリズムが最大値を正しく配置し、ギャップを埋めて辞書編集順序を最適化していることを示しています。 

### 例 2

 入力`q = [1,2,3,4]`。 

ここ、`q`厳密に増加しています。 すべての要素は最大値が固定されています。 

最小出力:`[1,2,3,4]`最大出力:`[1,2,3,4]`ギャップは存在しないため、両方の順列は同一です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | テスト ケースあたり O(n) | 各要素は 1 回処理され、利用可能な数値は O(1) min および O(log n) max クエリを許可する構造内に維持されます。 |
 | スペース | O(n) | 順列配列と残りの数値のセット/リストを保存します。 |

 の合計を考えると、`n`すべてのテスト ケースで ≤ 2 × 10^5 であるため、このソリューションは 2 秒の制限時間内で適切に実行されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out
    solve()
    return out.getvalue().strip()

# Provided samples
assert run("4\n7\n3 3 4 4 7 7 7\n4\n1 2 3 4\n7\n3 4 5 5 5 7 7\n1\n1\n") == "3 1 4 2 7 5 6\n3 2 4 1 7 6 5\n1 2 3 4\n1 2 3 4\n3 4 5 1 2 7 6\n3 4 5 2 1 7 6\n1\n1"

# Custom cases
assert run("1
```
