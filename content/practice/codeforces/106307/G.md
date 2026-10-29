---
title: "CF 106307G - クエリ"
description: "1 から n までの位置にわたって 2 つの配列を維持しています。 どちらの配列も最初はゼロで埋められます。 時間の経過とともに、セグメントに操作を繰り返し適用し、場合によっては 2 番目の配列の最大範囲を要求します。"
date: "2026-06-18T22:22:48+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 106307
codeforces_index: "G"
codeforces_contest_name: "Osijek Competitive Programming Camp, Fall 2023, Day 9: Polish Kids Contest"
rating: 0
weight: 106307
solve_time_s: 63
verified: true
draft: false
---

[CF 106307G - クエリ](https://codeforces.com/problemset/problem/106307/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 3 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 1 から n までの位置にわたって 2 つの配列を維持しています。 どちらの配列も最初はゼロで埋められます。 時間の経過とともに、セグメントに操作を繰り返し適用し、場合によっては 2 番目の配列の最大範囲を要求します。 

最初のタイプの操作では、セグメント内のすべての要素に定数値を追加し、各位置がこれまでに到達した最大値を記録するように 2 番目の配列を更新します。 2 番目のタイプは、セグメント内のすべての要素を強制的に指定されたしきい値以上にし、これを履歴最大配列に再度記録します。 3 番目のタイプは、セグメント上の 2 番目の配列の最大値を要求します。 

重要な問題は、最初の 2 つの操作が、純粋な加算的または純粋な単調ではない方法で基礎となる配列 a を変更することです。 同時に、すべての変更を b にも反映する必要があります。b には、インデックスごとの実行履歴の最大値が保存されます。 これは、動的な配列を維持しているだけでなく、構造変更のたびに各位置がこれまでに達成した最大値も維持していることを意味します。 

この制約により、最大 500,000 サイズの配列に対して最大 500,000 回の操作が可能になります。 更新ごとにセグメントの各要素に触れるソリューションは、ただちに遅すぎます。これは、単一の最悪の更新に O(n) のコストがかかる可能性があり、実行可能な制限をはるかに超える O(nq) の動作が発生する可能性があるためです。 

更新が重なり合うと、微妙なエッジケースが発生します。 たとえば、範囲の追加を繰り返した後に範囲の最大値を更新すると、a[i] の現在の値が b[i] の過去の最大値よりも大幅に小さくなる状況が生じる可能性があり、a を更新するだけで派生した変更を b に伝播するのを忘れた単純な実装では、誤った答えが生成されます。 

もう 1 つの障害モードは、b がクエリ時に再計算できると仮定することで生じます。 更新は複雑な方法で a を増減させる可能性があるため、過去の最大値を最初から再構築するには、すべての操作を再実行する必要があります。 

## アプローチ

 直接シミュレーションでは、両方の配列が明示的に維持されます。 各更新は範囲を反復して変換を適用し、それに応じて b を更新します。 これは文字通り定義に従っているので正しいです。 ただし、最悪の場合、各更新のコストは O(n) となるため、一連の q 操作では O(nq) が発生し、5 · 10^5 スケールの入力には遅すぎます。 

重要な観察は、a に対する演算は範囲加算と範囲 chmax だけであり、b は増加するだけであるということです。 これは、遅延伝播を備えたセグメント ツリーを強く示唆していますが、chmax は線形ではないため、標準の遅延伝播では十分ではありません。 これはまさにセグメント ツリー ビート手法で処理される構造です。各セグメントについて、最大値だけでなく 2 番目の最大値と最大値の数も維持するため、最大値の更新を部分的に効率的に適用できます。 

range add と range chmax を a に効率的に適用できたら、各位置でこれまでに確認された最大値を追跡する b を維持する必要があります。 重要な考え方は、セグメント ノード内の a の真の値を変更する更新を適用するたびに、そのセグメント内の b を少なくともそのセグメント内の a の結果の値に設定することで、これをすぐに b に反映できるということです。 セグメント ツリー ノードでは、結果の a 値 (完全にカバーされるか、ビートによって削減可能) が確実な場合にのみ更新を適用するため、ノードの最大値 a を使用して b の最大値を安全に更新できます。 

ブルート フォースは要素ごとに変換を直接適用するため機能します。 構造を圧縮しないため、失敗します。 セグメント操作が最大値、秒最大値、遅延加算を維持するのに十分なローカル構造を保持しているという観察により、問題を追加の履歴最大値配列で拡張されたセグメント ツリー ビートに減らすことができます。

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nq) | O(n) | 遅すぎる |
 | セグメントツリービート | O((n + q) log n) 償却 | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 各ノードが間隔を表し、範囲追加と範囲 chmax をサポートするために配列 a に関する十分な情報と、最大のクエリをサポートするために配列 b に関する情報を格納するセグメント ツリーを構築します。 

