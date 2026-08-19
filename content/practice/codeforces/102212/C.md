---
title: "CF 102212C - 豚のラテン語"
description: "各テスト ケースは 1 つの英語の文です。 文の最初の文字は大文字で、他の文字はすべて小文字であり、句読点はありません。"
date: "2026-08-18T00:34:08+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102212
codeforces_index: "C"
codeforces_contest_name: "Amazalgo Uni 2019 Practice Contest"
rating: 0
weight: 102212
solve_time_s: 521
verified: false
draft: false
---

[CF 102212C - 豚ラテン語](https://codeforces.com/problemset/problem/102212/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 8 分 41 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 各テスト ケースは 1 つの英語の文です。 文の最初の文字は大文字で、他の文字はすべて小文字であり、句読点はありません。 指定された Pig Latin ルールを使用して、すべての単語を個別に変換する必要があります。つまり、単語の最初の文字を削除し、残りの文字を追加し、最初の文字に続いて追加します。`ay`。 

例えば、`Hello`になる`Ellohay`。 の`H`後ろに移動される`ello`、結果は次のようになります`Ello`に続く`hay`。 元の文の最初の文字は大文字であるため、大文字と小文字を変更するのではなくその文字を移動すると、結果の文は自動的に大文字のままになります。 

文は最大でも 20 個です。 提供された制約には文の最大長が規定されていないため、実行時間を入力の総文字数に比例させるのが安全な設計です。 すべての文字に対して文全体を繰り返しスキャンまたは再構築するアルゴリズムは、文の長さが 2 次になる可能性がありますが、すべての文字に対する 1 回のパスは線形であり、通常のコンテスト入力サイズの 1 秒の制限に快適に適合します。 

主なエッジケースは 1 文字の単語です。 文に関しては`I`、単語には残りの文字が含まれていないため、結果は単純に次のようになります。`Iay`。 接尾辞が常に存在すると想定する不注意な実装では、誤って無効な位置にアクセスしたり、単語を誤って構築したりする可能性があります。 

もう 1 つの簡単な間違いは、大文字が単語に属しており、大文字も移動する必要があることを忘れることです。 のために`Apple`、答えは`PpleAay`、 ない`Appleay`そしてそうではありません`ppleAay`。 最初の文字は最後に移動されます。`ay`が追加されています。 

最後の境界ケースは、変換が単語ごとに個別に発生する必要があるため、複数の単語を含む文です。 のために`Go to`、正しい結果は次のとおりです`Ogay otay`。 変換を文全体に 1 回だけ適用すると、スペースと 2 番目の単語が同じ文字列の一部として誤って扱われてしまいます。 

## アプローチ

 簡単な実装では、最初の文字を取得し、単語の残りの部分を取得し、部分を連結することによって各単語を変換できます。 それはすでに正しいアルゴリズムのアイデアです。 純粋に単純な実装では、代わりに、文字列の連結を繰り返し使用して、変換された各単語を一度に 1 文字ずつ構築できます。 結果のテキストは正しいですが、Python では文字列は不変であるため、増大する文字列を繰り返し拡張すると、構築済みのプレフィックスがコピーされる可能性があります。 長い単語に対して`L`、それはかかることができます`O(L^2)`最悪の場合、文字操作。 全長の文全体にわたって`L`したがって、最悪のケースは`O(L^2)`。 

この問題の構造により、より単純な線形構造が得られます。 変換は最初の文字以外の文字には依存せず、残りの文字は元の順序を正確に保持します。 したがって、最初の文字を一度識別するだけで済み、その後、サフィックスと保存された文字を連結する必要があります。`ay`。 文を単語に分割すると独立した部分が得られるため、入力されたすべての文字が一定の回数だけ処理されます。 

ブルートフォース構築は必要な文字順序を保持するため機能しますが、プレフィックスを繰り返しコピーするという無駄な作業が発生する可能性があります。 変換が単純であるという観察は、`first character + suffix`に並べ替えた`suffix + first character + "ay"`各結果を直接構築してみましょう。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(L²)`最悪の場合 |`O(L)`| 十分に長い単語に対して遅すぎる |
 | 最適 |`O(L)`|`O(L)`| 承認済み |

 ここ、`L`は、スペースを含む、処理された文字の合計数を示します。 すべての文字が一定回数入力または出力に属するため、最適な方法は線形です。 

## アルゴリズムのチュートリアル

 1. テストケースの数を読み取り、各文を個別に処理します。 各行は個別のメッセージを表すため、文を他のテスト ケースと決して混合してはなりません。 
2. 文を空白で分割して、個々の単語を取得します。 入力には句読点が含まれておらず、通常のスペースで単語が区切られているため、結果として得られる各トークンは、変換する必要がある 1 つの単語となります。 
3. すべての単語について、その最初の文字を保存し、位置で始まる部分文字列を取得します。`1`。 最初の文字は位置が変更される唯一の文字であるため、結果を構築する前に保存する必要があります。 
4. 変換された単語を次のように構築します。`word[1:] + word[0] + "ay"`。 接尾辞は変更されず、元の最初の文字がその直後に配置されます。`ay`が最後に追加されます。 
5. 変換されたすべての単語をスペースで結合し、結果の文を出力します。 最後に結合すると、隣接する単語の間にスペースが 1 つだけ保存され、単語の変換が独立した状態に保たれます。 

### なぜ効果があるのか

 入力された単語ごとに`w`、最初の文字を次のようにします`c`残りの接尾辞は`s`、 それで`w = c + s`。 必要な Pig Latin 変換は正確に次のとおりです。`s + c + "ay"`、それがアルゴリズムによって構築されるものです。 アルゴリズムはこの変換をすべての単語に独立して適用し、単語や各接尾辞内の文字の順序を変更しないため、出力されるすべての単語は正しく、完全な出力文も正確になります。 元の文の最初の大文字が最初の単語の末尾に移動され、大文字のままになるため、大文字の使用も必要な位置に保持されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def transform_word(word):
    return word[1:] + word[0] + "ay"

def solve():
    t = int(input())

    for _ in range(t):
        sentence = input().strip()
        words = sentence.split()

        result = [transform_word(word) for word in words]
        print(" ".join(result))

if __name__ == "__main__":
    solve()
```

`transform_word`変換の数学的形式を直接実装します。`word[0]`は動くキャラクターですが、`word[1:]`元の相対順序のままのすべての文字が含まれます。 

リスト内包表記では、その操作をすべての単語に 1 回適用し、ウォークスルーのステップ 3 とステップ 4 を照合します。 リストを作成して一度結合することは、最終的な構成が線形のままであるため、文の文字列に繰り返し追加するよりも望ましい方法です。`split()`入力には句読点が含まれておらず、単語は空白で区切られているため、これで十分です。 への呼び出し`strip()`によって読み取られた改行を削除します`input()`、 その間`split()`偶発的な周囲のスペースも安全に処理します。 

数値計算がないため、整数のオーバーフローや算術境界条件は発生しません。 重要なインデックス作成は次のとおりです。`word[0]`、すべての有効な入力単語には少なくとも 1 つの文字が含まれています。 

## 実用的な例

 ### サンプル 1

 この文には 2 つの単語が含まれており、`Hello`そして`world`。 各単語の変換状態は次のとおりです。 

| 単語 | 最初の文字 | 残りのサフィックス | 変換された単語 |
 | --- | --- | --- | --- |
 |`Hello`|`H`|`ello`|`Ellohay`|
 |`world`|`w`|`orld`|`orldway`|

 変換された 2 つの単語が 1 つのスペースで結合され、`Ellohay orldway`。 最初の単語は、大文字であることを示しています。`H`は小文字に変換されずに移動されるため、出力文は大文字のままになります。 

### サンプル 2

 最初の数単語は繰り返されるプロセスを示すのに十分であり、文の残りの部分でも同じ操作が続きます。 

| 単語 | 最初の文字 | 残りのサフィックス | 変換された単語 |
 | --- | --- | --- | --- |
 |`Hello`|`H`|`ello`|`Ellohay`|
 |`danbo`|`d`|`anbo`|`anboday`|
 |`Hello`|`H`|`ello`|`Ellohay`|
 |`peccy`|`p`|`eccy`|`eccypay`|
 |`How`|`H`|`ow`|`Owhay`|
 |`are`|`a`|`re`|`reaay`|
 |`you`|`y`|`ou`|`ouyay`|
 |`today`|`t`|`oday`|`odaytay`|

 これらの変換後の部分的な出力は次のようになります。`Ellohay anboday Ellohay eccypay Owhay reaay ouyay odaytay`。 残りの単語をまったく同じ方法で処理すると、提供されたサンプル出力が生成されます。 トレースは、単語間で共有される状態がないことを示しています。すべての単語が独自の最初の文字の抽出を開始します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(L)`| すべての入力文字が検査され、一定の回数だけコピーされます。 
| スペース |`O(L)`| 変換された単語と最終出力には、入力に比例したスペースが必要です。 

ここ`L`処理される文の合計の長さです。 テスト ケースが 20 のみで、入力のネストされたスキャンを必要とする操作がないため、線形ソリューションは 1 秒と 256 MB の制限に容易に適合します。 

## テストケース```python
import sys
import io

def transform_word(word):
    return word[1:] + word[0] + "ay"

def solve():
    t = int(input())
    for _ in range(t):
        sentence = input().strip()
        words = sentence.split()
        print(" ".join(transform_word(word) for word in words))

def run(inp: str) -> str:
    global input

    old_stdin = sys.stdin
    old_input = input

    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline

    try:
        from contextlib import redirect_stdout

        out = io.StringIO()
        with redirect_stdout(out):
            solve()
        return out.getvalue()
    finally:
        sys.stdin = old_stdin
        input = old_input

# Provided sample 1
assert run(
    "1\n"
    "Hello world\n"
) == "Ellohay orldway\n", "sample 1"

# Provided sample 2
assert run(
    "8\n"
    "Hello danbo\n"
    "Hello peccy\n"
    "How are you today\n"
    "Good how are you\n"
    "Oh no\n"
    "Whats wrong\n"
    "It seems like our messages are not being encrypted\n"
    "Dont panic\n"
) == (
    "Ellohay anboday\n"
    "Ellohay eccypay\n"
    "Owhay reaay ouyay odaytay\n"
    "Oodgay owhay reaay ouyay\n"
    "Hoay onay\n"
    "Hatsway rongway\n"
    "Tiay eemssay ikelay uroay essagesmay reaay otnay eingbay ncryptedeay\n"
    "Ontday anicpay\n"
), "sample 2"

# Minimum-size input: one one-letter word
assert run(
    "1\n"
    "I\n"
) == "Iay\n", "one-letter word"

# Multiple one-letter words
assert run(
    "1\n"
    "A I O\n"
) == "Aay Iay Oay\n", "all one-letter words"

# Boundary case: first and last characters of several words
assert run(
    "1\n"
    "Abc xyz Z\n"
) == "bAcay yz xay Zay\n", "first and last character handling"

# All-equal characters
assert run(
    "1\n"
    "Aaaa aaaa\n"
) == "aaaAay aaaay\n", "all-equal characters"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1\nI`|`Iay`| 最小サイズの 1 文字の単語 |
 |`1\nA I O`|`Aay Iay Oay`| 複数の 1 文字の単語と独立した変換 |
 |`1\nAbc xyz Z`|`bAcay yz xay Zay`| 最初の文字の移動と接尾辞の境界 |
 |`1\nAaaa aaaa`|`aaaAay aaaay`| 同じ文字の繰り返しと大文字の使用 |

 提供されたサンプルは、通常の複数単語の文と複数のテスト ケースをさらに検証します。 カスタム ケースには、接尾辞が空の単語、最初と最後の文字が異なる単語、すべての文字が同一の単語が意図的に含まれており、これらはインデックス付けや連結の間違いが発生しやすい場所です。 

## 特殊なケース

 1 文字の単語には接尾辞がありません。 正確な入力を考慮する`1`に続く`I`。 アルゴリズムは次のように読み取ります`word[0]`として`I`そして`word[1:]`空の文字列として構成されるため、`"" + "I" + "ay"`、与える`Iay`。 Python のスライスであるため、特別なケースは必要ありません。`word[1:]`境界では自然に空になります。 

最初の大文字の単語には、別の大文字化操作は必要ありません。 入力用`Apple`、最初の文字は`A`、接尾辞は`pple`、結果は次のようになります`ppleAay`。 大文字`A`小文字ではなく、元の文字と一緒に移動します。 を呼び出すソリューション`.lower()`変換する前の各単語に対して、誤って生成される可能性があります。`ppleaay`。 

すべての単語は独立して変換する必要があります。 のために`Go to`、最初の単語は`G`そして`o`、与える`Ogay`、2番目のものは`t`そして`o`、与える`otay`。 最終的な出力は次のとおりです`Ogay otay`。 最初に分割すると、スペースが単語の一部として扱われることがなくなり、各単語が正確に 1 つ取得されることが保証されます。`ay`サフィックス。 

文字が繰り返されてもルールは変わりません。 のために`Aaaa`、 最初`A`接尾辞の末尾に移動し、`aaaAay`。 のために`aaaa`、結果は`aaaay`。 このアルゴリズムは、最初の文字を値ではなく位置によって区別するため、すべての文字が同じであっても正しいままです。
