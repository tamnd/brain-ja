---
title: "CF 412E - 電子メール アドレス"
description: "小文字、数字、、@、および . のみを含む 1 つの長い文字列が与えられます。この文字列の部分文字列のうち有効な電子メール アドレスがいくつあるかを数えなければなりません。 部分文字列は、テキストの内容ではなく、その位置によって区別されます。"
date: "2026-06-07T02:21:17+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 412
codeforces_index: "E"
codeforces_contest_name: "Coder-Strike 2014 - Round 1"
rating: 1900
weight: 412
solve_time_s: 218
verified: false
draft: false
---

[CF 412E - 電子メール アドレス](https://codeforces.com/problemset/problem/412/E)

 **評価:** 1900
 **タグ:** 実装
 **解決時間:** 3 分 38 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 小文字、数字のみを含む 1 つの長い文字列が与えられます。`_`、`@`、 そして`.`。 

この文字列のうち、有効な電子メール アドレスである部分文字列がいくつあるかを数えなければなりません。 部分文字列は、テキストの内容ではなく、その位置によって区別されます。 同じ電子メール テキストが 2 つの異なる場所に出現する場合、両方の出現が回答に寄与します。 

有効なアドレスの形式は次のとおりです。`local_part@domain.tld`ローカル部分は空であってはならず、文字、数字、および文字を含めることができます。`_`、その最初の文字は文字である必要があります。 

間のドメイン部分`@`そして`.`空であってはならず、文字または数字のみを含む必要があります。 

この後の最後の部分は、`.`空であってはならず、文字のみを含む必要があります。 

文字列の長さは次のとおりです$10^6$。 すべての部分文字列を検査するアルゴリズムは不可能です。 くらいあります$5 \cdot 10^{11}$最悪の場合、部分文字列の一部をチェックするだけでも制限時間を超えてしまいます。 

課題は、単一の`@`ローカル部分は異なる文字位置で始まる可能性があるため、多くの異なる有効なアドレスに参加できます。`.`アドレスは最後の文字ブロックの異なる文字で終わる可能性があるため、さまざまな有効なアドレスを含めることができます。 

いくつかの特殊なケースは、誤って処理されやすいものです。 

考慮する：```
1@a.b
```ローカル部分は数字で始まるため、有効なアドレスはありません。 ローカル部分に有効な文字が含まれているかどうかだけをチェックするソリューションでは、誤って 1 つのアドレスがカウントされます。 

考慮する：```
ab@12.xyz
```ドメインには数字が含まれる場合があるため、これは有効です。 ドメインを文字のみとして扱うと、有効な回答が失われます。 

考慮する：```
a@b.cd3
```アドレスをその後で終了することはできません`3`最後のコンポーネントは文字のみで構成されている必要があるためです。 有効な語尾は、ドットの直後の連続文字ブロック内にのみあります。 

考慮する：```
ab_cd@x.yz
```ローカル部分は次から始まる可能性があります`a`またはで`b`開始位置が文字であり、同じ有効なローカル文字ブロック内にある場合は、有効なローカル部分が作成されるためです。 最大のローカル部分のみをカウントすると、過少カウントになります。 

## アプローチ

 ブルート フォース ソリューションでは、すべての部分文字列を列挙し、それが電子メールの形式と一致するかどうかを確認します。 がある$O(n^2)$部分文字列。 たとえ検証があったとしても$O(1)$、これはすでに大きすぎます$n = 10^6$。 

重要な観察は、有効なアドレスは次の選択によって完全に決定されるということです。`@`そして`.`。 

ドットの位置が固定されていると仮定します。`j`。 

ドメイン部分を有効にするには、間のすべての文字が必要です。`@`そして`.`英数字である必要があります。 最大の英数字ブロックの末尾を次のようにします。`j - 1`位置から開始`t`。 

その場合、考えられるのは最大でも 1 つです`@`: 正確な位置になければなりません`t - 1`。 

それより前の場合、間隔には英数字以外の文字が含まれます。 それより後の場合は、英数字ブロックの内側にあることになりますが、これは不可能です。`@`は英数字ではありません。 

これにより問題が大幅に軽減されます。 各ドットには最大 1 つの候補があります`@`。 

ここで必要なのは 2 つの多重度だけです。 

固定有効の場合`@`、ローカル部分の開始候補はいくつ存在しますか? 許可されるローカル文字の最大ブロック内のすべての文字が直前で終わる`@`始まりとして機能できます。 

固定された有効なドットの場合、可能な端はいくつ存在しますか? ドットの直後の連続文字ブロック内のすべての文字が終了位置として機能します。 

もし`starts = number of valid local-part starts`そして`ends = number of valid address endings`それからこれ`@`そして`.`貢献する`starts × ends`有効な部分文字列。 

必要な情報はすべてリニア スキャンで事前計算できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n²) 以上 | お(1) | 遅すぎる |
 | 最適 | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 左から右にスキャンし、ローカル部分で許可される文字の最大ブロックに関する情報を維持します。つまり、文字、数字、`_`。 

