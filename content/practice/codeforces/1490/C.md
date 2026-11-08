---
 タイトル: 「CF 1490C - 立方体の合計」
 説明: 「いくつかの数字が与えられ、それぞれの数字について$x$、2 つの正の立方体の和として記述できるかどうかを判断する必要があります。 言い換えれば、正の整数が存在するかどうかを知りたいのです。$a$そして$b$そのような$$a^3 + b^3 = x." date: "2026-06-10T22:37:19+07:00" tags: ["codeforces", "competitive-programming", "binary-search", "brute-force", "math"] categories: ["algorithms"] codeforces_contest: 1490 codeforces_index: "C" codeforces_contest_name: "Codeforces Round 702 (Div. 3)" rating: 1100 weight: 1490 solve_time_s: 150 verified: true draft: false --- [CF 1490C - Sum of Cubes](https://codeforces.com/problemset/problem/1490/C) **Rating:** 1100   **Tags:** binary search, brute force, math   **Solve time:** 2m 30s   **Verified:** yes   ## Solution ## Problem Understanding We are given several numbers, and for each number $x$, we must determine whether it can be written as the sum of two positive cubes. In other words, we want to know whether there exist positive integers $a$ and $b$ such that$$a^3 + b^3 = x。$$
The answer for each test case is simply "YES" if such a pair exists and "NO" otherwise.
The largest possible value of $x$ is $10^{12}$. That bound immediately tells us something useful about the size of the search space. Since
$$10000^3 = 10^{12}、$$
any cube participating in the sum must come from an integer no larger than about 10,000.
A naive search over all pairs $(a,b)$ would require checking roughly $10^4 \times 10^4 = 10^8$ combinations for a single test case. With up to 100 test cases, that becomes completely impractical.
The constraints suggest that an algorithm around $10^4$ operations per test case is perfectly reasonable, while $10^8$ operations per test case is not.
There are a few easy-to-miss edge cases.
Consider:
⟦PROTECT_0⟧
The correct answer is:
⟦PROTECT_1⟧
Although $1 = 1^3$, the problem requires a sum of two positive cubes. The smallest possible sum is $1^3 + 1^3 = 2$.
Consider:
⟦PROTECT_2⟧
The correct answer is:
⟦PROTECT_3⟧
because $16 = 2^3 + 2^3$. A careless implementation that only checks distinct values of $a$ and $b$ would incorrectly reject this case.
Consider:
⟦PROTECT_4⟧
The correct answer is:
⟦PROTECT_5⟧
because $2 = 1^3 + 1^3$. This is the smallest valid representation and is a useful boundary test.
Another common mistake is using floating-point cube roots. For values near $10^{12}$, rounding errors can cause an exact cube to be missed. Integer arithmetic avoids that problem entirely.
## Approaches
The most direct solution is to try every possible pair $(a,b)$. Since every relevant cube root is at most about 10,000, we could iterate through all values of $a$ and $b$, compute $a^3+b^3$, and check whether it equals $x$.
This approach is correct because it explicitly examines every candidate pair. The problem is the running time. Roughly $10^8$ pairs must be checked for a single test case, which is far beyond what fits within the time limit.
The key observation is that once we choose one cube, the other cube is completely determined.
Suppose we fix $a$. Then we need
$$b^3 = x - a^3.$$
 あらゆる可能性を試す代わりに$b$、値が次の値であるかどうかを判断するだけで済みます。$x-a^3$それ自体は完全な立方体です。 
可能なキューブの値は約 10,000 個しかないため、次の値までのすべてのキューブを事前計算できます。$10^{12}$そしてそれらをハッシュセットに保存します。 次に、すべての候補者について$a$、計算します$x-a^3$そして実行します$O(1)$セット内のメンバーシップテスト。 
これにより、検索がすべてのペアのチェックから、考えられるすべての最初の要素のみのチェックに減ります。 
| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(M^2)$|$O(1)$| 遅すぎる |
 | 最適 |$O(M)$|$O(M)$| 承認済み |
 ここ$M \approx 10^4$、可能な最大の立方根。 
## アルゴリズムのチュートリアル
 1. すべてのキューブを事前計算する$i^3$正の整数の場合$i$その間$i^3 \le 10^{12}$。 
2. これらのキューブをハッシュ セットに保存します。 これにより、数値が事前に計算された範囲から完全な立方体であるかどうかを定数でチェックできます。 
3. 各テスト ケースについて、次の内容を読んでください。$x$。 
4. 事前に計算されたすべてのキューブ値を反復処理します。$c=a^3$。 
5. 残りの値を計算します$x-c$。 
6.次のことを確認します。$x-c$キューブ セット内に存在します。 
7. 存在する場合、$x=a^3+b^3$何らかの正の整数の場合$b$ので、出力します`"YES"`このテスト ケースの処理を停止します。 
8. 一致するものが見つからずにループが終了した場合、出力`"NO"`。 
### なぜ効果があるのか
 アルゴリズムは、最初のキューブのすべての可能な値をチェックします。$a^3$。 有効な表現の場合$x=a^3+b^3$、反復は最終的にその特定に到達します$a^3$。 この時点で、計算された剰余は次のようになります。$b^3$、これはキューブ セット内に存在するため、アルゴリズムは次のように答えます。`"YES"`。 
