---
title: "CF 1383A - 文字列変換 1"
description: "a から t までの小文字の最初の 20 文字だけを使用した、同じ長さの 2 つの文字列 A と B が与えられています。 同じ文字 x をすべて含む A 内の位置のグループを繰り返し選択し、それらを厳密に大きな文字 y に置き換えることができます。"
date: "2026-06-11T10:48:08+07:00"
tags: ["codeforces", "competitive-programming", "dsu", "graphs", "greedy", "sortings", "strings", "trees", "two-pointers"]
categories: ["algorithms"]
codeforces_contest: 1383
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 659 (Div. 1)"
rating: 1700
weight: 1383
solve_time_s: 97
verified: true
draft: false
---

[CF 1383A - 文字列変換 1](https://codeforces.com/problemset/problem/1383/A)

 **評価:** 1700
 **タグ:** dsu、グラフ、貪欲、ソート、文字列、ツリー、2 つのポインター
 **解決時間:** 1 分 37 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 2 つの文字列が与えられています`A`そして`B`同じ長さで、最初の小文字 20 文字のみを使用します。`a`に`t`。 ポジションのグループを繰り返し選択することができます。`A`すべて同じ文字が含まれている`x`厳密に大きな文字に置き換えます`y`。 タスクは、変換に必要な最小移動数を決定することです。`A`の中へ`B`またはそれは不可能であると報告します。 

主な制限は、文字はアルファベット順にのみ進むことができるということです。 これは、どのポジションについても、`i`、`A[i] > B[i]`、それなら到達することは不可能です`B`文字数を減らすことができないからです。 たとえば、次の場合`A = "c"`そして`B = "a"`、一連の動きは変形できません`c`の中へ`a`、したがって、答えは次のとおりです`-1`。 

文字列の長さは最大です。`10^5`、すべてのテスト ケースの長さの合計も最大です。`10^5`。 これにより、次のようなアルゴリズムが除外されます。`O(n^2)`なぜなら、すべての位置にわたる単一のネストされたループでも、最大で`10^10`操作。 線形または線形に近いアプローチが必要です。 

明らかではないエッジケースには、複数の文字が含まれる状況が含まれます。`A`でターゲットに到達するには、中間の文字を通過する必要があります`B`。 たとえば、次の場合`A = "abb"`そして`B = "ccc"`、変身することはできません`b`直接に`c`すべてを変更する必要がある以前のステップがあった場合`a`に`b`s. 文字を任意の順序で変更する不用意な貪欲な実装では、最小限のシーケンス、さらには有効なシーケンスを見つけることができない可能性があります。 

## アプローチ

 素朴なアプローチは、繰り返しスキャンすることです`A`そしてあらゆるポジションについて、`A[i] != B[i]`、もたらすために有効な手を見つけようとします`A[i]`に近い`B[i]`。 不一致の位置をそれぞれ選択し、個別にまたは任意のグループで変更してみることもできます。 これは原理的には正確性を保つために機能しますが、各スキャンに時間がかかるため非効率的です。`O(n)`そして、私たちはまでパフォーマンスすることができました`O(n)`移動、与える`O(n^2)`が遅すぎる。 

重要な洞察は、次の最小文字にのみ注目すればよいということです。`A`対応するターゲットよりも小さい`B`。 文字ごとに`x`、最小値を見つけることができます`y > x`に登場する`B`ある位置で`A`もっている`x`。 次に、すべてのポジションが`A`を含む`x`そして到達する必要がある`y`以降にアップグレードできます`y`たった一度の動きで。 この移動を実行した後、再帰的に処理します`y`新しいものとして`x`次の反復で。 これにより、順序の制約に違反することなく、それぞれの動きが文字を最大限に進めることができ、ターゲットを「オーバーシュート」したり、不可能な状況を作り出したりすることがないことが保証されます。 

これにより、各文字を順番にスキャンするという問題が軽減されます。`a`に`t`そして各文字について、次の文字に到達するために必要な最小限のアップグレードのセットを計算します。`B`。 文字が 20 個しかなく、それぞれの位置を最大 1 回処理するため、アルゴリズムは次のように実行されます。`O(n)`時間。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^2) | O(n) | 遅すぎる |
 | 最適 | O(n * 20) ≈ O(n) | O(n + 20^2) ≈ O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各テスト ケースについて、最初にすべての位置をスキャンします。`i`かどうかを確認する`A[i] > B[i]`。 そうなった場合はすぐに元に戻してください`-1`変形は不可能だから`A`の中へ`B`。 
