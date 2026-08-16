---
title: "CF 102386B - \u0422\u0443\u0440\u043d\u0438\u0440 \u0423\u0440\u0424\u0423"
description: "ジャンケン・トカゲ・スポックを1ラウンド判定する必要があります。 最初の入力ラインは最初のプレイヤーが選択した手で、2 番目のラインは 2 番目のプレイヤーが選択した手です。 各動きは、じゃんけん、紙、トカゲ、またはスポックのいずれかです。"
date: "2026-08-15T18:36:15+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102386
codeforces_index: "B"
codeforces_contest_name: "\u041a\u0432\u0430\u043b\u0438\u0444\u0438\u043a\u0430\u0446\u0438\u043e\u043d\u043d\u044b\u0439 \u0442\u0443\u0440 \u0423\u0440\u0430\u043b\u044c\u0441\u043a\u043e\u0433\u043e \u0447\u0435\u0442\u0432\u0435\u0440\u0442\u044c\u0444\u0438\u043d\u0430\u043b\u0430 \u0427\u0435\u043c\u043f\u0438\u043e\u043d\u0430\u0442\u0430 \u043c\u0438\u0440\u0430 \u043f\u043e \u043f\u0440\u043e\u0433\u0440\u0430\u043c\u043c\u0438\u0440\u043e\u0432\u0430\u043d\u0438\u044e 2019"
rating: 0
weight: 102386
solve_time_s: 378
verified: false
draft: false
---

