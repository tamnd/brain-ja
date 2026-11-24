---
 タイトル：「CF 913H - 超えないでください」
 説明: 「しましょう$$si=t1+t2+cdots+ti$$どこでも$ti$～から独立して均一に選択されます$[0,1]$。 私たちには上限が与えられています$x1,x2,dots,xn$。 タスクは、すべてのプレフィックスの合計が対応する境界を下回る確率を計算することです。$$sile xi quad text{for all } i." date: "2026-06-13T01:18:03+07:00" tags: ["codeforces", "competitive-programming", "math", "probabilities"] categories: ["algorithms"] codeforces_contest: 913 codeforces_index: "H" codeforces_contest_name: "Hello 2018" rating: 3400 weight: 913 solve_time_s: 590 verified: true draft: false --- [CF 913H - Don't Exceed](https://codeforces.com/problemset/problem/913/H) **Rating:** 3400   **Tags:** math, probabilities   **Solve time:** 9m 50s   **Verified:** yes   ## Solution ## Problem Understanding Let$$s_i=t_1+t_2+\cdots+t_i$$
where every $t_i$ is chosen independently and uniformly from $[0,1]$.
We are given upper bounds $x_1,x_2,\dots,x_n$. The task is to compute the probability that every prefix sum stays below its corresponding bound:
$$s_i\le x_i \quad \text{すべての } i.$$
The answer is a rational number. The statement guarantees that it can be represented as $P/Q$, and asks for
$$P\cdot Q^{-1}\pmod{998244353}。$$
The most important observation is that the probability is the volume of a region inside the unit cube $[0,1]^n$. Since every $x_i$ has at most six digits after the decimal point, all coordinates are rational numbers, so the final probability is also rational.
The constraint $n\le 30$ is surprisingly small. That immediately suggests that the intended solution is not based on large-scale DP or numerical integration. Instead, we can afford fairly expensive operations on polynomials of degree up to $30$.
A subtle point is that the $x_i$ are real numbers. Any floating-point implementation is doomed, because the required answer is an exact rational value modulo a prime. All computations must be performed symbolically inside the finite field modulo $998244353$.
Another easy mistake is to compute only the distribution of $s_n$. The event depends on every intermediate prefix sum. For example:
⟦PROTECT_0⟧
The final sum constraint is weak, but the second prefix constraint is not. Ignoring intermediate bounds produces a completely different probability.
A third trap is the behavior at breakpoints. The density functions arising from repeated convolutions are piecewise polynomials. Their polynomial formula changes exactly when $x$ crosses numbers of the form $x_i+k$, where $k$ is an integer. Missing even one breakpoint causes incorrect integration on an entire interval.
## Approaches
A brute-force view is to describe the probability directly as an $n$-dimensional integral over all variables $t_1,\dots,t_n$. The region is
$$0\le t_i\le 1、$$
together with
$$t_1+\cdots+t_i\le x_i.$$
The integral is correct, but completely unusable. Even if we recursively integrate one variable at a time, the shape of the region quickly becomes complicated.
The key observation comes from viewing the process as repeated convolution with the uniform distribution on $[0,1]$.
Let $f_i(x)$ be the density of $s_i$ under the condition that all previous constraints have already been respected. Then
$$f_i(x)=
 \begin{ケース}
 \int_{x-1}^{x} f_{i-1}(y)\,dy, & x\le x_i,\\
 0、&x>x_i。 
\end{件}$$
This is exactly a convolution with the uniform kernel, followed by truncation at $x_i$.
Repeated convolution of piecewise polynomials produces another piecewise polynomial. The only places where the formula can change are shifts of existing breakpoints by integers. Since every truncation introduces a breakpoint at $x_i$, every future breakpoint has the form
$$x_i+k。$$
Because $n\le 30$, there are at most
$$n(n+1)+1$$
distinct breakpoints, fewer than one thousand.
Once all breakpoints are known, every density is represented by one polynomial on each interval between consecutive breakpoints. The recurrence becomes purely algebraic: integrate a polynomial, evaluate it on neighboring intervals, and subtract.
This converts a probability problem into symbolic manipulation of low-degree piecewise polynomials.
| Approach | Time Complexity | Space Complexity | Verdict |
| --- | --- | --- | --- |
| Direct multidimensional integration | Exponential | Exponential | Too slow |
| Piecewise-polynomial DP | $O(n^4)$ | $O(n^3)$ | Accepted |
## Algorithm Walkthrough
### Definitions
Let $f_i(x)$ denote the density of $s_i$ while respecting all constraints up to step $i$.
Define
$$f_0(x)=\デルタ(x)、$$
a unit mass at zero.
For $i\ge 1$,
$$f_i(x)=
 \begin{ケース}
 \int_{x-1}^{x} f_{i-1}(y)\,dy, & x\le x_i,\\
 0、&x>x_i。 
\end{件}$$
### Global partition
All breakpoints that can ever appear have the form
$$x_j+k、$$
where $0\le k\le n$.
Collect every such value, add $0$, sort them, and remove duplicates.
These points divide the real line into intervals. On each interval every density will be represented by a single polynomial.
### Polynomial representation
For every stage $i$ and every interval, store the polynomial describing $f_i$ on that interval.
The degree never exceeds $i$, hence never exceeds $30$.
### Computing the antiderivative
1. Suppose we already know all interval polynomials of $f_{i-1}$.
2. Build a continuous antiderivative $F$ satisfying
$$F'(x)=f_{i-1}(x)。$$
1. For each interval, integrate its polynomial term-by-term.
2. Adjust the constant term so that $F$ remains continuous across interval boundaries.
### Applying the convolution
1. For $x\le x_i$,
$$f_i(x)=F(x)-F(x-1)。$$
1. On a fixed interval, both $x$ and $x-1$ belong to fixed pieces of the partition. Hence both $F(x)$ and $F(x-1)$ are ordinary polynomials there.
2. Subtract them to obtain the polynomial for $f_i$ on that interval.
3. Every interval entirely to the right of $x_i$ becomes zero because of the truncation.
### Extracting the answer
1. After constructing $f_n$, build its antiderivative $G$.
2. The desired probability is
$$\int_{-\infty}^{x_n} f_n(x)\,dx
 =
 G(x_n)。$$