2. 文字ごとに`x`から`a`に`t`、すべてのターゲット文字のセットを構築します`y`で`B`そのような`y > x`そして`A[i] = x`。 これにより、次の文字が識別されます。`x`に進む必要があります。 
3. このセットが空の場合は、次の文字に移動します。 アクションは必要ありません`x`。 
4. それ以外の場合は、最小値を見つけます`y`このセットでは。 すべてのポジション`A[i] = x`そして`B[i] >= y`にアップグレードできます`y`たった一度の動きで。 この動きを記録します。 
5. すべての文字に対してステップ 2 ～ 4 を昇順で繰り返します。 各移動は制約を維持しながら厳密に文字を増やします`y > x`。 
6. 実行された合計移動数を数えます。 これにより、変換に必要な最小限の移動数が得られます。`A`の中へ`B`。 

なぜ機能するのか: 各ステップで、からの文字のみを増やします。`x`必要最小限に`y`。 文字を順番に処理するため、より大きなターゲットは後続の反復で処理されます。 文字が減少することはなく、各手は同時に複数のポジションの進歩を最大化し、最小の手数を保証します。 不変条件は、すべての文字を処理した後、`z`、すべての文字`<= z`すでに正しいか、将来の動きでターゲットに到達できる文字に設定されています。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def min_moves_to_transform(n, A, B):
    A = list(A)
    moves = 0
    for x_ord in range(20):  # letters 'a' to 't'
        x = chr(ord('a') + x_ord)
        targets = set()
        for i in range(n):
            if A[i] == x and B[i] != x:
                if B[i] < x:
                    return -1
                targets.add(B[i])
        if not targets:
            continue
        y = min(targets)
        for i in range(n):
            if A[i] == x and B[i] >= y:
                A[i] = y
        moves += 1
    return moves

t = int(input())
for _ in range(t):
    n = int(input())
    A = input().strip()
    B = input().strip()
    print(min_moves_to_transform(n, A, B))
```この関数はまず、何かを検出して不可能性をチェックします。`A[i] > B[i]`。 文字ごとに`x`、最小限のアップグレード文字を計算します`y`必要です。 更新中`A`in-place は、1 回の移動でアップグレードされた文字が次のように機能する可能性があるため、カスケード変換を正しく処理できるようにします。`x`後の反復で。 使用する`min(targets)`guarantees that each move progresses all relevant positions as little as necessary, minimizing the total number of moves.

 ## 実用的な例

 サンプル入力 1:```
A = "aab"
B = "bcc"
```| ステップ | 文字 x | 目標設定 | 選択されました | A | を更新しました。 
| --- | --- | --- | --- | --- |
 | 1 | | {b,c} | b | "bbb" |
 | 2 | b | {c} | c | "bcc" |
 | 3 | c | {} | - | "bcc" |

 出力: 2 移動。 The trace confirms that the algorithm efficiently upgrades groups of letters without violating the ordering constraint.

 サンプル入力 2:```
A = "cabc"
B = "abcb"
```ここで、位置 1 では、`A[0] = 'c' > B[0] = 'a'`、したがって関数はすぐに戻ります`-1`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n * 20) ≈ O(n) | 各文字 (合計 20) について、すべての n 位置を 1 回スキャンしてターゲットを収集し、更新を適用します。 
| スペース | O(n + 20) ≈ O(n) | 文字列を保存します`A`反復ごとのターゲット文字の小さなセット |

 与えられた`n ≤ 10^5`そして`t ≤ 10`, このアルゴリズムは、実際にはテスト ケースごとに最大 200 万回の操作を実行しますが、これは 1 秒の制限時間内に十分収まります。 メモリ使用量の大部分は文字列の保存であり、256 MB 以内に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    exec(open("solution.py").read())
    return output.getvalue().strip()

# Provided samples
assert run("5\n3\naab\nbcc\n4\ncabc\nabcb\n3\nabc\nts r\n4\naabd\ncccd\n5\nabcbd\nbcdda\n") == "2\n-1\n3\n2\n-1"

# Custom cases
assert run("1\n1\na\na\n") == "0", "single letter equal"
assert run("1\n1\na\nb\n") == "1", "single letter increase"
assert run("1\n3\naaa\nttt\n") == "1", "all letters same, one move suffices"
assert run("1\n5\naabcd\naabcd\n") == "0", "already equal strings"
assert run("1\n3\nabc\nabc\n") == "0", "no moves needed"
assert run("1\n3\nabc\nacb\n") == "-1", "impossible due to decrease"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1 | 0 | すでに等しい文字列なので、ゼロが移動します |
 | 2 | | |
