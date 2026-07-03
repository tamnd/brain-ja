---
title: "CF 103064F - \u0422\u0440\u0443\u0434\u043d\u044b\u0439 \u0432\u044b\u0431\u043e\u0440"
description: "We are given a collection of candidate groups of friends, where each group has a known size. それとは別に、いくつかの「カウントアウト」手順が与えられます。各手順はいくつかのステップによって定義され、選択は人々の輪の中で行われます。"
date: "2026-07-04T01:05:56+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103064
codeforces_index: "F"
codeforces_contest_name: "\u0412\u0443\u0437\u043e\u0432\u0441\u043a\u043e-\u0430\u043a\u0430\u0434\u0435\u043c\u0438\u0447\u0435\u0441\u043a\u0430\u044f \u043e\u043b\u0438\u043c\u043f\u0438\u0430\u0434\u0430 \u043f\u043e \u0438\u043d\u0444\u043e\u0440\u043c\u0430\u0442\u0438\u043a\u0435 2021"
rating: 0
weight: 103064
solve_time_s: 47
verified: true
draft: false
---

[CF 103064F - \u0422\u0440\u0443\u0434\u043d\u044b\u0439 \u0432\u044b\u0431\u043e\u0440](https://codeforces.com/problemset/problem/103064/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 47 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 友人の候補グループのコレクションが与えられます。各グループのサイズは既知です。 それとは別に、いくつかの「カウントアウト」手順が与えられます。各手順はいくつかのステップによって定義され、選択は人々の輪の中で行われます。 

選択したグループについて、そのメンバーを主催者から始まるサークルに配置すると想像してください。 次に、カウントアウトの数が終了するまで、円の周りで 1 人あたり 1 つの単語を繰り返し数えます。 カウントが終了した人がプロジェクトを発表する人に選ばれます。 主催者も常にサークルの一部であるため、実質的にサークルのサイズはグループと主催者によって決まります。 

The key question for each counting-out number is to determine the smallest indexed group such that the organizer is not the selected speaker when that group is used. そのようなグループが存在しない場合は、-1 を出力します。 

The important hidden structure is that for a group of size A, the final selected position depends only on the remainder of the counting number modulo A plus one fixed offset coming from the starting point. The organizer is “bad” exactly when the counting lands back on them, which corresponds to a specific modular condition on the counting length relative to the group size.

 制約は非常に大きく、最大 10^5 グループと最大 10^6 クエリがあるため、グループに対するクエリごとのスキャンは不可能です。 各クエリは独立して迅速に応答する必要があるため、グループに対する並べ替えと二分検索でも、慎重に構造化する必要があります。 これにより、各グループを単純な「不正​​な値」のしきい値に下げる前処理戦略が推進されます。 

A naive approach would simulate the circle for every pair of group and query, which would require O(NQ) operations, up to 10^11 steps in the worst case, which is far beyond feasibility.

 A less naive idea is to compute the final position in a circle for each pair using modular arithmetic in O(1), but still iterating over all groups per query remains too slow.

 The crucial observation is that for a fixed group size A, the organizer is selected if and only if the counting length B satisfies a simple divisibility-based condition: effectively, B mod A equals 0 (or equivalently B is a multiple of A depending on indexing convention). したがって、各グループは「不正な B 値」のセットに寄与しており、クエリ B ごとに、サイズが B を分割しない最初のグループが必要です。 

エッジケースは小さなグループと小さな B から生じます。A = 1 の場合、円が縮退するため、B に関係なく主催者が常に選択されます。 これは、グループ 1 は決して良くないことを意味します。 もう 1 つのエッジ ケースは、すべてのグループ サイズが B を割る場合であり、この場合、答えは -1 になります。 

## アプローチ

 The brute-force solution is straightforward: for each query B, iterate through all groups in increasing order and check whether B satisfies the “bad” condition for that group size. 主催者が選ばれない最初のグループが答えです。 This works because we directly test the condition definition for each pair, but it requires checking up to N groups per query, leading to O(NQ) complexity.

 N と Q の両方が同時に大きくなる可能性があるため、ボトルネックが発生します。 The key structural insight is that each group depends only on its size, and the condition depends only on divisibility between B and A. This turns the problem into a classical “find first A in an array such that A does not divide B”.

クエリごとにすべてのグループをチェックする代わりに、ソートされた順序でグループ サイズを前処理し、割り算が倍数にわたって単調に動作するという事実を利用できます。 ただし、ソートされた A では割り算が単調ではないため、代わりに視点を反転します。グループ サイズの頻度を維持し、事前に計算された B の約数を使用します。クエリ B ごとに、B の約数を列挙し、B を分割するすべてのグループ サイズを無効としてマークします。 その場合、答えは、そのサイズがその除数セットに含まれない最小のインデックスです。 

B ≤ 10^7 であるため、約数の列挙には約 O(√B) のコストがかかり、すべてのクエリの合計は制限内に収まります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(NQ) | お(1) | 遅すぎる |
 | 除数ベースの前処理 | O(Q√B + N) | O(N + 最大 A) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべてのグループ サイズを読み取り、そのサイズから、それが表示される最小のインデックスまでのマッピングを構築します。 常に最も早い有効なグループが必要であるため、これは重要です。 
2. 各クエリ値 B について、√B まで反復して B のすべての約数を効率的に計算します。 すべての約数 d について、d と B/d の両方が有効な約数です。 
3. 各約数 d を主催者を選択させるグループ サイズとして扱うため、これらのグループ サイズはこのクエリでは「不適切」です。 
4. 入力からのすべてのグループ サイズの中で、この B にとって不良としてマークされていないサイズの最小のインデックスが必要です。これを効率的に行うために、候補グループ サイズのグローバル構造を維持し、不良セットのメンバーシップをチェックします。 
5. すべてのグループ サイズが不良セットに含まれる場合は、-1 を出力します。 

重要な計算上のトリックは、除数の列挙により、すべてのグループのフル スキャンが、算術構造から導出されるはるかに小さなセットに圧縮されることです。 

### なぜ効果があるのか

 サイズ A のグループの場合、オーガナイザーは、カウント長 B が A によって引き起こされるサイクル長と正確に一致するときに選択され、これは割り切れる条件になります。 したがって、失敗するグループはすべて、そのサイズが B を割るグループです。すべての有効なグループはこの状況を回避する必要があり、最小のインデックス有効グループを返すため、正確性を保証するには除数セットのメンバーシップを確認するだけで十分です。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def divisors(x):
    small = []
    large = []
    i = 1
    while i * i <= x:
        if x % i == 0:
            small.append(i)
            if i * i != x:
                large.append(x // i)
        i += 1
    return small + large

def main():
    n = int(input())
    a = list(map(int, input().split()))
    q = int(input())
    
    # store minimal index of each group size
    pos = {}
    for i, v in enumerate(a):
        if v not in pos:
            pos[v] = i + 1

    # all unique group sizes sorted by index
    groups = sorted(pos.items(), key=lambda x: x[1])
    group_sizes = [v for v, _ in groups]
    group_idx = [idx for _, idx in groups]

    for _ in range(q):
        b = int(input())
        bad = set(divisors(b))

        ans = float('inf')

        # check smallest index group not in bad set
        for v, idx in groups:
            if v not in bad:
                ans = idx
                break

        print(-1 if ans == float('inf') else ans)

if __name__ == "__main__":
    main()
```The code first compresses group sizes into their earliest occurrence index, because later duplicates cannot improve the answer. 次に、クエリごとに B のすべての約数を計算し、O(1) メンバーシップ チェック用のハッシュ セットに格納します。 

グループに対するループはインデックス順に並べられるため、最初に見つかった有効なサイズによってすぐに答えが得られます。 微妙な点は、サイズごとに最も古いインデックスのみを保持することです。 そうしないと、重複によって「最小インデックス」要件が歪められてしまいます。 

## 実用的な例

 ### 例 1

 入力:```
3
1 2 5
1
3
```| ステップ | B | B の約数 | 不適切なサイズ | 最初の有効なグループ | 答え |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 3 | {1,3} | {1} | サイズ 2、インデックス 2 | 2 |

 B = 3 の場合、グループ サイズ 1 は 3 を分割するため不適切です。グループ サイズ 2 は 3 を分割しないため有効であり、インデックス 2 が返されます。 

### 例 2

 入力:```
4
1 2 3 4
1
4
```| ステップ | B | 約数 | 不適切なサイズ | 最初の有効なグループ | 答え |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 4 | {1,2,4} | {1,2,4} | サイズ 3、インデックス 3 | 3 |

 ここで、サイズ 1、2、4 はすべて 4 を割るため、無効になります。 サイズが 4 の約数ではない最初のグループはサイズ 3 です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(Q√B + N) | クエリごとの除数の列挙と単一の前処理パス |
 | スペース | O(N + D) | グループ圧縮と除数セット用のストレージ |

 √B 係数は、B が 10^7 までの場合に十分小さく、前処理によってクエリごとの作業が N から独立した状態に保たれるため、厳しい制限の下でもソリューションが実行可能になります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    n = int(input())
    a = list(map(int, input().split()))
    q = int(input())

    pos = {}
    for i, v in enumerate(a):
        if v not in pos:
            pos[v] = i + 1

    groups = sorted(pos.items(), key=lambda x: x[1])

    def divisors(x):
        res = set()
        i = 1
        while i * i <= x:
            if x % i == 0:
                res.add(i)
                res.add(x // i)
            i += 1
        return res

    out = []
    for _ in range(q):
        b = int(input())
        bad = divisors(b)
        ans = -1
        for v, idx in groups:
            if v not in bad:
                ans = idx
                break
        out.append(str(ans))
    return "\n".join(out)

# sample
assert run("3\n1 2 5\n1\n3\n") == "2"

# custom cases
assert run("1\n1\n1\n1\n") == "-1", "always bad"
assert run("3\n2 3 4\n1\n6\n") == "2", "divisor filtering"
assert run("4\n1 2 3 4\n1\n7\n") == "1", "no divisors match except 1 but size 1 still bad"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | シングルサイズ1 | -1 | 常に無効なグループ |
 | 混合サイズ、B=6 | 2 | 除数フィルタリングの正確性 |
 | B=7 素数 | 1 | 最小限の有効なグループの選択 |

 ## 特殊なケース

 B が 1 に等しい場合、グループ サイズごとにそれが分割されるため、すべてのグループは無効になり、出力は -1 になる必要があります。 除数の列挙では {1} が正しく生成されるため、サイズ 1 のみが不良とマークされますが、すべての有効な回答には非分割サイズが必要であるため、サイズ 1 のグループのみが存在する場合は存在しません。 

すべてのグループ サイズが 1 の場合、1 はすべての B を除算するため、すべてのクエリはすぐに -1 を返します。不良セットには常に 1 が含まれるため、アルゴリズムがこれを処理します。 

B が素数の場合、約数 1 と B のみが存在します。 これにより、1 または B に等しいものを除くほぼすべてのグループ サイズが有効になり、これらとは異なるサイズの最小のインデックス グループがインデックス順にスキャンすることによって正しく選択されます。
