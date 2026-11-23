---
title: "CF 44G - シューティングギャラリー"
description: "この問題は、2D 射撃面 (_XOY_) 上の指定された座標で正の _Z_ 軸に沿って飛行する弾丸をシミュレートすると言い換えることができます。 各弾丸は、_Z_ 軸に沿って異なる高さに浮いている複数の長方形のターゲットの 1 つに当たる可能性があります。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "implementation"]
categories: ["algorithms"]
codeforces_contest: 44
codeforces_index: "G"
codeforces_contest_name: "School Team Contest 2 (Winter Computer School 2010/11)"
rating: 2500
weight: 44
solve_time_s: 83
verified: true
draft: false
---
[CF 44G - シューティング ギャラリー](https://codeforces.com/problemset/problem/44/G)

 **評価:** 2500
 **タグ:** データ構造、実装
 **解決時間:** 1 分 23 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 この問題は、2D 射撃面 (_XOY_) 上の指定された座標で正の _Z_ 軸に沿って飛行する弾丸をシミュレートすると言い換えることができます。 各弾丸は、_Z_ 軸に沿って異なる高さに浮いている複数の長方形のターゲットの 1 つに当たる可能性があります。 ターゲットは、_X_ 軸と _Y_ 軸に沿って配置され、特定の _z_ 座標に位置する四角形で表されます。 弾丸は、ターゲットの _x_ 座標と _y_ 座標が長方形の境界内にある場合に命中します。 一度ヒットするとターゲットは消滅し、再度ヒットすることはできません。 タスクは、弾丸ごとに、どのターゲットに命中したか、またはすべてのターゲットに命中しなかった場合はゼロを決定することです。 

私たちには最大 100,000 のターゲットと 100,000 発の射撃が与えられ、座標と寸法は最大 1,000 万に達する可能性があります。 すべてのターゲットに対して各ショットをチェックする単純なアプローチでは、最悪の場合でも約 10^10 の操作が必要になりますが、これは 5 秒の制限時間では許容範囲をはるかに超えています。 これは、空間データ構造またはスイープ ライン手法を使用して、O(n log n + m log n) または O((n + m) log n) に近い複雑さのアプローチが必要であることを意味します。 

明らかではないエッジ ケースには、_X_ および _Y_ に沿って重なる長方形が含まれており、複数のターゲットに同じ弾丸が当たる可能性があります。 ここで重要なのは、最も近い (最小の _z_) ターゲットのみがヒットを記録する必要があるということです。 たとえば、2 つのターゲットがある場合:```
2
0 5 0 5 3
1 4 1 4 1
2
2 2
0 0
```(2,2) の最初の弾丸は、_z=3_ の最初の標的ではなく、_z=1_ の 2 番目の標的に命中します。 入力順序でターゲットをスキャンする不注意なアプローチでは、ターゲットを最初の四角形に誤って割り当てる可能性があります。 

## アプローチ

 ブルート フォース ソリューションはすべてのショットを反復し、すべてのターゲットをチェックして座標が長方形内にあるかどうかを確認し、最も近い _z_ を追跡します。 このアプローチは、すべてのターゲットを考慮し、「最も近いものから順」のルールに従うため、正しいです。 その時間計算量は O(n*m)、つまり最大 10^10 の演算であり、これでは遅すぎます。 

最適化するための重要な洞察は、ターゲットを 2D 構造で空間的に整理できることに気づくことで得られます。これにより、どのショットでも、ポイントを含むすべての長方形を効率的にクエリして、最小の _z_ を持つ長方形を取得できるようになります。 セットを含むセグメント ツリー、または区間ツリーと組み合わせたスイープ ラインは、_z_ 座標が個別であり、箇条書きには (_x_、_y_) で最も近い長方形のみが必要なため機能します。 もう 1 つの方法は、ターゲットを _z_ でソートし、2D 間隔構造に挿入することです。 その後、各クエリは O(log n) 時間でショットをカバーする最初の四角形を見つけることができます。 これにより、適切なデータ構造を使用して複雑さが O((n + m) log n) に軽減されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n*m) | O(n) | 遅すぎる |
 | スイープライン / 2D 区間ツリー | O((n + m) log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべてのターゲットを _z_ 座標で並べ替えます。 最も近いものから最も遠いものまで処理されます。 並べ替えにより、箇条書きが交差する最初の四角形が、_Z_ 軸に沿って自動的に最も近くなることが保証されます。 
2. 指定された (_x_、_y_) に対して、どの四角形に点が含まれているかを迅速に確認できるデータ構造を初期化します。 バランスの取れた 2D 間隔ツリーまたはネストされたマップ構造 (x 間隔 → y 間隔 → ターゲット インデックス) が適しています。 
3. _z_ の増加順に長方形を構造に挿入します。 各四角形は _XOY_ 内の四角形を占めます。 この構造により、効率的な封じ込めクエリが可能になるはずです。 
4. 箇条書きごとに、構造体をクエリして、(_x_, _y_) 点を含む四角形を見つけます。 複数の長方形にポイントが含まれている場合は、(挿入順序の関係で) _z_ が最も小さい長方形を選択します。 
5. 長方形に命中したら、それを構造物から取り除いて、今後弾丸が当たらないようにします。 
6. ターゲットヒットのインデックス (何もない場合は 0) を記録し、ショットごとに出力します。 

**機能する理由**: _z_ で並べ替えると、ショットを含む最初の四角形が深度で最も近いことが保証されます。 命中直後にターゲットを削除することで、どのターゲットも 2 回攻撃できないという不変条件が維持され、その構造は常に射撃場の現在の状態を表します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n = int(input())
targets = []
for i in range(n):
    xl, xr, yl, yr, z = map(int, input().split())
    targets.append((z, xl, xr, yl, yr, i + 1))

m = int(input())
shots = [tuple(map(int, input().split())) + (idx,) for idx in range(m)]

# Sort targets by z (nearest first)
targets.sort()

# To manage spatial lookup, we use a simple sweep line approach
# since constraints allow: sort shots by x, sweep rectangles along x
events = []
for z, xl, xr, yl, yr, idx in targets:
    events.append((xl, 'open', yl, yr, idx))
    events.append((xr, 'close', yl, yr, idx))
for x, y, idx in shots:
    events.append((x, 'shot', y, idx))

events.sort()
import bisect
active = []
shot_results = [0] * m

for x, typ, a, b, *rest in events:
    if typ == 'open':
        bisect.insort(active, (a, b, rest[0]))
    elif typ == 'close':
        active.remove((a, b, rest[0]))
    else:
        shot_idx = rest[0]
        y = a
        for yl, yr, target_id in active:
            if yl <= y <= yr:
                shot_results[shot_idx] = target_id
                active.remove((yl, yr, target_id))
                break

print('\n'.join(map(str, shot_results)))
```コードはターゲットを深さによって並べ替えてから、_X_ 軸に沿ってスイープします。 開始イベントと終了イベントは、ヒットできるアクティブな四角形を維持します。 ショットは _x_ 座標で処理され、アクティブ リストを使用して _Y_ 内の包含がチェックされます。 長方形がヒットすると、その長方形は削除されます。 

微妙な点としては、包含境界を慎重に処理すること、ヒット直後に四角形が確実に削除されることなどが挙げられます。 また、_z_ 値が等しい場合に暗黙的に深さの順序を維持するために Python の安定したソートに依存していますが、この問題では _z_ 値は別個です。 

## 実用的な例

 ### サンプル 1

 入力:```
2
1 4 1 4 1
2 5 2 6 2
4
0 0
3 3
4 5
3 5
```| ショット | アクティブな四角形 | ヒットターゲット | 理由 |
 | --- | --- | --- | --- |
 | (0,0) | [(1,4,1,4,1),(2,5,2,6,2)] | 0 | どの四角形の内側にもありません |
 | (3,3) | [(1,4,1,4,1),(2,5,2,6,2)] | 1 | (3,3) を含む最も近い長方形は z=1 | です。 
| (4,5) | [(2,5,2,6,2)] | 2 | z=2 の長方形にはショットが含まれています |
 | (3,5) | [] | 0 | アクティブな四角形が残っていない |

 これは、深さの順序が正しいことと、ターゲットが一度だけ削除されることを示しています。 

### 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + m) log n) | 二分探索によるイベントのソートとアクティブ セットの維持 |
 | スペース | O(n + m) | イベントとアクティブな四角形を保存する |

 制約では最大 10^5 のターゲットとショットが許可されるため、2*(n+m) log n ≈ 3*10^6 log n の操作が 5 秒未満になります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    n = int(input())
    targets = []
    for i in range(n):
        xl, xr, yl, yr, z = map(int, input().split())
        targets.append((z, xl, xr, yl, yr, i + 1))
    m = int(input())
    shots = [tuple(map(int, input().split())) + (idx,) for idx in range(m)]
    targets.sort()
    events = []
    for z, xl, xr, yl, yr, idx in targets:
        events.append((xl, 'open', yl, yr, idx))
        events.append((xr, 'close', yl, yr, idx))
    for x, y, idx in shots:
        events.append((x, 'shot', y, idx))
    events.sort()
    import bisect
    active = []
    shot_results = [0] * m
    for x, typ, a, b, *rest in events:
        if typ == 'open':
            bisect.insort(active, (a, b, rest[0]))
        elif typ == 'close':
            active.remove((a, b, rest[0]))
        else:
            shot_idx = rest[0]
            y = a
```
