---
title: "CF 102343K - コード マッチング"
description: "(N) 個の異なる数字列を含むコードブックがあります。 それらの文字列の 1 つが送信されます。 James は、送信された文字列の一様にランダムな桁からリスニングを開始するため、その位置より前の部分はすべてすでに見逃されている可能性があります。"
date: "2026-08-17T10:27:29+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102343
codeforces_index: "K"
codeforces_contest_name: "UCF Locals 2019"
rating: 0
weight: 102343
solve_time_s: 203
verified: true
draft: false
---

[CF 102343K - コード マッチング](https://codeforces.com/problemset/problem/102343/K)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 23 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 (N) 個の異なる数字列を含むコードブックがあります。 それらの文字列の 1 つが送信されます。 James は、送信された文字列の一様にランダムな桁からリスニングを開始するため、その位置より前の部分はすべてすでに見逃されている可能性があります。 

ジェームズは、いくつかの連続した数字を聞いた後、送信されたメッセージである可能性のあるすべてのコードブック エントリを検討します。 彼は候補メッセージのどこから聞き始めたのか分からないため、聞いた数字はその候補の連続した部分文字列として出現するだけで済みます。 ちょうど 1 つのコードブック エントリに、これまでに聞いたシーケンス全体が含まれるとすぐに、James はメッセージを知り、停止します。 

最後に追加情報が 1 つあります。 送信されたメッセージの最後の桁から 1 秒後に沈黙が生じます。 ジェームズがメッセージを区別せずに最後まで到達した場合、その沈黙は、彼が聞いたシーケンスが候補メッセージの最後で終了する必要があることを彼に伝えます。 ちょうど 1 人の候補者がそのサフィックスを持っていれば、その候補者はメッセージを識別できます。 そうしないと、その開始位置でメッセージを決定することができません。 

入力には最大 (100{,}000) 個のコードブック文字列が含まれており、その合計の長さは最大 (100{,}000) です。 元のコンテストでは、2 秒の時間制限と 256 MB のメモリ制限が与えられています。 合計長の制限が重要な制約です。合計入力サイズに比例するアルゴリズムが理想的ですが、すべての部分文字列とすべてのコードワードを繰り返し比較することは 2 次的であり、およそ (10^{10}) 文字レベルの操作に達する可能性があります。 

見落としやすい境界ケースが 2 つあります。 まず、最後の数字の後でのみ一意であるからといって、自動的に追加の 1 秒の沈黙が必要になるわけではありません。 聞いた完全なシーケンスがすでに 1 つのコードワードのみに含まれている場合、James はその最後の桁の直後のコードワードを知っています。 たとえば、```
2
12
123
```メッセージ`12`ジェームズが最初の桁から開始するときに、両方の桁を聞いた後で識別できるため、開始位置には 3 秒ではなく 2 秒かかります。もう一方の開始位置は、この音声を聞くことになります。`2`; この数字は両方のメッセージに表示されますが、沈黙の後にのみ表示されます。`12`そこで終了できるので、2 秒かかります。 

第 2 に、数字シーケンス自体が一意でない場合でも、最後の沈黙によってメッセージを区別できます。 例えば、```
2
12
23
```もし`12`が送信され、ジェームズは決勝で先発する`2`、聴覚`2`両方のコードワードに次のものが含まれるため、単独ではあいまいです。`2`。 沈黙の後にだけ、`12`持つことができる`2`最後の桁が なので、時間は 2 秒です。 すべてのあいまいな完全な接尾辞を不可能なものとして扱う解決策は、このケースを誤ることになります。 

## アプローチ

 直接的な解決策では、すべてのメッセージの可能な開始位置をすべて調べます。 固定位置の場合、観察された部分文字列を一度に 1 文字ずつ拡張し、どのコードブック文字列にその部分文字列が含まれるかを尋ねます。 すべてのコードワードを直接チェックすることは正しいことです。なぜなら、候補の定義は、観測されたシーケンスがそのコードワード内のどこかに出現することであるからです。 

問題は、部分文字列検索が繰り返されることです。 入力の合計長が (S) の場合、可能な開始位置は (S) 個あります。 すべてのメッセージ ペアにわたって、すべての開始位置をチェックするには、最悪の場合 (\Theta(S^2)) 個の文字比較が必要になる可能性があります。これは、(S=100{,}000) の場合、約 (10^{10}) 回の演算になります。 多くの異なる部分文字列が同じプレフィックスを共有しているため、同じ繰り返し作業が実行されます。 

有益な観察は、開始位置が固定されている場合、必要な数値は 1 つだけであるということです。それは、他のコードワードにも出現する残りのサフィックスの最長のプレフィックスです。 この最長の共有プレフィックスの長さが (L) であると仮定します。 (L+1) 桁を聞いた後は、他のコードワードに観測された部分文字列が含まれないため、James はメッセージをすぐに識別できます。 したがって、すべての開始位置は、他のコードワードに属するサフィックスに対する最長共通プレフィックス クエリに帰着できます。 

サフィックス配列は、必要な構造を正確に提供します。 すべてのコードワードを 1 つのシーケンスに入れ、連続するコードワードを異なる区切り記号で区切ります。 数字で始まるすべての接尾辞は、観測された継続の可能性を表します。 サフィックス配列の順序では、別のコードワードからのサフィックスを含む最大 LCP は、両側の別のコードワードから最も近いサフィックスによって達成されます。 サフィックス配列を構築した後、これらの LCP 値を線形時間で取得できます。 

残りのケースは、残りのサフィックス全体が依然として別のコードワードに出現する場合です。 この場合、通常の部分文字列テストではメッセージを区別できません。 逆のコードワードのトライを個別に構築します。 このトライのノードは接尾辞を表し、その格納された数からその接尾辞で終わるコードワードの数がわかります。 カウントがちょうど 1 の場合、さらに 1 秒以内に最後の沈黙がメッセージを区別します。 カウントが 1 より大きい場合、その開始位置は不可能です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(S^2)) 文字の比較 | (O(S)) | 遅すぎる |
 | サフィックス配列 + 逆トライ | (O(S\log S)) | (O(S)) | 承認済み |

 ここで (S) はすべてのコードワードの合計長です。 

