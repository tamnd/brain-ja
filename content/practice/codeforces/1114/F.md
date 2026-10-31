---
title: "CF 1114F - アレイに関する別のクエリをお願いします?"
description: "それぞれ 1 ～ 300 の整数の配列があり、2 種類のクエリを処理する必要があります。 最初のクエリは、配列の連続セグメントに指定された数値を乗算します。"
date: "2026-06-12T04:53:50+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "data-structures", "divide-and-conquer", "math", "number-theory"]
categories: ["algorithms"]
codeforces_contest: 1114
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 538 (Div. 2)"
rating: 2400
weight: 1114
solve_time_s: 67
verified: true
draft: false
---

[CF 1114F - 配列に別のクエリを実行してください?](https://codeforces.com/problemset/problem/1114/F)

 **評価:** 2400
 **タグ:** ビットマスク、データ構造、分割統治、数学、数論
 **解決時間:** 1 分 7 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 それぞれ 1 ～ 300 の整数の配列があり、2 種類のクエリを処理する必要があります。 最初のクエリは、配列の連続セグメントに指定された数値を乗算します。 2 番目のクエリは、配列のセグメントの積のオイラー トーティエントを求めます。$10^9 + 7$。 オイラー トーティエント関数、$\varphi(n)$、整数を次までカウントします。$n$と互いに素である$n$。 乗算は積に対して分配されるため、乗算クエリはセグメント内の各要素の素因数分解を変更し、その結果、それらの要素を含むセグメントの合計に影響を与えます。 

制約は重要です。配列の要素は最大 400,000 個まで、クエリは 200,000 個まで可能です。 毎回積を再計算したり、セグメントごとに素因数分解を再計算したりする素朴なアプローチでは、実行する演算が多すぎます。 具体的には、クエリごとに積を再計算します。$O(n)$産むだろう$O(nq)\approx 8 \cdot 10^{10}$これは 6 秒の制限時間では許容範囲をはるかに超えています。 

明らかでないエッジ ケースには、1 を含むセグメントが含まれます。1 の合計は 1 であるため、完全に 1 で構成されるセグメントは 1 を返す必要があります。同様に、1 による乗算演算は何も変更しませんが、構造内で追跡する必要があります。 すべての要素が 1 より大きいと仮定する不注意な実装では、次のような式を使用すると、ゼロが誤って計算される可能性があります。$\prod (p_i-1)p_i^{k_i-1}$素数用$p_i$製品を分割すること。 

もう 1 つの微妙な点は、セグメント要素の積が非常に大きくなり、すぐに 64 ビット整数を超える可能性があることです。 これにより、積の単純な計算が妨げられます。 解は、理想的には素数指数を介して、乗法構造を間接的に操作する必要があります。 

## アプローチ

 ブルートフォースアプローチでは、以下を反復することで MULTIPLY クエリを処理します。$l$に$r$各要素を乗算し、セグメントの積を計算して因数分解することで TOTIENT クエリを処理します。 これは、配列要素が 300 で制限されているため、因数分解が安価であるため、小さな配列で機能します。 ただし、400,000 個の要素と 200,000 個のクエリを使用すると、この方法では最大で$8 \cdot 10^{10}$乗算や因数分解はまったく実行不可能です。 

重要な洞察は、両方の演算が素因数の指数空間で線形であるということです。 1 から 300 までのすべての整数には固定の素因数セットがあり、セグメントに数値を乗算することは、そのセグメント内の各要素に対応する素数指数を加算することと同じです。 同様に、積の合計を計算すると、素数指数を結合することになります。$\varphi(\prod a_i) = \prod p^{k_i-1}(p-1)$すべての素数に対して$p$製品を分割すること。 これにより、セグメントごとに素数のビットマスクまたは指数ベクトルを保存し、遅延伝播を備えたセグメント ツリーを使用して効率的に更新/クエリを実行できるようになります。 

300 までのすべての数値について素因数分解を事前に計算します。次に、結合された素数情報によってセグメント ツリー内の各セグメントを表します。 乗算クエリは指数ベクトルを加算し、合計クエリは指数ベクトルを結合して合計モジュロを計算します。$10^9 + 7$。 ビットマスクを使用してセグメントを分割する素数を追跡すると、非常に高速な結合操作が可能になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * q * 8) | O(n) | 遅すぎる |
 | セグメント ツリー + ビットマスク指数 | O((n + q) * log n * P) | O(n * P) | 承認済み |

 ここ、$P$は素数の数 ≤ 300、つまり 62 です。 

