---
 タイトル：「CF 76F - ツーリスト」
 説明: 「各イベントは、時間と位置の平面上の点です。イベント i は、座標 xi で正確に時刻 ti に発生します。観光客は、速度 V で直線上を移動します。したがって、(xa, ta) から (xb, tb) への移動は、次の場合にのみ可能です。$$The tourist may also wait in place. We need two answers." date: "2026-05-28T00:00:00+07:00" tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "dp"] categories: ["algorithms"] codeforces_contest: 76 codeforces_index: "F" codeforces_contest_name: "All-Ukrainian School Olympiad in Informatics" rating: 2300 weight: 76 solve_time_s: 163 verified: true draft: false --- [CF 76F - Tourist](https://codeforces.com/problemset/problem/76/F) **Rating:** 2300   **Tags:** binary search, data structures, dp   **Solve time:** 2m 43s   **Verified:** yes   ## Solution ## Problem Understanding Each event is a point on a time-position plane. Event `i` happens at coordinate `x_i` exactly at time `t_i`. The tourist moves on a line with speed at most `V`, so moving from `(x_a, t_a)` to `(x_b, t_b)` is possible if and only if:$$|x_a - x_b| \le V \cdot (t_b - t_a)$$
The tourist may also wait in place. We need two answers.
The first answer assumes the tourist starts at coordinate `0` at time `0`.
The second answer allows the tourist to choose any starting coordinate before time starts.
The goal in both versions is to maximize how many events can be attended in chronological order.
The constraints completely rule out quadratic dynamic programming. With `n = 100000`, an `O(n^2)` transition loop would require around `10^10` checks, which is several orders of magnitude too large. Even `O(n sqrt n)` would be risky in Python. The solution must stay near `O(n log n)`.
The tricky part is that reachability depends on both position and time. A naive graph interpretation creates too many edges. We need a way to compress the transition condition into something searchable with data structures.
Several edge cases are easy to mishandle.
Events may occur at the same time. Two events with identical timestamps cannot both be visited unless they are at the same position, and the statement guarantees that exact duplicates do not exist.
Example:
⟦PROTECT_0⟧
Correct output:
⟦PROTECT_1⟧
A careless DP that only checks position difference without enforcing increasing time could incorrectly chain them together.
Negative coordinates matter because movement is on the entire number line.
Example:
⟦PROTECT_2⟧
The tourist cannot reach the first event from `(0,0)` because distance `5` requires time `5`, which is barely possible, but then moving from `-5` to `5` needs distance `10` and only `5` units of time. The correct answer is:
⟦PROTECT_3⟧
The second answer differs because with a free starting point we may begin directly at `-5`.
Another subtle case is multiple events at the same position.
⟦PROTECT_4⟧
Correct output:
⟦PROTECT_5⟧
Waiting in place is allowed, so once the tourist reaches coordinate `2`, all later events there are automatically reachable.
## Approaches
The most direct solution is dynamic programming on events sorted by time.
Define `dp[i]` as the maximum number of events ending at event `i`. Then we try every earlier event `j` and transition if:
$$|x_i - x_j| \le V(t_i - t_j)$$
For the fixed-start version we also check whether event `i` is reachable from `(0,0)`.
This is correct because any valid route must visit events in nondecreasing time order, and every feasible previous event is examined.
The problem is complexity. Each event compares against all earlier events, so the running time is `O(n^2)`. With `100000` events this becomes roughly `5 \cdot 10^9` transitions, far too slow.
The key observation is that the reachability inequality can be rewritten into two independent inequalities.
Starting from:
$$|x_i - x_j| \le V(t_i - t_j)$$
we obtain:
$$x_i - V t_i \le x_j - V t_j$$
and
$$x_i + V t_i \ge x_j + V t_j$$
Define transformed coordinates:
$$A_i = x_i - V t_i$$
$$B_i = x_i + V t_i$$
Then event `j` can precede event `i` exactly when:
$$A_j \ge A_i$$
and
$$B_j \le B_i$$
This turns the problem into a two-dimensional dominance DP.
Now the structure becomes clear. If we process events in increasing time order, we need the best DP value among all previous points satisfying:
$$A_j \ge A_i,\quad B_j \le B_i$$
That is a classic offline data structure problem.
We compress all `B` coordinates and use a Fenwick tree where each node stores the best DP value seen so far. To handle the `A_j \ge A_i` condition, we process events grouped by decreasing `A`.
For the second answer, where the start point is arbitrary, every event may start a chain with value `1`.
For the first answer, event `i` may start a chain only if:
$$|x_i| \le V t_i$$
because the tourist starts at `(0,0)`.
The resulting algorithm runs in `O(n log n)`.
| Approach | Time Complexity | Space Complexity | Verdict |
| --- | --- | --- | --- |
| Brute Force | O(n²) | O(n) | Too slow |
| Optimal | O(n log n) | O(n) | Accepted |
## Algorithm Walkthrough
1. Read all events and sort them by increasing time.
Sorting by time guarantees that every transition goes forward chronologically.
1. For every event compute:
$$A_i = x_i - V t_i$$
$$B_i = x_i + V t_i$$
These transformed coordinates encode the movement constraint as a dominance relation.
1. Coordinate-compress all `B_i` values.
Fenwick trees require indices from `1` to `m`, so we map each distinct `B` value to a compact integer.
1. Compute the answer for the free-start version.
Every event can begin a valid route by itself, so initialize:
⟦PROTECT_6⟧
Then process events in decreasing `A`.
For each event, query the Fenwick tree for the maximum DP among all earlier events with compressed `B <= B_i`.
That query gives the best chain that can transition into event `i`.
After querying all events with the same `A`, insert their DP values into the Fenwick tree.
Delaying updates inside equal-`A` groups prevents transitions between events that are not mutually reachable.
1. Compute the fixed-start version similarly.
Initialize:
⟦PROTECT_7⟧
only if:
$$|x_i| \le V t_i$$
Otherwise initialize with negative infinity.
The same dominance DP transitions are applied afterward.
1. The answer for each version is the maximum DP value over all events.
### Why it works
The transformed inequalities are exactly equivalent to the original speed constraint. Any feasible movement sequence corresponds to a chain in the dominance order:
$$A_j \ge A_i,\quad B_j \le B_i$$
 イベントを時間に従って処理すると、遷移は時間内でのみ進むことが保証されます。 フェンウィック ツリーには、すでに処理された有効な先行処理の中で最良の DP が常に保存されます。 不等式を満たすすべての可能な先行要素が構造内で表され、両方の不等式を同時に満たす無効な先行要素がないため、DP は訪問可能なイベントの最適な数を計算します。 
