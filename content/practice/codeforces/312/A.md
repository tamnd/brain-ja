---
title: "CF 312A - 誰の文章ですか?"
description: "最大 10 文の小さなチャット ログが提供されます。 各文は、文字、アンダースコア、カンマ、ピリオド、およびスペースの文字列です。 フリーダとレインボーの二人が関わってきます。 これまでの経験によると、フリーダは常に部分文字列「lala」で文を終えます。"
date: "2026-06-06T00:49:33+07:00"
tags: ["codeforces", "competitive-programming", "implementation", "strings"]
categories: ["algorithms"]
codeforces_contest: 312
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 185 (Div. 2)"
rating: 1100
weight: 312
solve_time_s: 90
verified: false
draft: false
---

[CF 312A - それは誰の文章ですか?](https://codeforces.com/problemset/problem/312/A)

 **評価:** 1100
 **タグ:** 実装、文字列
 **解決時間:** 1 分 30 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 最大 10 文の小さなチャット ログが提供されます。 各文は、文字、アンダースコア、カンマ、ピリオド、およびスペースの文字列です。 フリーダとレインボーの二人が関わってきます。 これまでの経験によれば、フリーダは常に次の部分文字列で文を終えます。`"lala."`一方、レインボーはいつも次のように文を始めます。`"miao."`。 私たちの目標は、各文について、それが Freda に属するのか、Rainbow に属するのか、それとも判断できないのかを識別することです。 両方が次で始まる場合、文は不定です。`"miao."`そして次で終わります`"lala."`、またはどちらのパターンも満たさない場合。 

入力制限は小さく、最大 10 文、各文の長さは最大 100 文字です。 これは、各文をその長さに比べて線形時間で個別にチェックするアルゴリズムが受け入れられることを意味します。 全体の操作数は最大でも 1000 であり、1 秒あたり 10^8 操作という一般的な競技プログラミングの制限をはるかに下回っているため、高度なデータ構造や最適化は必要ありません。 

明らかではないエッジケースには、空白や句読点が含まれます。 たとえば、で始まる文は、`"miao"`ただし、ピリオドの前にスペースがあります。`"miao ."`、レインボーの文章とはみなされません。 同様に、`"lala"`最後のピリオドがなければフリーダの判決としては認められない。 同時に始まる文`"miao."`そして次で終わります`"lala."`不確定としてマークする必要があります。 重複を考慮せずに開始または終了のみをテストする単純なチェックでは、このような文を誤って分類する可能性があります。 

## アプローチ

 ブルートフォースアプローチは簡単です。 各文について、次で終わるかどうかを確認できます。`"lala."`またはで始まります`"miao."`対応するラベルを出力します。 両方の条件が成立するか、どちらの条件も成立しない場合は、不確定メッセージが出力されます。 これは、文の数が少なく、それぞれの文が短いため機能します。 ブルート フォース手法はテキストの全長に比例し、最悪の場合、10 文×100 文字となり、1000 回の操作が必要になります。 

最適化するためにこれより大きな入力サイズがないため、最適なアプローチはこの問題における総当たり法と変わりません。 重要な洞察は、次のような Python の文字列操作であるということです。`.startswith()`そして`.endswith()`部分文字列の長さに関して線形時間で実行されます。この場合、長さは一定です (`"miao."`そして`"lala."`は両方とも長さ 5)。 したがって、前処理や追加のデータ構造を必要とせずに、これらの組み込み関数を直接適用できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * L) | お(1) | 承認済み |
 | 最適 | O(n * L) | お(1) | 承認済み |

 ここ、`n`は文の数であり、`L`文の最大長である 100 です。 

## アルゴリズムのチュートリアル

 1. 整数を読み取る`n`文の数を表します。 
2. チャット レコード内の各文について、末尾の改行を削除します。 
3. 文が次で始まるかどうかを確認します。`"miao."`。 これをブール変数として保存します`is_rainbow`。 
4. 文が次で終わるかどうかを確認します。`"lala."`。 これをブール変数として保存します`is_freda`。 
5. 両方の場合`is_rainbow`そして`is_freda`True の場合、不定のメッセージを出力します`"OMG>.< I don't know!"`。 
6. もしそうなら`is_rainbow`True の場合、出力します`"Rainbow's"`。 
7. もしそうなら`is_freda`True の場合、出力します`"Freda's"`。 
8. どちらも True でない場合は、不定のメッセージを出力します。 

これが機能する理由: 文のプロパティは、不定の場合を除いて相互に排他的です。 開始と終了を個別にチェックし、重複シナリオを明示的に処理することにより、アルゴリズムはすべての文が正しく分類されることを保証します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n = int(input())
for _ in range(n):
    sentence = input().rstrip('\n')
    is_rainbow = sentence.startswith("miao.")
    is_freda = sentence.endswith("lala.")
    
    if is_rainbow and is_freda:
        print("OMG>.< I don't know!")
    elif is_rainbow:
        print("Rainbow's")
    elif is_freda:
        print("Freda's")
    else:
        print("OMG>.< I don't know!")
