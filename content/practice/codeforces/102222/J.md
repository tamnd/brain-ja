---
title: "CF 102222J - 入れ子になった三角形"
description: "2 つの固定ピボット (P) と (Q)、および (n) 個の他の点 (A1,ldots,An) があります。 他の点はいずれも線上 (PQ) 上にありません。 すべての点 (A{v{i+1}}) が (P,Q,A{vi}) によって形成される三角形の内側に厳密に存在するようなインデックスのシーケンス (v1,v2,ldots,vk) が必要です。"
date: "2026-08-19T00:31:29+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102222
codeforces_index: "J"
codeforces_contest_name: "2018-2019 ACM-ICPC, China Multi-Provincial Collegiate Programming Contest"
rating: 0
weight: 102222
solve_time_s: 227
verified: true
draft: false
---

[CF 102222J - 入れ子になった三角形](https://codeforces.com/problemset/problem/102222/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 47 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 2 つの固定ピボット (P) と (Q) と、(n) 個の他の点 (A_1,\ldots,A_n) があります。 他の点はいずれも線上 (PQ) 上にありません。 すべての点 (A_{v_{i+1}}) が (P,Q,A_{v_i}) によって形成される三角形の内側に厳密に存在するようなインデックスのシーケンス (v_1,v_2,\ldots,v_k) が必要です。 

最初の目的は、(k) を最大化することです。 その最大長のすべてのシーケンスの中で、必要な答えは、元のインデックスの辞書編集上最小のシーケンスです。 公式サンプルには 3 つのケースが含まれており、答えの長さは (6)、(3)、および (1) です。 

制約 (n\le 10^5) は、単一の大きなケースに対して二次時間に近いものをすでに除外しています。 点のすべてのペアをチェックすると、約 (n(n-1)/2) のコストがかかります。これは、(n=10^5) の場合、おおよそ (5\cdot10^9) 個のペア チェックに相当します。 すべてのテスト ケースの合計点数は (10^6) であるため、(O(n\log^2 n)) の解であっても、かなり小さな定数を使用して実装する必要があります。 座標は (10^9) に達する可能性があるため、幾何学的比較は浮動小数点の角度に基づくのではなく正確である必要があります。 

間違った答えを導き出す特に簡単な方法が 2 つあります。 まず、(PQ) の同じ側の点は独立して処理する必要があります。 例えば、```
1
0 0 10 0
2
5 1
5 -1
```回答の長さは (2) ではなく (1) です。 2 番目の点は (PQ) の反対側にあるため、3 番目の頂点が最初の点である三角形の内側にあることはできません。 

第 2 に、角度方向の等価性は三角形のエッジ上の点を表すため、許可されません。 例えば、```
1
0 0 10 0
3
1 1
2 2
3 3
```答えがあります```
Case #1: 1
1
```3 つの点はすべて (P) からの同じ光線上にあります。 不注意で非厳密な LIS は、後のすべての点が前の点によって決定された三角形の境界上にあるにもかかわらず、それらをチェーンとして扱います。 

3 番目の特殊なケースは、ライン (PQ) が水平である必要がないということです。 例えば、```
1
0 0 0 10
4
1 5
2 5
-1 5
-2 5
```最大長は (2) です。 右側のチェーンは (2,1)、左側のチェーンは (4,3) であるため、辞書順に小さい最大解は次のようになります。```
Case #1: 2
2
1
```(y/x) などの通常の傾きに基づく解は、垂直方向について特別な処理を必要とします。 外積を使用すると、この種の問題全体が回避されます。 

## アプローチ

 直接動的計画法では、(PQ) の同じ側にある順序付けされた点のペアをすべて考慮します。 考えられる外側の点 (A_i) ごとに、考えられるすべての内側の点 (A_j) を検査し、(A_j) が厳密に三角形 (PQA_i) の内側にあるかどうかをテストし、結果の関係を DP 遷移として使用します。 これは正しいです。幾何学的座標が適切なランクに変換されると、入れ子関係によって有向非巡回順序が形成されるからです。 問題はペアチェックの数です。 (n=10^5) の場合、(\frac{n(n-1)}2) 個の可能なペアは、幾何学的述語のコストを考慮する前に、すでに約 (5\cdot10^9) 個のテストを実行します。 

有益な観察は、三角形の包含が 2 つのピボットから見た点の方向のみを使用して記述できることです。 (A) が厳密に三角形 (PQB) の内側にあると仮定します。 したがって、(A) と (B) は (PQ) の同じ側になければなりません。 (P) から、光線 (PA) は厳密に (PQ) と (PB) の間にあります。 (Q) から、光線 (QA) は厳密に (QP) と (QB) の間にあります。 

これにより、ジオメトリが 2 つの厳密な順序関係に変わります。 すべての点について、光線 (PQ) から測定した (P) 周りの 1 つの角度ランクと、(QP) から測定した (Q) 周りの別の角度ランクを割り当てます。 (PQ) の一方の辺内で、両方のランクが小さい場合に、点を別の辺の中にネストすることができます。 

ランクは角度や浮動小数点を使用せずに計算できます。 2 つのベクトル (u) と (v) が与えられた場合、(u\times v) の符号は、どちらのベクトルが半平面内の角度順序で最初に来るかを示します。 同じ光線上の点は外積ゼロを持ち、同じランクを受け取り、厳密な入れ子に参加できない境界ケースを正しく表します。 

2 つのランクを取得した後、各辺は 2 次元の厳密増加部分列問題になります。 最初のランクでソートし、2 番目のランクでフェンウィック ツリーを使用すると、(O(n\log n)) で最長のチェーンが得られます。 

辞書編集上の要件は、同じ DP 結果に自然に適合します。 (f[i]) をランク空間の点 (i) で終わる最長の増加チェーンの長さとします。 (f[i]=L) を持つすべての点は、最適解の最初の最も外側の点になる可能性があります。 すでに選択されている外側の点と互換性のあるすべての点の中で、最小の元のインデックスを選択するだけです。 (f=L,L-1,\ldots,1) を処理するため、再構築中にすべての点が 1 回だけ検査されます。 

どの三角形にも反対側の点を含めることはできないため、(PQ) の 2 つの辺は独立して解決されます。 長い方の結果を採用し、長さが等しい場合、最初のインデックスが小さいシーケンスの方が辞書編集的に小さくなります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(n^2)) ペア チェック | (O(n)) | 遅すぎる |
 | 最適 | (O(n\log n)) | (O(n)) | 承認済み |

 ## アルゴリズムのチュートリアル

