---
 タイトル: 「CF 2025B - 二項係数の一種」
 説明: 「ステートメント内のコードはパスカルの三角形を構築しようとしていますが、反復が間違っています。代わりに$$C[n][k] = C[n-1][k] + C[n-1][k-1]$$それは計算します$$C[n][k] = C[n][k-1] + C[n-1][k-1].$$境界値は引き続き次のように初期化されます。$$C[n][0] = 1,qquad C[n][n] = 1." date: "2026-06-09T03:20:23+07:00" tags: ["codeforces", "competitive-programming", "combinatorics", "dp", "math"] categories: ["algorithms"] codeforces_contest: 2025 codeforces_index: "B" codeforces_contest_name: "Educational Codeforces Round 170 (Rated for Div. 2)" rating: 1100 weight: 2025 solve_time_s: 437 verified: false draft: false --- [CF 2025B - Binomial Coefficients, Kind Of](https://codeforces.com/problemset/problem/2025/B) **Rating:** 1100   **Tags:** combinatorics, dp, math   **Solve time:** 7m 17s   **Verified:** no   ## Solution ## Problem Understanding The code in the statement tries to build a Pascal triangle, but the recurrence is wrong. Instead of$$C[n][k] = C[n-1][k] + C[n-1][k-1]$$
it computes
$$C[n][k] = C[n][k-1] + C[n-1][k-1]。$$
The boundary values are still initialized as
$$C[n][0] = 1、\qquad C[n][n] = 1。$$
For many queries $(n,k)$, we must determine the value produced by this incorrect recurrence and print it modulo $10^9+7$.
The first input line gives the number of queries. The second line contains all $n_i$ values and the third line contains the corresponding $k_i$ values. For every pair $(n_i,k_i)$, we must output the entry that appears in the wrongly generated triangle.
The largest $n$ is $10^5$, and there can also be $10^5$ queries. Any algorithm that explicitly constructs a triangle of size $10^5$ is impossible. A triangle with $10^5$ rows contains roughly $5 \cdot 10^9$ cells, far beyond both the time and memory limits.
The constraints strongly suggest that there is a simple closed-form expression for the values. Once that expression is found, preprocessing up to $10^5$ and answering each query in $O(1)$ time becomes feasible.
A subtle trap is assuming that the wrong recurrence still produces ordinary binomial coefficients. For example:
Input:
⟦PROTECT_0⟧
The correct binomial coefficient would be
$$\binom{5}{2}=10、$$
but the wrong triangle gives 4, so directly using combinations produces the wrong answer.
Another easy mistake is to derive a formula involving powers of two but forget the modulo operation. For large $k$, values grow exponentially. For example, $k=99999$ requires modular exponentiation. Ordinary integer powers would be far too large.
A third trap is trying to build rows up to the queried $n$. The answer actually turns out to depend only on $k$, so any solution that processes all rows up to $10^5$ for every query will time out.
## Approaches
The most direct approach is to simulate exactly what the buggy code does.
We initialize $C[n][0]=1$ and $C[n][n]=1$, then fill every interior position using the given recurrence. This reproduces the intended triangle exactly, so it is obviously correct.
The problem is scale. To answer queries up to $n=10^5$, we would need to construct all rows up to $10^5$. The number of entries is
$$1+2+\cdots+100000 \約 5 \times 10^9、$$
which is completely infeasible.
To get further, we need to understand the structure of the recurrence.
Let us write out the first few rows:
| n | Row values |
| --- | --- |
| 0 | 1 |
| 1 | 1 1 |
| 2 | 1 2 1 |
| 3 | 1 2 4 1 |
| 4 | 1 2 4 8 1 |
| 5 | 1 2 4 8 16 1 |
A pattern appears immediately. Every interior value seems to be a power of two.
Suppose every row above satisfies
$$C[n-1][k]=2^k$$
for interior positions.
Then
$$C[n][k]
 =
 C[n][k-1]+C[n-1][k-1]。$$
If $C[n][k-1]=2^{k-1}$ and $C[n-1][k-1]=2^{k-1}$, then
$$C[n][k]
 =
 2^{k-1}+2^{k-1}
 =
 2^k。$$
This proves by induction that every interior entry equals $2^k$.
The row index $n$ disappears entirely. As long as $1 \le k < n$,
$$C[n][k] = 2^k。$$
The whole problem reduces to answering powers of two modulo $10^9+7$.
Since $k\le 99999$, we can precompute
$$pow2[i]=2^i \bmod (10^9+7)$$
for all needed exponents and answer every query in constant time.
| Approach | Time Complexity | Space Complexity | Verdict |
| --- | --- | --- | --- |
| Brute Force triangle construction | $O(N^2)$ | $O(N^2)$ | Too slow |
| Power-of-two observation + preprocessing | $O(K_{\max}+t)$ | $O(K_{\max})$ | Accepted |
## Algorithm Walkthrough
1. Read all query pairs.
2. Find the largest queried value of $k$. This determines how many powers of two we must precompute.
3. Build an array `pow2` where `pow2[i] = 2^i mod M`.
4. Initialize `pow2[0] = 1`.
5. For each `i` from 1 to `max_k`, compute
$$pow2[i] = 2 \cdot pow2[i-1] \pmod M.$$
This gives every required power of two in linear time.
6. For each query $(n,k)$, output `pow2[k]`.
The derived formula shows that every interior entry of the buggy triangle equals $2^k$, independent of $n$.
### Why it works
We prove that every interior entry satisfies
$$C[n][k]=2^k$$
for $1\le k<n$.
The base case is $k=1$:
$$C[n][1]
 =
 C[n][0]+C[n-1][0]
 =
 1+1
 =
 2
 =
 2^1。$$
