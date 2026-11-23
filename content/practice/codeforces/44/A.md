---
title: "CF 44A - インディアン サマー"
description: "各葉は、樹種と葉の色という 2 つの文字列で記述されます。 Alyona は、まったく同じ値のペアを持つ別のリーフをまだ持っていない場合にのみ、リーフを保持します。 タスクは単に、入力内に出現する異なる (種、色) の組み合わせの数を数えるだけです。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 44
codeforces_index: "A"
codeforces_contest_name: "School Team Contest 2 (Winter Computer School 2010/11)"
rating: 900
weight: 44
solve_time_s: 83
verified: true
draft: false
---

[CF 44A - インディアン サマー](https://codeforces.com/problemset/problem/44/A)

 **評価:** 900
 **タグ:** 実装
 **解決時間:** 1 分 23 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各葉は、樹種と葉の色という 2 つの文字列で記述されます。 Alyona は、まったく同じ値のペアを持つ別のリーフをまだ持っていない場合にのみ、リーフを保持します。 

タスクは単純に、異なるものがいくつあるかを数えるということです。`(species, color)`組み合わせが入力に表示されます。 同じペアが複数回出現する場合は、1 回だけカウントします。 

制約は非常に小さいです。 葉の数は最大 100 で、各文字列の長さは最大 10 です。$O(n^2)$比較ベースのソリューションは、最悪の場合でも約 10,000 件の比較しか実行されないため、制限時間内に簡単に収まります。 これは、問題はアルゴリズムの最適化ではなく、正しい実装にあることを意味します。 

間違いの主な原因は、何が重複とみなされるかを誤解することです。 2 つの葉は、種と色の両方が一致する場合にのみ同一とみなされます。 

次の例を考えてみましょう。```
3
birch yellow
birch green
birch yellow
```正しい答えは次のとおりです。```
2
```種を追跡するだけの不注意な実装は、誤った結果を返す可能性があります。`1`。 

もう 1 つの簡単な間違いは、種とは別に色を数えることです。```
3
maple red
birch red
oak red
```正しい答えは次のとおりです。```
3
```3 つの葉はすべて同じ色ですが、異なる木から来たものです。 

最後のエッジケースは、すべてのリーフが同一である場合です。```
4
oak green
oak green
oak green
oak green
```正しい答えは次のとおりです。```
1
```アルゴリズムは、繰り返されるペアを複数回カウントすることを避ける必要があります。 

## アプローチ

 最も直接的な解決策はブルートフォースです。 葉を 1 つずつ処理し、これまでに見つかった固有の葉のリストを維持できます。 新しいリーフごとにリストをスキャンし、同じかどうかを確認します。`(species, color)`ペアはすでに存在します。 そうでない場合は追加します。 

この問題は、ペアが以前に出現したかどうかのみを尋ねるので、これは機能します。 リーフの数は最大でも 100 であるため、最悪の場合のパフォーマンスは次のようになります。$$1 + 2 + 3 + \dots + 99 \approx 5000$$ペアの比較はまったく問題ありません。 

ブルートフォース手法の制限は、以前に確認された要素を繰り返しスキャンすることです。 入力サイズがはるかに大きい場合、この繰り返しの検索のコストが高くなります。 

重要な観察は、リーフ記述が自然に一意のキーのように動作するということです。 Python セットはまさにこの目的のために設計されています。 セットは重複を自動的に削除するため、すべてのセットを挿入すると、`(species, color)`ペアをセットにすると、最終的なセットのサイズは、個別のリーフの数とまったく同じになります。 

これにより、繰り返しの線形探索が平均化されます。$O(1)$挿入操作と検索操作。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(n^2)$|$O(n)$| 承認済み |
 | 最適 |$O(n)$平均 |$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 整数を読み取る`n`、葉の数。 
2. という空のセットを作成します。`seen`。 

セットには次のペアが保存されます。`(species, color)`。 セットは重複を自動的に無視します。 
3. 繰り返します`n`回:

 入力から種と色の文字列を読み取ります。 
4. ペアを挿入します`(species, color)`セットに。 

ペアがすでに存在する場合、セットは変更されません。 新しい場合はセットが 1 つ増えます。 
5. すべてのリーフを処理した後、セットのサイズを印刷します。 

セット内の要素の数は、個別のリーフ記述の数と正確に一致します。 

### なぜ効果があるのか

 アルゴリズムは次の不変条件を維持します。`seen`あらゆるユニークな要素が含まれています`(species, color)`これまで出会ったペア。 

新しいリーフが処理されるたびに、それをセットに挿入すると、この不変条件が保持されます。 重複したペアは追加のエントリを作成しませんが、新しいペアは 1 回だけ追加されます。 

すべてのリーフが処理された後、すべての個別のリーフ記述がセット内に 1 回だけ表示されます。 したがって、セットのサイズが必要な答えになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())
    
    seen = set()
    
    for _ in range(n):
        species, color = input().split()
        seen.add((species, color))
    
    print(len(seen))

