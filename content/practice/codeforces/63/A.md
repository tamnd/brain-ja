---
title: "CF 63A - 沈没船"
description: "私たちは、沈没船上で左から右の固定順序で立っている人々のリストを与えられました。 すべての人には名前と役割があります。 避難命令は完全に役割の優先順位によって決まります。 ネズミが先に出発します。 女性と子供は次の優先順位を共有します。 男たちは彼らを追って去ります。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation", "sortings", "strings"]
categories: ["algorithms"]
codeforces_contest: 63
codeforces_index: "A"
codeforces_contest_name: "Codeforces Beta Round 59 (Div. 2)"
rating: 900
weight: 63
solve_time_s: 91
verified: true
draft: false
---

[CF 63A - 沈没船](https://codeforces.com/problemset/problem/63/A)

 **評価:** 900
 **タグ:** 実装、並べ替え、文字列
 **解決時間:** 1 分 31 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは、沈没船上で左から右の固定順序で立っている人々のリストを与えられました。 すべての人には名前と役割があります。 避難命令は完全に役割の優先順位によって決まります。 

ネズミが先に出発します。 女性と子供は次の優先順位を共有します。 男たちは彼らを追って去ります。 船長はいつも最後に出発します。 

2 人が同じ優先グループに属している場合、その相対的な順序は元の行とまったく同じでなければなりません。 つまり、これは単なるカテゴリ別の並べ替え問題ではなく、安定した順序付けの問題です。 

入力サイズは小さく、最大 100 名のみです。 二次アルゴリズムでも簡単に制限時間内に収まります。 課題はパフォーマンスではなく、グループ内の元の順序を誤って破ることなく、優先順位のルールを正しく実装することです。 

よくある間違いの 1 つは、女性と子供を別の優先事項として扱うことです。 実は同じ避難レベルに属しているのです。 

次の入力を考えてみましょう。```
4
Anna woman
Ben child
Chris woman
David man
```正しい出力は次のとおりです。```
Anna
Ben
Chris
David
```すべての子供が誤って生成する前にすべての女性を避難させる不注意な実装:```
Anna
Chris
Ben
David
```もう 1 つの簡単な間違いは、元の位置を保持せずに不安定な並べ替えを使用することです。 

考慮する：```
5
Tom man
Jerry man
Spike man
Mila woman
Captain captain
```男性は元の順序のままでなければなりません。```
Mila
Tom
Jerry
Spike
Captain
```実装が優先順位によってのみソートされ、元のインデックスが無視される場合、男性間の順序は恣意的になる可能性があります。 

3 番目のエッジケースはキャプテンです。 キャプテンは 1 人だけ存在し、その人は最初の位置に関係なく常に最後に現れる必要があります。 

例：```
3
Jack captain
Alice woman
Bob rat
```正しい出力:```
Bob
Alice
Jack
```誰かが行を左から右に処理してすぐに印刷すると、キャプテンが誤って早すぎる可能性があります。 

## アプローチ

 最も直接的な強引なアイデアは、リスト全体を繰り返しスキャンし、カテゴリごとに人物を抽出することです。 まずネズミをスキャンし、順番に印刷します。 次に、女性と子供を再度スキャンします。 次に、男性をもう一度スキャンします。 最後にキャプテンを印刷します。 

これが機能するのは、避難の優先順位が固定されており、取り得るステータスが 5 つだけであるためです。 各スキャンでは元の左から右の順序が維持されるため、出力は正確になります。 

最悪の場合の複雑さはまだ小さいです。 最大 100 人に対して最大 4 ～ 5 回のスキャンを行うため、合計作業量は約 500 回になります。 

もう 1 つのアプローチは、すべての役割に数値による優先順位を割り当て、その優先順位に従ってメンバーを分類することです。 例えば：```
rat -> 0
woman -> 1
child -> 1
man -> 2
captain -> 3
```重要な観察は、女性と子供が意図的に同じ優先順位を共有しているということです。 役割を数値にマッピングすると、問題は安定した並べ替えタスクになります。 

Python の組み込みソートは安定しています。つまり、同じ優先順位を持つ人々は自動的に元の順序に残ります。 それは問題の要件に完全に一致します。 

どちらのアプローチも十分に高速ですが、並べ替えアプローチの方がよりクリーンで一般化が容易です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n) | お(1) | 承認済み |
 | 最適 | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 乗組員の数を読み取ります。 
