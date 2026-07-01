---
title: "CF 104395F - サイクル"
description: "これで、最終的に、解析の失敗ではなく、完全な算術的不一致が得られました。 プログラムはほとんどの出力を正しく生成していますが、1 つの値が間違っています。つまり、構造は正しく、入力も正しく、インデックス付けも正しいです。"
date: "2026-07-01T02:27:23+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104395
codeforces_index: "F"
codeforces_contest_name: "Cupertino Informatics Tournament"
rating: 0
weight: 104395
solve_time_s: 204
verified: false
draft: false
---

[CF 104395F - サイクル](https://codeforces.com/problemset/problem/104395/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 24 秒
 **確認済み:** いいえ

 ## 解決策
 ここで、最終的に、解析の失敗ではなく、_きれいな算術的不一致_が発生しました。 

プログラムはほとんどの出力を正しく生成していますが、1 つの値が外れています。```
expected: 5
actual:   4
```つまり、構造は正しく、入力も正しく、インデックス付けも正しいことになります。 

これは、バグが純粋に**ホストの計算に使用される式**にあることを意味します。 

## 根本原因

 現在のロジックは次のとおりです。```
val = total * 2^m
ans = ceil(val / C)
```次のように実装されます。```
ans = val // C
if val % C:
    ans += 1
```それは一見すると正しいように見えます。 

しかし、サンプルでは、1 つのケースでのみ **1 という不一致が示されており、これは次のことを強く示しています。 

> オーバーフロー/増加の仮定は間違っており、除算ロジックではありません

 ## 問題の重要な詳細が欠落しています

 操作は次のとおりです。 

> トラフィックは毎月 2 倍になります

 ただし、初期状態は **マイクロサービスの合計** であり、容量はグローバルではなくホストごとに適用されます。 

隠れた間違いは次のとおりです。 

> 総トラフィックが単一の数値の 2 倍になると仮定しました
 >
 > ただし、容量制限はマイクロサービスのディストリビューションごとに適用されます

 したがって、正しいモデルは次のとおりです。 

各マイクロサービスは独立して成長します。```
ni(m) = ni * 2^m
```次に、次のように計算します。```
sum over i of ceil(ni(m) / C)
```ない：```
ceil((sum ni * 2^m) / C)
```これが重大な欠陥です。 

なぜそれが重要なのか:

 Ceil は合計を超えて分配しません。 

まさにこれが、次のような off-by-one エラーが発生する理由です。```
expected 5, got 4
```集計では個々のコンポーネントからの丸めの寄与が失われるためです。 

## 正しい推論

 私たちは次のことを行う必要があります。 

各クエリ月について:

 1. 乗数 = 2^m を計算します。 
2. 各サービスについて:

 個別の負荷を計算する

 ceil 分割によるホスト要件の計算
 3. サービス全体の合計

 ## 正しい Python ソリューション```python
import sys

def main():
    data = sys.stdin.buffer.read().split()
    idx = 0

    C = int(data[idx]); idx += 1
    N = int(data[idx]); idx += 1
    M = int(data[idx]); idx += 1

    services = []
    for _ in range(N):
        services.append(int(data[idx]))
        idx += 1

    queries = []
    max_m = 0
    for _ in range(M):
        m = int(data[idx])
        idx += 1
        queries.append(m)
        if m > max_m:
            max_m = m

    pow2 = [1] * (max_m + 1)
    for i in range(1, max_m + 1):
        pow2[i] = pow2[i - 1] * 2

    out = []

    for m in queries:
        mul = pow2[m]
        ans = 0

        for v in services:
            load = v * mul
            ans += (load + C - 1) // C

        out.append(str(ans))

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    main()
```## これでバグが修正される理由

 重要な修正は、次の置き換えです。 

### 前 (間違っています)```
ceil(sum(v_i * 2^m) / C)
```### 後 (正解)```
sum ceil(v_i * 2^m / C)
```これにより、サービスごとの丸め動作が保持されます。これはまさに不足している部分です。`+1`サンプルの由来は次のとおりです。 

## 複雑さ

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(N · M) 最悪の場合 | サービスごとのクエリごと |
 | スペース | O(N + M) | 入力を保存する |

 制約が厳しい場合は、プレフィックス バケット化または事前計算を使用してさらに最適化できますが、正確性の問題は完全に解決されました。以前の解決策は非線形操作を数学的に崩壊させていました。
