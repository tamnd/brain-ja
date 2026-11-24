---
title: "CF 87B - Vasya とタイプ"
description: "この問題の言語には、void と errtype という 2 つの実際の基本型しかありません。 他のすべての型は typedef によって定義され、すべてのクエリで typeof を使用して型式を評価するように求められます。 型式は、基本名といくつかの and & 演算子から構築されます。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation", "strings"]
categories: ["algorithms"]
codeforces_contest: 87
codeforces_index: "B"
codeforces_contest_name: "Codeforces Beta Round 73 (Div. 1 Only)"
rating: 1800
weight: 87
solve_time_s: 116
verified: true
draft: false
---

[CF 87B - Vasya とタイプ](https://codeforces.com/problemset/problem/87/B)

 **評価:** 1800
 **タグ:** 実装、文字列
 **解決時間:** 1 分 56 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 この問題の言語には実際の基本型が 2 つだけあります。`void`そして`errtype`。 他のすべての型は次のように定義されます。`typedef`そして、すべてのクエリでは、次の式を使用して型式を評価するように求められます。`typeof`。 

型式は、基本名といくつかの名前から構築されます。`*`そして`&`オペレーター。 ルールは意図的に C とは異なります。 

追加中`*`「へのポインタ」を意味します。 追加中`&`「逆参照」を意味します。 逆参照すると、ポインタの深さが 1 つ減少します。 プレーンを逆参照しようとすると`void`、結果は次のようになります`errtype`。 

特殊なタイプ`errtype`吸収している。 式が次のようになると、`errtype`、追加`*`または`&`と等しく保つ`errtype`。 

重要な詳細はオペレーターの優先順位です。 逆参照はポインタの作成よりも優先順位が低くなります。 つまり:```
&T* = T
```ない```
(&T)*
```したがって、型式を解析すると、すべて`*`演算子は概念的にはすべての前に発生します`&`オペレーター。 

入力は、順番に処理される一連の操作です。 あ`typedef A B`操作は現在の意味を保存します`A`名前に`B`。 内部で使用される名前の将来の再定義`A`遡及的に影響しない`B`。 

あ`typeof A`クエリは評価を求めます`A`今すぐ正規形式を出力してください:```
void*****
```または`errtype`。 

制約はわずかです。 操作は最大 100 個あり、各演算子文字列には最大 10 個の明示的な操作が含まれます。`*`そして`&`シンボル。 typedef チェーンが長くなったとしても、総作業量は非常に少ないままです。 クエリごとに線形または二次処理を行うソリューションはどれも簡単に適合します。 

難しいのはパフォーマンスではなく、型システムを忠実にモデル化することです。 

よくある間違いの 1 つは、優先順位を尊重せずに演算子を左から右に評価することです。 

考慮する：```
typedef void* p
typeof &p*
```不用意に実装すると、これが次のように解釈される可能性があります。```
(&p)*
```これは次のようになります。```
(&void*)* = void*
```しかし、この言語では次のように定義されています。```
&p* = &(void**) = void*
```正しい結果は`void*`。 

もう 1 つの微妙なケースは、typedef スナップショット セマンティクスです。 

考慮する：```
typedef void* a
typedef a b
typedef void a
typeof b
```正しい出力は次のとおりです。```
void*
```なぜなら`b`の意味を保存します`a`定義時に。 参考にはなりません`a`ダイナミックに。 

もう 1 つの危険なエッジ ケースは、`errtype`。 

考慮する：```
typedef &void bad
typeof bad*****
```逆参照`void`すぐに生成する`errtype`そして、それが起こると、その後のすべてのオペレーターは依然としてそれをそのままにしておきます。`errtype`。 

正しい出力は次のとおりです。```
errtype
```負のポインタの深さを維持するか、操作を継続しようとする単純な実装`void`失敗した後は、静かに間違った答えを生み出す可能性があります。 

## アプローチ

 ブルートフォースのアイデアは、すべての型を次のような文字列として文字通り表現することです。```
void****
```次に、クエリごとに、具体的な型に到達するまで typedef 名を繰り返し展開します。 その後、すべての演算子を 1 つずつ適用します。 

これが機能するのは、言語のセマンティクスが単純で制約が小さいためです。 型が複数の typedef 層を介して展開される場合でも、操作は合計で 100 しかありません。 

この表現の問題は、誤った解析を助長することです。 間の優先規則`*`そして`&`生の文字列を操作する場合は扱いにくくなります。 誤って演算子を意味的な順序ではなくテキストの順序で処理してしまうことがよくあります。 

重要な観察は、すべての有効な非エラー型は 1 つの整数、つまり、その上にあるポインター層の数によって完全に決定されるということです。`void`。 

例えば：```
void      -> depth 0
void*     -> depth 1
void***   -> depth 3
```逆参照すると深さが 1 つ減少します。 ポインタを作成すると深さが 1 つ増加します。 

深さが負になる場合、結果は次のようになります。`errtype`。 

これにより、問題全体が整数演算に変換されます。 

式を解析するとき:```
&&name***
```演算子の優先順位は次のことを意味します。```
((name***)) with two dereferences afterward
```したがって、必要なのは次のものだけです。```
final_depth = base_depth + stars - amps
```基本タイプがすでにある場合`errtype`、または最終的な深さが負になる場合、結果は次のようになります。`errtype`。 

この表現は、typedef スナップショットのセマンティクスも自然に処理します。 新しい型名を定義するときは、その時点で評価された整数の深さを単純に保存します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n² · L) | O(n・L) | 受け入れられるがエラーが発生しやすい |
 | 最適 | O(n・L) | O(n) | 承認済み |

 ここ、`L`式の最大長です。 

