---
title: "CF 106215A - 不幸な偶然"
description: "私たちにはn個の言葉が与えられています。 単語ごとに、それが文字列 \"WY\" と正確に一致するかどうかを確認する必要があります。 単語が正確に「WY」である場合、出力ではそれを「Whitney Young」に置き換えます。 1 つおきの単語は変更せずに出力する必要があります。 入力は、整数 n とそれに続く n 個の文字列で構成されます。"
date: "2026-06-25T06:50:05+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 106215
codeforces_index: "A"
codeforces_contest_name: "2025-2026 Whitney Young Practice Contest 1"
rating: 0
weight: 106215
solve_time_s: 40
verified: true
draft: false
---

[CF 106215A - 不幸な偶然](https://codeforces.com/problemset/problem/106215/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 40 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは与えられています`n`言葉。 単語ごとに、それが正確に文字列であるかどうかを確認する必要があります。`"WY"`。 

言葉が正確であれば`"WY"`で置き換えます。`"Whitney Young"`出力で。 1 つおきの単語は変更せずに出力する必要があります。 

入力は整数で構成されます`n`、 に続く`n`文字列。 各文字列には、大文字と小文字の英字のみが含まれます。 出力には正確に次のものが含まれます`n`行、入力単語ごとに 1 つの結果。 

制約は非常に小さいです。 単語数は最大 2025 個で、各単語の長さは最大 67 です。すべての単語のすべての文字を検査する最も直接的なソリューションでさえ、実行される操作は数十万回のみです。 どのリニア スキャンも簡単に十分に高速です。 

ミスの主な原因は、何が一致とみなされるかを誤解することです。 

次の入力を考えてみましょう。```
3
WY
wy
WYOMING
```正しい出力は次のとおりです。```
Whitney Young
wy
WYOMING
```最初の単語のみが置換されます。 比較では大文字と小文字が区別されるため、`"wy"`一致しません。 また、`"WYOMING"`含まれています`"WY"`接頭辞として使用されますが、単語全体は次と等しくありません`"WY"`。 

もう 1 つの簡単な間違いは、部分文字列の置換を試みることです。 例えば：```
2
AWYB
WY
```正しい出力は次のとおりです。```
AWYB
Whitney Young
```完全な単語の同等性のみが重要です。 

## アプローチ

 最も簡単な解決策は、単語を 1 つずつ処理することです。 

それぞれの単語について、次のように比較します。`"WY"`。 

それらが等しい場合、出力します`"Whitney Young"`。 それ以外の場合は、元の単語を出力します。 

ブルートフォース解釈では、すべての文字を手動でチェックする可能性があります。 各単語の長さは最大 67 文字であるため、制限内であれば問題なく実行できます。 総作業量は、入力文字の総数に比例します。 

重要な点は、タスクが文字列内のパターン マッチングを要求していないことです。 単語が 1 つの特定の値と正確に等しいかどうかのみを尋ねます。 最新の言語ではすでに文字列等価演算が提供されているため、解決策はほとんど簡単になっています。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート フォースの文字ごとの比較 | O(入力の合計の長さ) | お(1) | 承認済み |
 | 文字列の直接の等価性チェック | O(入力の合計の長さ) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 整数を読み取る`n`。 
2. 繰り返します`n`回:

 次の単語を読んでください。 
3. 言葉が正確であれば`"WY"`、印刷`"Whitney Young"`。 
4. それ以外の場合は、単語を変更せずに出力します。 

### なぜ効果があるのか

 すべての入力単語に対して、問題は置換をトリガーする条件を 1 つだけ定義します。単語は以下に等しい必要があります。`"WY"`。 

アルゴリズムはその状態を正確にチェックします。 条件が true の場合、必要な置換テキストが出力されます。 条件が false の場合、元の単語が出力されます。 すべての単語は独立して問題定義に従って処理されるため、生成される出力は常に正しいものになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n = int(input())

for _ in range(n):
    s = input().strip()
    if s == "WY":
        print("Whitney Young")
    else:
        print(s)
```プログラムはまず単語数を読み取ります。 

後続の各行が読み取られ、末尾の改行が削除されます。 比較`s == "WY"`大文字と小文字を区別した正確な等価性チェックを実行します。 

比較が成功すると、必要な置換テキストが出力されます。 それ以外の場合は、元の単語が変更されずに出力されます。 

使用する`strip()`入力行の末尾に改行文字が含まれるため、これは重要です。 それを取り外さずに比較すると、`"WY"`失敗するだろう。 

各ワードは読み取った直後に処理できるため、追加のデータ構造は必要ありません。 

## 実用的な例

 ### 例 1

 入力:```
9
I
miss
WY
Math
Team
and
WY
Coding
Club
```トレース:

 | 単語 | 「WY」と一致しますか? | 出力 |
 | --- | --- | --- |
 | 私 | いいえ | 私 |
 | ミス | いいえ | ミス |
 | ワイオミング | はい | ホイットニー・ヤング
 | 数学 | いいえ | 数学 |
 | チーム | いいえ | チーム |
 | と | いいえ | と |
 | ワイオミング | はい | ホイットニー・ヤング
 | コーディング | いいえ | コーディング |
 | クラブ | いいえ | クラブ |

 出力：```
I
miss
Whitney Young
Math
Team
and
Whitney Young
Coding
Club
```この例は、正確な単語が出現するたびに、`"WY"`独立して交換されます。 

### 例 2

 入力:```
4
WY
wy
WYOMING
WY
```トレース:

 | 単語 | 「WY」と一致しますか? | 出力 |
 | --- | --- | --- |
 | ワイオミング | はい | ホイットニー・ヤング
 | どうして | いいえ | どうして |
 | ワイオミング州 | いいえ | ワイオミング州 |
 | ワイオミング | はい | ホイットニー・ヤング

 出力：```
Whitney Young
wy
WYOMING
Whitney Young
```これは、大文字と小文字の区別と、単語全体が一致する必要があるという要件の両方を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(入力の合計の長さ) | 各単語は 1 回読み取られ、1 回比較されます。 
| スペース | お(1) | 一度に保存できる単語は 1 つだけです |

 合計入力サイズは小さいため、このソリューションは制限内で快適に実行されます。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

def solve():
    input = sys.stdin.readline

    n = int(input())
    for _ in range(n):
        s = input().strip()
        if s == "WY":
            print("Whitney Young")
        else:
            print(s)

def run(inp: str) -> str:
    backup_stdin = sys.stdin
    backup_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    solve()

    out = sys.stdout.getvalue()

    sys.stdin = backup_stdin
    sys.stdout = backup_stdout

    return out

# provided sample
assert run(
"""9
I
miss
WY
Math
Team
and
WY
Coding
Club
"""
) == (
"""I
miss
Whitney Young
Math
Team
and
Whitney Young
Coding
Club
"""
)

# minimum size
assert run(
"""1
WY
"""
) == (
"""Whitney Young
"""
)

# no replacements
assert run(
"""3
abc
DEF
Wy
"""
) == (
"""abc
DEF
Wy
"""
)

# all replacements
assert run(
"""4
WY
WY
WY
WY
"""
) == (
"""Whitney Young
Whitney Young
Whitney Young
Whitney Young
"""
)

# substring but not exact match
assert run(
"""3
AWYB
WYOMING
WY
"""
) == (
"""AWYB
WYOMING
Whitney Young
"""
)
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 一言`WY`|`Whitney Young`| 最小入力サイズ |
 | 等しい言葉はない`WY`| オリジナルの言葉 | 誤って交換することはありません |
 | すべての言葉は等しい`WY`| すべて交換されました | 繰り返しの交換 |
 |`AWYB`、`WYOMING`、`WY`| 最後に交換しただけ | 部分文字列の一致ではなく、完全に等しい |

 ## 特殊なケース

 次のことを考慮してください。```
1
wy
```アルゴリズムは比較します`"wy"`と`"WY"`。 文字列の比較では大文字と小文字が区別されるため、それらは異なります。 出力はそのまま残ります。```
wy
```これにより、大文字と小文字の違いが正しく処理されます。 

考慮する：```
1
WYOMING
```アルゴリズムは文字列全体が以下に等しいかどうかをチェックします。`"WY"`。 そうではないので、出力は次のようになります。```
WYOMING
```これにより、単に次の内容を含む単語を置き換えるというよくある間違いを回避できます。`"WY"`部分文字列として。 

考慮する：```
3
WY
WY
WY
```各単語は独立して処理されます。 すべての比較は成功し、以下が生成されます。```
Whitney Young
Whitney Young
Whitney Young
```アルゴリズムは最初の置換後に停止せず、複数の置換を正しく処理します。
