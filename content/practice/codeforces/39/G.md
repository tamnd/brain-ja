---
title: "CF 39G - 逆関数"
description: "厳しく制限された C++ のサブセットで書かれた小さな再帰関数 f(n) のソース コードが与えられます。 この関数には、シーケンシャル if (...) return ...; のみが含まれます。 ステートメントと無条件の戻り ...; 発言。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 39
codeforces_index: "G"
codeforces_contest_name: "School Team Contest 1 (Winter Computer School 2010/11)"
rating: 2400
weight: 39
solve_time_s: 122
verified: true
draft: false
---
[CF 39G - 逆関数](https://codeforces.com/problemset/problem/39/G)

 **評価:** 2400
 **タグ:** 実装
 **解決時間:** 2 分 2 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 小さな再帰関数のソースコードが与えられました。`f(n)`厳しく制限された C++ のサブセットで書かれています。 関数にはシーケンシャルのみが含まれます`if (...) return ...;`ステートメントと無条件`return ...;`発言。 式には、算術演算、比較、および再帰呼び出しが含まれる場合があります。`f`。 

機械演算は異常です。 加算、減算、乗算はモジュロで実行されます。`32768`、一方、除算は通常の整数の除算です。 すべての有効な入力`n`にある`[0, 32767]`、すべての関数の結果もその範囲内にあります。 

タスクは通常とは逆の方向に進みます。 私たちには価値が与えられている`y = f(n)`そして関数定義自体。 最大のものを回収しなければなりません`n`そのような`f(n) = y`。 そうでない場合`n`存在する場合は印刷します`-1`。 

このステートメントの最も恐ろしい部分は再帰構文ですが、実際の入力サイズによって問題の性質は完全に変わります。 プログラムテキスト全体は最大 100 バイトです。 これは、解析された構文ツリーが小さいことを意味します。 ドメインサイズも正確に固定されています`32768`可能な入力。 可能なすべての関数を個別に評価することも可能`n`各評価が効率的であれば、現実的です。 

再帰的な制限は重要な構造的保証です。 評価するたびに`f(N)`、再帰呼び出しでは、より小さい引数のみを使用できます。 これは、依存関係グラフが非循環であることを意味します。 すべての値を安全に計算できます。`0`メモ化または動的プログラミングを使用して上向きに実行します。 

不注意な実装は、いくつかの場所で静かに失敗する可能性があります。 

よくある間違いの 1 つは、乗算も剰余であることを忘れていることです。`32768`。 

例えば：```
return 200 * 200;
```数学的な結果は次のとおりです`40000`ですが、関数の値は次のとおりです。```
40000 mod 32768 = 7232
```通常の整数乗算を使用すると、完全に間違った答えが生成されます。 

もう 1 つの微妙な問題は、除算の評価順序です。 

考慮する：```
return 7 / 2 * 3;
```操作は、同じ優先レベル内で左から右に評価されます。 結果は次のとおりです。```
(7 / 2) * 3 = 3 * 3 = 9
```ない：```
7 / (2 * 3) = 1
```結合性を無視するパーサーは多くの式を誤って評価します。 

メモ化が省略されている場合、再帰呼び出しでもトラップが作成されます。 

例：```
if (n == 0) return 1;
return f(n - 1) + 1;
```キャッシュなしですべてを評価する`32768`状態は再帰的に二次になります。 メモ化では、各状態が 1 回計算されます。 

最後の微妙な点は、回答要件です。 有効な最大値が必要です`n`、最初に遭遇したものではありません。 

例：```
if (n < 100) return 17;
return 17;
```すべての入力は次のようにマッピングされます`17`、したがって、答えは次のとおりです`32767`。 最初の一致で停止すると、誤って返される`0`。 

## アプローチ

 直接的な総当たりのアイデアは単純です。 関数を解析してから、すべての`n`から`0`に`32767`、 評価する`f(n)`そして目標値と比較します。 再帰呼び出しはより小さい引数にのみ行われるため、再帰は自然に終了します。 

ドメインのサイズがわずかであるため、これはすでに実現可能に思えます。`32768`。 問題は再計算の繰り返しです。 関数が次のとおりであるとします。```
return f(n - 1) + 1;
```評価中`f(30000)`毎回、ほぼ全体のプレフィックスを再帰的に再計算します。 The total work becomes roughly:```
1 + 2 + 3 + ... + 32768
```それは約です`5 * 10^8`Python では評価が遅すぎます。 

重要な観察は、再帰的な依存関係グラフが厳密に一方向であるということです。 すべての国家はより小さな国家にのみ依存します。 つまりそれぞれ`f(n)`一度だけ計算すれば永久に再利用できます。 

プログラムを解析して抽象構文ツリーにした後、値を昇順に評価します。`n`。 式が必要なときはいつでも`f(x)`、の値`x`すでに計算されているため、`x < n`。 

構文自体は十分に小さいため、手書きの再帰降下パーサーが理想的です。 文法はすでに優先規則をエンコードしています。```
product before sum
left associativity inside each level
```したがって、文法に従って式を正確に解析し、現在の式に対して評価します。`n`。 

ドメインが小さいため総当たり攻撃は機能しますが、再帰的拡張を繰り返すとコストがかかりすぎます。 メモ化は、再帰を全体的に線形時間の動的プログラムに変換します。`32768`州。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | メモ化を行わないブルートフォース | 最悪の再帰チェーンでの指数関数 | O(再帰の深さ) | 遅すぎる |
 | 最適なメモ化評価 | O(32768 × プログラム サイズ) | O(32768) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. ターゲット値を読み取り、残りの入力を 1 つの文字列に連結します。 

スペースと改行は文法とは無関係なので、1 つのコンパクトな文字列を扱うと解析が簡単になります。 
2. 文法用のトークナイザーと再帰降下パーサーを構築します。 

標準の優先順位階層を使用して式を解析します。`comparison -> sum -> product -> atom`これにより、正しい評価順序が保証されます。`+`、`-`、`*`、 そして`/`。 
3. 関数本体を解析してステートメントのリストを作成します。 

各ステートメントは次のいずれかです。```
return expr;
```または：```
if (cond) return expr;
```4. 式を構文ツリー ノードとして表現します。 

各ノードはそのタイプを保存します。```
number
variable n
binary operator
recursive call
```これにより、さまざまな値に対する評価を迅速に繰り返すことができます。`n`。 
5. 配列を作成する`dp[0..32767]`。`dp[x]`の計算値を保存します`f(x)`。 
6. から昇順に値を評価します。`0`に`32767`。 

評価する場合`f(n)`、ステートメントを順番にスキャンします。 

条件付き返品の場合:

 - 条件を評価する
 - true の場合、式を評価して直ちに返します。 

無条件返品の場合:

 - 評価してすぐに返す

 再帰呼び出しはより小さな引数のみを使用するため、必要なすべての引数が使用されます。`dp[k]`すでに存在します。 
7. 式の評価中:

 - 加算、減算、乗算は剰余として計算されます。`32768`- 減算は正規化されて次のようになります。`[0, 32767]`- 除算は通常の整数除算を使用します
 - 再帰呼び出しは以前に計算されたものを使用します`dp[value]`8. 最大のものを追跡する`n`その計算値はターゲットと等しくなります。 
9. 最大の一致を出力します`n`、 または`-1`一致するものが存在しない場合。 

### なぜ効果があるのか

 この問題により、からの再帰呼び出しが保証されます。`f(N)`より小さい値のみをターゲットにします`N`。 これにより、状態に対するトポロジー的な順序が作成されます。```
0, 1, 2, ...
```計算するとき`dp[n]`、すべての再帰的な依存関係はすでに完了しています。 式の評価は言語文法に正確に従うため、演算子の優先順位と結合性は仕様と一致します。 ステートメントは順次処理され、最初に返されたステートメントで評価が停止するため、シミュレートされた実行は元のプログラムと同じになります。 毎`dp[n]`したがって、真の関数値と正確に等しくなります。`f(n)`。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 32768

class Parser:
    def __init__(self, s):
        self.s = s
        self.i = 0

    def skip(self):
        while self.i < len(self.s) and self.s[self.i].isspace():
            self.i += 1

    def match(self, t):
        self.skip()
        if self.s.startswith(t, self.i):
            self.i += len(t)
            return True
        return False

    def expect(self, t):
        assert self.match(t)

    def parse_number(self):
        self.skip()
        j = self.i
        while self.i < len(self.s) and self.s[self.i].isdigit():
            self.i += 1
        return ("num", int(self.s[j:self.i]))

    def parse_atom(self):
        self.skip()

        if self.match("n"):
            return ("n",)

        if self.match("f"):
            self.expect("(")
            expr = self.parse_sum()
            self.expect(")")
            return ("call", expr)

        if self.s[self.i].isdigit():
            return self.parse_number()

        if self.match("("):
            expr = self.parse_sum()
            self.expect(")")
            return expr

    def parse_product(self):
        node = self.parse_atom()

        while True:
            self.skip()

            if self.match("*"):
                node = ("bin", "*", node, self.parse_atom())
            elif self.match("/"):
                node = ("bin", "/", node, self.parse_atom())
            else:
                break

        return node

    def parse_sum(self):
        node = self.parse_product()

        while True:
            self.skip()

            if self.match("+"):
                node = ("bin", "+", node, self.parse_product())
            elif self.match("-"):
                node = ("bin", "-", node, self.parse_product())
            else:
                break

        return node

    def parse_condition(self):
        left = self.parse_sum()

        self.skip()

        if self.match("=="):
            op = "=="
        elif self.match(">"):
            op = ">"
        else:
            self.expect("<")
            op = "<"

        right = self.parse_sum()

        return (op, left, right)

    def parse_statement(self):
        self.skip()

        if self.match("if"):
            self.expect("(")
            cond = self.parse_condition()
            self.expect(")")
            self.expect("return")
            expr = self.parse_sum()
            self.expect(";")
            return ("if", cond, expr)

        self.expect("return")
        expr = self.parse_sum()
        self.expect(";")
        return ("ret", expr)

    def parse_program(self):
        self.expect("int")
        self.expect("f")
        self.expect("(")
        self.expect("int")
        self.expect("n")
        self.expect(")")
        self.expect("{")

        stmts = []

        while True:
            self.skip()
            if self.match("}"):
                break
            stmts.append(self.parse_statement())

        return stmts

def eval_expr(node, n, dp):
    t = node[0]

    if t == "num":
        return node[1]

    if t == "n":
        return n

    if t == "call":
        x = eval_expr(node[1], n, dp)
        return dp[x]

    op = node[1]

    a = eval_expr(node[2], n, dp)
    b = eval_expr(node[3], n, dp)

    if op == "+":
        return (a + b) % MOD

    if op == "-":
        return (a - b) % MOD

    if op == "*":
        return (a * b) % MOD

    return a // b

def eval_cond(cond, n, dp):
    op, left, right = cond

    a = eval_expr(left, n, dp)
    b = eval_expr(right, n, dp)

    if op == "<":
        return a < b

    if op == ">":
        return a > b

    return a == b

def solve():
    target = int(input())

    code = sys.stdin.read()

    parser = Parser(code)
    stmts = parser.parse_program()

    dp = [0] * MOD

    ans = -1

    for n in range(MOD):
        for stmt in stmts:
            if stmt[0] == "if":
                _, cond, expr = stmt

                if eval_cond(cond, n, dp):
                    dp[n] = eval_expr(expr, n, dp)
                    break
            else:
                _, expr = stmt
                dp[n] = eval_expr(expr, n, dp)
                break

        if dp[n] == target:
            ans = n

    print(ans)

solve()
```パーサーは文法を直接反映します。`parse_product`ハンドル`*`そして`/`、 その間`parse_sum`ハンドル`+`そして`-`。 各レベルは演算子を左から右に繰り返し消費するため、結合性は自動的に正しくなります。 

式ツリーは、クラスではなくタプルで表されます。 これにより、評価が軽量に保たれ、数万回の実行に十分な速度が保たれます。 

動的プログラミングの順序は、最も重要な実装の詳細です。 私たちは計算します`dp[0]`、 それから`dp[1]`、 等々。 問題保証では、再帰呼び出しではより小さい引数のみが使用されるため、すべての再帰依存関係がすでに利用可能であると記載されています。 

モジュロ処理も間違いが起こりやすい場所です。 パイソンの`%`すでに減算結果が非負に保たれています。```
(a - b) % MOD
```問題定義と正確に一致します。 

ステートメントのループは、実際の実行と同様に、最初に分岐を返した直後に停止します。 を忘れて`break`誤って以降のステートメントのスキャンを続行してしまう可能性があります。 

## 実用的な例

 ### 例 1

 入力:```
17
int f(int n)
{
if (n < 100) return 17;
if (n > 99) return 27;
}
```選択した値のトレース:
 | と | 最初の条件`n < 100`| 戻り値 | ターゲットと一致します |
 | --- | --- | --- | --- |
 | 0 | 本当 | 17 | はい |
 | 50 | 本当 | 17 | はい |
 | 99 | 本当 | 17 | はい |
 | 100 | 偽 | 27 | いいえ |
 | 32767 | 偽 | 27 | いいえ |

 最大の一致する入力は次のとおりです。`99`。 

この例は、最初に満たされた return ステートメントで実行が停止することを示しています。 2 番目の条件は決してチェックされません`n < 100`。 

### 例 2

 入力:```
1
int f(int n)
{
if(n==0)return 1;
return f(n-1)+1;
}
```最初のいくつかの状態をトレースします。 

| n | 再帰的な依存関係 | 計算値 |
 | --- | --- | --- |
 | 0 | なし | 1 |
 | 1 | f(0) | 2 |
 | 2 | f(1) | 3 |
 | 3 | f(2) | 4 |

 目標値`1`にのみ表示されます`n = 0`。 

このトレースは、昇順 DP が機能する理由を裏付けています。 すべての再帰的な依存関係は、現在の状態が評価される前にすでに存在します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(32768×S) |`S`解析されたプログラムの小さなサイズ |
 | スペース | O(32768) | すべての関数値の DP 配列 |

 ソースコードの長さは最大でも 100 バイトであるため、構文ツリーは非常に小さくなります。 からのすべての入力を評価する`0`に`32767`5秒以内であれば完全に安全です。 メモリ使用量は DP 配列のサイズによって支配されます。`32768`。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

MOD = 32768

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()

    input = sys.stdin.readline

    class Parser:
        def __init__(self, s):
            self.s = s
            self.i = 0

        def skip(self):
            while self.i < len(self.s) and self.s[self.i].isspace():
                self.i += 1

        def match(self, t):
            self.skip()
            if self.s.startswith(t, self.i):
                self.i += len(t)
                return True
            return False

        def expect(self, t):
            assert self.match(t)

        def parse_number(self):
            self.skip()
            j = self.i
            while self.i < len(self.s) and self.s[self.i].isdigit():
                self.i += 1
            return ("num", int(self.s[j:self.i]))

        def parse_atom(self):
            self.skip()

            if self.match("n"):
                return ("n",)

            if self.match("f"):
                self.expect("(")
                expr = self.parse_sum()
                self.expect(")")
                return ("call", expr)

            if self.s[self.i].isdigit():
                return self.parse_number()

            self.expect("(")
            expr = self.parse_sum()
            self.expect(")")
            return expr

        def parse_product(self):
            node = self.parse_atom()

            while True:
                self.skip()

                if self.match("*"):
                    node = ("bin", "*", node, self.parse_atom())
                elif self.match("/"):
                    node = ("bin", "/", node, self.parse_atom())
                else:
                    break

            return node

        def parse_sum(self):
            node = self.parse_product()

            while True:
                self.skip()

                if self.match("+"):
                    node = ("bin", "+", node, self.parse_product())
                elif self.match("-"):
                    node = ("bin", "-", node, self.parse_product())
                else:
                    break

            return node

        def parse_condition(self):
            left = self.parse_sum()

            if self.match("=="):
                op = "=="
            elif self.match(">"):
                op = ">"
            else:
                self.expect("<")
                op = "<"

            right = self.parse_sum()

            return (op, left, right)

        def parse_statement(self):
            if self.match("if"):
                self.expect("(")
                cond = self.parse_condition()
                self.expect(")")
                self.expect("return")
                expr = self.parse_sum()
                self.expect(";")
                return ("if", cond, expr)

            self.expect("return")
            expr = self.parse_sum()
            self.expect(";")
            return ("ret", expr)

        def parse_program(self):
            self.expect("int")
            self.expect("f")
            self.expect("(")
            self.expect("int")
            self.expect("n")
            self.expect(")")
            self.expect("{")

            stmts = []

            while True:
                self.skip()
                if self.match("}"):
                    break
                stmts.append(self.parse_statement())

            return stmts

    def eval_expr(node, n, dp):
        t = node[0]

        if t == "num":
            return node[1]

        if t == "n":
            return n

        if t == "call":
            return dp[eval_expr(node[1], n, dp)]

        op = node[1]
        a = eval_expr(node[2], n, dp)
        b = eval_expr(node[3], n, dp)

        if op == "+":
            return (a + b) % MOD
        if op == "-":
            return (a - b) % MOD
        if op == "*":
            return (a * b) % MOD

        return a // b

    def eval_cond(cond, n, dp):
        op, l, r = cond
        a = eval_expr(l, n, dp)
        b = eval_expr(r, n, dp)

        if op == "<":
            return a < b
        if op == ">":
            return a > b
        return a == b

    target = int(input())
    code = sys.stdin.read()

    parser = Parser(code)
    stmts = parser.parse_program()

    dp = [0] * MOD
    ans = -1

    for n in range(MOD):
        for stmt in stmts:
            if stmt[0] == "if":
                _, cond, expr = stmt
                if eval_cond(cond, n, dp):
                    dp[n] = eval_expr(expr, n, dp)
                    break
            else:
                _, expr = stmt
                dp[n] = eval_expr(expr, n, dp)
                break

        if dp[n] == target:
            ans = n

    return str(ans)

# provided sample
assert run("""17
int f(int n)
{
if (n < 100) return 17;
if (n > 99) return 27;
}
""") == "99"

# constant function
assert run("""5
int f(int n){return 5;}
""") == "32767"

# modulo multiplication
assert run("""7232
int f(int n){return 200*200;}
""") == "32767"

# recursive chain
assert run("""4
int f(int n){
if(n==0)return 1;
return f(n-1)+1;
}
""") == "3"

# impossible target
assert run("""123
int f(int n){return 0;}
""") == "-1"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 5 を返す定数関数 | 32767 | 最大の有効な入力を返す必要があります |
 |`200*200`| 32767 | 正しいモジュロ乗算 |
 | 再帰的な増分チェーン | 3 | 再帰的 DP 評価 |
 | ターゲット 123 の定数ゼロ関数 | -1 | 回答漏れの適切な処理 |

 ## 特殊なケース

 モジュロ算術の場合を考えてみましょう。```
7232
int f(int n){return 200*200;}
```アルゴリズムは以下を評価します。```
200 * 200 = 40000
40000 mod 32768 = 7232
```すべての入力は次のようにマッピングされます`7232`したがって、最大の答えは次のようになります。`32767`。 乗算は剰余剰余なので、`32768`、実装は問題の算術モデルと正確に一致します。 

次に、再帰的な依存関係について考えてみましょう。```
3
int f(int n){
if(n==0)return 1;
return f(n-1)+1;
}
```DP 配列は次の順序で入力されます。```
dp[0] = 1
dp[1] = 2
dp[2] = 3
```計算するとき`dp[2]`、再帰呼び出し`f(1)`ただ読むだけ`dp[1]`、すでに決定されています。 繰り返しの再帰は発生しません。 

最後に、複数の一致する入力を考慮します。```
17
int f(int n){return 17;}
```毎`n`有効です。 アルゴリズムはすべてをスキャンします`32768`値を更新し、別の一致が現れるたびに答えを継続的に更新します。 最終的に保存された回答は次のようになります。`32767`、これはまさにステートメントが要求しているものです。