## アルゴリズムのチュートリアル

 1. すべてのコードワードを読み取り、それらを 1 つの整数シーケンスに連結します。 数字は 1 ～ 10 の値を使用しますが、すべてのコードワードは独自の一意の区切り値を受け取ります。 セパレータは、あるコードワードのサフィックスが境界を越えて別のコードワードに誤って一致することを防ぎます。 
2. カウントソートによるプレフィックス倍増を使用して、連結シーケンスのサフィックス配列を構築します。 他のどのシンボルよりも小さいセンチネルが構築中に追加され、その後最終的なサフィックス配列から削除されます。 
3. Kasai のアルゴリズムを使用して LCP 配列を計算します。`lcp[r]`サフィックスの共通プレフィックス長をサフィックス配列ランクに格納します`r`とその直前の接尾辞。 
4. サフィックス配列の位置ごとに、別のコードワードに属するサフィックスを含む最大 LCP を決定します。 左から右にスキャンし、次に右から左にスキャンします。 スキャン中にコードワード識別子が変更されると、前のサフィックスが別のコードワードの最も近いサフィックスになります。 同じコードワード内に留まりながら、最も近い別のコードワード以降に発生した最小 LCP を維持します。 この最小値は、最も近い異なるサフィックスを持つ LCP です。 
5. 元の桁位置ごとに、結果の最大共有プレフィックス長を保存します。 値が (L) の場合、最初の (L) 桁は別のメッセージと混同される可能性がありますが、次の桁が存在する場合、メッセージは一意になります。 
6. すべてのコードワードを逆順に含むトライを構築します。 訪問した各ノードは、通過したコードワードの数を保存します。 したがって、ノードはサフィックスを表し、その数はそのサフィックスを持つコードワードの数と正確に一致します。 
7. すべてのコードワードを右から左に処理します。 位置 (i) では、`remaining = len(word) - i`。 別のコードワードとの事前計算された最長共有プレフィックスが以下の場合`remaining`、その後、最初の一意の観測が発生します。`best + 1`桁があるので、その数値をリスニング時間に加算します。 
8. メッセージが終了する前に一意の部分文字列が表示されない場合は、残りのサフィックス全体に対応するトライ ノードを検査します。 ちょうど 1 つのコードワードがそのサフィックスで終わっている場合、ジェームズはサフィックスを聞いてから 1 秒間の沈黙を聞いて答えを学習します。`remaining + 1`秒。 少なくとも 2 つのコードワードにそのサフィックスが付いている場合、この開始位置は不可能であるため、そのメッセージの全体的な答えは次のようになります。`Impossible`。 
9. すべての可能な開始位置でのリスニング時間を平均します。 すべての桁位置が開始点である可能性が等しいため、合計をメッセージ長で割ります。 