solve()
```ソリューションはアルゴリズムに直接に従います。 

セット`seen`のタプルを格納します`(species, color)`。 タプルは Python でハッシュ可能であるため、安全かつ効率的にセットに挿入できます。 

行:```
seen.add((species, color))
```ソリューションの核心です。 ペアがすでに存在する場合は、何も変わりません。 これにより、手動による重複チェックが回避されます。 

使用する`input().split()`各行には常にスペースで区切られた 2 つの文字列が含まれるため、安全です。 

この実装には、難しい境界条件はありません。 すべてのリーフが同一であるか、すべてが一意である場合でも、セットは自動的に正しく動作します。 

## 実用的な例

 ### 例 1

 入力:```
5
birch yellow
maple red
birch yellow
maple yellow
maple green
```| ステップ | 現在のリーフ | セット内容 | セットサイズ |
 | --- | --- | --- | --- |
 | 1 |`(birch, yellow)`|`{(birch, yellow)}`| 1 |
 | 2 |`(maple, red)`|`{(birch, yellow), (maple, red)}`| 2 |
 | 3 |`(birch, yellow)`|`{(birch, yellow), (maple, red)}`| 2 |
 | 4 |`(maple, yellow)`|`{(birch, yellow), (maple, red), (maple, yellow)}`| 3 |
 | 5 |`(maple, green)`|`{(birch, yellow), (maple, red), (maple, yellow), (maple, green)}`| 4 |

 最終出力:```
4
```このトレースは、重複挿入によってセットが変更されないことを示しています。 

### 例 2

 入力:```
4
oak green
oak green
oak green
oak green
```| ステップ | 現在のリーフ | セット内容 | セットサイズ |
 | --- | --- | --- | --- |
 | 1 |`(oak, green)`|`{(oak, green)}`| 1 |
 | 2 |`(oak, green)`|`{(oak, green)}`| 1 |
 | 3 |`(oak, green)`|`{(oak, green)}`| 1 |
 | 4 |`(oak, green)`|`{(oak, green)}`| 1 |

 最終出力:```
1
```この例は、繰り返される同一の葉が 1 回だけカウントされることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n)$平均 | 各セット挿入は平均的$O(1)$|
 | スペース |$O(n)$| すべてが一意である場合、セットはすべてのリーフを格納できます。 

と$n \le 100$、解決策は簡単に十分に高速です。 最大でも 100 ペアしか保存されないため、メモリの使用量もわずかです。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def solve():
    input = sys.stdin.readline

    n = int(input())
    
    seen = set()
    
    for _ in range(n):
        species, color = input().split()
        seen.add((species, color))
    
    print(len(seen))

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    solve()

    return sys.stdout.getvalue()

# provided sample
assert run(
"""5
birch yellow
maple red
birch yellow
maple yellow
maple green
"""
) == "4\n", "sample 1"

# minimum input size
assert run(
"""1
oak red
"""
) == "1\n", "minimum case"

# all leaves identical
assert run(
"""4
oak green
oak green
oak green
oak green
"""
) == "1\n", "all equal"

# same color but different species
assert run(
"""3
oak red
birch red
maple red
"""
) == "3\n", "species matters"

# same species but different colors
assert run(
"""4
oak red
oak green
oak yellow
oak red
"""
) == "3\n", "color matters"

# maximum distinct small-style case
inp = "100\n"
for i in range(100):
    inp += f"tree{i} color{i}\n"

assert run(inp) == "100\n", "many unique pairs"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 一枚の葉 | 写真`1`| 最小入力サイズ |
 | すべて同じ葉 |`1`| 重複の削除 |
 | 同じ色、異なる種 |`3`| 種は鍵の一部です |
 | 同じ種、異なる色 |`3`| 色は鍵の一部です |
 | 100 枚のユニークな葉 |`100`| 最大の個別エントリを処理します。 

## 特殊なケース

 種のみが一致する場合を考えてみましょう。```
3
oak red
oak green
oak red
```アルゴリズムはペアを順番に処理します。 

読んだ後`(oak, red)`、セットには 1 つの要素が含まれます。 

読んだ後`(oak, green)`はカラー変更によりペアが異なりますのでセットサイズは2本となります。 

決勝戦`(oak, red)`セット内にすでに存在するため、サイズは 2 のままです。 

出力は次のとおりです。```
2
```これにより、アルゴリズムが両方のフィールドを一緒に比較することが確認されます。 

次に、同じ色で種が異なる葉を考えてみましょう。```
3
birch yellow
maple yellow
oak yellow
```種は毎回異なるため、挿入するたびに新しいペアが作成されます。 

セットは次のように進化します。```
{(birch, yellow)}
{(birch, yellow), (maple, yellow)}
{(birch, yellow), (maple, yellow), (oak, yellow)}
```出力は次のようになります。```
3
```これにより、色だけをアイデンティティとして扱うという間違いを防ぐことができます。 

最後に、完全に複製されたケースを考えてみましょう。```
5
pine green
pine green
pine green
pine green
pine green
```最初の挿入で追加されるのは、`(pine, green)`セットへ。 ペアはすでに存在しているため、それ以降の挿入は無視されます。 

最終的なセットのサイズは次のとおりです。```
1
```これにより、重複が正確に 1 回カウントされることが確認されます。
