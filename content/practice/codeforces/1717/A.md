---
 タイトル：『CF1717A まどかと奇妙な想い』
 説明: 「各テスト ケースに対して、数値 n が与えられます。両方の数値が 1 から n の間にあるように、すべての順序ペア (a, b) を検討します。これらのペアのうち、満たすものがいくつあるかを数えたいと思います。$$frac{operatorname{lcm}(a,b)}{gcd(a,b)} le 3." date: "2026-06-09T19:46:29+07:00" tags: ["codeforces", "competitive-programming", "math", "number-theory"] categories: ["algorithms"] codeforces_contest: 1717 codeforces_index: "A" codeforces_contest_name: "Codeforces Round 818 (Div. 2)" rating: 800 weight: 1717 solve_time_s: 57 verified: true draft: false --- [CF 1717A - Madoka and Strange Thoughts](https://codeforces.com/problemset/problem/1717/A) **Rating:** 800   **Tags:** math, number theory   **Solve time:** 57s   **Verified:** yes   ## Solution ## Problem Understanding For each test case we are given a number `n`. We consider every ordered pair `(a, b)` such that both numbers lie between `1` and `n`. Among those pairs, we want to count how many satisfy$$\frac{\operatorname{lcm}(a,b)}{\gcd(a,b)} \le 3.$$
The answer is the number of ordered pairs with this property.
The limits are very revealing. There can be up to `10^4` test cases, and each `n` can be as large as `10^8`. Any approach that iterates over all pairs immediately becomes impossible, since one test case alone would contain `10^16` pairs. Even an `O(n)` solution per test case would perform about `10^{12}` operations in the worst case across all tests, which is too much. The target complexity is constant time per test case.
There are a few edge cases that deserve attention.
For `n = 1`, the only pair is `(1,1)`, so the answer is `1`. A formula that accidentally assumes the existence of numbers greater than `1` would fail here.
For example:
⟦PROTECT_0⟧
The correct output is
⟦PROTECT_1⟧
For `n = 2`, every ordered pair works:
⟦PROTECT_2⟧
so the answer is `4`. A careless derivation that forgets ordered pairs and counts only unordered pairs would produce `3`.
For example:
⟦PROTECT_3⟧
The correct output is
⟦PROTECT_4⟧
Another common mistake is to assume that only equal numbers are valid. For `n = 3`, pairs `(1,3)` and `(3,1)` also satisfy the condition because
$$\frac{\operatorname{lcm}(1,3)}{\gcd(1,3)}=3。$$
The correct answer is `7`.
Input:
⟦PROTECT_5⟧
Output:
⟦PROTECT_6⟧
## Approaches
The most direct solution examines every ordered pair `(a,b)`, computes `gcd(a,b)` and `lcm(a,b)`, and checks whether their ratio is at most `3`. Since there are `n²` pairs, this requires up to `10^16` checks when `n = 10^8`, which is hopeless.
The brute force works because it literally follows the definition, but it ignores the arithmetic structure hidden inside the ratio.
Suppose
$$g=\gcd(a,b)。$$
We may write
$$a=gx,\qquad b=gy,$$
where `gcd(x,y)=1`.
Since
$$\オペレーター名{lcm}(a,b)=gxy,$$
the ratio becomes
$$\frac{\オペレーター名{lcm}(a,b)}{\gcd(a,b)}
 =xy。$$
Now the problem becomes much simpler. We need coprime positive integers `x` and `y` whose product is at most `3`.
The possibilities are extremely limited.
When `xy=1`, we have
⟦PROTECT_7⟧
When `xy=2`, the coprime pairs are
⟦PROTECT_8⟧
When `xy=3`, the coprime pairs are
⟦PROTECT_9⟧
No larger product is allowed.
Multiplying these pairs by `g` gives:
⟦PROTECT_10⟧
The value of `g` must keep both numbers at most `n`.
Equal pairs contribute `n`.
Pairs `(g,2g)` and `(2g,g)` exist for every `g ≤ n/2`, contributing `2⌊n/2⌋`.
Pairs `(g,3g)` and `(3g,g)` exist for every `g ≤ n/3`, contributing `2⌊n/3⌋`.
Hence
$$\boxed{n+2\left\lfloor\frac n2\right\rfloor+2\left\lfloor\frac n3\right\rfloor}$$
 これはテスト ケースごとの一定時間です。 
| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n²) | お(1) | 遅すぎる |
 | 最適 | お(1) | お(1) | 承認済み |
 ## アルゴリズムのチュートリアル
 1. の値を読み取ります。`n`。 
2. 同じ数を持つすべてのペアを数えます。 まさにあります`n`そのようなペアがあるため、`(g,g)`すべての場合に有効です`1 ≤ g ≤ n`。 
3. フォームのペアを数える`(g,2g)`そして`(2g,g)`。 値`g`からの範囲が可能です`1`に`⌊n/2⌋`、したがって、これらは貢献します`2⌊n/2⌋`。 
4. フォームのペアを数える`(g,3g)`そして`(3g,g)`。 ここ`g`からの範囲`1`に`⌊n/3⌋`、貢献`2⌊n/3⌋`。 
5. 3 つの寄与を加算し、結果を出力します。 
### なぜ効果があるのか
 両方の数値を gcd で割った後、残りの数値は互いに素になります。 比率`lcm/gcd`はそれらの互いに素数の積になります。 その製品は以下を超えてはなりませんので、`3`、正規化されたペアのみ`(1,1)`、`(1,2)`、`(2,1)`、`(1,3)`、 そして`(3,1)`可能です。 すべての有効なペアは、これらの正規化されたペアの 1 つに正の整数を乗算することによって一意に取得されます。`g`。 カウント式はそのようなすべての選択肢を 1 回だけカバーするため、アルゴリズムがペアを見逃したり、1 つを 2 回カウントしたりすることはありません。 
## Python ソリューション```python
import sys
input = sys.stdin.readline
t = int(input())
for _ in range(t):
    n = int(input())
    ans = n + 2 * (n // 2) + 2 * (n // 3)
    print(ans)
```プログラムは各テスト ケースを独立して処理します。 範囲内に収まるのは完全な倍数だけであるため、整数の除算が使用されます。`[1, n]`。 
用語`n`等しいペアをカウントします。 用語`2 * (n // 2)`両方をカウントします`(g,2g)`そして`(2g,g)`、 その間`2 * (n // 3)`カウント`(g,3g)`そして`(3g,g)`。 
整数演算を使用すると、精度の問題を回避できます。 Python の整数は、考えられる最大の答えを自動的に処理します。`266666666`いつ`n = 10^8`。 
3 つのカテゴリは互いに素であるため、操作の順序は簡単です。 複数のカテゴリに属する​​ペアはありません。 
## 実用的な例
 ### 例 1: n = 3
 | ステップ | 値 |
 | --- | --- |
 | 等しいペア | 3 |
 | 比率 2 のペア | 2 |
 | 比率 3 のペア | 2 |
 | 合計 | 7 |
 有効なペアは次のとおりです。```
(1,1)
(2,2)
(3,3)
(1,2)
(2,1)
(1,3)
(3,1)
```この例は、等しくない数値でも条件を満たす可能性があることを示しています。 
### 例 2: n = 5
 | ステップ | 値 |
 | --- | --- |
 | 等しいペア | 5 |
 | 比率 2 のペア | 4 |
 | ペアと | |