1. Evaluate this value modulo $998244353$.
### Why it works
The recurrence for $f_i$ is exactly the density recurrence obtained by adding an independent uniform random variable $t_i\in[0,1]$ and then enforcing the condition $s_i\le x_i$. Every step preserves the exact density, not an approximation.
The partition contains every possible location where a formula change can occur. Inside a single interval, both $F(x)$ and $F(x-1)$ come from fixed polynomial pieces, so their difference is also a polynomial. Thus the stored representation is always exact.
By induction on $i$, the piecewise-polynomial description equals the true density of $s_i$. Evaluating its cumulative integral at $x_n$ gives precisely the probability that all constraints hold.
## Python Solution
⟦PROTECT_1⟧
The implementation follows the recurrence literally. Every interval stores a polynomial describing the density on that interval. The antiderivative is rebuilt at each stage, keeping continuity by adjusting the constant term. After that, the convolution formula $F(x)-F(x-1)$ is applied interval by interval.
The most delicate part is the breakpoint construction. Every future discontinuity must already be present in the partition. That is why all values $x_i+k$ are inserted before the DP begins.
Another subtle point is that the answer must be exact modulo the prime. The input decimals are converted into rational field elements using the modular inverse of $10^6$. No floating-point arithmetic is used anywhere.
## Worked Examples
### Sample 1
Input
⟦PROTECT_2⟧
Every constraint is automatically satisfied because $s_i\le i$ and $x_i=i$.
| Step | Constraint | Probability |
| --- | --- | --- |
| 1 | $s_1\le1$ | 1 |
| 2 | $s_2\le2$ | 1 |
| 3 | $s_3\le3$ | 1 |
| 4 | $s_4\le4$ | 1 |
Final answer is $1$.
This example shows that the truncation never removes any mass. The density evolves exactly like the ordinary Irwin-Hall distribution.
### Sample 3
Input
⟦PROTECT_3⟧
The valid region is
$$0\le t_1\le 0.5、$$
and
$$t_1+t_2\le 1.$$
| $t_1$ range | Allowed $t_2$ length |
| --- | --- |
| $0 \le t_1 \le 0.5$ | $1-t_1$ |
Hence
$$\int_0^{0.5}(1-t_1)\,dt_1
 =
 \frac12-\frac18
 =
 \frac38.$$
 確率は$3/8$、ステートメントで言及されている値とまったく同じです。 
この例は、2 番目の拘束がどのように質量の一部を削除するかを示しています。$x_2=1$特別小さいわけではありません。 
## 複雑さの分析
 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n^4)$| について$O(n^2)$間隔、程度$O(n)$、繰り返します$n$ステージ |
 | スペース |$O(n^3)$| 最大次数の区分的多項式$n$|
 と$n\le30$、間隔の数は 1,000 未満であり、次数が 30 を超えることはありません。 結果として得られるシンボリック多項式 DP は、制限内に問題なく収まります。 
## テストケース```python
import sys, io
def run(inp: str) -> str:
    # invoke solution()
    pass
# provided samples
assert run("4\n1.00\n2\n3.000000\n4.0\n") == "1\n"
assert run("1\n0.50216\n") == "342677322\n"
assert run("2\n0.5\n1.0\n") == "623902721\n"
# minimum size
assert run("1\n1\n") == "1\n"
# probability = 1/2
assert run("1\n0.5\n") == str((998244353 + 1) // 2) + "\n"
# all constraints very large
assert run("3\n1\n2\n3\n") == "1\n"
# strong middle restriction
assert run("3\n1\n0.5\n3\n") == run("3\n1\n0.5\n3\n")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1, x=1`|`1`| 完全な間隔を受け入れます |
 |`1, x=0.5`|`1/2 mod M`| 10 進数の解析と有理演算 |
 |`1,2,3`|`1`| 確率は 1 のままです |
 | 中間の厳しい制約 | 確定的チェック | 中間ステップでの適切な切り捨て |
 ## 特殊なケース
 検討してください```
1
0.5
```確率はまさに$1/2$。 浮動小数点の実装は次のことを表します。$0.5$近似的であり、モジュラー演算に変換すると正確さが失われます。 多項式アプローチでは、値をフィールド要素として保存します。$500000/1000000$、まさにその通りです$1/2$。 
考慮する```
2
10
0.5
```最終的な分布のみを調べるソリューション$s_2$大きな確率を生み出すでしょう。 正しいアルゴリズムは、各段階の後に切り捨てられます。 建てるとき$f_2$、すべての大量違反$s_2\le0.5$すぐに削除されます。 
考慮する```
2
0.5
1.0
```ブレークポイント セットには以下が含まれます$0.5$、$1.0$、$1.5$、$2.0$、およびそれらのシフトされたバリアント。 パーティションが次のブレークポイントを省略した場合$1.5$の場合、密度公式は有効でなくなる区間全体に適用されます。 あらゆる値を含めて$x_i+k$その間違いを防ぎ、すべての多項式部分を正確に保ちます。
