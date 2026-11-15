---
 タイトル：『CF1890D ドレミーのコネクティングプラン』
 説明: 「私たちは持っています$n$都市、どこの都市$i$含まれています$ai$人々。 最初はすべての都市が孤立しています。 都市間のエッジ$i$そして$j$接続されたコンポーネントに属するすべての都市の総人口が次の値を含む場合にのみ追加できます。$i$または$j$少なくとも$$i cdot j cdot c." date: "2026-06-09T01:09:44+07:00" tags: ["codeforces", "competitive-programming", "graphs", "greedy", "math", "sortings"] categories: ["algorithms"] codeforces_contest: 1890 codeforces_index: "D" codeforces_contest_name: "Codeforces Round 906 (Div. 2)" rating: 1700 weight: 1890 solve_time_s: 138 verified: true draft: false --- [CF 1890D - Doremy's Connecting Plan](https://codeforces.com/problemset/problem/1890/D) **Rating:** 1700   **Tags:** graphs, greedy, math, sortings   **Solve time:** 2m 18s   **Verified:** yes   ## Solution ## Problem Understanding We have $n$ cities, where city $i$ contains $a_i$ people. Initially every city is isolated. An edge between cities $i$ and $j$ can be added only if the total population of all cities belonging to the connected components containing $i$ or $j$ is at least$$i \cdot j \cdot c.$$
Whenever we connect two different components, their populations merge and become available for future operations.
The task is to determine whether there exists some sequence of edge additions that eventually makes the whole graph connected.
The constraints are the first clue. The total number of cities across all test cases is at most $2 \cdot 10^5$, which strongly suggests an $O(n \log n)$ solution. Any algorithm that repeatedly scans all pairs of cities would require $O(n^2)$ or worse operations, which would be far too slow for $n=2\cdot10^5$.
The tricky part is that the validity of an edge depends on the current connected components, not only on the endpoints. After several merges, a previously impossible edge may become possible because the component population has grown.
A common mistake is to think that any pair of cities can serve as the next merge candidate. The city indices appear in the cost formula, and index $1$ is special because
$$1 \cdot i \cdot c = i c$$
is the smallest possible cost involving city $i$.
Consider:
⟦PROTECT_0⟧
City $1$ alone already has population $100$. It can connect to city $2$ because $100+1 \ge 20$, then to city $3$ because $101+1 \ge 30$. The answer is YES.
A naive strategy that tries to connect arbitrary cheapest pairs first may fail to notice that city $1$ is the natural hub.
Another subtle case is:
⟦PROTECT_1⟧
The answer is NO. Although the total population is positive, city $1$ cannot connect to city $2$ because $0+0 < 2$, and city $1$ cannot connect to city $3$ because $0+2 < 3$. No merge is possible.
The solution must correctly identify when the growth process gets stuck before reaching all cities.
## Approaches
A brute-force simulation would treat each city as its own component and repeatedly search for any pair of components that can be merged.
To test whether two components can be connected, we would need their total population and some pair of cities capable of satisfying the inequality. Even with a disjoint-set structure, searching all component pairs after every merge is expensive. There can be $O(n)$ merges, and each step may examine $O(n^2)$ candidates, leading to $O(n^3)$ behavior in the worst case.
The key observation is that city $1$ dominates the problem.
Suppose we already have a component containing city $1$, with total population $S$. To attach city $i$ directly to this component, we need
$$S + a_i \ge i \cdot c.$$
This is the cheapest possible way to bring city $i$ into the growing component, because every other city index $j\ge1$ gives
$$i \cdot j \cdot c \ge i \cdot c。$$
If city $i$ cannot be attached to the component containing city $1$, then connecting it through some other city cannot be easier.
This transforms the problem into a growth process.
Start with the component containing city $1$. Its current population is $a_1$.
For every city $i>1$, define its contribution:
$$ゲイン_i = a_i - i c.$$
If the current population is $S$, then city $i$ is attachable exactly when
$$S + ゲイン_i \ge 0.$$
After attaching it, the population becomes
$$S \leftarrow S + a_i。$$
Among all currently attachable cities, we should take the one with the largest contribution $gain_i$. This maximizes future population growth and can never hurt.
The resulting process is identical to a classic greedy problem. We sort cities by $gain_i$ in descending order and keep adding cities while the current population is large enough to satisfy the corresponding requirement.
If every city can be added, the graph can be connected. Otherwise it cannot.
| Approach | Time Complexity | Space Complexity | Verdict |
| --- | --- | --- | --- |
| Brute Force | $O(n^3)$ worst case | $O(n)$ | Too slow |
| Optimal Greedy + Sorting | $O(n \log n)$ | $O(n)$ | Accepted |
## Algorithm Walkthrough
1. Start with the component containing city $1$. Let its current population be $S=a_1$.
2. For every city $i\ge2$, compute
$$ゲイン_i = a_i - i c.$$
This quantity measures how much population remains after paying the minimum possible connection cost involving city $1$.
3. Sort all cities $i\ge2$ by $gain_i$ in descending order.
4. Process the sorted cities from largest gain to smallest.
5. For the current city, check whether
$$S + ゲイン_i \ge 0.$$
This is equivalent to
$$S + a_i \ge i c,$$
meaning the city can be connected directly to the component containing city $1$.
6. If the condition fails for some city, output `"NO"`.
The remaining cities have gain no larger than the current one, so none of them can be connected either.
7. Otherwise merge the city into the component and update
$$S \leftarrow S + a_i。$$
8. After all cities are processed successfully, output `"YES"`.
### Why it works
Let $S$ be the population of the component containing city $1$.
For any city $i$, the cheapest possible way to attach it to that component is using an edge involving city $1$, whose requirement is $i c$. Any edge using another city $j$ requires $i j c$, which is never smaller.
Hence a city is usable exactly when $S+a_i \ge i c$.
Suppose several cities are currently usable. Choosing the city with the largest gain adds the most population to $S$, producing the largest possible future component. If a solution exists after choosing some other city, it also exists after choosing the largest-gain city because the component population is at least as large afterward.
This exchange argument shows that the greedy choice is always safe. If the greedy process gets stuck at some city, no alternative ordering could have produced a larger current population, so no valid sequence exists.
## Python Solution
⟦PROTECT_2⟧
The variable `cur` stores the population of the component containing city $1$.
For every other city we store two values. The first is `gain = a[i] - i*c`, which determines whether the city can currently be attached. The second is its population, which will be added to `cur` after the merge.
Sorting by descending gain implements the greedy choice. The check
⟦PROTECT_3⟧
is exactly the condition
$$cur + a_i < i c.$$
 失敗すると街が繋がらなくなる。 残りのゲインはすべてさらに小さいため、それ以降の都市に接続することもできません。 
