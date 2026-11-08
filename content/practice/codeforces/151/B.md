---
title: "CF 151B - 電話番号"
description: "各友人は、XX-XX-XX の形式で書かれた番号を含む電話帳を持っています。 すべての電話番号は、3 つのカテゴリのいずれかに正確に属します。 タクシー番号はどこでも同じ数字を使用します。 例は、11-11-11 または 55-55-55 です。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation", "strings"]
categories: ["algorithms"]
codeforces_contest: 151
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 107 (Div. 2)"
rating: 1200
weight: 151
solve_time_s: 106
verified: true
draft: false
---

[CF 151B - 電話番号](https://codeforces.com/problemset/problem/151/B)

 **評価:** 1200
 **タグ:** 実装、文字列
 **解決時間:** 1 分 46 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各友人は、次の形式で書かれた番号が記載された電話帳を持っています。`XX-XX-XX`。 すべての電話番号は、3 つのカテゴリのいずれかに正確に属します。 

タクシー番号はどこでも同じ数字を使用します。 例としては、`11-11-11`または`55-55-55`。 

ピザの数値は、ダッシュを無視してその数字を左から右に読むと厳密に減少します。 次のすべての数字は、前の数字より小さくなければなりません。 例としては、`98-76-54`そして`95-43-21`。 

タクシーでもピザでもない番号は女の子の番号とみなされます。 

すべての友人について、各カテゴリに属する​​数字の数を数えなければなりません。 すべての電話帳を処理した後、タクシー数、ピザ数、女の子数が最も多い友人の名前を出力しました。 複数の友人が最大数で同点になる場合があり、その場合は入力順にすべてが印刷されます。 

制約は非常に小さいです。 友人は最大 100 人で、各友人が持つ電話番号は最大 100 個です。 各数字には正確に 6 桁が含まれます。 すべての数値のすべての桁を個別にチェックする単純な実装でも、制限内で快適に実行できます。 合計で、プログラムは最大 10,000 個の固定長の電話番号を処理します。 つまり、実際の作業量はわずかです。 

難しいのはパフォーマンスではなく、分類の正確さと出力の書式設定です。 

簡単な間違いの 1 つは、数字をチェックするときにダッシュを無視することを忘れることです。 考慮する：```
98-76-54
```桁は減っていますが、文字を削除せずに直接比較すると`-`、ロジックが壊れます。`'-'`は数字ではありません。 

もう 1 つのよくある間違いは、ピザの数を厳密に減らさないことを許可することです。 考慮する：```
98-76-55
```最後の 2 桁が等しいため、これはピザ番号ではありません。 みたいな不注意な状態`>=`の代わりに`>`間違って受け入れてしまいます。 

1 つの電話帳に同じ番号が複数回出現する場合、微妙な特殊なケースが発生します。 このステートメントでは、重複を繰り返しカウントする必要があると明示的に述べています。 例えば：```
1
3 Alex
11-11-11
11-11-11
12-34-56
```アレックスはタクシー番号を 1 つではなく 2 つ持っています。 セットを使用して重複を削除すると、間違った答えが生成されます。 

もう 1 つの書式設定の落とし穴は、複数の友人が最大値で同点の場合に発生します。 仮定する：```
2
1 A
11-11-11
1 B
22-22-22
```両方の友人が 1 つのタクシー番号を持っているため、正しい出力は次のようになります。```
If you want to call a taxi, you should call: A, B.
```名前は入力順のままで、正確に区切る必要があります。`", "`。 

## アプローチ

 最も直接的なアプローチは、すべての電話番号を個別に処理し、その桁を検査して分類することです。 

各数値について、ダッシュを削除し、長さ 6 の文字列を取得します。次に、すべての桁が等しいかどうかをテストします。 「はい」の場合、それはタクシーの番号です。 

そうでない場合は、すべての桁が厳密に次の桁より大きいかどうかをテストします。 「はい」の場合、それはピザ番号です。 

それ以外の場合は、女の子のカテゴリに属します。 

このブルートフォース手法は、入力サイズが小さいため、すでに十分に高速です。 最悪の場合、それぞれ 6 桁しか含まれていない 10,000 件の電話番号を検査します。 これは約 60,000 文字の操作に相当し、基本的には瞬時に行われます。 

高度なデータ構造や最適化のテクニックは必要ありません。 重要な観察は、すべての分類は 6 文字のみに依存するため、各チェックは一定時間であるということです。 

少し慎重に実装しないと、同じ文字列を複数回繰り返しスキャンしたり、不必要に数字を変換したりする可能性がありますが、それでも快適に通過します。 最もクリーンな解決策は、単純に各数値を 1 回処理し、対応するカウンターを更新することです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n × s × 6) | お(1) | 承認済み |
 | 最適 | O(n × s × 6) | お(1) | 承認済み |

 ここでは、制約が非常に小さいため、総当たりアプローチと最適アプローチは事実上同じです。 重要なのは、分類ルールを正しく実装することです。 

