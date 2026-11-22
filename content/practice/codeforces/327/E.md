---
title: "CF 327E - アクシスウォーキング"
description: "n 個の正のセグメント長があります。 それらの合計は d であり、これは数直線上の目的点でもあります。 ルートは単にこれらの長さを順序付けしたものです。 ルートをたどっている間、実行中のプレフィックスの合計を保持します。"
date: "2026-06-06T08:58:49+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "combinatorics", "constructive-algorithms", "dp", "meet-in-the-middle"]
categories: ["algorithms"]
codeforces_contest: 327
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 191 (Div. 2)"
rating: 2300
weight: 327
solve_time_s: 145
verified: true
draft: false
---

[CF 327E - 軸ウォーキング](https://codeforces.com/problemset/problem/327/E)

 **評価:** 2300
 **タグ:** ビットマスク、組み合わせ論、構成的アルゴリズム、dp、中間者会合
 **解決時間:** 2 分 25 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは持っています`n`正のセグメントの長さ。 それらの合計は`d`、これは数直線上の目的点でもあります。 

ルートは単にこれらの長さを順序付けしたものです。 ルートをたどっている間、実行中のプレフィックスの合計を保持します。 最初のセグメントを取得した後、一度停止し、2 番目のセグメントの後で再び停止します。 最終ストップは常にその位置にあります`d`。 

いくつかのポジションは不運です。 ルートの停止点がいずれも不運な位置と一致しない場合、そのルートは良好です。 

タスクは、与えられた長さの適切な順列の数を法で数えることです。`10^9 + 7`。 

最も重要な観察は、すべての停止点が順列の何らかの接頭辞の合計であるということです。 すべての長さが正であるため、プレフィックスの合計は厳密に増加します。 この単調性が問題を扱いやすくしています。 

制約`n ≤ 24`すべての順列を含むものは即座に除外されます。 平`20!`天文学的に大きいです。 ソリューションは、順列ではなくサブセットを使用して機能する必要があります。 

アンラッキーなポジションの数は最大 2 つです。 この小さな境界が 2 番目の重要な手がかりです。 これは、不運な位置に対する包含と排除を示唆しており、少数の計数下位問題のみを残します。 

見逃しやすいコーナーケースがいくつかあります。 

不運な位置をサブセット合計として表現できない場合、ルートはそこに停止することはできません。 例えば：```
n = 3
a = [2, 4, 8]
unlucky = 5
```サブセット合計はありません`5`, したがって、すべての順列は有効です。 

アンラッキーポジションが合計額に等しい場合`d`、終点は常に であるため、すべてのルートは無効です。`d`。 例えば：```
n = 2
a = [3, 7]
unlucky = 10
```答えは`0`。 

不運な位置が 2 つある場合、ルートは両方にヒットする可能性があります。 包含/除外では、これらのルートを一度追加し直す必要があります。 例えば：```
a = [1, 2, 3]
unlucky = {1, 3}
```順列`[1,2,3]`は両方の不運な位置に当たるため、各悪いセットを個別に減算すると、それが 2 回削除されます。 

## アプローチ

 ブルートフォースソリューションはすべてを生成します`n!`順列を計算し、すべてのプレフィックスの合計を計算し、アンラッキーなプレフィックスがあるかどうかを確認します。 

これはルートの定義に直接従うため、これは正しいです。 残念ながら、すぐに役に立たなくなります。 たとえ`n = 15`、順列の数が次を超えています`10^12`。 

実現可能なものを得るには、順列を間接的にカウントする必要があります。 

不運な立場を 1 つ考えてみましょう`x`。 

ルートが到着する`x`ちょうどその前に配置された要素が停止するサブセットを形成するとき、その合計は`x`。 

サブセットを仮定します`S`合計があります`x`そしてサイズ`s`。 のすべての要素が含まれるすべての順列`S`外部のすべての要素の前に表示されます`S`届くだろう`x`。 内部の要素`S`は任意に順序付けることができ、残りの要素も任意に順序付けることができます。 

したがって、そのサブセットが貢献します```
s! · (n - s)!
```悪い順列。 

したがって、問題は次のようになります。```
For every subset whose sum is x,
add size! · remaining_size!
```これは部分集合の合計を数える問題であり、順列の問題ではありません。 

以来`n ≤ 24`、中間者会議は自然なツールです。 配列を最大で 2 つのサイズに分割する`12`減らす`2^24`サブセットから約`2 · 2^12 = 8192`列挙。 

2 つの不運な位置について`x < y`、包含と除外では、両方の位置にヒットするルートをカウントする必要があります。 

このようなルートには次のものが含まれます。```
S          -> prefix sum x
S ∪ U      -> prefix sum y
```どこ```
sum(S) = x
sum(U) = y - x
S and U are disjoint
```もし```
|S| = a
|U| = b
```対応する順列は次のようになります。```
a! · b! · (n - a - b)!
```なぜなら、のすべての要素が`S`最初に来てから、次のすべての要素を指定する必要があります`U`、それからその他すべて。 

各要素には 3 つの可能な状態があります。```
belongs to S
belongs to U
belongs to neither
```これにより、三者間での衝突が発生します。`3^(n/2)`states.

 のために`n = 24`、各半分には最大でも`3^12 = 531441`状態は完全に管理可能です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n・n!) | O(n) | 遅すぎる |
 | 最適 | O(2^(n/2) + 3^(n/2)) | O(3^(n/2)) | 承認済み |

 ## アルゴリズムのチュートリアル

 ### 不運な位置に当たったルートを数える

 1. アレイを 2 つの半分に分割します。 
