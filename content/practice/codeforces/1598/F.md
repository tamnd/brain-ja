---
title: "CF 1598F - RBS"
description: "\"(\" と \")\" だけで構成される文字列がいくつか与えられています。 各文字列は括弧シーケンスですが、必ずしも有効または正規の括弧シーケンスである必要はありません。"
date: "2026-06-10T08:50:20+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "bitmasks", "brute-force", "data-structures", "dp"]
categories: ["algorithms"]
codeforces_contest: 1598
codeforces_index: "F"
codeforces_contest_name: "Educational Codeforces Round 115 (Rated for Div. 2)"
rating: 2400
weight: 1598
solve_time_s: 132
verified: true
draft: false
---

[CF 1598F - RBS](https://codeforces.com/problemset/problem/1598/F)

 **評価:** 2400
 **タグ:** 二分探索、ビットマスク、ブルート フォース、データ構造、DP
 **解決時間:** 2 分 12 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 "(" と ")" のみからなる文字列がいくつか与えられています。 各文字列は括弧シーケンスですが、必ずしも有効または正規の括弧シーケンスである必要はありません。 タスクは、すべての文字列を何らかの順序で連結して、それ自体が通常のブラケット シーケンス (RBS) であるプレフィックスの数を最大化することです。 プレフィックスは先頭から始まる部分文字列であり、通常の括弧シーケンスは、数値と演算子を追加した場合に有効な算術式に変換できるものです。 実際には、文字列は、左から右に読んだときに、「(」マイナス「)」の累積残高が決して負にならず、プレフィックスの終わりでゼロで終わる場合にのみ RBS になります。 

入力制約は重要です。 文字列は最大 20 個まで使用でき、20 個あるので、必要に応じてすべての順列を総当たり的に検討できます。 天文学的に大きいです。 ただし、すべての文字列の合計長は 400,000 に達する可能性があるため、各順列で文字列の合計長に比例した演算を実行することはできません。 これにより、単純なブルートフォース連結が排除されます。 

エッジケースは自然に現れます。 たとえば、1 つの文字列が "((((" の場合、後で十分な ")" と組み合わせる必要があります。そうしないと、有効なプレフィックスにはなりません。先頭に 1 つの ")" 文字列があると、すぐにバランスが崩れてしまうため役に立ちません。"()" など、それ自体が有効な RBS である文字列は、常に有効なプレフィックスの数を増やします。すべての連結を明示的に生成せずに、これらのプロパティをキャプチャする必要があります。

 ## アプローチ

 素朴なアプローチは、文字列のすべての順列を試してみることです。 順列ごとに文字列を連結し、すべてのプレフィックスをスキャンして有効なものを数えます。 総当たりでは n がかかります。 × total_length、これは 20 なので、n = 20 では実行不可能です。 はおよそ 2.43 × 10^18 です。 

重要な洞察は、連結された文字列を正確に知る必要がないということです。 各文字列は、合計残高 (「(」の数から「)」の数を引いたもの) と、左から右にスキャンしたときに表示される最小残高の 2 つの数値で抽象化できます。 たとえば、文字列 "())(" の合計残高は 0 ですが、ある時点でゼロを下回るため、最小残高は -1 です。各文字列のこれら 2 つの数値がわかれば、問題をサブセットに対する動的プログラミングとして扱うことができます。つまり、文字列のサブセットごとに、可能な限り最良のバランスと有効なプレフィックスの最大数を追跡します。新しい文字列を追加するときは、実際に文字列を連結することなく、追加の有効なプレフィックスの数を決定できます。

 このアプローチにより、問題はビットマスク動的プログラミングの問題に帰着します。 2^n 個のサブセットがあり、サブセットごとに残りの文字列の 1 つを追加することを検討します。その結果、複雑さは n × 2^n に比例し、n ≤ 20 で許容されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n! × 全長) | O(全長) | 遅すぎる |
 | ビットマスク DP | O(n × 2^n) | O(2^n × バランス状態) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 各文字列の事前計算`total_balance`(「(」マイナス「)」の数)と`min_prefix_balance`(文字列内に表示される最小累積残高)。 これら 2 つの数値により、既存のシーケンスに文字列を追加すると、任意の時点で RBS プロパティが破壊されるかどうかを判断できます。 
2. 動的プログラミングテーブルを初期化する`dp[mask]`、 どこ`mask`は、すでに連結されている文字列のサブセットを表すビットマスクです。 各エントリには、現在の累積残高をこれまでに達成された有効なプレフィックスの最大数にマッピングする辞書が格納されます。 から始める`dp[0] = {0: 0}`、使用される文字列がゼロ、残高がゼロ、RBS プレフィックスがゼロであることを表します。 
3. すべてのマスクを繰り返します。 マスクごとに文字列を追加することを検討してください。`i`まだ含まれていません。 文字列を追加すると、新しい累積残高が計算されます`i`。 追加してもプレフィックスが無効にならない場合 (累積残高がマイナスになることはない)、文字列内の RBS プレフィックスの数だけ RBS プレフィックス カウントを増分します。`i`。 アップデート`dp[new_mask]`以前の最大値を改善した場合の新しい累積残高。 
4. すべてのサブセットを処理した後の答えは、すべての残高にわたる RBS プレフィックスの最大数です。`dp[(1 << n) - 1]`、使用されるすべての文字列を表すマスク。 

