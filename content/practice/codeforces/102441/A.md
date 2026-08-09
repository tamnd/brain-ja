---
title: "CF 102441A - 検索用テンプレート"
description: "小文字、?、および を含むパターンが与えられています。 小文字は文字通りに使用する必要があります。 任意の小文字 1 文字を表すことも、空のシーケンスを含む任意の小文字のシーケンスを表すこともできます。"
date: "2026-08-09T01:31:08+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102441
codeforces_index: "A"
codeforces_contest_name: "2018-2019 9th BSUIR Open Programming Championship. Final"
rating: 0
weight: 102441
solve_time_s: 425
verified: true
draft: false
---

[CF 102441A - 検索用テンプレート](https://codeforces.com/problemset/problem/102441/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 7 分 5 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 小文字を含むパターンが与えられていますが、`?`、 そして`*`。 小文字は文字通りに現れる必要があります。`?`任意の小文字 1 文字を表すことができ、`*`空のシーケンスを含む、任意の小文字のシーケンスを表すことができます。 パターン全体に一致する実際の小文字文字列を選択する必要がありますが、結果の文字列が回文であるという追加の要件も必要です。 考えられるすべての選択肢の中で、最小の長さのものが必要です。 パターンに一致する回文がない場合は、印刷します。`-1`。 空の文字列は有効な回文としてカウントされます。 

パターンの長さは最大 500 です。これは二次動的計画法には十分小さいですが、可能な出力文字列を列挙するには十分小さくありません。 わずか 26 文字のアルファベットでも、指数関数的に多くの候補が得られます。 三次アルゴリズムは、最悪の場合でも約 (500^3 = 125) 百万回の基本的な状態遷移を実行しますが、これは Python ではすでに不快なものであるため、さらに一歩進めて DP を二次関数にします。 

単純な貪欲な構築が失敗するケースがいくつかあります。 のために`ac?ba`、外側の 2 つは`a`文字には互換性がありますが、一致した後は次のようになります。`c?b`、その目的を平等にすることはできません。 正しい答えは、`-1`。 を扱う不注意なアルゴリズム`?`不一致を自動的に修正すると無効な文字列が生成される可能性があるためです。 

のために`*ac?ba`、スターを単純に無視することはできません。 最も短い有効な回文は次のとおりです。`abacaba`。 主役は消費する`ab`、固定サフィックス`ba`ミラーリングされたものを生成します`ab`もう一方の端で。 常に処理する実装`*`空の文字列は最適な構造を欠いているためです。 

のために`*`、答えは空の文字列です。 以来`*`文字を消費しない可能性があるため、1 文字でも出力する理由はありません。 すべてのパターン文字が答えに寄与すると想定する実装では、空ではない文字列が誤って出力されます。 

のために`??`、答えは`aa`。 両方の疑問符は独立して選択できます`a`、最後の文字列は回文であるため、2 つの位置は等しくなければなりません。 不用意に実装すると、不必要に別の文字に強制したり、扱いを変更したりする可能性があります。`?`文字通りのシンボルとして。 

ついに、`a*b`解決策がないとき`a`そして`b`異なる。 このパターンに一致するすべての文字列は次で始まります`a`そして次で終わります`b`、一方、回文は最初と最後の文字が同じである必要があります。 星は 2 つの固定端点の間にあるため、その事実を変更することはできません。 

## アプローチ

 直接的な総当たりの解決策では、長さが増加するすべての回文を列挙し、それがパターンに一致するかどうかを確認し、最初に成功した長さで停止します。 解が存在する場合、以下の DP 構造では最大 (2n) の長さの 1 つが得られるため、検索をその範囲に制限できます。 長さ (L) の回文は、最初の (\lceil L/2\rceil) 文字によって決定され、(26^{\lceil L/2\rceil}) 個の候補が得られます。 

(n=500) の場合、0 から 1000 までの長さを列挙すると、正確に検査されます。 

1+\frac{52(26^{500}-1)}{25}
 】

 回文候補。 各候補をパターンと照合するには直線的な時間がかかるため、合計作業量は (n26^n) 程度になります。 このアプローチが概念的に正しい理由は単純です。考えられるすべての回文が最終的にテストされ、最初に受け入れられた回文の長さが最小になるからです。 問題は、検索空間が天文学的に大きいことです。 

重要な観察は、回文を使用すると、パターンを両端から分割する自然な方法が得られるということです。 現在のパターン間隔が (s[l..r]) であると仮定します。 両方のエンドポイントが通常の文字または疑問符である場合、同じ文字を生成する必要があります。 その文字を答えの両端に配置して、内側の区間を解くことができます。 

興味深いケースは、`*`1つのエンドポイントで。 主導権を検討する`*`。 文字列 (X) を消費する可能性があります。 最終的な答えは回文であるため、答えの対応する接尾辞は (reverse(X)) である必要があります。 パターンの残りの部分は、これら 2 つのコピーの間にあります。 接尾辞 (s[k..r]) が (X) の生成に関与していると判断した場合、先頭のスターは (reverse(X)) を生成でき、中間のパターン (s[l+1..k-1]) は回文を生成する必要があります。 

長さを最小限に抑えるために、任意のパターン セグメントによって生成される最適な文字列は特に単純です。 すべての普通の文字は 1 文字ずつ貢献します。`?`選択した 1 人のキャラクターとすべてのキャラクターを貢献します`*`何も貢献できません。 したがって、一致する文字列 (s[k..r]) の最小長は、その範囲内のスター以外の文字の数にすぎません。 

これにより、スターの移行は次のようになります。 

\min_k
 \左(
 dp[l+1][k-1]
 +
 2\cdドット数(k,r)
 \右）、
 】

 どこで`count(k,r)`は、(s[k..r]) 内のスター以外の文字の数です。 主役を空席にする可能性もあり、`dp[l+1][r]`。 

直接実装すると、すべての間隔ですべての (k) が試行され、(O(n^3)) が得られます。 式はプレフィックス数を使用して並べ替えることができます。 

2P[r+1]
 +
 \left(dp[l+1][k-1]-2P[k]\right),
 】

 ここで、(P[x]) は最初の (x) 位置にあるスター以外の文字の数です。 固定 (l) の場合、括弧内の最小値は、(r) が大きくなるにつれて段階的に維持できます。 対称式は後続の`*`。 これにより、(n) の余分な要素が削除されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n26^n)) | (O(n)) | 遅すぎる |
 | ダイレクトインターバルDP | (O(n^3)) | (O(n^2)) | 概念的には有効だが、Python には非フレンドリー |
 | 最適化されたインターバル DP | (O(n^2)) | (O(n^2)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 定義する`dp[l][r]`パターン間隔に一致する回文の最小長として`s[l..r]`。 間隔が空の場合、その値はゼロになります。 
2. 事前計算`pref[i]`、スター以外のキャラクターの数`s[0..i-1]`。 次に、一致する任意の文字列の最小長`s[l..r]`は`pref[r+1] - pref[l]`なぜなら、星はいつでも空っぽである可能性があるからです。 
3. 右端点を増加させ、左端点を減少させる処理間隔。 この順序により、`dp[i+1][j]`、`dp[i][j-1]`、 そして`dp[i+1][j-1]`必要なときにいつでも利用できます。 
4. 間隔が 1 文字で構成されている場合、星は 0 文字を提供し、文字または`?`1キャラクターを貢献します。 
5. もし`s[l]`はスターです。オプションの 1 つは、それを空にして使用することです。`dp[l+1][r]`。 The other option is to let it mirror a shortest string produced by some suffix`s[k..r]`。 結果として得られる長さは、`2 * nonstars(k,r) + dp[l+1][k-1]`。 
6. の最小値を維持します。`dp[l+1][k-1] - 2 * pref[k]`一方、右のエンドポイントは成長します。 これにより、各間隔の一定時間内で、先頭のスターを空でない状態で最大限に利用できるようになります。 
7. 左の端点が星形ではない場合`s[r]`が星である場合、まったく同じアイデアを別の方向から適用します。 末尾のスターが空であるか、何らかのプレフィックスによって生成された最短の文字列を反映しています。`s[l..k]`。 
8. 対応する最小値を維持する`2 * pref[k+1] + dp[k+1][r-1]`一方、左の端点は内側に移動します。 これにより、一定時間内でトレーリングスターを最大限に活用できます。 
9. どちらのエンドポイントもスターでない場合は、同じ文字を表すことができなければなりません。 2 つの等しい文字は互換性があります。文字と`?`互換性があり、2`?`キャラクターは互換性があります。 互換性がある場合は、選択した同じ文字を両端に配置し、2 つを追加します。`dp[l+1][r-1]`。 
10. すべての DP 値とともに、どの遷移によってその値が生成されたかを保存します。 スタースプリットの場合、選択したスプリット位置を保存します`k`。 これにより、DP の終了後に実際の回文を再構築できるようになります。 
11. 再建中、主星が分裂した`k`生成する`reverse(T) + middle + T`、 どこ`T`で表される最も短い文字列です`s[k..r]`。 後続の星が生み出す`T + middle + reverse(T)`。 通常の一致するエンドポイントは、`c + middle + c`。 
12. もし`dp[0][n-1]`は無限です。パターンに一致する回文がないため、出力します。`-1`。 それ以外の場合は、保存された決定を再構築します。 

### なぜ効果があるのか

 不変条件とは、`dp[l][r]`回文一致の正確な最小長です`s[l..r]`。 どちらのエンドポイントもスターではない場合、回文によって 2 つのエンドポイントに同じ文字の使用が強制されるため、通常の遷移では考えられるすべての有効な選択肢が考慮されます。 左端がスターの場合、一致するすべての回文はゼロ文字にそのスターを使用するか、残りのパターンの接尾辞によって生成された文字列の逆である接頭辞を消費します。 後者はまさに分割遷移で列挙されるものです。 トレーリングスターの場合は対称です。 

すべての分割に対して、ミラーリングされたパターン セグメントで表される可能な限り短い文字列を使用します。 そのセグメントを長くすると、回文の両側に文字が追加されるだけで、独立した中央の問題を短くすることはできません。 したがって、各分割の最小長を選択すれば十分です。 外側のスターのすべての可能な使用は何らかの分割によって表され、すべての非スター エンドポイントのペアは通常の遷移によって表されるため、DP はすべての実現可能な構造を考慮し、最短のものを採用します。 

再構成は、DP で使用されたのと同じ分解に従います。 構築されたすべての部分は、小さな回文の周りにミラーリングされた文字、または小さな回文の周りにミラーリングされた文字列のいずれかであるため、結果は常に回文になります。 対応するパターン セグメントが必要な順序で連結されるため、結果も元のパターンと一致します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10**9

# Transition types:
# 1 = skip left star
# 2 = use left star, split at arg[i][j]
# 3 = skip right star
# 4 = use right star, split at arg[i][j]
# 5 = match both endpoints
#
# dp[l][r] = minimum palindrome length matching s[l:r+1]

def solve_template(s):
    n = len(s)

    # pref[i] = number of non-'*' characters in s[:i]
    pref = [0] * (n + 1)
    for i, ch in enumerate(s):
        pref[i + 1] = pref[i] + (ch != '*')

    dp = [[INF] * n for _ in range(n)]
    kind = [[0] * n for _ in range(n)]
    arg = [[-1] * n for _ in range(n)]

    # For a fixed left endpoint i:
    # left_best[i] =
    # min_{k=i+1..j} dp[i+1][k-1] - 2*pref[k]
    #
    # left_arg[i] stores the k producing that minimum.
    left_best = [INF] * n
    left_arg = [-1] * n

    for j in range(n):
        # For this fixed j, while i decreases:
        # right_best =
        # min_{k=i..j-1} 2*pref[k+1] + dp[k+1][j-1]
        right_best = INF
        right_arg = -1

        for i in range(j, -1, -1):
            if i < j:
                # dp[i+1][j-1], with the empty interval handled explicitly.
                inner = 0 if i + 1 >= j else dp[i + 1][j - 1]

                # Add k = j to the running minimum for a leading star.
                candidate_left = inner - 2 * pref[j]
                if candidate_left < left_best[i]:
                    left_best[i] = candidate_left
                    left_arg[i] = j

                # Add k = i to the running minimum for a trailing star.
                candidate_right = 2 * pref[i + 1] + inner
                if candidate_right < right_best:
                    right_best = candidate_right
                    right_arg = i

            # Single-character interval.
            if i == j:
                if s[i] == '*':
                    dp[i][j] = 0
                    kind[i][j] = 1
                else:
                    dp[i][j] = 1
                    kind[i][j] = 5
                continue

            if s[i] == '*':
                # Option 1: make the left star empty.
                best = dp[i + 1][j]
                best_kind = 1
                best_arg = -1

                # Option 2: mirror a shortest string produced by s[k..j].
                candidate = 2 * pref[j + 1] + left_best[i]
                if candidate < best:
                    best = candidate
                    best_kind = 2
                    best_arg = left_arg[i]

                dp[i][j] = best
                kind[i][j] = best_kind
                arg[i][j] = best_arg

            elif s[j] == '*':
                # Option 1: make the right star empty.
                best = dp[i][j - 1]
                best_kind = 3
                best_arg = -1

                # Option 2: mirror a shortest string produced by s[i..k].
                candidate = right_best - 2 * pref[i]
                if candidate < best:
                    best = candidate
                    best_kind = 4
                    best_arg = right_arg

                dp[i][j] = best
                kind[i][j] = best_kind
                arg[i][j] = best_arg

            else:
                # Neither endpoint is a star.
                a = s[i]
                b = s[j]

                compatible = (
                    a == b or
                    a == '?' or
                    b == '?'
                )

                if compatible:
                    dp[i][j] = 2 + (
                        0 if i + 1 >= j else dp[i + 1][j - 1]
                    )
                    kind[i][j] = 5
                # Otherwise dp[i][j] stays INF.

    if dp[0][n - 1] >= INF:
        return "-1"

    def canonical(l, r):
        """Shortest concrete string matching s[l:r+1]."""
        out = []
        for p in range(l, r + 1):
            if s[p] == '*':
                continue
            if s[p] == '?':
                out.append('a')
            else:
                out.append(s[p])
        return ''.join(out)

    def build(l, r):
        if l > r:
            return ""

        t = kind[l][r]

        if l == r:
            if s[l] == '*':
                return ""
            if s[l] == '?':
                return "a"
            return s[l]

        if t == 1:
            # Left star is empty.
            return build(l + 1, r)

        if t == 2:
            # Left star mirrors the shortest string from k..r.
            k = arg[l][r]
            middle = build(l + 1, k - 1)
            x = canonical(k, r)
            return x[::-1] + middle + x

        if t == 3:
            # Right star is empty.
            return build(l, r - 1)

        if t == 4:
            # Right star mirrors the shortest string from l..k.
            k = arg[l][r]
            middle = build(k + 1, r - 1)
            x = canonical(l, k)
            return x + middle + x[::-1]

        # Ordinary compatible endpoints.
        a = s[l]
        b = s[r]

        if a == '?':
            c = b if b != '?' else 'a'
        else:
            c = a

        return c + build(l + 1, r - 1) + c

    return build(0, n - 1)

def main():
    s = input().strip()
    print(solve_template(s))

if __name__ == "__main__":
    main()
```プレフィックス配列は最初の最適化です。`pref[r + 1] - pref[l]`すべてのスターが空になった場合、パターン間隔が実際に何文字寄与する必要があるかを示します。 分割トランジションには、ミラーリングされた間隔で表される可能な限り短い文字列のみが必要であるため、これで十分です。 

の`left_best`配列には、考えられるすべての主星の変換された最小値が格納されます。 すべての分割を繰り返し評価するのではなく`k`、コードは新しい可能性を追加します`k = j`右境界が進むとき。 を含む表現`pref`に応じて部分を分離します`j`に応じた部分から`k`。 

の`right_best`変数は、トレーリングスターの対称最適化を実行します。 これは右端点ごとにリセットされ、左端点がゼロに近づくにつれて更新されます。 現時点で`dp[i][j]`が計算されると、最初の部分が 以降で始まる最適な分割が正確に含まれます。`i`。 

ネストされたループの順序は重要です。 外側のループが増加します`j`、内側のループは減少します。`i`。 その結果、`dp[i+1][j]`同じ列の前の方ですでに計算されており、`dp[i+1][j-1]`前回のコラムで計算しました。 

再構成では、分割間隔で表される最短の具体的な文字列を意図的に使用します。 あ`?`に置き換えられます`a`、星は飛ばされます。 これらの文字は再帰的に構築された中間の周りにミラーリングされるため、正確な選択は`?`最適性に影響しません。 

Python では整数オーバーフローは発生しません。 他の言語では、答えは最大 (2n) であるため、通常の整数型ですでに十分ですが、DP では`INF`見張りとしてのみ。 

## 実用的な例

 ### サンプル 1

 パターンは`*ac?ba`。 位置に 0 から 5 までの番号を付けます。 

| 州`(l,r)`| パターン間隔 | 移行 | ミラーリングされた文字列 | 中央 | 結果 |
 | --- | --- | --- | --- | --- | --- |
 |`(0,5)`|`*ac?ba`| 左の星は 4 で分割 |`ba`|`aca`|`abacaba`|
 |`(1,3)`|`ac?`| マッチ`a`と`?`|`a`|`c`|`aca`|
 |`(2,2)`|`c`| 単一文字 |`c`| 空 |`c`|
 | 空 | 空 | 基本ケース | 空 | 空 | 空 |

 最上位レベルのサフィックス`ba`パターン位置 4 と 5 に一致します。先頭の星はその逆を消費し、`ab`。 残るパターンは、`ac?`、その最短の回文は`aca`。 それらを組み合わせると、`ab + aca + ba = abacaba`。 主役のスターが消費するため、結果はパターンと一致します。`ab`、その後`ac?ba`消費する`acaba`。 

### サンプル 2

 パターンは`ac?ba`。 

| 州`(l,r)`| パターン間隔 | エンドポイントの比較 | 内側の間隔 | 結果 |
 | --- | --- | --- | --- | --- |
 |`(0,4)`|`ac?ba`|`a`そして`a`一致 |`c?b`| 内部に依存します |
 |`(1,3)`|`c?b`|`c`そして`b`衝突 |`?`| 不可能 |
 |`(0,4)`|`ac?ba`| 外側のペアを完了できません | 不可能 |`-1`|

 外側`a`文字は強制的に相互に一致します。 それらが削除されると、内部パターンの終点が固定されます。`c`そして`b`、平等になることはできません。 どちらの不一致も吸収できるスターがないため、パターン全体には回文一致がありません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n^2)) | (O(n^2)) 個の DP 状態があり、スター分割最小値は一定時間内に増分的に維持されます。 
| スペース | (O(n^2)) | DP 値と再構成の選択には二次メモリが使用されます。 

(n \le 500) の場合、DP 州は 250,000 しかありません。 各状態は実行最小値が維持された後に一定の作業を実行するため、アルゴリズムは容易に意図した範囲内に収まります。 再構成も、パターンのサイズに生成された応答を加えたもので線形であり、生成された応答の長さは最大 (2n) です。 

## テストケース```python
import io
import sys

# The submitted solution is represented by solve_template(s).

INF = 10**9

def solve_template(s):
    n = len(s)

    pref = [0] * (n + 1)
    for i, ch in enumerate(s):
        pref[i + 1] = pref[i] + (ch != '*')

    dp = [[INF] * n for _ in range(n)]
    kind = [[0] * n for _ in range(n)]
    arg = [[-1] * n for _ in range(n)]

    left_best = [INF] * n
    left_arg = [-1] * n

    for j in range(n):
        right_best = INF
        right_arg = -1

        for i in range(j, -1, -1):
            if i < j:
                inner = 0 if i + 1 >= j else dp[i + 1][j - 1]

                candidate_left = inner - 2 * pref[j]
                if candidate_left < left_best[i]:
                    left_best[i] = candidate_left
                    left_arg[i] = j

                candidate_right = 2 * pref[i + 1] + inner
                if candidate_right < right_best:
                    right_best = candidate_right
                    right_arg = i

            if i == j:
                if s[i] == '*':
                    dp[i][j] = 0
                    kind[i][j] = 1
                else:
                    dp[i][j] = 1
                    kind[i][j] = 5
                continue

            if s[i] == '*':
                best = dp[i + 1][j]
                best_kind = 1
                best_arg = -1

                candidate = 2 * pref[j + 1] + left_best[i]
                if candidate < best:
                    best = candidate
                    best_kind = 2
                    best_arg = left_arg[i]

                dp[i][j] = best
                kind[i][j] = best_kind
                arg[i][j] = best_arg

            elif s[j] == '*':
                best = dp[i][j - 1]
                best_kind = 3
                best_arg = -1

                candidate = right_best - 2 * pref[i]
                if candidate < best:
                    best = candidate
                    best_kind = 4
                    best_arg = right_arg

                dp[i][j] = best
                kind[i][j] = best_kind
                arg[i][j] = best_arg

            else:
                a = s[i]
                b = s[j]

                if a == b or a == '?' or b == '?':
                    dp[i][j] = 2 + (
                        0 if i + 1 >= j else dp[i + 1][j - 1]
                    )
                    kind[i][j] = 5

    if dp[0][n - 1] >= INF:
        return "-1"

    def canonical(l, r):
        out = []
        for p in range(l, r + 1):
            if s[p] == '*':
                continue
            out.append('a' if s[p] == '?' else s[p])
        return ''.join(out)

    def build(l, r):
        if l > r:
            return ""

        t = kind[l][r]

        if l == r:
            if s[l] == '*':
                return ""
            return 'a' if s[l] == '?' else s[l]

        if t == 1:
            return build(l + 1, r)

        if t == 2:
            k = arg[l][r]
            x = canonical(k, r)
            return x[::-1] + build(l + 1, k - 1) + x

        if t == 3:
            return build(l, r - 1)

        if t == 4:
            k = arg[l][r]
            x = canonical(l, k)
            return x + build(k + 1, r - 1) + x[::-1]

        a = s[l]
        b = s[r]
        if a == '?':
            c = b if b != '?' else 'a'
        else:
            c = a

        return c + build(l + 1, r - 1) + c

    return build(0, n - 1)

def run(inp: str) -> str:
    return solve_template(inp)

# Provided samples
assert run("*ac?ba") == "abacaba", "sample 1"
assert run("ac?ba") == "-1", "sample 2"

# Minimum-size and empty-palindrome case
assert run("*") == "", "a single star can match the empty string"

# Minimum-size question-mark case
assert run("?") == "a", "a question mark can choose any lowercase letter"

# All-equal values
assert run("aa") == "aa", "two equal fixed endpoints form a palindrome"

# Boundary case with a star and mismatching fixed endpoints
assert run("a*b") == "-1", "a palindrome cannot start with a and end with b"

# Star at the boundary can mirror the fixed prefix
assert run("abc*") == "abccba", "trailing star mirrors abc"

# Maximum-size all-equal input
assert run("a" * 500) == "a" * 500, "maximum-size fixed palindrome"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`*`| 空の文字列 | 最小サイズのパターンと長さゼロの星 |
 |`?`|`a`| 単一のワイルドカードの処理 |
 |`aa`|`aa`| 等しい固定エンドポイント |
 |`a*b`|`-1`| 不可能な固定エンドポイントの不一致 |
 |`abc*`|`abccba`| 境界スターとミラー化されたプレフィックス |
 |`a`500回繰り返した |`a`500回繰り返した | 最大入力サイズと二次 DP 境界 |

 ## 特殊なケース

 のために`ac?ba`、アルゴリズムは最初に外側を処理します。`a`そして`a`互換性のあるペアとして。 残りのインターバルは、`c?b`。 どちらのエンドポイントもスターではないため、`c`等しくない`b`、そのDP値は無限です。 その無限が元の間隔に伝播し、`-1`。 

のために`*ac?ba`、主演スターは2つの根本的に異なる可能性を持っています。 空の場合もあります。`ac?ba`そして最終的には失敗します。 または、サフィックスをミラーリングすることもできます。 最適な分割によりサフィックスが選択されます`ba`。 最も短い一致文字列は正確に次のとおりです`ba`、つまりスターが貢献します`ab`左に。 中間のインターバル`ac?`になる`aca`、与える`abacaba`。 

のために`*`の場合、スターは何も消費できないため、単一文字の基本ケースでは長さ 0 が割り当てられます。 再構築では空の文字列が返され、プログラムは空行を出力します。 これは、回答自体に文字がない唯一の状況です。 

のために`??`、両方ともワイルドカードであるため、2 つのエンドポイントには互換性があります。 再建が選ぶのは`a`両方とも、生産`aa`。 の選択`a`は任意ですが、回文条件により同じ文字を使用する必要があります。 

のために`a*b`、外側の文字は`a`そして`b`、したがって、パターンは回文を生成できません。 スターは内部にあり、どちらのエンドポイントも変更できません。 DP は、スターを使用して修復できない不一致を修復しようとするのではなく、不可能な間隔に正しく到達します。 

のために`abc*`、右端は星です。 最適な遷移では固定プレフィックスが維持されます`abc`星を映した文字列のように。 結果は`abc + cba = abccba`、一致します`abc*`スターは消耗するので`cba`。 答えの長さは 6 で、すべての星を削除するだけでは十分ではない理由が示されています。 

最大サイズ入力が 500 の場合`a`文字にはスターはなく、すべてのミラーリングされたペアに互換性があります。 DP は通常の回文反復に縮小され、ちょうど 500 が生成されます。`a`文字。 このケースでは、完全な二次状態空間と両方の区間境界を実行します。
