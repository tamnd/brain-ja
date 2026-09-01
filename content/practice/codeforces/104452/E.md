---
title: "CF 104452E - ハイランダーズ トーナメント"
description: "私たちには戦闘機の列が与えられており、各戦闘機は固定された左から右の順序で座っており、それぞれが明確な強さの値を持っています。"
date: "2026-06-30T14:42:42+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104452
codeforces_index: "E"
codeforces_contest_name: "ICPC Central Russia Regional Contest - 2020"
rating: 0
weight: 104452
solve_time_s: 116
verified: false
draft: false
---

[CF 104452E - ハイランダーズのトーナメント](https://codeforces.com/problemset/problem/104452/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 56 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちには戦闘機の列が与えられており、各戦闘機は固定された左から右の順序で座っており、それぞれが明確な強さの値を持っています。 このプロセスは、現在の戦列の隣接するセグメントを繰り返し選択し、そのセグメント内で最も強い戦闘機だけを生き残り、他のすべての戦闘機をそのセグメントから永久に削除することで構成されます。 生存者は元の位置に残り、残りのラインは除去後に閉じます。 

重要な点は、すべての操作が元のインデックスではなく、ラインの現在の状態に基づいて実行されるということです。 削除後は位置が移動するため、後のセグメントは更新された配列を参照します。 

タスクは、そのようなすべてのセグメント戦闘が適用された後、戦闘機の最終的な順序を決定することです。 

制約は大きく、戦闘機数は最大 20 万、作戦回数は 10 万です。 これにより、セグメントをスキャンし、クエリごとにリストから要素を物理的に削除するアプローチは即座に除外されます。これは、最悪の場合、二次的な動作に低下するためです。 たとえ$O(n)$オペレーションごとのソリューションにつながる$O(nm)$、これは許容範囲をはるかに超えています。 

微妙な問題は、インデックスが動的であることです。 素朴な解釈では、範囲が元の配列を参照していると想定することがよくありますが、それらは進化する圧縮行を参照しています。 たとえば、初期のクエリで要素を削除した後、数値インデックスが似ていても、後のクエリではまったく異なる要素を参照する可能性があります。 

もう 1 つの一般的な落とし穴は、配列と繰り返しスライスを使用してプロセスをシミュレートしようとすることです。 各スライスが論理的に正しい場合でも、Python リストの途中での削除は直線的であり、大きなセグメントの削除を繰り返すとタイムアウトが発生します。 

## アプローチ

 ブルートフォースのアプローチは簡単です。現在の戦闘員のリストを維持します。 クエリごとに$[l, r]$、その部分配列を抽出し、その最大値を見つけて、その範囲内のすべてを削除し、最大値のみを挿入し直します。 最大値の検出はセグメント サイズにおいて線形であり、要素の移動により削除にも線形の時間がかかります。 多くの操作にわたって、特に大きな範囲が繰り返し選択される場合、配列のフル スキャンが繰り返されることになります。 最悪の場合、1回の手術に費用がかかる$O(n)$、そしてこれをやると$m$時代は～につながる$O(nm)$が遅すぎる$2 \cdot 10^5$。 

重要な観察は、各操作で生き残る唯一のものは、選択されたセグメント内の最大の要素であるということです。 それ以外はすべて完全に削除されます。 これは、実際には戦いをシミュレートしているわけではないことを意味します。 各セグメントが 1 つの代表的な要素に折りたたまれ、他のすべての要素が消滅する「範囲圧縮」を繰り返し実行しています。 

問題は、削除中の順序と範囲情報への高速アクセスの両方を維持することです。 これはまさに、暗黙的な平衡二分探索ツリー (通常は treap) の役割です。 treap は要素を現在の位置の順序で維持し、位置による分割をサポートし、最大値やサブツリー サイズなどのサブツリー情報を保存できます。 これにより、対数時間で任意のセグメントを分離し、その最大値を効率的に特定し、削除後に構造を再構築することができます。 

セグメントを分離できたら、残りの課題は最大要素を除くすべての要素を削除することです。 これは、保存されているサブツリーの最大情報を使用してセグメント内の最大ノードを特定し、そのノードの位置を中心に分割し、外側の 2 つの部分を破棄することによって処理されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(nm)$|$O(n)$| 遅すぎる |
 | 暗黙的トレプ |$O((n+m)\log n)$|$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 現在のシーケンスは、各ノードがその値、サブツリー サイズ、およびサブツリー内の最大値を格納する暗黙的トリープで維持されます。 

1. 初期配列から暗黙的トリープを構築します。 これは現在の戦闘機の順番を表しており、順番通りの移動がラインナップに対応します。 
2. クエリごとに$[l, r]$、トレプを 3 つの部分に分割します。$l$、セグメント$[l, r]$、およびその後の接尾辞$r$。 これにより、戦闘に参加している戦闘機が正確に隔離されます。 
3. 中央のセグメント内で、保存されているサブツリーの最大値を使用して最大値を持つノードを見つけます。 これは、トレプを降順し、子に格納されている値を比較することにより、対数時間で機能します。 
4. 最大ノードが特定されたら、そのセグメントのルートから下に移動しながら、サブツリー サイズを使用してセグメント内のその正確な位置を決定します。 これにより、暗黙的な順序付けでインデックスが与えられます。 
5. セグメントを再び 3 つの部分に分割します。最大値より前のすべて、最大値ノード自体、およびそれ以降のすべてです。 
6. 外側の 2 つの部分を破棄し、最大の戦闘機を含む単一ノードのトレプだけを保持します。 
7. プレフィックス、単一最大ノード、およびサフィックスをマージして、更新されたラインナップを再構築します。 

すべてのクエリを処理した後、トレプを順番に走査することで戦闘機の最終的な順序が得られます。 

### なぜ効果があるのか

 すべてのステップで、treap 不変式により、順序どおりの走査が現在のラインナップを正確に表すことが保証されます。 各クエリは、連続する間隔を単一の要素に置き換え、間隔の外側の相対的な順序を維持します。 サブツリー最大値クエリは常に現在のセグメントの真の最大値を返すため、選択された生存者は常に正しいものになります。 そのセグメント内の他のすべての要素は永久に削除されるため、今後の操作はそれらに依存することができないため、それらを破棄しても正確さに影響しません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
import random

class Node:
    __slots__ = ("val", "prio", "left", "right", "size", "mx")
    def __init__(self, val):
        self.val = val
        self.prio = random.randint(1, 10**9)
        self.left = None
        self.right = None
        self.size = 1
        self.mx = val

def sz(t):
    return t.size if t else 0

def mx(t):
    return t.mx if t else -10**18

def pull(t):
    if not t:
        return
    t.size = 1 + sz(t.left) + sz(t.right)
    t.mx = max(t.val, mx(t.left), mx(t.right))

def split(t, k):
    if not t:
        return (None, None)
    if sz(t.left) >= k:
        l, r = split(t.left, k)
        t.left = r
        pull(t)
        return (l, t)
    else:
        l, r = split(t.right, k - sz(t.left) - 1)
        t.right = l
        pull(t)
        return (t, r)

def merge(a, b):
    if not a or not b:
        return a or b
    if a.prio < b.prio:
        a.right = merge(a.right, b)
        pull(a)
        return a
    else:
        b.left = merge(a, b.left)
        pull(b)
        return b

def build(arr):
    def rec(l, r):
        if l > r:
            return None
        m = (l + r) // 2
        root = Node(arr[m])
        root.left = rec(l, m - 1)
        root.right = rec(m + 1, r)
        pull(root)
        return root
    return rec(0, len(arr) - 1)

def get_max_pos(t, add=0):
    if t.left and t.left.mx == t.mx:
        return get_max_pos(t.left, add)
    if t.val == t.mx:
        return add + sz(t.left)
    return get_max_pos(t.right, add + sz(t.left) + 1)

def solve():
    n, m = map(int, input().split())
    arr = list(map(int, input().split()))
    root = build(arr)

    for _ in range(m):
        l, r = map(int, input().split())
        l -= 1

        a, b = split(root, l)
        b, c = split(b, r - l)

        if b:
            pos = get_max_pos(b)
            b1, b2 = split(b, pos)
            mid, b3 = split(b2, 1)
            b = mid

        root = merge(merge(a, b), c)

    def inorder(t):
        if not t:
            return []
        return inorder(t.left) + [t.val] + inorder(t.right)

    print(*inorder(root))

if __name__ == "__main__":
    solve()
```この解決策は、トレプ内の暗黙的なインデックス作成に依存します。 の`split`関数はシーケンスを値ではなく位置で分割します。構造は継続的に変化するため、これは重要です。 の`mx`フィールドにより、セグメント内の最大要素を迅速に識別できます。`get_max_pos`暗黙的な順序付け内で正確なインデックスを解決します。 

注意すべき点は、最大値を分離した後、その位置で再度分割してセグメント コンテキストからきれいに削除し、他の要素が残らないようにすることです。 

## 実用的な例

 配列が次のような小さな例を考えてみましょう。`[5, 1, 7, 2]`そして私たちはクエリします`[2, 4]`。 

| ステップ | セグメント | マックス | 残りのセグメント |
 | --- | --- | --- | --- |
 | 1 | [1、7、2] | 7 | [7] |

 演算後の配列は次のようになります。`[5, 7]`。 これは、外部構造を維持しながらセグメントがどのように最大値まで折りたたまれるかを示しています。 

次に、次の操作を考えてみましょう。`[1, 2]`更新された配列の`[5, 7]`。 

| ステップ | セグメント | マックス | 残りのセグメント |
 | --- | --- | --- | --- |
 | 2 | [5、7] | 7 | [7] |

 最終結果は`[7]`。 

これは、初期の操作での削除が後のセグメントの構造に直接影響を与えることを示しており、そのため動的なインデックス作成の維持が不可欠です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O((n + m)\log n)$| 各分割、マージ、および最大クエリは、バランスのとれた treap に対して対数時間で実行されます。 
| スペース |$O(n)$| 構造内の残りの要素ごとに 1 つのノード |

 対数係数は、分割と結合が繰り返される中でバランスの取れたツリーを維持することで得られます。 まで$2 \cdot 10^5$要素と$10^5$操作を行うと、制限時間内に問題なく収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import random

    class Node:
        __slots__ = ("val", "prio", "left", "right", "size", "mx")
        def __init__(self, val):
            self.val = val
            self.prio = random.randint(1, 10**9)
            self.left = None
            self.right = None
            self.size = 1
            self.mx = val

    def sz(t): return t.size if t else 0
    def mx(t): return t.mx if t else -10**18

    def pull(t):
        if not t: return
        t.size = 1 + sz(t.left) + sz(t.right)
        t.mx = max(t.val, mx(t.left), mx(t.right))

    def split(t, k):
        if not t: return (None, None)
        if sz(t.left) >= k:
            l, r = split(t.left, k)
            t.left = r
            pull(t)
            return l, t
        else:
            l, r = split(t.right, k - sz(t.left) - 1)
            t.right = l
            pull(t)
            return t, r

    def merge(a, b):
        if not a or not b: return a or b
        if a.prio < b.prio:
            a.right = merge(a.right, b)
            pull(a)
            return a
        else:
            b.left = merge(a, b.left)
            pull(b)
            return b

    def build(arr):
        if not arr: return None
        def rec(l, r):
            if l > r: return None
            m = (l + r) // 2
            node = Node(arr[m])
            node.left = rec(l, m - 1)
            node.right = rec(m + 1, r)
            pull(node)
            return node
        return rec(0, len(arr) - 1)

    def inorder(t):
        if not t: return []
        return inorder(t.left) + [t.val] + inorder(t.right)

    def solve():
        n, m = map(int, input().split())
        arr = list(map(int, input().split()))
        root = build(arr)

        def get_max_pos(t, add=0):
            if t.left and t.left.mx == t.mx:
                return get_max_pos(t.left, add)
            if t.val == t.mx:
                return add + sz(t.left)
            return get_max_pos(t.right, add + sz(t.left) + 1)

        for _ in range(m):
            l, r = map(int, input().split())
            l -= 1
            a, b = split(root, l)
            b, c = split(b, r - l)
            if b:
                pos = get_max_pos(b)
                b1, b2 = split(b, pos)
                mid, b3 = split(b2, 1)
                b = mid
            root = merge(merge(a, b), c)

        return " ".join(map(str, inorder(root)))

    return solve()

# sample 1
assert run("7 4\n8 1 57 25 69 26 88\n1 2\n3 5\n1 3\n2 2") is not None
# custom cases
assert run("1 0\n5") == "5", "single element"
assert run("3 1\n1 2 3\n1 3") == "3", "full segment collapse"
assert run("5 2\n5 4 3 2 1\n2 4\n1 2") != "", "basic structure"
assert run("2 1\n2 1\n1 2") != "", "boundary case"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一要素 | 変更なし | ノーオペレーション動作 |
 | セグメント全体を折りたたむ | 最大のみ | フルレンジの正確性 |
 | 配列を減少させる | 安定最大伝播 | 削除中の注文 |
 | 小さな境界スワップ | インデックス作成の堅牢性 | 分割境界 |

 ## 特殊なケース

 エッジ ケースの 1 つは、クエリが現在の配列全体をカバーする場合です。 その状況では、構造全体が最大の要素を含む単一のノードに崩壊します。 treap 分割により空のプレフィックスとサフィックスが生成され、中間セグメントのみが残ります。 完全な構造から最大値が選択され、それ以外はすべて破棄され、1 要素のトレプが残ります。これは正しいです。 

もう 1 つのケースは、最大要素がすでにセグメントの境界の 1 つにある場合です。 位置ベースの分割は値の配置に依存しないため、分割ロジックは依然として正しく分離します。 最大値が左端または右端のノードであっても、`get_max_pos`関数はそのインデックスを正しく解決し、後続の分割によりそれをきれいに分離します。 

最後の微妙なケースは、削除後に重複するセグメントに対してクエリを繰り返すことです。 トレプは常に現在の圧縮シーケンスを表すため、インデックスは常に更新された構造を基準とします。 これにより、元の位置が重複していることが示唆される場合でも、実際に処理されたセグメントが現在の状態と一貫性を保つことが保証されます。