位置ごとに、その位置で終わる現在のブロックに出現する文字の数を保存します。 
2. あらゆる人にとって`@`位置、計算`start_count`。 

これは、直前で終わるローカル文字ブロックの最大の文字数と等しくなります。`@`。 

このような各文字は、ローカル部分の最初の文字として選択できます。 
3. 左から右にスキャンして、現在の最大の英数字ブロックの開始位置を計算します。 

英数字の位置ごとに`i`、を含むブロックの左境界を格納します。`i`。 
4. 右から左にスキャンして計算します`letter_len[i]`、位置から始まる連続する文字ブロックの長さ`i`。 
5. すべてのドット位置を反復します。`j`。 
6. 最後のコンポーネントはドットの直後から開始する必要があります。`j + 1`は文字ではないので、このドットは飛ばしてください。 
7. ドメインは空ではなく、英数字である必要があります。`j - 1`英数字である必要があります。 そうでない場合は、このドットをスキップしてください。 
8. しましょう`t`で終わる最大の英数字ブロックの始まりである`j - 1`。 

唯一可能なのは`@`位置にあります`t - 1`。 
9. その位置が存在し、次の内容が含まれている場合`@`、 追加`start_count[@] × letter_len[j + 1]`答えに。 
10. 累計を出力します。 

### なぜ効果があるのか

 すべての有効な電子メール部分文字列について、`@`そして`.`空ではない英数字ブロックです。 ドットの前で終わる最大の英数字ブロックによって、どこにあるかが一意に決まります。`@`つまり、そのブロックの直前に立っていなければなりません。 

逆に、ドットの前にそのような記号があるときは常に、`@`、すべての有効なローカル部分の開始文字とすべての有効な最終文字終了文字が、個別の有効な部分文字列を生成します。 

アルゴリズムはこれらの組み合わせを正確にカウントし、それ以外はカウントしません。 すべての有効な電子メールが 1 回貢献し、カウントされるすべての組み合わせがすべての形式要件を満たします。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    s = input().strip()
    n = len(s)

    def is_letter(c):
        return 'a' <= c <= 'z'

    def is_digit(c):
        return '0' <= c <= '9'

    def is_alnum(c):
        return is_letter(c) or is_digit(c)

    def is_local(c):
        return is_alnum(c) or c == '_'

    local_letters = [0] * n
    cur = 0

    for i, ch in enumerate(s):
        if is_local(ch):
            if i > 0 and is_local(s[i - 1]):
                cur += 1 if is_letter(ch) else 0
            else:
                cur = 1 if is_letter(ch) else 0
            local_letters[i] = cur
        else:
            cur = 0

    start_count = [0] * n
    for i, ch in enumerate(s):
        if ch == '@' and i > 0:
            start_count[i] = local_letters[i - 1]

    run_start = [0] * n
    start = 0

    for i, ch in enumerate(s):
        if is_alnum(ch):
            if i > 0 and is_alnum(s[i - 1]):
                run_start[i] = start
            else:
                start = i
                run_start[i] = start

    letter_len = [0] * (n + 1)
    for i in range(n - 1, -1, -1):
        if is_letter(s[i]):
            letter_len[i] = letter_len[i + 1] + 1

    ans = 0

    for j, ch in enumerate(s):
        if ch != '.':
            continue

        if j + 1 >= n or letter_len[j + 1] == 0:
            continue

        if j == 0 or not is_alnum(s[j - 1]):
            continue

        t = run_start[j - 1]
        at = t - 1

        if at >= 0 and s[at] == '@':
            ans += start_count[at] * letter_len[j + 1]

    print(ans)

if __name__ == "__main__":
    solve()
```最初のスキャンでは、位置ごとに、現在のローカル文字ブロックに属する文字の数が計算されます。 これは、任意のローカルパートの有効な開始位置の数を直接与えます。`@`。 

2 番目のスキャンでは、すべての英数字の実行の開始が記録されます。 これにより、ユニークな可能性を回復できるようになります。`@`与えられたドットに対して。 

逆方向スキャンでは、各位置から始まる連続する文字ブロックの長さを計算します。 ドットの後に一定の長さの文字ブロックが続く場合`k`、そうすると、まさにあります`k`有効な終了位置。 

最もデリケートな部分は候補者を見つけることです`@`。 正しい位置は任意ではありません。 これは、次で終わる最大の英数字ブロックの直前に存在する必要があります。`j - 1`。 この観察が欠けていると、通常、二次関数の動作や誤ったカウントが発生します。 

すべてのカウントは 64 ビット整数に快適に収まりますが、必ずしも 32 ビット整数に収まるわけではありません。 Python の整数はこれを自動的に処理します。 

## 実用的な例

 ### 例 1

 入力:```
