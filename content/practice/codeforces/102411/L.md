---
title: "CF 102411L - 長さと期間"
description: "長さが最大 ​​200000 の文字列 w があります。その中で最も繰り返しの多い部分文字列を見つけたいと考えています。ここで、繰り返しは次のコピーの途中で停止できます。 部分文字列の周期が p で長さが L であるとします。その指数は L / p です。"
date: "2026-08-11T07:53:30+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102411
codeforces_index: "L"
codeforces_contest_name: "ICPC 2019-2020 North-Western Russia Regional Contest"
rating: 0
weight: 102411
solve_time_s: 722
verified: true
draft: false
---

[CF 102411L - 長さと期間](https://codeforces.com/problemset/problem/102411/L)

 **評価:** -
 **タグ:** -
 **解決時間:** 12 分 2 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 文字列があります`w`せいぜい長さの`200000`。 次のコピーの途中で繰り返しが停止できる、その中で最も繰り返しの多い部分文字列を見つけたいと考えています。 

部分文字列にピリオドがあるとします`p`そして長さ`L`。 その指数は`L / p`。 例えば、`abababa`期間あり`2`なぜなら、両方の位置が存在する場合、すべての文字は 2 番目の位置の文字と一致するからです。 その指数は`7 / 2`。 タスクは、すべての部分文字列およびすべての有効期間にわたってこの比率を最大化することです。 

答えは必ずしも整数である必要はありません。 部分文字列には、ピリオドの完全なコピーをいくつか含めて、さらにもう 1 つのコピーの接頭辞を含めることができます。 必要な出力は、最小の項に換算された最大比率です。 

長さの限界`200000`すべての部分文字列を明示的に調べてその文字を比較するものはすべて除外されます。 すでに約`n²/2`部分文字列なので、部分文字列ごとに一定の作業を行うだけでも多すぎます。 周りのアルゴリズム`O(n log n)`2 秒の制限に適しています。 最終的なソリューションでは、`O(n log n)`サフィックス配列構築、線形時間 LCP 構築、および予想される`O(n log n)`集合ユニオンの合計作業量。 

誤って処理されやすい小さなケースがいくつかあります。 のために`a`、明確な接尾辞のペアや繰り返しはありませんが、答えは依然として次のとおりです。`1/1`1 つの文字には指数 1 があるためです。 答えをゼロに初期化する解法では、誤って無効な分数が生成される可能性があります。 

のために`abc`、部分文字列はそれ自体の長さよりも短い期間を持たないため、答えは次のようになります。`1/1`。 繰り返されるペアのみを検索するソリューションでは、何も見つからない場合でも、ペアを返す必要があります。 

のために`aba`、文字列全体にピリオドがあります`2`: 最初の 2 文字は`ab`、残りの文字は接頭辞です`a`その時代の。 その指数は`3/2`。 完全な繰り返しのみを考慮するソリューションでは、この部分的な答えが見逃され、誤った結果が返されます。`1/1`。 

のために`aaaa`、文字列全体にピリオドがあります`1`、指数を与える`4/1`。 このケースでは、すべてのサフィックスのペアに長い共通のプレフィックスがあるため、非効率な実装も明らかになります。 すべてのペアにわたる文字ごとの比較は三次的になります。 

## アプローチ

 直接アプローチでは 2 つの位置を列挙できます`i < j`そして考慮してください`j - i`候補期間として。 次に比較します`w[i:]`そして`w[j:]`文字ごとに、最長の共通プレフィックスを見つけます。 その LCP に長さがある場合`L`、で始まる部分文字列`i`そしてそれらの後に終わる`L`一致する文字には長さがあります`L + (j-i)`そして期間`j-i`、それで指数が得られます

 [
 \frac{L+(j-i)}{j-i}。 
】

 これは正しいです。ピリオド付きの有効な繰り返しはすべて`p`で始まる 2 つの接尾辞を与えます`p`共通のプレフィックスにピリオドの最初のコピーを除くすべてが含まれる、離れた位置にあります。 

問題はコストです。 がある`Theta(n²)`ペア`(i,j)`、単一の LCP 比較には時間がかかります`Theta(n)`時間。 次のような文字列で`aaaa...a`、ほぼすべての比較では、直線的な数の文字がスキャンされます。 総作業量は、`Theta(n³)`、 その周り`8 * 10^15`文字比較の場合`n = 200000`、それは限界をはるかに超えています。 

重要な点は、実際にはすべての LCP を個別に計算する必要はないということです。 接尾辞配列はすべての接尾辞を辞書編集順に配置し、任意の 2 つの接尾辞の LCP はそれらのランク間の間隔の最小 LCP 値になります。 もし`height[k]`suffix-array エントリの LCP です`k-1`そして`k`、その後

 [
 LCP(i,j)=\min(高さ[r_i+1],\ldots,高さ[r_j])。 
】

 これにより、問題はしきい値接続の問題に変わります。 

を処理することを想像してください。`height`値を最大から最小の順に並べます。 ある値に達すると`h`、LCP が少なくとも であるすべての隣接するサフィックスのペアを接続します。`h`。 接続コンポーネントには、少なくとも長さのプレフィックスを共有するサフィックスが正確に含まれるようになりました。`h`。 

このようなコンポーネント内では、元の文字列内の開始位置ができるだけ近い 2 つのサフィックスが必要です。 両者の立場が異なる場合`d`、LCP は少なくとも`h`、したがって、長さの有効な部分文字列が生成されます。`h+d`ピリオド付き`d`。 その指数は

 [
 \frac{h+d}{d}。 
】

 固定の場合`h`、この比率を最大化することは、最小化することとまったく同じです。`d`。 

残りのデータ構造の問題は、すべての DSU コンポーネントで元の 2 つの文字列位置間の最小距離を維持することです。 位置は整数であるため、順序付きセットで十分です。 2 つのコンポーネントをマージする場合、それらの結合内の最小距離が必要です。 各コンポーネントは、元の文字列位置をキーとしたランダム化された treap として維持されます。 すべての treap ノードは、サブツリー内の最初の位置、最後の位置、および連続する位置間の最小ギャップを保存します。 treap Union は、2 つの素な順序セットを効率的に結合します。 

サフィックス配列は LCP 情報を提供し、降順スキャンは正しいしきい値コンポーネントを提供し、DSU はそれらのコンポーネントを維持し、トリープは元の位置の最も近いペアを維持します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(n³)`|`O(n)`| 遅すぎる |
 | 最適 | 期待される`O(n log n)`|`O(n)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. サフィックス配列を構築します。`w`。 接尾辞配列には、辞書編集順ですべての接尾辞の開始位置が含まれます。 カウントソートで接頭辞の倍増を使用するため、倍増ラウンドごとにコストがかかります`O(n)`そしてそこにあります`O(log n)`ラウンドします。 
2. Kasai のアルゴリズムを使用して LCP アレイを構築します。 すべてのサフィックス配列ランクについて`r > 0`、`height[r]`サフィックスのLCPを保存します`sa[r-1]`そして`sa[r]`。 葛西氏は以前の LCP を再利用することで、全体の構造を直線的にしています。 
3. すべてを解釈する`height[r]`サフィックス配列位置間のエッジとして`r-1`そして`r`。 価値のエッジ`h`これら 2 つの接尾辞が同じ長さの接頭辞を共有していることを示します`h`。 
4. 正の LCP エッジを高さの降順に並べ替えます。 最初は、すべてのサフィックスは独自の DSU コンポーネントです。 値のあるエッジを処理する場合`h`、その 2 つのコンポーネントをマージします。 結果として得られるコンポーネント内のすべてのサフィックスは、少なくとも長さのプレフィックスを共有します。`h`。 
5. 順序付けされたトリープをすべての DSU コンポーネントに関連付けます。 キーは、元の文字列内の対応する接尾辞の開始位置です。 各 treap は、コンポーネント内の 2 つのキー間の最小の差を保存します。 
6.高さのエッジを結合した後`h`、 させて`d`結果として得られる treap によって保存される最小距離になります。 開始位置が異なる 2 つの接尾辞を選択してください`d`。 それらの共通プレフィックスは少なくとも長さがある`h`, したがって、最初の部分文字列は`d`共通プレフィックスに続く文字には長さがある`d+h`そして期間`d`。 その指数は`(d+h)/d`。 
7. 相互乗算を使用して、その分数を現在の最良の答えと比較します。 比較します`a/b`そして`c/d`として`a*d`対`c*b`、浮動小数点演算を完全に回避します。 
8. ゼロの LCP 値を無視します。 指数を生成することしかできません`1`、これはすでに最初の答えです`1/1`。 
9. 印刷する前に、最後の分子と分母を最大公約数で減算します。 分母は常に正であり、すべての中間値は Python 整数に快適に適合します。 

### なぜ効果があるのか

 位置から始まる任意の 2 つの接尾辞を考慮します`i < j`、そしてさせてください`d = j-i`。 LCP が`L`、次に位置の文字`i+k`そして`j+k`誰にとっても平等である`0 <= k < L`。 2番目のサフィックスが正確に始まるので、`d`文字以降、長さの部分文字列`d+L`から始まる`i`期間あり`d`。 したがって、すべての接尾辞のペアは有効な指数を与えます`(d+L)/d`。 

次に、ピリオドを含む有効な部分文字列を検討します。`p`そして長さ`T`。 その最初の`T-p`文字は部分文字列の先頭と等しい`p`位置が後になるため、最初の 2 つのピリオド位置の接尾辞には少なくとも LCP が含まれます。`T-p`。 これら 2 つの立場を取ると、少なくとも候補指数が得られます。 

[
 \frac{p+(T-p)}p=\frac Tp.
 】

 したがって、最適な答えは、接尾辞のペアによって表されます。 

固定 LCP しきい値の場合`h`、サフィックス配列ランク間のすべての LCP エッジが少なくとも`h`。 したがって、そのコンポーネント内のペアには少なくとも LCP があります。`h`。 原点位置最小距離`d`コンポーネントでは可能な限り最大の値が得られます`(h+d)/d`少なくともLCPを持つことが知られているペア間で`h`。 

最適なペアの実際の LCP が`L`、スキャンは最終的に高さに達します`L`。 この時点では、ペアは 1 つのコンポーネントに属しているため、その距離が考慮されます。 したがって、アルゴリズムは最適値を見逃すことはできません。 生成されるすべての候補は実際の周期部分文字列に対応するため、無効な答えを生成することもできません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

sys.setrecursionlimit(1_000_000)

INF = 10**18

def suffix_array(s):
    n = len(s)

    # Append a unique sentinel smaller than every real character.
    a = [c - 96 for c in s] + [0]
    m = n + 1

    # Initial sorting by character using counting sort.
    alphabet = 27
    cnt = [0] * alphabet
    for x in a:
        cnt[x] += 1

    for i in range(1, alphabet):
        cnt[i] += cnt[i - 1]

    p = [0] * m
    for i in range(m - 1, -1, -1):
        x = a[i]
        cnt[x] -= 1
        p[cnt[x]] = i

    c = [0] * m
    classes = 1
    c[p[0]] = 0

    for i in range(1, m):
        if a[p[i]] != a[p[i - 1]]:
            classes += 1
        c[p[i]] = classes - 1

    k = 1

    while k < m and classes < m:
        # Shift every cyclic suffix by k.
        shifted = [0] * m
        for i in range(m):
            x = p[i] - k
            if x < 0:
                x += m
            shifted[i] = x

        # Counting-sort shifted positions by their class.
        cnt = [0] * classes
        for x in shifted:
            cnt[c[x]] += 1

        total = 0
        for i in range(classes):
            v = cnt[i]
            cnt[i] = total
            total += v

        new_p = [0] * m
        for x in shifted:
            cls = c[x]
            new_p[cnt[cls]] = x
            cnt[cls] += 1

        p = new_p

        new_c = [0] * m
        new_classes = 1
        new_c[p[0]] = 0

        for i in range(1, m):
            cur = p[i]
            prev = p[i - 1]

            cur_pair = (c[cur], c[(cur + k) % m])
            prev_pair = (c[prev], c[(prev + k) % m])

            if cur_pair != prev_pair:
                new_classes += 1

            new_c[cur] = new_classes - 1

        c = new_c
        classes = new_classes
        k <<= 1

    # The sentinel itself is first and is not a suffix of the original string.
    return p[1:]

def build_lcp(s, sa):
    n = len(s)
    rank = [0] * n

    for i, pos in enumerate(sa):
        rank[pos] = i

    height = [0] * n
    h = 0

    for i in range(n):
        r = rank[i]

        if r == 0:
            continue

        j = sa[r - 1]

        while i + h < n and j + h < n and s[i + h] == s[j + h]:
            h += 1

        height[r] = h

        if h:
            h -= 1

    return height

def solve(s):
    n = len(s)

    if n == 1:
        return "1/1"

    sa = suffix_array(s)
    height = build_lcp(s, sa)

    # Treap arrays. Node i represents original string position i.
    left = [0] * n
    right = [0] * n
    priority = [0] * n

    first = list(range(n))
    last = list(range(n))
    min_gap = [INF] * n

    # Deterministic 32-bit pseudo-random priorities.
    seed = 0x12345678
    for i in range(n):
        seed = (seed * 1664525 + 1013904223) & 0xffffffff
        priority[i] = seed

    def pull(t):
        l = left[t]
        r = right[t]

        if l:
            first[t] = first[l]
        else:
            first[t] = t

        if r:
            last[t] = last[r]
        else:
            last[t] = t

        g = INF

        if l:
            if min_gap[l] < g:
                g = min_gap[l]
            d = t - last[l]
            if d < g:
                g = d

        if r:
            if min_gap[r] < g:
                g = min_gap[r]
            d = first[r] - t
            if d < g:
                g = d

        min_gap[t] = g

    def split(t, key):
        # All keys in the first result are < key.
        # All keys in the second result are > key.
        # key itself is guaranteed not to occur in t.
        if not t:
            return 0, 0

        if key < t:
            a, b = split(left[t], key)
            left[t] = b
            pull(t)
            return a, t
        else:
            a, b = split(right[t], key)
            right[t] = a
            pull(t)
            return t, b

    def unite(a, b):
        if not a:
            return b
        if not b:
            return a

        if priority[a] < priority[b]:
            a, b = b, a

        bl, br = split(b, a)

        left[a] = unite(left[a], bl)
        right[a] = unite(right[a], br)

        pull(a)
        return a

    # DSU over suffix-array ranks.
    parent = list(range(n))
    treap_root = list(sa)

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x

    def merge_components(a, b):
        a = find(a)
        b = find(b)

        if a == b:
            return a

        parent[b] = a
        treap_root[a] = unite(treap_root[a], treap_root[b])
        treap_root[b] = 0

        return a

    # Each positive height is an edge between ranks idx-1 and idx.
    edges = [i for i in range(1, n) if height[i] > 0]
    edges.sort(key=height.__getitem__, reverse=True)

    best_num = 1
    best_den = 1

    for idx in edges:
        h = height[idx]

        root = merge_components(idx - 1, idx)
        d = min_gap[treap_root[root]]

        # The component contains at least two suffixes here,
        # so d is finite and positive.
        num = h + d
        den = d

        if num * best_den > best_num * den:
            best_num = num
            best_den = den

    g = __import__("math").gcd(best_num, best_den)
    return f"{best_num // g}/{best_den // g}"

def main():
    s = input().strip().encode()
    sys.stdout.write(solve(s) + "\n")

if __name__ == "__main__":
    main()
```suffix-array ルーチンは、最初にゼロで表されるセンチネルを追加します。 循環シフトのソート`w + sentinel`の接尾辞を並べ替えることと同じです。`w`なぜなら、センチネルはユニークで、実際のどのキャラクターよりも小さいからです。 プレフィックスを 2 倍にして、同じ長さのプレフィックスを置き換えます`k`同値クラスによって、長さのプレフィックスをソートします`2k`2 つのクラス値を使用します。 

LCP ルーチンは Kasai のアルゴリズムです。`rank[i]`で始まるサフィックスのサフィックス配列の位置を返します。`i`。 辞書順で前の接尾辞と比較すると、前の LCP 値が新しい比較の下限を与えるため、文字比較の合計数は線形になります。 

トレプには、元の文字列位置ごとに 1 つのノードがあります。 ノードのキーは単にそのインデックスであるため、別のキー配列は必要ありません。`first`、`last`、 そして`min_gap`サブツリーで表される順序付きセットを要約します。 最小ギャップは、左側のサブツリー内、右側のサブツリー内、現在のキーと左側の最大のキーの間、または現在のキーと右側の最小のキーの間にのみ存在できます。`split`キーによってトレプを区切ります。 が使用するキー`unite`DSU コンポーネントには互いに素なサフィックス位置が含まれているため、他のトレプには常に存在しません。`unite`ルートをより大きなランダム優先度で保持し、そのルートのキーを中心に他のツリーを分割します。 これは、標準的なランダム化された treap set-union 操作です。 

DSU は、元の文字列の位置ではなく、サフィックス配列のランクによってインデックスが付けられます。 その区別は不可欠です。 アクティブ化されるエッジは辞書編集順で隣接する接尾辞の間にあり、指数式で使用される距離は元の開始位置の間です。 

答えの比較では、除算ではなく乗算が使用されます。 たとえば、比較するには`7/3`そして`2/1`、コードチェック`7*1 > 2*3`。 Python の整数はオーバーフローしませんが、正確な整数演算を使用すると、浮動小数点比較によって生じる精度の問題も回避できます。 

コードは意図的に答えを初期化します。`1/1`。 繰り返される文字パターンのない文字列には正の LCP エッジはありませんが、臨界指数は 1 のままです。 

## 実用的な例

 ### サンプル 1:`mississippi`ゼロベースの位置を使用すると、サフィックス配列は次のようになります。`[10, 7, 4, 1, 0, 9, 8, 6, 3, 5, 2]`。 

対応する LCP 配列は次のとおりです。`[0, 1, 1, 4, 0, 0, 1, 0, 2, 1, 3]`。 

アルゴリズムは、正の高さを降順に処理します。 

| エッジインデックス | 身長`h`| 新しくマージされたサフィックスの位置 | 最小距離`d`| 候補者 | これまでで最高 |
 | --- | --- | --- | --- | --- | --- |
 | 3 | 4 |`{4, 1}`| 3 |`7/3`|`7/3`|
 | 10 | 3 |`{5, 2}`| 3 |`6/3 = 2`|`7/3`|
 | 8 | 2 |`{6, 3}`| 3 |`5/3`|`7/3`|
 | 1 | 1 |`{10, 7}`| 3 |`4/3`|`7/3`|
 | 2 | 1 |`{10, 7, 4, 1}`| 3 |`4/3`|`7/3`|
 | 6 | 1 |`{9, 8}`| 1 |`2/1`|`7/3`|
 | 9 | 1 |`{6, 3, 5}`| 1 |`2/1`|`7/3`|

 高所で`4`、位置で始まる接尾辞`4`そして`1`プレフィックスを共有する`issi`。 彼らの距離は`3`、つまり、位置で始まる部分文字列`1`長さがある`4+3=7`そして期間`3`。 それは`ississi`、指数を与える`7/3`。 

ポジションを含む後のマージ`9`そして`8`繰り返される部分文字列を見つけます`pp`、指数を与える`2`。 有力な候補ではあるが、勝てるわけではない`7/3`。 

### サンプル 2:`abab`接尾辞は次のように並べられます。`ab`、`abab`、`b`、`bab`、

 したがって、サフィックス配列は次のようになります`[2, 0, 3, 1]`。 LCP 配列は`[0, 2, 0, 1]`。 

| エッジインデックス | 身長`h`| 新しく統合されたポジション | 最小距離`d`| 候補者 | これまでで最高 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 2 |`{2, 0}`| 2 |`4/2 = 2`|`2/1`|
 | 3 | 1 |`{3, 1}`| 2 |`3/2`|`2/1`|

 最初のマージでは位置を使用します`2`そして`0`。 それらのサフィックスは共通しています`ab`、したがって、距離は`2`結果の部分文字列には長さがあります`4`。 これにより正確な平方が得られます`abab`、その指数は`2`。 

2 番目のマージは部分的な繰り返しを表します。`bab`、ピリオドが付いています`2`と指数`3/2`。 完全な正方形よりも小さいです。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | 期待される`O(n log n)`| プレフィックス倍増のサフィックス配列コスト`O(n log n)`、葛西費用`O(n)`、LCP エッジ コストの並べ替え`O(n log n)`、および treap セット共用体は期待される値を受け取ります`O(n log n)`合計 |
 | スペース |`O(n)`| サフィックス配列配列、LCP データ、DSU 配列、および文字列位置ごとに 1 つの treap ノード |

 入力には最大でも次のものが含まれます`200000`したがって、二次列挙はすでに大きすぎ、三次比較は完全に実行不可能です。 このソリューションは、サフィックス配列の構築中に対数的に多くのフル パスのみを実行し、すべての補助構造を文字列長において線形に保ちます。 ランダム化された treap により、Python の非標準の順序付きセット ライブラリが必要なくなります。 

## テストケース```
# Assume the submitted solution is saved as solution.py
from solution import solve

def run(inp: str) -> str:
    return solve(inp.strip().encode())

# Provided samples
assert run("mississippi") == "7/3", "sample 1"
assert run("abab") == "2/1", "sample 2"

# Minimum-size input
assert run("a") == "1/1", "single character"

# No repetition at all
assert run("abc") == "1/1", "all characters different"

# Fractional exponent
assert run("aba") == "3/2", "fractional repetition"

# Small repeated block, catches period and boundary handling
assert run("aab") == "2/1", "repeated pair at the beginning"

# All equal values
assert run("aaaaa") == "5/1", "all equal characters"

# Maximum-size input
assert run("a" * 200000) == "200000/1", "maximum-size all-equal string"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`a`|`1/1`| 最小入力とエッジなしの場合 |
 |`abc`|`1/1`| 繰り返しパターンのない文字列 |
 |`aba`|`3/2`| 小数部の指数と部分的な最終ピリオド |
 |`aab`|`2/1`| 境界で正確に終了する繰り返し |
 |`aaaaa`|`5/1`| 可能な最長の指数と多数の等しい LCP 値 |
 |`a * 200000`|`200000/1`| 最大入力サイズと大きな整数の答え |

 ## 特殊なケース

 1文字入力の場合`a`の場合、接尾辞配列には接尾辞が 1 つだけ含まれ、LCP 配列には 0 だけが含まれます。 DSU のマージは実行されません。 最初の答えが残っている`1/1`、これはまさに空ではない唯一の部分文字列の指数です。 

のために`abc`、すべての正の LCP 値が存在しません。 共通の最初の文字を持つ接尾辞のペアは存在しないため、部分文字列はそれ自体の長さよりも短いピリオドを持つことはできません。 またしてもイニシャル`1/1`保存されています。 少なくとも 1 つの正の LCP エッジを想定したソリューションは、ここでは失敗します。 

のために`aba`、位置で始まる接尾辞`0`そして`2`LCPあり`1`。 彼らの距離は`2`したがって、アルゴリズムは最終的に対応する LCP しきい値をアクティブにして、

 [
 \frac{1+2}{2}=\frac32。 
】

 対応する部分文字列は次のとおりです`aba`。 まさにこれが、アルゴリズムで使用する必要がある理由です。`h+d`、だけではなく`2d`または、繰り返されるブロックのみを完了します。 

のために`aab`、位置で始まる接尾辞`0`そして`1`LCPあり`1`。 彼らの距離は`1`、与える

 [
 \frac{1+1}{1}=2。 
】

 部分文字列は`aa`。 これにより、現在のサフィックスを超えて重複する部分文字列を誤って拡張する必要がある実装や、最終的な LCP 位置を誤って処理する実装が捕捉されます。 

のために`aaaaa`、サフィックスのすべてのペアには、長い共通のプレフィックスがあります。 最大の有用な LCP しきい値では、最も近い 2 つの開始位置には距離があります。`1`。 最後のマージは 5 つの位置すべてを含むコンポーネントに到達し、候補は次のようになります。 

[
 \frac{4+1}{1}=5。 
】

 したがって、答えは次のとおりです`5/1`。 このケースは、サフィックス配列コンポーネントが非常に大きくなる可能性があるため、順序付けされた構造が元の位置の最小ギャップを効率的に維持する必要がある理由も示しています。 

最大入力の場合、`200000`のコピー`a`、同じ推論で期間が与えられます`1`そして長さ`200000`、したがって、答えは次のとおりです`200000/1`。 分子は整数として直接処理され、浮動小数点計算や固定幅のオーバーフローの心配はありません。
