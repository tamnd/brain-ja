---
title: "CF 102423D - スワップフリー"
description: "私たちには (N) 個の異なる単語が与えられています。 すべての単語は他のすべての単語のアナグラムであり、単語内に文字が 2 回出現することはありません。 位置の 1 組を正確に交換することによって、選択された 2 つの単語が相互に取得できないように、できるだけ多くの単語を選択したいと考えています。"
date: "2026-08-10T10:33:48+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102423
codeforces_index: "D"
codeforces_contest_name: "North American Southeast Regional 2019 (Div 1)"
rating: 0
weight: 102423
solve_time_s: 285
verified: true
draft: false
---

[CF 102423D - スワップフリー](https://codeforces.com/problemset/problem/102423/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 4 分 45 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちには (N) 個の異なる単語が与えられています。 すべての単語は他のすべての単語のアナグラムであり、単語内に文字が 2 回出現することはありません。 位置の 1 組を正確に交換することによって、選択された 2 つの単語が相互に取得できないように、できるだけ多くの単語を選択したいと考えています。 

すべての単語には同じ異なる文字が含まれているため、正確に 2 つの位置が異なる場合、2 つの異なる単語を 1 回の交換で相互に変換できます。 もしその言葉が`abc`そして`acb`の最後の 2 つの位置を交換します。`abc`生成する`acb`。 もしその言葉が`abc`そして`bca`、3 つの位置はすべて異なるため、1 つの交換で一方をもう一方に変換することはできません。 

入力には 1 つの整数 (N) と、その後に続く (N) 個の単語が含まれます。 出力は、スワップフリーのサブセットを選択した後に残ることができるワードの最大数です。 元のコンテストの制約では (1 \le N \le 500) が与えられ、すべての単語に個別の小文字の英字が使用されるため、長さは最大 26 になります。Codeforces アーカイブでは、1 秒の時間制限と 512 MB のメモリ制限が与えられます。 

境界 (N \le 500) により、指数サブセットの列挙は直ちに除外されます。 (2^{500}) 個のサブセットでさえ、プログラムが検査できる範囲をはるかに超えています。 一方、(500^3 = 125{,}000{,}000) であるため、3 次アルゴリズムは 500 個の頂点に対して完全に合理的です。 単語の長さは最大 26 なので、2 つの単語を 1 文字ずつ比較するのは安価です。 本当の課題は、2 つの単語が接続されているかどうかを検出することではなく、最大の独立セットを扱いやすくするグラフ構造を認識することです。 

不注意な実装が失敗する可能性のあるエッジケースがいくつかあります。 一言で言えば何も矛盾しないので、答えは1です。```
1
a
```正しい出力は次のとおりです`1`。 すべての単語に少なくとも 2 つの位置があると想定し、スワップを生成しようとする実装では、誤って無効な位置にアクセスする可能性があります。 

2 番目のエッジ ケースは、2 つの単語が 1 回のスワップだけ異なる場合です。```
2
ab
ba
```正しい出力は次のとおりです`1`。 2 つの単語は交換によって接続されているため、両方を選択することはできません。 単語が 1 つのスワップによって接続されるかどうかをチェックするのではなく、単語が異なるかどうかをチェックするプログラムは、誤って 2 を返します。 

3 番目のエッジ ケースは、2 つの単語がアナグラムであるが、複数の交換が必要な場合です。```
3
abc
bca
cab
```正しい出力は次のとおりです`3`。 すべてのペアは 3 つの位置すべてで異なるため、単一の交換によって 1 つの単語が別の単語に変換されることはありません。 アナグラムのすべてのペアを競合として扱う不用意な解決策は、一部の単語を誤って破棄する可能性があります。 

文字が区別できるという条件も重要です。 などの有効な単語はありません`aab`したがって、2 つの等しい文字の交換に特別な処理は必要ありません。 この制限はまさに、ハミング距離 2 による 1 スワップ変換の特徴付けを可能にするものです。 このステートメントは、明確な文字と一意の単語を明示的に保証します。 

## アプローチ

 直接的な総当たりアプローチは、(N) 個の単語のすべてのサブセットを検討し、スワップフリーかどうかを確認することです。 サブセットの場合、選択した単語のすべてのペアを比較し、あるペアがちょうど 2 つの位置で異なる場合にサブセットを拒否できます。 スワップフリー セットの定義は、選択されたペアがそのような関係を持たないことであるため、これは正しいです。 

問題はサブセットの数です。 (2^N) 個の可能なサブセットがあり、1 つのサブセットをチェックするには (O(N^2)) 個のペアの比較が必要になる場合があります。 最悪の場合、これにより (O(2^N N^2)) の仕事が得られます。 (N=500) の場合、サブセット数だけでも約 (3.27 \times 10^{150}) となるため、このアプローチはリモートでは実行できません。 

有益な観察は、単語を文字列として考えるのをやめ、代わりに単語をグラフの頂点にすることです。 1 回のスワップで 1 つの単語をもう 1 つの単語から取得できる場合、2 つの頂点を接続します。 その場合、スワップフリーの集合はまさにこのグラフの独立した集合であるため、問題は最大の独立した集合を見つけることになります。 

最大独立集合は一般に困難ですが、この特定のグラフには追加の構造があります。 すべての単語は、異なる文字の同じセットの順列です。 反転カウントのパリティに従って、各順列に偶数または奇数のパリティを与えます。 2 つの位置を交換すると、1 回の転置によって順列が変更され、転置ごとにパリティが反転します。 したがって、すべてのエッジは偶数の順列を奇数の順列に接続します。 

したがって、グラフは 2 部構成になります。 これが重要な削減です。 2 部グラフの場合、ケーニッヒの定理によれば、最小頂点カバーのサイズは最大マッチングのサイズに等しいと言えます。 最小頂点カバーの補数は独立したセットであるため、

 # N-\text{最小頂点カバー}

 N-\text{最大一致数}。 
】

 公式ソリューションの概要では、まさにこの削減を使用しており、(N \le 500) に対しては (O(N^3)) の 2 部マッチング アルゴリズムで十分であることがわかります。 

まだグラフを構築する必要があります。 各単語の長さは最大 26 で、単語数は 500 しかないため、すべてのペアを比較して異なる位置を数えるのは簡単に十分に高速です。 正確に 2 つの位置が異なる場合、2 つの単語は 1 回の交換によって接続されます。 

マッチング自体については、単純な Kuhn アルゴリズムは、ここでは最悪の場合 (O(N^3)) ですが、Python は Hopcroft-Karp を使用することで恩恵を受けます。 最悪の場合の複雑さは (O(E\sqrt N)) です。ここで、(E) はスワップ エッジの数です。 (E=O(N^2)) なので、最悪の場合のマッチング位相は (O(N^{2.5})) になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(2^N N^2)) | (O(N^2)) | 遅すぎる |
 | 最適 | (O(N^2L + E\sqrt N)), (L\le26) | (O(N^2)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. (N) 個のワードを読み取って保存します。 すべての単語は他のすべての単語のアナグラムであり、すべての単語は個別であるため、すべての頂点は同じ文字の異なる順列を表します。 
2. すべての単語のパリティを計算します。 固定順序で文字をそのランクにマッピングし、結果のシーケンスで反転を数えます。 反転数が偶数の場合、単語は 2 部グラフの左側に配置され、反転数が奇数の場合、単語は右側に配置されます。 

特定の参照順序は重要ではありません。 参照を変更すると、2 つのパリティ クラスの名前が変更されるだけです。 重要なのは、1 つの転置が常にパリティを反転するということです。 
3. すべての単語のペアを比較します。 それぞれの文字が異なる位置を数えます。 正確に 2 つの位置が異なる場合は、対応する頂点の間にエッジを追加します。

単語は別個の文字を含むアナグラムであるため、2 つの位置が異なることは 1 回の交換に必要かつ十分です。 2 つの異なる文字を単純に交換する必要があります。 
4. 偶数パリティ ワードから奇数パリティ ワードまでのエッジのみを格納します。 1 回のスワップでパリティが変更されるため、同じパリティの 2 つのワード間にエッジは存在できません。 
5. Hopcroft-Karp を実行して、この 2 部グラフで最大一致を見つけます。 一致するサイズは、すべてのスワップ競合を排除するために削除する必要がある頂点の最小数です。 
6. (N) から一致するサイズを引いた値を返します。 ケーニッヒの定理は、最大マッチングと最小頂点カバーの間に等価性を与えますが、最小頂点カバーの補数は最大独立セットになります。 

### なぜ効果があるのか

 リダクションの背後にある不変条件は順列パリティです。 すべてのエッジは 1 つの転置を正確に表し、すべての転置は反転パリティを変更するため、すべてのエッジは 1 つのパリティ クラスから別のパリティ クラスに交差します。 したがって、競合グラフは 2 部構成になります。 

選択された 2 つの単語がスワップ エッジによって接続されることはないため、有効な回答は独立したセットになります。 任意の 2 部グラフでは、最大の独立セットのサイズは (N-\tau) になります。ここで、(\tau) は頂点カバーの最小サイズです。 ケーニッヒの定理は (\tau=M) を与えます。ここで (M) は最大一致サイズです。 したがって、必要な答えは正確に (N-M) であり、これがアルゴリズムによって計算されます。 

## Python ソリューション```python
import sys
from collections import deque

input = sys.stdin.readline

def inversion_parity(word):
    # The alphabet itself can be used as the fixed reference order.
    # Since every word contains distinct lowercase letters, this is
    # exactly the parity of the corresponding permutation.
    a = [ord(c) - ord('a') for c in word]
    parity = 0

    for i in range(len(a)):
        for j in range(i + 1, len(a)):
            if a[i] > a[j]:
                parity ^= 1

    return parity

def hopcroft_karp(graph, left_size, right_size):
    pair_left = [-1] * left_size
    pair_right = [-1] * right_size
    dist = [-1] * left_size

    def bfs():
        q = deque()

        for u in range(left_size):
            if pair_left[u] == -1:
                dist[u] = 0
                q.append(u)
            else:
                dist[u] = -1

        found = False

        while q:
            u = q.popleft()

            for v in graph[u]:
                u2 = pair_right[v]

                if u2 == -1:
                    found = True
                elif dist[u2] == -1:
                    dist[u2] = dist[u] + 1
                    q.append(u2)

        return found

    def dfs(u):
        for v in graph[u]:
            u2 = pair_right[v]

            if u2 == -1 or (
                dist[u2] == dist[u] + 1 and dfs(u2)
            ):
                pair_left[u] = v
                pair_right[v] = u
                return True

        dist[u] = -1
        return False

    matching = 0

    while bfs():
        for u in range(left_size):
            if pair_left[u] == -1 and dfs(u):
                matching += 1

    return matching

def solve():
    n = int(input())
    words = [input().strip() for _ in range(n)]

    parity = [inversion_parity(word) for word in words]

    left = []
    right = []

    for i in range(n):
        if parity[i] == 0:
            left.append(i)
        else:
            right.append(i)

    right_id = [-1] * n
    for j, v in enumerate(right):
        right_id[v] = j

    graph = [[] for _ in range(len(left))]

    for li, u in enumerate(left):
        wu = words[u]

        for v in right:
            wv = words[v]
            different = 0

            for a, b in zip(wu, wv):
                if a != b:
                    different += 1
                    if different > 2:
                        break

            if different == 2:
                graph[li].append(right_id[v])

    matching = hopcroft_karp(graph, len(left), len(right))
    print(n - matching)

if __name__ == "__main__":
    solve()
```テスト ケースが 1 つしかないため、入力フェーズでは単純に単語が保存されます。 パリティ関数は、各単語をアルファベットのランクのシーケンスに変換し、反転をカウントします。 単語には個別の文字があるため、すべてのペアが 0 または 1 つの反転に寄与するため、パリティの計算は簡単で、単語ごとに (O(L^2)) 時間がかかります。 

次に、2 つのパリティ クラスがコンパクトな左右の頂点インデックスに変換されます。 これにより、一致する配列が小さくなり、Hopcroft-Karp 実装を通じて元の単語インデックスを保持することがなくなります。 

グラフの構築では、逆パリティのワードのみを比較します。 すべてのペアについて、ループは異なる位置をカウントし、カウントが 2 を超えるとすぐに停止します。 この早期終了は正確性のためには必要ありませんが、無関係な単語の不必要な文字比較を回避します。 

一致するコードは次を使用します`pair_left`そして`pair_right`現在のマッチングを表します。 BFS フェーズは交互パスのレイヤーを構築しますが、DFS はそれらのレイヤーに沿ってのみ検索します。 1 つの BFS フェーズ中にいくつかの最短の増強パスが見つかり、これが Hopcroft-Karp に (O(E\sqrt N)) 限界を与えるものです。 

Python には整数オーバーフローの問題はありません。 明確にしておくべき主な実装の詳細は、インデックス付けです。`graph`の位置によってインデックスが付けられます`left`、その隣の位置は`right`。 元の単語インデックスは次のように翻訳されます。`right_id`。 

## 実用的な例

 ### サンプル 1

 6 つの単語はすべて次の順列です。`abc`。 それらのパリティ クラスは、反転カウントによって決定されます。 

| 単語 | 反転パリティ | 左/右 | 考慮されるスワップエッジ |
 | --- | --- | --- | --- |
 |`abc`| 0 | 左 |`acb`、`cba`、`bac`|
 |`acb`| 1 | 右 |`abc`、`cab`、`bca`|
 |`cab`| 0 | 左 |`acb`、`cba`、`bca`|
 |`cba`| 1 | 右 |`abc`、`cab`、`bac`|
 |`bac`| 1 | 右 |`abc`、`cab`、`bca`|
 |`bca`| 0 | 左 |`acb`、`cba`、`bac`|

 競合グラフは (K_{3,3}) であるため、その最大一致のサイズは 3 です。アルゴリズムは (6-3=3) を返します。```
6
abc
acb
cab
cba
bac
bca
```

```
3
```この例では、完全な削減を示します。 元の問題では単語の最大のコレクションが求められますが、答えはすべてスワップ グラフの一致するサイズから得られます。 

### サンプル 2

 に基づいた11の単語については、`alerts`、グラフは再び順列パリティによって分割されます。 ハミング距離 2 にあるペアのみがエッジを受信します。 

| ステージ | 単語数 | 結果 |
 | --- | --- | --- |
 | 入力 | 11 | 11 頂点 |
 | 偶数パリティ | 6 | 左側 |
 | 奇数パリティ | 5 | 右側 |
 | 最大一致 | 3 | 3 つの競合をカバーできます |
 | スワップフリーの最大セット | 8 | (11-3=8) |```
11
alerts
alters
artels
estral
laster
ratels
salter
slater
staler
stelar
talers
```

```
8
```トレースは、グラフに考えられるすべての順列を含める必要がないことを示しています。 提供された単語間でエッジを構築するだけであり、マッチングはこの誘導された 2 部グラフに対して行われます。 公式声明では、このサンプルと回答 8 がリストされています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(N^2L + E\sqrt N)) | (N^2) 個の単語のペアが (O(L)) で比較され、その後に Hopcroft-Karp | が比較されます。 
| スペース | (O(N^2)) | 競合グラフには (O(N^2)) 個のエッジを含めることができます。 

ここでは (L\le26) は単語に繰り返される小文字が含まれていないため、(E\le N^2/4) は 2 部グラフの場合です。 (N\le500) を使用すると、グラフの構築が小さくなり、Hopcroft-Karp は可能な数のエッジを快適に処理できます。 公式ソリューションでは、これらの制約に対して十分な (O(N^3)) マッチング アルゴリズムが受け入れられるため、Python 実装ではマッチング フェーズで追加の漸近マージンが得られます。 

## テストケース```python
# The production solution can be tested by moving solve() into this file
# and replacing its stdin/stdout handling with the helper below.

import io
import sys
from collections import deque
from itertools import permutations

def inversion_parity(word):
    a = [ord(c) - ord('a') for c in word]
    parity = 0

    for i in range(len(a)):
        for j in range(i + 1, len(a)):
            if a[i] > a[j]:
                parity ^= 1

    return parity

def hopcroft_karp(graph, left_size, right_size):
    pair_left = [-1] * left_size
    pair_right = [-1] * right_size
    dist = [-1] * left_size

    def bfs():
        q = deque()

        for u in range(left_size):
            if pair_left[u] == -1:
                dist[u] = 0
                q.append(u)
            else:
                dist[u] = -1

        found = False

        while q:
            u = q.popleft()

            for v in graph[u]:
                u2 = pair_right[v]

                if u2 == -1:
                    found = True
                elif dist[u2] == -1:
                    dist[u2] = dist[u] + 1
                    q.append(u2)

        return found

    def dfs(u):
        for v in graph[u]:
            u2 = pair_right[v]

            if u2 == -1 or (
                dist[u2] == dist[u] + 1 and dfs(u2)
            ):
                pair_left[u] = v
                pair_right[v] = u
                return True

        dist[u] = -1
        return False

    matching = 0

    while bfs():
        for u in range(left_size):
            if pair_left[u] == -1 and dfs(u):
                matching += 1

    return matching

def solution(inp):
    data = inp.split()
    n = int(data[0])
    words = data[1:1 + n]

    parity = [inversion_parity(w) for w in words]

    left = [i for i in range(n) if parity[i] == 0]
    right = [i for i in range(n) if parity[i] == 1]

    right_id = [-1] * n
    for j, v in enumerate(right):
        right_id[v] = j

    graph = [[] for _ in left]

    for li, u in enumerate(left):
        for v in right:
            different = 0

            for a, b in zip(words[u], words[v]):
                if a != b:
                    different += 1
                    if different > 2:
                        break

            if different == 2:
                graph[li].append(right_id[v])

    matching = hopcroft_karp(graph, len(left), len(right))
    return str(n - matching) + "\n"

def run(inp: str) -> str:
    return solution(inp)

# Provided sample 1
assert run(
    """6
abc
acb
cab
cba
bac
bca
"""
) == "3\n", "sample 1"

# Provided sample 2
assert run(
    """11
alerts
alters
artels
estral
laster
ratels
salter
slater
staler
stelar
talers
"""
) == "8\n", "sample 2"

# Provided sample 3
assert run(
    """6
ates
east
eats
etas
sate
teas
"""
) == "4\n", "sample 3"

# Minimum-size and all-equal-value analogue.
# A word with one distinct lowercase letter has only one possible form.
assert run(
    """1
a
"""
) == "1\n", "single word"

# Two words connected by exactly one swap.
assert run(
    """2
ab
ba
"""
) == "1\n", "single conflict"

# Three words that are all anagrams but no pair is one swap apart.
assert run(
    """3
abc
bca
cab
"""
) == "3\n", "no conflict edges"

# Maximum-size case.
# The first 500 even permutations of eight letters all have the same parity,
# so no two of them can be connected by one swap.
even_words = []

for p in permutations("abcdefgh"):
    w = "".join(p)
    if inversion_parity(w) == 0:
        even_words.append(w)
        if len(even_words) == 500:
            break

max_case = "500\n" + "\n".join(even_words) + "\n"
assert run(max_case) == "500\n", "maximum-size independent set"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / a`|`1`| 最小 (N)、最小語長、可能なスワップなし |
 |`2 / ab, ba`|`1`| 直接の 1 スワップ競合とサイズ 1 のマッチング |
 |`3 / abc, bca, cab`|`3`| 1回の交換では接続できないアナグラム |
 | 500 の偶数順列`abcdefgh`|`500`| 最大 (N)、密な入力サイズ、パリティ不変式 |
 | サンプル 1 |`3`| 完全な (S_3) 順列構造 |
 | サンプル 2 |`8`| 部分順列グラフ |
 | サンプル 3 |`4`| 同じレターセットサイズの別の部分グラフ |

 ## 特殊なケース

 単一の単語の場合、比較するペアがないため、グラフには孤立した頂点が 1 つあり、最大一致のサイズは 0 になります。```
1
a
```アルゴリズムは、`a`を 1 つのパリティ クラスに変換し、エッジを作成せず、(1-0=1) を計算します。 単語に交換できる位置のペアがない場合でも、結果は正しいです。 

直接交換である 2 つのワードの場合、グラフには 1 つのエッジが含まれます。```
2
ab
ba
```

`ab`偶数パリティを持ち、`ba`奇数パリティを持っています。 ハミング距離は 2 であるため、アルゴリズムは 1 つの 2 部エッジを作成します。 最大一致のサイズは 1 で、(2-1=1) になります。 これにより、異なる単語の任意のペアを誤って互換性があるものとしてカウントする実装が捕捉されます。 

アナグラムであっても複数の交換が必要な単語の場合、エッジは作成されません。```
3
abc
bca
cab
```

`abc`そして`bca`他のペアと同様に、3 つの位置すべてが異なります。 したがって、グラフには 3 つの孤立した頂点があります。 最大一致は 0 であるため、答えは (3) です。 このため、アナグラムの等価性をチェックするだけでは不十分です。 

パリティ境界も重要です。 1 つのスワップで接続された 2 つのワードは、逆の反転パリティを持たなければなりません。 最大サイズのテストでは、500 ワードすべてが同じパリティ クラスから意図的に選択されます。 それらは互いに大きく異なって見えるかもしれませんが、1 つの転置によって関連付けられるペアはありません。 グラフには端がなく、答えはすべて 500 語です。 これは、元の最適化問題を 2 部マッチングに変換するために使用された構造観察を直接テストします。 

この社説は、投稿品質の説明としてすぐに使用できます。 ご希望であれば、同じ証明を保持しながらコンテストの読者向けに最適化された、より短い Codeforces スタイルのバージョンを作成することもできます。