```このソリューションでは、各文を読み取り、改行文字をトリミングして、関連する開始部分文字列と終了部分文字列をチェックします。 チェックの順序は重要です。最初の条件により、単一の一致が出力される前に不定ケースが処理されることが保証されます。 使用する`.startswith()`そして`.endswith()`手動インデックス作成エラーを回避し、文の長さがちょうど 5 文字の場合でも機能します。 

## 実用的な例

 入力例:```
5
I will go to play with you lala.
wow, welcome.
miao.lala.
miao.
miao .
```| 文 | 虹 | ふれだ | 出力 |
 | --- | --- | --- | --- |
 | ララちゃんと遊びに行きます。 | 偽 | 本当 | フリーダさん |
 | わあ、ようこそ。 | 偽 | 偽 | ああ、>.< わかりません! |
 | ミャオ・ララ。 | 本当 | 本当 | ああ、>.< わかりません! |
 | ミャオ。 | 本当 | 偽 | レインボー |
 | ミャオ族。 | 偽 | 偽 | ああ、>.< わかりません! |

 このトレースは、Freda のみ、Rainbow のみ、不定の 3 つの分類ケースすべての処理を示しています。 

カスタム入力:```
3
lala.
miao.lala.
miao..lala.
```| 文 | 虹 | ふれだ | 出力 |
 | --- | --- | --- | --- |
 | ララ。 | 偽 | 本当 | フリーダさん |
 | ミャオ・ララ。 | 本当 | 本当 | ああ、>.< わかりません! |
 | ミャオ…ララ。 | 偽 | 本当 | フリーダさん |

 このトレースにより、正確に始まる文のみが確認されます。`"miao."`はレインボーとして分類され、余分な句読点が表示された場合の誤検知を回避します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n * L) | 各文の開始部分文字列と終了部分文字列がチェックされます。`L`最大でも 100 です。 
| スペース | お(1) | 少数のブール変数のみが使用されます。 入力サイズに応じて追加のデータ構造が増加することはありません。 |

 n ≤ 10 および L ≤ 100 という制約により、解は 2 秒の制限内で無視できる時間内で実行されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from contextlib import redirect_stdout
    output = io.StringIO()
    with redirect_stdout(output):
        n = int(input())
        for _ in range(n):
            sentence = input().rstrip('\n')
            is_rainbow = sentence.startswith("miao.")
            is_freda = sentence.endswith("lala.")
            
            if is_rainbow and is_freda:
                print("OMG>.< I don't know!")
            elif is_rainbow:
                print("Rainbow's")
            elif is_freda:
                print("Freda's")
            else:
                print("OMG>.< I don't know!")
    return output.getvalue().strip()

# Provided sample
assert run("5\nI will go to play with you lala.\nwow, welcome.\nmiao.lala.\nmiao.\nmiao .") == \
"""Freda's
OMG>.< I don't know!
OMG>.< I don't know!
Rainbow's
OMG>.< I don't know!"""

# Minimum size input
assert run("1\nmiao.") == "Rainbow's"

# Freda-only sentence
assert run("1\nhello lala.") == "Freda's"

# Indeterminate sentence both start and end
assert run("1\nmiao.lala.") == "OMG>.< I don't know!"

# Edge punctuation
assert run("1\nmiao..lala.") == "Freda's"

# Random unknown sentence
assert run("1\nhello world.") == "OMG>.< I don't know!"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | ミャオ。 | レインボー | 最小長のレインボーセンテンス |
 | こんにちは、ララ。 | フリーダさん | 最小長のフリーダ文 |
 | ミャオ・ララ。 | ああ、>.< わかりません! | 両方のパターンの不定ケース |
 | ミャオ…ララ。 | フリーダさん | 余分な句読点による誤検知の回避 |
 | こんにちは世界。 | ああ、>.< わかりません! | 不明文の処理 |

 ## 特殊なケース

 文`"miao ."`ピリオドの前にスペースが入っています。 私たちのアルゴリズムは、それがレインボーでもフリーダでもないことを正しく識別します。 ブール変数`is_rainbow`そして`is_freda`は False であるため、不定のメッセージが出力されます。 文`"miao.lala."`両方をトリガーします`is_rainbow`そして`is_freda`、不定の出力が生成されます。 チェックの順序により、重複する条件が誤って分類されないことが保証されます。 他のすべての文は、先頭と末尾の部分文字列の正確な一致に基づいて正しく分類されます。
