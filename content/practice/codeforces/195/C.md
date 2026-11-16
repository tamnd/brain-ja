---
title: "CF 195C - トライアンドキャッチ"
description: "私たちに与えられた小さな言語のソース コードには、try、catch(type、message)、および 1 つの throw(type) という 3 種類のステートメントのみが含まれています。 各 Try は後の catch とペアになり、try-catch ブロックを形成します。"
date: "2026-06-05T00:36:58+07:00"
tags: ["codeforces", "competitive-programming", "expression-parsing", "implementation"]
categories: ["algorithms"]
codeforces_contest: 195
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 123 (Div. 2)"
rating: 1800
weight: 195
solve_time_s: 143
verified: true
draft: false
---

[CF 195C - トライ アンド キャッチ](https://codeforces.com/problemset/problem/195/C)

 **評価:** 1800
 **タグ:** 式の解析、実装
 **解決時間:** 2 分 23 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちに与えられたのは、3 種類のステートメントのみを含む小さな言語のソース コードです。`try`、`catch(type, message)`、そしてちょうど 1 つ`throw(type)`。 

それぞれ`try`後者とペアになっています`catch`、try-catch ブロックを形成します。 入力は構文的に正しいことが保証されているため、これらのブロックは適切にネストされています。 

例外がスローされた場合、どの catch ブロックがそれを処理するかを決定する必要があります。 catch ブロックは、次の 3 つの条件が満たされる場合に例外を処理できます。 

1. 対応する`try`の前に表示されます`throw`。 
2. それ`catch`の後に表示されます`throw`。 
3. 例外の型がスローされた型と一致します。 

このようなすべてのブロックの中から、言語はそのブロックを選択します。`catch`ステートメントはスロー後最も早く表示されます。 その catch ブロックのメッセージを出力する必要があります。 一致するブロックがない場合は、次のように出力します。```
Unhandled Exception
```プログラムには最大でも次の内容が含まれます$10^5$線。 これにより、プログラムの大部分を繰り返しスキャンするソリューションは即座に除外されます。 二次アルゴリズムでは、大まかに次のことが必要になります。$10^{10}$限界をはるかに超えた最悪の場合の動作。 線形または線形に近い解決策が必要です。 

解析自体も課題の一部です。 行には、キーワード、括弧、カンマ、引用符の周囲に任意のスペースを含めることができます。 正確な書式設定に依存するソリューションは失敗します。 

微妙な点の 1 つは、選択したハンドラーが必ずしも最も内側のハンドラーではないということです。`try`。 言語は、一致するブロックを選択します。`catch`投げた後最初に発生します。 

考慮する：```
try
    try
        throw(AE)
    catch(BE,"x")
catch(AE,"y")
```内側のブロックはスローを囲んでいますが、その例外タイプが一致しません。 正しい出力は次のとおりです。```
y
```もう 1 つの簡単な間違いは、スローに達したときにまだ「開いている」ブロックの中だけを検索することです。 言語の定義は、次の相対的な位置に依存します。`try`、`throw`、 そして`catch`実行のシミュレーションではありません。 

例えば：```
try
    throw(AE)
catch(AE,"first")
try
catch(AE,"second")
```正しい出力は次のとおりです。```
first
```投げた後に最初に一致するキャッチが選択されるためです。 

3 番目のエッジ ケースは、一致するハンドラーがない場合に発生します。```
try
    throw(AE)
catch(BE,"wrong")
```出力は次のようになります。```
Unhandled Exception
```たとえスローがトライキャッチブロック内にあったとしても。 

## アプローチ

 ブルート フォース ソリューションでは、最初にすべての try-catch ブロックが再構築されます。 次に、スローを見つけた後、すべてのブロックを調べて、次のことを確認します。 

- ブロックの`try`投げる前です、
 - ブロックの`catch`投げた後です、
 - 例外の種類が一致する。 

すべての予選ブロックの中で、キャッチ位置が最も小さいブロックを選択します。 

このアプローチは仕様を直接実装しているため、正しいです。 あれば$m$ブロック、仕事は$O(m)$、これはすでに許容されています。 本当に難しいのは、解析中にブロックを効率的に再構築することです。 

慎重に実装しないと、一致するキャッチを繰り返し検索したり、ネスト構造を繰り返し走査したりする可能性があり、その結果、$O(n^2)$深くネストされた入力に対する動作。 

重要な観察点は、プログラム構造が完全に入れ子になっているということです。 毎`catch`最新の不一致を閉じます`try`。 これはまさにスタックの動作です。 

プログラムを 1 回スキャンしている間に、すべてのプログラムの行番号をプッシュできます。`try`。 とき`catch`と表示されると一番上の部分が閉じます`try`ので、すぐに 1 つの完全なブロックを構築できるようになります。 

すべてのブロックを収集し、単一のスローを見つけた後、答えを決定することは、ブロックに対する単純な線形スキャンになります。 

ソリューション全体は線形時間で実行されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 繰り返しの検索によるブルートフォース | O(n²) | O(n) | 遅すぎる |
 | スタックベースの解析 + 1 回のスキャン | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべてのプログラム行を読み取ります。 
2. すべての行で、先頭と末尾の空白を削除します。 
3. その行にどのステートメントが含まれているかを検出します。 
4.`try`が見つかった場合は、その行インデックスをスタックにプッシュします。 

最新の比類のない`try`次に閉じられるものでなければなりません。 
5. とき`catch(type,message)`が見つかった場合は、スタックをポップします。 

ポップされた位置が一致します`try`この獲物のために。 以下を含むレコードを保存します。 

- ポジションを試す
 - キャッチポジション
 - 例外の種類
 - メッセージ
 6. とき`throw(type)`が見つかった場合は、次のように保存します。 

- 投げる位置
 - スローされた例外の種類
 7. プログラム全体を解析した後、記録されたすべてのブロックを調べます。 
8. ブロックは、次の場合に例外を処理できます。 

-`try_position < throw_position`-`catch_position > throw_position`-`block_type == thrown_type`9. 一致するすべてのブロックの中で、キャッチ位置が最小のものを選択します。 
10. メッセージを出力します。 
11. 該当するブロックがない場合は、次のように出力します。```
Unhandled Exception
```### なぜ効果があるのか

 ステートメントはすべての`catch`最後に開いた一致しないものを閉じます`try`。 これはまさに標準のかっこ一致プロパティです。 

したがって、記録されたすべてのブロックには、正しい位置のペアが含まれます。 ブロックが適格となるのは、次の場合です。`try`投げる前とその`catch`はスローの後であり、言語定義と一致します。 適格なブロックの中で、最小のキャッチ位置を選択すると、スロー後の最も早いキャッチが有効になるというルールが実装されます。 考えられるすべてのブロックが 1 回だけチェックされるため、アルゴリズムが正しいハンドラーを見逃すことはありません。 

## Python ソリューション```python
import sys
import re

input = sys.stdin.readline

def solve():
    n = int(input())

    stack = []
    blocks = []

    throw_pos = -1
    throw_type = ""

    catch_re = re.compile(
        r'catch\s*\(\s*([A-Za-z]+)\s*,\s*"([^"]+)"\s*\)'
    )
    throw_re = re.compile(
        r'throw\s*\(\s*([A-Za-z]+)\s*\)'
    )

    for i in range(n):
        line = input().strip()

        if not line:
            continue

        if line.startswith("try"):
            stack.append(i)

        elif line.startswith("throw"):
            m = throw_re.match(line)
            throw_type = m.group(1)
            throw_pos = i

        elif line.startswith("catch"):
            m = catch_re.match(line)
            exc_type = m.group(1)
            message = m.group(2)

            try_pos = stack.pop()
            blocks.append((try_pos, i, exc_type, message))

    answer = None
    best_catch_pos = n + 1

    for try_pos, catch_pos, exc_type, message in blocks:
        if (
            try_pos < throw_pos < catch_pos
            and exc_type == throw_type
        ):
            if catch_pos < best_catch_pos:
                best_catch_pos = catch_pos
                answer = message

    if answer is None:
        print("Unhandled Exception")
    else:
        print(answer)

solve()
```コードの最初の部分では、try-catch ブロックを再構築しながらプログラムを解析します。 スタックは比類のないものを格納します`try`ポジション。 いつでも`catch`と表示される場合、スタックの最上位が唯一の有効な一致です`try`, そのため、すぐに完全なブロック レコードを作成します。 

正規表現は任意の間隔を処理します。 これにより、次のような入力によって引き起こされる多くの解析バグが回避されます。```
catch ( AE , "msg" )
```または```
throw( AE )
```第 2 フェーズでは、スローされた例外に対してすべてのブロックを評価します。 条件```
try_pos < throw_pos < catch_pos
```これは、スローがブロック内にあるというステートメントの要件と正確に一致します。 

言語ではスローの後に最初に表示される一致するキャッチが選択されるため、最後のタイブレークでは最小のキャッチ位置が使用されます。 

## 実用的な例

 ### 例 1

 入力:```
try
    try
        throw(AE)
    catch(BE,"BE in line 3")

    try
    catch(AE,"AE in line 5")
catch(AE,"AE somewhere")
```| イベント | イベント後のスタック | 記録されたブロック |
 | --- | --- | --- |
 | 0 で試してください | [0] | - |
 | 1 で試してください | [0,1] | - |
 | 2 でスロー(AE) | [0,1] | - |
 | 3でキャッチ(BE) | [0] | (1,3,BE) |
 | 5時に試してください | [0,5] | 前 |
 | 6でキャッチ(AE) | [0] | (5,6,AE) |
 | 7でキャッチ(AE) | [] | (0,7,AE) |

 ブロックを確認する:

 | ブロック | スローが含まれていますか? | タイプ一致? | 適格？ |
 | --- | --- | --- | --- |
 | (1,3,BE) | はい | いいえ | いいえ |
 | (5,6,AE) | いいえ | はい | いいえ |
 | (0,7,AE) | はい | はい | はい |

 選択されたメッセージは次のとおりです。```
AE somewhere
```この例は、型が一致するだけでは不十分であることを示しています。 スローも、対応するトライとキャッチの間になければなりません。 

### 例 2```
try
    try
        throw(AE)
    catch(AE,"inner")
catch(AE,"outer")
```| ブロック | 位置を試してください | キャッチポジション | タイプ |
 | --- | --- | --- | --- |
 | インナー | 1 | 3 | ＡＥ |
 | アウター | 0 | 4 | ＡＥ |

 両方のブロックにスローが含まれており、両方のタイプが一致します。 

| ブロック | キャッチポジション |
 | --- | --- |
 | インナー | 3 |
 | アウター | 4 |

 最も早いキャッチは位置 3 なので、答えは次のようになります。```
inner
```これはタイブレーク ルールを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | 解析に 1 回のパス、ブロックを介して 1 回のパス |
 | スペース | O(n) | スタックと格納ブロック情報 |

 せいぜい$10^5$線形処理は簡単に十分に高速です。 メモリ使用量も直線的であり、制限内に快適に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
import re

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    n = int(input())

    stack = []
    blocks = []

    throw_pos = -1
    throw_type = ""

    catch_re = re.compile(
        r'catch\s*\(\s*([A-Za-z]+)\s*,\s*"([^"]+)"\s*\)'
    )
    throw_re = re.compile(
        r'throw\s*\(\s*([A-Za-z]+)\s*\)'
    )

    for i in range(n):
        line = input().strip()

        if not line:
            continue

        if line.startswith("try"):
            stack.append(i)

        elif line.startswith("throw"):
            m = throw_re.match(line)
            throw_type = m.group(1)
            throw_pos = i

        elif line.startswith("catch"):
            m = catch_re.match(line)
            exc_type = m.group(1)
            message = m.group(2)

            blocks.append((stack.pop(), i, exc_type, message))

    ans = None
    best = n + 1

    for l, r, typ, msg in blocks:
        if l < throw_pos < r and typ == throw_type:
            if r < best:
                best = r
                ans = msg

    return (ans if ans is not None else "Unhandled Exception") + "\n"

# provided sample
assert run(
"""8
try
    try
        throw ( AE )
    catch ( BE, "BE in line 3" )

    try
    catch(AE, "AE in line 5")
catch(AE,"AE somewhere")
"""
) == "AE somewhere\n"

# minimum size
assert run(
"""3
try
throw(AE)
catch(AE,"ok")
"""
) == "ok\n"

# no matching handler
assert run(
"""3
try
throw(AE)
catch(BE,"x")
"""
) == "Unhandled Exception\n"

# earliest catch wins
assert run(
"""5
try
try
throw(AE)
catch(AE,"inner")
catch(AE,"outer")
"""
) == "inner\n"

# spaces everywhere
assert run(
"""3
try
 throw (  ABC  )
 catch ( ABC , "works" )
"""
) == "works\n"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | シングルトライキャッチアラウンドスロー |`ok`| 最小の有効なインスタンス |
 | 例外タイプが一致しません |`Unhandled Exception`| ハンドラーが見つかりません |
 | ネストされた一致ハンドラー |`inner`| 最も早いキャッチの選択 |
 | 大量の空白のバリエーション |`works`| 堅牢な解析 |
 | 公式サンプル |`AE somewhere`| フルスペック |

 ## 特殊なケース

 ### 外側のブロックは一致するが、内側のブロックは一致しない

 入力:```
3
try
throw(AE)
catch(BE,"x")
```唯一のブロックにはスローが含まれていますが、そのタイプは`BE`一方、スローされる型は`AE`。 

アルゴリズムは 1 つのブロックを記録します。 

| 試してみる | キャッチ | タイプ |
 | --- | --- | --- |
 | 0 | 2 | ある |

 型の比較は失敗するため、候補は選択されず、出力は次のようになります。```
Unhandled Exception
```### 複数の一致するハンドラー

 入力:```
5
try
try
throw(AE)
catch(AE,"inner")
catch(AE,"outer")
```記録されたブロック:

 | 試してみる | キャッチ | タイプ |
 | --- | --- | --- |
 | 1 | 3 | ＡＥ |
 | 0 | 4 | ＡＥ |

 どちらにもスローが含まれており、両方ともタイプに一致します。 アルゴリズムは小さい方のキャッチ位置を選択します。`3`、生産:```
inner
```### スロー後のハンドラーですが、スロー後に試行が開始されます

 入力:```
5
throw(AE)
try
catch(AE,"later")
try
catch(AE,"another")
```どちらのブロックも次の条件を満たしていません。```
try_position < throw_position
```したがって、どちらも例外を処理できません。 

アルゴリズムはブロックと出力の両方を拒否します。```
Unhandled Exception
```これは言語定義と一致します。
