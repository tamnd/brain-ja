---
title: "CF 295E - ヤロスラフとポイント"
description: "数直線上に点の集合があります。 各ポイントには ID があるため、更新で「ポイント p を d ずつ移動」と指示すると、その特定のポイントのみが位置を変更します。 操作は 2 つあります。"
date: "2026-06-05T17:48:14+07:00"
tags: ["codeforces", "competitive-programming", "data-structures"]
categories: ["algorithms"]
codeforces_contest: 295
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 179 (Div. 1)"
rating: 2500
weight: 295
solve_time_s: 139
verified: true
draft: false
---

[CF 295E - ヤロスラフとポイント](https://codeforces.com/problemset/problem/295/E)

 **評価:** 2500
 **タグ:** データ構造
 **解決時間:** 2 分 19 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 数直線上に点の集合があります。 各ポイントには ID があるため、更新で「ポイントを移動」と表示されると、`p`による`d`」の場合、その特定の点だけが位置を変更します。 

操作は 2 つあります。 

最初の操作では、現在の座標から 1 つの点を削除し、それを新しい座標に挿入します。 このステートメントは、更新のたびに座標が明確なままであることを保証します。 

2 番目の操作では、座標が指定された間隔内にあるすべての点を調べます。`[l, r]`。 これらの点の間で、順序のないすべてのペアにわたる距離の合計を計算する必要があります。 

選択した座標が$$y_1 < y_2 < \dots < y_k,$$クエリが求めるのは$$\sum_{1 \le i < j \le k}(y_j-y_i).$$制約こそが本当の課題なのです。 到達できるポイント数とクエリ数の両方$10^5$。 すべてのクエリに対してすべてのポイントをスキャンするソリューションには、大まかに次のものが必要になります。$10^{10}$最悪の場合の操作は完全に不可能です。 

座標自体は大きく、最大で$10^9$絶対値で表され、更新により変更され続けます。 座標によってインデックス付けされた配列を直接構築することはできません。 

微妙なケースは、間隔に 0 または 1 つの点が含まれる場合に発生します。 ペアはないので、答えはゼロでなければなりません。 

例えば：```
Points: 5

Query: [0, 10]
```1 つの点だけが選択されているため、答えは次のようになります。```
0
```もう 1 つの簡単な間違いは、更新によって座標値ではなく特定の点が移動されることを忘れていることです。```
Points: 1 10
Update: move point 1 by +20
```結果の座標は次のとおりです。```
21 10
```ない```
1 30
```3 番目のバグの原因は、クエリ境界の処理です。 If a point lies exactly at`l`または正確に`r`を含める必要があります。```
Points: 0 5 10
Query: [0, 10]
```３点とも参加です。 

答えは次のとおりです。```
(5-0) + (10-0) + (10-5) = 20
```厳密な不等式を使用すると、境界点が誤って破棄されます。 

## アプローチ

 最も直接的な解決策は、各クエリを個別に処理することです。 タイプ 2 クエリの場合、座標が次の位置にあるすべての点を収集します。`[l, r]`、それらを並べ替えて、ペアごとの距離の合計を計算します。 

この定義は選択されたすべてのペアの正確な合計であるため、これは正しいです。 

残念ながら、それは遅すぎます。 単一のクエリには次のものが含まれる場合があります$10^5$ポイント。 この方法で答えを計算するにはコストがかかります$O(n \log n)$クエリごとに、おおよそ$10^{10}$オペレーション全般。 

重要な観察は、順序付けられた座標のセットの答えが、より小さな部分からマージできるということです。 

座標範囲を左半分と右半分に分割するとします。 左半分のすべての座標は、右半分のすべての座標よりも小さくなります。 

2 つのグループの場合:$$L=\{x\},\qquad R=\{y\},$$相互貢献度は$$\sum (y-x).$$この式を展開すると、$$|L|\cdot \sum R - |R|\cdot \sum L.$$これは、点の数と各半分の座標の合計にのみ依存します。 

これはセグメントツリーを示唆しています。 すべてのノードについて、以下を保存します。$$cnt = \text{number of active points}$$

$$sum = \text{sum of their coordinates}$$

$$ans = \text{sum of pairwise distances inside this segment}$$2 人の子を結合する場合:$$cnt = cnt_L + cnt_R$$

$$sum = sum_L + sum_R$$

$$ans = ans_L + ans_R + cnt_L \cdot sum_R - cnt_R \cdot sum_L$$最後の項は、まさに、エンドポイントが異なる子にあるペアの寄与です。 

更新はポイントの削除と挿入になります。 クエリは通常のセグメント ツリー範囲クエリになります。 

座標は大きくて動的であるため、出現する可能性のあるすべての座標を圧縮します。 すべての更新値は事前にわかっているため、オフラインでシーケンスをシミュレートし、存在するすべての座標を収集できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(mn \log n)$|$O(n)$| 遅すぎる |
 | 最適なセグメント ツリー + 座標圧縮 |$O((n+m)\log(n+m))$|$O(n+m)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 初期座標を読み取ります。 
2. すべてのクエリを処理する前に 1 回スキャンします。 
3. すべての点の座標変化をシミュレートし、これまでに出現したすべての座標を収集します。 
4. それらの座標を並べ替えて重複を排除します。 これが座標圧縮配列になります。 
5. 圧縮された座標上にセグメント ツリーを構築します。 
6. すべての初期点をツリーに挿入します。 リーフには以下が格納されます。$$cnt=1,\quad sum=\text{coordinate},\quad ans=0.$$7. タイプ 1 アップデートの場合:

 1. 点の古い座標を削除します。 
2. 新しい座標を計算します。 
3. 新しい座標を挿入します。 
4. 保存されているその点の位置を更新します。 
8. タイプ 2 クエリの場合:

 1. 以下の値以上の最初の圧縮座標を見つけます。`l`。 
2. 以下の最後の圧縮座標を見つけます。`r`。 
3. そのセグメント ツリー範囲をクエリします。 
4. 返されたものを出力します`ans`。 
9. マージのたびに、以下を計算します。$$cross = cnt_L \cdot sum_R - cnt_R \cdot sum_L$$そしてセット$$ans = ans_L + ans_R + cross.$$これが機能する理由は、左の子のすべての座標が右の子のすべての座標よりも小さいためです。 

### なぜ効果があるのか

 各セグメント ツリー ノードには、その区間内のペアごとの距離の合計を再構築するために必要な正確な情報が格納されます。 

保存された値`ans`すでに、完全に左の子の内側にすべてのペアが含まれており、右の子の内側に完全にすべてのペアが含まれています。 欠落しているペアは、各子にエンドポイントが 1 つあるペアだけです。 

座標はセグメント ツリーによって順序付けされているため、すべてのクロス ペアが寄与します。`right - left`。 このようなペアをすべて合計すると、次のようになります。$$cnt_L \cdot sum_R - cnt_R \cdot sum_L.$$したがって、すべてのペアはマージ中に 1 回だけカウントされます。 セグメント ツリー ノードの帰納法により、すべてのクエリは、選択されたすべての点間の距離の正しい合計を返します。 

## Python ソリューション```python
import sys
from bisect import bisect_left, bisect_right

input = sys.stdin.readline

class SegTree:
    def __init__(self, coords):
        self.coords = coords
        self.n = len(coords)
        size = 4 * self.n + 5

        self.cnt = [0] * size
        self.sm = [0] * size
        self.ans = [0] * size

    def pull(self, p):
        lc = p * 2
        rc = lc + 1

        cnt_l = self.cnt[lc]
        cnt_r = self.cnt[rc]

        sum_l = self.sm[lc]
        sum_r = self.sm[rc]

        self.cnt[p] = cnt_l + cnt_r
        self.sm[p] = sum_l + sum_r

        cross = cnt_l * sum_r - cnt_r * sum_l
        self.ans[p] = self.ans[lc] + self.ans[rc] + cross

    def update(self, p, l, r, idx, delta):
        if l == r:
            self.cnt[p] += delta
            self.sm[p] += self.coords[idx] * delta
            self.ans[p] = 0
            return

        mid = (l + r) >> 1
        if idx <= mid:
            self.update(p * 2, l, mid, idx, delta)
        else:
            self.update(p * 2 + 1, mid + 1, r, idx, delta)

        self.pull(p)

    def query(self, p, l, r, ql, qr):
        if ql <= l and r <= qr:
            return (self.cnt[p], self.sm[p], self.ans[p])

        mid = (l + r) >> 1

        if qr <= mid:
            return self.query(p * 2, l, mid, ql, qr)

        if ql > mid:
            return self.query(p * 2 + 1, mid + 1, r, ql, qr)

        left = self.query(p * 2, l, mid, ql, qr)
        right = self.query(p * 2 + 1, mid + 1, r, ql, qr)

        cnt_l, sum_l, ans_l = left
        cnt_r, sum_r, ans_r = right

        cross = cnt_l * sum_r - cnt_r * sum_l

        return (
            cnt_l + cnt_r,
            sum_l + sum_r,
            ans_l + ans_r + cross
        )

def solve():
    n = int(input())
    x = list(map(int, input().split()))

    m = int(input())

    queries = []
    coords = x[:]

    cur = x[:]

    for _ in range(m):
        q = list(map(int, input().split()))
        queries.append(q)

        if q[0] == 1:
            p, d = q[1], q[2]
            cur[p - 1] += d
            coords.append(cur[p - 1])

    coords = sorted(set(coords))
    pos = {v: i for i, v in enumerate(coords)}

    seg = SegTree(coords)

    current = x[:]

    for v in current:
        seg.update(1, 0, seg.n - 1, pos[v], 1)

    out = []

    for q in queries:
        if q[0] == 1:
            p, d = q[1], q[2]
            p -= 1

            old = current[p]
            new = old + d

            seg.update(1, 0, seg.n - 1, pos[old], -1)
            seg.update(1, 0, seg.n - 1, pos[new], 1)

            current[p] = new

        else:
            lq, rq = q[1], q[2]

            L = bisect_left(coords, lq)
            R = bisect_right(coords, rq) - 1

            if L > R:
                out.append("0")
                continue

            _, _, ans = seg.query(1, 0, seg.n - 1, L, R)
            out.append(str(ans))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```クエリの最初のパスでは、座標圧縮が実行されます。 出現する可能性のあるすべての座標は、セグメント ツリーが構築される前に収集されます。 

各葉は 1 つの圧縮された座標値に対応します。 座標は常に異なるため、リーフには 0 または 1 つのアクティブ ポイントが含まれますが、実装は、より大きな数が許可された場合でも自然に機能します。 

マージ式はソリューションの中心です。 セグメント ツリーはペアを明示的に列挙することはありません。 代わりに、すべてのクロスペアの合計寄与を一定時間内に再構築するのに十分な集計情報を保持します。 

範囲クエリはトリプルを返します`(count, sum, answer)`。 クエリ処理中に 2 つの部分的な結果がマージされる場合、ツリー自体とまったく同じマージ式が使用されます。 

すべての算術演算では 64 ビット整数を使用する必要があります。 Python ではこれは自動的に行われます。 

## 実用的な例

 ### 例 1```
Points: 0 5 10
Query: [0,10]
```| セグメント | カウント | 合計 | ペアの距離の合計 |
 | --- | --- | --- | --- |
 | {0} | 1 | 0 | 0 |
 | {5} | 1 | 5 | 0 |
 | マージ | 2 | 5 | 5 |
 | {10} | 1 | 10 | 0 |
 | 最終マージ | 3 | 15 | 20 |

 最終的な答えは次のとおりです。$$(5-0)+(10-0)+(10-5)=20.$$この表は、ペアを列挙せずに相互寄与率がどのように距離を再作成するかを示しています。 

### 例 2```
Points: 1 4 8
Update: point 2 += 3
Query: [0,10]
```更新後の座標は次のようになります。```
1 7 8
```| アクティブな座標 | カウント | 合計 | 答え |
 | --- | --- | --- | --- |
 | {1} | 1 | 1 | 0 |
 | {7} | 1 | 7 | 0 |
 | マージ | 2 | 8 | 6 |
 | {8} | 1 | 8 | 0 |
 | 最終マージ | 3 | 16 | 14 |

 答えは次のとおりです。$$(7-1)+(8-1)+(8-7)=14.$$このトレースは、更新に必要なのは 1 つの座標を削除して別の座標を挿入するだけであることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O((n+m)\log(n+m))$| 各更新とクエリは、一定数のセグメント ツリー操作を実行します。 
| スペース |$O(n+m)$| 座標圧縮とセグメントツリーストレージ |

 圧縮された座標の数は、最大でも初期座標に更新ごとに 1 つの新しい座標を加えたものになります。$O(n+m)$。 と$10^5$ポイントと$10^5$クエリの場合、対数係数は制限内に問題なく収まるほど十分に小さいです。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    from bisect import bisect_left, bisect_right

    input_data = io.StringIO(inp)
    input = input_data.readline

    # paste solve() body here and return output
    return ""

# minimum size
# one point, one query
# answer must be 0

# assert run("1\n5\n1\n2 0 10\n") == "0"

# interval contains no points
# assert run("2\n0 10\n1\n2 20 30\n") == "0"

# boundary inclusion
# assert run("3\n0 5 10\n1\n2 0 10\n") == "20"

# update then query
# assert run(
# "3\n1 4 8\n2\n1 2 3\n2 0 10\n"
# ) == "14"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | シングルポイント | 0 | ペアが存在しません |
 | 空の間隔 | 0 | 正しい範囲の処理 |
 | 正確に境界線上の点 | 20 | 包括的なエンドポイント |
 | 更新してからクエリ | 14 | 削除と挿入のロジック |

 ## 特殊なケース

 ポイントを含まないクエリ間隔を考えてみましょう。```
Points: 0 10
Query: [20,30]
```二分検索では、空の圧縮範囲が生成されます。 アルゴリズムはすぐに戻ります`0`。 セグメント ツリー クエリは実行されません。 

ちょうど 1 つの点を含む区間を考えてみましょう。```
Points: 5
Query: [0,10]
```クエリされたセグメントが返されます`cnt = 1`そして`ans = 0`。 ペアがないので、結果は正しいです。 

境界座標を考慮してください。```
Points: 0 5 10
Query: [0,10]
```

`bisect_left`そして`bisect_right`両方のエンドポイントが含まれます。 3 点すべてを選択すると、答えは次のようになります。`20`。 

他の多くの座標を移動する点を考えてみましょう。```
Points: 1 100
Update: point 1 += 200
```古い座標がリーフから削除され、新しい座標が別のリーフに挿入されます。 セグメント ツリーは座標値順に並べられたままであるため、将来の相互寄与計算は引き続き有効です。 正しさの議論は、点がどれだけ移動するかには決して依存しません。
