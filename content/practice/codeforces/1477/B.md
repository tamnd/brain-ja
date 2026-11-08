---
title: "CF 1477B - ネザールとバイナリ文字列"
description: "バイナリ文字列から開始しますが、一連の操作の後、最終的には別のバイナリ文字列になる必要があります。 毎日、固定セグメントが検査されます。 そのセグメントに 0 と 1 の両方が含まれている場合、プロセスは直ちに失敗します。"
date: "2026-06-10T23:53:23+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "greedy"]
categories: ["algorithms"]
codeforces_contest: 1477
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 698 (Div. 1)"
rating: 1900
weight: 1477
solve_time_s: 145
verified: true
draft: false
---

[CF 1477B - Nezzar とバイナリ文字列](https://codeforces.com/problemset/problem/1477/B)

 **評価:** 1900
 **タグ:** データ構造、貪欲
 **解決時間:** 2 分 25 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 バイナリ文字列から開始しますが、一連の操作の後、最終的には別のバイナリ文字列になる必要があります。 

毎日、固定セグメントが検査されます。 そのセグメントに両方が含まれている場合`0`そして`1`、プロセスはすぐに失敗します。 つまり、毎回の検査の前に、検査対象のセグメントが完全に検査される必要があります。`0`または完全に`1`s.

 検査後、夜間に同じセグメント内の文字の半分未満を変更することが許可されます。 

入力は元の文字列を返します`s`、目的の最終文字列`f`、および検査されたセグメントのシーケンス。 変換する一連の有効な夜間変更が存在するかどうかを判断する必要があります。`s`の中へ`f`同時に、検査されたすべてのセグメントがチェックされた時点で均一に保たれます。 

制約は、意図する方向性を最初に示唆するものです。 すべてのテスト ケースにわたって、文字列の合計長とクエリの合計数は両方とも最大です。`2 · 10^5`。 すべてのクエリに対してセグメントのすべての位置にアクセスするソリューションには、大まかに次のものが必要になります。`O(nq)`約に到達できる操作`4 · 10^10`最悪の場合。 それはまったく不可能です。 周りに何かが必要です`O((n+q) log n)`。 

この問題の最も微妙な点は、操作が時間的に前方に記述されているにもかかわらず、最終的な文字列がすでにわかっていることです。 毎晩セグメントを変更する方法が多数存在する可能性があるため、フォワード シミュレーションは困難です。 

いくつかの特殊なケースでは、注意しないと誤った推論が発生します。 

長さが偶数で、現在ちょうど半分の 0 と半分の 1 が含まれているセグメントを考えてみましょう。```
length = 4
segment = 0011
```セグメントを均一にするには、ちょうど 2 文字を変更する必要があります。 ルールでは厳密にセグメントの半分未満の変更のみが許可されているため、2 文字の変更は禁止されています。 「最大でも半分」を有効なものとして扱う解決策は、そのようなケースを誤って受け入れることになります。 

もう 1 つの危険なケースは、すでに完全多数派を獲得しているセグメントです。```
segment = 0001
```変更される可能性があるポジションは半分未満であるため、前日までに可能な唯一の均一な状態はすべてゼロです。 オール 1 にするには 3 つの位置を変更する必要があり、制限を超えます。 どちらかの均一な値を許可する不注意な実装は間違っています。 

最後に、クエリがまったくない場合、変換はできません。```
n = 3, q = 0
s = 101
f = 111
```答えは`NO`文字列は決して変更されないからです。 どのアルゴリズムでも、再構築された初期状態が以下に等しいことを明示的に検証する必要があります。`s`。 

## アプローチ

 ブルートフォースアプローチでは、プロセスをシミュレートしようとします。 から始まる`s`、クエリごとに、検査されたセグメントが均一であるかどうかを判断し、夜間の変更の可能性を検討します。 問題は、多くの異なる中間文字列に到達できる可能性があることです。 あらゆる可能性を探求すると、すぐに指数関数的なものになります。 

何らかの方法で分岐を回避し、1 つの状態のみを追跡したとしても、クエリごとにセグメント全体を更新するにはコストがかかります`O(nq)`時間。 両方の値が到達すると、`2 · 10^5`、それは限界をはるかに超えています。 

重要な観察点は、最後の文字列が`f`知られています。 どうすれば到達できるかを問うのではなく、`f`かどうかを尋ねます。`f`有効な一連の操作から発生した可能性があります。 

クエリセグメントを見ていると仮定します。`[l,r]`そして、その夜の変更直後の文字列がわかります。 変更直前のセグメントはどのようになっていたでしょうか?

 セグメントの長さを次のようにします`len`、そして現在それが含まれているようにします`ones`もの。 

夜間運行前は検査を通過したばかりなので、セグメントは完全に均一になっていたはずだ。 

一律の値だったら`0`、その後、夜間の動作が正確に変更されました`ones`ポジション。 

一律の値だったら`1`、その後、夜間の動作が正確に変更されました`len - ones`ポジション。 

変更される位置は半分未満であるため、これらの可能性のうち 1 つだけが有効になります。 

もし`ones < len/2`の場合、セグメントは以前はすべて 0 であったはずです。 

もし`ones > len/2`の場合、セグメントは以前はすべて 1 であったはずです。 

もし`ones = len/2`、どちらの可能性も機能しません。どちらも位置のちょうど半分を変更する必要があるためです。 

これにより、曖昧さが完全に解消されます。 

から始まる`f`、クエリを逆の順序で処理します。 各セグメントについて、現在含まれているセグメントの数を数えます。 

カウントがセグメント長のちょうど半分である場合、再構築は不可能です。 

それ以外の場合は、セグメント全体を強制多数決値で上書きして続行します。 

すべてのクエリを逆にすると、元の文字列の唯一の候補が得られます。 一致する場合`s`、答えは`YES`; さもないと`NO`。 

残りの課題は、範囲カウントのクエリと範囲割り当ての更新を効率的にサポートすることです。 これは古典的な遅延伝播セグメント ツリーの問題です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nq) 以上 | O(n) | 遅すぎる |
 | 最適 | O((n + q) log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 最終的な文字列からセグメント ツリーを構築します。`f`。 

各ノードは、その間隔内で 1 の数を保存します。 
2. クエリを逆の順序で処理します。 

逆にすると、問題は「多くの可能な将来の状態」から「最大で 1 つの可能な前の状態」に変換されます。 
3. 現在のセグメントの場合`[l,r]`、その中にある 1 の数をクエリします。 

させて`len = r - l + 1`。 
4. もし`2 * ones == len`、すぐに戻ります`NO`。 

以前の一様セグメントは、厳密に半分未満の位置を変更しながら、ちょうど半分が 1 で半分が 0 の状態を生成できません。 
5. もし`2 * ones < len`、セグメント全体をゼロに割り当てます。 

この操作の前に、セグメントはすべてゼロでなければなりません。 
6. それ以外の場合は、セグメント全体を 1 つに割り当てます。 

この操作の前に、セグメントはすべて 1 でなければなりません。 
7. すべての逆クエリが処理されるまで続行します。 
8. 再構築が完了したら、すべての位置を元の文字列と比較します。`s`。 

位置が異なる場合は戻ります`NO`。 
9. 再構築された文字列が一致する場合`s`、 戻る`YES`。 

### なぜ効果があるのか

 重要な不変条件は、クエリを逆方向に処理している間、セグメント ツリーは常に、対応する順方向操作の直後の文字列の一意の状態を表すことです。 

逆向きのクエリ セグメントを考えてみましょう。 現在の 1 の数を次のようにします。`ones`。 

もし`ones < len/2`、すべて 1 つのセグメントからこの状態を取得するには、半分以上の位置を変更する必要があります。 唯一の有効な先行セグメントは、すべてゼロのセグメントです。 

もし`ones > len/2`、対称引数は、唯一の有効な先行セグメントがオール 1 セグメントであることを示しています。 

もし`ones = len/2`、両方の候補の前任者は、位置のちょうど半分を変更する必要があり、これは禁止されています。 

したがって、すべての逆方向のステップは一意に決定されるか、不可能になります。 このアルゴリズムは、有効なフォワード プロセスに存在する可能性のある一連の状態を正確に再構築します。 再構築が異なる文字列に到達した場合`s`の場合、有効なシーケンスはそこから始まりません`s`。 復興が果たせれば`s`、逆の推論を順方向に再生すると、有効な一連の操作が得られます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SegmentTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.tree = [0] * (4 * self.n)
        self.lazy = [-1] * (4 * self.n)
        self._build(1, 0, self.n - 1, arr)

    def _build(self, node, l, r, arr):
        if l == r:
            self.tree[node] = arr[l]
            return

        mid = (l + r) // 2
        self._build(node * 2, l, mid, arr)
        self._build(node * 2 + 1, mid + 1, r, arr)
        self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]

    def _apply(self, node, l, r, val):
        self.tree[node] = (r - l + 1) * val
        self.lazy[node] = val

    def _push(self, node, l, r):
        if self.lazy[node] == -1 or l == r:
            return

        mid = (l + r) // 2
        val = self.lazy[node]

        self._apply(node * 2, l, mid, val)
        self._apply(node * 2 + 1, mid + 1, r, val)

        self.lazy[node] = -1

    def update(self, ql, qr, val):
        self._update(1, 0, self.n - 1, ql, qr, val)

    def _update(self, node, l, r, ql, qr, val):
        if qr < l or r < ql:
            return

        if ql <= l and r <= qr:
            self._apply(node, l, r, val)
            return

        self._push(node, l, r)

        mid = (l + r) // 2
        self._update(node * 2, l, mid, ql, qr, val)
        self._update(node * 2 + 1, mid + 1, r, ql, qr, val)

        self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]

    def query(self, ql, qr):
        return self._query(1, 0, self.n - 1, ql, qr)

    def _query(self, node, l, r, ql, qr):
        if qr < l or r < ql:
            return 0

        if ql <= l and r <= qr:
            return self.tree[node]

        self._push(node, l, r)

        mid = (l + r) // 2
        return (
            self._query(node * 2, l, mid, ql, qr) +
            self._query(node * 2 + 1, mid + 1, r, ql, qr)
        )