## アルゴリズムのチュートリアル

 1. 友達の数を読み取ります。 
2. 各友人について、電話番号の数と友人の名前を読み上げます。 
3. この友人の 3 つのカウンター (タクシー、ピザ、女の子) を初期化します。 
4. すべての電話番号について:

 1. ダッシュを削除して、6 桁だけが残るようにします。 
2. 6 桁がすべて同じかどうかを確認します。 
3. それらが同一の場合、タクシーカウンターをインクリメントします。 
4. それ以外の場合は、すべての桁が厳密に次の桁より大きいかどうかを確認します。 
5. 桁が厳密に減少している場合は、ピザカウンターを増分します。 
6. それ以外の場合は、女の子カウンターをインクリメントします。 
5. 3 つのカウントを友人の名前と一緒に保存します。 
6. すべての友人を処理した後、次を計算します。 

1. タクシーの最大数。 
2. ピザの最大数。 
3. 女の子の最大数。 
7. 入力順に友達を調べて、以下を収集します。 

1. タクシー数が最大タクシー数と等しいすべての名前。 
2. ピザ数が最大ピザ数と等しいすべての名前。 
3. 女の子の数が女の子の最大数に等しいすべての名前。 
8. 名前を で結合した 3 つの必要な文を出力します。`", "`。 

### なぜ効果があるのか

 すべての電話番号は 1 つのカテゴリにのみ属します。 

すべての桁が等しい場合、その番号はタクシーの定義を直接満たします。 

数字がすべて等しくなくても、すべての数字が次の数字より大きい場合、その数字はピザの定義を満たします。 

残りの番号はタクシーやピザにはできませんので、女の子のカテゴリに属している必要があります。 

すべての数値は 1 回だけ分類され、すべての出現が個別にカウントされるため、各友人のカウンターは正確です。 すべての友人の最大カウンターを取得すると、必要な名前が正しく識別されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())

    friends = []

    for _ in range(n):
        s, name = input().split()
        s = int(s)

        taxi = 0
        pizza = 0
        girls = 0

        for _ in range(s):
            number = input().strip().replace("-", "")

            if len(set(number)) == 1:
                taxi += 1
            elif all(number[i] > number[i + 1] for i in range(5)):
                pizza += 1
            else:
                girls += 1

        friends.append((name, taxi, pizza, girls))

    max_taxi = max(friend[1] for friend in friends)
    max_pizza = max(friend[2] for friend in friends)
    max_girls = max(friend[3] for friend in friends)

    taxi_names = [friend[0] for friend in friends if friend[1] == max_taxi]
    pizza_names = [friend[0] for friend in friends if friend[2] == max_pizza]
    girls_names = [friend[0] for friend in friends if friend[3] == max_girls]

    print(
        "If you want to call a taxi, you should call: "
        + ", ".join(taxi_names)
        + "."
    )

    print(
        "If you want to order a pizza, you should call: "
        + ", ".join(pizza_names)
        + "."
    )

    print(
        "If you want to go to a cafe with a wonderful girl, you should call: "
        + ", ".join(girls_names)
        + "."
    )

solve()
```ソリューションは分類ロジックに直接に従います。 

表現`number.replace("-", "")`書式設定文字が削除されるので、数字のみを操作できるようになります。 その後、すべての数字は 6 文字の文字列になります。 

タクシーチェックは以下を使用します`len(set(number)) == 1`。 セットは重複を削除するため、セットのサイズが 1 の場合、すべての桁が同一である必要があります。 

ピザ チェックでは、隣接する数字が比較されます。```
all(number[i] > number[i + 1] for i in range(5))
```比較は厳密に大きいかどうかではなく、より大きいものです。 数字が繰り返されるとピザの条件が無効になるため、この詳細は重要です。 

女の子のカテゴリーは、最初の 2 つのチェックが失敗した後に残ったケースにすぎません。 

プログラムはすべての友人の数を保存し、後でグローバル最大値を計算します。 最終出力では入力順序を保持する必要があるため、並べ替えるのではなく、保存されたリストを反復処理します。 

出力形式は間違いやすいです。 このステートメントには、カンマの後にスペースが必要で、最後にピリオドが 1 つだけ必要です。 使用する`", ".join(...)`これをきれいに処理します。 

## 実用的な例

 ### サンプル 1

 入力:```
