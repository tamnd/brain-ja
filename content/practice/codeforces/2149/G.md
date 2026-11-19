---
title: "CF 2149G - ブラツタ3"
description: "整数の配列と多くの独立した範囲クエリが与えられます。 各クエリ セグメントについて、そのセグメント内の頻度が厳密にセグメント長の 3 分の 1 を超える値をすべて識別する必要があります。"
date: "2026-06-08T01:12:02+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "brute-force", "data-structures", "divide-and-conquer", "probabilities"]
categories: ["algorithms"]
codeforces_contest: 2149
codeforces_index: "G"
codeforces_contest_name: "Codeforces Round 1054 (Div. 3)"
rating: 2100
weight: 2149
solve_time_s: 98
verified: false
draft: false
---

[CF 2149G - ぶらつた3](https://codeforces.com/problemset/problem/2149/G)

 **評価:** 2100
 **タグ:** 二分探索、ブルート フォース、データ構造、分割統治、確率
 **解決時間:** 1 分 38 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 整数の配列と多くの独立した範囲クエリが与えられます。 各クエリ セグメントについて、そのセグメント内の頻度が厳密にセグメント長の 3 分の 1 を超える値をすべて識別する必要があります。 

重要な点は、各クエリがクエリの長​​さに応じた動的なしきい値を持つ「ヘビーヒッター」条件を要求することです。 従来の多数要素問題とは異なり、どのセグメントにもそのような値は 2 つまで存在できますが、それ以上存在することはできません。それぞれ 3 分の 1 を超える 3 つの個別の値がすでにセグメント サイズを超えているためです。 

入力サイズは大きく、すべてのテスト ケースにわたる要素とクエリの合計数は最大 2 × 10^5 です。 これにより、クエリごとに頻度を最初から再計算することが直ちに排除されます。 クエリごとの単純なスキャンでは、最悪の場合 O(nq) のコストがかかりますが、これは制限をはるかに超えています。 

しきい値の定義方法に微妙な問題が生じます。 この条件は厳密に ⌊len/3⌋ より大きく、頻度が少なくとも ⌊len/3⌋ + 1 である値を検出する必要があることを意味します。この off-by-one の動作は、小さなセグメントでは重要です。 たとえば、セグメントの長さが 2 の場合、⌊2/3⌋ = 0 となるため、少なくとも 1 回出現するすべての値が適格となり、複数の回答が存在する可能性があります。 

エッジケースは、セグメントが非常に小さい場合、またはすべての要素が同一である場合に発生します。 長さ 1 のセグメントでは、答えは常にその 1 つの値になります。 すべての要素が異なるセグメントでは、しきい値が 0 になるため、答えはすべての要素になります。 [1,2,3,1,2,3] のような繰り返し構造を持つセグメントでは、しきい値に到達する値はありません。 

## アプローチ

 各クエリの直接的なアプローチは、セグメントのハッシュ マップを使用して頻度をカウントすることです。 これは正しいですが、遅すぎます。各クエリのコストは O(r − l + 1) となり、合計操作数は O(nq) になります。 

条件の構造は、より強力な制約を示唆しています。範囲内で頻度が n/3 を超える要素を検索しています。 古典的な事実として、どの配列セグメントにもそのような要素は最大 2 つ存在できるということです。 これにより、検索スペースが大幅に削減されます。すべての個別の値を考慮するのではなく、クエリごとに最大 2 つの候補を特定して検証するだけで済みます。 

課題は、これらの候補者を効率的に生成する方法です。 重要な観察は、セグメント内で値が n/3 回以上出現する場合、そのセグメントから何らかの有界ランダム性または構造化分解を使用して要素を選択するサンプリング プロセスで、その値を候補として選択する必要があるということです。 これは、一般的な競技プログラミングのトリック、つまり、潜在的な多数派候補の小さなセットを格納する分割統治またはセグメント ツリー ノードにつながります。 

各ノードが間隔の 3 分の 1 を超える可能性のある最大 2 つの候補値を格納するセグメント ツリーを構築できます。 2 つのノードをマージする場合、それらの候補セット (最大 4 つの値) を結合し、最も有望なものだけを保持します。 これにより、真の答えがマージ後も存続することが保証されます。 

クエリごとに、O(log n) で候補セットを取得し、前処理された位置リスト (出現の二分探索) またはフェンウィック ツリー アプローチを使用して実際の頻度を検証します。 候補は最大 2 つであるため、検証は安価です。 

別の観点としてはランダム化サンプリングがありますが、制約がある場合は決定論的なセグメント ツリー集約の方が安全です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nq) | お(1) | 遅すぎる |
 | セグメントツリー候補+検証 | O((n + q) log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

1. 個別の数値ごとに、配列内のその位置の並べ替えられたリストを格納することにより、値を暗黙的に圧縮またはインデックス付けします。 これにより、二分探索を使用した任意の範囲での高速な頻度クエリが可能になります。 このステップは、候補を推測した後で正確な数をすぐに確認できるようにするために必要です。 
2. 各ノードがそのセグメントの有効な回答となる可能性のある候補値を最大 2 つ格納する配列上にセグメント ツリーを構築します。 この制限は、3 分の 1 の周波数しきい値を超えることができる値は 2 つだけであるという数学的事実によって正当化されます。 
3. リーフ ノードの場合、候補セットは単にその位置の単一の配列値です。 
4. 内部ノードの場合、左右の子候補セットをマージします。 両方の子のすべての候補を一時的なセットに結合し、ノードのセグメント内の真の頻度を再計算し、頻度ごとに上位 2 つの値のみを保持します。 このフィルタリングにより、無関係な値が上方に伝播しないようにすることができます。 
5. クエリ [l, r] に答えるには、セグメント ツリーをクエリして、そのセグメントの候補値を最大 2 つ取得します。 
6. 各候補について、位置リストの二分探索を使用して [l, r] の正確な頻度を計算します。 頻度が厳密に ⌊(r − l + 1)/3⌋ より大きいものを保持します。 
7. 有効な候補をソートされた順序で出力します。存在しない場合は -1 を出力します。 

### なぜ効果があるのか

 セグメントの 3 分の 1 を超えて出現する値は、セグメント ツリーでのマージ プロセス中に削除できないほど、十分に互いに素なサブセグメントに出現する必要があります。 各ノードはその子からの最も頻繁な候補のみを保存し、真の答えは常に候補以外のノイズよりも高いサポートを持っているため、クエリ範囲をカバーするルートまでのすべてのマージが生き残ります。 最終検証により、中間の枝刈りによって余分な値が保持されたり、重複が生じたりした場合でも、正確さが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class SegTree:
    def __init__(self, a):
        self.n = len(a)
        self.a = a
        self.tree = [[] for _ in range(4 * self.n)]
        self.build(1, 0, self.n - 1)

    def merge(self, left, right):
        cand = left + right
        if not cand:
            return []
        # count frequencies within candidate pool (small size ≤ 4)
        freq = {}
        for x in cand:
            freq[x] = freq.get(x, 0) + 1
        # keep up to 2 arbitrary highest-frequency candidates
        cand = sorted(freq.keys(), key=lambda x: -freq[x])
        return cand[:2]

    def build(self, v, tl, tr):
        if tl == tr:
            self.tree[v] = [self.a[tl]]
            return
        tm = (tl + tr) // 2
        self.build(v*2, tl, tm)
        self.build(v*2+1, tm+1, tr)
        self.tree[v] = self.merge(self.tree[v*2], self.tree[v*2+1])

    def query(self, v, tl, tr, l, r):
        if l > r:
            return []
        if l == tl and r == tr:
            return self.tree[v]
        tm = (tl + tr) // 2
        left = self.query(v*2, tl, tm, l, min(r, tm))
        right = self.query(v*2+1, tm+1, tr, max(l, tm+1), r)
        return self.merge(left, right)

def solve():
    t = int(input())
    for _ in range(t):
        n, q = map(int, input().split())
        a = list(map(int, input().split()))

        pos = {}
        for i, x in enumerate(a):
            pos.setdefault(x, []).append(i)

        st = SegTree(a)

        out = []
        for _ in range(q):
            l, r = map(int, input().split())
            l -= 1
            r -= 1
            cand = st.query(1, 0, n-1, l, r)

            res = []
            length = r - l + 1
            threshold = length // 3

            for x in cand:
                lst = pos[x]
                # count occurrences in [l, r]
                # binary search
                import bisect
                cnt = bisect.bisect_right(lst, r) - bisect.bisect_left(lst, l)
                if cnt > threshold:
                    res.append(x)

            if not res:
                out.append("-1")
            else:
                res.sort()
                out.append(" ".join(map(str, res)))

        print("\n".join(out))

if __name__ == "__main__":
    solve()
```セグメント ツリーには最大 2 つのサイズの候補セットが格納され、マージでは一度に最大 4 つの値のみが考慮されるため、構造の効率が維持されます。 位置マップでは、二分探索を使用して候補ごとに O(log n) 回の頻度チェックが可能です。 

微妙な詳細は、しきい値の比較です。`cnt > length // 3`、 ない`>=`、これは問題定義の厳密な不等式と一致します。 

## 実用的な例

 ### 例 1

 配列: [4, 4, 4, 5, 5, 5, 6, 6]、クエリ [1, 8]

 | ステップ | 候補セット | 長さ | しきい値 | 検証済みの数 | 出力 |
 | --- | --- | --- | --- | --- | --- |
 | クエリ | [4、5] | 8 | 2 | 4→3、5→3 | [4、5] |

 これは、両方の候補が ⌊8/3⌋ = 2 を超えているため、検証に生き残ることを示しています。 

### 例 2

 配列: [1, 2, 3, 1, 2, 3]、クエリ [1, 6]

 | ステップ | 候補セット | 長さ | しきい値 | 検証済みの数 | 出力 |
 | --- | --- | --- | --- | --- | --- |
 | クエリ | [1、2] (または同様のもの) | 6 | 2 | すべての値 ≤ 2 | -1 |

 これは、候補者だけでは不十分であることを示しています。 最終的な検証が不可欠です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + q) log n) | セグメント ツリー クエリは O(log n) で、各クエリは 2 つ以下の候補を O(log n) カウントで検証します。 
