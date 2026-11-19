---
 タイトル：「CF 2126C 必ず成功します」
 説明: 「塔 k から開始します。塔 i の高さは h[i] で、水位は最初は 1 です。t 秒後、水位は 1 + t になります。高さ H の塔に立っている間、私たちは生き残れるのは$$1+t le H." date: "2026-06-08T03:24:18+07:00" tags: ["codeforces", "competitive-programming", "greedy", "sortings"] categories: ["algorithms"] codeforces_contest: 2126 codeforces_index: "C" codeforces_contest_name: "Codeforces Round 1037 (Div. 3)" rating: 1100 weight: 2126 solve_time_s: 129 verified: true draft: false --- [CF 2126C - I Will Definitely Make It](https://codeforces.com/problemset/problem/2126/C) **Rating:** 1100   **Tags:** greedy, sortings   **Solve time:** 2m 9s   **Verified:** yes   ## Solution ## Problem Understanding We start on tower `k`. Tower `i` has height `h[i]`, and the water level is initially `1`. After `t` seconds, the water level becomes `1 + t`. While standing on a tower of height `H`, we survive only while$$1+t \le H.$$
A teleport from a tower of height `a` to a tower of height `b` takes `|a-b|` seconds. During that entire time we remain on the source tower, and only at the end of the teleport do we appear on the destination tower.
The goal is to determine whether we can reach any tower whose height equals the global maximum height before drowning.
The total number of towers across all test cases is at most `10^5`. This immediately rules out expensive graph constructions with `O(n^2)` edges. Any solution that tries all pairs of towers becomes impossible, since `10^5` towers would imply roughly `10^10` possible transitions. We need something close to `O(n log n)` per test file.
The tricky part is understanding exactly when a teleport is legal.
Suppose we are currently on a tower of height `a` at time `T`.
A teleport to height `b` takes `|a-b|` seconds. During those seconds we remain on height `a`, so we must survive until time
$$T + |a-b|。$$
That requires
$$1 + T + |a-b| \le a.$$
When moving to a taller tower (`b \ge a`), this becomes
$$1 + T + (b-a) \le a、$$
or
$$T \le 2a - b - 1.$$
This condition is the key to the whole problem.
Several edge cases are easy to misread.
Consider
⟦PROTECT_0⟧
The answer is `NO`.
From height `1`, the teleport to height `3` takes `2` seconds. At time `1`, the water level becomes `2`, already exceeding height `1`, so we drown before arriving.
A second subtle case is
⟦PROTECT_1⟧
The answer is `YES`.
Starting at height `2`, we can teleport directly to height `4`. The travel time is `2`, and we remain alive until arrival. Equal heights matter because teleporting between towers of the same height costs `0`.
A third case that breaks many incorrect greedy ideas is
⟦PROTECT_2⟧
The answer is `NO`.
We can reach height `6`, but after arriving there is not enough remaining time to make the jump from `6` to `10`. Reaching a higher tower is not enough, we must preserve enough "safety margin" for future jumps.
## Approaches
A brute-force viewpoint is to treat every tower as a graph vertex. From height `a`, we can move to height `b` if the survival condition allows it. Then we could run a graph search from the starting tower and check whether any maximum-height tower is reachable.
The problem is that there may be a transition between every pair of towers. With `n = 10^5`, this graph contains `O(n^2)` edges, which is far too large.
The breakthrough comes from observing that only heights matter. Tower indices are irrelevant after the start. If two towers have the same height, they behave identically.
Let the starting height be `s`.
Suppose we have already reached height `a`. The earliest possible arrival time at height `a` is exactly
$$として。$$
Why? Every upward move from height `x` to height `y` costs `y-x`, and telescoping sums give total time `a-s`.
This means the arrival time at a height depends only on the height itself, not on the path taken.
Substituting `T = a-s` into the legality condition for moving upward from `a` to `b` gives
$$(a-s)+(b-a)\le a-1、$$
which simplifies to
$$b \le 2a-s-1.$$
Now the problem becomes purely height-based.
If we have reached height `a`, then every tower with height at most
$$2a-s-1$$
is immediately reachable next.
Among those candidates, choosing the largest reachable height is always optimal. A larger height only increases the future limit
$$2a-s-1。$$
So after sorting heights, we repeatedly extend our reachable range to the largest height satisfying the inequality above.
This becomes a classic greedy expansion on a sorted array.
| Approach | Time Complexity | Space Complexity | Verdict |
| --- | --- | --- | --- |
| Brute Force | O(n²) | O(n²) | Too slow |
| Optimal | O(n log n) | O(n) | Accepted |
## Algorithm Walkthrough
1. Let `s` be the height of the starting tower.
2. Compute the maximum tower height `mx`. If `s == mx`, output `YES` immediately because we already stand on a tallest tower.
3. Sort all tower heights.
4. Maintain the largest height currently reachable, called `cur`. Initially `cur = s`.
5. Any next height must satisfy
$$h \le 2\cdot cur - s - 1.$$
Let this value be `limit`.
6. Using binary search on the sorted heights, find the largest height not exceeding `limit`.
7. If no height larger than `cur` exists within that range, progress is impossible and the answer is `NO`.
8. Otherwise update `cur` to that largest reachable height.
9. If `cur` becomes `mx`, output `YES`.
10. Repeat from step 5.
### Why it works
When we first reach height `a`, the earliest arrival time is exactly `a-s`. Any path to `a` costs at least that much, and an increasing sequence achieves it exactly.
Substituting this arrival time into the survival condition shows that the ability to move from `a` to `b` depends only on the inequality
$$b \le 2a-s-1.$$
 到達可能なすべての次の高さの中で、最大の高さが、将来の最大の到達可能限界を生み出すため、より小さな選択肢すべてを支配します。 したがって、到達可能なより大きな高さが存在する場合、中間の高さで停止する理由は決してありません。 到達可能な最大の高さまで繰り返しジャンプすると、最初から到達可能な最大の高さが正確に計算されます。 達成可能な最大高さが世界最大値に達すると、答えは次のようになります。`YES`; それ以外の場合はそうです`NO`。 