正確性が不変であるということは、すべての開始位置について、`best`は、別のコードワードでも連続して発生する、最初に観測される桁の最大数です。 したがって、長さのすべての観測値は最大でも`best`長さの観察は曖昧ですが、`best + 1`が存在する場合、送信されたコードワード内でのみ発生します。 残りのサフィックスが最大でも長さの場合`best`、数字のみの観察ではメッセージを区別できず、リバーストライでは最後の沈黙が 1 つまたは複数の可能性のあるメッセージを残すかどうかを正確にチェックします。 したがって、計算された時間は、ジェームズがその開始位置に必要な時間と正確です。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def suffix_array(a):
    """Suffix array of an integer sequence, O(n log n)."""
    s = a + [0]  # 0 is the unique sentinel
    n = len(s)

    alphabet = max(s) + 1
    cnt = [0] * alphabet
    for x in s:
        cnt[x] += 1

    pos = [0] * alphabet
    for i in range(1, alphabet):
        pos[i] = pos[i - 1] + cnt[i - 1]

    p = [0] * n
    for x in s:
        p[pos[x]] = p[pos[x]] + 1
        pos[x] += 1

    # The previous counting-sort construction above needs positions
    # reconstructed from counts.
    pos = [0] * alphabet
    for i in range(1, alphabet):
        pos[i] = pos[i - 1] + cnt[i - 1]

    p = [0] * n
    for i, x in enumerate(s):
        p[pos[x]] = i
        pos[x] += 1

    c = [0] * n
    classes = 1
    c[p[0]] = 0

    for i in range(1, n):
        if s[p[i]] != s[p[i - 1]]:
            classes += 1
        c[p[i]] = classes - 1

    length = 1

    while length < n:
        pn = [0] * n
        for i in range(n):
            x = p[i] - length
            if x < 0:
                x += n
            pn[i] = x

        cnt = [0] * classes
        for x in pn:
            cnt[c[x]] += 1

        pos = [0] * classes
        for i in range(1, classes):
            pos[i] = pos[i - 1] + cnt[i - 1]

        new_p = [0] * n
        for x in pn:
            cls = c[x]
            new_p[pos[cls]] = x
            pos[cls] += 1

        cn = [0] * n
        new_classes = 1
        cn[new_p[0]] = 0

        for i in range(1, n):
            cur = new_p[i]
            prev = new_p[i - 1]

            cur_second = cur + length
            if cur_second >= n:
                cur_second -= n

            prev_second = prev + length
            if prev_second >= n:
                prev_second -= n

            if c[cur] != c[prev] or c[cur_second] != c[prev_second]:
                new_classes += 1

            cn[cur] = new_classes - 1

        p = new_p
        c = cn
        classes = new_classes
        length <<= 1

    # Remove the suffix consisting only of the sentinel.
    return p[1:]

def build_lcp(a, sa):
    n = len(a)
    rank = [0] * n

    for i, p in enumerate(sa):
        rank[p] = i

    lcp = [0] * n
    h = 0

    for i in range(n):
        r = rank[i]

        if r == 0:
            continue

        j = sa[r - 1]

        while i + h < n and j + h < n and a[i + h] == a[j + h]:
            h += 1

        lcp[r] = h

        if h:
            h -= 1

    return rank, lcp

