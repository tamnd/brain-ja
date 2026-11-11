---
title: "CF 1669B - トリプル"
description: "この問題では、配列内で少なくとも 3 回出現する任意の数値を見つけるように求められます。 複数のテスト ケースが与えられ、各テスト ケースに対して整数の配列が提供されます。 整数は 1 から配列のサイズまでであることが保証されます。"
date: "2026-06-10T02:00:49+07:00"
tags: ["codeforces", "competitive-programming", "implementation", "sortings"]
categories: ["algorithms"]
codeforces_contest: 1669
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 784 (Div. 4)"
rating: 800
weight: 1669
solve_time_s: 447
verified: true
draft: false
---

[CF 1669B - トリプル](https://codeforces.com/problemset/problem/1669/B)

 **評価:** 800
 **タグ:** 実装、並べ替え
 **解決時間:** 7 分 27 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 この問題では、配列内で少なくとも 3 回出現する任意の数値を見つけるように求められます。 複数のテスト ケースが与えられ、各テスト ケースに対して整数の配列が提供されます。 整数は 1 から配列のサイズまでであることが保証されます。 私たちのタスクは、そのような数値が存在する場合は 1 つ、頻度が 3 に達する数値がない場合は -1 を出力することです。 

The constraints indicate that the number of test cases can be as high as 10,000 and the total sum of array sizes across all test cases does not exceed 200,000. これは、テスト ケースごとに配列を複数回反復したり、入れ子になったループを実行したりするソリューションは、速度が非常に遅くなる可能性があることを意味します。 したがって、テスト ケースあたり線形パス、またはテスト ケースあたり最大 O(n log n) のアプローチが必要です。 

Edge cases that could cause naive solutions to fail include arrays with fewer than three elements, where it is impossible for any value to appear three times. たとえば、配列`[1, 2]`すぐに -1 を返す必要があります。 もう 1 つの微妙なケースは、複数の数字が 3 回出現する場合です。 この問題では任意の有効な数値が許可されるため、1 つだけを識別する必要があります。 すべての要素が同一である配列 (例:`[5, 5, 5, 5]`、その値を正しく返す必要があります。 

## アプローチ

 ブルートフォース手法では、1 から n までのすべての値を反復処理し、配列内のその値の出現回数をカウントします。 この方法はすべての要素を明示的にチェックするので正しいですが、最悪の場合、n が 200,000 の場合、各数値を個別にカウントすると O(n^2) の演算が必要となり、制約を考慮すると遅すぎます。 

最適なアプローチでは、周波数カウンターを使用して、単一パスですべての数値をカウントします。 これは、カウントが 3 に達したかどうかのみを知る必要があり、3 を超えた正確なカウントは必要ないという事実を利用しています。 辞書またはサイズ n+1 の事前に割り当てられた配列 (すべての値が 1 から n の間にあるため) を使用すると、テスト ケースごとに O(n) 時間で頻度を追跡できます。 数値が 3 に達するとすぐにそれを出力し、それ以降の処理を停止できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^2) | お(1) | 遅すぎる |
 | 周波数カウンタ | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. テスト ケース t の数を読み取ります。 
2. 各テスト ケースについて、n と n 個の整数の配列を読み取ります。 
3. 周波数カウンタを初期化します。 リストを使用する場合は、1 から n までのトラック カウントに n+1 個のゼロを割り当てます。 
4. 配列内の各数値を繰り返し処理します。 周波数カウンタのカウントを増加させます。 
5. 数値が 3 に達するとすぐに、その数値を出力し、現在のテスト ケースの処理を停止します。 
6. どの数値も 3 に達しないまま配列の末尾に到達した場合は、-1 を出力します。 

このアルゴリズムが機能するのは、頻度カウンタによって各数値が何回出現するかを正確に追跡できるためです。 カウントが 3 に達したときにすぐにチェックすることで、有効な出力が不必要な計算なしで印刷されることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        n = int(input())
        arr = list(map(int, input().split()))
        freq = [0] * (n + 1)
        found = False
        for num in arr:
            freq[num] += 1
            if freq[num] == 3:
                print(num)
                found = True
                break
        if not found:
            print(-1)

if __name__ == "__main__":
    solve()
```コードは、次を使用して入力を効率的に読み取ります。`sys.stdin.readline`そしてリストを初期化します`freq`各数値の出現をカウントします。 数字が に達したときにすぐにブレイクすることで、
