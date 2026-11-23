---
title: "CF 444C - DZY は色が大好き"
description: "位置 1...n で構成されるリボンがあります。 最初は、位置 i には色 i があるため、すべての位置は個別の色で始まります。 2 種類の操作が実行されます。 ペイント操作は、セグメント [l, r] 内のすべての位置に新しいカラー x を割り当てます。"
date: "2026-06-07T16:00:27+07:00"
tags: ["codeforces", "competitive-programming", "data-structures"]
categories: ["algorithms"]
codeforces_contest: 444
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 254 (Div. 1)"
rating: 2400
weight: 444
solve_time_s: 319
verified: false
draft: false
---

[CF 444C - DZY は色が大好き](https://codeforces.com/problemset/problem/444/C)

 **評価:** 2400
 **タグ:** データ構造
 **解決時間:** 5 分 19 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 ポジションで構成されるリボンがあります`1...n`。 まずはポジション`i`色がある`i`したがって、すべての位置は個別の色で始まります。 

2 種類の操作が実行されます。 

ペイント操作により新しい色が割り当てられます`x`セグメント内のすべての位置に`[l, r]`。 現在色が付いている位置の場合`y`に再塗装されます`x`、その蓄積されたカラフルさは次のように増加します`|x - y|`。 

クエリ操作では、セグメント全体の全体的なカラフルさを要求します。`[l, r]`。 

課題は、リボンの長さと操作の数の両方が次の条件に達するかどうかです。`10^5`。 すべての更新内のすべての位置を再描画する簡単なシミュレーションには、最大で`10^10`最悪の場合には位置が更新されますが、これは 2 秒の制限内ではまったく実行不可能です。 

最初の観察は、色は何度も変化しますが、ペイント操作のたびに間隔全体が単一の色になるということです。 リボンは、同じ現在の色を持つ最大のセグメントに自然に分解されます。 個々の位置ではなくこれらのセグメントを直接維持できれば、操作ごとの変更の数ははるかに少なくなります。 

もう 1 つの微妙な側面は、カラフルさが永続的に蓄積されることです。 位置を再描画しても、以前のコントリビューションは上書きされません。 例えば：```
n = 1

paint [1,1] color 5
paint [1,1] color 2
query [1,1]
```答えは```
|5-1| + |2-5| = 4 + 3 = 7
```現在の色のみを保存する不注意な実装では、以前の寄与が失われます。 

2 番目の簡単な間違いは、同じ色で再ペイントするときに発生します。```
n = 3

paint [1,3] color 10
paint [1,3] color 10
query [1,3]
```すべての位置にすでに色があるため、2 番目の更新はゼロに寄与します。`10`。 2 回目の更新後も、答えは同じでなければなりません。 

バグの 3 番目の原因は、既存のカラー セグメントとの部分的な重複です。```
n = 5

paint [2,4] color 7
```最初にカラーセグメントは次のようになります。```
[1]=1 [2]=2 [3]=3 [4]=4 [5]=5
```このアップデートは 3 つの既存の色と交差します。 カラフルさへの貢献は、```
|7-2| + |7-3| + |7-4|
```それぞれ位置 2、3、4 に対応します。 どのセグメント構造も、最初に更新範囲の境界で分割する必要があります。分割しないと、セグメントの長さと寄与が不正確になります。 

## アプローチ

 ブルートフォース手法では、現在の色とすべての位置の累積されたカラフルさが保存されます。 

アップデートの場合`[l,r] -> x`、範囲内のすべての位置を反復処理し、追加します`|current_color - x|`そのカラフルさに合わせて、その色を次のように置き換えます。`x`。 

クエリの場合、要求された間隔にわたるカラフルさの値を合計します。 

この方法は定義に直接従っているため、明らかに正しいです。 残念ながら、1 回のアップデートで影響を受ける可能性があります。`O(n)`ポジション。 と`10^5`長さのリボンのアップデート`10^5`、最悪の場合の仕事は`10^10`操作。 

重要な観察は、ペイント操作が単色セグメント全体にまったく同じように影響を与えるということです。 

位置を仮定する`[L,R]`現在はすべて色がついています`c`そして色で再ペイントします`x`。 

どのポジションも同じ量を獲得します。```
|x-c|
```各位置を個別に更新する代わりに、この値を間隔データ構造に追加できます。`[L,R]`。 

これは、問題を 2 つの独立した部分に分割することを示唆しています。 

最初の部分では、現在の色を追跡します。 そのために、最大単色間隔の順序付けされたセットを維持します。 範囲を再描画するには、その範囲と交差する間隔を調べるだけで済みます。 

2 番目のパートでは、蓄積されたカラフルさを追跡します。 一定の長さの間隔ごとに`k`そして色`c`に再塗装されます`x`、その間隔内のすべての位置が追加の増分を受け取ります`|x-c|`。 これは単なる範囲追加操作です。 クエリでは範囲の合計が求められます。 遅延伝播を備えたセグメント ツリーは、両方の操作を次のように処理します。`O(log n)`。 

設定された間隔により、どの位置がどの増分を受け取るかがわかります。 セグメント ツリーには、累積された色彩値が格納されます。 

各再描画操作では、間隔の境界が次のように分割されます。`l`そして`r+1`、影響を受けるすべての単色区間を訪問し、対応する範囲の追加をセグメント ツリーに適用し、それらの区間を削除し、最後に 1 つの新しい色付き区間を挿入します。`x`。 

古典的な Ordered-Disjoint-Interval-tree 引数は、各間隔の作成と破棄の回数が限られていることを示しています。 すべての操作にわたって、インターバル訪問の合計数は管理可能なままです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nm) | O(n) | 遅すぎる |
 | 最適 | O((m + K) log n) ここで、K は合計間隔分割/訪問数です。 O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 現在の色を表す間隔の順序付きセットを構築します。 

