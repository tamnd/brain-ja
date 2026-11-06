---
title: "CF 1375G - ツリーの変更"
description: "$Nge 1$ を固定しましょう。 $m$-survivor は、すべての素因数が $m$ を超える正の整数です。 $$f(x,m)={nle x:text{$n$ は $m$-survivor}},$$ とし、$$fk(x,m)={nle x:text{$n$ は正確に $k$ 素因数を持つ $m$-survivor}},$$ とします。ここで素因数は数えられます…"
date: "2026-06-11T11:06:04+07:00"
tags: ["codeforces", "competitive-programming", "brute-force", "constructive-algorithms", "dfs-and-similar", "graph-matchings", "graphs", "trees"]
categories: ["algorithms"]
codeforces_contest: 1375
codeforces_index: "G"
codeforces_contest_name: "Codeforces Global Round 9"
rating: 2800
weight: 1375
solve_time_s: 149
verified: false
draft: false
---

[CF 1375G - ツリーの変更](https://codeforces.com/problemset/problem/1375/G)

 **評価:** 2800
 **タグ:** ブルート フォース、構築的アルゴリズム、DFS および類似のアルゴリズム、グラフ マッチング、グラフ、ツリー
 **解決時間:** 2 分 29 秒
 **確認済み:** いいえ

 ## 解決策
 ## セットアップ

 しましょう$N\ge 1$修正されます。 アン$m$-survivor は、すべての素因数が次を超える正の整数です。$m$。 させて$$
f(x,m)=#{n\le x:\text{$n$ is an $m$-survivor}},
$$そしてさせてください$$
f_k(x,m)=#{n\le x:\text{$n$ is an $m$-survivor having exactly $k$ prime factors}},
$$ここで、素因数は多重度で数えられます。 

問題は、次の式を確立することです。$\pi(N^3)$そして、関係する量が次の値からどのように計算できるかを説明します。$\pi(x)$と$x\le N^2$。 

## 解決策

 ### (a)

 毎$N$-生存者$n\le N^3$次のクラスのいずれかに正確に属します。 

初め、$n=1$。 

2番、$n=p$、 どこ$p>N$プライムです。 

三番目、$n$少なくとも 2 つの素因数があり、すべてが次を超えています$N$。 

仮定する$n$です$N$-少なくとも 3 つの素因数を持つ生存者。 それから$$
n\ge (N^+)^3>N^3,
$$どこ$N^+$を超える最小素数を示します$N$。 したがって、そのような整数は存在しません$\le N^3$。 

したがって、すべての複合材料は$N$-生存者$\le N^3$にはちょうど 2 つの素因数があります。 その結果、

 1+(\pi(N^3)-\pi(N))+f_2(N^3,N)。$$
Rearranging gives
$$\boxed{\pi(N^3)=\pi(N)+f(N^3,N)-1-f_2(N^3,N)}。$$
This proves part (a).
### (b)
An $N$-survivor with exactly two prime factors has the form
$$n=pq、$$
where
$$N<p\le q,\qquad pq​​\le N^3。$$
For a fixed prime $p>N$, the admissible values of $q$ are the primes satisfying
$$p\le q\le \frac{N^3}{p}。$$
Hence
\sum_{\substack{p>N\ p\le N^{3/2}}}
\Bigl(
\pi(N^3/p)-\pi(p-1)
\Bigr).
$$以来$p>N$、 我々は持っています$$
N^3/p\le N^2,
$$したがって、のすべての値$\pi(N^3/p)$式で要求される値は、以下を超えない引数にあります。$N^2$。 

のために$N=10^3$、

 \sum_{\substack{1000<p\le 31622\ p\text{ prime}}}
 \ビグル(
 \pi(10^9/p)-\pi(p-1)
 \大きい)。$$
Using the tabulated values of $\pi(x)$ for $x\le10^6$, the summation evaluates to
$$f_2(10^9,10^3)​​=563,158。$$
### (c)
Let $p_j$ be the $j$th prime and let $p_0=1$.
Define
#{n\le x:\text{all prime factors of $n$ exceed }p_j}.
$$パーティションを分割する$p_{j-1}$- 以下の生存者$x$2つのクラスに分けます。 

最初のクラスは、素因数がすべて次の値を超える整数で構成されます。$p_j$。 彼らの番号は$f(x,p_j)$。 

2 番目のクラスは、次で割り切れるもので構成されます。$p_j$。 このような整数はすべて次の形式をとります$$
n=p_jm,
$$どこ$m\le x/p_j$そしてそのすべての素因数は$m$を超える$p_{j-1}$。 

したがって、2 番目のクラスにはカーディナリティがあります$$
f(x/p_j,p_{j-1}).
$$したがって、

 ## f(x,p_{j-1})

 f(x/p_j,p_{j-1})
 }。$$
This is the stated recurrence.
The initial condition is
$$f(x,p_0)=\lf フロア x\r フロア。$$
Repeated application of the recurrence computes $f(N^3,N)$ using only values
$$f(y,p_i),
 \qquad
 y\le N^2、$$
because every division by a prime exceeding $N$ reduces the argument below $N^2$.
Thus $f(N^3,N)$ can be obtained from the table of values $\pi(x)$ for $x\le N^2$.
Combining part (a) with the values of $f(N^3,N)$ and $f_2(N^3,N)$ yields $\pi(N^3)$.
### (d)
The recurrence in part (c) repeatedly requests values of the form
$$f(y,p_j),$$
for many pairs $(y,j)$.
Efficient evaluation is obtained by storing previously computed values in a table indexed by $(y,j)$. Since many recursive branches reach the same pair, memoization prevents repeated computation.
The arguments $y$ that arise are of the form
$$\左\lf床\frac{N^3}{d}\右\r床、$$
where $d$ is a product of distinct small primes. The number of distinct values is $O(N^{2/3})$, which permits compact storage.
For the evaluation of $f_2(N^3,N)$, a table of $\pi(x)$ for all required arguments $x\le N^2$ is sufficient. The summation
$$\sum_{N<p\le N^{3/2}}
 \bigl(\pi(N^3/p)-\pi(p-1)\bigr)$$
can then be accumulated directly.
The resulting computation requires storage only for the distinct arguments that occur in the recursion together with the prime-counting table up to $N^2$.
## Verification
Part (a) depends on the fact that an $N$-survivor $\le N^3$ cannot possess three prime factors exceeding $N$. Indeed,
$$p_1p_2p_3>(N)(N)(N)=N^3。$$
Hence every composite survivor contributes to $f_2$ and to no higher $f_k$.
Part (b) counts each semiprime survivor exactly once because the condition $p\le q$ selects a unique ordering of the two prime factors.
Part (c) partitions the set of $p_{j-1}$-survivors according to divisibility by $p_j$; the two classes are disjoint and exhaustive, therefore the recurrence is exact.
The formula of part (a) then reconstructs $\pi(N^3)$ from the survivor count and the semiprime correction term.
## Notes
The recurrence
## f(x,p_{j-1})
f(x/p_j,p_{j-1})
$$Lagarias-Miller-Odlyzko 素数計数法の組み合わせ基礎です。 直接ふるい分けの代わりに、$N^3$までの情報のみを含む計算により、$N^2$、これが複雑さの改善の原因です。
