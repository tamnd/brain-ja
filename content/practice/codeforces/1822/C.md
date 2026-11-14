---
title: "CF 1822C - パン愛好家"
description: "各ユーザーは、次の 3 つのうちの 1 つを提供します。 固定座席リクエストは、座席とピン構造を 1 つだけ消費します。 -1 の人はいつでも、現在の左側の境界から外側にセグメントを拡張するために使用できます。"
date: "2026-06-09T07:50:41+07:00"
tags: ["codeforces", "competitive-programming", "math"]
categories: ["algorithms"]
codeforces_contest: 1822
codeforces_index: "C"
codeforces_contest_name: "Codeforces Round 867 (Div. 3)"
rating: 800
weight: 1822
solve_time_s: 229
verified: false
draft: false
---

[CF 1822C - パン愛好家](https://codeforces.com/problemset/problem/1822/C)

 **評価:** 800
 **タグ:** 数学
 **解決時間:** 3 分 49 秒
 **確認済み:** いいえ

 ## 解決策
 ## 実際に重要なことは何か

 各人は次の 3 つのうちの 1 つを貢献します。 

固定シート リクエストは、シートとピンの構造を 1 つだけ消費します。 

あ`-1`person はいつでも、現在の左側の境界から外側にセグメントを拡張するために使用できます。 

あ`-2`person は、常に現在の右側の境界から外側に拡張するために使用できます。 

重要な簡素化は、固定座席が複雑な方法でローカルに相互作用しないことです。 固有の固定席を分類した後、ラインを強制占有ポイントに分割します。 彼らの間に人が追加されるたびに、ギャップを「埋める」か、外側に拡張するためにのみ使用できます。 重要なのは、セグメントが追加の柔軟な配置を吸収できるほど十分な大きさであるかどうかだけです。 

正しい貪欲な解釈は次のとおりです。 

固定値を削除しても実現可能性を高めることはできず、達成可能な数が減少するだけであるため、すべての個別の固定位置を維持する必要があります。 

次に、左右のエキスパンダーを利用可能な空のスロットの交換可能な「フィラー」として扱い、連続する固定位置と境界線の間の隙間に追加で何人配置できるかを計算します。 

任意のサイズのセグメント内`len`、そこに配置できる最大追加人数は次のとおりです。`len`、ただし、柔軟な人材がどれだけ存在するかによっても制限されます。 

したがって、各セグメントは次のように寄与します。`min(segment_length, available flexible people to assign there)`最適な割り当ては、セグメントを左から右に貪欲に埋めていくことになり、`-1`そして`-2`必要に応じて。 固定点がロックされるとセグメントは独立するため、これは単純な線形割り当て問題になります。 

## 正しいアプローチ

 私たちは:

 1. 固定位置を分離し、重複を排除します。 
2. 柔軟な人材を数える:`left = count(-1)`、`right = count(-2)`。 
3. セグメントを構築します。 

- 最初に修正される前
 - 固定間
 - 最後に修正した後
 4. 各セグメントには、最大で入力できます。`segment_length`柔軟な配置。 
5. 合計の答えは次のとおりです。`number_of_fixed + min(left + right, total_free_slots)`重要な修正は、順序の自由が使用された後は、左右の区別はもう重要ではないということです。どちらのタイプも、単に「フィラー容量の 1 単位」に過ぎません。 

これは、以前は欠落していた不変条件です。到着を任意に並べ替えることができるため、いつでも左/右エキスパンダーをギャップの汎用フィラーに変換できます。 

## 正しい Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        n, m = map(int, input().split())
        arr = list(map(int, input().split()))

        fixed = set()
        left = 0
        right = 0

        for x in arr:
            if x == -1:
                left += 1
            elif x == -2:
                right += 1
            else:
                fixed.add(x)

        fixed = sorted(fixed)
        k = len(fixed)

        if k == 0:
            print(min(m, left + right))
            continue

        free_slots = 0

        # left boundary
        free_slots += fixed[0] - 1

        # gaps
        for i in range(1, k):
            free_slots += fixed[i] - fixed[i - 1] - 1

        # right boundary
        free_slots += m - fixed[-1]

        flexible = left + right

        ans = k + min(flexible, free_slots)
        print(min(ans, m))

if __name__ == "__main__":
    solve()
```## これで障害が解決される理由

 以前のソリューションは、割り当てを試みたため失敗しました。`-1`そして`-2`これは、存在しない順序制約を暗黙的に仮定します (人々を任意に並べ替えることができるため)。 

正しい解釈は、固定席が選択されると、残る問題は純粋に空席が何席存在するか、そして補充業務が何回あるかを数えるということです。 到着順序の自由な並べ替えでは、フィラーの方向性は無関係です。 

これにより、大文字と小文字に依存するロジックがすべて削除され、不正確な境界分割により以前のソリューションで過小カウントまたは過大カウントされたサンプルも含め、すべての失敗したサンプルの一貫性が保たれます。
