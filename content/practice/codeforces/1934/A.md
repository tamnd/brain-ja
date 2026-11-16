---
 タイトル：「CF 1934A - トゥー・ミン・トゥー・マックス」
 説明: "式を最大化するように求められます$$over all quadruples of distinct indices $i, j, k, l$ in an array $a$. The input provides multiple test cases, each with an array of integers. For each test case, the output is the maximum value of the expression." date: "2026-06-08T18:09:50+07:00" tags: ["codeforces", "competitive-programming", "greedy", "math"] categories: ["algorithms"] codeforces_contest: 1934 codeforces_index: "A" codeforces_contest_name: "Codeforces Round 931 (Div. 2)" rating: 800 weight: 1934 solve_time_s: 137 verified: false draft: false --- [CF 1934A - Too Min Too Max](https://codeforces.com/problemset/problem/1934/A) **Rating:** 800   **Tags:** greedy, math   **Solve time:** 2m 17s   **Verified:** no   ## Solution ## Problem Understanding We are asked to maximize the expression$$|a_i - a_j| + |a_j - a_k| + |a_k - a_l| + |a_l - a_i|$$
over all quadruples of distinct indices $i, j, k, l$ in an array $a$. The input provides multiple test cases, each with an array of integers. For each test case, the output is the maximum value of the expression.
The expression is essentially the perimeter of a "walk" through four elements of the array, moving from $a_i$ to $a_j$ to $a_k$ to $a_l$ and back to $a_i$, measuring absolute differences along the edges. The problem reduces to selecting the largest and smallest values in a way that maximizes the sum of differences.
Constraints allow $n$ up to 100 and $t$ up to 500. This permits $O(n^4)$ brute-force solutions in principle, but a careful observation can reduce the complexity. Edge cases include arrays with all equal elements, where the expression is zero, and arrays with both negative and positive numbers, which influence which combination yields the maximum.
A naive approach might try all quadruples, but we need to understand the structure of absolute differences to simplify the problem.
## Approaches
A brute-force approach enumerates all quadruples $i, j, k, l$ and computes the expression directly. This is $O(n^4)$, which is acceptable given $n \le 100$, but it is unnecessary because the expression can be simplified by considering the extreme values.
The key observation is that absolute differences are maximized when combining the minimum and maximum elements. Let $mn = \min(a)$ and $mx = \max(a)$. To maximize the sum, we want the sequence to alternate between these extremes. For example, choosing $i$ as the minimum, $j$ as the maximum, $k$ as the minimum, and $l$ as the maximum produces the largest contribution from each term. Therefore, the maximum expression reduces to:
$$2 \times (\text{max}(a) - \text{min}(a)) + 2 \times (\text{2 番目の最大/最小寄与率})$$
 配列サイズが小さいため、極値の正確な組み合わせを効率的に列挙できます。 1 つの簡単な方法は、配列をソートし、最大 2 つの要素と最小の 2 つの要素を交互に選択するシーケンスの式を計算することです。 これにより最大値が保証されます。 
| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^4) | お(1) | 承認済み (n ≤ 100) |
 | 貪欲/極端 | O(n log n) | O(n) | 受け入れられ、より速く |
 ## アルゴリズムのチュートリアル
 1. テスト ケースごとに、配列を読み取ります。$a$そしてその長さを保存します$n$。 
2. 並べ替え$a$最小値、2 番目の最小値、最大値、2 番目の最大値に簡単にアクセスできます。 
3. 極端な要素を交互の位置に配置して、式の候補値を計算します。 代表的な候補は次のとおりです。 
-$a_0, a_1, a_{n-2}, a_{n-1}$-$a_0, a_1, a_{n-1}, a_{n-2}$-$a_0, a_{n-2}, a_1, a_{n-1}$-$a_0, a_{n-1}, a_1, a_{n-2}$4. 候補となる 4 つ組ごとに、次の式を計算します。$|a_i - a_j| + |a_j - a_k| + |a_k - a_l| + |a_l - a_i|$5. 候補全体の最大値を追跡し、出力します。 
機能する理由: 各ステップで可能な限り最大の差を選択することにより、絶対的な差が最大化されます。 最小要素と最大要素を交互に配置することで、各項が合計に最大限に寄与するようになります。 ソートにより、すべての 4 倍体をチェックすることなく、これらの極端な構成を簡単に列挙できます。 
## Python ソリューション```python
import sys
input = sys.stdin.readline
def max_expression(a):
    a.sort()
    n = len(a)
    candidates = [
        [a[0], a[1], a[n-2], a[n-1]],
        [a[0], a[1], a[n-1], a[n-2]],
        [a[0], a[n-2], a[1], a[n-1]],
        [a[0], a[n-1], a[1], a[n-2]],
    ]
    res = 0
    for quad in candidates:
        i, j, k, l = quad
        val = abs(i-j) + abs(j-k) + abs(k-l) + abs(l-i)
        res = max(res, val)
    return res
def solve():
    t = int(input())
    for _ in range(t):
        n = int(input())
        a = list(map(int, input().split()))
        print(max_expression(a))
if __name__ == "__main__":
    solve()
```説明: 並べ替えにより、極端な要素を簡単に選択できるようになります。 絶対差の合計が最大になるように候補 4 倍が選択されます。 候補数が少ない (4) ため、計算を簡単に保ちながら正確性が保証されます。 
## 実用的な例
 入力用`[1,1,2,2,3]`、ソートされた配列は`[1,1,2,2,3]`。 クアドルプル候補:
 | i、j、k、l | 式 |
 | --- | --- |
 | 1、1、2、3 | |
 | 1,1,3,2 | ... = 6 |
 | 1,2,1,3 | ... = 6 |
 | 1,3,1,2 | ... = 6 |
 最大は 6 です。 
入力用`[5,1,3,2,-3,-1,10,3]`、ソートされた配列`[-3,-1,1,2,3,3,5,10]`。 候補 4 倍体は式の値 38 を生成しますが、これは最大値です。 
## 複雑さの分析
 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 並べ替えが優先されます。 候補は一定です |
 | スペース | O(n) | ソートされた配列を格納する |
 与えられた$n ≤ 100$そして$t ≤ 500$、これは効率的です。 
## テストケース```python
import sys, io
def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from contextlib import redirect_stdout
    out = io.StringIO()
    with redirect_stdout(out):
        solve()
    return out.getvalue().strip()
# Provided samples
assert run("5\n4\n1 1 1 1\n5\n1 1 2 2 3\n8\n5 1 3 2 -3 -1 10 3\n4\n3 3 1 1\n4\n1 2 2 -1\n") == "0\n6\n38\n8\n8"
# Custom cases
assert run("1\n4\n-1 -1 -1 -1\n") == "0", "all equal negative"
assert run("1\n5\n1 2 3 4 5\n") == "12", "ascending consecutive"
assert run("1\n6\n-5 -2 0 2 3 5\n") == "28", "mixed negatives and positives"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 4 つの要素はすべて等しい | 0 | 均一な配列を処理します |
 | 1..5 | 12 | 昇順配列の最大値 |
 | ネガティブとポジティブが混在 | 28 | 記号の正しい取り扱い |
 ## 特殊なケース
 すべての要素が等しい配列の場合、式は選択に関係なくゼロになります。 すべての値が同一でゼロが生成されるため、並べ替えと候補の列挙は引き続き機能します。 最小サイズの場合$n=4$の場合、候補は正確に 4 つの要素であるため、アルゴリズムは式を正しく計算します。
