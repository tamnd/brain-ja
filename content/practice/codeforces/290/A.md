---
title: "CF 290A - 謎の文字列"
description: "課題全体はエイプリルフールコンテストのジョーク問題です。 入力は 1 ～ 40 の単一の整数です。何かを計算する代わりに、その位置に関連付けられた特定の文字列を出力する必要があります。"
date: "2026-06-05T16:49:16+07:00"
tags: ["codeforces", "competitive-programming", "*special", "implementation"]
categories: ["algorithms"]
codeforces_contest: 290
codeforces_index: "A"
codeforces_contest_name: "April Fools Day Contest 2013"
rating: 1400
weight: 290
solve_time_s: 83
verified: true
draft: false
---

[CF 290A - 謎の文字列](https://codeforces.com/problemset/problem/290/A)

 **評価:** 1400
 **タグ:** *特殊、実装
 **解決時間:** 1 分 23 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 課題全体はエイプリルフールコンテストのジョーク問題です。 

入力は単一の整数です`a`何かを計算する代わりに、その位置に関連付けられた特定の文字列を出力する必要があります。 サンプル値から次のことがわかります。`2`にマップします`"Adams"`、`8`にマップします`"Van Buren"`、 そして`29`にマップします`"Harding"`。 隠された観察は、その数字が米国の最初の 40 人の大統領に年代順に対応しているということです。 必要な出力は単にユーザーの姓です。`a`- 番目の大統領。 

制約は小さいです。 以来`a`40 を超えることはありません。40 個の文字列を保存し、1 回の検索を実行するソリューションは実質的に瞬時に実行されます。 

間違いの唯一の原因はインデックス作成です。 

たとえば、入力の場合```
1
```正しい出力は```
Washington
```リストには 1 から始まる番号が付けられているためです。入力を 0 から始まる配列インデックスとして直接使用する不注意な実装では、代わりに 2 番目のエントリが返されます。 

もう 1 つのよくある間違いは入力時に発生します```
40
```正しい出力は次のとおりです```
Reagan
```配列にはちょうど 40 個の要素が含まれているため、最後の有効な 0 から始まるインデックスは次のとおりです。`39`。 インデックスへのアクセス`40`限界外だろう。 

## アプローチ

 ブルートフォースの観点では、考えられるすべての値を手動でチェックすることになります。`a`の長いチェーンを使用して、対応する回答を出力します。`if`発言。 可能性は 40 個しかないため、これでも十分に高速です。 

よりクリーンなアプローチでは、40 の姓すべてを配列に保存します。 その場合、問題はテーブルの直接検索になります。 入力値は 1 から始まりますが、配列は通常 0 から始まるため、答えは の位置に格納されます。`a - 1`。 

ブルート フォース バージョンは、入力範囲が固定されているため、すでに一定時間で実行されます。 配列ソリューションは、短く、検証が容易で、エラーが発生しにくいため、推奨されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース (`if`チェーン) | お(1) | お(1) | 承認済み |
 | 最適 (配列ルックアップ) | お(1) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 最初の 40 人の米国大統領の姓を年代順に含む配列を作成します。 
2. 整数を読み取る`a`。 
3. 計算により、1 から始まる位置を 0 から始まる配列インデックスに変換します。`a - 1`。 
4. そのインデックスに格納されている文字列を出力します。 

なぜ効果があるのか:

 配列は次の位置に構築されます。`i`社長番号に対応する姓が含まれています`i + 1`。 入力値`a`を求めます`a`そのシーケンスの - 番目のエントリ。 インデックスへのアクセス`a - 1`はその姓を正確に取得するため、生成される出力はすべての有効な入力に必要なマッピングと一致します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

presidents = [
    "Washington", "Adams", "Jefferson", "Madison", "Monroe",
    "Adams", "Jackson", "Van Buren", "Harrison", "Tyler",
    "Polk", "Taylor", "Fillmore", "Pierce", "Buchanan",
    "Lincoln", "Johnson", "Grant", "Hayes", "Garfield",
    "Arthur", "Cleveland", "Harrison", "Cleveland",
    "McKinley", "Roosevelt", "Taft", "Wilson", "Harding",
    "Coolidge", "Hoover", "Roosevelt", "Truman",
    "Eisenhower", "Kennedy", "Johnson", "Nixon",
    "Ford", "Carter", "Reagan"
]

a = int(input())
print(presidents[a - 1])
```配列には、問題に必要なマッピング全体が含まれています。 入力を読み取ると、1 ～ 40 の値が返されます。Python リストのインデックスは 0 から始まるため、正しい要素は次のとおりです。`presidents[a - 1]`。 

唯一の微妙な点はインデックスの変換です。 を忘れて`- 1`すべての答えを 1 桁ずらして、誤った答えまたは範囲外のアクセスを引き起こします。`a = 40`。 

## 実用的な例

 ### 例 1

 入力:```
2
```| ステップ | | 使用されるインデックス | 出力 |
 | --- | --- | --- | --- |
 | 入力を読み取る | 2 | - | - |
 | インデックスを計算する | 2 | 1 | - |
 | 検索 | 2 | 1 | アダムス |

 出力：```
Adams
```このトレースは、1 ベースから 0 ベースへの変換を示しています。 2 番目の大統領は配列インデックス 1 に格納されます。 

### 例 2

 入力:```
29
```| ステップ | | 使用されるインデックス | 出力 |
 | --- | --- | --- | --- |
 | 入力を読み取る | 29 | - | - |
 | インデックスを計算する | 29 | 28 | - |
 | 検索 | 29 | 28 | ハーディング |

 出力：```
Harding
```この例では、このソリューションがリストの奥深くにある位置でも同様に機能することを確認しています。 反復や検索は必要ありません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | お(1) | 1 つの配列アクセス |
 | スペース | お(1) | 40 個の文字列の固定サイズの配列 |

 実行時間は入力値に依存しません。 1 回の検索と印刷の操作で、簡単に制限内に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

PRESIDENTS = [
    "Washington", "Adams", "Jefferson", "Madison", "Monroe",
    "Adams", "Jackson", "Van Buren", "Harrison", "Tyler",
    "Polk", "Taylor", "Fillmore", "Pierce", "Buchanan",
    "Lincoln", "Johnson", "Grant", "Hayes", "Garfield",
    "Arthur", "Cleveland", "Harrison", "Cleveland",
    "McKinley", "Roosevelt", "Taft", "Wilson", "Harding",
    "Coolidge", "Hoover", "Roosevelt", "Truman",
    "Eisenhower", "Kennedy", "Johnson", "Nixon",
    "Ford", "Carter", "Reagan"
]

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    a = int(sys.stdin.readline())
    return PRESIDENTS[a - 1] + "\n"

# provided sample
assert run("2\n") == "Adams\n", "sample 1"

# custom cases
assert run("1\n") == "Washington\n", "first element"
assert run("40\n") == "Reagan\n", "last element"
assert run("8\n") == "Van Buren\n", "middle value"
assert run("29\n") == "Harding\n", "another known mapping"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1`|`Washington`| 有効なインデックスの最小値 |
 |`40`|`Reagan`| 有効なインデックスの最大値 |
 |`8`|`Van Buren`| 正しい中間検索 |
 |`29`|`Harding`| もう一つの重要な立場 |

 ## 特殊なケース

 最小の入力を考えてみましょう。```
1
```アルゴリズムは計算します`1 - 1 = 0`そして最初の配列要素にアクセスします。 出力は次のとおりです。```
Washington
```これにより、最初の位置が正しく処理され、負のインデックス付けが発生しないことが確認されます。 

最大の入力を考えてみましょう。```
40
```アルゴリズムは計算します`40 - 1 = 39`そして、最後の有効な配列位置にアクセスします。 出力は次のとおりです。```
Reagan
```これにより、実装が配列の範囲内に収まっていることが検証されます。 

先頭付近の値を考えてみましょう。```
2
```計算されたインデックスは次のとおりです。`1`、生産:```
Adams
```このケースでは、古典的な off-by-one エラーが発生します。 減算が省略された場合、プログラムは誤って返します。`"Jefferson"`その代わり。