## アルゴリズムのチュートリアル

 1. 既知のすべてのタイプの意味を格納する辞書を作成します。 

私たちは以下を代表します:```
void -> 0
errtype -> -1
```負でない整数は、次の数を意味します。`*`上のシンボル`void`。 値`-1`を表します`errtype`。 
2. 操作ごとに、行をトークンに分割します。 

操作は次のいずれかです。```
typedef A B
```または：```
typeof A
```3. 型式を評価するには、型式を 3 つの部分に分割します。 

先頭の`&`シンボル、

 後続の`*`シンボル、

 およびコアのタイプ名。 

例えば：```
&&ptr***
```は次のようになります:```
amps = 2
name = ptr
stars = 3
```4. コアタイプ名の現在の意味を調べます。 

名前が存在しない場合は、次のように扱います。`errtype`。 
5. 基本タイプがすでに`errtype`、 戻る`errtype`すぐに。 

この言語では次のように定義されます。```
errtype* = errtype
&errtype = errtype
```6. それ以外の場合は、次のように計算します。```
depth = base_depth + stars - amps
```これは、すべての優先規則と正確に一致します。`*`演算子はすべての前に適用されます`&`オペレーター。 
7. もし`depth < 0`、 戻る`errtype`。 

負の深さは、プレーンを逆参照しようとしたことを意味します`void`。 
8. のために`typedef`、評価された深さを新しい名前で保存します。 

これにより、定義時に完全に評価されたタイプのスナップショットが保存されます。 
9. のために`typeof`、印刷:```
errtype
```結果が次の場合`-1`、それ以外の場合は次のように出力します。```
void + '*' * depth
```### なぜ効果があるのか

 不変条件は、格納されたすべての型名が常にその完全に評価された正規形式にマップされることです。 

非エラー型は、上記のポインタの深さによってのみ表されます。`void`。 言語操作はポインターの深さを増減するだけなので、この表現は必要な情報をすべて取得します。 

すべての式は次のように評価されるため、演算子の優先順位は維持されます。```
base + all pointers - all dereferences
```これは、逆参照の優先順位がポインタの作成よりも低いという言語規則とまったく同じです。 

