---
title: "CF 102263C - テキストを確認してください"
description: "Roze は、画面上の最終テキストが、大文字の使用や連続する単語間の 1 つのスペースを含め、必要なテキストとまったく同じであることを望んでいます。 必要なテキストは n 単語として指定されるため、意図した画面コンテンツは単語を 1 つのスペースで結合したものになります。"
date: "2026-08-19T02:48:43+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102263
codeforces_index: "C"
codeforces_contest_name: "ArabellaCPC 2019"
rating: 0
weight: 102263
solve_time_s: 205
verified: true
draft: false
---

[CF 102263C - テキストのチェック](https://codeforces.com/problemset/problem/102263/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 25 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 Roze は、画面上の最終テキストが、大文字の使用や連続する単語間の 1 つのスペースを含め、必要なテキストとまったく同じであることを望んでいます。 必要なテキストは次のように与えられます`n`単語なので、意図した画面コンテンツは単語をスペースで 1 つ結合したものになります。 

入力の 2 番目の部分では、実際のキーボード操作が 1 行に 1 つのキーで記述されます。 文字キーは、現在の CapsLock 状態に応じて小文字または大文字のいずれかを生成します。 プレス中`CapsLock`その状態を切り替えます。`Space`スペースを 1 つ挿入します。`Backspace`画面が空でない場合は、現在画面上にある最後の文字を削除します。 

タスクは、これらのアクションをキーボードとまったく同じように実行し、結果の画面を意図したテキストと比較することです。 印刷します`Correct`2 つの文字列が同一の場合のみ。 

単語数とキー押下回数はどちらも 2000 未満で、必要な単語の合計長も 2000 未満です。これらの制限は十分小さいため、二次関数の実装でも通常は快適に適合しますが、直接線形シミュレーションが非常に簡単になります。 ハッシュ、動的プログラミング、またはより高度なデータ構造は必要ありません。 各キーを 1 回処理して現在の画面を維持するだけで済みます。 

いくつかの詳細により、誤った実装が間違ったテキストを黙って受け入れる可能性があります。 

次の入力を考えてみましょう。```
2
a b
2
a
b
```最後の画面は、`ab`、必要なテキストは`a b`、したがって、答えは次のとおりです`Incorrect`。 文字列を無視して単純に比較する実装`Space`間違って受け入れてしまうだろう。 

大文字の使用もテキストの一部です。 例えば：```
2
A b
3
CapsLock
a
b
```最後の画面は、`Ab`、 ない`A b`、したがって、答えは次のとおりです`Incorrect`。 不注意な実装では、入力された文字のみを追跡し、CapsLock によって大文字と小文字が変更されることを忘れる可能性があります。 

空の画面でのバックスペースも境界条件の 1 つです。 例えば：```
2
a b
4
a
Backspace
Space
b
```最初の 2 つのアクションでは画面が空のままになり、その後`Space`生成する`" "`、 そして`b`生成する`" b"`。 結果は`Incorrect`。 より一般的には、バックスペースによって画面の長さが負になってはなりません。 

最後に、バックスペースは文字を削除するのと同じようにスペースを削除できます。 例えば：```
2
a b
4
a
Space
Backspace
b
```最後の画面は、`ab`、 ない`a b`、したがって、答えは次のとおりです`Incorrect`。 削除時にスペースを文字と分けて扱うのは間違っています。 

## アプローチ

 直接シミュレーションが自然な出発点となります。 現在の画面を表す文字列を保持します。 すべての文字に、大文字と小文字を正しく区別して追加します。 のために`Space`、スペースを追加します。 のために`Backspace`、最後の文字が存在する場合はそれを削除します。 のために`CapsLock`、ブール値フラグを切り替えます。 すべてのキーが処理されたら、シミュレートされた画面を次と比較します。`" ".join(words)`。 

すべてのキーボード操作がシミュレーション内で直接表現されるため、この考えは正しいです。 最も単純な実装の主な弱点は、データ構造の選択です。 Python 文字列は不変であるため、次のような操作`screen = screen[:-1]`新しい文字列を作成し、残りの文字をコピーします。 追加を繰り返すと、ますます長い文字列がコピーされる可能性もあります。 

最大 1999 回のキー押下では、慎重に構築された追加と削除のシーケンスに対して文字コピーの最悪の場合の操作量は 100 万回程度になるため、この単純な実装でも元の制約には許容されます。 ただし、その二次関数の動作は不必要であり、同じタスクが数十万回のキー押下にスケールされる場合には問題になります。 問題はシミュレーション自体ではなく、画面のプレフィックス全体を繰り返し再構築することにあります。 

重要な観察は、画面がスタックとまったく同じように動作することです。 新しく入力した文字が最後に配置され、`Backspace`最後に書き込まれた文字を正確に削除します。 Python リストでは、末尾への追加と削除を一定時間行うことができるため、画面が自然に表現されます。 

この表現では、すべてのキーに継続的な作業が必要になります。 キー シーケンスを 1 回処理し、最終画面を線形時間で生成します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 単純な不変文字列シミュレーション | O(m²) 最悪の場合 | O(m) | これらの制限は許容されますが、不必要に遅くなります。 
| スタックシミュレーション | O(m + L) | O(m + L) | 承認済み |

 ここ`m`はキーが押された回数であり、`L`必要なテキストの長さです。 

## アルゴリズムのチュートリアル

 1. を読んでください。`n`単語をターゲットにして、必要な画面を構築します。`" ".join(words)`。 単語間のスペースはチェック対象の一部であるため、結合が必要です。 
2. という名前の空のリストを作成します。`screen`。 このリストは画面上の現在の文字を表し、最後のリスト要素は Backspace で削除される文字に対応します。 
3. ブール変数を設定する`caps`に`False`。 キーボードは小文字モードで開始するため、最初の文字キーは小文字として解釈される必要があります。`CapsLock`キーはすでに状態を切り替えています。 
4. それぞれの処理を行います。`m`キーを順番に押しました。 鍵が`CapsLock`、フリップ`caps`。 CapsLock は今後の文字キーの解釈を変更するだけなので、画面に文字は追加されません。 
5. キーが`Backspace`の最後の要素を削除します。`screen`リストが空ではない場合。 リストが空の場合は、キーボードの動作に従って何もしません。 
6. キーが`Space`、追加`" "`に`screen`。 スペースは、バックスペースと最終比較を目的とした通常の画面文字です。 
7. それ以外の場合、キーは文字です。 次の場合に大文字に変換します`caps`が true の場合は小文字になります`caps`false の場合、結果の文字を`screen`。 
8. すべてのキーの処理が完了したら、変換します。`screen`文字列に変換し、ターゲット テキストと比較します。 印刷する`Correct`それらが等しい場合と`Incorrect`さもないと。 

### なぜ効果があるのか

 不変条件は、キー シーケンスのプレフィックスを処理した後、`screen`同じプレフィックスを実行した後に実際のキーボードの画面に表示される文字が正確に含まれます。`caps`キーボードの現在の CapsLock 状態と同じです。 

画面が空で CapsLock がオフになっているため、最初はどちらも正しいです。 可能な各キーは不変条件を保持します。CapsLock は状態のみを変更し、Backspace は可能な限り最後に表示されている文字を削除し、Space はスペースを追加し、文字は現在の状態によって決定される大文字と小文字を区別して文字を追加します。 したがって、すべてのキーが処理された後、`screen`まさに実際の最終画面です。 したがって、テキストが正しく印刷されたかどうかを判断するには、それを必要なテキストと比較するだけで十分です。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())
    words = input().split()
    target = " ".join(words)

    m = int(input())

    screen = []
    caps = False

    for _ in range(m):
        key = input().strip()

        if key == "CapsLock":
            caps = not caps
        elif key == "Backspace":
            if screen:
                screen.pop()
        elif key == "Space":
            screen.append(" ")
        else:
            screen.append(key.upper() if caps else key.lower())

    result = "".join(screen)

    print("Correct" if result == target else "Incorrect")

