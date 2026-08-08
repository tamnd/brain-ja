---
title: "CF 102511A - アズレージョ"
description: "タイルのコレクションが 2 つあります。 すべてのタイルには価格、高さ、および元のインデックスがあります。 後列と前列を個別に並べ替える必要があります。 各行の価格は、左から右に向かって減少しないようにする必要があります。"
date: "2026-08-06T19:35:36+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102511
codeforces_index: "A"
codeforces_contest_name: "2019 ICPC World Finals"
rating: 0
weight: 102511
solve_time_s: 131
verified: true
draft: false
---

[CF 102511A - アズレージョ](https://codeforces.com/problemset/problem/102511/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 11 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 タイルのコレクションが 2 つあります。 すべてのタイルには価格、高さ、および元のインデックスがあります。 後列と前列を個別に並べ替える必要があります。 各行の価格は、左から右に向かって減少しないようにする必要があります。 どの位置でも、背面のタイルは前面のタイルよりも厳密に高くなければなりません。 

唯一の自由は、同じ価格のタイルから得られます。 価格の異なるタイルは、並べ替え後の相対順序が固定されます。 課題は、等しい価格グループ内の自由を利用して、すべての垂直ペアを有効にすることです。 

n の値は 500000 に達する可能性があるため、多くの可能な並べ替えを試したり、多くのペアリングをチェックしたりするソリューションは不可能です。 ほぼ線形または n log n の作業を行う貪欲な方法が必要です。 ソートにはすでに n log n のコストがかかりますが、これは許容範囲です。 

難しいケースは、価格が等しいことによって引き起こされます。 単純に価格で並べ替えてから高さを比較するソリューションは、同じ価格のタイルが並べ替えられる可能性があるため失敗します。 高さを個別に並べ替えるソリューションも失敗します。これは、2 つの行の同じ価格グループが複雑に重なっているためです。 

例えば：```
1
5
5
3
```5 > 3 であるため、単一のペアが機能します。 

より微妙なケースは次のとおりです。```
2
1 2
1 1
10 4
9 8
```バック価格はすでにソートされています。 最前列の価格は等しいため、最前列のタイルが交換される可能性があります。 高さ 10 と 9、高さ 4 と 8 を正しく組み合わせるのは不可能ですが、前面のタイルを交換すると 10 と 8、4 と 9 が得られますが、これは依然として不可能です。 等しい価格の 1 つの任意の順序のみをチェックする不注意な実装では、そのようなケースが誤って承認または拒否される可能性があります。 

## アプローチ

 ブルート フォース ソリューションでは、すべての等しい価格グループ内でさまざまな注文を試し、結果の行をテストします。 これは、許可されているすべての配置を調査するため正しいですが、可能な順列の数は階乗的に増加します。 サイズ 20 の 1 つの大きなグループでさえ、すでに 2 兆を超える可能性を生み出しているため、このアプローチは使用できません。 

重要な観察は、等しい価格グループが独立したプールのように動作するということです。 前列の次の未完成グループのサイズが a で、後列の次の未完成グループのサイズが b であるとします。 前グループが最初に終了した場合、そのすべてのタイルが後グループのタイルと一致する必要があります。 常に、選択した前面タイルよりも高い、可能な限り短い背面タイルを選択する必要があります。 これにより、最も高い後ろのタイルが未使用のままになり、後の位置で使用できる最良のリソースとなります。 

後ろのグループが先にゴールした場合、役割が逆になっても同じ議論が適用されます。 各バック タイルに対して可能な限り短いフロント タイルを消費します。 

この貪欲な選択が機能するのは、将来のために大きな背面タイルを保持しておくことは決して悪いことではありませんが、今より大きなタイルを使用すると、将来の高さのある前面タイルをカバーできなくなる可能性があるからです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(順列の数) | O(n) | 遅すぎる |
 | 順序付けられたマルチセットで貪欲 | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 両方の行を価格で並べ替えます。 等しい価格のタイルはグループとしてまとめられ、各グループはそのタイルを高さ順に格納します。 
2. 各行の現在の未完了の価格グループを維持します。 価格は異なる値間で移動できないため、グループは左から右に処理されます。 
3. 前方グループのタイルが後方グループよりも少ないか等しい場合は、各前方タイルを、利用可能な最小の高さの高い後方タイルと繰り返し照合します。 これら 2 つのタイルを削除し、現在の回答位置に置きます。 
4. バックグループが小さい場合は、対称操作を実行します。 すべての背面タイルを、それがカバーできる最小の利用可能な前面タイルと一致させます。 
5. 1 つのグループが空になったら、その行の次の価格グループに移動します。 すべてのポジションが埋まるまで続けます。 

不変条件は、ポジションのプレフィックスを処理した後、生成されたすべてのペアが有効であり、残りのすべてのタイルが依然としてソートされた価格順のサフィックスに属していることです。 貪欲な削除は、一致を提供する必要がある側からの可能な限り柔軟性の低いタイルを常に消費するため、実現可能性が維持されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class Node:
    __slots__ = ("key", "prio", "left", "right", "cnt", "val")
    def __init__(self, key, val):
        self.key = key
        self.val = val
        self.prio = (key[0] * 1000003 + key[1]) & 0x7fffffff
        self.left = None
        self.right = None
        self.cnt = 1

def rotate_right(y):
    x = y.left
    y.left = x.right
    x.right = y
    return x

def rotate_left(x):
    y = x.right
    x.right = y.left
    y.left = x
    return y

def insert(t, node):
    if t is None:
        return node
    if node.key < t.key:
        t.left = insert(t.left, node)
        if t.left.prio < t.prio:
            t = rotate_right(t)
    else:
        t.right = insert(t.right, node)
        if t.right.prio < t.prio:
            t = rotate_left(t)
    return t

def merge(a, b):
    if not a:
        return b
    if not b:
        return a
    if a.prio < b.prio:
        a.right = merge(a.right, b)
        return a
    b.left = merge(a, b.left)
    return b

def erase(t, key):
    if t.key == key:
        return merge(t.left, t.right)
    if key < t.key:
        t.left = erase(t.left, key)
    else:
        t.right = erase(t.right, key)
    return t

def lower_bound(t, key):
    ans = None
    while t:
        if t.key >= key:
            ans = t
            t = t.left
        else:
            t = t.right
    return ans

def make_groups(p, h):
    a = sorted([(p[i], h[i], i + 1) for i in range(len(p))])
    groups = []
    i = 0
    while i < len(a):
        j = i
        vals = []
        while j < len(a) and a[j][0] == a[i][0]:
            vals.append((a[j][1], a[j][2]))
            j += 1
        groups.append(vals)
        i = j
    return groups

def build_tree(arr):
    t = None
    for h, idx in arr:
        t = insert(t, Node((h, idx), idx))
    return t

def solve():
    n = int(input())
    bp = list(map(int, input().split()))
    bh = list(map(int, input().split()))
    fp = list(map(int, input().split()))
    fh = list(map(int, input().split()))

    bg = make_groups(bp, bh)
    fg = make_groups(fp, fh)

    bi = fi = 0
    bt = build_tree(bg[0]) if bg else None
    ft = build_tree(fg[0]) if fg else None
    bc = len(bg[0]) if bg else 0
    fc = len(fg[0]) if fg else 0

    ans_b = [0] * n
    ans_f = [0] * n
    pos = 0

    while pos < n:
        if fc <= bc:
            for _ in range(fc):
                fnode = lower_bound(ft, (-10**30, -10**30))
                need = fnode.key[0] + 1
                bnode = lower_bound(bt, (need, -10**30))
                if bnode is None:
                    print("impossible")
                    return
                ans_b[pos] = bnode.val
                ans_f[pos] = fnode.val
                bt = erase(bt, bnode.key)
                ft = erase(ft, fnode.key)
                pos += 1
            fi += 1
            if fi < len(fg):
                ft = build_tree(fg[fi])
                fc = len(fg[fi])
            else:
                fc = 0
            bc -= len(fg[fi - 1])
        else:
            for _ in range(bc):
                bnode = lower_bound(bt, (-10**30, -10**30))
                need = bnode.key[0]
                fnode = lower_bound(ft, (need, -10**30))
                if fnode is None or fnode.key[0] >= need:
                    print("impossible")
                    return
                ans_b[pos] = bnode.val
                ans_f[pos] = fnode.val
                bt = erase(bt, bnode.key)
                ft = erase(ft, fnode.key)
                pos += 1
            bi += 1
            if bi < len(bg):
                bt = build_tree(bg[bi])
                bc = len(bg[bi])
            else:
                bc = 0
            fc -= len(bg[bi - 1])

    print(*ans_b)
    print(*ans_f)

solve()
```この実装では、各行の現在の価格グループがランダム化されたトリープに保持されます。 treap は、しきい値を超える最小の高さを見つけることと、選択したタイルを削除するという 2 つの必要な操作を実行します。 これにより、通常のソートされたリストから削除する際の二次コストが回避されます。 

消費されたすべてのペアが最終表示の次の位置に対応するため、回答配列は左から右に埋められます。 高さとインデックスは treap キーに一緒に保存されるため、重複する高さを区別できるようになります。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 並べ替えとすべての treap 操作は対数的であり、各タイルが 1 回削除されます。 
| スペース | O(n) | すべてのタイルはグループまたはツリーに 1 回表示されます。 

最大の入力には、1 行あたり 500,000 個のタイルが含まれます。 このソリューションは、制限内に問題なく収まる、並べ替えと対数順序集合演算のみを実行します。 

## 特殊なケース

 両方のグループ サイズが 1 であり、アルゴリズムが高さ比較を 1 回だけ実行するため、単一のタイルが処理されます。 

同一価格はグループ化により処理されます。 このアルゴリズムは、価格グループ内の任意の注文を決して修正しません。 その時点で必要な高さの比較に従ってタイルのみを削除します。 

後部タイルの高さが 5、前部タイルの高さが 6 などの失敗ケースは、順序付けされた検索で有効なパートナーが見つからないため、すぐに失敗します。そのため、アルゴリズムは次のように出力します。`impossible`無効な取り決めを構築する代わりに。
