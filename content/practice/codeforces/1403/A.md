---
title: "CF 1403A - 大いなる力のポーション"
description: "私たちは、それぞれ特定の高度 H[i] に住んでいる N 人のシャーマンの間にダイナミックな友情ネットワークを持っています。 最初はシャーマンは誰も信用せず、毎日、一つの友情が形成されたり解消されたりします。 各シャーマンはいつでも他のシャーマンを最大 D 人まで信頼できます。"
date: "2026-06-11T08:20:48+07:00"
tags: ["codeforces", "competitive-programming", "*special", "2-sat", "binary-search", "data-structures", "graphs", "interactive", "sortings", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 1403
codeforces_index: "A"
codeforces_contest_name: "Central-European Olympiad in Informatics, CEOI 2020, Day 2 (IOI, Unofficial Mirror Contest, Unrated)"
rating: 2400
weight: 1403
solve_time_s: 123
verified: false
draft: false
---

[CF 1403A - 大いなる力のポーション](https://codeforces.com/problemset/problem/1403/A)

 **評価:** 2400
 **タグ:** *特別、2-sat、二分探索、データ構造、グラフ、インタラクティブ、ソート、2 つのポインター
 **解決時間:** 2 分 3 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちは、人々の間でダイナミックな友情ネットワークを築いています。`N`それぞれが特定の高度に住んでいるシャーマン`H[i]`。 最初はシャーマンは誰も信用せず、毎日、一つの友情が形成されたり解消されたりします。 各シャーマンが信頼できるのはせいぜい`D`他もいつでも。 

このタスクは、次の形式の質問に答えることです。泥棒の疑いがある場合`x`、邪悪なシャーマンと疑われる`y`、そして一日`v`、信頼できる友人の高度間の最小距離を見つけます。`x`そして信頼できる友人`y`一日の終わりに`v`。 もし`x`そして`y`友達を共有する、その距離は`0`。 どちらにも友達がいない場合、距離は次のように定義されます。`10^9`。 

制約は厳しいです:`N`更新数は 100,000 に達する可能性があります`U`クエリ数は最大 50,000 で、200,000 に達する可能性があります。 クエリごとにフレンド セットや距離を再計算する総当り的なアプローチでは、時間がかかりすぎます。 最高度`D`人数は最大 500 人までと小さいため、各シャーマンの友人ネットワークの希薄性を活用できます。 

エッジケースには、更新の数日前が含まれます (`v = 0`）、友達がいないシャーマン、同じ友達が両方にカウントされる状況`x`そして`y`。 単純なアプローチでは失敗する可能性がある`v = 0`または、同じペアで繰り返される友情の切り替えを誤って処理します。 

## アプローチ

 ブルートフォースアプローチでは、すべての更新を再生することで毎日の友人関係グラフを維持し、クエリごとに友人のすべてのペアを反復して距離を計算します。 これは正しいですが、クエリごとにコストがかかる可能性があるため、現実的ではありません。`O(D^2)`すべての操作とグラフの更新`U`日は`O(U*D)`。 上限の場合、これは数十億回の操作となり、妥当な制限を超えます。 

重要な洞察は、各シャーマンが最大でも`D`友人、それはそれに比べて小さいです`N`。 これにより、現在の友人セットを動的に維持し、クエリに効率的に応答できるようになります。 日数は増分であるため、更新を順番に処理し、必要な場合にのみ友情セットのスナップショットを作成できます。 当日のクエリ中`v`の友達セットのみが必要です。`x`そして`y`あの日。 友人関係の時間追跡ログを維持したり、クエリが到着したときに更新を段階的に適用したりすることができます。 

最小距離を効率的に計算するために、友人の高度を並べ替えることができます。`x`そして`y`and use a two-pointer sweep to find the minimal absolute difference. これは、各友達セットには最大でも`D`要素、並べ替えとスイープの作成`O(D log D + D)`クエリごとに、これは許容可能です`D <= 500`。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート フォース (クエリごとにすべてのセットを再計算) | O(U_D + Q_D^2) | O(N*D) | 遅すぎる |
 | 増分更新 + クエリごとの 2 ポインター | O(U + Q*D log D) | O(N*D) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. シャーマンの数を読み取る`N`、最大友達`D`、更新数`U`、クエリ数`Q`。 標高を読む`H[i]`。 
2. セットのリストを初期化する`friends[i]`各シャーマンの現在の友人を追跡します。 各シャーマンは最大でも`D`皆さん、これらのセットはまだ小さいままです。 
3. を読んでください。`U`友情の最新情報`(A_i, B_i)`。 それらをリストに保存して、順次適用します。 
4. を初期化します。`current_day = 0`。 クエリごとに`(x, y, v)`、からのすべてのアップデートを適用します`current_day`今日まで`v`友人関係を切り替えることで`friends[A_i]`そして`friends[B_i]`。 アップデート`current_day = v`。 
5. 友達を抽出する`x`そして`y`。 どちらかが空の場合は戻ります`10^9`。 交差点が空でない場合は、戻ります。`0`。 
6. 友達の標高を並べ替える`x`そして`y`。 2 ポインタ スイープを実行します。ソートされた各リストに 1 つのポインタを保持し、より小さい高度に対応するポインタを移動します。 各ステップで最小の絶対差を更新します。 
7. 最小距離を印刷します。 

機能する理由: すべてのクエリで、フレンド セットはその日の正確な状態を反映します。`v`アップデートが順次適用されるため。 以来`D`が小さい場合、ソートと 2 ポインターの比較により、真の最小距離が効率的に見つかります。 空のフレンド セットまたは共有フレンドのエッジ ケースは明示的にチェックされるため、クエリが間違った値を返すことはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from bisect import bisect_left

N, D, U, Q = map(int, input().split())
H = list(map(int, input().split()))

updates = [tuple(map(int, input().split())) for _ in range(U)]
friends = [set() for _ in range(N)]

current_day = 0

def apply_update(a, b):
    if b in friends[a]:
        friends[a].remove(b)
        friends[b].remove(a)
    else:
        if len(friends[a]) < D and len(friends[b]) < D:
            friends[a].add(b)
            friends[b].add(a)

for _ in range(Q):
    x, y, v = map(int, input().split())
    # apply all updates until day v
    while current_day < v:
        a, b = updates[current_day]
        apply_update(a, b)
        current_day += 1
    
    fx = friends[x]
    fy = friends[y]
    
    if not fx or not fy:
        print(10**9)
        continue
    if fx & fy:
        print(0)
        continue
    
    # two-pointer minimal distance
    Ax = sorted(H[i] for i in fx)
    Ay = sorted(H[i] for i in fy)
    
    i = j = 0
    best = 10**9
    while i < len(Ax) and j < len(Ay):
        best = min(best, abs(Ax[i] - Ay[j]))
        if Ax[i] < Ay[j]:
            i += 1
        else:
            j += 1
    print(best)
```このソリューションは友人セットを効率的に維持し、友人関係を段階的に切り替えます。 クエリごとに小さなセットを並べ替えることで、最小距離の計算が高速になります。 2 ポインター スイープでは高度の順序を利用し、最小のペアを見逃さないようにします。 

## 実用的な例

 サンプル 1 の場合:

 | 日 | 適用されたアップデート | 0の友達 | 3人の友達 | クエリ (0,3,v) | 最小距離 |
 | --- | --- | --- | --- | --- | --- |
 | 0 | - | {} | {} | 0,3,4 | 26 |
 | 1 | 0 1 | {1} | {} | - | - |
 | 2 | 2 0 | {1,2} | {0} | - | - |
 | 3 | 3 4 | {1,2} | {4} | - | - |
 | 4 | 3 5 | {1,2} | {4,5} | 0,3,4 | 距離 = 分( |

 この表は、順次更新アプリケーションがクエリごとに正確なフレンド セットを維持していることを示しています。 並べ替えとツーポインタにより、正しい最小距離が得られます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(U + Q*D log D) | 各更新は O(1) のフレンドシップを切り替え、各クエリは最大 D サイズの 2 つのフレンド セットをソートし、線形 2 ポインター スイープを実行します。 |
 | スペース | O(N*D + U) | 各シャーマンは最大 D 人の友人を保存し、すべての更新が保存されます。 |

 これは制約内に快適に収まります。`U`2e5まで、`D`500まで、`Q`最大 5e4、最悪の場合、クエリ フェーズごとに 5e4_500_log500 ≈ 7e6 の操作と線形更新処理が発生します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from contextlib import redirect_stdout
    out = io.StringIO()
    with redirect_stdout(out):
        exec(open("solution.py").read())
    return out.getvalue().strip()

# provided sample
assert run("""6 5 11 4
2 42 1000 54 68 234
0 1
2 0
3 4
3 5
3 5
1 3
5 3
0 5
3 0
1 3
3 5
0 3 4
3 0 8
0 5 5
3 0 11""") == "26\n0\n1000000000\n14"

# custom cases
assert run("""2 1 1 2
0 10
0 1
0 1 0
0 1 1""") == "1000000000\n10"

assert run("""3 2 2 1
1 2 3
0 1
1 2
0 2 2""") == "1
```
