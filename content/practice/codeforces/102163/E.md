---
title: "CF 102163E - アドナンと焼けたドライバー"
description: "小文字の変更可能な文字列を維持します。 更新により、1 つの位置が指定された文字に変更されます。 クエリでは範囲 ([l,r]) が与えられ、その範囲内の部分文字列が両方向から同じように読み取られるかどうかを判断する必要があります。"
date: "2026-08-19T07:46:27+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102163
codeforces_index: "E"
codeforces_contest_name: "NCD 2019"
rating: 0
weight: 102163
solve_time_s: 151
verified: true
draft: false
---

[CF 102163E - Adnan と焼けたドライバー](https://codeforces.com/problemset/problem/102163/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 31 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 小文字の変更可能な文字列を維持します。 更新により、1 つの位置が指定された文字に変更されます。 クエリでは範囲 ([l,r]) が与えられ、その範囲内の部分文字列が両方向から同じように読み取られるかどうかを判断する必要があります。 

たとえば、現在の文字列が`abacaba`、範囲 ([2,6]) には以下が含まれます`bacab`、これは回文です。 長さ 1 の範囲は、前方表現と後方表現に同じ 1 文字が含まれるため、常に回文になります。 

1 つのテスト ケースには、最大 (10^5) 個の位置と (10^5) 個のイベントを含めることができます。 これだけ多くのイベントがあると、すべてのクエリのすべての文字をチェックするのはコストがかかりすぎます。 最悪の場合、(10^5) クエリはそれぞれ (10^5) 文字を検査し、おおよそ (10^{10}) 文字の比較を行う可能性があります。 これは、一般的なコンテストの制限時間の許容範囲をはるかに超えています。 更新と回文クエリの両方を対数時間に近づける必要があります。 

最初の境界ケースは単一文字のクエリです。 例えば：```
1
1 1
a
2 1 1
```答えは次のとおりです。```
Adnan Wins
```少なくとも 2 文字を想定する比較ルーチンでは、このケースが誤って拒否される可能性があります。 

もう 1 つの簡単な間違いは、アップデートによってキャラクターがすでに持っているのと同じキャラクターに変更される可能性があることを忘れていることです。 例えば：```
1
3 2
aba
1 2 b
2 1 3
```答えは`Adnan Wins`。 更新によって文字列は変更されないため、回文ステータスは変更されないままでなければなりません。 すべての更新を構造的な変更として扱う実装は依然として正しい可能性がありますが、誤った増分調整を適用するのではなく、格納された値を上書きする必要があります。 

最も一般的な境界エラーは、クエリされた間隔が文字列のどちらかの端に触れた場合に発生します。 例えば：```
1
5 2
abcba
2 1 5
2 2 4
```どちらの答えも`Adnan Wins`。 内部表現では通常、半開区間が使用されるため、ゼロベースのインデックスを使用する実装では、包括的な入力範囲を慎重に変換する必要があります。 

最後に、部分文字列は一致する文字数を持つことができますが、それでも回文にはなりません。 例えば、`aabb`2つ含まれています`a`キャラクターと二人`b`文字ですが、回文ではありません。 周波数ベースのソリューションは、それを誤って受け入れてしまいます。 

## アプローチ

 直接的な解決策は、クエリされた部分文字列を両端から検査することです。 クエリ ([l,r]) の場合は、(l) と (r) の文字を比較し、次に (l+1) と (r-1) を比較し、2 つのポインタが一致するまで続けます。 これは正しいです。ミラー化されたすべてのペアに等しい文字が含まれている場合、文字列は回文になるからです。 

問題は仕事量です。 長さ (k) の部分文字列に対するクエリには (O(k)) 時間がかかります。 文字列に (10^5) 文字が含まれており、(10^5) に近い長さの範囲に対して (10^5) クエリを実行する場合、最悪の場合は約 (5 \times 10^9) 文字の比較になります。 ポイント更新ではこの状況は改善されません。 

有用な観察は、回文を前方と後方に読んだときにまったく同じ順序になるということです。 これらの文字を 1 つずつ比較する代わりに、ローリング ハッシュで部分文字列全体を表すことができます。 すべてのセグメントの順方向ハッシュと、同じセグメントの逆方向ハッシュを維持します。 2 つのハッシュが等しい場合、部分文字列は回文として扱われます。 

セグメント ツリーは、そのノードが文字列の連続した部分を表すため、自然に適合します。 各ノードは、そのセグメントのハッシュを左から右、右から左に格納します。 2 つの隣接するセグメントが結合されると、ハッシュ基底の累乗を使用して、それらのハッシュを一定時間で組み合わせることができます。 

ポイントの更新は、変更されたリーフからルートまでのパス上の (O(\log N)) 個のセグメント ツリー ノードにのみ影響します。 範囲クエリは (O(\log N)) 個の関連ノードを訪問し、それらのハッシュを元の順序で結合します。 結果として得られる順方向ハッシュと逆方向ハッシュを比較できます。 

ハッシュ比較は、標準的なローリング ハッシュの意味で確率的です。 2 つの異なる大きな素数係数を使用すると、偶発的な衝突が非常に起こりにくくなります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | クエリごとに (O(N))、最悪の場合は (O(NE)) | (O(N)) | 遅すぎる |
 | セグメント ツリー + ダブル ハッシュ | イベントごとに (O(\log N)) | (O(N)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 選択した両方の素数を法とするハッシュ基底の累乗を事前計算します。 長さ (k) のセグメントを別のセグメントと組み合わせる場合は (B^k) が必要なので、これらのべき乗を一度計算すると、累乗を繰り返す必要がなくなります。 
2. 現在の文字列にセグメント ツリーを構築します。 葉は 1 つの文字を表します。 すべてのノードについて、その順方向ハッシュ、逆方向ハッシュ、およびセグメント長を保存します。 リーフの場合、どちらのハッシュもその文字に割り当てられた単なる数値です。 
3. 左側の子 (A) と右側の子 (C) をマージする場合、それらの長さが (x) と (y) であると仮定します。 ハッシュが次のように定義されている場合
 [
 H(s)=\sum_{i=0}^{|s|-1} 値(s_i)B^i、
 】
 (AC) のフォワード ハッシュは次のようになります。 
[
 H(A)+B^xH(C)。 
】
 逆ハッシュも同じ方法で形成されますが、左右の部分が逆の概念的な順序で表示されます。 
[
 RH(C)+B^yRH(A)。 
】
 どちらの式も一定の時間がかかります。 
4. アップデートについて`1 i c`、(i) をセグメント ツリーのインデックス付け規則に変換し、対応するリーフを次の値に置き換えます。`c`。 マージ式を使用してすべての祖先を再計算します。 ポイントは 1 つのルートからリーフへのパスに属しているため、(O(\log N)) 個のノードのみが変更されます。 
5. お問い合わせの場合`2 l r`、正確にその間隔の集約ノード情報を取得します。 複数の部分が返された場合は、同じマージ操作を使用してそれらを左から右の順序で連結します。 したがって、クエリは完全な部分文字列に対して 1 つの順方向ハッシュと 1 つの逆方向ハッシュを生成します。 
6. 両方の係数の下で、結果として得られる 2 つのハッシュを比較します。 両方が一致する場合は印刷します`Adnan Wins`; それ以外の場合は印刷します`ARCNCD!`。 回文には同一の順方向シーケンスと逆方向シーケンスがあるため、それらのハッシュは一致する必要があります。 ダブルハッシュでは、非回文が両方の比較に合格する可能性はほとんどありません。 

これが機能する理由: セグメント ツリーの不変条件は、すべてのノードがその表現された部分文字列のローリング ハッシュを正確に保存し、その部分文字列のローリング ハッシュを反転したものを正確に保存することです。 マージ式は、2 つの隣接するセグメントが結合されるときに、この不変条件を保持します。 ポイント更新では、影響を受けるパスを再構築することでパスが保存され、範囲クエリでは、選択したセグメントを元の順序で連結することでパスが保存されます。 したがって、最後の順方向ハッシュはクエリされた部分文字列を表し、最後の逆方向ハッシュは同じ部分文字列を逆方向に表します。 ハッシュが等しいとは、2 つの表現が一致することを意味し、これはまさに回文条件であり、二重ハッシュ衝突の確率が無視できる程度に達します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD1 = 1_000_000_007
MOD2 = 1_000_000_009
BASE = 911382323

def solve_case(n, q, s, queries):
    size = 1
    while size < n:
        size <<= 1

    # Powers used when concatenating hashes.
    pow1 = [1] * (n + 1)
    pow2 = [1] * (n + 1)

    for i in range(1, n + 1):
        pow1[i] = pow1[i - 1] * BASE % MOD1
        pow2[i] = pow2[i - 1] * BASE % MOD2

    total = size << 1

    # Forward hashes.
    hf1 = [0] * total
    hf2 = [0] * total

    # Reverse hashes.
    hr1 = [0] * total
    hr2 = [0] * total

    # Segment lengths.
    length = [0] * total

    def value(ch):
        return ord(ch) - 96

    # Leaves.
    for i, ch in enumerate(s):
        p = size + i
        v = value(ch)

        hf1[p] = v
        hf2[p] = v
        hr1[p] = v
        hr2[p] = v
        length[p] = 1

    # Padding leaves have length zero.
    for p in range(size - 1, 0, -1):
        left = p << 1
        right = left | 1

        ll = length[left]
        lr = length[right]
        length[p] = ll + lr

        hf1[p] = (hf1[left] + pow1[ll] * hf1[right]) % MOD1
        hf2[p] = (hf2[left] + pow2[ll] * hf2[right]) % MOD2

        hr1[p] = (hr1[right] + pow1[lr] * hr1[left]) % MOD1
        hr2[p] = (hr2[right] + pow2[lr] * hr2[left]) % MOD2

    def pull(p):
        left = p << 1
        right = left | 1

        ll = length[left]
        lr = length[right]
        length[p] = ll + lr

        hf1[p] = (hf1[left] + pow1[ll] * hf1[right]) % MOD1
        hf2[p] = (hf2[left] + pow2[ll] * hf2[right]) % MOD2

        hr1[p] = (hr1[right] + pow1[lr] * hr1[left]) % MOD1
        hr2[p] = (hr2[right] + pow2[lr] * hr2[left]) % MOD2

    def update(pos, ch):
        p = size + pos
        v = value(ch)

        hf1[p] = v
        hf2[p] = v
        hr1[p] = v
        hr2[p] = v
        length[p] = 1

        p >>= 1
        while p:
            pull(p)
            p >>= 1

    def merge(a, b):
        # Each item is:
        # (forward_hash_1, forward_hash_2,
        #  reverse_hash_1, reverse_hash_2, length)
        if a[4] == 0:
            return b
        if b[4] == 0:
            return a

        a1, a2, ar1, ar2, la = a
        b1, b2, br1, br2, lb = b

        return (
            (a1 + pow1[la] * b1) % MOD1,
            (a2 + pow2[la] * b2) % MOD2,
            (br1 + pow1[lb] * ar1) % MOD1,
            (br2 + pow2[lb] * ar2) % MOD2,
            la + lb
        )

    def query(left, right):
        # Convert [left, right) into segment-tree coordinates.
        left += size
        right += size

        res_left = (0, 0, 0, 0, 0)
        res_right = (0, 0, 0, 0, 0)

        while left < right:
            if left & 1:
                node = (
                    hf1[left], hf2[left],
                    hr1[left], hr2[left],
                    length[left]
                )
                res_left = merge(res_left, node)
                left += 1

            if right & 1:
                right -= 1
                node = (
                    hf1[right], hf2[right],
                    hr1[right], hr2[right],
                    length[right]
                )
                res_right = merge(node, res_right)

            left >>= 1
            right >>= 1

        return merge(res_left, res_right)

    output = []

    for typ, x, y in queries:
        if typ == 1:
            update(x - 1, y)
        else:
            # Input uses inclusive [x, y].
            # query() uses half-open [x - 1, y).
            h1, h2, rh1, rh2, _ = query(x - 1, y)

            if h1 == rh1 and h2 == rh2:
                output.append("Adnan Wins")
            else:
                output.append("ARCNCD!")

    return "\n".join(output)

def main():
    t = int(input())
    answers = []

    for _ in range(t):
        n, q = map(int, input().split())
        s = input().strip()

        queries = []
        for _ in range(q):
            parts = input().split()
            typ = int(parts[0])

            if typ == 1:
                queries.append((1, int(parts[1]), parts[2]))
            else:
                queries.append((2, int(parts[1]), int(parts[2])))

        answers.append(solve_case(n, q, s, queries))

    sys.stdout.write("\n".join(answers))

if __name__ == "__main__":
    main()
```すべてのマージには (B^k) が必要であるため、2 つのべき乗配列が最初に構築されます。ここで、(k) は子セグメントの 1 つの長さです。 クエリされる長さはすべて最大 (N) であるため、(N+1) 乗で十分です。 

セグメント ツリーは、葉が次から始まる反復レイアウトを使用します。`size`。 実際の文字列は最初 (N) 個のリーフを占めますが、追加のリーフは次の理由で作成されます。`size`2 のべき乗は空のままです。 これらの長さはゼロであるため、マージには何も寄与しません。 

の`pull`関数はセグメント不変式を実装します。 順方向の場合、左側のセグメントは現在の指数のままで、右側のセグメントは左側のセグメントの長さだけシフトされます。 逆方向の場合は、右のセグメントが最初になります。`left + right`は`reverse(right) + reverse(left)`。 

クエリ ルーチンは 2 つのアキュムレータを使用します。`res_left`左側から見つかった選択されたノードを受け取り、それらを通常どおり追加します。`res_right`右側から見つかった選択されたノードを受け取り、各新しいノードを先頭に追加します。 セグメント ツリーのトラバーサルでは、選択されたすべてのノードが左から右に必ずしも遭遇するとは限らないため、この順序付けが必要です。 

入力間隔は包括的ですが、内部クエリ関数は半開間隔を使用します。 したがって、入力クエリは`[l, r]`になる`query(l - 1, r)`。 この 1 回の変換が、他の方法では簡単に発生するいくつかの off-by-one バグの原因となります。 

Python の整数はオーバーフローしませんが、ハッシュ値は選択されたモジュラー範囲内に収まる必要があります。 ハッシュ式のすべての乗算と加算の後には、`% MOD1`または`% MOD2`。 通常の Python の任意精度演算を超える整数オーバーフローの懸念はありません。 

この実装では、両方の方向と両方の係数がツリーに直接保存されます。 これは単一のハッシュを保存するよりも多くのメモリを使用しますが、(O(N)) のままであり、(N \le 10^5) の 256 MB 制限に余裕で適合します。 

## 実用的な例

 提供されたサンプルの場合、最初の文字列は次のとおりです。`adaersd`。 位置 5 の更新が変更されます`r`に`a`、生産`adaeasd`。 クエリ ([3,5]) は次のとおりです。`aea`、これは回文です。 

| イベント | 操作 | 現在の文字列 | クエリされた部分文字列 | 前進＝後進？ | 出力 |
 | --- | --- | --- | --- | --- | --- |
 | 1 |`1 5 a`|`adaeasd`| | | |
 | 2 |`2 3 5`|`adaeasd`|`aea`| はい |`Adnan Wins`|
 | 3 |`2 1 6`|`adaeasd`|`adaeas`| いいえ |`ARCNCD!`|
 | 4 |`1 1 d`|`ddaeasd`| | | |
 | 5 |`2 1 2`|`ddaeasd`|`dd`| はい |`Adnan Wins`|

 トレースは、ツリーが単なる元の文字列ではなく、更新のたびに現在の文字列を表していることを示しています。 最後のクエリでも、最初の位置から始まる範囲が実行されます。 

2 番目の例は、1 回の更新後に回文が非回文になることを示しています。```
1
5 3
abcba
2 1 5
1 3 d
2 1 5
```| イベント | 操作 | 現在の文字列 | クエリ | フォワードハッシュ | 逆ハッシュ | 出力 |
 | --- | --- | --- | --- | --- | --- | --- |
 | 1 |`2 1 5`|`abcba`|`[1,5]`| 等しい | 等しい |`Adnan Wins`|
 | 2 |`1 3 d`|`abdba`| | | | |
 | 3 |`2 1 5`|`abdba`|`[1,5]`| 等しい | 等しい |`Adnan Wins`|

 この特定の更新では回文が保存されています。`abdba`も対称的です。 失敗したクエリも示すために、更新を位置 2 に変更します。```
1
3 2
aba
1 2 c
2 1 3
```| イベント | 操作 | 現在の文字列 | クエリ | 結果 |
 | --- | --- | --- | --- | --- |
 | 1 |`1 2 c`|`aca`| | |
 | 2 |`2 1 3`|`aca`|`[1,3]`|`Adnan Wins`|

 不変式は両方のトレースで確認できます。クエリされた部分文字列が対称である場合は常に、その順方向表現と逆方向表現は同一です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(N + E\log N)) | ツリーとパワーの構築には (O(N)) かかりますが、すべての更新とクエリには (O(\log N)) かかります。 |
 | スペース | (O(N)) | 累乗およびすべてのセグメント ツリー配列には、(O(N)) 個の要素が含まれます。 |

 (N,E \le 10^5) を使用すると、アルゴリズムはイベントごとに対数的に多くのツリー演算のみを実行します。 セグメント ツリー レベルの合計数は (10^5) 要素に対して約 17 であるため、数十億の文字比較ではなく、おおよそ (O(10^5 \log 10^5)) 回のノード操作が必要になります。 メモリ使用量は線形で、256 MB の制限内に収まります。 

## テストケース```python
# This test harness contains the same algorithm as the submission,
# exposed through run() so that the assertions can execute it.

import sys
import io

MOD1 = 1_000_000_007
MOD2 = 1_000_000_009
BASE = 911382323

def solve_case(n, q, s, queries):
    size = 1
    while size < n:
        size <<= 1

    pow1 = [1] * (n + 1)
    pow2 = [1] * (n + 1)

    for i in range(1, n + 1):
        pow1[i] = pow1[i - 1] * BASE % MOD1
        pow2[i] = pow2[i - 1] * BASE % MOD2

    total = size << 1
    hf1 = [0] * total
    hf2 = [0] * total
    hr1 = [0] * total
    hr2 = [0] * total
    length = [0] * total

    def value(ch):
        return ord(ch) - 96

    for i, ch in enumerate(s):
        p = size + i
        v = value(ch)
        hf1[p] = hf2[p] = hr1[p] = hr2[p] = v
        length[p] = 1

    def pull(p):
        left = p << 1
        right = left | 1

        ll = length[left]
        lr = length[right]
        length[p] = ll + lr

        hf1[p] = (hf1[left] + pow1[ll] * hf1[right]) % MOD1
        hf2[p] = (hf2[left] + pow2[ll] * hf2[right]) % MOD2
        hr1[p] = (hr1[right] + pow1[lr] * hr1[left]) % MOD1
        hr2[p] = (hr2[right] + pow2[lr] * hr2[left]) % MOD2

    for p in range(size - 1, 0, -1):
        pull(p)

    def update(pos, ch):
        p = size + pos
        v = value(ch)
        hf1[p] = hf2[p] = hr1[p] = hr2[p] = v
        length[p] = 1

        p >>= 1
        while p:
            pull(p)
            p >>= 1

    def merge(a, b):
        if a[4] == 0:
            return b
        if b[4] == 0:
            return a

        a1, a2, ar1, ar2, la = a
        b1, b2, br1, br2, lb = b

        return (
            (a1 + pow1[la] * b1) % MOD1,
            (a2 + pow2[la] * b2) % MOD2,
            (br1 + pow1[lb] * ar1) % MOD1,
            (br2 + pow2[lb] * ar2) % MOD2,
            la + lb
        )

    def query(left, right):
        left += size
        right += size

        a = (0, 0, 0, 0, 0)
        b = (0, 0, 0, 0, 0)

        while left < right:
            if left & 1:
                a = merge(a, (
                    hf1[left], hf2[left],
                    hr1[left], hr2[left],
                    length[left]
                ))
                left += 1

            if right & 1:
                right -= 1
                b = merge((
                    hf1[right], hf2[right],
                    hr1[right], hr2[right],
                    length[right]
                ), b)

            left >>= 1
            right >>= 1

        return merge(a, b)

    ans = []

    for typ, x, y in queries:
        if typ == 1:
            update(x - 1, y)
        else:
            h1, h2, rh1, rh2, _ = query(x - 1, y)
            if h1 == rh1 and h2 == rh2:
                ans.append("Adnan Wins")
            else:
                ans.append("ARCNCD!")

    return "\n".join(ans)

def run(inp: str) -> str:
    data = io.StringIO(inp)
    t = int(data.readline())
    all_answers = []

    for _ in range(t):
        n, q = map(int, data.readline().split())
        s = data.readline().strip()

        queries = []
        for _ in range(q):
            parts = data.readline().split()
            if parts[0] == "1":
                queries.append((1, int(parts[1]), parts[2]))
            else:
                queries.append((2, int(parts[1]), int(parts[2])))

        all_answers.append(solve_case(n, q, s, queries))

    return "\n".join(all_answers)

# Provided sample.
sample1 = """\
1
7 5
adaersd
1 5 a
2 3 5
2 1 6
1 1 d
2 1 2
"""

assert run(sample1) == """\
Adnan Wins
ARCNCD!
Adnan Wins
""".strip(), "sample 1"

# Minimum size and length-one palindrome.
case2 = """\
1
1 3
a
2 1 1
1 1 z
2 1 1
"""

assert run(case2) == """\
Adnan Wins
Adnan Wins
""".strip(), "minimum size"

# All equal characters remain palindromes after updates.
case3 = """\
1
5 4
aaaaa
2 1 5
1 3 a
2 2 4
2 1 4
"""

assert run(case3) == """\
Adnan Wins
Adnan Wins
Adnan Wins
""".strip(), "all equal"

# Boundary queries and a change that destroys the palindrome.
case4 = """\
1
5 4
abcba
2 1 5
2 2 4
1 1 z
2 1 5
"""

assert run(case4) == """\
Adnan Wins
Adnan Wins
ARCNCD!
""".strip(), "boundary and update"

# Maximum-size construction. The first query is a palindrome,
# then one endpoint changes and the full-range query must fail.
n = 100000
case5 = (
    "1\n"
    f"{n} 3\n"
    + "a" * n
    + "\n2 1 100000\n"
    + "1 1 b\n"
    + "2 1 100000\n"
)

assert run(case5) == """\
Adnan Wins
ARCNCD!
""".strip(), "maximum size"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 |`Adnan Wins`、`ARCNCD!`、`Adnan Wins`| 公式更新と範囲クエリのシーケンス |
 |`N=1`、1 文字の範囲 |`Adnan Wins`二度 | 最小サイズとシングルトン間隔 |
 |`aaaaa`| 三つ`Adnan Wins`答え | すべて等しい値と変更されていない更新 |
 |`abcba`エンドポイントの更新を伴う |`Adnan Wins`、`Adnan Wins`、`ARCNCD!`| 全範囲の境界と更新の伝播 |
 |`100000`等しい文字 |`Adnan Wins`、`ARCNCD!`| 最大サイズとパフォーマンス |

 ## 特殊なケース

 シングルトン間隔には、ツリー内で特別なケースは必要ありません。 入力用```
1
1 1
a
2 1 1
```内部間隔は`[0,1)`, したがって、クエリは長さ 1 のセグメントを返します。 その順方向ハッシュと逆方向ハッシュは両方とも次の値です。`a`、そしてプログラムは出力します`Adnan Wins`。 

現在の文字を再度割り当てる更新は、リーフを同じ値に置き換え、その祖先を再構築することによって処理されます。 のために```
1
3 2
aba
1 2 b
2 1 3
```文字列は残ります`aba`したがって、全範囲のハッシュは等しいままとなり、出力は次のようになります。`Adnan Wins`。 実装では、更新ごとに値が変更されるとは想定していません。 

右側の境界に触れるクエリは、包含インデックスからハーフオープンインデックスへの変換を実行します。 のために```
1
5 2
abcba
2 1 5
2 2 4
```最初のクエリは次のようになります`[0,5)`そして2番目は次のようになります`[1,4)`。 それらの部分文字列は次のとおりです。`abcba`そして`bcb`、それぞれ、両方とも一致する順方向ハッシュと逆方向ハッシュを持ちます。 どちらの出力も`Adnan Wins`。 

対称的な文字頻度を持つ非回文は、カウントのみに基づいたアプローチを捕捉します。 例えば、```
1
4 1
aabb
2 1 4
```生成する`ARCNCD!`。 順方向シーケンスは`aabb`、その逆は`bbaa`。 セグメント ツリーはこれら 2 つの異なるハッシュを保存するため、たとえ頻度が高くても範囲を拒否します。`a`そして`b`は同一です。 

完全な文字列更新の場合は、変更がルートまで伝播するかどうかもチェックします。 から始まる`abcba`、位置 1 を に変更します。`z`生成する`zbcba`。 クエリ終了`[1,5]`次に比較します`zbcba`に対して`abcbz`、それらは異なるため、答えは次のようになります。`ARCNCD!`。 これにより、更新パスが、変更された位置を含む部分文字列が格納されているすべての祖先を正しく再構築することが確認されます。
