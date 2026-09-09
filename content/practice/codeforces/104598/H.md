---
title: "CF 104598H - モデルの評価"
description: "このタスクでは、2 つの画像のピクセル強度を表す、サイズが $N × N$ の 2 つの正方形の数値グリッドを与えます。 各クエリに対して、これらのグリッド内の 2 つの対向する角によって指定される長方形のサブ領域が与えられます。"
date: "2026-06-30T03:07:07+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104598
codeforces_index: "H"
codeforces_contest_name: "GPL 2023 Advanced"
rating: 0
weight: 104598
solve_time_s: 86
verified: true
draft: false
---

[CF 104598H - モデル評価](https://codeforces.com/problemset/problem/104598/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 26 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 このタスクでは、両方のサイズの数値の 2 つの正方形グリッドが与えられます。$N \times N$、2 つの画像のピクセル強度を表します。 各クエリに対して、これらのグリッド内の 2 つの対向する角によって指定される長方形のサブ領域が与えられます。 その長方形について、画像内の値の合計を計算します。$A$画像内の値の合計$B$、次に、これら 2 つの合計の差の絶対値を出力します。 

各クエリは独立しているため、2 つの行列の長方形の合計を評価し、それらを比較することが繰り返し求められます。 

制約があるため、その素朴なアイデアは実行不可能になります。 と$N \le 800$、グリッドには最大$6.4 \times 10^5$セルがあり、最大で$7 \times 10^4$クエリ。 各クエリが領域内のすべてのセルをスキャンして四角形の合計を再計算する場合、最悪の場合の四角形はグリッド全体となり、約$800^2 \cdot 70000$制限時間をはるかに超えた作戦。 

一般的なエッジケースには大きな長方形が含まれます。$r_1 > r_2$または$c_1 > c_2$。 問題ステートメントでは任意の順序の座標が許可されているため、順序付けされたコーナーを想定する単純な実装では、最初に座標を正規化しない限り、不正なサブ領域が暗黙的に計算されます。 

もう 1 つの落とし穴はオーバーフローです。 各セルは最大で$10^9$、つまり、完全な$800 \times 800$合計が達する$6.4 \times 10^{14}$、これは 32 ビット整数に収まらず、64 ビット演算が必要です。 

## アプローチ

 ブルート フォース ソリューションは、四角形内のすべてのセルを反復処理し、両方のグリッドの値を合計することによって各クエリを処理します。 これは簡単で正しいですが、クエリごとのコストは長方形の面積によって異なります。 最悪の場合、各クエリは$O(N^2)$細胞、につながる$O(N^2 Q)$、これは大規模なものには遅すぎます$Q$。 

重要な点は、接頭辞合計テーブルを使用して四角形の合計を事前に計算できることです。 すべてのクエリの合計を再計算する代わりに、各グリッドを 2D プレフィックス合計配列に前処理して、包含-除外を使用して部分四角形の合計を一定時間で取得できるようにします。 両方の画像の合計が必要なため、2 つのプレフィックス合計配列を構築し、それらを減算して絶対値を取得することで各クエリに答えます。 

これにより、各クエリのスキャンが軽減されます。$O(N^2)$細胞から$O(1)$、これにより、問題は実行不可能なものから効率的なものに変わります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(N^2 Q)$|$O(1)$| 遅すぎる |
 | プレフィックス合計 |$O(N^2 + Q)$|$O(N^2)$| 承認済み |

 ## アルゴリズムのチュートリアル

 各画像の標準 2D プレフィックス合計を構築します。 

1. 両方のグリッドを読み取ります$A$そして$B$。 これらは、1 から 2 までのインデックスが付けられた行列として扱われます。$N$。 これにより、プレフィックス合計の境界処理が簡素化されます。 
2. プレフィックスの合計を構築する$SA$そして$SB$、各エントリには部分行列の合計が格納されます。$(1,1)$に$(i,j)$。 各値は、以前に計算されたプレフィックスを使用して計算され、各セルが 1 回処理されることが保証されます。 
3. クエリごとに、次のように座標を正規化します。$r_1 \le r_2$そして$c_1 \le c_2$。 これにより、入力コーナーが逆の順序で指定された場合に不正確な範囲が回避されます。 
4. の長方形の合計を計算します。$A$包含/除外を使用する:$$SA(r_2,c_2) - SA(r_1-1,c_2) - SA(r_2,c_1-1) + SA(r_1-1,c_1-1)$$同じ計算が適用されます$SB$。 
5. Output the absolute difference between the two results.

 Each query now uses only constant-time arithmetic.

 ### なぜ効果があるのか

 A 2D prefix sum encodes cumulative area sums so that any rectangle can be decomposed into four prefix regions. Inclusion-exclusion cancels overlapping areas exactly once. Because every cell contributes to exactly one combination of prefix terms, the computed value matches the true rectangle sum. The subtraction between two independently correct rectangle sums preserves correctness of the final absolute difference.

 ## Python ソリューション```python
import sys
input = sys.stdin.readline

def build_prefix(grid, n):
    ps = [[0] * (n + 1) for _ in range(n + 1)]
    for i in range(1, n + 1):
        row_sum = 0
        for j in range(1, n + 1):
            row_sum += grid[i-1][j-1]
            ps[i][j] = ps[i-1][j] + row_sum
    return ps

def rect_sum(ps, r1, c1, r2, c2):
    return (
        ps[r2][c2]
        - ps[r1-1][c2]
        - ps[r2][c1-1]
        + ps[r1-1][c1-1]
    )

def solve():
    n, q = map(int, input().split())

    A = [list(map(int, input().split())) for _ in range(n)]
    B = [list(map(int, input().split())) for _ in range(n)]

    psa = build_prefix(A, n)
    psb = build_prefix(B, n)

    out = []
    for _ in range(q):
        r1, c1, r2, c2 = map(int, input().split())
        if r1 > r2:
            r1, r2 = r2, r1
        if c1 > c2:
            c1, c2 = c2, c1

        sa = rect_sum(psa, r1, c1, r2, c2)
        sb = rect_sum(psb, r1, c1, r2, c2)
        out.append(str(abs(sa - sb)))

    print("\n".join(out))

if __name__ == "__main__":
    solve()
```入力を読み取った後、インデックス付けがプレフィックス構造と一致するように、両方の行列が明示的に保存されます。 クエリ内で境界チェックが繰り返されるのを避けるために、プレフィックス配列には 1 からインデックスが付けられます。 

長方形の合計関数は、標準の包含/除外の恒等式を適用します。 入力ではコーナーの順序が保証されないため、クエリ座標の正規化ステップは不可欠です。 

## 実用的な例

 最初のサンプルを考えてみましょう。 

両方の行列の接頭辞の合計を構築し、クエリを処理します$(1,1,1,3)$。 After normalization it remains unchanged. 長方形の合計はプレフィックス配列から定数時間で計算され、次の値に対して 11 が生成されます。$A$と9$B$したがって、出力は 2 になります。 

2 番目のクエリの場合$(3,3,1,2)$、正規化により生成される$(1,2,3,3)$。 プレフィックスの合計は両方の行列の合計が等しいため、差は 0 になります。 

これらの例では、順序付けされた座標入力と反転された座標入力の両方が正しく処理され、接頭辞の合計が一貫した四角形の集合体を返すことを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(N^2 + Q)$| プレフィックス構築と定数時間クエリ |
 | スペース |$O(N^2)$| 2 つのプレフィックス テーブルのストレージ |

 この境界では最大 800×800 の前処理が可能ですが、これは十分に制限内です。 最大 70,000 件のクエリのそれぞれに一定時間内に応答するため、ソリューションが時間の制約内に確実に適合します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    n, q = map(int, input().split())
    A = [list(map(int, input().split())) for _ in range(n)]
    B = [list(map(int, input().split())) for _ in range(n)]

    def build(ps):
        n = len(ps)
        for i in range(n):
            for j in range(n):
                ps[i][j] += (ps[i-1][j] if i else 0) + (ps[i][j-1] if j else 0) - (ps[i-1][j-1] if i and j else 0)
        return ps

    psa = build(A)
    psb = build(B)

    def get(ps, r1, c1, r2, c2):
        res = ps[r2][c2]
        if r1: res -= ps[r1-1][c2]
        if c1: res -= ps[r2][c1-1]
        if r1 and c1: res += ps[r1-1][c1-1]
        return res

    out = []
    for _ in range(q):
        r1, c1, r2, c2 = map(int, input().split())
        r1, r2 = sorted([r1-1, r2-1])
        c1, c2 = sorted([c1-1, c2-1])
        out.append(str(abs(get(psa, r1, c1, r2, c2) - get(psb, r1, c1, r2, c2))))

    return "\n".join(out)

# provided sample
assert run("""3 2
3 1 7
2 5 2
5 8 4
5 2 2
1 3 7
4 9 4
1 1 1 3
3 3 1 2
""") == "2\n0"

# custom cases
assert run("""1 1
5
3
1 1 1 1
""") == "2", "single cell"

assert run("""2 1
1 2
3 4
4 3
2 1
1 1 2 2
""") == "0", "equal sums"

assert run("""2 1
1 1
1 1
2 2
2 2
1 1 2 2
""") == "0", "all equal"

assert run("""3 1
1 2 3
4 5 6
7 8 9
9 8 7
6 5 4
3 2 1
1 1 3 3
""") == "0", "full symmetry"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1x1 grids | 2 | 単一セルの減算の正確性 |
 | 2x2 swapped values | 0 | フルグリッド全体でのキャンセル |
 | identical matrices | 0 | baseline correctness |
 | 反転構造グリッド | 0 | 一貫性のある四角形の集約 |

 ## 特殊なケース

 次のような単一セルのクエリ$(i,j,i,j)$プレフィックスの減算が正しく縮退するかどうかをテストします。 この場合、外側の接頭辞の項はすべてキャンセルされ、単一のセルだけが残るため、両方の合計がそのエントリまで減り、差が正しく計算されます。 

次のような逆座標クエリ$(r_2,c_2,r_1,c_1)$正規化が適用されているかどうかをテストします。 ソートを行わないと、接頭辞の減算は無効な領域にアクセスし、不正確な結果が生成されますが、座標を交換した後は四角形が有効になり、包含/除外がきれいに適用されます。 

フルグリッド クエリは、大きなプレフィックスの合計が 32 ビットの境界を超えるかどうかをテストします。 Python 整数を使用すると、オーバーフローが回避され、合計が次の値に達した場合でも正確さが維持されます。$10^{14}$規模。