agapov@r1.com
```関連する値:

 | ドット位置 | 候補者 @ | ローカルスタート | 手紙の終わり | 貢献 |
 | --- | --- | --- | --- | --- |
 | 9 | 6 | 6 | 3 | 18 |

 答え：```
18
```中の6文字は、`agapov`すべてローカル部分の開始として選択できます。 中の3文字は、`com`すべて終了位置として選択できます。 すべての組み合わせにより、個別の有効な部分文字列が生成されます。 

### 例 2

 入力:```
ab@c.de
```| ドット位置 | 候補者 @ | ローカルスタート | 手紙の終わり | 貢献 |
 | --- | --- | --- | --- | --- |
 | 4 | 2 | 2 | 2 | 4 |

 答え：```
4
```ローカル部分は次から始まる可能性があります`a`または`b`。 アドレスは次で終わる場合があります`d`または`e`。 That gives four valid substrings.

 この例では、カウント式の乗算構造を示します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | A constant number of linear scans |
 | スペース | O(n) | Arrays storing run information |

 文字列の長さは 100 万文字に達する場合があります。 線形ソリューションは数百万の単純な操作のみを実行し、制限時間内に簡単に収まります。 補助配列のサイズも線形であり、メモリ制限内に快適に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def solve_io():
    input = sys.stdin.readline
    s = input().strip()
    n = len(s)

    def is_letter(c):
        return 'a' <= c <= 'z'

    def is_digit(c):
        return '0' <= c <= '9'

    def is_alnum(c):
        return is_letter(c) or is_digit(c)

    def is_local(c):
        return is_alnum(c) or c == '_'

    local_letters = [0] * n
    cur = 0

    for i, ch in enumerate(s):
        if is_local(ch):
            if i > 0 and is_local(s[i - 1]):
                cur += 1 if is_letter(ch) else 0
            else:
                cur = 1 if is_letter(ch) else 0
            local_letters[i] = cur
        else:
            cur = 0

    start_count = [0] * n
    for i, ch in enumerate(s):
        if ch == '@' and i > 0:
            start_count[i] = local_letters[i - 1]

    run_start = [0] * n
    start = 0

    for i, ch in enumerate(s):
        if is_alnum(ch):
            if i > 0 and is_alnum(s[i - 1]):
                run_start[i] = start
            else:
                start = i
                run_start[i] = start

    letter_len = [0] * (n + 1)
    for i in range(n - 1, -1, -1):
        if is_letter(s[i]):
            letter_len[i] = letter_len[i + 1] + 1

    ans = 0

    for j, ch in enumerate(s):
        if ch != '.':
            continue

        if j + 1 >= n or letter_len[j + 1] == 0:
            continue

        if j == 0 or not is_alnum(s[j - 1]):
            continue

        t = run_start[j - 1]
        at = t - 1

        if at >= 0 and s[at] == '@':
            ans += start_count[at] * letter_len[j + 1]

    print(ans)

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    solve_io()
    return sys.stdout.getvalue().strip()

# provided sample
assert run("agapov@r1.com\n") == "18", "sample 1"

# custom cases
assert run("a\n") == "0", "minimum size"
assert run("a@b.c\n") == "1", "smallest valid email"
assert run("1@b.c\n") == "0", "local part must start with a letter"
assert run("ab@c.de\n") == "4", "multiple starts and endings"
assert run("ab_cd@x.yz\n") == "4", "underscore allowed inside local part"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`a`|`0`| 最小サイズの文字列 |
 |`a@b.c`|`1`| 最小の有効なアドレス |
 |`1@b.c`|`0`| ローカル部分を数字で始めることはできません |
 |`ab@c.de`|`4`| 複数の有効な開始と終了 |
 |`ab_cd@x.yz`|`4`| ローカル部分でのアンダースコアの処理 |

 ## 特殊なケース

 次のことを考慮してください。```
1@a.b
```前の最大ローカル文字ブロック`@`文字が含まれていません。 アルゴリズムストア`start_count = 0`したがって、ドメインとサフィックスが有効であっても、寄与はゼロになります。 出力は正しく次のようになります。```
0
```考慮する：```
a@12.xyz
```ブロック`12`完全に英数字です。 ドットは先行する一意の文字列を検索します。`@`、`start_count = 1`、接尾辞の長さは`3`。 答えは次のようになります。```
3
```のエンディングに対応`x`、`y`、 そして`z`。 

考慮する：```
a@b.cd3
```ドットの後の連続文字ブロックは長さがあります`2`、つまり`cd`。 数字`3`最終コンポーネントの一部ではありません。 アルゴリズムが使用するのは、`letter_len[j + 1] = 2`、正確に 2 つの有効なエンディングを生成します。 

考慮する：```
ab_cd@x.yz
```前に`@`、最大のローカル文字ブロックには文字が含まれます`a`そして`b`。 どちらも有効な開始位置です。 ドットの後の文字ブロックには長さがあります`2`。 アルゴリズムは計算します`2 × 2 = 4`、4 つの有効な部分文字列と一致します。
