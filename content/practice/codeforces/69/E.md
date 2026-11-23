---
title: "CF 69E - サブセグメント"
description: "整数の配列と固定ウィンドウ サイズ k が与えられます。 長さ k の連続するサブ配列 (セグメント) ごとに、そのセグメント内で 1 回だけ出現する最大の要素を見つける必要があります。 要素が 1 回だけ出現しない場合は、「Nothing」が出力されます。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "implementation"]
categories: ["algorithms"]
codeforces_contest: 69
codeforces_index: "E"
codeforces_contest_name: "Codeforces Beta Round 63 (Div. 2)"
rating: 1800
weight: 69
solve_time_s: 82
verified: true
draft: false
---

[CF 69E - サブセグメント](https://codeforces.com/problemset/problem/69/E)

 **評価:** 1800
 **タグ:** データ構造、実装
 **解決時間:** 1 分 22 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 整数の配列と固定ウィンドウ サイズが与えられます。`k`。 長さの連続するサブ配列 (セグメント) ごとに`k`、そのセグメント内で 1 回だけ出現する最大の要素を見つける必要があります。 要素が 1 回だけ出現しない場合は、「Nothing」が出力されます。 

入力は配列サイズから始まります`n`そしてセグメントの長さ`k`、 に続く`n`整数。 出力は`n-k+1`ラインのそれぞれが 1 つのセグメントに対応します。 

制約は重要です。`n`最大 100,000 になる可能性があり、おおよそ以上のことを行うソリューションは除外されます。`O(n log n)`または`O(n)`1 秒未満に抑えたい場合は、テスト ケースごとに作業します。 各セグメント内のすべての要素の一意性をチェックする素朴なアプローチは次のようになります。`O(n*k)`、最悪の場合は 100 億回の操作に達し、タイムアウトになる可能性があります。 

セグメントにすべての要素が重複している場合、明らかではないエッジ ケースが発生します。 たとえば、セグメントが`[2, 2, 2]`、一度だけ出現する要素はありません。 不注意な実装では、とにかく最大値を返そうとし、`2`「何もない」の代わりに。 もう 1 つのエッジ ケースは、配列に負の数値または大きな値が含まれる場合です。 アルゴリズムは、肯定的なものを想定せずにそれらを正しく比較する必要があります。 

## アプローチ

 ブルート フォース ソリューションは、長さのすべてのセグメントにわたって反復されます。`k`。 各セグメントについて、各数値の頻度をカウントし、頻度が 1 つだけの数値の最大値を選択します。 これは、直接検査によって正しい結果が保証されるため機能しますが、複雑さは次のとおりです。`O(n*k)`- のために`n=10^5`そして`k=10^5`、それは遅すぎます。 

より高速なアプローチに関する重要な観察は、次のサイズのスライディング ウィンドウを維持できることです。`k`あるセグメントから次のセグメントに移動するときに頻度を更新します。 すべてのセグメントの周波数を最初から再計算する代わりに、ウィンドウから出ていく要素のカウントをデクリメントし、ウィンドウに入る要素のカウントをインクリメントします。 これにより、ステップごとの作業が実質的に減少します`O(log k)`バランスの取れた BST や`SortedSet`。 

この問題では、`SortedSet`現在のウィンドウに 1 回だけ表示される要素の数。 数値の頻度が 1 から 2 に変化するたびに、その数値をセットから削除します。 2 から 1 に下がったら追加します。 このセットの最大値は、まさに出力する必要がある数値です。 セットが空の場合は、「Nothing」を出力します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n*k) | O(k) | 遅すぎる |
 | 周波数マップ + SortedSet を使用したスライディング ウィンドウ | O(n log k) | O(k) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 現在のウィンドウ内の要素の出現をカウントするために頻度辞書を初期化します。 を初期化します`SortedSet`(または同等の) 頻度を持つすべての要素を正確に 1 に保ちます。 
2. 周波数マップを設定し、最初の長さのウィンドウを設定します。`k`。 要素ごとに、その数を増やします。 カウントが 1 になった場合は、それをセットに追加します。 2つになったら取り除きます。 
3. 最初のウィンドウのセットの最大値を出力します。 セットが空の場合は、「Nothing」を出力します。 
4. ウィンドウを一度に 1 要素ずつスライドさせます。 各ステップで、ウィンドウから出る要素の頻度を削除します。その頻度が 2 から 1 に低下した場合は、その要素をセットに追加し直します。 1 から 0 に下がった場合は削除します。 次に、ウィンドウに入る新しい要素の頻度を増やします。1 つになったら、それを追加します。 2つになったら取り除きます。 
5. 新しいウィンドウのセットを更新した後、最大の要素を出力するか、セットが空の場合は「なし」を出力します。 すべてのウィンドウが処理されるまで繰り返します。 