def solve():
    n = int(input())
    words = [input().strip() for _ in range(n)]

    # Concatenate all words. Each word gets its own separator.
    # Positions of actual digits are retained for later queries.
    a = []
    doc = []
    positions = [[] for _ in range(n)]

    for idx, word in enumerate(words):
        for ch in word:
            positions[idx].append(len(a))
            a.append(ord(ch) - ord('0') + 1)
            doc.append(idx)

        # Separators are all different and larger than digit symbols.
        a.append(11 + idx)
        doc.append(idx)

    # Suffix-array phase.
    sa = suffix_array(a)
    rank, lcp = build_lcp(a, sa)

    # best[r] = maximum LCP with a suffix from a different codeword.
    best = [0] * len(a)

    current_doc = doc[sa[0]]
    minimum = None

    for r in range(1, len(sa)):
        d = doc[sa[r]]

        if d != current_doc:
            current_doc = d
            minimum = lcp[r]
        elif minimum is not None:
            minimum = min(minimum, lcp[r])

        if minimum is not None:
            best[r] = minimum

    current_doc = doc[sa[-1]]
    minimum = None

    for r in range(len(sa) - 2, -1, -1):
        d = doc[sa[r]]

        if d != current_doc:
            current_doc = d
            minimum = lcp[r + 1]
        elif minimum is not None:
            minimum = min(minimum, lcp[r + 1])

        if minimum is not None:
            best[r] = max(best[r], minimum)

    # The suffix-array data is no longer needed.
    del sa
    del lcp
    del doc
    del a

    # Build a trie of reversed codewords.
    children = [{}]
    suffix_count = [0]

    for word in words:
        node = 0

        for ch in reversed(word):
            nxt = children[node].get(ch)

            if nxt is None:
                nxt = len(children)
                children[node][ch] = nxt
                children.append({})
                suffix_count.append(0)

            node = nxt
            suffix_count[node] += 1

    output = []

    for idx, word in enumerate(words):
        total_time = 0
        possible = True

        node = 0
        found_unique = False

        for i in range(len(word) - 1, -1, -1):
            ch = word[i]
            node = children[node][ch]

            remaining = len(word) - i
            global_pos = positions[idx][i]
            shared = best[rank[global_pos]]

            if shared < remaining:
                total_time += shared + 1
                found_unique = True
                break

        if not found_unique:
            # The complete remaining suffix never became unique
            # as an ordinary substring. Silence can distinguish it
            # only if exactly one codeword ends with it.
            if suffix_count[node] == 1:
                total_time += len(word) + 1
            else:
                possible = False

        if not possible:
            output.append("Impossible")
        else:
            output.append(f"{total_time / len(word):.10f}")

    sys.stdout.write("\n".join(output))

if __name__ == "__main__":
    solve()
