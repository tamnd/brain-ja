---
title: "CF 1569E - プレーオフの復活"
description: "$2^k$ のチームによるシングルエリミネーション トーナメントが与えられます。$k$ の範囲は 1 ～ 5 です。試合は固定ブラケットで構成されます。最初のラウンドでは、連続するチームが互いに対戦し、勝者が次のラウンドに進み、同じルールに従ってペアを組みます。"
date: "2026-06-10T11:38:56+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "brute-force", "hashing", "implementation", "meet-in-the-middle"]
categories: ["algorithms"]
codeforces_contest: 1569
codeforces_index: "E"
codeforces_contest_name: "Educational Codeforces Round 113 (Rated for Div. 2)"
rating: 2600
weight: 1569
solve_time_s: 136
verified: false
draft: false
---

[CF 1569E - プレーオフの復元](https://codeforces.com/problemset/problem/1569/E)

 **評価:** 2600
 **タグ:** ビットマスク、ブルート フォース、ハッシュ、実装、中間者攻撃
 **解決時間:** 2 分 16 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 シングルエリミネーショントーナメントが与えられています$2^k$チーム、どこ$k$範囲は 1 から 5 までです。試合は固定ブラケットで構成されます。最初のラウンドでは、連続するチームが互いに対戦し、勝者が次のラウンドに進み、どちらかのチームがチャンピオンになるまで同じルールに従ってペアになります。 各チームの最終ランキングは、負けたラウンドによって決まります。チャンピオンが 1 位、ファイナリストが 2 位、準決勝進出者が 3 位、準々決勝進出者が 5 位というように、最初に負けたラウンドのサイズが決まります。$2^r$場所に対応する$2^{k-r}+1$そのラウンドの敗者全員に。 

トーナメントの後、結果のハッシュは次のように計算されます。$$h = \left( \sum_{i=1}^{2^k} i \cdot A^{p_i} \right) \bmod 998244353,$$どこ$p_i$チームの場所です$i$、 そして$A$は既知の整数です。 実際の試合結果はすべて失われ、残るのは$k$、$A$、 そして$h$。 タスクは、指定されたハッシュを生成できるか、不可能であることを報告できる、すべてのチームの有効な場所のセットを再構築することです。 

制約を考慮すると、$k$小さい（最大 5）ため、チームの総数は$n=2^k$最大でも 32 です。$n$可能な試合結果を徹底的に調査できます。 課題は、各ラウンドでチームが固定のグループに分割され、トーナメントの構造と一致するのは特定の敗退パターンのみであることです。 たとえば、最初のペアの 2 チームは最初のラウンドで対戦するため、両方とも決勝に進むことはできません。 

複数の消去パターンが同じハッシュを生成する可能性がある場合、明白ではないエッジケースが発生します。 ブラケット制約を無視して任意に場所を割り当てる単純なアプローチでは、ハッシュが一致する場所のセットが生成される可能性があります。$h$数値的には可能ですがブラケットの構造上不可能です。 たとえば、$k=2$、場所の割り当て$[1,2,3,3]$一部の場合は数値的に正しく合計される可能性があります$A$, ただし、チーム 1 と 2 はすぐに対戦するため、両方とも上位 2 位を獲得することはできません。 

## アプローチ

 強引なアプローチでは、考えられるすべてのトーナメント結果を試します。 各試合には 2 人の勝者が考えられます。$2^k - 1$マッチ、あります$2^{2^k - 1}$考えられる結果。 のために$k=5$、つまり$2^{31} \approx 2 \times 10^9$, これは 4 秒の制限が実現可能かぎりぎりです。 すべての結果を単純に生成するには、構成ごとにハッシュを保存して計算する必要もあり、現実的ではありません。 

重要な洞察は、トーナメント ブラケットが再帰的であることです。つまり、左半分と右半分は最終試合まで独立しています。 この構造により、中間者対応アプローチが可能になります。 第 1 ラウンドでトーナメントを 2 つに分割します。 それぞれの半分には、$2^{k-1}$チーム、だから$2^{2^{k-1}-1}$半分あたりの成果。 考えられるすべての消去パターンについて、左半分と右半分のすべての考えられるハッシュを事前計算し、ハッシュ マップに保存できます。 次に、左半分と右半分のハッシュの合計と最終一致の寄与が等しくなるように、左半分と右半分を一致させようとします。$h$これにより、複雑さが軽減されます。$2^{2^k}$おおよそに$2 \cdot 2^{2^{k-1}}$、これは実現可能です$k \le 5$。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^{2^k}) | O(2^k) | k=5 では遅すぎる |
 | 中間者会 | O(2 * 2^{2^{k-1}}) | O(2 * 2^{2^{k-1}}) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. トーナメントの各半分を再帰的に代表します。 のグループの場合$m$チームは、考えられるすべての排除パターンを生成します。 各パターンは、チームが負けた場合のラウンドに応じて順位を割り当てます。 場所の割り当てと、対応するハッシュ モジュロ 998244353 の両方を保存します。 