1. 有向線 (PQ) に対するすべての (A_i) の向きを計算します。 ((Q-P)\times(A_i-P)) の符号は、2 つの開いた半平面を識別します。 (PQ) 上には点がないため、ゼロの場合は存在しません。 
2. 点 (P) を中心に並べ替え、光線からの角距離 (PQ) によって 2 つの辺を別々に並べます。 一方では相互積比較が時計回りに行われ、もう一方では反時計回りに行われます。 等しいレイはグループ化され、同じ最初のランクを受け取ります。 
3. (Q) の周りの同じ点を並べ替えます。今回は (QP) からの角距離を測定します。 ここでも、等しい光線は同じ 2 番目のランクを受け取ります。 
4. 一度に片側ずつ作業します。 最初のランクを増やすことによってポイントを並べ替え、最初のランクが同じ場合は 2 番目のランクを下げることによって並べ替えます。 同順位の減少により、同じ最初のランクを持つ 2 つのポイントが後続の遷移を形成することが防止されます。 それらは (P) からの同じ光線上にあるため、そのような遷移は厳密な内部点ではなく境界点を表すことになります。 
5. ポイントをこの順序でスキャンし、位置が 2 番目のフェンウィック ツリーを維持します。 ポイント (i) については、それ自身のランクよりも厳密に小さいすべての 2 番目のランクをクエリします。 最大値が(x)の場合、(f[i]=x+1)とします。 次に、ポイントの 2 番目のランクにあるフェンウィック ツリーを (f[i]) で更新します。 
6. 最大値 (f[i]) は、この側の最大ネスト深さです。 DP 値に従って、すべてのポイントをバケットに保存します。 これらのバケットは、辞書編集的に最小の答えを再構築するために使用されます。 
7. 最大 DP 値から始めて下に向かって、2 つのランクが以前に選択したポイントのランクよりも厳密に小さい、最小の元のインデックスを選択します。 最初の位置には前のポイントがないため、最大 DP 値を持つすべてのポイントの中で最小のインデックスを選択します。 
8. (PQ) の反対側についても計算を繰り返します。 結果として得られる 2 つのシーケンスを、最初に長さで比較し、長さが等しい場合は最初のインデックスで比較します。 

