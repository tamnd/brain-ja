---
title: "CF 202A - LLPS"
description: "小文字の文字列が与えられているので、回文であり辞書編集的にできるだけ大きな部分列を形成するには、いくつかの文字を選択する必要があります。 サブシーケンスは連続している必要はありません。"
date: "2026-05-29T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "bitmasks", "brute-force", "greedy", "implementation", "strings"]
categories: ["algorithms"]
codeforces_contest: 202
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 127 (Div. 2)"
rating: 800
weight: 202
solve_time_s: 66
verified: true
draft: false
---

[CF 202A - LLPS](https://codeforces.com/problemset/problem/202/A)

 **評価:** 800
 **タグ:** バイナリ検索、ビットマスク、ブルート フォース、貪欲、実装、文字列
 **解決時間:** 1 分 6 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 小文字の文字列が与えられているので、回文であり辞書編集的にできるだけ大きな部分列を形成するには、いくつかの文字を選択する必要があります。 

サブシーケンスは連続している必要はありません。 文字をスキップすることはできますが、残りの文字は元の順序を維持する必要があります。 

文字列の長さは最大でも 10 であり、非常に小さいです。 ここでは指数関数的なアルゴリズムさえも技術的に可能です。 すべてのサブシーケンスに対する総当り検索では、最大でも次の内容が検査されます。$2^{10} = 1024$候補は制限内に簡単に収まります。 

この問題の興味深い部分は、パフォーマンスではなく、辞書編集順序の中に隠された構造を認識することです。 

よくある間違いは、回文が長いほど常に優れていると想定することです。 辞書編集的な順序はそのようには機能しません。 たとえば:

 入力:```
radar
```考えられる回文部分列には次のものがあります。`"radar"`そして`"rr"`。 

それでも`"radar"`より長い、`"rr"`最初の異なる文字は次のとおりであるため、辞書編集的には大きくなります。`'r'`対`'a'`、 そして`'r' > 'a'`。 

もう 1 つの簡単な間違いは、最初に最長の回文部分列を構築してから、辞書編集順を最大化しようとすることです。 これらは異なる目的です。 

たとえば:

 入力:```
abac
```最長の回文部分列は次のとおりです。`"aba"`ですが、正解は次のとおりです。```
c
```辞書順に、次で始まる任意の文字列`'c'`で始まるすべての文字列をビートします`'a'`。 

もう 1 つの微妙なケースは、最大の文字が複数回出現する場合に発生します。 

入力：```
azbz
```正しい答えは次のとおりです。```
zz
```不注意な解決策では、`"z"`ただし、最大文字が複数回出現する場合、それらをすべて取得すると自動的に回文が形成され、長い等接頭辞文字列の方が大きいため、辞書編集的により大きな結果が得られます。 

## アプローチ

 入力が非常に小さいため、総当たりアプローチは簡単です。 

ビットマスクを使用してすべてのサブシーケンスを生成できます。 サブシーケンスごとに、それが回文であるかどうかを確認します。 すべての回文部分列の中で、辞書編集的に最大のものを保持します。 

これが機能するのは、$2^n$後続。 と$n \le 10$、つまり候補者は最大 1024 人です。 費やしても$O(n)$各サブシーケンスの構築とチェックに時間を費やせば、合計の作業量はわずかにとどまります。 

この問題に対しては、ブルートフォース手法も十分に受け入れられます。 それでも、もっと単純な観察があります。 

文字列内に現れる最大の文字が次であると仮定します。`'z'`。 小さな文字で始まる回文は、次の文字で始まる回文に勝つことはできません。`'z'`、長さに関係なく。 

次に、この最大文字が出現するすべての文字を考慮します。 それらをすべて取得すると、結果の文字列は完全に同じ文字で構成され、自動的に回文になります。 

例えば：```
abzczz
```最大文字数は`'z'`、3回登場します。 続いて`"zzz"`は回文であり、より小さな文字で始まる回文は辞書編集的にこれに勝るものはありません。 

これで問題は完全に解決されます。 

答えは単純に、文字列内の最大文字がすべて出現することです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(2^n \cdot n) | O(n) | 承認済み |
 | 最適 | O(n) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 入力文字列を読み取ります。 
2. Python の関数を使用して文字列内の最大文字を見つけます。`max()`。 

この文字は、辞書編集上最大の回文の先頭に出現する必要があります。 開始文字が小さいと、辞書編集上の比較ではすぐに負けてしまいます。 
3. 文字列を再度スキャンし、その最大文字が出現するすべてを収集します。 

繰り返される 1 つの文字だけで構成される文字列は常に回文です。 
4. 結果の文字列を出力します。 

### なぜ効果があるのか

 文字列内の最大文字数を次のようにします。`c`。 

より小さい文字で始まる回文部分列。`c`は自動的に辞書編集上、次で始まる部分列よりも小さくなります。`c`。 

したがって、最適な答えは次のように始まる必要があります。`c`。 

最初の文字が固定されたら`c`のコピーをさらに追加します`c`結果が改善されるだけです。 2 つの文字列が同じプレフィックスを共有する場合、長い文字列の方が辞書編集的に大きくなります。 

すべてのコピー以来、`c`有効な回文を形成し、すべての出現を考慮すると、考えられる最大の答えが得られます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

s = input().strip()

mx = max(s)

answer = []

for ch in s:
    if ch == mx:
        answer.append(ch)

print("".join(answer))
```この解決策は、上で証明された観察に直接従うものです。`max(s)`finds the lexicographically largest character appearing in the string.

 The second loop gathers every occurrence of that character while preserving order. Since all selected characters are identical, the resulting subsequence is automatically a palindrome.

 One subtle point is that we must take all occurrences, not just one. If the maximum character appears multiple times, a longer string of the same repeated character is lexicographically larger.

 例えば：```
"zz" > "z"
```なぜなら`"z"`の接頭辞です`"zz"`。 

もう 1 つの詳細は、後続の順序の保持です。 左から右に反復すると、自然に元の順序が維持されます。 

## 実用的な例

 ### 例 1

 入力:```
radar
```| ポジション | キャラクター | 最大文字数 | 回答に追加 | 現在の回答 |
 | --- | --- | --- | --- | --- |
 | 0 | r | r | はい | r |
 | 1 | | r | いいえ | r |
 | 2 | d | r | いいえ | r |
 | 3 | | r | いいえ | r |
 | 4 | r | r | はい | RR |

 出力：```
rr
```この例は、辞書編集順が回文の長さに影響を与える理由を示しています。`"rr"`ビート`"radar"`最初の異なる文字の方が大きいためです。 

### 例 2

 入力:```
azbz
```| ポジション | キャラクター | 最大文字数 | 回答に追加 | 現在の回答 |
 | --- | --- | --- | --- | --- |
 | 0 | | z | いいえ | |
 | 1 | z | z | はい | z |
 | 2 | b | z | いいえ | z |
 | 3 | z | z | はい | ズズ |

 出力：```
zz
```このトレースは、最大文字のすべての出現を含める必要がある理由を示しています。`"zz"`辞書編集的にはより大きい`"z"`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | 最大の文字を見つけるのに 1 回のパス、それを収集するのに 1 回のパス |
 | スペース | O(n) | 回答には文字列 | のすべての文字が含まれる場合があります。 

と$n \le 10$、プログラムは即座に実行されます。 強引なソリューションでも簡単に適合しますが、貪欲な観察により、実装は数行に削減されます。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

def solve():
    input = sys.stdin.readline

    s = input().strip()

    mx = max(s)

    ans = []

    for ch in s:
        if ch == mx:
            ans.append(ch)

    print("".join(ans))

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
assert run("radar\n") == "rr", "sample 1"

# custom cases
assert run("a\n") == "a", "single character"
assert run("aaaaa\n") == "aaaaa", "all equal characters"
assert run("abac\n") == "c", "largest character appears once"
assert run("azbz\n") == "zz", "largest character appears multiple times"
assert run("zyxwvutsrq\n") == "z", "strictly decreasing characters"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`a`|`a`| 最小サイズの入力 |
 |`aaaaa`|`aaaaa`| すべての文字が等しい |
 |`abac`|`c`| 回文は長いほど良いとは限りません。 
|`azbz`|`zz`| 最大文字数をすべて指定する必要があります |
 |`zyxwvutsrq`|`z`| 最大文字は 1 回出現します |

 ## 特殊なケース

 次の入力を考えてみましょう。```
abac
```最大文字数は`'c'`。 アルゴリズムは文字列をスキャンし、`'c'`、生産:```
c
```最長回文アルゴリズムでは次のような結果が得られます。`"aba"`、辞書編集的に次のとおりなので、これは間違っています。```
c > aba
```アルゴリズムは最初の文字を何よりも優先するため、成功します。 

次に、次のことを考えてみましょう。```
zzza
```最大文字数は`'z'`。 アルゴリズムはすべてを収集します`'z'`そして出力:```
zzz
```最大 1 文字だけを返す不注意な実装では、`"z"`、これは小さいため、`"z"`の接頭辞です`"zzz"`。 

最後に、次のことを考慮してください。```
bacab
```最大文字数は`'c'`、1回登場。 アルゴリズムは次を出力します。```
c
```それでも`"bacab"`それ自体は回文であり、次から始まります`'b'`、したがって、辞書編集的には、で始まる回文に負けます。`'c'`。
