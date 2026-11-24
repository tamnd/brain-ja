---
title: "CF 89B - ウィジェット ライブラリ"
description: "小さな GUI レイアウト システムをシミュレートするように求められます。 ウィジェットは3種類あります。 プレーン ウィジェットの幅と高さは固定です。 HBox と VBox は、他のウィジェットを保存できるコンテナ ウィジェットです。 HBox は子を水平に配置しますが、VBox は子を垂直に配置します。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "dp", "expression-parsing", "graphs", "implementation"]
categories: ["algorithms"]
codeforces_contest: 89
codeforces_index: "B"
codeforces_contest_name: "Codeforces Beta Round 74 (Div. 1 Only)"
rating: 2300
weight: 89
solve_time_s: 123
verified: true
draft: false
---

[CF 89B - ウィジェット ライブラリ](https://codeforces.com/problemset/problem/89/B)

 **評価:** 2300
 **タグ:** dp、式解析、グラフ、実装
 **解決時間:** 2 分 3 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 小さな GUI レイアウト システムをシミュレートするように求められます。 

ウィジェットは3種類あります。 平野`Widget`幅と高さは固定です。`HBox`そして`VBox`他のウィジェットを格納できるコンテナ ウィジェットです。 アン`HBox`子を水平に配置しますが、`VBox`垂直に配置します。 

各コンテナには 2 つのパラメータもあります。`border`コンテンツの四方に空のパディングを追加します。`spacing`連続する子の間にギャップを追加します。 

厄介な点は、コンテナにはコピーではなくウィジェットへの参照が保存されるということです。 より多くの子がウィジェットに詰め込まれたために後でウィジェットのサイズが変更された場合、そのウィジェットが表示されるすべての場所に新しいサイズがすぐに反映されます。 

入力は一連のスクリプト コマンドです。 コマンドの中には、ウィジェットを作成するもの、間隔や境界線を変更するもの、ウィジェットをコンテナにパックするものがあります。 最後に、すべてのウィジェットの最終的な幅と高さを辞書順に出力する必要があります。 

制約は非常に小さいです。 命令は最大 100 個あり、ウィジェット名は短いです。 多くのサイズを繰り返し再計算するアルゴリズムでも、制限内に問題なく収まります。 本当の課題は、依存関係構造を正しくモデル化し、サイズの式における微妙な間違いを回避することです。 

パッキング関係は非周期的であることが保証されています。 ウィジェットにそれ自体を間接的に含めることはできません。 最終的なサイズが依存関係グラフを形成するため、この保証は重要です。 サイクルがなければ、すべてのウィジェットのサイズが明確に定義されます。 

いくつかの特殊なケースは、誤って処理されやすいものです。 

空の`HBox`または`VBox`常にサイズがある`0 x 0`、境界線や間隔に関係なく。 

例えば：```
3
HBox a
a.set_border(10)
a.set_spacing(20)
```正しい出力:```
a 0 0
```不用意に実装すると、子がない場合でも境界式が適用され、誤った結果が生成される可能性があります。`20 x 20`。 

間隔は隣接するウィジェット間にのみ表示され、最初のウィジェットの前や最後のウィジェットの後には表示されません。 

例えば：```
4
HBox a
Widget x(10,5)
Widget y(20,5)
a.set_spacing(7)
a.pack(x)
a.pack(y)
```幅は次のとおりです。```
10 + 20 + 7 = 37
```ない`44`。 ウィジェットが 2 つしかないため、ギャップは 1 つだけです。 

もう 1 つの微妙なケースは、共有ウィジェットです。```
5
Widget x(10,20)
HBox a
VBox b
a.pack(x)
b.pack(x)
```ウィジェット`x`は一度だけ存在しますが、両方のレイアウトに寄与します。 以降の変更は`x`両方のコンテナに自動的に影響を与える必要があります。 パックされたウィジェットをコピーされたオブジェクトとして扱うと、この動作は静かに破壊されます。 

入れ子になったコンテナには再帰的な計算も必要です。```
5
HBox outer
VBox inner
Widget x(10,20)
inner.pack(x)
outer.pack(inner)
```のサイズ`outer`の計算されたサイズに依存します`inner`直接のパラメータだけではありません。 

## アプローチ

 最も直接的なアプローチは、何かが変更されるたびにサイズを最初から繰り返し再計算することです。 

すべてのコンテナーについて、すべての子孫を再帰的に評価できます。 アン`HBox`子の幅を合計し、子の最大の高さを取得します。 あ`VBox`子の高さを合計し、子の最大幅を取得します。 次に、境界線と間隔の寄与を追加します。 

ここでは命令が最大 100 個しかないため、この総当りメソッドは実際には十分に高速です。 すべての再計算がすべてのウィジェットを横断する場合でも、合計の作業量はわずかにとどまります。 

単純な再帰実装の主な弱点は、パフォーマンスではなく正確さです。 再帰が不注意に記述されている場合、共有サブツリーへの繰り返しアクセスによって一貫性のない結果が生じる可能性があります。 サイクルによっても無限再帰が発生する可能性がありますが、この問題により、サイクルが決して出現しないことが保証されます。 

重要な観察は、ウィジェット構造が有向非循環グラフを形成しているということです。 すべてのコンテナはその子のみに依存します。 サイクルがないため、各ウィジェットのサイズを再帰的に定義できます。 

これにより、DFS とメモ化を使用して問題を明確にモデル化できます。 

プレーンウィジェットはすでにその寸法を知っています。 

コンテナの場合:

 のために`HBox`:```
width  = sum(child widths) + spacing * (k - 1) + 2 * border
height = max(child heights) + 2 * border
```のために`VBox`:```
width  = max(child widths) + 2 * border
height = sum(child heights) + spacing * (k - 1) + 2 * border
```どこ`k`子供の数です。 

特別な空のコンテナー ルールはすべてをオーバーライドします。 もし`k = 0`、答えはまさにそのとおりです`0 x 0`。 

グラフは非巡回であるため、DFS はすべてのサイズを正しく計算します。 メモ化により、同じサブツリーを何度も再計算することがなくなります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース再帰的再計算 | O(N²) | O(N) | 承認済み |
 | メモ化による最適な DFS | O(N + E) | O(N + E) | 承認済み |

 ここ`N`はウィジェットの数であり、`E`はパッキング関係の数です。 

## アルゴリズムのチュートリアル

 1. すべての命令を解析し、ウィジェット オブジェクトを構築します。 

各ウィジェットには以下が保存されます。`type`(`Widget`、`HBox`、 または`VBox`)

 プレーンウィジェットの固定寸法`border`

`spacing`詰め込まれた子供のリスト
 2. 作成コマンドの場合、対応するウィジェットを初期化します。 

プレーンウィジェットは、幅と高さをすぐに認識します。 
3. のために`pack()`コマンドを実行すると、子ウィジェット名が親コンテナの子リストに追加されます。 

間隔は隣接性に依存するため、順序が重要です。 
4. のために`set_border()`そして`set_spacing()`コマンドを使用して、コンテナーのパラメーターを更新します。 
5. DFS 関数を定義する`solve(name)`最終結果を返す`(width, height)`ウィジェットの。 
6. ウィジェットがプレーンの場合`Widget`、保存されている寸法をすぐに返します。 
7. ウィジェットが空のコンテナの場合は、戻り値を返します。`(0, 0)`。 

この特殊なケースは、境界線または間隔の式を適用する前に発生する必要があります。 
8. すべての子のサイズを再帰的に計算します。 
9. ウィジェットが`HBox`:

 1. すべての子の幅を合計します。 
2. 間隔を乗算して追加します。`(children_count - 1)`。 
3.追加`2 * border`。 
4. 子の最大の高さを取得し、追加します。`2 * border`。 
10. ウィジェットが`VBox`:
 11. すべての子供の身長を合計します。 
12. 間隔を乗算して追加します。`(children_count - 1)`。 
13.追加`2 * border`。 
14. Take the maximum child width and add`2 * border`。 
15. すべての計算結果をメモ化して、同じウィジェットを繰り返し参照するコストを削減します。 
16. すべてのウィジェットを評価し、結果を辞書順にソートして出力します。 

### なぜ効果があるのか

 自己封じ込めが禁止されているため、パッキング構造は DAG です。 すべてのウィジェットのサイズは、このグラフの下位の子孫にのみ依存します。 

DFS は親のサイズよりも先に子のサイズを計算するため、コンテナーが評価されるときに、必要なすべての子のサイズがすでに正確になっています。 

数式はレイアウト ルールに正確に一致します。 

のために`HBox`、幅は水平方向に蓄積されますが、高さは最も背の高い子によって制限されます。 

のために`VBox`、高さは垂直方向に累積されますが、幅は最も幅の広い子によって制限されます。 

間隔は連続するウィジェット間に 1 回だけ寄与するため、乗数は次のようになります。`(k - 1)`。 

空のコンテナの特殊ケースはステートメントに直接一致し、境界線や間隔が空のレイアウトに誤って影響を与えるのを防ぎます。 

すべてのウィジェットはこれらの正確なルールに従って評価されるため、計算された寸法はすべて正確です。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())

    widgets = {}

    for _ in range(n):
        s = input().strip()

        if s.startswith("Widget "):
            rest = s[7:]
            name, dims = rest.split("(")
            dims = dims[:-1]

            w, h = map(int, dims.split(","))

            widgets[name] = {
                "type": "Widget",
                "w": w,
                "h": h,
                "border": 0,
                "spacing": 0,
                "children": []
            }

        elif s.startswith("HBox "):
            name = s[5:]

            widgets[name] = {
                "type": "HBox",
                "border": 0,
                "spacing": 0,
                "children": []
            }

        elif s.startswith("VBox "):
            name = s[5:]

            widgets[name] = {
                "type": "VBox",
                "border": 0,
                "spacing": 0,
                "children": []
            }

        elif ".pack(" in s:
            left, right = s.split(".pack(")
            child = right[:-1]

            widgets[left]["children"].append(child)

        elif ".set_border(" in s:
            left, right = s.split(".set_border(")
            val = int(right[:-1])

            widgets[left]["border"] = val

        elif ".set_spacing(" in s:
            left, right = s.split(".set_spacing(")
            val = int(right[:-1])

            widgets[left]["spacing"] = val

    memo = {}

    def dfs(name):
        if name in memo:
            return memo[name]

        node = widgets[name]

        if node["type"] == "Widget":
            ans = (node["w"], node["h"])
            memo[name] = ans
            return ans

        children = node["children"]

        if not children:
            memo[name] = (0, 0)
            return (0, 0)

        sizes = [dfs(child) for child in children]

        border = node["border"]
        spacing = node["spacing"]
        k = len(children)

        if node["type"] == "HBox":
            width = sum(w for w, h in sizes)
            width += spacing * (k - 1)
            width += 2 * border

            height = max(h for w, h in sizes)
            height += 2 * border

        else:
            width = max(w for w, h in sizes)
            width += 2 * border

            height = sum(h for w, h in sizes)
            height += spacing * (k - 1)
            height += 2 * border

        ans = (width, height)
        memo[name] = ans
        return ans

    for name in sorted(widgets):
        w, h = dfs(name)
        print(name, w, h)

