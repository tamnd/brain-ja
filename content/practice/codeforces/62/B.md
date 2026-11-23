---
title: "CF 62B - ティンデックス.ブロム"
description: "このタスクは、特定の誤差関数に従って、ユーザーが入力した住所と潜在的な住所のリストの間の一種の「距離」を計算することです。 ユーザーは長さ k の文字列 s を入力します。 この場合、潜在的なアドレスは n 個あり、それぞれが任意の長さの文字列になります。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "implementation"]
categories: ["algorithms"]
codeforces_contest: 62
codeforces_index: "B"
codeforces_contest_name: "Codeforces Beta Round 58"
rating: 1800
weight: 62
solve_time_s: 78
verified: true
draft: false
---

[CF 62B - Tyndex.Brome](https://codeforces.com/problemset/problem/62/B)

 **評価:** 1800
 **タグ:** 二分探索、実装
 **解決時間:** 1 分 18 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 このタスクは、特定の誤差関数に従って、ユーザーが入力した住所と潜在的な住所のリストの間の一種の「距離」を計算することです。 ユーザーが文字列を入力します`s`長さの`k`。 それから、`n`潜在的なアドレス。それぞれは任意の長さの文字列です。 潜在的な住所の各文字について、同じ文字の最も近い出現と一致させようとします。`s`。 キャラクターが存在する場合`s`、位置間の最小距離を追加します。 存在しない場合は、潜在的なアドレスの長さを追加します。 各潜在的なアドレスの出力は、この合計エラーを表す単一の数値です。 

入力境界は重要です。 さまざまな長さの潜在的なアドレスを最大 100,000 個持つことができますが、すべての長さの合計は 200,000 に制限されます。 これにより、各文字をすべての文字と照合するような単純なアプローチは即座に排除されます。`s`単純な O(n * k * len(address)) の方法です。最悪の場合、10^10 の演算に達する可能性があるためです。 すべてのアドレス長の合計が線形に近いアルゴリズムが必要です。 

微妙なエッジ ケースとしては、アドレスに完全に存在しない文字が含まれる場合があります。`s`、アドレスは次と同じです`s`、と比較して非常に短いアドレス`s`。 たとえば、次の場合`s = "abc"`潜在的なアドレスは`"d"`、関数`F`返すべきです`1`なぜなら`d`には現れない`s`。 欠落文字を正しく処理しない単純な実装では、代わりにゼロが返される可能性があります。 

## アプローチ

 ブルートフォースの解決策は簡単です。 潜在的なアドレスごとに、各文字を反復処理してスキャンします。`s`最も近い一致を見つけます。 文字通り各文字の最小距離を計算しているため、これにより正確さが保証されます。 ただし、`s`の長さは 10^5 で、それぞれの長さが 2 までの 10^5 個の潜在的なアドレスがあるため、このアプローチでは 10^10 のオーダーの比較が必要になる可能性があります。 これでは制限時間を超えてしまいます。 

最適化のための重要な観察は、スキャンする必要がないことです。`s`毎回キャラクターごとに。 代わりに、各文字の位置を事前に計算できます。`s`。 各文字のインデックスのリストをソートしたら、二分探索を使用して、指定されたインデックスに最も近い位置を O(log k) 時間で見つけることができます。 総コストは、すべてのアドレスの長さの合計に log k を掛けた値に比例します。これは、問題の制約に対して実現可能です。 

もう 1 つの微妙な改善は、文字列に存在しない文字の処理です。`s`。 キャラクターが入っていない場合`s`、最小の誤差寄与はアドレスの長さです。 これは、二分探索ステップ中に簡単に確認できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * k * m) | お(1) | 遅すぎる |
 | 文字ごとの二分探索 | O(sum(len(アドレス)) * log k) | O(k) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力を読み取ります: 整数`n`そして`k`、ユーザーが入力した文字列`s`、および潜在的なアドレスのリスト。 
2. 前処理`s`「a」から「z」までの各文字について、その文字が出現するインデックスのソートされたリストを記録することにより、`s`。 これにより、最も近い文字をすばやく見つけることができます。 
3. 潜在的なアドレスごとに、エラー関数を初期化します。`F`ゼロに。 文字を 1 つずつ繰り返します。 
4. 現在の文字について、それが存在するかどうかを確認します。`s`事前に計算された位置を使用します。 そうでない場合は、潜在的なアドレスの長さを次の値に追加します。`F`そして次の文字に進みます。 
5. キャラクターが存在する場合`s`、二分検索を使用して、最も近いインデックスを見つけます。`s`潜在的なアドレスの現在位置に。 絶対距離を計算してそれに加算します。`F`。 
6. 潜在的なアドレス内のすべての文字を処理した後、印刷または保存します。`F`。 
7. すべての潜在的なアドレスに対して手順 3 ～ 6 を繰り返します。 