2. 右半分のすべてのサブセットを列挙し、それらをグループ化します。`(sum, size)`。 
3. 左半分のすべてのサブセットを列挙します。 
4. ターゲットのアンラッキーポジションについて`x`、左のサブセットが寄与すると仮定します。`(sumL, sizeL)`。 合計を持つ正しいサブセットが必要です`x - sumL`。 
5. すべての一致するペアは完全なサブセットを形成し、その合計サイズは`sizeL + sizeR`。 
6.追加```
count · (sizeL + sizeR)! · (n - sizeL - sizeR)!
```答えに。 

これにより、ヒットした順列の数が得られます。`x`。 

### 両方の不運な位置に当たるルートを数える

 仮定する`x < y`そしてさせてください```
d = y - x
```1. アレイを 2 つの半分に分割します。 
2. 右半分の 3 項代入をすべて列挙します。 
3. すべての割り当てレコードについて:```
(sumS, sumU, sizeS, sizeU)
```そしてその頻度。 

1. 左半分の 3 項代入をすべて列挙します。 
2. 左の代入の場合、必要な右の代入は以下を満たしている必要があります。```
sumS_right = x - sumS_left
sumU_right = d - sumU_left
```1. すべての一致するペアが素のペアを決定します`(S,U)`。 
2. 組み合わせたサイズが次の場合:```
a = sizeS_left + sizeS_right
b = sizeU_left + sizeU_right
```追加```
a! · b! · (n - a - b)!
```一致するペアの数を倍します。 

### 包含と除外

 アンラッキーなポジションがない場合:```
answer = n!
```不運なポジションが 1 つある場合:```
answer = n! - bad(x)
```不運な位置が 2 つある場合:```
answer = n! - bad(x) - bad(y) + bad_both(x,y)
```すべての算術はモジュロで実行されます`10^9 + 7`。 

### なぜ効果があるのか

 単一の不運な位置の場合、その位置に到達するすべてのルートによって、停止の前に表示される要素の 1 つのサブセットが正確に決まります。 逆に、必要な合計を持つすべてのサブセットは正確に生成します。`s!(n-s)!`そういったルート。 これにより、不正なルートとカウントされたサブセット構成の間の全単射が確立されます。 

2 つのアンラッキーな位置の場合、すべての長さがポジティブであるため、小さい方のアンラッキーな位置に最初に到達する必要があります。 したがって、両方の位置に到達するすべてのルートは、互いに素なセットに一意に分解されます。`S`そして`U`、 どこ`S`最初の位置に到達し、`S ∪ U`2番目に達します。 カウント式は、順列内のこれらのセットの必要なブロック順序から直接導き出されます。 

包含と除外が正確であるため、すべてのルートが正しい多重度でカウントされます。 