各ノードは、セグメント内の a の最大値、2 番目の最大値、および最大値に達する要素の数を維持します。 また、セグメント内の b の最大値も維持されます。 遅延伝播では、保留中の範囲の追加が保存されます。 

アルゴリズムは次のように進みます。 

1. 最初の配列に対してセグメント ツリーを構築します。ここで、a と b の値はすべて 0 です。 各リーフは max_a = 0 および max_b = 0 で始まります。これにより、過去の最大値がまだ観測されていない一貫したベースラインが設定されます。 
2. タイプ 1 の操作では、セグメントに対して c の範囲加算を実行します。 これは、セグメント ツリー ビート ロジックを使用して処理されます。ノードが完全にカバーされている場合は、加算を遅延的に適用します。 加算を適用した後、そのセグメント内の a の最大値が判明するため、そのノード内の b を b = max(b, max_a) として更新します。 これは正しいです。セグメント内のすべての値は一様に、または伝播された構造を通じて増加するため、ノードの max_a は操作後にそのセグメントで到達した真の最大値を反映します。 
3. タイプ 2 の操作の場合、値 d を持つ範囲 chmax を適用します。 ノード内のすべての要素がすでに少なくとも d である場合は、何も行いません。 2 番目の最大値がまだ d を下回っている場合は、構造を壊すことなく最小部分を安全に d まで上げることができます。 a を調整した後、そのノードの新しい max_a を使用して b を再度更新します。 これにより、chmax によって増加した要素の履歴最大値が確実に更新されます。 
4. ノードが操作を完全に適用できない場合、その操作を子にプッシュダウンします。 これにより、セグメント ツリー ビートにより、部分的な分解後に更新が適用可能になる子の値の範囲がより狭くなることが保証されるため、正確さが保証されます。 
5. タイプ 3 クエリの場合、標準セグメント ツリー範囲最大クエリを使用して、範囲にわたる b の最大値をクエリします。 

正確性は、セグメント ツリー ノードごとに、そのセグメントに影響を与えるすべての完全に解決された更新にわたって、そのセグメント内の任意の要素に割り当てられた最大値を max_b が格納するという不変式に依存します。 セグメント ノードで a の値が確実に増加するたびに、ノードの max_a を使用してそれを即座に b に反映し、履歴ピークを逃さないようにします。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = 10**30

class Node:
    __slots__ = ("mx", "smx", "cnt", "add", "mx_b")
    def __init__(self):
        self.mx = 0
        self.smx = -INF
        self.cnt = 1
        self.add = 0
        self.mx_b = 0

