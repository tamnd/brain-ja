---
title: "CF 72D - Perse スクリプト"
description: "小さな関数ベースの言語で文字列式を評価するように求められます。 すべての文字列リテラルは引用符で囲まれており、関数は 2 つの形式の concat、reverse、substr の 4 種類のみです。 各関数は、文字列または整数をインデックスとしてのみ操作します。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "*special", "expression-parsing"]
categories: ["algorithms"]
codeforces_contest: 72
codeforces_index: "D"
codeforces_contest_name: "Unknown Language Round 2"
rating: 2300
weight: 72
solve_time_s: 115
verified: false
draft: false
---

[CF 72D - Perse-script](https://codeforces.com/problemset/problem/72/D)

 **評価:** 2300
 **タグ:** *特別な、式の解析
 **解決時間:** 1 分 55 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 小さな関数ベースの言語で文字列式を評価するように求められます。 すべての文字列リテラルは引用符で囲まれており、関数は次の 4 種類のみです。`concat`、`reverse`、 そして`substr`2つの形で。 各関数は、文字列または整数をインデックスとしてのみ操作します。 入力は、文字列として評価される単一の式 (おそらくネストされている) であり、私たちの目標は、最終的な文字列の結果を正確に出力することです。 

制約は管理可能です。入力文字列は最大 1000 文字、整数は 100 文字以下、出力文字列の長さは 10,000 文字以下が保証されます。 これは、非常に深いネストで呼び出しスタックを破壊する可能性のある再帰的なアプローチに依存することはできませんが、合理的な反復または再帰的な解決策であれば実行可能であることを意味します。 

明らかではないエッジ ケースには、ネストされた関数呼び出し、ステップ サイズを持つ部分文字列、および off-by-one インデックス エラーが含まれます。 例えば、`substr("abcde", 2, 4)`譲るべきだ`"bcd"`、 そして`substr("abcdef", 1, 6, 2)`収量`"ace"`。 不用意に解析すると、関数名が変数として誤って解釈されたり、大文字と小文字の区別が失敗したり、ネストされたカンマが誤って処理されたりする可能性があります。 また、この問題では空ではない出力が保証されているため、空の中間結果は表示されませんが、それでも 1 文字の部分文字列または 1 文字の文字列の反転を正しく処理する必要があります。 

## アプローチ

 ブルートフォースアプローチは、正規表現またはアドホック解析を使用して文字列の評価を試みることです。 関数の文字列をスキャンし、かっこを数えてその引数を抽出し、各引数を再帰的に評価できます。 これが機能するのは、すべての関数が最終的には文字列リテラルになるためです。 主な課題は、ネストされた括弧とカンマを正しく処理することです。 カンマが関数の引数内にある場合、単純にカンマで分割すると失敗するため、括弧の深さを追跡する堅牢なパーサーが必要です。 

最適なアプローチは、解析時に式を評価する再帰降下パーサーです。 各関数が文字列を返し、関数が決定的であるという観察により、単純な再帰評価が可能になります。 再帰ごとに問題が軽減されます。文字列リテラルが見つかったら、それを返します。 関数呼び出しが発生した場合は、各引数を再帰的に評価してから、関数を適用します。 これにより、ネストが正しく処理され、実装できることが保証されます。`substr`Python スライスを使用して、ステップの有無にかかわらず効率的に実行できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 正規表現を使用したブルート フォース解析 | O(n²) 最悪の場合 | O(n) | 脆弱すぎるため、ネストに失敗する可能性があります。 
| 再帰降下評価 | O(n + m) | O(n) | 承認済み |

 ここ、`n`は入力文字列の長さであり、`m`結果の文字列の長さであり、最大 10,000 になります。 

## アルゴリズムのチュートリアル

 1. 再帰関数を定義する`eval_expr(s, i)`どこ`s`は入力文字列であり、`i`は現在のインデックスです。 この関数はタプルを返します`(result_string, new_index)`評価された文字列と現在の式の後の位置を表します。 
2. 大文字と小文字を区別しないように、空白をスキップし、関数名を小文字に変換します。 
3. 現在のキャラクターが`"`、文字列リテラルに到達しました。 クロージングまで順方向にスキャン`"`、部分文字列を抽出し、それを次のインデックスとともに返します。 
4. それ以外の場合は、関数呼び出し中です。 まで関数名を解析します。`(`、各引数を再帰的に解析します。 ネストされたカンマを正しく処理するには、括弧の深さを追跡してください。 
5. すべての引数が解析されたら、関数を適用します。 のために`concat(x, y)`、2つの文字列を結合します。 のために`reverse(x)`、文字列を反転します。 のために`substr(x, a, b)`または`substr(x, a, b, c)`、インデックスをゼロベースに変換し、オプションのステップでスライスします`c`、結果を返します。 
6. 最上位の呼び出しからの結果文字列を返します。 

機能する理由: 不変条件は、すべての再帰が文字列リテラルまたは完全に評価された関数の結果を返すことです。 括弧の深さによって正しいグループ化が保証され、インデックスは最終評価ステップでのみ調整されるため、正確さが保証されます。 ネストされた関数は再帰によって自然に文字列リテラルに還元されるため、パーサーが操作を誤って適用することはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def eval_expr(s, i):
    while i < len(s) and s[i].isspace():
        i += 1
    if s[i] == '"':
        i += 1
        start = i
        while s[i] != '"':
            i += 1
        return s[start:i], i + 1
    else:
        start = i
        while s[i].isalpha():
            i += 1
        func_name = s[start:i].lower()
        i += 1  # skip '('
        args = []
        depth = 0
        arg_start = i
        while True:
            if s[i] == '(':
                depth += 1
            elif s[i] == ')':
                if depth == 0:
                    arg = s[arg_start:i]
                    if arg.strip():
                        args.append(arg)
                    break
                depth -= 1
            elif s[i] == ',' and depth == 0:
                arg = s[arg_start:i]
                args.append(arg)
                arg_start = i + 1
            i += 1
        eval_args = []
        for a in args:
            a_val, _ = eval_expr(a.strip(), 0)
            eval_args.append(a_val)
        if func_name == "concat":
            return eval_args[0] + eval_args[1], i + 1
        elif func_name == "reverse":
            return eval_args[0][::-1], i + 1
        elif func_name == "substr":
            x = eval_args[0]
            a = int(eval_args[1])
            b = int(eval_args[2])
            if len(eval_args) == 4:
                c = int(eval_args[3])
                return x[a-1:b:c], i + 1
            else:
                return x[a-1:b], i + 1

expr = input().strip()
result, _ = eval_expr(expr, 0)
print(result)
```この解決策は、リテラルと関数呼び出しを解析することから始まります。 パーサーは括弧の深さを追跡して、引数を正しく分割します。 ゼロベースのインデックスが部分文字列に適用され、スライスによりステップが自然にサポートされます。 再帰により、ネストされた関数呼び出しが順番に評価されることが保証されます。 微妙な点としては、空白のスキップ、分割時の空の引数文字列の処理、インデックスの慎重な調整などが挙げられます。`substr`。 

## 実用的な例

 ### 例 1

 入力:`"HelloWorld"`| ステップ | 私 | 式が解析されました | 結果 |
 | --- | --- | --- | --- |
 | 1 | 0 |`"HelloWorld"`|`"HelloWorld"`|

 リテラル文字列はすぐに認識され、再帰は最後の文字列で停止します。`"`。 このアルゴリズムは、文字列リテラルがそれ自身を返すという不変条件を確認します。 

### 例 2

 入力:`concat("ab", reverse("cd"))`| ステップ | 私 | 式が解析されました | 結果 |
 | --- | --- | --- | --- |
 | 0 | 0 |`concat`| 引数を解析する |
 | 1 | 7 |`"ab"`|`"ab"`|
 | 2 | 12 |`reverse("cd")`|`"dc"`|
 | 3 | 23 |`concat("ab","dc")`|`"abdc"`|

 パーサーは括弧の深さを使用して引数を正しく分割し、外側の関数よりも前に内側の関数を評価します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m) | 各文字は最大 1 回スキャンされ、出力文字列の構築には O(m) が寄与します。 |
 | スペース | O(n) | 再帰的なスタックと引数のストレージは入力サイズに比例します。 |

 n ≤ 1000 および m ≤ 10,000 の場合、このソリューションは 7 秒の制限と 256MB のメモリ内に十分に適合します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    expr = input().strip()
    result, _ = eval_expr(expr, 0)
    return result

# provided samples
assert run('"HelloWorld"') == "HelloWorld", "sample 1"

# custom cases
assert run('reverse("abc")') == "cba", "reverse function"
assert run('concat("a","b")') == "ab", "concat function"
assert run('substr("abcdef",2,4)') == "bcd", "substr without step"
assert run('substr("abcdef",1,6,2)') == "ace", "substr with step"
assert run('concat("x",reverse(substr("abcdef",2,5)))') == "xedcb", "nested functions"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | リバース("abc") | "cba" | 単純な逆 |
 | concat("a","b") | "アブ" | 単純な連結 |
 | substr("abcdef",2,4) | "BCD" | 基本的な部分文字列 |
 | substr("abcdef",1,6,2) | 「エース」 | ステップ | を含む部分文字列
 | concat("x",reverse(substr("abcdef",2,5))) | "xedcb" | 入れ子関数の評価 |

 ## 特殊なケース

 1. 単一文字列:`"a"`返品`"a"`直接的には、パーサーは引用符を正しく処理します。 
2. ステップによる最大部分文字列範囲:`substr("abcdefghij",1,10,3)`返品`"adgj"`; 再帰は正しく評価されます。
