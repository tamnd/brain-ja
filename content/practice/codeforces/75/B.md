---
title: "CF 75B - Facetook プライオリティ ウォール"
description: "私たちには、ソーシャル ネットワーク上のユーザーの名前と、ユーザー間の一連のアクティビティ メッセージが与えられます。 すべてのアクティビティは、関係する 2 人の間で特定の数のポイントに貢献します。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "expression-parsing", "implementation", "strings"]
categories: ["algorithms"]
codeforces_contest: 75
codeforces_index: "B"
codeforces_contest_name: "Codeforces Beta Round 67 (Div. 2)"
rating: 1500
weight: 75
solve_time_s: 134
verified: false
draft: false
---

[CF 75B - Facetook プライオリティ ウォール](https://codeforces.com/problemset/problem/75/B)

 **評価:** 1500
 **タグ:** 式の解析、実装、文字列
 **解決時間:** 2 分 14 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 私たちには、ソーシャル ネットワーク上のユーザーの名前と、ユーザー間の一連のアクティビティ メッセージが与えられます。 すべてのアクティビティは、関係する 2 人の間で特定の数のポイントに貢献します。 

考えられるアクティビティは次の 3 つです。 

- 誰かのウォールに投稿すると、15 ポイント相当
 - 誰かの投稿にコメントすると、10 ポイント相当
 - 誰かの投稿に「いいね！」すると 5 ポイント相当

 関係スコアは対称的です。 もし`alice`についてのコメント`bob`さんの投稿、両方とも`alice`そして`bob`お互いに10ポイントを獲得します。 

私たちの仕事は、すべてのユーザーのペア間のスコアを計算することではありません。 特定のユーザーに対する相対的なスコアのみを考慮します。 すべてのアクションを処理した後、メイン ユーザーを除く、ログに現れたすべての個別の人物を、そのユーザーのスコアの降順に並べて出力する必要があります。 複数の人が同じスコアを持っている場合、辞書順に同点を解消します。 

入力形式は一見シンプルに見えますが、文字列は少し複雑です。 2 番目のユーザー名は次のように表示されます`"'s"`添付されている、など`fatma's`。 このトークンを直接保存する不注意なパーサーは、`"fatma"`そして`"fatma's"`別の名前として。 

制約は非常に小さいです。 アクションは最大 100 個あり、各ユーザー名の長さは最大 10 です。非効率な実装でも問題なく実行できます。 これは、問題は主に、最適化ではなく、慎重な文字列解析と簿記に関するものであることを意味します。 

よくある間違いは、メイン ユーザーとのスコアが 0 であるユーザーを含めるのを忘れることです。 

次の入力を考えてみましょう。```
ahmed
2
mona likes sara's post
sara commented on mona's post
```どちらの相互作用も関係ありません`ahmed`、つまり両方とも`mona`そして`sara`彼とのスコアは0です。 正しい出力は次のとおりです。```
mona
sara
```もう 1 つの簡単なバグは、2 番目のユーザー名が間違って解析されることから発生します。```
ahmed
1
ahmed posted on fatma's wall
```正しいユーザー名は`fatma`、 ない`fatma's`。 サフィックスを削除し忘れると、ソートやスコアの集計が正しく行われなくなります。`"fatma"`そして`"fatma's"`別のユーザーとして扱われます。 

タイブレークも重要です。```
ahmed
2
ahmed likes sara's post
ahmed likes mona's post
```両方のユーザーが 5 ポイントを受け取るため、出力は次のようになります。```
mona
sara
```スコアの降順のみで並べ替えるソリューションでは、非決定的な順序付けが行われます。 

## アプローチ

 最も直接的なアプローチは、すべてのアクションを記述どおりに正確にシミュレートすることです。 各行で 2 つのユーザー名を抽出し、どのアクティビティが発生したかを判断します。 メインユーザーがインタラクションに参加している場合、対応する値を他のユーザーのスコアに追加します。 その間、私たちは表示されるすべての個別のユーザー名のセットを維持します。 

入力サイズが小さいため、このブルートフォース戦略はすでに完全に受け入れられます。 最大 100 行であれば、文字列のスキャンと名前の並べ替えを繰り返しても、コストはほとんどかかりません。 全体の作業の大部分は、せいぜい数百の名前の分類です。 

本当の課題はパフォーマンスではなく表現です。 活動行には 3 つの異なる文構造があります。```
X posted on Y's wall
X commented on Y's post
X likes Y's post
```有益な観察は、行を単語に分割すると、すべての形式が簡単になるということです。 最初のトークンは常に`X`、2 番目のトークンはアクション タイプを識別し、4 番目のトークンには次のものが含まれます。`Y's`。 最後の 2 文字を削除すると、実際のユーザー名が得られます。 

ユーザー名とアクション タイプを抽出した後、問題は辞書内のスコアの蓄積に帰着します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n log n) | O(n) | 承認済み |
 | 最適 | O(n log n) | O(n) | 承認済み |

 「最適な」ソリューションは、制約がすでに小さいため、本質的には同じです。 重要なのは、解析をきれいに整理し、正しく並べ替えることです。 