typedef はシンボリック参照ではなく評価された深さを格納するため、後で再定義しても以前に定義された型に影響を与えることはできません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def evaluate(expr, types):
    amps = 0
    i = 0

    while i < len(expr) and expr[i] == '&':
        amps += 1
        i += 1

    stars = 0
    j = len(expr) - 1

    while j >= i and expr[j] == '*':
        stars += 1
        j -= 1

    name = expr[i:j + 1]

    if name not in types:
        return -1

    base = types[name]

    if base == -1:
        return -1

    depth = base + stars - amps

    if depth < 0:
        return -1

    return depth

def solve():
    n = int(input())

    types = {
        "void": 0,
        "errtype": -1
    }

    out = []

    for _ in range(n):
        parts = input().split()

        if parts[0] == "typedef":
            expr = parts[1]
            new_name = parts[2]

            types[new_name] = evaluate(expr, types)

        else:
            expr = parts[1]

            result = evaluate(expr, types)

            if result == -1:
                out.append("errtype")
            else:
                out.append("void" + "*" * result)

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```の`evaluate`関数は型システム全体を実装します。 

最初に先頭にカウントされます`&`記号と末尾`*`シンボル。 真ん中にあるものはすべて実際の型名です。 この解析方法は言語の文法と直接一致し、演算子の優先順位を自動的に尊重します。 

ディクショナリには、シンボリック式の代わりに評価された深さが格納されます。 これは、typedef スナップショットのセマンティクスを保持する実装の詳細です。 後で型が再定義された場合、以前に保存されたエントリには最終的な整数の深さがすでに含まれているため、変更されないままになります。 

特別な値`-1`を表します`errtype`。 単一のセンチネル値を使用すると、すべての伝播ルールが単純になります。 評価が届き次第`-1`、今後のすべての操作は維持されます`-1`。 

最も一般的な実装上の間違いは、演算子をテキスト順に処理することです。 このソリューションでは、式を次のように変換することで、それを完全に回避します。```
base + stars - amps
```もう 1 つの微妙な点は、未定義の名前の処理です。 声明では、彼らはすぐにそうなると述べています`errtype`、したがって、評価者は戻ります`-1`名前が辞書に載っていないとき。 

## 実用的な例

 ### サンプル 1

 入力:```
5
typedef void* ptv
typeof ptv
typedef &&ptv node
typeof node
typeof &ptv
```トレース:

 | 操作 | ベースタイプ | 星 | アンプ | 結果の深さ | 保存 / 出力 |
 | --- | --- | --- | --- | --- | --- |
 | typedef void* ptv | 0 | 1 | 0 | 1 | PTV = 1 |
 | PTV の種類 | 1 | 0 | 0 | 1 | 無効* |
 | typedef &&ptv ノード | 1 | 0 | 2 | -1 | ノード = エラータイプ |
 | ノードのタイプ | エラータイプ | 0 | 0 | エラータイプ | エラータイプ |
 | &ptv の種類 | 1 | 0 | 1 | 0 | ボイド |

 3 番目の操作は、逆参照が多すぎることを示しています。`ptv`等しい`void*`、 それで`&&ptv`逆参照を 2 回試行し、深さがゼロを下回ります。 

### カスタム例

 入力:```
