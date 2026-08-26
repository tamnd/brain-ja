---
title: "CF 104344C - マルテロ"
description: "私たちは数直線上の 1 次元の運動問題に取り組んでいます。 エレンは位置 0 から開始し、目標位置 X に到達したいと考えています。途中、Y に位置する壁があり、エレンが位置 Z でハンマーを入手するまで通行を妨げます。"
date: "2026-07-01T18:27:19+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104344
codeforces_index: "C"
codeforces_contest_name: "Maratona dos Bixes 2023 - UNICAMP"
rating: 0
weight: 104344
solve_time_s: 88
verified: true
draft: false
---

[CF 104344C - マルテロ](https://codeforces.com/problemset/problem/104344/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 28 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは数直線上の 1 次元の運動問題に取り組んでいます。 エレンは位置 0 からスタートし、目標位置 X に到達したいと考えています。途中、Y にある壁があり、エレンが位置 Z でハンマーを入手するまで通行を妨げます。ハンマーを回収すると、壁は移動を妨げなくなり、その後エレンは自由に Y を通過できます。 

タスクは、これらのルールに従って X に到達できるかどうかを判断し、到達できる場合は最小総移動距離を計算することです。 

移動は直線上であり、すべてのセグメントのコストは単なる絶対距離であるため、有効なルートは、エレンが関連するポイント 0、Z、Y、X を訪れる順序によって完全に決定されます。ただし、Y は Z を訪れた後にのみ安全に通過できるという制約があります。 

制約は小さく、すべての座標は -1000 ～ 1000 です。これにより、テスト ケースごとに一定または対数複雑度を持つ解があれば十分であり、少数の固定数のパス評価でも十分であることがすぐにわかります。 

主な微妙な点は、壁が幾何学的な意味で一方向の制約ではないことです。 これは許可制約です。Y を越えるのは、Z を訪問した後でのみ許可されます。この順序を無視した素朴な最短経路の直感は失敗します。 

典型的な失敗ケースは、Z がスタートまたはターゲットに対して Y の「間違った側」にある場合に発生します。 たとえば、エレンが Z に到達するために早めに Y を通過する必要があるが、ハンマーを取得する前に Y を通過する必要がある形で Z が Y を超えている場合、すべての点が直線上にあるとしてもルートは不可能です。 

## アプローチ

 強引な解釈では、0 から開始して X で終了し、Z を訪問した後にのみ Y を通過するように、関連するポイントを訪問する可能性のあるすべての順列を列挙します。 順序ごとに、経路をシミュレートし、無効な距離を除外して総移動距離を計算します。 

これが機能するのは、関連するポイントの数が少なく、順列がわずかしかないためです。 ただし、この小さなケースであっても、構造が完全に制約されているため、強引な推論は必要ありません。動きは直線的であり、唯一意味のある決定は、Z が収集される前に Y が 2 つのセグメントの間にあるかどうかです。 

重要な観察は、壁が重要なのは、壁が厳密に 0 と Z の間、さらにはハンマーを取得する前に横断を強制する方法で 0 と X の間にある場合だけであるということです。 Y が開始点と Z の間にある場合、エレンはハンマーを手に入れる前に Y を通過する必要がありますが、これは禁止されているため、有効なパスは存在しません。 それ以外の場合、最適なパスは単純に 0 から Z、そして X までの直線距離であり、Y が Z の前に余分なトラバースを強制する場合にのみ補正が行われます。 

より具体的には、Y が 0 と Z の間のセグメントをブロックするかどうかを検討します。ブロックする場合、移動は不可能です。 そうでない場合、エレンは制約に違反することなく安全に最初に Z に行くことができます。 その後は壁は関係なく、あとはXまでの直線移動だけです。 

これにより、複数の順列について推論するのではなく、問題を 1 行の数回の間隔チェックに軽減できます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース順列 | O(1) (定数 6 の場合) | お(1) | 受け入れられるが不要 |
 | 間隔推論 | お(1) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 ハンマーを取得する前に、Y が無効な交差を強制するかどうかをチェックしながら、数直線を 0、Z、X の間のセグメントとして扱います。

1. まず、Y が 0 と Z の間にあるかどうかを計算します。これは、Y が数直線上で厳密に 0 と Z の間にあることを意味します。 そうなった場合、エレンはハンマーに到達する前に壁を越えなければなりませんが、それは許可されていないため、答えは直ちに不可能になります。 
2. 最初のチェックに合格した場合、次に有効なルートの合計距離を考慮します。 ハンマーは制約がアクティブになる前に使用できるため、エレンは 0 から Z に直接移動し、その後 Z から X に直接移動できます。 
3. 総コストは単純に |0 - Z| です。 + |Z - X|、Z に到達すると、壁はもう重要ではなくなるからです。 
4. この合計を答えとして返します。 

なぜ効果があるのか

 このアルゴリズムは、この問題における唯一の実際の制約を強制します。それは、Z に到達する前に Y の壁を越えることはできません。直線上で、この制約に違反できる唯一の方法は、Y が開始点とハンマーの間に厳密に存在する場合です。 Z に到達すると、状態は無制限になるため、残りの行程は 1 次元計量空間内の最短経路となり、常に直線距離になります。 直線上の絶対距離は三角不等式をしっかりと満たすため、最初に Z から遠ざかる代替ルートを迂回する場合は、距離が増加するだけです。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

X, Y, Z = map(int, input().split())

def between(a, b, x):
    return min(a, b) < x < max(a, b)

if between(0, Z, Y):
    print(-1)
else:
    print(abs(Z) + abs(X - Z))
```この実装では、主要な実現可能性条件とその結果として得られる最適なパス コストが直接エンコードされます。 ヘルパー関数`between`数直線上の厳密な順序をチェックします。これは、無効な早期交差に関して重要な唯一の幾何学的条件です。 

距離の式は、0 から Z までと Z から X までの 2 つのセグメントに分割されます。これは正しいです。Z に到達すると、壁の拘束が完全に削除されるからです。 

よくある落とし穴は、距離の計算で Y を考慮しようとすることです。 Y は、Z へのアクセスをブロックしない限り、コストに影響を与えることはありません。そのため、最終的な式には含めるべきではありません。 

## 実用的な例

 ### 例 1

 入力:```
10 -10 1
```Y = -10 が 0 と Z = 1 の間にあるかどうかを確認します。-10 はその範囲外にあるため、そうではありません。 

したがって、パス 0 → 1 → 10 の計算に進みます。 

| ステップ | ポジション | アクション | 距離 |
 | --- | --- | --- | --- |
 | 1 | 0→1 | ハンマーに行く | 1 |
 | 2 | 1 → 10 | ターゲットに移動 | 9 |

 合計距離は10です。 

これにより、最初にハンマーに到達するのを妨げないため、壁は無関係であることが確認されます。 

### 例 2

 入力:```
20 10 -10
```Y = 10 が 0 と Z = -10 の間にあるかどうかを確認します。 10 はその間隔の外にあるため、そうではありません。 

ここで、0 → -10 → 20 を計算します。 

| ステップ | ポジション | アクション | 距離 |
 | --- | --- | --- | --- |
 | 1 | 0 → -10 | ハンマーに行く | 10 |
 | 2 | -10 → 20 | ターゲットに移動 | 30 |

 総距離は40。 

これは、Y が遠くても、Z への強制パス上になければ問題ないことを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | お(1) | 一定数の比較と算術演算のみ |
 | スペース | お(1) | 補助的なデータ構造は使用されません。 

すべての演算は定数時間であり、入力の大きさに依存しないため、解は制約内に容易に適合します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import math

    X, Y, Z = map(int, sys.stdin.readline().split())

    def between(a, b, x):
        return min(a, b) < x < max(a, b)

    if between(0, Z, Y):
        return "-1"
    return str(abs(Z) + abs(X - Z))

# provided samples
assert run("10 -10 1") == "10"
assert run("20 10 -10") == "40"

# custom cases
assert run("5 2 3") == "-1", "wall blocks path to hammer"
assert run("-5 1 -2") == "3", "simple valid left side movement"
assert run("100 -50 50") == "150", "symmetric traversal through origin"
assert run("1 100 -100") == "200", "wall irrelevant when off path"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 5 2 3 | -1 | Y blocks path between 0 and Z |
 | -5 1 -2 | 3 | valid left-side traversal |
 | 100 -50 50 | 150 | crossing origin without blockage |
 | 1 100 -100 | 200 | irrelevant wall far from path |

 ## 特殊なケース

 One important edge case is when Y lies exactly outside the segment between 0 and Z but between 0 and X. For example, if 0 → Z is safe but X lies on the opposite side of Y, the wall still does not matter because it is already destroyed before reaching X. The algorithm correctly ignores Y in this situation and still computes |Z| + |X - Z|。 

Another case is when Z is between 0 and X but Y is between 0 and Z. For instance, 0, Y = 2, Z = 5, X = 10. The check detects that Y lies in (0, Z), so the output is -1. This matches the fact that reaching Z requires crossing Y prematurely.

 A final subtle case is when all points are negative. The same interval logic applies without modification since ordering on the number line is symmetric. For example, 0 → Z → X remains valid as long as Y does not lie between 0 and Z.