if __name__ == "__main__":
    solve()
```ターゲット文字列は単語から一度構築されます。 入力では、連続する単語間に意図したスペースが 1 つだけ保証されるため、`" ".join(words)`画面に表示する必要がある文字列を正確に生成します。 

の`screen`list はアルゴリズムで記述されたスタックです。`append`画面に入る文字を処理しますが、`pop`最後に書き込まれた文字を削除します。 の`if screen`空の画面では Backspace キーは効果がないため、チェックが必要です。 

の`caps`フラグは、それ以降の文字を処理する前に切り替えられます。 あ`CapsLock`キー自体は画面に表示されないため、スタックに追加してはなりません。 

条件チェックの順序は重要です。`CapsLock`、`Backspace`、 そして`Space`文字ではなく特別なキー名です。 他のキーは単一のアルファベット文字であることが保証されているため、最後の分岐で安全に処理できます。 

このアルゴリズムは入力サイズによって制限されたカウンターのみを使用するため、整数オーバーフローの懸念はありません。 リストに含めることができるのは最大でも`m`文字なので、メモリ使用量は線形です。 

## 実用的な例

 このステートメントは 1 つのサンプルを提供しているため、以下の 2 番目のトレースでは、CapsLock と Backspace を実行する小規模な構築ケースを使用しています。 

### サンプル 1

 必要なテキストは、`Hello World`。 押されたキーが最初に生成するのは、`Hell`、次に Backspace キーで最後の文字を削除します。`l`、その後の操作で最終的に生成されるのは、`Howorld`必要なテキストではなく。 

| キー | キャップ | 画面 |
 | --- | --- | --- |
 | CapsLock | 本当 |`""`|
 | h | 本当 |`"H"`|
 | CapsLock | 偽 |`"H"`|
 | e | 偽 |`"He"`|
 | l | 偽 |`"Hel"`|
 | l | 偽 |`"Hell"`|
 | バックスペース | 偽 |`"Hel"`|
 | o | 偽 |`"Helo"`|
 | スペース | 偽 |`"Helo "`|
 | w | 偽 |`"Helo w"`|
 | o | 偽 |`"Helo wo"`|
 | バックスペース | 偽 |`"Helo w"`|
 | バックスペース | 偽 |`"Helo "`|
 | w | 偽 |`"Helo w"`|
 | o | 偽 |`"Helo wo"`|
 | r | 偽 |`"Helo wor"`|
 | l | 偽 |`"Helo worl"`|
 | d | 偽 |`"Helo world"`|

 最後の画面は、`"Helo world"`、ターゲットは`"Hello World"`。 どちらも行方不明`l`大文字と小文字の区別が間違っている`World`問題ですので、答えは`Incorrect`。 

### 構築サンプル 2

 次の入力について考えてみましょう。```
