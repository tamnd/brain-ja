---
title: "CF 53A - オートコンプリート"
description: "簡素化されたオートコンプリート機能を実装するように求められます。 入力は、ユーザーがこれまでに入力したテキストを表す文字列 s と、その後に以前にアクセスしたページのリストで構成されます。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 53
codeforces_index: "A"
codeforces_contest_name: "Codeforces Beta Round 49 (Div. 2)"
rating: 1100
weight: 53
solve_time_s: 208
verified: true
draft: false
---

[CF 53A - オートコンプリート](https://codeforces.com/problemset/problem/53/A)

 **評価:** 1100
 **タグ:** 実装
 **Solve time:** 3m 28s
 **確認済み:** はい

 ## Solution
 ## Problem Understanding

 簡素化されたオートコンプリート機能を実装するように求められます。 入力は文字列で構成されます`s`、ユーザーがこれまでに入力したテキストを表し、その後に以前にアクセスしたページのリストが続きます。 目標は延長することです`s`訪問済みリストから完全なページ URL に変換します。複数のページが次で始まる場合は、辞書順に最小のオプションを選択します。`s`。 で始まるページがない場合は、`s`、戻ります`s`変わらない。 

入力境界は小さく、訪問ページ数は最大 100、各長さは最大 100 です。これは、最悪の場合の文字比較の総数はおよそ 100 * 100 = 10,000 であり、2 秒の制限時間としては無視できる程度であるため、プレフィックスを比較する単純なリニア スキャンでも制限時間内で快適に実行されることを意味します。 

明らかではないエッジケースには、次のような状況が含まれます。`s`すでにリスト内の一部のページと等しい場合、または複数のページが`s`接頭辞として使用され、辞書編集上最小となるのは 1 つだけです。 たとえば、次の場合`s = "a"`訪問したページは`["apple", "apricot", "banana"]`、正しい出力は次のとおりです。`"apple"`、 ない`"apricot"`。 で始まるページがない場合にも、微妙なケースが発生します。`s`、例えば、`s = "xyz"`そしてページは`["abc", "def"]`; 出力は次のようにする必要があります`"xyz"`。 

## アプローチ

 ブルートフォース手法は簡単です。訪問したすべてのページを反復処理し、各ページが次で始まっているかどうかを確認します。`s`。 一致するものをすべて収集し、辞書順に最小のものを選択します。 ページ数が少ないのでこれで十分です。 プレフィックスのチェックは、Python の組み込みメソッドを使用して実行できます。`startswith`方法。 最悪の場合、すべてのページのすべての文字を次のものと比較します。`s`、おおよそ次のようになります`n * len(page)`比較ですが、ここでは許容されます。 

最適なアプローチは同じリニア スキャンに依存しますが、不必要なストレージを回避します。 一致するすべてのページを収集する代わりに、現在の最適な候補の変数を維持します。 で始まる各ページについて`s`、前の候補よりも辞書順に小さい場合、この候補を更新します。 これにより、スペースの複雑さが軽減され、ロジックが簡素化されます。 問題の制約は十分に小さいため、try のような高度なデータ構造は必要ありません。 

すべてのページを反復処理することですべての候補を確実に見つけることができるため、総当り攻撃が機能します。 最適化によりメモリ使用量が削減され、明瞭さが向上しますが、基本的な時間計算量はすべてのページの合計サイズにおいて線形のままです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * m) | O(n * m) | 承認済み |
 | 最適化されたリニアスキャン | O(n * m) | お(1) | 承認済み |

 ここ`n`はページ数であり、`m`ページの平均的な長さです。 

## アルゴリズムのチュートリアル

 1. 入力文字列を読み取る`s`そして訪問したページ数`n`。 最初に入力を読み取ることで、どのプレフィックスと一致するか、および候補がいくつ存在するかを確実に知ることができます。 
2. 変数を初期化する`best_candidate`として`None`。 これは、現在の辞書編集上最小のページを保持します。`s`。 
3. 以下のリストを繰り返し処理します。`n`ページ。 各ページで、次で始まるかどうかを確認します。`s`を使用して`startswith`方法。 このステップでは、オートコンプリートの有効な候補を特定します。 
4. ページが次で始まる場合`s`そして`best_candidate`は`None`、このページをに割り当てます`best_candidate`。 これにより、一致するページがあれば常に少なくとも 1 つの候補が存在することが保証されます。 
5. ページが次で始まる場合`s`そして`best_candidate`ではありません`None`とページを比較します。`best_candidate`辞書順に。 小さい場合は更新してください`best_candidate`。 このステップでは、次の不変条件が維持されます。`best_candidate`は常にこれまでの最小の一致ページです。 
6. すべてのページを繰り返し処理した後、次のことを確認します。`best_candidate`まだです`None`。 そうであれば、一致するページは存在しないため、印刷します。`s`変わらない。 それ以外の場合は、印刷します`best_candidate`。 

