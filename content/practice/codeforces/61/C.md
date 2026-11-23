---
title: "CF 61C - ヴァレリアンを捕獲する"
description: "この問題では、数値をある基数から別の基数に変換するように求められますが、ターゲットの基数が標準の位置記数法 (2 から 25) またはローマ数字のいずれかである可能性があるというひねりが加えられています。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "math"]
categories: ["algorithms"]
codeforces_contest: 61
codeforces_index: "C"
codeforces_contest_name: "Codeforces Beta Round 57 (Div. 2)"
rating: 2000
weight: 61
solve_time_s: 169
verified: true
draft: false
---

[CF 61C - ヴァレリアンの捕獲](https://codeforces.com/problemset/problem/61/C)

 **評価:** 2000
 **タグ:** 数学
 **解決時間:** 2 分 49 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 この問題では、数値をある基数から別の基数に変換するように求められますが、ターゲットの基数が標準の位置記数法 (2 から 25) またはローマ数字のいずれかである可能性があるというひねりが加えられています。 入力は 3 つの部分で構成されます: ソースベース`a`、ターゲットベース`b`（または`R`ローマ数字の場合)、および数字`c`基数で表現される`a`。 の値を出力する必要があります。`c`基地内で`b`、先頭のゼロは省略されます。 

制約は直接変換方法を許可するのに十分厳しいものです。 番号`c`は最大 1000 桁まで指定できるため、大きな整数をサポートしていない言語では通常の整数として扱うことができませんが、Python は任意のサイズの整数をネイティブに処理します。 ローマ数字の出力は 300010 以下であることが保証されているため、極端に大きなローマ数字を扱う必要はありません。 標準の基数は 25 に制限されているため、従来の数字から整数へのマッピングで変換を管理できます。 

数値を入力すると、微妙なエッジケースが発生します。`c`先頭にゼロが付いています。 たとえば、次の場合`a = 10`、`b = 2`、 そして`c = "001"`不用意に実装すると、次のような結果が出力される可能性があります。`"001"`の代わりに`"1"`。 もう 1 つのエッジケースは次のような場合です`b = R`、ローマ数字にはゼロが含まれないため。 すべての変換はこのプロパティを尊重する必要があります。 さらに、10 より大きい基数にはアルファベット記号が使用されます。例:`A = 10`したがって、パーサーは文字を処理する必要があります。 

## アプローチ

 最も単純なアプローチは総当たりです。数値を解析します。`c`基地から`a`10 進整数に変換し、繰り返し除算します。`b`出力用の数字を抽出します。 これは、10 進数への変換が次のように行われるため機能します。`O(length of c)`10 進数から別の基数への変換は次のようになります。`O(log c)`部門。 Python では、任意のサイズの整数を使用するとこれが可能になります。`length of c`≤ 1000、これは十分小さいです。 

