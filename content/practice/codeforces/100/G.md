---
title: "CF 100G - アルバムに名前を付けます"
description: "アリオは、新しいアルバムのタイトルを候補名のリストから選びたいと考えています。 いくつかの名前は以前にすでに使用されています。 彼の決定ルールには 2 つの層があります。 候補名がこれまでに使用されたことがない場合、それが可能な限り最良の選択です。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "*special", "data-structures", "implementation"]
categories: ["algorithms"]
codeforces_contest: 100
codeforces_index: "G"
codeforces_contest_name: "Unknown Language Round 3"
rating: 1800
weight: 100
solve_time_s: 110
verified: true
draft: false
---

[CF 100G - アルバムに名前を付けます](https://codeforces.com/problemset/problem/100/G)

 **評価:** 1800
 **タグ:** *特殊、データ構造、実装
 **解決時間:** 1 分 50 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 アリオは、新しいアルバムのタイトルを候補名のリストから選びたいと考えています。 いくつかの名前は以前にすでに使用されています。 彼の決定ルールには 2 つの層があります。 

候補名がこれまでに使用されたことがない場合、それが可能な限り最良の選択です。 未使用のすべての候補の中から、アルファベット順で最大のものを選択します。 

すべての候補が以前に使用されている場合、最後に使用されたのが最も古いものを選択します。 複数の名前が同じ最も古い年を共有する場合、彼は再びアルファベット順で最大の名前を選択します。 

入力により、出版年とともにアルバム名の履歴が得られ、その後、可能性のある新しい名前のリストが表示されます。 選択したタイトルを印刷する必要があります。 

制約は、解が線形に近いことを直ちに示唆します。 まであります$10^5$名前が使用されているため、すべての候補について履歴全体を繰り返しスキャンするとコストが高くなります。 二次方程式の解法は、$10^9$これは 2 秒の制限をはるかに超えています。 各名前に関連付けられた最新の年を高速に検索するだけでよいため、ここではハッシュ マップが自然なツールになります。 

微妙な点の 1 つは、名前が歴史の中で複数回出現する可能性があることです。 私たちは最初の年には興味がなく、最新の使用状況に興味があります。 次の入力を考えてみましょう。```
3
echo 1999
echo 2005
nova 2001
2
echo
nova
```正しい答えは、`nova`、 なぜなら`nova`最後に使用されたのは 2001 年でしたが、`echo`最後に使用されたのは 2005 年です。最新のものではなく最初に出現したものを保存する不注意な実装では、誤って選択されてしまいます。`echo`。 

もう 1 つの簡単な間違いは、タイをアルファベット順に誤って処理することです。 次のものがあるとします。```
2
alpha 2000
beta 2000
2
alpha
beta
```どちらの名前も同じくらい昔に使用されていたため、答えは次のようになります。`beta`アルファベット順に後なので。 最小年のみを追跡し、同点での更新を停止すると、間違った結果が返されます。 

未使用の名前も慎重に扱う必要があります。 これらは、年に関係なく、使用されている名前よりも常に優先されます。 例えば：```
1
dream 2010
2
dream
vision
```正しい答えは、`vision`、 それでも`dream`有効な古い年があります。 未使用の名前を年付きとして扱う`0`概念的には機能しますが、実装が使用されている名前よりも明示的にそれらを優先する場合に限ります。 

## アプローチ

 直接的なブルートフォース ソリューションでは、履歴全体をスキャンしてすべての候補名を処理し、以前に出現したかどうか、および最新の年は何年かを判断します。 各候補者について、以前に使用されたすべての名前と比較し、その候補者について確認された最大の年を保持します。 

これは、決定ルールに必要な情報を正確に計算するため、論理的に機能します。 問題はコストです。 と$10^5$歴史的なエントリーと$10^4$候補者にとって最悪のケースは、$10^9$比較。 Python では制限時間内にその量の作業を処理することはできません。 

問題の構造により、より迅速な方向性が得られます。 すべてのクエリは同じことを尋ねます。「この名前に関連付けられた最新の年は何ですか?」 繰り返し再計算するのは無駄です。 代わりに、履歴を一度前処理して、名前から最新の年までのハッシュ マップを作成します。 

履歴を読みながら、以下を更新します。```
latest[name] = max(latest[name], year)
```前処理後、各候補は一定の予想時間内で評価できます。 

判定ルール自体も簡素化することができる。 未使用の名前は、使用されている名前よりも常に優れています。 未使用の名前のうち、アルファベット順で最大のものを選択します。 未使用の名前が存在しない場合は、アルファベット順で大きい順に並べて、最新の年が最も小さい使用済みの名前を選択します。 

これにより、問題全体が、単純な比較による候補の 1 回のパスに変わります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(n \cdot m)$|$O(1)$| 遅すぎる |
 | 最適 |$O(n + m)$|$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. という辞書を作成します。`latest`。 

この辞書には、各アルバム名が使用された最新の年が保存されます。 
2. 歴史的なアルバム名を 1 つずつ読みます。 

それぞれについて`(name, year)`ペアリング、更新:```
latest[name] = max(existing_year, year)
```この問題では最初の使用法ではなく、最新の使用法が考慮されるため、最大年を保持します。 
3. 2 つの追跡変数を初期化します。 

1 つの変数には、これまでに見つかった未使用の最良の候補が格納されます。 

もう 1 つは、最もよく使用された候補を最新の使用年とともに保存します。 
4. すべての候補名を処理します。 

名前が存在しない場合は、`latest`、未使用です。 それを現在最も未使用の候補と比較し、アルファベット順で大きい方を保持します。 
5. 候補が以前に使用されたことがある場合は、その最新の年を辞書から取得します。 

現在最もよく使用されている候補と比較してください。 

候補者は次の場合に適しています。 

- 最新の年は小さいので、より前に使用されたことを意味します
 - または、年が同じで、名前のアルファベット順が大きいです。 
6. すべての候補が処理された後、未使用の候補が存在するかどうかを確認します。 

「はい」の場合、未使用の名前が常に優先されるため、未使用の最適な候補が出力されます。 

それ以外の場合は、最もよく使用される候補を出力します。 

### なぜ効果があるのか

 辞書の不変条件は、前処理の後、`latest[name]`は、その名前が歴史に登場した最新の年に相当します。 これまでに確認された最大の年を常に保持しているため、すべての更新でこれが保持されます。 

候補の処理中、未使用トラッカーは常に、これまでに検出されたアルファベット順で最大の未使用名を保存します。 使用されるトラッカーには、常に次の順序に従って最適な候補が保存されます。 

1. 最新の年は小さいほど良い。 
2. 年が等しい場合は、アルファベット順に大きい方が良いです。 

すべての候補はまさにこれらのルールに基づいて現在の最適値と比較されるため、最終的に保存された答えは全体的に最適になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())

    latest = {}

    for _ in range(n):
        name, year = input().split()
        year = int(year)

        if name not in latest or year > latest[name]:
            latest[name] = year

    m = int(input())

    best_unused = None

    best_used_name = None
    best_used_year = float('inf')

    for _ in range(m):
        name = input().strip()

        if name not in latest:
            if best_unused is None or name > best_unused:
                best_unused = name
        else:
            year = latest[name]

            if (year < best_used_year or
               (year == best_used_year and name > best_used_name)):
                best_used_year = year
                best_used_name = name

    if best_unused is not None:
        print(best_unused)
    else:
        print(best_used_name)

