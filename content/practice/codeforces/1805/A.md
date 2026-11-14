---
 タイトル：「CF 1805A - ゼロが必要だ」
 説明: 「配列が与えられました$a$, しかし、その値を直接知ることはできません。 代わりに、数字を選択できることがわかっています$x$、のすべての要素の XOR$a$と$x$、新しい配列を取得します$b$、 どこ$$bi = ai oplus x." date: "2026-06-09T09:16:00+07:00" tags: ["codeforces", "competitive-programming", "bitmasks", "brute-force"] categories: ["algorithms"] codeforces_contest: 1805 codeforces_index: "A" codeforces_contest_name: "Codeforces Round 862 (Div. 2)" rating: 800 weight: 1805 solve_time_s: 193 verified: false draft: false --- [CF 1805A - We Need the Zero](https://codeforces.com/problemset/problem/1805/A) **Rating:** 800   **Tags:** bitmasks, brute force   **Solve time:** 3m 13s   **Verified:** no   ## Solution ## Problem Understanding We are given an array $a$, but we do not know its values directly. Instead, we know that we may choose a number $x$, XOR every element of $a$ with $x$, and obtain a new array $b$, where$$b_i = a_i \oplus x。$$
The goal is to find whether there exists some integer $x$ such that the XOR of all elements of $b$ becomes zero:
$$b_1 \oplus b_2 \oplus \cdots \oplus b_n = 0。$$
If such an $x$ exists, we must output one valid value between $0$ and $255$. Otherwise we output $-1$.
The constraints are very small. Every array element is less than $2^8$, and the statement guarantees that whenever a solution exists, there is also one in the range $[0,255]$. The total number of array elements across all test cases is at most $1000$. Even checking all $256$ possible values of $x$ for every test case requires only about
$$256 \cdot 1000 = 256000$$
XOR operations, which is trivial for a one second time limit.
The main danger is making assumptions about parity without carefully deriving them. For example, with
⟦PROTECT_0⟧
the XOR after applying $x$ is simply $1 \oplus x$. Choosing $x=1$ produces zero, so the answer is not $-1$.
Another easy mistake is assuming that if the XOR of the original array is nonzero then no answer exists. Consider
⟦PROTECT_1⟧
The original XOR equals $6$, yet $x=6$ produces a valid answer.
A third subtle case is an even-length array. For
⟦PROTECT_2⟧
the original XOR is $2$. No value of $x$ works, so the answer is $-1$. A brute-force search confirms this.
## Approaches
The most direct approach is brute force. Since every valid answer must lie between $0$ and $255$, we can try every possible $x$. For each candidate, compute
$$(a_1 \oplus x)\oplus(a_2 \oplus x)\oplus\cdots\oplus(a_n \oplus x)$$
and check whether the result is zero.
This method is completely correct because it explicitly tests every possible answer. The search space contains only $256$ values, so it already passes comfortably.
Looking more closely at the XOR expression reveals a stronger observation. Let
$$S=a_1\oplus a_2\oplus\cdots\oplus a_n。$$
Then
$$(a_1\oplus x)\oplus\cdots\oplus(a_n\oplus x)
 =
 S \oplus \underbrace{x\oplus x\oplus\cdots\oplus x}_{n\text{ 回}}。$$
