---
title: "CF 202B - 新しい簡単な問題"
description: "Lesha の新しい問題と、Torcoder からのいくつかのアーカイブ問題のタイトル文字が与えられています。 リーシャのタイトルにある言葉はどれも特徴的です。 アーカイブのタイトルは単語を繰り返す場合があります。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "brute-force"]
categories: ["algorithms"]
codeforces_contest: 202
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 127 (Div. 2)"
rating: 1700
weight: 202
solve_time_s: 209
verified: true
draft: false
---

[CF 202B - 新しい簡単な問題](https://codeforces.com/problemset/problem/202/B)

 **評価:** 1700
 **タグ:** ブルートフォース
 **解決時間:** 3 分 29 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 Lesha の新しい問題と、Torcoder からのいくつかのアーカイブ問題のタイトル文字が与えられています。 リーシャのタイトルにある言葉はどれも特徴的です。 アーカイブのタイトルは単語を繰り返す場合があります。 

アーカイブの問題ごとに、その中にある Lesha のタイトルのすべての単語をサブシーケンスとして (おそらくは異なる順序で) 見つけることができるかどうかを知りたいと考えています。 すべての有効な順序の中で、Lesha のタイトルの元の順序と比較して反転の数が最小のものを選択します。 

反転とは、元の順序に対して 2 つの単語が入れ替わることを意味します。 Lesha のタイトルが次の場合:```
find the next palindrome
```次に順列:```
find the palindrome next
```反転が 1 つあるため、`palindrome`前に引っ越した`next`。 

アーカイブの問題に対して最適な順列が存在する場合、`x`反転とリーシャのタイトルは`n`つまり、類似性スコアは次のとおりです。```
p = n * (n - 1) / 2 - x
```可能な最大値は、反転が 0 の場合に表示されます。これは、アーカイブに元の順序で単語がすでに含まれていることを意味します。 

最も類似性の高いアーカイブの問題を見つけなければなりません。 アーカイブの問題にすべての単語の順列が含まれていない場合は、次のように出力します。`"Brand new problem!"`。 

制約によってソリューションが完全に形作られます。 Lesha のタイトルには最大 4 単語が含まれます。 それは小さいです。 順列の最大数は次のとおりです。```
4! = 24
```各アーカイブの説明には最大 20 単語が含まれ、アーカイブの問題は最大 10 個あります。 ここでは、すべての順列に対する総当りチェックさえも簡単です。 

危険なのはパフォーマンスではなく、正しさです。 

簡単な間違いの 1 つは、アーカイブに同じ単語が繰り返し含まれている可能性があることを忘れることです。 リーシャのタイトルが次であると仮定します。```
a b
```アーカイブのタイトルは次のとおりです。```
a a a
```言葉`b`は決して表示されないため、並べ替えは機能しません。 長さまたは部分一致のみをチェックする不用意なサブシーケンス マッチャーは、それを誤って受け入れてしまいます。 

もう 1 つの微妙なケースは、複数の順列がサブシーケンスとして表示される場合に、間違った順列を選択することです。 サブシーケンスの柔軟性を最大化するのではなく、反転を最小限に抑える必要があります。 

例：```
Lesha: a b c
Archive: b a c
```両方の順列`b a c`そして`a c b`はサブシーケンスです。 それらの反転数は異なります。 

| 順列 | 反転 |
 | --- | --- |
 | b a c | 1 |
 | ａ ｃ ｂ | 1 |

 ここでは両者は同点ですが、類似性は反転に直接依存するため、より大きなケースでは最小値が重要になります。 

もう 1 つのよくあるバグは、アーカイブの問題間の関係を誤って処理することです。 2 つの問題が同じ類似性を達成する場合、最小のインデックスを出力する必要があります。 

例：```
Lesha: a b
Archive 1: b a
Archive 2: b a
```どちらも同じスコアなので、答えはアーカイブされている必要があります`1`。 

## アプローチ

 最も直接的なアプローチは、Lesha の言葉のあらゆる順列を生成することです。 以来`n ≤ 4`、並べ替えは最大 24 個あります。 アーカイブの問題ごとに、どの順列が部分列として現れるかを確認します。 その中で、反転回数を計算して最小値を維持します。 

シーケンスがサブシーケンスであるかどうかのチェックは、アーカイブの長さにおいて線形です。 アーカイブには最大 20 単語が含まれるため、1 回のチェックで最大 20 回の比較が行われます。 

作業全体はごくわずかです。```
10 archive problems × 24 permutations × 20 checks
```それはわずか数千回の操作です。 

より素朴なブルートフォースでは、アーカイブ位置のすべてのサブセットを試し、結果のシーケンスを比較します。 長さ 20 のアーカイブの場合、`n = 4`、それは以下をチェックすることを意味します:```
C(20,4) = 4845
```アーカイブごとのポジションの組み合わせの問題。 これでもまだ簡単に適合しますが、選択した位置から順列を再構築する必要があるため、実装が複雑になります。 

重要な観察は次のとおりです`n`非常に小さいです。 可能な順序付けの数は 24 に制限されているため、すべての候補順列を直接列挙し、それらを個別にテストできます。 この問題は、順列の生成、部分列のマッチング、および反転カウントをきれいに組み合わせたものになります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | アーカイブ位置サブセットに対するブルート フォース | O(m × C(k,n) × n) | O(n) | 承認済み |
 | すべての順列を列挙する | O(m × n! × k) | お(ん!) | 承認済み |

 ここ`k ≤ 20`そして`n ≤ 4`。 

## アルゴリズムのチュートリアル

 1. Lesha の言葉を配列に読み取る`base`。 
2. すべての順列を生成します。`base`。 

以来`n ≤ 4`、これにより最大 24 個のシーケンスが生成されます。 
3. すべての順列について、元の順序と比較してその反転数を計算します。 

すべての元の単語をそのインデックスにマッピングします。 次に、ペアごとに`(i,j)`と`i < j`、元の位置が逆転した場合に反転をカウントします。`perm[i]`元の位置よりも大きいです`perm[j]`。 
4. すべてのアーカイブの問題を個別に処理します。 
5. 各順列について、それがアーカイブ タイトル内のサブシーケンスとして表示されるかどうかを確認します。 

2 つのポインターを使用します。 

- 1 つのポインターが順列をスキャンします
 - 1 つのポインターがアーカイブの単語をスキャンします

 単語が一致するたびに、順列ポインタを進めます。 

順列ポインタが末尾に達すると、順列はサブシーケンスになります。 
6. 現在のアーカイブ問題で見つかったすべてのサブシーケンスの中で、最小の反転数を維持します。 

最小の反転は最大の類似性を意味します。 
7. 最小反転数を類似度に変換します。```
p = n * (n - 1) / 2 - inversions
```1. 類似性が最も高いアーカイブの問題を追跡します。 

類似性が同点の場合は、小さい方のアーカイブ インデックスを保持します。 
2. アーカイブの問題がどの順列にも一致しない場合は、次のように出力します。```
Brand new problem!
```1. それ以外の場合は、最適なアーカイブ インデックスを出力し、類似性バーを作成します。```
[:||||...||:]
```まさに`p`垂直バー。 

### なぜ効果があるのか

 すべての有効な解決策は、リーシャの言葉のいくつかの並べ替えに対応します。 すべての順列を列挙するため、候補を見逃すことはありません。 

後続のチェックは正確です。 並べ替えは、その単語がアーカイブ タイトル内で順番に一致する場合にのみ受け入れられます。 

問題定義では、受け入れられるすべての順列のうち、反転が最小のものを明示的に求めます。 すべての有効な順列の反転数を計算し、最小値を維持するため、選択された順列は正確になります。 

最後に、類似性は反転の単調変換です。 反転を最小限に抑えることは、類似性を最大限に高めることと同じです。 

## Python ソリューション```python
import sys
from itertools import permutations

input = sys.stdin.readline

def is_subsequence(perm, archive):
    j = 0

    for word in archive:
        if j < len(perm) and word == perm[j]:
            j += 1

    return j == len(perm)

def solve():
    n = int(input())
    base = input().split()

    pos = {word: i for i, word in enumerate(base)}

    perms = []

    for perm in permutations(base):
        inv = 0

        for i in range(n):
            for j in range(i + 1, n):
                if pos[perm[i]] > pos[perm[j]]:
                    inv += 1

        perms.append((perm, inv))

    m = int(input())

    best_similarity = -1
    best_index = -1

    max_pairs = n * (n - 1) // 2

    for idx in range(1, m + 1):
        parts = input().split()

        k = int(parts[0])
        archive = parts[1:]

        best_inv = None

        for perm, inv in perms:
            if is_subsequence(perm, archive):
                if best_inv is None or inv < best_inv:
                    best_inv = inv

        if best_inv is not None:
            similarity = max_pairs - best_inv

            if similarity > best_similarity:
                best_similarity = similarity
                best_index = idx

    if best_index == -1:
        print("Brand new problem!")
    else:
        print(best_index)
        print("[:{}:]".format('|' * best_similarity))

if __name__ == "__main__":
    solve()
```この解決策は、すべての順列を 1 回生成することから始まります。 単語の数が少ないため、すべての順列とその反転数を事前に計算しておくと、後のロジックが簡素化されます。 

逆変換計算では、に保存されている元の順序マッピングが使用されます。`pos`。 これにより、配列内のインデックスを繰り返し検索する必要がなくなります。 

サブシーケンス マッチャーは標準の貪欲スキャンを使用します。 以前の出現を一致させても、後でサブシーケンスが不可能になることはないため、Greedy は機能します。 

多くの場合間違いを引き起こす実装の詳細の 1 つは、類似度を正しく計算することです。 式では次のものが使用されます。```
n * (n - 1) / 2
```これは、順序付けされたペアの最大可能数です。 Python は整数の除算を使用するため、`//`、次のように計算します。```
max_pairs = n * (n - 1) // 2
```もう一つの細かい点はネクタイの扱いです。 新しい類似性が厳密に大きい場合にのみ、最良のアーカイブを更新します。 アーカイブの問題を昇順に繰り返すため、タイは自動的に小さいインデックスを維持します。 

## 実用的な例

 ### サンプル 1

 入力:```
4
find the next palindrome
1
10 find the previous palindrome or print better luck next time
```チェックされたキーの順列:

 | 順列 | 反転 | 続編？ |
 | --- | --- | --- |
 | 次の回文を見つける | 0 | いいえ |
 | 次に回文を見つけます | 1 | はい |
 | 次の回文を見つけます | 2 | いいえ |

 最も有効な置換は次のとおりです。```
find the palindrome next
```1回の反転で。 

可能な最大ペア数:```
4 × 3 / 2 = 6
```類似性:```
6 - 1 = 5
```出力：```
1
[:|||||:]
```このトレースは、最小反転が重要である理由を示しています。 Several permutations may appear, but only the one closest to the original order determines the score.

 ### カスタム例

 入力:```
3
a b c
2
3 c b a
3 a b c
```トレース:

 | アーカイブ | ベスト順列 | 反転 | 類似性 |
 | --- | --- | --- | --- |
 | c b a | c b a | 3 | 0 |
 | a b c | a b c | 0 | 3 |

 2 番目のアーカイブが勝ちます。 

出力：```
2
[:|||:]
```この例は、類似性が元の順序を維持することに直接つながることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(m × n! × k) | すべてのアーカイブについて、すべての順列をサブシーケンスとしてチェックします。 
| スペース | お(ん!) | すべての順列と反転数を保存する |

 と`n ≤ 4`、最大 24 個の順列があります。 最悪の場合でも:```
10 × 24 × 20 = 4800
```後続の比較が実行されます。 ソリューションは制限内に簡単に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
from itertools import permutations

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    def is_subsequence(perm, archive):
        j = 0

        for word in archive:
            if j < len(perm) and word == perm[j]:
                j += 1

        return j == len(perm)

    out = io.StringIO()

    n = int(input())
    base = input().split()

    pos = {word: i for i, word in enumerate(base)}

    perms = []

    for perm in permutations(base):
        inv = 0

        for i in range(n):
            for j in range(i + 1, n):
                if pos[perm[i]] > pos[perm[j]]:
                    inv += 1

        perms.append((perm, inv))

    m = int(input())

    best_similarity = -1
    best_index = -1

    max_pairs = n * (n - 1) // 2

    for idx in range(1, m + 1):
        parts = input().split()
        archive = parts[1:]

        best_inv = None

        for perm, inv in perms:
            if is_subsequence(perm, archive):
                if best_inv is None or inv < best_inv:
                    best_inv = inv

        if best_inv is not None:
            similarity = max_pairs - best_inv

            if similarity > best_similarity:
                best_similarity = similarity
                best_index = idx

    if best_index == -1:
        print("Brand new problem!", file=out)
    else:
        print(best_index, file=out)
        print("[:{}:]".format('|' * best_similarity), file=out)

    return out.getvalue()

# provided sample
assert run(
"""4
find the next palindrome
1
10 find the previous palindrome or print better luck next time
"""
) == "1\n[:|||||:]\n", "sample 1"

# minimum size
assert run(
"""1
hello
1
1 hello
"""
) == "1\n[::]\n", "single word"

# no valid permutation
assert run(
"""2
a b
1
3 a a a
"""
) == "Brand new problem!\n", "missing word"

# tie between archives, smaller index wins
assert run(
"""2
a b
2
2 b a
2 b a
"""
) == "1\n[::]\n", "tie breaking"

# perfect ordering
assert run(
"""3
a b c
1
3 a b c
"""
) == "1\n[:|||:]\n", "maximum similarity"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一単語の場合 |`[::]`| ゼロ反転とゼロ最大ペア |
 | アーカイブに単語がありません |`Brand new problem!`| 後続検証の正確性 |
 | 2 つの同一のアーカイブ スコア | 小さいインデックスが選択されました | ネクタイの取り扱い |
 | 完璧な注文 | 最大類似度 | 正しい反転計算 |

 ## 特殊なケース

 アーカイブに繰り返し単語が含まれているが、必要な単語が 1 つ欠落している場合を考えてみましょう。```
2
a b
1
4 a a a a
```アルゴリズムはすべての順列をチェックします。```
a b
b a
```サブシーケンス マッチャーは、どちらの置換の終わりにも決して到達しません。`b`決して現れません。`best_inv`残っている`None`、したがって、アーカイブは正しく拒否されます。 

次に、タイの処理を考えてみましょう。```
2
a b
2
2 b a
2 b a
```両方のアーカイブに順列が含まれています`b a`1回の反転で。 以来：```
max_pairs = 1
similarity = 1 - 1 = 0
```両方のアーカイブには同等の類似性があります。 

アルゴリズムは、類似性が厳密に大きい場合にのみ答えを更新します。 アーカイブ`1`は選択されたままになり、必要な動作と一致します。 

最後に、可能な最小の入力を検討します。```
1
hello
1
1 hello
```順列は 1 つだけあり、反転の可能性はゼロです。```
1 × 0 / 2 = 0
```類似性バーは次のようになります。```
[::]
```実装ではこれを自然に処理します。`'|' * 0`空の文字列を生成します。