2. 事前計算力$A$各ハッシュには必要なため、可能な最大の桁まで$A^{p_i}$。 これにより、べき乗剰余演算の繰り返しが回避されます。 
3. ブラケットの左半分の場合（最初の$2^{k-1}$チーム）、考えられるすべての敗退結果を列挙します。 の合計を計算します$i \cdot A^{p_i}$各結果のモジュロ 998244353。 結果のハッシュを、対応する場所の割り当てにマッピングする辞書のキーとして保存します。 
4. ブラケットの右半分（最後の部分）に対してステップ 3 を繰り返します。$2^{k-1}$チーム）。 
5. 左半分と右半分の結果の組み合わせごとに、最終試合で一方の半分の勝者がもう一方の半分の勝者を破った場合の合計ハッシュを計算します。 決勝戦が貢献$A^{1}$勝者（1位）と$A^{2}$敗者 (2 位) の場合、再帰的に計算されたチームの残りの順位を調整します。 結合されたハッシュが等しいかどうかを確認します$h$モジュロ 998244353。 
6. 一致する組み合わせが見つかったら、左半分と右半分を組み合わせて決勝の勝者/敗者を調整することにより、トーナメントの順位配列全体を再構築します。 
7. 一致する組み合わせがない場合$h$、-1を返します。 

機能する理由: 再帰的分割により、生成されたすべての削除パターンが括弧制約に従うことが保証されます。 ハッシュを保存すると、すべてを列挙することなく直接比較できます。$2^k!$順列。 ハーフトーナメントの起こり得るすべての結果をカバーしているため、解決策が存在する場合は、必ず見つかります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 998244353

def powmod(a, b):
    res = 1
    while b:
        if b & 1:
            res = res * a % MOD
        a = a * a % MOD
        b >>= 1
    return res

def generate_places(teams):
    n = len(teams)
    if n == 1:
        return [ { (teams[0], 1): 1 } ]
    
    half = n // 2
    left = generate_places(teams[:half])
    right = generate_places(teams[half:])
    res = []
    for l in left:
        for r in right:
            # final: left winner beats right winner
            combined1 = l.copy()
            combined1.update(r)
            left_winner = min(teams[:half], key=lambda x: l[x])
            right_winner = min(teams[half:], key=lambda x: r[x])
            combined1[left_winner] = 1
            combined1[right_winner] = 2
            for t in teams:
                if t not in (left_winner, right_winner):
                    combined1[t] = max(l.get(t, 0), r.get(t, 0)) + 2
            res.append(combined1)
            # final: right winner beats left winner
            combined2 = l.copy()
            combined2.update(r)
            combined2[right_winner] = 1
            combined2[left_winner] = 2
            for t in teams:
                if t not in (left_winner, right_winner):
                    combined2[t] = max(l.get(t, 0), r.get(t, 0)) + 2
            res.append(combined2)
    return res

def solve():
    k, A, h = map(int, input().split())
    n = 2**k
    teams = list(range(1, n+1))
    
    from itertools import product
    # recursive approach for small k
    # but we can implement simpler backtracking due to small n
    # use meet-in-the-middle
    
    if k == 1:
        # only two teams
        for first_wins in [True, False]:
            p = [0,0]
            if first_wins:
                p[0]=1; p[1]=2
            else:
                p[0]=2; p[1]=1
            total = (1*powmod(A,p[0]) + 2*powmod(A,p[1])) % MOD
            if total==h:
                print(*p)
                return
        print(-1)
        return
    
    # for k<=5, we can brute-force recursively
    powers = [powmod(A,i) for i in range(1, n+3)]
    
    def dfs(arr):
        # arr: current place of each team, 0 if not assigned
        m = len(arr)
        if m==1:
            yield arr
            return
        half = m//2
        for left in dfs(arr[:half]):
            for right in dfs(arr[half:]):
                # left wins
                res = left[:]; res.extend(right[:])
                for i in range(m):
                    if i<half:
                        res[i]=left[i]
                    else:
                        res[i]=right[i]
                yield res
    
    # implement optimized backtracking below
    # For brevity, use precomputed solution from contest
    import itertools
    
    # generate all possible match outcomes
    # this is feasible since n <=32
    def gen(teams):
        if len(teams)==1:
            return [[1]]
        res = []
        n = len(teams)
        half = n//2
        left = gen(teams[:half])
        right = gen(teams[half:])
        for l in left:
            for
```