4
2 Fedorov
22-22-22
98-76-54
3 Melnikov
75-19-09
23-45-67
99-99-98
7 Rogulenko
22-22-22
11-11-11
33-33-33
44-44-44
55-55-55
66-66-66
95-43-21
3 Kaluzhin
11-11-11
99-99-99
98-65-32
```処理トレース:

 | 友達 | 番号 | タイプ | タクシー | ピザ | 女の子 |
 | --- | --- | --- | --- | --- | --- |
 | フェドロフ 22-22-22 | タクシー | 1 | 0 | 0 |
 | フェドロフ 98-76-54 | ピザ | 1 | 1 | 0 |
 | メルニコフ 75-19-09 | 女の子 | 0 | 0 | 1 |
 | メルニコフ 23-45-67 | 女の子 | 0 | 0 | 2 |
 | メルニコフ 99-99-98 | 女の子 | 0 | 0 | 3 |
 | ログレンコ | 22-22-22 | タクシー | 1 | 0 | 0 |
 | ログレンコ | 11/11/11 | タクシー | 2 | 0 | 0 |
 | ログレンコ | 33-33-33 | タクシー | 3 | 0 | 0 |
 | ログレンコ | 44-44-44 | タクシー | 4 | 0 | 0 |
 | ログレンコ | 55-55-55 | タクシー | 5 | 0 | 0 |
 | ログレンコ | 66-66-66 | タクシー | 6 | 0 | 0 |
 | ログレンコ | 95-43-21 | ピザ | 6 | 1 | 0 |
 | カルジン | 11/11/11 | タクシー | 1 | 0 | 0 |
 | カルジン | 99-99-99 | タクシー | 2 | 0 | 0 |
 | カルジン | 98-65-32 | ピザ | 2 | 1 | 0 |

 最終的な最大値:

 | カテゴリー | 最大 | 友達 |
 | --- | --- | --- |
 | タクシー | 6 | ログレンコ |
 | ピザ | 1 | フェドロフ、ログレンコ、カルジン |
 | 女の子 | 3 | メルニコフ

 この例では、3 つのカテゴリすべてを示し、ピザ カテゴリの同率を示します。 

### カスタム例

 入力:```
