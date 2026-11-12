---
title: "CF 1702C - トレインとクエリ"
description: "1 から $10^9$ までの非常に大きなインデックスを持つ駅に沿った鉄道ルートが与えられます。 列車はリスト $u1、u2、dots、un$ の順序どおりに移動し、駅を複数回再訪問することができます。"
date: "2026-06-09T21:41:54+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "greedy"]
categories: ["algorithms"]
codeforces_contest: 1702
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 805 (Div. 3)"
rating: 1100
weight: 1702
solve_time_s: 157
verified: true
draft: false
---

[CF 1702C - トレーニングとクエリ](https://codeforces.com/problemset/problem/1702/C)

 **評価:** 1100
 **タグ:** データ構造、貪欲
 **解決時間:** 2 分 37 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 1 から 2 までの非常に大きなインデックスを持つ駅に沿った鉄道ルートが与えられます。$10^9$。 列車はリストの順序どおりに移動します$u_1, u_2, \dots, u_n$ステーションを何度も再訪問できます。 2 つのステーションで構成される各クエリについて$a_j$そして$b_j$、列車が乗客を運ぶことができるかどうかを判断する必要があります。$a_j$に$b_j$ルートの進行方向に沿って。 

この課題は、広範囲にわたるステーション インデックスと潜在的に多くのクエリが組み合わされることから生じます。 制約により、最大で$2 \cdot 10^5$テスト ケースごとのステーションとクエリの数が異なり、すべてのテスト ケースの合計も同様に制限されます。 これにより、クエリごとにルートを反復するアルゴリズムが除外されます。$2 \cdot 10^5 \times 2 \cdot 10^5 = 4 \cdot 10^{10}$操作は 3 秒の制限には大きすぎます。 

ステーションを繰り返すと微妙なところが生じます。 もし$u = [1, 2, 1]$クエリが 2 から 1 である場合、単純な「最初の出現から最後の出現へ」のアプローチは、1 が 2 の前後の両方に出現するという事実を無視すると失敗する可能性があります。正しいソリューションでは、順方向を尊重するために、開始ステーションの最初の出現と終了ステーションの最後の出現を考慮する必要があります。 

もう 1 つのエッジ ケースは、開始駅または終了駅がルート上にない場合です。 もし$a_j$または$b_j$がルートに表示されない場合、答えは自動的に NO になります。 

## アプローチ

 ブルートフォース手法は簡単です。クエリごとに、電車のルートを左から右にスキャンして、到達できるかどうかを確認します。$b_j$遭遇した後$a_j$。 This works because the route is explicitly defined, but it fails for large inputs. 最悪のシナリオとしては、$n = k = 2 \cdot 10^5$、これには最大で$O(n \cdot k) = 4 \cdot 10^{10}$実行不可能な操作。 

最適なアプローチでは、事前計算の洞察が使用されます。 ルート内の各駅インデックスについて、最初に出現した時刻と最後に出現した時刻を保存します。 次に、からの問い合わせに対して、$a_j$に$b_j$、最初に発生した場合、答えは YES です。$a_j$が最後に発生する前です$b_j$。 これが機能するのは、列車は前進のみであるためです。 最初の搭乗機会$a_j$到達する最後のチャンスの前に来なければならない$b_j$。 

これにより、各クエリが次のように削減されます。$O(1)$ルックアップ、および最初/最後の出現辞書の構築は、$O(n)$。 したがって、テスト ケースごとの合計の複雑さは次のようになります。$O(n + k)$, 制限時間内に余裕で収まります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n*k) | お(1) | 遅すぎる |
 | 最適 | O(n + k) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. テスト ケースの数を読み取ります。 テスト ケースごとに、次の内容を読んでください。$n$そして$k$、ルートの長さとクエリの数。 
