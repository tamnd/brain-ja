---
title: "CF 106169B - ノスタルジア"
description: "分析対象のプログラムは Scratch で書かれており、値を読み取り、変数に格納し、新しい値を計算し、結果を出力する単純なコマンドで構成されています。 初期のテスト中、プログラマはすべての入力操作の前に追加のデバッグ出力を使用しました。"
date: "2026-06-25T11:07:47+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 106169
codeforces_index: "B"
codeforces_contest_name: "2025-2026 ICPC NERC, Kyrgyzstan Regional Contest"
rating: 0
weight: 106169
solve_time_s: 37
verified: true
draft: false
---

[CF 106169B - ノスタルジア](https://codeforces.com/problemset/problem/106169/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 37 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 分析対象のプログラムは Scratch で書かれており、値を読み取り、変数に格納し、新しい値を計算し、結果を出力する単純なコマンドで構成されています。 初期のテスト中、プログラマはすべての入力操作の前に追加のデバッグ出力を使用しました。 その追加の出力は、入力を受け取ろうとしている単なる変数の名前であり、プログラムがどのような値を待っているかを理解するのに役立ちました。 

タスクは、これらの追加のデバッグ出力によって出力された文字数を計算することです。 プログラムの最終提出バージョンにはこれらの出力は含まれないため、入力操作に関連付けられた変数名のみが答えに寄与します。 

入力には、コマンド自体が後に続くプログラム行数が含まれます。 可能なすべてのコマンドのうち、特別な一時値を保存する行のみ`answer`変数への入力操作を表します。 このような行ごとに、宛先変数名の長さを結果に追加する必要があります。 

行数は 100000 に達する可能性があるため、ソリューションでは各行を一定の回数検査する必要があります。 Scratch プログラムをシミュレートするアプローチや、すべての変数値を評価しようとするアプローチは、不必要な作業を行うことになります。 答えは入力を受け取る変数の名前のみに依存するため、リニア スキャンで十分です。 

よくある間違いは、プログラム内に出現するすべての変数名を数えることです。 例えば：```
6
Ask read_token and wait
Set first to answer
Set result to first + second
Say result
Ask read_token and wait
Set second to answer
```正しい出力は次のとおりです。```
11
```のみ`first`そして`second`入力に使用され、貢献しました`5 + 6 = 11`。 数える`result`計算されただけの変数から文字を誤って追加していました。 

別のエッジ ケースが同じ変数に繰り返し入力されます。```
8
Ask read_token and wait
Set a to answer
Ask read_token and wait
Set a to answer
Set b to a + a
Say b
Ask read_token and wait
Set c to answer
```正しい出力は次のとおりです。```
3
```変数`a`2回貢献し、`c`は 1 回貢献するため、合計は`1 + 1 + 1 = 3`。 一意の変数名をカウントする不注意な実装では、次のような結果が返されます。`2`、これは間違いです。 

## アプローチ

 簡単なアプローチは、Scratch プログラムのすべてのコマンドを理解し、変数を追跡し、実行をシミュレートすることです。 これは元のプログラムの動作を正確に従っているため、これは正しいと考えられます。 ただし、必要以上に難しい問題を解決します。 変数に格納された値、算術演算、および出力結果は、答えに影響を与えることはありません。 すべての操作をシミュレートすると、実装では無関係な情報の維持に時間がかかってしまいます。 

重要な観察は、追加の出力が入力の直前にのみ発生したということです。 このステートメントは、すべての入力リクエストが、からの 1 つの割り当てと正確にペアになることを保証します。`answer`、そのような割り当てはすべて入力リクエストの後に続きます。 これはフォームのすべての行を意味します`Set name to answer`テスト中に出力された 1 つの変数名を直接明らかにします。 

問題は文字列処理に帰着します。 これらの代入行を認識し、その後に表示される変数名の長さを追加するだけで済みます。`Set`。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | 不必要なシミュレーション作業による O(n) | O(変数の数) | 正しいが複雑すぎる |
 | 最適 | O(n) | O(1) 余分なスペース | 承認済み |

 ## アルゴリズムのチュートリアル

 1. Scratch プログラムの行数を読み取り、コマンドを 1 つずつ処理します。 すべての行を独立して検査できるため、プログラム全体を保存する必要はありません。 
2. を表すすべての行について`Set name to answer`、 抽出する`name`そしてその長さを答えに追加します。 これは、追加のローカル テスト出力に対応する唯一のコマンド タイプです。 
3. 他のコマンドはすべて、内部で値を操作するか、最終的なプログラム出力を表示するため、無視します。 余分なデバッグ文字は作成されません。 
4. 累計を印刷します。 

なぜ効果があるのか:

 スキャン中に維持される不変条件は、プログラムの接頭辞を処理した後、保存された回答がその接頭辞内のすべての入力操作によって生成された余分な文字の数に等しいということです。 ラインは格納する場合にのみ貢献します。`answer`その場合、ローカル テストの出力はまさにその変数名になります。 このような行はすべて 1 回カウントされ、他の行は寄与できません。 完全なスキャンの後、不変式により必要な合計が得られます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())
    ans = 0

    for _ in range(n):
        line = input().strip()
        if line.startswith("Set ") and line.endswith(" to answer"):
            name = line[4:line.index(" to answer")]
            ans += len(name)

    print(ans)

