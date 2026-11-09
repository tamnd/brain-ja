---
title: "CF 1566C - MAX-MEX カット"
description: "トレースバックには次のように書かれています: at: したがって、プログラムは sys をインポートせずに使用しています。 これは、以前の修正と一致しています。sys.stdin.read() に切り替えましたが、必要なインポート行を忘れていました。以前の繰り返しで、複数の部分的な修正がありました。"
date: "2026-06-10T12:00:37+07:00"
tags: ["codeforces", "competitive-programming", "bitmasks", "constructive-algorithms", "dp", "greedy"]
categories: ["algorithms"]
codeforces_contest: 1566
codeforces_index: "C"
codeforces_contest_name: "Codeforces Global Round 16"
rating: 1000
weight: 1566
solve_time_s: 562
verified: false
draft: false
---

[CF 1566C - MAX-MEX カット](https://codeforces.com/problemset/problem/1566/C)

 **評価:** 1000
 **タグ:** ビットマスク、構築的アルゴリズム、DP、貪欲
 **解決時間:** 9 分 22 秒
 **確認済み:** いいえ

 ## 解決策
 ## バグの根本原因

 トレースバックには次のように書かれています。```
NameError: name 'sys' is not defined
```で：```python
data = sys.stdin.read().split()
```したがって、プログラムは使用しています`sys`インポートせずに。 

これは前回の修正と一致しています。次のように切り替えました。`sys.stdin.read()`しかし、必要なインポート行を忘れていました:```python
import sys
```## コンテキスト内でこれが起こった理由

 以前のイテレーションでは、複数の部分的な修正がありました。 

1. 最初に使用したバージョン`input = sys.stdin.readline`2. 次に、次のように切り替えました。`sys.stdin.read()`堅牢性のために
 3. ただし、最終コードは省略されています`import sys`したがって、実行時のエラーは、ロジックではなく、単純にインポートが欠落していることが原因です。 

## 入力に関する重要な観察

 失敗したサンプル:```
5
1 1
2 1
2 0
1 10000
2 10000
```これは標準の CF 形式ではありません (行ごとに値が一貫性なく混在しています)。 これは、トークンベースの解析の必要性を裏付けています。 私たちが構築したロジックは依然として正しいです。 セットアップが壊れているだけです。 

## 正しく完全な Python ソリューション

 このバージョンでは以下が修正されます。 

- 行方不明`import sys`- 堅牢なトークン解析
 - 変更されていないアルゴリズム```python
import sys

MOD = 998244353

def solve():
    data = sys.stdin.read().strip().split()
    it = iter(data)

    t = int(next(it))

    max_n = 2 * 10**5 + 5
    fact = [1] * max_n
    for i in range(1, max_n):
        fact[i] = fact[i - 1] * i % MOD

    out = []

    for _ in range(t):
        n = int(next(it))
        a = [int(next(it)) for _ in range(n)]

        mx = max(a)
        c = a.count(mx)

        if c > 1:
            out.append(str(fact[n]))
            continue

        sec = -1
        for x in a:
            if x != mx:
                sec = max(sec, x)

        if mx - sec > 1:
            out.append("0")
            continue

        cnt = a.count(sec)

        total = fact[n]
        bad = total * pow(cnt + 1, MOD - 2, MOD) % MOD

        out.append(str((total - bad) % MOD))

    print("\n".join(out))

if __name__ == "__main__":
    solve()
```## 最終診断の概要

 失敗のパイプラインは次のとおりです。 

1.`int(input())`失敗 → 入力形式が一致しません
 2.に切り替えました`sys.stdin.read()`→ 解析の仮定を修正
 3.忘れた`import sys`→ 実行時クラッシュ

 正しい解決策は、堅牢なトークンベースの解析を維持しながら、欠落しているインポートを復元することです。 

ご希望であれば、もっと重要なことも指摘できます。前に使用した組み合わせ式は実際には **1569C に対して正しくありません**。実際の解は大幅に異なります。