最初はすべての位置に固有の色があるため、間隔を作成します。```
[1,1] color 1
[2,2] color 2
...
[n,n] color n
```2. カラフルさの値を格納する遅延セグメント ツリーを構築します。 

すべての値はゼロから始まります。 
3. 更新を処理する前に、間隔構造を次の位置で分割します。`l`。 

何かの間隔を越えたら`l`、これを 2 つの間隔に置き換えて、1 つの間隔が正確に開始されるようにします。`l`。 
4. 位置で再度分割します`r+1`。 

このステップの後、すべての間隔が完全に内側になります`[l,r]`セット内の明示的な間隔になります。 
5. に完全に含まれるすべての間隔を繰り返します。`[l,r]`。 

間隔が`[L,R]`現在の色で`c`。 

そのインターバル内のすべてのポジションは以下を獲得します。```
|x-c|
```セグメントツリー範囲の加算を適用します。`|x-c|`の上`[L,R]`。 
6. に含まれるすべての間隔を削除します。`[l,r]`。 

再描画操作によってそれらの色が上書きされるため、それらの色は関係なくなります。 
7. 新しい間隔を 1 つ挿入します`[l,r]`色付き`x`。 

この間隔は、再描画された領域全体を表します。 
8. クエリ操作の場合は、セグメント ツリーに範囲の合計を求めるだけです。`[l,r]`。 

セグメント ツリーには、これまでに適用されたすべてのカラフルさの増分がすでに含まれています。 

### なぜ効果があるのか

 間隔セットは常に、リボンの正確な現在の色を、切り離された単色間隔へのパーティションとして表します。 

で分割した後、`l`そして`r+1`、内部のすべての間隔`[l,r]`現在の色は 1 つだけです。 そんな区間を色から塗り直す`c`色を付ける`x`その間隔内のすべての位置を正確に増加させます`|x-c|`これはまさにセグメント ツリーに適用される更新です。 