これが機能する理由: (PQ) の同じ側にある 2 点の場合、光線 (PA) が厳密に (PQ) と (PB) の間にあり、光線 (QA) が厳密に (QP) と (QB) の間にあるとき、点 (A) は厳密に三角形 (PQB) の内側にあります。 これら 2 つの厳密な角度条件は、まさに 2 つのランクの不等式です。 したがって、すべての有効なネスト シーケンスは、内側から外側に読み取られる場合、厳密に増加するランク ペアのシーケンスに対応します。 フェンウィック ツリーは、そのような最長のシーケンスを計算します。 再構築中、DP 値 (d) のポイントには常に (d-1) 個の先行インデックスのチェーンがあるため、すべてのレベルで最小の有効な元のインデックスを選択すると、最も古い異なるインデックスを最小限に抑えながら、可能な最大の残りの長さが維持されます。 それはまさに辞書編集的な最小化です。 

## Python ソリューション```python
import sys
from functools import cmp_to_key

input = sys.stdin.readline

class FenwickMax:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 1)

    def update(self, i, value):
        n = self.n
        bit = self.bit
        while i <= n:
            if value > bit[i]:
                bit[i] = value
            i += i & -i

    def query(self, i):
        bit = self.bit
        ans = 0
        while i > 0:
            if bit[i] > ans:
                ans = bit[i]
            i -= i & -i
        return ans

def cross(ax, ay, bx, by):
    return ax * by - ay * bx

def solve_side(points, pivot_p, pivot_q, side):
    if not points:
        return []

    px, py = pivot_p
    qx, qy = pivot_q

    # The points are already assigned to one side.
    # Rank 1: angular order around P, starting from P->Q.
    def cmp_p(a, b):
        ax = a[0] - px
        ay = a[1] - py
        bx = b[0] - px
        by = b[1] - py
        c = ax * by - ay * bx

        if c == 0:
            return 0

        # side == 0 means cross(PQ, PA) < 0.
        # side == 1 means cross(PQ, PA) > 0.
        if side == 0:
            return -1 if c < 0 else 1
        return -1 if c > 0 else 1

    points.sort(key=cmp_to_key(cmp_p))

    rank = 0
    first = None

    for p in points:
        if first is None:
            rank = 1
            first = p
            p[4] = rank
            continue

        ax = first[0] - px
        ay = first[1] - py
        bx = p[0] - px
        by = p[1] - py

        if ax * by - ay * bx != 0:
            rank += 1
            first = p

        p[4] = rank

    # Rank 2: angular order around Q, starting from Q->P.
    def cmp_q(a, b):
        ax = a[0] - qx
        ay = a[1] - qy
        bx = b[0] - qx
        by = b[1] - qy
        c = ax * by - ay * bx

        if c == 0:
            return 0

        if side == 0:
            return -1 if c > 0 else 1
        return -1 if c < 0 else 1

    points.sort(key=cmp_to_key(cmp_q))

    rank = 0
    first = None

    for p in points:
        if first is None:
            rank = 1
            first = p
            p[5] = rank
            continue

        ax = first[0] - qx
        ay = first[1] - qy
        bx = p[0] - qx
        by = p[1] - qy

        if ax * by - ay * bx != 0:
            rank += 1
            first = p

        p[5] = rank

    # Strictly increasing rank pairs.
    # For equal rank1, decreasing rank2 prevents equal-rank1 transitions.
    points.sort(key=lambda p: (p[4], -p[5]))

    max_rank2 = rank
    bit = FenwickMax(max_rank2)

    groups = [[]]
    maximum = 0

    for p in points:
        f = bit.query(p[5] - 1) + 1
        p[6] = f

        if f > maximum:
            maximum = f
            groups.extend([[] for _ in range(f - len(groups) + 1)])

        groups[f].append(p)
        bit.update(p[5], f)

    # Reconstruct the lexicographically smallest chain.
    answer = []
    current = None

    for length in range(maximum, 0, -1):
        best = None

        if current is None:
            for p in groups[length]:
                if best is None or p[2] < best[2]:
                    best = p
        else:
            r1 = current[4]
            r2 = current[5]

            for p in groups[length]:
                if p[4] < r1 and p[5] < r2:
                    if best is None or p[2] < best[2]:
                        best = p

        current = best
        answer.append(current[2])

    return answer

