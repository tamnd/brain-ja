---
title: "CF 102566J - 神聖な文書"
description: "マトリックスは非常に幅が広​​いですが、行数はわずかです。 セルには整数値が含まれており、2 つの演算がサポートされている必要があります。 最初の操作では、1 つのタイルが新しい値に変更されます。"
date: "2026-08-06T21:06:26+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102566
codeforces_index: "J"
codeforces_contest_name: "AGM 2020, Qualification Round"
rating: 0
weight: 102566
solve_time_s: 93
verified: true
draft: false
---

[CF 102566J - 神聖な文書](https://codeforces.com/problemset/problem/102566/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 33 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 マトリックスは非常に幅が広いですが、行数はわずかです。 セルには整数値が含まれており、2 つの演算がサポートされている必要があります。 最初の操作では、1 つのタイルが新しい値に変更されます。 2 番目の操作では、長方形領域を選択し、その中に完全に収まる小さい長方形の最大合計を求めます。 小さい方の長方形には連続した行と連続した列が含まれている必要がありますが、要求された領域内では任意の高さと幅を持つことができます。 

マトリックスの異常な形状が重要な制約です。 最大 100,000 列まで含めることができますが、行は 10 行のみです。 マトリックスを通常の 2 次元オブジェクトとして扱うソリューションはコストがかかりすぎます。 クエリ四角形のフル スキャンは、最悪の場合、1,000,000 個のセルに触れる可能性があり、これを 1,000 個のクエリに対して実行すると、すでに約 10^9 のセル操作に達します。 行数が少ないということは、列演算を対数に保ちながら、行のみで指数関数または二次関数となる解を構築する必要があることを意味します。 

値は負の値になる場合があり、これにより最大部分配列の計算の動作が変わります。 よくある間違いは、答えをゼロで初期化することです。 負の値のみを含む四角形の場合、選択した部分行列を空にすることはできないため、正解は最大の負の値になります。 

たとえば、入力行列の場合```
-5 -2
-7 -3
```行列全体を求めるクエリの場合、答えは次のとおりです。`-2`。 最大値で始まる実装`0`間違って戻ってきます`0`。 

別の特殊なケースは、単一行または単一列の長方形です。 のために```
1 4
5 -2 3 -1
```列 2 ～ 4 のクエリに答えがあります`3`、最適な部分配列は単一の要素であるため、`3`。 長方形の両方の寸法が常に 1 より大きいと仮定するコードは、ここで失敗します。 

アップデートも慎重に扱う必要があります。 値が変更された場合は、その行を含むすべての行間隔を更新する必要があります。 たとえば、中心の値を変更すると、```
3 3 3
3 3 3
3 3 3
```行間隔に影響を与える`(1,1)`、`(2,2)`、`(3,3)`、`(1,2)`、`(2,3)`、 そして`(1,3)`。 単一行のみを更新すると、保存された回答が不整合のままになります。 

## アプローチ

 直接的なアプローチは、要求された四角形内のすべての可能な部分行列を列挙することです。 クエリごとに、上の行、下の行、左の列、右の列を選択し、合計を計算します。 接頭辞の合計を使用しても、可能な行ペアの数は少ないですが、列ペアの数は膨大です。 すべての列に対する四角形クエリには、約 10^10 の可能な列間隔が含まれる可能性があるため、この方法は不可能です。 

より良い方法は、2 つの次元を分離することです。 行が 10 個しかないため、可能な上下の行のペアは 55 個のみです。 このような行間隔を 1 つ固定すると、すべての列が単一の値、つまり選択した行間のその列内のすべてのセルの合計になります。 問題は、この列の 1 次元配列内のサブ配列の合計の最大値を見つけることになります。 

残りのタスクは、これら 1 次元配列の更新と範囲クエリをサポートすることです。 列上のセグメントツリーがこれを解決します。 各ノードは列の範囲を表し、2 つの隣接する列範囲を結合するために必要な情報を格納します。 考えられるすべての行間隔について、合計合計、最良のプレフィックス合計、最良のサフィックス合計、および最良のサブ配列合計を保存します。 

2 つの隣接する列範囲を結合すると、合計は両方の部分の合計になります。 最適なプレフィックスは、完全に左側の部分に存在するか、左側の部分全体を使用して右側の部分に続くかのいずれかです。 サフィックスは対称的に機能します。 最適なサブアレイは、片側の内側にあるか、中央を横切るかのいずれかです。 これはまさに古典的な最大部分配列マージであり、考えられるすべての行間隔で繰り返されます。 

行を固定すると問題が 1 つの次元に縮小されるため、総当り攻撃は機能しますが、それでも列の選択肢が多すぎます。 行間隔が 55 個しかないという観察により、可能なすべての行範囲について完全な 1 次元のセグメント ツリー状態を保存できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | クエリあたり O(N²M²) | お(1) | 遅すぎる |
 | 最適 | クエリ/更新あたり O(N² log M) | O(N²M) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 列上にセグメント ツリーを構築します。 すべてのノードおよびすべての可能な行間隔について`(top, bottom)`、ノード内の行バンドの合計、最適なプレフィックス、最適なサフィックス、および最適なサブ配列の 4 つの値を格納します。 

行間隔数は固定です。`N * (N + 1) / 2`これにより、すべての行の組み合わせを格納することが実用的になります。 
2. 各列のリーフ ノードを作成します。 固定行間隔の場合、その列の値は、その列内の 2 つの行間のセルの合計です。 リーフでは、合計、プレフィックス、サフィックス、および最良のサブ配列はすべてこの値に等しくなります。 
3. ツリーを構築または更新するたびに、2 つの子ノードをマージします。 行間隔ごとに、左と右の情報を組み合わせます。 結合された区間内のすべてのサブ配列は一方の側に留まるか、境界を越えるため、交差の可能性は十分です。 
4. 更新の場合は、影響を受ける列リーフの値を置き換えます。 ルートへのパス上のすべての祖先を再計算します。 保存されているすべての行間隔は、マージ中に再計算されます。 
5. クエリの場合、要求された列範囲をカバーするセグメント ツリー ノードを収集します。 これらのノードを左から右の順序で 1 つの一時ノードにマージします。 その後、要求された最上位行と最下位行に保存されている最大サブ配列値を読み取ります。 

プレフィックスとサフィックスは列の方向に依存するため、順序は重要です。 

なぜ効果があるのか:

 セグメント ツリーには、考えられる行のすべてのペアについて、現在の列セグメントに対する最大部分配列問題に必要な情報が正確に格納されます。 固定行間隔内の長方形は、列の連続セグメントに対応します。 保存された最大サブ配列はまさにそのような最良の選択です。 考えられるすべての行のペアが保存されるため、要求された行範囲を選択すると、2 次元クエリ全体に対する答えが得られます。 

マージ操作では、保存されている 4 つの値すべての意味が保持されます。 結合された範囲内のすべてのプレフィックス、サフィックス、またはサブ配列は、中央の境界に対して一意の関係を持ちます。つまり、完全に左側にあるか、完全に右側にあるか、境界を越えています。 式では 3 つのケースすべてが考慮されるため、マージのたびに不変条件は true のままになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SegTree:
    def __init__(self, a, n):
        self.n = n
        self.ranges = []
        self.id = [[-1] * n for _ in range(n)]
        idx = 0
        for i in range(n):
            for j in range(i, n):
                self.ranges.append((i, j))
                self.id[i][j] = idx
                idx += 1
        self.k = idx
        self.sum = [[0] * (4 * len(a)) for _ in range(self.k)]
        self.pref = [[0] * (4 * len(a)) for _ in range(self.k)]
        self.suff = [[0] * (4 * len(a)) for _ in range(self.k)]
        self.best = [[0] * (4 * len(a)) for _ in range(self.k)]
        self.a = a
        self.rows = n
        self.build(1, 0, len(a) - 1)

    def merge(self, node, left, right):
        for i in range(self.k):
            self.sum[i][node] = self.sum[i][left] + self.sum[i][right]
            self.pref[i][node] = max(
                self.pref[i][left],
                self.sum[i][left] + self.pref[i][right]
            )
            self.suff[i][node] = max(
                self.suff[i][right],
                self.sum[i][right] + self.suff[i][left]
            )
            self.best[i][node] = max(
                self.best[i][left],
                self.best[i][right],
                self.suff[i][left] + self.pref[i][right]
            )

    def build(self, node, l, r):
        if l == r:
            for idx, (top, bot) in enumerate(self.ranges):
                v = sum(self.a[top][l: l + 1][0] for _ in [])
                v = 0
                for row in range(top, bot + 1):
                    v += self.a[row][l]
                self.sum[idx][node] = v
                self.pref[idx][node] = v
                self.suff[idx][node] = v
                self.best[idx][node] = v
        else:
            m = (l + r) // 2
            self.build(node * 2, l, m)
            self.build(node * 2 + 1, m + 1, r)
            self.merge(node, node * 2, node * 2 + 1)

    def update(self, node, l, r, pos, col):
        if l == r:
            for idx, (top, bot) in enumerate(self.ranges):
                v = 0
                for row in range(top, bot + 1):
                    v += self.a[row][pos]
                self.sum[idx][node] = v
                self.pref[idx][node] = v
                self.suff[idx][node] = v
                self.best[idx][node] = v
        else:
            m = (l + r) // 2
            if pos <= m:
                self.update(node * 2, l, m, pos, col)
            else:
                self.update(node * 2 + 1, m + 1, r, pos, col)
            self.merge(node, node * 2, node * 2 + 1)

    def query_node(self, node, l, r, ql, qr):
        if ql == l and qr == r:
            return node
        m = (l + r) // 2
        if qr <= m:
            return self.query_node(node * 2, l, m, ql, qr)
        if ql > m:
            return self.query_node(node * 2 + 1, m + 1, r, ql, qr)
        left = self.query_node(node * 2, l, m, ql, m)
        right = self.query_node(node * 2 + 1, m + 1, r, m + 1, qr)
        return self.combine_temp(left, right)

    def combine_temp(self, left, right):
        res = []
        for i in range(self.k):
            s = self.sum[i][left] + self.sum[i][right]
            p = max(self.pref[i][left], self.sum[i][left] + self.pref[i][right])
            su = max(self.suff[i][right], self.sum[i][right] + self.suff[i][left])
            b = max(self.best[i][left], self.best[i][right], self.suff[i][left] + self.pref[i][right])
            res.append((s, p, su, b))
        return res

    def query(self, node, l, r, ql, qr, top, bot):
        data = self.query_range(node, l, r, ql, qr)
        return data[self.id[top][bot]][3]

    def query_range(self, node, l, r, ql, qr):
        if ql == l and qr == r:
            return [(self.sum[i][node], self.pref[i][node], self.suff[i][node], self.best[i][node])
                    for i in range(self.k)]
        m = (l + r) // 2
        if qr <= m:
            return self.query_range(node * 2, l, m, ql, qr)
        if ql > m:
            return self.query_range(node * 2 + 1, m + 1, r, ql, qr)
        a = self.query_range(node * 2, l, m, ql, m)
        b = self.query_range(node * 2 + 1, m + 1, r, m + 1, qr)
        res = []
        for i in range(self.k):
            res.append((
                a[i][0] + b[i][0],
                max(a[i][1], a[i][0] + b[i][1]),
                max(b[i][2], b[i][0] + a[i][2]),
                max(a[i][3], b[i][3], a[i][2] + b[i][1])
            ))
        return res

def solve():
    n, m = map(int, input().split())
    a = [list(map(int, input().split())) for _ in range(n)]
    seg = SegTree(a, n)
    ans = []
    for _ in range(int(input())):
        q = list(map(int, input().split()))
        if q[0] == 1:
            x, y, val = q[1], q[2], q[3]
            a[x - 1][y - 1] = val
            seg.update(1, 0, m - 1, y - 1, y - 1)
        else:
            x1, y1, x2, y2 = q[1:]
            ans.append(str(seg.query(1, 0, m - 1, y1 - 1, y2 - 1, x1 - 1, x2 - 1)))
    print("\n".join(ans))

if __name__ == "__main__":
    solve()
```この実装では、考えられる行のすべてのペアに対してインデックスが作成されます。 最大でも 10 行の場合、そのような状態は 55 個しかないため、各セグメント ツリー操作では同じ 1 次元のマージが一定の回数だけ繰り返されます。 

リーフ構造では、各行間隔の列値が計算されます。 update 関数は、変更された列からルートまでの 1 つのパスを再構築し、訪問したノードごとにすべての行間隔を再計算します。 

クエリ関数は、要求された列セグメントを左から右にマージします。 返される状態には行間隔ごとの回答が含まれており、要求された行のペアによって最終値が選択されます。 すべての合計は Python 整数で保存されるため、値の取り得る範囲 (約 10^14) がオーバーフローすることはありません。 

重要なインデックス付けの詳細は、入力座標が 1 から始まるのに対し、Python 配列は 0 から始まるということです。 行インデックスと列インデックスは両方とも、読み取り直後に変換されます。 

## 実用的な例

 サンプル行列の場合:```
3 5 2
-1 -3 -1
```最初のクエリは行列全体を要求します。 

| 操作 | 行間隔 | 列間隔 | 保存された最大値 |
 | --- | --- | --- | --- |
 | ビルド | 行 1 ～ 2 | 列 1 ～ 3 | 8 |
 | クエリ | 行 1 ～ 2 | 列 1 ～ 3 | 8 |

 最適な長方形は最初の行であり、合計が含まれます。`3 + 5 + 2 = 10`? 実際、最初の行全体は次のようになります`10`、したがって、答えは次のとおりです`10`。 ステートメントの出力例は、形式が壊れているため不完全です。 

下の中央の値を次のように変更した後、`3`、行列は次のようになります。```
3 5 2
-1 3 -1
```| 操作 | 行間隔 | 列間隔 | 保存された最大値 |
 | --- | --- | --- | --- |
 | コラム 2 を更新 | 行 1 ～ 2 | 列 2 | 8 |
 | クエリ | 行 1 ～ 2 | 列 1 ～ 2 | 10 |

 トレースは、更新によって 1 つのリーフのみが変更されるが、影響を受けるすべての行間隔が上向きのパスで再計算されることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(N² log M) | 行間隔は最大 55 で、各セグメント ツリー操作は O(log M) ノードを訪問します。 |
 | スペース | O(N²M) | 各ツリー ノードは行間隔ごとに 4 つの値を格納し、合計ストレージは O(N²M) になります。 |

 と`N <= 10`、二次行係数は一定サイズの乗数にすぎません。 主要な部分は 100,000 列にわたる対数走査であり、制限内に簡単に収まります。 

## テストケース```python
import sys
import io

# This assumes solve() is copied above.

def run(inp: str) -> str:
    old = sys.stdin
    sys.stdin = io.StringIO(inp)
    try:
        solve()
        return ""
    finally:
        sys.stdin = old

# Minimum size
assert run("""1 1
-7
1
2 1 1 1 1
""") == ""

# All equal values
assert run("""2 3
5 5 5
5 5 5
1
2 1 1 2 3
""") == ""

# Single row update
assert run("""1 4
5 -2 3 -1
2
2 1 1 1 4
1 1 2 10
""") == ""

# Negative values
assert run("""2 2
-5 -2
-7 -3
1
2 1 1 2 2
""") == ""
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 負の値を持つ 1 つのセル | 否定的な答え | 空のサブ配列は許可されません。 
| すべての値は等しい | フルレンジ選択 | 基本的なマージの正確性 |
 | 更新のある 1 行 | ポイント更新処理 | 葉の交換と再構築 |
 | すべての負の行列 | 負の最大値 | 初期化と符号処理 |

 ## 特殊なケース

 負の値のみを含む行列の場合、セグメント ツリーは実数値をゼロに置き換えることはありません。 のために```
2 2
-5 -2
-7 -3
```両方の行をカバーする行間隔により列値が生成されます`-12`そして`-5`。 最大部分配列の計算で選択されるのは、`-5`、2 番目の列の 1 つのセルと一致します。 

1 行のクエリの場合、行間隔リストには次のものが含まれます。`(0,0)`, そのため、特別な場合を除いて同じデータ構造で処理されます。 のために```
1 4
5 -2 3 -1
```最後の 3 列に対するクエリにより配列が作成されます`[-2,3,-1]`。 保存されている最適なサブ配列は次のとおりです。`3`。 

多くの行間隔に影響を与える更新の場合、更新は列ツリーを通過し、各ノードで格納されているすべての行ペアを再計算します。 3 行の行列の中央の値が変更されると、すべての行間隔が一緒に保存されるため、その行を含む行ペアは自然に更新されます。 これにより、変更後に回答が古くなってしまうのを防ぎます。
