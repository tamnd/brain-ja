---
 タイトル：『CF 1898D 絶対美』
 説明: 「すべての位置 i に対して、数値のペア (ai、bi) があります。現在の美しさは、ペアの値間の距離の合計です。$$sum We may choose at most one pair of indices and swap the corresponding values inside array b." date: "2026-06-08T21:31:48+07:00" tags: ["codeforces", "competitive-programming", "greedy", "math"] categories: ["algorithms"] codeforces_contest: 1898 codeforces_index: "D" codeforces_contest_name: "Codeforces Round 910 (Div. 2)" rating: 1900 weight: 1898 solve_time_s: 133 verified: true draft: false --- [CF 1898D - Absolute Beauty](https://codeforces.com/problemset/problem/1898/D) **Rating:** 1900   **Tags:** greedy, math   **Solve time:** 2m 13s   **Verified:** yes   ## Solution ## Problem Understanding For every position `i`, we have a pair of numbers `(a_i, b_i)`. The current beauty is the sum of the distances between the paired values:$$\sum |a_i-b_i|$$
We may choose at most one pair of indices and swap the corresponding values inside array `b`. After that swap, every position still contributes an absolute difference with its own `a_i`, and we want the largest possible total beauty.
A useful way to think about the problem is that every index defines an interval between its two values. If `a_i = 3` and `b_i = 8`, the pair corresponds to the interval `[3,8]`. The contribution of that index is exactly the interval length.
The total length of all intervals is already known. The only thing a swap can change is the contribution of the two indices involved in that swap.
The constraints are the real challenge. The sum of all `n` across test cases is at most `2·10^5`, which means an `O(n log n)` or `O(n)` solution is expected. Any algorithm that tries all pairs of indices would require `O(n²)` work, roughly `2·10^10` operations in the worst case, which is far beyond what can run in two seconds.
Several edge cases are easy to mishandle.
Consider:
⟦PROTECT_0⟧
The current beauty is `1 + 1 = 2`. Swapping the two elements of `b` produces `[1,2]`, whose beauty is `0`. The correct answer is `2`, because the operation is optional. Any solution that assumes a swap must be performed gives the wrong result.
Consider:
⟦PROTECT_1⟧
The initial beauty is `0`. After swapping, the beauty becomes `2`. The answer is `2`. A solution that only checks whether intervals overlap would miss that a beneficial swap exists even though both original intervals have length zero.
Another subtle case is when intervals are already disjoint:
⟦PROTECT_2⟧
The beauty is `4 + 4 = 8`. Any swap leaves it unchanged. The answer remains `8`. A careless derivation may incorrectly assume that every pair of non-overlapping intervals allows a gain.
## Approaches
The brute-force idea is straightforward. Compute the current beauty. For every pair `(i,j)`, swap `b_i` and `b_j`, recompute the change in contribution of those two positions, and keep the best result.
This works because a swap affects only positions `i` and `j`. Instead of recomputing the whole sum, we can evaluate a pair in constant time. Even then, there are `O(n²)` candidate pairs. With `n = 2·10^5`, that means roughly twenty billion pairs, which is completely infeasible.
The key observation is that each position can be represented by the interval
$$[l_i、r_i]$$
where
$$l_i=\min(a_i,b_i),\qquad r_i=\max(a_i,b_i)。$$
Let
$$S=\sum (r_i-l_i)$$
be the original beauty.
Suppose we swap values between positions `i` and `j`. The only thing that changes is the combined contribution of those two positions.
A very useful identity is:
$$|x-v|+|u-y|
 -
 (|x-y|+|u-v|)
 =
 2\cdot \max(0,\; \text{ギャップ})$$