Python の整数は、次の値まで自動的に処理します。$10^{12}$したがって、オーバーフローのリスクはありません。 固定幅の整数を使用する言語では、64 ビットの算術演算が必要になります。 
## 実用的な例
 ### 例 1
 入力:```
4 10
0 20 15 10
```計算されたゲイン:
 | 都市 | 人口 | ゲイン = ai - i*c |
 | --- | --- | --- |
 | 2 | 20 | 0 |
 | 3 | 15 | -15 |
 | 4 | 10 | -30 |
 ソート順はそのまま$2,3,4$。 
| ステップ | 現在のS | ゲイン | S+ゲイン >= 0 | を確認してください。 新しいS |
 | --- | --- | --- | --- | --- |
 | 開始 | 0 | - | - | 0 |
 | シティ2 | 0 | 0 | はい | 20 |
 | 都市3 | 20 | -15 | はい | 35 |
 | 都市4 | 35 | -30 | はい | 45 |
 すべての都市が接続されているため、答えは「YES」です。 
この例は、コンポーネントが時間の経過とともにどのように成長するかを示しています。 早期にマージすると利用可能な人口が増加し、後のマージが可能になります。 
### 例 2
 入力:```
5 2
1 1 3 1 1
```計算されたゲイン:
 | 都市 | 人口 | ゲイン |
 | --- | --- | --- |
 | 2 | 1 | -3 |
 | 3 | 3 | -3 |
 | 4 | 1 | -7 |
 | 5 | 1 | -9 |
 並べ替えられた利益:$-3,-3,-7,-9$。 