The behavior now depends entirely on the parity of $n$.
If $n$ is even, all copies of $x$ cancel out, so the final XOR is always $S$. Either $S=0$, in which case any $x$ works, or $S\neq0$, in which case no $x$ can help.
If $n$ is odd, the copies of $x$ reduce to a single $x$, giving
$$さらに×。$$
To make the result zero we simply choose
$$x=S。$$
This observation reduces the problem to computing one XOR per test case.
| Approach | Time Complexity | Space Complexity | Verdict |
| --- | --- | --- | --- |
| Brute Force | $O(256n)$ | $O(1)$ | Accepted |
| Optimal | $O(n)$ | $O(1)$ | Accepted |
## Algorithm Walkthrough
1. Compute the XOR of all elements of the array and store it in `xr`.
2. If $n$ is odd, output `xr`.
For odd length arrays, the transformed XOR becomes `xr ^ x`. Choosing `x = xr` makes the result zero.
3. If $n$ is even and `xr == 0`, output `0`.
For even length arrays, the transformed XOR never depends on `x`. Since it is already zero, any value works. Outputting `0` is simplest.
4. If $n$ is even and `xr != 0`, output `-1`.
No choice of `x` can change the final XOR, so a solution does not exist.
### Why it works
Let
$$S=a_1\oplus a_2\oplus\cdots\oplus a_n。$$
After applying $x$, the XOR of all transformed elements equals
$$S\oplus(x\oplus x\oplus\cdots\oplus x)。$$
When $n$ is even, the repeated XOR of $x$ equals zero, so the result is always $S$. A solution exists exactly when $S=0$.
When $n$ is odd, the repeated XOR of $x$ equals $x$, so the result becomes $S\oplus x$. Setting $x=S$ makes the value zero.
These are the only possibilities, so the algorithm is correct.
## Python Solution
⟦PROTECT_3⟧
The implementation follows the algebraic derivation directly. First we compute the XOR of the entire array. After that, only the parity of $n$ matters.
For odd $n$, the answer is exactly the array XOR. For even $n$, the transformed XOR cannot be influenced by $x$, so we either output `0` when it is already zero or `-1` otherwise.
No overflow concerns exist because XOR operates on integers that are at most $255$. The implementation uses fast input and processes each test case in a single pass.
## Worked Examples
### Example 1
Input:
⟦PROTECT_4⟧
Compute the array XOR.
| Step | Value | Running XOR |
| --- | --- | --- |
| Start | - | 0 |
| 1 | 1 | 1 |
| 2 | 2 | 3 |
| 3 | 5 | 6 |
Since $n=3$ is odd, we output $x=6$.
Checking:
$$(1\oplus6)\oplus(2\oplus6)\oplus(5\oplus6)
 =
 7\oplus4\oplus3
 =
 0.$$
This example demonstrates the odd-length rule.
### Example 2
Input:
⟦PROTECT_5⟧
Compute the array XOR.
| Step | Value | Running XOR |
| --- | --- | --- |
| Start | - | 0 |
| 1 | 1 | 1 |
| 2 | 2 | 3 |
| 3 | 2 | 1 |
| 4 | 3 | 2 |
Here $n=4$ is even and the XOR equals $2$.
For any value of $x$,
$$(a_1\oplus x)\oplus\cdots\oplus(a_4\oplus x)=2。$$
The result can never become zero, so the answer is `-1`.
This example demonstrates why even-length arrays behave differently.
## Complexity Analysis
| Measure | Complexity | Explanation |
| --- | --- | --- |
| Time | $O(n)$ per test case | One pass computes the array XOR |
| Space | $O(1)$ | Only a few variables are used |
Since the total number of elements across all test cases is at most $1000$, the running time is far below the limit.
## Test Cases
⟦PROTECT_6⟧
| Test input | Expected output | What it validates |
| --- | --- | --- |
| `n=1, [0]` | `0` | Smallest possible input |
| `n=1, [7]` | `7` | Odd-length formula |
| `[5,5,7,7]` | `0` | Even length with zero XOR |
| `[1,2]` | `-1` | Even length with nonzero XOR |
| `[1,1,1,1,1]` | `1` | General odd-length case |
## Edge Cases
Consider a single-element array:
⟦PROTECT_7⟧
The array XOR is $13$. Since the length is odd, the algorithm outputs $13$. Applying $x=13$ gives
$$13\oplus13=0.$$
 必要な条件が成立します。 
XOR がすでに 0 である偶数長の配列を考えてみましょう。```
1
4
5 5 9 9
```合計の XOR はゼロに等しくなります。 アルゴリズムの出力`0`。 使用する$x=0$配列は変更されず、XOR は 0 のままになります。 
XOR がゼロ以外の偶数長の配列を考えてみましょう。```
1
4
1 2 2 3
```合計の XOR は次となります$2$。 長さは均等なので、$x$全体的な XOR でキャンセルされます。 変換された XOR は常に次のようになります。$2$、アルゴリズムは正しく出力します`-1`。 
XOR が 0 である奇数長の配列を考えてみましょう。```
1
3
1 2 3
```配列 XOR はゼロに等しく、アルゴリズムの出力は次のとおりです。`0`。 変換された XOR は次のようになります。$0\oplus0=0$, したがって、条件はすぐに満たされます。