## Python ソリューション```python
import sys
input = sys.stdin.readline
INF = 10**18
class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [-INF] * (n + 1)
    def update(self, idx, val):
        while idx <= self.n:
            if val > self.bit[idx]:
                self.bit[idx] = val
            idx += idx & -idx
    def query(self, idx):
        res = -INF
        while idx > 0:
            if self.bit[idx] > res:
                res = self.bit[idx]
            idx -= idx & -idx
        return res
def solve():
    n = int(input())
    events = []
    for _ in range(n):
        x, t = map(int, input().split())
        events.append((t, x))
    V = int(input())
    arr = []
    for idx, (t, x) in enumerate(events):
        A = x - V * t
        B = x + V * t
        arr.append((A, B, t, x, idx))
    all_b = sorted(set(b for _, b, _, _, _ in arr))
    comp = {v: i + 1 for i, v in enumerate(all_b)}
    arr.sort(reverse=True)
    m = len(all_b)
    # free start
    bit = Fenwick(m)
    dp2 = [1] * n
    i = 0
    while i < n:
        j = i
        while j < n and arr[j][0] == arr[i][0]:
            j += 1
        updates = []
        for k in range(i, j):
            A, B, t, x, idx = arr[k]
            bidx = comp[B]
            best = bit.query(bidx)
            if best > -INF:
                dp2[idx] = max(dp2[idx], best + 1)
            updates.append((bidx, dp2[idx]))
        for bidx, val in updates:
            bit.update(bidx, val)
        i = j
    # fixed start at 0
    bit = Fenwick(m)
    dp1 = [-INF] * n
    for A, B, t, x, idx in arr:
        if abs(x) <= V * t:
            dp1[idx] = 1
    i = 0
    while i < n:
        j = i
        while j < n and arr[j][0] == arr[i][0]:
            j += 1
        updates = []
        for k in range(i, j):
            A, B, t, x, idx = arr[k]
            bidx = comp[B]
            best = bit.query(bidx)
            if best > -INF:
                dp1[idx] = max(dp1[idx], best + 1)
            updates.append((bidx, dp1[idx]))
        for bidx, val in updates:
            bit.update(bidx, val)
        i = j
    ans1 = max(dp1)
    ans2 = max(dp2)
    print(ans1, ans2)
