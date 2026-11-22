---
title: "CF 409B - 謎の言語"
description: "これは Codeforces のエイプリル フール問題の 1 つです。 実際のアルゴリズム入力はありません。 裁判官は、カスタム呼び出しシステムを通じて「シークレット」と呼ばれる特別な言語を提供します。 タスクは、その言語が実際に何であるかを特定し、その言語で書かれたプログラムを提出することです。"
date: "2026-06-07T02:00:20+07:00"
tags: ["codeforces", "competitive-programming", "*special"]
categories: ["algorithms"]
codeforces_contest: 409
codeforces_index: "B"
codeforces_contest_name: "April Fools Day Contest 2014"
rating: 1700
weight: 409
solve_time_s: 238
verified: true
draft: false
---

[CF 409B - Mysterious Language](https://codeforces.com/problemset/problem/409/B)

 **評価:** 1700
 **タグ:** *特別な
 **解決時間:** 3 分 58 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 これは Codeforces のエイプリル フール問題の 1 つです。 実際のアルゴリズム入力はありません。 

The judge provides a special language called "Secret" through the custom invocation system. タスクは、その言語が実際に何であるかを特定し、その言語で書かれたプログラムを提出することです。 When executed, the program must print the exact name of the language.

 入力が空です。 何も読む必要はありません。 

The output is a single fixed string, the name of the mysterious language.

 この挑戦は計算的なものではありません。 The intended difficulty comes from discovering which language "Secret" corresponds to. The accepted answer for this problem is **FORTRAN 77**.

 Since there is no input, there are no numerical constraints, no complexity concerns, and no data structures involved.

 唯一の本当のエッジケースは出力フォーマットです。 The answer is case-sensitive and contains a space and digits.

 たとえば、次のように印刷します。```
Fortran 77
```大文字と小文字が異なるので間違っています。 

同様に:```
FORTRAN77
```必要なスペースが不足しているため、これは間違っています。 

正しい出力は次のとおりです。```
FORTRAN 77
```## アプローチ

 通常の競技プログラミングの問題では、入力を処理し、何らかの結果を計算する必要があります。 ここでは計算はまったく行われません。 

強引な観点としては、言語の特徴を実験し、裁判官がどのように行動するかを観察することによって、隠された言語を特定しようとすることです。 それがコンテスト中に意図されたパズルでした。 言語が FORTRAN 77 として認識されると、解決策は簡単になります。 

重要な観察は、裁判官がアルゴリズム ロジックをまったく評価していないということです。 謎の言語で書かれた送信されたプログラムがその言語の名前を出力するかどうかだけをチェックします。 

「Secret」が FORTRAN 77 であることが判明すると、問題全体は固定文字列の出力に帰着します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース言語調査 | 意味がない | 意味がない | コンテスト発見プロセス |
 | 固定回答を出力 | お(1) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 裁判官が提供した隠し言語が FORTRAN 77 であると判断します。 
2. FORTRAN 77 プログラムを作成します。 
3. 正確な文字列を出力します`"FORTRAN 77"`。 
4. プログラムを終了します。 

### なぜ効果があるのか

 裁判官は謎の言語の名前を予想しています。 隠し言語は FORTRAN 77 なので、正確に印刷されます。`"FORTRAN 77"`必要な出力と一致します。 入力や追加のロジックがないため、この正確な文字列を出力するプログラムはすべて受け入れられます。 

## Python ソリューション

 元の問題は FORTRAN 77 で提出する必要がありました。この編集は標準の競技プログラミング テンプレートに従っているため、同等の Python 表現は次のようになります。```python
import sys
input = sys.stdin.readline

print("FORTRAN 77")
```テスト ファイルが空であるため、ソリューションには入力処理が含まれません。 

実行される操作は、必要な文字列を出力することだけです。 ループ、条件、計算はありません。 

最も一般的な間違いは、大文字と小文字を変更したり、間のスペースを省略したりすることです。`FORTRAN`そして`77`。 

## 実用的な例

 入力は常に空であるため、すべての実行は同じトレースに従います。 

### 例 1

 入力:```
<empty>
```| ステップ | アクション | 出力 |
 | --- | --- | --- |
 | 1 | プログラムを開始 | |
 | 2 | 答えを印刷する | フォートラン77 |
 | 3 | 終了 | フォートラン77 |

 トレースは、入力が消費されず、修正された答えがすぐに生成されることを示しています。 

### 例 2

 入力:```
<empty>
```| ステップ | アクション | 出力 |
 | --- | --- | --- |
 | 1 | プログラムを開始 | |
 | 2 | 答えを印刷する | フォートラン77 |
 | 3 | 終了 | フォートラン77 |

 これは、データが存在しないため、プログラムの動作がデータから独立していることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | お(1) | 1 回の印刷操作 |
 | スペース | お(1) | 一定のメモリ使用量 |

 このソリューションは単一の定数時間出力操作を実行するため、時間とメモリの制限内に簡単に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

def solve():
    print("FORTRAN 77")

def run(inp: str) -> str:
    backup_stdin = sys.stdin
    backup_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out

    solve()

    sys.stdin = backup_stdin
    sys.stdout = backup_stdout

    return out.getvalue()

# provided sample
assert run("") == "FORTRAN 77\n", "sample 1"

# custom cases
assert run("") == "FORTRAN 77\n", "empty input"
assert run("\n") == "FORTRAN 77\n", "extra newline ignored"
assert run("random text\n") == "FORTRAN 77\n", "input is irrelevant"
assert run("1 2 3\n4 5 6\n") == "FORTRAN 77\n", "still fixed output"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 空の入力 | フォートラン77 | 公式シナリオ |
 | 単一の改行 | フォートラン77 | 入力内容に依存しない |
 | ランダムなテキスト | フォートラン77 | 出力は固定です |
 | 複数行 | フォートラン77 | プログラムはすべての入力を無視します |

 ## 特殊なケース

 最初の微妙なケースは大文字化です。 

入力：```
<empty>
```ソリューションが出力される場合:```
Fortran 77
```必要な出力では大文字と小文字が区別されるため、拒否されます。 アルゴリズムは、正確な定数を出力することでこれを処理します。```
FORTRAN 77
```2 番目の微妙なケースはスペースです。 

入力：```
<empty>
```印刷：```
FORTRAN77
```は、予想される答えに単語と数字の間にスペースが含まれているため、不正解です。 このソリューションでは、受け入れられた文字列を正確にハードコーディングして、書式設定の間違いを回避します。 

最後のエッジケースは、入力が存在することを前提としています。 

入力：```
<empty>
```必須データを読み取ろうとするプログラムは失敗するか、予期しない動作をする可能性があります。 受け入れられた解決策は入力処理を実行せず、問題の仕様に一致する回答を直接出力します。