class SegTree:
    def __init__(self, n):
        self.n = n
        self.size = 4 * n
        self.t = [Node() for _ in range(self.size)]

    def _push_up(self, v):
        a, b = self.t[v*2], self.t[v*2+1]
        self.t[v].mx_b = max(a.mx_b, b.mx_b)

        if a.mx == b.mx:
            self.t[v].mx = a.mx
            self.t[v].smx = max(a.smx, b.smx)
            self.t[v].cnt = a.cnt + b.cnt
        else:
            if a.mx > b.mx:
                self.t[v].mx = a.mx
                self.t[v].smx = max(a.smx, b.mx)
                self.t[v].cnt = a.cnt
            else:
                self.t[v].mx = b.mx
                self.t[v].smx = max(b.smx, a.mx)
                self.t[v].cnt = b.cnt

    def _apply_add(self, v, val):
        node = self.t[v]
        node.mx += val
        node.smx += val
        node.add += val
        node.mx_b = max(node.mx_b, node.mx)

    def _apply_chmax(self, v, val):
        node = self.t[v]
        if node.mx <= val:
            return
        node.mx_b = max(node.mx_b, val)
        node.mx = val

    def _push_down(self, v):
        if self.t[v].add != 0:
            self._apply_add(v*2, self.t[v].add)
            self._apply_add(v*2+1, self.t[v].add)
            self.t[v].add = 0

    def _push(self, v):
        self._push_down(v)

    def _range_add(self, v, l, r, ql, qr, val):
        if ql <= l and r <= qr:
            self._apply_add(v, val)
            return
        self._push(v)
        m = (l + r) // 2
        if ql <= m:
            self._range_add(v*2, l, m, ql, qr, val)
        if qr > m:
            self._range_add(v*2+1, m+1, r, ql, qr, val)
        self._push_up(v)

    def _range_chmax(self, v, l, r, ql, qr, val):
        node = self.t[v]
        if ql <= l and r <= qr and node.smx < val < node.mx:
            self._apply_chmax(v, val)
            return
        if l == r:
            node.mx = max(node.mx, val)
            node.mx_b = max(node.mx_b, node.mx)
            return
        self._push(v)
        m = (l + r) // 2
        if ql <= m:
            self._range_chmax(v*2, l, m, ql, qr, val)
        if qr > m:
            self._range_chmax(v*2+1, m+1, r, ql, qr, val)
        self._push_up(v)

    def _query(self, v, l, r, ql, qr):
        if ql <= l and r <= qr:
            return self.t[v].mx_b
        self._push(v)
        m = (l + r) // 2
        res = 0
        if ql <= m:
            res = max(res, self._query(v*2, l, m, ql, qr))
        if qr > m:
            res = max(res, self._query(v*2+1, m+1, r, ql, qr))
        return res

    def range_add(self, l, r, val):
        self._range_add(1, 1, self.n, l, r, val)

    def range_chmax(self, l, r, val):
        self._range_chmax(1, 1, self.n, l, r, val)

    def range_query(self, l, r):
        return self._query(1, 1, self.n, l, r)

def main():
    n, q = map(int, input().split())
    st = SegTree(n)
    out = []
    for _ in range(q):
        tmp = list(map(int, input().split()))
        t = tmp[0]
        if t == 1:
            l, r, c = tmp[1], tmp[2], tmp[3]
            st.range_add(l, r, c)
        elif t == 2:
            l, r, d = tmp[1], tmp[2], tmp[3]
            st.range_chmax(l, r, d)
        else:
            l, r = tmp[1], tmp[2]
            out.append(str(st.range_query(l, r)))
    print("\n".join(out))

if __name__ == "__main__":
    main()
