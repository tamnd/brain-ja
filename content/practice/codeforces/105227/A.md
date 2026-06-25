---
title: "CF 105227A - LLPS"
description: "小文字で構成される単一の文字列が与えられます。 この文字列から、順序を維持しながら文字を削除し、任意のサブシーケンスを生成することができます。"
date: "2026-06-24T16:30:43+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105227
codeforces_index: "A"
codeforces_contest_name: "CPG Training Contest - 1"
rating: 0
weight: 105227
solve_time_s: 315
verified: false
draft: false
---

[CF 105227A - LLPS](https://codeforces.com/problemset/problem/105227/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 5 分 15 秒
 **Verified:** no

 ## 解決策
 ## 問題の理解

 小文字で構成される単一の文字列が与えられます。 この文字列から、順序を維持しながら文字を削除し、任意のサブシーケンスを生成することができます。 Among all such subsequences, we want one that is a palindrome and, among all palindromic subsequences, we want the lexicographically largest.

 文字列の長さは最大 10 であるため、入力は非常に小さいです。 それは問題の性質を即座に変えます。指数関数的な探索はすでに実現可能ですが、それでもすべてのサブシーケンスを強引に実行するのではなく、クリーンな構造的洞察を目指す必要があります。

 ここでの辞書編集順序は標準ルールに従います。つまり、左から右に比較し、文字が異なる最初の位置が決定するか、一方が他方のプレフィックスである場合は、長い文字列の方が大きくなります。

 A naive pitfall is to assume we need to “build” a palindrome greedily from both ends. That fails because optimal choices depend on global structure, not local matching. もう一つの間違いは、長くすれば必ず勝てると考えることです。 これは false です。なぜなら、長いサブシーケンスはより小さな文字で始まり、辞書編集的にすぐに失われる可能性があるからです。

 すべての文字が区別できる場合、小さな特殊なケースが表示されます。 Then every palindrome subsequence has length 1, and the answer is just the maximum character. もう 1 つのケースは、答えが「s」であるサンプル「codeforces」に見られるように、最良の答えが最も長い回文ではない場合です。

 ## アプローチ

 The brute-force view is straightforward: enumerate all subsequences of the string, check whether each is a palindrome, and keep the best under lexicographic order. サブシーケンスは最大 2ⁿ です。 それぞれの回文のチェックには O(n) がかかるため、合計の複雑さは O(n・2ⁿ) になります。 With n ≤ 10, this is at most about 10,240 checks, which is perfectly fine in practice.

 ただし、この強引なアプローチは概念的に過剰です。 The structure of palindromic subsequences in a string has a key simplification: any palindrome has matching first and last characters, and lexicographically larger strings prioritize the first character above everything else. したがって、最初に答えの最初の文字が何であるかを理解する必要があります。

 回文の最初の文字として任意の文字 c を選択した場合、最後の文字も c でなければならず、両方とも文字列内の c の出現に由来する必要があります。 The lexicographically largest palindrome must therefore start with the largest possible character that appears at least once. Once we choose such a character c, we can always form a valid palindrome subsequence consisting only of one occurrence of c, or two occurrences of c if there are at least two.

 次に、可能性を比較します。 c が少なくとも 2 回出現すると、「cc」を形成できます。 c で始まる長い回文も c で始まり、選択された 2 つの出現の間に何かが続く必要があります。 ただし、内部部分には c より小さい文字を先頭に導入してはなりません。そうしないと、辞書編集上の順序が改善されません。 Since c is already the maximum character in the string, nothing can beat a sequence that starts with c and keeps c’s as much as possible, which collapses to repeating c.

 したがって、問題は、文字列内の最大文字を見つけて、それが何回出現するかを数えるということになります。 If it appears once, answer is that character. If it appears k times, the best palindrome is that character repeated k times, since we can pick all occurrences as a subsequence and it is trivially a palindrome.

 これが重要な構造の崩壊です。回文を探索する代わりに、必要なのは最大文字の頻度だけです。

 | アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n・2ⁿ) | O(n) | 受け入れられるが不要 |
 | 最大文字の頻度 | O(n) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

文字列を 1 回処理して、存在する最大の文字とそれが何回出現するかを判断します。 

1. 文字列をスキャンし、これまでに確認された最大文字数を維持します。 これは正しいです。辞書編集上の最適性はまず先頭の文字に依存するため、それをグローバルに最大化する必要があります。 
2. この最大文字が何回出現するかを数えます。 同じ最大文字を繰り返すと回文構造と辞書編集上の値の両方が保持されるため、これは重要です。 
3. 入力内に出現する回数だけ繰り返されるこの文字で構成される文字列を構築します。 
4. この構築された文字列は有効なサブシーケンスであり回文であるため、直接出力します。 

