---
title: "CF 102448G - ゴージャスなピーターの偉大な友人"
description: "グローバルな提出物の流れから各候補者のスコアを計算する必要があります。 Peter は一連の問題を選択しました。選択されたすべての問題には固定スコアが付いています。 受験者は、その問題への提出が評決 AC を受け取ったときに、その問題のスコアを獲得します。"
date: "2026-08-08T12:19:45+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102448
codeforces_index: "G"
codeforces_contest_name: "UFPE Starters Final Try-Outs 2020"
rating: 0
weight: 102448
solve_time_s: 742
verified: true
draft: false
---

[CF 102448G - ゴージャスなピーターの偉大な友人](https://codeforces.com/problemset/problem/102448/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 12 分 22 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 グローバルな提出物の流れから各候補者のスコアを計算する必要があります。 Peter は一連の問題を選択しました。選択されたすべての問題には固定スコアが付いています。 受験者は、その問題への提出が評決を受けたときに、その問題のスコアを獲得します。`AC`。 

入力により、最初に候補ハンドルが与えられます。 次に、選択した問題 ID とそのスコアが表示されます。 最後に、任意のユーザーからの投稿を提供します。 送信には、ユーザー ハンドル、問題 ID、および判定が含まれます。 提出物の中には、候補者ではないユーザーに属するものや、Peter が選択しなかった問題に関するものもあります。 どちらも受験者のスコアには影響しません。 

すべての候補について、出力には元の候補の順序が保存され、ハンドルと、その後に解決したすべての選択された問題のスコアの合計が含まれている必要があります。 

主な制約は、候補者が最大 50,000 人、選択された問題が 50,000 件、提出物が 50,000 件まで存在できることです。 すべての候補者のすべての提出物を検査するソリューションは、次のようなパフォーマンスを発揮します。 

[
 50,000 \times 50,000 = 2.5 \times 10^9
 】

 小切手。 これは、1 秒の制限時間で対応できる範囲をはるかに超えています。 入力サイズにおいて線形に近いアプローチが必要です。 すべてのハンドルと問題 ID は最大 20 文字であるため、直接検索にハッシュ テーブルを使用することも実用的です。 

不注意な実装により、誤って間違った答えが生成されるケースがいくつかあります。 まず、`AC`非候補者は誰のスコアにも貢献してはなりません。 例えば：```
1 1 1
alice
p1 100
bob p1 AC
```正しい出力は次のとおりです。```
alice 0
```問題だけでスコアを蓄積する解法では、誤ってアリスに 100 点が与えられてしまいます。 

第 2 に、選択されなかった問題は、受験者が解決したとしても無視する必要があります。```
1 1 1
alice
p1 100
alice p2 AC
```正しい出力は次のとおりです。```
alice 0
```不用意に実装すると、すべての項目にスコアが割り当てられる可能性があります。`AC`問題が選択されたセットに属しているかどうかを確認する代わりに、送信します。 

第三に、間違った提出がスコアに影響を与えてはなりません。 考慮する：```
1 1 2
alice
p1 100
alice p1 WA
alice p1 AC
```正しい出力は次のとおりです。```
alice 100
```のみ`AC`提出物が重要です。 以前の`WA`影響はないはずです。 

最後に、受験者は問題を解決するまでに何度か間違った提出をする可能性があります。 これらの試行を処理するときに問題スコアを追加してはなりません。 受信後にユーザーが同じ問題を再度送信しないことを保証します。`AC`1回に1回という意味`AC`と表示される場合、そのユーザーと問題のペアは後で別の提出物を生成しません。 したがって、次のときにスコアを追加できます。`AC`別の重複防止構造を必要とせずに、この問題に遭遇することになります。 

## アプローチ

 最も直接的な解決策は、各候補を個別に処理することです。 1 人の候補者について、すべての提出物をスキャンし、ユーザーがその候補者であるレコードを探します。評決は次のとおりです。`AC`、問題は選択された問題の 1 つです。 そのような提出物が見つかった場合は、対応する問題のスコアを追加します。 

候補者がポイントを獲得できるあらゆる方法が明示的に検査されるため、このアプローチは正しいです。 ただし、すべての候補者に対して同じ提出スキャンが繰り返されます。 候補者が 50,000 人、提出物が 50,000 件ある場合、最悪のケースでは 25 億件の候補者と提出者の比較が行われます。 たとえそれぞれの比較が非常に安価だったとしても、制限時間に対してその作業量は多すぎます。 

より良い観点は、各送信を 1 回だけ処理することです。 提出物にはユーザー、問題、評決がすでに記載されているため、関連する候補者を繰り返し検索する必要はありません。 すべての候補ハンドルを出力内のその位置にマッピングするハッシュ テーブルと、選択されたすべての問題 ID をスコアにマッピングする別のハッシュ テーブルを構築できます。 

その後、以外の評決を伴う提出`AC`すぐに無視できます。 のために`AC`、候補テーブルでそのユーザーを検索し、選択された問題テーブルでその問題を検索します。 両方が存在する場合、その 1 つの提出物によって、スコアが増加する候補者と追加される量が直接特定されます。 

ブルートフォース手法は、最終的にすべての候補者に関連するすべての提出物を検査するため機能しますが、作業を繰り返すため失敗します。 すべての提出物が独立して最大 1 つの候補と 1 つの選択された問題を識別するという観察により、提出物ごとに問題を定数時間のハッシュ テーブル ルックアップに変えることができます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(CS) | O(P + C) | 遅すぎる |
 | 最適 | O(C + P + S) が期待されます | O(C + P) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 候補者の数、選択された問題、および提出物を確認します。 すべての候補ハンドルを出力配列内の位置とともに辞書に保存します。 送信が処理される順序に関係なく、最終出力では入力と同じ順序を使用する必要があるため、この位置は便利です。 
2. 選択したすべての問題とそのスコアを読みます。 問題 ID がキー、スコアが値である辞書にペアを保存します。 これにより、選択されたすべての問題をスキャンして問題のスコアを見つけることが、予想される定数時間の検索に変換されます。 
3. の配列を作成します。`C`ゼロ。 エントリ`i`ハンドルが位置に現れた候補者の累積スコアを表します`i`。 
4. 各送信を 1 回処理します。 その評決がそうでない場合`AC`, スコアが上がらないのでスキップします。 
5.`AC`送信すると、候補辞書で送信されたユーザーのハンドルを検索します。 ハンドルが存在しない場合、提出物は候補者以外のものであるため、回答に影響を与えることはできません。 
6. ユーザーが受験者の場合は、選択された問題辞書で送信された問題を検索します。 これが存在しない場合、問題は選択されていないため、何も貢献しません。 
7. 両方の検索が成功した場合、選択した問題のスコアを候補者の累積スコアに追加します。 提出後の保証について`AC`これは、同じユーザーが後でその問題に対する別の提出を受け取ることができないことを意味するため、スコアは 1 回だけ追加されます。 
8. すべての提出物が処理された後、元の候補者リストを繰り返し処理し、各ハンドルを累積スコアとともに出力します。 ハンドルを入力順序に保つことで、出力順序が正しいことが保証されます。 

不変条件は、提出リストのプレフィックスを処理した後、各受験者が保存したスコアが、その受験者がすでに評価を受けている選択されたすべての問題の合計スコアに等しいということです。`AC`そのプレフィックス内で。 非`AC`送信によって不変式を変更することはできません。 アン`AC`非候補または未選択の問題からの投稿もできません。 のために`AC`選択した問題の受験者から、その受験者のスコアがその問題のスコアだけ増加します。 したがって、不変式は送信のたびに true のままとなり、最後の送信後には必要な答えが正確に得られます。 

## Python ソリューション```python
import sys

input = sys.stdin.readline

def solve():
    C, P, S = map(int, input().split())

    candidates = []
    candidate_index = {}

    for i in range(C):
        handle = input().strip()
        candidates.append(handle)
        candidate_index[handle] = i

    problem_score = {}

    for _ in range(P):
        problem, score = input().split()
        problem_score[problem] = int(score)

    answer = [0] * C

    for _ in range(S):
        user, problem, verdict = input().split()

        if verdict != "AC":
            continue

        idx = candidate_index.get(user)
        if idx is None:
            continue

        score = problem_score.get(problem)
        if score is None:
            continue

        answer[idx] += score

    output = []
    for i in range(C):
        output.append(f"{candidates[i]} {answer[i]}")

    sys.stdout.write("\n".join(output))

if __name__ == "__main__":
    solve()
```の`candidates`list は正確な入力順序を保持しますが、`candidate_index`対応するスコア位置への素早いアクセスを提供します。 たとえば、次の場合`beza`第二候補だったのですが、`candidate_index["beza"]`は`1`、つまり、対象となるすべての提出物は、`beza`アップデート`answer[1]`。 

の`problem_score`辞書は問題に対して同じ役割を果たします。 キーの存在によって問題が選択されたことがすでにわかり、その値によってスコアが得られるため、選択した問題を個別に保存する必要はありません。 

判定は辞書検索の前にチェックされます。 これは、漸近的に複雑になるためには必要ありませんが、そうでない潜在的に多数の送信に対する不必要な作業を回避します。`AC`。 

使用する`.get()`これにより、例外を発生させることなく、行方不明のユーザーまたは問題を区別できるようになります。 スコアは厳密にプラスであるため、`None`有効なスコアと混同しないでください。 

Python の整数は、任意の大きな値を自動的に処理します。 個々のスコアは最大 20,000 ですが、選択された多くの問題が解決されると合計は約 10 億に達する可能性があり、それでも Python 整数によって安全に処理されます。 

候補位置はゼロベースのインデックスを使用して直接保存されるため、オフバイワンの計算はありません。 出力ループは、それらとまったく同じ位置にアクセスします。 

## 実用的な例

 ### サンプル 1

 候補者は、`GabrielPessoa`そして`beza`。 選ばれた問題は、`metebronca`、100の価値があり、`geometry`、200の価値があります。 

| 提出 | 評決 | 候補者の検索 | 問題の検索 | 提出後のスコア |
 | --- | --- | --- | --- | --- |
 |`beza metebronca AC`| 交流 |`beza -> 1`|`metebronca -> 100`| ガブリエルペソア = 0、ベザ = 100 |
 |`ffern numbertheory AC`| 交流 | 不在 | 必要ありません | ガブリエルペソア = 0、ベザ = 100 |
 |`GabrielPessoa geometry WA`| ワシントン州 | 必要ありません | 必要ありません | ガブリエルペソア = 0、ベザ = 100 |
 |`beza geometry AC`| 交流 |`beza -> 1`|`geometry -> 200`| ガブリエルペソア = 0、ベザ = 300 |

 2回目の提出物は、`AC`、 しかし`ffern`は候補ではないので破棄します。 3 番目の提出物は候補者からのもので、選択された問題に関するものですが、その評決は次のとおりです。`WA`, したがって、これも破棄されます。 選ばれた 2 つの成功した問題を解決したのは、`beza`100 と 200 を寄付すると、300 になります。 

最終的な出力は次のとおりです。```
GabrielPessoa 0
beza 300
```### 構築例 2

 次のことを考慮してください。```
3 2 5
alice
bob
carol
p1 50
p2 100
alice p1 WA
bob p3 AC
carol p2 AC
alice p1 AC
bob p1 AC
```トレースは次のとおりです。 

| 提出 | 評決 | 候補者 | 選択した問題 | 提出後のスコア |
 | --- | --- | --- | --- | --- |
 |`alice p1 WA`| ワシントン州 | 処理されていません | 処理されていません | アリス = 0、ボブ = 0、キャロル = 0 |
 |`bob p3 AC`| 交流 |`bob`見つかりました |`p3`不在 | アリス = 0、ボブ = 0、キャロル = 0 |
 |`carol p2 AC`| 交流 |`carol`見つかりました |`p2 -> 100`| アリス = 0、ボブ = 0、キャロル = 100 |
 |`alice p1 AC`| 交流 |`alice`見つかりました |`p1 -> 50`| アリス = 50、ボブ = 0、キャロル = 100 |
 |`bob p1 AC`| 交流 |`bob`見つかりました |`p1 -> 50`| アリス = 50、ボブ = 50、キャロル = 100 |

 最終的な出力は次のとおりです。```
alice 50
bob 50
carol 100
```この例では、3 つの異なるフィルターを実行します。 の`WA`無視されると、`AC`未選択の場合`p3`は無視され、選択された問題の提出が成功すると、対応する候補が正確に更新されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(C + P + S) が期待されます | 各候補者、問題、提出物は 1 回処理され、予想される O(1) のハッシュ テーブル操作が行われます。 
| スペース | O(C + P) | 候補辞書、問題辞書、候補リスト、および回答配列はそれぞれ線形にスケールされます。 

最大入力には、3 つの主要セクション全体で 150,000 レコードのみが含まれます。 このアルゴリズムは送信ごとに一定数の辞書操作を実行するため、その予想実行時間は、総当たりアプローチで必要な 25 億回の操作と比較して、1 秒の制限に容易に適合します。 辞書と配列には、最大 100,000 の候補と選択された問題に比例する情報が保存され、256 MB 以内に収まります。 

## テストケース

 次のテスト ハーネスは同じものを実装します。`solve`関数構造を使用して、提供されたサンプルといくつかのカスタム ケースに対してソリューションを実行します。 最大サイズのケースはプログラムによって生成されるため、50,000 人の受験者、50,000 個の問題、および 50,000 件の提出物を実行しながら、テスト自体は読みやすいままになります。```python
import sys
import io

def solve():
    input = sys.stdin.readline

    C, P, S = map(int, input().split())

    candidates = []
    candidate_index = {}

    for i in range(C):
        handle = input().strip()
        candidates.append(handle)
        candidate_index[handle] = i

    problem_score = {}

    for _ in range(P):
        problem, score = input().split()
        problem_score[problem] = int(score)

    answer = [0] * C

    for _ in range(S):
        user, problem, verdict = input().split()

        if verdict != "AC":
            continue

        idx = candidate_index.get(user)
        if idx is None:
            continue

        score = problem_score.get(problem)
        if score is None:
            continue

        answer[idx] += score

    output = []
    for i in range(C):
        output.append(f"{candidates[i]} {answer[i]}")

    return "\n".join(output)

def run(inp: str) -> str:
    old_stdin = sys.stdin
    sys.stdin = io.StringIO(inp)
    try:
        return solve()
    finally:
        sys.stdin = old_stdin

# Provided sample.
sample1 = """\
2 2 4
GabrielPessoa
beza
metebronca 100
geometry 200
beza metebronca AC
ffern numbertheory AC
GabrielPessoa geometry WA
beza geometry AC
"""

assert run(sample1) == """\
GabrielPessoa 0
beza 300
""", "sample 1"

# Minimum-size case.
minimum = """\
1 1 1
a
p 1
a p AC
"""

assert run(minimum) == """\
a 1
""", "minimum-size case"

# All submissions are relevant, with several candidates solving
# the same selected problems.
all_equal = """\
3 2 4
a
b
c
p1 7
p2 7
a p1 AC
b p1 AC
b p2 AC
c p2 AC
"""

assert run(all_equal) == """\
a 7
b 14
c 7
""", "all-equal scores"

# Boundary behavior: WA, unknown user, and unselected problem
# must all be ignored.
filters = """\
2 1 5
alice
bob
selected 100
alice selected WA
alice other AC
unknown selected AC
bob selected AC
bob selected WA
"""

assert run(filters) == """\
alice 0
bob 100
""", "filtering irrelevant submissions"

# A candidate can have several wrong submissions before AC.
# The selected problem score must be added only for AC.
retries = """\
2 2 10
alice
bob
p1 10
p2 20
alice p1 WA
alice p1 CE
alice p1 AC
bob p1 WA
bob p2 AC
alice p2 AC
bob p3 AC
alice p2 WA
bob p1 AC
alice p1 WA
"""

assert run(retries) == """\
alice 30
bob 30
""", "multiple attempts and irrelevant problems"

# Maximum-size generated case.
C = 50000
P = 50000
S = 50000

parts = [f"{C} {P} {S}"]

for i in range(C):
    parts.append(f"u{i}")

for i in range(P):
    parts.append(f"p{i} 1")

# Each submission is a valid AC for a corresponding candidate
# and problem. Every candidate receives exactly one point.
for i in range(S):
    parts.append(f"u{i} p{i} AC")

maximum = "\n".join(parts) + "\n"

expected = "\n".join(f"u{i} 1" for i in range(C))

assert run(maximum) == expected, "maximum-size case"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 最小サイズのケース |`a 1`| 最小の有効な入力と成功した直接ルックアップ |
 | すべて等しいスコア |`a 7`、`b 14`、`c 7`| 複数の受験者が同じ問題を解き、スコア値を繰り返す |
 | フィルタリングケース |`alice 0`、`bob 100`| 不明なユーザー、選択されていない問題、および非`AC`判決 |
 | 複数回の試行のケース |`alice 30`、`bob 30`| 送信が成功するまでに数回送信が失敗しました |
 | 生成される最大サイズのケース | すべての候補者にはスコアがあります`1`| 3 つの主要な入力サイズすべての最大値とパフォーマンス |

 ## 特殊なケース

 アン`AC`候補に含まれていないユーザーからのメッセージは、候補検索の段階では無視されます。 例えば：```
1 1 1
alice
p1 100
bob p1 AC
```候補辞書には、`alice`。 いつ`bob`加工されて、`candidate_index.get("bob")`返品`None`, したがって、スコアは変わりません。 出力は次のとおりです。```
alice 0
```選択されていない問題への送信が成功した場合も、同様に処理されます。 考慮する：```
1 1 1
alice
p1 100
alice p2 AC
```候補者の検索は成功しますが、`problem_score.get("p2")`返品`None`なぜなら、ただ`p1`が選ばれました。 アルゴリズムは送信を破棄し、次のように出力します。```
alice 0
```ユーザーと問題の両方が有効な場合でも、失敗した送信は何も貢献してはなりません。 と：```
1 1 2
alice
p1 100
alice p1 WA
alice p1 AC
```最初の提出は評決チェックによって即座に拒否されます。 2 番目のチェックはすべてのチェックに合格し、100 を追加します。結果は次のようになります。```
alice 100
```いくつかの試行が失敗しても、特別な処理は行われません。 のために：```
1 1 3
alice
p1 50
alice p1 WA
alice p1 CE
alice p1 AC
```最初の 2 つのレコードはスコアを 0 のままにし、最後のレコードはスコアをゼロのままにします。`AC`これを 50 に変更します。出力は次のようになります。```
alice 50
```提出の順序は候補者の順序と一致する必要はありません。 候補者が次のとおりであるとします。```
2 1 2
alice
bob
p1 25
bob p1 AC
alice p1 AC
```最初に処理された送信では、インデックス 1 であるボブのエントリが更新されます。2 番目の送信では、インデックス 0 であるアリスのエントリが更新されます。回答配列には元の候補位置によってインデックスが付けられているため、最終的な出力は次のようになります。```
alice 25
bob 25
```問題は多くの異なる受験者によって解決される可能性があり、各受験者は個別にスコアを受け取る必要があります。 例えば：```
2 1 2
alice
bob
p1 100
alice p1 AC
bob p1 AC
```最初の送信後の回答配列は次のようになります。`[100, 0]`。 2番目以降は次のようになります`[100, 100]`。 問題がアリスによってすでに解決されているという事実は、ボブがその問題を利用できないわけではありません。保証は、異なるユーザーによる提出ではなく、同じユーザーによる繰り返しの提出に関係するためです。 

合計スコアが最大であっても、Python では整数オーバーフローの問題は発生しません。 受験者が選択した多くの問題を解決したとしても、Python の整数型は必要に応じて増加します。 このアルゴリズムは、固定幅の整数表現には決して依存しません。
