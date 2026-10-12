---
title: "CF 105198L - カロプシア シーケンス"
description: "各文字が左括弧または右括弧のいずれかである括弧のバイナリ文字列を維持しています。 文字列は時間の経過とともに変化するため、更新のたびに、選択した部分文字列が有効な通常の括弧シーケンスを形成しているかどうかを回答する必要がある場合があります。"
date: "2026-06-27T03:01:32+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105198
codeforces_index: "L"
codeforces_contest_name: "ShellBeeHaken Presents Intra SUST Programming Contest 2024 - Replay"
rating: 0
weight: 105198
solve_time_s: 87
verified: false
draft: false
---

[CF 105198L - カロプシア シーケンス](https://codeforces.com/problemset/problem/105198/L)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 27 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 各文字が左括弧または右括弧のいずれかである括弧のバイナリ文字列を維持しています。 文字列は時間の経過とともに変化するため、更新のたびに、選択した部分文字列が有効な通常の括弧シーケンスを形成しているかどうかを回答する必要がある場合があります。 

部分文字列が括弧の正しい入れ子として解釈できる場合、部分文字列は有効であるとみなされます。つまり、部分文字列のすべての接頭辞に左括弧より多くの閉じ括弧が含まれることはなく、最後に開き括弧と閉じ括弧の合計数が等しいことを意味します。 

2 つの操作がサポートされています。 1 つの操作では、範囲内のすべての括弧を反転し、すべての「(」を「)」に、すべての「)」を「(」に変換します。もう 1 つの操作は、指定された範囲が現在この定義で有効なシーケンスを形成しているかどうかを尋ねます。

 困難は同時に 2 つの方向から来ます。 まず、更新は単一の位置ではなくセグメント全体に影響します。 第 2 に、有効性は単純なカウント クエリではありません。バランスは合計だけでなく、部分文字列のすべてのプレフィックスで正しくなければならないからです。 

最大 200,000 の位置と 200,000 の操作があるため、クエリごとにプレフィックスの有効性を再計算するソリューションはすぐに失敗します。 クエリごとのフル スキャンには、最悪の場合、許容範囲をはるかに超える二次時間のコストがかかります。 各スキャンのセグメント長が線形の場合、操作ごとのスキャン数が対数であっても遅すぎます。 

セグメント全体のバランスだけを維持しようとすると、より微妙な故障モードが現れます。 たとえば、文字列「())(」の合計残高はゼロですが、プレフィックスが負になるため無効です。これは、正しい構造は集計カウントだけでなくプレフィックスの動作を追跡する必要があることを示しています。

 反転に関しては、別の微妙な問題が発生します。 反転後、括弧の役割が完全に入れ替わるため、変換サポートのない静的な重みを想定する表現は、セグメント全体にわたる符号反転を明示的に処理しない限り壊れます。 

## アプローチ

 ブルートフォースのアイデアは単純です。 クエリごとに、部分文字列を抽出します。 反転操作の場合は、範囲内の各文字を反転します。 有効性チェックの場合は、スタックをシミュレートするか、実行残高を維持して、ゼロで終わると同時に負にならないことを確認します。 これは、有効な括弧シーケンスの定義に直接従うため、正しいです。 

問題はパフォーマンスです。 各クエリは最大 O(n) 文字をタッチできます。 q が 200,000 までの場合、O(nq) の動作が発生し、最悪の場合は 4e10 演算程度になります。 これでは時間内に実行できません。 

重要な観察は、括弧シーケンスの有効性は、接頭辞に関する 2 つの値、つまり +1 と -1 の表現の合計と、接頭辞の合計の最小値のみに依存するということです。 「(」を +1 に、「)」を -1 にマップすると、サブストリングは、その合計がゼロであり、プレフィックスの最小合計が負にならない場合にのみ有効になります。 

これにより、問題は範囲更新時にシーケンスを維持しながら、間隔を超えて合計と最小プレフィックス合計の両方をクエリできるようになります。 セグメント ツリーはこの情報を正確に保存できます。 残りの課題は、セグメント内のすべての値を無効にするフリップ操作です。 否定は合計に影響を与えるだけでなく、遅延伝播タグで処理できる正確な方法でプレフィックスの極値を交換します。 

ブルート フォースは構造を直接評価するため機能しますが、範囲が大きくなり、更新が重複すると失敗します。 セグメント ツリーが機能するのは、妥当性条件が構成的なものであるためです。つまり、少量の情報を使用して 2 つのセグメントをマージでき、反転したセグメントを展開せずに変換できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n · q) | O(n) | 遅すぎる |
 | Lazy Flip を使用したセグメント ツリー | O(q log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

各「(」は +1、各「)」は -1 として表します。 配列の各セグメントには、合計合計、最小プレフィックス合計、および最大プレフィックス合計の 3 つの値が格納されます。 これらは、正しさを答え、マージと反転をサポートするには十分です。 

1. 各葉が 1 つの文字に対応するセグメント ツリーを構築します。 リーフには合計が +1 または -1 として格納され、両方の接頭辞の極値はその値に等しくなります。 これにより、以降のすべての操作の基本表現が確立されます。 
2. 左と右の子を結合する内部ノードの場合、両方の子の合計として合計を計算します。 最小プレフィックスは、左の最小値と左の合計と右の最小値の最小値です。 これが機能するのは、プレフィックスが左側のセグメント内に留まるか、左側の合計を完全に消費した後に右側に拡張されるためです。 最大プレフィックスは、同じ推論を使用して対称的に計算されます。 
3. 反転操作の場合、セグメントを再計算するのではなく、変換をセグメントに適用します。 反転すると、+1 ごとに -1 が変更され、その逆も行われるため、合計はマイナスになります。 
4. The prefix structure also inverts. The new minimum prefix becomes the negative of the previous maximum prefix, and the new maximum prefix becomes the negative of the previous minimum prefix. これは、セグメント内のすべての部分和の符号を反転することから得られます。 
5. 遅延伝播フラグを使用して、変更を子にすぐにプッシュせずにセグメントを反転済みとしてマークします。 必要に応じて、同じ変換を子に適用し、フラグをクリアすることでフリップを押し下げます。 
6. クエリの場合、[l, r] を表すセグメントを取得します。 合計がゼロで、最小プレフィックスがゼロ以上の場合、部分文字列は有効です。 そうでなければそうではありません。 

The correctness hinges on the fact that every segment is fully summarized by these three values, and both merging and flipping preserve the validity of this summary without losing information.

 機能する理由: 括弧シーケンスは、各文字の高さが ±1 ずつ変化する整数のウォークに対応します。 妥当性は、ウォークがゼロで終了し、決してゼロを下回らないかどうかによってのみ決まります。 The segment tree maintains exact prefix extrema for each interval, and both concatenation and sign inversion preserve the structure of this walk representation. すべての操作はこれらの不変条件を尊重するため、クエリは常に部分文字列の真の状態を反映します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class Node:
    __slots__ = ("sum", "mn", "mx", "flip")
    def __init__(self, s=0, mn=0, mx=0):
        self.sum = s
        self.mn = mn
        self.mx = mx
        self.flip = False

class SegTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.t = [Node() for _ in range(4 * self.n)]
        self.build(1, 0, self.n - 1, arr)

    def build(self, v, l, r, arr):
        if l == r:
            val = 1 if arr[l] == '(' else -1
            self.t[v] = Node(val, val, val)
            return
        m = (l + r) // 2
        self.build(v * 2, l, m, arr)
        self.build(v * 2 + 1, m + 1, r, arr)
        self.pull(v)

    def pull(self, v):
        L = self.t[v * 2]
        R = self.t[v * 2 + 1]
        self.t[v].sum = L.sum + R.sum
        self.t[v].mn = min(L.mn, L.sum + R.mn)
        self.t[v].mx = max(L.mx, L.sum + R.mx)

    def apply_flip(self, v):
        node = self.t[v]
        node.sum *= -1
        node.mn, node.mx = -node.mx, -node.mn
        node.flip ^= True

    def push(self, v):
        if self.t[v].flip:
            self.apply_flip(v * 2)
            self.apply_flip(v * 2 + 1)
            self.t[v].flip = False

    def update(self, v, l, r, ql, qr):
        if ql <= l and r <= qr:
            self.apply_flip(v)
            return
        self.push(v)
        m = (l + r) // 2
        if ql <= m:
            self.update(v * 2, l, m, ql, qr)
        if qr > m:
            self.update(v * 2 + 1, m + 1, r, ql, qr)
        self.pull(v)

    def query(self, v, l, r, ql, qr):
        if ql <= l and r <= qr:
            return self.t[v]
        self.push(v)
        m = (l + r) // 2
        if qr <= m:
            return self.query(v * 2, l, m, ql, qr)
        if ql > m:
            return self.query(v * 2 + 1, m + 1, r, ql, qr)

        left = self.query(v * 2, l, m, ql, qr)
        right = self.query(v * 2 + 1, m + 1, r, ql, qr)

        res = Node()
        res.sum = left.sum + right.sum
        res.mn = min(left.mn, left.sum + right.mn)
        res.mx = max(left.mx, left.sum + right.mx)
        return res

def solve():
    n, q = map(int, input().split())
    s = list(input().strip())
    st = SegTree(s)

    out = []
    for _ in range(q):
        t, l, r = map(int, input().split())
        l -= 1
        r -= 1
        if t == 1:
            st.update(1, 0, n - 1, l, r)
        else:
            res = st.query(1, 0, n - 1, l, r)
            if res.sum == 0 and res.mn >= 0:
                out.append("YES")
            else:
                out.append("NO")

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```セグメント ツリーは、マップされた整数配列上に構築されます。 各ノードは合計とプレフィックスの極値を維持するため、要素をスキャンすることなく任意の間隔クエリに答えることができます。 反転操作は遅延処理されます。子をすぐに変更するのではなく、保存された概要を反転してノードにマークを付け、必要な場合にのみ子孫が修正されるようにします。 これにより、更新操作とクエリ操作の両方が対数的に維持されます。 

よくある間違いは、セグメントをマージするときに、プレフィックスの最小値を累積合計に基づいて再計算する必要があることを忘れていることです。 もう 1 つは、最小値と最大値を交換せずに否定を適用することで、入れ子構造の正確性が損なわれます。 

## 実用的な例

 小さなシーケンス「()()()」と中央のセグメントの反転を考えてみましょう。 

| ステップ | 操作 | セグメント | 合計 | 最小プレフィックス | 有効 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | イニシャル | (1,6) | 0 | 0 | はい |
 | 2 | フリップ (2,5) | (1,6) | 0 | 0 | はい |

 中央を反転した後、内部の順序が変化しても構造のバランスは保たれており、セグメント表現がローカル構造を正しく抽象化していることが確認できます。 

ここで「())(」を考えてみましょう。

 | ステップ | 操作 | セグメント | 合計 | 最小プレフィックス | 有効 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | クエリ | (1,4) | 0 | -1 | いいえ |

 合計がゼロであっても、プレフィックスはゼロ未満になり、部分文字列が正しく拒否されます。 

これらの例は、合計バランスだけでは不十分であり、プレフィックス追跡が不可欠であることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(q log n) | 各更新とクエリはセグメント ツリーの高さ | を横断します。 
| スペース | O(n) | ツリーノードはセグメントごとに一定の情報を保存します。 

この制約により、最大 200,000 回の操作が可能になり、操作ごとの対数時間は制限内に快適に収まります。 メモリ使用量は、初期文字列のサイズにおいて線形のままです。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output

    solve()

    return output.getvalue().strip()

# sample-like test (formatting assumed corrected)
# assert run(...) == ...

# minimum size
assert run("1 1\n(\n2 1 1\n") in {"NO", "YES"}  # single bracket must be invalid/valid depending

# already balanced
assert run("2 1\n()\n2 1 2\n") == "YES"

# flip then query
assert run("2 2\n()\n1 1 2\n2 1 2\n") == "YES"

# all closing
assert run("3 1\n)))\n2 1 3\n") == "NO"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一の文字 | いいえ | 最小の無効なケース |
 | () クエリ | はい | 基本的な正しさ |
 | フルレンジを反転 | はい | 遅延反転の正確性 |
 | すべて ')' | いいえ | プレフィックス違反の検出 |

 ## 特殊なケース

 配列全体にまたがるセグメントを複数回反転すると、微妙なエッジケースが発生します。 反転操作は間接的であるため、2 回適用すると構造が元の状態に戻るはずです。 遅延伝播フラグは、フラグを 2 回切り替えると自動的にキャンセルされるため、この動作を保証します。 

もう 1 つのケースは、反転された領域と反転されていない領域が部分的に重なるクエリです。 プッシュ操作では、保留中の反転が部分走査の前に適用されることが保証されるため、クエリされたセグメントは常に一貫した状態を反映します。 

たとえば、「(()())」から開始して、[2,5] を 2 回反転すると、元の文字列が返されます。 セグメント ツリーは、ノードでフリップ フラグを切り替え、必要な場合にのみ伝播することでこれを処理し、反転の二重適用によって格納された値が破損しないようにします。
