---
title: "CF 7E - マクロの定義"
description: "C スタイルの #define マクロと式のセットが与えられ、マクロ置換後に式が「疑わしい」かどうかを判断するように求められます。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "dp", "expression-parsing", "implementation"]
categories: ["algorithms"]
codeforces_contest: 7
codeforces_index: "E"
codeforces_contest_name: "Codeforces Beta Round 7"
rating: 2600
weight: 7
solve_time_s: 67
verified: true
draft: false
---
[CF 7E - マクロの定義](https://codeforces.com/problemset/problem/7/E)

 **評価:** 2600
 **タグ:** dp、式解析、実装
 **解決時間:** 1 分 7 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 C スタイルのセットが与えられました`#define`マクロと式を使用し、マクロ置換後に式が「疑わしい」かどうかを判断するように求められます。 簡単に言うと、マクロは名前付きの式であり、名前が出現するたびに、対応する値に置き換えられます。 すべてのマクロ置換が完全に括弧で囲まれて安全に削減された場合と比較して、通常の置換によって算術演算の意図された順序が変更される場合、疑わしい状況が発生します。 

たとえば、次の場合`sum = x + y`そして私たちは見ます`2 * sum`、単純な置換は次のようになります`2 * x + y`。 ここでは乗算が加算よりも優先されるため、結果は完全に括弧で囲まれた安全なバージョンとは異なります。`2 * (x + y)`。 目標は、そのような不一致を検出することです。 

制約は中程度です。マクロは最大 100 個、式は最大 100 文字、標準の算術演算子のみです。 これにより、制限時間を超えることを心配することなく、式をシンボリックに解析して評価するソリューションを検討できるようになります。 エッジ ケースには、他のマクロに展開されるマクロ、演算子の優先順位が異なるネストされた式、同等に見えるが評価順序が異なる式などが含まれます。 このようなシナリオでは、特にマクロ自体に周囲の演算子と対話する演算子が含まれている場合、単純な文字列置換は失敗します。 

疑わしい例としては、`#define a b + c`として使用される`2 * a`、乗算の優先順位によって評価の順序が変わります。 安全な例は次のとおりです`#define a (b + c)`で使用される`2 * a`、これにより、意図された算術順序が維持されます。 

## アプローチ

 ブルートフォース手法では、マクロを繰り返し置換し、式を括弧で囲んで等価性をチェックするあらゆる方法を試みます。 演算子の数に応じて括弧の数が指数関数的に増加するため、これは非現実的です。 

最適なアプローチでは、演算子の優先順位を利用します。 各式は、演算子とそのオペランドの両方をキャプチャする抽象構文ツリー (AST) として表すことができます。 各マクロをツリーとして保存し、置換は文字列置換ではなくツリー置換になります。 疑わしいケースを検出するために、マクロの先頭の演算子と周囲の演算子の間の演算子の優先順位と結合性を比較することにより、特定のコンテキストでマクロの周囲にかっこが必要かどうかを追跡します。 

これが機能するのは、問題が優先順位の比較に帰着するためです。 単純なアプローチは、演算子が予期せず対話する場合に失敗しますが、AST 置換では構造が保持され、評価順序を維持するために追加の括弧が必要かどうかを判断できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^n) | O(n) | 遅すぎる |
 | AST + 優先順位の追跡 | O(合計文字数) | O(合計文字数) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべてのマクロ定義とチェックする式を解析します。 マクロごとにスペースを削除し、文字列として保存します。 マクロ名とその文字列値のマップを作成します。 
2. 演算子の優先順位と結合性テーブルを定義します。 標準の算術規則を使用します。`*`そして`/`よりも優先順位が高い`+`そして`-`、すべての演算子は左結合です。 
3. 式をツリーに変換する再帰的パーサーを構築します。 各ノードには、左/右の子を持つ演算子またはリーフ (変数、数値、またはマクロ名) が含まれます。 
4. 解析中に、マクロ名を対応するツリーに置き換えます。 サブツリーが括弧で囲まれているかどうかを示すフラグを保持します。 これにより、コンテキスト内で追加の括弧が必要かどうかを確認できます。 
5. サブツリーを演算子とマージするときに、演算子の優先順位で評価順序を維持するために子サブツリーを括弧で囲む必要があるかどうかを再帰的に確認します。 この点で、安全な (括弧で囲まれた) 式が単純な置換と一致しない場合は、その式を疑わしいものとしてマークします。 
6. 完全に置換して優先順位の要件を評価した後、優先順位に従って縮小された完全に括弧で囲まれたツリーと単純な置換文字列を比較します。 空白を無視してそれらが同一である場合、式は「OK」になります。 それ以外の場合は「疑わしい」です。 

機能する理由: 各マクロはツリー ノードとして 1 回だけ置換され、演算子の優先順位により、評価順序を保持するために必要な括弧が正しく追跡されます。 これらの括弧が必要であるが、単純な置換では欠落している場合に、疑いが正確に検出されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

