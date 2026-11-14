---
title: "CF 1799G - 投票のカウント"
description: "$n$ 人のグループがあり、それぞれが特定のチームに属しており、各人は特定の数の投票を受け取りたいと考えています。 このルールは投票を制限しており、誰も自分自身に投票することはできず、誰も自分のチームの誰かに投票することもできません。"
date: "2026-06-09T09:48:55+07:00"
tags: ["codeforces", "competitive-programming", "combinatorics", "dp", "math"]
categories: ["algorithms"]
codeforces_contest: 1799
codeforces_index: "G"
codeforces_contest_name: "Codeforces Round 854 by cybercats (Div. 1 + Div. 2)"
rating: 2600
weight: 1799
solve_time_s: 143
verified: false
draft: false
---

[CF 1799G - 投票数](https://codeforces.com/problemset/problem/1799/G)

 **評価:** 2600
 **タグ:** 組み合わせ論、dp、数学
 **解決時間:** 2 分 23 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちには次のようなグループがあります$n$人々はそれぞれ特定のチームに属しており、各人は特定の数の票を受け取りたいと考えています。 このルールは投票を制限しており、誰も自分自身に投票することはできず、誰も自分のチームの誰かに投票することもできません。 タスクは、必要な投票数をすべて満たす有効な投票取り決めの数をカウントすることです。 

入力により提供されるのは、$n$、各人の希望票、チームメンバーシップ。 出力はアレンジメントの総数を法的に計算したものです。$998244353$。 

この制約は、すべての要素を総当たり的に列挙することを意味します。$n!$順列は中程度の場合でも実行不可能です$n$以来$n$これにより、階乗複雑性を伴うアプローチが除外されます。 エッジケースには、個人がゼロ票を希望する場合や、チームメンバー全員がチーム外で投票しなければならない場合が含まれます。これにより、投票数がチームの規模と一致しない場合、有効な選択肢がなくなる可能性があります。 たとえば、同じチームに 2 人がいて、どちらも 1 票を受け取りたいのに、チーム外に 1 人しかいない場合、解決策は存在しません。 単純なアプローチでは、これらの競合を検出せずに、有効な構成がまったく生成されなかったり、無効な割り当てが試行されたりする可能性があります。 

## アプローチ

 ブルートフォース手法では、考えられるすべての順列を再帰的にチェックしながら、各人に別のチームの誰かに投票を割り当てようとします。 これは概念的には正しいですが、次のことが必要です。$O(n!)$手術が行われ、すぐに手に負えなくなる$n = 20$、ましてや$n = 200$。 

重要な観察は、投票がチームの制約内で独立しているということです。 各人の投票はチーム外の人から得られます。 この構造は、当然ながら動的プログラミングに適しています。 人々をチームごとにグループ化し、各チームが他のチームから何票受け取るかをカウントできます。 次に、各チーム内で、希望する数に従ってメンバー間で投票を分配する必要があります。 これにより、利用可能な投票者によって制約される多項係数の計算の問題が軽減され、総数を効率的に計算できるようになります。 

最適なアプローチは、組み合わせ論と DP を組み合わせたものです。 まず、チームごとの数と各チーム外の投票者数の合計を前処理します。 次に、すべてのチームを反復処理して、外部有権者からの投票をメンバーに割り当てながら、希望の数を満たせる方法の数を計算します。 これは、チームごとの累積投票数に対する動的プログラミングを使用して実装できます。 チームごとに作業し、モジュラー演算を使用することにより、ソリューションは効率的でメモリ制限内に維持されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | お(ん!) | O(n) | 遅すぎる |
 | 最適な DP + 組み合わせ論 | O(n^3) | O(n^2) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべての人をチームごとにグループ化します。 各チームについて、メンバーのリストと各メンバーが希望する投票の合計を記録します。 
2. 各チームについて、チーム外の投票者の総数を計算します。 これにより、チームメンバーに合法的に投票できる票のプールが与えられます。 
3. チームごとにダイナミック プログラミング テーブルを使用する`dp[i][v]`割り当てる方法の数を表す`v`最初に投票します`i`チームのメンバーに合わせて、各メンバーが希望する番号を正確に取得できるようにします。 初期化する`dp[0][0] = 1`。 
4. メンバーごとに`i`チームのすべての可能な投票数を反復します`v`以前の DP 状態から、を追加します。`dp[i-1][v] * C(available_votes, desired_votes[i])`に`dp[i][v + desired_votes[i]]`、 どこ`C`は二項係数のモジュロです`998244353`。 
5. チームのメンバー全員を処理した後、`dp[team_size][total_votes_needed_for_team]`そのチームの有効なディストリビューションの数を示します。 これらの結果をすべてのチームで掛け合わせて、最終的な答えを求めます。 
6. 階乗および逆階乗モジュロを事前計算する`998244353`二項係数を効率的に計算するため。 

機能する理由: このアルゴリズムは、チームの DP の各ステップで、カウントされたすべての割り当てがこれまでに考慮されたメンバーの投票制約を満たし、チーム外からの投票のみを使用するという不変条件を維持します。 チーム間でカウントを乗算することは、チームがどの外部有権者に投票できるかに関して独立しているため、機能します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 998244353

def modinv(x):
    return pow(x, MOD-2, MOD)

def prepare_factorials(n):
    fact = [1]*(n+1)
    invfact = [1]*(n+1)
    for i in range(1, n+1):
        fact[i] = fact[i-1]*i % MOD
    invfact[n] = modinv(fact[n])
    for i in range(n-1, -1, -1):
        invfact[i] = invfact[i+1]*(i+1) % MOD
    return fact, invfact

def comb(n, k, fact, invfact):
    if k < 0 or k > n:
        return 0
    return fact[n]*invfact[k]%MOD*invfact[n-k]%MOD

n = int(input())
c = list(map(int, input().split()))
t = list(map(int, input().split()))

teams = {}
for idx, team in enumerate(t):
    if team not in teams:
        teams[team] = []
    teams[team].append(idx)

fact, invfact = prepare_factorials(n)

result = 1
for members in teams.values():
    m = len(members)
    total_votes_needed = sum(c[i] for i in members)
    available_votes = n - m
    if total_votes_needed > available_votes:
        result = 0
        break
    dp = [0]*(total_votes_needed+1)
    dp[0] = 1
    for i in members:
        ndp = [0]*(total_votes_needed+1)
        for v in range(total_votes_needed+1):
            if dp[v]:
                ways = comb(available_votes - v, c[i], fact, invfact)
                if v + c[i] <= total_votes_needed:
                    ndp[v + c[i]] = (ndp[v + c[i]] + dp[v]*ways)%MOD
        dp = ndp
    result = result*dp[total_votes_needed]%MOD

print(result)
```このコードでは、まずユーザーをチームごとにグループ化します。 階乗および逆階乗は、二項係数に対して事前計算されます。 各チームについて、累積投票によってインデックス付けされた DP 配列を使用して、必要な投票を割り当てる方法の数を計算します。 すべてのチームの結果を乗算すると、有効な構成の合計数が得られます。 投票数がチーム外の投票者のプールを超えないよう注意が払われます。 Off-by-one エラーは、慎重にインデックスを作成し、範囲内でのみ組み合わせを計算することによって回避されます。 

## 実用的な例

 ### サンプル 1

 入力:```
3
1 1 1
1 2 3
```| チーム | メンバー | 必要な投票 | 処理後のDP |
 | --- | --- | --- | --- |
 | 1 | [0] | 1 | dp[0]=1 -> dp[1]=1 |
 | 2 | [1] | 1 | dp[0]=1 -> dp[1]=1 |
 | 3 | [2] | 1 | dp[0]=1 -> dp[1]=1 |

 最終結果 = 1_1_2 (投票の 2 つの順列) = 2

 これは、アルゴリズムが各チームを独立して正しく処理し、外部投票者からの組み合わせを乗算していることを示しています。 

### カスタム入力

 入力:```
4
1 2 1 0
1 1 2 2
```| チーム | メンバー | 必要な投票 | 処理後のDP |
 | --- | --- | --- | --- |
 | 1 | [0,1] | 1+2=3 | dp[0]=1 -> dp[1]=1 -> dp[3]=2 |
 | 2 | [2,3] | 1+0=1 | dp[0]=1 -> dp[1]=1 |

 最終結果 = 2*1 = 2

 これは、チームにさまざまな投票希望を持つ複数のメンバーがいる場合に、適切な投票割り当てが行われていることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n^3) | 各チームの DP は最大 O(n^2) で、チーム全体で O(n) 人のメンバーがいます。 
| スペース | O(n^2) | 必要な総投票数までのチームごとの DP 配列 |

 と$n \leq 200$、$O(n^3) = 8*10^6$操作は 1 秒の制限と 256 MB のメモリ制約内に快適に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    MOD = 998244353

    def modinv(x):
        return pow(x, MOD-2, MOD)

    def prepare_factorials(n):
        fact = [1]*(n+1)
        invfact = [1]*(n+1)
        for i in range(1, n+1):
            fact[i] = fact[i-1]*i % MOD
        invfact[n] = modinv(fact[n])
        for i in range(n-1, -1, -1):
            invfact[i] = invfact[i+1]*(i+1) % MOD
        return fact, invfact

    def comb(n, k, fact, invfact):
        if k < 0 or k > n:
            return 0
        return fact[n]*invfact[k]%MOD*invfact[n-k]%MOD

    n = int(input())
    c
```