逆に、アルゴリズムが次のことを検出すると、$x-a^3$キューブ セットに属しており、正の整数が存在します$b$その立方体がその余りに等しい。 方程式$x=a^3+b^3$それで満足なので、答えます`"YES"`は正しいです。 
考えられるすべての最初のキューブが検査され、メンバーシップ テストで事前計算されたキューブが正確に認識されるため、有効な表現が見逃されることはなく、無効な表現が受け入れられることもありません。 
## Python ソリューション```python
import sys
input = sys.stdin.readline
LIMIT = 10 ** 12
cubes = []
cube_set = set()
i = 1
while i ** 3 <= LIMIT:
    c = i ** 3
    cubes.append(c)
    cube_set.add(c)
    i += 1
t = int(input())
for _ in range(t):
    x = int(input())
    found = False
    for c in cubes:
        if c >= x:
            break
        if x - c in cube_set:
            found = True
            break
    print("YES" if found else "NO")
```最初のセクションでは、次のすべての立方体を事前計算します。$10^{12}$。 それらは 10,000 個しかないため、メモリ使用量も前処理時間もわずかです。 
ハッシュ セットは重要なデータ構造です。 それがなければ、$x-c$立方体の場合は別の検索が必要となり、解決が遅くなります。 
各テスト ケース内で、考えられるすべての最初のキューブを反復処理します。 それぞれについて、到達するために必要な残りの金額を計算します。$x$。 その残りがキューブ セット内にある場合、有効な分解が見つかったことになります。 
条件`if c >= x: break`小さな最適化です。 現在のキューブが少なくとも$x$、後のすべての立方体はさらに大きいため、剰余はゼロまたは負になります。 両方の整数が正でなければならないため、その点を超えると解は現れません。 
Python の整数は 32 ビットより大きい値を自動的に処理するため、立方体が近づいてもオーバーフローの心配はありません$10^{12}$。 
## 実用的な例
 ### 例 1
 入力:```
35
```| 現在のキューブ$c$| 残り$35-c$| キューブセットで？ |
 | --- | --- | --- |
 | 1 | 34 | いいえ |
 | 8 | 27 | はい |
 アルゴリズムが到達する$c=8=2^3$。 余りは ​​27 で、次と同じになります。$3^3$。 27 は立方体セットに含まれるため、答えは次のようになります。`"YES"`。 
この例では、単一のキューブをチェックして、唯一の可能なパートナー キューブを自動的に決定する方法を示します。 
### 例 2
 入力:```
34
```| 現在のキューブ$c$| 残り$34-c$| キューブセットで？ |
 | --- | --- | --- |
 | 1 | 33 | いいえ |
 | 8 | 26 | いいえ |
 | 27 | 7 | いいえ |
 次の立方体は 64 となり、34 を超えるため、ループは停止します。 
キューブセットには余りが見つからなかったので、答えは次のようになります。`"NO"`。 
この例は、すべての候補の最初のキューブがチェックされますが、残りとして別のキューブを生成するものがないことを示しています。 
## 複雑さの分析
 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(M)$テストケースごと | すべての事前計算されたキューブを反復処理します。 
| スペース |$O(M)$| キューブとハッシュ セットを保存する |
 ここ$M \approx 10^4$、 なぜなら$10000^3 = 10^{12}$。 
各テスト ケースでは最大 10,000 個のキューブ値が検査されます。 テスト ケースが 100 個あったとしても、総作業量はわずか約 100 万回の反復であり、制限内に問題なく収まります。 
## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline
    LIMIT = 10 ** 12
    cubes = []
    cube_set = set()
    i = 1
    while i ** 3 <= LIMIT:
        c = i ** 3
        cubes.append(c)
        cube_set.add(c)
        i += 1
    t = int(input())
    ans = []
    for _ in range(t):
        x = int(input())
        found = False
        for c in cubes:
            if c >= x:
                break
            if x - c in cube_set:
                found = True
                break
        ans.append("YES" if found else "NO")
    return "\n".join(ans) + "\n"
# provided sample
assert run(
"""7
1
2
4
34
35
16
703657519796
"""
) == """NO
YES
NO
NO
YES
YES
YES
"""
# minimum value
assert run(
"""1
1
"""
) == """NO
"""
# smallest valid representation
assert run(
"""1
2
"""
) == """YES
"""
# equal cubes
assert run(
"""1
16
"""
) == """YES
"""
# large boundary cube sum
assert run(
"""1
2000000000000
"""
) == """YES
"""
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1`|`NO`| 可能な最小値 |
 |`2`|`YES`| 最小の有効な分解 |
 |`16`|`YES`| 両方の立方体が等しい場合 |
 |`2000000000000`|`YES`| 立方根境界付近の大きな値 |
 ## 特殊なケース
 次の入力を考えてみましょう。```
1
1
```アルゴリズムは最小の立方体から始まり、$1$。 以来$1 \ge x$、ループはすぐに停止します。 両方の立方体が正でなければならないため、有効なペアは存在しません。 出力は`"NO"`。 
次の入力を考えてみましょう。```
1
2
```最初の立方体は、$1$。 残りは$2-1=1$。 1 がキューブ セットに存在するため、アルゴリズムは次の表現を見つけます。$1^3+1^3$そして出力`"YES"`。 
次の入力を考えてみましょう。```
1
16
```アルゴリズムチェック$1$、 それから$8$。 のために$c=8$、残りも$8$。 キューブ セットには 8 が含まれているため、アルゴリズムは分解を受け入れます。$2^3+2^3$。 これにより、等しい立方体が自然に処理されることが確認されます。 
次の入力を考えてみましょう。```
1
34
```このアルゴリズムは、34 未満の最初の立方体をすべて調べます。残りは 33、26、7 ですが、どれも立方体ではありません。 検索は終了し、答えは`"NO"`。 これは、有効な 3 乗和に近いが実際には表現できない数値をアルゴリズムが誤って受け入れないことを検証します。