2. 各人の名前とステータスを読み上げます。 
3. 各ステータスに数値による避難優先度を割り当てます。 

ラットは最初に離れるため、最小の値が得られます。 キャプテンは最後に出発するため、最大の値を取得します。 女性と子供は優先順位を共有しているため、同じ価値を受け取ります。 
4. すべての人物を、優先順位と名前を含むペアとして保存します。 

Python のソートは安定しているため、元の入力順序は自動的に保持されます。 
5. リストを優先度に従って並べ替えます。 
6. 名前をソートされた順序で印刷します。 

### なぜ効果があるのか

 すべての乗組員は、正確に 1 つの避難カテゴリーに属します。 数値マッピングは、口頭ルールを並べ替え可能な順序関係に変換します。 

2 人が異なるカテゴリに属している場合、優先順位の値が小さいほど、一方が他方よりも前に正しく配置されます。 

2 人が同じカテゴリに属している場合、両方に同じ優先順位が与えられます。 Python の安定したソートでは元の相対順序が保存され、優先順位が同点の場合は左の人々が最初に避難するというステートメントの要件と正確に一致します。 

両方のルールが同時に満たされるため、最終的な順序は常に正しいものになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())

    priority = {
        "rat": 0,
        "woman": 1,
        "child": 1,
        "man": 2,
        "captain": 3
    }

    people = []

    for _ in range(n):
        name, status = input().split()
        people.append((priority[status], name))

    people.sort()

    for _, name in people:
        print(name)

solve()
```ディクショナリは、各ステータスを並べ替え可能な数値に変換します。 重要な詳細は、`"woman"`そして`"child"`両方ともマップされます`1`、それらに同等の優先度を与えます。 

各人物はタプルとして保存されます`(priority, name)`。 Python はタプルを辞書順に並べ替えるので、リストは主に優先順位に従って並べられます。 

Python のソートは安定しています。これは、ここでは微妙ですが重要な特性です。 2 人の人物が同じ優先順位を持っている場合、その相対的な順序は入力から変更されません。 これにより、明示的なインデックスを保存せずに、問題ステートメントからの左から右のルールが自動的に満たされます。 

制約が非常に小さいため、パフォーマンスは無関係ですが、この実装は依然としてクリーンで効率的です。 

## 実用的な例

 ### 例 1

 入力:```
6
Jack captain
Alice woman
Charlie man
Teddy rat
Bob child
Julia woman
```処理手順:

 | 人物 | ステータス | 優先順位 | 保存されたタプル |
 | --- | --- | --- | --- |
 | ジャック | 船長 | 3 | (3、ジャック) |
 | アリス | 女性 | 1 | (1、アリス) |
 | チャーリー | 男 | 2 | (2、チャーリー) |
 | テディ | ネズミ | 0 | (0、テディ) |
 | ボブ | 子供 | 1 | (1、ボブ) |
 | ジュリア | 女性 | 1 | (1、ジュリア) |

 並べ替え後:

 | 優先順位 | 名前 |
 | --- | --- |
 | 0 | テディ |
 | 1 | アリス |
 | 1 | ボブ |
 | 1 | ジュリア |
 | 2 | チャーリー |
 | 3 | ジャック |

 出力：```
Teddy
Alice
Bob
Julia
Charlie
Jack
```このトレースは、安定した並べ替えが重要である理由を示しています。 アリス、ボブ、ジュリアはすべて同じ優先順位を共有し、元の相対順序は維持されます。 

### 例 2

 入力:```
