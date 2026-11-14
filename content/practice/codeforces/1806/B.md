---
 タイトル: 「CF 1806B - メキシコマスター」
 説明: "配列が与えられ、その要素を好きなように並べ替えることができます。順序を選択した後、隣接する要素の合計で構成される新しい配列を構築します。$$b=[a1+a2, a2+a3, ldots, a{n-1}+an]." date: "2026-06-09T09:11:13+07:00" tags: ["codeforces", "competitive-programming", "constructive-algorithms", "greedy"] categories: ["algorithms"] codeforces_contest: 1806 codeforces_index: "B" codeforces_contest_name: "Codeforces Round 858 (Div. 2)" rating: 900 weight: 1806 solve_time_s: 290 verified: false draft: false --- [CF 1806B - Mex Master](https://codeforces.com/problemset/problem/1806/B) **Rating:** 900   **Tags:** constructive algorithms, greedy   **Solve time:** 4m 50s   **Verified:** no   ## Solution ## Problem Understanding We are given an array and may permute its elements however we like. After choosing an ordering, we build a new array consisting of sums of neighboring elements:$$b=[a_1+a_2,\ a_2+a_3,\ \ldots,\ a_{n-1}+a_n]。$$
The score of the arrangement is the MEX of $b$, where MEX is the smallest non-negative integer that does not appear.
Our task is not to construct the optimal ordering. We only need the minimum possible score obtainable after rearranging the original numbers.
The constraints immediately suggest that the answer must come from a simple structural observation. The total length over all test cases is only $2\cdot 10^5$, so an $O(n)$ or $O(n \log n)$ solution per test case is easily fast enough. Any approach that tries many permutations is hopeless because even $n=20$ already makes enumeration impossible.
The interesting part is that only the values $0$, $1$, and "greater than $1$" matter.
Consider a few edge cases.
If there is a positive number between every pair of zeros, then no adjacent pair sums to $0$. For example:
⟦PROTECT_0⟧
The adjacent sums are:
⟦PROTECT_1⟧
MEX is $0$.
A careless solution that only checks whether zeros exist would incorrectly return $1$.
Now consider:
⟦PROTECT_2⟧
The only adjacent sum is:
⟦PROTECT_3⟧
MEX is $1$.
Finally:
⟦PROTECT_4⟧
No matter how we arrange the numbers, at least one adjacent pair of zeros exists. That creates a sum $0$. At the same time, every positive number is at least $2$, so sum $1$ can never appear. The resulting minimum MEX is $1$.
These examples already hint that only the counts of zeros and ones control the answer.
## Approaches
A brute-force solution would try every permutation of the array. For each permutation it would build the adjacent-sum array, compute its MEX, and keep the minimum.
This is correct because it directly checks every possible arrangement. Unfortunately, it requires $n!$ permutations. Even $n=12$ would already be completely infeasible.
The key observation is that the minimum possible MEX can only be $0$, $1$, or $2$.
To obtain MEX $0$, we need to avoid creating a sum equal to $0$. Since all values are non-negative, the only way to get a sum $0$ is with two adjacent zeros.
Thus MEX $0$ is possible exactly when we can arrange the array so that no two zeros are adjacent.
Suppose there are $z$ zeros and $p=n-z$ positive numbers.
Each positive number can separate at most one gap between consecutive zeros. To place all zeros without adjacency we need
$$z \le p+1.$$
Equivalently,
$$z \le n-z+1.$$
If this holds, we can eliminate all occurrences of $0$ from the adjacent-sum array, so MEX becomes $0$.
Now assume $z>p+1$. Then some pair of adjacent zeros is unavoidable, so $0$ must appear.
Can we make MEX equal to $1$? We need $0$ to appear but $1$ not to appear.
A sum $1$ can only be produced by $0+1$, because all numbers are non-negative integers.
If the array contains no ones, then sum $1$ is impossible, hence the answer is immediately $1$.
The only remaining situation is:
$$z>p+1$$
 以下に等しい値が少なくとも 1 つあります$1$。 
それから$0$避けられないものであり、$0+1$隣接も避けられません。 その場合はどちらも$0$そして$1$が表示されるため、最小 MEX は$2$。 
これで完全に答えが決まります。 
| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(n!\cdot n)$|$O(n)$| 遅すぎる |
 | 最適 |$O(n)$|$O(1)$| 承認済み |
 ## アルゴリズムのチュートリアル
 1. ゼロの数を数えて、それを呼び出します$z$。 
2. 1の数を数えて、それを呼び出します$o$。 
3. しましょう$p=n-z$、正の要素の数。 
4. もし$z \le p+1$、出力$0$。 
この場合、ゼロは正の要素で区切ることができるため、隣接するペアの和が一致することはありません。$0$。 以来$0$隣接和配列に存在しない場合、その MEX は$0$。 
5. そうしないと、隣接するゼロのペアが発生することは避けられないため、$0$が表示されます。 
6. もし$o=0$、出力$1$。 
合計$1$が必要です$0$そして$1$。 何もない場合の合計$1$起こり得ない。 
7. それ以外の場合は出力$2$。 
ここ$0$隣接するゼロは避けられないため表示されます。$1$強制的に表示することもできます。$1$存在します。 最小の欠損値は次のようになります。$2$。 
### なぜ効果があるのか
 合計を作成する唯一の方法$0$隣接するゼロです。 したがって、MEX を達成する可能性は$0$すべてのゼロを分離できるかどうかに完全に依存します。 
ゼロをすべて分離できない場合は、$0$は必ず隣接和配列に属します。 次の質問は、$1$回避できる。 
すべての値は負ではないため、$1$2 つの配列要素の合計は$0+1$。 等しい要素がない場合$1$存在する場合は合計します$1$不可能であり、MEX は$1$。 それ以外の場合は両方とも$0$そして$1$が発生し、MEX が強制的に$2$。 
それ以外の答えはあり得ません。 
## Python ソリューション```python
import sys
input = sys.stdin.readline
def solve():
    t = int(input())
    ans = []
    for _ in range(t):
        n = int(input())
        a = list(map(int, input().split()))
        zeros = a.count(0)
        ones = a.count(1)
        positive = n - zeros
        if zeros <= positive + 1:
            ans.append("0")
        elif ones == 0:
            ans.append("1")
        else:
            ans.append("2")
    sys.stdout.write("\n".join(ans))
if __name__ == "__main__":
    solve()
```実装は証明に直接続きます。 
変数`zeros`隣接するゼロを回避できるかどうかを決定します。 条件`zeros <= positive + 1`は、まさに上で導出された区切り条件です。 
隣接するゼロが避けられない場合は、いずれかの要素が以下に等しいかどうかだけを知る必要があります。`1`。 他の値は重要ではありません。`0+1`以下に等しい合計を生成できます`1`。 
すべての算術演算は標準の Python 整数に簡単に適合します。 
## 実用的な例
 ### 例 1
 入力:```
3
0 0 1
```| 数量 | 値 |
 | --- | --- |
 | ゼロ | 2 |
 | もの | 1 |
 | ポジティブ | 1 |
 | チェック |$2 \le 1+1$|
 条件が成り立つため、ゼロを区切ることができます。```
0 1 0
```隣接する合計:```
1 1
```メキシコは$0$。 
答え：```
0
```これは、単にゼロがあるだけでは合計が強制されないことを示しています。$0$現れること。 
### 例 2
 入力:```
8
1 0 0 0 2 0 3 0
```| 数量 | 値 |
 | --- | --- |
 | ゼロ | 5 |
 | もの | 1 |
 | ポジティブ | 3 |
 | チェック |$5 \le 3+1$は偽です |
 隣接するゼロは避けられないため、$0$が表示されます。 
値$1$存在するので合計$1$も発生する可能性があります。 
したがって両方とも$0$そして$1$は隣接合計配列に属し、最小 MEX は次と等しくなります。$2$。 
答え：```
2
```これはまさに 3 番目のサンプルです。 
## 複雑さの分析
 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n)$| 0 と 1 を数える 1 回のパス |
 | スペース |$O(1)$| 少数のカウンターのみが保存されます。 
すべてのテスト ケースの合計入力サイズは最大でも$2\cdot10^5$したがって、すべての配列の線形スキャンは簡単に制限内に収まります。 
## テストケース```python
import sys
import io
def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline
    t = int(input())
    out = []
    for _ in range(t):
        n = int(input())
        a = list(map(int, input().split()))
        zeros = a.count(0)
        ones = a.count(1)
        positive = n - zeros
        if zeros <= positive + 1:
            out.append("0")
        elif ones == 0:
            out.append("1")
        else:
            out.append("2")
    return "\n".join(out)
# provided samples
assert run(
"""3
2
0 0
3
0 0 1
8
1 0 0 0 2 0 3 0
"""
) == "1\n0\n2"
# minimum size
assert run(
"""1
2
5 7
"""
) == "0"
# all zeros
assert run(
"""1
5
0 0 0 0 0
"""
) == "1"
# many zeros and a one
assert run(
"""1
5
0 0 0 0 1
"""
) == "2"
# enough positive separators
assert run(
"""1
6
0 0 1 2 3 4
"""
) == "0"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`5 7`|`0`| ゼロ和は存在できません。 
|`0 0 0 0 0`|`1`| 隣接するゼロは不可避、ゼロは不可 |
 |`0 0 0 0 1`|`2`| 隣接するゼロは避けられませんが、1 つは存在します。 
|`0 0 1 2 3 4`|`0`| 正の数値はすべてのゼロを区切ることができます。 
## 特殊なケース
 次のことを考慮してください。```
2
0 0
```ここ`zeros = 2`、`positive = 0`。 条件`2 <= 1`は誤りです。 誰もいないので、答えは次のとおりです。`1`。 唯一の隣接和は`0`、その MEX は確かに`1`。 
考慮する：```
3
0 0 1
```ここ`zeros = 2`、`positive = 1`。 条件`2 <= 2`それは本当です。 数字を次のように整理できます`0 1 0`、隣接する合計を生成します`1, 1`。 以来`0`が存在しない場合、答えは`0`。 
考慮する：```
5
0 0 0 0 1
```ここ`zeros = 4`、`positive = 1`。 条件`4 <= 2`は false であるため、いくつかの隣接するゼロが存在する必要があります。 以来`1`が存在し、両方の合計`0`そして`1`発生する可能性があります。 答えは`2`。 
これらのケースは、ソリューションの考えられるすべての分野をカバーしています。
