---
title: "CF 102268D - 日付"
description: "暦日は (t) 日あり、日 (x) には最大で (ax) 日をホストできます。 各女の子は最大 1 回デートすることができ、女の子 (i) は (li) から (ri) までの任意の日を受け入れます。 彼女とのデートがその答えに貢献します。"
date: "2026-08-19T04:18:45+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102268
codeforces_index: "D"
codeforces_contest_name: "300iq Contest 1"
rating: 0
weight: 102268
solve_time_s: 1010
verified: false
draft: false
---

[CF 102268D - 日付](https://codeforces.com/problemset/problem/102268/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 16 分 50 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 暦日は (t) 日あり、日 (x) には最大で (a_x) 日をホストできます。 各女の子は最大 1 回デートすることができ、女の子 (i) は (l_i) から (r_i) までの任意の日を受け入れます。 彼女とのデートがその答えに貢献します (p_i)。 課題は、女の子のサブセットを選択し、合計の喜びが最大になるように、選ばれたすべての女の子に能力を尊重した有効な日を割り当てることです。 

異常な構造条件は、両方のエンドポイントがソートされていることです。 

[
 l_1\le l_2\le\cdots\le l_n,\qquad
 r_1\le r_2\le\cdots\le r_n。 
】

 この条件は、一致制約を圧縮可能にするための鍵となります。 これがなければ、自然な定式化は大規模な重み付きマッチング問題となり、(n,t\le 300000) にとってはあまりにも高価すぎます。 

境界には、おおよそ (O((n+t)\log n)) 個の演算の余地が残されていますが、二次作業の余地はありません。 すべての女の子のペアをチェックするプロシージャには、最大サイズで (9\cdot10^{10}) 程度の (O(n^2)) 個の操作がすでに含まれています。 サブセットは (2^n) 個あるため、すべてのサブセットを列挙することは明らかに不可能です。 最終的な解決策は、ソートと 2 つの遅延セグメント ツリーを使用し、(O((n+t)+n\log n)) の時間を与えます。 

いくつかの特殊なケースは、誤って処理されやすいものです。 

利用可能な容量がない場合、答えはゼロでなければなりません。```
1 1
0
1 1 10
```答えは`0`。 各間隔自体が空でないかどうかだけをチェックする不注意な解決策では、女の子が誤って選択されてしまいます。 

数人の女の子がまったく同じ間隔を持つことができますが、容量は依然として共有されます。 例えば：```
2 1
1
1 1 5
1 1 4
```答えは`5`、 ない`9`。 どちらの女の子も使用できるスロットは 1 つだけです。 

選択した間隔を個別にチェックするだけでは不十分です。 考慮する：```
2 2
1 0
1 1 100
1 2 99
```各女の子には個別に少なくとも 1 単位のキャパシティーを含む間隔がありますが、それらをまとめてスケジュールすることはできません。 最初の女の子は日 (1) の唯一のスロットを消費しますが、日 (2) のキャパシティーがゼロであるため、2 番目の女の子は日 (1) のみを使用できます。 答えは`100`。 これはまさに、ホールの定理によって捉えられる種類のグローバルな状態です。 

最後に、境界は包括的に処理する必要があります。 で```
2 2
0 1
1 2 5
2 2 4
```どちらの女の子も日 (2) しか使用できないため、どちらか 1 つだけを選択でき、答えは次のようになります。`5`。 誤って区間をハーフオープンとして扱うと、マッチング制約が変更されてしまいます。 

公式声明では、エンドポイントの順序と、(O(n\log n)) 要件を推進する (300000) 境界が確認されています。 

## アプローチ

 強引なアプローチは、女の子のサブセットを列挙し、それらの女の子を個別の利用可能な日付スロットに割り当てることができるかどうかを判断し、実行可能なサブセット間で最大の喜びを維持することです。 それは正しいです。なぜなら、女の子のあらゆる選択肢が考慮されているからです。 実現可能性チェック自体が、選択された間隔を利用可能なスロットに貪欲に割り当てる場合、1 つのサブセットはすでに (O(n+t)) の作業を必要とする可能性があり、最悪の場合は (O(2^n(n+t))) になります。 実現可能コストを無視しても、(2^{300000}) 個のサブセットは処理できません。 

次に自然なアイデアは、最大コスト フローです。 毎日の容量コピーを作成し、各女の子をインターバル内の毎日に接続します。 これは問題を正しくモデル化していますが、グラフには (\Theta(nt)) エッジが含まれる可能性があり、一般的なマッチング アルゴリズムやフロー アルゴリズムは非常に遅すぎます。 

有用な観察は、実行可能な女の子の集合がマトロイドを形成するということです。 1 日あたりのキャパシティのすべての単位を個別のスロットとして考えてください。 女の子は、インターバル内の 1 日に対応する任意のスロットにマッチングできます。 女の子のセットは、これらの女の子を個別のスロットに一致させることができた場合に正確に実現可能です。 このような一致可能なサブセットは、横断マトロイドを形成します。 マトロイドの重み付き貪欲定理は、喜びの降順に女の子を処理し、選択されたセットを実行可能に保つために女の子を追加するときに正確に女の子を受け入れることができることを示しています。 これは中心的な最適化ステップです。 

残りの問題は、実現可能性を迅速にテストすることです。 

ホールの定理によれば、すべての女の子のコレクションに、選択された女の子の数と少なくとも同じ数の利用可能な日枠が許可日の和集合にある場合に、選択されたセットがスケジュール可能になります。 許可されるセットはすべて間隔であり、両方のエンドポイント シーケンスが減少しないため、ガール インデックスの連続ブロックをチェックするだけで十分です。 これにより、一致条件がプレフィックスに関する不等式に変わります。 

女の子 (i) がすでに受け入れられている場合は (b_i) を (1)、そうでない場合は (0) とします。 しましょう

 [
 A_x=\sum_{j=1}^{x}a_j
 】

 をプレフィックス容量とし、

 [
 B_x=\sum_{j=1}^{x}b_j
 】

 選択された女の子のプレフィックス番号になります。 

女の子のブロック (L,\ldots,R) にとって、彼女たちが考えられるすべての日は ([l_L,r_R]) の中にあります。 ホールの状態はこうなります

 [
 B_R-B_{L-1}\le A_{r_R}-A_{l_L-1}。 
】

 並べ替えると

 [
 B_R-A_{r_R}\le B_{L-1}-A_{l_L-1}。 
】

 定義する

 [
 c_R=B_R-A_{r_R}
 】

 そして

 [
 d_L=B_{L-1}-A_{l_L-1}。 
】

 実現可能な集合全体の特徴は次のとおりです。 

[
 c_R\le d_L\qquad\text{すべての }L\le R について。 
】

 これはまさに、この問題の既知の解決策で使用される削減です。 

女の子 (x) を考えているとします。 彼女を受け入れる前に、(b_x=0)。 彼女を追加すると、(i\ge x) で (B_i) ごとに増加するため、(i\ge x) で (c_i) ごとに (1) ずつ増加します。 また、(d_i) には (B_{i-1}) が含まれるため、(i>x) の場合にのみ (d_i) が増加します。 したがって、より困難になる可能性がある唯一の新たな不平等は、

 [
 L\le x\le R.
 】

 これらの不等式では、(c_R) は (1) 増加しますが、(d_L) は増加しません。 したがって、女の子 (x) は、次のようなときに受け入れられます。 

[
 \max_{R\ge x}c_R < \min_{L\le x}d_L。 
】

 彼女を受け入れた後、サフィックス (c_x,\ldots,c_n) に (1) を追加し、サフィックス (d_{x+1},\ldots,d_n) に (1) を追加します。 

したがって、(c) の範囲追加でサフィックスの最大値を維持する 1 つのセグメント ツリーと、(d) の範囲追加でプレフィックスの最小値を維持する別のセグメント ツリーが必要です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(2^n(n+t))) | (O(n+t)) | 遅すぎる |
 | 重み付けマッチング/フロー | 全区間グラフでは少なくとも超線形 | 潜在的に (O(nt)) | 遅すぎる |
 | 貪欲 + 2 つの遅延セグメント ツリー | (O(t+n+n\log n)) | (O(t+n)) | 承認済み |

 ## アルゴリズムのチュートリアル

1. プレフィックス容量 (A_x) を計算します。ここで (A_x) は、日 (1) から (x) までの日付スロットの合計数です。 これにより、すべての容量間隔 ([u,v]) が一定時間で (A_v-A_{u-1}) として評価されます。 
2. すべての女の子 (i) について、最初は女の子が選択されていないため (B_i=0)。 ストア

 [
 c_i=-A_{r_i}
 】

 そして

 [
 d_i=-A_{l_i-1}。 
】

 これらは、すべて (b_i=0) での以前の定義とまったく同じです。 

1. 喜びの少ない順に女の子を分類します。 選択されたセットは横断マトロイド内で独立しているため、この順序ですべての実行可能な女の子を受け入れると、最大重みの実行可能なセットが生成されます。 
2. 次の女の子 (x) を考えます。 クエリ

 [
 C=\max_{i\ge x}c_i
 】

 最初のセグメントツリーから、そして

 [
 D=\min_{i\le x}d_i
 】

 2 番目のセグメント ツリーから。 

1. (C<D) の場合、女の子 (x) を受け入れます。 彼女を受け入れると、関連するすべての (c_i) がちょうど 1 だけ増加するため、厳密な不等式が必要です。 古い不等式には少なくとも 1 単位のスラックが必要です。 
2. 女の子 (x) が受け入れられた場合、(c_x,\ldots,c_n) に (1) を追加します。 これは、(i\ge x) を持つすべての (B_i) が 1 ずつ増加することを反映しています。 
3. (1) を (d_{x+1},\ldots,d_n) に追加します。 値 (d_i) には (B_{i-1}) が含まれているため、(i-1\ge x) の場合にのみ変化します。 (d_x) は意図的に更新されていません。 
4. 女の子の喜びを答えに加えて、次の女の子に進みます。 女の子がテストに失敗した場合は、両方の木を変更せずに先に進みます。 

不変条件は、喜びでソートされた順序の接頭辞を処理した後、2 つのツリーが受け入れられた女の子の (c_i) と (d_i) の値を正確に表すということです。 すべてのペア (L\le R) の条件はすでに満たされています。 新しい女の子を考慮すると、(L\le x\le R) の不平等だけがさらに厳しくなる可能性があります。 これらの不等式は、右側の最大の古い (c_R) が左側の最小の古い (d_L) よりも厳密に小さい場合に、挿入後に有効になります。 したがって、受け入れられたすべてのセットは実行可能であり、拒否されたすべての女の子が追加された場合、ホールの条件に違反することになります。 実現可能性システムはマトロイドであり、女の子は重量を減らして処理されるため、結果として得られるセットは最大の総合的な喜びを持っています。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

INF = 10**30

class SegmentTree:
    def __init__(self, values, is_max):
        self.n = len(values)
        self.is_max = is_max
        size = 4 * self.n + 5
        self.val = array('q', [0]) * size
        self.tag = array('q', [0]) * size
        self.values = values
        self._build(1, 0, self.n - 1)

    def _merge(self, x, y):
        if self.is_max:
            return x if x > y else y
        return x if x < y else y

    def _build(self, v, l, r):
        if l == r:
            self.val[v] = self.values[l]
            return
        m = (l + r) >> 1
        self._build(v << 1, l, m)
        self._build(v << 1 | 1, m + 1, r)
        self.val[v] = self._merge(
            self.val[v << 1],
            self.val[v << 1 | 1]
        )

    def update_suffix(self, pos, delta=1):
        if pos >= self.n:
            return
        self._update(1, 0, self.n - 1, pos, delta)

    def _update(self, v, l, r, pos, delta):
        if pos <= l:
            self.val[v] += delta
            self.tag[v] += delta
            return

        m = (l + r) >> 1

        if pos <= m:
            self._update(v << 1, l, m, pos, delta)
        self._update(v << 1 | 1, m + 1, r, pos, delta)

        self.val[v] = self._merge(
            self.val[v << 1],
            self.val[v << 1 | 1]
        ) + self.tag[v]

    def query_suffix(self, pos):
        if pos >= self.n:
            return -INF if self.is_max else INF
        return self._query_suffix(1, 0, self.n - 1, pos)

    def _query_suffix(self, v, l, r, pos):
        if pos <= l:
            return self.val[v]

        m = (l + r) >> 1

        if pos <= m:
            left = self._query_suffix(v << 1, l, m, pos)
            right = self.val[v << 1 | 1]
            return self._merge(left, right) + self.tag[v]

        return self._query_suffix(v << 1 | 1, m + 1, r, pos) + self.tag[v]

    def query_prefix(self, pos):
        if pos < 0:
            return -INF if self.is_max else INF
        if pos >= self.n - 1:
            return self.val[1]
        return self._query_prefix(1, 0, self.n - 1, pos)

    def _query_prefix(self, v, l, r, pos):
        if r <= pos:
            return self.val[v]

        m = (l + r) >> 1

        if pos <= m:
            return self._query_prefix(v << 1, l, m, pos) + self.tag[v]

        left = self.val[v << 1]
        right = self._query_prefix(v << 1 | 1, m + 1, r, pos)
        return self._merge(left, right) + self.tag[v]

def solve():
    n, t = map(int, input().split())

    pref = [0] * (t + 1)
    cur = 0
    a = list(map(int, input().split()))

    for i, x in enumerate(a, 1):
        cur += x
        pref[i] = cur

    order = [None] * n
    c = [0] * n
    d = [0] * n

    for i in range(n):
        l, r, p = map(int, input().split())
        order[i] = (p, i)
        c[i] = -pref[r]
        d[i] = -pref[l - 1]

    order.sort(reverse=True)

    tree_c = SegmentTree(c, True)
    tree_d = SegmentTree(d, False)

    del c
    del d
    del pref
    del a

    ans = 0

    for p, x in order:
        right_c = tree_c.query_suffix(x)
        left_d = tree_d.query_prefix(x)

        if right_c < left_d:
            tree_c.update_suffix(x)
            tree_d.update_suffix(x + 1)
            ans += p

    print(ans)

if __name__ == "__main__":
    solve()
```すべての女の子のイニシャル (c_i) と (d_i) は、彼女の右側または左側のエンドポイントの直前までの総容量のみに依存するため、プレフィックス配列が最初に構築されます。 これらの値が初期化されると、元の (l_i) と (r_i) は必要なくなります。 

の`order`配列ストアのみ`(pleasure, index)`。 逆に並べ替えると、喜びは減少します。これは、マトロイド貪欲アルゴリズムが要求する順序とまったく同じです。 Python の整数は任意の精度であるため、プレジャーとプレフィックスの合計容量は 32 ビット オーバーフローの危険がありません。 

セグメント ツリーは、少し珍しい遅延伝播スタイルを使用します。`val[v]`ノード (v) に属する遅延更新がすでに含まれていますが、`tag[v]`子に組み込まれていない金額を記録します。 子に降順する場合、親のタグが子の戻り値に追加されます。 親を再構築すると、マージされた子の値が親のタグによって増加します。 これにより、明示的なプッシュ操作が回避され、サフィックスの更新が特にコンパクトになります。 

最初のツリーは、(c) の範囲加算、範囲最大の構造です。 2 番目は、(d) の範囲加算、範囲最小構造です。 候補テストは、次の 2 つの範囲を正確にクエリします。 

[
 \max_{R\ge x}c_R < \min_{L\le x}d_L。 
】

 承認後、(c) のサフィックス更新は (x) から開始され、(d) のサフィックス更新は (x+1) から開始されます。 この 1 つのインデックスの違いは不可欠であり、実装における最も可能性の高い 1 つ違いの間違いです。 

## 実用的な例

 ### サンプル 1

 実際のサンプル入力は次のとおりです。```
3 5
0 1 0 1 0
1 2 2
2 4 1
3 5 5
```プレフィックス容量は次のとおりです。 

[
 A=[0,0,1,1,2,2]。 
】

 初期値 (c_i=-A_{r_i}) と (d_i=-A_{l_i-1}) は次のとおりです。 

[
 c=[-1,-2,-2]、
 \qquad
 d=[0,0,-1]。 
】

 女の子は楽しい順に考慮されます (3、1、2)。 

| 女の子 | 楽しみ | (x) | (\max c[x..]) | (\min d[..x]) | 決定 | 答え |
 | --- | --- | --- | --- | --- | --- | --- |
 | 3 | 5 | 2 | (-2) | (-1) | 同意する | 5 |
 | 1 | 2 | 0 | (-1) | (0) | 同意する | 7 |
 | 2 | 1 | 1 | (0) | (0) | 拒否 | 7 |

 女の子(3)の場合は(-2<-1)なので挿入するのに十分な余裕があります。 彼女を受け入れると、(c_3) が 1 増加します。 女の子(1)も当てはまります。 女の子 (2) を考慮すると、2 つの辺は等しいため、彼女を挿入すると、ホール不等式が 1 単位で失敗します。 最終的な答えは、`7`。 これは、受け入れテストが厳しい理由を示しています。 

### 境界が多い例

 次のことを考慮してください。```
2 2
1 0
1 1 100
1 2 99
```プレフィックスの容量は (A=[0,1,1]) です。 当初は、

 [
 c=[-1,-1],\qquad d=[0,0]。 
】

 | 女の子 | 楽しみ | (x) | (\max c[x..]) | (\min d[..x]) | 決定 | 答え |
 | --- | --- | --- | --- | --- | --- | --- |
 | 1 | 100 | 0 | (-1) | (0) | 同意する | 100 |
 | 2 | 99 | 1 | (0) | (0) | 拒否 | 100 |

 女の子(1)を受け入れた後、唯一の容量スロットが消費されます。 女の子 (2) は、日 (2) のキャパシティがゼロであるため、同じ有効キャパシティと重複します。そのため、2 番目の候補者は同等に不合格となります。 出力は`100`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(t+n+n\log n)) | プレフィックスの合計には (O(t))、ソートには (O(n\log n)) がかかり、各女の子は一定数の (O(\log n)) 個のセグメント ツリー操作を実行します。 
| スペース | (O(t+n)) | プレフィックス容量、ソートされた候補、および 2 つのセグメント ツリーはすべて線形メモリを使用します。 

(n,t\le300000) の場合、支配項は (O(n\log n)) となり、2 秒のターゲットに適しています。 セグメント ツリーは、Python 実装で通常の Python 整数配列ではなくコンパクトな 64 ビット配列を使用し、メモリ使用量を制御します。 

## テストケース```python
import sys
import io
from array import array

input = sys.stdin.readline

INF = 10**30

class SegmentTree:
    def __init__(self, values, is_max):
        self.n = len(values)
        self.is_max = is_max
        size = 4 * self.n + 5
        self.val = array('q', [0]) * size
        self.tag = array('q', [0]) * size
        self.values = values
        self._build(1, 0, self.n - 1)

    def _merge(self, x, y):
        if self.is_max:
            return x if x > y else y
        return x if x < y else y

    def _build(self, v, l, r):
        if l == r:
            self.val[v] = self.values[l]
            return
        m = (l + r) >> 1
        self._build(v << 1, l, m)
        self._build(v << 1 | 1, m + 1, r)
        self.val[v] = self._merge(
            self.val[v << 1],
            self.val[v << 1 | 1]
        )

    def update_suffix(self, pos):
        if pos >= self.n:
            return
        self._update(1, 0, self.n - 1, pos)

    def _update(self, v, l, r, pos):
        if pos <= l:
            self.val[v] += 1
            self.tag[v] += 1
            return

        m = (l + r) >> 1

        if pos <= m:
            self._update(v << 1, l, m, pos)
        self._update(v << 1 | 1, m + 1, r, pos)

        self.val[v] = self._merge(
            self.val[v << 1],
            self.val[v << 1 | 1]
        ) + self.tag[v]

    def query_suffix(self, pos):
        if pos >= self.n:
            return -INF if self.is_max else INF
        return self._query_suffix(1, 0, self.n - 1, pos)

    def _query_suffix(self, v, l, r, pos):
        if pos <= l:
            return self.val[v]

        m = (l + r) >> 1

        if pos <= m:
            left = self._query_suffix(v << 1, l, m, pos)
            right = self.val[v << 1 | 1]
            return self._merge(left, right) + self.tag[v]

        return self._query_suffix(v << 1 | 1, m + 1, r, pos) + self.tag[v]

    def query_prefix(self, pos):
        if pos < 0:
            return -INF if self.is_max else INF
        if pos >= self.n - 1:
            return self.val[1]
        return self._query_prefix(1, 0, self.n - 1, pos)

    def _query_prefix(self, v, l, r, pos):
        if r <= pos:
            return self.val[v]

        m = (l + r) >> 1

        if pos <= m:
            return self._query_prefix(v << 1, l, m, pos) + self.tag[v]

        left = self.val[v << 1]
        right = self._query_prefix(v << 1 | 1, m + 1, r, pos)
        return self._merge(left, right) + self.tag[v]

def solve_case(inp):
    data = iter(inp.split())
    n = int(next(data))
    t = int(next(data))

    pref = [0] * (t + 1)
    for i in range(1, t + 1):
        pref[i] = pref[i - 1] + int(next(data))

    order = [None] * n
    c = [0] * n
    d = [0] * n

    for i in range(n):
        l = int(next(data))
        r = int(next(data))
        p = int(next(data))
        order[i] = (p, i)
        c[i] = -pref[r]
        d[i] = -pref[l - 1]

    order.sort(reverse=True)

    tc = SegmentTree(c, True)
    td = SegmentTree(d, False)

    ans = 0

    for p, x in order:
        if tc.query_suffix(x) < td.query_prefix(x):
            tc.update_suffix(x)
            td.update_suffix(x + 1)
            ans += p

    return str(ans)

def run(inp: str) -> str:
    return solve_case(inp)

# Provided sample.
assert run("""\
3 5
0 1 0 1 0
1 2 2
2 4 1
3 5 5
""") == "7", "sample 1"

# Minimum-size case with zero capacity.
assert run("""\
1 1
0
1 1 5
""") == "0", "minimum size and zero capacity"

# All girls have the same interval and compete for one slot.
assert run("""\
2 1
1
1 1 5
1 1 4
""") == "5", "shared single capacity"

# Boundary case where day 2 has no capacity.
assert run("""\
2 2
1 0
1 1 100
1 2 99
""") == "100", "Hall constraint across a boundary"

# All values equal, with enough total capacity for every girl.
assert run("""\
4 2
2 2
1 2 10
1 2 10
1 2 10
1 2 10
""") == "40", "all equal values"

# Maximum-size construction.
n = 300000
parts = [f"{n} {n}\n", ("1 " * (n - 1)) + "1\n"]
for i in range(1, n + 1):
    parts.append(f"{i} {i} 1\n")

large_input = "".join(parts)
assert run(large_input) == "300000", "maximum-size instance"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1 / 0 / 1 1 5`|`0`| 最小サイズとゼロ容量 |
 |`2 1 / 1 / 1 1 5 / 1 1 4`|`5`| 複数の女の子が 1 つのスロットを争う |
 |`2 2 / 1 0 / 1 1 100 / 1 2 99`|`100`| グローバル ホール制約とエンドポイント処理 |
 |`4 2 / 2 2 / four [1,2] intervals`|`40`| 等間隔、等しい楽しみ、十分な容量 |
 | (n=t=300000)、ユニット容量と ([i,i]) 間隔 |`300000`| 最大入力サイズとリニアメモリ動作 |

 ## 特殊なケース

 すべての容量がゼロの場合、すべての初期値 (c_i) と (d_i) はゼロになります。 位置 (x) の女の子の場合、テストは接尾辞の最大値 0 と接頭語の最小値 0 を比較します。 (0<0) は false であるため、誰も受け入れられません。 のために```
1 1
0
1 1 10
```アルゴリズムが戻ります`0`、必要に応じて正確に。 

When several girls share one day, the first one can be accepted, but the next one must fail. のために```
2 1
1
1 1 5
1 1 4
```最初の候補は (-1<0) を満たします。 彼女を受け入れた後、関連する (c) 値はゼロになります。 2 番目の候補は (0<0) を false とみなし、拒否されます。 結果は`5`。 

グローバル ホール制約は次のように表示されます。```
2 2
1 0
1 1 100
1 2 99
```最初の女の子を受け入れた後、2 番目の候補者は 1 ベースのインデックス付けで (x=2) になります。 右側の最大値 (c) と左側の最小値 (d) が等しくなるため、候補は拒否されます。 セグメント ツリーは、各女の子が個別に実行可能であるように見えても、2 人の女の子を合わせると、日数で提供できるよりも多くのキャパシティを必要とすることを検出しています。 

等間隔の場合、アルゴリズムは個別のエンドポイントに依存しません。 それぞれ間隔 ([1,2])、容量 (2,2)、および喜び (10) を持つ 4 人の女の子について考えてみましょう。 空き枠がちょうど 4 つあるため、すべての候補者が合格します。 サフィックスの更新により、承認された 4 人の女の子が正しく累積され、答えは次のようになります。`40`。 

右側の境界では、最後の女の子を受け入れても (d_n) を更新してはなりません。これは、(d_n) には (B_n) ではなく (B_{n-1}) が含まれるためです。 これが実装が実行される理由です`tree_d.update_suffix(x + 1)`それよりも`tree_d.update_suffix(x)`。 最終順位の女の子の場合、この更新は完全にスキップされます。 この境界により、接頭辞式 (B_{L-1}-A_{l_L-1}) がホール不等式と一致します。 

最大サイズのケースには (300,000) 人の女の子と (300,000) 日があり、毎日 1 単位の収容力があり、女の子 (i) は日 (i) に制限されます。 すべての女の子は個別にスケジュールできるため、すべて (300,000) 人の女の子が受け入れられ、答えは次のようになります。`300000`。 このアルゴリズムは、セグメント ツリーのストレージを線形に保ちながら、これを (O(n\log n)) 時間で処理します。