Assume for some $k>1$ that all interior entries with column $k-1$ equal $2^{k-1}$. Then
$$C[n][k]
 =
 C[n][k-1]+C[n-1][k-1]
 =
 2^{k-1}+2^{k-1}
 =
 2^k。$$
Thus every interior column $k$ equals $2^k$. Since the formula depends only on $k$, every query answer is simply $2^k$ modulo $10^9+7$.
## Python Solution
⟦PROTECT_1⟧
The solution never uses the queried values of $n$. The proof shows that every valid interior position depends only on the column index $k$.
The preprocessing array stores all powers of two that may be needed. Since the maximum possible $k$ is less than $10^5$, this requires only about one hundred thousand entries.
An easy implementation mistake is to compute powers separately for each query using repeated multiplication. That would still pass, but preprocessing is simpler and avoids redundant work. Another common mistake is forgetting the modulo at every multiplication step, which causes numbers to become enormous.
## Worked Examples
### Sample 1
Input:
⟦PROTECT_2⟧
Precomputed values:
| k | 2^k mod M |
| --- | --- |
| 1 | 2 |
| 2 | 4 |
| 3 | 8 |
| 33333 | 326186014 |
| 66666 | 984426998 |
| 99999 | 303861760 |
Query processing:
| Query | k | Answer |
| --- | --- | --- |
| (2,1) | 1 | 2 |
| (5,2) | 2 | 4 |
| (5,3) | 3 | 8 |
| (100000,1) | 1 | 2 |
| (100000,33333) | 33333 | 326186014 |
| (100000,66666) | 66666 | 984426998 |
| (100000,99999) | 99999 | 303861760 |
This example demonstrates the central observation: even though the row indices vary dramatically, the answer depends only on $k$.
### Constructed Example
Input:
⟦PROTECT_3⟧
Preprocessing:
| k | 2^k |
| --- | --- |
| 1 | 2 |
| 2 | 4 |
| 5 | 32 |
Queries:
| Query | k | Answer |
| --- | --- | --- |
| (3,1) | 1 | 2 |
| (4,2) | 2 | 4 |
| (6,5) | 5 | 32 |
Output:
⟦PROTECT_4⟧
This trace shows that even positions very close to the right boundary still follow the same formula.
## Complexity Analysis
| Measure | Complexity | Explanation |
| --- | --- | --- |
| Time | $O(\max k + t)$ | Precompute powers once, then answer each query in constant time |
| Space | $O(\max k)$ | Store all powers of two up to the largest queried exponent |
With $\max k < 10^5$ and $t \le 10^5$, both time and memory usage are tiny compared to the limits.
## Test Cases
⟦PROTECT_5⟧
| Test input | Expected output | What it validates |
| --- | --- | --- |
| `n=2, k=1` | `2` | Smallest valid query |
| Many different rows, same `k=1` | `2 2 2 2` | Independence from `n` |
| Consecutive small powers | `2 4 8 16 32` | Correct power generation |
| `(5,4)` and `(10,9)` | `16 512` | Values near the right boundary |
## Edge Cases
Consider the smallest possible query:
⟦PROTECT_6⟧
The recurrence gives
$$C[2][1]=C[2][0]+C[1][0]=1+1=2。$$
The algorithm returns $2^1=2$, which matches exactly.
Consider a query near the right boundary:
⟦PROTECT_7⟧
A manual construction gives row 5 as
$$[1,2,4,8,16,1].$$
 答えは 16 です。アルゴリズムは次の値を返します。$2^4=16$したがって、境界に近づいても式は変わりません。 
同じ列で異なる行を持つ 2 つのクエリを考えてみましょう。```
2
10 100000
3 3
```繰り返しにより、行に関係なく列 3 に 8 が生成されます。 アルゴリズムの出力$2^3=8$両方のクエリに対して。 これにより、式が導出された後は行インデックスが何の役割も果たさないことが確認されます。 
最後に、最大の指数を考慮します。```
1
100000
99999
```実際の値は天文学的な大きさです。 アルゴリズムはその整数を直接保存することはありません。 累乗剰余を計算します$10^9+7$前処理中に、制限内で必要な結果を効率的に生成します。
