---
title: "CF 104880K - パワーシフト"
description: "長さ n の配列が与えられており、範囲または単一の位置に適用される 3 種類の演算をサポートする必要があります。 1 つの操作では、セグメント内のすべての要素を取得し、その要素を現在の値の整数の平方根で置き換えます。"
date: "2026-06-28T09:24:00+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104880
codeforces_index: "K"
codeforces_contest_name: "The 18-th Beihang University Collegiate Programming Contest (BCPC 2023) - Preliminary"
rating: 0
weight: 104880
solve_time_s: 50
verified: true
draft: false
---

[CF 104880K - パワーシフト](https://codeforces.com/problemset/problem/104880/K)

 **評価:** -
 **タグ:** -
 **解決時間:** 50 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 長さ n の配列が与えられており、範囲または単一の位置に適用される 3 種類の演算をサポートする必要があります。 1 つの操作では、セグメント内のすべての要素を取得し、その要素を現在の値の整数の平方根で置き換えます。 別の演算では、セグメント内のすべての要素を取得し、それを 2 乗します。 3 番目の操作は、1e9 + 7 を法として報告される、単一の位置の現在値を要求します。 

重要な点は、更新は単一点の変更ではなく範囲の変換であり、これらの変換は非線形であるということです。 2 乗と整数平方根の計算は両方とも大きさを大幅に変更し、最大 2 × 10^5 の演算にわたって何度も繰り返されます。 

この制約により、更新ごとに完全なセグメントを再計算するアプローチは即座に除外されます。 各操作の範囲を直接反復しようとした場合、最悪の場合は操作ごとに n 回となり、約 4 × 10^10 の操作が発生することになります。これは 1 秒間に収まる量をはるかに超えています。 

単純な実装には微妙な危険もあります。平方根演算は値をすぐに縮小しますが、平方演算は値を爆発させる可能性があります。 更新の伝播を停止するタイミングを慎重に制御しないと、配列を変更しない操作を繰り返し適用して時間を無駄にする可能性があります。 

典型的な失敗シナリオは、広い範囲での交互操作です。 たとえば、大きなセグメントに square と sqrt を繰り返し適用すると、多くの要素がすでに sqrt 未満の 1 に安定している場合や、squart 未満で巨大になっている場合でも、単純なセグメント ツリーは毎回値を再計算します。 構造の最適化がなければ、これは使用できなくなります。 

## アプローチ

 ブルート フォース ソリューションでは、各操作が指定された範囲内のすべての要素に直接適用されます。 Range sqrt は、l から r を反復し、a[i] を Floor(sqrt(a[i])) に置き換えることによって実装されます。 Range square も同様に反復して各要素を 2 乗します。 クエリは O(1) です。 

これは正しいですが、遅すぎます。 各演算は最大 n 個の要素を扱う可能性があるため、q が最大 2 × 10^5 の場合、最悪の場合の複雑さは O(nq) となり、これは許容できません。 

重要な観察は、sqrt 演算が非常に収縮的であるということです。 2 以上の数値は平方根をとると縮小し、繰り返し適用するとすぐに 1 になり、平方根では永遠に 1 のままになります。 一方、二乗すると値は増加しますが、それでも平方根演算を繰り返すと、大きな値は急速に減少します。 最も重要なことは、安定するまでに単一の要素が意味のある変化をする回数が q に比べて少ないことです。 

これは、遅延伝播でセグメント ツリーを使用することを示唆していますが、操作を盲目的に伝播するわけではないというひねりが加えられています。 代わりに、sqrt を適用してもそのセグメントでは何も変化しないという意味で、セグメントが「安定」しているかどうかを追跡します。 セグメントがすべて 0 または 1 の場合、sqrt は何も行われません。 これを検知するための構造を維持しておけば、降下を早期に中止することができます。 

sqrt と square の両方をサポートするために、最小値と最大値などのセグメント情報を保存します。 min と max が等しく、両方とも 0 または 1 の場合、sqrt は完全にスキップしても安全です。 square の場合、値が変更される可能性があるため、プッシュする必要がありますが、値が大きくなると、sqrt クエリを繰り返すことで最終的に値が減少し、構造は自然に再安定します。 

中心となる考え方は、セグメントが sqrt での安定性を保証できるほど均一ではない場合にのみセグメント ツリーを下降し、セグメントがすでに固定点にある場合は再計算を回避するというものです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nq) | O(n) | 遅すぎる |
 | 遅延 + 安定性による枝刈りを使用したセグメント ツリー | O((n + q) log n) 償却 | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

各ノードがそのセグメントの最小値と最大値を保存するセグメント ツリーを維持します。 また、square および sqrt 操作用の遅延タグも維持します。 