[CF 102386B - \u0422\u0443\u0440\u043d\u0438\u0440 \u0423\u0440\u0424\u0423](https://codeforces.com/problemset/problem/102386/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 6 分 18 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 ジャンケン・トカゲ・スポックを1ラウンド判定する必要があります。 最初の入力ラインは最初のプレイヤーが選択した手で、2 番目のラインは 2 番目のプレイヤーが選択した手です。 それぞれの動きは次のいずれかです`Rock`、`Scissors`、`Paper`、`Lizard`、 または`Spock`。 

すべての動きは、他の 2 つの動きを正確に打ち負かします。`Scissors`敗北`Paper`そして`Lizard`、`Paper`敗北`Rock`そして`Spock`、`Rock`敗北`Lizard`そして`Scissors`、`Lizard`敗北`Spock`そして`Paper`、 そして`Spock`敗北`Scissors`そして`Rock`。 両方のプレイヤーが同じ手を選択した場合、結果は引き分けになります。 

プログラムは印刷する必要があります`First`最初の手が二番目の手を倒すとき、`Second`その逆が真の場合、そして`Tie`動きが等しいとき。 

ここには可変サイズの入力はありません。 正確に 2 つの文字列が読み取られ、それぞれが 5 つの可能な値の固定セットに属します。 したがって、可能なすべての手のペアを明示的に考慮するメソッドでも、最大 25 回の比較が実行されます。 意味のある大きなものはありません`n`この問題ではパフォーマンスの問題があるため、O(1) ソリューションで十分であり、通常の Codeforce 制限に簡単に適合します。 

主なエッジケースは、ゲームを通常のジャンケンとして扱ったり、すべての動きに 2 人の勝者がいるということを忘れたりすることによって発生します。 例えば、```
Rock
Rock
```生産しなければならない`Tie`。 最初の手が 2 番目の手を上回るかどうかだけをチェックする不注意な実装は、次のような失敗につながる可能性があります。`Second`最初に平等を扱うのではなく。 

もう一つのケースは、```
Lizard
Spock
```生み出すのは`First`。 どちらの技も通常のじゃんけんの標準的な 3 つの選択肢には属しませんでしたが、リザードはスポックを破りました。 古典的な 3 つの関係のみを含む実装では、誤った結果が得られます。 

3 番目の有用な境界ケースは次のとおりです。```
Spock
Paper
```生み出すのは`Second`、ペーパーがスポックを倒すからです。 各手ごとに 2 つの勝ちの関係のうち 1 つだけをチェックすると、このケースは見逃されます。 

## アプローチ

 直接的なブルート フォース ソリューションでは、25 個の順序付きの動きのペアをすべて明示的に列挙し、各ペアをその結果に関連付けることができます。 各プレイヤーの可能な手は 5 つだけであるため、最悪の場合はちょうど 25 のペア チェックになります。 25 は入力サイズに依存しない定数であるため、このアプローチはすでに十分に高速です。 この特定のブルートフォース手法が遅くなりすぎる入力サイズはありません。 

より自然な実装では、ゲーム自体の構造を使用します。 10 個の勝ちの方向関係を保存し、最初のプレーヤーの手が 2 番目のプレーヤーの手を打ち負かす手の 1 つであるかどうかを確認します。 その場合、最初のプレイヤーが勝ちとなります。 それ以外の場合、動きが等しい場合、結果は引き分けになります。 ルールでは異なる手のペアごとに勝者が定義されているため、残りのペアはすべて 2 番目のプレーヤーの勝利を意味する必要があります。 

重要な観察は、ゲーム全体が頂点が 5 つだけある固定グラフであるということです。 それぞれの動きは頂点であり、そこからのエッジです。`A`に`B`ということです`A`敗北`B`。 このグラフを検索したり、動的に何かを構築したりする必要はありません。 一定サイズのルックアップ構造は、考えられるすべての勝利関係を直接表します。 

2 つのアプローチには次のような複雑さがあります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 可能な 25 のペアをすべて列挙する | お(1) | お(1) | 承認済み |
 | 勝敗関係検索 | お(1) | お(1) | 承認済み |

 ルックアップのアプローチは、ルールを直接表し、特殊なケースの長い連鎖を回避するため、推奨されます。 

## アルゴリズムのチュートリアル

 1. 2 つの動きを読みます。`first`そして`second`。 入力行はちょうど 2 つあるため、テストケース ループは必要ありません。 
2. もし`first == second`、印刷`Tie`。 どの手であっても、同じ手同士が負けることはありません。 
3. 可能な各技によって倒された 2 つの技を保存します。 例えば、`Rock`に関連付けられています`Lizard`そして`Scissors`、 その間`Spock`に関連付けられています`Rock`そして`Scissors`。 
4. かどうかを確認します。`second`に負けた一連の動きに属します`first`。 存在する場合は、印刷してください`First`。 
5. 動きが異なり、最初の動きが 2 番目の動きに勝てない場合は、次のコマンドを出力します。`Second`。 すべての異なるペアには勝者が 1 人だけ存在するため、考慮すべき 4 番目の結果はありません。 

### なぜ効果があるのか

 あらゆる動きに対して`A`、ルックアップ構造には、次の 2 つの手が正確に含まれています。`A`ゲームルールに従って負けます。 平等チェックの後、2 人のプレーヤーは明確な動きをします。 2 番目の手が最初の手の勝ちセットに現れた場合、ルールでは最初のプレイヤーが勝ちとなります。 そうしないと、最初のプレーヤーが 2 番目のプレーヤーに勝つことができず、すべてのペアに勝者がいるため、2 番目のプレーヤーが勝たなければなりません。 したがって、考えられるすべての入力は正確に正しい結果に達します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

first = input().strip()
second = input().strip()

wins = {
    "Rock": {"Lizard", "Scissors"},
    "Scissors": {"Paper", "Lizard"},
    "Paper": {"Rock", "Spock"},
    "Lizard": {"Spock", "Paper"},
    "Spock": {"Scissors", "Rock"},
}

if first == second:
    print("Tie")
elif second in wins[first]:
    print("First")
else:
    print("Second")
```辞書`wins`ゲームグラフを完全に表現したものです。 各キーは最初のプレーヤーの可能な 1 つの動きであり、その値には、それが打ち負かした 2 つの手が正確に含まれます。 

等価性には独自の結果があるため、等価性チェックは優先ルックアップの前に行われます。`Tie`。 このチェックがないと、等しいペアが誤って分類されてしまいます。`Second`場合。 

表現`second in wins[first]`最初のプレイヤーが勝つために必要な条件を正確にチェックします。 すでに手が異なることが示された後でそれが偽の場合、必然的に 2 番目のプレーヤーが勝ちます。 

ここにはインデックス、入力データのループ、算術演算がないため、境界や整数オーバーフローの問題はありません。 の`.strip()`呼び出しは、によって生成された改行文字を削除します。`readline()`正確な技名を維持しながら。 

## 実用的な例

 ### サンプル 1

 入力は次のとおりです。```
Rock
Paper
```関連する状態変化は次のとおりです。 

| ステップ |`first`|`second`| 状態 | 結果 |
 | --- | --- | --- | --- | --- |
 | 入力を読み取る |`Rock`|`Paper`| 両方の動きが保存されました | 続ける |
 | 等価性チェック |`Rock`|`Paper`|`first == second`は偽です | 続ける |
 | 優勝ルックアップ |`Rock`|`Paper`|`Paper`に負けていない`Rock`|`Second`|`Rock`敗北`Lizard`そして`Scissors`、 ない`Paper`。 手が異なるため、残った勝者は後手のプレイヤーだけです。 プログラムは印刷します`Second`。 

### サンプル 2

 入力は次のとおりです。```
Rock
Rock
```トレースは次のとおりです。 

| ステップ |`first`|`second`| 状態 | 結果 |
 | --- | --- | --- | --- | --- |
 | 入力を読み取る |`Rock`|`Rock`| 両方の動きが保存されました | 続ける |
 | 等価性チェック |`Rock`|`Rock`|`first == second`それは本当です |`Tie`|

 ルックアップは決して必要ありません。 これは、勝利関係をチェックする前に平等性を処理する必要がある理由を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | お(1) | 2 つの文字列のみが読み取られ、一定サイズの検索が 1 回実行されます。 |
 | スペース | お(1) | 辞書には、ちょうど 5 つのキーと 10 の勝利関係が含まれています。 |

 入力サイズは 5 要素セットからの 2 手に固定されているため、アルゴリズムは一定数の演算のみを実行し、一定量のメモリを使用します。 問題の時間とメモリの制限に快適に適合します。 

## テストケース```python
import sys
import io

def solve():
    first = input().strip()
    second = input().strip()

    wins = {
        "Rock": {"Lizard", "Scissors"},
        "Scissors": {"Paper", "Lizard"},
        "Paper": {"Rock", "Spock"},
        "Lizard": {"Spock", "Paper"},
        "Spock": {"Scissors", "Rock"},
    }

    if first == second:
        return "Tie"
    if second in wins[first]:
        return "First"
    return "Second"

def run(inp: str) -> str:
    global input
    old_stdin = sys.stdin
    old_input = input

    try:
        sys.stdin = io.StringIO(inp)
        input = sys.stdin.readline
        return solve()
    finally:
        sys.stdin = old_stdin
        input = old_input

# Provided samples
assert run("Rock\nPaper\n") == "Second", "sample 1"
assert run("Rock\nRock\n") == "Tie", "sample 2"
assert run("Lizard\nSpock\n") == "First", "sample 3"

# All equal values
assert run("Spock\nSpock\n") == "Tie", "equal moves"

# Reverse direction of a winning relationship
assert run("Paper\nRock\n") == "First", "Paper defeats Rock"
assert run("Rock\nPaper\n") == "Second", "Paper defeats Rock"

# Second winning relationship of a move
assert run("Spock\nRock\n") == "Second", "Rock defeats Spock"
assert run("Spock\nScissors\n") == "First", "Spock defeats Scissors"

# Lizard's two different winning relationships
assert run("Lizard\nPaper\n") == "First", "Lizard defeats Paper"
assert run("Paper\nLizard\n") == "Second", "Lizard defeats Paper"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`Spock / Spock`|`Tie`| 別の動きに対する等価処理 |
 |`Paper / Rock`|`First`| 勝利の関係の一方向 |
 |`Spock / Rock`|`Second`| 後手のプレイヤーが先手の相手に勝つ |
 |`Lizard / Paper`|`First`| Lizard の 2 番目の勝ち点 |
 |`Paper / Lizard`|`Second`| 同じ関係を逆転する |

 この問題には、実際には個別の最小サイズまたは最大サイズのパラメーターはありません。 各テスト ケースには常に 2 つの手が正確に含まれるため、関連する境界は 5 つの可能な値の完全なセットになります。 上記のテストは、等しい動きやいくつかの関係の両方向を含む、すべての構造的なケースをカバーしています。 

## 特殊なケース

 最初のエッジケースは平等です。 のために```
Rock
Rock
```アルゴリズムは両方の手を読み取り、次のことを検出します。`first == second`、すぐに戻ります`Tie`。 治療しようとするものではありません`Rock`自己比較はゲームルールによって除外されているため、自分自身に勝つことと同じです。 

2 番目のエッジケースは、勝つための 2 つの異なる方法がある動きです。 考慮する```
Lizard
Spock
```の辞書項目`Lizard`は`{Spock, Paper}`。 以来`Spock`存在する、状態`second in wins[first]`は true であり、結果は`First`。 これにより、Lizard の 2 つの勝利関係のうち 1 つだけを記憶する実装が捕捉されます。 

3 番目のエッジケースは逆ペアです```
Paper
Lizard
```のエントリー`Paper`は`{Rock, Spock}`、 それで`Lizard`は存在しません。 手が等しくないため、アルゴリズムは最後の分岐に到達し、出力します。`Second`。 これは、関係に方向性があり、無向の接続として扱うことができないことを確認します。 

4 番目の特殊なケースは、スポックとロックのあまり目立たないやり取りです。```
Spock
Rock
```

`Rock`に登場する`wins["Spock"]`、アルゴリズムは次のように出力します`First`。 入力を反転すると、```
Rock
Spock
```ルックアップが失敗し、アルゴリズムが出力します。`Second`。 これら 2 つの入力により、すべての関係の方向が正しく解釈されていることを確認できます。
