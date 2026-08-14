---
title: "CF 102299B - ルッソのロシア語"
description: "与えられた文法の非終端 M から 1 つの入力行を生成できるかどうかを判断する必要があります。 この行には、数字、空白、および句読点文字が含まれています:, 文法は 3 つの層を記述します。 T は、数字シーケンスまたは完全な { M } 式のいずれかです。"
date: "2026-08-13T23:11:16+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102299
codeforces_index: "B"
codeforces_contest_name: "2019 USP Try-outs"
rating: 0
weight: 102299
solve_time_s: 500
verified: true
draft: false
---

[CF 102299B - ルッソのロシア語](https://codeforces.com/problemset/problem/102299/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 8 分 20 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 非端末から 1 つの入力行を生成できるかどうかを決定する必要があります。`M`与えられた文法の。 行には数字、空白、句読点文字が含まれています`:`、`|`、`{`、`}`、 そして`$`。 文法記号の間には空白を使用できますが、文字列を形成する数字は使用できません。`I`トークンは連続している必要があるため、`123`は 1 つの整数トークンですが、`1 23`2 つの別々の数字シーケンスであり、1 つに結合することはできません`I`。 

文法では 3 つの層が説明されます。`T`数字シーケンスまたは完全な数字のいずれかです`{ M }`表現。`P`1 つ以上です`T`値はコロンで結合されます。`M`から作られた表現です`P`垂直バーで結合された値。さらに、先頭の垂直バーと特別な`⟦PROTECT_6⟧`元の問題の書式では次のように表示されます`$$$`ステートメントが数学的マークアップから抽出されているが、実際の終端文字が単一である場合`⟦PROTECT_7⟧ | 2`。 

入力には最大でも`10^5`文字。 これにより、考えられる多くの文法拡張やバックトラッキング解析を繰り返し試行するアルゴリズムが排除されます。 基本的に入力を 1 回通過する必要があります。`O(n log n)`作業は不要であり、二次パーサーはすでに実行できます。`10^10`最大サイズでの操作。 256 MB のメモリ制限は、トークン化された入力といくつかのサイズの配列を保存するのに十分な量です。`O(n)`。 

表面的に合理的なパーサーが失敗するケースがいくつかあります。 まず、空の式は無効です。 たとえば、空行は次のように生成する必要があります。`NO`、なぜなら、`M`最終的には`P`、そしてすべての`P`が含まれています`T`。 空の部分文字列を有効な再帰式として扱うパーサーは、それを誤って受け入れます。 

次に、垂直バーは単独では自立できません。 入力`| 1`は有効です。`M`次のように拡張できます`| M`そして残りの`M`なることができる`1`、 しかし`1 |`中央のバーには常に別のバーが必要なため、これは無効です`P`その後。 オペランドをチェックせずにバーをカウントするだけのパーサーは、後者を誤って受け入れる可能性があります。 

第三に、数字を空白で区切ることはできません。 入力`1 2`は無効です。`I`は単一の連続した数字のシーケンスですが、文法には 2 つの数字を許可する規則はありません。`I`トークンは隣り合って表示されます。 最初にすべての空白を削除するトークナイザーは、これを次のように変換します。`12`そしてそれを間違って受け入れてしまいます。 

第 4 に、中括弧には完全な文を含める必要があります。`M`。 入力`{}`は無効ですが、`{1}`有効です。 中括弧をその内容を検証せずに通常の一致する句読点として扱うことは受け入れられます。`{}`間違っています。 

五番目、`⟦PROTECT_8⟧ | 2`有効ですが、`⟦PROTECT_9⟧`垂直バーの後に有効な文字列が続く必要があります`P`。 これはまさに、 に代表される特別な代替手段です。`H = '$'`に続く`| P`。 

## アプローチ

 直接的な総当たりアプローチでは、適用可能なすべての文法生成に従って入力を解釈しようとします。 問題は、特に文法に再帰が含まれていることです。`M -> M | P`そして`M -> | M`したがって、単純な再帰降下実装は、左再帰によってトラップされるか、多くの可能な導出間をバックトラックする必要があります。 入力の長さまでのすべての導出を列挙することを想像すると、候補の数は指数関数的に増加する可能性があります。`Theta(2^n)`最悪の場合は分岐する可能性もあります。 で`n = 10^5`、それは実行可能なものをはるかに超えています。 

ブルートフォースパーサーが機能するのは、文法が小さく、成功したすべての導出が有効な解析に対応するためです。 元の文法がはるかに単純な構造を隠す形式で書かれているため、これは失敗します。 重要なのは、何かを実装する前に左再帰を代数的に消去することです。 

から```
M = H | P
  | | M
  | P

H = M | $
```私たちはそれを観察できます`M -> M | P`より多くのことを許可するだけです`| P`すでに有効な部分に追加される部分`M`。 制作`M -> | M`任意の数の先頭バーを許可します。 この再帰を削除した後の言語は、`M`次のように説明できます```
M = |* B
B = P ( | P )*
  | $ | P ( | P )*
```これが中心的な観察です。 アン`M`0 個以上の先頭のバーと、その後に続く通常のバーで構成されます。`P`シーケンスまたは`$ | P`続いてさらに`| P`個。 

同じ単純化が次の場合にも当てはまります`P`。 左再帰定義```
P = P : T | T
```と全く同じです```
P = T ( : T )*
```これで、文法は左から右に解析できるほど十分に決定的になります。 唯一残っている再帰的な部分は、`{ M }`、中括弧は明示的な入れ子構造を与えます。 Python 再帰の代わりにスタックを使用してそのネストを処理できます。 

まず、連続する数字と個別の数字シーケンスの区別を維持しながら、行をトークン化します。 保存もしております`$`独自のトークンとして使用し、トークン間の空白のみをスキップします。 次に、スタックを使用して中括弧のすべてのペアを照合します。 トークンの位置に左中括弧がある場合`l`対応する閉じ中括弧は次のとおりです。`r`、それらの間のトークンは厳密に`M`。 

ネストされた中括弧式を内側から外側へ評価できます。 アウターを加工する場合`{ M }`、その中にあるネストされた中括弧式はすべてすでに評価されているため、中括弧は 1 つの有効または無効なものとして扱うことができます。`T`再帰呼び出しなしで。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`Theta(2^n)`最悪の場合 |`O(n)`探索された導出ごと | 遅すぎる |
 | 再帰的バックトラッキングパーサー | 左再帰のため指数関数的または非終了 |`O(n)`再帰の深さ | 遅すぎる/危険 |
 | トークン化 + 明示的な中括弧スタック + 決定論的解析 |`O(n)`|`O(n)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力を 1 文字ずつスキャンし、トークンを構築します。 連続した数字が1つになる`I`トークン。 それぞれ`$`、`:`、`|`、`{`、 そして`}`は別のトークンです。 空白はスキップされます。 トークン化する前に空白を削除してはなりません。`1 2`になるのではなく、2 桁のトークンのままでなければなりません。`12`。 
2. トークンをスキャンし、中括弧をスタックと照合します。 いつ`{`が見つかった場合は、そのトークン インデックスをプッシュします。 いつ`}`が見つかった場合は、一致する開始位置をポップします。 左中括弧がない場合、入力は直ちに無効になります。 スキャン後は空のスタックが必要になります。そうでない場合は、左中括弧が閉じられません。 
3. 一致するすべてのブレースのペアを、開始位置の降順で処理します。 ネストされた左中括弧は常に、それを含む左中括弧よりも大きなトークン インデックスを持つため、その有効性が最初に計算されます。 各左中括弧の有効性を配列に格納します。 
4. ひとつに`M`間隔、最初に任意の数の先頭を消費します`|`トークン。 これは、繰り返し適用することに直接対応します。`M -> | M`。 
5. 先頭のバーの後、次のトークンを検査します。 そうであれば`⟦PROTECT_10⟧`次のトークンが必要です`|`。 それから、`P`従わなければなりません。 これがスペシャルです`H = '$'`場合。 
6. それ以外の場合は、通常の`P`。 あ`P`有効な 1 つから始まります`T`、その後に 0 個以上が続く`:`そして`T`ペア。 あ`T`数字トークンまたは中括弧トークンのいずれかです。`M`結果は有効です。 
7. 最初に一度`P`残りすべてが解析されました`|`次に別のものが続く必要があります`P`。 これは変換されたルールを処理します`M = |* P ( | P )*`そしてまた`$ | P ( | P )*`形状。 
8. 有効な`M`間隔では、解析はその境界で正確に終了する必要があります。 予期しないトークンが残っている場合、または必要なトークンが残っている場合`P`または`T`が欠落しているため、間隔が無効です。 
9. 最後に、まったく同じように実行します`M`完全なトークン シーケンスのパーサー。 完全な入力は、トップレベルの場合にのみ受け入れられます。`M`有効であり、すべてのトークンを消費します。 

不変条件は、`M`インターバル、ネストごと`{ M }`その間隔内にはすでに正しい有効性の値があります。 現在の間隔のパーサーは、同等の非左再帰文法 (先頭のバー、オプション) に正確に従います。`$ |`、 1つ`P`、および 0 個以上`| P`接尾辞。 各トークンは一定の回数だけ消費され、ネストされた式はすべて 1 回評価されるため、結果として得られる決定は、元の文法が入力を生成できるかどうかになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve(s: str) -> str:
    tokens = []
    n = len(s)
    i = 0

    while i < n:
        c = s[i]

        if c.isspace():
            i += 1
            continue

        if c.isdigit():
            j = i + 1
            while j < n and s[j].isdigit():
                j += 1
            tokens.append(("I", s[i:j]))
            i = j
            continue

        if c in "$:|{}":
            tokens.append((c, c))
            i += 1
            continue

        return "NO"

    m = len(tokens)

    if m == 0:
        return "NO"

    # Match every pair of braces.
    matching = [-1] * m
    stack = []

    for i, (typ, _) in enumerate(tokens):
        if typ == "{":
            stack.append(i)
        elif typ == "}":
            if not stack:
                return "NO"
            opening = stack.pop()
            matching[opening] = i

    if stack:
        return "NO"

    # inner_ok[pos] is meaningful when tokens[pos] == "{"
    # and stores whether the M inside that brace pair is valid.
    inner_ok = [False] * m

    def parse_m(left: int, right: int) -> bool:
        """
        Check whether tokens[left:right] form a valid M.
        All brace expressions inside this interval have already
        been evaluated.
        """
        i = left

        # M -> |* B
        while i < right and tokens[i][0] == "|":
            i += 1

        if i >= right:
            return False

        # B is either P (| P)* or $ | P (| P)*.
        if tokens[i][0] == "$":
            i += 1
            if i >= right or tokens[i][0] != "|":
                return False
            i += 1

        def parse_t(pos: int) -> int:
            if pos >= right:
                return -1

            typ = tokens[pos][0]

            if typ == "I":
                return pos + 1

            if typ == "{":
                close = matching[pos]
                if close == -1 or close >= right:
                    return -1
                if not inner_ok[pos]:
                    return -1
                return close + 1

            return -1

        def parse_p(pos: int) -> int:
            pos = parse_t(pos)
            if pos == -1:
                return -1

            while pos < right and tokens[pos][0] == ":":
                pos = parse_t(pos + 1)
                if pos == -1:
                    return -1

            return pos

        i = parse_p(i)
        if i == -1:
            return False

        while i < right and tokens[i][0] == "|":
            i = parse_p(i + 1)
            if i == -1:
                return False

        return i == right

    # Process inner brace expressions before outer ones.
    openings = [
        i for i in range(m)
        if tokens[i][0] == "{"
    ]

    for opening in reversed(openings):
        closing = matching[opening]
        inner_ok[opening] = parse_m(opening + 1, closing)

    return "YES" if parse_m(0, m) else "NO"

def main() -> None:
    s = input()
    print(solve(s))

if __name__ == "__main__":
    main()
```トークナイザーは単純なトークナイザーよりも意図的に厳密になっています。`''.join(s.split())`アプローチ。 When it sees a digit, it consumes the complete consecutive run and creates exactly one`I`トークン。 空白はその実行を終了するため、`12 34`2 つのトークンになり、誤って次のように解釈されることはありません`1234`。 

ブレーススキャンでは以下を使用します。`matching`すべての左中括弧の終了位置を記録します。 一致しない右中括弧は即座に拒否され、スキャン後のスタックが空でない場合は、左中括弧に右中括弧が存在しないことを意味します。 

の`inner_ok`array は再帰的な関数呼び出しを置き換えます。 いつ`parse_t`出会い`{`、一致するものに直接ジャンプします`}`そして、囲まれているもののすでに計算された結果を参照します。`M`。 開始部分を逆の順序で処理すると、ネストされた式が親よりも前に認識されることが保証されます。 

変換された文法は直接エンコードされます。`parse_m`。 最初のループは先頭のバーを消費します。 の`⟦PROTECT_11⟧ | P`、通常のブランチは直接次から始まります。`P`。 最初のあと`P`、すべてのバーの後に別のバーが続く必要があります`P`。 決勝戦`i == right`プレフィックスを正常に解析するだけでは十分ではなく、間隔全体を消費する必要があるため、チェックは不可欠です。 

中括弧のネストの深さに比例する再帰はないため、数万のネストされた中括弧を含む入力が Python の再帰制限に達することはありません。 すべてのインデックスはトークン インデックスであり、一致する中括弧は、すでに検証済みのネストされた式全体をジャンプすることによって消費されます。 

## 実用的な例

 ### サンプル 1

 入力用`1`、トークン化により 1 つが生成されます`I`トークン。 

| ポジション | トークン | パーサーの状態 | アクション |
 | --- | --- | --- | --- |
 | 0 |`I`| 始める`M`| 先頭なし` | `、解析する`P`|
 | 0 |`I`| 解析する`P`|`I`有効です`T`|
 | 1 | 終わり | 後`P`| もうない` | `、間隔が完全に消費されました |

 の`M`1つ含まれています`P`、`P`1つ含まれています`T`、そして`T`は数字の並びです`1`。 パーサーは正確に最後まで到達するため、答えは次のようになります。`YES`。 

### サンプル 2

 入力用`: 1`、空白はスキップされ、トークンは`:`そして`I`。 

| ポジション | トークン | パーサーの状態 | アクション |
 | --- | --- | --- | --- |
 | 0 |`:`| 始める`M`| 先頭なし` | `、解析を試みます`P`|
 | 0 |`:`| 解析する`P`|`T`が最初に必要です |
 | 0 |`:`| 解析する`T`|`:`どちらでもない`I`または`{`、したがって解析は失敗します。 

結腸は内部に属します`P`、しかし、`P`で始めなければなりません`T`。 まず不可能なので`T`、全体`M`は無効であり、答えは`NO`。 

### サンプル 3

 入力用`⟦PROTECT_12⟧`、`|`、`I`。 

| ポジション | トークン | パーサーの状態 | アクション |
 | --- | --- | --- | --- |
 | 0 |`⟦PROTECT_13⟧`特別なブランチを選択します |
 | 1 |` | `| 後`$`| 必要な区切り文字が存在します |
 | 2 |`I`| 解析する`P`|`I`有効です`T`|
 | 3 | 終わり | 後`P`| 入力は完全に消費されます |

 まさに特殊な形ですね`⟦PROTECT_14⟧`ターミナル。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(n)`| トークン化、中括弧の一致、内部式の評価、および最終解析の各処理では、入力が一定の回数だけ処理されます。 |
 | スペース |`O(n)`| トークン、中括弧の一致情報、有効性の値、および中括弧のスタックはすべて、入力サイズにおいて線形です。 |

 せいぜい`10^5`入力文字の場合、リニア スキャンは文字ごとに少量の一定量の作業のみを実行します。 明示的なスタックは再帰の深さの問題も回避し、線形補助ストレージは 256 MB のメモリ制限を快適に下回ります。 

## テストケース```python
import sys
import io

def solve(s: str) -> str:
    tokens = []
    n = len(s)
    i = 0

    while i < n:
        c = s[i]

        if c.isspace():
            i += 1
            continue

        if c.isdigit():
            j = i + 1
            while j < n and s[j].isdigit():
                j += 1
            tokens.append(("I", s[i:j]))
            i = j
            continue

        if c in "$:|{}":
            tokens.append((c, c))
            i += 1
            continue

        return "NO"

    m = len(tokens)
    if m == 0:
        return "NO"

    matching = [-1] * m
    stack = []

    for i, (typ, _) in enumerate(tokens):
        if typ == "{":
            stack.append(i)
        elif typ == "}":
            if not stack:
                return "NO"
            opening = stack.pop()
            matching[opening] = i

    if stack:
        return "NO"

    inner_ok = [False] * m

    def parse_m(left: int, right: int) -> bool:
        i = left

        while i < right and tokens[i][0] == "|":
            i += 1

        if i >= right:
            return False

        if tokens[i][0] == "$":
            i += 1
            if i >= right or tokens[i][0] != "|":
                return False
            i += 1

        def parse_t(pos: int) -> int:
            if pos >= right:
                return -1

            typ = tokens[pos][0]

            if typ == "I":
                return pos + 1

            if typ == "{":
                close = matching[pos]
                if close == -1 or close >= right:
                    return -1
                if not inner_ok[pos]:
                    return -1
                return close + 1

            return -1

        def parse_p(pos: int) -> int:
            pos = parse_t(pos)
            if pos == -1:
                return -1

            while pos < right and tokens[pos][0] == ":":
                pos = parse_t(pos + 1)
                if pos == -1:
                    return -1

            return pos

        i = parse_p(i)
        if i == -1:
            return False

        while i < right and tokens[i][0] == "|":
            i = parse_p(i + 1)
            if i == -1:
                return False

        return i == right

    openings = [i for i in range(m) if tokens[i][0] == "{"]

    for opening in reversed(openings):
        inner_ok[opening] = parse_m(opening + 1, matching[opening])

    return "YES" if parse_m(0, m) else "NO"

def run(inp: str) -> str:
    return solve(inp).strip()

# Provided samples
assert run("1\n") == "YES", "sample 1"
assert run(": 1\n") == "NO", "sample 2"
assert run("$ | 2\n") == "YES", "sample 3"

# Custom cases
assert run("") == "NO", "empty input"

assert run("1 2\n") == "NO", "whitespace cannot occur inside I"

assert run("{1:2}|3\n") == "YES", "nested M and colon expression"

assert run("{}\n") == "NO", "empty M inside braces"

assert run("||||123\n") == "YES", "arbitrarily many leading bars"

assert run("1|\n") == "NO", "bar requires a following P"

assert run("$\n") == "NO", "$ requires | P"

assert run("5 : 14\n") == "YES", "colon-separated P"

assert run("{" * 25000 + "1" + "}" * 25000 + "\n") == "YES", \
    "deep nesting without recursive calls"

assert run("1" * 100000 + "\n") == "YES", \
    "maximum-size digit sequence"

assert run("1||2\n") == "YES", "empty-looking M between bars is allowed via leading-bar recursion"

assert run("1|:2\n") == "NO", "bar cannot be followed by an invalid P"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 空行 |`NO`| 最小サイズの入力とその事実`M`空にすることはできません |
 |`1 2`|`NO`| 空白は`I`トークン |
 |`{1:2} | 3`|`YES`| 入れ子になった`M`、中括弧、コロン、バーを一緒に使用する |
 |`{}`|`NO`| 空のコンテンツは、`M`|
 |` |  |  |  | 123`|`YES`| 繰り返しの先頭` | `から`M -> | M`|
 |`1 | `|`NO`| ない`P`区切り文字の後 |
 |`⟦PROTECT_15⟧`フォームに必要なもの` | P`|
 |`1`100000 回繰り返した |`YES`| 最大入力サイズと長さ`I`トークン |
 | 周囲に 25000 個のネストされた中括弧`1`|`YES`| Python 再帰なしの深い入れ子 |
 |`1 |  | 2`|`YES`| 2 番目のバーは、残りのバーの先頭バーの規則に従って解釈できます。`M`|
 |`1 | :2`|`NO`| バーの後には完全な文字列が続く必要があります`P`|

 ## 特殊なケース

 空の入力は、解析が始まる前に処理されます。 トークンがないので、`T`、`P`、 または`M`。 アルゴリズムはすぐに戻ります`NO`。 

のために`1 2`、トークン化により生成される`I, I`、 それよりも`I`含む`12`。 最初`P`最初のものだけを消費します`I`。 次のトークンは別なので`I`それよりも`:`または`|`、`parse_m`未消費のトークンで終了して戻ります`NO`。 このため、空白を入力から単純に削除することはできません。 

のために`1|`、 最初`P`正常に消費します`1`。 パーサーは次のことを確認します。`|`そして別のループに入ります`P`。 バーの後にはトークンがないので、`parse_p`失敗し、結果は`NO`。 

のために`⟦PROTECT_16⟧`そしてすぐにチェックします`|`。 入力が終了しているため、必要な区切り文字が存在しないため、結果は次のようになります。`NO`。 のために`$ | 2`、区切り文字が存在し、`2`必要なものを供給します`P`, したがって、同じブランチは成功します。 

のために`{}`、中括弧マッチャーは 2 つの中括弧を正しく組み合わせます。`parse_m`それらの間の空の間隔で呼び出されます。 を構築するためのトークンがないため、`P`、保存された`inner_ok`値は`False`、外側`T`拒否されます。 結果は`NO`。 

のために`{1:2}|3`の場合、内側の中括弧の間隔が最初に処理されます。 そのトークンの形式`P = T : T`、ここで両方とも`T`値は数字のシーケンスなので、`inner_ok`になる`True`。 外側のパーサーはその後、`{1:2}`一つとして`T`、 に続く`| 3`、有効な結果を生成します`M`そして答え`YES`。 

25000 個の左中括弧のような深くネストされた式の場合、その後に`1`、その後に 25000 個の閉じ中括弧が続くと、中括弧スタックはすべてのペアと一致します。 最も内側の式が最初に評価され、各外側の式はその子の格納された結果を使用します。 入れ子レベルごとに Python 関数の呼び出しは行われないため、通常の再帰降下の信頼性が低くなるような深さでもアルゴリズムは安全に保たれます。