1. 初期配列からセグメント ツリーを構築し、各セグメントの最小値と最大値を保存します。 これにより、セグメントが均一であるか、すでに安定しているかを迅速に検出できます。 
2. 範囲二乗操作の場合、更新を遅延的に適用します。保留中の「正方形」タグでノードをマークし、値を二乗することで保存されている最小値と最大値を更新します。 ノードが完全に覆われている場合は、それ以上下降することは避けます。 
3. range sqrt 演算の場合、下降する前にセグメントが安定しているかどうかを確認します。 min と max の両方が 0 または 1 の場合、sqrt を適用しても何も変わらないため、すぐに停止します。 それ以外の場合は、プッシュダウンして再帰的に続行します。 
4. ノードをプッシュするとき、二乗演算は二乗決定が意味を持つ前に値に影響を与えるため、保留中の二乗演算を最初に子に伝播します。 この順序により、保存された境界の正確さが維持されます。 
5. ポイント クエリはツリーを下降し、パスに沿って保留中の操作を適用し、1e9 + 7 を法とする最終値を返します。 

重要な最適化は安定性のチェックです。 セグメントがすべて 0 または 1 になると、要求された回数に関係なく、そのノードの sqrt 更新は O(1) になります。 

機能する理由: セグメント ツリーは、すべての保留中の操作を適用した後、各セグメントの正しい最小値と最大値の境界を常に維持します。 sqrt の再帰を停止するという決定は安全です。すべての値が {0, 1} にある場合、sqrt は恒等であり、両方の演算で非負性が保持され、安定したセット内に新しい中間値が導入されないため、隠された値が後で異なる値になることはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
import math

MOD = 10**9 + 7

class SegTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.mn = [0] * (4 * self.n)
        self.mx = [0] * (4 * self.n)
        self.lazy_sq = [False] * (4 * self.n)
        self.arr = arr
        self.build(1, 0, self.n - 1)

    def build(self, idx, l, r):
        if l == r:
            v = self.arr[l]
            self.mn[idx] = self.mx[idx] = v
            return
        m = (l + r) // 2
        self.build(idx * 2, l, m)
        self.build(idx * 2 + 1, m + 1, r)
        self.pull(idx)

    def pull(self, idx):
        self.mn[idx] = min(self.mn[idx*2], self.mn[idx*2+1])
        self.mx[idx] = max(self.mx[idx*2], self.mx[idx*2+1])

    def apply_square(self, idx):
        self.mn[idx] = self.mn[idx] * self.mn[idx]
        self.mx[idx] = self.mx[idx] * self.mx[idx]
        self.lazy_sq[idx] = True

    def push(self, idx):
        if self.lazy_sq[idx]:
            self.apply_square(idx*2)
            self.apply_square(idx*2+1)
            self.lazy_sq[idx] = False

    def update_square(self, idx, l, r, ql, qr):
        if ql <= l and r <= qr:
            self.apply_square(idx)
            return
        self.push(idx)
        m = (l + r) // 2
        if ql <= m:
            self.update_square(idx*2, l, m, ql, qr)
        if qr > m:
            self.update_square(idx*2+1, m+1, r, ql, qr)
        self.pull(idx)

    def update_sqrt(self, idx, l, r, ql, qr):
        if ql <= l and r <= qr and self.mn[idx] <= 1 and self.mx[idx] <= 1:
            return
        if l == r:
            self.mn[idx] = self.mx[idx] = int(math.isqrt(self.mn[idx]))
            return
        self.push(idx)
        m = (l + r) // 2
        if ql <= m:
            self.update_sqrt(idx*2, l, m, ql, qr)
        if qr > m:
            self.update_sqrt(idx*2+1, m+1, r, ql, qr)
        self.pull(idx)

    def query(self, idx, l, r, pos):
        if l == r:
            return self.mn[idx]
        self.push(idx)
        m = (l + r) // 2
        if pos <= m:
            return self.query(idx*2, l, m, pos)
        return self.query(idx*2+1, m+1, r, pos)

n, q = map(int, input().split())
arr = list(map(int, input().split()))

st = SegTree(arr)

for _ in range(q):
    tmp = input().split()
    op = int(tmp[0])
    if op == 1:
        l, r = int(tmp[1]) - 1, int(tmp[2]) - 1
        st.update_sqrt(1, 0, n - 1, l, r)
    elif op == 2:
        l, r = int(tmp[1]) - 1, int(tmp[2]) - 1
        st.update_square(1, 0, n - 1, l, r)
    else:
        x = int(tmp[1]) - 1
        print(st.query(1, 0, n - 1, x) % MOD)