if __name__ == "__main__":
    solve()
```コードは各 Scratch コマンドを 1 回だけ読み取ります。 この条件は、入力割り当てを識別する両方の部分をチェックします。コマンドは次で始まる必要があります。`Set`、割り当てられる値は予約語である必要があります`answer`。 

変数名は固定プレフィックスの間にあります。`Set `そして接尾辞` to answer`。 接尾辞の位置を使用すると、可変長に依存することが回避され、すべての有効な名前が正しく処理されます。 

前の行からの情報は必要ないため、実装ではコマンドや変数は保存されません。 整数の答えは次のようになります。`10^9`、Python はオーバーフローを考慮せずに処理します。 

## 実用的な例

 ### 例 1

 入力:```
6
Ask read_token and wait
Set first to answer
Ask read_token and wait
Set second to answer
Set result to first + second
Say result
```| ステップ | コマンド | 認識された入力変数 | 現在の答え |
 | --- | --- | --- | --- |
 | 1 | read_token に問い合わせて待ちます | なし | 0 |
 | 2 | 最初に回答するように設定します | 最初 | 5 |
 | 3 | read_token に問い合わせて待ちます | なし | 5 |
 | 4 | 2 番目に回答するように設定します | 2番目 | 11 |
 | 5 | 結果を最初 + 2 番目に設定 | なし | 11 |
 | 6 | 結果を言う | なし | 11 |

 トレースは、ユーザー入力を受け取る変数のみが重要であることを示しています。 次のような計算変数`result`無視されます。 

### 例 2

 入力:```
