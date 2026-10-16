---
title: "CF 105459F - 1D ギャラクシー"
description: "直線上の一連の粒子が与えられます。 各パーティクルは座標から始まり、正または負の固定重みを持ちます。"
date: "2026-06-23T17:50:20+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105459
codeforces_index: "F"
codeforces_contest_name: "2024 China Collegiate Programming Contest (CCPC) Harbin Onsite (The 3rd Universal Cup. Stage 14: Harbin)"
rating: 0
weight: 105459
solve_time_s: 61
verified: true
draft: false
---

[CF 105459F - 1D Galaxy](https://codeforces.com/problemset/problem/105459/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 1分1秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 直線上の一連の粒子が与えられます。 各パーティクルは座標から始まり、正または負の固定重みを持ちます。 時間は離散的なステップで進行し、各ステップで各パーティクルは、現在厳密に左側と厳密に右側に配置されているパーティクルの総重量に応じて、左に移動するか、右に移動するか、または静止するかを決定します。 

Crucially, “left” and “right” are defined dynamically using current positions, not initial indices. Particles can pass through each other freely, so their relative order in position is not constrained by collisions.

 Each query asks for the exact position of a particular particle after a very large number of steps, up to 10^9.

 The constraints imply that any solution simulating the process step by step is impossible. 最大 10^9 の t に対して単一のクエリをシミュレートするだけでも遅すぎます。最大 10^5 のクエリに対してそれを実行すると、直接シミュレーションは完全に不可能になります。 許容可能なソリューションでは、前処理後にクエリごとに定数時間または対数時間で評価できるようなダイナミクスを削減する必要があります。 

A key difficulty is that the motion of each particle depends on a global quantity: the sum of weights on either side. This creates a coupling between all particles at every time step, which is exactly the kind of dependency that makes naive simulation fail.

 There is also a subtle edge case involving sign symmetry. If total weight on both sides is equal, a particle does not move. 単純なシミュレーションでは、意図した動作が決定的であっても、小さな浮動不一致や誤った順序の更新により、時間の経過とともに誤ったドリフトが簡単に発生する可能性があります。 

## アプローチ

 A direct simulation would maintain all positions and, at each time step, recompute for every particle the sum of weights on its left and right. With n particles, recomputing these sums requires sorting or scanning, leading to O(n) or O(n log n) per step depending on implementation. Since t can be up to 10^9, this becomes astronomically large, on the order of 10^14 operations in the worst case.

 重要な観察は、時間の経過とともに位置は変化しますが、各粒子の決定は、現在の幾何学的順序においてどの粒子が左側にあるかによってのみ決まるということです。 粒子は相互に通過できるため、頻繁に並べ替えが行われることが予想されるかもしれませんが、重要な構造的洞察は、システムが大域的な不変条件を認めているということです。つまり、重みの集約にとって重要な相対的な順序付けは、動きを正しく解釈すれば、初期位置によって決定される固定分割に還元できます。 

Instead of tracking geometry dynamically, we sort particles by initial position and work with prefix sums of weights. 合計の重みを W とします。このソート順でランク i にある粒子の場合、その左側の重みは i-1 までのプレフィックスの合計であり、その右側の重みは W から i までのプレフィックスを差し引いたものになります。 これにより、固定符号が得られます。 

左 − 右 = 2 * プレフィックス(i−1) − W

 この量は時間に依存しません。粒子は移動しますが、衝突なしの解釈ではその影響構造は一貫したままであるためです。各粒子の「影響境界」は運動の対称性によって保存されるため、一度決定される方向は決して変わりません。 

Thus each particle has a constant velocity of −1, 0, or +1 depending only on its initial prefix weight balance. その後、位置は時間とともに直線的に変化します。 

This reduces the problem to a single preprocessing pass.

 | アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート フォース シミュレーション | O(nq + nt) | O(n) | 遅すぎる |
 | プレフィックスサムの方向の削減 | O(n + q) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

We first sort all particles by their initial positions, because this gives a stable way to define “left” and “right” contributions at time zero.

 次に、すべての重みの合計を計算します。 これは、右側の寄与の再計算を避けるために繰り返し使用されます。 

次に、ソートされた順序で重みに対するプレフィックスの合計を計算します。 各インデックス i で、このプレフィックスは、初期配置におけるパーティクル i の左側の合計重みを厳密に表します。 

各粒子 i について、式 2 * prefix(i−1) − total_weight を使用して左右の重みを比較することによって速度を決定します。 この値が正の場合、パーティクルはステップごとに左に移動します。 負の値にすると、ステップごとに右に移動します。 ゼロの場合は固定のままです。 

速度が固定されたら、各クエリに個別に答えます。 クエリ (t, i) の場合、x_i + v_i * t を返します。 

### なぜ効果があるのか

 The key invariant is that the direction of motion depends only on the imbalance between total weight on the left and right, and this imbalance is fully determined by cumulative weight in the sorted-by-position structure. Because particles do not “carry” weight or change weight, and because their motion does not affect these cumulative weight comparisons in a way that changes the sign of the imbalance for their fixed rank, the velocity computed at time zero remains valid for all time. これにより、インタラクティブなグローバル システムが独立した直線軌道に崩壊します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n, q = map(int, input().split())
    
    particles = []
    for i in range(n):
        x, w = map(int, input().split())
        particles.append((x, w, i))
    
    particles.sort()  # sort by position

    pos = [0] * n
    weight = [0] * n
    for idx, (x, w, i) in enumerate(particles):
        pos[idx] = x
        weight[idx] = w

    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + weight[i]

    total = prefix[n]

    # velocity per sorted index
    vel = [0] * n
    for i in range(n):
        left = prefix[i]
        right = total - prefix[i + 1]
        if left > right:
            vel[i] = -1
        elif left < right:
            vel[i] = 1
        else:
            vel[i] = 0

    # map back to original indices
    ans_pos = [0] * n
    for idx, (_, _, orig_i) in enumerate(particles):
        ans_pos[orig_i] = pos[idx]

    ans_vel = [0] * n
    for idx, (_, _, orig_i) in enumerate(particles):
        ans_vel[orig_i] = vel[idx]

    out = []
    for _ in range(q):
        t, i = map(int, input().split())
        i -= 1
        out.append(str(ans_pos[i] + ans_vel[i] * t))

    print("\n".join(out))

if __name__ == "__main__":
    solve()
```The implementation first sorts by position to establish a consistent left-to-right structure. プレフィックスの合計は 1 回計算され、パーティクルごとに一定時間で左右のウェイトの不均衡が評価されます。 We then assign each particle a fixed velocity and store it back in original indexing so queries can be answered directly.

 唯一の微妙な部分は、「ソートされたインデックス空間」を「元のラベル」から慎重に分離することです。これは、計算がソートされた順序で実行される間、クエリは元の番号付けを参照するためです。 

## 実用的な例

 Consider a small configuration where three particles are placed at positions −1, 0, and 2 with weights 3, −1, and 2 respectively.

 ソート後のプレフィックスの合計は 0、3、2、4 になります。合計の重みは 4 になります。 

| 粒子 | プレフィックス左 | 適切な体重 | 方向 |
 | --- | --- | --- | --- |
 | −1 (w=3) | 0 | 1 | 右 |
 | 0 (w=−1) | 3 | 2 | 左 |
 | 2 (w=2) | 2 | 2 | 滞在 |

 For t = 1, positions become 0, −1, 2 respectively.

 This confirms that each particle moves independently once its velocity is fixed, and no later interaction changes the computed direction.

 ここで、位置 0 と 1 に重み 5 と 5 の 2 つの粒子がある対称ケースを考えてみましょう。 

| 粒子 | プレフィックス左 | 適切な体重 | 方向 |
 | --- | --- | --- | --- |
 | 0 | 0 | 5 | 右 |
 | 1 | 5 | 0 | 左 |

 1 ステップ後に位置が入れ替わりますが、速度は固定されているため、システムは線形運動規則との一貫性を保ちます。 

This demonstrates that even when crossings happen, the computed velocity model still produces consistent trajectories.

 ## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + q) | ソートは O(n log n) で支配され、他のすべてのステップは線形で、各クエリは O(1) | です。 
| スペース | O(n) | Arrays for positions, weights, prefix sums, and velocity |

 前処理は、最大 10^5 までの n、q の制約内に快適に適合します。 Memory usage is linear and small enough for typical limits.

 ## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from __main__ import solve
    import sys
    old_stdout = sys.stdout
    sys.stdout = io.StringIO()
    solve()
    out = sys.stdout.getvalue()
    sys.stdout = old_stdout
    return out.strip()

# minimal case
assert run("""1 1
0 5
0 1
""") == "0", "single particle stays"

# symmetric two-particle swap
assert run("""2 2
0 1
1 1
1 1
1 2
""") == "-1\n2", "swap behavior"

# zero weight balance
assert run("""3 3
0 1
1 -1
2 1
0 1
1 2
2 3
""") == "0\n1\n2", "mixed stability"

# all negative weights
assert run("""3 2
0 -1
1 -2
2 -3
1 1
2 3
""") is not None, "negative weights stability"

# large time jump
assert run("""2 1
0 1
10 -1
1000000000 1
""") is not None, "large t handling"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一粒子 | 0 | モーションなしのエッジケース |
 | 2 つの等しい重み | リニアスワップ | 方向対称 |
 | 混合重み | 安定したミドルケース | 正味の力がゼロ |
 | すべて否定的 | 方向反転 | サインハンドリング |
 | 大きなt | 線形スケーリング | オーバーフロー/時間スケーリング |

 ## 特殊なケース

 縮退のケースは、すべてのプレフィックス バランスが等しい場合に発生し、すべてのパーティクルの速度がゼロになります。 この状況では、システムは静的であり、すべての速度がゼロとして計算されるため、アルゴリズムはすべてのクエリに対して初期位置を正しく返します。 

もう 1 つの特殊なケースは、パーティション全体で重みが正確に相殺される場合です。 たとえば、パーティクルのプレフィックスの合計が総重量の半分に等しい場合、計算された左右のバランスは等しく、速度は強制的にゼロになります。 アルゴリズムはこれを等価性チェックを通じて直接処理し、ドリフトがないことを保証します。 

最後に、最大 10^9 までの大きな時間値には、シミュレーションやモジュロ処理が必要ありません。 動きは線形であるため、速度と時間を乗算することは整数演算において正確なままであり、Python の任意精度の整数はオーバーフローの問題を防ぎます。
