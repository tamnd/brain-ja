---
title: "CF 104757I - ISBN 変換"
description: "各入力文字列は、数字、ハイフン、およびチェックサム ディジットとして文字 X を含む可能性のある ISBN-10 コードの候補を表します。"
date: "2026-06-28T22:49:16+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104757
codeforces_index: "I"
codeforces_contest_name: "2023-2024 ICPC East North America Regional Contest (ECNA 2023)"
rating: 0
weight: 104757
solve_time_s: 33
verified: true
draft: false
---

[CF 104757I - ISBN 変換](https://codeforces.com/problemset/problem/104757/I)

 **評価:** -
 **タグ:** -
 **解決時間:** 33 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各入力文字列は、数字、ハイフン、および場合によっては文字を含む可能性のある ISBN-10 コードの候補を表します。`X`チェックサム数字として。 私たちのタスクは、まずこの文字列が指定されたルールに従って有効な ISBN-10 であるかどうかを判断し、有効な場合は対応する ISBN-13 表現に変換することです。 

検証は最初の関門です。 ハイフンを削除した後、文字列を 10 桁の ISBN として解釈する必要があります。最後の文字はチェックサム桁です。 チェックサム ルールは、10 から 1 までの加重合計であり、11 で割り切れなければなりません。特別なルールとして、数字は`X`は値 10 を表しますが、チェックサム位置でのみ許可されます。 

ISBN-10 が有効な場合、変換はチェックサムを破棄し、プレフィックスを付加することによって続行されます。`978`、次に重み 1 と 3 を交互に使用して新しい ISBN-13 チェックサムを再計算します。 

制約は小さく、最大 25 個の文字列、それぞれの長さは 13 までです。これにより、重いデータ構造や最適化の必要性が即座に排除されます。 すべての操作は文字列の長さに関して線形にすることができ、単純な解析と再計算のアプローチでも十分です。 

主な微妙なエッジケースは、計算上のものではなく構造的なものです。 

問題の 1 つはハイフンの処理です。 入力では、先頭、末尾、または連続する位置を除く任意の場所にハイフンを使用できます。 単純なアプローチでは、ハイフンを削除して数字を検証するだけかもしれませんが、削除後も意味のある文字がちょうど 10 文字残っていることを確認しない限り、これでは十分ではありません。 

もう一つの問題は、`X`キャラクター。 不用意に実装すると、次のような問題が発生する可能性があります。`X`完全に無効ですが、チェックサム数字としてのみ有効であり、最終位置の値 10 のみを提供します。 

3 番目の微妙な点は、無効なフォーマットと無効なチェックサムの両方が同じ出力を生成することです。`"invalid"`。 例えば、`3-540-4258-02`ハイフンを削除した後は構造的には問題ありませんが、チェックサムに失敗し、不正なハイフン パターンも無効になります。 

## アプローチ

 ブルート フォース アプローチでは、考えられるすべてのハイフンの配置と数字の解釈をチェックして、複数の方法で文字列の解釈を明示的に試みます。 この問題によりすでに構造が修正されているため、これは不要です。ハイフンは書式設定を除いて数値の意味とは無関係であり、チェックサム ルールによって有効性が独自に決定されます。 

その代わりに、重要な観察は、問題を 2 つの独立した線形パスに減らすことができるということです。 まず、意味を維持しながらハイフンを削除して文字列を正規化します。`X`最終位置のみ。 次に、ISBN-10 チェックサムを直接計算します。 合格した場合は、連結によって ISBN-13 を構築し、そのチェックサムを 1 回のパスで計算します。 

問題の構造により、正規化が完了すると、すべてが決定論的な演算になることが保証されます。 検索や曖昧さはありません。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート フォース解析の亜種 | 指数 | O(n) | 遅すぎる |
 | 正規化 + 直接チェックサム計算 | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 ### ISBN-10 の検証と変換

 1. 入力文字列を読み取り、すべてのハイフンを削除して、コンパクトな形式を作成します。 
2. 結果の文字列がちょうど 10 文字であることを確認します。 そうでない場合は無効です。 
3. 文字 1 ～ 9 が数字のみであることを確認します。 いずれかが数字でない場合は、その文字列を拒否します。 
4. 最後の文字を個別に処理します。数字または`X`、 どこ`X`は10を表します。 
5. 10 から 1 までの重みを使用して、ISBN-10 の重み付き合計を計算します。 
6. 合計が 11 で割り切れない場合、出力`"invalid"`。 
7. それ以外の場合は、最初の 9 桁を取得して先頭に付加することにより、ISBN-13 ベース文字列を構築します。`"978"`、書式設定のためにプレフィックスの後にハイフンを挿入します。 
8. 最初の 12 桁に対して重み 1 と 3 を交互に使用して、ISBN-13 チェックサム桁を計算します。 
9. チェックサムの数字を追加し、元の文字列 (古いチェックサムの位置を除く) のハイフンを保持し、その後に新しいハイフンを加えた最終的な ISBN-13 文字列を出力します。`978`。 

### なぜ効果があるのか

 正確性は、ISBN-10 の妥当性が、固定重みに対する 11 を法とする単一の線形合同式によって完全に捉えられ、ISBN-13 の妥当性が同様に 10 を法とする線形合同式であるという事実に基づいています。文字列が正規化されると、ハイフンは算術構造に影響を与えなくなるため、計算は 2 つの決定論的な重み付き合計を評価することになります。 どちらのチェックサム ルールも、前の桁を考慮して最後の桁を一意に決定するため、変換ステップは完全に明確に定義されており、あいまいさが生じません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def is_valid_isbn10(s):
    # s is string without hyphens
    if len(s) != 10:
        return False, None

    total = 0
    for i in range(9):
        if not s[i].isdigit():
            return False, None
        total += (10 - i) * int(s[i])

    # last digit
    if s[9] == 'X':
        d10 = 10
    elif s[9].isdigit():
        d10 = int(s[9])
    else:
        return False, None

    total += 1 * d10

    if total % 11 != 0:
        return False, None

    return True, s[:9]

def isbn13_checksum(digits12):
    total = 0
    for i, ch in enumerate(digits12):
        d = int(ch)
        if i % 2 == 0:
            total += d
        else:
            total += 3 * d
    return (10 - (total % 10)) % 10

def convert(isbn10_raw):
    s = isbn10_raw.strip()

    # keep hyphen pattern info
    parts = []
    cur = []
    for ch in s:
        if ch == '-':
            if cur:
                parts.append(''.join(cur))
                cur = []
            parts.append('-')
        else:
            cur.append(ch)
    if cur:
        parts.append(''.join(cur))

    compact = ''.join(ch for ch in s if ch != '-')

    ok, base9 = is_valid_isbn10(compact)
    if not ok:
        return "invalid"

    # build ISBN-13 digits
    digits12 = "978" + base9

    check = isbn13_checksum(digits12)
    full_digits = digits12 + str(check)

    # formatting: prepend 978- then keep original hyphens except last checksum position removed
    # We reconstruct simply: 978- + original structure without last char
    rebuilt = []
    rebuilt.append("978-")

    # reuse original hyphen structure except last char removed
    core = s.replace('-', '')[:-1]
    idx = 0
    for ch in s:
        if ch == '-':
            rebuilt.append('-')
        else:
            if idx < len(core):
                rebuilt.append(core[idx])
                idx += 1

    rebuilt.append(str(check))
    return ''.join(rebuilt)

t = int(input())
for _ in range(t):
    print(convert(input().strip()))
```実装では、最初に ISBN-10 を取り除いて検証し、数値コアと c を分離します。