if __name__ == "__main__":
    solve()
```最初のセクションでは、`latest`辞書。 重要な点は、名前の繰り返しに最大年を使用することです。 最新の使用法を重視するため、これを忘れると問題の意味が完全に変わってしまいます。 

2 番目のセクションでは、候補リストを 1 回スキャンします。 未使用の名前が常に使用済みの名前よりも優先されるため、コードは未使用の候補と使用済みの候補を分離します。 これにより、未使用の名前 year を割り当てるような厄介な人工的な値を回避できます。`-1`。 

タイブレーク ロジックは明示的に記述されます。```
year < best_used_year
```これは、その名前が以前に使用されていたことを意味し、その方が良いと考えられます。```
year == best_used_year and name > best_used_name
```アルファベット順のタイブレークを実装します。 使用する`>`Python は文字列を辞書順に比較するため、機能します。 

による初期化`float('inf')`最初に使用された候補が常に最初のプレースホルダーを置き換えることを保証します。 

## 実用的な例

 ### サンプル 1

 入力:```
3
eyesonme 2008
anewdayhascome 2002
oneheart 2003
2
oneheart
bienbien
```処理履歴:

 | 名前 | 年 | 更新後の最新 |
 | --- | --- | --- |
 | めいそん 2008年 | {アイソンメ: 2008} |
 | 新たな日が来た | 2002年 | {eyesonme: 2008、新たな日が来た: 2002} |
 | ワンハート | 2003年 | {eyesonme: 2008、anewdayhascome: 2002、oneheart: 2003} |

 処理候補:

 | 候補者 | 以前に使用 | アクション | 現在のベスト |
 | --- | --- | --- | --- |
 | ワンハート | はい | 最もよく使われる = ワンハート | 中古: ワンハート |
 | ビエンビエン | いいえ | 最高の未使用 = ビアンビアン | 未使用: ビアンビアン |

 アルゴリズムは出力します`bienbien`未使用の名前は、使用されているすべての名前よりもランクが高いためです。 

### カスタム例

 入力:```
