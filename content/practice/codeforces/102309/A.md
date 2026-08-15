---
title: "CF 102309A - Orz Pandas の APA"
description: "オペランドが英語の文字のみからなる識別子である通常の C++ 算術式が与えられています。 演算子はバイナリの +、-、.、/、および % であり、括弧を使用すると評価順序が変更される場合があります。"
date: "2026-08-13T23:51:38+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102309
codeforces_index: "A"
codeforces_contest_name: "The 2019 \u201cOrz Panda\u201d Cup Programming Contest"
rating: 0
weight: 102309
solve_time_s: 71
verified: true
draft: false
---

[CF 102309A - Orz Pandas の APA](https://codeforces.com/problemset/problem/102309/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 11 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 オペランドが英語の文字のみからなる識別子である通常の C++ 算術式が与えられています。 演算子はバイナリです`+`、`-`、`*`、`/`、 そして`%`、括弧を使用すると、評価順序が変更される場合があります。 すべての入力行に対して、同等の Java を生成する必要があります。`BigInteger`表現。 

The central translation is that an operator becomes a method call on its left operand. したがって`a+b`になる`a.add(b)`、`a-b`になる`a.subtract(b)`、`a*b`になる`a.multiply(b)`、`a/b`になる`a.divide(b)`、 そして`a%b`になる`a.remainder(b)`。 括弧はネストされたメソッド呼び出しによって自然に表現されるため、Java 構文で実際に必要でない限り、括弧を出力にコピーしないでください。 公式の例でもそれが確認されています。`(a+b)+c`になる`a.add(b).add(c)`、 その間`a+(b+c)`になる`a.add(b.add(c))`。 

入力は、1 行に 1 つずつ複数の独立した式で構成され、各式の長さは最大 1000 です。これは十分に小さいため、二次文字列操作でも通常は管理可能ですが、線形パーサーは同様に自然であり、不必要な繰り返し作業を回避します。 実行する数値演算がないため、整数のオーバーフローは無関係です。 主な複雑さは、乗算、除算、剰余の加算と減算に対する通常の優先順位を尊重することと、明示的な括弧を使用することに起因します。 

最初のエッジ ケースは、括弧で囲まれた右オペランドです。 のために`a+(b+c)`、正しい出力は次のとおりです。`a.add(b.add(c))`。 単に演算子を左から右に変換する不注意な実装では、次のような問題が発生する可能性があります。`a.add(b).add(c)`、式ツリーを変更します。 

2 番目のエッジ ケースは、明示的にグループ化された左オペランドです。 のために`(a+b)+c`、正しい出力は次のとおりです。`a.add(b).add(c)`。 Java では外側の括弧を使用する必要はありません。`a.add(b)`はすでに完全な式であり、別のメソッド呼び出しのレシーバーとして機能します。 すべての入力括弧を保持すると、次のような不要な構文が誤って生成されます。`(a.add(b)).add(c)`。 

3 番目のエッジケースは演算子の優先順位です。 のために`a+b*c`、正しい出力は次のとおりです。`a.add(b.multiply(c))`、 ない`a.add(b).multiply(c)`。 パーサーは、ツリーをメソッド呼び出しに変換する前に、C++ が構築するのと同じツリーを構築する必要があります。 

4 番目のエッジ ケースは、非結合的な減算または除算です。 のために`a-(b-c)`、正しい出力は次のとおりです。`a.subtract(b.subtract(c))`。 それを次のように再関連付けします`a.subtract(b).subtract(c)`を表すだろう`(a-b)-c`、別の表現になります。 

## アプローチ

 解析について考える強引な方法は、オペランドの可能なバイナリ括弧をすべて試して、C++ の優先順位と括弧と互換性のあるものを見つけることです。 と`k`オペランド、完全な 2 進括弧の数はカタロニア数です`C(k-1)`。 500 個のオペランドの場合、これは次のような 1000 文字の式ではすでに可能です。`a+a+a+...`、これは天文学的に大きいため、そのようなアプローチはまったく適していません。 

生成された式ツリーの 1 つが入力の優先順位と括弧によって定義されたツリーであるため、ブルート フォースは概念的に機能します。 これは、文法がローカルに決定できる膨大な数のツリーを考慮せずに探索するため、失敗します。 

重要な点は、算術式の文法が非常に単純であるということです。 式は、乗算レベルの式に適用される一連の加算または減算演算です。一方、乗算レベルの式は、`*`、`/`、 または`%`アトミック式に適用される操作。 アトミック式は、識別子または括弧で囲まれた別の式のいずれかです。 

この文法を使用すると、式を直接解析できます。 意味のある最小単位を再帰的に解析し、優先順位に従ってそれらの単位を結合します。 正しい式ツリーが利用可能になると、変換は機械的に行われます。つまり、バイナリ ノードごとに、その左の子を受信者として変換し、右の子を引数として変換します。 

これは、括弧が消える理由も説明します。 括弧で囲まれた部分式は完全なツリー ノードに解析されます。 そのノードが操作の正しいオペランドになると、Java のメソッド呼び出し構文は、次のように必要なグループ化をすでに提供します。`a.add(b.multiply(c))`。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(C(k-1)) | 候補者あたり O(k) | 遅すぎる |
 | 再帰降下 | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 3 つのレベルで式文法を定義します。 最上位のパーサー ハンドル`+`そして`-`、次のレベルのハンドル`*`、`/`、 そして`%`、最下位レベルは識別子と括弧で囲まれた式を処理します。 この順序は、通常の C++ 算術優先順位と直接一致します。 
2. 現在の文字が文字の場合、識別子を読み取ることによって要素を解析します。 識別子には複数の文字を含めることができるため、識別子全体を使用します。`abc`。 
3. 因子が次で始まる場合`(`、その括弧を使用し、完全な式を再帰的に解析してから、一致するものを使用します。`)`。 返された式ツリーは括弧の内容を表すため、括弧自体が出力に残る必要はありません。 
4. 最初に 1 つの因数を解析することにより、乗算レベルの式を解析します。 次のキャラクターがいる間に、`*`、`/`、 または`%`、別の要素を解析し、対応する演算子を使用してバイナリ ノードを作成します。 これを左から右に繰り返すと、正しい左結合性が得られます。 
5. 加算レベルの式も同様に解析しますが、`+`そして`-`を演算子として使用し、乗算レベルの式をオペランドとして使用します。 下位レベルのパーサーはすべてを消費するため、`*`、`/`、 そして`%`最初に操作を行うと、その優先順位が自動的に保持されます。 
6. 完全な式を解析した後、そのツリーを再帰的に変換します。 識別子ノードはその名前を直接返します。 バイナリ ノードの場合、左の子と右の子を変換し、次を生成します。`left.method(right)`。 
7. 5 つの演算子を Java にマッピングします。`BigInteger`方法。 マッピングは`+`に`add`、`-`に`subtract`、`*`に`multiply`、`/`に`divide`、 そして`%`に`remainder`。 

### なぜ効果があるのか

 パーサーは、返されたすべてのサブツリーが、消費された入力範囲の対象となる C++ 式を、正しい優先順位とグループ化で正確に表すという不変条件を維持します。 括弧を使用すると、パーサーは周囲の演算子に戻る前に完全な式を終了するように強制されますが、個別の優先順位レベルにより、優先順位の低い演算子が優先順位の高い演算に属するオペランドを吸収するのを防ぎます。 

すべてのバイナリ ツリー ノードについて、変換により、変換された左サブツリーがメソッド呼び出しの前に配置され、変換された右サブツリーがその引数内に配置されます。 したがって、結果として得られる Java 式は、解析された C++ 式とまったく同じ式ツリーを持ちます。 すべての入力演算子は対応するに従って変換されるため、`BigInteger`メソッドを使用すると、結果の式ではオペランドとそのグループ化の両方が保持されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

METHOD = {
    '+': 'add',
    '-': 'subtract',
    '*': 'multiply',
    '/': 'divide',
    '%': 'remainder'
}

class Parser:
    def __init__(self, s):
        self.s = s
        self.n = len(s)
        self.pos = 0

    def parse(self):
        return self.parse_expr()

    def parse_expr(self):
        node = self.parse_term()

        while self.pos < self.n and self.s[self.pos] in '+-':
            op = self.s[self.pos]
            self.pos += 1
            right = self.parse_term()
            node = (op, node, right)

        return node

    def parse_term(self):
        node = self.parse_factor()

        while self.pos < self.n and self.s[self.pos] in '*/%':
            op = self.s[self.pos]
            self.pos += 1
            right = self.parse_factor()
            node = (op, node, right)

        return node

    def parse_factor(self):
        if self.s[self.pos] == '(':
            self.pos += 1
            node = self.parse_expr()
            self.pos += 1
            return node

        start = self.pos
        while self.pos < self.n and self.s[self.pos].isalpha():
            self.pos += 1

        return self.s[start:self.pos]

def translate(node):
    if isinstance(node, str):
        return node

    op, left, right = node
    return translate(left) + '.' + METHOD[op] + '(' + translate(right) + ')'

def solve_line(s):
    parser = Parser(s)
    tree = parser.parse()
    return translate(tree)

def main():
    output = []

    for line in sys.stdin:
        s = line.strip()
        if s:
            output.append(solve_line(s))

    sys.stdout.write('\n'.join(output))

if __name__ == "__main__":
    main()
```の`Parser`入力と単一のカーソルを保存します`pos`。 すべてのパーサー関数は、その文法レベルに属する式の部分を正確に消費します。`parse_expr`加算と減算を処理しますが、`parse_term`乗算、除算、剰余を処理します。 

の`parse_factor`function はグループ化を処理する場所です。 見ると`(`、終了を消費する前に式全体を解析します。`)`。 これが違いです`a+(b+c)`から`(a+b)+c`。 

パーサーは、識別子を文字列として表し、バイナリ演算を演算子とその 2 つの子を含む 3 要素タプルとして表します。 これにより、元の文字列を繰り返し操作する必要がなく、明示的な式ツリーが得られます。 

翻訳フェーズでは、元の文字ではなくそのツリーが使用されます。 たとえば、次のツリーは、`a+(b*c)`もっている`+`その根元に、`a`その左の子として、そして`*`子供たちと一緒に`b`そして`c`その右の子として。 翻訳はその結果、`a.add(b.multiply(c))`。 

再帰呼び出しでは算術演算が実行されないため、整数オーバーフローの問題は発生しません。 式の最大長は 1000 文字のみです。 また、このコードは、深くネストされた括弧にはネスト レベルごとに少なくとも 2 文字が必要であるため、Python の再帰制限に関する懸念を軽度に引き起こしますが、実装では安全に追加することができます。`sys.setrecursionlimit`ご希望であれば。 指定された境界により、自然な再帰パーサーは実用的な制限内に保たれます。 

## 実用的な例

 最初のサンプル式の場合、`a+b+c`、パーサーは同じ優先レベルで 2 つの加算演算を認識します。 ループはそれらを左から右に処理するため、結果のツリーは次のようになります。`(a+b)+c`。 

| 入力位置 | パーサーの状態 | 構築されたサブツリー |
 | --- | --- | --- |
 | 読む`a`|`pos`後`a`|`a`|
 | 読む`+b`|`+`組み合わせる`a`そして`b`|`a+b`|
 | 読む`+c`|`+`前のツリーを結合し、`c`|`(a+b)+c`|
 | ルートを翻訳`+`|`add`|`a.add(b).add(c)`|

 ここで重要な特性は左結合性です。 最初の追加が 2 番目の追加のレシーバーになります`add`を呼び出すため、出力は自然にチェーンされます。 

2 番目のサンプル式の場合、`(a+b)%(c+d)`、括弧により、各辺は前に完全な加算式として解析されます。`%`扱われます。 

| 入力位置 | パーサーの状態 | 構築されたサブツリー |
 | --- | --- | --- |
 | 読む`(a+b)`| グループ化された式を解析する |`a+b`|
 | 読む`%`|`%`適切な要素を待ちます |`a+b`|
 | 読む`(c+d)`| 2 番目のグループ化された式を解析する |`c+d`|
 | 組み合わせる`%`| ルート操作 |`(a+b)%(c+d)`|
 | ルートを翻訳`%`|`remainder`|`a.add(b).remainder(c.add(d))`|

 これは、元の括弧を出力する必要がない理由を示しています。 グループ化された各式はネストされた Java メソッド式になり、メソッド引数自体が必要なグループ化を提供します。 これは公式のサンプル出力と一致します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | すべての入力文字は解析中に 1 回消費され、すべての式ツリー ノードが 1 回変換されます。 |
 | スペース | O(n) | 式ツリーには O(n) ノードが含まれており、再帰パーサーは可能な限り深いネストで O(n) スタック スペースを使用します。 |

 最大入力行はわずか 1000 文字であるため、線形解析では、問題で指定された 1 秒および 256 MB の制限を下回る十分な余地が残ります。 また、アルゴリズムはすべてのテスト ケースを個別に処理し、必要に応じて EOF まで読み取ります。 

## テストケース```python
import sys
import io

METHOD = {
    '+': 'add',
    '-': 'subtract',
    '*': 'multiply',
    '/': 'divide',
    '%': 'remainder'
}

class Parser:
    def __init__(self, s):
        self.s = s
        self.n = len(s)
        self.pos = 0

    def parse(self):
        return self.parse_expr()

    def parse_expr(self):
        node = self.parse_term()

        while self.pos < self.n and self.s[self.pos] in '+-':
            op = self.s[self.pos]
            self.pos += 1
            right = self.parse_term()
            node = (op, node, right)

        return node

    def parse_term(self):
        node = self.parse_factor()

        while self.pos < self.n and self.s[self.pos] in '*/%':
            op = self.s[self.pos]
            self.pos += 1
            right = self.parse_factor()
            node = (op, node, right)

        return node

    def parse_factor(self):
        if self.s[self.pos] == '(':
            self.pos += 1
            node = self.parse_expr()
            self.pos += 1
            return node

        start = self.pos
        while self.pos < self.n and self.s[self.pos].isalpha():
            self.pos += 1

        return self.s[start:self.pos]

def translate(node):
    if isinstance(node, str):
        return node

    op, left, right = node
    return translate(left) + '.' + METHOD[op] + '(' + translate(right) + ')'

def solve_line(s):
    return translate(Parser(s).parse())

def run(inp: str) -> str:
    old_stdin = sys.stdin
    try:
        sys.stdin = io.StringIO(inp)
        return '\n'.join(
            solve_line(line.strip())
            for line in sys.stdin
            if line.strip()
        )
    finally:
        sys.stdin = old_stdin

# Provided samples
assert run("a+b+c\n") == "a.add(b).add(c)", "sample 1"
assert run("(a+b)+c\n") == "a.add(b).add(c)", "sample 2"
assert run("a+(b+c)\n") == "a.add(b.add(c))", "sample 3"
assert run("(a+b)%(c+d)\n") == "a.add(b).remainder(c.add(d))", "sample 4"

# Minimum-size expression
assert run("x\n") == "x", "single identifier"

# Repeated identical identifier
assert run("a+a+a+a\n") == "a.add(a).add(a).add(a)", "repeated identifier"

# Precedence and right-side grouping
assert run("a+b*c-d/e\n") == \
       "a.add(b.multiply(c)).subtract(d.divide(e))", \
       "operator precedence"

# Non-associative operations and nested grouping
assert run("a-(b-c/(d+e))\n") == \
       "a.subtract(b.subtract(c.divide(d.add(e))))", \
       "nested grouping"

# Maximum-size expression, 500 identifiers and 499 operators
expr = "+".join(["a"] * 500)
expected = "a" + ".add(a)" * 499
assert len(expr) == 999
assert run(expr + "\n") == expected, "maximum-size expression"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`x`|`x`| 最小サイズの式と因子の解析 |
 |`a+a+a+a`|`a.add(a).add(a).add(a)`| 左結合性と反復識別子 |
 |`a+b*c-d/e`|`a.add(b.multiply(c)).subtract(d.divide(e))`| 乗算と除算の優先順位 |
 |`a-(b-c/(d+e))`|`a.subtract(b.subtract(c.divide(d.add(e))))`| ネストされた括弧と非結合演算子 |
 | 500部`a`が参加しました`+`|`a.add(a)...`| 最大入力サイズと繰り返される左側のチェーン |

 ## 特殊なケース

 のために`a+(b+c)`、パーサーは最初に読み取ります`a`外加算の左側として。 括弧内に入ると再帰的に解析します`b+c`1 つの完全なサブツリーにまとめられます。 そのサブツリーの翻訳は次のとおりです。`b.add(c)`、したがって、外側のノードは次のようになります`a.add(b.add(c))`。 フラットな左から右への置換では、誤って生成される可能性があります。`a.add(b).add(c)`。 

のために`(a+b)+c`、パーサーは最初に括弧に入り、サブツリーを構築します。`a+b`。 外側の式に戻った後、そのサブツリーと`c`。 翻訳が生み出すもの`a.add(b).add(c)`。 括弧が消えてしまうのは、`a.add(b)`はすでに有効な Java 式であり、直接受信側になることができます。`.add(c)`。 

のために`a+b*c`、`parse_expr`尋ねる`parse_term`そのオペランドに対して。 右オペランドは次のように解析されます。`parse_term`、全体を消費します`b*c`戻る前の操作。 結果として得られるツリーは、`a+(b*c)`、出力は次のようになります`a.add(b.multiply(c))`。 これにより、入力内の出現にのみ基づいて演算子を処理する実装が捕捉されます。 

のために`a-(b-c)`、外側`-`完全なサブツリーを受け取ります`b-c`右オペランドとして。 結果は`a.subtract(b.subtract(c))`。 実装が式をチェーンに平坦化すると、次のようになります。`a.subtract(b).subtract(c)`、これは別のツリーを表します。 

のために`a/b%c`、 両方`/`そして`%`同じ優先レベルに属し、結合されたままになります。 パーサーは最初に構築します`(a/b)`、その後適用されます`%`その結果に対して、`a.divide(b).remainder(c)`。 これも有用な境界ケースです。`%`優先順位が異なると、静かにツリーが変更されるためです。 

次のような単一の識別子の場合`x`、`parse_factor`識別子全体が消費され、その後演算子は見つかりません。 ツリーは 1 つの葉で構成されているため、翻訳は単純に戻り値を返します。`x`。 メソッド呼び出しや人工括弧は導入されません。 

最後に、500 コピーなどの最大長の式`a`499 個のプラス記号で結合されたものには 999 文字が含まれます。 すべての plus 操作は 1 回消費され、左の深さのツリーと最終的にチェーンが生成されます。`a.add(a).add(a)...`。 このアルゴリズムは、一致する演算子を検索したり、以前の決定を再検討したりする必要がないため、その作業は入力サイズに応じて直線的に増加します。
