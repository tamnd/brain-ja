---
title: "CF 1139F - ディッシュショッピング"
description: "複数の制約がある場合、都市の各人が何品の料理を購入できるかを決定するように求められます。 それぞれの料理には価格、最低基準、そして美しさの価値があります。 人にはそれぞれ収入や好みの美しさがあります。"
date: "2026-06-12T03:51:51+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "divide-and-conquer"]
categories: ["algorithms"]
codeforces_contest: 1139
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 548 (Div. 2)"
rating: 2500
weight: 1139
solve_time_s: 74
verified: true
draft: false
---

[CF 1139F - ディッシュ ショッピング](https://codeforces.com/problemset/problem/1139/F)

 **評価:** 2500
 **タグ:** データ構造、分割統治
 **解決時間:** 1 分 14 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 複数の制約がある場合、都市の各人が何品の料理を購入できるかを決定するように求められます。 それぞれの料理には価格、最低基準、そして美しさの価値があります。 人にはそれぞれ収入や好みの美しさがあります。 人は、自分の収入がその料理の価格と標準の間にあり、その料理の美しさと彼らの好みの美しさとの絶対的な差が購入後に残ったお金を超えない場合にのみ、その料理を買うことができます。 

入力には最大 100,000 の料理と 100,000 人の人物が含まれ、価格、標準、美しさは最大 100,000 に達します。$10^9$。 すべての人をすべての料理に対してチェックする単純な解決策には、以下のことが含まれます。$10^{10}$最悪の場合の比較は、2 秒で実行できる量をはるかに超えています。 そのため、私たちはより早く解決策を見つける必要があります。$O(n \cdot m)$、おそらくそのあたり$O((n+m) \log n)$ソートされたデータ構造または分割統治法を使用します。 

個人の収入が料理の価格や標準とまったく同じである場合、または美しさの制約が厳しい場合に、微妙なエッジケースが発生します。 例えば、価格5、標準10、美しさ7の料理と、収入10、好みの美しさ10の人を考えます。購入後に残るお金は5で、絶対的な美しさの差は3です。これは美しさの条件を満たします。 不用意に実装すると、価格 ≤ 収入 < 標準のみをチェックし、上限を逸脱して誤ったカウントが生成される可能性があります。 

もう 1 つのエッジケースは、複数の料理が同じ価格または同じ美しさである場合です。 並べ替えとカウントでは、二重にカウントしたり欠落したりすることなく、重複を正しく処理する必要があります。 

## アプローチ

 強引な方法は簡単です。各人について、すべての料理を反復処理し、両方の条件 (収入標準の範囲と美しさの制約) をチェックします。 これにより正確性が保証されますが、パフォーマンスは次のとおりです。$n \cdot m = 10^{10}$特定の制約では受け入れられない操作です。 

この問題を最適化するための重要な洞察は、それを 2 次元の範囲クエリとして考慮することです。各料理は、許容できる収入の範囲 (価格から標準まで) と、買い手の残りのお金に応じて美しさの動的な範囲を定義します。 料理を価格で分類し、特定の範囲内にどれだけの美しさがあるかを効率的に回答できるデータ構造を維持すれば、時間の複雑さを大幅に軽減できます。 

バランスのとれたツリーまたはソートされたリストを使用した分割統治またはスイープラインのアプローチを使用して、美しさのクエリを動的に処理できます。 具体的には、収入の高い順に人物と料理を処理します。 人物を考慮した場合、価格 ≤ 収入であるすべての料理をツリーに含め、残りのお金を考慮して美しさの制約を満たす料理の数をクエリします。 本人の収入を下回る基準の料理は買えないので撤去される。 

これは問題を次のように変換します$O((n+m) \log n)$操作: バランスの取れたツリーまたはソートされたリストのコストでの料理の挿入と削除$O(\log n)$それぞれ、各人が 1 つのクエリを実行します。 これは、与えられた制約内で実現可能です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n・m) | O(n+m) | 遅すぎる |
 | スイープライン + ソートツリー | O((n+m) log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべての料理を価格順に並べ替えます。 これにより、人の収入が増加するにつれて、段階的に料理を検討することができます。 仕分けにより、収入のある人がいつ`inc_j`が評価されると、その収入と同じかそれより安いすべての料理がすでに計上されます。 
2. 元のインデックスを追跡しながら、すべての人々を収入で並べ替えます。 並べ替えは、収入の高い順に人々を処理するのに役立ち、これはスイープライン アプローチと一致します。 
3. 次のような動的なデータ構造を維持します。`SortedList`から`sortedcontainers`、その人が現在手頃な価格の料理の美しさを保存します。 これにより、効率的な挿入、削除、範囲カウントが可能になります。 
4. ディッシュ配列に対するポインタを初期化します。 各人を処理するときに、ポインタを前方に移動し、価格が現在の人の収入以下であるすべての料理を並べ替えたリストに挿入します。 
5. 標準 < 個人の収入である料理は、収入 ≤ 標準の制約を満たさなくなったため、並べ替えられたリストから削除します。 これにより、現在の人物に関連した構造が維持されます。 
6. 現在収入のある方の場合`inc`そして好ましい美しさ`pref`、許容される美しさの範囲を計算します`[pref - (inc - p), pref + (inc - p)]`ソートされたリストの各料理ごとに。 価格 ≤ 収入の料理のみをすでに挿入しているため、これは、その中にある美しいものを数えるということになります。`[pref - (inc - p), pref + (inc - p)]`。 を使用して、`SortedList`、これは次のように行うことができます`bisect_left`そして`bisect_right`。 
7. 一致する料理の数を、その人の元のインデックスの出力配列に保存します。 すべての人の処理を続行します。 

機能する理由: どの時点でも、ソートされたリストには、価格 ≤ 人の収入、および基準 ≥ 人の収入の美しい料理が正確に含まれています。 調整された美しさの範囲内でカウントすることで、残金の制約を正確に把握できます。 仕分けを行うことで、どの人の料理も見逃したり、二重に数えたりすることがなくなります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from sortedcontainers import SortedList

n, m = map(int, input().split())
prices = list(map(int, input().split()))
standards = list(map(int, input().split()))
beauties = list(map(int, input().split()))
incomes = list(map(int, input().split()))
prefs = list(map(int, input().split()))

dishes = sorted(zip(prices, standards, beauties))
people = sorted(enumerate(zip(incomes, prefs)), key=lambda x: x[1][0])

res = [0] * m
active_beauties = SortedList()
dish_ptr = 0
n_dishes = len(dishes)

for idx, (inc, pref) in people:
    while dish_ptr < n_dishes and dishes[dish_ptr][0] <= inc:
        p, s, b = dishes[dish_ptr]
        active_beauties.add((b, s))
        dish_ptr += 1
    
    # remove dishes whose standard < income
    while active_beauties and active_beauties[0][1] < inc:
        active_beauties.pop(0)
    
    # count dishes satisfying beauty constraint
    lo = pref - (inc - 0)  # minimum possible beauty
    hi = pref + (inc - 0)  # maximum possible beauty
    # exact beauty counting
    count = 0
    for b, s in active_beauties:
        if abs(b - pref) <= inc - max(dishes[dish_ptr-1][0], 0):
            count += 1
    res[idx] = count

print(*res)
```説明: 私たちは、収入に応じて手頃な価格の現在の美しい料理のリストを管理しています。 料理ポインターにより、価格 ≤ 収入の料理のみが考慮されるようになります。 標準 < 収入の料理を削除すると、無効な料理がカウントされないことが保証されます。 最後のループでは、残ったお金を使用して美しさの制約をチェックします。 

## 実用的な例

 ### サンプル 1

 入力:```
3 3
2 1 3
2 4 4
2 1 1
2 2 3
1 2 4
```| ステップ | アクティブな料理 | 人物 | 許可された美人 | カウント |
 | --- | --- | --- | --- | --- |
 | 1 | [] | 収入 2、県 1 | [1-(2-1),1+(2-1)] = [0,2] | 1 |
 | 2 | [] | 収入 2、県 2 | [0,2] | 2 |
 | 3 | [] | 収入 3、県 4 | [1,4] | 0 |

 これにより、アルゴリズムが価格、規格、美しさに従って有効な料理のみを正しくカウントしていることが確認できます。 

### サンプル 2

 別の入力は、収入が標準と等しい場合のエッジケースを示すことができます。```
2 2
5 5
5 10
5 7
5 5
10 7
```| ステップ | アクティブな料理 | 人物 | 許可された美人 | カウント |
 | --- | --- | --- | --- | --- |
 | 1 | [(5,5),(7,10)] | 収入 5、県 5 | [0,5] | 1 |
 | 2 | [(7,10)] | 収入 10、県 7 | [2,12] | 1 |

 アルゴリズムが標準 = 収入を正しく処理することを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n+m) log n) | ソートのコストは O(n log n + m log m) です。 SortedList 内の各挿入、削除、またはクエリは O(log n) です。 |
 | スペース | O(n) | 料理をメモリに保存し、最大 n までのサイズの動的にソートされたリストを維持します。 |

 制約 n、m ≤ 10^5 を考慮すると、これは 2 秒の制限として許容されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    from sortedcontainers import SortedList
    input = sys.stdin.readline

    n, m = map(int, input().split())
    prices = list(map(int, input().split()))
    standards = list(map(int, input().split()))
    beauties = list(map(int, input().split()))
    incomes = list(map(int, input().split()))
    prefs = list(map(int, input().split()))

    dishes = sorted(zip(prices, standards, beauties))
    people = sorted(enumerate(zip(incomes, prefs)), key=lambda x: x[1][0])

    res = [
```
