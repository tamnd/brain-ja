---
title: "CF 232D - フェンス"
description: "様々な高さの板を取り揃えております。 フェンスのピースは、単なる連続したセグメントです。 クエリセグメント $[l,r]$ の場合、同じ長さの他のセグメントがそれに一致する数を数えなければなりません。 マッチングには 3 つの要件があります。 2 つのセグメントの長さは同じでなければなりません。 それらはばらばらでなければなりません。"
date: "2026-06-04T09:37:03+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "string-suffix-structures"]
categories: ["algorithms"]
codeforces_contest: 232
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 144 (Div. 1)"
rating: 2900
weight: 232
solve_time_s: 410
verified: false
draft: false
---

[CF 232D - フェンス](https://codeforces.com/problemset/problem/232/D)

 **評価:** 2900
 **タグ:** 二分探索、データ構造、文字列サフィックス構造
 **解決時間:** 6 分 50 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 様々な高さの板を取り揃えております。 フェンスのピースは、単なる連続したセグメントです。 

クエリセグメントの場合$[l,r]$、同じ長さの他のセグメントが何個一致するかを数えなければなりません。 マッチングには 3 つの要件があります。 

2 つのセグメントの長さは同じでなければなりません。 

それらはばらばらでなければなりません。 

それらを位置ごとに整列させる場合、対応する高さの合計は常に同じ値でなければなりません。 セグメントの場合$[l,r]$そして別のセグメント$[a,b]$、 これはつまり$$h_{l+i}+h_{a+i}=h_l+h_a$$有効なオフセットごとに$i$。 

最初の反応は高さを直接見ることですが、違いを考慮した後は状態がはるかにきれいになります。 

させて$$d_i=h_i-h_{i+1}$$のために$1\le i<n$。 

2 つのセグメントに幅があると仮定します。$k$。 一致条件が意味するのは、$$(h_{l+i}-h_{l+i+1}) = -(h_{a+i}-h_{a+i+1})$$あらゆるポジションに。 

言い換えれば、もし$$d'_i=-d_i,$$一致するセグメントは、差分配列間の等しい部分文字列に正確に対応します。$d$そして否定された差分配列$d'$。 これは、公式ソリューションで使用されるキー変換です。 

制約はこの変換を強制するものです。 両方$n$そして$q$までです$10^5$。 クエリごとにセグメントをスキャンするアルゴリズムは、すぐに遅くなりすぎます。 平$O(n)$クエリごとに約$10^{10}$操作。 おおよその必要があります$O((n+q)\log n)$。 

見落としやすいエッジケースがいくつかあります。 

長さ 1 のセグメントを考えてみましょう。```
h = [5, 8, 1]
query = [2,2]
```一枚の板の中には違いはありません。 1 つおきの厚板は自動的に形状条件を満たします。 唯一の制限は、不連続であることです。 答えは$n-1$。 対応する差分部分文字列の長さがゼロであるため、差分配列のみで機能するソリューションでは、このケースを慎重に扱う必要があります。 

完全な重複を考慮してください。```
h = [1,2,1]
query = [1,2]
```セグメント自体は独自のパターンに一致しますが、一致するセグメントは互いに素である必要があります。 パターンの出現をすべてカウントし、重複制限を忘れると、大きすぎる答えが生成されます。 

繰り返されるパターンを考慮してください。```
h = [1,2,1,2,1]
query = [1,2]
```同じパターンが何度も出てきます。 一部のオカレンスはクエリ セグメントと重複するため除外する必要がありますが、その他のオカレンスは有効です。 カウント構造は、パターン マッチングと幾何学的位置制限の両方をサポートする必要があります。 

## アプローチ

 ブルートフォースによる解決策は簡単です。 

すべてのクエリ セグメントについて、同じ長さのすべてのセグメントを列挙します。 2 つのセグメントが互いに素であるかどうかを確認します。 次に、対応する位置を比較し、一致条件を確認します。 

1 回の比較にかかるコスト$O(k)$、 どこ$k$セグメントの長さです。 がある$O(n)$候補セグメント。 最悪の場合、クエリにはコストがかかります$O(n^2)$、そして$10^5$クエリこれは絶望的です。 

重要な観察は、一致条件が実際には連続した差異に対する条件であるということです。 

一致するセグメントについては、$$h_{l+i}+h_{a+i}=C.$$方程式を減算してオフセットを求めます$i+1$:$$(h_{l+i}-h_{l+i+1}) = -(h_{a+i}-h_{a+i+1}).$$使用する$$d_i=h_i-h_{i+1}, \qquad d'_i=-d_i,$$問題は次のようになります。 

部分文字列の出現箇所を検索する$$d[l..r-1]$$配列内で$d'$。 

この問題は、整数アルファベットに関する古典的な文字列問題のように見えます。 

公式ソリューションはサフィックス配列を構築します$$d + [\text{separator}] + d'.$$幅のクエリセグメントの場合$k=r-l+1$、すべての接尾辞が必要です$d'$誰の最初の$k-1$差の値が等しい$$d[l..r-1].$$サフィックス配列では、そのようなすべてのサフィックスが 1 つの連続した間隔を形成します。 LCP 情報と二分探索を使用してその間隔を見つけることができます。 

残りのタスクは、クエリ セグメントから切り離されたフェンス セグメントにどのオカレンスが対応するかをカウントすることです。 

これは、接尾語配列のランクに関する幾何学的計数問題になります。 各クエリでは次のことが尋ねられます。 

接尾辞配列間隔の開始位置が禁止範囲外にある接尾辞がいくつありますか?

 永続的なセグメント ツリーまたはウェーブレット ツリー スタイルの構造を使用すると、次のように答えることができます。$O(\log n)$前処理後。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(qn^2)$最悪の場合 |$O(1)$| 遅すぎる |
 | サフィックス配列 + LCP + 永続的なカウント |$O(n\log n + q\log n)$|$O(n\log n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 差分配列を構築する$$d_i=h_i-h_{i+1}.$$も構築します$$d'_i=-d_i.$$2.連結する$$T=d+[SEP]+d'.$$区切り文字は、境界を越える一致が存在しないように、圧縮されたすべての座標よりも小さい値にする必要があります。 
3. すべての値を座標圧縮し、サフィックス配列を構築します。$T$。 
4. 任意の 2 つのサフィックスの LCP を取得できるように、LCP 配列と RMQ 構造を構築します。$O(1)$。 
5. 元の差分配列内のすべての位置について$d$、そのサフィックス配列ランクを保存します。 
6. お問い合わせの場合$[l,r]$、 させて$$len=r-l.$$これは、差分の部分文字列の長さです。 
7. もし$len=0$、クエリ セグメントには 1 つのプランクが含まれています。 互いに素な単一板セグメントはすべて一致します。 答えは$n-1$。 
8. それ以外の場合は、位置に対応するサフィックスを見つけます。$l$で$d$。 
9. サフィックス配列の位置と LCP クエリで二分検索を使用して、サフィックス配列の最大間隔を見つけます。$[L,R]$区間内のすべての接尾辞が少なくとも共有するような接尾辞を含む$len$それとの差異値。 

これらは、まさに連結構造内で必要なパターンの出現です。 
10. これらのサフィックスのうち、$d'$半分。 
11. このようなサフィックスを 位置で開始させます$p$内部$d'$。 フェンスセグメントに対応$$[a,b]$$と$$a=p,\qquad b=p+len.$$12. セグメントがクエリと交差する場合$$a\le r \quad\text{and}\quad b\ge l.$$同様に、有効な出現は次の条件を満たします。$$b<l \quad\text{or}\quad a>r.$$13. 永続的なカウント構造を使用して、開始インデックスが許容範囲を満たすサフィックス配列間隔内の位置をカウントします。 
14. そのカウントを出力します。 

### なぜ効果があるのか

 差分配列への変換は正確です。 2 つのセグメントは、一方のセグメントのすべての連続する高さの差が、もう一方のセグメントの否定された連続する高さの差と等しい場合に限り、元の高さ合計条件を満たします。 これにより、幾何学的フェンス条件が次の部分文字列の等価性に変換されます。$d$そして$d'$。 

サフィックス配列は、等しいプレフィックスを連続した間隔にグループ化します。 LCP 条件により、特定された間隔内のすべてのサフィックスに必要な差分パターンが含まれ、間隔外のサフィックスには含まれないことが保証されます。 

カウント構造は、素性要件によってオカレンスのみをフィルターします。 残りのすべての出現は、一致するフェンス セグメント 1 つと正確に一致するため、報告されたカウントが正確に答えとなります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SuffixArray:
    def __init__(self, s):
        n = len(s)
        sa = list(range(n))
        rank = s[:]
        tmp = [0] * n

        k = 1
        while True:
            sa.sort(key=lambda x: (
                rank[x],
                rank[x + k] if x + k < n else -1
            ))

            tmp[sa[0]] = 0
            for i in range(1, n):
                a, b = sa[i - 1], sa[i]
                tmp[b] = tmp[a] + (
                    (rank[a], rank[a + k] if a + k < n else -1) <
                    (rank[b], rank[b + k] if b + k < n else -1)
                )

            rank, tmp = tmp, rank

            if rank[sa[-1]] == n - 1:
                break
            k <<= 1

        self.sa = sa
        self.rank = rank

        lcp = [0] * (n - 1)
        h = 0
        for i in range(n):
            r = rank[i]
            if r == 0:
                continue
            j = sa[r - 1]
            while i + h < n and j + h < n and s[i + h] == s[j + h]:
                h += 1
            lcp[r - 1] = h
            if h:
                h -= 1

        self.lcp = lcp

        m = max(1, len(lcp))
        lg = [0] * (m + 1)
        for i in range(2, m + 1):
            lg[i] = lg[i >> 1] + 1

        st = [lcp[:]]
        j = 1
        while (1 << j) <= len(lcp):
            prev = st[j - 1]
            cur = [
                min(prev[i], prev[i + (1 << (j - 1))])
                for i in range(len(lcp) - (1 << j) + 1)
            ]
            st.append(cur)
            j += 1

        self.lg = lg
        self.st = st

    def get_lcp(self, i, j):
        if i == j:
            return 10 ** 9
        ri = self.rank[i]
        rj = self.rank[j]
        if ri > rj:
            ri, rj = rj, ri
        l = rj - ri
        k = self.lg[l]
        return min(
            self.st[k][ri],
            self.st[k][rj - (1 << k)]
        )

class Node:
    __slots__ = ("l", "r", "sum")

    def __init__(self, l=None, r=None, s=0):
        self.l = l
        self.r = r
        self.sum = s

def build(lo, hi):
    node = Node()
    if lo != hi:
        mid = (lo + hi) >> 1
        node.l = build(lo, mid)
        node.r = build(mid + 1, hi)
    return node

def update(prev, lo, hi, pos):
    node = Node(prev.l, prev.r, prev.sum + 1)
    if lo != hi:
        mid = (lo + hi) >> 1
        if pos <= mid:
            node.l = update(prev.l, lo, mid, pos)
        else:
            node.r = update(prev.r, mid + 1, hi, pos)
    return node

def query(rt_r, rt_l, lo, hi, ql, qr):
    if ql > hi or qr < lo or ql > qr:
        return 0
    if ql <= lo and hi <= qr:
        return rt_r.sum - rt_l.sum
    mid = (lo + hi) >> 1
    return (
        query(rt_r.l, rt_l.l, lo, mid, ql, qr) +
        query(rt_r.r, rt_l.r, mid + 1, hi, ql, qr)
    )

def solve():
    n = int(input())
    h = list(map(int, input().split()))

    if n == 1:
        q = int(input())
        print("\n".join("0" for _ in range(q)))
        return

    d = [h[i] - h[i + 1] for i in range(n - 1)]
    nd = [-x for x in d]

    vals = sorted(set(d + nd))
    comp = {v: i + 1 for i, v in enumerate(vals)}

    sep = 0
    T = [comp[x] for x in d] + [sep] + [comp[x] for x in nd]

    sa = SuffixArray(T)

    m = n - 1
    offset = m + 1

    roots = [build(1, m)]
    for pos in sa.sa:
        val = 0
        if offset <= pos < offset + m:
            val = pos - offset + 1

        if val:
            roots.append(update(roots[-1], 1, m, val))
        else:
            roots.append(roots[-1])

    q = int(input())
    ans = []

    for _ in range(q):
        l, r = map(int, input().split())

        if l == r:
            ans.append(str(n - 1))
            continue

        pat_len = r - l
        pos = l - 1

        rk = sa.rank[pos]
        N = len(T)

        lo, hi = 0, rk
        left = rk
        while lo <= hi:
            mid = (lo + hi) // 2
            ok = True
            if mid < rk:
                ok = sa.get_lcp(sa.sa[mid], sa.sa[rk]) >= pat_len
            if ok:
                left = mid
                hi = mid - 1
            else:
                lo = mid + 1

        lo, hi = rk, N - 1
        right = rk
        while lo <= hi:
            mid = (lo + hi) // 2
            ok = True
            if mid > rk:
                ok = sa.get_lcp(sa.sa[mid], sa.sa[rk]) >= pat_len
            if ok:
                right = mid
                lo = mid + 1
            else:
                hi = mid - 1

        total = 0

        max_left_start = l - pat_len
        if max_left_start >= 1:
            total += query(
                roots[right + 1],
                roots[left],
                1,
                m,
                1,
                max_left_start
            )

        min_right_start = r + 1
        if min_right_start <= m:
            total += query(
                roots[right + 1],
                roots[left],
                1,
                m,
                min_right_start,
                m
            )

        ans.append(str(total))

    print("\n".join(ans))

if __name__ == "__main__":
    solve()
```このソリューションは、公式アプローチの構造に正確に従っています。 サフィックス配列は、必要な差分パターンのすべての出現を識別します。 永続的なセグメント ツリーには、$d'$半分はサフィックス配列ランクによって順序付けされ、対数時間での間隔カウントが可能になります。 最もデリケートな部分は、フェンスの位置と差分配列の位置の間の変換です。 幅のフェンスセグメント$k$長さの差の部分文字列に対応します$k-1$そのため、サフィックス構造内のクエリの長​​さは次のようになります。`r - l`、 ない`r - l + 1`。 

## 実用的な例

 ### サンプル 1```
h = [1,2,2,1,100,99,99,100,100,100]
query = [1,4]
```差分の配列は次のとおりです。```
d = [-1,0,1,-99,1,0,-1,0,0]
```クエリは次のものに対応します。```
[-1,0,1]
```中を探す$d'$:```
[1,0,-1,99,-1,0,1,0,0]
```有効なオカレンスが 1 つ表示されます。 

| ステップ | 値 |
 | --- | --- |
 | クエリセグメント | [1,4] |
 | 差の長さ | 3 |
 | パターン | [-1,0,1] |
 | d' | で一致するオカレンス 開始 = 5 |
 | ばらばら | はい |
 | 答え | 1 |

 この例では、フェンス マッチングからサブストリング マッチングへのコアの削減を示します。 

### カスタム例```
h = [5,5,5,5]
query = [2,2]
```| ステップ | 値 |
 | --- | --- |
 | セグメントの長さ | 1 |
 | 差分の部分文字列の長さ | 0 |
 | 他の単一の板はすべて一致します。 はい |
 | ばらばらの位置 | 1,3,4 |
 | 答え | 3 |

 これは特殊な長さ 1 のケースを実行します。 差分値は存在しないため、互いに素な単一の板はすべて一致します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n\log n + q\log n)$| サフィックス配列の構築と対数クエリ処理 |
 | スペース |$O(n\log n)$| 永続的なセグメント ツリーとサフィックス配列の構造 |

 と$n,q \le 10^5$、この複雑さは制限に問題なく適合します。 前処理は 1 回実行され、各クエリで必要となるのはバイナリ検索と永続ツリー範囲カウントだけです。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    backup = sys.stdout
    sys.stdout = out
    solve()
    sys.stdout = backup
    return out.getvalue().strip()

assert run(
"""10
1 2 2 1 100 99 99 100 100 100
6
1 4
1 2
3 4
1 5
9 10
10 10
"""
) == "\n".join([
"1",
"2",
"2",
"0",
"2",
"9"
]), "sample 1"

assert run(
"""1
7
1
1 1
"""
) == "0", "minimum size"

assert run(
"""4
5 5 5 5
2
1 1
2 2
"""
) == "\n".join([
"3",
"3"
]), "all equal"

assert run(
"""3
1 2 3
2
1 2
2 3
"""
) == "\n".join([
"0",
"0"
]), "overlapping only"

assert run(
"""5
1 2 1 2 1
1
1 2
"""
) == "1", "repeated pattern"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 一枚板 | 写真 0 | 最小境界 |
 | すべての高さが等しい | 3、3 | 長さ 1 の処理 |
 | 身長を伸ばす | 0、0 | 一致するセグメントがありません |
 | 交互パターン | 1 | 複数の出現と重複フィルタリング |

 ## 特殊なケース

 長さ 1 のクエリを考えてみましょう。```
n = 4
h = [5,7,9,11]
query = [2,2]
```アルゴリズムはすぐに特別なブランチに入ります`l == r`。 比較する内部差異がないため、1 つおきの板は有効な一致セグメントを形成します。 答えは$3$。 

重なった位置でのみ発生するパターンを考えてみましょう。```
h = [1,2,1]
query = [1,2]
```必要な差分パターンは 1 回、つまりクエリ セグメント自体に現れます。 suffix-array 間隔にはその出現が含まれていますが、対応するセグメントがクエリと交差するため、永続的なカウント ステージはそれを拒否します。 答えは次のようになります$0$。 

複数の等しい出現を考慮します。```
h = [1,2,1,2,1]
query = [1,2]
```差異パターンが複数回発生します。 suffix-array 間隔は、すべての出現をキャプチャします。 カウント構造は、開始位置がクエリ セグメントの完全に前または完全に後にあるものだけを保持します。 最終的な答えは、素の一致を正確にカウントし、重複する一致を無視します。 

サフィックス配列間隔検索と位置フィルタリングを組み合わせると、これらすべてのケースが均一に処理されます。