def solve():
    t = int(input())
    ans = []

    for _ in range(t):
        n, q = map(int, input().split())
        s = input().strip()
        f = input().strip()

        queries = []
        for _ in range(q):
            l, r = map(int, input().split())
            queries.append((l - 1, r - 1))

        st = SegmentTree([int(c) for c in f])

        ok = True

        for l, r in reversed(queries):
            ones = st.query(l, r)
            length = r - l + 1

            if ones * 2 == length:
                ok = False
                break

            if ones * 2 < length:
                st.update(l, r, 0)
            else:
                st.update(l, r, 1)

        if not ok:
            ans.append("NO")
            continue

        for i in range(n):
            if st.query(i, i) != int(s[i]):
                ok = False
                break

        ans.append("YES" if ok else "NO")

    sys.stdout.write("\n".join(ans))

if __name__ == "__main__":
    solve()
```セグメント ツリーには、各間隔の 1 の数という 1 つの情報のみが保存されます。 これはまさに、逆のプロセスが先行状態を決定するために必要なものです。 

逆の各ステップでセグメント全体がすべて 0 またはすべて 1 で上書きされるため、遅延伝播は不可欠です。 遅延伝播がないと、1 回の更新に直線的な時間がかかる可能性があります。 

との比較`s`すべての逆の操作が適用された後にのみ実行されます。 その時点で、セグメント ツリーは再構築された初期文字列を表します。 

等価性テストでは次のように使用されます。`ones * 2 == length`浮動小数点除算の代わりに。 これにより、精度の問題が回避され、ステートメントからの strict-half 条件が直接表現されます。 

## 実用的な例

 ### 例 1```
