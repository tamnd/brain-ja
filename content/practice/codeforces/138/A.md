---
title: "CF 138A - 文学のレッスン"
description: "それぞれの詩は 4 行のグループである quatrains に分かれています。 2 つの行は、最後から k 番目の母音から始まる接尾語が両方の行で同じである場合に韻を踏みます。 たとえば、k = 1 の場合、最後の母音から始まる接尾辞を比較します。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 138
codeforces_index: "A"
codeforces_contest_name: "Codeforces Beta Round 99 (Div. 1)"
rating: 1600
weight: 138
solve_time_s: 120
verified: true
draft: false
---

[CF 138A - 文学レッスン](https://codeforces.com/problemset/problem/138/A)

 **評価:** 1600
 **タグ:** 実装
 **解決時間:** 2 分
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 それぞれの詩は 4 行のグループである quatrains に分かれています。 2 つの行は、最後から k 番目の母音から始まる接尾語が両方の行で同じである場合に韻を踏みます。 たとえば、`k = 1`、最後の母音から始まる接尾辞を比較します。 と`k = 2`では、最後から 2 番目の母音から始まる接尾辞を比較します。 

すべての四行詩について、考えられる 4 つの韻構造のみを考慮します。 

-`aabb`、線を意味します`(1,2)`韻を踏んで`(3,4)`韻を踏む
 -`abab`、 意味`(1,3)`韻を踏んで`(2,4)`韻を踏む
 -`abba`、 意味`(1,4)`韻を踏んで`(2,3)`韻を踏む
 -`aaaa`、4行すべてが韻を踏むことを意味します

 すべての四行詩が同じ構造と互換性がある場合、詩全体は有効なスキームを持ちます。 4 つの行すべてが韻を踏んでいる四行詩は、あらゆるスキームを表すことができるため、柔軟性があります。 

入力により得られるのは、`n`四行詩とその価値観`k`。 それなら来てください`4n`テキストの行。 有効なスキームの 1 つを出力するか、`"NO"`共通のスキームが存在しない場合。 

制約は十分に小さいため、高度な文字列アルゴリズムは必要ありません。 多くてもあります`2500`四行詩なので、せいぜい`10000`線。 すべての文字列の合計の長さは`10^4`つまり、すべての文字を数回スキャンしても低コストです。 アン`O(total_length)`または`O(total_length + n)`ソリューションは簡単に十分に高速です。 

難しいのはパフォーマンスではなく、韻のルールを正しく処理することです。 

よくある間違いの 1 つは、以下の行を扱うことです。`k`母音は互いに一致します。 別の短いセリフも含めて、決して韻を踏むことはありません。 

例えば：```
1 2
sky
try
day
may
```最初の 2 行にはそれぞれ母音が 1 つしか含まれていないため、韻を踏むことができません。`k = 2`。 正しい出力は次のとおりです。```
NO
```もう一つの簡単なバグは、それを忘れることです`aaaa`quatrain はあらゆるスキームと互換性があります。 

例：```
2 1
day
may
sun
fun
code
mode
road
toad
```最初の四行詩は、`aabb`。 2つ目は`aaaa`。 詩全体は今でも有効です`aabb`。 

3 番目の微妙なケースは、複数のスキームが個別に適合するが、単一のスキームがグローバルに機能しない場合に発生します。 

例：```
2 1
day
may
sun
fun
red
bed
red
bed
```最初の四行詩はただ`aabb`。 2つ目は`abab`そして`abba`。 すべての四行詩に共通のスキームはないため、答えは次のようになります。```
NO
```## アプローチ

 ブルートフォースソリューションでは、2 つの行が韻を踏んでいるかどうかをテストする必要がある場合は常に、行を 1 文字ずつ直接比較します。 比較するたびに、逆方向にスキャンして k 番目の母音を見つけ、接尾辞を抽出し、それを別の接尾辞と比較します。 

これは、合計入力サイズが小さいため機能します。 四行連ごとに複数の比較を実行したとしても、処理されるテキストの総量はまだ管理可能な範囲に収まります。 

ブルートフォースバージョンの弱点は反復です。 同じ行が複数の韻チェックに参加する可能性があり、韻接尾辞を繰り返し再計算する必要があります。 

重要な観察は、行を単一の正規表現、つまり末尾から k 番目の母音から始まる接尾辞に縮小できることです。 この接尾辞を一度計算すると、韻チェックは単純な等価性テストになります。 

例えば：```
commit -> ommit   (k = 2)
hermit -> ermit
```韻を踏むことは次のようになります。```
suffix1 == suffix2
```行の内容が以下の場合`k`母音に無効なマーカーを割り当てると、すべての韻テストに自動的に失敗します。 

各四行詩には 4 行しかないため、テストする意味のある組み合わせパターンは 3 つだけです。 Quatrain がどのスキームをサポートするかを決定した後、それらの可能性を有効なスキームのグローバル セットと交差させます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n × 平均行長 × 繰り返しチェック数) | お(1) | 承認済み |
 | 最適 | O(合計長 + n) | O(全長) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべての入力行を読み取ります。 
2. すべての行について、韻の接尾辞を計算します。 

