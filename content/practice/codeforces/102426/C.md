---
title: "CF 102426C - LytchenLovesJSON"
description: "タスクは基本的に、小さな JSON インタープリターを実装することです。 入力は、ルートが常にオブジェクトである 1 つの有効な JSON ドキュメントで始まります。 ドキュメントには、ネストされたオブジェクト、配列、文​​字列、数値、ブール値、および null が含まれる場合があります。"
date: "2026-08-14T15:23:47+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102426
codeforces_index: "C"
codeforces_contest_name: "The 7-th BIT Campus Programming Contest for Junior Grade Group"
rating: 0
weight: 102426
solve_time_s: 175
verified: true
draft: false
---

[CF 102426C - LytchenLovesJSON](https://codeforces.com/problemset/problem/102426/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 55 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 タスクは基本的に、小さな JSON インタープリターを実装することです。 

入力は、ルートが常にオブジェクトである 1 つの有効な JSON ドキュメントで始まります。 ドキュメントには、ネストされたオブジェクト、配列、文​​字列、数値、ブール値、および`null`。 その書式設定は任意であるため、空白や改行を使用して個々の値が終了する場所を決定することはできません。 

JSON ドキュメントの後の残りの行はすべてクエリです。 クエリは、オブジェクト グラフ内のパスを記述します。 のような名前`birthday.year`見上げるという意味`birthday`現在のオブジェクト内で、その後`year`結果として得られるオブジェクト内。 次のような接尾辞`[3]`文字列から配列要素または文字を取得することを意味します。 次のように、いくつかのインデックス操作を連鎖させることができます。`a[0][2]`。 

すべてのクエリについて、値に到達し、その値をステートメントで必要な特別な形式で出力するか、発生した最初の種類のエラーを報告します。 オブジェクト属性が欠落していると、`Error: no such attribute`。 文字列または配列以外のものにインデックスを適用すると、`Error: invalid type`。 範囲外の有効な文字列または配列インデックスは、`Error: index overflow`。 

ドキュメントには最大 100 行、最大 100 文字が含まれるため、テキスト サイズは最大約 10,000 文字になります。 クエリは最大 100 個、各クエリは最大 100 文字です。 これらの境界は十分に小さいため、クエリごとにドキュメント全体を再解析しても、最悪の場合でもスキャンできる入力文字は約 100 万文字だけです。 工学的に有用な選択は、やはり一度解析することです。解析されたオブジェクトはすべてのクエリで再利用でき、実装が概念的にクリーンになるからです。 

JSON 文法は、一般的な文字列トークナイザーが必要であることも意味します。 JSON 値は複数の物理行にまたがることがあり、構造トークンの間に空白が表示されることがあります。 に基づくパーサー`split`、個々の行にわたる正規表現、またはインデントに関する仮定は、通知なく失敗する可能性があります。 

文字列のエスケープ シーケンスは問題の観察可能な動作の一部であるため、文字列には特別な処理が必要です。 たとえば、次のことを考えてみましょう。```
{"s":"a\\nb"}
s
s[1]
```文字列には 2 つの文字のバックスラッシュと`n`実際の改行ではなく、その真ん中にあります。 必要な出力`s`は`a\nb`、エスケープシーケンスは保持されます。 Python の通常の JSON デコーダを盲目的に使用するパーサーは、`\n`改行に変換すると、間違った表現が生成されます。 

2 番目の微妙なケースは、エスケープされた引用符です。```
{"s":"a\"b"}
s
s[1]
```値は`a"b`したがって、出力は次のようになります`a"b`そしてインデックス付きの文字は`"`。 引用符を文字列の終わりとして扱うと、解析が破損する可能性があります。 

インデックスは配列ではなく文字列に適用することもできます。```
{"s":"abc"}
s[0]
s[3]
```正しい出力は次のとおりです。```
a
Error: index overflow
```を使用した不用意な実装`if index > len(s)`の代わりに`if index >= len(s)`間違って受け入れてしまうだろう`s[3]`。 

最後に、インデックス付きプリミティブの後に属性検索を実行できます。```
{"a":[10]}
a[0].x
```結果は次のとおりです。```
Error: no such attribute
```後`a[0]`現在の値は数値です`10`、オブジェクト属性はありません。 これは、数値自体にインデックスを適用することとは異なります。`invalid type`エラー。 

## アプローチ

 最も直接的な解決策は、JSON ドキュメントを解析し、各クエリに即座に応答することです。 単純な実装では、クエリごとに JSON ドキュメント全体を個別に解析することもできます。 ドキュメントの文字数は最大 10,000 文字で、クエリは最大 100 個であるため、このアプローチでは最大約 1,000,000 個の文字レベルの解析操作とクエリ処理が実行されます。 これらの制約の下では、実際には十分に高速です。 

このアプローチの弱点は、繰り返し作業が行われることです。 すべてのクエリはまったく同じルート オブジェクトから開始されるため、同じオブジェクト グラフを最大 100 回再構築してもアルゴリズム上の利点はありません。 

有益な観察は、JSON ドキュメントが入力全体を通じて不変であるということです。 オブジェクト、配列、およびプリミティブ値のツリーに変換されると、すべてのクエリは同じツリーをたどるだけになります。 ドキュメントを 1 回だけ解析し、各オブジェクトを辞書として、各配列をリストとして保持し、保存された構造に対して各クエリを解釈できます。 

パーサー自体は再帰降下パーサーです。 JSON には、特に便利な再帰構造があります。オブジェクトにはキーと値のペアが含まれ、配列には値が含まれ、値は再帰的に別のオブジェクトまたは配列になる可能性があります。 各パーサー関数は、共有位置から文字を消費し、解析された値と新しい位置の両方を返します。 

唯一の非標準部分は文字列処理です。 Python の JSON ライブラリに文字列のデコードを依頼する代わりに、パーサーはエスケープを自ら処理します。 逃亡者たち`\t`、`\\`、`\/`、 そして`\"`は対応する文字に変換されますが、他のエスケープ シーケンスは出力仕様で保持する必要があるためリテラルのままです。 これにより、文字列のインデックス付けも、問題で期待される正確な表現が得られます。 

2 つのアプローチは次のように比較できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | すべてのクエリの JSON を再解析する | O(QS + QL) | O(S) | これらの制約の下で受け入れられます |
 | 一度解析して、ツリー上のクエリに応答します。 O(S + QL) | O(S) | 承認済み、優先 |

 ここ`S`は JSON ドキュメントの長さ、`Q`はクエリの数であり、`L`クエリの最大長です。 

## アルゴリズムのチュートリアル

 1. すべての入力行を読み取り、それらを 1 つの文字ストリームに連結します。 JSON ドキュメントには任意の書式設定や改行が含まれている可能性があるため、行を見て JSON ドキュメントがどこで終了するかを判断することはできません。 
2. 再帰降下 JSON パーサーを使用してルート値を解析します。 パーサーは、すべての値の前にある JSON 空白をスキップし、最初の文字に従ってディスパッチします。 あ`{`オブジェクトを開始し、`[`配列を開始し、`"`文字列を開始し、`t`または`f`ブール値を開始し、`n`始まります`null`、符号または数字が数字の始まりになります。 
3. 解析されたすべての値を、その型とデータを含むペアとして表現します。 オブジェクトはキーから値までの辞書を格納し、配列は値のリストを格納し、文字列は処理された文字表現を格納し、数値は浮動小数点値を格納し、プリミティブ ブール値と`null`直接保存されます。 
4. ルート オブジェクトを解析した後、パーサーの現在の文字位置を使用して残りのクエリ行を見つけます。 これは、パーサーがルート値の終了場所を正確に認識しているため、中括弧やインデントを使用して最後の JSON 行を識別しようとするよりも安全です。 
5. 各クエリを次の位置で分割します。`.`属性アクセス セグメントを取得します。 各セグメント内で、最初にアルファベットの属性名を読み取り、次にすべての属性を読み取ります。`[index]`接尾語が付けられています。 
6. すべてのクエリはルート オブジェクトで開始します。 属性名ごとに、現在の値がオブジェクトであるかどうか、および要求されたキーが存在するかどうかを確認します。 いずれかの条件が失敗した場合は、出力します`Error: no such attribute`そしてそのクエリの処理を停止します。 
7. 属性値の取得に成功したら、そのインデックス操作を左から右に処理します。 インデックスは、現在の値が文字列または配列の場合にのみ有効です。 それ以外の場合は印刷します`Error: invalid type`。 
8. 有効な文字列または配列については、要求されたインデックスとその長さを比較します。 インデックスはまさに次の場合に有効です。`0 <= index < length`。 範囲外の場合は印刷する`Error: index overflow`; それ以外の場合は、現在の値を選択した要素に置き換えます。 
9. 完全なクエリが使用されたら、その型に従って結果の値をシリアル化します。 数値は、小数点以下 2 桁の固定小数点表記を使用します。 配列とオブジェクトは再帰的にシリアル化され、オブジェクトのキーはキーと値のペアが出力される前に辞書順に並べ替えられます。 

クエリ評価全体を通して不変なのは、`current`は、これまでに処理されたクエリのプレフィックスが到達した JSON 値とまったく同じです。 属性処理では、現在のオブジェクトの対応する子に変更され、インデックス処理では、対応する要素または文字に変更されます。 各操作は現在の型と境界を確認した後にのみ実行されるため、成功したすべての遷移は JSON オブジェクト グラフの有効なエッジとなります。 操作を実行できない場合、報告されるエラーは最初の無効な操作に正確に対応します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class Parser:
    def __init__(self, text):
        self.s = text
        self.n = len(text)
        self.i = 0

    def skip_ws(self):
        while self.i < self.n and self.s[self.i] in " \t\r\n":
            self.i += 1

    def parse(self):
        self.skip_ws()
        c = self.s[self.i]

        if c == '{':
            return self.parse_object()
        if c == '[':
            return self.parse_array()
        if c == '"':
            return ("string", self.parse_string())
        if c == 't':
            self.i += 4
            return ("bool", True)
        if c == 'f':
            self.i += 5
            return ("bool", False)
        if c == 'n':
            self.i += 4
            return ("null", None)

        return self.parse_number()

    def parse_string(self):
        self.i += 1
        result = []

        while True:
            c = self.s[self.i]
            self.i += 1

            if c == '"':
                return ''.join(result)

            if c != '\\':
                result.append(c)
                continue

            esc = self.s[self.i]
            self.i += 1

            if esc == 't':
                result.append('\t')
            elif esc == '\\':
                result.append('\\')
            elif esc == '/':
                result.append('/')
            elif esc == '"':
                result.append('"')
            else:
                # The statement requires other escape sequences
                # to be kept as written.
                result.append('\\')
                result.append(esc)

    def parse_number(self):
        start = self.i

        if self.s[self.i] == '-':
            self.i += 1

        while self.i < self.n and self.s[self.i].isdigit():
            self.i += 1

        if self.i < self.n and self.s[self.i] == '.':
            self.i += 1
            while self.i < self.n and self.s[self.i].isdigit():
                self.i += 1

        if self.i < self.n and self.s[self.i] in 'eE':
            self.i += 1
            if self.s[self.i] in '+-':
                self.i += 1
            while self.i < self.n and self.s[self.i].isdigit():
                self.i += 1

        return ("number", float(self.s[start:self.i]))

    def parse_object(self):
        self.i += 1
        obj = {}
        self.skip_ws()

        if self.s[self.i] == '}':
            self.i += 1
            return ("object", obj)

        while True:
            self.skip_ws()
            key = self.parse_string()

            self.skip_ws()
            self.i += 1  # ':'

            value = self.parse()
            obj[key] = value

            self.skip_ws()
            if self.s[self.i] == '}':
                self.i += 1
                return ("object", obj)

            self.i += 1  # ','

    def parse_array(self):
        self.i += 1
        arr = []
        self.skip_ws()

        if self.s[self.i] == ']':
            self.i += 1
            return ("array", arr)

        while True:
            arr.append(self.parse())
            self.skip_ws()

            if self.s[self.i] == ']':
                self.i += 1
                return ("array", arr)

            self.i += 1  # ','

def format_value(value):
    typ, data = value

    if typ == "bool":
        return "true" if data else "false"

    if typ == "number":
        return f"{data:.2f}"

    if typ == "string":
        return data

    if typ == "null":
        return "null"

    if typ == "array":
        return "[ " + ", ".join(format_value(x) for x in data) + " ]"

    # object
    items = []
    for key in sorted(data):
        items.append(f'"{key}": {format_value(data[key])}')
    return "{ " + ", ".join(items) + " }"

def answer_query(root, query):
    current = root

    for part in query.split('.'):
        p = 0

        while p < len(part) and part[p].isalpha():
            p += 1

        key = part[:p]

        if current[0] != "object" or key not in current[1]:
            return "Error: no such attribute"

        current = current[1][key]

        while p < len(part):
            # part[p] must be '[' because the input is guaranteed valid.
            p += 1
            start = p

            while p < len(part) and part[p].isdigit():
                p += 1

            index = int(part[start:p])
            p += 1  # ']'

            if current[0] not in ("string", "array"):
                return "Error: invalid type"

            if index >= len(current[1]):
                return "Error: index overflow"

            if current[0] == "string":
                current = ("string", current[1][index])
            else:
                current = current[1][index]

    return format_value(current)

def main():
    lines = []
    while True:
        line = input()
        if not line:
            break
        lines.append(line)

    text = ''.join(lines)

    parser = Parser(text)
    root = parser.parse()

    # The parser stops exactly after the JSON document.
    rest = text[parser.i:]

    queries = rest.splitlines()
    out = []

    for query in queries:
        query = query.strip()
        if query:
            out.append(answer_query(root, query))

    sys.stdout.write('\n'.join(out))

if __name__ == "__main__":
    main()
```の`Parser`クラスは 1 つのカーソルを維持します。`self.i`、完全な入力ストリームに追加されます。 すべての解析関数は、その値に属する文字を正確に消費します。 再帰呼び出しはネストを自然に処理するものであるため、別のオブジェクトを含む配列を含むオブジェクトには特別な場合の深さロジックは必要ありません。`parse_string`特に注目に値します。 出力ルールが明示的に考慮する 4 つのエスケープ形式は、実際の文字に変換されます。 他のエスケープは先頭のバックスラッシュで保持されます。 特に、`\u25A0`6 文字のシーケンスのまま`\u25A0`、Python の Unicode デコード動作ではなく、必要な出力動作と一致します。`parse_number`オプションの符号、整数部分、小数部分、指数を個別に消費します。 入力には有効な JSON が含まれることが保証されているため、パーサーは不正な数値のケースをすべて検証する必要はありません。 

クエリ エバリュエーターは、括弧を処理する前に属性を意図的にチェックします。 次のようなクエリ`missing[0]`インデックスを操作できる値がないため、無効なインデックス タイプではなく、欠落している属性を報告する必要があります。 

インデックス境界チェックでは次を使用します。`index >= len(current[1])`。 インデックスは非負であることが保証されているため、個別の下限ケースはありません。 Python 自体は負のインデックスを備えているため、制限の少ない入力形式では負のインデックスを明示的に拒否する必要がありますが、ここではすべてのクエリ インデックスがすでに負ではありません。 

シリアライザーは配列とオブジェクトを再帰的にフォーマットします。 入力順序が必ずしも必要な出力順序であるとは限らないため、オブジェクト キーは出力時にソートされます。 数値の書式は次のようになります。`:.2f`、小数点以下ちょうど 2 桁を提供します。 

## 実用的な例

 提供されたステートメントには 1 つの大きなサンプルが含まれています。 プロンプトの抜粋では、最後の部分付近のサンプルの一部が失われたか破損しているようです。`teammates`クエリなので、次のトレースはそのサンプルの明確な部分を使用します。 

問い合わせについては`grades[4][1]`、文書の関連部分は次のとおりです。```
"grades": [90, 80, 88, 100, [55, 80]]
```評価は次のように進められます。 

| ステップ | 操作 | 現在の値 |
 | --- | --- | --- |
 | 1 | ルートから開始 | オブジェクト |
 | 2 | アクセス`grades`|`[90, 80, 88, 100, [55, 80]]`|
 | 3 | 適用する`[4]`|`[55, 80]`|
 | 4 | 適用する`[1]`|`80`|
 | 5 | 形式番号 |`80.00`|

 重要な不変条件がここに表示されます。すべての操作の後、`current`これは、クエリの処理されたプレフィックスによって正確に記述される値です。 2 番目のインデックスは、元のインデックスではなく、最初のインデックスから取得した配列を操作します。`grades`配列。 

2 番目の例として、次のことを考えてみましょう。```
{
"a": {
"z": 1,
"x": [10, 20]
},
"s": "abc"
}
a.x[1]
s[2]
a.x[2]
a.x[0].missing
```最初のクエリは、オブジェクト エッジ、別のオブジェクト エッジ、配列エッジをたどって、最終的に数値に到達します。`20`。 

| ステップ | 操作 | 現在の値 |
 | --- | --- | --- |
 | 1 | ルートから開始 | オブジェクト |
 | 2 | アクセス`a`| オブジェクト |
 | 3 | アクセス`x`|`[10, 20]`|
 | 4 | 適用する`[1]`|`20`|
 | 5 | フォーマット |`20.00`|

 のために`s[2]`、文字列には直接インデックスが付けられ、次の結果が生成されます。`c`。 のために`a.x[2]`、配列の長さは 2 なので、インデックス`2`法定範囲外です`0`を通して`1`、生産`Error: index overflow`。 のために`a.x[0].missing`、インデックスは成功し、現在の値が数値として残ります。`10`; 次の属性検索ではオブジェクト属性が見つからず、`Error: no such attribute`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(S + QL + S log S) | 解析コストは O(S)、クエリ コストは O(QL)、シリアル化中のオブジェクト キーの並べ替えコストは、格納されているオブジェクト全体で最大でも O(S log S) です。 
| スペース | O(S) | 解析された JSON ツリーには、ドキュメントの値、キー、配列、およびネストされたオブジェクトが保存されます。 

ここ`S`最大でも 10,000 文字程度であり、`Q`最大でも 100 であり、`L`繰り返し解析するアプローチでも、スキャンされるドキュメント文字はおよそ 100 万文字だけですが、選択された実装では 1 回解析され、結果のツリーが再利用されます。 メモリ使用量は 128 MB 未満です。 

## テストケース

 以下のテスト ハーネスは、提出されたソリューションが次のように保存されることを前提としています。`solution.py`そして暴露します`main`エントリーポイント。 ヘルパーはそのプログラムを呼び出し、送信に使用されたものと同じパーサー、クエリ エバリュエーター、およびシリアライザーをアサーション テストします。```python
import subprocess
import sys

def run(inp: str) -> str:
    result = subprocess.run(
        [sys.executable, "solution.py"],
        input=inp,
        text=True,
        capture_output=True,
        check=True
    )
    return result.stdout

# Provided sample core
sample1 = r'''{
"name": "Lchen",
"gender": false,
"height": 1.60e+2,
"birthday": {
"year": 2000,
"month": 1,
"day": 1,
"aggregate": [2000, 1, 1]
},
"grades": [90, 80, 88, 100, [55, 80]],
"laboratory": null
}
name
name[0]
name.gender
gender
gender[1]
height
birthday.year
grades[2]
grades[4]
grades[4][1]
laboratory
grades[5]
'''

assert run(sample1) == (
    "Lchen\n"
    "L\n"
    "Error: no such attribute\n"
    "false\n"
    "Error: invalid type\n"
    "160.00\n"
    "2000.00\n"
    "88.00\n"
    "[ 55.00, 80.00 ]\n"
    "80.00\n"
    "null\n"
    "Error: index overflow"
), "provided sample core"

# Custom 1: minimum-size object, missing attribute, invalid index type.
case1 = '''{"a":0}
a
b
a[0]
'''
assert run(case1) == (
    "0.00\n"
    "Error: no such attribute\n"
    "Error: invalid type"
), "minimum object and error types"

# Custom 2: nested arrays and string escape handling.
case2 = r'''{
"a": [[1, 2], []],
"s": "A\\B"
}
a[0][1]
a[1][0]
s[1]
'''
assert run(case2) == (
    "2.00\n"
    "Error: index overflow\n"
    "\\"
), "nested indexing and backslash"

# Custom 3: boundary index, object key sorting, exponent and negative number.
case3 = '''{
"z": 3,
"a": {
"y": 2,
"x": [-12.5e0, 3]
}
}
a
a.x[0]
a.x[2]
z
'''
assert run(case3) == (
    '{ "x": [ -12.50, 3.00 ], "y": 2.00 }\n'
    "-12.50\n"
    "Error: index overflow\n"
    "3.00"
), "sorting, exponent and upper-bound index"

# Custom 4: maximum number of JSON lines and maximum number of queries.
keys = [chr(ord('a') + i) for i in range(26)]
keys += ['a' + chr(ord('a') + i) for i in range(26)]
keys += ['b' + chr(ord('a') + i) for i in range(26)]
keys += ['c' + chr(ord('a') + i) for i in range(20)]

json_lines = ['{']
for i, key in enumerate(keys):
    json_lines.append(f'"{key}": 7' + (',' if i + 1 < len(keys) else ''))
json_lines.append('}')

# Add enough repeated queries to reach the 100-query limit.
queries = [keys[i % len(keys)] for i in range(100)]
max_case = '\n'.join(json_lines + queries) + '\n'

expected = ''.join("7.00\n" for _ in range(100)).rstrip('\n')
assert run(max_case) == expected, "maximum query count and large document"
```カスタム ケースでは、さまざまな故障モードと構造特性が適用されます。 

| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`{"a":0}`と`a`、`b`、`a[0]`|`0.00`、属性がありません、無効なタイプ | 最小サイズのオブジェクトとエラーの優先順位 |
 | ネストされた配列`"A\\B"`|`2.00`、オーバーフロー、`\`| 複数のインデックスと文字列エスケープの処理 |
 | ネストされたオブジェクト`[-12.5e0,3]`| ソートされたオブジェクト、`-12.50`、オーバーフロー、`3.00`| 数値解析、シリアル化、キーソート、上限 |
 | 100 行のドキュメントと 100 のクエリ | 100行`7.00`| 最大クエリ数と大きなドキュメントの処理 |

 ## 特殊なケース

 エスケープ文字列については、次のことを考慮してください。```
{"s":"a\"b"}
s
s[1]
```パーサーは、最初の引用符の後に文字列を入力します。 見ると`\"`、両方の文字を消費し、文字列を終了するのではなくリテラルの引用符を追加します。 保存されている値は、`a"b`したがって、最初のクエリは出力されます。`a"b`そして2枚目のプリント`"`。 次の生の引用符を検索するパーサーは、文字列を終了するのが早すぎます。 

エスケープ シーケンスをテキストのままにする必要がある場合は、次のことを考慮してください。```
{"s":"x\u25A0y"}
s
```パーサーが見る`\u`、デコードする必要がある 4 つのエスケープ形式のいずれでもないことを認識し、バックスラッシュとバックスラッシュの両方を保存します。`u`、その後に残りの数字が通常の文字として続きます。 結果の出力は次のとおりです。`x\u25A0y`。 使用する`json.loads`直接実行すると、代わりに Unicode 黒四角文字が作成され、指定された出力動作に違反します。 

文字列のインデックス作成については、次の点を考慮してください。```
{"s":"abc"}
s[0]
s[2]
s[3]
```最初の 2 つのクエリでは、`a`そして`c`。 3番目は見る`index == len(s)`、それで報告します`Error: index overflow`。 同じ条件が配列にも当てはまります。 法定間隔は半分開いていますが、`[0, length)`。 

無効なインデックス タイプについては、次の点を考慮してください。```
{"x":false,"y":null,"z":{"a":1}}
x[0]
y[0]
z[0]
```3 つのクエリはすべて次の結果を生成します`Error: invalid type`。 ブール値、`null`、オブジェクト値はインデックス付けできません。 パーサーは、辞書やブール値のインデックス作成などの Python 固有の操作を解釈しようとしません。 

インデックスが成功した後に欠落している属性については、次の点を考慮してください。```
{"a":[10]}
a[0].x
```

`a`配列に解決されます。`[0]`番号に解決されます`10`、 そして`.x`次に、その番号の属性を尋ねます。 オブジェクトのみが属性を持つため、正しい結果は次のようになります。`Error: no such attribute`。 実装では、辞書を調べる前に現在の値の型をチェックします。 

ネストされた配列の場合は、次のことを考慮してください。```
{"a":[[[7]]]}
a[0][0][0]
```最初のインデックスは現在の値を外側の配列から中央の配列に変更し、2 番目のインデックスは内側の配列に変更し、3 番目のインデックスは次の値に達します。`7`、次のように出力されます`7.00`。 括弧を順番に処理することで、特殊な場合を除き、任意の数の連鎖インデックスが機能します。 

オブジェクトの書式設定については、次の点を考慮してください。```
{"z":1,"a":2}
a
```出力は次のとおりです。```
{ "a": 2.00, "z": 1.00 }
```入力順序は関係ありません。 シリアライザーは、出力を構築する前に辞書キーをソートします。これは、入力内の JSON オブジェクトの順序が必要な辞書編集順序と一致することが保証されていないため、必要です。
