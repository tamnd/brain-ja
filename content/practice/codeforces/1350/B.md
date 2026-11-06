---
title: "CF 1350B - オラックとモデル"
description: "いくつかの独立したテスト ケースが与えられています。 In each one, we have a sequence of model sizes indexed from 1 to n. インデックスのサブセットを選択することはできますが、選択したインデックスは昇順に保持する必要があり、これはインデックスのサブシーケンスを選択することと同じです。"
date: "2026-06-11T14:32:29+07:00"
tags: ["codeforces", "competitive-programming", "dp", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 1350
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 641 (Div. 2)"
rating: 1400
weight: 1350
solve_time_s: 96
verified: true
draft: false
---

[CF 1350B - Orac とモデル](https://codeforces.com/problemset/problem/1350/B)

 **評価:** 1400
 **タグ:** dp、数学、数論
 **解決時間:** 1 分 36 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 いくつかの独立したテスト ケースが与えられています。 In each one, we have a sequence of model sizes indexed from 1 to n. インデックスのサブセットを選択することはできますが、選択したインデックスは昇順に保持する必要があり、これはインデックスのサブシーケンスを選択することと同じです。 

A chosen subsequence is valid only if every consecutive pair of chosen indices satisfies two constraints at the same time. First, the next index must be a multiple of the previous index. Second, the size at the next index must be strictly larger than the size at the previous index. The goal is to maximize how many indices can be chosen under these rules.

 制約は十分に大きく、テスト ケースごとに n が 100,000 に達し、すべてのテスト ケースの合計も最大 100,000 に達します。 これにより、インデックスのすべてのペアに対する二次アプローチが即座に除外されます。 A solution that checks transitions between all pairs or tries to build subsequences explicitly will be too slow. We need something closer to linear or n log n per test case.

 A subtle point is that the structure is not a general subsequence DP over indices. The divisibility constraint forces transitions only from i to its multiples, which makes the graph sparse and structured. Ignoring this structure leads to an O(n^2) DP that will fail.

 Edge cases that often break naive solutions include arrays where all values are equal, for example n = 5, s = [7, 7, 7, 7, 7]. ここでは、厳密な増加は不可能であるため、有効な遷移は存在しません。そのため、答えは 1 でなければなりません。もう 1 つのエッジ ケースは、値は厳密に増加するが、インデックスが割り算構造と一致しない場合です (n = 5、s = [1, 2, 3, 4, 5])。 値が増加しても、1 → 2 → 4 のような乗算インデックス チェーンのみが許可されるため、答えは値の順序ではなくインデックス構造によって制限されます。 

## アプローチ

 A direct brute-force strategy would attempt to compute the longest valid subsequence ending at every index. For each index i, we would check all previous indices j < i and verify whether j divides i and s[j] < s[i]. If both hold, we update dp[i] = max(dp[i], dp[j] + 1). This is correct because it directly follows the definition of a valid chain.

 However, this approach checks up to i previous elements for every i, leading to roughly n(n−1)/2 transitions in the worst case. With n = 100,000, this is far beyond feasible limits.

 The key observation is that transitions are not arbitrary. From any index i, we only need to consider its multiples i, 2i, 3i, and so on. This flips the direction of thinking: instead of checking all previous states for i, we propagate dp values forward along multiples. This is efficient because the number of multiples over all i is bounded by n log n.

 i で終わる最適なチェーンとして dp[i] を計算します。 Then for every i, we try to extend it to all multiples j = 2i, 3i, ..., updating dp[j] if s[j] is larger than s[i]. This converts the problem into a structured DP over a divisibility graph.

 | アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | すべてのペアに対するブルート フォース DP | O(n^2) | O(n) | 遅すぎる |
 | 倍数上の DP (割り算伝播) | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 ### ## アルゴリズムのチュートリアル

 1. Initialize a dp array where dp[i] represents the maximum length of a valid chain ending at index i. Start with dp[i] = 1 for all i because each element alone forms a valid chain.
 2. i を 1 から n まで昇順に繰り返します。 For each i, we treat it as a starting point for extending chains forward.
 3. For each i, iterate over its multiples j = 2i, 3i, 4i, up to n. For each such j, check whether s[j] is strictly greater than s[i]. そうであれば、dp[j] = max(dp[j], dp[i] + 1) を更新します。

このステップが機能するのは、有効な遷移がインデックスの割り算条件を満たさなければならないためであり、したがって i の可能なすべての後継は正確にその倍数になります。 
4. 改善を前進させ続けます。 i を昇順に処理するため、i から拡張するときに、より小さいインデックスからのすべての寄与がすでに考慮されます。 
5. すべてのインデックスを処理した後の答えは、dp の最大値になります。 

### なぜ効果があるのか

 dp 状態は、遷移が有効な可分性エッジに沿ってのみ発生するという制約の下で、各インデックスで終了する最長の有効なチェーンをキャプチャします。 すべての有効なシーケンスは、次の各インデックスが前のインデックスの倍数である連続したステップに分解できるため、より小さいエンドポイントを処理するときにすべての遷移が 1 回だけ考慮されます。 s[j] が厳密に大きい場合にのみ dp[j] を更新するため、値に必要な単調性が維持されます。 j の有効な先行要素はすべて j の約数でなければならず、したがって反復順序の早い段階で処理されるため、有効な拡張子が失われることはありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        n = int(input())
        s = [0] + list(map(int, input().split()))

        dp = [1] * (n + 1)
        ans = 1

        for i in range(1, n + 1):
            val = dp[i]
            if val > ans:
                ans = val

            j = 2 * i
            while j <= n:
                if s[j] > s[i]:
                    if dp[j] < val + 1:
                        dp[j] = val + 1
                j += i

        print(ans)

if __name__ == "__main__":
    solve()
```このソリューションは、各位置が長さ 1 のチェーンとして始まる dp 配列を構築します。 複数のネストされたループにより、j が i で割り切れるすべての有効な遷移 i → j が 1 回だけ考慮されることが保証されます。 s[j] > s[i] の比較では、サイズの厳密な増加条件が適用されます。 

実装の微妙な詳細は、複数回反復する前に dp[i] をローカル変数に格納することです。 これにより、配列検索の繰り返しが防止され、伝播が明確になります。 もう 1 つの重要な点は、1 ベースのインデックスを使用することです。これにより、問題ステートメントと直接一致し、複数を生成する際の off-by-one エラーが回避されます。 

## 実用的な例

 ### 例 1

 入力:

 n = 4、s = [5、3、4、6]

 dp を段階的に計算します。 

| 私 | dp[i] 前 | j の倍数をチェック | 更新情報 | |後の dp
 | --- | --- | --- | --- | --- |
 | 1 | 1 | 2、3、4 | 3 と 4 を更新 | [1,2,2,3] |
 | 2 | 2 | 4 | 6 該当なし | [1,2,2,3] |
 | 3 | 2 | なし | なし | [1,2,2,3] |
 | 4 | 3 | なし | なし | [1,2,2,3] |

 最長のチェーンは長さ 3 のインデックス 4 で終わり、1 → 2 → 4 に対応します。 

### 例 2

 入力:

 n = 5、s = [5、4、3、2、1]

 ここでは、増加する値の遷移は存在しません。 

| 私 | dp[i] | j | の倍数 更新情報 |
 | --- | --- | --- | --- |
 | 1 | 1 | 2、3、4、5 | なし (すべて小さい) |
 | 2 | 1 | 4 | なし |
 | 3 | 1 | なし | なし |
 | 4 | 1 | なし | なし |
 | 5 | 1 | なし | なし |

 すべての dp は 1 のままであるため、答えは 1 になります。これにより、値の増加がなければ割り算だけでは不十分であることが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 各インデックス i はその倍数にわたって反復され、すべての i にわたる倍数の合計調和系列は n log n | によって制限されます。 
| スペース | O(n) | サイズ n の dp 配列 |

 すべてのテスト ケースにわたる更新試行の合計数は除数と倍数のペアの数に比例し、100,000 log 100,000 以内に収まるため、複雑さは十分に高速です。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    t = int(input())
    out = []
    for _ in range(t):
        n = int(input())
        s = [0] + list(map(int, input().split()))

        dp = [1] * (n + 1)
        ans = 1

        for i in range(1, n + 1):
            ans = max(ans, dp[i])
            val = dp[i]
            for j in range(2 * i, n + 1, i):
                if s[j] > s[i]:
                    dp[j] = max(dp[j], val + 1)

        out.append(str(ans))

    return "\n".join(out)

# provided samples
assert run("""4
4
5 3 4 6
7
1 4 2 3 6 4 9
5
5 4 3 2 1
1
9
""") == """2
3
1
1"""

# custom cases
assert run("""1
1
10
""") == "1", "single element"

assert run("""1
5
1 2 3 4 5
""") == "3", "chain 1->2->4 or 1->3->... limited by divisibility"

assert run("""1
6
1 1 1 1 1 1
""") == "1", "equal values block transitions"

assert run("""1
8
1 3 2 4 6 5 7 8
""") == "4", "mixed structure"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | n=1 シングル | 1 | 最小限のエッジケース |
 | 値を増やす | 3 | 分割性の制約により成長が制限される |
 | すべて等しい | 1 | 厳密な不等式はすべての遷移をブロックします。 
| 混合パターン | 4 | インターリーブ下の正確性 |

 ## 特殊なケース

 単一要素を含む最小限の入力は、遷移が不可能であるため、すぐに 1 を返します。 dp は 1 に初期化され、複数のループで更新が生成されないため、アルゴリズムはこれを自然に処理します。 

s = [7, 7, 7, 7] など、すべての値が等しい場合、s[j] > s[i] のすべての比較は失敗するため、dp はすべてのインデックスで 1 のまま変化しません。 したがって、最終的な最大値は 1 となり、正しい出力と一致します。 

s = [1, 2, 3, 4, 5, 6] のように、値が増加してもインデックスが乗算構造とうまく一致しない場合、1 → 2 → 4 または 1 → 3 → 6 のようなインデックスの割り算に従ったチェーンのみが可能です。 アルゴリズムはこれを正しく尊重します。これは、任意のインデックス間ではなく、倍数に沿ってのみ伝播するためです。
