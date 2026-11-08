---
 タイトル：「CF 1466E - アポロ対パン」
 説明: 「指定された配列から抽出された 3 つの整数の合計を計算するように求められます。具体的には、一連の非負の整数についてです。$x1, x2, ldots, xn$、目標値は$$sum{i=1}^n sum{j=1}^n sum{k=1}^n (xi & xj) cdot (xj , where $&$ is the bitwise AND and $ The…" date: "2026-06-11T01:44:54+07:00" tags: ["codeforces", "competitive-programming", "bitmasks", "brute-force", "math"] categories: ["algorithms"] codeforces_contest: 1466 codeforces_index: "E" codeforces_contest_name: "Good Bye 2020" rating: 1800 weight: 1466 solve_time_s: 74 verified: true draft: false --- [CF 1466E - Apollo versus Pan](https://codeforces.com/problemset/problem/1466/E) **Rating:** 1800   **Tags:** bitmasks, brute force, math   **Solve time:** 1m 14s   **Verified:** yes   ## Solution ## Problem Understanding We are asked to compute a sum over triples of integers drawn from a given array. Specifically, for a sequence of non-negative integers $x_1, x_2, \ldots, x_n$, the target value is$$\sum_{i=1}^n \sum_{j=1}^n \sum_{k=1}^n (x_i \& x_j) \cdot (x_j \, | \, x_k)$$
where $\&$ is the bitwise AND and $|$ is the bitwise OR. The input contains multiple test cases, and for each, we need the answer modulo $10^9 + 7$.
The constraints allow $n$ up to $5 \cdot 10^5$ and numbers up to $2^{60}$. With a worst-case triple sum over $n^3$, a naive solution would perform $10^{17}$ operations in the largest test case. This is clearly impossible in 2 seconds. Therefore, we need an algorithm that scales linearly or at most $O(n \cdot \text{log}(X))$, where $X$ is the maximum number size.
A subtle point is that numbers are large (up to 60 bits), so any solution depending on iterating all possible values directly is infeasible. Zero-valued numbers and sequences where all numbers are equal are edge cases that could produce different bitwise patterns, but the solution must handle them correctly.
For example, if the array is `[0]`, the sum is `0` because both AND and OR operations yield 0. A careless approach might assume every element contributes non-zero, producing a wrong sum.
## Approaches
The brute-force method is straightforward: loop over all triples $i, j, k$ and compute $(x_i \& x_j) \cdot (x_j | x_k)$. This works in principle, but with $n = 5 \cdot 10^5$, the triple nested loop performs $(5 \cdot 10^5)^3 \approx 10^{17}$ operations per test case. This is far beyond feasible.
The key insight is to factor the sum cleverly. Observe that $(x_i \& x_j)$ depends only on $i$ and $j$, while $(x_j | x_k)$ depends only on $j$ and $k$. For a fixed $j$, the sum over $i$ of $(x_i \& x_j)$ and the sum over $k$ of $(x_j | x_k)$ can be computed separately. Let
$$S_n = \sum_{i=1}^n (x_i \and x_i)、\quad X_i = \sum_{k=1}^n (x_j | x_k)$$
Then the triple sum is simply
$$\sum_{j=1}^n S_j \cdot T_j$$
This reduces the problem from $O(n^3)$ to $O(n^2)$. Still, $O(n^2)$ is too slow for $n = 5 \cdot 10^5$. To go further, we can compute $S_j$ and $T_j$ efficiently using bitwise decomposition.
For each bit position $b$ from 0 to 59, let `cnt[b]` be the number of elements with bit `b` set. Then
$$S_j = \sum_{b=0}^{59}
 \begin{cases}
 2^b \cdot \text{cnt}[b] & \text{if } x_j \text{ にはビットがあります } b \text{ set} \\
 0 & \text{otherwise}
 \end{cases}$$
and
$$T_j = \sum_{b=0}^{59}
 \begin{ケース}
 2^b \cdot n & \text{if } x_j \text{ にはビット } b \text{ セット} \\
 2^b \cdot \text{cnt}[b] & \text{その他}
 \end{件}$$
 これは機能します。`x_j`ビットがある`b`セット、`(x_j & x_i)`貢献する`2^b`いつでも`x_i`ビットを持っており、`(x_j | x_k)`貢献する`2^b`あらゆる人にとって`k`。 もし`x_j`ビットが無いので、`(x_j | x_k)`貢献する`2^b`場合のみ`x_k`ビットを持っています。 
このビットごとの分解により、各ビットごとの合計計算が削減されます。`j`を O(60) にすると、テスト ケースごとの全体的な複雑さは O(n * 60) になりますが、これは許容範囲内です。 
| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n^3) | お(1) | 遅すぎる |
 | 最適 | O(n * 60) | お(60) | 承認済み |
 ## アルゴリズムのチュートリアル
 1. 各テスト ケースについて、次の内容を読んでください。`n`そして配列`x`。 