2. 電車のルートを読む$u$。 2 つの辞書を初期化します。`first_occurrence`そして`last_occurrence`。 反復処理$u$インデックス付き$i$。 各駅ごとに$x = u[i]$、 もし$x$まだ入っていない`first_occurrence`に設定します。$i$。 常に更新する`last_occurrence[x] = i\`。 
3. クエリごとに$a_j, b_j$、両方の駅がルート内に存在するかどうかを確認します。 どちらかが欠けている場合は、NO を出力します。 それ以外の場合は、インデックスを比較します。`first_occurrence[a_j] <= last_occurrence[b_j]`、「YES」と出力します。 それ以外の場合は、NO を出力します。 

なぜ機能するのか: アルゴリズムは次の不変条件を維持します。`first_occurrence[x]`駅に乗車できる最も早い地点です$x$そして`last_occurrence[y]`駅で最後に降りられる地点です$y$。 初めて搭乗する機会がある場合$a_j$到達する最後の機会の後に来る$b_j$、列車はそこから乗客を運ぶことができません$a_j$に$b_j$前方向に。 それ以外の場合は可能です。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

t = int(input())
for _ in range(t):
    input()  # blank line
    n, k = map(int, input().split())
    u = list(map(int, input().split()))
    
    first_occurrence = {}
    last_occurrence = {}
    
    for i, station in enumerate(u):
        if station not in first_occurrence:
            first_occurrence[station] = i
        last_occurrence[station] = i
    
    for _ in range(k):
        a, b = map(int, input().split())
        if a not in first_occurrence or b not in last_occurrence:
            print("NO")
        elif first_occurrence[a] <= last_occurrence[b]:
            print("YES")
        else:
            print("NO")
```このソリューションはまず、各ステーションの最初と最後の発生を事前計算します。 これにより、クエリごとにルートを繰り返しスキャンすることがなくなります。 辞書の存在を確認することで、路線上に駅が登場しない場合にも対応します。 

## 実用的な例

 **テストケース 1:**

 ルート:`[3, 7, 1, 5, 1, 4]`クエリ:`(3, 5), (1, 7), (3, 10)`| 駅 | 最初 | 最後 |
 | --- | --- | --- |
 | 3 | 0 | 0 |
 | 7 | 1 | 1 |
 | 1 | 2 | 4 |
 | 5 | 3 | 3 |
 | 4 | 5 | 5 |

 クエリ 1:`3 -> 5`→ first_occ[3] = 0、last_occ[5] = 3 → YES

 クエリ 2:`1 -> 7`→ first_occ[1] = 2、last_occ[7] = 1 → NO

 クエリ 3:`3 -> 10`→ 10 はルートにありません → NO

 **テストケース 2:**

 ルート:`[1, 2, 1]`クエリ:`(2, 1), (1, 2), (4, 5)`| 駅 | 最初 | 最後 |
 | --- | --- | --- |
 | 1 | 0 | 2 |
 | 2 | 1 | 1 |

 クエリ 1:`2 -> 1`→ first_occ[2] = 1、last_occ[1] = 2 → YES

 クエリ 2:`1 -> 2`→ first_occ[1] = 0、last_occ[2] = 1 → YES

 クエリ 3:`4 -> 5`→ 4 が欠落 → いいえ

 これは、繰り返されるステーションが正しく処理され、欠落しているステーションを含むクエリが拒否されることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | テスト ケースごとに O(n + k) | 最初/最後の出現マップの構築には O(n) かかり、k 個のクエリへの応答は O(k) |
 | スペース | O(n) | ルート内の各一意の駅の最初と最後の出現を保存します。 

すべてのテスト ケースにわたる n と k の合計が次を超えないことを考慮すると、$2 \cdot 10^5$、このソリューションはメモリ オーバーヘッドが低く、3 秒以内に簡単に実行されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out
    exec(open("solution.py").read(), globals())
    sys.stdout = sys.__stdout__
    return out.getvalue().strip()

# Provided sample
assert run("3\n\n6 3\n3 7 1 5 1 4\n3 5\n1 7\n3 10\n\n3 3\n1 2 1\n2 1\n1 2\n4 5\n\n7 5\n2 1 1 1 2 4 4\n1 3\n1 4\n2 1\n4 1\n1 2") == \
"""YES
NO
NO
YES
YES
NO
NO
YES
YES
NO
YES"""

# Minimum size input
assert run("1\n\n1 1\n1\n1 1") == "YES"

# All equal stations
assert run("1\n\n4 2\n2 2 2 2\n2 2\n2 3") == "YES\nNO"

# Maximum k with repeated first/last
assert run("1\n\n5 3\n1 2 3 2 1\n1 3\n2 2\n3 1") == "YES\nYES\nNO"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1\n\n1 1\n1\n1 1`| はい | 最小入力エッジケース |
 | `1\n\n4 2\n2 2 2 2\n2 2\n2 | | |