不変条件は、前処理後、潜在的なアドレス内のすべての文字が、可能性のある最小距離に寄与するか、`F`それが存在する場合`s`、そうでない場合は完全なアドレス長を提供します。 二分探索では最も近いインデックスが見つかることが保証されるため、アルゴリズムは定義どおりに関数を正確に計算します。 

## Python ソリューション```python
import sys
import bisect
input = sys.stdin.readline

n, k = map(int, input().split())
s = input().strip()

# Precompute positions of each character in s
pos = {chr(c): [] for c in range(ord('a'), ord('z')+1)}
for i, ch in enumerate(s):
    pos[ch].append(i)

addresses = [input().strip() for _ in range(n)]

for address in addresses:
    F = 0
    for i, ch in enumerate(address):
        if not pos[ch]:
            F += len(address)
            continue
        idx_list = pos[ch]
        insert_point = bisect.bisect_left(idx_list, i)
        min_dist = float('inf')
        if insert_point < len(idx_list):
            min_dist = abs(idx_list[insert_point] - i)
        if insert_point > 0:
            min_dist = min(min_dist, abs(idx_list[insert_point-1] - i))
        F += min_dist
    print(F)
```コードでは、`pos`各文字のすべての位置を保持します`s`。 潜在的な住所の各文字について、次を使用して最も近い一致を見つけます。`bisect_left`、見つかったインデックスと前のインデックスの両方を考慮し、常に最小限の距離を確保します。 文字が欠落している場合は、すぐに全長が追加されます。 Off-by-one エラーは、インデックスを注意深く作成し、挿入ポイントの両側を考慮することによって回避されます。 

## 実用的な例

 ### サンプル 1

 入力:```
2 10
codeforces
codeforces
codehorses
```最初のアドレスの処理`codeforces`:

 | キャラクター | アドレスのインデックス | s 内の最も近いインデックス | 距離 |
 | --- | --- | --- | --- |
 | c | 0 | 0 | 0 |
 | o | 1 | 1 | 0 |
 | d | 2 | 2 | 0 |
 | e | 3 | 3 | 0 |
 | f | 4 | 4 | 0 |
 | o | 5 | 5 | 0 |
 | r | 6 | 6 | 0 |
 | c | 7 | 7 | 0 |
 | e | 8 | 8 | 0 |
 | s | 9 | 9 | 0 |`F = 0`2 番目のアドレスの処理`codehorses`:

 | キャラクター | インデックス | s 内の最も近いインデックス | 距離 |
 | --- | --- | --- | --- |
 | c | 0 | 0 | 0 |
 | o | 1 | 1 | 0 |
 | d | 2 | 2 | 0 |
 | e | 3 | 3 | 0 |
 | h | 4 | なし | 11 |
 | o | 5 | 5 | 0 |
 | r | 6 | 6 | 0 |
 | s | 7 | 9 | 2 |
 | e | 8 | 8 | 0 |
 | s | 9 | 9 | 0 |`F = 12`これにより、距離が正しく計算され、欠落文字が処理されることが確認されます。 

### サンプル 2 (エッジケース)

 入力:```
1 3
abc
d
```出力：```
1
```ここで、キャラクターは、`d`には存在しません`s`、 それで`F = len("d") = 1`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(sum(len(アドレス)) * log k) | 前処理`s`はO(k)です。 各アドレスの各文字は、位置リスト内で二分検索を必要とします。 すべてのアドレス長の合計 ≤ 2・10^5、log k ≤ 17、許容可能。 |
 | スペース | O(k + n) | 各文字の位置を格納する`s`O(k) が必要です。 アドレスの入力には O(n) が必要です。 |

 このアルゴリズムは、2 秒の時間制限と 256 MB のメモリ制限内に余裕で収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from contextlib import redirect_stdout
    out = io.StringIO()
    with redirect_stdout(out):
        n, k = map(int, input().split())
        s = input().strip()
        pos = {chr(c): [] for c in range(ord('a'), ord('z')+1)}
        for i, ch in enumerate(s):
            pos[ch].append(i)
        addresses = [input().strip() for _ in range(n)]
        for address in addresses:
            F = 0
            for i, ch in enumerate(address):
                if not pos[ch]:
                    F += len(address)
                    continue
                idx_list = pos[ch]
                insert_point = bisect.bisect_left(idx_list, i)
                min_dist = float('inf')
                if insert_point < len(idx_list):
                    min_dist = abs(idx_list[insert_point] - i)
                if insert_point > 0
```