solve()
```解析ロジックはスクリプト言語を直接反映しています。 すべてのウィジェットは、そのタイプ、レイアウト パラメーター、および子リストを含む辞書になります。 

再帰的`dfs`関数はウィジェット サイズの数学的定義を実装します。 メモ化辞書は、同じウィジェットが複数のコンテナにパックされている場合の計算の繰り返しを防ぎます。 

最も重要な実装の詳細は、空のコンテナーのチェックです。```
if not children:
    return (0, 0)
```これは、境界線や間隔を適用する前に行う必要があります。 それ以外の場合は、境界線のある空のコンテナー`10`間違ってそうなってしまう`20 x 20`。 

もう 1 つの微妙な点は、間隔の公式です。```
spacing * (k - 1)
```隣接するウィジェット間にのみギャップがあります。 子供が1人いるとギャップはゼロです。 

入力によってサイクルがないことが保証されるため、再帰は安全です。 

## 実用的な例

 ### サンプル 1

 入力:```
12
Widget me(50,40)
VBox grandpa
HBox father
grandpa.pack(father)
father.pack(me)
grandpa.set_border(10)
grandpa.set_spacing(20)
Widget brother(30,60)
father.pack(brother)
Widget friend(20,60)
Widget uncle(100,20)
grandpa.pack(uncle)
```### トレース

 | ステップ | ウィジェット | 計算 | 結果 |
 | --- | --- | --- | --- |
 | 1 | 私 | 固定ウィジェット | 50×40 |
 | 2 | 兄 | 固定ウィジェット | 30×60 |
 | 3 | 叔父 | 固定ウィジェット | 100×20 |
 | 4 | 父 | 幅 = 50 + 30、高さ = max(40,60) | 80×60 |
 | 5 | おじいちゃん | 幅 = 最大(80,100) + 20 | 120 |
 | 6 | おじいちゃん | 高さ = 60 + 20 + 間隔 20 + 境界線 20 | 120 |

 最終出力:```