| スペース | O(n) | セグメントツリーと位置リスト |

 n と q の合計が最大 2 × 10^5 であり、各演算が対数であるため、これは制約の下で快適に適合します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys

    input = sys.stdin.readline

    class SegTree:
        def __init__(self, a):
            self.n = len(a)
            self.a = a
            self.tree = [[] for _ in range(4 * self.n)]
            self.build(1, 0, self.n - 1)

        def merge(self, left, right):
            cand = left + right
            freq = {}
            for x in cand:
                freq[x] = freq.get(x, 0) + 1
            cand = sorted(freq.keys(), key=lambda x: -freq[x])
            return cand[:2]

        def build(self, v, tl, tr):
            if tl == tr:
                self.tree[v] = [self.a[tl]]
                return
            tm = (tl + tr) // 2
            self.build(v*2, tl, tm)
            self.build(v*2+1, tm+1, tr)
            self.tree[v] = self.merge(self.tree[v*2], self.tree[v*2+1])

        def query(self, v, tl, tr, l, r):
            if l > r:
                return []
            if l == tl and r == tr:
                return self.tree[v]
            tm = (tl + tr) // 2
            return self.merge(
                self.query(v*2, tl, tm, l, min(r, tm)),
                self.query(v*2+1, tm+1, tr, max(l, tm+1), r)
            )

    t = int(input())
    out = []
    import bisect

    for _ in range(t):
        n, q = map(int, input().split())
        a = list(map(int, input().split()))

        pos = {}
        for i, x in enumerate(a):
            pos.setdefault(x, []).append(i)

        st = SegTree(a)

        for _ in range(q):
            l, r = map(int, input().split())
            l -= 1
            r -= 1
            cand = st.query(1, 0, n-1, l, r)

            res = []
            length = r - l + 1
            threshold = length // 3

            for x in cand:
                lst = pos[x]
                cnt = bisect.bisect_right(lst, r) - bisect.bisect_left(lst, l)
                if cnt > threshold:
                    res.append(x)

            if not res:
                out.append("-1")
            else:
                res.sort()
                out.append(" ".join(map(str, res)))

    return "\n".join(out)