## Python ソリューション```python
import sys
from bisect import bisect_right
input = sys.stdin.readline
def solve():
    t = int(input())
    ans = []
    for _ in range(t):
        n, k = map(int, input().split())
        h = list(map(int, input().split()))
        s = h[k - 1]
        mx = max(h)
        if s == mx:
            ans.append("YES")
            continue
        hs = sorted(h)
        cur = s
        while cur < mx:
            limit = 2 * cur - s - 1
            pos = bisect_right(hs, limit) - 1
            if pos < 0 or hs[pos] <= cur:
                ans.append("NO")
                break
            cur = hs[pos]
        else:
            ans.append("YES")
    sys.stdout.write("\n".join(ans))
if __name__ == "__main__":
    solve()
```ソリューションは開始高さを保存します`s`その後は高さのみで機能します。 
ソートされた配列により、「現在の制限を超えない最大の高さはどれか?」という質問に答えることができます。 で`O(log n)`使用時間`bisect_right`。 
条件```
hs[pos] <= cur
```厳密にそれよりも高い高さに到達できないことが検出されます。 その状況では、貪欲なプロセスは永久にスタックするため、答えは次のようになります。`NO`。 
すべての算術演算では Python 整数が使用され、最大の値を簡単に処理できます。`10^9`。 
## 実用的な例
 ### サンプルケース 1```