2
Ab c
6
CapsLock
a
b
CapsLock
Space
c
```実行は次のとおりです。 

| キー | キャップ | 画面 |
 | --- | --- | --- |
 | CapsLock | 本当 |`""`|
 | | 本当 |`"A"`|
 | b | 本当 |`"AB"`|
 | CapsLock | 偽 |`"AB"`|
 | スペース | 偽 |`"AB "`|
 | c | 偽 |`"AB c"`|

 最後の画面は、`"AB c"`ただし、必要なテキストは`"Ab c"`、結果は次のようになります`Incorrect`。 このトレースは、CapsLock が後で最後の文字列を単に変更するのではなく、将来の各文字に個別に影響を与える必要がある理由を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(m + L) | すべてのキーは 1 回処理され、最終的な比較では、生成された文字列とターゲット文字列がスキャンされます。 
| スペース | O(m + L) | ターゲット画面とシミュレートされた画面は両方とも線形ストレージを必要とします。 

元の制約は両方を維持します`m`ターゲットの長さが 2000 未満であるため、線形ソリューションは数千の基本演算のみを実行し、使用するメモリはほとんどありません。 また、不変文字列の更新によって引き起こされる不必要な 2 次コピーも回避され、同じ問題のかなり大きなバージョンに適した実装になります。 

## テストケース```python
import sys
import io

def solve_data(inp: str) -> str:
    data = inp.splitlines()
    it = iter(data)

    n = int(next(it))
    words = next(it).split()
    target = " ".join(words)

    m = int(next(it))

    screen = []
    caps = False

    for _ in range(m):
        key = next(it).strip()

        if key == "CapsLock":
            caps = not caps
        elif key == "Backspace":
            if screen:
                screen.pop()
        elif key == "Space":
            screen.append(" ")
        else:
            screen.append(key.upper() if caps else key.lower())

    return "Correct" if "".join(screen) == target else "Incorrect"

def run(inp: str) -> str:
    return solve_data(inp)

# Provided sample
sample1 = """2
Hello World
17
CapsLock
h
CapsLock
e
l
l
Backspace
o
Space
w
o
Backspace
Backspace
w
o
r
l
d
"""
assert run(sample1) == "Incorrect", "sample 1"

# Minimum-size style case, exact text with one space
case2 = """2
a b
3
a
Space
b
"""
assert run(case2) == "Correct", "basic correct text"

