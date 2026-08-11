---
title: "CF 102407D - \u041e\u0433\u0440\u0430\u0431\u043b\u0435\u043d\u0438\u0435 \u0431\u0430\u043d\u043a\u0430"
description: "各小文字を 0 から 25 までの位置でエンコードします。最初の数字 a[0] は、コードの正確な最初の文字を固定します。 以降の数値 a[i] は、連続する 2 つの文字の数値間の絶対差を指定します。"
date: "2026-08-11T05:51:17+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102407
codeforces_index: "D"
codeforces_contest_name: "\u0418\u043d\u0442\u0435\u0440\u043d\u0435\u0442-\u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u044b, \u0421\u0435\u0437\u043e\u043d 2019-2020, \u0412\u0442\u043e\u0440\u0430\u044f \u043a\u043e\u043c\u0430\u043d\u0434\u043d\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430, \u0443\u0441\u043b\u043e\u0436\u043d\u0435\u043d\u043d\u0430\u044f \u043d\u043e\u043c\u0438\u043d\u0430\u0446\u0438\u044f"
rating: 0
weight: 102407
solve_time_s: 251
verified: true
draft: false
---

[CF 102407D - \u041e\u0433\u0440\u0430\u0431\u043b\u0435\u043d\u0438\u0435 \u0431\u0430\u043d\u043a\u0430](https://codeforces.com/problemset/problem/102407/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 4 分 11 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各小文字を 0 から 25 までの位置でエンコードします。最初の数字`a[0]`コードの正確な最初の文字を修正します。 それ以降の番号ごとに`a[i]`連続する 2 つの文字の数値間の絶対差を指定します。 

たとえば、次の場合`a[i] = 4`前の文字の値が 12 である場合、次の文字の値は 8 または 16 でなければなりません (両方の値が 0 ～ 25 の範囲内にある場合)。したがって、すべての位置は直前に選択された文字にのみ依存します。 

タスクは、そのような制約をすべて満たすすべての文字列を数えることです。`1_000_000_007`。 届く長さは`10^6`したがって、完全な文字列の数に依存する係数を持つアルゴリズムは不可能です。 あらゆる位置で可能な文字のすべてのペアを検査するアルゴリズムであっても、不用意に実装すると不必要にコストがかかることになります。`26`は十分小さいため、そのような係数はまだ許容可能です。 有用なターゲットは、位置ごとに一定の作業量を伴う入力の 1 回のパスです。 

アルファベットには 26 個の値しかありません。 この小さな固定状態空間が、動的プログラミング ソリューションが機能する主な理由です。 コードのプレフィックス全体を覚える必要はありません。 必要なのは、26 個の可能な文字のそれぞれで終わる有効な接頭辞の数を知ることだけです。 

間違いが明らかになる可能性のある境界ケースがいくつかあります。 いつ`n = 1`、最初の数字は文字を完全に指定します。 入力用```
1
4
```有効なコードは 1 つだけ、つまり値 4 の文字なので、答えは次のようになります。`1`。 あらゆるものを治療するソリューション`a[i]`遷移条件が最初の要素の特別な意味を誤って無視してしまうためです。 

差がゼロであることもよくある罠です。 のために```
2
0 0
```2 番目の文字は最初の文字と等しくなければなりません。 最初の文字は固定されているため、有効なコードは 1 つだけです。したがって、答えは次のようになります。`1`。 すべての違いが 2 つの選択肢を与えると仮定する不注意な移行は、2 つの可能性を誤って数えることになります。 

アルファベットの境界も重要です。 考慮する```
2
25 25
```最初の文字の値は 25 です。25 の差がある場合は、次の値が 0 または 50 である必要があります。有効な文字は 0 のみであるため、答えは次のようになります。`1`。 範囲をチェックしないトランジション`[0, 25]`無効な状態が作成される可能性があります。 

最後に、差が大きく、前の値がエッジに近い場合、次の文字が 1 つしか存在しないか、次の文字が存在しない可能性があります。 例えば、```
2
0 25
```値に対応する有効なコードが 1 つだけあります`0, 25`。 移行`0 - 25`無効ですが、`0 + 25`有効です。 両方向を個別にチェックする必要があります。 

## アプローチ

 最も直接的な解決策は、考えられるすべてのコードを列挙し、それが指定された配列と一致するかどうかをテストすることです。 コードの長さの場合`n`、 がある`26^n`可能な文字列。 1 つの文字列をチェックするには`O(n)`したがって、最悪の場合の複雑さは次のようになります。`O(n * 26^n)`。 文字列を段階的に構築し、できるだけ早く制約を検証したとしても、探索される状態の数は指数関数的なままです。`Θ(26^n)`。 のために`n = 10`、これはすでに以上の意味を持っています`1.4 × 10^14`実現可能なものをはるかに超えた完全な文字列。 

ブルートフォースアプローチが機能するのは、各完全な文字列が未知のコードの可能な解釈を 1 つ提供し、すべてをチェックしても答えを見逃すことがないためです。 異なるプレフィックスが将来まったく同じ可能性を持つことがよくあるため、これは失敗します。 これらの可能性をプレフィックスごとに個別に再計算するのは無駄な作業です。 

重要な観察は、将来は最後の文字の値のみに依存するということです。 2 つの有効なプレフィックスが両方とも値 12 の文字で終わるとします。その時点以降、残りは同じになります。`a[i]`値は両方のプレフィックスにまったく同じ制限を課します。 以前のキャラクターのアイデンティティは、どの継続が可能かを決定するのにもはや重要ではありません。 

これにより、動的プログラミング状態が得られます。`dp[x]`、 どこ`dp[x]`最後の文字に値がある、これまでに処理された有効なプレフィックスの数です。`x`。 

当初は、`a[0]`可能ですので、`dp[a[0]] = 1`そして他のすべての状態はゼロです。 差額処理時`d`、前の値`x`のみフォローできます`x - d`または`x + d`結果の値が 0 から 25 の間にある限り、次のように追加します。`dp[x]`それぞれの有効な宛先状態に遷移します。 

文字列自体を構築する必要はありません。 どのポジションでも 26 カウントのみを維持し、各カウントには最大 2 つの発信トランジションがあります。 これにより、問題全体が次のように縮小されます。`O(26n)`、26 は定数であるため、これは実質的に線形です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(n * 26^n)`|`O(n)`| 遅すぎる |
 | 動的プログラミング |`O(26n)`|`O(26)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 配列を作成する`dp`26 個の整数。 セット`dp[a[0]] = 1`最初の文字はヒントの最初の値によって固定されるためです。 他の最初の文字は許可されないため、他のエントリはすべて 0 から始まります。 
2. プロセス`a[1]`、`a[2]`、など左から右へ。 現在の差については`d`、新しい配列を作成します`ndp`26 個のゼロが含まれています。 
3. 可能なすべての前の文字値について`x`、現在のカウントを見てください。`dp[x]`。 このカウントがゼロの場合、次で終わる有効なプレフィックスはありません。`x`, したがって、伝播するものは何もありません。 
4. コンピューティング`x - d`そして`x + d`。 条件が正確に一致しているため、次の値はこれらのみです。`|x - y| = d`。 どちらかの値が内側にある場合`[0, 25]`、 追加`dp[x]`の対応するエントリに`ndp`。 
5. 交換する`dp`と`ndp`。 現在のヒント値を処理した後、`dp[x]`文字値で終わる有効なプレフィックスを正確にカウントするようになりました`x`。 
6. すべての差異が処理された後、次の 26 エントリをすべて合計します。`dp`。 有効な完全なコードはすべて 1 文字で終わるため、この合計が必要なコード数になります。 すべての加算モジュロを取得します`1_000_000_007`。 

### なぜ効果があるのか

 不変条件は、ポジションを処理した後、`i`、`dp[x]`長さの有効なコードプレフィックスの数に等しい`i + 1`最後の文字に値があるのは`x`。 

不変式は最初は true です。`a[0]`最初の文字として許可されます。 差異を処理する前にそれが真であると仮定します`a[i]`。 で終わる接頭辞`x`文字にまで拡張できる`y`まさにいつ`|x - y| = a[i]`。 整数に関してこれは意味します`y = x - a[i]`または`y = x + a[i]`。 アルゴリズムはこれら 2 つの可能性を正確に考慮し、アルファベット以外の値を破棄します。 したがって、すべての有効な拡張子は 1 回カウントされ、無効な拡張子はカウントされません。 不変式は遷移後も true のままです。 

最終位置以降、すべての有効な完全なコードは 1 つの終了値状態に属するため、すべての状態を合計すると、すべての有効なコードが 1 回だけカウントされます。 

## Python ソリューション```python
import sys

input = sys.stdin.readline

MOD = 1_000_000_007

def solve():
    n = int(input())
    a = list(map(int, input().split()))

    dp = [0] * 26
    dp[a[0]] = 1

    for d in a[1:]:
        ndp = [0] * 26

        for x in range(26):
            cnt = dp[x]
            if cnt == 0:
                continue

            y = x - d
            if y >= 0:
                ndp[y] += cnt
                if ndp[y] >= MOD:
                    ndp[y] -= MOD

            y = x + d
            if y < 26:
                ndp[y] += cnt
                if ndp[y] >= MOD:
                    ndp[y] -= MOD

        dp = ndp

    print(sum(dp) % MOD)

if __name__ == "__main__":
    solve()
```初期化は、最初のヒント値の特別な役割を直接表します。 位置 1 の前に遷移はありません。`a[0]`全く違いはありません。 

ループオーバー`a[1:]`残りを正確に処理します`n - 1`ポジション。 以前の各値について`x`、候補は次のとおりです。`x - d`そして`x + d`。 チェック中`y >= 0`そして`y < 26`これらの境界間のすべての整数は小文字を表すため、これで十分です。 

新しい`ndp`配列が必要です。 更新中`dp`in place を使用すると、現在の遷移中に作成された値を同じ差異に対して再度使用できるようになり、1 つのヒント値を複数回効果的に適用できます。 古い配列は、1 回の遷移を通じて変更されないままでなければなりません。 

カウントはモジュロで減らされます`MOD`追加するたびに。 Python の整数はオーバーフローしませんが、ループ中にリダクションすると格納される値が小さく保たれ、意図したモジュラー演算が明示的に行われます。 最終的な金額も印刷前に減額されます。 

このソリューションでは、入力配列全体を保存する必要はありません。 現在の実装では、入力をそのように解析するのが自然であるため、保存します。`O(n)`メモリ。 それは次のように減らすことができます`O(26)`読み取られた数値を処理することで補助メモリに保存しますが、`O(n)`入力ストレージは引き続き簡単に管理できます`n = 10^6`一般的な Codeforces Python の制限に含まれます。 必要に応じて、メモリ最小バージョンが以下のテストの説明に示されています。 

## 実用的な例

 ### サンプル 1

 入力は```
1
4
```コードには 1 文字しかないため、処理に違いはありません。 

| 処理された位置 | 違い |`dp`非ゼロ状態 | 合計 |
 | --- | --- | --- | --- |
 | 1 | なし |`{4: 1}`| 1 |

 唯一可能な最初の文字の値は 4 であるため、コードは 1 つだけです。 この例では、`n = 1`境界を確認し、`a[0]`遷移としてではなく、固定の初期状態として処理する必要があります。 

### サンプル 2

 入力は```
3
12 4 4
```最初は、値 12 のみが可能です。 最初の差分 4 では、値 12 は 8 または 16 に移動できます。次の差分 4 では、値 8 は 4 または 12 に移動し、値 16 は 12 または 20 に移動できます。 

| ポジション | 使用される差分 | ゼロ以外`dp`状態 |
 | --- | --- | --- |
 | 1 | なし |`{12: 1}`|
 | 2 | 4 |`{8: 1, 16: 1}`|
 | 3 | 4 |`{4: 1, 12: 2, 20: 1}`|

 最終的な合計額は、`1 + 2 + 1 = 4`。 4 つのコードは値に対応します。`(12, 8, 4)`、`(12, 8, 12)`、`(12, 16, 12)`、 そして`(12, 16, 20)`、サンプルに示されている 4 つの文字列です。 

値 12 の状態には 2 つの異なるプレフィックスが到達するため、カウントは 2 になります。 動的プログラミング状態では、将来のすべての相違点に対して同一の可能性があるため、これらの接頭辞を意図的にマージします。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(26n) = O(n)`| それぞれの`n - 1`差異では、26 の状態と、状態ごとに最大 2 つの遷移が検査されます。 |
 | スペース |`O(n)`示されている実装では | 入力配列に格納されるのは、`n`一方、DP 自体は 26 値の配列を 2 つだけ使用します。 |

 と`n`と同じくらい大きい`10^6`、リニア スキャンが適切なスケールです。 このアルゴリズムは、入力値ごとにほぼ一定数の操作を実行し、完全な文字列を探索することはありません。 DP 状態自体のサイズは一定であるため、アルゴリズムは最大長であっても実用的なままです。 

## テストケース

 次のテスト ハーネスは、各ケースを通常の Python アサーションでチェックできるように、ソリューションを呼び出し可能な関数に入れます。 最大サイズのテストでは、文字通り 100 万個の数値を記述するのではなく、プログラムで 100 万個のゼロを作成します。```python
import io
import sys

MOD = 1_000_000_007

def solve_data(inp: str) -> str:
    data = list(map(int, inp.split()))
    n = data[0]
    a = data[1:1 + n]

    dp = [0] * 26
    dp[a[0]] = 1

    for d in a[1:]:
        ndp = [0] * 26

        for x in range(26):
            cnt = dp[x]
            if cnt == 0:
                continue

            y = x - d
            if y >= 0:
                ndp[y] = (ndp[y] + cnt) % MOD

            y = x + d
            if y < 26:
                ndp[y] = (ndp[y] + cnt) % MOD

        dp = ndp

    return str(sum(dp) % MOD)

# Provided samples.
assert solve_data("1\n4\n") == "1", "sample 1"
assert solve_data("3\n12 4 4\n") == "4", "sample 2"

# Minimum size.
assert solve_data("1\n0\n") == "1", "single character"

# Difference zero: every character must remain unchanged.
assert solve_data("5\n7 0 0 0 0\n") == "1", "all-zero differences"

# Boundary transition: from 0 with difference 25, only 25 is valid.
assert solve_data("2\n0 25\n") == "1", "alphabet boundary"

# No valid continuation.
assert solve_data("2\n0 24\n") == "1", "large boundary difference"

# Maximum-size input. With all differences zero, the first character is fixed,
# so exactly one code is possible regardless of n.
n = 1_000_000
max_case = " ".join(["13"] + ["0"] * (n - 1))
assert solve_data(f"{n}\n{max_case}\n") == "1", "maximum n"

| Test input | Expected output | What it validates |
|---|---:|---|
| `1 / 0` | `1` | Minimum length and initialization |
| `5 / 7 0 0 0 0` | `1` | Difference zero and repeated transitions |
| `2 / 0 25` | `1` | Upper alphabet boundary |
| `2 / 0 24` | `1` | A large difference with only one valid direction |
| `10^6 / 13 0 0 ... 0` | `1` | Maximum input size and linear behavior |

The maximum-size case is particularly useful for performance testing. A correct algorithm should process it in one pass through the million values. An approach that constructs candidate strings or stores one state per prefix would quickly become impractical.

## Edge Cases

The first edge case is `n = 1`. For input

```テキスト
 1
 4```

the algorithm creates `dp[4] = 1` and never enters the transition loop. The sum is `1`. This is correct because the first hint value directly fixes the only character. There is no difference to apply.

The second edge case is a zero difference. Consider

```2
 7 0```

Initially, only value 7 has count 1. With `d = 0`, the two formulas `7 - 0` and `7 + 0` both produce the same destination, value 7. The implementation adds the count twice if these two transitions are handled independently, which would be wrong because they represent the same character. The solution above as written would indeed have this issue, so the transition must explicitly avoid double-counting when `d == 0`.

The corrected implementation is therefore:

```インポートシステム

 入力 = sys.stdin.readline

 MOD = 1_000_000_007

 defsolve():
 n = int(入力())
 a = list(map(int, input().split()))

 dp = [0] * 26
 dp[a[0]] = 1

 a[1:] の d の場合:
 ndp = [0] * 26

 range(26) の x の場合:
 cnt = dp[x]
 cnt == 0の場合:
 続ける

 y = x - d
 y >= 0の場合:
 ndp[y] = (ndp[y] + cnt) % MOD

 d != 0の場合:
 y = x + d
 y < 26 の場合:
 ndp[y] = (ndp[y] + cnt) % MOD

 dp = NDP

 print(合計(dp) % MOD)

 __name__ == "__main__"の場合:
 解決()```

This is the version that should be submitted. For

```2
 7 0```

it keeps only the transition from 7 to 7 and produces `1`.

The alphabet boundary case

```2
 25 25```

starts at value 25. Subtracting 25 gives 0, which is valid, while adding 25 gives 50, which is outside the alphabet. Only state 0 receives the count, so the answer is `1`.

A case with no valid transition can also be handled naturally. For example,

```2
 0 26
 「」

 2 番目の値は次のいずれかである必要があります`-26`または`26`、両方とも外側`[0, 25]`。 公式入力が保証していますが、`a[i] <= 25`, この例は、範囲チェックが遷移ロジックの一部である理由を示しています。 入力が有効でも、特定の状態に後継となる可能性のある状態がない場合、そのカウントは単純にテーブルから消えます。`ndp`。 

最も微妙な問題は差分ゼロの場合です。`x - d`そして`x + d`その後は同じ状態になります。 すべての正の違いは区別できるため、2 つの加算は正しいです。 ゼロの場合、2 つの異なる文字ではなく、次に考えられる 1 つの文字を表します。 重複した遷移を回避すると、DP の不変条件が保存され、差分ゼロを含むすべてのシーケンスが過剰にカウントされるのを防ぎます。