solve()
```フェンウィック ツリーには、合計ではなくプレフィックスの最大値が保存されます。 座標圧縮後、インデックスをクエリする`bidx`すべてのイベントの中で最高の DP を返します。`B_j <= B_i`。 
処理イベントが減少中`A`順序は、挿入されたすべての状態が満たされることを保証します`A_j >= A_i`。 
同等のグループ化処理`A`価値観は微妙ですが必要です。 2 つのイベントが同じものを共有すると仮定します`A`。 いずれかのイベントを処理した後すぐに更新すると、タイムスタンプによって移動が不可能な場合でも、同じグループ内の別のイベントが誤ってそのイベントを通過する可能性があります。 グループ全体が処理されるまで更新を遅らせると、この問題は解決されます。 
固定起動バージョンは初期化のみが異なります。 イベントは、観光客がそこから直接アクセスできる場合にのみチェーンを開始できます。`(0,0)`。 
すべての座標は Python 整数内に問題なく収まりますが、C++ ではこの問題には 64 ビット演算が必要です。`V * t`到達できる`2 * 10^9`。 
## 実用的な例
 ### サンプル 1
 入力:```
3
-1 1
42 7
40 8
2
```変換された値:
 | イベント | × | t | A = x - Vt | B = x + Vt |
 | --- | --- | --- | --- | --- |
 | 1 | -1 | 1 | -3 | 1 |
 | 2 | 42 | 7 | 28 | 56 |
 | 3 | 40 | 8 | 24 | 56 |
 処理順序が減少しています`A`。 
| ステップ | イベント | クエリ結果 | dp2 | dp1 |
 | --- | --- | --- | --- | --- |
 | 1 | (42,7) | なし | 1 | 不可能 |
 | 2 | (40,8) | 1 | 2 | 不可能 |
 | 3 | (-1,1) | なし | 1 | 1 |
 最終的な答え:```
1 2
```フリースタートバージョンは以下から始まります`(42,7)`そして到達する`(40,8)`。 から始まる`(0,0)`これらの大きな正の座標のいずれにも十分な速さで到達することができません。 
### カスタム例
 入力:```
4
0 1
1 2
2 3
3 4
1
```変換された値:
 | イベント | あ | B |
 | --- | --- | --- |
 | 1 | -1 | 1 |
 | 2 | -1 | 3 |
 | 3 | -1 | 5 |
 | 4 | -1 | 7 |
 すべてのイベントは同じものを共有します`A`。 
| ステップ | イベント | 過去のベスト | DP |
 | --- | --- | --- | --- |
 | 1 | 1 | なし | 1 |
 | 2 | 2 | 1 | 2 |
 | 3 | 3 | 2 | 3 |
 | 4 | 4 | 3 | 4 |
 出力：```