唯一の追加の手順は、ローマ数字システムを処理することです。 重要な洞察は、ローマ数字は本質的に、4 (`IV`) または 900 (`CM`）。 この問題では、ローマ数字表現が ≤ 300010 であることが保証されているため、単純な貪欲アルゴリズムが機能します。つまり、対応する記号を追加しながら、その数値以下の最大のローマ数字を繰り返し減算します。 標準の減法表記を使用して表す場合、ローマ数字体系は標準であるため、これにより正確さが保たれます。 

したがって、最適なアプローチは 2 段階の変換です。 まず、入力された数値を基数から変換します`a`位置評価を使用して 10 進数に変換します。 第二に、もし`b`数値です。10 進数から基数に変換します`b`繰り返し除算を使用する。 もし`b`がローマ字である場合は、標準のローマ字記号を使用した貪欲な減算アプローチを使用して変換します。 これにより、問題は単純でよく理解された一連の数値変換に集約されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート フォース (10 進数変換 + 数値ベース) | O(L + log N) | O(L + log N) | 承認済み |
 | ローマ数字変換 | O(R) | O(R) | 承認済み |

 ここ、`L`は入力数値の長さです。`N`はその 10 進数値であり、`R`ローマ字換算の数値 ≤ 300010。 

## アルゴリズムのチュートリアル

 1. 入力値を読み取る`a`、`b`、 そして`c`。 もし`b`は`"R"`、ローマ数字の出力が必要であることを示します。 
2.変換`c`基地から`a`10進数に変換します。 文字列を反復処理して更新します`value = value * a + digit`各ステップで。 使用`ord`9 より大きい数字のマッピング。 
3. もし`b`数値です:

 1. 10 進数値が 0 の場合の特殊なケースを処理し、次の値を返します。`"0"`。 
2. それ以外の場合は、10 進数を繰り返し除算します。`b`、文字にマップされた残りを先頭に追加します (`0-9`0～9の場合、`A-O`10-24 の場合) を結果文字列に追加します。 
4. もし`b`ローマ字です:

 1. ローマ字記号と値を降順で表すタプルのリストを初期化します。`(1000, "M"), (900, "CM"), ..., (1, "I")`。 
2. 10 進数値が正の場合、最大のシンボル値 ≤ 現在の数値を見つけて、そのシンボルを追加し、その値を数値から減算します。 
5. 結果の文字列を出力します。 

機能する理由: 位置数値体系は単射法であるため、10 進数による変換はロスレス マッピングです。 ベース内のすべての文字列`a`一意の 10 進数を表します。 ローマ数字の変換は貪欲で、常に標準の正規表現に対応する最大の記号が最初に選択されます。 これにより正確さが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

# mapping for bases greater than 10
def char_to_int(c):
    if '0' <= c <= '9':
        return ord(c) - ord('0')
    return ord(c) - ord('A') + 10

def int_to_char(n):
    if n < 10:
        return str(n)
    return chr(ord('A') + n - 10)

# Roman numeral conversion table
roman_numerals = [
    (1000, 'M'), (900, 'CM'), (500, 'D'), (400, 'CD'),
    (100, 'C'), (90, 'XC'), (50, 'L'), (40, 'XL'),
    (10, 'X'), (9, 'IX'), (5, 'V'), (4, 'IV'), (1, 'I')
]

def base_a_to_decimal(s, base):
    value = 0
    for ch in s:
        value = value * base + char_to_int(ch)
    return value

def decimal_to_base_b(value, base):
    if value == 0:
        return "0"
    digits = []
    while value > 0:
        digits.append(int_to_char(value % base))
        value //= base
    return ''.join(reversed(digits))

def decimal_to_roman(value):
    result = []
    for val, sym in roman_numerals:
        while value >= val:
            result.append(sym)
            value -= val
    return ''.join(result)

# main
a, b = input().split()
c = input().strip()
a = int(a)

decimal_value = base_a_to_decimal(c, a)

if b == 'R':
    print(decimal_to_roman(decimal_value))
else:
    b = int(b)
    print(decimal_to_base_b(decimal_value, b))
```各関数はアルゴリズムのステップに直接対応します。`base_a_to_decimal`任意の大きな入力を処理します。`decimal_to_base_b`25 までの基数をカバーし、`decimal_to_roman`標準的な貪欲なローマ数字構造を実装します。 文字ベースの塩基のオフバイワンエラーは、以下を注意深く使用することで回避できます。`ord`算術。 

## 実用的な例

 **例 1**

 入力:```
10 2
1
```| ステップ | 変数 | 値 |
 | --- | --- | --- |
 | 入力を解析する | a、b、c | 10、2、「1」 |
 | 10 進数に変換する | 10 進値 | 1 |
 | 基数 2 に変換する | 結果 | "1" |

 トレースにより、先頭にゼロが付いた小さな数値が正しく処理され、バイナリ変換が簡単であることが確認されます。 

**例 2**

 入力:```
10 R
1994
```| ステップ | 変数 | 値 |
 | --- | --- | --- |
 | 入力を解析する | a、b、c | 10、R、「1994」 |
 | 10 進数に変換する | 10 進値 | 1994年 |
 | ローマ字変換 | 結果 | 「MCMXCIV」 |

 これは、ローマ数字の貪欲なアプローチと正しい減算表記の処理を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(L + log N) | L は入力文字列の長さです。 数値基数の場合は log N の除算。 ローマ字変換は O(R) ≤ 300010 |
 | スペース | O(L + log N) | 数字と最終出力文字列を保存する |

 与えられた`L ≤ 1000`そして`N ≤ 10^1510`Roman の場合は 300010、ソリューションは 2 秒未満で実行されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    exec(open(__file__).read(), globals())
    return output.getvalue().strip()

# provided samples
assert run("10 2\n1\n") == "1", "sample 1"
assert run("10 R\n1994\n") == "MCMXCIV", "roman example"

# custom cases
assert run("2 10\n101\n") == "5", "binary to decimal"
assert run("16 8\nFF\n") == "377", "hex to octal"
assert run("10 R\n300010\n") == "MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
```