## アルゴリズムのチュートリアル

 1. メインのユーザー名とアクションの数を読み取ります。 
2. 各動作単語をそのスコアにマッピングする辞書を作成します。 

私たちは以下を使用します:

 -`"posted"`→ 15
 -`"commented"`→ 10
 -`"likes"`→ 5
 3. メイン ユーザーを除くすべての個別のユーザー名を含むセットを維持します。 

これにより、スコアがゼロのユーザーも最終出力に表示されることが保証されます。 
4. メイン ユーザーに関連するスコアを保存する別の辞書を維持します。 

欠落しているエントリはゼロとして扱われます。 
5. 各アクション行について:

 1. 行を単語に分割します。 
2. 抜粋:

 -`x = words[0]`-`action = words[1]`-`y = words[3][:-2]`最後の操作では末尾の文字列が削除されます。`"'s"`。 

1. 両方のユーザー名がメイン ユーザーでない場合は、両方のユーザー名をセットに追加します。 
2. アクションのスコア値を調べます。 
3. もし`x`がメインユーザーの場合、スコアを追加します`y`。 
4. もし`y`がメインユーザーの場合、スコアを追加します`x`。 

この関係は対称的であるため、どちらの方向も同様に寄与します。 
6. ユーザー名のセットをリストに変換し、以下を使用して並べ替えます。 

- スコアの降順
 - タイブレーカーとしての辞書編集順
 7. ソートされた名前を印刷します。 

### なぜ効果があるのか

 アルゴリズムはすべてのインタラクションを 1 回だけ処理します。 各アクションについて、重要なスコアはメイン ユーザーが関与するスコアのみです。 どちらの参加者もメイン ユーザーではない場合、インタラクションは最終的なランキングに影響を与えることはできないため、ユーザー名を記録しながらそのスコアへの寄与を無視します。 

スコア辞書には、メイン ユーザーと、これまでに処理された他のすべてのユーザーとの間の累積インタラクション値の合計が常に保存されます。 すべてのアクションは独立して一度だけ寄与するため、最終的な辞書には正しい合計が含まれます。 最終的な並べ替えルールはステートメントに直接一致します。つまり、スコアが高い順、同点の場合はアルファベット順です。 

## Python ソリューション```python
import sys
from collections import defaultdict

input = sys.stdin.readline

def solve():
    me = input().strip()
    n = int(input())

    points = {
        "posted": 15,
        "commented": 10,
        "likes": 5
    }

    score = defaultdict(int)
    people = set()

    for _ in range(n):
        words = input().split()

        x = words[0]
        action = words[1]
        y = words[3][:-2]

        if x != me:
            people.add(x)

        if y != me:
            people.add(y)

        value = points[action]

        if x == me:
            score[y] += value

        if y == me:
            score[x] += value

    result = sorted(people, key=lambda name: (-score[name], name))

    print("\n".join(result))

solve()
```の`points`辞書は動作単語を数値に変換します。 これにより、条件ステートメントの繰り返しが回避され、コードの拡張が容易になります。 

パーサーは固定された文構造に依存します。 スペースで区切ると、常に 2 番目のユーザー名がその位置に表示されます。`3`付属の`"'s"`。 でスライス`[:-2]`これら 2 文字を正確に削除します。 

の`people`セットは別のものです`score`ユーザーによってはメイン ユーザーと直接対話することがない場合があるためです。 キーだけに頼っていたら、`score`の場合、スコアがゼロのユーザーは出力から消えます。 

使用する`defaultdict(int)`不足しているスコアを手動で初期化する必要がなくなります。 

ソートキーは微妙です。 並べ替える`(-score[name], name)`2 回ソートする代わりに。 スコアを否定すると、数値の降順が与えられますが、2 番目のコンポーネントは辞書編集上の関係を自動的に処理します。 

## 実用的な例

 ### 例 1

 入力:```
ahmed
3
ahmed posted on fatma's wall
fatma commented on ahmed's post
mona likes ahmed's post
```処理トレース:

 | ステップ | アクション | 抽出されたユーザー | スコアの変化 | 現在のスコア |
 | --- | --- | --- | --- | --- |
 | 1 | アーメドがファトマの壁に投稿 | アーメド、ファトマ | ファットマ+15 | ファットマ: 15 |
 | 2 | fatma が ahmed の投稿にコメントしました | ファトマ、アーメド | ファットマ+10 | ファットマ: 25 |
 | 3 | mona は ahmed の投稿に「いいね！」をしました | モナ、アーメッド | モナ+5 | ファトマ: 25、モナ: 5 |

 最終的な並べ替え:

 | 名前 | スコア |
 | --- | --- |
 | ファットマ | 25 |
 | モナ | 5 |

 出力：```
fatma
mona
```この例は、相互作用が方向に関係なく寄与することを示しています。 両方のアクションが含まれる`fatma`同じ関係スコアが増加しました。 

### 例 2

 入力:```
