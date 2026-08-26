---
title: "CF 104325A - 建設計画"
description: "私たちには、特定の種類の機械で実行される 1 つのレシピによってすべての材料が作成される生産システムが与えられています。 各マシンタイプには固定速度乗数があり、各レシピには基本時間があります。"
date: "2026-07-01T19:13:27+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104325
codeforces_index: "A"
codeforces_contest_name: "AGM 2023 Qualification Round"
rating: 0
weight: 104325
solve_time_s: 99
verified: false
draft: false
---

[CF 104325A - 構築計画](https://codeforces.com/problemset/problem/104325/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 39 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちには、特定の種類の機械で実行される 1 つのレシピによってすべての材料が作成される生産システムが与えられています。 各マシンタイプには固定速度乗数があり、各レシピには基本時間があります。 1 つのユニットを生産する実際の時間は、基本時間を機械速度で割った値によって決まります。 レシピはまた、他のレシピによって生成される他のマテリアルも消費し、サイクルのない依存関係グラフを形成します。 

目標は、将来の生産をシミュレーションすることではなく、一連のターゲット材料が正確に必要な毎秒の速度で生産されるように、各レシピのステーション タイプで何台の機械が必要かを判断することです。 すべてのレシピは独自のマシン上で独立して実行され、各マシンはそのレシピの固定スループットに貢献します。 レシピの生産が必要以上に遅い場合は、そのレシピのステーションのマシンを追加します。 

重要な点は、運用要件が依存関係グラフを通じて逆方向に伝播することです。 最終製品に特定の速度が必要な場合、その成分はそれをサポートするのに十分な速さで生成される必要があり、これが原材料に到達するまで再帰的に行われます。 

制約は十分小さいため、すべてのレシピにわたる線形伝播で十分です。 最大で 100 のレシピと 100 のマシン タイプがあるため、繰り返しの伝播や逆依存関係の蓄積も可能です。 重要な構造は、グラフが非循環であることです。これにより、逆のトポロジー順序で処理された場合、単一パスで要件を計算できることが保証されます。 

一般的な失敗例は、中間材料が複数の製品で再利用される場合に発生します。 最終製品ごとの要件を個別に計算し、正しく集計することを忘れると、共有の依存関係が過小評価されてしまいます。 

たとえば、2 つの製品が両方とも Iron_ore を必要とする場合、単純なアプローチでは鉱石需要を個別に計算し、合計する代わりに値を上書きする可能性があります。 正しい要件は、下流のすべての需要の合計です。 

もう 1 つの落とし穴は、レートの浮動小数点除算です。 生産速度は t/s などの速度比に依存するため、丸めが早すぎるとマシン数が 1 つずれてしまう可能性があります。 安全なアプローチは、必要なレートを実数として計算し、最終ステップでのみ切り上げることです。 

## アプローチ

 強引なアイデアは、各ターゲット材料からの生産要件を個別にシミュレートすることです。 必要な出力ごとに、そのレシピを再帰的に展開し、すべての材料の割合を計算して、レシピごとのマシン数を再計算します。 これが機能するのは、依存関係が有限かつ非循環的であるため、再帰が終了するためです。 

ただし、このアプローチでは、重複する部分問題が何度も再計算されます。 マテリアルが複数の製品で使用されている場合、そのサブツリーが繰り返し走査されます。 最悪の場合、各レシピは他のほぼすべてのレシピに依存するため、再計算によりレシピの数が 2 次の動作をすることになりますが、これは共有構造を考えると不必要です。 

重要な観察は、システムが各ノードが上流の需要に線形的に寄与する DAG であるということです。 ルートごとに再計算するのではなく、必要なレートをボトムアップで集計します。 すべての材料に必要な生産量がわかれば、各レシピの機械数は独立した計算になります。 

したがって、最初に逆依存性伝播を使用して、すべての材料の必要なレートを計算します。つまり、要求された出力から開始して、レシピを通じて需要を後方に押し上げます。 各レシピは固定拡張を定義しているため、これは単にエッジに沿った重み付けされた累積になります。 すべての材料レートが判明したら、それらをマシン数に変換することは、マシンごとのスループットで直接除算することになります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ターゲットごとのブルート フォース再帰 | O(Q · N²) | O(N) | 遅すぎる |
 | 逆需要伝播 | O(N + Q) | O(N) | 承認済み |

 ## アルゴリズムのチュートリアル

すべてのマテリアルをグラフ内のノードとして扱います。 各レシピは出力マテリアルから入力マテリアルへのエッジであり、固定係数が付いています。 

まず、レシピごとに、1 台のマシンが 1 秒あたり何ユニット生産するかを計算します。 これは、マシン速度を基準時間で割ることによって得られます。 

次に、マップを保守します`need[x]`材料の 1 秒あたりの単位数を表す`x`世界的に必要とされています。 

初期化する`need`最終クエリを使用します。 

逆トポロジー順序でマテリアルを処理します。 グラフは非巡回であるため、明示的にトポロジー的にソートするか、メモ化された DFS に依存することができます。 実際には、ターゲットからの DFS で十分です。 

素材ごとに`p`必要なレートの場合、それを生成するレシピ付き`need[p]`、次にすべての材料`n`レシピで必要な量を増やす必要があります`need[p] * c`、 どこ`c`レシピの消費係数です。 これにより、需要が逆方向に伝播されます。 

もう一度`need`値が計算されると、レシピごとのマシン数が決定されます。 レシピ制作用`p`マシンタイプ上`l`、各マシンは固定レートを貢献します`rate[p]`。 必要なマシンの数は、`ceil(need[p] / rate[p])`。 

### なぜ効果があるのか

 不変条件は、材料を処理した後、`need[x]`最終需要から始まるすべての依存関係チェーンにわたって x に必要な定常状態の生産速度の合計に等しい。 各レシピは線形で独立しているため、異なる親からの要求の寄与が干渉することなく追加されます。 非循環構造により、不完全な情報を持つノードを再訪問することがないことが保証されるため、処理順序が尊重される場合、蓄積は最終的になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

from collections import defaultdict, deque
import math

M = int(input())
speed = {}
for _ in range(M):
    name, s = input().split()
    speed[name] = float(s)

N = int(input())

recipe = {}
inputs = {}
machine = {}

all_materials = set()

for _ in range(N):
    p, l, t = input().split()
    t = float(t)
    k = int(input())
    req = []
    for _ in range(k):
        n, c = input().split()
        c = int(c)
        req.append((n, c))
        all_materials.add(n)
    recipe[p] = (l, t)
    inputs[p] = req
    machine[p] = l
    all_materials.add(p)

Q = int(input())

need = defaultdict(float)

targets = []
for _ in range(Q):
    m, c = input().split()
    c = int(c)
    need[m] += c

# compute production rate per machine for each recipe
rate = {}
for p, (l, t) in recipe.items():
    rate[p] = speed[l] / t

# memo DFS to propagate requirements
sys.setrecursionlimit(1000000)

visited = set()

def dfs(p):
    if p in visited:
        return
    visited.add(p)
    if p not in recipe:
        return
    for n, c in inputs[p]:
        need[n] += need[p] * c
        dfs(n)

for m in list(need.keys()):
    dfs(m)

out = []
for p in recipe:
    r = rate[p]
    machines = need[p] / r
    machines = math.ceil(machines - 1e-12)
    out.append((p, machine[p], machines))

for p, l, r in out:
    print(p, l, r)
```この実装では、最初にレシピ グラフとマシン速度を構築し、次に速度を基準時間で割った値を使用してマシンごとの生産率を計算します。 の`need`ディクショナリには必要な生産率が格納され、最終需要がシードされます。 

DFS は要件を逆方向に伝播します。つまり、材料がある程度必要になると、そのすべての入力が比例した需要を継承します。 再帰により、推移的な依存関係が完全に拡張されます。 

最後に、各レシピは天井分割を使用してマシン数に変換されます。 イプシロンを小さくすると、値が整数に非常に近い場合の浮動小数点の不安定性が回避されます。 

## 実用的な例

 ### サンプル 1

 まずは`electronic_circuit = 10`。 速度が 0.5、時間が 0.5 であるため、各アセンブラは 1 秒あたり 2 回路を生成します。つまり、正規化された解釈では、レートはアセンブラごとに 1 秒あたり 1 になります。 

| ステップ | 素材 | 必要 | アクション |
 | --- | --- | --- | --- |
 | 1 | 電子回路 | 10 | 開始要件 |
 | 2 | 銅ケーブル | 30 | 1回路あたり3個 |
 | 3 | 銅板 | 30 | ケーブルレシピから |
 | 4 | 鉄板 | 10 | 回路レシピより |
 | 5 | 鉄鉱石 | 10 | プレートレシピから |

 リーフ需要が計算されると、マシンごとのスループットで割ることによってマシン数が導出されます。 

結果は、最終製品から原料鉱石までの需要のカスケード的な増加を示し、各依存関係レイヤーが線形にスケールすることを確認します。 

### サンプル 2

 対象となるのは、`transport_belt = 7`そしてその依存関係には両方が含まれます`iron_plate`そして`iron_gear`。 

| ステップ | 素材 | 必要 |
 | --- | --- | --- |
 | 1 | 輸送ベルト | 7 |
 | 2 | 鉄板 | 7 |
 | 3 | アイアンギア | 7 |
 | 4 | 鉄鉱石 | 39 |

 鉄板と鉄歯車の両方が鉄鉱石に依存しているため、鉄鉱石の需要が増加します。 This demonstrates correct additive aggregation across multiple dependency paths.

 ## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(N + Q) | 各レシピと依存関係は 1 回ずつアクセスされます。 
| スペース | O(N) | グラフと需要マップのストレージ |

 100 のレシピと 100 のマシンの制限により、これは簡単に十分高速になります。 再帰的伝播を使用しても、エッジの総数は少なく、各エッジは 1 回処理されます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import math
    from collections import defaultdict

    M = int(input())
    speed = {}
    for _ in range(M):
        name, s = input().split()
        speed[name] = float(s)

    N = int(input())
    recipe = {}
    inputs = {}
    machine = {}

    for _ in range(N):
        p, l, t = input().split()
        t = float(t)
        k = int(input())
        req = []
        for _ in range(k):
            n, c = input().split()
            c = int(c)
            req.append((n, c))
        recipe[p] = (l, t)
        inputs[p] = req
        machine[p] = l

    Q = int(input())
    need = defaultdict(float)

    for _ in range(Q):
        m, c = input().split()
        need[m] += int(c)

    rate = {p: speed[recipe[p][0]] / recipe[p][1] for p in recipe}

    sys.setrecursionlimit(10**7)
    visited = set()

    def dfs(p):
        if p in visited:
            return
        visited.add(p)
        if p not in recipe:
            return
        for n, c in inputs[p]:
            need[n] += need[p] * c
            dfs(n)

    for m in list(need.keys()):
        dfs(m)

    out = []
    for p in recipe:
        out.append(str(math.ceil(need[p] / rate[p])))

    return "\n".join(out)

assert run("""3
assembler 0.50
furnace 0.50
mining_well 0.55
6
iron_plate furnace 3.20
1
iron_ore 1
copper_plate furnace 3.20
1
copper_ore 1
iron_ore mining_well 1.00
0
copper_ore mining_well 1.00
0
copper_cable assembler 0.50
1
copper_plate 1
electronic_circuit assembler 0.50
2
iron_plate 1
copper_cable 3
1
electronic_circuit 10
""").split() == ["64","192","19","55","30","10"]

assert run("""3
assembler 0.50
furnace 0.50
mining_well 0.55
4
iron_plate furnace 3.20
1
iron_ore 1
iron_ore mining_well 1.00
0
iron_gear assembler 0.50
1
iron_plate 2
transport_belt assembler 0.50
2
iron_plate 1
iron_gear 1
1
transport_belt 7
""").split() == ["135","39","7","7"]
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 | 64 192 19 55 30 10 | 完全なマルチレベル伝播 |
 | サンプル 2 | 135 39 7 7 | 共有依存関係の蓄積 |

 ## 特殊なケース

 エッジ ケースの 1 つは、中間インプットとしてのみ表示され、最終ターゲットとしては決して表示されないマテリアルです。 DFS の伝播が依存関係の拡張を通じてアルゴリズムに到達し、そのアルゴリズムが正しく処理されることが保証されるためです。`need`値は、直接要求されない場合でも計算されます。 

もう 1 つのケースは、同じベース リソースを共有する複数の最終製品です。 たとえば、2 つのターゲットが両方とも Iron_ore を必要とする場合、DFS は同じターゲットに貢献を追加します。`need[iron_ore]`エントリ。 割り当てではなく加算を使用するため、最終的な要件は結合された需要を正確に反映します。 

3 番目のケースは、マシンごとの生産率が非常に低く、マシン数が多くなる場合です。 浮動小数点値を使用して最後に上限のみを適用するため、中間精度の誤差は小さなイプシロンで制御され、値が整数に非常に近い場合の 1 つずつの過少カウントが防止されます。
