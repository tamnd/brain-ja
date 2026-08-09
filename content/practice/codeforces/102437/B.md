---
title: "CF 102437B - 暗号の解読"
description: "長さ n の文字列 s から始めます。 1 つの文字を繰り返し削除できますが、削除できるのは、現在最初の 2 つの位置の 1 つまたは最後の 2 つの位置の 1 つを占めている文字だけです。 正確に n-k 回削除した後、残りの文字がパスワードを形成します。"
date: "2026-08-09T12:40:21+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102437
codeforces_index: "B"
codeforces_contest_name: "\u0418\u043d\u0442\u0435\u0440\u043d\u0435\u0442-\u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u044b, \u0421\u0435\u0437\u043e\u043d 2019-2020, \u0427\u0435\u0442\u0432\u0451\u0440\u0442\u0430\u044f \u043a\u043e\u043c\u0430\u043d\u0434\u043d\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430, \u0443\u0441\u043b\u043e\u0436\u043d\u0435\u043d\u043d\u0430\u044f \u043d\u043e\u043c\u0438\u043d\u0430\u0446\u0438\u044f"
rating: 0
weight: 102437
solve_time_s: 373
verified: true
draft: false
---

[CF 102437B - コードの解読](https://codeforces.com/problemset/problem/102437/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 6 分 13 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 文字列から始めます`s`長さの`n`。 1 つの文字を繰り返し削除できますが、削除できるのは、現在最初の 2 つの位置の 1 つまたは最後の 2 つの位置の 1 つを占めている文字だけです。 まさにその後`n-k`削除すると、残りの文字がパスワードを形成します。 可能な長さのすべてのパスワードの中で`k`、辞書編集的に最小のものが必要です。 

制約`n <= 500000`文字列の長さの二次関数を除外します。 平`O(nk)`次の場合に二次関数になる可能性があります`k`に近いです`n`したがって、主な作業が線形に近いか、最悪でもそのようなソリューションが必要です。`O(n log n)`。 26 個の小文字の小さなアルファベットは境界の選択に役立ちますが、辞書編集上の比較は長い共通接頭語に依存する可能性があるため、それだけでは問題は解決されません。 

最初のエッジケースは`k = n`。 削除は不可能なので、答えは単純に元の文字列になります。 たとえば、`s = "abc"`そして`k = 3`、答えは`abc`。 少なくとも 1 つの削除を常に前提とするソリューションでは、無効な 2 番目または最後から 2 番目の位置に誤ってアクセスする可能性があります。 

2 番目のエッジケースは、`k = 1`。 その文字が最初の文字になるまで左から繰り返し削除できるため、個々の文字を最後の文字として残すことができます。 したがって、答えは文字列全体の最小文字になります。 のために`s = "zba"`そして`k = 1`、答えは`a`。 

3 番目のエッジケースは、`k = 2`。 この場合、すべてのポジションのペアを 2 人の生き残ったキャラクターにすることができます。 のために`s = "bac"`そして`k = 2`、考えられる答えは次のとおりです。`ac`そして`ba`、したがって、答えは次のとおりです`ac`。 単純に 2 つの小さい文字を並べ替えると、次のようになります。`ab`、 しかし`ab`の続きではありません`bac`そして生産できません。 

削除が 1 つしかない場合は、より微妙な境界ケースが表示されます。 のために`s = "abcde"`そして`k = 4`、削除できません`c`、なぜなら最初だけだから`a`、`b`、`d`、 そして`e`アクセス可能です。 可能な文字列は次のとおりです。`bcde`、`acde`、`abce`、 そして`abcd`、したがって、答えは次のとおりです`abcd`。 操作を任意のサブシーケンスの削除として扱うソリューションでは、誤って許可されてしまいます。`abde`。 

## アプローチ

 直接的なブルート フォース ソリューションでは、4 つの削除操作すべてを再帰的に試行できます。 これは正しいです。すべての正当な操作が明示的に考慮されるため、到達可能なすべての文字列が再帰ツリーのどこかに表示されます。 ただし、その後`n-k`ツリーにある最大の削除数`4^(n-k)`操作シーケンス。 と`n = 500000`、この探索空間のごく一部であっても探索することは不可能です。 すべての個別の中間文字列を保存することもコストがかかりすぎます。 

有益な観察は、消滅する文字ではなく、生き残る位置に注目することによって得られます。 プロセス中、残りの位置は常に非常に制限された形状になります。 それらは 1 つの連続したインターバルで構成され、おそらくそのインターバルの前に 1 つの追加の生存ポジションがあり、場合によってはそのインターバルの後に 1 つの追加の生存ポジションがあります。 

その理由を確認するには、1 つの間隔である文字列全体から始めます。 最初の文字を削除すると、左から間隔が短くなります。 2 番目の文字を削除すると、最初の文字はシングルトンとして残り、残りは連続したままになります。 同じ議論が右側にも対称的に当てはまります。 このプロセスを繰り返しても、複雑な間隔の集合を作成することはできません。 中央の連続区間の各側から最大 1 つのシングルトンを切り離すことができます。 

逆もまた真です。 生き残る位置が連続した間隔であり、オプションでその前に 1 文字、その後に 1 文字の生き残る文字があると仮定します。 中央の間隔より前のすべてを左側から削除でき、2 番目の位置を繰り返し削除することでオプションの左側の文字を保持します。 中央の間隔以降はすべて、右から対称的に処理できます。 したがって、この構造を持つすべての文字列に到達可能です。 

したがって、到達可能なパスワードはすべて 4 つの形式のいずれかになります。 連続した長さの部分文字列にすることができます`k`。 1 文字の後に、一定の長さの連続した部分文字列を続けることができます。`k-1`。 連続した長さの部分文字列にすることができます`k-1`続いて1文字。 または、1 つの文字と、その長さの連続する部分文字列を指定することもできます。`k-2`、次に 1 文字。 

残りの課題は、固定長の辞書編集上最小の部分文字列を効率的に見つけることです。 のサフィックス配列を構築します。`s`。 同じ長さの 2 つの部分文字列の場合、部分文字列が等しくない限り、辞書編集上の順序は対応する接尾辞の順序と同じになります。 サフィックスを最初の順序に従ってグループ化します。`m`サフィックス配列とその LCP 配列を使用した文字。 これにより、あらゆる長さが得られます。`m`サブストリング コンパクトな辞書編集ランク。 

これらのランクが利用可能になると、4 つの構造ケースのそれぞれが線形スキャンになります。 フォームについて`c + middle`、最初の文字が比較を支配し、次に次の順位が続きます。`middle`。 のために`middle + c`、中央の部分文字列が最初に優先され、その後に最後の文字が続きます。 のために`c + middle + d`の順に比較されます。`c`、`middle`、`d`。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(4^(n-k))`状態 | 指数 | 遅すぎる |
 | 最適 |`O(n log n)`|`O(n)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1.ハンドル`k = 1`そして`k = 2`直接。 1 文字の場合、最小の文字を選択します。 2 文字の場合、可能なすべての最初の位置をスキャンし、その後に表示される最小の文字と組み合わせます。 これは、すべての 2 位置のサブシーケンスが到達可能であるため機能します。 
2. のために`k >= 3`、のサフィックス配列を構築します`s`センチネルはすべての小文字よりも小さくなります。 接尾辞の配列は、すべての接尾辞の辞書編集的な順序を示します。 
3. Kasai のアルゴリズムを使用して LCP 配列を構築します。 連続するサフィックス間の LCP 値から、それらの共通プレフィックスの長さがわかります。 
4.必要なミドルレングスの場合`m`、サフィックス配列をスキャンし、LCP が少なくとも である連続するサフィックスに同じランクを割り当てます。`m`。 2 つのポジションは、その長さが正確に同じランクになります。`m`部分文字列は等しい。 ランクは辞書順に並べられています。 
5. ちょうど 1 つの連続した部分文字列の長さであるパスワードを検討してください。`k`。 すべての有効な開始位置の中で、長さが最も短い位置を選択します。`k`部分文字列のランク。 
6. 次の形式のパスワードを検討してください`c + middle`、 どこ`middle`長さがある`k-1`。 あらゆる可能な開始位置に対して、`middle`、可能な限り最高の`c`は、その位置の前にある最小の文字です。 まず候補を比較します`c`、次にランク順に`middle`。 
7. 次の形式のパスワードを検討してください`middle + c`。 長さの考えられるすべての中間部分文字列に対して`k-1`、その後の最小の文字を選択します。 最初に中間の部分文字列ランクで候補を比較し、次に最後の文字で比較します。 
8. 次の形式のパスワードを検討してください`c + middle + d`、 どこ`middle`長さがある`k-2`。 考えられるすべての中間スタートについて、その前にある最小の文字とその後ろにある最小の文字を選択します。 候補者を比較する`c`、次に中位、次に`d`。 
9. 4 つの形式のそれぞれから最適な候補を再構築し、最後にそれらを比較します。 完全な候補は 4 つだけなので、それらを直接比較すると、最大でもコストがかかります`O(k)`合計追加作業。 

なぜ効果があるのか

 中心的な不変条件は、到達可能な生存位置のセットがすべて、両側に最大 1 つの余分な位置を持つ連続した間隔であるということです。 削除操作ではこのプロパティが保持され、このプロパティを持つすべてのセットは、対応する側から不要な文字を削除することによって構築できます。 

アルゴリズムの 4 つのケースは、これら 4 つの可能な形状を正確に列挙します。 各ケースの内部では、選択された境界文字は、連続する中間の前後に出現するため、独立して最小化されます。 あるケースではすべての中間部分文字列が同じ長さであるため、接尾辞由来のランクでは中間部分文字列が正しく比較されます。 したがって、それぞれのケースで辞書編集上最小の到達可能なパスワードが生成され、これら 4 つの候補の最小値を取ることで全体的な最適値が得られます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def build_suffix_array(s):
    n = len(s)

    # 1..26 are letters, 0 is the unique sentinel.
    a = [x - 96 for x in s] + [0]
    N = n + 1

    cnt = [0] * 27
    for x in a:
        cnt[x] += 1

    pos = [0] * 27
    for i in range(1, 27):
        pos[i] = pos[i - 1] + cnt[i - 1]

    p = [0] * N
    for i, x in enumerate(a):
        p[pos[x]] = i
        pos[x] += 1

    c = [0] * N
    classes = 1
    for i in range(1, N):
        if a[p[i]] != a[p[i - 1]]:
            classes += 1
        c[p[i]] = classes - 1

    shift = 1
    while shift < N:
        pn = [
            x - shift if x >= shift else x - shift + N
            for x in p
        ]

        cnt = [0] * classes
        for x in pn:
            cnt[c[x]] += 1

        pos = [0] * classes
        total = 0
        for i in range(classes):
            pos[i] = total
            total += cnt[i]

        p_new = [0] * N
        for x in pn:
            cls = c[x]
            p_new[pos[cls]] = x
            pos[cls] += 1

        c_new = [0] * N
        new_classes = 1
        for i in range(1, N):
            cur = p_new[i]
            prev = p_new[i - 1]

            cur_pair = (c[cur], c[(cur + shift) % N])
            prev_pair = (c[prev], c[(prev + shift) % N])

            if cur_pair != prev_pair:
                new_classes += 1

            c_new[cur] = new_classes - 1

        p = p_new
        c = c_new
        classes = new_classes
        shift <<= 1

    # Remove the sentinel suffix.
    return p[1:], a

def build_lcp(suffix_array, a):
    N = len(a)
    rank = [0] * N

    for i, pos in enumerate(suffix_array):
        rank[pos] = i

    lcp = [0] * N
    common = 0

    for i in range(N):
        r = rank[i]

        if r == 0:
            continue

        j = suffix_array[r - 1]

        while i + common < N and j + common < N:
            if a[i + common] != a[j + common]:
                break
            common += 1

        lcp[r] = common

        if common:
            common -= 1

    return lcp

def fixed_length_ranks(suffix_array, lcp, n, length):
    """
    rank[i] is the lexicographic rank of s[i:i+length].
    Equal substrings receive the same rank.
    """
    rank = [0] * n

    group = -1

    for idx, pos in enumerate(suffix_array):
        if idx == 0:
            group = 0
        elif lcp[idx] < length:
            group += 1

        rank[pos] = group

    return rank

def best_by_rank(rank, lo, hi):
    best = lo

    for i in range(lo + 1, hi + 1):
        if rank[i] < rank[best]:
            best = i

    return best

def solve_instance(s, k):
    n = len(s)

    if k == 1:
        return min(s)

    if k == 2:
        best = None
        right_min = s[-1]

        for i in range(n - 2, -1, -1):
            candidate = s[i] + right_min

            if best is None or candidate < best:
                best = candidate

            if s[i] < right_min:
                right_min = s[i]

        return best

    suffix_array, a = build_suffix_array(s)
    lcp = build_lcp(suffix_array, a)

    values = s.encode()

    # Prefix minima and suffix minima of characters.
    pref = bytearray(n)
    suf = bytearray(n + 1)

    pref[0] = values[0]
    for i in range(1, n):
        pref[i] = min(pref[i - 1], values[i])

    suf[n] = 123
    for i in range(n - 1, -1, -1):
        suf[i] = min(suf[i + 1], values[i])

    candidates = []

    # Case 1: one contiguous substring of length k.
    ranks = fixed_length_ranks(suffix_array, lcp, n, k)
    start = best_by_rank(ranks, 0, n - k)
    candidates.append(values[start:start + k])

    # Case 2: one character + substring of length k - 1.
    middle_len = k - 1
    ranks = fixed_length_ranks(
        suffix_array, lcp, n, middle_len
    )

    best_key = None
    best_start = -1
    best_left = -1

    for start in range(1, n - middle_len + 1):
        left_char = pref[start - 1]
        key = (left_char, ranks[start])

        if best_key is None or key < best_key:
            best_key = key
            best_start = start
            best_left = left_char

    candidates.append(
        bytes([best_left]) +
        values[best_start:best_start + middle_len]
    )

    # Case 3: substring of length k - 1 + one character.
    best_key = None
    best_start = -1
    best_right = -1

    for start in range(0, n - middle_len):
        end = start + middle_len
        right_char = suf[end]
        key = (ranks[start], right_char)

        if best_key is None or key < best_key:
            best_key = key
            best_start = start
            best_right = right_char

    candidates.append(
        values[best_start:best_start + middle_len] +
        bytes([best_right])
    )

    # Case 4: one character + substring of length k - 2
    # + one character.
    middle_len = k - 2
    ranks = fixed_length_ranks(
        suffix_array, lcp, n, middle_len
    )

    best_key = None
    best_start = -1
    best_left = -1
    best_right = -1

    for start in range(1, n - middle_len):
        end = start + middle_len

        left_char = pref[start - 1]
        right_char = suf[end]

        key = (left_char, ranks[start], right_char)

        if best_key is None or key < best_key:
            best_key = key
            best_start = start
            best_left = left_char
            best_right = right_char

    candidates.append(
        bytes([best_left]) +
        values[best_start:best_start + middle_len] +
        bytes([best_right])
    )

    return min(candidates).decode()

def solve():
    s = input().strip()
    k = int(input())
    print(solve_instance(s, k))

if __name__ == "__main__":
    solve()
```実装では、最初に次の 2 つの最小値が処理されます。`k`別に。 これにより、空の中間間隔を表す必要がなくなります。 のために`k = 2`、生き残っている位置のすべてのペアに到達できるため、右から左への接尾辞は最小限で十分です。 

大型の場合`k`、`build_suffix_array`実際のすべてのキャラクターよりも小さなセンチネルを追加します。 標準のダブリング構造では、最初のクラスを表す同値クラスのペアによってサフィックスを繰り返しソートします。`2^h`文字。 カウンティングソートは各倍加段階を線形に保ち、次のようになります。`O(n log n)`建設時間。`build_lcp`笠井アルゴリズムを使用します。 LCP 配列が必要なのは、最初の場合でもサフィックス ランクだけでサフィックスを区別するためです。`m`文字は等しいです。`fixed_length_ranks`共通のプレフィックスが少なくとも長さがある場合は常に、連続するサフィックスをマージします。`m`、長さの正確な辞書編集クラスを生成します。`m`部分文字列。 

プレフィックスとサフィックスの最小値は次の場所に保存されます。`bytearray`オブジェクト。 これにより、メモリ消費量が小さく保たれながら、中間間隔ごとに可能な限り最小の外側文字への定時間アクセスが可能になります。 

4 つのケースの範囲は意図的に異なります。 左の追加文字の場合、中央は少なくとも次の位置で開始する必要があります`1`、で終了する可能性がありますが、`n-1`。 右の追加文字の場合、中央は次から始まる場合があります。`0`ただし、末尾の後に 1 文字を残す必要があります。 エクストラが 2 つある場合は、両方の制限が適用されます。 これらは、off-by-one エラーが最も発生しやすい場所です。 

Python では整数がオーバーフローすることはできず、サフィックス配列には整数のインデックスとランクのみが格納されます。 最終候補はバイト文字列であるため、辞書編集的な比較も効率的になります。 

## 実用的な例

 ### サンプル 1

 のために`s = "abacaba"`そして`k = 3`、4 つの構造形式は中間の長さです。`3`、`2`、`2`、 そして`1`。 

| フォーム | 最高の施工 | 候補者 |
 | --- | --- | --- |
 | 中間のみ |`aba`|`aba`|
 | 左 + 中央 |`a`+`ab`|`aab`|
 | 中央 + 右 |`ab`+`a`|`aba`|
 | 左 + 中 + 右 |`a`+`a`+`a`|`aaa`|

 最終形態が勝利します。 その生き残った立場は、`1, 3, 5`。 から始まる`abacaba`、2番目の文字を削除します`b`、最後から 2 番目の文字`b`、さらに 2 つアクセス可能`c`そして`b`必要に応じて文字を残します`aaa`。 

トレースの重要な部分は、答えが連続した部分文字列ではないということです。 部分文字列のみを考慮した解決策は次で終了します。`aab`、一方、許可される左右のシングルトン位置は次のようになります。`aaa`可能。 

### サンプル 2

 のために`s = "qwerty"`そして`k = 2`、すべての位置のペアに到達可能です。 

| 最初の位置 | 可能な限り最高の 2 番目の文字 | 候補者 |
 | --- | --- | --- |
 |`q`|`e`|`qe`|
 |`w`|`e`|`we`|
 |`e`|`r`|`er`|
 |`r`|`t`|`rt`|
 |`t`|`y`|`ty`|

 最小の候補は`er`。 

この事例も、その理由を示しています。`k = 2`ショートカットが便利です。 答えは単純に辞書編集上最小の長さ 2 の部分列であり、これは接尾辞配列を構築せずに最小の接尾辞で見つけることができます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(n log n)`| サフィックス配列の構築が優先されます。 LCP、固定長ランク、および候補スキャンは線形です。 
| スペース |`O(n)`| サフィックス配列、LCP 配列、ランク配列、および補助配列はすべて線形です。 

と`n <= 500000`、`O(n log n)`ブルートフォース状態空間は指数関数的に増大しますが、それは実現可能です。 サフィックス配列は一度構築され、構造ケースに必要な 3 つの固定部分文字列長は、その後線形スキャンで処理されます。 

## テストケース```
# Save the submitted solution as solution.py before running this block.
from solution import solve_instance

def run(inp: str) -> str:
    lines = inp.strip().splitlines()
    s = lines[0].strip()
    k = int(lines[1])
    return solve_instance(s, k)

# Provided samples
assert run("abacaba\n3\n") == "aaa", "sample 1"
assert run("qwerty\n2\n") == "er", "sample 2"

# Minimum-size input
assert run("z\n1\n") == "z", "minimum size"

# Two-character password, catches incorrect sorting of characters
assert run("bac\n2\n") == "ac", "two-character subsequence"

# Only one deletion is possible, so the interior character cannot be removed
assert run("abcde\n4\n") == "abcd", "one deletion boundary"

# All characters equal
assert run("aaaaa\n3\n") == "aaa", "all equal"

# No deletion is required
assert run("abc\n3\n") == "abc", "k equals n"

# Maximum-size case
s = "z" * 500000
assert run(s + "\n250000\n") == "z" * 250000, "maximum size"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`z / 1`|`z`| 可能な最小限の入力と`k = 1`|
 |`bac / 2`|`ac`| 2 位のサブシーケンスと順序付け |
 |`abcde / 4`|`abcd`| 内部の文字はすぐには削除できません。 
|`aaaaa / 3`|`aaa`| 等しい文字と繰り返される部分文字列のランク |
 |`abc / 3`|`abc`| 削除なし |
 |`z...z / 250000`|`z...z`| 最大入力サイズと反復性の高いサフィックス |

 ## 特殊なケース

 のために`s = "zba"`そして`k = 1`、アルゴリズムはすぐに戻ります`a`。 該当する側のキャラクターを削除することですべてのキャラクターを唯一の生存者にすることができるため、最小限のキャラクターで十分です。 

のために`s = "bac"`そして`k = 2`、アルゴリズムは、最小文字をその右側に維持しながら、可能な最初の位置を右から左にスキャンします。 考慮します`ac`ポジションから`2,3`そして`ba`ポジションから`1,3`、選択する`ac`。 誘惑の文字列`ab`生き残ったキャラクターは元の順序を維持する必要があるため、形成することはできません。 

のために`s = "abcde"`そして`k = 4`の場合、5 文字が残ったまま削除のみが行われます。 アクセス可能な文字は、`a`、`b`、`d`、 そして`e`、 それで`c`削除することはできません。 結果として得られる 4 つの文字列の最小値は、`abcd`。 構造的特徴付けでもこれを直接確認します。`c`2 つの非シングルトン インターバルが残りますが、これは 4 つの到達可能な形式の 1 つではありません。 

のために`s = "abacaba"`そして`k = 3`、最適なパスワード`aaa`生き残ったポジションがある`1`、`3`、 そして`5`。 これらの位置は、左シングルトン、中央の 1 文字の間隔、および右シングルトンを形成します。 これはまさに、削除操作で許可される最も一般的な形状です。 

のために`s = "abc"`そして`k = 3`、有効なすべての構造ケースは元の文字列を再構築し、削除は実行されません。 答えは残っている`abc`、境界の計算に実際の削除可能な文字が必要ないことが確認されました。 

完全に以下で構成される最大サイズの文字列の場合`500000`のコピー`z`、あらゆる可能な長さ -`k`中間の部分文字列は同一です。 固定長のランキング手順では、それらを同じ同値クラスにグループ化するため、有効な開始位置はどれでも受け入れられ、結果の答えは期待どおりになります。`k`のコピー`z`。 

ご希望であれば、同じ証明を維持しながら実装に関する議論を大幅に削減した、この社説の **短い Codeforces スタイル バージョン** も提供できます。