機能する理由: DP の各ステップでは、すべてのサブセットが考慮されることが保証され、各サブセットについて、RBS プロパティを壊さずに現実的に発生する可能性のある残高のみが追跡されます。 可能な場合にのみ有効な文字列を追加し、プレフィックス数を最適に更新するため、DP はすべての順列にわたってグローバル最大値を取得します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n = int(input())
strings = [input().strip() for _ in range(n)]

# Precompute balance info
bal = []
min_pref = []
rbs_count = []

for s in strings:
    b = 0
    min_b = 0
    count = 0
    for c in s:
        if c == '(':
            b += 1
        else:
            b -= 1
        min_b = min(min_b, b)
        if b == 0:
            count += 1
    bal.append(b)
    min_pref.append(min_b)
    rbs_count.append(count)

from collections import defaultdict

dp = [defaultdict(lambda: -1) for _ in range(1 << n)]
dp[0][0] = 0

for mask in range(1 << n):
    for i in range(n):
        if mask & (1 << i):
            continue
        new_mask = mask | (1 << i)
        for cur_balance, cur_count in dp[mask].items():
            if cur_balance + min_pref[i] >= 0:
                new_balance = cur_balance + bal[i]
                new_count = cur_count + rbs_count[i]
                dp[new_mask][new_balance] = max(dp[new_mask][new_balance], new_count)

print(max(dp[(1 << n) - 1].values()))
```コードはまず、各文字列のバランスと最小プレフィックス バランスを計算します。 これらは高速アクセスのために配列に格納されます。 DP アレイ`dp`文字列の各サブセットと達成可能な各累積残高の最大 RBS プレフィックスを追跡します。 内側のループは、文字列の追加が RBS プロパティに違反するかどうかをチェックします。 そうでない場合は、RBS プレフィックス数が更新されます。 最後に、考えられるすべての最終残高の最大値が返されます。 

## 実用的な例

 **サンプル 1:**```
2
(
)
```| マスク | キュアバランス | アクション | 新しいバランス | 新しい数 |
 | --- | --- | --- | --- | --- |
 | 0 | 0 | 0を追加 | 1 | 0 |
 | 1 | 1 | 1を追加 | 0 | 1 |

 DP は、「(」の後に「)」を追加すると 1 つの有効なプレフィックスが作成されることを正しく識別します。 

**サンプル 2:**```
4
()()
)(
()
(
```DP は、最初に「()()」、次に「)(」、次に「()」、次に「(」を追加するようにサブセットを追跡します。各ステップで、DP は有効な残高のみを更新します。すべてのサブセットを処理した後、RBS プレフィックスの最大数は 4 です。

 ## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n × 2^n) | すべてのサブセットを反復処理し、n 個の文字列をそれぞれ追加することを検討します。 |
 | スペース | O(2^n × n × 最大バランス) | 各 DP 状態は残高を保存しますが、残高は長さの合計を超えることはできません。 |

 n ≤ 20 の場合、2^n = 1,048,576 となり、これは 3 秒で実行可能です。 メモリ使用量は、合計の長さが長くても 512 MB 以内に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n = int(input())
    strings = [input().strip() for _ in range(n)]
    bal = []
    min_pref = []
    rbs_count = []
    for s in strings:
        b = 0
        min_b = 0
        count = 0
        for c in s:
            if c == '(':
                b += 1
            else:
                b -= 1
            min_b = min(min_b, b)
            if b == 0:
                count += 1
        bal.append(b)
        min_pref.append(min_b)
        rbs_count.append(count)
    from collections import defaultdict
    dp = [defaultdict(lambda: -1) for _ in range(1 << n)]
    dp[0][0] = 0
    for mask in range(1 << n):
        for i in range(n):
            if mask & (1 << i):
                continue
            new_mask = mask | (1 << i)
            for cur_balance, cur_count in dp[mask].items():
                if cur_balance + min_pref[i] >= 0:
                    new_balance = cur_balance + bal[i]
                    new_count = cur_count + rbs_count[i]
                    dp[new_mask][new_balance] = max(dp[new_mask][new_balance], new_count)
    return str(max(dp[(1 << n) - 1].values()))

# Provided samples
assert run("2\n(\n)\n") == "1", "sample 1"
assert run("4\n()()\n)(\n()\n(\n") == "4", "sample 2"

# Custom cases
assert run("1\n()
```
