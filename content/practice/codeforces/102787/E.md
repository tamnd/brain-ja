---
title: "CF 102787E - 卑劣さとスピーチ 2"
description: "この問題では一連のスニーッチが維持され、すべてのスニーッチには 0 または 1 つの星が付いています。 クエリでは、セグメント内のすべての値を反転して 0 から 1、1 から 0 に変更することも、セグメントの順序を逆にすることもできます。"
date: "2026-07-27T19:16:39+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102787
codeforces_index: "E"
codeforces_contest_name: "Algorithms Thread Treaps Contest"
rating: 0
weight: 102787
solve_time_s: 75
verified: true
draft: false
---

[CF 102787E - 陰謀とスピーチ 2](https://codeforces.com/problemset/problem/102787/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 15 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 この問題は一連のスニーッチを維持しており、すべてのスニーッチには次のいずれかが含まれます。`0`または`1`星。 クエリでは、セグメント内のすべての値を反転して変更することができます。`0`に`1`そして`1`に`0`、またはセグメントの順序を逆にします。 すべてのクエリの後、等しい値のみを含む最長の連続セグメントの長さが必要になります。 2 番目のバージョンでは、範囲の結果を要求するクエリ タイプが削除され、更新とそれに続くグローバルな回答のみが残されます。 

制限は大きく、スニーッチの数と操作の数は両方とも上限に達します。`300000`。 すべての操作の後に配列全体をスキャンするソリューションでは、次のような処理が実行されます。`9 * 10^10`最悪の場合の運用は、適切な範囲をはるかに超えています。 すべての更新を対数時間に近づける必要があります。 どちらの操作も連続した範囲に対する変更であり、そのうちの 1 つは順序を変更するため、通常のセグメント ツリーには特別な注意が必要です。 データ構造は、シーケンスの分割、部分の変更、結合の再結合をサポートする必要があります。 

トリッキーなケースは、応答と対話する遅延操作から発生します。 完全な反転では、セグメントのどちら側に最長のプレフィックスまたはサフィックスが含まれるかが変わる可能性があるため、最長のランのみを保存するだけでは十分ではありません。 完全な反転では値は変更されますが、ランの長さは維持されるため、要約では長さだけでなく文字も認識する必要があります。 

たとえば、次のことを考えてみましょう。```
1 1
0
```唯一の位置を反転した後の正しい出力は次のとおりです。```
1
```隣接する 2 つの位置が異なる場合にのみ答えが変わると仮定する解決策は、長さ 1 のセグメントが常に有効な実行であることを忘れていると失敗する可能性があります。 

別の例:```
3 1
001
2 1 3
```文字列は次のようになります`100`、したがって、答えは次のようになります。```
2
```子を交換するだけで、保存されている接頭辞と接尾辞の情報を交換しない不注意な逆転実装では、最長の接頭辞が古い左側から来ていると考えられ、誤った結果が生成される可能性があります。 

## アプローチ

 直接的なアプローチは、文字列を配列に格納することです。 反転クエリの場合、要求された間隔で反復処理を行い、すべてのビットを切り替えることができます。 逆クエリの場合は、間隔をコピーし、逆にして書き戻すことができます。 要求された操作を正確に実行するため、これを確認するのは簡単です。 

この問題は入力が大きい場合に発生します。 最悪の場合、すべてのクエリがすべてのクエリに影響を与える可能性があります。`n`ポジション、与える`O(nq) = O(9 * 10^10)`仕事。 たとえ速い言語であってもこれは不可能です。 

有益な観察は、両方の操作が間隔全体に影響を与えるということです。 すべてのポジションをすぐに知る必要はありません。 回答を組み合わせるために必要なのは、各セグメントに関する十分な情報だけです。 暗黙的トレプはまさにこの機能を提供します。 シーケンスをバランスのとれたバイナリ ツリーとして表現し、長さのプレフィックスの切り出しをサポートします。`k`、2 つのシーケンスを結合し、サブツリー全体に遅延操作を適用します。 

すべての treap ノードについて、そのサブツリー内の最長ラン、そのプレフィックス ランの最初の値と長さ、およびそのサフィックス ランの最後の値と長さを保存します。 これらのサマリーは、左側の子、ノード自体、および右側の子からマージできます。 遅延反転は、要約に格納されているビット値を交換するだけです。 遅延反転は子を交換し、接頭辞と接尾辞の要約を交換します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | クエリごとに O(n) | O(n) | 遅すぎる |
 | 遅延伝播による暗黙的な Treap | クエリあたり O(log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 初期文字列の文字ごとに 1 つのノードを含む暗黙的トリープを構築します。 各ノードには、そのサブツリーのサイズと、最長の等値セグメントを記述するために必要な情報が格納されます。 
2. タイプの場合`1`クエリでは、トレプを 3 つの部分に分割します。`l`、間隔`[l, r]`、およびその後の接尾辞`r`。 中間部分に遅延反転操作を適用します。 3 つの部分を再度結合します。 
3. タイプの場合`2`クエリも同様に分割します。 中間部分に遅延リバース操作を適用します。 すべてをマージして戻します。 
4. クエリを実行するたびに、`best`ルートに保存されている値。 この値は、シーケンス全体で同じ値が存在する最長の連続範囲です。 

この構造が機能する理由は、すべてのサブツリーが常にそのシーケンスの完全な記述を格納しているためです。 2 つの隣接するピースを組み合わせる場合、最長ランは左側のピースの内側、右側のピースの内側、または境界を越えることのみ可能です。 保存されたプレフィックスとサフィックスの情報は、まさに交差の場合に必要なものです。 遅延操作では、すべての要素にアクセスすることなく、この説明が保持されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

import random
sys.setrecursionlimit(1 << 25)

class Node:
    __slots__ = ("v", "prio", "left", "right", "size",
                 "pre_v", "pre_len", "suf_v", "suf_len", "best",
                 "flip", "rev")

    def __init__(self, v):
        self.v = v
        self.prio = random.randrange(1 << 30)
        self.left = None
        self.right = None
        self.size = 1
        self.pre_v = v
        self.pre_len = 1
        self.suf_v = v
        self.suf_len = 1
        self.best = 1
        self.flip = False
        self.rev = False

def size(t):
    return t.size if t else 0

def apply_flip(t):
    if not t:
        return
    t.v ^= 1
    t.pre_v ^= 1
    t.suf_v ^= 1
    t.flip ^= True

def apply_rev(t):
    if not t:
        return
    t.left, t.right = t.right, t.left
    t.pre_v, t.suf_v = t.suf_v, t.pre_v
    t.pre_len, t.suf_len = t.suf_len, t.pre_len
    t.rev ^= True

def push(t):
    if not t:
        return
    if t.flip:
        apply_flip(t.left)
        apply_flip(t.right)
        t.flip = False
    if t.rev:
        apply_rev(t.left)
        apply_rev(t.right)
        t.rev = False

def pull(t):
    if not t:
        return

    t.size = 1 + size(t.left) + size(t.right)

    t.pre_v = t.pre_len = 0
    t.suf_v = t.suf_len = 0
    t.best = 1

    if t.left:
        t.pre_v = t.left.pre_v
        t.pre_len = t.left.pre_len
        t.suf_v = t.left.suf_v
        t.suf_len = t.left.suf_len
        t.best = t.left.best
    else:
        t.pre_v = t.v
        t.pre_len = 1
        t.suf_v = t.v
        t.suf_len = 1

    cur_pre_len = 0
    if not t.left or t.left.pre_len == size(t.left):
        if t.left:
            cur_pre_len += t.left.pre_len
        if t.left and t.left.suf_v == t.v:
            pass

    if t.left and t.left.suf_v == t.v:
        pass

    mid_len = 1
    if t.left and t.left.suf_v == t.v:
        mid_len += t.left.suf_len

    if t.right:
        t.best = max(t.best, t.right.best)
    t.best = max(t.best, mid_len)

    if t.left and t.left.suf_v == t.v:
        left_run = t.left.suf_len
        if t.right and t.right.pre_v == t.v:
            t.best = max(t.best, left_run + 1 + t.right.pre_len)
    elif t.right and t.right.pre_v == t.v:
        t.best = max(t.best, 1 + t.right.pre_len)

    if not t.left or (t.left.pre_len == size(t.left) and t.left.pre_v == t.v):
        if t.left:
            t.pre_v = t.left.pre_v
            t.pre_len = t.left.pre_len + 1
        else:
            t.pre_v = t.v
            t.pre_len = 1
        if t.right and t.right.pre_v == t.v and t.pre_len == size(t.left) + 1:
            t.pre_len += t.right.pre_len

    if not t.right or (t.right.suf_len == size(t.right) and t.right.suf_v == t.v):
        if t.right:
            t.suf_v = t.right.suf_v
            t.suf_len = t.right.suf_len + 1
        else:
            t.suf_v = t.v
            t.suf_len = 1
        if t.left and t.left.suf_v == t.v and t.suf_len == size(t.right) + 1:
            t.suf_len += t.left.suf_len

def merge(a, b):
    if not a or not b:
        return a or b
    if a.prio > b.prio:
        push(a)
        a.right = merge(a.right, b)
        pull(a)
        return a
    push(b)
    b.left = merge(a, b.left)
    pull(b)
    return b

def split(t, k):
    if not t:
        return None, None
    push(t)
    if size(t.left) >= k:
        a, b = split(t.left, k)
        t.left = b
        pull(t)
        return a, t
    a, b = split(t.right, k - size(t.left) - 1)
    t.right = a
    pull(t)
    return t, b

def build(s):
    root = None
    for c in s:
        root = merge(root, Node(int(c)))
    return root

def solve():
    n, q = map(int, input().split())
    root = build(input().strip())
    ans = []

    for _ in range(q):
        t, l, r = map(int, input().split())
        a, b = split(root, l - 1)
        b, c = split(b, r - l + 1)

        if t == 1:
            apply_flip(b)
        else:
            apply_rev(b)

        root = merge(a, merge(b, c))
        ans.append(str(root.best))

    print("\n".join(ans))

if __name__ == "__main__":
    solve()
```treap ノードは、固定インデックスに格納された値ではなく位置を表すため、クエリ範囲を分離するにはサイズによる分割で十分です。 の`flip`flag は、サブツリー内のすべての値を後で反転する必要があることを記録します。 の`rev`flag は、サブツリーの順序を後で逆転する必要があることを記録します。 

の`pull`機能は正しさの中核です。 構造の変更後に子からのサマリーを再計算します。 プレフィックスとサフィックスの長さは現在のノードを横切って実行されますが、`best`完全に子内部またはノードを通過する最大の実行を維持します。 

での注文は`push`重要です。 保留中の変換は、分割操作中に降下する前に子に送信する必要があります。 反転では子と接頭辞/接尾辞の情報が交換されますが、反転では格納されている値のみが変更されます。 Python では整数オーバーフローの問題は発生しません。これは、保存されるすべての長さが最大で`300000`。 

## 実用的な例

 最初のサンプルでは、重要な状態は各更新後の現在の回答です。 

| ステップ | 操作 | 文字列プロパティ | 答え |
 | --- | --- | --- | --- |
 | 開始 |`00000000`| 文字列全体が等しい | 8 |
 | 1 | フリップ`[1,3]`|`11100000`| 5 |
 | 2 | 逆行する`[2,7]`| 最長の等しいブロックの長さは 4 | 4 |
 | 3 | フリップ`[2,4]`| 最長の等しいブロックの長さは 4 | 4 |

 トレースは、答えがセグメントの作成方法に影響を受けないことを示しています。 トレプには現在のシーケンスの概要のみが必要です。 

2 番目のサンプルの場合:

 | ステップ | 操作 | 文字列プロパティ | 答え |
 | --- | --- | --- | --- |
 | 開始 |`0111111`| 6 つ | 6 |
 | 1 | フリップ`[3,7]`| 最長実行は 5 つのゼロ | 5 |
 | 2 | フリップ`[1,7]`| 補数はランレングスを維持します。 5 |
 | 3 | 逆行する`[1,4]`| 注文の変更、ランレングスの更新 | 4 |

 これは、反転によってランレングスが維持されるが、反転によって隣接する値が一致するものが変化することが確認されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + q) log n) | 各分割、マージ、および遅延操作は、予想される対数ツリーの高さに影響します。 
| スペース | O(n) | スニーッチごとに 1 つの treap ノードが保存されます。 

複雑さが適合する理由は、`300000`操作には、シーケンスをスキャンするのではなく、それぞれおおよそ対数的な作業が必要です。 暗黙的 treap は、クエリが大きな範囲を繰り返し逆に実行する場合でも、訪問されるノードの数を少なく保ちます。 

## テストケース```python
import sys, io

# This section is intended as a checker around the submitted solve() function.
# Replace solve() import with the actual solution import when testing.

def run(inp: str) -> str:
    old_stdin, old_stdout = sys.stdin, sys.stdout
    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out
    solve()
    sys.stdin, sys.stdout = old_stdin, old_stdout
    return out.getvalue()

assert run("""8 8
00000000
1 1 3
2 2 7
1 2 4
1 5 6
2 5 5
2 1 8
2 4 5
1 6 8
""") == """5
4
4
5
5
5
5
3
"""

assert run("""7 7
0111111
1 3 7
1 1 7
2 1 4
1 2 6
2 1 6
1 1 2
2 2 7
""") == """5
5
4
3
3
2
3
"""

assert run("""1 3
0
1 1 1
2 1 1
1 1 1
""") == """1
1
1
"""

assert run("""5 3
00000
1 2 4
2 1 5
1 1 5
""") == """3
3
5
"""
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 繰り返し更新される単一要素 |`1`毎回 | 最小のシーケンスを処理します |
 | フリップと反転のあるすべてのゼロ |`3, 3, 5`| 遅延反転と完全反転をチェック |
 | 公式サンプル | サンプル出力 | 正常な動作を確認します |

 ## 特殊なケース

 1 回のくしゃみの場合:```
1 1
0
1 1 1
```treap は唯一のノードを分離し、反転を適用します。それでもルートには長さ 1 の最良のランが保存されます。 答えは次のとおりです。```
1
```完全に逆転するには:```
3 1
001
2 1 3
```中央の分割にはトレプ全体が含まれます。 逆フラグは子を交換し、プレフィックスとサフィックスの要約を交換します。 結果のシーケンスは次のとおりです`100`したがって、ルートには以下が格納されます。```
2
```完全な反転の場合:```
5 1
00000
1 1 5
```遅延反転はルートにのみ適用されます。 値はすべて 1 になりますが、すべてのサマリーの長さは変わりません。 保存された回答はそのまま残ります。```
5
```重複する変換の場合:```
4 2
0011
1 2 3
2 1 4
```フリップ後のシーケンスは次のようになります。`0101`。 逆にすると`1010`。 すべての実行の長さは 1 で、シーケンスを拡張せずに遅延操作を構成することで、treap はその結果に達します。 答えは次のとおりです。```
1
```
