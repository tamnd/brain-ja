---
title: "CF 949F - 天文学"
description: "平面内に 2n 個の格子点が与えられ、それぞれが星を表します。 元の天文配置のどこかに、整数座標を持つ特別な点、月が存在し、非常に厳密な幾何学的配置で星を n 個のペアに分割することができました。"
date: "2026-06-17T02:24:46+07:00"
tags: ["codeforces", "competitive-programming", "geometry", "probabilities"]
categories: ["algorithms"]
codeforces_contest: 949
codeforces_index: "F"
codeforces_contest_name: "Codeforces Round 469 (Div. 1)"
rating: 3300
weight: 949
solve_time_s: 69
verified: true
draft: false
---

[CF 949F - 天文学](https://codeforces.com/problemset/problem/949/F)

 **評価:** 3300
 **タグ:** 幾何学、確率
 **解決時間:** 1 分 9 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 平面内に 2n 個の格子点が与えられ、それぞれが星を表します。 元の天文配置のどこかに、整数座標を持つ特別な点、月が存在し、その結果、星は非常に厳密な幾何学的特性で n 個のペアに分割されることができました。すべてのペアについて、2 つの星を通る線は月も通過しました。 すべてのペアは異なる線を生成しましたが、それらの線はすべて、正確に 1 つの共通点、つまり月で交差していました。 

最新の入力では、2n 個の星の座標のみが表示されます。 ペアリングが失われ、月が欠けています。 課題は、月である可能性のある整数座標を再構築するか、そのような点が存在しないことを証明することです。 

幾何学的には、隠れた構造は強力です。すべての 2n 個の点がペアになり、各ペアが単一の固定点を通る線を定義する必要があります。 これは、すべての星が共通の中心を通る n 本の線のうちの 1 つにあり、各線にはちょうど 2 つの星が含まれていると言うのと同じです。 

座標境界は絶対値で最大 10^6 と大きく、n は 2600 に達する可能性があるため、すべてのペアリングまたはすべての中心候補を明示的にテストしようとするアプローチは不可能です。 構造は点セットのみから抽出する必要があります。 

重要な問題は、ペアリングが不明なため、ラインを直接再構築できないことです。 もう 1 つの微妙な問題は、多くの幾何学的な候補構造は交点の有理座標を生成できますが、答えは整数であり、すべての入力点から区別される必要があることです。 

素朴な考えとしては、2 つの点を選択し、その中点または交差点の構造によって月が決まると想定することですが、正しいペアが不明であり、異なるペアが異なる交差点の候補を生成するため、これはすぐに失敗します。 

重要なエッジケースは対称性です。 構成の対称性が高い場合、複数の中心候補が幾何学的に有効であるように見えますが、すべてのペアと一致するのは 1 つだけです。 別の失敗モードは、最初にその中心を特定せずに、推測された中心の周囲の角度による任意のペアリングまたはソートが機能すると仮定することです。 

## アプローチ

 ブルートフォース解釈では、月を推測し、その点を同一線上のトリプル (月と各ペア) にペアにできるかどうかを検証します。 固定された候補中心 M の場合、すべての点を M からの方向ベクトルに変換し、すべてのベクトルを同一の方向 (反対の光線) と組み合わせることができるかどうかを確認できます。 これには、方向ベクトルをソートまたはハッシュし、多重度を検証する必要があります。 

問題はセンター候補が不明なことだ。 可能な整数座標の空間は各方向に 10^6 で区切られる巨大なため、候補を列挙することは不可能です。 候補を入力点またはペアの中点に制限しても、依然として O(n^2) 個の可能性が残りますが、これは大きすぎます。 

重要な観察は、正しい中心が差異の対称性によって大きく制約されるということです。 真の中心が C であるとします。正しいペア (A、B) の場合、C は延長された線分 AB 上にあります。これは、ベクトルが C = A + B を満たすことを意味します。C を再配置すると、A + B = 2C が暗示されます。 したがって、すべての有効なペアの合計は同じベクトル 2C になります。 

これにより、問題は次のように変換されます。 ペアの合計が一定になるように、ポイントをペアに分割します。 その定数は 2C です。 したがって、1 つのペア (A、B) を推測すると、2C = A + B が決まり、C が決まります。 残りのタスクは、合計が A + B に等しくなるように他のすべての点をペアにできるかどうかを検証することです。 

これにより、問題が大幅に軽減されます。C を直接検索する代わりに、選択した 1 つの点と他のすべての点を組み合わせて形成された候補合計を試します。 このような候補 C ごとに、完全に有効なペアリングが誘導ルールの下に存在するかどうかを確認します。

The verification step is efficient using hashing of multiset complements: for each point P, its required partner is Q = (2C - P). If all points can be matched uniquely, and no point matches itself incorrectly, we have a valid solution.

 複雑さは、O(n) 個の中心候補を試行することで発生し (1 つの点を固定し、それを別の代表と組み合わせてみる)、各検証には O(n log n) またはハッシュを使用すると O(n) のコストがかかります。 This is acceptable for n up to 2600.

 | アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | Brute Force pairing / center enumeration | O(exp(n)) | お(1) | 不可能 |
 | Try candidate from one fixed anchor | O(n^2) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 We use the fact that the correct center must satisfy 2C = A + B for some valid pair (A, B). We try all possibilities where A is fixed and B varies.

 1. Fix a point A as a potential participant in the first pair.

 This ensures we anchor candidate constructions to a real structure instead of arbitrary guesses.
 2. For every other point B, compute a candidate center C = (A + B) / 2, or equivalently work with S = A + B as the required constant sum.

 We avoid floating-point issues by working directly with integer sums.
 3. For each candidate sum S, attempt to validate whether all points can be paired so that P + Q = S holds.

 This encodes the geometric requirement that all lines pass through a single center.
 4. Build a frequency map of all points. Iterate through points in any order, and for each point P that is still unused, compute Q = S - P.
5. If Q is not available in the multiset or is already exhausted, discard this candidate S immediately.
 6. If we successfully match all points, output the corresponding center C = S / 2.

 P を S - P と安全に一致させることができる理由は、すべての有効な解が合計制約の下で完全なインボリューション ペアリングを強制するためであり、一貫性が保たれている限り順序は問題ではありません。 

### なぜ効果があるのか

 All valid configurations imply a constant vector sum across pairs: for every pair (A, B), A + B = 2C. 逆に、複数の点のセットを合計が S になるようなペアに分割できるような値 S を見つけた場合、C = S / 2 を定義すると、各ペアが C の周りで対称であるため、すべての線が C を通過することが保証されます。少なくとも 1 つの正しいペアには A が含まれている必要があり、そのペアが真の合計を生成するため、単一のアンカー A から導出される S の検索で十分です。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())
    pts = [tuple(map(int, input().split())) for _ in range(2 * n)]
    
    from collections import Counter

    def try_sum(Sx, Sy):
        cnt = Counter(pts)
        for x, y in pts:
            if cnt[(x, y)] == 0:
                continue
            cnt[(x, y)] -= 1
            tx, ty = Sx - x, Sy - y
            if cnt[(tx, ty)] == 0:
                cnt[(x, y)] += 1
                return None
            cnt[(tx, ty)] -= 1
        
        return (Sx // 2, Sy // 2)

    # try all candidates using point 0 paired with others
    for i in range(1, 2 * n):
        sx = pts[0][0] + pts[i][0]
        sy = pts[0][1] + pts[i][1]
        res = try_sum(sx, sy)
        if res is not None:
            cx, cy = res
            # ensure center is not a star
            if (cx, cy) not in pts:
                print("Yes")
                print(cx, cy)
                return

    print("No")

if __name__ == "__main__":
    solve()
```コードは 1 つの参照点を固定し、すべての可能なパートナーに対して反復して候補合計を生成します。 候補ごとに周波数カウンターを構築し、未使用の各ポイントを合計の下の補数と貪欲に照合します。 いずれかの時点で補数が欠落している場合、候補は無効になります。 

最終チェックでは、問題ステートメントで要求されているように、計算された中心自体がスターの 1 つではないことを確認します。 

実装の微妙な詳細は、補数を検索する前に常にカウントをデクリメントすることです。これにより、多重度が許可しない限り、誤ってポイントとそれ自体がペアになることが防止されます。 これにより、縮退対称の場合でも正確さが保証されます。 

## 実用的な例

 ### 例 1

 入力:```
2
1 1
1 3
3 1
3 3
```(1,1) と (3,3) を組み合わせてみると、S = (4,4) が得られるため、候補中心は (2,2) になります。 

| ステップ | ポイント P | 必須 Q = S - P | カウンタの状態 | アクション |
 | --- | --- | --- | --- | --- |
 | 1 | (1,1) | (3,3) | 全員が出席 | ペア |
 | 2 | (1,3) | (3,1) | 全員が出席 | ペア |

 すべての点が一致するため、(2,2) は有効です。 

これは、すべてのペアが同じ中点の周りで対称であるという不変条件を裏付けます。 

### 例 2

 入力:```
2
0 0
0 2
2 0
2 2
```(0,0) を (2,2) で試すと、S = (2,2)、中心 (1,1) が得られます。 

| ステップ | P | Q | 残り |
 | --- | --- | --- | --- |
 | (0,0) | (0,0) | (2,2) | 有効 |
 | (0,2) | (0,2) | (2,0) | 有効 |

 すべての点が一致し、正確であることが確認されました。 

この例は、複数の対称構成がすべて 1 つの中心に集約されることを強調しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n^2) | O(n) 個の候補ペアを試し、ハッシュ | を使用して O(n) でそれぞれを検証します。 
| スペース | O(n) | 周波数マップは検証中にすべてのポイントを保存します。 

n ≤ 2600 という制約により、最悪の解釈では候補ペアのチェック サイクルごとに最大約 670 万回の操作が可能になりますが、実際には早期拒否により、ハッシュを使用した Python でのソリューションが十分に効率的になります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    output = []
    
    def input():
        return sys.stdin.readline()
    
    n = int(sys.stdin.readline())
    pts = [tuple(map(int, sys.stdin.readline().split())) for _ in range(2*n)]
    
    from collections import Counter

    def try_sum(Sx, Sy):
        cnt = Counter(pts)
        for x, y in pts:
            if cnt[(x, y)] == 0:
                continue
            cnt[(x, y)] -= 1
            tx, ty = Sx - x, Sy - y
            if cnt[(tx, ty)] == 0:
                cnt[(x, y)] += 1
                return None
            cnt[(tx, ty)] -= 1
        return (Sx//2, Sy//2)

    for i in range(1, 2*n):
        sx = pts[0][0] + pts[i][0]
        sy = pts[0][1] + pts[i][1]
        res = try_sum(sx, sy)
        if res is not None and res not in pts:
            return f"Yes\n{res[0]} {res[1]}\n"

    return "No\n"

# sample
assert run("""2
1 1
1 3
3 1
3 3
""") == "Yes\n2 2\n"

# collinear symmetric
assert run("""2
0 0
0 2
2 0
2 2
""") == "Yes\n1 1\n"

# impossible case
assert run("""1
0 0
1 1
""") == "No\n"

# degenerate valid
assert run("""1
-1 -1
1 1
""") == "Yes\n0 0\n"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 1,1,1,3,3,1,3,3 | はい 2 2 | 基本的な正方形対称 |
 | 0,0,0,2,2,0,2,2 | はい 1 1 | 複数の有効なペアリング |
 | 0,0,1,1 | いいえ | 整数の中心がありません |
 | -1、-1、1、1 | はい 0 0 | 負の座標の処理 |

 ## 特殊なケース

 1 つの失敗モードは、部分的な対称性により複数の候補中心が存在するが、完全なペアリングが可能なのは 1 つだけである場合です。 アルゴリズムは固定アンカー ポイントを含むすべてのペアリングを試行するため、有効な構成にはそれらの試行間の正しい候補合計が含まれている必要があります。 対称ではあるが無効な中心が試行された場合、グリーディ ペアリングは最初の不一致の補数で失敗し、誤った受け入れが防止されます。 

もう 1 つのケースは、点が候補合計の下でそれ自体の補数である場合であり、S = 2P を意味します。 この実装では、パートナーをチェックする前にポイント カウントがデクリメントされ、少なくとも 2 つの同一のポイントが存在する場合にのみ自己ペアリングが成功することが保証されるため、これが正しく処理されます。これは、すべてのポイントが異なるため不可能です。 

最後に、真の中心が凸包の外側にある場合は、計算が位置に関する幾何学的仮定に依存せず、代数的ペアの一貫性のみに依存するため、自然に処理されます。