セグメント ツリーには、時間の経過とともに蓄積されたすべての再描画寄与の合計が保存されます。 すべての再描画寄与は影響を受けるすべての位置に一度追加され、決して削除されないため、範囲合計クエリは要求された合計のカラフルさを正確に返します。 

したがって、インターバル構造は正しい現在の色を維持し、セグメント ツリーは正しい累積された色を維持し、すべての操作を正確にシミュレートします。 

## Python ソリューション```python
import sys
from bisect import bisect_left
input = sys.stdin.readline

class SegTree:
    def __init__(self, n):
        self.n = n
        self.sum = [0] * (4 * n)
        self.lazy = [0] * (4 * n)

    def _push(self, p, l, r):
        if self.lazy[p] == 0:
            return

        mid = (l + r) >> 1
        v = self.lazy[p]

        self.sum[p * 2] += v * (mid - l + 1)
        self.sum[p * 2 + 1] += v * (r - mid)

        self.lazy[p * 2] += v
        self.lazy[p * 2 + 1] += v

        self.lazy[p] = 0

    def _add(self, p, l, r, ql, qr, val):
        if ql <= l and r <= qr:
            self.sum[p] += val * (r - l + 1)
            self.lazy[p] += val
            return

        self._push(p, l, r)

        mid = (l + r) >> 1

        if ql <= mid:
            self._add(p * 2, l, mid, ql, qr, val)
        if qr > mid:
            self._add(p * 2 + 1, mid + 1, r, ql, qr, val)

        self.sum[p] = self.sum[p * 2] + self.sum[p * 2 + 1]

    def add(self, l, r, val):
        self._add(1, 1, self.n, l, r, val)

    def _query(self, p, l, r, ql, qr):
        if ql <= l and r <= qr:
            return self.sum[p]

        self._push(p, l, r)

        mid = (l + r) >> 1
        ans = 0

        if ql <= mid:
            ans += self._query(p * 2, l, mid, ql, qr)
        if qr > mid:
            ans += self._query(p * 2 + 1, mid + 1, r, ql, qr)

        return ans

    def query(self, l, r):
        return self._query(1, 1, self.n, l, r)

def solve():
    n, m = map(int, input().split())

    seg = SegTree(n)

    starts = list(range(1, n + 1))
    color = {i: i for i in range(1, n + 1)}
    right = {i: i for i in range(1, n + 1)}

    def split(pos):
        if pos > n:
            return len(starts)

        idx = bisect_left(starts, pos)

        if idx < len(starts) and starts[idx] == pos:
            return idx

        idx -= 1
        s = starts[idx]
        e = right[s]
        c = color[s]

        starts.insert(idx + 1, pos)

        right[s] = pos - 1
        right[pos] = e

        color[pos] = c

        return idx + 1

    out = []

    for _ in range(m):
        op = list(map(int, input().split()))

        if op[0] == 1:
            _, l, r, x = op

            ir = split(r + 1)
            il = split(l)

            affected = starts[il:ir]

            for s in affected:
                e = right[s]
                c = color[s]
                seg.add(s, e, abs(x - c))

            for s in affected:
                del color[s]
                del right[s]

            starts[il:ir] = [l]
            color[l] = x
            right[l] = r

        else:
            _, l, r = op
            out.append(str(seg.query(l, r)))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```セグメント ツリーは、カラフルさの値のみを担当します。 すべての再描画は範囲追加操作に変換され、すべてのクエリは範囲合計リクエストになります。 

間隔構造には現在の色が保存されます。 各間隔は、その左の端点によって識別されます。 の`starts`リストはソートされたままですが、辞書には間隔の正しいエンドポイントと色が保存されます。 

の`split(pos)`ルーチンは重要な作業です。 間隔を越えたら`pos`、同じ色の 2 つの区間に分割されます。 両方の更新境界で分割した後、再描画範囲内のすべての間隔を個別に処理できます。 

実装の微妙な詳細は、2 つの分割の順序です。 で分かれました`r + 1`で分割する前に`l`。 これは標準の ODT 実装を反映しており、後で処理するイテレータ範囲の無効化を回避します。 

もう 1 つの詳細は、答えが 32 ビットの制限を超える可能性があることです。 すべてのセグメント ツリー値は Python 整数に保存され、任意の大きな値を自動的に処理します。 

## 実用的な例

 ### 例 1

 入力:```
