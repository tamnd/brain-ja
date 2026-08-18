---
title: "CF 102270C - 分割"
description: "3 つの独立して見える制限を満たす、包含範囲 ([A,B]) 内の正の整数 (N) をカウントする必要があります。 まず、(N) は (X) で割り切れなければなりません。 次に、(N) のすべての 10 進数は、文字列 (S) で記述されるセットに属している必要があります。"
date: "2026-08-17T18:24:26+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102270
codeforces_index: "C"
codeforces_contest_name: "HCW 19 Individual Day 2"
rating: 0
weight: 102270
solve_time_s: 319
verified: false
draft: false
---

[CF 102270C - 分割](https://codeforces.com/problemset/problem/102270/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 5 分 19 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 3 つの独立して見える制限を満たす、包含範囲 ([A,B]) 内の正の整数 (N) をカウントする必要があります。 

まず、(N) は (X) で割り切れなければなりません。 次に、(N) のすべての 10 進数は、文字列 (S) で記述されるセットに属している必要があります。 第三に、(1) から右に数えて位置 (i) に数字がある場合、その数字 (d) は (\gcd(d,i)=1) を満たす必要があります。 

最後の条件は珍しい部分です。 たとえば、単位の桁は、すべての桁が (1) と互いに素であるため、常に gcd 条件によって制限されません。 位置 (2) では、奇数桁のみが出現できます。 (3) の位置では、(3) で割り切れる数字は禁止されています。 位置 (5) では、(0) と (5) の両方が禁止されます。 ゼロの数字は特に誤って処理されやすいです。(\gcd(0,1)=1) であるため、ゼロは位置 (1) では有効ですが、(1) を超えるすべての位置では無効になります。 

境界は、直接列挙が不可能な本当の理由です。 (B) には、最大許容値が (10^{100}) であるため、最大 (101) の 10 進数を含めることができますが、(X) は (10^5) までの大きさにすることができます。 間隔内の整数を反復処理することはできず、有効な数字列をすべて列挙することさえできません。 この解決策は、剰余モジュロ (X) を使用して、レイヤーごとにおよそ (O(100X)) 個の状態を持つ数字 DP を与える必要があります。 

間違いやすい境界例がいくつかあります。 考慮する```
1 20 2
02
```上限付近で使用可能な桁を使用する唯一の候補は (20) ですが、その 10 の位が (2) であり、(\gcd(2,2)=2) であるため無効です。 正解は (0) です。 数字が (S) に属するかどうかだけをチェックする実装では、数字が誤ってカウントされます。 

ここで考えてみましょう```
10 10 2
01
```答えは (1) です。(10) は (2) で割り切れ、その単位の位は (0) と (\gcd(0,1)=1)、十の位は (1) と (\gcd(1,2)=1) であるためです。 単純にゼロ桁を拒否する実装では、このケースは見逃されます。 

2 番目の微妙なケースは 1 桁です。```
1 1 1
0
```許可される唯一の数字はゼロであるため、答えは (0) ですが、この問題では正の自然数がカウントされるため、(0) 自体を含めることはできません。 これが、DP がゼロである単位桁と、ゼロである完全な 1 桁の数値を区別する必要がある理由です。 

最後に、上限には有効な数値より多くの桁が含まれる可能性があります。 たとえば、(B=10^{100}) の場合、(101) 桁未満の有効な数値はすべて自動的に (B) より小さくなります。 先行ゼロを実際の数字として扱うと、存在しない先行位置に位置 gcd 条件が誤って適用されます。 

## アプローチ

 ブルートフォースアプローチは簡単です。 (A) から (B) までのすべての整数を反復し、(X) で割り切れない場合はその整数を拒否し、その桁を調べて、すべての位置の桁セット制限と gcd 条件の両方を検証します。 すべての候補が直接チェックされるため、これは正しいです。 

問題は間隔の大きさです。 (A=1) かつ (B) が (10^{100}) に近い場合、実質的に (10^{100}) 個の候補が存在します。 各候補の 1 桁をチェックするだけでも、信じられないほど多くの操作が必要になります。 整数を文字列に変換しても、基本的な複雑さは変わりません。 

有益な観察は、部分的に構築された数値の値は決して必要ではなく、その剰余 (X) だけが必要であるということです。 最大 (10^5) 個の異なる剰余が存在します。 gcd 条件も位置と選択した桁にのみ依存するため、位置 (i) についてはどの桁が正当であるかを正確に事前計算できます。 

位置方向を便利にするもう 1 つの構造上の詳細があります。 数字は右から左に処理されます。 位置 (1) から (i) を処理した後の残りは単純に次のようになります。 

[
 r=\sum_{j=1}^{i} d_j10^{j-1}\pmod X.
 】

 新しい数字が位置 (i+1) に配置されると、その寄与は (d10^i\pmod X) としてすぐにわかります。 さらに重要なのは、固定長の数値を境界と比較する場合、新しく追加された数値が、すでに処理されたすべての数値よりも重要であるということです。 対応する境界桁と異なる場合、その数値が小さいか大きいかを完全に決定します。 等しい場合は下位の比較結果を保持する。 

これにより、主状態として剰余を含む数字 DP と、境界長の 3 値比較状態が得られます。 

また、可能な長さごとに個別の数字 DP を実行することも避けます。 各長さの有効な文字列の数を段階的に維持します。 新しい最上位桁が追加されると、古い桁はすべて右から数えてまったく同じ位置を維持するため、有効性は変わりません。 新しい桁は、その新しい位置の条件を満たすだけで済みます。 

2 つのアプローチは次のように要約できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O((B-A+1)\log B)) | (O(\log B)) | 遅すぎる |
 | 最適桁 DP | (O(LX D)) | (O(X)) | 承認済み |

 ここで、(L\le101) は関係する桁数、(D\le10) は使用可能な 10 進数の桁数です。 最適な方法では、2 つの範囲境界に対してそのような DP パスを一定数実行します。 

## アルゴリズムのチュートリアル

 1. 数字列 (S) を許可された数字の配列に変換します。 (1) から必要な最大長までのすべての位置 (i) について、(d\in S) と (\gcd(d,i)=1) の両方を満たす数字のリスト (d) を作成します。 これにより、gcd テストがメイン DP ループから移動されます。 
2. 剰余モジュロ (X) によってインデックス付けされた DP 配列を維持します。 右から左に数字を構築しながら、`dp[r]`は、(X) を法とする数値が (r) である、可能な下位桁シーケンスの数を表します。 
3. ゼロを含むすべての許可された数字を使用して位置 (1) を初期化します。 (10) のように、ゼロはより大きな数値の完全に有効な単位の桁であるため、ゼロはこの状態のままでなければなりません。 
4. DP を位置 (i) から位置 (i+1) まで拡張します。 新しい数字は新しい最上位数字であるため、ゼロ以外の値にする必要があります。 剰余への寄与は (d10^i\bmod X) です。 したがって、剰余 (r) がある状態は次のようになります。 

[
 (r+d10^i)\bmod X.
 】

 新しく追加された数字は最上位の数字であるため、それをゼロ以外にする必要があるため、結果の文字列が正確に (i+1) 桁の数値を表すことが保証されます。 

1.ストア`dp[0]`各拡張の後に、(X) で割り切れる正確な長さの有効な正の整数の数として表示されます。 長さ (1) の場合、初期 DP には可能な単位桁としてゼロが含まれていますが、ゼロは正の整数ではないため、ゼロを個別に処理します。 
2. (B) のような境界を処理するには、その正確な桁長のみに特別な処理が必要です。 桁数の少ない有効な数値はすべて、自動的に (B) より小さくなります。 以前に計算された正確な長さのカウントは、これらすべての短い数値を処理できます。 
3. 境界の正確な長さを求めるには、小さい、等しい、大きいという 3 つの値を含む比較状態を追加します。 数値を右から左に処理します。 新しく追加された桁が境界の対応する桁より小さい場合、完全な比較は小さくなります。 大きくすると比較も大きくなります。 等しい場合、前の比較は変更されません。 
4. 境界 DP の終わりで、比較結果が小さいか等しい剰余ゼロ状態を合計します。 これにより、境界とまったく同じ長さで、それを超えない有効な整数の数が得られます。 
5. 定義する`count_leq(B)`(B) の長さ未満のすべての正確な長さの合計に、(B) の長さの境界 DP の結果を加えたもの。 同様に計算します`count_leq(A-1)`そして 2 つの値を法 (10^9+7) で減算します。 
6. 10 進数の文字列から 1 を減算することは、文字列に対して直接行われます。 (A) と (B) は組み込みの 64 ビット整数よりも大きい可能性があるため、これが必要です。 

### なぜ効果があるのか

 主な不変条件は、位置 (i) を処理した後、すべての DP 状態が位置 (1) から (i) までの有効な選択肢を正確に表し、剰余モジュロ (X) によってのみグループ化され、境界 DP の場合は境界の対応するサフィックスとの比較によってグループ化されるということです。 

位置条件は数字が導入されるときに正確にチェックされるため、格納されたすべてのシーケンスは gcd 要件を満たします。 剰余遷移は標準の位置値式であるため、状態 (0) には (X) で割り切れる数値が正確に含まれます。 新しく追加されたすべての最上位桁がゼロ以外であることを要求すると、すべての正の整数が先頭のゼロの重複がなく、正確に 1 つの表現になります。 

境界 DP の場合、新しい上位桁を追加すると、境界と異なる場合は常に、すべての下位桁の比較が正しくオーバーライドされます。 したがって、最終的な比較状態は、構築された数値と境界との間の通常の数値比較とまったく同じになります。 すべての短い長さを正確な長さの境界カウントと組み合わせると、その結果、境界までのすべての有効な数値が 1 回だけカウントされます。 

## Python ソリューション```python
import sys
from math import gcd

input = sys.stdin.readline

MOD = 1_000_000_007

def dec_string(s):
    """Return s - 1 for a non-negative decimal string."""
    s = s.lstrip('0') or '0'
    if s == '0':
        return '-1'

    a = list(s)
    i = len(a) - 1

    while a[i] == '0':
        a[i] = '9'
        i -= 1

    a[i] = chr(ord(a[i]) - 1)
    res = ''.join(a).lstrip('0')
    return res or '0'

def prepare_digits(s, max_len):
    digits = [ord(c) - 48 for c in s]

    valid = [[] for _ in range(max_len + 1)]
    for pos in range(1, max_len + 1):
        valid[pos] = [d for d in digits if gcd(d, pos) == 1]

    return valid

def exact_counts(max_len, x, valid):
    """
    counts[len] = number of valid positive numbers with exactly len digits
    that are divisible by x.
    """
    counts = [0] * (max_len + 1)

    # Position 1 is the units digit. Zero is allowed here because it can
    # be the last digit of a multi-digit number.
    dp = [0] * x
    for d in valid[1]:
        dp[d % x] += 1

    # A one-digit number itself cannot be zero.
    c = 0
    for d in valid[1]:
        if d != 0 and d % x == 0:
            c += 1
    counts[1] = c % MOD

    power = 10 % x

    for pos in range(2, max_len + 1):
        ndp = [0] * x

        # Position pos is the most significant digit, so it must be nonzero.
        shifts = []
        for d in valid[pos]:
            if d != 0:
                shifts.append((d * power) % x)

        for shift in shifts:
            if shift == 0:
                for r in range(x):
                    ndp[r] += dp[r]
            else:
                cut = x - shift
                for r in range(cut):
                    ndp[r + shift] += dp[r]
                for r in range(cut, x):
                    ndp[r - cut] += dp[r]

        for r in range(x):
            ndp[r] %= MOD

        dp = ndp
        counts[pos] = dp[0]

        power = (power * 10) % x

    return counts

def boundary_count(bound, x, valid):
    """
    Count valid positive numbers with exactly len(bound) digits,
    divisible by x, and <= bound.
    """
    if bound == '0':
        return 0

    n = len(bound)

    # Relation:
    # 0 = smaller, 1 = equal, 2 = larger.
    less = [0] * x
    equal = [0] * x
    greater = [0] * x

    bound_digit = ord(bound[-1]) - 48

    # Start with the units digit. Zero is allowed here because the final
    # number may have more digits.
    for d in valid[1]:
        r = d % x
        if d < bound_digit:
            less[r] += 1
        elif d == bound_digit:
            equal[r] += 1
        else:
            greater[r] += 1

    if n == 1:
        # Only nonzero digits represent positive one-digit numbers.
        ans = 0
        for d in valid[1]:
            if d == 0:
                continue
            r = d % x
            if d <= bound_digit and r == 0:
                ans += 1
        return ans % MOD

    power = 10 % x

    for pos in range(2, n + 1):
        ndp_less = [0] * x
        ndp_equal = [0] * x
        ndp_greater = [0] * x

        bd = ord(bound[n - pos]) - 48

        for d in valid[pos]:
            if d == 0:
                continue

            shift = (d * power) % x

            if d < bd:
                # The new, more significant digit makes the whole number
                # smaller, regardless of the old comparison.
                if shift == 0:
                    for r in range(x):
                        ndp_less[r] += less[r] + equal[r] + greater[r]
                else:
                    cut = x - shift
                    for r in range(cut):
                        ndp_less[r + shift] += less[r] + equal[r] + greater[r]
                    for r in range(cut, x):
                        ndp_less[r - cut] += less[r] + equal[r] + greater[r]

            elif d > bd:
                # The new digit makes the whole number larger.
                if shift == 0:
                    for r in range(x):
                        ndp_greater[r] += less[r] + equal[r] + greater[r]
                else:
                    cut = x - shift
                    for r in range(cut):
                        ndp_greater[r + shift] += less[r] + equal[r] + greater[r]
                    for r in range(cut, x):
                        ndp_greater[r - cut] += less[r] + equal[r] + greater[r]

            else:
                # Equal new digit preserves the previous comparison.
                if shift == 0:
                    for r in range(x):
                        ndp_less[r] += less[r]
                        ndp_equal[r] += equal[r]
                        ndp_greater[r] += greater[r]
                else:
                    cut = x - shift

                    for r in range(cut):
                        nr = r + shift
                        ndp_less[nr] += less[r]
                        ndp_equal[nr] += equal[r]
                        ndp_greater[nr] += greater[r]

                    for r in range(cut, x):
                        nr = r - cut
                        ndp_less[nr] += less[r]
                        ndp_equal[nr] += equal[r]
                        ndp_greater[nr] += greater[r]

        for r in range(x):
            ndp_less[r] %= MOD
            ndp_equal[r] %= MOD
            ndp_greater[r] %= MOD

        less = ndp_less
        equal = ndp_equal
        greater = ndp_greater

        power = (power * 10) % x

    return (less[0] + equal[0]) % MOD

def count_leq(bound, x, valid, counts):
    if bound == '-1' or bound == '0':
        return 0

    n = len(bound)

    ans = 0

    # Every positive number with fewer digits is automatically below bound.
    for length in range(1, n):
        ans += counts[length]
        if ans >= MOD:
            ans -= MOD

    ans += boundary_count(bound, x, valid)
    return ans % MOD

def solve():
    A, B, X = input().split()
    X = int(X)
    S = input().strip()

    max_len = max(len(A), len(B))

    valid = prepare_digits(S, max_len)
    counts = exact_counts(max_len, X, valid)

    A_minus_one = dec_string(A)

    right = count_leq(B, X, valid, counts)
    left = count_leq(A_minus_one, X, valid, counts)

    print((right - left) % MOD)

if __name__ == "__main__":
    solve()
```最初のヘルパーは、値を整数に変換せずに 10 進減算を実行します。 入力がマシンの整数の範囲を超える可能性があるため、これは重要です。`prepare_digits`位置に依存する桁制限を事前計算します。 位置 (1) では、ゼロが複数桁の数値の単位の桁になる可能性があるため、リストに残ります。 非ゼロ要件は、ある桁が最上位桁になる場合にのみ適用されます。`exact_counts`は無制限の長さ DP です。 その状態には剰余モジュロ (X) のみが含まれます。 (10) のような数値は (1) の位置にゼロが必要であるため、初期状態にはゼロが含まれています。 (2) の位置以降、新しく追加された先頭の数字は強制的にゼロ以外になります。 剰余は、新しい桁に適切な 10 のべき乗を加算することによって更新されます。 

境界 DP は同じ右から左の構造を使用しますが、比較関係のために 3 つの配列を追加します。 重要な実装の詳細は、新しい数字が以前に処理されたすべての数字よりも重要であるということです。 したがって、バインドされた数字より小さい数字は、以前のすべての比較状態を`less`、数字が大きいほどすべての状態が送信されます。`greater`。 古い比較が保持されるのは、等しい桁のみです。 

トランジションでは、`shift`整数を構築するのではなく。 DP は右から左に処理されるため、桁の追加は剰余配列の循環シフトにすぎません。 コードは回避します`% X`配列を分割することにより、最も内側の遷移内で`X - shift`。 最も内側のループは何百万回も実行されるため、これは重要です。 

DP 値は、加算のたびではなく、各位置の後に 1 回モジュロ (10^9+7) で減算されます。 1 つの位置中に、最大 10 個の前の値が 1 つのセルに追加されるため、一時的な Python 整数はこの最適化に十分な大きさのままです。 

ついに、`count_leq(B) - count_leq(A-1)`2 つのプレフィックス数を要求された包含範囲に変換します。 減算はモジュロ (10^9+7) で実行されるため、負の中間結果は Python の関数によって正しく処理されます。`%`オペレーター。 

## 実用的な例

 最初のサンプルでは、```
1 20 2
1234789
```使用可能な数字は (1、2、3、4、7、8、9) です。 間隔内の有効な数値は (2,4,8,12,14,18) です。 

境界 (20) の重要な状態の発展を以下に示します。 

| 右からの位置 | バインドされた数字 | 考えられる先頭の数字の動作 | 関連する剰余ゼロの数値 |
 | --- | --- | --- | --- |
 | 1 | 0 | 単位の桁には (1,2,3,4,7,8,9) を指定できますが、ゼロは使用できません。 (2,4,8) |
 | 2 | 2 | 10 の位はゼロ以外であり、(2) | と互いに素である必要があります。 (12,14,18) |
 | 最終回 | 2 | 等しい状態は許可され、より小さい状態は許可されます。 (2,4,8,12,14,18) |

 1 桁の数 (2、4、8) は (2) で割り切れます。 2 桁の場合、位置 (2) は (2) と一致する必要があり、(20) を超えない数のうち (12,14,18) を残すため、10 の位は奇数でなければなりません。 したがって、答えは(6)となります。 

2 番目のサンプルについては、```
1 20 3
0123678
```使用可能な数字は (0,1,2,3,6,7,8) です。 

| 右からの位置 | バインドされた数字 | S | からの位置有効数字 答えに貢献する数字 |
 | --- | --- | --- | --- |
 | 1 | 0 | (0,1,2,3,6,7,8) | (3,6) |
 | 2 | 2 | (1,2,7,8) | (12,18) |
 | 最終回 | 2 | (3) で割り切れる数、最大で (20) までの数 | (3,6,12,18) |

 位置 (2) では、数字 (3) と (6) は (2) と互いに素でないため拒否されます。 (3) の有効な倍数は (3,6,12,18) となり、必要な答え (4) が得られます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(LXD)) | 最大で (L\le101) 個の位置、(X\le10^5) 個の剰余、および最大で (D=10) 個の桁遷移があります。 一定数の DP パスが 2 つの境界を処理します。 |
 | スペース | (O(X)) | 各 DP は、現在と次の剰余配列のみを保持します。 正確な長さのカウントには、(O(L)) 個の追加メモリのみが必要です。 |

 (L) の境界が約 100、(X) の境界が (10^5) である場合、状態空間はおおよそ (10^7) 個の剰余位置の組み合わせによって制御されます。 この実装では長さ (X) の配列を少数だけ保持するため、メモリ使用量は 512 MB の制限を大幅に下回ります。 

## テストケース```python
import sys
import io
from math import gcd

MOD = 1_000_000_007

def dec_string(s):
    s = s.lstrip('0') or '0'
    if s == '0':
        return '-1'

    a = list(s)
    i = len(a) - 1

    while a[i] == '0':
        a[i] = '9'
        i -= 1

    a[i] = chr(ord(a[i]) - 1)
    return ''.join(a).lstrip('0') or '0'

def solve_instance(inp: str) -> str:
    data = inp.split()
    A, B, Xs, S = data
    X = int(Xs)

    max_len = max(len(A), len(B))

    digits = [ord(c) - 48 for c in S]
    valid = [[] for _ in range(max_len + 1)]

    for p in range(1, max_len + 1):
        valid[p] = [d for d in digits if gcd(d, p) == 1]

    counts = [0] * (max_len + 1)

    dp = [0] * X
    for d in valid[1]:
        dp[d % X] += 1

    counts[1] = sum(
        1 for d in valid[1]
        if d != 0 and d % X == 0
    ) % MOD

    power = 10 % X

    for p in range(2, max_len + 1):
        ndp = [0] * X

        for d in valid[p]:
            if d == 0:
                continue

            shift = (d * power) % X

            if shift == 0:
                for r in range(X):
                    ndp[r] += dp[r]
            else:
                cut = X - shift
                for r in range(cut):
                    ndp[r + shift] += dp[r]
                for r in range(cut, X):
                    ndp[r - cut] += dp[r]

        for r in range(X):
            ndp[r] %= MOD

        dp = ndp
        counts[p] = dp[0]
        power = (power * 10) % X

    def boundary(bound):
        if bound in ('0', '-1'):
            return 0

        n = len(bound)

        less = [0] * X
        equal = [0] * X
        greater = [0] * X

        bd = int(bound[-1])

        for d in valid[1]:
            r = d % X
            if d < bd:
                less[r] += 1
            elif d == bd:
                equal[r] += 1
            else:
                greater[r] += 1

        if n == 1:
            return sum(
                1 for d in valid[1]
                if d != 0 and d <= bd and d % X == 0
            ) % MOD

        power = 10 % X

        for p in range(2, n + 1):
            nl = [0] * X
            ne = [0] * X
            ng = [0] * X

            bd = int(bound[n - p])

            for d in valid[p]:
                if d == 0:
                    continue

                shift = (d * power) % X

                if d < bd:
                    for r in range(X):
                        v = less[r] + equal[r] + greater[r]
                        nr = r + shift
                        if nr >= X:
                            nr -= X
                        nl[nr] += v

                elif d > bd:
                    for r in range(X):
                        v = less[r] + equal[r] + greater[r]
                        nr = r + shift
                        if nr >= X:
                            nr -= X
                        ng[nr] += v

                else:
                    for r in range(X):
                        nr = r + shift
                        if nr >= X:
                            nr -= X
                        nl[nr] += less[r]
                        ne[nr] += equal[r]
                        ng[nr] += greater[r]

            for r in range(X):
                nl[r] %= MOD
                ne[r] %= MOD
                ng[r] %= MOD

            less, equal, greater = nl, ne, ng
            power = (power * 10) % X

        return (less[0] + equal[0]) % MOD

    def count_leq(bound):
        if bound in ('0', '-1'):
            return 0

        n = len(bound)
        ans = sum(counts[1:n]) % MOD
        ans += boundary(bound)
        return ans % MOD

    left = count_leq(dec_string(A))
    right = count_leq(B)

    return str((right - left) % MOD)

assert solve_instance(
    "1 20 2\n1234789\n"
) == "6", "sample 1"

assert solve_instance(
    "1 20 3\n0123678\n"
) == "4", "sample 2"

assert solve_instance(
    "1 1 1\n1\n"
) == "1", "single valid number"

assert solve_instance(
    "10 10 2\n01\n"
) == "1", "zero is valid at position 1"

assert solve_instance(
    "20 20 2\n02\n"
) == "0", "digit 2 is invalid at position 2"

assert solve_instance(
    "111 111 3\n1\n"
) == "1", "equal boundaries and repeated digits"

assert solve_instance(
    "2 4 1\n1234\n"
) == "3", "inclusive boundary"

assert solve_instance(
    "1 " + "1" + "0" * 100 + " 1\n1\n"
) == "100", "maximum length"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 1 / 1`|`1`| 最小サイズの範囲と厳密な等価性 |
 |`10 10 2 / 01`|`1`| (1) の位置ではゼロが許可されます。 
|`20 20 2 / 02`|`0`| 位置 (2) での Gcd 制限 |
 |`111 111 3 / 1`|`1`| 下限と上限が等しい |
 |`2 4 1 / 1234`|`3`| 包含範囲の境界 |
 |`1 10^100 1 / 1`|`100`| 小数点以下の最大長 |

 ## 特殊なケース

 のために```
10 10 2
01
```DP は両方の単位桁 (0) と (1) で始まります。 位置 (1) が許可しているため、ゼロ状態は保持されます。 位置 (2) を追加すると、桁 (1) が許可され、ゼロ以外の最上位桁になります。 結果の数値は (10) で、(2 を法とした剰余は 0) です。 答えは(1)です。 

のために```
20 20 2
02
```単位の桁 (0) は有効ですが、使用できる 10 の桁は (2) のみです。 位置 (2) には (\gcd(2,2)=1) が必要ですが、これは false であるため、10 の位 (2) を作成する遷移は DP に挿入されません。 最終的な答えは (0) です。 

のために```
1 1 1
1
```境界 DP には 1 つの位置があります。 桁 (1) はバインドされた桁と等しく、(1) を法とした余りがゼロで、非ゼロです。 等しい状態は 1 つの数値に寄与するため、答えは (1) になります。 

最大長テストの場合```
1 10000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000 1
1
```上限は (10^{100}) です。 許可される唯一の数字は (1) であるため、有効な数値は完全に (1) で構成され、(1) から (100) までの長さの文字列になります。 数値 (10^{100}) 自体にはゼロが含まれており、(S) を使用して表すことはできません。 DP は、最初の (100) 個の長さごとに 1 つの割り切れる数を記録し、(100) となります。 

## 特殊なケース

 下位のゼロと先頭のゼロの区別も、重複カウントのバグの原因です。 数値 (102) の場合、中央のゼロは位置 (2) にある実際の数字ですが、(\gcd(0,2)=2) であるため無効です。 数値 (10) の場合、ゼロは位置 (1) にあり、有効です。 DP は両方の状況に対応します。これは、位置の有効性が右からの実際の位置を使用してチェックされる一方で、非ゼロ制限は新しい最上位桁が導入された場合にのみ適用されるためです。 

範囲の減算では、可能な最小の下限も正しく処理されます。 (A=1) の場合、(A-1=0)、および`count_leq(0)`DP は正の整数のみをカウントするため、ゼロを返します。 したがって、プレフィックスの差は、(1) から (B) までの有効な整数の正確な数になり、他の場所で特別な調整を行う必要はありません。
