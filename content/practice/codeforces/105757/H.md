---
title: "CF 105757H - クライン・モレッティのなぞなぞ"
description: "長さ n の配列と固定サブシーケンス サイズ k が与えられます。 すべてのクエリ値 x について、x と等しいビット単位の OR を持つ正確に k 個の要素を含むサブシーケンスがいくつあるかを数えなければなりません。"
date: "2026-06-25T23:22:56+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105757
codeforces_index: "H"
codeforces_contest_name: "Insomnia 2025"
rating: 0
weight: 105757
solve_time_s: 50
verified: true
draft: false
---

[CF 105757H - クライン・モレッティの謎](https://codeforces.com/problemset/problem/105757/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 50 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 長さの配列が与えられます`n`固定サブシーケンス サイズ`k`。 すべてのクエリ値について`x`を正確に含むサブシーケンスがいくつあるかを数えなければなりません。`k`要素のビット単位の OR が次と等しい`x`。 

ここでのサブシーケンスは選択した位置によって決定されるため、同じ値が複数回出現する場合は、異なる出現が個別にカウントされます。 

制約が主な課題です。 両方`n`クエリの数は 100 万に達する可能性があり、すべての配列値とすべてのクエリ値は最大 100 万です。 以来`10^6 < 2^20`、すべての値は 20 ビット内に収まります。 各クエリを個別に処理するソリューションは直ちに除外されます。 たとえ`O(q log n)`アプローチが高価すぎる場合`q = 10^6`。 唯一の現実的な方向は、考えられるすべての 20 ビット マスクに対する回答を 1 回前処理し、その後一定時間で各クエリに回答することです。 

よくある間違いは、サブシーケンスの OR 値について直接考えることです。 可能なサブシーケンスの数は膨大です。 

考慮する：```
n = 3, k = 2
a = [1, 2, 4]
```サブシーケンスは次のとおりです。```
{1,2} -> 3
{1,4} -> 5
{2,4} -> 6
```OR 値を明示的に生成しようとすると、明らかにスケールが合わなくなります。 

複数の値が同一である場合には、別の微妙なケースが発生します。```
n = 4, k = 2
a = [1,1,1,1]
```OR は常に`1`、しかし答えはそうではありません`1`。 それは次のとおりです。```
C(4,2) = 6
```個別の値セットではなく、位置の選択をカウントします。 

3 番目のエッジ ケースは、選択した要素に決して出現しないビットがクエリ マスクに含まれている場合に発生します。```
n = 3, k = 2
a = [1,2,2]
query = 7
```サブシーケンスはビットを生成できません`4`、したがって、答えは次のとおりです`0`。 実際の頻度を考慮せずにクエリ マスクのサブセットのみを追跡するアプローチでは、誤ってゼロ以外の結果が生成される可能性があります。 

## アプローチ

 ブルートフォースのアイデアは単純です。 サイズのすべての部分列を列挙します`k`、その OR を計算し、各 OR 値が出現する回数を数えます。 

すべての有効なサブシーケンスが 1 回だけ検査されるため、これは正しいです。 

問題は部分列の数です。```
C(n,k)
```中程度の値であっても、`n`、これは天文学的な規模になります。 と`n`最大 100 万人までは、ブルートフォースは完全に不可能です。 

重要な観察は、質問を逆にすると得られます。 

尋ねる代わりに:```
How many subsequences have OR exactly x?
```聞く：```
How many subsequences have OR contained inside x?
```サブシーケンスの中に OR が含まれています`x`選択されたすべての要素がすでに存在するビットのみを使用する場合`x`。 

マスク用`x`、 させて：```
g[x] = number of array elements whose bit set is a subset of x
```すべてのサイズ-`k`この中から選んだ部分列`g[x]`要素には最大でも OR が含まれます`x`。 

したがって、次のようになります。```
F[x] = C(g[x], k)
```OR が次のサブセットであるサブシーケンスをカウントします。`x`。 

それでは、次のようにしましょう。```
ans[x] = number of subsequences whose OR equals x
```すべてのサブシーケンスがカウントされます`F[x]`正確に 1 つの OR 値に寄与します`y`と`y ⊆ x`。 

それで：```
F[x] = Σ ans[y]
       y⊆x
```これは古典的なサブセットのゼータ変換関係です。 

もう一度`F[x]`値がわかっている場合は、サブセット メビウス反転を使用して正確な答えを取得します。 

残りのタスクはコンピューティングです`g[x]`。 

させて`freq[v]`値の周波数になります`v`配列内で。 

それから：```
g[x] = Σ freq[s]
       s⊆x
```これはまさにサブセット合計 SOS DP です。 

しかないので、`2^20 = 1,048,576`マスクを使用すると、20 次元の SOS 変換が可能になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(C(n,k) · k) | O(OR 値の数) | 遅すぎる |
 | 最適 | O(20 · 2^20 + n + q) | O(2^20) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 配列を読み取り、周波数配列を構築します`freq`、 どこ`freq[v]`何倍の値を保存するか`v`が表示されます。 
2. 階乗および逆階乗モジュロを事前計算する`10^9+7`まで`n`、組み合わせの定数時間計算が可能になります。 
3. コピー`freq`配列に入れる`g`。 
4. サブセット SOS 変換を実行します。`g`。 

この変換後:```
g[x] = Σ freq[s]
       s⊆x
```これは、マスクがそのサブセットである配列要素の数に等しくなります。`x`。 
5. マスクごとに`x`、次を計算します。```
F[x] = C(g[x], k)
```もし`g[x] < k`、値はゼロです。 
6. サブセットメビウス反転を適用します`F`。 

反転後:```
F[x] = Σ ans[y]
       y⊆x
```になる```
ans[x]
```直接。 
7. 各クエリ値について`x`、出力`ans[x]`。 

### なぜ効果があるのか

 固定マスクの場合`x`、OR が内部に含まれるサブシーケンスに参加できる唯一の要素`x`独自のマスクがそのサブセットである要素です。`x`。 SOS 変換は、そのような要素がいくつ存在するかを正確に計算します。 

どれかを選択すると`k`これらの要素のサブシーケンスは、その OR が次のサブセットでもあります。`x`、 それで`F[x] = C(g[x],k)`最大でも OR を含むすべてのサブシーケンスをカウントします`x`。 

すべてのサブシーケンスは、正確に 1 つの OR 値に寄与します。 その結果、`F[x]`に含まれるすべてのマスクの正確な OR カウントの合計です。`x`。 Subset Möbius inversion is precisely the inverse operation of this summation, so it recovers the exact number of subsequences whose OR equals each mask.

 ## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

MOD = 1000000007
B = 20
M = 1 << B

def main():
    n, k = map(int, input().split())

    freq = array('I', [0]) * M
    arr = list(map(int, input().split()))
    for x in arr:
        freq[x] += 1

    fact = [1] * (n + 1)
    for i in range(1, n + 1):
        fact[i] = fact[i - 1] * i % MOD

    ifact = [1] * (n + 1)
    ifact[n] = pow(fact[n], MOD - 2, MOD)
    for i in range(n, 0, -1):
        ifact[i - 1] = ifact[i] * i % MOD

    def comb(nn, rr):
        if nn < rr:
            return 0
        return fact[nn] * ifact[rr] % MOD * ifact[nn - rr] % MOD

    g = array('I', freq)

    for bit in range(B):
        step = 1 << bit
        for mask in range(M):
            if mask & step:
                g[mask] += g[mask ^ step]

    dp = [0] * M
    for mask in range(M):
        dp[mask] = comb(g[mask], k)

    for bit in range(B):
        step = 1 << bit
        for mask in range(M):
            if mask & step:
                dp[mask] -= dp[mask ^ step]
                dp[mask] %= MOD

    q = int(input())
    out = []
    for _ in range(q):
        x = int(input())
        out.append(str(dp[x]))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    main()
```頻度配列には、各値が入力に出現する回数が格納されます。 すべての値が以下であるため、`2^20`、考えられるすべてのマスクには専用のスロットがあります。 

SOS 変換は、周波数をサブセット数に変換します。 それが終わった後、`g[x]`OR が超えないサブシーケンスに出現できる配列要素の数が含まれます。`x`。 

組み合わせの値は階乗および逆階乗を使用して計算されます。 係数は素数であるため、フェルマーの定理は逆モジュールを提供します。 

配列`dp`最初に格納する`F[x] = C(g[x],k)`。 2 番目の SOS スタイルのパスは、メビウス反転を実行します。 減算ステップはサブセット累積の正確な逆であり、「OR が x の中に含まれている」カウントを「OR が x に等しい」カウントに変換します。 

中間値が負になる可能性があるため、各減算の後にモジュロ演算が必要です。 

## 実用的な例

 ### 例 1```
n = 3, k = 2
a = [1, 2, 3]
```関連するマスク:

 | マスク | g[マスク] | F[マスク] = C(g,k) |
 | --- | --- | --- |
 | 0 | 0 | 0 |
 | 1 | 1 | 0 |
 | 2 | 1 | 0 |
 | 3 | 3 | 3 |

 メビウス反転後:

 | マスク | 正確な OR 数 |
 | --- | --- |
 | 1 | 0 |
 | 2 | 0 |
 | 3 | 3 |

 3 つのサブシーケンスは次のとおりです。```
{1,2} -> 3
{1,3} -> 3
{2,3} -> 3
```全員がマスクに貢献`3`。 

### 例 2```
n = 4, k = 2
a = [1,1,1,1]
```| マスク | g[マスク] | F[マスク] |
 | --- | --- | --- |
 | 0 | 0 | 0 |
 | 1 | 4 | 6 |

 反転後:

 | マスク | 正確な OR 数 |
 | --- | --- |
 | 0 | 0 |
 | 1 | 6 |

 この例は、サブシーケンスが位置によってカウントされることを示しています。 明確な値が 1 つしかない場合でも、2 つの位置を選択するには 6 つの異なる方法があります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(20 · 2^20 + n + q) | 2 つの SOS スタイルの変換が支配的 |
 | スペース | O(2^20) | すべてのマスクにわたる周波数と DP 配列 |`2^20`は約 100 万回であるため、各 SOS 変換は約 2,000 万回の更新を実行します。 これは、2 秒の C++ ソリューションの制限内に問題なく収まっており、問題の意図された複雑さでもあります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    MOD = 1000000007
    B = 20
    M = 1 << B

    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline

    n, k = map(int, input().split())
    arr = list(map(int, input().split()))

    from array import array

    freq = array('I', [0]) * M
    for x in arr:
        freq[x] += 1

    fact = [1] * (n + 1)
    for i in range(1, n + 1):
        fact[i] = fact[i - 1] * i % MOD

    ifact = [1] * (n + 1)
    ifact[n] = pow(fact[n], MOD - 2, MOD)
    for i in range(n, 0, -1):
        ifact[i - 1] = ifact[i] * i % MOD

    def C(nn, rr):
        if nn < rr:
            return 0
        return fact[nn] * ifact[rr] % MOD * ifact[nn - rr] % MOD

    g = array('I', freq)

    for bit in range(B):
        b = 1 << bit
        for mask in range(M):
            if mask & b:
                g[mask] += g[mask ^ b]

    dp = [0] * M
    for mask in range(M):
        dp[mask] = C(g[mask], k)

    for bit in range(B):
        b = 1 << bit
        for mask in range(M):
            if mask & b:
                dp[mask] = (dp[mask] - dp[mask ^ b]) % MOD

    q = int(input())
    ans = []
    for _ in range(q):
        ans.append(str(dp[int(input())]))
    return "\n".join(ans)

# custom cases
assert run("1 1\n1\n2\n1\n2\n") == "1\n0"
assert run("4 2\n1 1 1 1\n2\n1\n3\n") == "6\n0"
assert run("3 2\n1 2 4\n4\n3\n5\n6\n7\n") == "1\n1\n1\n0"
assert run("3 3\n7 7 7\n2\n7\n1\n") == "1\n0"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一要素配列 |`1, 0`| 最小サイズのケース |
 | すべての値は等しい |`6, 0`| 正しい組み合わせカウント |
 | 2 の累乗 |`1,1,1,0`| 正確な OR 再構成 |
 |`k = n`|`1,0`| 部分列が 1 つだけ存在する境界 |

 ## 特殊なケース

 次のことを考慮してください。```
n = 4
k = 2
a = [1,1,1,1]
query = 1
```SOS 変換は次のようになります。```
g[1] = 4
```それで：```
F[1] = C(4,2) = 6
```メビウス反転の葉`ans[1] = 6`。 このアルゴリズムは、個別の値ではなく位置の選択肢をカウントします。これはまさに問題が必要とするものです。 

考慮する：```
n = 3
k = 2
a = [1,2,2]
query = 7
```配列値にビットが含まれていません`4`したがって、すべてのサブシーケンス OR が内部に含まれます。`3`。 

SOS ステージは、既存のマスクからのみ有効なカウントを計算します。 反転時、マスク`7`寄付を受け取らず、次のものを残します。```
ans[7] = 0
```それは正しいです。 

考慮する：```
n = 3
k = 3
a = [1,2,4]
query = 7
```すべての位置を使用するサブシーケンスは 1 つだけ存在します。 

アルゴリズムは以下を取得します。```
g[7] = 3
F[7] = C(3,3) = 1
```そして反転すると次が生成されます。```
ans[7] = 1
```一方、他のマスクはすべてゼロを受け取ります。 これにより、極端なケースが正しく処理されることが確認されます。`k = n`。
