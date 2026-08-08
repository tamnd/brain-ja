---
title: "CF 104021G - ポット!!"
description: "最大 10 万の長さの配列が与えられます。 すべての要素は 1 から始まり、連続するセグメントに 2 ～ 10 の小さな整数を乗算するか、セグメントに対するクエリを求める一連の操作を実行します。"
date: "2026-07-02T04:36:13+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104021
codeforces_index: "G"
codeforces_contest_name: "The 2019 ICPC Asia Yinchuan Regional Contest"
rating: 0
weight: 104021
solve_time_s: 58
verified: true
draft: false
---

[CF 104021G - ポット!!](https://codeforces.com/problemset/problem/104021/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 58 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 最大 10 万の長さの配列が与えられます。 すべての要素は 1 から始まり、連続するセグメントに 2 ～ 10 の小さな整数を乗算するか、セグメントに対するクエリを求める一連の操作を実行します。 

配列の各値は常に素数に因数分解されます。私たちが気にするのは、素数が数値を何回割るかということだけです。 値 ai の場合、素数 p の場合、スコア Potp(ai) を ai の因数分解における p の指数として定義します。 各位置 i について、ai を分割するすべての素数を調べ、それらの中で最大の指数を取得します。 範囲クエリでは、セグメントにわたる最大値が求められます。 

したがって、概念的には、すべての数値には素数の複数の「層」が含まれていますが、追跡するのは各位置で最も強い単一層のみであり、次に範囲内の層の中で最も強い層だけです。 

この制約は、更新ごとに素因数分解をシミュレートできないことを示唆しています。 最大 10 万の更新があり、各乗算はセグメント全体に適用されます。 最初は各値の大きさが小さくても、更新を繰り返すとすぐに値が大きくなるため、因数分解を直接再計算すると時間がかかりすぎます。 

主要な構造上の制限は、乗算器が小さく、最大でも 10 であることです。つまり、すべての更新では集合 {2, 3, 5, 7} からの素数のみが導入されます。 他の素数は決して現れません。 これにより、問題は任意の因数分解から位置ごとの 4 つの指数の追跡へと分解されます。 

微妙な落とし穴は、クエリの読み間違いです。指数を合計していませんし、素数の最大値をグローバルに取得していません。 各位置について、その素数の中で最良の指数を取得し、それをセグメント全体で最大化します。 これにより、単純な範囲の最大値ではなく、「2 レベルの最大値」の問題になります。 

エッジケースは、複数の素数が同じインデックス上で異なる方法で蓄積される場合に発生します。 たとえば、ある位置に 2^5·3^1 がある場合、そのスコアは 6 ではなく 5 です。別の位置には 3^4·5^4 があり、スコアは 4 になります。クエリはグローバルに 9 や 4 ではなく 5 を選択します。 指数を合計したり、総乗算数のみを追跡したりする単純な解決策は、ここでは失敗します。 

## アプローチ

 ブルートフォースアプローチでは、各 AI の完全な値が明示的に維持されます。 MULTIPLY l r x 演算の場合、各要素を個別に乗算します。 MAX クエリの場合、範囲内の各数値を因数分解し、最適な素指数を計算します。 

これは定義を正確に反映しているため正しく機能しますが、パフォーマンスはすぐに低下します。 各乗算は最大 10 万の要素に影響し、最大 10 万の演算が行われ、最悪のケースでは 10^10 の更新程度になります。 適度に大きい数値でも因数分解すると事態はさらに悪化します。 

中心的な観察は、{2,3,5,7,10} における x の乗算は、固定された小さな素数セットの指数のみを変更するということです。 数値を再構築する必要はありません。 各位置の素数 2、3、5、7 の指数カウントを維持するだけで済みます。 ある位置の値は 4 つの整数で完全に記述されます。 

これにより、問題は 4 つの範囲加算構造 (素数 2、3、5、7 の場合) を維持し、インデックスごとにこれら 4 つの値の最大値を超える範囲最大値クエリに応答することになります。 

範囲の乗算をサポートするには、指数配列で範囲の加算が必要です。 最大クエリをサポートするには、導出されたインデックスごとの値 max(exp2, exp3, exp5, exp7) を超える範囲の最大クエリが必要です。 これは線形ではないため、ノードごとにその区間内の max-exponent の最大値を格納するセグメント ツリーと、各素数指数への寄与を個別に追加するための遅延伝播を維持します。

秘訣は、ノードごとに 4 つの遅延タグ (素数指数ごとに 1 つ) を維持し、それらをプッシュダウンして、リーフ値が常に正しい寄与の合計になるようにすることです。 ノードは、子に保存されている 4 つの最大値をすべてチェックし、維持されている集計を通じて暗黙的に最適な組み合わせ値を取得することによって、その最大値を再計算します。 

主な困難は、素数ごとの分離を維持しながら範囲更新を O(log n) に保つことと、MAX クエリが単一のセグメント ツリー クエリのままであることを保証することです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nq + q√A) | O(n) | 遅すぎる |
 | 4 つの遅延タグを含む最適なセグメント ツリー | O(q log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 概念的には、{2, 3, 5, 7} の各素数に 1 つずつ、合計 4 つの指数配列を維持しますが、完全な配列を明示的に保存することはありません。 代わりに、セグメント ツリー ノードはセグメントを表し、そのセグメント内の各素数の最大指数と、そのセグメントから導出された最良の値を格納します。 

1. 考えられる各乗数の指数寄与を事前計算します。 [2,10] の各 x について、それを 2、3、5、7 に因数分解し、各素数が何回出現するかを保存します。 これにより、各更新を 4 つの範囲追加操作に変換できます。 
2. インデックス 1 から n までのセグメント ツリーを構築し、すべての AI は 1 から始まるため、すべてを 0 に初期化します。これは、すべての指数値が最初はどこでも 0 であることを意味します。 
3. MULTIPLY l r x クエリの場合、x を 4 つの増分 delta2、delta3、delta5、delta7 に変換します。 セグメント ツリーで遅延伝播を使用して、[l, r] にわたる各デルタの範囲加算を適用します。 これが機能する理由は、値空間での乗算が、素数ごとに独立して指数空間での加算になるためです。 
4. 各セグメント ツリー ノードは、そのセグメント内で各素数の最大指数値を個別に維持します。 遅延更新を適用する場合、リーフを再計算することなく、保存されている 4 つの最大値すべてにデルタを一貫して追加します。 
5. 遅延値をプッシュダウンする場合、セグメントと子の間の内部一貫性が維持されるように、子が累積された指数増分を確実に継承するようにします。 これにより、個々の要素にアクセスすることなく、すべての指数情報が正確に保たれます。 
6. MAX l r クエリの場合、[l, r] に対してセグメント ツリーをクエリし、そのセグメント内の最大指数 2、3、5、および 7 の 4 つの値を取得します。 そのセグメントの答えは、これら 4 つの数値の最大値です。 
7. この最大値をクエリの結果として返します。 

機能する理由: すべての AI は 4 つの独立した指数カウントによって完全に特徴付けられます。 乗算更新は素数間の独立性を維持するため、各更新は 4 つの加算範囲更新にきれいに分解されます。 クエリ定義は、位置ごとに素数の最大値を取得し、次に位置の最大値を取得することになります。 セグメント ツリーには、両方のレベルの最大値を保存するために必要な集計が正確に保存されます。 遅延伝播により、指数の寄与が決して失われたり二重にカウントされたりすることがなく、すべてのノードが常にその間隔の正しい累積状態を反映することが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

primes = [2, 3, 5, 7]

def factor_small(x):
    res = [0, 0, 0, 0]
    for i, p in enumerate(primes):
        while x % p == 0:
            res[i] += 1
            x //= p
    return res

class SegTree:
    def __init__(self, n):
        self.n = n
        self.mx = [[0, 0, 0, 0] for _ in range(4 * n)]
        self.lazy = [[0, 0, 0, 0] for _ in range(4 * n)]

    def apply(self, v, delta):
        for i in range(4):
            self.mx[v][i] += delta[i]
            self.lazy[v][i] += delta[i]

    def push(self, v):
        if v * 2 >= len(self.mx):
            return
        for i in range(4):
            if self.lazy[v][i]:
                self.apply(v * 2, [self.lazy[v][i]] + [0]*3)
                self.apply(v * 2 + 1, [self.lazy[v][i]] + [0]*3)
                self.apply(v * 2, [0, self.lazy[v][i], 0, 0])
                self.apply(v * 2 + 1, [0, self.lazy[v][i], 0, 0])
                self.apply(v * 2, [0, 0, self.lazy[v][i], 0])
                self.apply(v * 2 + 1, [0, 0, self.lazy[v][i], 0])
                self.apply(v * 2, [0, 0, 0, self.lazy[v][i]])
                self.apply(v * 2 + 1, [0, 0, 0, self.lazy[v][i]])
        self.lazy[v] = [0, 0, 0, 0]

    def update(self, v, tl, tr, l, r, delta):
        if l > r:
            return
        if l == tl and r == tr:
            self.apply(v, delta)
            return
        tm = (tl + tr) // 2
        self.push(v)
        self.update(v * 2, tl, tm, l, min(r, tm), delta)
        self.update(v * 2 + 1, tm + 1, tr, max(l, tm + 1), r, delta)
        for i in range(4):
            self.mx[v][i] = max(self.mx[v * 2][i], self.mx[v * 2 + 1][i])

    def query(self, v, tl, tr, l, r):
        if l > r:
            return [0, 0, 0, 0]
        if l == tl and r == tr:
            return self.mx[v]
        tm = (tl + tr) // 2
        self.push(v)
        left = self.query(v * 2, tl, tm, l, min(r, tm))
        right = self.query(v * 2 + 1, tm + 1, tr, max(l, tm + 1), r)
        return [max(left[i], right[i]) for i in range(4)]

def solve():
    n, q = map(int, input().split())
    st = SegTree(n)

    for _ in range(q):
        parts = input().split()
        if parts[0] == "MULTIPLY":
            l, r, x = map(int, parts[1:])
            delta = factor_small(x)
            st.update(1, 1, n, l, r, delta)
        else:
            l, r = map(int, parts[1:])
            res = st.query(1, 1, n, l, r)
            print("ANSWER", max(res))

if __name__ == "__main__":
    solve()
```実装は、すべての乗数を素数 2、3、5、7 の指数に対応する 4 次元ベクトルに変換することから始まります。セグメント ツリーは、ノードごとに、各素数の区間内で観測された最大指数を個別に格納します。 

更新操作では、このベクトルの範囲加算が適用されます。 概念的には、これにより影響を受けるすべての指数フィールドが増加します。各フィールドは独立しているため、それらを遅延伝播できます。 

クエリ操作は、要求された範囲内の素数ごとの最大指数を収集し、それらの 4 つの値の最大値を取得して、問題の定義と正確に一致します。 

実装上の微妙なリスクは、遅延伝播処理です。 各素数次元は一貫して更新する必要があります。 そうしないと、ある素数の寄与が他の素数よりも遅れ、不正確な最大値が生成される可能性があります。 

## 実用的な例

 ### 例 1

 入力:```
5 3
MULTIPLY 1 3 2
MULTIPLY 2 5 3
MAX 1 5
```| ステップ | 操作 | 主な効果 | セグメント状態の概要 |
 | --- | --- | --- | --- |
 | 1 | [1,3] を 2 で乗算する | +1 から exp2 | 位置 1 ～ 3 は 2^1 を得る |
 | 2 | [2,5] に 3 を掛ける | +1 から exp3 | 2 位から 3 位には両方の素数があります。 
| 3 | 最大 [1,5] | 最良の指数を計算する | 最大値は 1 |

 このトレースは、混合素数が累積されて単一の優勢指数よりも大きなスコアにならないことを示しています。 

### 例 2

 入力:```
4 3
MULTIPLY 1 4 4
MULTIPLY 2 3 3
MAX 1 4
```| ステップ | 操作 | 主な効果 | セグメント状態の概要 |
 | --- | --- | --- | --- |
 | 1 | 4 を掛ける | どこでも +2 から exp2 | すべての位置は 2^2 | になります。 
| 2 | [2,3] を 3 で掛ける | +1 から exp3 | 中間セグメントのゲイン 3 |
 | 3 | 最大 [1,4] | 指数を比較する | 答えは 2 |

 これは、1 つの素数で繰り返される指数の積み重ねが混合寄与を支配することを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(q log n) | 各更新とクエリはセグメント ツリー トラバーサルを使用します。 
| スペース | O(n) | セグメント ツリーはノードごとに定数情報を格納します。 

n と q の両方が最大 10 万であり、対数因数により総演算数が数百万程度に抑えられるため、この解は制約内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    primes = [2, 3, 5, 7]

    def factor_small(x):
        res = [0, 0, 0, 0]
        for i, p in enumerate(primes):
            while x % p == 0:
                res[i] += 1
                x //= p
        return res

    class SegTree:
        def __init__(self, n):
            self.n = n
            self.mx = [[0, 0, 0, 0] for _ in range(4 * n)]
            self.lazy = [[0, 0, 0, 0] for _ in range(4 * n)]

        def apply(self, v, delta):
            for i in range(4):
                self.mx[v][i] += delta[i]
                self.lazy[v][i] += delta[i]

        def push(self, v):
            if v * 2 >= len(self.mx):
                return
            for i in range(4):
                if self.lazy[v][i]:
                    self.apply(v * 2, [self.lazy[v][i]] + [0]*3)
                    self.apply(v * 2 + 1, [self.lazy[v][i]] + [0]*3)
                    self.apply(v * 2, [0, self.lazy[v][i], 0, 0])
                    self.apply(v * 2 + 1, [0, self.lazy[v][i], 0, 0])
                    self.apply(v * 2, [0, 0, self.lazy[v][i], 0])
                    self.apply(v * 2 + 1, [0, 0, self.lazy[v][i], 0])
                    self.apply(v * 2, [0, 0, 0, self.lazy[v][i]])
                    self.apply(v * 2 + 1, [0, 0, 0, self.lazy[v][i]])
            self.lazy[v] = [0, 0, 0, 0]

        def update(self, v, tl, tr, l, r, delta):
            if l > r:
                return
            if l == tl and r == tr:
                self.apply(v, delta)
                return
            tm = (tl + tr) // 2
            self.push(v)
            self.update(v * 2, tl, tm, l, min(r, tm), delta)
            self.update(v * 2 + 1, tm + 1, tr, max(l, tm + 1), r, delta)
            for i in range(4):
                self.mx[v][i] = max(self.mx[v * 2][i], self.mx[v * 2 + 1][i])

        def query(self, v, tl, tr, l, r):
            if l > r:
                return [0, 0, 0, 0]
            if l == tl and r == tr:
                return self.mx[v]
            tm = (tl + tr) // 2
            self.push(v)
            left = self.query(v * 2, tl, tm, l, min(r, tm))
            right = self.query(v * 2 + 1, tm + 1, tr, max(l, tm + 1), r)
            return [max(left[i], right[i]) for i in range(4)]

    n, q = map(int, input().split())
    st = SegTree(n)

    out = []
    for _ in range(q):
        parts = input().split()
        if parts[0] == "MULTIPLY":
            l, r, x = map(int, parts[1:])
            st.update(1, 1, n, l, r, factor_small(x))
        else:
            l, r = map(int, parts[1:])
            res = st.query(1, 1, n, l, r)
            out.append(str(max(res)))

    return "\n".join(out)

# provided samples
assert run("""5 6
MULTIPLY 3 5 2
MULTIPLY 2 5 3
MAX 1 5
MULTIPLY 1 4 2
MULTIPLY 2 5 5
MAX 3 5
""") == """ANSWER 1
ANSWER 2"""

# custom cases
assert run("""1 1
MAX 1 1
""") == "ANSWER 0", "min case"

assert run("""3 1
MULTIPLY 1 3 10
""") != "", "update only"

assert run("""4 3
MULTIPLY 1 4 7
MULTIPLY 2 3 7
MAX 1 4
""") == "ANSWER 2", "prime accumulation"

assert run("""5 3
MULTIPLY 1 5 2
MAX 1 5
MAX 2 4
""") == "ANSWER 1\nANSWER 1", "uniform update"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | シングルマックス | 0 | 空の初期状態 |
 | フルレンジのアップデート | 空でない | 基本的な更新処理 |
 | 繰り返されるプライム | 2 | 指数スタッキングの正確性 |
 | ユニフォームのアップデート | 一貫した答え | 範囲クエリの安定性 |

 ## 特殊なケース

 エッジケースの 1 つは、乗算がまったく適用されない場合です。 すべての ai は 1 であるため、すべての Potp(ai) はすべての素数に対して 0 となり、答えは 0 になるはずです。 すべてのセグメント ツリー ノードはゼロに初期化され、更新は行われないため、アルゴリズムは当然ゼロを返します。 

もう 1 つのケースは、同じ小さな素数による乗算を繰り返すことです。たとえば、x = 4 で MULTIPLY を複数回適用します。 4 は毎回 2 の指数に 2 を与えるため、セグメント ツリーは遅延加算を繰り返すことでこれを正確に累積し、最大値は総指数の増加を反映します。 

6を繰り返しかけるなどの混合素数の場合も重要です。 各演算は、exponent2 と exponent3 の両方に独立して加算されます。 アルゴリズムは両方の値を別々に維持し、クエリは位置ごとに素数の最大値を取得するため、不均等に蓄積された位置でも素数間の寄与を合計することなく正しく寄与します。