| ステップ | 現在のS | ゲイン | S+ゲイン >= 0 | を確認してください。 
| --- | --- | --- | --- |
 | 開始 | 1 | - | - |
 | 最初の都市 | 1 | -3 | いいえ |
 プロセスはすぐに停止するため、答えは「NO」です。 
これは、総人口がプラスであるだけでは十分ではないことを示しています。 都市を含むコンポーネント$1$成長を始めることができるはずです。 
## 複雑さの分析
 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n \log n)$| を並べ替える$n-1$ゲインが優勢 |
 | スペース |$O(n)$| ゲインと人口の保存 |
 すべての合計$n$テスト ケース全体の値は最大でも$2\cdot10^5$。 アン$O(n \log n)$このアルゴリズムは Python の 1 秒制限内に簡単に収まり、メモリ使用量は 256 MB 制限を余裕で下回ります。 
## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    def solve():
        input = sys.stdin.readline
        t = int(input())
        out = []
        for _ in range(t):
            n, c = map(int, input().split())
            a = list(map(int, input().split()))
            cur = a[0]
            cities = []
            for i in range(2, n + 1):
                cities.append((a[i - 1] - i * c, a[i - 1]))
            cities.sort(reverse=True)
            ok = True
            for gain, pop in cities:
                if cur + gain < 0:
                    ok = False
                    break
                cur += pop
            out.append("YES" if ok else "NO")
        return "\n".join(out)
    return solve()
# provided sample
assert run(
"""7
4 10
0 20 15 10
2 1
1 1
5 1
0 1 0 4 199
5 2
1 1 3 1 1
5 5
5 6 1 10 2
5 1000000
1000000000000 1000000000000 1000000000000 1000000000000 1000000000000
3 1
0 0 2
"""
) == """YES
YES
YES
NO
NO
YES
NO"""
# minimum size, connectable
assert run(
"""1
2 1
10 0
"""
) == "YES"
# minimum size, not connectable
assert run(
"""1
2 5
0 0
"""
) == "NO"
# all equal values
assert run(
"""1
4 1
5 5 5 5
"""
) == "YES"
# city 1 cannot start growing
assert run(
"""1
3 1
0 0 2
"""
) == "NO"
# large value boundary
assert run(
"""1
2 1000000
1000000000000 1000000000000
"""
) == "YES"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 1 / 10 0`| はい | 最小有効インスタンス |
 |`2 5 / 0 0`| いいえ | 無効なインスタンスの最小数 |
 | すべての値は 5 に等しい | はい | 貪欲な注文は問題ではありません |
 |`0 0 2`| いいえ | 都市 1 を含むコンポーネントは成長を開始できません。 
| 大きい$10^{12}$値 | はい | 64 ビット スケール数値の正しい処理 |
 ## 特殊なケース
 次のことを考慮してください。```
1
3 1
0 0 2
```最初は$S=0$。 唯一の利益は、$-2$そして$-1$。 並べ替え後も、最初の候補は満たされます。$S+gain<0$。 アルゴリズムは直ちに NO を返します。 No edge can be added in reality either, so the answer is correct.
 考慮する：```
1
2 1
1 1
```から始めます$S=1$。 2番目の都市が利益を得ました$1-2=-1$。 以来$1+(-1)=0$、マージは許可されます。 アルゴリズムは YES を受け入れて出力します。 これにより、誰かが`<=`の代わりに`<`。 
考慮する：```
1
4 10
0 20 15 10
```初期コンポーネントの母集団はゼロです。 不注意な解決策はすぐに拒否される可能性があります。 代わりに、アルゴリズムは利得順に都市をチェックします。 市$2$利益があります$0$、最初のマージが可能になります。 その後、コンポーネントが成長し、残りの都市に到達できるようになります。 正解は「はい」です。 
考慮する：```
1
5 5
5 6 1 10 2
```人口ベースで最も見栄えの良い都市は city です$4$ですが、最初は取り付けられません。 ゲインによって並べ替えると、実際に合併できる都市に優先順位が付けられます。 最終的にプロセスは行き詰まり、アルゴリズムは公式の答えと一致する NO を出力します。
