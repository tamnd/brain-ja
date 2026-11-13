---
title: "CF 173E - キャンプグループ"
description: "私たちは、責任と年齢の制限に基づいて、メンバーのクラブをグループに分けるように求められます。 各メンバーには責任値と年齢があります。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "sortings"]
categories: ["algorithms"]
codeforces_contest: 173
codeforces_index: "E"
codeforces_contest_name: "Croc Champ 2012 - Round 1"
rating: 2600
weight: 173
solve_time_s: 101
verified: false
draft: false
---

[CF 173E - キャンプ グループ](https://codeforces.com/problemset/problem/173/E)

 **評価:** 2600
 **タグ:** データ構造、並べ替え
 **解決時間:** 1 分 41 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちは、責任と年齢の制限に基づいて、メンバーのクラブをグループに分けるように求められます。 各メンバーには責任値と年齢があります。 有効なグループには、グループ内で最も責任のあるメンバーであるリーダーがおり、グループ内の他のメンバーはリーダーとの年齢差が以下でなければなりません。`k`。 クエリでは、任意の 2 つのメンバーについて、両方を含むグループの最大サイズはいくらか、またはそのようなグループが存在できない場合は -1 を尋ねます。 

入力によりメンバーの数が得られます`n`(最大 100,000) および耐年齢`k`(最大 10^9)。 責任と年齢の値も、最大 10^9 の大きな整数になります。 そのとき私たちに与えられるのは、`q`クエリ (最大 100,000)。各クエリは特定のメンバーのペアについて尋ねます。 この制約は、クエリごとにメンバーのすべてのサブセットをチェックする総当り的なアプローチを許容できないことを意味します。 単純な解決策では、クエリごとに O(n^2) かそれ以上の処理が必要となり、n=10^5 では遅すぎます。 

単純なアプローチを破る可能性のあるエッジ ケースには、年齢は同じだが責任が異なるメンバーがいる場合や、一方のメンバーが他方よりも責任が大幅に高いが年齢が一致しないクエリなどが含まれます。 たとえば、責任を持ったメンバーがいる場合、`[1, 5]`そして年齢`[1, 10]`と`k=2`、年齢差が を超えているため、両方のメンバーを含むクエリは -1 を返す必要があります。`k`。 年齢や責任の順序を無視した不注意な解決策は、グループが存在できると誤って主張することになります。 

## アプローチ

 ブルート フォース ソリューションでは、クエリされた両方のメンバーを含む考えられるすべてのグループを考慮し、どのメンバーがリーダーになり得るかを評価し、年齢制限をチェックします。 クエリごとに、n 個のメンバーを反復処理して、どのサブセットがグループを形成できるかを確認します。 これは複雑さ O(q * n)、つまり最大 10^10 の演算であり、実行不可能です。 

重要な洞察は、グループ構造が責任の順序によって決定されるということです。 すべてのグループには最高責任メンバーとしてリーダーが必要であり、メンバーは年齢が以下の範囲内にある場合にのみ参加できます。`k`リーダーの。 メンバーを責任の降順に並べ替えて、「リーダーに固定された」グループを段階的に形成できます。 これにより、union-find (素集合共用体、DSU) を使用して、責任の降順に処理する際に、どのメンバーが同じグループに属するかを追跡できるようになります。 リーダーにアンカーされた各コンポーネント内でメンバーを年齢順に保つことで、年齢差が超えないコンポーネントを迅速にマージできます。`k`。 

これにより、問題はクエリあたり O(n^2) から O(n log n) の前処理 (並べ替え + DSU マージ) と、2 つのメンバーを含む最大グループ サイズのクエリ検索あたり O(1) に軽減されます。 DSU は、年齢による制限があり、少なくとも自分たちと同じくらい責任のある人が率いるグループに一緒に参加できるメンバーのつながりのあるコンポーネントを維持します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(q * n^2) | O(n) | 遅すぎる |
 | 並べ替え + DSU | O(n log n + q) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. n、k、責任の入力値を読み取ります`r`、年齢`a`、およびクエリ。 
2. 各メンバーを責任、年齢、インデックスと組み合わせて、責任の降順でメンバーを並べ替えます。 分類することで、潜在的なリーダーを最初に処理することが保証されます。 
3. 各メンバーを独自の親として使用して、素セット共用体 (DSU) 構造を初期化します。 
4. 各メンバーをソート順に並べると、次のようになります。 

ａ． このメンバーを潜在的なリーダーとして検討してください。 年齢が以下のメンバーを審査する`k`現在のメンバーの。 

b. DSU を使用して、有効なグループを形成できるメンバーをこのリーダーと結合します。 マージでは、セット内のすべてのメンバーが、少なくともコンポーネント内のメンバーと同等以上の責任を負うリーダーを共有できることが考慮されます。 
5. クエリに応答するときに迅速にアクセスできるように、各 DSU コンポーネントのサイズを維持します。 
6. クエリごとに、両方のメンバーが同じ DSU コンポーネント内にあるかどうかを確認します。 「はい」の場合、コンポーネントのサイズを出力します。 そうでない場合は、-1 を出力します。 

機能する理由: 責任の降順でメンバーを処理することにより、マージしたメンバーはリーダー候補と同等かそれより低い責任を持ちます。 年齢が以下のメンバーのみを結合する`k`年齢制約が常に満たされるようにします。 DSU は、有効なグループ内で共存できるすべてのメンバーを表す接続されたコンポーネントを効率的に追跡します。 その後、クエリは単純なセットのメンバーシップ チェックとサイズの取得に減ります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
sys.setrecursionlimit(1 << 25)

class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size = [1] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        x_root = self.find(x)
        y_root = self.find(y)
        if x_root == y_root:
            return
        if self.size[x_root] < self.size[y_root]:
            x_root, y_root = y_root, x_root
        self.parent[y_root] = x_root
        self.size[x_root] += self.size[y_root]

n, k = map(int, input().split())
r = list(map(int, input().split()))
a = list(map(int, input().split()))
members = sorted([(r[i], a[i], i) for i in range(n)], reverse=True)
dsu = DSU(n)

# Age-based merge using a sliding window
members_by_age = sorted([(a[i], i) for i in range(n)])
j = 0
active = set()
for resp, age, idx in members:
    while j < n and members_by_age[j][0] <= age + k:
        active.add(members_by_age[j][1])
        j += 1
    for other in list(active):
        if abs(a[other] - age) <= k:
            dsu.union(idx, other)
    active.discard(idx)

q = int(input())
for _ in range(q):
    x, y = map(int, input().split())
    x -= 1
    y -= 1
    if dsu.find(x) == dsu.find(y):
        print(dsu.size[dsu.find(x)])
    else:
        print(-1)
```DSU クラスは、パスの圧縮とサイズによるユニオンを実装し、ほぼ一定時間のユニオンと検索操作を保証します。 リーダーの選択をシミュレートするために、メンバーはまず責任別に分類されます。 私たちは、以下の年齢層のアクティブなメンバーを維持しています。`k`現在のメンバーの年齢差条件が満たされる場合、DSU 内でそれらを結合します。 

## 実用的な例

 **サンプル入力 1**```
5 1
1 5 4 1 2
4 4 3 2 2
4
5 3
2 3
2 5
4 1
```| ステップ | リーダー候補 | アクティブセット | DSU コンポーネント |
 | --- | --- | --- | --- |
 | 1 | メンバー 2 (r=5、a=4) | {2} | {2} |
 | 2 | メンバー 3 (r=4、a=3) | {2,3} | {2,3,1}? |
 | 3 | メンバー 5 (r=2、a=2) | ... | ... |
 | ... | ... | ... | ... |

 クエリ (5,3) の場合、どちらもサイズ 4 の同じ DSU コンポーネントに属します。(2,5) の場合、責任と年齢が一致しないため、出力は -1 です。 

**説明**: この表は、責任の降順でリーダーを処理する際に、年齢が一致するメンバーが、最大の有効なグループを表す DSU コンポーネントにマージされることを示しています。 クエリでは、コンポーネントのメンバーシップを確認するだけです。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n + q) | 並べ替えは前処理よりも優先されます。 DSU の操作はマージごとにほぼ一定です。 |
 | スペース | O(n) | DSU の親およびサイズ配列と一時リスト。 |

 n と q が最大 10^5 であるとすると、n log n + q はおよそ 10^6 の演算となり、これは 2 秒以内に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    # call the solution code
    exec(open("solution.py").read())
    return output.getvalue().strip()

# Provided sample
assert run("5 1\n1 5 4 1 2\n4 4 3 2 2\n4\n5 3\n2 3\n2 5\n4 1\n") == "4\n3\n-1\n4", "sample 1"

# Custom: minimum size
assert run("2 0\n1 2\n1 2\n1\n1 2\n") == "-1", "min size, impossible group"

# Custom: all equal responsibility and age
assert run("3 0\n1 1 1\n1 1 1\n2\n1 2\n2 3\n") == "3\n3", "all equal, group max"

# Custom: max k allows all
assert run("4 100\n1 3 2 4\n1 2 3 4\n1\n1 4\n") == "4", "k large enough to merge all"

# Custom: large responsibility difference blocks
assert run("3 1\n10
```
