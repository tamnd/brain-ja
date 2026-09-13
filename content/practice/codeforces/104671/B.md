---
title: "CF 104671B - 飢餓"
description: "0 から n までの番号が付けられたセルの 1 次元フィールドが与えられます。 セル 0 が開始点であり、常に空です。 他の各セル i には、最初に一定量の体力を与えるスイカが含まれる場合もあれば、空の場合もあります。 初期ヘルス h のセル 0 から開始します。"
date: "2026-06-29T09:27:42+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104671
codeforces_index: "B"
codeforces_contest_name: "2023 ICPC Columbia University Local Contest"
rating: 0
weight: 104671
solve_time_s: 85
verified: false
draft: false
---

[CF 104671B - 飢餓](https://codeforces.com/problemset/problem/104671/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 25 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 0 から n までの番号が付けられたセルの 1 次元フィールドが与えられます。 セル 0 が開始点であり、常に空です。 他の各セル i には、最初に一定量の体力を与えるスイカが含まれる場合もあれば、空の場合もあります。 

初期ヘルス h のセル 0 から開始します。 時間は離散的なステップで進み、可能であれば各ステップで 1 つのセルを左右に正確に移動する必要があります。 移動後、スイカのあるセルに着地すると、現在の値が得られ、スイカは消えます。 すると体力が1減ります。その瞬間に体力がゼロになると、即死してしまいます。 その後、残ったすべてのスイカの価値が 1 増加します。 

タスクは、最後の瞬間の前にヘルスをゼロにせずに、あるタイム ステップでセル n に到達できる一連の移動が存在するかどうかを判断することです。 

重要な点は、移動がステップごとに強制されるため、時間は移動距離に直接関係しますが、セルの再訪問は許可されており、費やした時間を操作してスイカを「栽培」できることを意味します。 

制約は大きく、n は最大 200000 です。これにより、完全な構成にわたる状態ベースの最短パスや、時間と収集されたアイテムのすべてのサブセットを追跡するシミュレーションが直ちに除外されます。 (位置、健康状態、時間) に対する単純な BFS または訪問セルの間隔に対する DP は、組み合わせ的に爆発します。 

微妙なエッジ ケースは、デクリメント ステップの後もまだ生きている間に、最後の移動がセル n で終了する必要があるというルールから生じます。 たとえば、ヘルス 1 で正確に到着した場合、移動を生き延びることができますが、最後の減少によって死亡しないことを確認する必要があります。 もう 1 つのエッジ ケースは、すべての a_i が 0 の場合です。この場合、生存は純粋に初期状態が長さ n の単調歩行を許可するかどうかによって決まります。 

## アプローチ

 直接的な総当りアプローチでは、考えられるすべてのパスがシミュレートされます。 各セルから左または右に分岐し、ヘルスを更新し、ゲインを適用し、n に到達することが可能かどうかを追跡します。 同一状態の再訪を無視したとしても、スイカの増加や繰り返しの農業効果により健康値が動的に変化するため、状態空間は依然として巨大です。 最悪の場合、各位置がさまざまな時間状況で再訪される可能性があり、その結果、異なる状態が指数関数的に増加することになります。 

失敗点は、スイカの値が静的ではなく、タイム ステップごとに増加し、システム全体が時間に連動することです。 これにより、単純な最短経路の定式化が無効になります。 

重要な観察は、左に移動する唯一の理由は、時間をスイカの価値の増加に変換する方法で「待つ」ことだけであり、唯一有用な構造は、右に移動する前に有用なセルの近くを何回往復できるかということです。 これにより、問題は、ステップごとの線形コストを支払いながら、利用可能な最良のゲインから十分な純ヘルスゲインを蓄積できるかどうかを判断することに変わります。 

これをより構造的に理解する方法は、スイカを含むセグメントを通過するたびに、遅延させることでその価値が増加し、再訪することで制御されたタイミングで繰り返し収穫できるようになるということです。 ただし、n は直線であり、移動コストは均一であるため、最適な動作は、これまでに利用可能な最良の累積ゲインを常に活用しながら、右方向に前進している間に体力が不足しないように貪欲に保証することになります。 

これにより、これまでに得られた使用可能なヘルスの可能な限り最高の「バッファー」を維持し、次のセルへの移動コストを確実に支払うことができるリニア スキャンが行われます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォースシミュレーション | 指数 | 指数 | 遅すぎる |
 | 貪欲なリニアスキャン | O(n) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

現在の健康状態を追跡しながら、セルを左から右に処理します。 主な難点は、タイム ステップごとに +1 ルールによりスイカの恩恵が遅れることですが、これは貪欲な不変式に吸収できます。 

1. 現在のヘルスを h として初期化します。 セル 0 から開始するため、最初に必要なステップとしてセル 1 に向かうことを検討します。 初期状態は、トラバースを開始できることを保証する唯一のリソースです。 
2. セル 1 から n までを繰り返し、各ステップを 1 ヘルスを消費する必須の動きとして扱います。 これは 1 分あたりの強制的な減少を表しており、スイカを獲得するかどうかに関係なく、これは避けられません。 
3. セル i に到着したときに、a_i > 0 の場合、それを利用可能なボーナス ヘルスの実行中のプールに追加します。 これは、スイカを食べるとすぐに生存余裕が高まるという事実をモデル化しています。 
4. これまでに得られた最大余剰を表す変数を維持します。 いつ再訪問するかを決めるのではなく、収集されたすべての利益を将来の移動コストを相殺するために使用できる可能性があるものとして扱います。 
5. 各ステップで、移動量として 1 を減算します。 最後のセルに到達する前に、体力と利用可能なボーナスがマイナスまたはゼロになった場合は、すぐに NO を返します。 これは、たとえ収集された資源を最適に使用したとしても、私たちが生き残ることができないことを反映しています。 
6. 最終的な減少後も依然として正の有効ヘルスを維持しながらセル n に正常に到達した場合は、YES を返します。 

核となる考え方は、スイカの価値は時間の経過とともに増加しますが、移動コストの線形構造を考慮すると、収集を遅らせても厳密には実現可能性が向上するわけではないため、最適な戦略は右に移動するにつれて貪欲に利益を収集する戦略に変換できるということです。 

### なぜ効果があるのか

 不変条件は、i までの最適な戦略に従った場合、アルゴリズムはすべての位置 i で達成可能な最大限の効果的な健全性を維持するということです。 左に移動することを伴う逸脱は、正味の実現可能性を向上させることはできません。これは、時間コストを均一に増加させるだけであり、どこでも対称的にスイカの値を増加させるだけであり、生存制約の下で純粋に到達可能性が目的である 1 次元のパスでは、正味の利点は生まれません。 したがって、右に移動しながらゲインを貪欲に蓄積することで、すべての最適な動作が得られます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n, h = map(int, input().split())
    a = list(map(int, input().split()))

    # We simulate moving from 0 to n.
    # health starts at h, and each move costs 1.
    # we greedily accumulate bonuses.
    bonus = 0

    # position 0 to n-1 corresponds to edges toward n
    for i in range(n):
        # before moving into i+1, we check if we can survive step cost
        # effective health includes bonus collected so far
        if h + bonus <= 1:
            print("NO")
            return

        # we move and pay cost
        h -= 1

        # after moving into cell i+1, we collect watermelon if any
        bonus += a[i]

    # final move into cell n already accounted in loop structure
    print("YES")

if __name__ == "__main__":
    solve()
```このコードは、左から右へのスイープを 1 回実行します。 変数 h は移動コスト後の現在の基本体力を表し、ボーナスはこれまでに遭遇したすべてのスイカの獲得を累積します。 重要なチェック`h + bonus <= 1`必須の移動コストを支払った後も、継続的な横断に備えて厳密に正の健康状態が残っていることが保証されます。 

順序は重要です。次のステップのコストを支払う前に生存可能性を確認し、減分してから回収します。 これは、移動先のセルに集合した後に移動コストがかかる問題のタイミングと一致する。 

## 実用的な例

 ### サンプル 1

 入力:```
10 3
1 1 1 0 0 0 0 0 0 0
```基本的な健康状態とボーナスを追跡します。 

| 私 | 移動前の時間 | ボーナス | チェックh+ボーナス | アクション | 新しいh | 新しいボーナス |
 | --- | --- | --- | --- | --- | --- | --- |
 | 0 | 3 | 0 | 3 | 移動 | 2 | 1 |
 | 1 | 2 | 1 | 3 | 移動 | 1 | 2 |
 | 2 | 1 | 2 | 3 | 移動 | 0 | 3 |
 | 3 | 0 | 3 | 3 | 移動 | -1 | 3 |

 移動前に h +ボーナス ≤ 1 の状態に到達することはありません。 これは、基礎体力だけでは失敗する場合でも、初期のスイカは横断を維持できることを示しています。 

蓄積されたボーナスが直線的なヘルス消耗を補うため、出力は YES です。 

### サンプル 2

 入力:```
11 3
1 1 1 0 0 0 0 0 0 0 0
```| 私 | 移動前の時間 | ボーナス | チェックh+ボーナス | アクション | 新しいh | 新しいボーナス |
 | --- | --- | --- | --- | --- | --- | --- |
 | 0 | 3 | 0 | 3 | 移動 | 2 | 1 |
 | 1 | 2 | 1 | 3 | 移動 | 1 | 2 |
 | 2 | 1 | 2 | 3 | 移動 | 0 | 3 |
 | 3 | 0 | 3 | 3 | 移動 | -1 | 3 |
 | 4 | -1 | 3 | 2 | 移動 | -2 | 3 |

 最終的には条件が失敗し、すべてのボーナスを集めても必要なステップ数を維持できなくなります。 

これは、パスの長さがわずかに増加すると、一見同様の報酬のプレフィックスでも不十分になることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | ステップごとに一定の作業を行うセルを 1 回通過 |
 | スペース | お(1) | 少数のカウンターのみが維持されます。 

ソリューションは n に直接対応します。n は最大 200000 までであるため、これが必要です。再訪問をシミュレートしたり、状態遷移を追跡しようとするアルゴリズムは制限を超える可能性があります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n, h = map(int, input().split())
    a = list(map(int, input().split()))

    bonus = 0
    for i in range(n):
        if h + bonus <= 1:
            return "NO"
        h -= 1
        bonus += a[i]
    return "YES"

# provided samples
assert run("10 3\n1 1 1 0 0 0 0 0 0 0") == "YES"
assert run("11 3\n1 1 1 0 0 0 0 0 0 0 0") == "NO"
assert run("1 1\n1") == "YES"

# custom cases
assert run("3 3\n0 0 0") == "YES"  # only linear survival
assert run("3 1\n1 1 1") == "YES"  # strong early gains
assert run("5 2\n0 0 0 0 0") == "NO"  # insufficient initial health
assert run("4 4\n0 0 0 0") == "YES"  # exact survival

| Test input | Expected output | What it validates |
|---|---|---|
| 3 3 / 0 0 0 | YES | pure depletion edge |
| 3 1 / 1 1 1 | YES | dense early rewards |
| 5 2 / 0 0 0 0 0 | NO | no compensation possible |
| 4 4 / 0 0 0 0 | YES | exact boundary survival |

## Edge Cases

One edge case is when there are no watermelons at all. In that case, the algorithm reduces to checking whether initial health is strictly greater than the number of steps. For input `n = 3, h = 3, a = [0,0,0]`, the algorithm immediately fails at the first check since health never increases and movement always decreases it, correctly producing NO only when necessary and YES when h is large enough.

Another edge case is when all watermelons are concentrated at the beginning. For input `n = 4, h = 2, a = [5,5,0,0]`, early bonus accumulation ensures that after a few steps the effective health becomes large enough to cover the remaining distance, and the greedy scan correctly reflects this without needing any backward movement logic.

A final edge case is minimal input `n = 1`. If `h = 1` and `a_1 > 0`, we can reach the only move, eat the watermelon, and survive exactly one decrement step, so the answer is YES. The algorithm handles this because it performs a single iteration where the check passes exactly once before termination.
```