def solve():
    t = int(input())
    output = []

    for case_id in range(1, t + 1):
        xP, yP, xQ, yQ = map(int, input().split())
        n = int(input())

        P = (xP, yP)
        Q = (xQ, yQ)

        dx = xQ - xP
        dy = yQ - yP

        right = []
        left = []

        for idx in range(1, n + 1):
            x, y = map(int, input().split())
            c = dx * (y - yP) - dy * (x - xP)

            # p = [x, y, original_id, side, rank1, rank2, dp]
            point = [x, y, idx, 0, 0, 0, 0]

            if c < 0:
                point[3] = 0
                right.append(point)
            else:
                point[3] = 1
                left.append(point)

        ans_right = solve_side(right, P, Q, 0)
        ans_left = solve_side(left, P, Q, 1)

        if len(ans_right) > len(ans_left):
            answer = ans_right
        elif len(ans_left) > len(ans_right):
            answer = ans_left
        else:
            if ans_right[0] < ans_left[0]:
                answer = ans_right
            else:
                answer = ans_left

        output.append(f"Case #{case_id}: {len(answer)}")
        output.extend(map(str, answer))

    sys.stdout.write("\n".join(output))

if __name__ == "__main__":
    solve()
```入力ループは最初に、(PQ) を基準としたすべての点の方向を計算します。 このステートメントでは、ピボット ライン上に正確に点が存在しないことが保証されるため、符号だけで十分です。 

各ポイントには、その座標、元のインデックス、辺、2 つの角度ランク、およびその DP 値が保存されます。 Python の整数には任意の精度があるため、座標が (10^9) に達しても外積は正確なままです。 

最初のカスタム コンパレータは (P) を中心にレイを順序付けし、2 番目は (Q) を中心にレイを順序付けします。 コンパレータは、外積の代わりに意図的に外積を使用します。`atan2`。 浮動小数点角度はほとんどの方向を区別できますが、その差が機械精度よりも小さい有理方向については正しい順序付けを保証できません。 

ランク割り当てでは、すべてのポイントと現在の等光線グループの最初のポイントが比較されます。 ゼロクロス積は、2 つのベクトルが関連する開いた半平面内で同じ方向を持つことを意味します。 厳密なネスト遷移を形成できないため、同じランクが与えられます。 

フェンウィック ツリーには最大 DP 値のみが含まれています。`query(r2 - 1)`2番目のランクに厳格な不平等を強制します。 等しい 1 位グループ内の減少する 2 位の順序は、別のグループ処理パスを必要とせずに、他の厳密な不等式を処理します。 

再構成は、最大の DP 値から 1 まで意図的に行われます。 DP 値 (L) の点は最も外側の点であり、DP 値 (L-1) の点はその内側に配置されます。 幾何学的に互換性のあるすべての候補の中から最小の元のインデックスを選択すると、残りのチェーンの長さを変更せずに、可能な限り最小の次のインデックスが得られます。 

浮動小数点を使用した減算や乗算は実行されず、Python の任意精度の整数を使用すると、低レベル言語では注意が必要となるオーバーフローの問題が解決されます。 

## 実用的な例

 最初のサンプルは、6 つの点すべてが (PQ) の同じ側にあり、1 つの完全なチェーンを形成しているため、特に役立ちます。 公式の出力は (6,5,4,3,2,1) です。 

| ポイント | 1位 | 2位 | DP | 復興 |
 | --- | --- | --- | --- | --- |
 | (A_1=(5,1)) | 1 | 1 | 1 | 最後に選択された |
 | (A_2=(5,2)) | 2 | 2 | 2 | 5 番目に選択 |
 | (A_3=(5,3)) | 3 | 3 | 3 | 4 番目に選択 |
 | (A_4=(6,4)) | 4 | 4 | 4 | 3 番目に選択 |
 | (A_5=(6,5)) | 5 | 5 | 5 | 2 番目に選択 |
 | (A_6=(6,6)) | 6 | 6 | 6 | 最初に選択されました |

 各ポイントに対する Fenwick クエリでは、前の 2 番目のランクがすべて参照されるため、DP 値は (1,2,3,4,5,6) になります。 再構成は DP (6) から始まり、ポイント (6)、次にポイント (5) を選択し、ポイント (1) まで続きます。 結果は、まさに必要な外側から内側の順序になります。 

2 番目のサンプルでは、​​ピボットは (P=(6,6)) および (Q=(0,0)) で、最大チェーンは (1,3,2) です。 選択した 3 つの点は、ピボット ラインの同じ側にあります。 DP は長さ (3) のチェーンを見つけますが、他の点は反対側に属するか、2 つの厳密な順位不等式のいずれかに当てはまりません。 

| 復興段階 | 必要なDP | 選択されたインデックス | 理由 |
 | --- | --- | --- | --- |
 | 最初のポイント | 3 | 1 | 長さ 3 のチェーンを開始できる最小点 |
 | 2 番目の点 | 2 | 3 | 残りの長さと互換性のある最小点 |
 | 3 番目のポイント | 1 | 2 | チェーンを完成させる互換性のあるポイント |

 2 番目の例は、辞書編集的最小化で単純にグローバルに最小のインデックスを選択できない理由を示しています。 点 (1) が最初の選択肢としては最適ですが、これを修正した後、次の選択肢は幾何学的な入れ子関係と残りの DP 要件を満たす必要があります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(n\log n)) | 2 つの正確な角度ソート、ランク処理、フェンウィック DP、線形再構築 |
 | スペース | (O(n)) | ポイント ストレージ、フェンウィック ツリー、DP バケット |

 最大のケースには (10^5) 個のポイントが含まれており、すべてのケースの合計は (10^6) になります。 このアルゴリズムは、ソート ステージとフェンウィック ステージでポイントごとに対数の操作を実行しますが、すべての再構成パスとランク割り当てパスは線形です。 メモリ使用量は、現在のテスト ケースのポイント数に線形です。 

## テストケース```python
# This test block assumes the solve() function from the solution above
# has already been defined.