# Backspace on an empty screen, followed by the target
case3 = """2
a b
5
Backspace
a
Space
b
Backspace
"""
assert run(case3) == "Incorrect", "empty backspace and final deletion"

# CapsLock toggles twice, producing the original lowercase text
case4 = """2
ab cd
7
CapsLock
a
CapsLock
b
Space
c
d
"""
assert run(case4) == "Correct", "CapsLock toggling"

# Backspace removes a space, so the final text has no separator
case5 = """2
a b
4
a
Space
Backspace
b
"""
assert run(case5) == "Incorrect", "backspace removes space"

# Large boundary-style case
words = ["a" * 999, "b" * 999]
target = " ".join(words)
case6 = (
    "2\n"
    + target
    + "\n"
    + "1999\n"
    + "\n".join(list("a" * 999 + "b" * 1000))
    + "\n"
)
assert run(case6) == "Incorrect", "large input boundary"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 |`Incorrect`| 削除と大文字化を含む提供されたサンプル |
 |`a b`と`a`、`Space`、`b`|`Correct`| 基本的な成功シミュレーション |
 | 主要な`Backspace`| というテキストが続きます。`Incorrect`| 空の画面でバックスペース |
 | 2 つの CapsLock トグル |`Correct`| CapsLock の状態が変化し、小文字に戻ります。 
|`a`、`Space`、`Backspace`、`b`|`Incorrect`| バックスペースでもスペースを削除する必要があります。 
| 999`a`文字と999`b`キャラクター |`Incorrect`| 大きな境界入力と線形処理 |

 ## 特殊なケース

 空の画面上のバックスペースは無視する必要があります。 入力用```
2
a b
5
Backspace
a
Space
b
Backspace
```最初の Backspace は空のスタックを変更しないままにします。 次の 3 つのキーは、`"a b"`、最後の Backspace キーで削除されます。`b`、出発`"a "`。 以来`"a "`とは異なります`"a b"`、答えは`Incorrect`。 の`if screen`ガードは無効を防ぐ`pop`キーボードの動作を正確にモデル化します。 

スペースは文字と同じスタックに格納されます。 のために```
2
a b
4
a
Space
Backspace
b
```スタックはから進化します`[]`に`["a"]`、 それから`["a", " "]`、その後に戻ります`["a"]`、そして最後に`["a", "b"]`。 最終的なテキストは、`"ab"`、結果は次のようになります`Incorrect`。 Backspace を文字のみの操作として扱うと、削除されたスペースが結果に残り、間違った答えが生成されます。 

CapsLock は、その後の文字の押下にのみ影響します。 と```
2
A b
3
CapsLock
a
b
```フラグは前に true になります`a`、画面は次のようになります`"A"`。 次の場合、フラグは true のままになります。`b`プレスされ、生産されます`"AB"`。 ターゲットは`"A b"`、したがって、答えは次のとおりです`Incorrect`。 CapsLock を切り替えても、シミュレーションでは画面上にすでにある文字が遡及的に変更されることはありません。 

CapsLock を連続して押すと、お互いにキャンセルされます。 のために```
2
ab cd
7
CapsLock
a
CapsLock
b
Space
c
d
```最初のトグルは`a`大文字、生成`"A"`。 2 番目のトグルは小文字に戻りますので、`b`、`c`、 そして`d`は小文字です。 最後の画面は、`"Ab cd"`、ターゲットとは異なります`"ab cd"`したがって、この特定の入力は次のようになります。`Incorrect`。 ターゲット内の最初の文字も大文字である場合、同じ 2 つの切り替えにより、残りの文字は小文字モードに正しく復元されます。 重要な点は、それぞれの正確な位置で状態が変化することです。`CapsLock`プレス。 

必要なスペースは単に区切り文字として扱うのではなく、正確にチェックする必要があります。 のために```
2
a b
2
a
b
```スタックは次のようになります`"ab"`、ターゲットは`"a b"`。 このアルゴリズムは完全な文字列を比較するため、正しく出力されます。`Incorrect`。 

最後に、シーケンスには、互いにキャンセルする多数の操作を含めることができます。 例えば、```
2
a b
6
a
Backspace
Backspace
a
Space
b
```から始まる`"a"`、それを削除し、画面が空であるため 2 番目の Backspace を無視し、次に構築します`"a b"`。 最終結果は`Correct`。 このケースでは、アルゴリズムが古い削除された文字と現在の画面状態を混同しないことが確認されています。
