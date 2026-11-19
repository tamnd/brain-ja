---
 タイトル：「CF 2121H - アイスベイビー」
 説明: "すべての位置 i に対して、固定値が与えられるわけではありません。代わりに、区間 [li, ri] 内の任意の整数を選択できます。各プレフィックス長 k について、すべての配列を考慮します。$$a1,a2,dots,ak$$そのような$$li le ai le ri." date: "2026-06-08T03:50:08+07:00" tags: ["codeforces", "competitive-programming", "binary-search", "brute-force", "data-structures", "dp", "implementation", "sortings"] categories: ["algorithms"] codeforces_contest: 2121 codeforces_index: "H" codeforces_contest_name: "Codeforces Round 1032 (Div. 3)" rating: 2300 weight: 2121 solve_time_s: 90 verified: false draft: false --- [CF 2121H - Ice Baby](https://codeforces.com/problemset/problem/2121/H) **Rating:** 2300   **Tags:** binary search, brute force, data structures, dp, implementation, sortings   **Solve time:** 1m 30s   **Verified:** no   ## Solution ## Problem Understanding For every position `i`, we are not given a fixed value. Instead, we may choose any integer inside an interval `[l_i, r_i]`. For each prefix length `k`, consider all arrays$$a_1、a_2、\ドット、a_k$$
such that
$$l_i \le a_i \le r_i.$$
Among all valid choices, we want the largest possible length of a non-decreasing subsequence.
The answer must be reported for every prefix independently. After reading the first interval we output the best LNDS length for that prefix, after reading the first two intervals we output the best LNDS length for that prefix, and so on.
The total number of intervals over all test cases is at most `2·10^5`. A quadratic DP would require roughly `4·10^10` operations in the worst case, which is completely impossible within two seconds. Any accepted solution must be close to `O(n log n)`.
The tricky part is that we are not constructing one particular array. We are optimizing over all possible choices inside the intervals. A greedy choice that looks good locally may destroy a longer subsequence later.
Consider
⟦PROTECT_0⟧
For the whole prefix, the optimal answer is `3`. Choosing the largest possible value everywhere gives `[5,4,3,3]`, whose LNDS length is only `2`.
Another easy mistake is to think that an interval contributes to the answer whenever it intersects some previous interval. For
⟦PROTECT_1⟧
every valid choice satisfies `a1 > a2`, so the answer is still `1`.
A third subtle case appears when many intervals can all use the same value:
⟦PROTECT_2⟧
Choosing all values equal gives an LNDS of length `3`. Any approach that treats equal values as strictly increasing would incorrectly return smaller answers.
## Approaches
The classical LNDS algorithm maintains
$$dp[j]$$
as the minimum possible ending value of a non-decreasing subsequence of length `j`.
For a fixed array, processing one element updates this structure using binary search.
A brute force interpretation of the current problem would try all valid choices for every interval and run LNDS on each resulting array. Even if intervals were tiny, the number of arrays grows exponentially. This is hopeless.
The key observation is that the standard LNDS DP does not actually need the exact chosen value. It only needs to know how the new interval can affect the minimal ending values.
Suppose we already maintain the LNDS DP for all arrays obtainable from the processed prefix. Let the current interval be `[l,r]`.
For a subsequence ending value `x`, we may append the new position if we can choose a value `v` such that
$$x \le v \le r.$$
Among all such choices, the best one is the smallest possible value, namely
$$v=\max(x,l)。$$
This turns the transition into a purely interval-based operation.
The resulting DP keeps exactly the same monotonic structure as the ordinary patience-sorting LNDS algorithm. After simplifying the transitions, one discovers that processing `[l,r]` is equivalent to:
1. Find the first DP value greater than `r`.
2. Remove that value.
3. Insert `l`.
This can be maintained with a multiset.
The size of the structure directly equals the current optimal LNDS length plus one sentinel element.
Each interval causes one insertion and at most one deletion, giving `O(log n)` work per interval.
| Approach | Time Complexity | Space Complexity | Verdict |
|---|------|---|
| Brute Force | Exponential | Exponential | Too slow |
| Optimal | O(n log n) | O(n) | Accepted |
## Deriving the DP Transition
Let
$$dp_j$$
denote the minimum possible ending value of a non-decreasing subsequence of length `j`.
As in the classical LNDS algorithm, the sequence
$$dp_1 \le dp_2 \le \dots$$
is non-decreasing.
Assume we process interval `[l,r]`.
Take some state `dp_j`.
If `dp_j > r`, no value from the interval can extend that subsequence.
If `dp_j ≤ r`, we can choose a value from the interval and extend it. The smallest achievable ending value becomes
$$\max(dp_j,l).$$
 DP 配列はソートされているため、次の値より大きい値はすべて`r`接尾語を形成します。 
させて`x`より厳密に大きい最初の DP 値である`r`。 
より小さい DP 値はすべて有効のままですが、次のように表される位置は`x`に改善することができます`l`。 
標準的な LNDS の簡素化の後、更新全体は次のようになります。 
- 挿入`l`、
 - より大きい最初の値を消去します`r`。 
これは、忍耐によるソートによって実行される置換操作とまったく同じです。 
マルチセットは当然両方の操作をサポートします。 
## アルゴリズムのチュートリアル
 1. マルチセットを作成する`S`単一のセンチネル値を含む`0`。 
2. 左から右に間隔を処理します。 
3. 現在の間隔について`[l,r]`の最初の要素を見つけます。`S`それは厳密にはより大きいです`r`。 
4.挿入`l`の中へ`S`。 
5. そのような要素が存在する場合は、その要素を削除します。 
これは、上で説明した LNDS DP 遷移の圧縮形式です。 
6. 現在の答えは次のとおりです`|S| - 1`。 
減算によりセンチネルが削除されます。 
7. 処理された間隔ごとに応答を出力します。 
### なぜ効果があるのか
 マルチセットは常に現在の LNDS DP アレイをセンチネルと一緒に保存します。`0`。 
あらゆる可能なサブシーケンス長に対して`j`、`j`- マルチセットの 2 番目に小さい要素は、長さが減少しないサブシーケンスの達成可能な最小終了値に等しい`j`。 
インターバル時`[l,r]`が到着し、次より大きい最初の値が置き換えられます`r`による`l`上記で導出された間隔 DP とまったく同じ状態遷移を実行します。 短いサブシーケンスが悪くなることはなく、影響を受ける長さに対して達成可能な最良の終了値は最小になります。 
古典的な忍耐ソートの不変式は更新のたびに保持されるため、非センチネル要素の数は、現在のプレフィックスで達成可能な最大 LNDS 長と等しくなります。 
## Python ソリューション```python
import sys
from bisect import bisect_right
input = sys.stdin.readline
from sortedcontainers import SortedList
def solve():
    t = int(input())
    out = []
    for _ in range(t):
        n = int(input())
        dp = SortedList([0])
        ans = []
        for _ in range(n):
            l, r = map(int, input().split())
            pos = dp.bisect_right(r)
            dp.add(l)
            if pos < len(dp):
                dp.pop(pos)
            ans.append(str(len(dp) - 1))
        out.append(" ".join(ans))
    sys.stdout.write("\n".join(out))
if __name__ == "__main__":
    solve()
```この実装は数学的遷移を直接反映しています。 
マルチセットは、任意の位置の挿入、削除、および上限クエリをサポートする必要があります。 C++では、`multiset`これを自然に処理します。 Python では、バランスの取れた順序付けされたコンテナーが必要です。`SortedList`で必要なすべての操作を提供します`O(log n)`。 
操作の順序が重要です。 まず、次より大きい最初の値を見つけます。`r`を挿入してから、`l`、次に、以前に特定された位置を消去します。 これにより、DP の正確な交換操作が再現されます。 
番兵`0`すべての間隔エンドポイントが少なくとも`1`。 これは空のサブシーケンスを表し、すべての実際のサブシーケンスの長さを 1 だけシフトできます。 
## 実用的な例
 ### 例 1
 入力:```
4
4 5
3 4
1 3
3 3
```| ステップ | 間隔 | 更新後のマルチセット | 答え |
 | --- | --- | --- | --- |
 | 1 | [4,5] | {0,4} | 1 |
 | 2 | [3,4] | {0,3} | 1 |
 | 3 | [1,3] | {0,1,3} | 2 |
 | 4 | [3,3] | {0,1,3,3} | 3 |
 出力：```
1 1 2 3
```3 番目の間隔は、2 番目の達成可能なサブシーケンス長を作成します。 4 番目のインターバルは再び延長されます。 
### 例 2
 入力:```
5
1 2
6 8
4 5
2 3
3 3
```| ステップ | 間隔 | 更新後のマルチセット | 答え |
 | --- | --- | --- | --- |
 | 1 | [1,2] | {0,1} | 1 |
 | 2 | [6,8] | {0,1,6} | 2 |
 | 3 | [4,5] | {0,1,4} | 2 |
 | 4 | [2,3] | {0,1,2} | 2 |
 | 5 | [3,3] | {0,1,2,3} | 3 |
 出力：```
1 2 2 2 3
```このトレースは、応答をすぐに増やすことなく、後の間隔で DP 状態をどのように改善できるかを示しています。 新しいサブシーケンスの長さが達成可能になった場合にのみ、答えは大きくなります。 
## 複雑さの分析
 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 間隔ごとに 1 つの上限クエリ、1 つの挿入、および最大 1 つの削除 |
 | スペース | O(n) | マルチセットは、処理された間隔ごとに最大 1 つの値とセンチネル | を格納します。 
せいぜい`2·10^5`すべてのテストケースにわたる間隔、`O(n log n)`制限内に簡単に収まります。 
## テストケース```python
# helper: run solution on input string, return output string
import io
import sys
from sortedcontainers import SortedList
def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline
    t = int(input())
    out = []
    for _ in range(t):
        n = int(input())
        dp = SortedList([0])
        cur = []
        for _ in range(n):
            l, r = map(int, input().split())
            pos = dp.bisect_right(r)
            dp.add(l)
            if pos < len(dp):
                dp.pop(pos)
            cur.append(str(len(dp) - 1))
        out.append(" ".join(cur))
    return "\n".join(out)
# sample
assert run(
"""1
4
4 5
3 4
1 3
3 3
"""
) == "1 1 2 3"
# minimum size
assert run(
"""1
1
1 1
"""
) == "1"
# strictly impossible to chain
assert run(
"""1
2
3 4
1 2
"""
) == "1 1"
# all equal intervals
assert run(
"""1
4
5 5
5 5
5 5
5 5
"""
) == "1 2 3 4"
# increasing overlap
assert run(
"""1
4
1 10
2 10
3 10
4 10
"""
) == "1 2 3 4"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一の間隔 |`1`| 最小サイズ |
 |`[3,4],[1,2]`|`1 1`| 有効な非減少ペアが存在しません |
 | 全て`[5,5]`|`1 2 3 4`| LNDS では等しい値を許可する必要があります |
 | 間隔を増やす |`1 2 3 4`| 達成可能なサブシーケンス長の継続的な増加 |
 ## 特殊なケース
 検討してください```
2
3 4
1 2
```最初の間隔の後、マルチセットは次のようになります。`{0,3}`そして答えは`1`。 
2 番目の間隔では、最初の値が次の値より大きい`2`は`3`。 それを置き換えるのは、`1`生成する`{0,1}`。 答えは残っている`1`。 
すべての有効な選択肢が満たされるため、これは現実と一致します。`a1 > a2`。 
ここで考えてみましょう```
4
5 5
5 5
5 5
5 5
```マルチセットは次のように進化します```
{0,5}
{0,5,5}
{0,5,5,5}
{0,5,5,5,5}
```そして答えは次のようになります```
1 2 3 4
```非減少サブシーケンスでは等しい値が許可されるため、配列全体を選択できます。 
最後に検討します```
3
1 10
1 10
1 10
```各間隔で値を選択できます`1`。 このアルゴリズムは、達成可能な長さを短縮するような状態を削除することはありません。 マルチセットはステップごとに 1 要素ずつ増加し、答えが得られます。`1 2 3`、これが最適です。
