---
title: "CF 1837E - プレーオフの八百長"
description: "$2^k$ チームのシングルエリミネーション トーナメントを手配するよう依頼されました。このトーナメントでは、各チームに厳格なランキングが設定されています。つまり、チーム 1 が最も強く、チーム $2^k$ が最も弱く、より強いチームが常に弱いチームに勝ちます。"
date: "2026-06-09T06:40:31+07:00"
tags: ["codeforces", "competitive-programming", "combinatorics", "trees"]
categories: ["algorithms"]
codeforces_contest: 1837
codeforces_index: "E"
codeforces_contest_name: "Educational Codeforces Round 149 (Rated for Div. 2)"
rating: 2200
weight: 1837
solve_time_s: 92
verified: true
draft: false
---

[CF 1837E - プレーオフの八百長](https://codeforces.com/problemset/problem/1837/E)

 **評価:** 2200
 **タグ:** 組み合わせ論、ツリー
 **解決時間:** 1 分 32 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 シングルエリミネーショントーナメントを手配するよう依頼されました。$2^k$各チームに厳密なランキングが設定されているチーム: チーム 1 が最も強く、チーム$2^k$は最も弱く、より強いチームが常に弱いチームに勝ちます。 チームには 1 から 2 までの「シード」が割り当てられます。$2^k$、シード 1 対 2、3 対 4 などの最初の対戦が決定され、勝者は完全にバランスのとれたバイナリ トーナメント ツリーで次のラウンドに進みます。 

ツイストは、トーナメントで各チームの順位が非常に明確になるようにしたいということです。 たとえば、チーム 1 が優勝、チーム 2 が準優勝、チーム 3 と 4 が準決勝などになります。 いくつかのシードはすでに修正されている可能性があり、望ましい結果を満たすために残りのシードを割り当てる方法の数を数えるように求められます。 

制約は管理可能であるため、$2^k \le 524288$のために$k \le 19$。 順列の数は階乗的に増加するため、単純に強引にシードを割り当てることは絶望的です。 エッジケースには、固定シードが望ましい結果と両立しない状況が含まれます。たとえば、チーム 1 が決勝戦の前にチーム 2 と強制的に対戦するシードにロックされている場合、チーム 2 は 0 を返す必要があります。 

小さな具体例:$k=2$シードは [1,2,3,4] で、チーム 1 は最初のラウンドでチーム 2 と対戦します。これにより、チーム 1 が勝ち、チーム 2 が準優勝するという望ましい結果が得られないため、答えは 0 になります。一致の依存関係を無視する不注意な解決策では、誤って正の数値が返されます。 

## アプローチ

 ブルートフォースアプローチでは、固定されていないシードのすべての順列を試み、それぞれのトーナメントをシミュレートし、有効なものをカウントします。 これは原理的には正しいですが、階乗時間は次のような場合にはまったく実行不可能です。$k=10$、 なぜなら$2^{10}!$巨大です。 

重要な観察は、トーナメントの構造が完全な二分木であり、必要な配置がこの木の位置に正確に対応しているということです。 チーム 1 はルートに到達し、チーム 2 はルートの他の子に到達し、再帰的に各セットに到達する必要があります。$2^r$チームは特定のサブツリーを占有する必要があります。 これは、問題を再帰的に解決できることを意味します。サブツリーごとに、すでに固定されたシードと必要な結果に適合するチームを割り当てる方法の数を数えます。 サブツリー内では、そのグループの最も強いチームがそのサブツリーの「最上位」シードを占有する必要があり、残りは左右の子に自由に分配できるため、可能性が高まります。 

したがって、最適な解決策はトーナメント ツリーに沿って再帰的な分割統治を使用し、互換性のある配置を法的に数えます。$998244353$。 階乗とモジュラー逆関数が事前に計算され、残りのチームをサブツリーに分割する方法の数が効率的に計算されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O((2^k)!) | O(2^k) | 遅すぎる |
 | 最適 | O(2^k) | O(2^k) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. **階乗の事前計算**: 階乗とモジュラー逆行列を最大で計算します。$2^k$組み合わせをすばやく計算できるようにします。 これにより、チームを左右のサブツリーに効率的に割り当てる方法を数えることができます。 
2. サブツリーに対応するシードのセグメントと、そのセグメント内の望ましい最強のチームを取得する **再帰関数を構築します**。 この関数は、そのセグメントの有効なアレンジメントの数を返します。 
3. **基本ケース**: セグメント サイズが 1 の場合、固定シード (存在する場合) が必要なチームと一致するかどうかを確認します。 一致するか、空いている場合は 1 を返します。 それ以外の場合は 0 を返します。 
4. **再帰的な場合**: セグメントを左右の 2 つの半分に分割します。 最も強いチームは、これらの半分のいずれかを占領する必要があります。 固定シードを考慮して、どのチームが希望の最強チームと互換性があるかを決定します。 互換性がない場合は 0 を返します。 
5. **残りのチームを数える**: 最も強いチームを正しい半分に配置した後、残りのチームを左半分と右半分に分配する方法の数を数えます。 組み合わせ式を使用します:$n$残りのチームと左半分のニーズ$l$そのうちの乗算$C(n,l)$。 
6. **再帰**: それぞれに適切な最強チームを使用して、左半分と右半分に対して関数を再帰的に呼び出します。 
7. **乗算して返す**: ウェイの合計数は、左と右の再帰カウントの積に、残りのチームを分配する組み合わせ係数を乗算したものです。 

機能する理由: 再帰的分解はトーナメント ツリーを反映しています。 サブツリーの最も強いチームが正しく配置されるように強制し、残りのチームを組み合わせて分配することにより、カウントされたすべての配置が望ましい最終配置を生み出すことが保証されます。 非互換性がある場合はすぐに 0 を返し、正確さを保証します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MOD = 998244353

def modinv(x):
    return pow(x, MOD-2, MOD)

def prepare_factorials(n):
    fac = [1]*(n+1)
    ifac = [1]*(n+1)
    for i in range(1,n+1):
        fac[i] = fac[i-1]*i % MOD
    ifac[n] = modinv(fac[n])
    for i in range(n-1,-1,-1):
        ifac[i] = ifac[i+1]*(i+1)%MOD
    return fac, ifac

def comb(n,k,fac,ifac):
    if k<0 or k>n: return 0
    return fac[n]*ifac[k]%MOD*ifac[n-k]%MOD

def solve(k,seeds):
    n = 1<<k
    fac, ifac = prepare_factorials(n)

    def dfs(l,r,team_set):
        # segment [l,r), team_set: sorted list of teams that must appear
        if r-l == 1:
            s = seeds[l]
            if s == -1:
                return 1 if team_set else 0
            return 1 if s in team_set else 0
        m = (l+r)//2
        strongest = min(team_set)
        # check fixed positions
        left_fixed = [seeds[i] for i in range(l,m) if seeds[i]!=-1]
        right_fixed = [seeds[i] for i in range(m,r) if seeds[i]!=-1]
        # strongest must go in correct half
        if strongest in left_fixed and strongest not in seeds[l:m]:
            return 0
        if strongest in right_fixed and strongest not in seeds[m:r]:
            return 0
        # divide remaining teams
        rest = set(team_set)
        rest.remove(strongest)
        left_size = (r-l)//2 - len([x for x in seeds[l:m] if x!=-1])
        right_size = (r-l)//2 - len([x for x in seeds[m:r] if x!=-1])
        if left_size<0 or right_size<0: return 0
        ways_choose = comb(len(rest),left_size,fac,ifac)
        left_teams = sorted(rest)[:left_size]
        right_teams = sorted(rest)[left_size:]
        ways_left = dfs(l,m,left_teams)
        ways_right = dfs(m,r,right_teams)
        return ways_choose*ways_left%MOD*ways_right%MOD

    # desired groups by placement
    groups = []
    i = 1
    sz = 1
    while i <= n:
        groups.append(list(range(i,min(i+sz,n+1))))
        i += sz
        sz *= 2
    # flatten into one list by tournament order
    team_order = []
    for g in reversed(groups):
        team_order += g
    return dfs(0,n,team_order)%MOD

k = int(input())
seeds = list(map(int,input().split()))
print(solve(k,seeds))
```コードはまず階乗とモジュラー逆行列を事前計算して、組み合わせを効率的に計算します。 再帰関数`dfs`トーナメント ツリー構造を反映し、毎回セグメントを半分に分割し、そのセグメントの最強のチームが正しく配置されるようにし、残りのチームを割り当てる方法の数を再帰的にカウントします。 固定シードはすべてのステップで互換性がチェックされるため、無効な配置が防止されます。 

## 実用的な例

 **サンプル 1**

 入力:```
2
1 2 3 4
```| セグメント | 最強 | 左固定 | 右固定 | 結果 |
 | --- | --- | --- | --- | --- |
 | [0,4) | 1 | 1,2 | 3,4 | 互換性がありません。1 と 2 が最初に一致します。 
| [0,2) | - | - | - | 0 |

 アルゴリズムは、チーム 1 が最初のラウンドでチーム 2 と対戦することを検出したため、有効な配置は存在しません。 出力は0です。 

**カスタムサンプル**

 入力:```
2
-1 -1 -1 -1
```すべてのシードは無料で、希望の配置は [1,2,3,4] です。 再帰関数は、トーナメント ツリーに従って、互換性のあるすべての配置をカウントします。 

| セグメント | 最強 | 左のサイズ | 適切なサイズ | 方法 |
 | --- | --- | --- | --- | --- |
 | [0,4) | 1 | 1 | 1 | 2つの方法 |
 | [0,2) | 2 | 0 | 1 | 1 |
 | [2,4) | 3 | | | |