import sys
import io

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    try:
        solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Official samples.
sample = """\
3
0 0 10 0
6
5 1
5 2
5 3
6 4
6 5
6 6
6 6
0 0
9
1 6
2 3
4 7
6 8
8 2
9 3
7 6
2 4
2 7
0 10 10 0
9
0 0
0 2
2 0
0 4
4 0
0 6
6 0
0 8
8 0
"""

expected_sample = """\
Case #1: 6
6
5
4
3
2
1
Case #2: 3
1
3
2
Case #3: 1
1
"""

assert run(sample) == expected_sample, "official samples"

# Minimum-size input.
assert run("""\
1
0 0 10 0
1
5 1
""") == """\
Case #1: 1
1
""", "minimum n"

# Equal ray from P: every point is on a boundary ray, so no pair can nest.
assert run("""\
1
0 0 10 0
3
1 1
2 2
3 3
""") == """\
Case #1: 1
1
""", "equal ray must remain strict"

# Both sides have a chain of length 2.
# The two maximum solutions are [2, 1] and [4, 3],
# so lexicographic order chooses [2, 1].
assert run("""\
1
0 0 10 0
4
5 1
5 2
5 -1
5 -2
""") == """\
Case #1: 2
2
1
""", "tie between the two sides"

# Vertical PQ. This catches implementations that rely on ordinary slopes.
assert run("""\
1
0 0 0 10
4
1 5
2 5
-1 5
-2 5
""") == """\
Case #1: 2
2
1
""", "vertical pivot line"

