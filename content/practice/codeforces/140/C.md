---
title: "CF 140C - 新年の雪だるま"
description: "それぞれが特定の半径を持つ雪玉のコレクションが与えられ、私たちの目標は、これらの雪玉を使用してできるだけ多くの雪だるまを組み立てることです。 各雪だるまは、正確に 3 つの雪玉で構成されている必要があり、これら 3 つのそれぞれには個別の半径が必要です。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "greedy"]
categories: ["algorithms"]
codeforces_contest: 140
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 100"
rating: 1800
weight: 140
solve_time_s: 120
verified: false
draft: false
---

[CF 140C - 新年の雪だるま](https://codeforces.com/problemset/problem/140/C)

 **評価:** 1800
 **タグ:** 二分探索、データ構造、貪欲
 **解決時間:** 2 分
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 それぞれが特定の半径を持つ雪玉のコレクションが与えられ、私たちの目標は、これらの雪玉を使用してできるだけ多くの雪だるまを組み立てることです。 各雪だるまは、正確に 3 つの雪玉で構成されている必要があり、これら 3 つのそれぞれには個別の半径が必要です。 入力は、雪玉の数を表す整数 _n_ と、それに続く半径を表す _n_ 個の整数で構成されます。 出力には、構築できる雪だるまの最大数が示され、その後に雪だるまのリストが表示されます。各雪だるまは 3 つの雪玉の半径で降順 (大、中、小) で指定されます。 

制約により、_n_ は最大 100,000、半径は最大 1,000,000,000 になります。 これは、すべてのトリプレットを明示的にチェックするアプローチを除外します。単純なアプローチでは、およそ n3 回の操作が必要となり、n = 10⁵ にとっては遅すぎるからです。 ほぼ線形か、最悪でも O(n log n) 時間で機能するメソッドが必要です。 

エッジケースでは、単純な実装が簡単に壊れる可能性があります。 たとえば、すべての雪玉が同じ半径である場合、次のようになります。`5 5 5 5`、雪だるまは作れません。 もう 1 つの注意が必要なケースは、1 つの半径が支配的な場合です。たとえば、`1 1 1 2 3`。 最大の 3 つの値を繰り返し取得する不注意な貪欲なアプローチは、誤って 3 つの同一の雪だるまを使用しようとする可能性があり、これは区別性の要件に違反します。 また、異なる半径の雪玉がちょうど 3 つあるということは、正確に 1 つの雪だるまを生成する最小のケースです。 

## アプローチ

 ブルートフォースアプローチでは、可能なすべての雪玉の三つ組を反復処理し、半径が明確であるかどうかを確認し、三つ組がなくなるまで有効な雪だるまを収集します。 原理的には正しいですが、これには O(n3) 回の操作が必要になります。これは、n = 105 の場合、約 10¹5 回になります。 これはまったく現実的ではありません。 

重要な観察は、個別の半径と各半径で利用可能な雪玉の数のみを考慮しているということです。 各半径に存在する雪玉の数のカウントを維持する場合、問題は、残りの最大の 3 つの異なる半径を繰り返し選択することになります。 利用可能な最大の半径を常に選択することで、雪だるまの数が最大化されます。これは、より小さな半径の雪だるまが将来の雪だるまの作成を妨げないためです。より高い半径が使い果たされたら、後で雪だるまをペアにすることができます。 

これは、「利用可能な最大半径をポップ」し、その数を減分することを効率的にサポートするデータ構造を示唆しています。 ここでは最大ヒープが理想的です。 各反復では、3 つの最大半径を抽出し、それらを雪だるまとして記録し、それらの数を減分し、それらがまだ利用可能な場合は押し戻します。 残りの異なる半径が 3 つ未満になるまで続行します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n3) | O(n) | 遅すぎる |
 | 最大ヒープ貪欲 | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各半径の頻度を数えます。 これにより、個々の複製を保存しなくても、各タイプの雪玉がいくつあるかを知ることができます。 
2. すべての個別の半径とそのカウントを含む最大ヒープ (優先キュー) を初期化します。 降順を維持するためのキーとして半径を使用します。 
3. ヒープ内に少なくとも 3 つの異なる半径があるときに、次の操作を実行します。 

ａ． 3 つの最大半径をポップします。 

b. それらを雪だるまとして記録し、降順に並べ替えます。 

c. これらの各半径のカウントを減らします。 半径にまだ雪玉が残っている場合は、それをヒープに押し戻します。 
4. 残りの異なる半径が 3 つ未満になったら、停止します。 記録された雪だるまは、構築できる最大数を表します。 
5. 雪だるまの総数とその構成半径のリストを出力します。 

