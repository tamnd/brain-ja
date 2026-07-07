---
title: "CF 102961D - コンサートチケット"
description: "この仕事は、定額のコンサートチケットの市場と、次々に到着する購入者を中心に展開します。 各チケットには価格があり、各購入者には支払ってもよい最大金額があります。"
date: "2026-07-04T06:50:48+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102961
codeforces_index: "D"
codeforces_contest_name: "CSES Problem Set: Sorting and Searching"
rating: 0
weight: 102961
solve_time_s: 51
verified: true
draft: false
---

[CF 102961D - コンサート チケット](https://codeforces.com/problemset/problem/102961/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 51 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 この仕事は、定額のコンサートチケットの市場と、次々に到着する購入者を中心に展開します。 各チケットには価格があり、各購入者には支払ってもよい最大金額があります。 購入者が到着すると、価格が予算を超えない 1 枚のチケットを選択します。 そうした入手可能なチケットすべての中で、彼らは常に自分が買える範囲で最も高価なものを選びます。 チケットは一度販売すると消滅し、再度使用することはできません。 

入力では、チケット価格の初期マルチセットと、その後に予算制約のある購入者のリストが記述されます。 各購入者に対して、最終的にどのチケットを購入するかを判断するか、適切なチケットが存在しないことを報告する必要があります。 

この構造は、挿入順序と削除の両方が重要であることをすぐに示唆しています。 重要な操作は、チケットがしきい値以下に存在するかどうかをクエリするだけではなく、動的セットの下で実行可能な最良の候補を繰り返し見つけて削除することです。 

チケットと購入者の数が 200,000 程度かそれに近い場合、購入者ごとにチケットのリスト全体をスキャンするアプローチは二次的な動作につながります。 クエリごとの単純な線形スキャンでは、残りのすべてのチケットをチェックする必要があり、最悪の場合、およそ 10^10 の操作が生成されます。これは、2 秒の実行ウィンドウでの実行可能な制限をはるかに超えています。 

これにより、フル スキャンの繰り返しや、クエリごとのフィルタリングによる単純な並べ替えはすでに排除されています。 

いくつかの特殊なケースが自然に現れます。 

1 つは、すべてのチケットが各購入者の予算よりも高価である場合です。 例えばチケットは、`[100, 200, 300]`そして購入者は`[10, 50]`。 正しい出力は次のとおりです`-1 -1`。 「候補者が見つからない」ケースを適切に処理しない不注意な実装では、誤って最小のチケットが返されたり、以前の回答が再利用されたりする可能性があります。 

もう 1 つは、複数のチケットが同じ価格を共有する場合です。 たとえば、チケット`[50, 50, 50]`そして購入者`[50, 50]`。 各購入者は 1 枚の異なるチケットを取得する必要があります。 使用済みのチケットをその構造から正しく削除しないソリューションでは、同じ論理チケットが複数回繰り返し割り当てられる可能性があります。 

3 番目の微妙なケースは、購入者が予算の降順に到着する場合です。これにより、適切な削除サポートがなければ、並べ替えられたリストに依存するソリューションが騙される可能性があります。 たとえば、チケット`[20, 40, 60]`そして購入者`[70, 50, 30]`各割り当ての後に状態が更新され、徐々に小さな一致が必要になります。 

## アプローチ

 直接的なブルート フォース ソリューションでは、残りのチケットのリストが維持されます。 購入者ごとに、すべてのチケットを反復処理し、予算を超えていないチケットをチェックし、その中から最大のものを選択します。 チケットを選択したら、リストからチケットを削除します。 

これが機能するのは、説明どおりにプロセスを正確にシミュレートし、構築による正確性が維持されるためです。 ただし、コストの大部分は、各購入者のチケット セット全体をスキャンすることによって決まります。 と`n`チケットと`m`購入者、これは次のことにつながります`O(nm)`時間の複雑さ。 両方が大きい場合、これは管理できなくなります。 

非効率性は、チケット セットが削除によって段階的に変更されるだけであるにもかかわらず、最適な候補を最初から繰り返し検索することから発生します。 私たちが必要とする構造は、値を超えない最大の要素を見つけることと、それを削除するという 2 つの操作を効率的にサポートする構造です。 

これはまさに、秩序だったバランスの取れた構造が提供するものです。 チケットを並べ替えて、先行クエリをサポートする構造でチケットを維持すると、各購入者は右端のチケット価格を見つけてサービスを受けることができます。`<= budget`。 その後、削除させていただきます。 バランスのとれた二分探索ツリーまたは順序付きマルチセットは、対数時間での両方の操作をサポートします。 

Python では、標準ライブラリはツリーベースのマルチセットを提供しないため、ソートされたコンテナを使用してシミュレートするか、競技プログラミングではより一般的には次を使用します。`bisect`フェンウィック ツリーまたはセグメント ツリーと組み合わせたソートされたリスト上で。 最もクリーンな概念的なソリューションは、圧縮された座標上のセグメント ツリーです。各ノードには利用可能な最大のインデックスまたはカウントが保存され、最適な実行可能なチケットをクエリして削除できるようになります。 

重要な観察は、価格は範囲内で静的ですが、可用性は動的に変化するため、削除の場合は「最適 <= x」クエリのみをサポートする必要があるということです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nm) | O(n) | 遅すぎる |
 | セグメントツリー / 順序構造 | O((n + m) log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 私たちは、チケットの価格を圧縮し、各価格のチケットが何枚残っているかを追跡する構造を構築することで、この問題を解決します。 

1. まず、すべてのチケット価格を並べ替えて、並べ替えられた一意の配列に圧縮します。 これにより、生の値の代わりにインデックスを操作できるようになります。 圧縮が役立つ理由は、実際の大きさではなく、相対的な順序のみを考慮しているためです。 
2. これらの圧縮インデックスに対して頻度配列を構築し、各価格レベルに存在するチケットの数を保存します。 これにより、重複が自然にキャプチャされます。 
3. この周波数配列上にセグメント ツリーを構築します。ここで、各ノードはそのセグメント内で利用可能なチケットの総数を保存します。 これにより、範囲内にチケットが存在するかどうかをすばやく判断できます。 
4. 予算のある各購入者向け`x`、圧縮された配列内で二分探索を行い、価格が以下の最大のインデックスを見つけます。`<= x`。 これにより、検索スペースが有効な候補のみに減ります。 
5. 範囲内のセグメントツリーをクエリします。`[0, idx]`チケットがまだ利用可能な右端の位置を見つけます。 このステップでは、最も手頃なチケットを特定します。 
6. そのような位置が存在しない場合は、次のように出力します。`-1`。 それ以外の場合は、対応するチケット価格を出力し、そのカウントを減分して、セグメント ツリーを更新します。 

これが効率的に機能する理由は、有効な価格境界の検索とその境界内の利用可能なチケットの検索が両方とも対数演算であり、各チケットが 1 回だけ削除されるためです。 

### なぜ効果があるのか

 セグメント ツリーはいつでも、残りのチケットのマルチセットを正確に表します。 クエリでは常に、正のカウントを持つ最大のインデックスが選択されます。これは、購入者の予算を超えない、入手可能な最も高価なチケットに正確に対応します。 更新ではチケットが削除されるだけなので、構造は単調に縮小し、将来のすべてのクエリの正確性が維持されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SegTree:
    def __init__(self, arr):
        self.n = len(arr)
        self.t = [0] * (4 * self.n)
        self.arr = arr
        self._build(1, 0, self.n - 1)

    def _build(self, v, l, r):
        if l == r:
            self.t[v] = 1
            return
        m = (l + r) // 2
        self._build(v*2, l, m)
        self._build(v*2+1, m+1, r)
        self.t[v] = self.t[v*2] + self.t[v*2+1]

    def _query(self, v, l, r, ql, qr):
        if ql > r or qr < l:
            return -1
        if l == r:
            return l if self.t[v] > 0 else -1
        if ql <= l and r <= qr:
            if self.t[v] == 0:
                return -1
            m = (l + r) // 2
            right = self._query(v*2+1, m+1, r, ql, qr)
            if right != -1:
                return right
            return self._query(v*2, l, m, ql, qr)

        m = (l + r) // 2
        right = self._query(v*2+1, m+1, r, ql, qr)
        left = self._query(v*2, l, m, ql, qr)
        return max(right, left)

    def update(self, v, l, r, idx):
        if l == r:
            self.t[v] -= 1
            return
        m = (l + r) // 2
        if idx <= m:
            self.update(v*2, l, m, idx)
        else:
            self.update(v*2+1, m+1, r, idx)
        self.t[v] = self.t[v*2] + self.t[v*2+1]

n, m = map(int, input().split())
tickets = list(map(int, input().split()))
buyers = list(map(int, input().split()))

vals = sorted(set(tickets))
idx = {v:i for i, v in enumerate(vals)}

freq = [0] * len(vals)
for t in tickets:
    freq[idx[t]] += 1

seg = SegTree(vals)
seg.t = seg.t  # structure initialized over presence; we adjust via updates

# rebuild tree properly with freq
def build(v, l, r):
    if l == r:
        seg.t[v] = freq[l]
        return
    m = (l + r) // 2
    build(v*2, l, m)
    build(v*2+1, m+1, r)
    seg.t[v] = seg.t[v*2] + seg.t[v*2+1]

build(1, 0, len(vals)-1)

def query_rightmost(v, l, r, ql, qr):
    if ql > r or qr < l or seg.t[v] == 0:
        return -1
    if l == r:
        return l
    m = (l + r) // 2
    res = query_rightmost(v*2+1, m+1, r, ql, qr)
    if res != -1:
        return res
    return query_rightmost(v*2, l, m, ql, qr)

out = []

for b in buyers:
    pos = bisect = None
    # binary search manually
    lo, hi = 0, len(vals) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if vals[mid] <= b:
            pos = mid
            lo = mid + 1
        else:
            hi = mid - 1

    if pos is None:
        out.append("-1")
        continue

    res = query_rightmost(1, 0, len(vals)-1, 0, pos)
    if res == -1:
        out.append("-1")
    else:
        out.append(str(vals[res]))
        # remove one ticket
        def upd(v, l, r, idx):
            if l == r:
                seg.t[v] -= 1
                return
            m = (l + r) // 2
            if idx <= m:
                upd(v*2, l, m, idx)
            else:
                upd(v*2+1, m+1, r, idx)
            seg.t[v] = seg.t[v*2] + seg.t[v*2+1]

        upd(1, 0, len(vals)-1, res)

print("\n".join(out))
```このソリューションでは、まずセグメント ツリーのインデックスが並べ替えられたチケットの値に対応するように価格を圧縮します。 二分検索ステップでは最も手頃な価格のインデックスが検索され、セグメント ツリー クエリではその範囲内でまだ入手可能な最良のチケットが検索されます。 チケットを出力した後、ポイント更新によってチケットのカウントが減らされるため、チケットは再利用できません。 

微妙な点は、二分探索とセグメント ツリー クエリの両方が必要であることです。 二分検索によりドメインが手頃な価格に制限され、セグメント ツリーにより削除時の可用性が強制されます。 

## 実用的な例

 ### 例 1

 チケットは`[5, 3, 7]`、購入者は`[4, 8, 3]`。 

| 購入者 | 予算 | 最大手頃な価格のインデックス | 選択されたチケット | 残りのマルチセット |
 | --- | --- | --- | --- | --- |
 | 1 | 4 | 3 (値 5 は大きすぎるため、インデックス 3) | 3 | [5、7] |
 | 2 | 8 | 2 | 7 | [5] |
 | 3 | 3 | 1 | -1 | [5] |

 このトレースは、構造が現在の縮小セットで利用可能な最良のチケットを常に選択する方法を示しています。 

### 例 2

 チケットは`[10, 10, 20]`、購入者は`[10, 10, 10]`。 

| 購入者 | 予算 | 入手可能なベスト | 選択されたチケット | 残りのマルチセット |
 | --- | --- | --- | --- | --- |
 | 1 | 10 | 10 | 10 | [10、20] |
 | 2 | 10 | 10 | 10 | [20] |
 | 3 | 10 | -1 | -1 | [20] |

 これは、繰り返される同じ価格が 1 つずつ消費される重複の正しい処理を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + m) log n) | 各購入者はバイナリ検索に加えてセグメント ツリーのクエリと更新を実行します。 
| スペース | O(n) | 圧縮された配列とセグメント ツリーのストレージ |

 対数係数により、数十万のチケットとクエリへの拡張性が保証され、一般的なコンテストの制限内に快適に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    n, m = map(int, input().split())
    tickets = list(map(int, input().split()))
    buyers = list(map(int, input().split()))

    vals = sorted(set(tickets))
    idx = {v:i for i, v in enumerate(vals)}

    freq = [0] * len(vals)
    for t in tickets:
        freq[idx[t]] += 1

    class Seg:
        def __init__(self):
            self.n = len(vals)
            self.t = [0] * (4*self.n)

        def build(self, v, l, r):
            if l == r:
                self.t[v] = freq[l]
                return
            m = (l+r)//2
            self.build(v*2,l,m)
            self.build(v*2+1,m+1,r)
            self.t[v]=self.t[v*2]+self.t[v*2+1]

        def query(self,v,l,r,ql,qr):
            if ql>r or qr<l or self.t[v]==0:
                return -1
            if l==r:
                return l
            m=(l+r)//2
            res=self.query(v*2+1,m+1,r,ql,qr)
            if res!=-1:
                return res
            return self.query(v*2,l,m,ql,qr)

        def upd(self,v,l,r,i):
            if l==r:
                self.t[v]-=1
                return
            m=(l+r)//2
            if i<=m:
                self.upd(v*2,l,m,i)
            else:
                self.upd(v*2+1,m+1,r,i)
            self.t[v]=self.t[v*2]+self.t[v*2+1]

    seg = Seg()
    seg.build(1,0,len(vals)-1)

    out=[]
    for b in buyers:
        pos=None
        lo,hi=0,len(vals)-1
        while lo<=hi:
            mid=(lo+hi)//2
            if vals[mid]<=b:
                pos=mid
                lo=mid+1
            else:
                hi=mid-1
        if pos is None:
            out.append("-1")
            continue
        res=seg.query(1,0,len(vals)-1,0,pos)
        if res==-1:
            out.append("-1")
        else:
            out.append(str(vals[res]))
            seg.upd(1,0,len(vals)-1,res)

    return "\n".join(out)

# provided samples
assert run("3 3\n5 3 7\n4 8 3\n") == "3\n7\n-1"

# custom cases
assert run("1 1\n10\n9\n") == "-1", "below all tickets"
assert run("1 2\n5\n5 5\n") == "5\n-1", "single ticket exhaustion"
assert run("4 4\n1 1 1 1\n1 1 1 1\n") == "1\n1\n1\n1", "all equal consumption"
assert run("3 3\n10 20 30\n5 15 25\n") == "-1\n10\n20", "boundary stepping"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 予算を下回るシングルチケット | -1 | 有効な選択肢の処理がありません |
 | 同じチケットを繰り返した | 5、-1 | 枯渇の正確性 |
 | すべて等しい値 | 繰り返し出力 | マルチセットのカウント |
 | しきい値を増やす | 段階的な選択 | 正しい貪欲マッチング |

 ## 特殊なケース

 すべてのチケットがすべての購入者の予算を上回っている場合、二分検索で範囲が有効なプレフィックスに狭まった後、セグメント ツリー クエリは常に空の状態を返します。 のような入力の場合`tickets = [100, 200]`そして`buyers = [10, 20]`、すべてのクエリ範囲は有効ですが、セグメント ツリーは可用性がゼロであると報告するため、`-1`一貫して出力します。 二分探索境界によってクエリ範囲が常に明確に定義されるため、この構造は無効なインデックスへのアクセスを試みることはありません。 

同一のチケットが複数存在する場合、更新のたびに 1 つのリーフの頻度が減少します。 のような場合`tickets = [50, 50]`の場合、最初のクエリでは圧縮に応じてインデックス 0 または 1 が検索され、更新後には 1 つだけが残ります。 2 番目のクエリは引き続き同じ範囲を検索しますが、すべてを使い果たす前に残りの出現を正しく返すようになりました。