4
alpha 2005
beta 2000
gamma 2000
alpha 2010
3
alpha
beta
gamma
```処理履歴:

 | 名前 | 年 | 更新後の最新 |
 | --- | --- | --- |
 | アルファ | 2005年 | {アルファ: 2005} |
 | ベータ | 2000年 | {アルファ: 2005、ベータ: 2000} |
 | ガンマ | 2000年 | {アルファ: 2005、ベータ: 2000、ガンマ: 2000} |
 | アルファ | 2010年 | {アルファ: 2010、ベータ: 2000、ガンマ: 2000} |

 処理候補:

 | 候補者 | 最新年 | 比較結果 | 現在のベスト中古 |
 | --- | --- | --- | --- |
 | アルファ | 2010年 | 第一候補 | アルファ |
 | ベータ | 2000年 | 2010 年より古い | ベータ |
 | ガンマ | 2000年 | 同じ年、アルファベット順に大きい | ガンマ |

 アルゴリズムの出力`gamma`。 

このトレースは、両方の重要な詳細を示しています。名前が繰り返される場合は最新の年を保持する必要があり、同じ年にはアルファベットでの比較が必要です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n + m)$| 各履歴エントリと各候補は 1 回処理されます。 
| スペース |$O(n)$| ディクショナリには、個別の使用名ごとに最大 1 つのエントリが保存されます。 

最大の入力サイズはこれらの範囲内に快適に収まります。 その周り$10^5$Python では辞書操作は簡単で、名前が短いため、メモリ使用量は 64 MB の制限を大幅に下回ります。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def solve():
    input = sys.stdin.readline

    n = int(input())

    latest = {}

    for _ in range(n):
        name, year = input().split()
        year = int(year)

        if name not in latest or year > latest[name]:
            latest[name] = year

    m = int(input())

    best_unused = None

    best_used_name = None
    best_used_year = float('inf')

    for _ in range(m):
        name = input().strip()

        if name not in latest:
            if best_unused is None or name > best_unused:
                best_unused = name
        else:
            year = latest[name]

            if (year < best_used_year or
               (year == best_used_year and name > best_used_name)):
                best_used_year = year
                best_used_name = name

    if best_unused is not None:
        print(best_unused)
    else:
        print(best_used_name)

def run(inp: str) -> str:
    backup_stdin = sys.stdin
    backup_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    solve()

    out = sys.stdout.getvalue().strip()

    sys.stdin = backup_stdin
    sys.stdout = backup_stdout

    return out

# provided sample
assert run(
"""3
eyesonme 2008
anewdayhascome 2002
oneheart 2003
2
oneheart
bienbien
"""
) == "bienbien", "sample 1"

# minimum input
assert run(
"""0
1
solo
"""
) == "solo", "single unused name"

# repeated historical names
assert run(
"""3
echo 1999
echo 2005
nova 2001
2
echo
nova
"""
) == "nova", "must keep latest year"

# tie on year, alphabetical rule
assert run(
"""2
alpha 2000
beta 2000
2
alpha
beta
"""
) == "beta", "alphabetical tie break"

# multiple unused names
assert run(
"""1
dream 2010
3
vision
future
galaxy
"""
) == "vision", "largest alphabetical unused name"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 歴史的な名前はありません |`solo`| すべての候補が未使用の場合の正しい処理 |
 | 歴史的な名前の繰り返し |`nova`| 最新の年はそれ以前の年を上書きする必要があります。 
| 同じ年 |`beta`| 使用されている名前間のアルファベット順のタイブレーク |
 | 複数の未使用の名前 |`vision`| アルファベット順で最大の未使用候補が勝ち |

 ## 特殊なケース

 歴史の中で繰り返されるアルバム名を考えてみましょう。```
3
echo 1999
echo 2005
nova 2001
2
echo
nova
```辞書は次のように進化します。```
echo -> 1999
echo -> 2005
nova -> 2001
```候補者にチェックを入れると、`echo`最新の年があります`2005`その間`nova`もっている`2001`。 以来`2001`が古い場合、答えは次のようになります`nova`。 アルゴリズムは常に各名前の最大年を保存するため、これを正しく処理します。 

次に、等しい年を考えてみましょう。```
2
alpha 2000
beta 2000
2
alpha
beta
```どちらの候補も、最新の使用年が同じです。 アルゴリズムはタイブレーク条件に達します。```
year == best_used_year and name > best_used_name
```以来`"beta" > "alpha"`辞書編集的には、答えは次のようになります`beta`。 

最後に、使用されている名前と未使用の名前の間の相互作用を考えてみましょう。```
1
dream 2010
2
dream
vision
```

`dream`現在最もよく使用される候補になります。 それから`vision`未使用として識別されるため、別の場所に保管されます。`best_unused`。 最後に、アルゴリズムは、未使用の候補が存在する場合は常にそれを出力するため、出力は次のようになります。`vision`。