# Maximum-size case with a deliberately simple answer.
# All 100000 points lie on the same ray from P, so the answer is still 1.
points = "\n".join(f"{i} {i}" for i in range(1, 100001))
max_case = "1\n0 0 1 0\n100000\n" + points + "\n"

assert run(max_case) == """\
Case #1: 1
1
""", "n = 100000"

print("all tests passed")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | (P=(0,0),Q=(10,0),A_1=(5,1)) |`Case #1: 1 / 1`| 最小入力サイズ |
 | 3 点 ((1,1),(2,2),(3,3)) |`Case #1: 1 / 1`| 等しい角度ランクと厳密性 |
 | 反対側の 2 つのチェーン |`Case #1: 2 / 2 / 1`| サイド分離と辞書編集上のタイブレーク |
 | 垂直 (PQ) |`Case #1: 2 / 2 / 1`| 除算なしの方向処理 |
 | 1 つの光線上の (100000) 点 |`Case #1: 1 / 1`| 最大 (n) とリニアメモリ動作 |

 ## 特殊なケース

 (PQ) の反対側にある点の場合、アルゴリズムは DP を実行する前にそれらを異なる配列に配置します。 のために```
1
0 0 10 0
2
5 1
5 -1
```最初の点は上位側の計算でのみランクを受け取り、2 番目の点は下位側の計算でのみランクを受け取ります。 各辺から長さ (1) のチェーンが生成されるため、最終的な答えは次のようになります。```
Case #1: 1
1
```三角形の内点 (PQA) は (PQ) の (A) と同じ側にある必要があるため、比較によって 2 つの辺の間に遷移が作成されることはありません。これはジオメトリと一致します。 

等しい光線については、次のように考えます。```
1
0 0 10 0
3
1 1
2 2
3 3
```3 つの点はすべて、(P) 周りで同じ最初の角度ランクを持ちます。 したがって、それらは 1 つの同じランクのグループ内で処理され、2 番目のランクの順序が減少するため、一方が他方を拡張することができなくなります。 すべての DP 値は (1) であるため、再構成では最小の元のインデックスが選択され、次の結果が得られます。```
Case #1: 1
1
```これは、通常の非厳密な LIS を捕捉する厳密な境界のケースです。 

垂直ピボット ラインの場合、```
1
0 0 0 10
4
1 5
2 5
-1 5
-2 5
```アルゴリズムは (y/x) などの傾きを計算しません。 外積を使用してベクトルを比較するため、(PQ) の垂直方向に特別な分岐は必要ありません。 それぞれの側で、(PQ) から遠い点が外側の点であり、2 つの可能なチェーン (2,1) と (4,3) が与えられます。 両方とも長さ (2) があるため、最初のインデックスが答えを決定し、次の結果が得られます。```
Case #1: 2
2
1
```最後に、(n=100000) テストにより、すべての点が 1 つの光線上に配置されます。 角度のランクは 1 つの第 1 ランクのグループに崩壊するため、フェンウィック DP は 1 つより長いチェーンを作成することはありません。 アルゴリズムは引き続きソートと線形パスと出力のみを実行します。```
Case #1: 1
1
```この例では、入力に非常に類似した幾何学的方向を持つ点が多数含まれている場合でも、正確な方向グループを保存することが重要である理由も確認できます。