6
typedef void** a
typedef &a b
typedef b* c
typeof a
typeof b
typeof c
```トレース:

 | 操作 | ベースタイプ | 星 | アンプ | 結果の深さ | 保存 / 出力 |
 | --- | --- | --- | --- | --- | --- |
 | typedef void** a | 0 | 2 | 0 | 2 | a = 2 |
 | typedef &a b | 2 | 0 | 1 | 1 | b = 1 |
 | typedef b* c | 1 | 1 | 0 | 2 | c = 2 |
 | | のタイプ 2 | 0 | 0 | 2 | ボイド** |
 | タイプ b | 1 | 0 | 0 | 1 | 無効* |
 | c の型 | 2 | 0 | 0 | 2 | ボイド** |

 この例では、typedef に評価値が格納されていることを確認します。`b`になる`void*`、 そして`c`になる`void**`後の変更とは独立して。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n・L) | 各操作はその式を 1 回スキャンします。 
| スペース | O(n) | ディクショナリには、typedef ごとに最大 1 つのエントリが保存されます。 

ここ、`L`式の最大長です。 

最大 100 個の演算と非常に短い式を使用するため、実行時間はごくわずかです。 ソリューションは制限内に簡単に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def solve():
    input = sys.stdin.readline

    def evaluate(expr, types):
        amps = 0
        i = 0

        while i < len(expr) and expr[i] == '&':
            amps += 1
            i += 1

        stars = 0
        j = len(expr) - 1

        while j >= i and expr[j] == '*':
            stars += 1
            j -= 1

        name = expr[i:j + 1]

        if name not in types:
            return -1

        base = types[name]

        if base == -1:
            return -1

        depth = base + stars - amps

        if depth < 0:
            return -1

        return depth

    n = int(input())

    types = {
        "void": 0,
        "errtype": -1
    }

    out = []

    for _ in range(n):
        parts = input().split()

        if parts[0] == "typedef":
            types[parts[2]] = evaluate(parts[1], types)
        else:
            result = evaluate(parts[1], types)

            if result == -1:
                out.append("errtype")
            else:
                out.append("void" + "*" * result)

    return "\n".join(out)

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    return solve()

# provided sample
assert run(
"""5
typedef void* ptv
typeof ptv
typedef &&ptv node
typeof node
typeof &ptv
"""
) == "void*\nerrtype\nvoid"

# minimum input
assert run(
"""1
typeof void
"""
) == "void"

# undefined type
assert run(
"""1
typeof abc
"""
) == "errtype"

# typedef snapshot semantics
assert run(
"""4
typedef void* a
typedef a b
typedef void a
typeof b
"""
) == "void*"

# errtype propagation
assert run(
"""3
typedef &void bad
typedef bad***** x
typeof x
"""
) == "errtype"

# precedence check
assert run(
"""2
typedef void* p
typeof &p*
"""
) == "void*"

print("All tests passed.")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`typeof void`|`void`| 最小の有効なクエリ |
 |`typeof abc`|`errtype`| 未定義型の処理 |
 | 再定義`a`定義した後`b`|`void*`| Typedef スナップショットのセマンティクス |
 | 使用する`bad*****`エラー後 |`errtype`| エラータイプの伝播 |
 |`typeof &p*`|`void*`| 正しい演算子の優先順位 |

 ## 特殊なケース

 未定義の型を考慮してください。```
1
typeof abc
```評価者は以下を抽出します。```
name = abc
```以来`abc`辞書にない場合、評価はすぐに返されます`-1`、次のように出力されます。```
errtype
```これは、不明な型が自動的に次の値になるというステートメントと一致します。`errtype`。 

ここで、過剰な逆参照について考えてみましょう。```
2
typedef void p
typeof &p
```

`p`深さがある`0`。 クエリは 1 つの逆参照を適用します。```
0 - 1 = -1
```負の深さは無効なので、結果は次のようになります。```
errtype
```アルゴリズムはこれを次の方法できれいに処理します。`depth < 0`チェック。 

最後に、typedef の再定義について考えてみましょう。```
5
typedef void* a
typedef a b
typedef void a
typeof a
typeof b
```トレースは次のとおりです。 

| ステップ | | b |
 | --- | --- | --- |
 | 最初の typedef の後 | 1 | 定義されていません |
 | 2 番目の typedef 以降 | 1 | 1 |
 | | を再定義した後 0 | 1 |

 出力は次のようになります。```
void
void*
```これは、アルゴリズムが完全に評価された深さを辞書内に格納するためです。`b`その後も古い値が保持されます`a`変化します。