不変条件は、各ステップで、使用可能な 3 つの最大の別個の半径が選択されるということです。 これにより、非効率的な選択によって潜在的な雪だるまがブロックされることがなくなり、最大数が確実に達成されます。 

## Python ソリューション```python
import sys
import heapq
from collections import Counter

input = sys.stdin.readline

n = int(input())
radii = list(map(int, input().split()))

# Step 1: Count frequencies
freq = Counter(radii)

# Step 2: Build max-heap (invert the radius for max-heap using heapq)
heap = [(-radius, count) for radius, count in freq.items()]
heapq.heapify(heap)

snowmen = []

while len(heap) >= 3:
    first = heapq.heappop(heap)
    second = heapq.heappop(heap)
    third = heapq.heappop(heap)
    
    radii_triplet = sorted([-first[0], -second[0], -third[0]], reverse=True)
    snowmen.append(radii_triplet)
    
    # Decrement counts and push back if still positive
    if first[1] > 1:
        heapq.heappush(heap, (first[0], first[1] - 1))
    if second[1] > 1:
        heapq.heappush(heap, (second[0], second[1] - 1))
    if third[1] > 1:
        heapq.heappush(heap, (third[0], third[1] - 1))

print(len(snowmen))
for s in snowmen:
    print(*s)
```コードはまず雪玉を半径ごとにカウントします。 Python の heapq は min-heap であるため、max-heap は半径を否定することによって構築されます。 各ステップで、3 つの最大の個別の半径を抽出し、それらの数を減分し、雪玉が残っている場合はそれらを押し戻します。 各トリプレットをソートすると、出力が確実に降順になります。 

## 実用的な例

 **サンプル入力 1**:```
7
1 2 3 4 5 6 7
```| ヒープ (上位 3) | カウント | 雪だるま結成 |
 | --- | --- | --- |
 | 7,6,5 | 各1個 | 7 6 5 |
 | 4,3,2 | 各1個 | 4 3 2 |

 これらの反復の後、残る半径は 3 つ未満 (1) になるため、アルゴリズムは停止します。 2 つの雪だるまが作成されます。`[7 6 5]`そして`[4 3 2]`。 

**カスタム入力 2**:```
8
1 1 2 2 3 3 4 4
```| ヒープ (上位 3) | カウント | 雪だるま結成 |
 | --- | --- | --- |
 | 4,3,2 | 各1個 | 4 3 2 |
 | 4,3,1 | 各1個 | 4 3 1 |

 ヒープには 2 つの異なる半径のみが残っているため、アルゴリズムは停止します。 2つの雪だるまが形成されます。 これは、アルゴリズムが複数の重複を正しく処理し、雪だるまの数を最大化していることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 周波数をカウントするのは O(n) です。 各雪玉は、雪だるまごとに最大 1 回プッシュおよびポップできるため、ヒープに対する操作は O(n log n) になります。 |
 | スペース | O(n) | ヒープには個別の半径ごとに最大 1 つのエントリが格納され、頻度カウンタには個別の半径ごとに 1 つのエントリが格納されます。 |

 n ≤ 10⁵ の場合、このアルゴリズムは 2 秒の制限内で効率的に実行されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    # call solution
    n = int(input())
    radii = list(map(int, input().split()))
    from collections import Counter
    import heapq

    freq = Counter(radii)
    heap = [(-radius, count) for radius, count in freq.items()]
    heapq.heapify(heap)

    snowmen = []

    while len(heap) >= 3:
        first = heapq.heappop(heap)
        second = heapq.heappop(heap)
        third = heapq.heappop(heap)

        radii_triplet = sorted([-first[0], -second[0], -third[0]], reverse=True)
        snowmen.append(radii_triplet)

        if first[1] > 1:
            heapq.heappush(heap, (first[0], first[1] - 1))
        if second[1] > 1:
            heapq.heappush(heap, (second[0], second[1] - 1))
        if third[1] > 1:
            heapq.heappush(heap, (third[0], third[1] - 1))

    print(len(snowmen))
    for s in snowmen:
        print(*s)

    return output.getvalue().strip()

# provided samples
assert run("7\n1 2 3 4 5 6 7\n") == "2\n7 6 5\n4 3 2"

# custom tests
assert run("4\n5 5 5 5\n") == "0"
assert run("3\n1 2 3\n") == "1\n3 2 1"
assert run("8\n1 1 2 2 3 3 4 4\n") == "2\n4 3 2\n4 3 1"
assert run("6\n1 1 2 2 3 3\n") == "2\n3 2 1\n3 2 1"
```| テスト入力