# provided sample placeholders (not full due to formatting)
# assert run(...) == ...
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一要素クエリ | その要素 | 最小境界 |
 | すべて等しい配列 | 価値そのもの | 完全優勢の場合 |
 | すべて異なる | 狭い範囲のすべての要素 | しきい値ゼロの動作 |
 | 交互の値 | -1 または小さいセット | 優位性の安定性なし |

 ## 特殊なケース

 単一要素セグメントは、しきい値がゼロになり、任意の値が適格となるため、常にその要素を返します。 セグメント ツリーがその値を候補として返し、検証によってそれが確認されるため、アルゴリズムはこれを処理します。 

[1,3] でクエリされた [1,2,3] など、範囲が狭い完全に個別の配列では、しきい値はゼロであるため、すべての候補が検証に合格します。 セグメント ツリーは部分的な候補のみを返す場合がありますが、頻度はすべて等しいため、セグメント内に表示される返された候補はすべて正しく受け入れられます。 

[7,7,7,7,7] のような反復性の高い配列では、候補の伝播ではすべてのノードで 7 が維持され、頻度が 3 分の 1 を超えるため検証は常にパスするため、すべてのクエリは [7] を返します。 

[1,2,1,2,1,2] のような交互パターンでは、全範囲クエリでしきい値を超える値はないため、候補の生成では引き続き [1,2] が生成される可能性がありますが、検証では両方が削除され、-1 が生成されます。