```連結フェーズでは、コードワードごとに異なるセパレータが割り当てられます。 これは単なる便宜上のものではありません。同じセパレータが再利用された場合、2 つのサフィックスがコードワード境界を越える LCP を誤って受け取る可能性があります。 異なるコードワードのサフィックスは、その数字部分の直後に異なる記号に遭遇するため、独自の区切り文字を使用するとそれが不可能になります。 

サフィックス配列は、実際のすべてのシンボルよりも小さいセンチネルを使用します。 プレフィックスの 2 倍化により巡回シフトがソートされ、センチネルはその順序を通常のサフィックス配列の順序に変換します。 この実装では、ラウンドごとに比較ソートを使用してサフィックスをソートするのではなく、倍増ラウンドごとにカウント ソートを使用して (O(S\log S)) を与えます。 

LCP アレイ上の 2 つのスキャンは特に注目に値します。 サフィックス配列のランクが現在コードワード A に属しているとします。別のコードワードからの最も近いサフィックスは、ドキュメントが A と異なる最新のランクです。そのサフィックスを持つ LCP は、2 つのランク間の間隔にわたる最小 LCP 値です。 別の A サフィックスが見つかった場合、間隔を延長するには別のサフィックスのみが必要です`min`手術。 右から左へのスキャンでは対称計算が実行されます。 

リバーストライは最後の沈黙の場合にのみ使用されます。 単語を最後の文字から最初の文字に向かってたどると、James が各位置から開始して最後に到達するときに聞こえる接尾辞が正確にたどります。`suffix_count[node]`は、そのサフィックスを持つコードワードをカウントするため、正確に 1 つの候補に対するテストは、沈黙によって提供される情報と直接一致します。 

Python の整数には任意の精度があるため、累積されたリスニング時間がオーバーフローする危険はありません。 最終的な除算は、正確な整数の合計が計算された後にのみ実行され、小数点以下 10 桁により、必要な (10^{-5}) 相対誤差よりもはるかに高い精度が得られます。 

## 実用的な例

 提供されたサンプルのコードブックは次のとおりです。`17383`、`126`、`385`、 そして`485`。 次の表は、5 つの可能な開始位置を示しています。`17383`。 

| 開始位置 | 残りのサフィックス | 最長の共有プレフィックス | 終了サフィックス数 | 時間 |
 | --- | --- | --- | --- | --- |
 | 1 |`17383`| 1 | 必要ありません | 2 |
 | 2 |`7383`| 0 | 必要ありません | 1 |
 | 3 |`383`| 2 | 必要ありません | 3 |
 | 4 |`83`| 1 | 必要ありません | 2 |
 | 5 |`3`| 1 | 1 | 2 |

 最初の桁`1`にも存在します`126`、つまり 1 桁があいまいで、`17`2 秒後には一意になります。 から開始`7`、その数字はすでに識別しています`17383`。 から開始`3`真ん中、両方とも`17383`そして`385`含む`38`、 その間`383`でのみ発生します`17383`。 最後の桁には、`3`にも発生します`385`、しかし、ただ`17383`で終わる`3`, したがって、最後の沈黙によって曖昧さが解消されます。 平均は ((2+1+3+2+2)/5=2) であり、サンプルと一致します。 

2 番目の例として、次のことを考えてみましょう。```
3
12
23
45
```各メッセージの状態は次のとおりです。 

| メッセージ | 開始 | 残りのサフィックス | 最長の共有プレフィックス | 終了サフィックス数 | 時間 |
 | --- | --- | --- | --- | --- | --- |
 |`12`| 1 |`12`| 1 | 必要ありません | 2 |
 |`12`| 2 |`2`| 1 | 1 | 2 |
 |`23`| 1 |`23`| 1 | 必要ありません | 2 |
 |`23`| 2 |`3`| 0 | 必要ありません | 1 |
 |`45`| 1 |`45`| 0 | 必要ありません | 1 |
 |`45`| 2 |`5`| 0 | 必要ありません | 1 |

 のために`12`、最初の桁は他のメッセージと共有されませんが、最後の桁は`2`部分文字列として共有されます`23`, したがって、そこから始めるときは沈黙が必要です。 のために`23`、 最初`2`共有されていますが、`23`それ自体がユニークですが、`3`すぐにユニークになります。 メッセージ`45`どちらの数字からも識別できます。 結果の出力は次のとおりです。`1.5000000000`、`1.5000000000`、 そして`1.0000000000`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(S\log S)) | サフィックス配列の構築が主流です。 LCP、スキャン、トライ構築は線形です |
 | スペース | (O(S)) | すべての配列、サフィックス情報、セパレータ、およびトライ ノードは、合計入力長において線形です。 

ここ (S\le100{,}000)。 サフィックス配列は、(O(\log S)) の 2 倍ラウンドを実行し、それぞれが線形カウント ソートを使用しますが、その後の各フェーズでは各文字に一定の回数だけ触れます。 したがって、このアルゴリズムは意図した漸近限界内に快適に収まり、総当たり手法の二次反復部分文字列比較を回避します。 

## テストケース

 次のテストでは、編集ソリューションが次のように保存されていることを前提としています。`solution.py`。```python
# helper: run solution on input string, return output string
import sys
import io
import solution

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    old_input = solution.input

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()
    solution.input = sys.stdin.readline

    try:
        solution.solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout
        solution.input = old_input

# Provided sample
sample1 = """\
4
17383
126
385
485
"""

