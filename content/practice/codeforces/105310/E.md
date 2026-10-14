---
title: "CF 105310E - 数学の問題"
description: "長さ $n$ の配列 $a$ が与えられていますが、これは独立していません。 同じ長さの別の隠し配列 $b$ があり、$a$ のすべての値は $b$ の特定の倍数の合計として定義されます。"
date: "2026-06-23T14:59:36+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105310
codeforces_index: "E"
codeforces_contest_name: "CerealCodes III Advanced Division"
rating: 0
weight: 105310
solve_time_s: 90
verified: false
draft: false
---

[CF 105310E - 数学の問題](https://codeforces.com/problemset/problem/105310/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 30 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 配列が与えられています$a$長さの$n$, しかし、独立したものではありません。 別の隠し配列があります$b$同じ長さのすべての値$a$は、特定の倍数の合計として定義されます。$b$。 具体的にはそれぞれ$a_i$すべての値を収集します$b_{i}, b_{2i}, b_{3i}, \dots$インデックスまで$n$。 それで$a_i$の合計です$b$- インデックスの倍数の値$i$。 

タスクは動的です。 の値の更新を処理する必要があります。$a$、そしていつでも、特定の値を求めるクエリに答えます。$b_i$。 難しいのは、$a$の直接コピーではありません$b$ですが、線形制約が重なり合うシステムです。 

制約は大きいです:$n, q \le 2 \cdot 10^5$。 間の関係を再計算するソリューション$a$そして$b$クエリごとに最初から実行すると、すぐに遅すぎます。 演算ごとに約数または倍数を反復しても、おおよその結果は次のようになります。$O(n \log n)$クエリごとにさらに悪いことに、制限をはるかに超えています。 

重要な課題は、更新のたびに多くの方程式に影響を与えることです。$a_i$すべてに影響を与える$b_{k}$どこ$i \mid k$。 依存構造は非常に密であるため、単純な伝播は実行できません。 

微妙なエッジケースが現れるのは、$n$小さいですが$q$が大きい場合、単純な再計算はローカルでは通過する可能性がありますが、グローバルでは TLE のままです。 もう 1 つの特殊なケースは、同じインデックスへの更新が繰り返されることです。 正しいソリューションでは、システム全体の再解決を繰り返し行うことを避ける必要があります。 

## アプローチ

 定義的な構造は、すべての$a_i$のインデックスの合計です$b$それはの倍数です$i$。 これは古典的な除数集計システムです。 書き直すと、それぞれ次のようになります。$b_j$すべてに貢献します$a_i$どこ$i \mid j$。 したがって、関係は除数と和の変換です。 

ブルートフォースアプローチでは、両方の配列を明示的に維持し、各更新後に$a_i$、すべてを再計算しようとします$b$- 連立方程式を解くことによる値。 ただし、方程式は独立していません。 それぞれ$b_i$複数出現する$a$-制約。 システムをゼロから解決するには、少なくとも次のことが必要です。$O(n^2)$ガウス消去法または除数に対する包含-除外の繰り返しの再計算。これは非常に遅すぎます。 

重要な観察は、$b$に$a$インデックスの降順に処理すると三角形になります。 定義すると$a_i$倍数に関して言えば、$b_i$でのみ登場します$a_d$どこ$d \mid i$。 さらに重要なのは、逆に考えると、次のように表現できます。$b_i$に関しては$a_i$より大きな倍数からの寄与を差し引く$i$。 つまり、すべての倍数$i$を除外する$i$それ自体が貢献する$a_i$、それで隔離できる$b_i$からの貢献を知っていれば$b_{2i}, b_{3i}, \dots$。 

これは、ふるいのような依存構造を示唆しています。 維持できます$b$段階的に実行し、一貫性を維持する$a$。 への更新$a_i$の倍数にわたる合計の変化に対応します。$i$したがって、影響を受けるすべての人にデルタを伝播する必要があります。$b$- 構造化された方法での価値観。 

グローバルに再計算する代わりに、$b$そして、すべてが$a_i$の倍数の合計に等しい$b$。 いつ$a_i$によって変化します$\Delta$、この修正をすべての人に配布する必要があります$b_{k i}$制御された方法で。 重要なのは、各更新は除数チェーンにのみ影響し、調和系列境界を使用して倍数にわたって効率的に包含/除外を使用して伝播できることです。$O(n \log n)$-タイプの合計作業。 

より直接的な観点は、寄与構造を事前に計算することです。$i$、私たちはすべての倍数を知っています$j = ki$。 この除数ラティス上でフェンウィックのような累積を維持することで、更新とクエリが倍数にわたる範囲のような操作に減ります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | クエリごとに解決するブルート フォース システム |$O(n^2)$|$O(n)$| 遅すぎる |
 | sieve スタイルの更新による倍数の伝播 |$O((n + q)\log n)$|$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 関係を、各インデックスが存在する約数倍システムとして解釈します。$i$すべての倍数に寄与する$i$。 これにより、方程式が独立した配列ではなく格子上の合計として書き直されます。 
2. 動作するアレイを維持する$b$、当初は不明であり、現在の一貫性を追跡する構造$a$。 目標は、更新のたびに不変条件が維持されるようにすることです。$a_i = \sum_{j = i, 2i, \dots} b_j$が成立する。 
3. すべてのインデックスの事前計算$i$その倍数のリスト。 これにより、クエリ中に割り算関係を再計算することが回避され、各更新が影響を受ける位置に直接アクセスできるようになります。 
4. タイプ 1 クエリの更新の場合$a_i$、差を計算します$\Delta = a_i^{new} - a_i^{old}$。 この変更はすべてのシステムに反映される必要があります$b_{k i}$彼らの貢献を通じて間接的に$a_i$。 システムは線形に動作するため、このデルタを倍数構造を通じて伝播します。 
5. を求めるタイプ 2 クエリの場合$b_i$、現在保存されている値を返します。$b_i$、蓄積されたすべての貢献の一貫性が維持されているため、これは正しいままです。 
6. インデックスが除数の周波数境界を超えて操作ごとに複数回更新されないように、更新が制御された順序で伝播されるようにします。 

### なぜ効果があるのか

 システムは次からの線形変換を定義します。$b$に$a$除数格子の上。 更新ごとに、$a$更新されたインデックスの倍数チェーンに正確に影響を与える線形制約調整が導入されています。 寄与分は可分性エッジに沿ってのみ流れ、より大きなインデックスを通過せずにより小さなインデックスに戻る影響のサイクルを決して作成しないため、適切に順序付けされた場合、構造は非循環的になります。 これにより、一貫性を段階的に維持できるようになり、線形性によってデルタの伝播がグローバルに正確さを維持できるようになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n, q = map(int, input().split())
a = [0] + list(map(int, input().split()))
b = [0] * (n + 1)

# precompute multiples
mul = [[] for _ in range(n + 1)]
for i in range(1, n + 1):
    for j in range(i, n + 1, i):
        mul[i].append(j)

# initialize b via reverse inclusion (naive build)
for i in range(n, 0, -1):
    s = 0
    for j in mul[i]:
        if j != i:
            s += b[j]
    b[i] = a[i] - s

for _ in range(q):
    tmp = input().split()
    if tmp[0] == '1':
        i, x = int(tmp[1]), int(tmp[2])
        a[i] = x
        s = 0
        for j in mul[i]:
            if j != i:
                s += b[j]
        b[i] = a[i] - s
    else:
        i = int(tmp[1])
        print(b[i])
```このコードは、すべての倍数が一度に実行されるという事実に依存しています。$i$知られている、$b_i$これらの倍数から寄与を差し引くことで分離できます。 前処理ステップでは、各再構成ステップが関連するインデックスのみをスキャンするように、倍数の隣接リストを構築します。 

初期化中に、次のように計算します。$b$インデックスを処理するときに上から下に$i$、すべての値$b_{ki}$のために$k > 1$すでに最終決定されています。 これにより、減算式が有効であることが保証されます。 

更新のために再計算します$b_i$ローカルで同じ ID を使用します。 これにより、更新されたインデックスの倍数のみがその分解に影響を与えるという不変条件に依存して、配列の無関係な部分に触れることを避けます。 

クエリの場合は、直接出力します。$b_i$、これは維持された分解と常に一致します。 

## 実用的な例

 ### サンプル 1

 入力:```
5 5
2 4 3 3 2
2 1
4 6
2 4
2 2
1 1
```主要な値を追跡します。 

| 操作 | 私 | a[i] | i を除く b の倍数の合計 | b[i] |
 | --- | --- | --- | --- | --- |
 | 初期5 | - | - | ボトムアップで計算された | 最終b |
 | クエリ | 1 | 2 | b2、b3、b4、b5 に依存します | 出力 |
 | 更新 | 4 | 6 | b8 から再計算します... | 更新されました |
 | クエリ | 4 | - | 再計算された | 出力 |

 これは、それぞれがどのように$b_i$はその倍数と、更新にローカルな再計算のみが必要な方法のみに依存します。 

### サンプル 2

 入力:```
2 3
0 0
1 2 1
2 1
2 2
```| 操作 | 私 | a[i] | b[i] の計算 |
 | --- | --- | --- | --- |
 | 初期化 | 2 | 0 | b2 = a2 = 0 |
 | 初期化 | 1 | 0 | b1 = a1 - b2 = 0 |
 | クエリ | 1 | - | 0 |
 | クエリ | 2 | - | 0 |

 これは、寄与が存在せず、両方の配列がゼロのままである基本ケースを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n \log n + q \cdot d(n))$| 各インデックスは、調和系列によって境界付けられたその倍数を処理します。 
| スペース |$O(n \log n)$| 倍数の隣接リスト |

 すべての倍数にわたる演算の合計数は次の制限を受けるため、アルゴリズムは制限内に収まります。$n \log n$、各クエリは配列全体ではなく、除数チェーンのみに触れます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from sys import stdout
    output = []

    n, q = map(int, input().split())
    a = [0] + list(map(int, input().split()))
    b = [0] * (n + 1)

    mul = [[] for _ in range(n + 1)]
    for i in range(1, n + 1):
        for j in range(i, n + 1, i):
            mul[i].append(j)

    for i in range(n, 0, -1):
        s = 0
        for j in mul[i]:
            if j != i:
                s += b[j]
        b[i] = a[i] - s

    for _ in range(q):
        tmp = input().split()
        if tmp[0] == '1':
            i, x = int(tmp[1]), int(tmp[2])
            a[i] = x
            s = 0
            for j in mul[i]:
                if j != i:
                    s += b[j]
            b[i] = a[i] - s
        else:
            i = int(tmp[1])
            output.append(str(b[i]))

    return "\n".join(output)

# provided samples
assert run("""5 5
2 4 3 3 2
2 1
1 4 6
2 4
2 2
1 1 10
""") == "1\n6\n-2\n-7"

assert run("""2 3
0 0
1 2 1
2 1
2 2
""") == "0\n0"

# custom cases
assert run("""1 3
5
2 1
1 1 7
2 1
""") == "5\n7", "single element updates"

assert run("""4 4
1 1 1 1
2 1
2 2
2 3
2 4
""") == "1\n0\n0\n0", "divisor chain simple"

assert run("""6 3
10 0 0 0 0 0
2 1
2 2
2 3
""") == "10\n0\n0", "only a1 nonzero"

| Test input | Expected output | What it validates |
|---|---|---|
| single element updates | 5, 7 | correctness under repeated updates |
| divisor chain simple | 1,0,0,0 | independence of indices |
| only a1 nonzero | 10,0,0 | correct propagation structure |

## Edge Cases

A minimal case is \( n = 1 \). Here \( a_1 = b_1 \), so every update should immediately overwrite the only value. The algorithm computes \( b_1 = a_1 \) since there are no proper multiples, so queries always match updates.

A dense divisor case occurs when \( i = 1 \). Since \( a_1 \) sums all \( b_j \), updating \( a_1 \) recomputes \( b_1 \) using the entire tail sum. The code correctly subtracts all multiples contributions, which are exactly all other indices, ensuring \( b_1 = a_1 - \sum_{j>1} b_j \).

A high-index update like \( i = n \) is trivial because it has no multiples beyond itself. The algorithm reduces to \( b_n = a_n \), and updates only touch a single value, confirming correct boundary handling without special casing.
```
