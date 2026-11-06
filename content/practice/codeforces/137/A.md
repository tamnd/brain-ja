---
title: "CF 137A - ポストカードと写真"
description: "C と P という 2 つの文字で構成される文字列が与えられています。各文字は、壁に掛かっている 1 つのオブジェクトを表します。 Cはポストカード、Pは写真を意味します。 Polycarpus は左から右にオブジェクトを削除します。 彼はポジションをスキップすることはできず、常に 1 種類のオブジェクトのみを運ぶことができます。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 137
codeforces_index: "A"
codeforces_contest_name: "Codeforces Beta Round 98 (Div. 2)"
rating: 900
weight: 137
solve_time_s: 88
verified: true
draft: false
---

[CF 137A - ポストカードと写真](https://codeforces.com/problemset/problem/137/A)

 **評価:** 900
 **タグ:** 実装
 **解決時間:** 1 分 28 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 2 つの文字で構成される文字列が与えられます。`C`そして`P`。 各文字は壁に掛かっている 1 つのオブジェクトを表します。`C`ポストカードという意味ですが、`P`写真という意味です。 

Polycarpus は左から右にオブジェクトを削除します。 彼はポジションをスキップすることはできず、常に 1 種類のオブジェクトのみを運ぶことができます。 また、一度に 5 つ以上の物体を運ぶこともできません。 

このタスクは、すべてのオブジェクトを削除するために必要なクローゼットへの最小移動回数を計算することです。 

重要な点は、オブジェクトの種類が変わったら、続行する前に手を空にする必要があるということです。 たとえば、次のシーケンスでは、`CCCPP`、彼は3枚のポストカードを一緒に持ち運ぶことができますが、最初の写真が表示されたときに、最初にクローゼットにアクセスする必要があります。 

文字列の長さは最大でも 100 であり、非常に小さいです。 二次解法でも簡単に合格します。 それでも、問題の構造により、直接リニア スキャンが可能になります。 

よくある間違いは、ローカルではなくグローバルに考えることです。 Polycarpus は厳密に左から右に歩き、項目をスキップできないため、等しい文字の最大連続ブロックはそれぞれ他のブロックから独立しています。 

次の例を考えてみましょう。```
CCCCCC
```正しい答えは、`2`、 ない`6`。 彼は最大 5 つのアイテムを運ぶことができるため、6 枚のポストカードのブロックには 2 回の旅行が必要です。 

もう 1 つの簡単な間違いは、型の変更による分割を忘れることです。```
CPPPP
```正しい答えは、`2`。 ポストカード 1 枚で 1 回、写真 4 枚で 1 回。 文字列全体の長さを 1 つのバッチとして扱うと、誤った結果が得られます。`1`。 

ブロック サイズがちょうど 5 で割り切れる場合、1 倍ではない落とし穴もあります。```
CCCCCCCCCC
```正しい答えは、`2`、 ない`3`。 サイズ 5 の 2 つの旅行に 10 個のアイテムがぴったり収まります。次のような式を使用します。`len // 5 + 1`without checking divisibility would fail here.

 ## アプローチ

 The brute-force idea is to simulate the process literally. We walk through the string while tracking the current carried type and how many objects are currently in hand. Whenever the type changes or the hand reaches 5 items, we make a trip to the closet and continue.

 This simulation is already fast enough because the string is extremely short. The total work is proportional to the string length.

 There is an even cleaner way to think about the same process.

 The wall naturally splits into maximal contiguous groups of equal characters. 例えば：```
CCCPPCCCC
```は次のようになります:```
CCC | PP | CCCC
```ポリュカルプスは手の中でタイプを混合できないため、各グループは完全に独立しています。 1 つのサイズのグループ内`k`、最小旅行数は単純に次のようになります。```
ceil(k / 5)
```各旅行で最大 5 つの物体を運ぶことができるためです。 

全体の問題は、文字列をスキャンし、連続する等しい文字ブロックの長さを見つけて、必要なトリップを合計することに帰着します。 

ブルート フォース シミュレーションは実際のプロセスを直接モデル化するため機能しますが、グループ化の観察により問題が単純なカウント タスクに変わります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォースシミュレーション | O(n) | お(1) | 承認済み |
 | 最適なグループカウント | O(n) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力文字列を読み取ります。 
2. 回答変数を初期化する`ans = 0`。 
3. 連続する等しい文字をグループ化しながら、文字列を左から右にスキャンします。 
4. 各グループについて、その長さを数えます`cnt`。 
5. 以下を使用して、このグループが必要とする旅行の回数を計算します。```
(cnt + 4) // 5
```これは整数演算です`ceil(cnt / 5)`。 

1. この値を最終的な答えに追加します。 
2. 文字列全体が処理されるまで続行します。 
3. 答えを印刷します。 

なぜ効果があるのか:

 各旅行には 1 つのオブジェクト タイプのみを含めることができるため、異なるグループのオブジェクトを 1 つの旅行に結合することはできません。 単一のグループ内では、各旅行のコストは同じであるため、旅行ごとにできるだけ多くのオブジェクトを持っていくことが常に最適です。 自発的に持ち歩く荷物が 5 つ未満であれば、旅行の回数が増えるだけです。 すべてのグループが最適かつ独立して処理されるため、合計は全体的に最適になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

s = input().strip()

n = len(s)
ans = 0
i = 0

while i < n:
    j = i

    while j < n and s[j] == s[i]:
        j += 1

    cnt = j - i
    ans += (cnt + 4) // 5

    i = j

print(ans)
```このソリューションでは 2 つのポインターを使用します。`i`そして`j`、等しい文字の連続したブロックを識別します。`i`現在のグループの開始をマークします。`j`キャラクターが変わるまで先に進みます。 グループの長さは`j - i`。 

表現`(cnt + 4) // 5`は、5 による上限除算を計算するための標準的な整数のみの方法です。グループ サイズがちょうど 5 で割り切れる場合を含め、すべてのケースを正しく処理します。 

更新中`i = j`次の未処理のグループに直接移動します。 

この実装では、グループがオンザフライで処理されるため、不要な配列や余分なメモリが回避されます。 

## 実用的な例

 ### 例 1

 入力:```
CPCPCPC
```| ステップ | グループ | 長さ | グループ旅行 | 合計回答 |
 | --- | --- | --- | --- | --- |
 | 1 | C | 1 | 1 | 1 |
 | 2 | P | 1 | 1 | 2 |
 | 3 | C | 1 | 1 | 3 |
 | 4 | P | 1 | 1 | 4 |
 | 5 | C | 1 | 1 | 5 |
 | 6 | P | 1 | 1 | 6 |
 | 7 | C | 1 | 1 | 7 |

 最終出力:```
7
```この例は、考えられる最悪の断片化を示しています。 オブジェクトごとにタイプが変わるため、何もグループ化することはできません。 

### 例 2

 入力:```
CCCCCCPPPPPP
```| ステップ | グループ | 長さ | グループ旅行 | 合計回答 |
 | --- | --- | --- | --- | --- |
 | 1 | CCCCCC | 6 | 2 | 2 |
 | 2 | PPPPPP | 6 | 2 | 4 |

 最終出力:```
4
```これは、大規模なグループが最大 5 つのアイテムのバッチにどのように分割されるかを示しています。 6 つの各ブロックにはちょうど 2 回のトリップが必要です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | 各文字は 1 回処理されます。 
| スペース | お(1) | 少数の変数のみが使用されます。 

入力サイズが小さいため、解は簡単に制限内に収まります。 最大長が 100 の場合でも、アルゴリズムは 1 回の線形スキャンのみを実行します。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def solve():
    input = sys.stdin.readline

    s = input().strip()

    n = len(s)
    ans = 0
    i = 0

    while i < n:
        j = i

        while j < n and s[j] == s[i]:
            j += 1

        cnt = j - i
        ans += (cnt + 4) // 5

        i = j

    print(ans)

def run(inp: str) -> str:
    backup_stdin = sys.stdin
    backup_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    solve()

    out = sys.stdout.getvalue()

    sys.stdin = backup_stdin
    sys.stdout = backup_stdout

    return out

# provided sample
assert run("CPCPCPC\n") == "7\n", "sample 1"

# custom cases
assert run("C\n") == "1\n", "single item"
assert run("CCCCCCCCCC\n") == "2\n", "exact multiple of 5"
assert run("PPPPPP\n") == "2\n", "single group larger than 5"
assert run("CCCPPCCCC\n") == "3\n", "multiple groups"
assert run(("C" * 100) + "\n") == "20\n", "maximum length all same"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`C`|`1`| 最小サイズの入力 |
 |`CCCCCCCCCC`|`2`| 5 で正確に割り切れる |
 |`PPPPPP`|`2`| 容量オーバー後の追加のトリップが 1 回 |
 |`CCCPPCCCC`|`3`| グループ間の正しい分離 |
 |`C`100回繰り返した |`20`| 最大サイズの入力 |

 ## 特殊なケース

 次の入力を考えてみましょう。```
CCCCCCCCCC
```アルゴリズムは、長さ 10 のグループを 1 つ見つけます。```
(10 + 4) // 5 = 14 // 5 = 2
```したがって、答えは次のとおりです`2`。 これにより、正確に割り切れるケースが正しく処理されます。 

ここで次のことを考えてみましょう。```
CPPPP
```グループは次のとおりです。```
C
PPPP
```最初の貢献者`1`旅行、2番目の貢献`1`旅行、合計`2`。 

このアルゴリズムは、ルールに正確に一致する型変更全体でオブジェクトを結合しようとすることはありません。 

最後に、非常に交互のケースを考えてみましょう。```
PCPCPC
```どのグループにもサイズがあります`1`したがって、すべてのオブジェクトには独自の移動が必要です。 

このアルゴリズムは 6 つの別々のグループを処理します。```
1 + 1 + 1 + 1 + 1 + 1 = 6
```これにより、グループ化ロジックが可能な最大の断片化も正しく処理することが確認できます。