母音を数えながら、文字列を右から左にスキャンします。 k 番目の母音に到達したら、そこから始まる部分文字列を保存します。 

見つかる前にスキャンが終了した場合`k`母音の場合は、その行を無効としてマークします。 
3. 各四行詩を個別に処理します。 

処理された 4 つの接尾辞を次のようにします。`a, b, c, d`。 
4. この四行詩がサポートする韻体系を決定します。 

2 行間の韻は、両方の接尾辞が有効で等しい場合にのみ存在します。 
5. まず四行韻を確認します。 

4 つの接尾辞がすべて等しく有効な場合、この四行列はサポートします。`aaaa`。 
6. 残りのパターンを確認します。`aabb`必要：```
a == b and c == d
```

`abab`必要：```
a == c and b == d
```

`abba`必要：```
a == d and b == c
```7. まだ可能性のある詩のスキームの世界的なセットを維持します。 

最初に:```
{aabb, abab, abba}
```四行詩がサポート`aaaa`何も制限しません。 

それ以外の場合は、このクアトレインでサポートされているスキームを使用してグローバル セットを交差させます。 
8. グローバル セットが空になった場合は、次のように出力します。`"NO"`。 
9. それ以外の場合は、次のように出力します。 

-`"aaaa"`すべての四行詩が完全に韻を踏んでいたら
 - それ以外の残りのスキーム

 ### なぜ効果があるのか

 各行は、問題のルールに基づいて韻を定義する正確な部分文字列に変換されます。 2 つの行は、これらの処理された接尾辞が等しく有効である場合にのみ韻を踏みます。 

すべての四行詩について、すべての法的な韻構造を明示的にテストします。 スキームは、すべての quatrain がそのスキームと互換性がある場合にのみグローバルに存続します。 このアルゴリズムは、真に有効なスキームを削除したり、無効なスキームを受け入れたりすることは決してないため、すべての交差の後に残ったセットは、まさに有効な詩スキームのセットになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

VOWELS = set("aeiou")

def get_suffix(s, k):
    cnt = 0

    for i in range(len(s) - 1, -1, -1):
        if s[i] in VOWELS:
            cnt += 1

        if cnt == k:
            return s[i:]

    return None

def solve():
    n, k = map(int, input().split())

    schemes = {"aabb", "abab", "abba"}
    all_aaaa = True

    for _ in range(n):
        lines = [input().strip() for _ in range(4)]
        suf = [get_suffix(x, k) for x in lines]

        possible = set()

        if (
            suf[0] is not None and
            suf[0] == suf[1] == suf[2] == suf[3]
        ):
            possible.add("aaaa")

        if (
            suf[0] is not None and
            suf[0] == suf[1] and
            suf[2] is not None and
            suf[2] == suf[3]
        ):
            possible.add("aabb")

        if (
            suf[0] is not None and
            suf[0] == suf[2] and
            suf[1] is not None and
            suf[1] == suf[3]
        ):
            possible.add("abab")

        if (
            suf[0] is not None and
            suf[0] == suf[3] and
            suf[1] is not None and
            suf[1] == suf[2]
        ):
            possible.add("abba")

        if "aaaa" not in possible:
            all_aaaa = False
            schemes &= possible

    if all_aaaa:
        print("aaaa")
    elif schemes:
        print(sorted(schemes)[0])
    else:
        print("NO")

solve()
```ヘルパー関数`get_suffix`韻定義を直接実装します。 k 番目の母音に到達するまで逆方向にスキャンします。 戻る`None`空の文字列は誤って等しいと比較される可能性があるため、無効な行の場合は空の文字列を返すより安全です。 

四行詩のロジックは、各韻体系の数学的定義を反映しています。 すべての比較では、参加しているサフィックスが有効であるかどうかが明示的にチェックされます。 

の`all_aaaa`flag は、すべての四行詩が完全に韻を踏んでいる場合、答えは次のようにする必要があるという特別なルールを処理します。`"aaaa"`任意の互換性のあるスキームではなく。 

グローバル交差ステップが中心的なアイデアです。 各四行詩は、許可される詩スキームのセットを制限します。 スキームが 1 つの四行詩で失敗すると、後で回復することはできません。 

## 実用的な例

 ### 例 1

 入力:```
1 1
day
may
sun
fun
```処理されたサフィックス:

 | ライン | サフィックス |
 | --- | --- |
 | 日 | そうだ |
 | 5月 | そうだ |
 | 太陽 | 国連 |
 | 楽しい | 国連 |

 スキームチェック:

 | スキーム | 有効 |
 | --- | --- |
 | ああ | いいえ |
 | あーぶ | はい |
 | アバブ | いいえ |
 | アバ | いいえ |

 最終的な答え:```
aabb
```このトレースは、最も単純で自明ではないケースを示しています。 組み合わせパターンは 1 つだけなので、詩のスキームは一意に決まります。 

### 例 2

 入力:```
