---
title: "CF 102419L - 不正行為の検出。"
description: "変数の定義、変数の読み取り、変数の出力、および 2 つの変数の合計を別の変数に割り当てるという 3 種類のステートメントを含む 2 つのプログラムが小さな言語で書かれています。"
date: "2026-08-12T20:38:39+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102419
codeforces_index: "L"
codeforces_contest_name: "SPC 2019"
rating: 0
weight: 102419
solve_time_s: 769
verified: true
draft: false
---

[CF 102419L - 不正行為の検出](https://codeforces.com/problemset/problem/102419/L)

 **評価:** -
 **タグ:** -
 **解決時間:** 12 分 49 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 変数の定義、変数の読み取り、変数の出力、および 2 つの変数の合計を別の変数に割り当てるという 3 種類のステートメントを含む 2 つのプログラムが小さな言語で書かれています。 変数は最大 1 回定義され、すべての変数は使用前に定義されます。 

最初のプログラムの変数の名前を変更して、すべてのステートメントが 2 番目のプログラムの対応するステートメントとまったく同じになる場合、2 つのプログラムは同等であると見なされます。 名前変更はグローバルなので、`a`に名前が変更されます`x`、すべての発生`a`にならなければなりません`x`。 変数の順序`define`ステートメントはプログラムの一部であり、加算の 2 つのオペランドも位置指定されます。 表現`a=b+c`と同等ではありません`a=c+b`単に加算が数学的に可換であるからです。 

入力には、最初のプログラムの行数とそれに続くそれらの行、次にそれらの行が続く 2 番目のプログラムの行数が含まれます。 各プログラムの行数は最大 1000 行です。 これは、線形スキャンが簡単に十分に高速であるほど十分に小さいですが、変数間の考えられるすべての対応を試みるのが完全に非現実的であるほど十分に大きいでもあります。 最大 1000 個の異なる変数を使用できます。`1000!`名前変更の可能性。 

最初のエッジケースは、プログラムの長さが異なることです。 例えば、```
1
define a
2
define x
define y
```生産しなければならない`NO`。 変数の名前変更ではステートメントの挿入や削除はできないため、行数が異なるプログラムは決して一致しません。 対応する位置で使用されている変数のみを比較する不注意な実装では、これをすぐに見落とす可能性があります。 

2 番目の特殊なケースは、オペランドの順序です。 考慮する```
5
define a
define b
define c
a=b+c
print a
5
define x
define y
define z
x=z+y
print x
```正しい出力は次のとおりです`NO`。 3 つの定義から可能な唯一のマッピングは次のとおりです。`a -> x`、`b -> y`、 そして`c -> z`。 そのマッピングの下で​​は、`a=b+c`になる`x=y+z`、 ない`x=z+y`。 治療中`+`数学的に可換であるため、このペアは誤って受け入れられます。 

3 番目のエッジ ケースは、変数マッピングが 1 対 1 である必要があるということです。 例えば、```
3
define a
define b
print a
3
define x
define x2
print x
```は`NO`。 最初のプログラムに必要なものは、`a -> x`、 その間`b`別の変数にマップする必要があります。 実装が最初のプログラムの名前から 2 番目のプログラムの名前へのマッピングのみを保存し、逆方向のチェックをまったく行わない場合、誤って 2 つの異なる変数が同じ名前にマップされる可能性があります。 

最後の有用なケースは、定義の順序が異なる場合です。 例えば、```
5
define a
define b
define c
a=b+c
print a
5
define x
define z
define y
x=y+z
print x
```は`YES`を使用して`a -> x`、`b -> y`、 そして`c -> z`。 テキスト名自体には意味がありません。 重要なのは、1 回の一貫した名前変更によってステートメントの完全なシーケンスが同一になるかどうかです。 

## アプローチ

 最も直接的な総当たりアプローチは、最初のプログラムのすべての変数と 2 番目のプログラムのすべての変数を収集し、2 つのセット間ですべての全単射を試行することです。 全単射候補ごとに、最初のプログラムのすべての変数を置き換え、結果のプログラムを 2 番目のプログラムと比較します。 これは正しいです。なぜなら、浮気の定義はまさにそのような全単射の存在だからです。 

問題は全単射の数です。 あれば`k`異なる変数があります。`k!`可能なマッピング。 1 つのマッピングをテストするには次のものが必要です`O(n+m)`したがって、全体の複雑さは次のようになります。`O(k! (n+m))`。 と`k=1000`、行の比較が意味を持つようになるずっと前に、候補の数は想像を絶するほど多くなります。 

重要な観察点は、どの変数が対応する必要があるかをプログラム自体が教えてくれるということです。 最初に完全なマッピングを推測する必要はありません。 最初のプログラムのステートメントの位置で変数が言及されると、2 番目のプログラムの対応する位置によって、どの変数にマップする必要があるかがわかります。 対応関係が確立されると、その後同じ変数が出現するたびに、まったく同じターゲットを使用する必要があります。 

2 つの辞書を使用してこれを直接強制できます。 1 つの辞書は、最初のプログラムの変数を 2 番目のプログラムの変数にマップします。 逆引き辞書は、2 番目のプログラムの変数を最初のプログラムの変数にマッピングします。 変数出現のペアを比較する場合、マッピングはこれまでに確認されていないため確立するか、すでに確立されており現在のペアと一致する必要があります。 逆マッピングにより、2 つの異なるソース変数が同じターゲット変数に割り当てられるのを防ぎます。 

この総当り方式は、考えられるすべての名前変更を明示的に検索するため機能しますが、その数が非常に多いため失敗します。 対応する各出現が唯一可能なマッピングを即座に制約するという観察により、プログラムを 1 回スキャンしながら必要な全単射を構築できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(k! (n+m))`|`O(k+n+m)`| 遅すぎる |
 | 双方向マッピング |`O(n+m)`|`O(k+n+m)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 両方の完全なプログラムを読み取り、行数が異なる場合は直ちに拒否します。 名前を変更すると名前は変わりますが、プログラムの構造は変更できません。 
2. 各行をコマンドとその変数の位置に解析します。 あ`define`、`read`、 または`print`行には変数が 1 つ含まれています。 代入には、デスティネーション、左オペランド、右オペランドの 3 つの変数が含まれます。 コマンド自体の名前は変更されません。 
3. 2 つの空の辞書を作成します。 1 つ目は、最初のプログラムの変数から 2 番目のプログラムの変数へのマッピングを保存します。 2 番目には逆マッピングが保存されます。 
4. 2 つのプログラムの対応する行を上から下にスキャンします。 両者のコマンドが異なる場合は戻ります`NO`変数の名前変更では次のようなコマンドを変更できないためです。`read`の中へ`print`。 
5. 対応する変数位置のペアごとに、2 つの変数名を検査します。 最初の変数にすでにマッピングがある場合は、それが現在の 2 番目の変数にマッピングされていることを確認します。 そうでない場合は、戻ります`NO`。 
6. 最初の変数にまだマッピングがない場合は、2 番目の変数が別の最初の変数から既にマッピングされているかどうかを確認します。 そうであれば、返してください`NO`。 それ以外の場合は、マッピングの両方向を確立します。 
7. 対応するすべての位置がこれらのチェックに合格した場合、戻ります。`YES`。 その時点で、すべての変数には単一の一貫した対応物があり、すべてのコマンドとすべての変数の位置が一致するため、最初のプログラムの名前を変更すると、正確に 2 番目のプログラムが生成されます。 

### なぜ効果があるのか

 中心的な不変条件は、2 つのプログラムの任意の接頭辞を処理した後、2 つの辞書がその接頭辞全体に対する有効な 1 対 1 の変数の名前変更を記述することです。 新しい変数のペアが見つかった場合、既存のマッピングはそのペアと一致する必要がありますが、新しいマッピングは、そのターゲットがまだ別のソース変数に割り当てられていない場合にのみ導入できます。 したがって、変数が出現するたびに不変式が保存されます。 

アルゴリズムが拒否した場合は、プログラム構造が異なるか、一部の変数に 2 つの異なる名前が必要か、または 2 つの異なる変数に同じ名前が必要かのいずれかです。 これらの状況はいずれも、別のグローバルな名前変更では修復できないため、プログラムを同等にすることはできません。 

アルゴリズムが最後まで到達すると、いずれかのプログラムで使用されるすべての変数の対応は一貫性があり、1 対 1 になります。 すべての最初のプログラム変数を、マップされた 2 番目のプログラム変数で置き換えると、対応するすべてのステートメントが同一になり、これがまさに必須条件になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def parse_line(line):
    line = line.strip()

    parts = line.replace(" ", "").split("=")

    if len(parts) == 1:
        command, var = line.split()
        return command, [var]

    left = parts[0]
    right = parts[1]
    b, c = right.split("+")
    return "assign", [left, b, c]

def equivalent(program1, program2):
    if len(program1) != len(program2):
        return False

    forward = {}
    backward = {}

    for line1, line2 in zip(program1, program2):
        command1, vars1 = parse_line(line1)
        command2, vars2 = parse_line(line2)

        if command1 != command2 or len(vars1) != len(vars2):
            return False

        for a, b in zip(vars1, vars2):
            if a in forward:
                if forward[a] != b:
                    return False
            else:
                if b in backward:
                    return False

                forward[a] = b
                backward[b] = a

    return True

def solve():
    n = int(input())
    program1 = [input().strip() for _ in range(n)]

    m = int(input())
    program2 = [input().strip() for _ in range(m)]

    print("YES" if equivalent(program1, program2) else "NO")

if __name__ == "__main__":
    solve()
```の`parse_line`関数は、代入構文を 3 つの変数位置に分割する前にスペースを削除することで代入構文を正規化します。 これは、次のような両方のコンパクトな形式を処理します。`a=b+c`および次のような間隔をあけた形式`a = b + c`。 

簡単なコマンドの場合は、`split()`コマンドワードをその変数から分離します。 パーサーは、次のような共通の内部表現を返します。`assign`代入と、デスティネーション、左オペランド、右オペランドを正確にその順序で含む関連リストを表します。 

の`equivalent`2 つのプログラムは同一の構造を持つ必要があるため、関数は最初に行数をチェックします。 その後、維持します`forward`そして`backward`、全単射の 2 方向を実装します。 

小切手`forward[a] != b`2 つの異なる名前を持つことを強制された変数をキャッチします。 の`backward`lookup は、1 つのターゲット名を共有することを強制されている 2 つの異なる変数を検出します。 必要な名前変更は 2 つのプログラムに現れる変数間の全単射であるため、両方のチェックが必要です。 

再帰や数値計算は存在しないため、整数のオーバーフローや再帰の深さは無関係です。 スキャンでは、すべての行とすべての変数の出現が 1 回処理され、辞書操作には予想される一定の時間がかかります。 

## 実用的な例

 ### サンプル 1

 2 つのプログラムは同じ構造を持っているため、アルゴリズムはそれらのステートメントの比較を開始します。 最初の 3 つの定義は、可能なマッピングのみを確立します。 

| ライン | コマンド | 最初の変数の位置 | 2 番目の変数の位置 | マッピング状態 | 結果 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 定義 |`a`|`a`|`a -> a`| 続ける |
 | 2 | 定義 |`b`|`b`|`a -> a`、`b -> b`| 続ける |
 | 3 | 定義 |`c`|`c`|`a -> a`、`b -> b`、`c -> c`| 続ける |
 | 4 | 割り当てる |`a,b,c`|`a,c,b`|`a -> a`同意します、`b -> c`衝突 | 拒否 |

 4号線で目的地`a`一貫性がありますが、最初のプログラムには次のことが必要です`b -> c`既存のマッピングには次のことが必要です`b -> b`。 単一のグローバル名前変更では両方の要件を満たすことはできないため、答えは次のようになります。`NO`。 

### サンプル 2

 ここでは定義の順序が異なるため、最初に出現したものによって重要な名前変更が確立されます。 

| ライン | コマンド | 最初の変数の位置 | 2 番目の変数の位置 | マッピング状態 | 結果 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 定義 |`a`|`a`|`a -> a`| 続ける |
 | 2 | 定義 |`b`|`c`|`a -> a`、`b -> c`| 続ける |
 | 3 | 定義 |`c`|`b`|`a -> a`、`b -> c`、`c -> b`| 続ける |
 | 4 | 割り当てる |`a,b,c`|`a,c,b`| すべてのマッピングが一致します | 続ける |
 | 5 | 印刷 |`a`|`a`|`a -> a`同意する | 受け入れる |

 マッピングの入れ替え`b`そして`c`。 最初のプログラムに適用すると変換されます`a=b+c`の中へ`a=c+b`、2番目のプログラムと完全に一致します。 以降の出現はすべて、定義によって確立されたマッピングを尊重するため、アルゴリズムは受け入れます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(n+m)`| すべての入力行とすべての変数の出現は、期待どおりに 1 回処理されます。`O(1)`辞書操作。 |
 | スペース |`O(n+m)`| 2 つのプログラムが保存され、2 つのマッピングには個別の変数の数に比例したスペースが必要です。 |

 どちらのプログラムも最大 1000 行しか含まれていないため、処理すべき変数の発生はわずか数千です。 線形解は、1 秒および 256 MB の制限をはるかに下回っています。 階乗総当たり検索は、根本的に不適切な唯一のアプローチです。 

## テストケース```python
import sys
import io

def parse_line(line):
    line = line.strip()

    parts = line.replace(" ", "").split("=")

    if len(parts) == 1:
        command, var = line.split()
        return command, [var]

    left = parts[0]
    right = parts[1]
    b, c = right.split("+")
    return "assign", [left, b, c]

def equivalent(program1, program2):
    if len(program1) != len(program2):
        return False

    forward = {}
    backward = {}

    for line1, line2 in zip(program1, program2):
        command1, vars1 = parse_line(line1)
        command2, vars2 = parse_line(line2)

        if command1 != command2 or len(vars1) != len(vars2):
            return False

        for a, b in zip(vars1, vars2):
            if a in forward:
                if forward[a] != b:
                    return False
            else:
                if b in backward:
                    return False
                forward[a] = b
                backward[b] = a

    return True

def solve():
    n = int(input())
    program1 = [input().strip() for _ in range(n)]

    m = int(input())
    program2 = [input().strip() for _ in range(m)]

    return "YES" if equivalent(program1, program2) else "NO"

def run(inp: str) -> str:
    global input
    old_stdin = sys.stdin
    old_input = input

    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline

    try:
        return solve()
    finally:
        sys.stdin = old_stdin
        input = old_input

sample1 = """\
5
define a
define b
define c
a=b+c
print a
5
define a
define b
define c
a=c+b
print a
"""
assert run(sample1) == "NO", "sample 1"

sample2 = """\
5
define a
define b
define c
a=b+c
print a
5
define a
define c
define b
a=c+b
print a
"""
assert run(sample2) == "YES", "sample 2"

sample3 = """\
5
define a
define b
define c
a=b+c
print a
5
define a
define b
define c
a=b+c
print a
"""
assert run(sample3) == "YES", "sample 3"

# Minimum-size programs. A single variable can always be renamed to another
# variable because there is no second constraint.
assert run("""\
1
define a
1
define x
""") == "YES", "minimum size"

# Different program lengths can never be made equal by renaming.
assert run("""\
1
define a
2
define x
define y
""") == "NO", "different lengths"

# The same source variable is forced to map to two different target variables.
assert run("""\
4
define a
define b
print a
print a
4
define x
define y
print x
print y
""") == "NO", "inconsistent mapping"

# The target variables are swapped, but the whole program is still equivalent.
assert run("""\
6
define first
define second
define third
first=second+third
print third
read first
6
define x
define z
define y
x=z+y
print y
read x
""") == "YES", "nontrivial bijection"

# Large input, exercising the linear scan.
lines1 = ["define v0"]
lines1.extend(f"define v{i}" for i in range(1, 1000))
lines1.append("print v999")

lines2 = ["define x0"]
lines2.extend(f"define x{i}" for i in range(1, 1000))
lines2.append("print x999")

large_input = (
    str(len(lines1)) + "\n" +
    "\n".join(lines1) + "\n" +
    str(len(lines2)) + "\n" +
    "\n".join(lines2) + "\n"
)
assert run(large_input) == "YES", "maximum-size linear scan"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1つ`define`各プログラムの行 |`YES`| 最小入力サイズと基本的な名前変更 |
 | 1 行と 2 行のプログラム |`NO`| プログラムの長さの違い |
 | 異なるターゲットにマッピングされたソース変数が繰り返される |`NO`| フォワードマッピングの一貫性 |
 | 自明ではない順列を持つ 3 つの変数 |`YES`| 双方向の名前変更と繰り返し使用 |
 | 1000 の定義と最終的な用途 |`YES`| 最大サイズの入力と線形複雑度 |

 ## 特殊なケース

 長さが異なる場合は、変数の比較の前に処理されます。 のために```
1
define a
2
define x
define y
```

`equivalent`プログラムの長さが次のとおりであることがわかります。`1`そして`2`そしてすぐに戻ります`False`。 出力は`NO`。 マッピングによってステートメントの数を変更することはできません。 

オペランド順序の場合、```
4
define a
define b
define c
a=b+c
4
define x
define y
define z
x=z+y
```定義が確立される`a -> x`、`b -> y`、 そして`c -> z`。 割り当てに到達すると、その最初の変数ペアは次のようになります。`a -> x`、これは有効です。 2番目のペアは次のように要求します`b -> z`、 しかし`b`はすでにマッピングされています`y`、そのため、アルゴリズムは拒否します。 出力は`NO`。 このアルゴリズムはオペランドを決して並べ替えないため、式を数学的な加算ではなく構文として正しく処理します。 

自明でない順列については、```
4
define a
define b
define c
a=b+c
4
define x
define z
define y
x=z+y
```最初の 3 行で確立されます`a -> x`、`b -> z`、 そして`c -> y`。 課題では、3 つのペアは次のとおりです。`a -> x`、`b -> z`、 そして`c -> y`、それらはすべて既存のマッピングと一致します。 出力は`YES`。 これは、生の変数名や定義位置の比較だけでは不十分である理由を示しています。 

一貫性のない再利用の場合、```
4
define a
define b
print a
print b
4
define x
define y
print x
print x
```最初の印刷物が確立する`a -> x`。 2 番目のプリントでは次のことを要求します`b -> x`。 逆引き辞書にはすでに次の内容が含まれています`x -> a`、そのため、アルゴリズムはマッピングを拒否します。 出力は`NO`。 逆引き辞書がないと、不注意な実装により 2 つの異なる変数が同じ変数に名前変更される可能性があり、これは有効な名前変更ではありません。