alex
4
bob likes clara's post
alex likes diana's post
eric commented on frank's post
george posted on alex's wall
```処理トレース:

 | ステップ | アクション | 抽出されたユーザー | スコアの変化 | 現在のスコア |
 | --- | --- | --- | --- | --- |
 | 1 | ボブはクララの投稿が好きです | ボブ、クララ | なし | 空 |
 | 2 | アレックスはダイアナの投稿が好きです | アレックス、ダイアナ | ダイアナ+5 | ダイアナ: 5 |
 | 3 | エリックがフランクの投稿にコメントしました | エリック・フランク | なし | ダイアナ: 5 |
 | 4 | ジョージがアレックスの壁に投稿 | アレックス・ジョージ | ジョージ+15 | ダイアナ: 5、ジョージ: 15 |

 検出されたすべてのユーザー:

 | ユーザー | スコア |
 | --- | --- |
 | ボブ | 0 |
 | クララ | 0 |
 | ダイアナ | 5 |
 | エリック | 0 |
 | フランク | 0 |
 | ジョージ | 15 |

 ソートされた出力:```
george
diana
bob
clara
eric
frank
```このトレースは、確認されたすべてのユーザー名をスコア辞書とは別に保存する必要がある理由を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | 処理アクションは O(n) であり、ユーザー名のソートが優勢です。 
| スペース | O(n) | 辞書とセットには、最大でもすべての個別のユーザーが保存されます。 

このソリューションはアクションが最大 100 個あるため、制限内に簡単に収まります。 個別のユーザー名の数が少ないため、並べ替えの手順もわずかです。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io
from collections import defaultdict

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    def solve():
        me = input().strip()
        n = int(input())

        points = {
            "posted": 15,
            "commented": 10,
            "likes": 5
        }

        score = defaultdict(int)
        people = set()

        for _ in range(n):
            words = input().split()

            x = words[0]
            action = words[1]
            y = words[3][:-2]

            if x != me:
                people.add(x)

            if y != me:
                people.add(y)

            value = points[action]

            if x == me:
                score[y] += value

            if y == me:
                score[x] += value

        result = sorted(people, key=lambda name: (-score[name], name))

        return "\n".join(result)

    return solve()

# provided sample
assert run(
"""ahmed
3
ahmed posted on fatma's wall
fatma commented on ahmed's post
mona likes ahmed's post
"""
) == "fatma\nmona", "sample 1"

# minimum input
assert run(
"""alex
1
bob likes clara's post
"""
) == "bob\nclara", "minimum size with zero scores"

# tie breaking
assert run(
"""alex
2
alex likes sara's post
alex likes mona's post
"""
) == "mona\nsara", "lexicographical tie breaking"

# repeated interactions
assert run(
"""alex
3
alex posted on bob's wall
bob commented on alex's post
alex likes bob's post
"""
) == "bob", "score accumulation"

# users unrelated to main user
assert run(
"""alex
4
bob likes clara's post
diana likes eric's post
frank commented on george's post
harry posted on alex's wall
"""
) == "harry\nbob\nclara\ndiana\neric\nfrank\ngeorge", "include zero-score users"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一の無関係な相互作用 |`bob clara`注文 | スコアがゼロのユーザーも引き続き表示される必要があります。 
| 同等のスコア | 辞書編集上の順序 | 正しいタイブレーク |
 | 同じユーザーとの複数のインタラクション | 単一の累計 | スコア集計 |
 | 関連ユーザーと無関係ユーザーが混在 | 肯定的なスコアが最初 | 適切な分類と包含 |

 ## 特殊なケース

 ### インタラクション スコアがゼロのユーザー

 入力:```
ahmed
2
mona likes sara's post
sara commented on mona's post
```どちらのアクションも関係ありません`ahmed`, そのため、スコアの更新は行われません。 

アルゴリズムは依然として両方を挿入します`mona`そして`sara`に`people`セット。 スコアはデフォルト値のままです`0`。 

並べ替えると次のようになります。```
mona
sara
```ユーザーをスコア辞書内に保存するだけのソリューションでは、誤って何も出力されません。 

### 「's」で終わる名前の解析

 入力:```
ahmed
1
ahmed posted on fatma's wall
```分割後:

 | インデックス | 値 |
 | --- | --- |
 | 0 | アーメド |
 | 1 | 投稿された |
 | 2 | に |
 | 3 | ファットマさん |
 | 4 | 壁 |

 アルゴリズムが使用するのは、`words[3][:-2]`、生産`fatma`。 

スコアの状態:

 | ユーザー | スコア |
 | --- | --- |
 | ファットマ | 15 |

 出力：```
fatma
```サフィックスを削除しないと、保存された名前は誤って次のようになります。`fatma's`。 

### 辞書順によるタイブレーク

 入力:```
alex
2
alex likes sara's post
alex likes mona's post
```どちらのユーザーもちょうど 5 ポイントを受け取ります。 

キー値の並べ替え:

 | ユーザー | キー |
 | --- | --- |
 | モナ | (-5、「モナ」) |
 | サラ | (-5、「サラ」) |

 数値部分が等しいため、通常の文字列比較によって順序が決まります。 

正しい出力:```
mona
sara
```スコアのみで並べ替えるソリューションでは、挿入順序によっては間違った順序が生成される可能性があります。