2 1
code
mode
road
toad
red
bed
red
bed
```最初の四行詩:

 | ライン | サフィックス |
 | --- | --- |
 | コード | 頌歌 |
 | モード | 頌歌 |
 | 道路 | ロード |
 | ヒキガエル ロード |

 有効なスキーム:

 | スキーム | 有効 |
 | --- | --- |
 | ああ | いいえ |
 | あーぶ | はい |
 | アバブ | いいえ |
 | アバ | いいえ |

 グローバルセットは次のようになります。```
{aabb}
```2番目の四行詩:

 | ライン | サフィックス |
 | --- | --- |
 | 赤 | 編 |
 | ベッド | 編 |
 | 赤 | 編 |
 | ベッド | 編 |

 有効なスキーム:

 | スキーム | 有効 |
 | --- | --- |
 | ああ | はい |
 | あーぶ | はい |
 | アバブ | はい |
 | アバ | はい |

 2 番目の四行詩には制限がありません。`aaaa`有効です。 

最終的な答え:```
aabb
```これはその理由を示しています`aaaa`四行詩は特別に扱われなければなりません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(合計長 + n) | サフィックスの抽出中に各文字が 1 回スキャンされます。 
| スペース | O(全長) | 保存されたサフィックス文字列がメモリ使用量の大半を占めます。 

合計入力サイズは`10^4`したがって、このソリューションは制限内に簡単に収まります。 この規模では、Python の文字列スライスも完全に安全です。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

VOWELS = set("aeiou")

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    def get_suffix(s, k):
        cnt = 0

        for i in range(len(s) - 1, -1, -1):
            if s[i] in VOWELS:
                cnt += 1

            if cnt == k:
                return s[i:]

        return None

    n, k = map(int, input().split())

    schemes = {"aabb", "abab", "abba"}
    all_aaaa = True

    for _ in range(n):
        lines = [input().strip() for _ in range(4)]
        suf = [get_suffix(x, k) for x in lines]

        possible = set()

        if (
            suf[0] is not None and
            suf[0] == suf[1] == suf[2] == suf[3]
        ):
            possible.add("aaaa")

        if (
            suf[0] is not None and
            suf[0] == suf[1] and
            suf[2] is not None and
            suf[2] == suf[3]
        ):
            possible.add("aabb")

        if (
            suf[0] is not None and
            suf[0] == suf[2] and
            suf[1] is not None and
            suf[1] == suf[3]
        ):
            possible.add("abab")

        if (
            suf[0] is not None and
            suf[0] == suf[3] and
            suf[1] is not None and
            suf[1] == suf[2]
        ):
            possible.add("abba")

        if "aaaa" not in possible:
            all_aaaa = False
            schemes &= possible

    if all_aaaa:
        return "aaaa"
    elif schemes:
        return sorted(schemes)[0]
    else:
        return "NO"

# provided sample
assert run(
"""1 1
day
may
sun
fun
"""
) == "aabb"

# minimum-size input with full rhyme
assert run(
"""1 1
cat
hat
mat
bat
"""
) == "aaaa"

# insufficient vowels
assert run(
"""1 2
sky
try
day
may
"""
) == "NO"

# conflicting schemes
assert run(
"""2 1
day
may
sun
fun
red
bed
red
bed
"""
) == "aabb"

# only abab works
assert run(
"""1 1
light
stone
bright
phone
"""
) == "abab"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | すべての韻を備えた単一の四行詩 | ああ | 普遍的な韻の特殊な取り扱い |
 | 母音が少なすぎる行 | いいえ | 無効な行は韻を踏みません。 
| 互換性のある混合四行詩 | あーぶ | グローバル交差ロジック |
 | クロスペアリングのみ | アバブ | 正しいスキーム検出 |
 | 最小の有効な入力 | ああ | 境界サイズの処理 |

 ## 特殊なケース

 行に十分な母音が含まれていない場合を考えてみましょう。 

入力：```
1 2
sky
try
day
may
```アルゴリズムは以下を計算します。 

| ライン | サフィックス |
 | --- | --- |
 | 空 | なし |
 | 試してみてください | なし |
 | 日 | そうだ |
 | 5月 | そうだ |

 すべてのスキームには、無効なサフィックスを含む少なくとも 1 つの比較が必要であるため、スキームは追加されません。`possible`。 最終的な答えはこうなります`"NO"`。 

次に、完全に韻を踏んだ四行詩について考えてみましょう。 

入力：```
1 1
red
bed
fed
led
```すべての接尾辞は次のようになります`"ed"`。 

アルゴリズムは、以下を含む 4 つのスキームすべてを有効としてマークします。`aaaa`。 すべての四行詩は満たすので、`aaaa`、最終的な出力は正確に次のようになります。```
aaaa
```印刷するため、これは重要です。`"aabb"`技術的には韻パターンを説明できますが、この問題では明示的に韻を踏む必要があります。`"aaaa"`すべての四行詩が完全に韻を踏むとき。 

最後に、互換性のない四行詩について考えてみましょう。 

入力：```
2 1
day
may
sun
fun
cat
dog
cat
dog
```最初の四行連はのみをサポートします`aabb`。 

2 番目はのみをサポートします`abab`。 

交差点後:```
{aabb} ∩ {abab} = empty
```アルゴリズムは、グローバル スキームが存在しないことを即座に検出し、次のように出力します。```
NO
```
