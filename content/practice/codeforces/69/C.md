---
title: "CF 69C - ゲーム"
description: "各プレイヤーはアーティファクトのマルチセットを所有しています。 一部のアーティファクトは基本的なものであり、直接購入できます。 いくつかは複合品であり、固定数量のいくつかの基本的なアーティファクトから作成できます。 プレイヤーが基本的なアーティファクトを購入するたびに、それはインベントリに追加されます。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 69
codeforces_index: "C"
codeforces_contest_name: "Codeforces Beta Round 63 (Div. 2)"
rating: 2000
weight: 69
solve_time_s: 135
verified: true
draft: false
---

[CF 69C - ゲーム](https://codeforces.com/problemset/problem/69/C)

 **評価:** 2000
 **タグ:** 実装
 **解決時間:** 2 分 15 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各プレイヤーはアーティファクトのマルチセットを所有しています。 一部のアーティファクトは基本的なものであり、直接購入できます。 いくつかは複合品であり、固定数量のいくつかの基本的なアーティファクトから作成できます。 

プレイヤーが基本的なアーティファクトを購入するたびに、それはインベントリに追加されます。 その購入の直後に、複合アーティファクトを 1 つだけ作成できるようになる場合があります。 クラフトでは必要な量の材料が消費され、複合アーティファクトのコピーが 1 つ追加されます。 このステートメントは、購入するたびに作成できる複合アーティファクトが最大 1 つであることを保証しているため、複数のレシピから選択する必要はありません。 

タスクは、すべての購入をシミュレートし、最終的にすべてのプレーヤーのインベントリを印刷することです。 正の数量を持つ成果物のみが表示され、名前は辞書順に印刷されなければなりません。 

制約は十分小さいため、直接シミュレーションが可能です。 プレイヤーは最大 100 人、レシピは最大 50 個、購入数は最大 500 個です。 購入するたびにすべてのレシピをスキャンするソリューションでも、レシピ チェックは約 500 × 50 = 25,000 件しか実行されず、ごくわずかです。 

主な困難はパフォーマンスではなく、正しい簿記です。 クラフトではコンポーネントが削除されるため、インベントリは単純なセットではなくマルチセットのように動作する必要があります。 もう 1 つの微妙な点は、複合アーティファクト自体がインベントリ内に残り、基本アーティファクトと共存できることです。 

よくある間違いは、作成後に材料を差し引くのを忘れることです。 次の入力を考えてみましょう。```
1 2 1 2
wood
stone
axe: wood 1, stone 1
1 wood
1 stone
```正しい最終在庫は次のとおりです。```
1
axe 1
```バグのある実装では両方が出力される可能性があります`wood`、`stone`、 そして`axe`、アイテムを作成しますが、材料を消費するのを忘れるためです。 

もう 1 つの簡単な間違いは、対応するプレイヤーの購入ごとにレシピを確認するのではなく、グローバルで 1 回だけレシピを確認することです。 例：```
1 1 1 2
gem
crown: gem 2
1 gem
1 gem
```最初の購入では何も起こりません。 2 回目の購入により、`crown`。 正しい出力は次のとおりです。```
1
crown 1
```購入するたびに再確認しないと、レシピは決してトリガーされません。 

微妙な解析の問題もあります。 レシピ行には、自由形式のテキスト形式でカンマとコロンが含まれています。```
sword: iron 2, wood 1
```スペースのみで分割する脆弱なパーサーでは、次のような名前に句読点が付けられたままになります。`"2,"`または`"sword:"`。 最も安全なアプローチは、最初に結腸の周りで分割し、次にコンマの周りで成分リストを分割することです。 

## アプローチ

 強引なアイデアは単純なシミュレーションです。 

購入するたびに、購入したアーティファクトがプレイヤーのインベントリに追加されます。 次に、すべてのレシピをスキャンして、プレーヤーが必要なすべての材料を十分に所有しているかどうかを確認します。 一部のレシピが作成可能である場合は、必要な数を減算し、複合アーティファクトのコピーを 1 つ追加します。 

これは正しいです。なぜなら、各購入後に作成可能になるレシピは 1 つだけであることが保証されているからです。 私たちは矛盾する選択に直面することはありません。 

ブルートフォース手法はすでに制限内に問題なく収まっています。 させて`q`購入数になりますが、`m`レシピの数と、`n`レシピ内の材料の最大数。 最悪の作業はおおよそ次のとおりです。`q × m × n`、最大でも`500 × 50 × 50 = 1,250,000`成分チェック。 

制約が小さいため、高度なグラフ処理や依存関係の追跡は必要ありません。 実際の作業は、シミュレーションを慎重に実装することにあります。 

重要な観察は、クラフトは 1 人のプレイヤーの現在のインベントリにのみ依存するということです。 購入がプレイヤー間で相互作用することはないため、各インベントリは独立して保存できます。 各プレイヤーのインベントリを頻度マップとして表すと、レシピのチェックは必要なカウントと所有されているカウントを直接比較することになります。 

最適なソリューションは本質的に同じシミュレーションですが、インベントリのハッシュ マップと解析されたレシピ構造を使用してクリーンに実装されています。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(q × m × n) | O(k × A) | 承認済み |
 | 最適 | O(q × m × n) | O(k × A) | 承認済み |

 ここ`A`個別のアーティファクト名の総数です。 

## アルゴリズムのチュートリアル

 1. すべての基本的なアーティファクト名を読み取ります。 

実際にはシミュレーション中に基本アーティファクトと複合アーティファクトを区別する必要はありませんが、名前を読み取ることでアーティファクト識別子の世界が確立されます。 
2. すべての複合レシピを解析します。 

各レシピには、結果として得られるアーティファクトと、材料と数のペアのリストが含まれています。 それらを次のように保存します。```
result_name -> list of (ingredient, required_count)
```カンマとコロンは入力形式の一部であるため、慎重に解析することが重要です。 
3. プレイヤーごとに 1 つのインベントリ マップを作成します。 

各インベントリには以下が保存されます。```
artifact_name -> quantity
```4. 購入を 1 つずつ処理します。 

ご購入の場合`(player, artifact)`:

 - そのプレイヤーの購入したアーティファクトの数を増やします。 
- すべてのレシピをスキャンします。 
- 各レシピについて、プレイヤーがすべての材料を十分に所有しているかどうかを確認してください。 
- レシピが作成可能な場合:

 - すべての成分数を減算します。 
- 複合アーティファクトのコピーを 1 つ追加します。 
- レシピのスキャンを停止します。 

ステートメントでは、購入後に利用できるレシピは最大 1 つであることが保証されているため、すぐに中止します。 
5. すべての購入が完了したら、すべてのプレーヤーのインベントリを印刷します。 

正のカウントを持つアーティファクトのみを印刷する必要があります。 出力は、アーティファクト名によって辞書順に並べ替える必要があります。 

### なぜ効果があるのか

 不変条件は、各購入を処理した後、すべてのプレイヤーの在庫がゲーム ルールと正確に一致することです。 

購入が発生すると、まず購入したアーティファクトを追加します。 次に、すべてのレシピを確認します。 レシピが作成可能な場合は、必要な材料を正確に消費し、結果として得られるアーティファクトを 1 つだけ追加します。 このステートメントは新しく利用可能なレシピの一意性を保証するため、最初に成功したクラフトが意図したゲーム動作と一致した後に停止します。 

在庫はイベント終了後すぐに更新されるため、次回購入前の状態は常に正しい状態になります。 すべての購入に対して帰納法を適用すると、最終的な在庫は正確になります。 

## Python ソリューション```python
import sys
from collections import defaultdict

input = sys.stdin.readline

def solve():
    k, n, m, q = map(int, input().split())

    basic = [input().strip() for _ in range(n)]

    recipes = []

    for _ in range(m):
        line = input().strip()

        result, rest = line.split(": ")

        ingredients = []

        parts = rest.split(", ")

        for part in parts:
            name, cnt = part.rsplit(" ", 1)
            ingredients.append((name, int(cnt)))

        recipes.append((result, ingredients))

    inventories = [defaultdict(int) for _ in range(k)]

    for _ in range(q):
        player, artifact = input().split()
        player = int(player) - 1

        inventories[player][artifact] += 1

        for result, ingredients in recipes:
            ok = True

            for name, need in ingredients:
                if inventories[player][name] < need:
                    ok = False
                    break

            if ok:
                for name, need in ingredients:
                    inventories[player][name] -= need

                inventories[player][result] += 1
                break

    out = []

    for inv in inventories:
        items = []

        for name in sorted(inv.keys()):
            if inv[name] > 0:
                items.append((name, inv[name]))

        out.append(str(len(items)))

        for name, cnt in items:
            out.append(f"{name} {cnt}")

    print("\n".join(out))

solve()
```ソリューションはシミュレーションに直接従います。 

レシピは結果名と材料リストのペアとして保存されます。 解析では、`split(": ")`まず、コロンが作成されたアーティファクトとその要件を区切っているためです。 次に、成分セクションは次のように分割されます。`", "`各要件を分離します。 ついに、`rsplit(" ", 1)`成分名とその数量を区別します。 使用する`rsplit`アーティファクト名は任意の文字列であるため、より安全です。 

各プレイヤーのインベントリは、`defaultdict(int)`、不足しているアーティファクトは自動的に数量ゼロとして扱われます。 

購入するたびに、入力された順にレシピをスキャンします。 レシピごとに、すべての材料数が利用可能かどうかを確認します。 その場合は、必要な数量を差し引いて、作成されたアーティファクトを追加します。 

実装の微妙な詳細の 1 つは、クラフト直後に停止することです。 この声明は、購入後に利用できるレシピが 1 つだけであることを保証します。 スキャンを続行すると、単一のイベントから複数のクラフトが誤って許可される可能性があります。 

作成中に消費された材料が辞書にキーとして残っている可能性があるため、結果を印刷するときにゼロカウントを除外します。 

## 実用的な例

 ### 例 1

 入力:```
2 3 2 5
desolator
refresher
perseverance
vanguard: desolator 1, refresher 1
maelstorm: perseverance 2
1 desolator
2 perseverance
1 refresher
2 desolator
2 perseverance
```### トレース

 | ステップ | プレーヤー | 購入 | クラフト前の在庫 | 細工された | 後の在庫 |
 | --- | --- | --- | --- | --- | --- |
 | 1 | 1 | 荒廃者 デソレーター=1 | なし | デソレーター=1 |
 | 2 | 2 | 忍耐力 | 忍耐力=1 | なし | 忍耐力=1 |
 | 3 | 1 | おさらい | デソレーター=1、リフレッシュ=1 | 前衛 | 前衛=1 |
 | 4 | 2 | 荒廃者 忍耐力 = 1、荒廃者 = 1 | なし | 忍耐力 = 1、荒廃者 = 1 |
 | 5 | 2 | 忍耐力 | 忍耐力 = 2、荒廃者 = 1 | メイルストーム | マエルストーム=1、デソレーター=1 |

 最終出力:```
1
vanguard 1
2
desolator 1
maelstorm 1
```この例では、材料の消費を示します。 製作後`vanguard`、 両方`desolator`そして`refresher`プレイヤー 1 のインベントリから消えます。 

### 例 2

 入力:```
1 2 1 4
wood
stone
axe: wood 2, stone 1
1 wood
1 stone
1 wood
1 wood
```### トレース

 | ステップ | 購入 | クラフト前の在庫 | 細工された | 後の在庫 |
 | --- | --- | --- | --- | --- |
 | 1 | 木材 | 木材=1 | なし | 木材=1 |
 | 2 | 石 | 木=1、石=1 | なし | 木=1、石=1 |
 | 3 | 木材 | 木=2、石=1 | 斧 | 斧=1 |
 | 4 | 木材 | 斧=1、木=1 | なし | 斧=1、木=1 |

 最終出力:```
2
axe 1
wood 1
```このトレースは、要件が満たされるとすぐに作成が行われ、正確に必要な量だけが消費されることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(q × m × n) | 購入するたびに、すべてのレシピとすべてのレシピの材料がスキャンされます。 
| スペース | O(k × A) | 各プレイヤーは所有するアーティファクトの数を保存します。 

最大作業量は約 125 万回の成分チェックですが、これは 2 秒以内の Python としては非常に小さいです。 個別のアーティファクト名の合計数が最大 100 であるため、メモリ使用量もわずかです。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
from collections import defaultdict

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    out_capture = io.StringIO()

    k, n, m, q = map(int, input().split())

    basic = [input().strip() for _ in range(n)]

    recipes = []

    for _ in range(m):
        line = input().strip()

        result, rest = line.split(": ")

        ingredients = []

        for part in rest.split(", "):
            name, cnt = part.rsplit(" ", 1)
            ingredients.append((name, int(cnt)))

        recipes.append((result, ingredients))

    inventories = [defaultdict(int) for _ in range(k)]

    for _ in range(q):
        player, artifact = input().split()
        player = int(player) - 1

        inventories[player][artifact] += 1

        for result, ingredients in recipes:
            ok = True

            for name, need in ingredients:
                if inventories[player][name] < need:
                    ok = False
                    break

            if ok:
                for name, need in ingredients:
                    inventories[player][name] -= need

                inventories[player][result] += 1
                break

    ans = []

    for inv in inventories:
        items = []

        for name in sorted(inv.keys()):
            if inv[name] > 0:
                items.append((name, inv[name]))

        ans.append(str(len(items)))

        for name, cnt in items:
            ans.append(f"{name} {cnt}")

    return "\n".join(ans)

# provided sample
assert run(
"""2 3 2 5
desolator
refresher
perseverance
vanguard: desolator 1, refresher 1
maelstorm: perseverance 2
1 desolator
2 perseverance
1 refresher
2 desolator
2 perseverance
"""
) == (
"""1
vanguard 1
2
desolator 1
maelstorm 1"""
), "sample 1"

# minimum input
assert run(
"""1 1 0 1
a
1 a
"""
) == (
"""1
a 1"""
), "minimum case"

# repeated crafting
assert run(
"""1 1 1 4
gem
crown: gem 2
1 gem
1 gem
1 gem
1 gem
"""
) == (
"""1
crown 2"""
), "multiple crafts over time"

# leftover ingredients
assert run(
"""1 2 1 3
wood
stone
axe: wood 2, stone 1
1 wood
1 stone
1 wood
"""
) == (
"""1
axe 1"""
), "exact consumption"

# independent player inventories
assert run(
"""2 1 1 2
gem
crown: gem 2
1 gem
2 gem
"""
) == (
"""1
gem 1
1
gem 1"""
), "players separated"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 最小入力 | 単一のアーティファクトを所有 | ベースの初期化 |
 | 繰り返しのクラフト |`crown 2`| 作成は購入間で複数回行われる可能性があります |
 | 残り物食材 | のみ`axe 1`| 食材を正しく摂取する |
 | 独立した在庫 | 各プレイヤーは自分の宝石を保持します | 偶発的な共有状態が発生しない |

 ## 特殊なケース

 クラフトですべての材料を正確に消費する場合を考えてみましょう。```
1 2 1 2
wood
stone
axe: wood 1, stone 1
1 wood
1 stone
```2 回目の購入後の在庫は次のようになります。```
wood=1, stone=1
```レシピは作成可能であるため、両方のカウントがゼロに減り、`axe=1`が追加されます。 出力中、ゼロカウントのエントリはスキップされます。 最終結果は次のとおりです。```
1
axe 1
```次に、遅延クラフトについて考えてみましょう。```
1 1 1 3
gem
crown: gem 2
1 gem
1 gem
1 gem
```初回購入後はクラフトできなくなります。 2 回目以降、在庫は次のとおりになります。`gem=2`、 それで`crown`が作成され、両方の宝石が消えます。 3 回目の購入後の在庫は次のようになります。```
crown=1, gem=1
```アルゴリズムは購入のたびにレシピをチェックするため、タイミングは正しく処理されます。 

最後に、複数のプレーヤーについて考えてみましょう。```
2 1 1 2
gem
crown: gem 2
1 gem
2 gem
```プレイヤー 1 は 1 つの宝石を所有し、プレイヤー 2 は 1 つの宝石を所有します。 どちらもクラフトはできない`crown`、在庫は独立しているためです。 このアルゴリズムはプレーヤーごとに個別の辞書を保存するため、プレーヤー間でカウントが混在することはありません。