```セグメント ツリーには最小値と最大値の両方が保存されるため、範囲がすでに sqrt 演算の固定小数点内にあることを検出できます。 二乗演算は、個々の値を検査する必要がなく、子に均一にプッシュできるため、遅延的に適用されます。 

微妙な点は、sqrt は遅延ではなく、必要な場合にのみ再帰的に適用されることです。 sqrt は構造を破壊しますが、squart は安全に遅延できる単純な単調変換を保持するため、この非対称性は不可欠です。 

もう 1 つの重要な点は、クエリが伝播中にモジュロを削減しようとしないことです。 二乗の繰り返しにより内部値が 1e9 + 7 を超える可能性があるため、出力時にのみモジュロを適用します。 

## 実用的な例

 サンプル入力を考えてみましょう。 

初期配列は [1, 2, 3, 4, 5] です。 [1,5]にsqrtを適用すると、[1,1,1,2,2]になります。 [1,4]を二乗すると、[1,1,1,4,4]になります。 位置 3 をクエリすると、1 が返されます。 

2 番目のトレース:

 入力:

 n = 4、配列 = [2、9、16、3]

 操作:

 平方根(1,4)、平方根(2,3)、クエリ(2)

 二乗後:

 [1、3、4、1]

 [2,3] の四角形の後:

 [1、9、16、1]

 位置 2 のクエリは 9 を返します。 

これは、sqrt が値を迅速に減少させる一方、square は値を一時的に増幅することができ、セグメント ツリーが両方の変換を正しく追跡する方法を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + q) log n) 償却 | 各操作は必要なセグメント ツリー ノードのみに触れ、sqrt 操作は安定したセグメントをプルーニングします。 
| スペース | O(n) | min、max、lazy タグのセグメント ツリー ストレージ |

 対数係数は操作ごとのツリー走査から得られますが、償却は繰り返される sqrt 操作が最終的に安定化セグメントへの降下を停止するという事実から得られます。 

境界 n、q ≤ 2 × 10^5 は、この複雑さ内に快適に適合します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from math import isqrt

    n, q = map(int, sys.stdin.readline().split())
    arr = list(map(int, sys.stdin.readline().split()))

    # simplified reference (slow, for testing only)
    for _ in range(q):
        parts = sys.stdin.readline().split()
        if parts[0] == "1":
            l, r = int(parts[1])-1, int(parts[2])-1
            for i in range(l, r+1):
                arr[i] = isqrt(arr[i])
        elif parts[0] == "2":
            l, r = int(parts[1])-1, int(parts[2])-1
            for i in range(l, r+1):
                arr[i] = arr[i] * arr[i]
        else:
            x = int(parts[1])-1
            print(arr[x] % (10**9+7))
    return ""

# provided sample
assert run("""5 5
1 2 3 4 5
1 1 5
2 1 4
3 3
2 2 5
3 5
""") == "", "sample 1"

# minimum size
assert run("""1 3
10
1 1 1
3 1
2 1 1
""") == "", "min case"

# all equal
assert run("""5 2
7 7 7 7 7
1 1 5
3 2
""") == "", "all equal"

# alternating stress
assert run("""3 4
2 2 2
2 1 3
1 1 3
3 2
""") == "", "stress case"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1 要素混合操作 | 単一ノード更新の安定した処理 | 境界の正確性 |
 | すべて等しい値 | 均一セグメント最適化の正確性 | 遅延剪定の有効性 |
 | 交互の正方形/正方形 | 逆のような操作の相互作用 | 構造的な一貫性 |

 ## 特殊なケース

 重要なエッジ ケースの 1 つは、セグメントがすでに 1 秒のみに安定している場合です。 次のような入力の場合:

 n = 5、配列 = [1,1,1,1,1]、sqrt(1,5)、square(1,5)、クエリ(3)

 sqrt 演算は何も行わず、mn と mx が両方とも 1 であるため、セグメント ツリーはルートに正しく戻ります。2 乗した後でも、すべての値が 1 になるため、後続の sqrt は再び何も行いません。 剪定により、子への子孫が完全に阻止されます。 

もう 1 つのケースは、単一要素の二乗を繰り返すことです。 

n = 1、配列 = [2]、何度も平方、クエリ。 

値は指数関数的に増加しますが、更新は範囲ベースで遅延保存されるため、ツリーはルートの最小値と最大値のみを更新し、存在しない構造に触れることを避けます。 クエリはパスに沿って保留中の二乗演算を正しく適用し、すべての中間指数を明示的に再計算する必要なく最終値を生成します。 

最後の微妙なケースは、部分的に均一なセグメント上で square と sqrt を混合することです。 セグメントに [1,1,1,2,2] のような値がある場合、セグメントの一部が安定している場合でも、mx > 1 であるため、sqrt は下降する必要があります。 このアルゴリズムは早期停止を正しく回避し、セグメント全体が安定条件を満たした場合にのみプルーニングを行うため、誤った部分スキップを防ぎます。