where the gap is the distance between the two intervals.
After working through the geometry of absolute values, the maximum gain obtainable from swapping two positions depends only on the intervals. If two intervals overlap, no swap can improve their total contribution. If they are disjoint, the best gain equals twice the distance between them.
For intervals
$$[l_i,r_i],\quad [l_j,r_j]$$
with `r_i < l_j`, the gain is
$$2(l_j-r_i)。$$
So the entire problem becomes:
Find two intervals whose separation
$$l_j-r_i$$
is as large as possible.
Equivalently, among all intervals we want the largest possible value of
$$\max l_i - \min r_i$$
taken from different intervals in the correct order.
A cleaner global interpretation emerges. Let
$$L=\max l_i,\qquad R=\min r_i。$$
If all intervals intersect, then `L ≤ R` and no improvement is possible.
If they do not all intersect, then `L > R`. The maximum achievable gain is
$$2(L-R)。$$
Why? The interval achieving `L` lies completely to the right of the interval achieving `R`, and their separation is exactly `L-R`. No pair of intervals can have a larger gap than that.
Thus the answer is simply
$$S + 2\max(0,L-R)。$$
| Approach | Time Complexity | Space Complexity | Verdict |
| --- | --- | --- | --- |
| Brute Force | O(n²) | O(1) | Too slow |
| Optimal | O(n) | O(1) | Accepted |
## Algorithm Walkthrough
1. Initialize `base = 0`.
2. For every position `i`, compute:
$$l_i=\min(a_i,b_i)$$
and
$$r_i=\max(a_i,b_i)。$$
Add `r_i - l_i` to `base`. This is exactly the original beauty contribution of that position.
3. Track:
$$L=\max l_i$$
and
$$R=\min r_i.$$
These summarize how all intervals relate to each other.
4. If `L ≤ R`, every interval shares a common intersection point. No swap can increase the beauty, so the answer is `base`.
5. Otherwise the intervals have a positive separation of `L - R`. The best possible gain is:
$$2(L-R)。$$
6. Output:
$$ベース + 2(L-R).$$
 ### なぜ効果があるのか
 各インデックスは間隔の長さに影響します。`[l_i,r_i]`。 スワップは 2 つの間隔にのみ影響します。 どのペアでも、合計寄与の最大増加はそれらの間隔間の距離の 2 倍に等しく、それらが重なっている場合はゼロに等しくなります。 
2 つの間隔間の可能な最大距離は、最大の左端点の間隔と最小の右端点の間隔を取ることによって取得されます。 彼らの別れは、`L-R`いつでも`L>R`。 左端のすべての端点が最大で 1 つであるため、他のペアはこれ以上離れることはできません。`L`そしてすべての適切なエンドポイントは少なくとも`R`。 
本来の美しさがあるので、`base`そして最高の追加ゲインはまさに`2·max(0,L-R)`、公式は常に最適な答えを導き出します。 
## Python ソリューション```python
import sys
input = sys.stdin.readline
def solve():
    t = int(input())
    ans = []
    for _ in range(t):
        n = int(input())
        a = list(map(int, input().split()))
        b = list(map(int, input().split()))
        base = 0
        max_l = 0
        min_r = 10**18
        for x, y in zip(a, b):
            l = min(x, y)
            r = max(x, y)
            base += r - l
            max_l = max(max_l, l)
            min_r = min(min_r, r)
        gain = max(0, max_l - min_r) * 2
        ans.append(str(base + gain))
    sys.stdout.write("\n".join(ans))
solve()
```最初のループは、各ペアに対応する間隔を計算します。`(a_i,b_i)`。 本来の美しさは単純に音程の長さの総和なので`base`蓄積する`r - l`。 
同時に、最大の左端点と最小の右端点を維持します。 これらは、可能な最大の改善を決定するために必要な唯一のグローバル値です。 
表現```
max(0, max_l - min_r)
```間隔の間隔を計算します。 すべての間隔が重なると、`max_l <= min_r`ゲインは自動的にゼロになります。 
すべての算術演算は Python 整数を使用して行われるため、値が次の値に達してもオーバーフローのリスクはありません。`10^9`合計はそれを超える可能性があります`32-bit`限界。 
## 実用的な例
 ### 例 1```