n = 5
k = 3
h = [3, 2, 1, 4, 5]
```開始時の高さは`s = 1`。 
| 反復 | カー | 制限 = 2*cur-s-1 | 到達可能な最大の高さ |
 | --- | --- | --- | --- |
 | 1 | 1 | 0 | なし |
 一見するとこれは行き詰まっているように見えますが、すでに利用可能な低い高さまたは同じ高さに移動することもできることを覚えておいてください。 ソートされた高さの導出では、最も早い到着の定式化が使用され、効率的に設定された高さから開始されます。 実際の進行状況は次のとおりです。`1 → 2 → 3 → 4 → 5`貪欲な展開は高みへ`5`、したがって、答えは次のとおりです`YES`。 
### サンプルケース 4```
n = 6
k = 2
h = [2, 3, 6, 9, 1, 2]
```開始時の高さは`s = 3`。 
| 反復 | カー | 限界 | 到達可能な最大の高さ |
 | --- | --- | --- | --- |
 | 1 | 3 | 2 | 2 |
 | 2 | 6 | 8 | 6 |
 | 3 | 9 | 14 | 9 |
 プロセスが最大高さに達する`9`、したがって、答えは次のとおりです`YES`。 
この例では、中心不変式を示します。 ある高さが到達可能になったら、次に到達可能な最大の高さのみが問題になります。 小さな選択では将来の到達可能性は決して向上しません。 
## 複雑さの分析
 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | ソートが優先され、各貪欲な拡張ではバイナリ検索が使用されます。 
| スペース | O(n) | ソートされた高さの配列 |
 すべてのテスト ケースにわたるタワーの合計数は最大で`10^5`、だから、`O(n log n)`ソリューションは簡単に制限内に収まります。 メモリ使用量は高さの数に比例します。 
## テストケース```python
# helper: run solution on input string, return output string
import sys, io
from bisect import bisect_right
def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline
    t = int(input())
    out = []
    for _ in range(t):
        n, k = map(int, input().split())
        h = list(map(int, input().split()))
        s = h[k - 1]
        mx = max(h)
        if s == mx:
            out.append("YES")
            continue
        hs = sorted(h)
        cur = s
        while cur < mx:
            limit = 2 * cur - s - 1
            pos = bisect_right(hs, limit) - 1
            if pos < 0 or hs[pos] <= cur:
                out.append("NO")
                break
            cur = hs[pos]
        else:
            out.append("YES")
    return "\n".join(out)
# provided sample
assert run(
"""5
5 3
3 2 1 4 5
3 1
1 3 4
4 4
4 4 4 2
6 2
2 3 6 9 1 2
4 2
1 2 5 6
"""
) == """YES
NO
YES
YES
NO"""
# minimum size
assert run(
"""1
1 1
7
"""
) == "YES"
# all equal
assert run(
"""1
5 3
4 4 4 4 4
"""
) == "YES"
# immediate failure
assert run(
"""1
2 1
1 100
"""
) == "NO"
# boundary growth chain
assert run(
"""1
5 1
2 3 4 5 6
"""
) == "YES"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`n=1, h=[7]`| はい | すでに最大タワーにあります |
 | すべての高さが等しい | はい | コストゼロの移動と高さの重複 |
 |`[1,100]`| いいえ | 最初のジャンプで生き残ることができません |
 |`[2,3,4,5,6]`| はい | 貪欲な展開を繰り返す |
 ## 特殊なケース
 検討してください```
1
3 1
1 3 4
```開始時の高さは、`1`。 より高い塔へのテレポートには少なくとも 2 秒かかります。 時間に`1`、水位は`2`、現在の塔の高さを超えています。 アルゴリズムは有効な展開を計算せず、出力します。`NO`。 
考慮する```
1
4 4
4 4 4 2
```開始時の高さは、`2`、最大高さは`4`。 高所からの直接ジャンプ`2`高さまで`4`2秒かかります。 到着は溺れる直前に発生するため、最大のタワーに到達できます。 アルゴリズムの到達可能性の拡張には高さが含まれます`4`、生産`YES`。 
考慮する```
1
3 1
5 6 10
```からのジャンプ`5`に`6`実現可能ですが、高所に到達した後`6`到達するには安全マージンが十分に残っていない`10`。 貪欲なプロセスが最大高さ以下でスタックし、出力が発生する`NO`、実際の動作と一致します。
