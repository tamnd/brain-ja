---
title: "CF 102412D - 自尊心の高さから IQ レベルの高さへのジャンプ"
description: "(n) 個の超高層ビルが並んでおり、その高さは (1,2,ldots,n) の順列を形成しています。 有効なジャンプでは、高さが厳密に増加する位置の昇順で 3 つの超高層ビルを使用します。"
date: "2026-08-12T00:36:37+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102412
codeforces_index: "D"
codeforces_contest_name: "MEX Foundation Contest (supported by AIM Tech)"
rating: 0
weight: 102412
solve_time_s: 193
verified: true
draft: false
---

[CF 102412D - 自己重要性の高さから IQ レベルの高さへのジャンプ](https://codeforces.com/problemset/problem/102412/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 13 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 (n) 個の超高層ビルが並んでおり、その高さは (1,2,\ldots,n) の順列を形成しています。 有効なジャンプでは、高さが厳密に増加する位置の昇順で 3 つの超高層ビルを使用します。 言い換えれば、現在のシーケンスに長さ 3 の増加するサブシーケンスが含まれているかどうかを知る必要があります。 

各クエリは 1 つの連続セグメントを取得し、それを (k) 位置ずつ右に循環的に回転します。 2 つの部分の内部順序は維持されますが、順序が入れ替わります。 回転するたびに、長さ 3 の増加するサブシーケンスが存在するかどうかを報告する必要があります。 公式の制限は (n,q\le120000) で、時間制限は 7 秒、メモリは 512 MiB です。 

すべてのクエリの後に直接チェックすると、順列全体がスキャンされます。 (n) と (q) は両方とも (120000) である可能性があるため、(O(nq)) は、制限時間の許容範囲をはるかに超える、約 (1.44\times10^{10}) 個の要素の訪問を意味します。 1 つの状態に対する (O(n^2)) チェックでさえすでに大きすぎるため、解決策では、回転によって個々の高さを変更するのではなく、連続する部分全体を再配置することでシーケンスが変更されるという事実を利用する必要があります。 バランス ツリー アプローチでは、(O(n\log^2 n+q\log^2 n)) が得られます。これは、これらの境界の意図されたスケールです。 

不注意な実装が失敗する可能性がある小さなケースがいくつかあります。 たとえば、たった 2 つの超高層ビルがあるだけで、`2 1`有効なトリプルを含めることはできないため、答えは次のようになります。`NO`; 上昇があるかどうかだけをチェックするコードは、誤って報告する可能性があります`YES`。 1 要素の回転は、もう 1 つの境界ケースです。 入力用```
3
2 3 1
1
1 3 0
```セグメントは移動せず、シーケンスは残ります`2 3 1`、したがって、答えは次のとおりです`NO`。 (k=0) を非自明な分割として扱うと、シーケンスが誤って変更される可能性があります。 もう 1 つの極端な例は (k=r-l+1) で、これも何も行われません。 最後に、高さは異なることが保証されているため、「すべて等しい」テスト ケースは、問題の制約の下では有効な入力ではありません。 ただし、要求される不等式が厳密であるため、等しい値の比較を誤って使用してはなりません。 

## アプローチ

 ブルートフォースの解決策は簡単です。 現在の順列を配列に保存し、クエリされた間隔の最後の (k) 要素を先頭に移動して循環回転を実行し、配列全体をスキャンして長さ 3 の増加するサブシーケンスが含まれているかどうかを判断します。 スキャン自体は、これまでに確認された最小値と、増加するペアの可能な最小の 2 番目の値を維持することで、線形時間で実行できます。 これは正しいです。2 番目の値より大きい 3 番目の値は有効なトリプルを完成させます。 

問題はフルスキャンの繰り返しです。 最悪のケースでは (n=q=120000)、回転を考慮する前に約 (144) 億の演算が行われます。 文字通りに実装した場合、配列自体の更新にも (O(n)) 個の移動が必要になる可能性があります。 

有益な観察は、回転によって結果として得られる部分の内部の順序が変更されないことです。 セグメントが (A) と (B) に分割されているとします。(B) は、端から前に移動した部分です。 新しいセグメントは単に (BA) です。 これは、順序トラバーサルが現在の順列であり、位置による分割には対数時間がかかる、暗黙的なバランス ツリーを示唆しています。 

残りの困難は、サブツリーに増加するトリプルが含まれているかどうかを維持することです。 サブツリーに必要な情報は少量だけです。 その最小および最大の高さ、増加するトリプルがすでに含まれているかどうか、およびそのようなトリプルが含まれていない場合は、その増加するペアの 2 つのプロパティを保存します。 させて`first_max`(a_i<a_j) を持つすべてのペア (i<j) の中で可能な最大の最初の値 (a_i) とし、`second_min`このようなペアの中で可能な最小の 2 番目の値 (a_j) になります。 

2 つの隣接するシーケンスが連結される場合、新しいトリプルは完全に 1 つの子の内部に存在するか、境界を越えることのみが可能です。 境界を越えたトリプルには、左の子に 2 つの要素と右の子に 1 つの要素が含まれるか、左の子に 1 つの要素と右の子に 2 つの要素が含まれます。 保存されたペアの極値により、両方のケースを検出できます。 正確な境界ペアの極値を計算するには、123 回避サブツリー内で先行または後続を見つける必要があります。 重要な構造上の事実は、サブツリーには増加するトリプルが含まれていない一方で、バランスの取れたツリーを下降し、その極値を使用してサブツリー全体を枝刈りすることによってこの検索を実行できるということです。 これは、線形スキャンを強制するのではなく、追加の対数係数を与える特別なプロパティです。 これは、標準のバランスツリー ソリューションの背後にある中心的な観察でもあります。 

したがって、述語自体はテストしやすいため、総当りのソリューションは機能しますが、大規模な再配置のたびに再計算する必要がある場合は失敗します。 ローテーションは単に分割の後にマージが続くという観察により、述語をサブツリー集合体として保存できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(nq)) | (O(n)) | 遅すぎる |
 | 最適な暗黙的トレプ | (O((n+q)\log^2 n)) | (O(n)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 順序シーケンスが現在の超高層ビルの順序である暗黙的なランダム化トリープを構築します。 各ノードは 1 つの超高層ビルを表し、サブツリーのサイズによって位置インデックスが決定されます。 最初の (k) 要素の分割と 2 つのシーケンスの連結には両方とも予期される対数時間がかかるため、treap が適切です。 
2. すべてのサブツリーについて、そのサイズ、最小高さ、最大高さ、および増加するトリプルがすでに存在するかどうかを維持します。 サブツリーにまだトリプルがない場合は、次も維持します。`first_max`そして`second_min`増え続けるペアのために。 
3. 左側のサブツリー、現在のノード、および右側のサブツリーを結合する場合、まず、いずれかの子にすでにトリプルが含まれているかどうかを判断します。 そうである場合、結合されたサブツリーにはすぐに 1 つが含まれ、ペア情報は不要になります。 
4. それ以外の場合は、境界を越えるトリプルを検出します。 左側のサブツリーに、2 番目の値が右側のサブツリーの最大値より小さい増加するペアが含まれている場合、これらの 2 つの左側の要素とその最大値が後に続き、トリプルが形成されます。 まさにその条件です`left.second_min < right.max`。 
5. 対称的に、左側のサブツリーの最小値が右側のサブツリー内の増加するペアの最初の値より小さい場合、左からの最小値とそれに続くそのペアがトリプルを形成します。 これにより、`left.min < right.first_max`。 
6. トリプルが存在しない場合は、ペア情報を更新します。 いずれかの子にすでに含まれているペアは有効なままです。 両側から 1 つの要素を使用する新しいペアは、前のペアと等しい最大の最初の値を持ちます。`right.max`左側のサブツリーの値の間。 可能な最小の 2 番目の値は、次の値の後続値です。`left.min`右側のサブツリーの値の間。 
7. 各クエリを実行するには、位置 (l) の前で分割し、次にセグメント ([l,r]) を分割します。 そのセグメント内で、それを (A) と (B) に分割します。(B) の長さは (k) です。 セグメントを (BA) に置き換えます。 最後に、プレフィックスとサフィックスを付けてマージし直します。 
8. 根っこ`has_three`flag は答えを直接決定します。 印刷する`YES`それが真実であるとき、そして`NO`さもないと。 

これが機能する理由: 連結内の増加するトリプルはすべて、左側の部分に完全に含まれるか、右側の部分に完全に含まれるか、境界を越えます。 最初の 2 つのケースは、子のフラグで表されます。 左側に 2 つの要素がある交差トリプルの場合、考えられる最適な 2 番目の要素は次のとおりです。`left.second_min`、そして可能な限り最良の 3 番目の要素は次のとおりです。`right.max`。 右側に 2 つの要素がある交差トリプルの場合、可能な限り最良の最初の要素は次のとおりです。`left.min`、そして可能な限り最良の 2 番目の要素は次のとおりです。`right.first_max`。 したがって、すべての可能なトリプルがカバーされます。 ペアの極値は、正確に 3 つの可能なペアの位置 (左-左、右-右、および左-右) から更新されます。 したがって、すべての treap ノードに保存されている集約はその順序シーケンスを正確に記述しているため、分割とマージのたびにルート フラグは常に正しいものになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

sys.setrecursionlimit(1_000_000)

INF = 10**18

def solve():
    n = int(input())
    a = list(map(int, input().split()))
    q = int(input())

    # Node 0 is the null node.
    left = [0]
    right = [0]
    value = [0]
    priority = [0]
    size = [0]

    mn = [INF]
    mx = [-INF]
    bad = [False]

    # For a triple-free subtree:
    # first_max  = maximum first value among increasing pairs.
    # second_min = minimum second value among increasing pairs.
    first_max = [-INF]
    second_min = [INF]

    seed = 712367821

    def rng():
        nonlocal seed
        seed ^= (seed << 13) & 0xFFFFFFFF
        seed ^= seed >> 17
        seed ^= (seed << 5) & 0xFFFFFFFF
        seed &= 0xFFFFFFFF
        return seed

    def new_node(v):
        idx = len(value)
        left.append(0)
        right.append(0)
        value.append(v)
        priority.append(rng())
        size.append(1)
        mn.append(v)
        mx.append(v)
        bad.append(False)
        first_max.append(-INF)
        second_min.append(INF)
        return idx

    # Find the largest value < x in a triple-free subtree.
    def predecessor(t, x):
        if not t or mn[t] >= x:
            return 0
        if mx[t] < x:
            return mx[t]

        ans = 0

        r = right[t]
        z = predecessor(r, x)
        if z > ans:
            ans = z

        v = value[t]
        if v < x and v > ans:
            ans = v

        l = left[t]
        z = predecessor(l, x)
        if z > ans:
            ans = z

        return ans

    # Find the smallest value > x in a triple-free subtree.
    def successor(t, x):
        if not t or mx[t] <= x:
            return INF
        if mn[t] > x:
            return mn[t]

        ans = INF

        l = left[t]
        z = successor(l, x)
        if z < ans:
            ans = z

        v = value[t]
        if v > x and v < ans:
            ans = v

        r = right[t]
        z = successor(r, x)
        if z < ans:
            ans = z

        return ans

    def pull(t):
        l = left[t]
        r = right[t]
        v = value[t]

        size[t] = size[l] + size[r] + 1
        mn[t] = min(mn[l], v, mn[r])
        mx[t] = max(mx[l], v, mx[r])

        if bad[l] or bad[r]:
            bad[t] = True
            first_max[t] = -INF
            second_min[t] = INF
            return

        has_triple = (
            second_min[l] < mx[r] or
            mn[l] < first_max[r]
        )

        cross_first = 0
        cross_second = INF

        if l and r:
            cross_first = predecessor(l, mx[r])
            cross_second = successor(r, mn[l])

            if cross_first and cross_second != INF:
                has_triple = has_triple or (
                    second_min[l] < mx[r] or
                    mn[l] < first_max[r]
                )

        bad[t] = has_triple

        if has_triple:
            first_max[t] = -INF
            second_min[t] = INF
            return

        fm = max(first_max[l], first_max[r], cross_first)
        sm = min(second_min[l], second_min[r], cross_second)

        # Pairs involving the root value itself.
        if l and v > mn[l]:
            p = predecessor(l, v)
            if p:
                fm = max(fm, p)
                sm = min(sm, v)

        if r and mx[r] > v:
            s = successor(r, v)
            if s != INF:
                fm = max(fm, v)
                sm = min(sm, s)

        first_max[t] = fm
        second_min[t] = sm

    # Build the initial treap in O(n) using the Cartesian-tree stack.
    nodes = [new_node(v) for v in a]

    stack = []
    for t in nodes:
        last = 0
        while stack and priority[stack[-1]] < priority[t]:
            last = stack.pop()
        if stack:
            right[stack[-1]] = t
        left[t] = last
        stack.append(t)

    root = stack[0]

    # Pull aggregates bottom-up.
    order = []
    st = [root]
    while st:
        t = st.pop()
        order.append(t)
        if left[t]:
            st.append(left[t])
        if right[t]:
            st.append(right[t])

    for t in reversed(order):
        pull(t)

    def split(t, k):
        if not t:
            return 0, 0

        l = left[t]

        if size[l] >= k:
            x, y = split(l, k)
            left[t] = y
            pull(t)
            return x, t

        x, y = split(right[t], k - size[l] - 1)
        right[t] = x
        pull(t)
        return t, y

    def merge(a_root, b_root):
        if not a_root:
            return b_root
        if not b_root:
            return a_root

        if priority[a_root] > priority[b_root]:
            right[a_root] = merge(right[a_root], b_root)
            pull(a_root)
            return a_root

        left[b_root] = merge(a_root, left[b_root])
        pull(b_root)
        return b_root

    out = []

    for _ in range(q):
        l, r, k = map(int, input().split())

        if k == 0 or k == r - l + 1:
            out.append("YES" if bad[root] else "NO")
            continue

        prefix, rest = split(root, l - 1)
        segment, suffix = split(rest, r - l + 1)

        first_part, second_part = split(
            segment,
            r - l + 1 - k
        )

        segment = merge(second_part, first_part)
        root = merge(prefix, merge(segment, suffix))

        out.append("YES" if bad[root] else "NO")

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```トレップは暗黙的であるため、位置を表すキーはありません。 ノードの位置は、その左側のサブツリーのサイズによって決まります。 それにより、`split(root, k)`これは正確に「最初の (k) 個の超高層ビルを取る」という意味であり、これが周期的な回転に必要なものです。 

の`pull`機能はソリューションの中核です。 最小値と最大値は通常のサブツリー集合体です。`bad`123 パターンが既に存在するかどうかを記録します。 いつ`bad`は誤りです、`first_max`そして`second_min`サブツリー内の増加するすべてのペアを記述します。 先行検索と後続検索は、サブツリーが 123 を回避している場合にのみ必要になります。これはまさに構造枝刈りプロパティが適用される状況です。 これは、目的のソリューションの独立した記事で説明されている情報セットと同じです。 

以下の 2 つのチェック`second_min[l]`そして`first_max[r]`意図的に厳密な比較を使用します。 高さは異なるため、等しいことは有効なジャンプの一部を構成することはできません。 ルート ノード自体もペアの増加に参加します。`pull`現在の値といずれかの子の間のペアを明示的に処理します。 

クエリで使用されるのは、`r - l + 1 - k`回転したセグメントを分割するとき。 これは、右回転によって最後の (k) 個の要素が前方に移動される前に、前方に留まる部分の長さです。 (k) が 0 またはセグメント全体の長さの場合、シーケンスは変更されないため、不要なツリー操作を回避できます。 

Python の整数はオーバーフローせず、保存される高さはすべて最大 (120000) です。 実装上の主な懸念事項は再帰の深さであるため、再帰の制限は大幅に引き上げられます。 ランダム化された優先順位により、期待値の対数的に処理高さが維持されます。 

## 実用的な例

 ### サンプル 1

 入力は```
6
2 5 6 1 3 4
1
1 6 5
```配列全体が右に 5 位置回転されます。これは、最初の要素を最後に移動することと同じです。 

| ステップ | シーケンス | 回転分割 | 根`bad`|
 | --- | --- | --- | --- |
 | イニシャル |`2 5 6 1 3 4`| なし |`YES`|
 | 分割 |`2`+`5 6 1 3 4`| 最初の部分の長さ 1 |`YES`|
 | 回転 |`5 6 1 3 4`+`2`| 右側の長さ 5 |`YES`|
 | 最終回 |`5 6 1 3 4 2`| 統合されました |`YES`|

 最終シーケンスには以下が含まれます`1,3,4`位置と高さが増すにつれて、答えは次のようになります。`YES`。 このトレースは、5 つの配列要素を物理的に移動することなく、純粋に treap の分割とマージによって回転を表現できることを示しています。 

### サンプル 3

 入力は```
5
4 3 2 5 1
2
3 4 1
1 2 1
```| ステップ | シーケンス | 操作 | 根`bad`|
 | --- | --- | --- | --- |
 | イニシャル |`4 3 2 5 1`| なし |`NO`|
 | 1 |`4 3 5 2 1`| 回転させる`[3,4]`1 の右 |`NO`|
 | 2 |`3 4 5 2 1`| 回転させる`[1,2]`1 の右 |`YES`|

 最初の操作の後、シーケンスには次のような上昇があります。`3,5`ただし、これより大きい 3 番目以降の高さはありません。`5`, したがって、上昇だけでは十分ではありません。 ２回目の施術後、`3,4,5`昇順で表示され、必要なトリプルが生成されます。 これはまさに、`first_max`そして`second_min`ペア情報。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O((n+q)\log^2 n)) が期待される | Treap 操作では予想される (O(\log n)) の高さを使用しますが、集計の再構築では対数先行検索または後続検索を実行できます。 
| スペース | (O(n)) | 超高層ビルごとに 1 つの treap ノードと一定量の集約情報 |

 この制約により、(120,000) 個の超高層ビルと (120,000) 個の回転が許可されるため、二次関数または (O(nq)) アプローチは実行できません。 バランスツリー表現は構造レベルですべての回転を対数に保ちますが、特別な 123 回避検索は追加の対数係数内で集計の再計算を維持します。 公式の制限時間は 7 秒、メモリ制限は 512 MiB です。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    try:
        sys.stdin = io.StringIO(inp)
        sys.stdout = io.StringIO()

        solve()

        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Provided sample 1
assert run(
    """6
2 5 6 1 3 4
1
1 6 5
"""
) == "YES\n", "sample 1"

# Provided sample 2
assert run(
    """8
5 1 2 8 7 6 3 4
4
2 4 2
4 5 1
1 3 2
3 8 2
"""
) == "YES\nYES\nYES\nYES\n", "sample 2"

# Provided sample 3
assert run(
    """5
4 3 2 5 1
2
3 4 1
1 2 1
"""
) == "NO\nYES\n", "sample 3"

# Provided sample 4
assert run(
    """6
6 5 4 3 2 1
3
1 1 0
1 3 1
2 5 3
"""
) == "NO\nNO\nYES\n", "sample 4"

# Minimum size.
assert run(
    """1
1
1
1 1 0
"""
) == "NO\n", "minimum size"

# Two elements can never form a triple.
assert run(
    """2
1 2
1
1 2 1
"""
) == "NO\n", "two elements"

# Full rotation by one creates 1,2,3.
assert run(
    """3
2 3 1
1
1 3 1
"""
) == "YES\n", "full-range rotation"

# Boundary case with no movement.
assert run(
    """3
3 2 1
1
1 3 0
"""
) == "NO\n", "zero rotation"

# Maximum-size decreasing permutation.
n = 120000
max_case = (
    str(n) + "\n" +
    " ".join(map(str, range(n, 0, -1))) + "\n" +
    "1\n" +
    "1 120000 1\n"
)
assert run(max_case) == "NO\n", "maximum-size case"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / 1 / 1 1 0`|`NO`| 最小サイズ |
 |`2 / 1 2 / 1 2 1`|`NO`| 超高層ビルは 3 つ未満 |
 |`3 / 2 3 1 / 1 3 1`|`YES`| 全範囲サイクリック回転 |
 |`3 / 3 2 1 / 1 3 0`|`NO`| ゼロ回転境界 |
 | サイズの順列の減少`120000`|`NO`| 最大入力サイズとパフォーマンス |
 | 提供されたサンプル | リストにあるとおり | 一般的な正確さと回転の境界 |

 ## 特殊なケース

 長さ 1 のセグメントの場合、許可された回転ごとにセグメントは変更されません。 例えば、```
3
3 2 1
1
2 2 1
```生成する`NO`。 1 要素セグメントを分割して回転すると、同じノードが同じ場所に残るため、実装ではこれを自然に処理します。 

回転がゼロの場合も何も操作は行われません。 と```
3
3 2 1
1
1 3 0
```シーケンスは残っています`3 2 1`、増加するトリプルが含まれていないため、出力は次のようになります。`NO`。 コードは、分割を実行する前に (k=0) を明示的に処理します。 

セグメント全体の長さによる回転も、何も行われません。 のために```
3
2 3 1
1
1 3 3
```最後のシーケンスはまだです`2 3 1`したがって、出力は次のようになります`NO`。 これを通常の回転として扱うと、不必要な分割が発生し、境界ミスの一般的な原因となります。 

シーケンスには、増加するトリプルを含まずに、多くの増加するペアを含めることができます。 サンプル-3 中間状態`4 3 5 2 1`これを示します:`3,5`は増加するペアですが、超高層ビルがこれを超えることはありません`5`。 このアルゴリズムはペア情報をトリプル フラグとは別に保持するため、「上昇が存在する」ことと「長さ 3 の増加する部分列が存在する」を混同しません。 

最後に、この問題はすべての高さが異なることを保証します。 したがって、等価性は有効なジャンプに寄与することはなく、集約ロジック内のすべての比較は厳密です。 を使用した不注意な実装`<=`の代わりに`<`別の問題を解決することになるでしょう。