機能する理由: アルゴリズムは、どの時点でも不変条件を維持します。`best_candidate`で始まる、辞書編集上最小のページです。`s`。 すべてのページを繰り返すことで、どの候補も見逃さないことが保証されるため、最後までに、`best_candidate`一致するページがあれば、正解が含まれます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

s = input().strip()
n = int(input())
best_candidate = None

for _ in range(n):
    page = input().strip()
    if page.startswith(s):
        if best_candidate is None or page < best_candidate:
            best_candidate = page

print(best_candidate if best_candidate is not None else s)
```コードはまず、入力された文字列を読み取ります。`s`そして訪問したページ数。 各ページが読み取られ、空白が削除されます。 の`startswith`このメソッドは一致を効率的に識別します。 比較`page < best_candidate`辞書編集的に最小のページが選択されるようにします。 使用する`None`最初の候補の場合、実際のページと誤って比較される可能性のある値に事前に設定されることを回避します。 

## 実用的な例

 サンプル 1 の使用:

 | ステップ | ページ | で始まる`s`? | 最適な候補者 | 理由 |
 | --- | --- | --- | --- | --- |
 | 1 | 次の順列 | はい | 次の順列 | 最初の試合 |
 | 2 | 次の要素 | はい | 次の要素 | 前の候補よりも小さい |

 出力：`nextelement`。 これは、アルゴリズムが辞書編集上の最小の一致を正しく選択していることを示しています。 

別の例:

 入力:```
abc
3
abcd
abce
abd
```| ステップ | ページ | で始まる`s`? | 最適な候補者 | 理由 |
 | --- | --- | --- | --- | --- |
 | 1 | ABCD | はい | ABCD | 最初の試合 |
 | 2 | アブセ | はい | ABCD | abce > abcd、変化なし |
 | 3 | アブド | はい | ABCD | abd > abcd、変化なし |

 出力：`abcd`。 このアルゴリズムは、反復を通じて最小のプレフィックス一致を保持します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n * m) | n ページのそれぞれが、長さ m までの文字ごとにプレフィックス マッチングについてチェックされます。 
| スペース | お(1) | 入力サイズに関係なく、単一の文字列変数のみが維持されます。 

n ≤ 100 および m ≤ 100 の場合、アルゴリズムは最大 10,000 文字の比較を実行しますが、これは 2 秒の制限内に十分収まります。 メモリ使用量は最小限です。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    s = input().strip()
    n = int(input())
    best_candidate = None
    for _ in range(n):
        page = input().strip()
        if page.startswith(s):
            if best_candidate is None or page < best_candidate:
                best_candidate = page
    return best_candidate if best_candidate is not None else s

# provided sample
assert run("next\n2\nnextpermutation\nnextelement\n") == "nextelement", "sample 1"

# s matches exactly one page
assert run("abc\n3\nabcd\nabce\nabd\n") == "abcd", "smallest lex first"

# s matches no page
assert run("xyz\n3\nabc\ndef\nghi\n") == "xyz", "no match"

# multiple identical pages
assert run("go\n4\ngo\ngo\ngone\ngood\n") == "go", "multiple identical pages"

# s equals a page exactly
assert run("home\n2\nhome\nhomepage\n") == "home", "exact match"

# minimum input size
assert run("a\n1\na\n") == "a", "single page"

# maximum page length
assert run("longprefix\n2\nlongprefixaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa\nlongprefixbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb\n") == "longprefixaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa", "max length comparison"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`abc\n3\nabcd\nabce\nabd\n`|`abcd`| 辞書編集上の最小の接頭辞 |
 |`xyz\n3\nabc\ndef\nghi\n`|`xyz`| 一致するケースがありません |
 |`go\n4\ngo\ngo\ngone\ngood\n`|`go`| 複数の同一のページ |
 |`home\n2\nhome\nhomepage\n`|`home`| 完全一致の処理 |
 |`a\n1\na\n`|`a`| 最小サイズの入力 |
 | 長いプレフィックス | 最初の最小レックス | 最大長の処理 |

 ## 特殊なケース

 いつ`s`訪問したページに一致しません。`s = "xyz"`そしてページ`["abc", "def"]`、アルゴリズムは決して更新されません`best_candidate`、そのままにしておきます`None`。 最終チェックの印刷`s`、このシナリオを正しく処理します。 

複数のページが次から始まる場合`s`と が同一の場合、最初に一致するものがセットされます`best_candidate`、その後の同一のページによって変更されません。 のために`s = "go"`そしてページ`["go", "go", "gone", "good"]`、アルゴリズムは戻ります`"go"`なぜなら、それはすでに辞書編集的に最小だからです。 

いつ`s`はページとまったく同じです。たとえば、`s = "home"`そしてページ`["home", "homepage"]`、アルゴリズムが選択します`"home"`で始まるので正しくは`s`で最小です
