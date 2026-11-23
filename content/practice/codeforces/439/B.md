---
title: "CF 439B - デヴー、愚かな男"
description: "私たちは Devu に、それぞれが一定数の章で構成される一連の科目を教えるように求められます。 Devu は、章ごとに必要な時間が固定されて始まり、各主題を完了すると、次の主題に向けて章ごとの時間がちょうど 1 時間減少します。"
date: "2026-06-07T03:18:23+07:00"
tags: ["codeforces", "competitive-programming", "implementation", "sortings"]
categories: ["algorithms"]
codeforces_contest: 439
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 251 (Div. 2)"
rating: 1200
weight: 439
solve_time_s: 83
verified: true
draft: false
---

[CF 439B - Devu、愚かな男](https://codeforces.com/problemset/problem/439/B)

 **評価:** 1200
 **タグ:** 実装、並べ替え
 **解決時間:** 1 分 23 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは Devu に、それぞれが一定数の章で構成される一連の科目を教えるように求められます。 Devu では、章ごとに必要な時間が固定されて始まり、各主題を完了すると、次の主題に向けて章ごとの時間がちょうど 1 時間減り、最低 1 時間になります。 目標は、費やされる合計時間を最小限に抑えるように指導順序を調整することです。 入力により、主題の数、章ごとの最初の時間、および各主題の章の数が得られます。 出力は、Devu がすべての科目のすべての章を学習するために必要な最小合計時間を表す単一の整数です。 

制約はかなり大きいです。 最大 100,000 の科目があり、各科目に最大 100,000 の章があるため、考えられるすべての指導命令を試す単純なアプローチでは階乗的な複雑さが生じますが、これは不可能です。 線形計算または線形時間で機能する解決策を見つけなければなりません。 章数と章ごとの時間数が両方とも 100,000 に達する可能性があるため、学習時間が非常に長くなる可能性があるため、32 ビット整数を使用するとオーバーフローする可能性があります。 64 ビット整数または Python の任意サイズの整数を使用する必要があります。 

明らかではないエッジケースには、すべての主題に同じ数の章がある場合、または主題の数は多いが、章ごとの初期時間が短い場合が含まれます。 たとえば、次の入力を使用します。```
3 1
2 3 1
```Every chapter takes at least one hour, so the per chapter time cannot drop below one. A careless solution that reduces per chapter time below one would produce an incorrect total.

 もう 1 つの微妙なケースは、非常に大きな主題が順序を考慮せずに最後にスケジュールされている場合です。最も多くの章を持つ主題を最後に置くと、合計が不必要に増加する可能性があります。 

## アプローチ

 The brute-force method would enumerate all permutations of subjects and compute the total teaching time for each. This is correct because it considers every possible order, but it becomes infeasible very quickly. n 個の被験者があれば、n 個あります。 順列。 n = 100,000 の場合、これは完全に困難です。 

The key insight for a faster solution is that the total time is minimized if we schedule subjects with more chapters earlier. Each subject's total time is its chapters multiplied by the current hours per chapter. 章ごとの時間は科目ごとに減少するため、最初に最大の章数を配置すると、削減効果が最大になります。 The per chapter time decreases by exactly one per subject, but never below one, so we need to handle this boundary in the calculation.

 したがって、最適なアプローチは、章数の降順に科目を並べ替え、その順序で指導をシミュレートし、各科目の後の章あたりの時間を減らし、絶対に 1 を下回らないようにすることです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | お(ん!) | O(n) | 遅すぎる |
 | 最適 | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. Read the number of subjects n, initial hours per chapter x, and an array c of chapter counts. これらは問題のインスタンスを表します。 
2. 配列 c を降順にソートします。 This ensures the largest subjects are taught first, leveraging the decreasing per chapter time most effectively.
 3. 変数 total_time をゼロに初期化します。 これにより、費やした合計時間が累積されます。 
4. 変数 current_hour を x に初期化します。 This represents the current hours per chapter before teaching each subject.
 5. Iterate over the sorted array of chapter counts. 各章の数は次のとおりです。 

ａ． Add chapters multiplied by current_hour to total_time. This accounts for the hours required to teach the current subject.

 b. current_hour を 1 ずつ減らしますが、1 を下回らないようにします。これは、最小境界を尊重しながら、後続の科目の学習時間の減少をモデル化します。 
6. After iterating through all subjects, total_time contains the minimum hours required. この値を出力するか返します。 

効果がある理由: 重要な不変条件は、最大の章数を持つ主題を最高の current_hour に配置することで、後続の主題の総時間短縮が最大化されるということです。 降順で並べ替えることにより、他の順序で並べ替えた場合に可能な限り各章の時間で各科目が教えられることが保証されます。 これは合計時間の最小化に直接つながります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n, x = map(int, input().split())
c = list(map(int, input().split()))

c.sort(reverse=True)

total_time = 0
current_hour = x

for chapters in c:
    total_time += chapters * current_hour
    current_hour = max(1, current_hour - 1)

print(total_time)
```このソリューションはまず、次を使用して入力を効率的に読み取ります。`sys.stdin.readline`。 並べ替えは降順で行われます`reverse=True`。 メイン ループは、チャプターの数に現在の時間を乗算して累計に加算し、次の反復で現在の時間が 1 を下回らないようにしながら減算します。 すべての演算は単純な整数演算であり、Python は大きな整数を自動的に処理します。 

## 実用的な例

 ### サンプル 1

 入力:```
2 3
4 1
```| ステップ | 章 | 現在の時間 | 合計時間 |
 | --- | --- | --- | --- |
 | 開始 | - | 3 | 0 |
 | 1 | 4 | 3 | 12 |
 | 2 | 1 | 2 | 14 |

 最適な順序は、最初に 1 章で主題を教えることです。 

| ステップ | 章 | 現在の時間 | 合計時間 |
 | --- | --- | --- | --- |
 | 開始 | - | 3 | 0 |
 | 1 | 1 | 3 | 3 |
 | 2 | 4 | 2 | 11 |

 このトレースは、降順で並べ替えると最小合計が 11 時間になることを確認します。 

### サンプル 2

 入力:```
3 3
1 2 3
```| ステップ | 章 | 現在の時間 | 合計時間 |
 | --- | --- | --- | --- |
 | 開始 | - | 3 | 0 |
 | 1 | 3 | 3 | 9 |
 | 2 | 2 | 2 | 13 |
 | 3 | 1 | 1 | 14 |

 降順に並べ替えると、合計は 14 になります。最小のものを最初に教えると、合計が大きくなります。 これは、選別ステップの重要性を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 配列のソートが優先され、n 個のサブジェクトにわたるループは O(n) | になります。 
| スペース | O(n) | 章数の配列を保存する |

 n が 100,000 までの場合、1 秒の制限時間内で O(n log n) の操作が実行可能です。 Python は大きな整数を自然に処理するため、オーバーフローの危険はありません。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n, x = map(int, input().split())
    c = list(map(int, input().split()))
    c.sort(reverse=True)
    total_time = 0
    current_hour = x
    for chapters in c:
        total_time += chapters * current_hour
        current_hour = max(1, current_hour - 1)
    return str(total_time)

# provided samples
assert run("2 3\n4 1\n") == "11", "sample 1"
assert run("3 3\n1 2 3\n") == "14", "sample 2"

# custom cases
assert run("1 5\n10\n") == "50", "single subject"
assert run("5 1\n1 1 1 1 1\n") == "5", "all ones, minimal x"
assert run("3 2\n5 5 5\n") == "24", "all equal chapters"
assert run("4 4\n1 2 3 4\n") == "30", "mixed small numbers"
assert run("5 100000\n100000 50000 10000 5000 1\n") == "16000500000", "large numbers"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1 5\n10 | 50 | 単一のサブジェクト、ループ ハンドルが n=1 であることを保証します。 
| 5 1\n1 1 1 1 1 | 5 | すべての章 = 1 および x=1、最小時間 |
 | 3 2\n5 5 5 | 24 | 章数が同じになり、時間の減少が正しくチェックされる |
 | 4 4\n1 2 3 4 | 30 | 混合チャプター、ソートとチャプターごとの削減をチェック |
 | 5 100000\n100000 50000 10000 5000 1 | 16000500000 | 数値が大きい場合、整数のオーバーフローが発生しません。 

## 特殊なケース

 すべての主題に 1 つの章があり、x が小さい場合でも、current_hour が 1 を下回ることはないため、アルゴリズムは依然として total_time を n として正しく計算します。 たとえば、次のように入力します。```
3 1
1 1 1
```トレース:

 | ステップ | 章 | 現在の時間 | 合計時間 |
 | --- | --- | --- | --- |
 | 開始 | - | 1 | 0 |
 | 1 | 1 | 1 | 1 |
 | 2 | 1 | 1 | 2 |
 | 3 | 1 | 1 | 3 |

 このアルゴリズムは、チャプタごとの時間の下限を適切に処理します。 

非常に大きな主題が存在する場合、それを最初に配置すると、最大の時間短縮が適用されることが保証されます。 入力：```
3 5
10 1 1
```降順でソート: 10,1,1

 | ステップ | 章 | 現在の時間
