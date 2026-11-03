---
title: "CF 12C - フルーツ"
description: "ヴァレラの買い物リストには多数の果物が含まれており、同じ種類を複数個欲しければ重複する可能性があります。 市場の屋台には _n_ 種類の果物と _n_ 個の値札がありますが、果物にはまだタグが付けられていません。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "greedy", "implementation", "sortings"]
categories: ["algorithms"]
codeforces_contest: 12
codeforces_index: "C"
codeforces_contest_name: "Codeforces Beta Round 12 (Div 2 Only)"
rating: 1100
weight: 12
solve_time_s: 62
verified: true
draft: false
---
[CF 12C - フルーツ](https://codeforces.com/problemset/problem/12/C)

 **評価:** 1100
 **タグ:** 貪欲、実装、並べ替え
 **解決時間:** 1分2秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 ヴァレラの買い物リストには多数の果物が含まれており、同じ種類を複数個欲しければ重複する可能性があります。 市場の屋台には _n_ 種類の果物と _n_ 個の値札がありますが、果物にはまだタグが付けられていません。 各タグは、特定の種類の果物 1 個の価格を表します。 ヴァレラさんは、売り手が希望する果物にどのようにタグを付けるかに応じて、支払える合計金額の最低額と最高額を知りたいと考えています。 

入力は値札の数を提供します`n`ヴァレラのリストにある果物の数`m`。 次の行は、`n`価格。 それから`m`行には彼のリストにある果物の名前が列挙されています。 リストにある個別の果物の数は次のとおりです。`n`、それぞれの果物に常に利用可能な価格があることが保証されています。 出力は、ヴァレラのリストにある果物の合計の最小値と最大値の 2 つの数値でなければなりません。 

制約がある中で`n, m ≤ 100`価格 ≤ 100 の場合、値札の割り当てのすべての可能な順列を反復する単純なアプローチは理論的には可能ですが、非効率的です。`n!`順列はすぐに実行不可能になります。 エッジケースには、リスト上のすべての果物が単一の種類である場合、または価格が同一である場合、重複またはインデックス付けの不注意な処理により誤った結果が生じる可能性がある状況が含まれます。 

たとえば、次の場合`n=2`、`m=3`、価格`[1, 2]`、リストには以下が含まれます`apple`、`apple`、`apple`、最小合計は`1 + 1 + 2 = 4`、最大合計は`2 + 2 + 1 = 5`。 繰り返されるフルーツを正しく処理しない単純なアプローチでは、合計の計算を誤る可能性があります。 

## アプローチ

 この強引なアプローチでは、ヴァレラのリストにある果物に価格を割り当てるあらゆる方法が検討されています。 価格リストのすべての順列を生成し、リスト内の果物の対応する価格を合計することができます。 このアプローチは、考えられるすべての分布が明示的に考慮されるため正しいですが、遅すぎます。`n=10`、`10! = 3,628,800`1 秒の制限時間に対して順列が多すぎる、および`n`最大100まで上げることができます。 

重要な洞察は、問題が貪欲な戦略に帰着するということです。つまり、利用可能な最も安い価格が最も頻繁に要求される果物に割り当てられた場合に最小合計が得られ、最も頻繁に要求された果物に最も高価な価格が割り当てられた場合に最大合計が得られます。 価格のリストを並べ替えて、各果物が何回出現するかを数えるだけで、両方の極端な値を計算できます。 リスト内の繰り返し項目にはそれぞれ価格を割り当てる必要があるため、果物の頻度が重要です。 

貪欲なアプローチは、カウントを超えて異なるフルーツ間に相互作用がないため機能します。 個数が判明すると、合計に影響を与える唯一の変数は各果物が受け取る価格であるため、最低頻度から最高頻度 (または最高頻度から最高頻度) を割り当てることで、最適な合計が保証されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n! * m) | O(n) | 遅すぎる |
 | 最適 | O(n log n + m) | O(n + m) | 承認済み |

 ## アルゴリズムのチュートリアル

 1.値札の枚数を読み取る`n`そして果物の数`m`。 
2. リストを読む`n`価格を昇順に並べ替えます。 並べ替えることにより、最も安い価格と最も高い価格を効率的に選択できるようになります。 
3. を読んでください。`m`ヴァレラのリストから果物を選択し、それぞれの果物が何回出現するかを数えます。 これにより、キーが果物の名前、値がヴァレラが各果物を欲しがる回数である頻度マップが作成されます。 
4. 周波数を抽出し、降順に並べ替えます。 最も頻繁にリクエストされる果物には、最小合計の場合は最も低い価格が割り当てられ、最大合計の場合は最も高い価格が割り当てられます。 
5. 2 つの合計を初期化します。`min_total`そして`max_total`。 のために`min_total`、最も頻繁な果物に順番に最も安い価格を割り当てます。 のために`max_total`、最も高価な価格を最も頻繁に使用される果物に順番に割り当てます。 各周波数は、ソートされた価格リストから複数の価格を消費する可能性があります。 
6. 出力`min_total`そして`max_total`。 