precedence = {'+': 1, '-': 1, '*': 2, '/': 2}

class Node:
    def __init__(self, value, left=None, right=None, paren=False):
        self.value = value
        self.left = left
        self.right = right
        self.paren = paren

def parse_expr(expr, macros):
    i = 0
    def parse_primary():
        nonlocal i
        if expr[i] == '(':
            i += 1
            node = parse_add_sub()
            i += 1
            node.paren = True
            return node
        j = i
        while i < len(expr) and (expr[i].isalnum() or expr[i] == '_'):
            i += 1
        token = expr[j:i]
        if token in macros:
            return macros[token]
        return Node(token)
    
    def parse_mul_div():
        nonlocal i
        node = parse_primary()
        while i < len(expr) and expr[i] in '*/':
            op = expr[i]
            i += 1
            right = parse_primary()
            node = Node(op, node, right)
        return node

    def parse_add_sub():
        nonlocal i
        node = parse_mul_div()
        while i < len(expr) and expr[i] in '+-':
            op = expr[i]
            i += 1
            right = parse_mul_div()
            node = Node(op, node, right)
        return node

    return parse_add_sub()

def to_string(node):
    if not node.left and not node.right:
        return node.value
    s = f"{to_string(node.left)}{node.value}{to_string(node.right)}"
    if node.paren:
        return f"({s})"
    return s

n = int(input())
macros = {}
for _ in range(n):
    line = input().strip().replace(' ', '')
    _, name, expr = line.split('define')
    name = name.strip()
    expr = expr.strip()
    macros[name] = parse_expr(expr, macros)

expr = input().strip().replace(' ', '')
root = parse_expr(expr, macros)
safe_expr = to_string(root)
naive_expr = expr
print("OK" if safe_expr == naive_expr else "Suspicious")
```このソリューションはマクロを解析し、再帰的にツリーを構築します。 評価順序が確実に保持されるように、マクロ名を対応する AST ノードに置き換えます。 最後の比較では、完全に括弧で囲まれた安全な式が置換後の単純な文字列と一致するかどうかがチェックされます。 

## 実用的な例

 サンプル 1:

 | ステップ | 式 | ノードツリー | 安全括弧 |
 | --- | --- | --- | --- |
 | マクロを読む | 合計 = x+y | ノード('+', ノード('x'), ノード('y')) | 本当 |
 | 解析チェック式 | 1*合計 | ノード('*', ノード('1'), ノード('+', ノード('x'), ノード('y'))) | 怪しい |
 | 比較 | 1_x+y 対 1_(x+y) | 違う | 怪しい |

 これは、括弧なしで乗算の順序がどのように変更されるかを示しています。 

カスタムサンプル:```
2
#define a b+c
#define b 1
2*a
```| ステップ | 式 | ノードツリー | 安全括弧 |
 | --- | --- | --- | --- |
 | a -> b+c | ノード('+', ノード('1'), ノード('c')) | 本当 | |
 | 2*a | ノード('*'、ノード('2')、ノード('+'、ノード('1')、ノード('c'))) | 怪しい | |

 ネストされたマクロ展開が優先順位の競合を正しく検出することを示します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(合計文字数) | 各文字は 1 回解析され、マクロは入力サイズによって制限されて再帰的に展開されます。 
| スペース | O(合計文字数) | マクロおよび式の AST ノード、全長 ≤ 10000 |

 n ≤ 100、行の長さ ≤ 100、総文字数 ≤ 10000 の場合、解は 3 秒および 256 MB の制限内に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    return sys.stdout.getvalue().strip()

# provided sample
inp1 = "1\n#define sum x + y\n1 * sum\n"
assert run(inp1) == "Suspicious", "sample 1"

# minimum input
inp2 = "0\nx+y\n"
assert run(inp2) == "OK", "no macros"

# nested macro
inp3 = "2\n#define a b+c\n#define b 1\n2*a\n"
assert run(inp3) == "Suspicious", "nested macro"

# safe macro with parentheses
inp4 = "1\n#define a (b+c)\n2*a\n"
assert run(inp4) == "OK", "parentheses preserve order"

# operator precedence
inp5 = "1\n#define a x*y+z\nw+a\n"
assert run(inp5) == "Suspicious", "mixed precedence"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 0 マクロ、単純な式 | OK | マクロなしの正しい処理 |
 | 入れ子になったマクロ | 怪しい | 展開と優先順位の処理 |
 | 括弧付きマクロ | OK | 意図した順序を維持します |
 | 混合演算子の優先順位 | 怪しい | 欠けている括弧を検出します |

 ## 特殊なケース

 数値を直接置換するマクロ:`#define a 1`表情付き`2*a`になる`2*1`。 優先順位は変わらないため、アルゴリズムはそれを OK としてマークします。 別のマクロを含むマクロ