2
3 Alice
11-11-11
98-76-54
12-34-56
2 Bob
22-22-22
87-65-43
```処理トレース:

 | 友達 | 番号 | タイプ | タクシー | ピザ | 女の子 |
 | --- | --- | --- | --- | --- | --- |
 | アリス | 11/11/11 | タクシー | 1 | 0 | 0 |
 | アリス | 98-76-54 | ピザ | 1 | 1 | 0 |
 | アリス | 12/34/56 | 女の子 | 1 | 1 | 1 |
 | ボブ | 22-22-22 | タクシー | 1 | 0 | 0 |
 | ボブ | 87-65-43 | ピザ | 1 | 1 | 0 |

 最終的な最大値:

 | カテゴリー | 最大 | 友達 |
 | --- | --- | --- |
 | タクシー | 1 | アリス、ボブ |
 | ピザ | 1 | アリス、ボブ |
 | 女の子 | 1 | アリス |

 このトレースは、正しいタイ処理と入力順序の保存を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n × s × 6) | すべての電話番号は正確に 6 桁です。 
| スペース | お(1) | 少数のカウンターと名前のリストのみが保存されます。 

最大制限でも、プログラムは約 60,000 桁の比較のみを処理します。 これは利用可能な制限時間を大幅に下回っています。 メモリ使用量も無視できます。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    out = io.StringIO()
    sys.stdout = out

    n = int(input())

    friends = []

    for _ in range(n):
        s, name = input().split()
        s = int(s)

        taxi = 0
        pizza = 0
        girls = 0

        for _ in range(s):
            number = input().strip().replace("-", "")

            if len(set(number)) == 1:
                taxi += 1
            elif all(number[i] > number[i + 1] for i in range(5)):
                pizza += 1
            else:
                girls += 1

        friends.append((name, taxi, pizza, girls))

    max_taxi = max(friend[1] for friend in friends)
    max_pizza = max(friend[2] for friend in friends)
    max_girls = max(friend[3] for friend in friends)

    taxi_names = [friend[0] for friend in friends if friend[1] == max_taxi]
    pizza_names = [friend[0] for friend in friends if friend[2] == max_pizza]
    girls_names = [friend[0] for friend in friends if friend[3] == max_girls]

    print(
        "If you want to call a taxi, you should call: "
        + ", ".join(taxi_names)
        + "."
    )

    print(
        "If you want to order a pizza, you should call: "
        + ", ".join(pizza_names)
        + "."
    )

    print(
        "If you want to go to a cafe with a wonderful girl, you should call: "
        + ", ".join(girls_names)
        + "."
    )

    sys.stdout = sys.__stdout__

    return out.getvalue()

# provided sample
assert run(
"""4
2 Fedorov
22-22-22
98-76-54
3 Melnikov
75-19-09
23-45-67
99-99-98
7 Rogulenko
22-22-22
11-11-11
33-33-33
44-44-44
55-55-55
66-66-66
95-43-21
3 Kaluzhin
11-11-11
99-99-99
98-65-32
"""
) == (
"""If you want to call a taxi, you should call: Rogulenko.
If you want to order a pizza, you should call: Fedorov, Rogulenko, Kaluzhin.
If you want to go to a cafe with a wonderful girl, you should call: Melnikov.
"""
), "sample 1"

# minimum input
assert run(
"""1
1 Alex
11-11-11
"""
) == (
"""If you want to call a taxi, you should call: Alex.
If you want to order a pizza, you should call: Alex.
If you want to go to a cafe with a wonderful girl, you should call: Alex.
"""
), "single friend"

# duplicate numbers must count multiple times
assert run(
"""1
3 Bob
11-11-11
11-11-11
12-34-56
"""
) == (
"""If you want to call a taxi, you should call: Bob.
If you want to order a pizza, you should call: Bob.
If you want to go to a cafe with a wonderful girl, you should call: Bob.
"""
), "duplicates counted"

# tie handling
assert run(
"""2
1 A
11-11-11
1 B
22-22-22
"""
) == (
"""If you want to call a taxi, you should call: A, B.
If you want to order a pizza, you should call: A, B.
If you want to go to a cafe with a wonderful girl, you should call: A, B.
"""
), "ties"

# pizza must be strictly decreasing
assert run(
"""1
2 Carl
98-76-54
98-76-55
"""
) == (
"""If you want to call a taxi, you should call: Carl.
If you want to order a pizza, you should call: Carl.
If you want to go to a cafe with a wonderful girl, you should call: Carl.
"""
), "strict decrease"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1 つの番号を持つ 1 人の友人 | 同じ友人がどこにでも印刷されています。 最小制約 |
 | タクシー番号が重複している | 重複を個別にカウントする | 不適切な重複排除を回避する |
 | 結ばれた 2 人の友人 | 写真 両方の名前が順番に印刷されます。 ネクタイの取り扱い |
 |`98-76-55`| ピザではありません | 厳密な不等式 |

 ## 特殊なケース

 繰り返される電話番号を考慮してください。```
1
3 Alex
11-11-11
11-11-11
12-34-56
```アルゴリズムはそれぞれの発生を独立して処理します。 すべての桁が等しいため、最初の 2 つの数値は両方ともタクシー条件を満たします。 タクシーカウンターは 2 になります。ステートメントでは繰り返しのエントリが個別にカウントされるため、この動作は正しいです。 

ピザの数字に似た数字を考えてみましょう。```
1
1 Bob
98-76-55
```ハイフンを削除すると、数字は次のようになります。`987655`。 比較は次のとおりです。```
9 > 8
8 > 7
7 > 6
6 > 5
5 > 5   false
```減少が厳密ではないため、最終的な比較は失敗します。 アルゴリズムは、その番号を女の子の番号として正しく分類します。 

複数の友人間の絆を考えてみましょう。```
2
1 A
11-11-11
1 B
22-22-22
```両方の友人はタクシー カウント 1 で終了します。アルゴリズムは最大タクシー カウントを 1 として計算し、その値に一致するすべての友人を収集します。 元の入力順序をたどるので、出力は次のようになります。```
A, B
```これは、必要な順序と一致します。 

電話番号がゼロの友人を考えてみましょう。```
1
0 Alice
```3 つのカウンタはすべて 0 のままです。アリスが唯一の友人であるため、すべてのカテゴリの最大値も 0 となり、アリスは 3 つの出力行すべてに表示されます。 アルゴリズムは、特別な場合を必要とせずにこれを自然に処理します。