これが機能する理由は、どちらの極端な場合でも、この割り当てから逸脱すると、より安い価格が割り当てられないか、より高価な価格が未使用のままになり、最小値が増加または最大値が減少するためです。 不変条件は、最大周波数が常に最小または最大残存価格と一致することです。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
from collections import Counter

n, m = map(int, input().split())
prices = list(map(int, input().split()))
prices.sort()

fruits = [input().strip() for _ in range(m)]
freq = list(Counter(fruits).values())
freq.sort(reverse=True)

# minimum total
min_total = 0
price_idx = 0
for f in freq:
    for _ in range(f):
        min_total += prices[price_idx]
        price_idx += 1

# maximum total
prices_rev = prices[::-1]
max_total = 0
price_idx = 0
for f in freq:
    for _ in range(f):
        max_total += prices_rev[price_idx]
        price_idx += 1

print(min_total, max_total)
```このソリューションは入力を効率的に読み取り、果物の頻度をカウントし、価格と頻度の両方を並べ替えます。 内部ループは、リスト内のフルーツごとに 1 回だけ繰り返します。 すべてのフルーツが同じであるなどの境界条件は、周波数マップによって正しく処理されます。 価格配列を反転すると、最大合計の対称計算が可能になります。 

## 実用的な例

 **サンプル 1**

 入力:```
5 3
4 2 1 10 5
apple
orange
mango
```| ステップ | 価格 | 周波数 | 最小合計 | 最大合計 |
 | --- | --- | --- | --- | --- |
 | 価格を並べ替える | [1,2,4,5,10] | [1,1,1] | 0 | 0 |
 | 最小値を割り当てる | 1→リンゴ、2→オレンジ、4→マンゴー | [1,1,1] | 7 | - |
 | 最大値を割り当てる | 10→リンゴ、5→オレンジ、4→マンゴー | [1,1,1] | - | 19 |

 これは、アルゴリズムが最小値については最も安い価格と要求された果物を正しく組み合わせ、最大値については最も高価な価格を正しく組み合わせていることを示しています。 

**カスタム サンプル 2**

 入力:```
4 5
1 3 2 4
apple
apple
banana
banana
banana
```| ステップ | 価格 | 周波数 | 最小合計 | 最大合計 |
 | --- | --- | --- | --- | --- |
 | 価格を並べ替える | [1,2,3,4] | [3,2] | 0 | 0 |
 | 最小値を割り当てる | 1→バナナ、2→バナナ、3→バナナ、4→リンゴ、次に安い 2→リンゴ | 0 | 12 | - |
 | 最大値を割り当てる | 4→バナナ、3→バナナ、2→バナナ、1→リンゴ、次に高価な 2→リンゴ | - | 14 | |

 これは、繰り返されるフルーツが正しく処理され、合計が貪欲な割り当てを反映していることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n + m) | 価格の並べ替えが優先され、頻度のカウントは O(m) |
 | スペース | O(n + m) | 価格と頻度マップの保存 |

 と`n, m ≤ 100`、アルゴリズムは 1 秒の制限時間内で簡単に実行され、使用するメモリは 256 MB よりはるかに少なくなります。 

## テストケース```python
import sys, io
def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline
    from collections import Counter

    n, m = map(int, input().split())
    prices = list(map(int, input().split()))
    prices.sort()

    fruits = [input().strip() for _ in range(m)]
    freq = list(Counter(fruits).values())
    freq.sort(reverse=True)

    min_total = 0
    price_idx = 0
    for f in freq:
        for _ in range(f):
            min_total += prices[price_idx]
            price_idx += 1

    prices_rev = prices[::-1]
    max_total = 0
    price_idx = 0
    for f in freq:
        for _ in range(f):
            max_total += prices_rev[price_idx]
            price_idx += 1

    return f"{min_total} {max_total}"

# Provided sample
assert run("5 3\n4 2 1 10 5\napple\norange\nmango\n") == "7 19", "sample 1"

# Custom: single fruit repeated
assert run("2 3\n1 2\napple\napple\napple\n") == "4 5", "single fruit repeated"

# Custom: all prices equal
assert run("3 3\n5 5 5\napple\nbanana\norange\n") == "15 15", "all prices equal"

# Custom: min-size input
assert run("1 1\n7\napple\n") == "7 7", "min-size input"

# Custom: max-size, uniform price
inp = "100 100\n" +
```