a = [1, 2]
b = [1, 2]
```| 私 | l | r | i の後のベース | 最大_l | 分_r |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | 1 | 0 | 1 | 1 |
 | 2 | 2 | 2 | 0 | 2 | 1 |
 すべての間隔を処理した後:
 | 変数 | 値 |
 | --- | --- |
 | ベース | 0 |
 | 最大_l | 2 |
 | 分_r | 1 |
 | ゲイン | 2 |
 | 答え | 2 |
 間隔`[1,1]`そして`[2,2]`距離とは素である`1`。 ゲインは`2`、これは 2 つの要素を交換した効果と一致します。 
### 例 2```
a = [1, 2, 3, 4]
b = [5, 6, 7, 8]
```| 私 | l | r | i の後のベース | 最大_l | 分_r |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | 5 | 4 | 1 | 5 |
 | 2 | 2 | 6 | 8 | 2 | 5 |
 | 3 | 3 | 7 | 12 | 3 | 5 |
 | 4 | 4 | 8 | 16 | 4 | 5 |
 最終値:
 | 変数 | 値 |
 | --- | --- |
 | ベース | 16 |
 | 最大_l | 4 |
 | 分_r | 5 |
 | ゲイン | 0 |
 | 答え | 16 |
 すべての間隔が交差するため、`max_l <= min_r`。 交換によって美しさを向上させることはできないため、元の値が最適なままになります。 
これらのトレースは、中心の不変条件を示しています。答えは、元の美しさと間隔の重なり構造にのみ依存します。 
## 複雑さの分析
 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | 各要素は 1 回処理されます。 
| スペース | お(1) | 少数の実行変数のみが保存されます。 
| すべてのテストの合計 | O(Σn) | すべての長さの合計は最大で`2·10^5`|
 このソリューションは、テスト ケースごとに 1 回のリニア スキャンを実行します。 すべてのテスト ケースにわたる要素の総数は次のように制限されるため、`2·10^5`、ランタイムは容易に制限内に収まり、一定のメモリ使用量はメモリ上限をはるかに下回ります。 
## テストケース```python
# helper: run solution on input string, return output string
import sys, io
def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out
    def solve():
        input = sys.stdin.readline
        t = int(input())
        ans = []
        for _ in range(t):
            n = int(input())
            a = list(map(int, input().split()))
            b = list(map(int, input().split()))
            base = 0
            max_l = 0
            min_r = 10**18
            for x, y in zip(a, b):
                l = min(x, y)
                r = max(x, y)
                base += r - l
                max_l = max(max_l, l)
                min_r = min(min_r, r)
            ans.append(str(base + 2 * max(0, max_l - min_r)))
        print("\n".join(ans))
    solve()
    sys.stdin = old_stdin
    sys.stdout = old_stdout
    return out.getvalue().strip()
# provided sample
assert run("""6
3
1 3 5
3 3 3
2
1 2
1 2
2
1 2
2 1
4
1 2 3 4
5 6 7 8
10
1 8 2 5 3 5 3 1 1 3
2 9 2 4 8 2 3 5 3 1
3
47326 6958 358653
3587 35863 59474
""") == """4
2
2
16
31
419045"""
# minimum size
assert run("""1
2
1 2
1 2
""") == "2"
# already optimal without swap
assert run("""1
2
1 2
2 1
""") == "2"
# all intervals overlap
assert run("""1
3
1 5 3
4 2 6
""") == "9"
# large separation
assert run("""1
2
1 100
1 100
""") == "198"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`a=[1,2], b=[1,2]`|`2`| 最小サイズと有益なスワップ |
 |`a=[1,2], b=[2,1]`|`2`| オプションの交換、答えは元の美しさになる可能性があります |
 | 重複する間隔の例 |`9`| 間隔が交差する場合、ゲインはゼロでなければなりません。 
|`a=[1,100], b=[1,100]`|`198`| 大きな間隔の分離とゲインの計算 |
 ## 特殊なケース
 次のことを考慮してください。```
1
2
1 2
2 1
```間隔は`[1,2]`そして`[1,2]`。 
以下を取得します。```
base = 2
max_l = 1
min_r = 2
```以来`max_l <= min_r`、ゲインはゼロであり、答えは次のようになります。`2`。 スワップすると実際には美しさが低下しますが、この操作はオプションであるため、アルゴリズムは元の値を正しく保持します。 
考慮する：```
1
2
1 2
1 2
```間隔は`[1,1]`そして`[2,2]`。 
以下を取得します。```
base = 0
max_l = 2
min_r = 1
```別れは、`1`、利益を生み出す`2`。 答えは次のようになります`2`、交換後の美しさと正確に一致します。 
考慮する：```
1
2
1 2
5 6
```間隔は`[1,5]`そして`[2,6]`。 
次のように計算します。```
base = 8
max_l = 2
min_r = 5
```以来`max_l <= min_r`、ゲインはゼロであり、答えは残ります。`8`。 間隔が重なっているため、交換によって追加の距離が生じることはありません。 数式はこれを自動的に取得します。