n = 5
s = 00000
f = 00111

queries:
[1,5]
[1,3]
```逆順は`[1,3]`、 それから`[1,5]`。 

| ステップ | セグメント | ワンズ | 長さ | アクション | 状態 |
 | --- | --- | --- | --- | --- | --- |
 | 開始 | - | - | - | イニシャル`f`| 00111 |
 | 1 | [1,3] | 1 | 3 | マジョリティ 0、フィル 0 | 00011 |
 | 2 | [1,5] | 2 | 5 | マジョリティ 0、フィル 0 | 00000 |

 最終的に再構築された文字列は`00000`に等しい`s`。 

答え：`YES`。 

このトレースは、逆再構築の中心的なアイデアを示しています。 各セグメントには厳密な多数決があるため、前任者は一意に決定されます。 

### 例 2```
n = 2
s = 00
f = 01

query:
[1,2]
```逆処理:

 | ステップ | セグメント | ワンズ | 長さ | アクション |
 | --- | --- | --- | --- | --- |
 | 開始 | - | - | - | 状態 = 01 |
 | 1 | [1,2] | 1 | 2 | 不可能 |

 セグメントには、0 と 1 が 1 つだけ含まれています。 均一なセグメントからそのような状態に到達するには、位置のちょうど半分を変更する必要があります。 

答え：`NO`。 

この例は、最も重要な拒否条件を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + q) log n) | 各クエリは 1 つの範囲クエリと 1 つの範囲割り当てを実行します。 
| スペース | O(n) | セグメントツリーと遅延配列 |

 の合計値`n`そして`q`すべてのテスト ケース全体で最大でも`2 · 10^5`。 セグメント ツリーで対数演算を行うと、総作業量は問題なく制限内に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    from io import StringIO
    sys.stdin = StringIO(inp)
    out = StringIO()

    input = sys.stdin.readline

    class SegmentTree:
        def __init__(self, arr):
            self.n = len(arr)
            self.tree = [0] * (4 * self.n)
            self.lazy = [-1] * (4 * self.n)
            self.build(1, 0, self.n - 1, arr)

        def build(self, node, l, r, arr):
            if l == r:
                self.tree[node] = arr[l]
                return
            m = (l + r) // 2
            self.build(node * 2, l, m, arr)
            self.build(node * 2 + 1, m + 1, r, arr)
            self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]

        def apply(self, node, l, r, v):
            self.tree[node] = (r - l + 1) * v
            self.lazy[node] = v

        def push(self, node, l, r):
            if self.lazy[node] == -1 or l == r:
                return
            m = (l + r) // 2
            v = self.lazy[node]
            self.apply(node * 2, l, m, v)
            self.apply(node * 2 + 1, m + 1, r, v)
            self.lazy[node] = -1

        def update(self, node, l, r, ql, qr, v):
            if qr < l or r < ql:
                return
            if ql <= l and r <= qr:
                self.apply(node, l, r, v)
                return
            self.push(node, l, r)
            m = (l + r) // 2
            self.update(node * 2, l, m, ql, qr, v)
            self.update(node * 2 + 1, m + 1, r, ql, qr, v)
            self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]

        def query(self, node, l, r, ql, qr):
            if qr < l or r < ql:
                return 0
            if ql <= l and r <= qr:
                return self.tree[node]
            self.push(node, l, r)
            m = (l + r) // 2
            return (
                self.query(node * 2, l, m, ql, qr)
                + self.query(node * 2 + 1, m + 1, r, ql, qr)
            )

    t = int(input())
    ans = []

    for _ in range(t):
        n, q = map(int, input().split())
        s = input().strip()
        f = input().strip()

        qs = []
        for _ in range(q):
            l, r = map(int, input().split())
            qs.append((l - 1, r - 1))

        st = SegmentTree([int(c) for c in f])

        ok = True

        for l, r in reversed(qs):
            ones = st.query(1, 0, n - 1, l, r)
            ln = r - l + 1

            if ones * 2 == ln:
                ok = False
                break

            st.update(1, 0, n - 1, l, r, 1 if ones * 2 > ln else 0)

        if ok:
            for i in range(n):
                if st.query(1, 0, n - 1, i, i) != int(s[i]):
                    ok = False
                    break

        ans.append("YES" if ok else "NO")

    return "\n".join(ans)

assert run("""1
1 0
0
0
""") == "YES"

assert run("""1
1 0
0
1
""") == "NO"

assert run("""1
2 1
00
01
1 2
""") == "NO"

assert run("""1
5 2
00000
00111
1 5
1 3
""") == "YES"

assert run("""1
5 1
11111
11111
1 5
""") == "YES"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`n=1, q=0, s=f`| はい | 最小インスタンス |
 |`n=1, q=0, s≠f`| いいえ | 利用可能な操作はありません |
 |`00 → 01`セグメント長 2 | いいえ | ちょうど半分は不可能 |
 | 最初のサンプルケース | はい | 逆再構築に成功 |
 | 冗長なクエリを持つすべてのもの | はい | 均一セグメントは引き続き有効です。 

## 特殊なケース

 次のことを考慮してください。```
1
2 1
00
01
1 2
```セグメントの長さは`2`正確に 1 つが含まれています`1`。 逆処理中に次の結果が得られます。`ones = 1`、`length = 2`、 それで`2 * ones = length`。 アルゴリズムは即座に拒否します。 2 つの位置のうち 1 つの位置を変更することは厳密には半分未満ではないため、これはルールに一致します。 

考慮する：```
1
4 1
0000
0001
1 4
```セグメントには 1 つが含まれます`1`そして3つ`0`s. 逆処理では、厳密なゼロ多数決を見て、先行するものを次のように再構築します。`0000`。 最終的に再構築された文字列は次のようになります。`s`、したがって、答えは次のとおりです`YES`。 このアルゴリズムは、唯一の可能な先行者を正確に識別します。 

考慮する：```
1
3 0
101
111
```質問はありません。 逆相は何も行いません。 再構築された文字列はそのまま残ります`111`、元の文字列と等しくない`101`。 アルゴリズムは戻ります`NO`、変更は発生しないことを正しく認識しています。 

考慮する：```
1
3 0
101
101
```ここでもクエリはありませんが、再構築された文字列はすでに一致しています。`s`。 アルゴリズムは戻ります`YES`これは、変更が必要ない場合には、まさに意図された動作です。