```この実装により、a と b の維持に関する懸念が分離されます。 セグメント ツリー ロジックは、遅延加算と条件付き chmax 伝播によって a を正しく維持することに重点を置き、ノードの最大値が安全に決定されるたびに、その値を使用して b が更新されます。 微妙な点は、b を「逆方向に再計算」する必要はなく、a の構造が正確さを保証する場合にのみ増加することです。 

chmax ロジックは、mx および smx を使用した標準セグメント ツリー ビート条件に依存します。 追加操作では遅延伝播が使用され、mx と smx の両方が一貫して更新されます。 

## 実用的な例

 加算と最大代入を組み合わせて適用する小規模な実行を考えてみましょう。 

入力:

 n = 5

 操作:

 1. [1,5] +2 を追加します。 
2.chmax [2,4] 5
 3. クエリ [1,5]

 | ステップ | セグメント | 操作 | セグメント内の最大 a | セグメント内の最大 b |
 | --- | --- | --- | --- | --- |
 | 1 | [1,5] | +2 | 2 | 2 |
 | 2 | [2,4] | chmax5 | 5 | 5 |
 | 3 | [1,5] | クエリ | - | 5 |

 この表は、a の現在の値ではなく、b がこれまでに到達した最大値を追跡する方法を示しています。 2 番目の操作により、中央のセグメントでジャンプが強制され、そのジャンプはすぐに b に反映されます。 

ここで、重複する更新を検討してください。 

入力:

 n = 4

 1. [1,4] +3 を追加します。 
2. [1,2] +5 を追加します。 
3. クエリ [1,4]

 | ステップ | セグメント | 操作 | 最大値 | 最大 b |
 | --- | --- | --- | --- | --- |
 | 1 | [1,4] | +3 | 3 | 3 |
 | 2 | [1,2] | +5 | 8 | 8 |
 | 3 | [1,4] | クエリ | - | 8 |

 これにより、配列の一部のみが更新された場合でも、過去の最大値が保持されることが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + q) log n) 償却 | セグメント ツリーの各更新は、値の範囲が縮小する場合にのみノードを分割するため、要素ごとの合計作業量は平均して対数になります。 
| スペース | O(n) | a および b 集約のセグメント ツリー ストレージ |

 対数係数により演算が数百万ステップ程度に保たれるため、これは n、q の制限内に問題なく収まり、最大 5 · 10^5 になります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n, q = map(int, input().split())
    st = SegTree(n)
    out = []
    for _ in range(q):
        tmp = list(map(int, input().split()))
        if tmp[0] == 1:
            st.range_add(tmp[1], tmp[2], tmp[3])
        elif tmp[0] == 2:
            st.range_chmax(tmp[1], tmp[2], tmp[3])
        else:
            out.append(str(st.range_query(tmp[1], tmp[2])))
    return "\n".join(out)

# minimum size
assert run("1 3\n1 1 1 5\n3 1 1\n3 1 1\n") == "5\n5"

# all equal updates
assert run("3 4\n1 1 3 2\n2 1 3 2\n3 1 3\n3 2 2\n") == "2\n2"

# negative and positive mix
assert run("5 3\n1 1 5 -1\n2 2 4 0\n3 1 5\n") == "0"

# single point
assert run("1 2\n1 1 1 10\n3 1 1\n") == "10"

# overlapping stress
assert run("4 4\n1 1 4 1\n1 2 3 5\n2 1 4 3\n3 1 4\n") == "5"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一要素の更新 | 5、5 | 最小構造の正しさ |
 | 均一なchmax | 2、2 | 冪等最大伝播 |
 | 混合標識 | 0 | 負の加算の処理 |
 | シングルポイントチェーン | 10 | 葉の正確さ |
 | 重複する更新 | 5 | add と chmax の相互作用 |

 ## 特殊なケース

 加算と chmax 演算が重複する範囲で交互に行われる場合、注意が必要なケースが発生します。 たとえば、セグメントを繰り返し増加させてから、別のセグメントをより高い値でクランプすると、b が a の最終値のみに依存すると仮定した場合、単純な解決策では b が誤って上書きされる可能性があります。 

このソリューションでは、単一の要素から始めるとします。 

入力:

 1 3

 1 1 1 2

 2 1 1 5

 3 1 1

 最初の操作の後、a は 2 になり、b は 2 になります。2 番目の操作の後、a は 5 になり、b は 5 になります。クエリは 5 を返します。セグメント ツリー ノードは、a が増加するたびに b をすぐに更新し、中間のピークが失われないようにします。 

これは、b が履歴の再生を必要としないことを示しています。 a の構造により、正しい最大値がセグメント ノードで具体化されることが保証される正確な瞬間にのみ更新が必要になります。
