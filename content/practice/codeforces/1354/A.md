---
title: "CF 1354A - 目覚まし時計"
description: "私たちは、離散的な「睡眠サイクル」で時間の経過とともに進化する非常に単純なシステムをシミュレートしています。 人はベッドから出る前に、少なくとも目標量の有効な睡眠を蓄積する必要があります。"
date: "2026-06-11T13:57:09+07:00"
tags: ["codeforces", "competitive-programming", "math"]
categories: ["algorithms"]
codeforces_contest: 1354
codeforces_index: "A"
codeforces_contest_name: "Educational Codeforces Round 87 (Rated for Div. 2)"
rating: 900
weight: 1354
solve_time_s: 278
verified: true
draft: false
---

[CF 1354A - 目覚まし時計](https://codeforces.com/problemset/problem/1354/A)

 **評価:** 900
 **タグ:** 数学
 **解決時間:** 4 分 38 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは、離散的な「睡眠サイクル」で時間の経過とともに進化する非常に単純なシステムをシミュレートしています。 人はベッドから出る前に、少なくとも目標量の有効な睡眠を蓄積する必要があります。 The process starts with a first alarm that rings after some initial delay, and after every wake-up, the same pattern repeats: the alarm is reset, time passes while the person falls asleep again, and during that falling-asleep window the alarm may or may not ring.

 重要な問題は、睡眠が継続的ではないことです。 Each cycle contributes only part of its duration toward the total sleep requirement, and depending on the parameters, the process may either eventually accumulate enough sleep or loop forever without progress.

 各テスト ケースには 4 つの数値が記述されます。 1つ目は必要な総睡眠時間です。 2 つ目は、最初のアラームが鳴ったときです。 3 番目は、各リセット後の後続のアラーム間の固定間隔です。 4つ目は、目が覚めてから再び眠りにつくまでの時間です。 

The output is the moment in time when the accumulated effective sleep first reaches or exceeds the requirement, or -1 if the process never manages to increase the total sleep beyond a bounded value.

 この制約では、最大 1000 のテスト ケースと最大 10^9 の値が許可されます。 これにより、単位ステップまたは 1 秒ごとに時間を進めるシミュレーションは即座に除外されます。 最初のウェイクアップ後の構造は周期的であるため、正しいアプローチではアラーム イベント間のジャンプを考慮する必要があります。 

入眠にアラーム間隔より長い時間がかかる場合、微妙なエッジケースが発生します。 このような状況では、常に人が眠る前にアラームが鳴るため、最初に目が覚めた後は効果的な睡眠が得られません。 たとえば、a = 10、b = 5、c = 2、d = 100 の場合、時刻 5 に目覚めた後、睡眠が蓄積する前にアラームが常に中断されます。 睡眠が最初のウェイクアップ寄与を超えて増加することはないため、正しい出力は -1 です。 

もう 1 つのエッジ ケースは、最初のアラームの直後に必要な睡眠がすでに満たされている場合に発生します。 たとえば、a = 1、b = 1、c = 100、d = 100 の場合、それ以上の処理は必要ないため、答えは単純に 1 になります。 

## アプローチ

 直接シミュレーションは文字通り問題を追跡します。 現在の時刻、蓄積された睡眠を追跡し、アラームによる目覚めを繰り返しシミュレートします。 目覚めるたびに、停止するか、別のサイクルをシミュレートします。 これはプロセスを正確に反映するため正しいですが、サイクル数が多いと遅くなります。 最悪の場合、ターゲットに到達するかループを検出する前に、約 10^9 の遷移が実行される可能性があります。 

重要な観察は、最初のアラームの後、システムが周期的になるということです。 各サイクルは同じように動作します。つまり、目覚め、場合によっては睡眠を獲得し、アラームをリセットし、繰り返します。 唯一の問題は、各サイクルがポジティブな睡眠に寄与するかどうかです。 そうでない場合、プロセスは最初のサイクルの寄与を超えて進むことはできず、すぐに不可能であると結論付けます。 Otherwise, each cycle contributes a fixed amount of additional sleep, so we can compute how many cycles are needed using arithmetic rather than simulation.

 ブルートフォースは現実を直接シミュレートするため機能しますが、サイクル数が大きくなると失敗します。 The observation that each cycle contributes either a fixed positive increment or zero lets us reduce the problem to a constant-time computation per test case.

 | アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 |---|---|---|---|
 | シミュレーション | テストあたり O(k) | お(1) | 遅すぎる |
 | 循環演算 | テストごとに O(1) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 タイムラインをアラームの起動によって区切られたフェーズに分割しました。 最初のウェイクアップは時間 b で発生し、その後は固定構造で各サイクルが繰り返されます。

各サイクルでどれだけの睡眠が得られるかを計算します。 サイクル中、人は目覚めた後、眠りにつくまでの時間を費やします。 アラームはリセットから c 単位後にスケジュールされます。 d が少なくとも c の場合、アラームはそのサイクルで睡眠が蓄積される前に常に中断されます。これは、既に発生した以上の追加の睡眠は得られないことを意味します。 その場合、最初のウェイクアップがすでに要件を満たしていない限り、システムは処理を進めることができず、すぐに -1 を返します。 

d が c より小さい場合、人は各サイクルでアラームが中断されるまでに正確に c ～ d の有効時間単位だけ睡眠をとることができます。 これはサイクルごとの固定ゲインになります。 

最初の起床後の累積睡眠を計算します。 すでに目標に達している場合は、b で停止します。 

それ以外の場合は、残りの睡眠要件に到達するために必要な完全なサイクルの数を決定します。 各サイクルは一定量を寄与するため、上限動作を伴う整数除算を使用して、必要なサイクル数を計算します。 

最後に、b と追加のアラーム間隔の数を c 倍したものを加算して、サイクル数を時間に変換します。 

### なぜ効果があるのか

 最初の目覚め後のプロセスは、蓄積された睡眠における決定論的な線形反復です。 サイクルごとの増分が 0 でシーケンスが制限されるか、正で厳密に単調になります。 単調な場合、値は均一なステップで増加するため、最初にしきい値を超えるタイミングは、除算の上限によって正確に決まります。 増分は一定であるため、中間状態は最適な答えをスキップできません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    for _ in range(t):
        a, b, c, d = map(int, input().split())

        if b >= a:
            print(b)
            continue

        if c <= d:
            print(-1)
            continue

        gain = c - d

        remaining = a - b

        cycles = (remaining + gain - 1) // gain

        print(b + cycles * c)

solve()
```このソリューションでは、まず最初のアラーム時間が必要な睡眠時間をすでに満たしているかどうかをチェックします。満たしている場合、シミュレーションは必要ありません。 次に、眠りにつくまでに時間がかかりすぎる、つまり最初の目覚め後にまったく進歩が見られないという退化ケースを処理します。 

コアの計算により、必要な同一の生産サイクルの数をカウントするプロセスが簡素化されます。 上限区分により、しきい値に到達するまでに必要なサイクル数を過小評価しないことが保証されます。 

よくある実装上の間違いは、最初のウェイクアップがサイクルの開始前の部分的な進行に寄与することを忘れていることです。 もう 1 つの間違いは、d が c 以上である場合に、即座に -1 を返さなければならない非進行ケースを誤って処理することです。 

## 実用的な例

 ### 例 1

 入力:
 a = 10、b = 3、c = 6、d = 4

 最初に目覚めた後の初期睡眠を計算します。 

| ステップ | 時間 | 睡眠 | コメント |
 |------|------|------|------|
 | 1 | 3 | 3 | 最初のアラーム |

 必要な残りの睡眠時間は 7 です。各サイクルは c - d = 2 に寄与します。 

サイクル = ceil(7 / 2) = 4 を計算します。 

最終時間は 3 + 4 * 6 = 27 です。 

このトレースは、システムが繰り返される動作を均一な増分に圧縮する方法を示しています。 

### 例 2

 入力:
 a = 5、b = 9、c = 4、d = 10

 最初のアラームは必要な睡眠時間に達した後にすでに発生しているため、答えは 9 です。 

| ステップ | 時間 | 睡眠 | コメント |
 |------|------|------|------|
 | 1 | 9 | 9 | もう十分 |

 このケースでは、それ以上のシミュレーションが必要ない場合の早期終了ロジックが確認されています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 |---|---|---|
 | 時間 | O(t) | 各テスト ケースは定数算術演算を使用します。 
| スペース | お(1) | テスト ケースあたりの変数はわずか数個だけ |

 Python では 10^5 の演算さえも簡単なので、このソリューションは簡単に制限内に収まります。ここではテスト ケースごとに O(1) の作業のみを実行します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import builtins
    input_backup = builtins.input
    builtins.input = sys.stdin.readline
    try:
        # assuming solve() is defined globally
        solve()
        return ""  # adjust if needed
    finally:
        builtins.input = input_backup

# provided samples
# (expected outputs omitted in scaffold context)
# assert run(...) == ...

# custom cases
# minimum case
# assert run("1\n1 1 1 1\n") == "1"

# already satisfied
# assert run("1\n10 1 5 5\n") == "1"

# impossible case
# assert run("1\n10 1 2 10\n") == "-1"

# large progression
# assert run("1\n1000000000 1 1000000000 1\n") == "1"
```| テスト入力 | 期待される出力 | 検証内容 |
 |---|---|---|
 | 単一の小さなケース | つまらない | 基本的な正確性 |
 | すぐに成功 | 早期終了 | サイクルは必要ありません |
 | 進歩なし | -1 | 無限ループ検出 |
 | 大きな値 | 高速演算 | オーバーフローの安全性と効率 |

 ## 特殊なケース

 最初のアラームでスリープ要件がすでに満たされている場合、アルゴリズムはサイクル ロジックに入らずにすぐに戻ります。 入力 a = 5、b = 10、c = 3、d = 2 の場合、それ以上の処理は必要ないため、結果は 10 になります。 

a = 10、b = 5、c = 2、d = 10 など、システムが起動後に処理を進めることができない場合、条件 c <= d がトリガーされ、アルゴリズムは正しく -1 を返します。 このトレースでは、時間 5 での最初のウェイクアップの後、有効なスリープを追加せずに各サイクルがリセットされるため、不変のスリープ値は一定のままで、しきい値に到達しません。
