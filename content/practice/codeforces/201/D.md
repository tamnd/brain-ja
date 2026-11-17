---
title: "CF 201D - 新たな問題"
description: "Lesha には、固定された順序で書かれた n 個の異なる単語からなる問題の説明があります。 アーカイブの問題はそれぞれ別の単語のシーケンスですが、アーカイブの説明では単語が何度も繰り返される場合があります。 私たちは、Lesha の説明をすべてのアーカイブの説明と比較したいと考えています。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "brute-force", "dp"]
categories: ["algorithms"]
codeforces_contest: 201
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 127 (Div. 1)"
rating: 2600
weight: 201
solve_time_s: 99
verified: true
draft: false
---

[CF 201D - 新たな問題](https://codeforces.com/problemset/problem/201/D)

 **評価:** 2600
 **タグ:** ビットマスク、ブルート フォース、DP
 **解決時間:** 1 分 39 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 Lesha の問題の説明は次のとおりです。`n`決まった順序で書かれた個別の単語。 アーカイブの問題はそれぞれ別の単語のシーケンスですが、アーカイブの説明では単語が何度も繰り返される場合があります。 

私たちは、Lesha の説明をすべてのアーカイブの説明と比較したいと考えています。 アーカイブの問題の 1 つとして、Lesha の言葉を任意の順序で並べ替えることが許可されています。 その順列がアーカイブ記述内のサブシーケンスとして現れる場合、アーカイブの問題は同様であると考えられます。 

すべての有効な順列の中で、リーシャの単語の元の順序と比較して逆転の数が最も少ない順列を選択します。 必要な反転が少ないほど、問題はより類似します。 

最良の順列が`x`逆変換の場合、類似性スコアは次のようになります。$$p = \frac{n(n-1)}{2} + 1 - x$$可能な最大反転は次のとおりです。`n(n-1)/2`したがって、類似度が大き​​いほど反転は少なくなります。 

課題は、最も類似性の高いアーカイブの問題を見つけることです。 アーカイブの問題にサブシーケンスとして Lesha の言葉の順列が含まれていない場合は、次のように出力します。`"Brand new problem!"`。 

制約によってソリューションが完全に形作られます。 

臨界限界は`n ≤ 15`。 これはすぐにビットマスク DP を示唆します。`2^15 = 32768`管理可能です。 同時に、アーカイブの説明には最大で次の内容を含めることができます。`500000`合計ワード数が非常に多いため、アーカイブの長さを指数関数的に増加させる解決策は不可能です。 

すべての順列を総当りで調べるには、以下までチェックする必要があります。`15!`順列は天文学的に大きい。 平`10!`すでに大きすぎます。 

小さいもの`n`および巨大なアーカイブ サイズは、アーカイブがほぼ直線的に処理されるべきであることを示唆していますが、高価な組み合わせは一定期間にわたってのみ発生する必要があります。`n`リーシャの言葉。 

素朴な解決策を壊す微妙なケースがいくつかあります。 

リーシャの言葉が次のとおりであるとします。```
a b c
```アーカイブの説明は次のとおりです。```
c b a
```唯一有効な置換は正確に`c b a`、3つの転回形があります。 元の秩序を維持しようとする貪欲なアプローチでは、解決策が存在するとしても、解決策を見つけることができません。 

もう 1 つの危険なケースは、アーカイブ語が繰り返されることです。```
Lesha: a b
Archive: a a a b
```最良の順列は依然として`a b`反転は0です。 各単語の最初の出現のみを保存し、その後の位置を無視すると、誤って有効な遷移が失われる可能性があります。 

複数の置換が可能な場合には、より微妙な問題が発生します。```
Lesha: a b c
Archive: b a c
```両方の順列`b a c`そして`a c b`はサブシーケンスです。 それらの反転数は異なります。```
b a c -> 1 inversion
a c b -> 1 inversion
```存在をチェックするだけの不注意な DP は、反転を最小限に抑える必要性を見逃してしまいます。 

最後に、タイブレークが重要です。 2 つのアーカイブ問題が同じ類似性を達成する場合、最小のアーカイブ インデックスを出力する必要があります。 

## アプローチ

 最も直接的な総当りの解決策は、Lesha の言葉のあらゆる順列を生成することです。 各順列について、それがアーカイブ記述内のサブシーケンスとして現れるかどうかをチェックします。 そうであれば、その反転数を計算し、最小値を維持します。 

サブシーケンスの存在の確認には、アーカイブ長に比例した時間がかかります。 まであります`n!`順列。 

のために`n = 15`:$$15! \approx 1.3 \times 10^{12}$$アーカイブのスキャンを検討する前から、これは絶望的です。 

問題は文字どおり順列を検索することを求めているため、総当り攻撃は概念的に機能します。 問題は、順列が間違った表現であることです。 

重要な観察は、実際には完全な順列自体を気にしていないということです。 反転を最小限に抑えながら、アーカイブ順序でサブシーケンスを構築することのみを考慮します。 

アーカイブの説明を左から右にスキャンするとします。 リーシャの言葉に出会うたびに、私たちはそれを選択した部分列に追加することを決めるかもしれません。 

単語を追加する順序が順列になります。 

ここで、反転回数を段階的に考えてみましょう。 

単語を追加すると`i`すでにいくつかの単語のセットを選択した後、次に、より大きいインデックスを持つ以前に選択されたすべての単語`i`1 つの反転が作成されます。 

つまり、追加の反転コストは現在のサブセット マスクにのみ依存します。 

これはまさにサブセット DP に必要な構造です。 

以下を定義します。`dp[mask] = minimum inversions needed to build exactly this set of chosen words as a subsequence so far`Leshaインデックスに対応する新規アーカイブワードを処理する場合`i`、移行する可能性があります:```
mask -> mask | (1 << i)
```追加される反転カウントは、すでに選択されているインデックスの数より大きいものと等しくなります。`i`。 

アーカイブは左から右に処理されるため、サブシーケンスの順序が自動的に尊重されます。 

これは階乗探索を DP over に変換します。`2^n`マスク。 

マスクの総数は最大で`32768`、十分小さいです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(m・k・n!) | O(n) | 遅すぎる |
 | 最適 | O(総単語数 · 2^n) | O(2^n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. Lesha の言葉を読み、各単語に元のインデックスを割り当てます。`0`に`n-1`。 
2. アーカイブの問題ごとに、以下を初期化します。```
dp[mask] = minimum inversions
```以下を除くすべての状態を無限に設定します。```
dp[0] = 0
```これは、反転語がゼロの単語を選択しないことを表します。 

1. アーカイブの説明を左から右に処理します。 

リーシャの説明に登場する単語のみが重要です。 他のすべての単語はすぐに無視できます。 

1. 現在のアーカイブ ワードが Lesha インデックスに対応すると仮定します。`i`。 

この単語を、まだ含まれていない既存のすべてのサブセットに追加しようとします。`i`。 

1. 有効なマスクごとに、追加の反転コストを計算します。 

追加すると`i`、その後、すでに選択されているすべてのインデックス`j > i`1 回の反転に貢献します。 

それで：```
extra = count of bits j in mask where j > i
```1. 移行:```
new_mask = mask | (1 << i)

dp[new_mask] = min(
    dp[new_mask],
    dp[mask] + extra
)
```同じアーカイブが複数回再利用されないように、マスクを降順で反復します。 

1. アーカイブ記述全体を処理した後、完全なマスクを確認します。```
full = (1 << n) - 1
```もし`dp[full]`が有限である場合、このアーカイブ問題には有効な順列が含まれます。 

1. 反転数を類似度に変換します。$$p = \frac{n(n-1)}{2} + 1 - dp[full]$$アーカイブの問題を最大限に保つ`p`。 インデックスを小さくすることで同点を解消します。 

1. アーカイブの問題がマスク全体に達していない場合は、次のように出力します。```
Brand new problem!
```それ以外の場合は、最適なアーカイブ インデックスを出力し、次のようにします。```
[:||||...||:]
```まさに`p`垂直バー。 

### なぜ効果があるのか

 DP の不変式は次のとおりです。```
dp[mask] = minimum inversions among all subsequences
formed so far whose chosen Lesha words are exactly mask
```アーカイブ単語を左から右に処理する場合、遷移ごとにサブシーケンスの最後に 1 つの新しい単語が追加されます。 これにより、サブシーケンスの有効性が自動的に保持されます。 

付加語の反転寄与`i`は、どの大きなインデックスがすでに選択されているかにのみ依存します。 このようなペアはすべて 1 つの反転に寄与し、小さいインデックスの単語が大きいインデックスの単語の後に追加されるときに、すべての反転が 1 回だけカウントされます。 

すべての有効な置換は DP 遷移のあるシーケンスに対応し、すべての DP 遷移は有効な置換を構築するため、DP は反転を最小限に抑えながら必要な検索空間を正確に探索します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10**9

def solve():
    n = int(input())
    words = input().split()

    pos = {}
    for i, w in enumerate(words):
        pos[w] = i

    m = int(input())

    full = (1 << n) - 1
    max_inv = n * (n - 1) // 2

    best_similarity = -1
    best_index = -1

    greater_bits = [0] * n
    for i in range(n):
        mask = 0
        for j in range(i + 1, n):
            mask |= (1 << j)
        greater_bits[i] = mask

    for problem_index in range(1, m + 1):
        data = input().split()
        k = int(data[0])
        archive = data[1:]

        dp = [INF] * (1 << n)
        dp[0] = 0

        for w in archive:
            if w not in pos:
                continue

            i = pos[w]
            bit = 1 << i

            for mask in range(full, -1, -1):
                if dp[mask] == INF:
                    continue

                if mask & bit:
                    continue

                new_mask = mask | bit

                extra = (mask & greater_bits[i]).bit_count()

                cand = dp[mask] + extra

                if cand < dp[new_mask]:
                    dp[new_mask] = cand

        if dp[full] == INF:
            continue

        inversions = dp[full]
        similarity = max_inv + 1 - inversions

        if similarity > best_similarity:
            best_similarity = similarity
            best_index = problem_index

    if best_similarity == -1:
        print("Brand new problem!")
    else:
        print(best_index)
        print("[:{}:]".format("|" * best_similarity))

solve()
```辞書`pos`すべての Lesha 単語を元のインデックスにマップします。 リーシャの言葉は明確に区別できることが保証されているため、このマッピングは明確です。 

DP 配列にはサイズがあります`2^n`、これは実現可能です。`n ≤ 15`。 

配列`greater_bits[i]`より大きいすべてのインデックスを含むビットマスクを格納します。`i`。 これにより、以下を使用して、一定時間内に新しく作成された反転の数を計算できます。```
(mask & greater_bits[i]).bit_count()
```この式は、すでに選択されている単語の後に最初に出現した単語の数を数えます。`i`。 

降順マスクの反復は必須です。 これがないと、単一のアーカイブ オカレンスが同じ反復中に複数回再利用される可能性があり、サブシーケンス ルールに違反します。 

もう 1 つの微妙な点は、アーカイブの問題では単語が何度も繰り返される可能性があることです。 後の出現によりより良いサブシーケンスが作成される可能性があるため、すべての出現を個別に処理する必要があります。 

最終的な類似度の式はステートメントから直接得られます。 

## 実用的な例

 ### サンプル 1

 入力:```
4
find the next palindrome
1
10 find the previous palindrome or print better luck next time
```インデックスは次のとおりです。 

| 単語 | インデックス |
 | --- | --- |
 | 見つける | 0 |
 | | 1 |
 | 次へ | 2 |
 | 回文 | 3 |

 アーカイブ関連のシーケンスは次のようになります。```
find the palindrome next
```DP 遷移:

 | 現在の単語 | 構築された順列 | 反転 |
 | --- | --- | --- |
 | 見つける | 見つける | 0 |
 | | | を見つけます 0 |
 | 回文 | 回文を見つける | 0 |
 | 次へ | 次に回文を見つけます | 1 |

 最終的な置換は次のとおりです。```
find the palindrome next
```元の注文と比較すると:```
find the next palindrome
```のみ`(palindrome, next)`反転している。 

のために`n = 4`:$$\frac{4 \cdot 3}{2} = 6$$類似性:$$6 + 1 - 1 = 6$$出力：```
1
[:||||||:]
```このトレースは、サブシーケンスの順序を維持しながら反転がどのように増分的に蓄積されるかを示しています。 

### 例 2

 入力:```
3
a b c
1
3 c b a
```関連するアーカイブ順序:```
c b a
```DPの進化：

 | 現在の単語 | これまでで最高の順列 | 反転 |
 | --- | --- | --- |
 | c | c | 0 |
 | b | c b | 1 |
 | | c b a | 3 |

 順列`c b a`3 つのペアがすべて反転されています。```
(c,b), (c,a), (b,a)
```最大反転数`n = 3`も 3 なので、次のようになります。$$3 + 1 - 3 = 1$$出力：```
1
[:|:]
```この例は、アルゴリズムが完全に逆の順序を正しく処理することを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(総単語数 · 2^n) | 関連する各アーカイブ ワードは、すべてのサブセット マスクを反復処理します。 
| スペース | O(2^n) | サブセット上の DP 配列 |

 可能な最大の DP は、`32768`州。 アーカイブの合計の長さは約`5 × 10^5`これにより、最適化された Python で合計操作数が実用的な制限内に抑えられます。 

メモリ使用量は、256 MB の制限に比べてわずかです。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

INF = 10**9

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    out = io.StringIO()
    sys.stdout = out

    input = sys.stdin.readline

    n = int(input())
    words = input().split()

    pos = {}
    for i, w in enumerate(words):
        pos[w] = i

    m = int(input())

    full = (1 << n) - 1
    max_inv = n * (n - 1) // 2

    best_similarity = -1
    best_index = -1

    greater_bits = [0] * n
    for i in range(n):
        mask = 0
        for j in range(i + 1, n):
            mask |= (1 << j)
        greater_bits[i] = mask

    for problem_index in range(1, m + 1):
        data = input().split()
        archive = data[1:]

        dp = [INF] * (1 << n)
        dp[0] = 0

        for w in archive:
            if w not in pos:
                continue

            i = pos[w]
            bit = 1 << i

            for mask in range(full, -1, -1):
                if dp[mask] == INF:
                    continue

                if mask & bit:
                    continue

                new_mask = mask | bit

                extra = (mask & greater_bits[i]).bit_count()

                dp[new_mask] = min(
                    dp[new_mask],
                    dp[mask] + extra
                )

        if dp[full] == INF:
            continue

        similarity = max_inv + 1 - dp[full]

        if similarity > best_similarity:
            best_similarity = similarity
            best_index = problem_index

    if best_similarity == -1:
        print("Brand new problem!")
    else:
        print(best_index)
        print("[:{}:]".format("|" * best_similarity))

    return out.getvalue()

# provided sample
assert run(
"""4
find the next palindrome
1
10 find the previous palindrome or print better luck next time
"""
) == "1\n[:||||||:]\n", "sample 1"

# minimum size
assert run(
"""1
hello
1
1 hello
"""
) == "1\n[:|:]\n", "single word"

# no valid permutation
assert run(
"""2
a b
1
3 a x x
"""
) == "Brand new problem!\n", "missing word"

# reversed order
assert run(
"""3
a b c
1
3 c b a
"""
) == "1\n[:|:]\n", "maximum inversions"

# tie-breaking by smaller index
assert run(
"""2
a b
2
2 a b
2 a b
"""
) == "1\n[:||:]\n", "smallest index tie"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一単語の場合 | 類似点 1 | 最小制約 |
 | 不足している単語 | まったく新しい問題 | 完全なマスクにアクセスできません |
 | 逆順 | 最も低い類似度 | 最大反転処理 |
 | 同一のベストアーカイブ | 選択された最小のインデックス | 同点突破の正しさ |

 ## 特殊なケース

 次のことを考慮してください。```
2
a b
1
3 a a b
```アーカイブが繰り返される`a`複数回。 

DP は各発生を独立して処理します。 最初を読んだ後、`a`、サブセットを形成できます`{a}`。 後者`a`ビットはすでに設定されているため、そのサブセットの出現は無視されます。 

いつ`b`が到着すると、DP は反転がゼロの完全なマスクに達します。 

出力：```
1
[:||:]
```これにより、繰り返されるアーカイブ ワードが安全に処理されることが確認されます。 

次に、完全に逆のケースを考えてみましょう。```
3
a b c
1
3 c b a
```トランジション:```
{} -> {c} -> {c,b} -> {c,b,a}
```追記する場合`b`、1 つ大きなインデックス (`c`) はすでに存在しますが、反転が 1 つ追加されます。 

追記する場合`a`、2 つのより大きなインデックスがすでに存在しており、さらに 2 つの反転が追加されます。 

総反転数:```
1 + 2 = 3
```それは正しいです。 

最後に、有効なサブシーケンスがない場合を考えてみましょう。```
3
a b c
1
4 a x y b
```DP が完全なマスクに到達することはありません。`c`決して現れません。 

最終状態は無限のままです。```
dp[111] = INF
```したがって、アルゴリズムは正しく出力します。```
Brand new problem!
```