15
Ask read_token and wait
Set a to answer
Ask read_token and wait
Set bb to answer
Set ccc to a * bb
Set dddd to a / bb
Set dddd to ccc + dddd
Say dddd
Ask read_token and wait
Set bb to answer
Ask read_token and wait
Set ccc to answer
Set x to bb * dddd
Set x to ccc - x
Say x
```| ステップ | コマンド | 認識された入力変数 | 現在の答え |
 | --- | --- | --- | --- |
 | 1 | read_token に問い合わせて待ちます | なし | 0 |
 | 2 | を設定して答える | | 1 |
 | 3 | read_token に問い合わせて待ちます | なし | 1 |
 | 4 | bb を応答するように設定します | BB | 3 |
 | 5 | ccc を a * bb | に設定します。 なし | 3 |
 | 6 | dddd を a / bb に設定します | なし | 3 |
 | 7 | dddd を ccc + dddd に設定します | なし | 3 |
 | 8 | ああ、と言う | なし | 3 |
 | 9 | read_token に問い合わせて待ちます | なし | 3 |
 | 10 | bb を応答するように設定します | BB | 5 |
 | 11 | read_token に問い合わせて待ちます | なし | 5 |
 | 12 | ccc を応答するように設定します | CCC | 8 |
 | 13 | x を bb * dddd に設定します | なし | 8 |
 | 14 | x を ccc - x に設定します | なし | 8 |
 | 15 | ×と言う | なし | 8 |

 この例は、繰り返される変数が入力を受け取るたびにカウントされることを示しています。 変数名自体はカウントされる単位ではなく、入力イベントがカウントされます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | すべてのプログラム行が 1 回チェックされます。 |
 | スペース | お(1) | 現在の行と累積された回答のみが保存されます。 |

 制約により最大 100,000 ラインが許可されるため、リニア スキャンは必要な制限内に簡単に収まります。 このソリューションでは、文字列のチェックと長さの追加のみが実行され、高価なデータ構造は実行されません。 

## テストケース```python
import sys
import io

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    solve()

    result = sys.stdout.getvalue()
    sys.stdin = old_stdin
    sys.stdout = old_stdout
    return result

# provided sample 1
assert run("""6
Ask read_token and wait
Set first to answer
Ask read_token and wait
Set second to answer
Set result to first + second
Say result
""") == "11\n", "sample 1"

# provided sample 2
assert run("""15
Ask read_token and wait
Set a to answer
Ask read_token and wait
Set bb to answer
Set ccc to a * bb
Set dddd to a / bb
Set dddd to ccc + dddd
Say dddd
Ask read_token and wait
Set bb to answer
Ask read_token and wait
Set ccc to answer
Set x to bb * dddd
Set x to ccc - x
Say x
""") == "8\n", "sample 2"

# minimum number of lines
assert run("""2
Ask read_token and wait
Set x to answer
""") == "1\n", "single character variable"

# repeated input to the same variable
assert run("""6
Ask read_token and wait
Set abc to answer
Ask read_token and wait
Set abc to answer
Say abc
Say abc
""") == "6\n", "repeated inputs"

# many non-input commands
assert run("""5
Set a to answer
Set b to a + a
Set c to b * b
Say c
Say a
""") == "1\n", "only answer assignments count"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | への単一入力`x`| 1 | 最小の可変長の処理 |
 | への 2 つの入力`abc`| 6 | 繰り返しの発生は個別にカウントされます。 
| 1 回の入力後にのみ算術演算を行うプログラム | 1 | 入力以外の割り当ては無視されます。 
| 提供されたサンプル | 11と8 | 公式の例と一致します |

 ## 特殊なケース

 プログラムには多くの変数名が含まれる場合がありますが、影響を与えるのは入力変数のみです。 入力の場合:```
6
Ask read_token and wait
Set first to answer
Set result to first + first
Say result
Ask read_token and wait
Set second to answer
```アルゴリズムが認識するのは`first`そして`second`、生産`5 + 6 = 11`。 関係する算術行`result`が含まれていないためスキップされます`answer`。 

繰り返し入力する場合は、入力が発生するたびにカウントする必要があります。 のために：```
8
Ask read_token and wait
Set a to answer
Ask read_token and wait
Set a to answer
Set b to a + a
Say b
Ask read_token and wait
Set c to answer
```スキャンにより追加される`1`の発生ごとに`a`そして`1`のために`c`、与える`3`。 アルゴリズムは変数名のセットを維持するのではなくコマンドを処理するため、これを自然に処理します。 

たまたま変数に言及していても、入力割り当てではないコマンドは無視されます。 次のような行`Set value to other + value`はプログラムの状態を変更しますが、その前に追加のデバッグ出力は生成されないため、答えに影響を与えることはありません。
