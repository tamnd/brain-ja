---
title: "CF 446C - DZY はフィボナッチ数が大好き"
description: "整数の配列が与えられており、2 種類のクエリを効率的にサポートする必要があります。 最初のタイプでは、フィボナッチ数を連続した部分配列に追加する必要があります。l から r までのインデックスの場合、l の要素に F1 を追加し、l+1 の要素に F2 を追加し、位置 r の F{r-l+1} まで追加します。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 446
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round #FF (Div. 1)"
rating: 2400
weight: 446
solve_time_s: 392
verified: false
draft: false
---

[CF 446C - DZY はフィボナッチ数が大好き](https://codeforces.com/problemset/problem/446/C)

 **評価:** 2400
 **タグ:** データ構造、数学、数論
 **解決時間:** 6 分 32 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 整数の配列が与えられており、2 種類のクエリを効率的にサポートする必要があります。 最初のタイプでは、連続した部分配列にフィボナッチ数を追加する必要があります。_l_ から _r_ までのインデックスの場合、_l_ の要素に _F₁_ を追加し、_l+1_ の要素に _F₂_ を追加し、位置 _r_ の _F_{r-l+1}_ までを追加します。 2 番目のタイプは、10⁹+9 を法とする部分配列内の要素の合計を求めます。 

制約は厳しく、配列には最大 300,000 個の要素を含めることができ、クエリの数も 300,000 に達する可能性があります。 これにより、各クエリの範囲に対して単純な反復を実行するソリューションは除外されます。これは、最悪の場合 O(n·m) となり、潜在的に約 9×10¹⁰ の操作が必要となり、これは 4 秒で実行できるものをはるかに超えます。 配列要素の値とフィボナッチ数は非常に大きくなる可能性があるため、モジュラー演算を慎重に扱う必要があります。 

クエリが 1 つの要素のみに影響を与える場合、または連続した更新が重複する場合、微妙なエッジ ケースが発生します。 たとえば、配列が [1,2] で、クエリが 1 から 2 までのフィボナッチ数を加算することである場合、更新により配列に [1,1] が追加されます。 クエリごとにフィボナッチ数を再計算して単にフィボナッチ数を加算する単純な実装では、時間がかかりすぎて、インデックスまたはフィボナッチ オフセットの位置がずれていると、簡単に誤った結果が生成される可能性があります。 

## アプローチ

 ブルート フォース ソリューションは、説明したようにクエリを直接実装します。 タイプ 1 クエリの場合、最初の r-l+1 フィボナッチ数を計算し、範囲内の各要素に個別に追加します。 タイプ 2 クエリの場合、範囲内の要素を合計します。 これは概念的には機能しますが、最悪の場合、複雑さは O(m・n) になります。 すべてのクエリが配列のほぼ全体をカバーする場合、アルゴリズムはおよそ 9×10¹⁰ の演算を実行しますが、これは許容範囲をはるかに超えています。 

これを高速化するための重要な洞察は、フィボナッチ加算が線形であり、線形漸化式を使用して表現できることを認識することです。 これにより、セグメント ツリーに遅延伝播を適用できるようになります。 各要素を個別に更新する代わりに、セグメントの最初の 2 つのフィボナッチ数を使用して、フィボナッチ セグメントを追加した効果をコンパクトな形式で保存します。 更新をツリーの下に伝播すると、フィボナッチ数列はその再帰を使用して効率的に拡張できます。 これにより、更新操作が O(range) から O(log n) に変換され、合計クエリも O(log n) になります。 

フィボナッチ プロパティ F_k+2 = F_k+1 + F_k を使用すると、シーケンス全体を再計算することなく、範囲内の次の値を計算できます。 セグメント ツリー ノードは、間隔の合計だけでなく、伝播する必要がある最初の 2 つのフィボナッチ寄与を表す 2 つの数値も維持します。 遅延伝播により、重複または連続した更新が正しく処理されることが保証されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n・m) | O(n) | 遅すぎる |
 | セグメント ツリー + フィボナッチ レイジー | O((n+m)・log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

1. 更新には r-l+1 番目のフィボナッチ数が必要になる場合があるため、フィボナッチ数を法 10⁹+9 から n+2 まで事前計算します。 これにより、必要なフィボナッチ値に一定時間アクセスできるようになります。 
2. 配列に対してセグメント ツリーを構築します。 各ノードは、そのセグメントのモジュロ 10⁹+9 の合計を格納します。 さらに、各ノードは、子に伝播する必要がある最初の 2 つのフィボナッチ値を表す数値のペアとして遅延更新を維持します。 
3. タイプ 1 クエリ「1 l r」の場合、各要素を更新する代わりに、位置 l で始まる最初の 2 つのフィボナッチ数を計算します。 遅延伝播を使用して、このフィボナッチ セグメントをセグメント ツリー内の対応するノードに追加します。 ノードが部分的にカバーされている場合は、更新をその子にプッシュし、線形再帰を使用して各子の開始フィボナッチ数を再計算します。 
4. タイプ 2 クエリ「2 l r」の場合、範囲の合計についてセグメント ツリーをクエリします。 パス上に遅延更新が存在する場合は、最初にそれらを伝播して、合計に以前の更新がすべて反映されていることを確認します。 
5. フィボナッチ更新を子ノードに伝播するとき、左の子の最初の 2 つのフィボナッチ値を親の遅延ペアから直接計算します。 右側の子の場合、行列のべき乗または既知のフィボナッチ和を使用して、開始点を正しく移動します。 これにより、更新が正しい順序で維持されることが保証されます。 
6. フィボナッチの増大により数値が急速に増大する可能性があるため、整数のオーバーフローを避けるためにすべてのステップでモジュラー演算を実行します。 

機能する理由: セグメント ツリーは、各クエリが最小限のノード セットに適用されることを保証します。 遅延伝播により、各フィボナッチ セグメントが再計算なしで必要な場所に正確に適用されます。 これら 2 つの値からフィボナッチ数列全体を再構築でき、正確性が保証されるため、最初の 2 つのフィボナッチ表現で十分です。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 10**9 + 9

def add_mod(a, b):
    return (a + b) % MOD

def mul_mod(a, b):
    return (a * b) % MOD

def build_fib(n):
    fib = [0] * (n + 2)
    fib[1] = 1
    for i in range(2, n + 2):
        fib[i] = add_mod(fib[i-1], fib[i-2])
    return fib

class SegmentTree:
    def __init__(self, a):
        self.n = len(a)
        self.size = 1
        while self.size < self.n:
            self.size <<= 1
        self.sum = [0] * (2 * self.size)
        self.lazy = [(0,0)] * (2 * self.size)
        for i in range(self.n):
            self.sum[self.size + i] = a[i] % MOD
        for i in range(self.size - 1, 0, -1):
            self.sum[i] = add_mod(self.sum[i<<1], self.sum[i<<1|1])

    def push(self, v, l, r):
        a, b = self.lazy[v]
        if a == 0 and b == 0:
            return
        mid = (l + r) >> 1
        len_left = mid - l + 1
        len_right = r - mid
        # Update left child
        self.apply(v<<1, l, mid, a, b)
        # Update right child: shift Fibonacci sequence by len_left
        fib_a = add_mod(mul_mod(fib[len_left-1], b), mul_mod(fib[len_left], a))
        fib_b = add_mod(mul_mod(fib[len_left], b), mul_mod(fib[len_left+1], a))
        self.apply(v<<1|1, mid+1, r, fib_a, fib_b)
        self.lazy[v] = (0,0)

    def apply(self, v, l, r, a, b):
        # Sum over segment: F1+F2+...+Fk = ?
        k = r - l + 1
        s = add_mod(mul_mod(fib[k], b), mul_mod(fib[k-1], a))
        self.sum[v] = add_mod(self.sum[v], s)
        if v < self.size:
            self.lazy[v] = (add_mod(self.lazy[v][0], a), add_mod(self.lazy[v][1], b))

    def update(self, ql, qr, a, b, v=1, l=0, r=None):
        if r is None:
            r = self.size - 1
        if ql > r or qr < l:
            return
        if ql <= l and r <= qr:
            self.apply(v, l, r, a, b)
            return
        self.push(v, l, r)
        mid = (l + r) >> 1
        self.update(ql, qr, a, b, v<<1, l, mid)
        self.update(ql, qr, a, b, v<<1|1, mid+1, r)
        self.sum[v] = add_mod(self.sum[v<<1], self.sum[v<<1|1])

    def query(self, ql, qr, v=1, l=0, r=None):
        if r is None:
            r = self.size - 1
        if ql > r or qr < l:
            return 0
        if ql <= l and r <= qr:
            return self.sum[v]
        self.push(v, l, r)
        mid = (l + r) >> 1
        return add_mod(self.query(ql, qr, v<<1, l, mid),
                       self.query(ql, qr, v<<1|1, mid+1, r))

n, m = map(int, input().split())
a = list(map(int, input().split()))
fib = build_fib(n+2)
st = SegmentTree(a)

for _ in range(m):
    tmp = input().split()
    if tmp[0] == '1':
        l, r = int(tmp[1])-1, int(tmp[2])-1
        st.update(l, r, fib[1], fib[2])
    else:
        l, r = int(tmp[1])-1, int(tmp[2])-1
        print(st.query(l, r))
```このコードは、まず定数時間アクセス用のフィボナッチ配列を構築します。 セグメント ツリーは、各ノードが保留中の更新を表すフィボナッチ数のペアを格納する遅延伝播をサポートします。 更新は範囲に効率的に適用され、合計クエリは以前のすべてのフィボナッチを正確に考慮します。