明らかではない部分は、最大文字のすべての出現が許容される理由です。 同一の文字のみで構成される部分シーケンスは、反転しても何も変わらないため、常に回文になります。 すべての文字は等しいため、出現箇所を削除しても、辞書編集上の順序は改善されずに文字列が短縮されるだけです。 したがって、最大性は、等しい開始候補間の最大長に減少します。 

### なぜ効果があるのか

 回文部分列には最初の文字があり、両方の候補がその文字を共有しない限り、辞書編集上の順序はその文字によって即座に決定されます。 したがって、最適な解決策は、文字列内で可能な最大の文字で始まる必要があります。 その文字に制限されると、他の文字は辞書編集上の順序を改善できなくなり、同一の文字のみを追加することで有効性と最適性の両方が維持されます。 これにより、答えは回文として配置された最大の文字のすべての出現のマルチセットになります。これは単にその文字の繰り返しです。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

s = input().strip()

mx = max(s)
cnt = s.count(mx)

print(mx * cnt)
```このソリューションでは、まず Python の組み込み文字比較を使用して最大文字数を計算します。これは、ASCII 順序が辞書編集順序と一致するため、直接機能します。 次に、その文字の出現をカウントします。 The output is constructed by repeating the character.

 微妙な点は、単一の繰り返し文字で構成される文字列は常に回文であるため、回文であることを明示的に検証する必要がないことです。 もう 1 つの微妙な点は、サブシーケンス制約を明示的に考慮する必要がないことです。最大文字のすべての出現を順番に選択できるため、繰り返しは常にサブシーケンスとして実現可能です。

 ## 実用的な例

 「レーダー」について考えてみましょう。

 スキャンすると、最大の文字は「r」であることがわかります。 2回登場します。

 | step | max char | count |
 | --- | --- | --- |
 | r | r | 1 |
 | | r | 1 |
 | d | r | 1 |
 | a | r | 1 |
 | r | r | 2 |

 The result is “rr”. This shows that even though “radar” itself is a palindrome, a lexicographically larger subsequence can be formed by focusing only on the dominant character.

 次に「コードフォース」について考えてみましょう。

 最大文字は「s」で、1 回出現します。

 | step | max char | カウント |
 | --- | --- | --- |
 | c | c | 1 |
 | o | o | 1 |
 | d | o | 1 |
 | e | o | 1 |
 | f | o | 1 |
 | o | o | 1 |
 | r | r | 1 |
 | c | r | 1 |
 | e | r | 1 |
 | s | s | 1 |

 The output is “s”, since no repeated character exists to form a longer palindrome.

 これらのトレースは、アルゴリズムが純粋に最大文字の優位性によって駆動され、他のすべての構造を無視していることを確認します。

 ## 複雑さの分析

 | Measure | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | 単一パスと発生回数のカウント |
 | Space | お(1) | 固定カウンターと最大文字のみ |

 入力サイズは最大 10 であるため、些細な解でも簡単に通過しますが、この線形アプローチはきれいに一般化され、列挙が回避されます。

 ## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline
    s = input().strip()
    mx = max(s)
    cnt = s.count(mx)
    return mx * cnt

# provided samples
assert run("radar\n") == "rr"
assert run("bowwowwow\n") == "wwwww"
assert run("codeforces\n") == "s"

# custom cases
assert run("a\n") == "a", "single character"
assert run("abcabc\n") == "cc", "multiple max letters"
assert run("zzxyzz\n") == "zzzz", "all max occurrences used"
assert run("abcd\n") == "d", "strictly increasing letters"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | | | 最小長の文字列 |
 | ABC | cc | 繰り返し最大文字選択 |
 | うーん ズズズ | 複数の max 出現が優勢 |
 | ABCD | d | 最後の単一の最大値 |

 ## 特殊なケース

 「a」のような単一文字列の場合、アルゴリズムは「a」を最大文字と唯一の文字の両方として識別し、「a」を生成します。 繰り返し回数が 1 回なので、特別な処理は必要ありません。 

「zzxyzz」など、最大文字が何度も出現する文字列の場合、スキャンによりすべての「z」の出現が正しく累積されます。 すべてが等しいため、それらの間の順序は重要ではなく、結果として得られる反復文字列は常に回文であり、有効な部分列の中で辞書編集的に最大になります。