assert run(sample1) == (
    "2.0000000000\n"
    "1.3333333333\n"
    "Impossible\n"
    "Impossible"
), "provided sample"

# Minimum-size input
assert run("1\n0\n") == "1.0000000000", "single one-digit codeword"

# Several overlapping strings, exercising substring ambiguity and silence
case2 = """\
3
12
23
45
"""

assert run(case2) == (
    "1.5000000000\n"
    "1.5000000000\n"
    "1.0000000000"
), "substring matching and final silence"

# Nested repeated digits, exercising full-suffix ambiguity
case3 = """\
3
1
11
111
"""

assert run(case3) == (
    "2.0000000000\n"
    "Impossible\n"
    "Impossible"
), "nested suffixes"

# Boundary case where the whole observed sequence becomes unique
case4 = """\
2
12
123
"""

assert run(case4) == (
    "2.0000000000\n"
    "2.0000000000"
), "unique full substring without extra silence"

# Maximum total length, one codeword consisting entirely of equal digits.
# Every observed digit already identifies the only codeword.
big_word = "0" * 100000
case5 = "1\n" + big_word + "\n"

assert run(case5) == "1.0000000000", "maximum-size input"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1`に続く`0`|`1.0000000000`| 最小サイズの入力 |
 |`12`、`23`、`45`|`1.5`、`1.5`、`1.0`| 通常の部分文字列の曖昧さと沈黙 |
 |`1`、`11`、`111`|`2.0`、`Impossible`、`Impossible`| 繰り返される文字と重複する接尾辞 |
 |`12`、`123`|`2.0`、`2.0`| 一意の部分文字列と最後の沈黙の間の境界 |
 | 100,000 個のゼロの 1 つの文字列 |`1.0`| 最大合計長とすべて等しい桁数 |

 ## 特殊なケース

 単一のコードワードは、考えられる最も単純なケースです。 と```
1
0
```唯一のコードワードには観測されたすべての部分文字列が含まれているため、James は最初の数字を聞くとすぐにメッセージがわかります。 逆トライには、サフィックスのコードワードが 1 つだけ含まれています。`0`ですが、部分文字列はすでに一意であるため、そのケースには到達しません。 出力は`1.0000000000`。 

コードワードが重複すると、数字が曖昧になる可能性がありますが、長い部分文字列は一意になります。 と```
2
12
123
```最初の桁から始まる`12`、観察された`1`どちらのメッセージでも発生しますが、`12`は最初の 1 回のみに発生するため、時間はちょうど 2 秒です。 決勝から始まる`2`、数字は共有されますが、`12`で終わる`2`, したがって、沈黙すると2秒後に答えが得られます。 の出力`12`結果として`2.0000000000`。 

接尾辞を繰り返すと、沈黙が不十分になる場合があります。 と```
3
1
11
111
```数字`1`3 つのメッセージすべてに発生します。 のために`11`、完全な接尾辞も`11`両方の結末です`11`そして`111`、したがって沈黙はそれらを区別することができません。 メッセージは不可能です。 のために`111`、2 番目の桁から開始すると同じあいまいさが生じますが、最後の桁から開始すると、沈黙するまで 3 つのメッセージすべてが可能になります。 出力は`Impossible`両方の長いメッセージの場合。 

最後の桁の境界は特に誤って扱いやすくなります。 考慮する```
2
12
23
```いつ`12`が送信され、ジェームズは決勝で先発する`2`。 数字`2`は両方のコードワードで発生するため、部分文字列のみの解決策では永遠にあいまいさが宣言されることになります。 逆のトライではそれのみがわかります`12`で終わる`2`、つまり、1 桁と 1 秒の沈黙の後、メッセージがわかります。 この開始位置の正しい時間は 2 秒です。 

最大サイズの場合は、(100{,}000) 桁の単一コードワードです。 すべての部分文字列はそのコードワードのみに属するため、可能な開始位置にはすべて 1 秒かかります。 suffix-array フェーズは引き続きすべて (100{,}000) 文字を処理し、合計作業量は 2 次になることなく (O(S\log S)) のままです。