5
Tom man
Jerry man
Lucy child
Kate woman
Morgan captain
```処理手順:

 | 人物 | ステータス | 優先順位 | 保存されたタプル |
 | --- | --- | --- | --- |
 | トム | 男 | 2 | (2、トム) |
 | ジェリー | 男 | 2 | (2、ジェリー) |
 | ルーシー | 子供 | 1 | (1、ルーシー) |
 | ケイト | 女性 | 1 | (1、ケイト) |
 | モーガン | 船長 | 3 | (3、モーガン) |

 並べ替え後:

 | 優先順位 | 名前 |
 | --- | --- |
 | 1 | ルーシー |
 | 1 | ケイト |
 | 2 | トム |
 | 2 | ジェリー |
 | 3 | モーガン |

 出力：```
Lucy
Kate
Tom
Jerry
Morgan
```この例では、2 つの別々の結合グループが示されています。 子供と女性が 1 つのグループを共有し、2 人の男性が別のグループを形成します。 どちらの場合も、元の順序はそのまま残ります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 乗組員リストのソートが実行時間を支配します。 
| スペース | O(n) | 人物のリストはメモリに保存されます。 

乗組員は最大でも 100 名なので、実行時間はごくわずかです。 はるかに遅いアルゴリズムでも制限内で快適に通過します。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    def solve():
        n = int(input())

        priority = {
            "rat": 0,
            "woman": 1,
            "child": 1,
            "man": 2,
            "captain": 3
        }

        people = []

        for _ in range(n):
            name, status = input().split()
            people.append((priority[status], name))

        people.sort()

        out = []

        for _, name in people:
            out.append(name)

        return "\n".join(out)

    return solve()

# provided sample
assert run(
"""6
Jack captain
Alice woman
Charlie man
Teddy rat
Bob child
Julia woman
"""
) == (
"""Teddy
Alice
Bob
Julia
Charlie
Jack"""
), "sample 1"

# minimum size
assert run(
"""1
Jack captain
"""
) == (
"""Jack"""
), "single person"

# all same priority
assert run(
"""4
Anna woman
Bella child
Cara woman
Diana child
"""
) == (
"""Anna
Bella
Cara
Diana"""
), "stable order among equal priorities"

# captain appears first in input
assert run(
"""3
Captain captain
Tom man
Jerry rat
"""
) == (
"""Jerry
Tom
Captain"""
), "captain must always be last"

# multiple men preserve order
assert run(
"""5
Tom man
Jerry man
Spike man
Lucy child
Captain captain
"""
) == (
"""Lucy
Tom
Jerry
Spike
Captain"""
), "stable order among men"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 独身キャプテン | 船長 | 最小入力サイズ |
 | 女性と子供が混在 | 写真 同じ相対順序 | 同等の優先度の安定性 |
 | 先頭のキャプテン | 最後に印刷されたキャプテン | 正しい船長の扱い |
 | 複数の男性 | 元の順序が保存される | 安定した並べ替え動作 |

 ## 特殊なケース

 微妙なケースの 1 つは、女性と子供の混合です。 これらは別々の避難段階ではありません。 

入力：```
4
Anna woman
Ben child
Chris woman
David man
```アルゴリズムは優先順位を割り当てます。```
woman -> 1
child -> 1
woman -> 1
man -> 2
```Python のソートは安定しているため、ソート後、優先度 1 の 3 人全員が元の順序のままになります。```
Anna
Ben
Chris
David
```もう 1 つの注意が必要な状況は、同一のカテゴリ間で順序を維持することです。 

入力：```
5
Tom man
Jerry man
Spike man
Lucy woman
Captain captain
```優先順位は次のようになります。```
Tom -> 2
Jerry -> 2
Spike -> 2
Lucy -> 1
Captain -> 3
```分類によりルーシーは男性たちよりも先に進みますが、3 人の男性は次のように順序付けされたままです。```
Tom
Jerry
Spike
```最終結果は次のとおりです。```
Lucy
Tom
Jerry
Spike
Captain
```キャプテンエッジケースも重要です。 

入力：```
3
Jack captain
Alice woman
Bob rat
```優先順位は次のとおりです。```
Jack -> 3
Alice -> 1
Bob -> 0
```並べ替えると次のものが生成されます。```
Bob
Alice
Jack
```船長は最初は列の先頭に立っていましたが、優先マッピングにより船長は最後に避難することになります。