brother 30 60
father 80 60
friend 20 60
grandpa 120 120
me 50 40
uncle 100 20
```この例では、ネストされたコンテナーを示します。`grandpa`の計算されたサイズに依存します`father`、それ自体が依存します`me`そして`brother`。 

### カスタム例

 入力:```
6
HBox a
a.set_border(5)
a.set_spacing(7)
Widget x(10,20)
Widget y(30,40)
a.pack(x)
a.pack(y)
```### トレース

 | ステップ | ウィジェット | 計算 | 結果 |
 | --- | --- | --- | --- |
 | 1 | × | 固定ウィジェット | 10×20 |
 | 2 | y | 固定ウィジェット | 30×40 |
 | 3 | 幅 | 10 + 30 + 7 + 10 | 57 |
 | 4 | 高さ | 最大(20,40) + 10 | 50 |

 最終出力:```
a 57 50
x 10 20
y 30 40
```この例は、境界線と間隔がどのように独立して寄与するかを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(N + E) | 各ウィジェットとパッキング エッジは 1 回処理されます。 
| スペース | O(N + E) | ウィジェット、子リスト、メモ化のためのストレージ |

 最大でも 100 個の命令があるため、このソリューションは問題なく制限内に収まります。 再帰的走査を繰り返しても成功しますが、メモ化により実装がクリーンに保たれ、冗長な作業が回避されます。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    n = int(input())

    widgets = {}

    for _ in range(n):
        s = input().strip()

        if s.startswith("Widget "):
            rest = s[7:]
            name, dims = rest.split("(")
            dims = dims[:-1]

            w, h = map(int, dims.split(","))

            widgets[name] = {
                "type": "Widget",
                "w": w,
                "h": h,
                "border": 0,
                "spacing": 0,
                "children": []
            }

        elif s.startswith("HBox "):
            name = s[5:]

            widgets[name] = {
                "type": "HBox",
                "border": 0,
                "spacing": 0,
                "children": []
            }

        elif s.startswith("VBox "):
            name = s[5:]

            widgets[name] = {
                "type": "VBox",
                "border": 0,
                "spacing": 0,
                "children": []
            }

        elif ".pack(" in s:
            left, right = s.split(".pack(")
            child = right[:-1]

            widgets[left]["children"].append(child)

        elif ".set_border(" in s:
            left, right = s.split(".set_border(")
            val = int(right[:-1])

            widgets[left]["border"] = val

        elif ".set_spacing(" in s:
            left, right = s.split(".set_spacing(")
            val = int(right[:-1])

            widgets[left]["spacing"] = val

    memo = {}

    def dfs(name):
        if name in memo:
            return memo[name]

        node = widgets[name]

        if node["type"] == "Widget":
            memo[name] = (node["w"], node["h"])
            return memo[name]

        children = node["children"]

        if not children:
            memo[name] = (0, 0)
            return memo[name]

        sizes = [dfs(c) for c in children]

        border = node["border"]
        spacing = node["spacing"]
        k = len(children)

        if node["type"] == "HBox":
            w = sum(x for x, y in sizes)
            w += spacing * (k - 1)
            w += 2 * border

            h = max(y for x, y in sizes)
            h += 2 * border

        else:
            w = max(x for x, y in sizes)
            w += 2 * border

            h = sum(y for x, y in sizes)
            h += spacing * (k - 1)
            h += 2 * border

        memo[name] = (w, h)
        return memo[name]

    out = []

    for name in sorted(widgets):
        w, h = dfs(name)
        out.append(f"{name} {w} {h}")

    return "\n".join(out)

# provided sample
assert run(
"""12
Widget me(50,40)
VBox grandpa
HBox father
grandpa.pack(father)
father.pack(me)
grandpa.set_border(10)
grandpa.set_spacing(20)
Widget brother(30,60)
father.pack(brother)
Widget friend(20,60)
Widget uncle(100,20)
grandpa.pack(uncle)
"""
) == (
"""brother 30 60
father 80 60
friend 20 60
grandpa 120 120
me 50 40
uncle 100 20"""
), "sample 1"

# single widget
assert run(
"""1
Widget a(5,7)
"""
) == (
"""a 5 7"""
), "single widget"

# empty container ignores border and spacing
assert run(
"""3
HBox a
a.set_border(10)
a.set_spacing(20)
"""
) == (
"""a 0 0"""
), "empty container"

# spacing counted only between widgets
assert run(
"""6
HBox a
Widget x(10,5)
Widget y(20,5)
a.set_spacing(7)
a.pack(x)
a.pack(y)
"""
) == (
"""a 37 5
x 10 5
y 20 5"""
), "spacing formula"

# nested layouts
assert run(
"""6
VBox root
HBox row
Widget x(10,20)
Widget y(30,40)
row.pack(x)
row.pack(y)
root.pack(row)
"""
) == (
"""root 40 40
row 40 40
x 10 20
y 30 40"""
), "nested containers"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一のウィジェット | 固定寸法が保持される | 基本ケース |
 | 空のコンテナ |`0 x 0`| 空のレイアウトでは境界線と間隔が無視される |
 | 間隔をあけた 2 つのウィジェット | 正しいギャップカウント | 間隔の数式で 1 ​​つずつオフ |
 | 入れ子になったレイアウト | 再帰的計算 | 親は子のサイズに依存します |

 ## 特殊なケース

 ### 空のコンテナ

 入力:```
