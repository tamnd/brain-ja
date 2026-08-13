---
title: "CF 102419F - xor-sum"
description: "For each test case, we need to print an array of exactly (n) integers. Every value must lie in the interval ([0,m]), the ordinary sum of all values must be (s), and their bitwise XOR must be (x). そのような配列が存在しない場合は、(-1) が出力されます。"
date: "2026-08-12T20:17:54+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102419
codeforces_index: "F"
codeforces_contest_name: "SPC 2019"
rating: 0
weight: 102419
solve_time_s: 745
verified: true
draft: false
---

[CF 102419F - xor-sum](https://codeforces.com/problemset/problem/102419/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 12 分 25 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各テスト ケースでは、正確に (n) 個の整数の配列を出力する必要があります。 すべての値は区間 ([0,m]) 内になければならず、すべての値の通常の合計は (s) であり、それらのビット単位の XOR は (x) である必要があります。 そのような配列が存在しない場合は、(-1) が出力されます。 公式の制約では、最大 (10^5) 個のテスト ケースが許可され、すべてのテスト ケースの配列要素の総数は最大 (3\cdot10^5) になります。 

(n) のサイズにより、多くの可能な配列を探索するものは即座に除外されます。 単一のテスト ケースの場合でも、すべての配列を列挙するには ((m+1)^n) 個の候補が必要になりますが、(n=10^5) の場合、これは天文学的に大きくなります。 値 (s) と (m) も、合計によってインデックス付けされた動的プログラムには大きすぎます。 有用な構造は、XOR のバイナリ表現と、配列の大部分を反復的に構築することによって得られる必要があります。 

最初の不変式は、加算と XOR の関係です。 2 つの整数の場合、

 [
 a+b=(a\oplus b)+2(a\mathbin{&}b)。 
]

 配列全体について、これは、通常の合計が常に少なくとも XOR であり、XOR と同じパリティを持つことを意味します。 したがって、(s<x) または (s\not\equiv x\pmod 2) は直ちにテスト ケースを不可能にします。 

あまり明確ではない境界ケースがいくつかあります。 (n=1) の場合、自由はまったくありません。 たとえば、(n=1,m=5,s=5,x=5) は配列 ([5]) では有効ですが、(1,5,5,0) は、唯一の要素がその合計と XOR の両方に等しくなる必要があるため不可能です。 常に 2 つの要素を確保する不注意な構築もここで失敗します。 

2 番目の境界ケースは (x>m) の場合に発生します。 値 (x) を単純に配列に入れることはできません。 たとえば、(4\oplus3=7) であるため、(n=3,m=4,s=7,x=7) は ([4,3,0]) とともに有効です。 (x) を 1 つの要素として使用することを主張する構造は、それを誤って拒否することになります。 

合計の上限も欺瞞的になる可能性があります。 (n=4,m=3,s=12,x=0) の場合、有効な答え ([3,3,3,3]) は XOR ゼロになります。 2 つのゼロから開始して、残りの合計を他の 2 つの要素に入力しようとすると、2 つの要素が (6) を超える寄与を与えることができないため、失敗します。 構築では、合計がより大きい非最小ペアを選択できなければなりません。 

最後に、(m=0) はすべての配列要素を強制的にゼロにします。 したがって、(n=4,m=0,s=0,x=0) は有効ですが、(n=4,m=0,s=2,x=0) は不可能です。 この場合は一般的な構成で当然処理されますが、(m) の最上位設定ビット付近の境界条件を確認する場合に便利です。 

## アプローチ

 ブルートフォースアプローチは概念的には単純です。 要素が (0) と (m) の間にあるすべての配列を生成し、その合計と XOR を計算し、両方が要求された値に一致すると停止します。 考えられるすべての候補が検討されるため、これは正しいです。 問題は、正確に ((m+1)^n) 個の候補が存在することです。 (n=10^5) では、(m) の自明ではない最小値でもこれは不可能になるため、ブルート フォースは単にわずかに遅すぎるだけでなく、完全に使用できません。 

重要な観察は、等しい数値は XOR にとって非常に便利であるということです。 (v,v) を配列に入れると、それらの XOR はゼロになりますが、合計への寄与は (2v) になります。 これは、XOR が (x) である小さな数値グループを取得したら、必要な XOR を変更せずに、残りの 2 つの位置をすべて同じ値で埋めることができることを意味します。 必要なのは、その小さな XOR キャリーグループを構築する方法と、その合計がどのくらいの大きさになるかを決定することだけです。 

偶数 (n) の場合、特別なグループには 2 つの数値を含めることができます。 奇数 (n) の場合、(x\le m) の場合は 1 つの数値を含めることができ、(x>m) の場合は 3 つの数値を含めることができます。 後者の場合、3 つの数値は、XOR (x) にゼロが続く有効なペアになります。 したがって、実際の問題は、規定の XOR と慎重に選択された合計を使用して 2 つの制限された数値を構築することに帰着します。 

2 つの数値が (a,b)、それらの XOR が (x)、それらの合計が (p) であるとします。 定義する

 [
 y=\frac{p-x}{2}。 
]

 上記のアイデンティティにより、

 [
 a\mathbin{&}b=y。 
]

(a\mathbin{&}b) に設定されているすべてのビットは (a\oplus b) ではゼロでなければならないため、(y\mathbin{&}x=0) が必要です。 逆に、この条件が当てはまる場合、(x) のビットは (a) と (b) に分割でき、(y) のすべてのビットは両方の数値に配置されます。 

これにより、バイナリで直接、有界ペアの問題を解決できるようになります。 固定 (y) の場合は、次のように書きます。 

[
 a=y+u,\qquad b=y+(x\oplus u),
 ]

 ここで、(u) は (x) の設定ビットの任意のサブセットです。 (u) と (x\oplus u) は互いに素なビットを使用しているため、これらは対応する通常の合計でもあります。 (c=m-y) の場合、(u\le c) と (x\oplus u\le c) の両方が必要です。 (c) を超えない (x) の最大の部分集合は、最上位ビットから最下位ビットまで貪欲に取得できます。 その補数がまだ (c) より大きい場合、他のサブセットはすべて大きくないため、他のサブセットは機能できません。 

残りの問題は、どれ (y) を試すかです。 特別なグループの後に (r) 個のポジションが残っている場合、それらのポジションは等しいペアで埋めることができます。 彼らの最大合計貢献度は (rm) です。 したがって、特別なペアは少なくとも (s-rm) の和を持たなければなりません。 その和は (x+2y) なので、次のようになります。 

[
 y\ge \frac{s-rm-x}{2}。 
]

 その範囲を満たす最小の (y) と (y\mathbin{&}x=0) を選択します。 (y) を増やすとペアの合計が大きくなり、同時に利用可能な境界 (m-y) が減少します。そのため、実現可能な最小の (y) が有界のペアを形成できない場合は、より大きな (y) を使用しても役に立ちません。 

これにより、テスト ケースごとに対数量のバイナリ作業が発生し、その後に実際に答えを出力するために必要な避けられない (O(n)) 作業が続きます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O((m+1)^n\cdot n)) | (O(n)) | 遅すぎる |
 | 最適 | (O(n+\log m)) | (O(n)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. (s<x) または (s) と (x) のパリティが異なるかどうかを確認します。 XOR は通常の合計を超えることができず、キャリーごとに合計が偶数だけ変化するため、どちらの条件でも解は不可能になります。 
2. 必要な特殊要素の数を決定します。 (n) が奇数で (x\le m) の場合は、単一の要素 (x) を使用します。 残りの (n-1) 個の位置は等しいペアを形成します。 それ以外の場合は、XOR (x) を運ぶ 2 つの要素を使用し、(n) が奇数の場合は、残りの位置の数が偶数になるようにゼロを追加します。 
3. (r) を特別グループの後に残る位置の数とします。 残りの (r) 位置は最大でも (rm) しか貢献できないため、特別なペアは少なくとも (L=s-rm) の和を持たなければなりません。 すべての有効なペアには合計 (x+2y) があるため、この下限から必要な最小値 (y) を計算します。 
4. (y\ge (L-x)/2) と (y\mathbin{&}x=0) の両方を満たす最小の (y\ge0) を見つけます。 下限がすでに非正である場合は、ゼロから開始します。 (x) の設定ビットを回避して次の整数を見つけるには、現在の値の禁止されている設定ビットの最下位を見つけて、許可されている現在 0 の最初の上位ビットに繰り上げます。 
5. この (y) に対して、(c=m-y) を設定します。 (x) のセット ビットを 2 つの互いに素なサブセット (u) と (x\oplus u) に分割する必要があります (両方とも最大 (c))。 (x) のビットを上位から下位までスキャンして、可能な最大のサブセット (u\le c) を構築します。 (x\oplus u>c) の場合、ペアは存在できません。 
6. (a=y+u) および (b=y+(x\oplus u)) としてペアを構築します。 それらの XOR は (x)、合計は (x+2y) で、両方とも最大 (m) です。 
7. (n) が奇数で (x>m) の場合、ペアの後に 0 を追加します。 ゼロは合計も XOR も変更せず、残りの位置の数を偶数にします。 
8. (E) を、特別なグループの後でまだ不足している合計とします。 (E) を 2 で割ります。 可能な限り、最大 (m) を等しいペアの値として繰り返し取得します。 すべてのペアはその値の 2 倍を合計に寄与し、ゼロは XOR に寄与するため、十分なペアが存在すると正確な残りの合計に達します。 
9. いずれかの時点で必要な特殊ペアを構築できない場合は、(-1) を出力します。 それ以外の場合は、構築された配列を出力します。

構築の背後にある不変条件は、特別なグループは常に正確に XOR (x) を持ち、その後追加されるすべてのペアは XOR 0 を持つということです。 同時に、追加された各ペアは合計に均等な量をもたらします。 選択された下限により、残りのポジションに十分な容量があることが保証され、貪欲な充填ステップにより、使用可能な容量がゼロから最大までのすべての必要な偶数和を実現するのに十分であることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10**30

def next_disjoint(value, x):
    """Smallest y >= value such that y & x == 0."""
    bad = value & x
    if bad == 0:
        return value

    k = (bad & -bad).bit_length() - 1

    for j in range(k + 1, 31):
        y_bit = (value >> j) & 1
        x_bit = (x >> j) & 1
        if y_bit == 0 and x_bit == 0:
            prefix = value & ~((1 << (j + 1)) - 1)
            return prefix | (1 << j)

    return 1 << 30

def make_pair(x, total, m):
    """
    Find a,b in [0,m] such that:
        a ^ b == x
        a + b == total
    """
    if total < x or ((total - x) & 1):
        return None

    y = (total - x) // 2

    if y > m or (y & x):
        return None

    cap = m - y

    if x > 2 * cap:
        return None

    u = 0
    for bit in range(29, -1, -1):
        b = 1 << bit
        if (x & b) and u + b <= cap:
            u |= b

    v = x ^ u

    if v > cap:
        return None

    return y + u, y + v

def solve_case(n, m, s, x):
    if s < x or ((s - x) & 1):
        return None

    # Odd n and x itself fits into one element.
    if n & 1 and x <= m:
        remaining = n - 1
        extra = s - x

        if extra < 0 or extra > remaining * m:
            return None

        ans = [x]
        half = extra // 2
        pairs = remaining // 2

        for _ in range(pairs):
            v = min(m, half)
            ans.append(v)
            ans.append(v)
            half -= v

        return ans

    # Otherwise we need a two-element XOR carrier.
    if n & 1:
        special = 3
    else:
        special = 2

    if n < special:
        return None

    remaining = n - special

    # The special pair must provide at least this much sum.
    lower = s - remaining * m

    if lower <= x:
        y_low = 0
    else:
        y_low = (lower - x) // 2

    if y_low < 0:
        y_low = 0

    if y_low > m:
        return None

    y = next_disjoint(y_low, x)

    if y > m:
        return None

    pair_sum = x + 2 * y

    if pair_sum > s:
        return None

    pair = make_pair(x, pair_sum, m)
    if pair is None:
        return None

    a, b = pair
    ans = [a, b]

    if n & 1:
        ans.append(0)

    extra = s - pair_sum

    if extra < 0 or extra > remaining * m or (extra & 1):
        return None

    half = extra // 2
    pairs = remaining // 2

    for _ in range(pairs):
        v = min(m, half)
        ans.append(v)
        ans.append(v)
        half -= v

    if half != 0 or len(ans) != n:
        return None

    return ans

def main():
    t = int(input())
    out = []

    for _ in range(t):
        n, m, s, x = map(int, input().split())
        ans = solve_case(n, m, s, x)

        if ans is None:
            out.append("-1")
        else:
            out.append(" ".join(map(str, ans)))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    main()
```最初の助っ人は、`next_disjoint`、設定ビットが (x) と重複しない、要求された下限を下回らない最小値を検索します。 現在の値がすでに条件を満たしている場合は、その値がすぐに返されます。 それ以外の場合は、競合する最下位ビットを削除する必要があり、(x) と現在の値のゼロの両方で許可される最初の上位ビットを設定することで、可能な限り最小の増加が得られます。 

の`make_pair`関数は恒等式 (a+b=x+2(a\mathbin{&}b)) を実装します。 変数`y`は正確に (a\mathbin{&}b) なので、`y & x`ゼロでなければなりません。 一度`y`が固定されている場合、(x) のすべてのビットは 2 つの数値のいずれかに正確に属します。 変数`u`最初の数値に属するビットを選択し、`x ^ u`他の部分を与えます。 

高から低までの貪欲な構造`u`利用可能な重みは 2 の累乗であるため、安全です。 (x) の設定ビットのうち、次の値を超えない最大のサブセットを見つけます。`cap`。 その補数が大きすぎる場合、より小さなサブセットにはさらに大きな補数が含まれるため、代替のパーティションは存在しません。 

主な構造では、等しいペアを配列の調整可能な部分として扱います。 残りの位置の数は常に偶数です。そのため、特別なグループには、奇数で直接表現可能な場合には 1 つの要素があり、偶数 (n) には 2 つの要素があり、(x>m) の場合は奇数 (n) には 3 つの要素があります。 Python 整数を使用すると、(10^{18}) までの値のオーバーフローに関する懸念も解消されます。 

操作の順序が重要です。 特別なペアの下限は、ペアを作成する前に計算されます。これは、選択したペアが小さすぎると、残りの要素が保持できる以上の合計が残る可能性があるためです。 逆に、必要以上に大きなペアを選択すると、残りの容量が減少するだけなので、実現可能な最小の (y) が正しい選択となります。 

## 実用的な例

 最初のサンプルでは、(n=4,m=4,s=15,x=7) を考えます。 (n) は偶数であるため、2 つの要素は XOR を実行し、2 つの位置は等しいペアとして残ります。 

| 変数 | 値 |
 | --- | --- |
 | (n) | 4 |
 | (男) | 4 |
 | (s) | 15 |
 | (x) | 7 |
 | 残りのポジション | 2 |
 | 下位ペアの合計 | (15-2\cdot4=7) |
 | (y) 下限 | 0 |
 | 選択済み (y) | 0 |
 | ペアの合計 | 7 |
 | ペア | (4,3) |
 | 残額 | 8 |
 | 等しいペア | (4,4) |
 | 最終配列 | (4,3,4,4) |

 特別なペアには (4\oplus3=7) と合計 (7) があります。 最後のペアは XOR を変更せずに (8) を寄与するため、合計は (15) になり、XOR は (7) のままになります。 これは、(x) を直接配置することが不正となる (x>m) ケースも示しています。 

2 番目のサンプルの場合、(n=4,m=4,s=4,x=4)。 ここでは (x\le m) ですが、(n) は偶数であるため、XOR キャリアは依然として 2 つの位置を使用する必要があります。 

| 変数 | 値 |
 | --- | --- |
 | (n) | 4 |
 | (男) | 4 |
 | (s) | 4 |
 | (x) | 4 |
 | 残りのポジション | 2 |
 | 下位ペアの合計 | (4-2\cdot4=-4) |
 | 選択済み (y) | 0 |
 | ペアの合計 | 4 |
 | ペア | (4,0) |
 | 残額 | 0 |
 | 最終配列 | (4,0,0,0) |

 ペア (4,0) には XOR (4) と合計 (4) があります。 残りの 2 つのゼロは両方の量を保持します。 

別の有用なトレースとして、(n=4,m=3,s=12,x=0) を考えてみましょう。 

| 変数 | 値 |
 | --- | --- |
 | (n) | 4 |
 | (男) | 3 |
 | (s) | 12 |
 | (x) | 0 |
 | 残りのポジション | 2 |
 | 下位ペアの合計 | (12-2\cdot3=6) |
 | 選択済み (y) | 3 |
 | ペアの合計 | 6 |
 | ペア | (3,3) |
 | 残額 | 6 |
 | 等しいペア | (3,3) |
 | 最終配列 | (3,3,3,3) |

 このトレースは、可能な最小の合計 (x) で常にペアを選択できない理由を示しています。 ここでは (x=0) ですが、(0,0) を使用すると、残りの 2 つの要素のみの合計が (12) になりますが、これは不可能です。 下限により、特別なペアに寄与 (6) が強制され、その後、他のペアが残り (6) に寄与します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n+\log m)) テスト ケースごと | バイナリの構築には最大約 30 ビットが使用され、(n) 個の値の出力には (O(n)) のコストがかかります。 |
 | スペース | (O(n)) | 出力配列には正確に (n) 個の整数が含まれます。 |

 すべてのテスト ケースにわたって、合計 (n) は最大でも (3\cdot10^5) であるため、合計の出力構造は (O(3\cdot10^5)) になります。 バイナリ部分は、テスト ケースごとに最大 30 ビットにわたって一定数のスキャンのみを実行します。 これは 1 秒の制限時間に快適に適合しますが、(O(n)) メモリは必要な出力サイズによって制限されます。 

## テストケース```python
# Self-contained assert-based tests for the construction.

import sys
import io

def next_disjoint(value, x):
    bad = value & x
    if bad == 0:
        return value

    k = (bad & -bad).bit_length() - 1

    for j in range(k + 1, 31):
        if ((value >> j) & 1) == 0 and ((x >> j) & 1) == 0:
            prefix = value & ~((1 << (j + 1)) - 1)
            return prefix | (1 << j)

    return 1 << 30

def make_pair(x, total, m):
    if total < x or ((total - x) & 1):
        return None

    y = (total - x) // 2

    if y > m or (y & x):
        return None

    cap = m - y

    if x > 2 * cap:
        return None

    u = 0
    for bit in range(29, -1, -1):
        b = 1 << bit
        if (x & b) and u + b <= cap:
            u |= b

    v = x ^ u

    if v > cap:
        return None

    return y + u, y + v

def solve_case(n, m, s, x):
    if s < x or ((s - x) & 1):
        return None

    if n & 1 and x <= m:
        remaining = n - 1
        extra = s - x

        if extra < 0 or extra > remaining * m:
            return None

        ans = [x]
        half = extra // 2

        for _ in range(remaining // 2):
            v = min(m, half)
            ans.extend([v, v])
            half -= v

        return ans

    special = 3 if (n & 1) else 2

    if n < special:
        return None

    remaining = n - special
    lower = s - remaining * m
    y_low = 0 if lower <= x else (lower - x) // 2

    if y_low > m:
        return None

    y = next_disjoint(y_low, x)

    if y > m:
        return None

    pair_sum = x + 2 * y

    if pair_sum > s:
        return None

    pair = make_pair(x, pair_sum, m)
    if pair is None:
        return None

    a, b = pair
    ans = [a, b]

    if n & 1:
        ans.append(0)

    extra = s - pair_sum

    if extra < 0 or extra > remaining * m or extra & 1:
        return None

    half = extra // 2

    for _ in range(remaining // 2):
        v = min(m, half)
        ans.extend([v, v])
        half -= v

    if half != 0 or len(ans) != n:
        return None

    return ans

def solve_text(inp: str) -> str:
    data = io.StringIO(inp)
    t = int(data.readline())
    out = []

    for _ in range(t):
        n, m, s, x = map(int, data.readline().split())
        ans = solve_case(n, m, s, x)

        if ans is None:
            out.append("-1")
        else:
            out.append(" ".join(map(str, ans)))

    return "\n".join(out)

def run(inp: str) -> str:
    return solve_text(inp)

def validate(inp: str, out: str):
    lines = out.strip().splitlines()
    data = inp.strip().splitlines()

    t = int(data[0])
    assert len(lines) == t

    for i in range(t):
        n, m, s, x = map(int, data[i + 1].split())
        line = lines[i].strip()

        if line == "-1":
            assert solve_case(n, m, s, x) is None
            continue

        a = list(map(int, line.split()))
        assert len(a) == n
        assert all(0 <= v <= m for v in a)
        assert sum(a) == s

        cur_xor = 0
        for v in a:
            cur_xor ^= v

        assert cur_xor == x

# Provided sample
sample = """\
3
4 4 15 7
4 4 4 4
4 4 15 1
"""
validate(sample, run(sample))

# Minimum-size valid case
case1 = """\
1
1 5 5 5
"""
validate(case1, run(case1))

# All elements equal
case2 = """\
1
4 3 12 0
"""
validate(case2, run(case2))

# Boundary case with x > m
case3 = """\
1
3 4 7 7
"""
validate(case3, run(case3))

# Impossible because the requested sum is too large for the XOR requirement
case4 = """\
1
4 4 15 1
"""
assert run(case4).strip() == "-1"

# Maximum-size n, with all elements forced to one
case5 = """\
1
100000 1 100000 0
"""
validate(case5, run(case5))
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / 5 5 5 5`|`5`| 最小 (n)、直接単一要素構築 |
 |`4 / 3 12 0`|`3 3 3 3`| すべての値が同じで、必要な合計が大きい |
 |`3 / 4 7 7`|`4 3 0`| XOR が (m) より大きいため、2 つの値に分割する必要があります。 
|`4 / 4 15 1`|`-1`| 合計容量と不可能な建設 |
 |`100000 / 1 100000 0`| 100000 個 | 最大 (n)、出力サイズ、およびペアの充填 |

 ## 特殊なケース

 (n=1) の場合、配列には値が 1 つだけあります。 考慮する`1 5 5 5`。 チェックはパスし (x\le m)、奇数の長さにより 1 つの特別な値 (x=5) が許可されます。 出力は`[5]`。 のために`1 5 4 5`の場合、考えられる唯一の要素は (5) でなければならず、その和が (4) になるはずがないため、パリティまたは合計関係はこのケースを拒否します。 

(x>m) の場合、値 (x) を直接挿入することはできません。 のために`3 4 7 7`、2 の最大有効累乗は (4)、および (7=4+3) です。 特別なグループは、`[4,3,0]`。 その合計は (7)、XOR は (4\oplus3\oplus0=7)、すべての値は最大 (4) です。 

完全に等しい答えを得るには、次のように考えます。`4 3 12 0`。 必要な XOR はゼロであるため、等しいペアが理想的です。 下限により、最初のペアに貢献が強制されます (6)。`[3,3]`。 残りの合計はさらに (6) となり、`[3,3]`また。 最終的な配列は`[3,3,3,3]`、その合計は (12) で、XOR は 0 です。 

(m=0) の場合、すべての値はゼロでなければなりません。 と`4 0 0 0`の場合、奇数または偶数の分岐はゼロのペアのみを構築し、要求された合計はすでにゼロであるため、4 つのゼロの配列が返されます。 と`4 0 2 0`の場合、残りの容量はゼロであるため、構築はテスト ケースを拒否します。 

容量の上限については、`100000 1 100000 0`可能な最大の合計が必要です。 (n) は偶数であり、XOR は 0 であるため、構築では 1 のペアが使用されます。 すべての要素は (1) になり、合計は (100000) になり、偶数個の 1 が XOR 演算されるため、XOR はゼロになります。 これにより、許容最大値 (n) も実行され、出力構造が配列サイズ内で線形のままであることが確認されます。