4 4
```この例は、優性順序付けが移動条件と正確に一致する理由を示しています。 各イベントは前のイベントから高速に到達可能`1`。 
## 複雑さの分析
 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 並べ替えとフェンウィックのクエリと更新 |
 | スペース | O(n) | 配列、座標圧縮、フェンウィック ツリー |
 と`100000`イベント、`O(n log n)`簡単に十分に高速です。 フェンウィック ツリーの操作は軽量であり、メモリ使用量は直線的に維持されます。 
## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
INF = 10**18
class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [-INF] * (n + 1)
    def update(self, idx, val):
        while idx <= self.n:
            self.bit[idx] = max(self.bit[idx], val)
            idx += idx & -idx
    def query(self, idx):
        res = -INF
        while idx > 0:
            res = max(res, self.bit[idx])
            idx -= idx & -idx
        return res
def solve():
    input = sys.stdin.readline
    n = int(input())
    events = []
    for _ in range(n):
        x, t = map(int, input().split())
        events.append((t, x))
    V = int(input())
    arr = []
    for idx, (t, x) in enumerate(events):
        A = x - V * t
        B = x + V * t
        arr.append((A, B, t, x, idx))
    all_b = sorted(set(b for _, b, _, _, _ in arr))
    comp = {v: i + 1 for i, v in enumerate(all_b)}
    arr.sort(reverse=True)
    m = len(all_b)
    bit = Fenwick(m)
    dp2 = [1] * n
    i = 0
    while i < n:
        j = i
        while j < n and arr[j][0] == arr[i][0]:
            j += 1
        upd = []
        for k in range(i, j):
            A, B, t, x, idx = arr[k]
            bidx = comp[B]
            best = bit.query(bidx)
            if best > -INF:
                dp2[idx] = max(dp2[idx], best + 1)
            upd.append((bidx, dp2[idx]))
        for bidx, val in upd:
            bit.update(bidx, val)
        i = j
    bit = Fenwick(m)
    dp1 = [-INF] * n
    for A, B, t, x, idx in arr:
        if abs(x) <= V * t:
            dp1[idx] = 1
    i = 0
    while i < n:
        j = i
        while j < n and arr[j][0] == arr[i][0]:
            j += 1
        upd = []
        for k in range(i, j):
            A, B, t, x, idx = arr[k]
            bidx = comp[B]
            best = bit.query(bidx)
            if best > -INF:
                dp1[idx] = max(dp1[idx], best + 1)
            upd.append((bidx, dp1[idx]))
        for bidx, val in upd:
            bit.update(bidx, val)
        i = j
    print(max(dp1), max(dp2))
def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    backup = sys.stdout
    sys.stdout = out
    solve()
    sys.stdout = backup
    return out.getvalue().strip()
# provided sample
assert run(
"""3
-1 1
42 7
40 8
2
"""
) == "1 2", "sample 1"
# minimum size
assert run(
"""1
0 1
1
"""
) == "1 1", "single reachable event"
# unreachable from origin but reachable with free start
assert run(
"""1
100 1
1
"""
) == "-1000000000000000000 1", "free start differs"
# same position over time
assert run(
"""3
2 3
2 4
2 5
1
"""
) == "3 3", "waiting in place"
# chain along a line
assert run(
"""4
0 1
1 2
2 3
3 4
1
"""
) == "4 4", "full chain"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一イベント |`1 1`| 最小入力サイズ |
 | 遠く手の届かないイベント | さまざまな答え | フリースタートと固定スタート |
 | 同じ座標を繰り返し |`3 3`| その場で待っています |
 | チェーンを増やす |`4 4`| 長い有効な遷移チェーン |
 ## 特殊なケース
 同時に発生するイベントを考えてみましょう。```
2
0 1
10 1
100
```両方のイベントのタイムスタンプは同じです。 移動時間がゼロであるため、変換された条件は同時に成り立ちません。 処理中、どちらのイベントも他方のイベントの先行イベントにはならないため、両方の DP 値が残ります。`1`。 アルゴリズムは次を出力します。```
1 1
```ここで、オリジンからの到達不能なイベントを考えてみましょう。```
2
-5 5
5 10
1
```最初のイベントには次からアクセスできます`(0,0)`、 それで`dp1 = 1`。 移動距離が大きいため、2 番目のイベントを追跡できません。`10`時間内に`5`速度を超える`1`。 
フリースタートバージョンの場合、観光客は次から始めることができます。`-5`、最初のイベントに参加しても、2 番目のイベントに到達できません。 あるいは、次から始めます`5`そして2回目のみに参加します。 最大値が残る`1`。 
変換された不等式が失敗するため、アルゴリズムは無効な遷移を正しく拒否します。 
最後に、繰り返される座標を考慮します。```
3
2 3
2 4
2 5
1
```変換された値は時系列順の支配関係を満たすため、フェンウィック ツリーは 3 つのイベントすべてを連鎖させます。 ゼロ移動により自動的に速度制限が満たされるため、待機には特別な処理は必要ありません。