## Python ソリューション```python
import sys
from collections import defaultdict

input = sys.stdin.readline

MOD = 1000000007

def subset_bad(arr, target, fact):
    n = len(arr)
    m = n // 2
    left = arr[:m]
    right = arr[m:]

    right_map = defaultdict(lambda: [0] * (len(right) + 1))

    rlen = len(right)
    for mask in range(1 << rlen):
        s = 0
        cnt = 0
        for i in range(rlen):
            if mask >> i & 1:
                s += right[i]
                cnt += 1
        right_map[s][cnt] += 1

    ans = 0

    llen = len(left)
    for mask in range(1 << llen):
        s = 0
        cnt = 0
        for i in range(llen):
            if mask >> i & 1:
                s += left[i]
                cnt += 1

        need = target - s
        if need not in right_map:
            continue

        vec = right_map[need]

        for rsz, ways in enumerate(vec):
            if ways == 0:
                continue

            sz = cnt + rsz
            ans = (ans + ways * fact[sz] * fact[n - sz]) % MOD

    return ans

def both_bad(arr, x, y, fact):
    if x > y:
        x, y = y, x

    diff = y - x
    n = len(arr)

    m = n // 2
    left = arr[:m]
    right = arr[m:]

    right_map = defaultdict(lambda: defaultdict(int))

    def gen_half(values):
        res = []

        def dfs(idx, sum_s, sum_u, cnt_s, cnt_u):
            if idx == len(values):
                res.append((sum_s, sum_u, cnt_s, cnt_u))
                return

            v = values[idx]

            dfs(idx + 1, sum_s, sum_u, cnt_s, cnt_u)
            dfs(idx + 1, sum_s + v, sum_u, cnt_s + 1, cnt_u)
            dfs(idx + 1, sum_s, sum_u + v, cnt_s, cnt_u + 1)

        dfs(0, 0, 0, 0, 0)
        return res

    right_states = gen_half(right)

    for ss, su, cs, cu in right_states:
        right_map[(ss, su)][(cs, cu)] += 1

    ans = 0

    left_states = gen_half(left)

    for ss, su, cs, cu in left_states:
        need = (x - ss, diff - su)

        if need not in right_map:
            continue

        bucket = right_map[need]

        for (rs, ru), ways in bucket.items():
            a = cs + rs
            b = cu + ru
            c = n - a - b

            ans += ways * fact[a] * fact[b] * fact[c]
            ans %= MOD

    return ans

def solve():
    n = int(input())
    a = list(map(int, input().split()))

    k = int(input())

    unlucky = []
    if k:
        unlucky = list(map(int, input().split()))

    fact = [1] * (n + 1)
    for i in range(1, n + 1):
        fact[i] = fact[i - 1] * i % MOD

    total = fact[n]

    if k == 0:
        print(total)
        return

    if k == 1:
        ans = (total - subset_bad(a, unlucky[0], fact)) % MOD
        print(ans)
        return

    x, y = unlucky

    bad_x = subset_bad(a, x, fact)
    bad_y = subset_bad(a, y, fact)
    bad_both = both_bad(a, x, y, fact)

    ans = (total - bad_x - bad_y + bad_both) % MOD
    print(ans)

if __name__ == "__main__":
    solve()
```最初のヘルパーは、単一の不運な位置にヒットする順列の数を計算します。 標準的な中間集合サブセット合計カウントを実行しますが、単にサブセットをカウントするのではなく、次の基準でサブセットに重み付けを行います。`size!(n-size)!`。 

2 番目のヘルパーは、包含と除外の交差項を計算します。 各要素は、次の 3 つのグループのいずれかに割り当てられます。`S`、`U`、またはそのどちらでもない。 中間ミートでは、必要なターゲットを合計する 3 つの状態が結合されます。 

微妙な点は、重みが特定の要素ではなく、最終的なグループ サイズのみに依存することです。 これにより、すべての状態が同一になります`(sumS, sumU, sizeS, sizeU)`集約される情報。 

もう 1 つの詳細は、アンラッキーなポジションをソートされた順序で処理することです。 すべてのセグメントの長さは正であるため、両方の位置に到達するということは、