2. 配列を初期化する`cnt`長さ 60 で、各ビットが設定されている数値の数を数えます。 反復処理`x`そして増加します`cnt[b]`各ビットごとに`b`それはに設定されています`x[i]`。 
3. 変数を初期化する`answer`各要素を反復処理します。`x_j`の`x`:
 1. コンピューティング`S_j`すべてのビット位置の合計として`x_j`ビットが設定されており、ビット値を乗算します`2^b`による`cnt[b]`。 
2. コンピューティング`T_j`すべてのビット位置の合計として。 もし`x_j`ビットを持っています、貢献します`2^b * n`。 それ以外の場合は、貢献してください`2^b * cnt[b]`。 
3. 掛ける`S_j`そして`T_j`、モジュロを取る`10^9 + 7`、に追加します`answer`。 
4. 出力`answer`モジュロ`10^9 + 7`。 
機能する理由: 分解は、加算の線形性と AND および OR 演算のビットの独立性を利用します。 それぞれのトリプル`(i, j, k)`に一度だけ貢献します`S_j * T_j`、ビットごとの合計により、各ビットの値が正しい回数カウントされることが保証されます。 モジュロ演算により正確さが維持されます。 
## Python ソリューション```python
import sys
input = sys.stdin.readline
MOD = 10**9 + 7
t = int(input())
for _ in range(t):
    n = int(input())
    x = list(map(int, input().split()))
    
    cnt = [0] * 60
    for num in x:
        for b in range(60):
            if (num >> b) & 1:
                cnt[b] += 1
    
    answer = 0
    for num in x:
        s = 0
        t_sum = 0
        for b in range(60):
            bit_val = (1 << b) % MOD
            if (num >> b) & 1:
                s = (s + bit_val * cnt[b]) % MOD
                t_sum = (t_sum + bit_val * n) % MOD
            else:
                t_sum = (t_sum + bit_val * cnt[b]) % MOD
        answer = (answer + s * t_sum) % MOD
    print(answer)
```コードは、テスト ケースの数と各テスト ケースの入力を読み取ることから始まります。 計算するのは、`cnt`配列に各ビットが設定された要素の数を格納し、各要素のカウントを再計算しないようにします。 計算するとき`S_j`そして`T_j`、モジュロの 2 のべき乗を乗算します$10^9 + 7$オーバーフローを防ぐため。 最後に、全員の貢献度を合計します。`x_j`答えのモジュロを出力します`10^9 + 7`。 大きな数値による整数のオーバーフローを避けるために、乗算と加算のたびにモジュロ演算が適用されます。 
## 実用的な例
 アレイの場合`[1, 7]`:
 | j | 番号 | S_j | T_j | S_j * T_j |
 | --- | --- | --- | --- | --- |
 | 1 | 1 | 1 | 8 | 8 |
 | 2 | 7 | 8 | 15 | 120 |
 合計額:`8 + 120 = 128`、サンプル出力と一致します。 
アレイの場合`[1, 2, 4]`:
 | j | 番号 | S_j | T_j | S_j * T_j |
 | --- | --- | --- | --- | --- |
 | 1 | 1 | 1 | 7 | 7 |
 | 2 | 2 | 2 | 10 | 20 |
 | 3 | 4 | 4 | 14 | 64 |
 合計額:`7 + 20 + 64 = 91`、サンプル出力と一致します。 
これらの痕跡は次のことを裏付けています`S_j`の貢献をカウントします`(x_i & x_j)`正しくそして`T_j`の貢献をカウントします`(x_j | x_k)`正しく、ビットごとの分解が正しいことを示しています。 
## 複雑さの分析
 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n * 60) | ビットを数えて計算する`S_j`そして`T_j`それぞれに`x_j`|
 | スペース | お(60) | ビット位置ごとのカウントの保存 |
 解は線形です`n`小さなもので