3 3
1 1 2 4
1 2 3 5
2 1 3
```各操作後の状態:

 | 操作 | 操作後の間隔 | 貢献を追加しました |
 | --- | --- | --- |
 | イニシャル | [1,1]:1 [2,2]:2 [3,3]:3 | 0 |
 | ペイント [1,2] -> 4 | [1,2]:4 [3,3]:3 | pos1 に 3、pos2 に 2 |
 | ペイント [2,3] -> 5 | [1,1]:4 [2,3]:5 | pos2 に 1、pos3 に 2 |
 | クエリ [1,3] | 変更なし | 答え = 8 |

 カラフルさの値は次のようになります。 

| ポジション | 値 |
 | --- | --- |
 | 1 | 3 |
 | 2 | 3 |
 | 3 | 2 |

 和：```
3 + 3 + 2 = 8
```この例は、セグメント ツリーが複数の再描画操作からの寄与をどのように蓄積するかを示しています。 

### 例 2

 入力:```
5 3
1 2 4 7
2 1 5
2 2 4
```再塗装後:

 | ポジション | 旧色 | 新色 | インクリメント |
 | --- | --- | --- | --- |
 | 2 | 2 | 7 | 5 |
 | 3 | 3 | 7 | 4 |
 | 4 | 4 | 7 | 3 |

 カラフルな配列:```
