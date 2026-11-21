---
title: "CF 280D - k-最大サブシーケンス合計"
description: "私たちは時間の経過とともに変化する配列を維持します。 操作には2種類あります。 1 回の操作で 1 つの位置が更新されます。 もう 1 つは、指定された間隔 [l, r] 内で最大 k 個のペアごとに互いに素な部分配列を選択することによって取得可能な最大合計を求めます。"
date: "2026-06-05T08:49:07+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "flows", "graphs", "implementation"]
categories: ["algorithms"]
codeforces_contest: 280
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 172 (Div. 1)"
rating: 2800
weight: 280
solve_time_s: 147
verified: true
draft: false
---

[CF 280D - k-最大サブシーケンス合計](https://codeforces.com/problemset/problem/280/D)

 **評価:** 2800
 **タグ:** データ構造、フロー、グラフ、実装
 **解決時間:** 2 分 27 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは時間の経過とともに変化する配列を維持します。 

操作には2種類あります。 1 回の操作で 1 つの位置が更新されます。 もう 1 つは、最大で選択することで得られる最大合計額を求めます。`k`指定された間隔内のペアごとに互いに素な部分配列`[l, r]`。 

「せいぜい`k`" は重要です。負の寄与を強制されることはありません。考えられるすべての部分配列が負の合計を持つ場合、正しい答えは次のとおりです。`0`、セグメントをまったく選択しないことによって取得されます。 

配列の長さが次の値に達しました`10^5`、まであります`10^5`操作。 クエリ間隔は、配列のほぼ全体をカバーできます。 すべてのクエリの間隔をスキャンするソリューションは不可能です。 平`O(n)`操作ごとに必要となるのは約`10^10`最悪の場合は動作します。 

2 番目に重要な観察は、`k ≤ 20`。 間隔は非常に長いかもしれませんが、選択できるセグメントの数は非常に少ないです。 この非対称性が解決の鍵となります。 

いくつかの特殊なケースにより、問題は標準の最大部分配列クエリよりも複雑になります。 

考慮する：```
[-5, -2, -7]
k = 3
```答えは`0`、 ない`-2`。 常に正確に選択するソリューション`k`セグメントは失敗します。 

考慮する：```
[9, -8, 9, -1, -1, -1, 9, -8, 9]
k = 2
```2 つの個別に最適なポジティブ ブロックを取ると、次のようになります。`9 + 9 = 18`ですが、最適な答えは次のとおりです。`25`、いくつかの負の値を結合することによって取得されます。 ローカルな選択は機能しません。 

考慮する：```
[10, -100, 10]
k = 1
```答えは`10`、 ない`20`。 1 つのセグメントのみが許可される場合、2 つの正の値を独立して取ることはできません。 

アップデートにより、新たな課題が生じます。 1 つの変更が多くの​​間隔に影響を与える可能性があるため、間隔ごとに答えを事前計算することは不可能です。 

最終的なソリューションでは、間隔の最適化とポイント更新の両方を効率的にサポートする必要があります。 

## アプローチ

 最も直接的なアプローチは、クエリ間隔ごとに動的プログラミングを行うことです。 

クエリで次のような質問があったとします。`[l, r]`。 古典的な DP を実行して選択できるのは、せいぜい`k`その間隔内の互いに素な部分配列。 ある長さの間隔に対して`m`、これにはおおよその必要があります`O(mk)`時間。 

DP は、開かれたセグメントと閉じられたセグメントの数を明示的に追跡するため、正しいです。 残念ながら最悪の場合は`m = 10^5`まであります`10^4`そのようなクエリ。 たとえ`k ≤ 20`、これはおおよそになります```
10^4 × 10^5 × 20 = 2 × 10^10
```操作。 

間隔をスキャンせずに間隔のクエリに答える方法が必要です。 

重要な観察は、最大独立サブ配列問題と最小コスト/最大コスト フローの間のよく知られた縮約から得られます。 

すべての配列要素を、利益がその値と等しいエッジとして想像してください。 サブ配列の選択は、これらの利益エッジの連続ブロックを通じて 1 単位のフローを送信することに対応します。 せいぜい以下に制限する`k`サブアレイは次の容量制約になります。`k`。 

一定間隔の場合、答えは最大で送信コストになります。`k`流量の単位。 

2 つの事実を解明するまでは、これは依然として高価に思えます。 

初め、`k`せいぜい小さいです`20`。 

第二に、区間に対応するグラフは非常に構造化されています。 すべての拡張パスは、現在の残差ネットワークの最大合計部分配列に正確に対応します。 

残余ネットワーク操作は、以下をサポートするセグメント ツリーを使用して実装できます。 

1. サブ配列合計クエリの最大数。 
2. ポイントの更新。 
3. 選択したサブ配列全体を否定します。 

現在最適なサブアレイを見つけた後、フローの 1 ユニットを増加します。 残差グラフでは、これは、選択したセグメント内のすべての要素の符号を反転することと同じです。 その後、再度検索します。 

これはまさに、最大コスト フローの古典的な連続最短パス解釈です。 

最大サブアレイ情報を格納するセグメント ツリーは、最適なセグメントを見つけることができます。`O(log n)`。 セグメント全体の否定は遅延伝播で処理されます。 せいぜい`k ≤ 20`拡張が必要ですが、各クエリのコストのみがかかります`O(k log n)`。 

更新は、同じセグメント ツリー内の通常のポイント変更です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | クエリごとのブルート フォース DP | O((r-l+1)k) | O(r-l+1) | 遅すぎる |
 | セグメントツリー + フロー解釈 | クエリごとに O(k log n)、更新は O(log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 受け入れられた解決策は、最大サブアレイ構造の古典的なセグメント ツリー表現を流量削減とともに使用します。 

各セグメント ツリー ノードには、最大サブ配列クエリに答えるのに十分な情報と、符号反転をサポートするのに十分な情報が格納されます。 

すべてのセグメントについて、次のことを維持します。 

- 合計。 
- 最適なプレフィックス合計。 
- 最適なサフィックス合計。 
- 最適な部分配列の合計。 

また、否定により最大値と最小値が入れ替わるため、対応する最小バージョンも維持します。 

### 1. セグメントツリーを構築する

 値を含むリーフの場合`x`、最大構造と最小構造の両方がすぐにわかります。 

内部ノードは、標準の最大サブ配列式を使用してマージされます。 

### 2. ポイントの更新を処理する

 いつ`a[i]`になる`val`、対応するリーフを更新し、すべての祖先を再計算します。 

セグメントツリーはその後も一貫性を保ちます。`O(log n)`仕事。 

### 3. 質問への回答を開始する`(l,r,k)`フローの解釈では、最適な拡張パスには正のコストがかかる間に、もう 1 つのフロー単位を繰り返し送信することがわかります。 

最初に、残差ネットワークは現在の配列値によって表されます。 

### 4. 内部の最大合計部分配列を見つけます。`[l,r]`セグメント ツリーを使用して、間隔の最大サブ配列情報を取得します。 

これにより、最良のセグメントとそのエンドポイントの両方の値が得られます。 

### 5. 値が正でない場合は停止します。 

この問題により、次の数よりも少ない数を選択できます`k`セグメント。 

利用可能な最良のセグメントに価値があると`≤ 0`、残りのすべての拡張パスは利益を生まないため、答えは増加できません。 

### 6. セグメント値を回答に追加します

 これは、フローのもう 1 つの単位の送信に相当します。 

### 7. 選択したセグメントを無効にします

 残差グラフでは、セグメントを使用すると、反対のコストを持つ逆エッジが導入されます。 

セグメント ツリーの実装では、選択した部分配列のすべての要素に次の値を乗算することで、まったく同じ効果が実現されます。`-1`。 

遅延伝播によりこの操作が行われます`O(log n)`。 

### 8. までを繰り返します`k`回

 各反復では、残差グラフ内で次に最適な拡張パスが検索されます。 

### 9. 変更されたすべてのセグメントを復元します

 クエリによって配列が永続的に変更されてはなりません。 

クエリ中に否定されたすべてのセグメントは最後にもう一度否定され、元の状態に戻ります。 

### なぜ効果があるのか

 流量の削減により、選択の問題は次のようになります。`k`素の部分配列をユニット拡張による最大コスト フロー問題に導きます。 

残差ネットワークでは、最大コスト拡張パスは現在の最大合計部分配列に正確に対応します。 フローの 1 単位を増強すると、そのパスに沿ったコストが逆転します。これは、選択したセグメントを無効にすることで表されます。 

最大コスト フロー アルゴリズムでの継続的な拡張により、常に最適なフローが生成されます。 各拡張は上記のセグメント ツリー操作によって正確に実装されるため、選択されたセグメントのシーケンスはフロー アルゴリズムによって生成されるシーケンスと同一です。 

追加の増強は総コストを増加させることができないため、利用可能な最高の利益がプラスでなくなったときに停止するのは正しいことです。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10 ** 18

class Node:
    __slots__ = (
        "sum",
        "mx",
        "mxl",
        "mxr",
        "mn",
        "mnl",
        "mnr",
        "mx_pos",
        "mxl_pos",
        "mxr_pos",
        "mn_pos",
        "mnl_pos",
        "mnr_pos",
    )

    def __init__(self):
        self.sum = 0

        self.mx = self.mxl = self.mxr = -INF
        self.mn = self.mnl = self.mnr = INF

        self.mx_pos = (0, 0)
        self.mxl_pos = (0, 0)
        self.mxr_pos = (0, 0)

        self.mn_pos = (0, 0)
        self.mnl_pos = (0, 0)
        self.mnr_pos = (0, 0)

def merge(a, b):
    res = Node()

    res.sum = a.sum + b.sum

    if a.mxl >= a.sum + b.mxl:
        res.mxl = a.mxl
        res.mxl_pos = a.mxl_pos
    else:
        res.mxl = a.sum + b.mxl
        res.mxl_pos = (a.mxl_pos[0], b.mxl_pos[1])

    if b.mxr >= b.sum + a.mxr:
        res.mxr = b.mxr
        res.mxr_pos = b.mxr_pos
    else:
        res.mxr = b.sum + a.mxr
        res.mxr_pos = (a.mxr_pos[0], b.mxr_pos[1])

    res.mx = a.mx
    res.mx_pos = a.mx_pos

    if b.mx > res.mx:
        res.mx = b.mx
        res.mx_pos = b.mx_pos

    cross = a.mxr + b.mxl
    if cross > res.mx:
        res.mx = cross
        res.mx_pos = (a.mxr_pos[0], b.mxl_pos[1])

    if a.mnl <= a.sum + b.mnl:
        res.mnl = a.mnl
        res.mnl_pos = a.mnl_pos
    else:
        res.mnl = a.sum + b.mnl
        res.mnl_pos = (a.mnl_pos[0], b.mnl_pos[1])

    if b.mnr <= b.sum + a.mnr:
        res.mnr = b.mnr
        res.mnr_pos = b.mnr_pos
    else:
        res.mnr = b.sum + a.mnr
        res.mnr_pos = (a.mnr_pos[0], b.mnr_pos[1])

    res.mn = a.mn
    res.mn_pos = a.mn_pos

    if b.mn < res.mn:
        res.mn = b.mn
        res.mn_pos = b.mn_pos

    cross = a.mnr + b.mnl
    if cross < res.mn:
        res.mn = cross
        res.mn_pos = (a.mnr_pos[0], b.mnl_pos[1])

    return res

class SegTree:
    def __init__(self, arr):
        self.n = len(arr) - 1
        self.t = [Node() for _ in range(self.n * 4 + 5)]
        self.lazy = [False] * (self.n * 4 + 5)
        self.build(1, 1, self.n, arr)

    def apply(self, p):
        node = self.t[p]

        node.sum = -node.sum

        node.mx, node.mn = -node.mn, -node.mx
        node.mxl, node.mnl = -node.mnl, -node.mxl
        node.mxr, node.mnr = -node.mnr, -node.mxr

        node.mx_pos, node.mn_pos = node.mn_pos, node.mx_pos
        node.mxl_pos, node.mnl_pos = node.mnl_pos, node.mxl_pos
        node.mxr_pos, node.mnr_pos = node.mnr_pos, node.mxr_pos

        self.lazy[p] ^= True

    def push(self, p):
        if self.lazy[p]:
            self.apply(p << 1)
            self.apply(p << 1 | 1)
            self.lazy[p] = False

    def build(self, p, l, r, arr):
        if l == r:
            x = arr[l]
            node = self.t[p]

            node.sum = x

            node.mx = node.mxl = node.mxr = x
            node.mn = node.mnl = node.mnr = x

            node.mx_pos = node.mxl_pos = node.mxr_pos = (l, l)
            node.mn_pos = node.mnl_pos = node.mnr_pos = (l, l)
            return

        m = (l + r) >> 1
        self.build(p << 1, l, m, arr)
        self.build(p << 1 | 1, m + 1, r, arr)

        self.t[p] = merge(self.t[p << 1], self.t[p << 1 | 1])

    def update_point(self, p, l, r, idx, val):
        if l == r:
            node = self.t[p]

            node.sum = val

            node.mx = node.mxl = node.mxr = val
            node.mn = node.mnl = node.mnr = val

            node.mx_pos = node.mxl_pos = node.mxr_pos = (l, l)
            node.mn_pos = node.mnl_pos = node.mnr_pos = (l, l)
            return

        self.push(p)

        m = (l + r) >> 1
        if idx <= m:
            self.update_point(p << 1, l, m, idx, val)
        else:
            self.update_point(p << 1 | 1, m + 1, r, idx, val)

        self.t[p] = merge(self.t[p << 1], self.t[p << 1 | 1])

    def update_range(self, p, l, r, ql, qr):
        if ql <= l and r <= qr:
            self.apply(p)
            return

        self.push(p)

        m = (l + r) >> 1

        if ql <= m:
            self.update_range(p << 1, l, m, ql, qr)
        if qr > m:
            self.update_range(p << 1 | 1, m + 1, r, ql, qr)

        self.t[p] = merge(self.t[p << 1], self.t[p << 1 | 1])

    def query(self, p, l, r, ql, qr):
        if ql <= l and r <= qr:
            return self.t[p]

        self.push(p)

        m = (l + r) >> 1

        if qr <= m:
            return self.query(p << 1, l, m, ql, qr)
        if ql > m:
            return self.query(p << 1 | 1, m + 1, r, ql, qr)

        left = self.query(p << 1, l, m, ql, qr)
        right = self.query(p << 1 | 1, m + 1, r, ql, qr)

        return merge(left, right)

def solve():
    n = int(input())
    arr = [0] + list(map(int, input().split()))

    seg = SegTree(arr)

    m = int(input())
    ans = []

    for _ in range(m):
        q = list(map(int, input().split()))

        if q[0] == 0:
            _, idx, val = q
            seg.update_point(1, 1, n, idx, val)
        else:
            _, l, r, k = q

            changed = []
            cur = 0

            for _ in range(k):
                node = seg.query(1, 1, n, l, r)

                if node.mx <= 0:
                    break

                cur += node.mx

                x, y = node.mx_pos
                changed.append((x, y))

                seg.update_range(1, 1, n, x, y)

            for x, y in changed:
                seg.update_range(1, 1, n, x, y)

            ans.append(str(cur))

    sys.stdout.write("\n".join(ans))

if __name__ == "__main__":
    solve()
```セグメント ツリーには、最大サブ配列情報と最小サブ配列情報の両方が格納されます。 セグメントを否定すると最大値が最小値に、またはその逆に変わるため、最小バージョンが必要です。 両方を保存しないと、遅延符号反転は不可能になります。 

の`apply()`機能は最も微妙な部分です。 否定はすべての和の符号を変更します。 否定後の最大部分配列は、前の最小部分配列の負の値に正確に対応します。 このコードは、最大構造体と最小構造体の値を否定しながら、その構造体を交換します。 

クエリ中に、最適なサブ配列を繰り返し抽出し、その寄与を追加し、それを否定し、続行します。 否定された間隔はすべて記憶されます。 終了後、これらの間隔はすべて再び無効になり、グローバル配列は元の状態に戻ります。 

間隔エンドポイントはツリー ノード内に直接格納されるため、実装では全体で 1 から始まるインデックスが使用されます。 これにより、多くのオフバイワンミスを回避できます。 

## 実用的な例

 ### 例 1

 入力:```
9 -8 9 -1 -1 -1 9 -8 9
k = 2
```| 反復 | 最高のセグメント | セグメント合計 | 実行中の答え |
 | --- | --- | --- | --- |
 | 1 | [1,7] | 16 | 16 |
 | 2 | [9,9] | 9 | 25 |

 答えは次のようになります`25`。 

この例は、目に見えてポジティブなブロックを貪欲に取得するだけでは不十分である理由を示しています。 最適な最初のセグメントには、合計の寄与が大きくなるため、意図的にいくつかの負の値が含まれています。 

### 例 2

 入力:```
-5 -2 -7
k = 3
```| 反復 | 最良のセグメント合計 | アクション |
 | --- | --- | --- |
 | 1 | -2 | 停止 |

 答えは`0`。 

これは、「最大でも`k`" 条件。アルゴリズムは、利用可能な最良のセグメントが非正になるとすぐに停止します。

 ## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(log n) 更新、O(k log n) クエリ | 各拡張には、1 つの間隔クエリと 1 つの範囲否定が必要です。 
| スペース | O(n) | セグメントツリーと遅延配列 |

 以来`k ≤ 20`、すべてのクエリは、少数の一定数のセグメント ツリー操作のみを実行します。 と`n, m ≤ 10^5`、これは簡単に制限内に収まります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()

    old_stdout = sys.stdout
    sys.stdout = out

    solve()

    sys.stdout = old_stdout
    return out.getvalue().strip()

# provided sample
assert run(
"""9
9 -8 9 -1 -1 -1 9 -8 9
3
1 1 9 1
1 1 9 2
1 4 6 3
"""
) == "17\n25\n0", "sample"

# minimum size
assert run(
"""1
5
1
1 1 1 1
"""
) == "5"

# all negative
assert run(
"""3
-1 -2 -3
1
1 1 3 5
"""
) == "0"

# update then query
assert run(
"""3
1 2 3
2
0 2 -10
1 1 3 2
"""
) == "4"

# off-by-one interval
assert run(
"""5
1 2 3 4 5
1
1 5 5 1
"""
) == "5"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一の正の要素 | 5 | 最小配列サイズ |
 | すべての負の値 | 0 | ゼロセグメントの選択 |
 | クエリの前に更新 | 4 | ポイント更新の正確性 |
 | 間隔`[5,5]`| 5 | 境界処理 |
 | 公式サンプル | 17,25,0 | コア機能 |

 ## 特殊なケース

 次のことを考慮してください。```
3
-5 -2 -7
1
1 1 3 3
```最大部分配列クエリは次の結果を返します。`-2`。 この値は正ではないため、拡張ループは直ちに終了します。 セグメントが選択されていない場合、出力は次のようになります。`0`。 これは、ゼロ セグメントの選択が許可されるという要件と一致します。 

考慮する：```
3
10 -100 10
1
1 1 3 1
```最適な部分配列は、値を持つ最初または 3 番目の要素です。`10`。 許可される拡張は 1 つだけであるため、アルゴリズムは 1 つのセグメントを選択した後に停止します。 大きな負のギャップを介して両方の正の値が結合されることはありません。 

考慮する：```
5
1 2 3 4 5
2
0 5 -10
1 4 5 1
```アップデート後の間隔`[4,5]`になる`[4,-10]`。 セグメント ツリーは、影響を受けるすべての祖先を再計算します。 後続のクエリは次の結果を返します。`4`、単一要素セグメントから取得`[4,4]`。 これにより、更新が最大サブ配列構造を通じて正しく伝播されることが確認されます。 

考慮する：```
5
5 -1 5 -1 5
1
1 1 5 10
```それでも`k=10`、収益性の高い拡張は有限の数しか存在しません。 正の寄与が枯渇すると、最良の残差セグメントは非正となり、アルゴリズムは早期に停止します。 セグメントを追加しても合計は増加しないため、答えは依然として最適です。
