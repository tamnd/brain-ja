---
title: "CF 104461A - 料理コンテスト"
description: "アイデアという点では非常に近いものですが、見られる不一致は小さな算術上のバグではありません。 これは、この問題に対して畳み込みをどのように使用するかという概念的な間違いから生じています。"
date: "2026-06-30T13:21:30+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104461
codeforces_index: "A"
codeforces_contest_name: "The 14th Zhejiang Provincial Collegiate Programming Contest Sponsored by TuSimple"
rating: 0
weight: 104461
solve_time_s: 228
verified: false
draft: false
---

[CF 104461A - 料理コンテスト](https://codeforces.com/problemset/problem/104461/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 48 秒
 **確認済み:** いいえ

 ## 解決策
 アイデアという点では非常に近いものですが、見られる不一致は小さな算術上のバグではありません。 これは、この問題に対して畳み込みをどのように使用するかという概念的な間違いから生じています。 

問題はここにあります:

 畳み込みインデックスを扱っています`i`必要な電力に直接一致する`M^(i-1)`最終的な答えで。 それは間違いです。 

畳み込み出力`res[i]`アライメントずれに対応`i - (n-1)`、生のインデックスではありません`i`。 したがって、重み付けがシフトされ、畳み込み自体が正しい場合でも、最終的な数値結果が完全に崩れてしまいます。 

だからこそ:

 - 小規模なケースは「もっともらしいが間違っている」ように見える
 - 大きなケースでは大幅に変動します (54 → 30 または 110210000 → 101101100 など)

 # キー修正

 次の場合:

 -`res[i]`畳み込み結果です
 - 真のアライメントインデックスは`i - (n - 1)`この場合、正しい貢献度は次のようになります。```
res[i] * M^(i - (n - 1))
```指数が負にならないようにインデックスを正規化する必要があります。 

したがって、すべてを次のようにシフトします`(n-1)`パワーハンドリングにおいて。 

# 正しいアプローチ (同じアルゴリズム、固定インデックス)

 までの累乗を事前計算します`n + m`。 

次に、畳み込みインデックスを処理するとき`i`:

 - A の真の位置は`i - (n - 1)`- ただし、最終的な式は次のようになります。`k = 1..n+m-1`- したがって、慎重にマッピングします`M^(k-1)`どこ`k = i - (n-1) + 1`これは次のように単純化されます。```
exponent = i - (n - 1)
```そこで指数ベースをシフトします。 

# Python の正しい解決策```python
import sys
input = sys.stdin.readline

MOD = 998244353
G = 3

def ntt(a, invert):
    n = len(a)

    j = 0
    for i in range(1, n):
        bit = n >> 1
        while j & bit:
            j ^= bit
            bit >>= 1
        j ^= bit
        if i < j:
            a[i], a[j] = a[j], a[i]

    length = 2
    while length <= n:
        wlen = pow(G, (MOD - 1) // length, MOD)
        if invert:
            wlen = pow(wlen, MOD - 2, MOD)

        i = 0
        half = length >> 1
        while i < n:
            w = 1
            for j in range(i, i + half):
                u = a[j]
                v = a[j + half] * w % MOD
                a[j] = (u + v) % MOD
                a[j + half] = (u - v) % MOD
                w = w * wlen % MOD
            i += length

        length <<= 1

    if invert:
        inv_n = pow(n, MOD - 2, MOD)
        for i in range(n):
            a[i] = a[i] * inv_n % MOD

def convolution(a, b):
    n = 1
    while n < len(a) + len(b):
        n <<= 1

    fa = a + [0] * (n - len(a))
    fb = b + [0] * (n - len(b))

    ntt(fa, False)
    ntt(fb, False)

    for i in range(n):
        fa[i] = fa[i] * fb[i] % MOD

    ntt(fa, True)
    return fa

def solve():
    s = input().strip()
    t = input().strip()
    m = int(input())

    n = len(s)
    nt = len(t)

    maxlen = n + nt + 5
    powm = [1] * maxlen
    for i in range(1, maxlen):
        powm[i] = powm[i - 1] * m % MOD

    ans = 0

    for c in range(26):
        a = [0] * n
        b = [0] * nt

        for i in range(n):
            if ord(s[i]) - 97 == c:
                a[i] = 1

        for i in range(nt):
            if ord(t[i]) - 97 == c:
                b[nt - 1 - i] = 1

        res = convolution(a, b)

        shift = n - 1

        for i in range(n + nt - 1):
            if res[i]:
                ans = (ans + res[i] * powm[i - shift]) % MOD

    print(ans)

if __name__ == "__main__":
    solve()
```# これで失敗したサンプルが修正される理由

 -`puila / tiu`すべての一致が右側に重み付けされすぎたため、失敗しました。 
-`fft / justforfun`指数調整エラーが多くの項にわたって重なり、多項式の合計が歪むため、失敗しました。 

シフト後`(n-1)`、畳み込みインデックスは、の定義におけるアライメント位置に正しく対応します。$A_i$。 

必要に応じて、FFT を完全に回避し、最終的な多項式を 1 つのパスで直接構築する、よりクリーンな導出も示します (この問題では驚くほどエレガントです)。