[0, 5, 4, 3, 0]
```クエリ:

 | クエリ | 結果 |
 | --- | --- |
 | [1,5] | 12 |
 | [2,4] | 12 |

 このトレースは、範囲の再描画がカバーされる各単色区間に独立して寄与することを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((m + K) log n) | 各セグメント ツリー操作のコストは O(log n) で、分割によって作成された各間隔は、更新によってアクセスされた場合にのみ処理されます。 
| スペース | O(n) | セグメント ツリーと区間表現 |

 間隔構造には、以下の内容が含まれることはありません。`O(n + m)`実行全体にわたって間隔が長くなり、セグメント ツリーは線形メモリを使用します。 と`n, m ≤ 10^5`、これは、この問題に対して受け入れられている解決策で使用される制限内に問題なく収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    from bisect import bisect_left

    sys.stdin = io.StringIO(inp)
    out = io.StringIO()

    input = sys.stdin.readline

    class SegTree:
        def __init__(self, n):
            self.n = n
            self.sum = [0] * (4 * n)
            self.lazy = [0] * (4 * n)

        def push(self, p, l, r):
            if not self.lazy[p]:
                return
            mid = (l + r) // 2
            v = self.lazy[p]
            self.sum[p * 2] += v * (mid - l + 1)
            self.sum[p * 2 + 1] += v * (r - mid)
            self.lazy[p * 2] += v
            self.lazy[p * 2 + 1] += v
            self.lazy[p] = 0

        def add(self, p, l, r, ql, qr, v):
            if ql <= l and r <= qr:
                self.sum[p] += v * (r - l + 1)
                self.lazy[p] += v
                return
            self.push(p, l, r)
            mid = (l + r) // 2
            if ql <= mid:
                self.add(p * 2, l, mid, ql, qr, v)
            if qr > mid:
                self.add(p * 2 + 1, mid + 1, r, ql, qr, v)
            self.sum[p] = self.sum[p * 2] + self.sum[p * 2 + 1]

        def query(self, p, l, r, ql, qr):
            if ql <= l and r <= qr:
                return self.sum[p]
            self.push(p, l, r)
            mid = (l + r) // 2
            ans = 0
            if ql <= mid:
                ans += self.query(p * 2, l, mid, ql, qr)
            if qr > mid:
                ans += self.query(p * 2 + 1, mid + 1, r, ql, qr)
            return ans

    n, m = map(int, input().split())
    seg = SegTree(n)

    starts = list(range(1, n + 1))
    color = {i: i for i in range(1, n + 1)}
    right = {i: i for i in range(1, n + 1)}

    def split(pos):
        if pos > n:
            return len(starts)
        idx = bisect_left(starts, pos)
        if idx < len(starts) and starts[idx] == pos:
            return idx
        idx -= 1
        s = starts[idx]
        starts.insert(idx + 1, pos)
        right[pos] = right[s]
        color[pos] = color[s]
        right[s] = pos - 1
        return idx + 1

    ans = []

    for _ in range(m):
        q = list(map(int, input().split()))

        if q[0] == 1:
            _, l, r, x = q
            ir = split(r + 1)
            il = split(l)

            cur = starts[il:ir]

            for s in cur:
                seg.add(1, 1, n, s, right[s], abs(x - color[s]))

            for s in cur:
                del color[s]
                del right[s]

            starts[il:ir] = [l]
            color[l] = x
            right[l] = r

        else:
            _, l, r = q
            ans.append(str(seg.query(1, 1, n, l, r)))

    return "\n".join(ans)

# sample
assert run(
"""3 3
1 1 2 4
1 2 3 5
2 1 3
"""
) == "8"

# minimum size
assert run(
"""1 1
2 1 1
"""
) == "0"

# repaint same color
assert run(
"""1 2
1 1 1 1
2 1 1
"""
) == "0"

# full range repaint
assert run(
"""3 2
1 1 3 10
2 1 3
"""
) == "24"

# boundary split test
assert run(
"""5 3
1 2 4 7
2 1 5
2 2 4
"""
) == "12\n12"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一要素クエリ | 0 | 初期状態の処理 |
 | 同じ色で塗り直す | 0 | 貢献ゼロの最新情報 |
 | フルレンジリペイント | 24 | アレイ全体の更新 |
 | 内装部分リペイント | 12、12 | 正しい間隔分割 |
 | 公式サンプル | 8 | 一般的な正しさ |

 ## 特殊なケース

 同じ色で再塗装することを検討してください。```
1 2
1 1 1 1
2 1 1
```位置 1 には、最初は色 1 が付いています。色 1 で再ペイントすると、色が追加されます。```
|1-1| = 0
```アルゴリズムは間隔を訪問し、計算します`abs(x - c)`、ゼロを取得し、ゼロ範囲加算を適用します。 クエリは正しく返されます`0`。 

同じ位置を繰り返し再描画することを検討してください。```
1 3
1 1 1 5
1 1 1 2
2 1 1
```最初のリペイントが貢献します`4`、2番目は貢献します`3`。 セグメント ツリーには両方の更新が保存されるため、最終的な答えは次のようになります。`7`。 貢献は追加されるだけで上書きされないため、蓄積されたカラフルさは正しいままです。 

既存の間隔内で開始および終了する再描画を検討してください。```
5 2
1 2 4 7
2 1 5
```更新を処理する前に、アルゴリズムは位置で分割されます`2`そして`5`。 影響を受けた領域は完全な単色区間の集合になります。 それぞれは、その色差に等しい 1 つの範囲加算を受け取ります。`7`。 外側のポジションはありません`[2,4]`タッチすると、クエリが正しく返されます。`12`。 

このようなケースでは、まさに間隔の分割と累積された範囲の追加が重要になります。 アルゴリズムは、設定された間隔が常に現在のカラーリングと一致し、セグメント ツリーがこれまでに適用されたすべての再描画寄与を常に保存するという不変条件を通じて、それらを自然に処理します。