機能する理由: すべてのステップで、セットには現在のウィンドウで 1 回だけ出現するすべての数値が含まれます。 段階的に更新することで、要素を見逃したり、重複を含めたりすることがないため、セットの最大値が問題の要件を満たすことが保証されます。 スライディングウィンドウは、私たちが費やすだけであることを保証します`O(log k)`バランスの取れた構造を使用している場合、アルゴリズムの効率を維持し、ステップごとの時間を短縮します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from sortedcontainers import SortedSet
from collections import defaultdict

n, k = map(int, input().split())
a = [int(input()) for _ in range(n)]

freq = defaultdict(int)
unique = SortedSet()

# initialize first window
for i in range(k):
    freq[a[i]] += 1
for i in range(k):
    if freq[a[i]] == 1:
        unique.add(a[i])
    elif freq[a[i]] == 2:
        unique.discard(a[i])

# output for the first window
print(unique[-1] if unique else "Nothing")

# slide the window
for i in range(k, n):
    left = a[i - k]
    freq[left] -= 1
    if freq[left] == 1:
        unique.add(left)
    elif freq[left] == 0:
        unique.discard(left)

    right = a[i]
    freq[right] += 1
    if freq[right] == 1:
        unique.add(right)
    elif freq[right] == 2:
        unique.discard(right)

    print(unique[-1] if unique else "Nothing")
```この解決策は、最初のウィンドウの周波数マップを構築することから始まり、`SortedSet`1 回だけ出現する要素の数。 ウィンドウをスライドするときは、カウントを慎重に更新し、それに応じてセットを調整します。 ウィンドウは左から右に変化するため、操作の順序は重要です。最初にデクリメント、次にインクリメントします。 

## 実用的な例

 **サンプル 1:**

 入力:```
5 3
1
2
2
3
3
```| ウィンドウ | 周波数 | ユニーク | 出力 |
 | --- | --- | --- | --- |
 | [1,2,2] | {1:1、2:2} | {1} | 1 |
 | [2,2,3] | {2:2,3:1} | {3} | 3 |
 | [2,3,3] | {2:1、3:2} | {2} | 2 |

 これは、要素がウィンドウに出入りするときの重複の正しい処理と適切な更新を示しています。 

**カスタム入力:**

 入力:```
6 2
4
4
5
6
5
7
```| ウィンドウ | 周波数 | ユニーク | 出力 |
 | --- | --- | --- | --- |
 | [4,4] | {4:2} | {} | 何も |
 | [4,5] | {4:1,5:1} | {4,5} | 5 |
 | [5,6] | {5:1,6:1} | {5,6} | 6 |
 | [6,5] | {6:1,5:1} | {5,6} | 6 |
 | [5,7] | {5:1,7:1} | {5,7} | 7 |

 ## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log k) | スライディング ウィンドウの更新には以下が含まれます`SortedSet`要素ごとの操作。これは log(ウィンドウのサイズ) |
 | スペース | O(k) | 周波数マップと`SortedSet`せいぜい保管する`k`要素 |

 このソリューションは 1 秒の制限時間内に簡単に収まります。`n=10^5`使用するメモリは 256 MB 未満です。 

## テストケース```python
import sys, io
from contextlib import redirect_stdout

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    f = io.StringIO()
    with redirect_stdout(f):
        # insert solution code here
        import sys
        input = sys.stdin.readline
        from sortedcontainers import SortedSet
        from collections import defaultdict

        n, k = map(int, input().split())
        a = [int(input()) for _ in range(n)]

        freq = defaultdict(int)
        unique = SortedSet()

        for i in range(k):
            freq[a[i]] += 1
        for i in range(k):
            if freq[a[i]] == 1:
                unique.add(a[i])
            elif freq[a[i]] == 2:
                unique.discard(a[i])
        print(unique[-1] if unique else "Nothing")

        for i in range(k, n):
            left = a[i - k]
            freq[left] -= 1
            if freq[left] == 1:
                unique.add(left)
            elif freq[left] == 0:
                unique.discard(left)

            right = a[i]
            freq[right] += 1
            if freq[right] == 1:
                unique.add(right)
            elif freq[right] == 2:
                unique.discard(right)

            print(unique[-1] if unique else "Nothing")
    return f.getvalue().strip()

# provided sample
assert run("5 3\n1\n2\n2\n3\n3\n") == "1\n3\n2"

# custom cases
assert run("2 1\n1\n1\n") == "1\n1", "single element windows"
assert run("3 3\n2\n2\n2\n") == "Nothing", "all duplicates"
assert run("4
```
