---
title: "CF 1163D - 謎のコード"
description: "部分的に読めないコードを表す文字列 c が与えられています。 c 内の一部の位置は読み取り可能な小文字であり、その他の位置は不明な文字を表すアスタリスクです。"
date: "2026-06-12T02:22:06+07:00"
tags: ["codeforces", "competitive-programming", "dp", "strings"]
categories: ["algorithms"]
codeforces_contest: 1163
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 558 (Div. 2)"
rating: 2100
weight: 1163
solve_time_s: 66
verified: true
draft: false
---

[CF 1163D - 謎のコード](https://codeforces.com/problemset/problem/1163/D)

 **評価:** 2100
 **タグ:** dp、文字列
 **解決時間:** 1 分 6 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 文字列が与えられています`c`これは部分的に読めないコードを表します。 一部のポジション`c`は判読可能な小文字で、その他は不明な文字を表すアスタリスクです。 また、次の 2 つの文字列も与えられます。`s`これは、最終的に復元されたコードにできるだけ多く出現する必要があります。`t`、同じ文字列内で最小化したいと考えています。 タスクは、次のアスタリスクを置き換えることです。`c`の出現数の差を最大化するには、小文字を使用します。`s`そして`t`。 

文字列`c`最大 1000 文字の長さにすることができますが、`s`そして`t`長さは最大 50 文字です。 なぜなら`c`は比較的小さいため、位置をまたいで動的プログラミングを実行することが可能です。`c`。 主な課題は、アスタリスク内の各置換が、可能性のある複数の部分文字列一致に影響を与えることです。`s`そして`t`、重複するオカレンスは慎重に処理する必要があります。 考えられるすべての置換を試みる単純なアプローチでは、チェックが必要になります。`26^1000`最悪の場合、その可能性は天文学的に大きく、まったく実行不可能です。 

明らかではないエッジケースには、次のような状況が含まれます。`s`そして`t`部分的に重なっている、またはすべての文字が重なっている場所`c`はアスタリスクです。 たとえば、次の場合`c = "***"`、`s = "aa"`、 そして`t = "aaa"`、不注意で貪欲なアプローチにより、`s`ローカルで誤って追加のオカレンスが作成される可能性があります。`t`、全体のスコアが低下します。 もう 1 つのエッジケースは次のような場合です`s`そして`t`共通のプレフィックスを共有する。 最大化する`s`同時に発生する可能性があります`t`重複が無視される場合。 

## アプローチ

 ブルートフォースソリューションでは、アスタリスクの可能なすべての置換が生成され、アスタリスクの出現数がカウントされます。`s`そして`t`候補文字列ごとに。 これは非常に小さな文字列に対しては機能しますが、`|c|`最大 1000、最大 1000 までの可能性があります`26^1000`候補文字列を指定することはできませんが、これは完全に不可能です。 

重要な観察は、この問題が文字列照合オートマトンを使用した動的プログラミングに帰着できるということです。 DP テーブルを維持できます`dp[pos][state_s][state_t]`どこ`pos`の現在位置です`c`、`state_s`のプレフィックスの長さです`s`ここまで一致していて、`state_t`のプレフィックスの長さです`t`ここまで一致しました。 の各キャラクターごとに、`c`(アスタリスクの場合はそれぞれの置換)、どのくらいの量を追跡する有限オートマトンの遷移に従って状態を更新します。`s`そして`t`マッチングされました。 満杯のとき`s`一致するとスコアが 1 増加し、フルになるとスコアが 1 つ増加します。`t`が一致すると、スコアが 1 減ります。 

オートマトンの効率的な構築は、Knuth-Morris-Pratt 故障関数に依存します。 これにより、特定の状態と次の文字、オートマトンの次の状態を定数時間で計算できます。 このアプローチにより、指数関数的な問題を DP over に削減します。`n * |s| * |t|`との状態`O(26)`状態ごとの遷移。これは、指定された制約で実現可能です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(26^n * n) | O(n) | 遅すぎる |
 | オートマトンDP | O(n * | s | * |

 ## アルゴリズムのチュートリアル

 1. 故障関数を事前計算します。`s`そして`t`部分一致間の素早い移行を可能にします。 文字列の場合`p`、`fail[i]`の最長プレフィックスの長さです`p`それはの接尾辞です`p[0:i]`。 これにより、部分一致と次の文字が与えられた場合に、新しい一致の長さがわかります。 
2. 遷移テーブルの構築`next_s[state][char]`そして`next_t[state][char]`のために`s`そして`t`それぞれ。`next_s[state][char]`の新しいプレフィックスの長さを示します`s`見てからマッチングしました`char`私たちが一致したことを考えると`state`これまでの登場人物たち。 同じことが当てはまります`t`。 
3. DP テーブルの初期化`dp[pos][state_s][state_t]`を除いて負の無限大で満たされます`dp[0][0][0] = 0`。 ここ`pos`のインデックスです`c`、`state_s`は何文字ですか`s`で終わりを一致させました`pos-1`、 そして`state_t`は何文字ですか`t`で終わりを一致させました`pos-1`。 
4. 位置を反復処理する`pos`0から`len(c)-1`。 それぞれについて`(state_s, state_t)`, try all possible next characters. もし`c[pos]`は既知の文字であるため、その文字のみが考慮されます。 それ以外の場合は、26 文字すべてが考慮されます。 
5. キャラクターごとに`ch`、次の状態を計算します`new_s = next_s[state_s][ch]`そして`new_t = next_t[state_t][ch]`。 DP を更新します。`dp[pos+1][new_s][new_t] = max(dp[pos+1][new_s][new_t], dp[pos][state_s][state_t] + score)`、 どこ`score`の場合は 1`new_s`満席に達しました`|s|`(引き算`|s|`から`new_s`数えた後)、場合は -1`new_t`満席に達しました`|t|`(引き算`|t|`から`new_t`数えた後）。 
6. DP テーブルに記入すると、答えは全体の最大値になります。`dp[len(c)][*][*]`。 

なぜ機能するのか: DP は次の不変条件を維持します。`dp[pos][state_s][state_t]`達成可能な最大値です`f(c', s) - f(c', t)`最初のものを使用して`pos`の登場人物`c`部分一致で終わる`state_s`そして`state_t`。 オートマトンの遷移により、重複するオカレンスが正しく処理されることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def compute_kmp_transition(pattern):
    m = len(pattern)
    fail = [0] * m
    for i in range(1, m):
        j = fail[i-1]
        while j > 0 and pattern[i] != pattern[j]:
            j = fail[j-1]
        if pattern[i] == pattern[j]:
            j += 1
        fail[i] = j
    nxt = [[0]*26 for _ in range(m+1)]
    for state in range(m+1):
        for c in range(26):
            if state < m and ord(pattern[state]) - ord('a') == c:
                nxt[state][c] = state + 1
            else:
                j = state
                while j > 0 and (j == m or ord(pattern[j]) - ord('a') != c):
                    j = fail[j-1]
                if j < m and ord(pattern[j]) - ord('a') == c:
                    nxt[state][c] = j + 1
                else:
                    nxt[state][c] = 0
    return nxt

def main():
    c = input().strip()
    s = input().strip()
    t = input().strip()
    n = len(c)
    ns = len(s)
    nt = len(t)
    nxt_s = compute_kmp_transition(s)
    nxt_t = compute_kmp_transition(t)
    INF = float('-inf')
    dp = [[ [INF]*(nt+1) for _ in range(ns+1)] for _ in range(n+1)]
    dp[0][0][0] = 0
    for i in range(n):
        for ps in range(ns+1):
            for pt in range(nt+1):
                if dp[i][ps][pt] == INF:
                    continue
                chars = [ord(c[i])-ord('a')] if c[i] != '*' else list(range(26))
                for ch in chars:
                    ns_state = nxt_s[ps][ch]
                    nt_state = nxt_t[pt][ch]
                    score = 0
                    if ns_state == ns:
                        score += 1
                        ns_state = nxt_s[ns_state-1][ch]
                    if nt_state == nt:
                        score -= 1
                        nt_state = nxt_t[nt_state-1][ch]
                    dp[i+1][ns_state][nt_state] = max(dp[i+1][ns_state][nt_state], dp[i][ps][pt] + score)
    result = max(dp[n][ps][pt] for ps in range(ns+1) for pt in range(nt+1))
    print(result)

if __name__ == "__main__":
    main()
```の`compute_kmp_transition`関数は、考えられる各文字のパターンの各プレフィックスに対して次の状態遷移を事前計算します。 DP テーブル`dp[i][ps][pt]`部分一致の組み合わせごとに達成可能な最高のスコアを追跡します。 アスタリスクを処理する場合、26 文字すべてが試行されますが、既知の文字の場合は 1 つの遷移が強制されます。 DP を更新する`max`常に最適な選択を維持できるようにします。 

## 実用的な例

 入力の場合:```
*****
katie
shiro
```| 私 | ps | ポイント | dp[i][ps][pt] | 試してみた | ns_状態 | nt_state | 新しいDP |
 | --- | --- | --- | --- | --- | --- | --- | --- |
 | 0 | | | | | | | |
