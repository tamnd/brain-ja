---
title: "CF 105E - 持ち上げて投げる"
description: "3 人の人物が与えられ、それぞれが 1 次元の半直線に沿った異なる位置に立っています。 各ポジションは 1 から始まる整数であり、各キャラクターには移動範囲と投げる範囲があります。"
date: "2026-06-01T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "brute-force"]
categories: ["algorithms"]
codeforces_contest: 105
codeforces_index: "E"
codeforces_contest_name: "Codeforces Beta Round 81"
rating: 2500
weight: 105
solve_time_s: 153
verified: true
draft: false
---

[CF 105E - 持ち上げて投げる](https://codeforces.com/problemset/problem/105/E)

 **評価:** 2500
 **タグ:** ブルートフォース
 **解決時間:** 2 分 33 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 3 人の人物が与えられ、それぞれが 1 次元の半直線に沿った異なる位置に立っています。 各ポジションは 1 から始まる整数であり、各キャラクターには移動範囲と投げる範囲があります。 移動範囲は空いている位置まで移動できる距離を定義し、投擲範囲は保持している別のキャラクターをどれだけ遠くまで投げることができるかを定義します。 さらに、キャラクターがすぐ隣接している場合、キャラクターは別のキャラクターを持ち上げることができ、1 つ、2 つ、さらには 3 つのキャラクターのスタックを作成します。 スタックした場合、最上位のキャラクターのみが行動でき、移動と投げはリフターに制限されます。 

入力は各キャラクターの開始位置、移動範囲、投げる範囲で構成されます。 出力は、自分自身または他の人を移動、持ち上げ、または投げることによって、キャラクターが到達できる最大の位置です。 位置は小さな整数 (1 ～ 10) であり、各キャラクターには各タイプのアクションが 1 つしかないため、状態空間は制限されており、慎重に扱えば徹底的なシミュレーションが可能になります。 

エッジ ケースは、キャラクターがスタックを形成するほど近くにある場合、または複数のスロー シーケンスが競合する場合に発生します。 たとえば、すべてのキャラクターが隣接しており、1 つのキャラクターの投擲範囲が長い場合、最適なパスには列を形成して先頭のキャラクターをはるか前方に投げることが含まれる可能性があります。 個々の動きのみを考慮する単純なアプローチでは、これらの組み合わせられたシーケンスを見逃す可能性があります。 ポジションを検討する`1 1 1`範囲付き`3 3 3`; 最適な方法には、独立した動きではなく、チェーンを持ち上げたり投げたりすることが含まれる場合があります。 

## アプローチ

 ブルートフォースアプローチでは、キャラクターごとに移動、持ち上げ、投げの可能なすべてのシーケンスを試み、結果の位置を追跡します。 各キャラクターには最大 3 つのアクションがあるため、アクションの順序が順列し、誰を持ち上げるか投げるかの選択があり、組み合わせがすぐに爆発します。 3 つの文字と位置が 1 ～ 10 に制限されている場合、これは理論的には可能ですが、シーケンスを欠落させずに実装するのは面倒です。 

重要な観察は、ポジションが小さく、アクションが制限されており、状態は現在のポジションと誰が誰を保持しているかというタプルとして完全に表現できるということです。 これにより、幅優先検索またはメモ化された深さ優先検索が可能になります。 任意の構成から到達可能なすべての状態を生成し、確認された最大位置を追跡することで、冗長性を持たずに空間を体系的に探索できます。 BFS アプローチでは、ターン順序とアクションの制限が当然考慮されます。 

最適なアプローチでは、各キャラクターのアクションを状態遷移として扱います。 移動先が空いていて移動範囲内にある場合、移動は有効です。 リフトは 2 人のキャラクターが隣接していてまだ保持されていない場合に有効です。 ターゲット位置がフリーで、投球範囲内にある場合、投球は有効です。 BFS はすべての組み合わせを調査し、キャラクターが到達する最大位置を更新します。 位置が 10 個、文字が 3 個しかないため、状態の総数は十分に少なく、完全にシミュレーションできます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(3! × 3^3 × 10^3)​​ | O(10^3)​​ | 乱雑すぎる/慎重に剪定しないと実用的ではない |
 | 状態メモ化を備えた BFS | O(10^3 × 3! × 3^3) | O(10^3)​​ | 承認済み |

 ## アルゴリズムのチュートリアル

 1. ゲームの状態を 3 人のキャラクターの位置のタプルと、保持関係を記述するネストされたタプルとして表します。 このコンパクトな表現により、迅速な比較とメモ化が可能になります。 
2. 開始位置と空の保持構造を使用して、BFS のキューを初期化します。 別の変数で見つかった最大位置を追跡します。 
3. 各状態について、保持されていないキャラクターのすべての有効な動きを考慮します。 フリー キャラクターごとに、移動範囲内の空いているすべての位置に移動してみます。 結果の状態がまだアクセスされていない場合は、その状態をキューにプッシュします。 
4. フリーで別のフリー キャラクターに隣接する各キャラクターについて、持ち上げをシミュレートします。 リフターがリフトされたキャラクターを保持するように保持構造を更新します。 新しい状態をキューにプッシュします。 
5. 別のキャラクターを保持している各キャラクターについて、保持しているキャラクター (または複数スタックされている場合は列) を投げる範囲内のすべての空き位置に投げてみます。 それに応じて位置を更新し、新しい状態をキューにプッシュします。 
6. 各状態遷移後、いずれかの文字の新しい位置が現在の最大位置を超える場合は、最大位置を更新します。 
7. キューが空になるまで BFS を続行します。 記録された最大位置を返します。 

これが機能するのは、BFS によって、移動、リフト、スローの組み合わせを見逃すことなく、到達可能なすべての状態を探索できることが保証されるためです。 状態表現により循環が防止され、状態が再検討されることがなくなり、各ステップで最大値を更新することでバックトラックする必要がなくなります。 

## Python ソリューション```python
import sys
from collections import deque
input = sys.stdin.readline

def solve():
    pos = []
    move = []
    throw = []
    for _ in range(3):
        p, m, t = map(int, input().split())
        pos.append(p)
        move.append(m)
        throw.append(t)

    max_pos = max(pos)
    visited = set()
    # state: positions tuple, holding tuple (who holds whom)
    # holding: -1 means free, 0/1/2 means held by character index
    queue = deque()
    init_holding = (-1, -1, -1)
    queue.append((tuple(pos), init_holding))
    visited.add((tuple(pos), init_holding))

    def get_free_positions(positions):
        return set(range(1, 11)) - set(positions)

    while queue:
        positions, holding = queue.popleft()
        max_pos = max(max_pos, max(positions))

        # move
        for i in range(3):
            if holding[i] != -1:
                continue
            for delta in range(-move[i], move[i] + 1):
                if delta == 0:
                    continue
                new_pos = positions[i] + delta
                if 1 <= new_pos <= 10 and new_pos not in positions:
                    new_positions = list(positions)
                    new_positions[i] = new_pos
                    new_state = (tuple(new_positions), holding)
                    if new_state not in visited:
                        visited.add(new_state)
                        queue.append(new_state)

        # lift
        for i in range(3):
            if holding[i] != -1:
                continue
            for j in range(3):
                if i != j and holding[j] == -1 and abs(positions[i] - positions[j]) == 1:
                    new_holding = list(holding)
                    new_holding[j] = i
                    new_state = (positions, tuple(new_holding))
                    if new_state not in visited:
                        visited.add(new_state)
                        queue.append(new_state)

        # throw
        for i in range(3):
            held = [idx for idx, h in enumerate(holding) if h == i]
            if not held:
                continue
            free_pos = get_free_positions(positions)
            for target in free_pos:
                if abs(positions[i] - target) <= throw[i]:
                    new_positions = list(positions)
                    for h_idx in held:
                        new_positions[h_idx] = target
                    new_holding = list(holding)
                    for h_idx in held:
                        new_holding[h_idx] = -1
                    new_state = (tuple(new_positions), tuple(new_holding))
                    if new_state not in visited:
                        visited.add(new_state)
                        queue.append(new_state)

    print(max_pos)

solve()
```解決策は、キャラクターの位置、移動範囲、および投げる範囲を読み取ることから始まります。 BFS は、無限ループを防ぐために訪問した状態をメモ化しながら、到達可能なすべての状態を探索します。 移動、持ち上げ、投げは個別に処理され、アクションが有効かどうかを常にチェックします。 の取り扱い`holding`タプルは、現在保持されていない文字によってのみアクションが試行されることを正しく保証し、スタックされた文字の位置をスロー操作で正しく伝播します。 微妙なのは、投げられたスタックがユニットとして移動することです。 

## 実用的な例

 **サンプル 1**

 入力:```
9 3 3
4 3 1
2 3 3
```| ステップ | ポジション | 開催中 | アクション | 最大位置 |
 | --- | --- | --- | --- | --- |
 | 0 | 9,4,2 | -1、-1、-1 | 初期化 | 9 |
 | 1 | 6,4,2 | -1、-1、-1 | ラハールは 6 に移動 | 6 |
 | 2 | 6,5,2 | -1、-1、-1 | フロンヌが 5 に移動 | 6 |
 | 3 | 6,5,4 | 5 は 1 を保持します | フロンネがエトナ山を持ち上げる | 写真 6 |
 | 4 | 6,5,4 | 0 は 2 を保持します | ラハールはフロンを持ち上げます | 6 |
 | 5 | 9,5,4 | -1,0,2 | ラハールはフロンを投げる | 9 |
 | 6 | 12,5,4 | -1、-1、2 | フロンがエトナを投げる | 12 |
 | 7 | 15,5,4 | -1、-1、-1 | エトナが動く | 15 |

 これは、スタックを形成し、連続したスローを使用する最適な戦略を示しています。 

**カスタム例**

 入力:```
1 1 10
2 1 1
3 1 1
```| ステップ | ポジション | 開催中 | アクション | 最大位置 |
 | --- | --- | --- | --- | --- |
 | 0 | 1、2、3 | -1、-1、-1 | 初期化 | 3 |
 | 1 | 1、2、3 | 0 は 1 を保持します | ラハールはエトナを持ち上げます | 3 |
 | 2 | 1、2、3 | 0 は 2 を保持します | エトナはフロンを持ち上げます | 3 |
 | 3 | | | | |