3
VBox a
a.set_border(5)
a.set_spacing(10)
```アルゴリズムが到達する`a`、子リストが空であることを確認し、すぐに戻ります`(0, 0)`。 

出力：```
a 0 0
```これにより、境界線と間隔が空のレイアウトに誤って影響を与えるのを防ぎます。 

### 間隔のある単一の子

 入力:```
4
HBox a
Widget x(10,20)
a.set_spacing(100)
a.pack(x)
```アルゴリズムは以下を計算します。```
width = 10 + 100 * (1 - 1) = 10
```子供が一人しかいないのでギャップはゼロです。 

出力：```
a 10 20
x 10 20
```これにより、ギャップごとに 1 回ではなく、子ごとに 1 回スペースを追加するという古典的な間違いが見つかります。 

### 共有ウィジェット

 入力:```
5
Widget x(10,20)
HBox a
VBox b
a.pack(x)
b.pack(x)
```DFS は計算します`x`一度メモしてください。 両方`a`そして`b`同じ寸法を再利用します。 

出力：```
a 10 20
b 10 20
x 10 20
```このアルゴリズムは、ウィジェットをコピーするのではなく、参照セマンティクスを正しくモデル化します。 

### 深い入れ子

 入力:```
5
VBox a
HBox b
VBox c
Widget x(7,9)
c.pack(x)
b.pack(c)
a.pack(b)
```再帰では以下が計算されます。```
x -> c -> b -> a
```すべての親は、すでに正しい子ディメンションを使用します。 

出力：```
a 7 9
b 7 9
c 7 9
x 7 9
```これにより、複数のコンテナー レベルを介した再帰的伝播が正しく機能することが確認されます。