## アルゴリズムのチュートリアル

 1. ふるいを使用して、300 までのすべての素数を事前計算します。 それぞれの数字$1 \le x \le 300$は、それを分割する素数のビットマスクに関連付けられます。 店$\varphi(x)$必要に応じて、最大 300 までの数値を別のテーブルとして作成します。 
2. 配列内の各要素について、その値をその素因数と各素数の指数のベクトルを表すビットマスクに変換します。 
3. 配列上にセグメント ツリーを構築します。各ノードには、セグメント内の素数の累積指数ベクトルと、セグメント内の要素を分割する素数を表すビットマスクという 2 つの情報が格納されます。 
4. MULTIPLY クエリの場合、乗数をその指数ベクトルとビットマスクに変換します。 セグメント ツリーに遅延更新を適用し、影響を受ける範囲に指数を追加します。 ビットマスクにビットごとの OR を使用して、素数の存在を示します。 
5. TOTIENT クエリの場合は、セグメント ツリーをクエリして、範囲の累積指数ベクトルとビットマスクを取得します。 次を使用して合計を計算します$\varphi(\prod a_i) = \prod_{p} p^{k_p - 1} (p-1)$モジュロ$10^9 + 7$、 どこ$k_p$素数の合計指数です$p$セグメント内で。 
6. 結果を順番に出力します。 

**機能する理由:** 指数ベクトルを含むセグメント ツリーにより、あらゆるクエリ範囲について、セグメントの正確な結合素因数分解がわかることが保証されます。 遅延伝播により、トーティエント クエリの前に複数の乗算が正しく累積され、各ノードがそのセグメントを正確に表すという不変条件が維持されます。 ビットマスクを使用すると、迅速な組み合わせが可能になり、どの素数が存在するかの再計算が回避されます。 正しさは、全関数の乗法性と乗算時の指数の加法性に依存します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 10**9 + 7

# Precompute primes and factorizations
MAX_A = 300
primes = []
is_prime = [True] * (MAX_A+1)
is_prime[0] = is_prime[1] = False
for i in range(2, MAX_A+1):
    if is_prime[i]:
        primes.append(i)
        for j in range(i*i, MAX_A+1, i):
            is_prime[j] = False

prime_index = {p:i for i,p in enumerate(primes)}
P = len(primes)

# Factorization as exponent vector
def factorize(x):
    exps = [0]*P
    for i,p in enumerate(primes):
        while x % p == 0:
            x //= p
            exps[i] += 1
    return exps

class SegmentTree:
    def __init__(self, data):
        self.n = len(data)
        self.size = 1
        while self.size < self.n:
            self.size *= 2
        self.tree = [[0]*P for _ in range(2*self.size)]
        self.lazy = [[0]*P for _ in range(2*self.size)]
        for i in range(self.n):
            self.tree[self.size + i] = data[i]
        for i in range(self.size-1, 0, -1):
            for j in range(P):
                self.tree[i][j] = self.tree[2*i][j] + self.tree[2*i+1][j]

    def push(self, v, l, r):
        for j in range(P):
            if self.lazy[v][j]:
                self.tree[v][j] += self.lazy[v][j]*(r-l+1)
                if v < self.size:
                    self.lazy[2*v][j] += self.lazy[v][j]
                    self.lazy[2*v+1][j] += self.lazy[v][j]
                self.lazy[v][j] = 0

    def update(self, ql, qr, add, v=1, l=0, r=None):
        if r is None:
            r = self.size-1
        self.push(v,l,r)
        if qr < l or r < ql:
            return
        if ql <= l and r <= qr:
            for j in range(P):
                self.lazy[v][j] += add[j]
            self.push(v,l,r)
            return
        m = (l+r)//2
        self.update(ql, qr, add, 2*v, l, m)
        self.update(ql, qr, add, 2*v+1, m+1, r)
        for j in range(P):
            self.tree[v][j] = self.tree[2*v][j] + self.tree[2*v+1][j]

    def query(self, ql, qr, v=1, l=0, r=None):
        if r is None:
            r = self.size-1
        self.push(v,l,r)
        if qr < l or r < ql:
            return [0]*P
        if ql <= l and r <= qr:
            return self.tree[v][:]
        m = (l+r)//2
        left = self.query(ql, qr, 2*v, l, m)
        right = self.query(ql, qr, 2*v+1, m+1, r)
        return [left[j]+right[j] for j in range(P)]

def mod_pow(a,b):
    res=1
    while b>0:
        if b&1:
            res=res*a%MOD
        a=a*a%MOD
        b>>=1
    return res

def totient(exps):
    res = 1
    for i,p in enumerate(primes):
        k = exps[i]
        if k > 0:
            res = res *
```
