---
title: "CF 259A - 小さな象とチェス"
description: "8×8 のボードが与えられ、各セルは白 (W) または黒 (B) になります。 目標は、このボードを標準のチェス盤に変換できるかどうかを判断することです。 標準的なチェス盤には、水平方向と垂直方向に色が交互に配置されています。"
date: "2026-06-04T17:39:39+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "strings"]
categories: ["algorithms"]
codeforces_contest: 259
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 157 (Div. 2)"
rating: 1000
weight: 259
solve_time_s: 155
verified: true
draft: false
---

[CF 259A - 小さな象とチェス](https://codeforces.com/problemset/problem/259/A)

 **評価:** 1000
 **タグ:** ブルート フォース、文字列
 **解決時間:** 2 分 35 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 8×8 のボードが与えられ、各セルは白 (`W`) または黒 (`B`）。 目標は、このボードを標準のチェス盤に変換できるかどうかを判断することです。 

標準的なチェス盤には、水平方向と垂直方向に色が交互に配置されています。 左上隅は白である必要があるため、有効な行パターンは 2 つだけです。```
WBWBWBWB
BWBWBWBW
```行はこれら 2 つのパターンの間で交互に配置する必要があります。 

許可される唯一の操作は、行を選択し、循環右シフトを何度でも実行することです。 行にはちょうど 8 つのセルが含まれるため、シフトを繰り返すと行が 8 つの循環位置のいずれかに回転される可能性があります。 

ボードサイズは8×8に固定されています。 つまり、徹底的な検索でも小規模なものになるということです。 行は 8 つしかなく、各行の長さは 8 なので、すべてのセルを数回検査するアルゴリズムは簡単に十分に高速です。 課題は効率ではなく、循環シフトによって生み出される構造を認識することです。 

微妙な点は、交互の行を 1 位置ずつ移動すると、2 つの有効なチェス盤パターン間で行が単純に入れ替わるだけであるということです。 例えば：```
WBWBWBWB  -> shift once ->  BWBWBWBW
```2 位置ずらすと元のパターンに戻ります。 行の長さは偶数であるため、交互行の循環シフトごとに、これら 2 つのパターンのうちの 1 つが生成され、他は何も生成されません。 

メインのエッジ ケースは、内部で交互になっていない行です。 

例：```
WWWBWBWB
```どれだけ巡回シフトを行っても、隣接する等しいものを削除することはできません。`W`s. どの回転でも、行のどこかに隣接する等しいセルのペアが含まれます。 正しい答えは、`NO`。 

もう 1 つの簡単な間違いは、各行が独立していずれかの有効な交互パターンになり得るかどうかだけをチェックすることです。 

例：```
WBWBWBWB
WBWBWBWB
WBWBWBWB
WBWBWBWB
WBWBWBWB
WBWBWBWB
WBWBWBWB
WBWBWBWB
```すべての行は個別に有効ですが、適切なチェス盤では隣接する行が異なる必要があります。 正解はまだ`YES`というのは、2 行ごとに 1 回シフトして、交互の行シーケンスを取得できるからです。 行を現在の位置と比較するだけのソリューションでは、このケースは誤って拒否されます。 

最後のエッジ ケースは、すでに適切なチェス盤を形成しているボードです。 答えは次のとおりです。`YES`ゼロ演算の使用が許可されているためです。 

## アプローチ

 最も直接的な総当たりのアイデアは、すべての行に対して可能なすべての循環シフトを試し、その結果得られるボードが有効なチェス盤であるかどうかを確認することです。 

各行には 8 つの可能な回転があるため、次のようになります。$$8^8 = 16,777,216$$possible configurations. Even though the board is small, checking more than sixteen million states is unnecessary.

 The key observation comes from studying a single row. 有効なチェス盤に行を表示するには、その行の色が交互になる必要があります。 Since the row length is even, every cyclic shift preserves the alternating property. More importantly, an alternating row can only rotate into one of two patterns:```
WBWBWBWB
BWBWBWBW
```行が交互になっていない場合、どの回転を行っても行が交互になることはありません。 

これにより問題が大幅に軽減されます。 考えられるすべてのシフトを考慮するのではなく、すべての行が内部的に交互していることを確認するだけで済みます。 これが真になると、各行を 2 つのチェス盤の行パターンのうち必要な方に回転させることができます。 

ターゲットのチェス盤の奇数行の場合、次のものが必要です。```
WBWBWBWB
```偶数行の場合は以下が必要です。```
BWBWBWBW
```シフトの適切なパリティを選択することで、すべての交互行をいずれかのパターンに変換できるため、すべての行が交互である場合に限り、ボード全体が実行可能になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(8^8 · 64) | お(64) | 不要 |
 | 最適 | お(64) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. ボードの 8 行を読み取ります。 
2. 各行について、その行内の隣接するセルのすべてのペアを調べます。 
3. 隣接するセルに同じ色がある場合は、すぐに出力します。`NO`。 

チェス盤の行は色を交互に変える必要があります。 循環シフトは位置を回転するだけであり、循環中の色の順序は変更しません。 サイクル内の 2 つの隣接するセルが等しい場合、その欠陥は決して消えることはありません。 
4. すべての行が交互チェックに合格した場合、出力`YES`。 

すべての行が交互のサイクルになります。 行の長さは偶数であるため、行を回転すると次のいずれかが生成されます。`WBWBWBWB`または`BWBWBWBW`。 各行に必要なバージョンを個別に選択し、標準のチェス盤を構築できます。 

### なぜ効果があるのか

 行を 8 つのセルのサイクルとして考えてください。 循環シフトは、そのサイクルの開始位置を変更するだけです。 

行にサイクル内のどこかに 2 つの等しい隣接セルが含まれている場合、すべての回転には依然として同じ同じペアが含まれます。 このような行は、交互のチェス盤の行になることはありません。 

逆に、サイクル内のすべての隣接するペアが異なる色を持つ場合、行は完全に交互になる必要があります。 周期の長さが偶数であるため、行は交互に構成されます。`W`そして`B`サイクル全体にわたって。 このような行の回転はどちらの色からでも開始でき、正確に 2 つのチェス盤の行パターンが生成されます。 

各行は独立して必要な交互パターンになることができるため、すべての行が交互になったときにボード全体を正確に適切なチェス盤に配置できます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    board = [input().strip() for _ in range(8)]

    for row in board:
        for i in range(7):
            if row[i] == row[i + 1]:
                print("NO")
                return

    print("YES")

if __name__ == "__main__":
    solve()
```実装は証明の後に直接続きます。 

外側のループは 8 行すべてを処理します。 各行について、行内の 7 つの隣接するペアをチェックします。 いずれかのペアに同じ色が含まれている場合、その行は循環シフトのシーケンスによって交互のチェス盤の行に変換できないため、すぐに出力します。`NO`。 

すべての行がテストに合格した場合、各行はすでに交互のシーケンスになっています。 このような行は、いずれかの色から開始するように回転させることができ、これにより、ボードの各行に必要なパターンを配置することができます。 次に印刷します`YES`。 

よくある間違いは、シフトをシミュレーションしようとすることです。 この証明は、シミュレーションが不要であることを示しています。 重要な唯一のプロパティは、すべての行が交互になるかどうかです。 

## 実用的な例

 ### サンプル 1

 入力:```
WBWBWBWB
BWBWBWBW
BWBWBWBW
BWBWBWBW
WBWBWBWB
WBWBWBWB
BWBWBWBW
WBWBWBWB
```| 行 | 隣接する等しいペアが見つかりましたか? |
 | --- | --- |
 | もりもり いいえ |
 | BWBWBWBW | いいえ |
 | BWBWBWBW | いいえ |
 | BWBWBWBW | いいえ |
 | もりもり いいえ |
 | もりもり いいえ |
 | BWBWBWBW | いいえ |
 | もりもり いいえ |

 すべての行が内部的に交互に配置されるため、アルゴリズムは次のように出力します。```
YES
```この例は、行が最初から正しいチェス盤配置である必要がないことを示しています。 すべての行が交互になっているため、適切な回転により必要なパターンを作成できます。 

### 例 2

 次のことを考慮してください。```
WWWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
```| 行 | 最初の等しい隣接ペア |
 | --- | --- |
 | WWWBWBWB | 位置 1 と 2 |
 | BWBWBWBW | なし |
 | もりもり なし |
 | BWBWBWBW | なし |
 | もりもり なし |
 | BWBWBWBW | なし |
 | もりもり なし |
 | BWBWBWBW | なし |

 最初の行には連続する 2 つの行が含まれます`W`s. 循環シフトではその欠陥を取り除くことはできません。 

出力：```
NO
```この例は、重要な不変条件を示しています。等しい隣接セルは、回転するたびにどこかに隣接したままになります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | お(64) | 8 行、1 行あたり 7 つの隣接性チェック |
 | スペース | お(1) | 入力ボードのみ収納 |

 ボード サイズは 8×8 に固定されているため、実行時間は事実上一定です。 この解決策は時間とメモリの制限をはるかに下回っています。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    board = [input().strip() for _ in range(8)]

    for row in board:
        for i in range(7):
            if row[i] == row[i + 1]:
                return "NO"

    return "YES"

# provided sample
assert run(
"""WBWBWBWB
BWBWBWBW
BWBWBWBW
BWBWBWBW
WBWBWBWB
WBWBWBWB
BWBWBWBW
WBWBWBWB
"""
) == "YES", "sample"

# already a perfect chessboard
assert run(
"""WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
"""
) == "YES", "already valid"

# all white
assert run(
"""WWWWWWWW
WWWWWWWW
WWWWWWWW
WWWWWWWW
WWWWWWWW
WWWWWWWW
WWWWWWWW
WWWWWWWW
"""
) == "NO", "all equal"

# one bad row
assert run(
"""WBWBWBWB
BWBWBWBW
WBWWWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
"""
) == "NO", "single invalid row"

# all rows alternating but identical
assert run(
"""WBWBWBWB
WBWBWBWB
WBWBWBWB
WBWBWBWB
WBWBWBWB
WBWBWBWB
WBWBWBWB
WBWBWBWB
"""
) == "YES", "rows can be shifted independently"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 完璧なチェス盤 | 写真 はい | 許可される操作はゼロ |
 | すべての白いセル | いいえ | 隣接する同じ色を固定することはできません。 
| 1 つの悪い行 | いいえ | 無効な行が 1 つあるとボードが不可能になります。 
| すべての行が等しく交互 | はい | 独立した行ローテーションで十分です。 

## 特殊なケース

 ボードについて考えてみましょう。```
WWWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
```最初の行には以下が含まれます`WW`初めに。 スキャン中に、アルゴリズムは次のことを検出します。`row[0] == row[1]`そしてすぐに戻ります`NO`。 行を回転すると、このペアが別の場所に移動するだけなので、拒否は正しいです。 

すべての行が次と等しいボードを考えてみましょう。```
WBWBWBWB
```8回繰り返しました。 

アルゴリズムは、どの行にも等しい隣接セルを見つけず、戻り値を返します。`YES`。 これは正しいです。2 行ごとに 1 回シフトでき、次のようになります。```
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
...
```それはまさに正しいチェス盤です。 

すでに有効なチェス盤であるボードを考えてみましょう。```
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
WBWBWBWB
BWBWBWBW
```すべての行が交互になるため、アルゴリズムは次の値を返します。`YES`。 操作の実行は許可されていないため、これは正しい結果です。
