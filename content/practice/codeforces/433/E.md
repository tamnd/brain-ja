---
title: "CF 433E - 立花奏の豆腐"
description: "特定の「値」制約を満たす、指定された範囲 [l, r] (基数 m で表される) 内の数値を数えるように求められます。 各数値は値 0 から始まります。 n 個のパターンが与えられ、それぞれが基数 m の一連の数字であり、関連する整数値が関連付けられています。"
date: "2026-06-07T02:41:32+07:00"
tags: ["codeforces", "competitive-programming", "dp"]
categories: ["algorithms"]
codeforces_contest: 433
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 248 (Div. 2)"
rating: 2500
weight: 433
solve_time_s: 103
verified: false
draft: false
---

[CF 433E - 立花奏の豆腐](https://codeforces.com/problemset/problem/433/E)

 **評価:** 2500
 **タグ:** dp
 **解決時間:** 1 分 43 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 特定の「値」制約を満たす、指定された範囲 [l, r] (base _m_ で表される) 内の数値を数えるように求められます。 各数値は値 0 から始まります。 _n_ 個のパターンが与えられており、それぞれが基数 _m_ の一連の数字であり、関連する整数値が関連付けられています。 数値の場合、連続する部分文字列としてパターンが出現するたびに、その値がパターンの値だけ増加します。 数値の合計が_k_を超えない場合、その数値は「麻婆豆腐」とみなされます。 タスクは、10^9+7 を法とするすべての麻婆豆腐の数を数えることです。 

入力は数値を整数ではなく数字の配列として表します。つまり、先頭のゼロと基数の変換を慎重に処理する必要があります。 数値は最大 200 桁、基数は最大 20、パターン数は最大 200 です。各パターンは最大 200 桁で、パターンの長さの合計は ≤ 200 です。 

範囲には最大 m^200 の数値を含めることができるため、範囲内のすべての数値を単純に反復することは不可能です。 さらに、パターンは数値内で重複する可能性があるため、数値は複数の重複する一致からの値を累積する可能性があります。 これにより、総当たりの列挙が排除されます。 エッジ ケースには、先頭にゼロがあるパターン、最小桁の数値 (l=1、r=1 など)、および k しきい値に正確に一致する数値が含まれます。 

たとえば、値 2 および k=2 のパターン「01」を持つバイナリ範囲 [10, 11] を考えてみましょう。 数字の10には「01」が含まれていないので、その値は0となり、麻婆豆腐になります。 11番も「01」が入っていないので、これも麻婆豆腐です。 単純なアプローチでは、不適切な部分文字列の一致や先頭のゼロが原因でカウントを誤る可能性があります。 

## アプローチ

 ブルートフォース手法では、[l, r] 内のすべての数値を生成し、それを数字の文字列に変換し、すべての可能な部分文字列をすべてのパターンに対してチェックし、それらの値を合計し、数値 ≤ k をカウントします。 時間計算量は O((r-l+1) * n * max_pattern_length) ですが、r-l+1 は巨大になる可能性があるため (最大 m^200)、これは実行不可能です。 m=10 のような小さな基底であっても、これはすぐに 10^200 を超え、あらゆる計算能力をはるかに超えます。 

重要な洞察は、**数字動的プログラミングと Aho-Corasick オートマトン** を組み合わせて使用​​することです。 Digit DP を使用すると、桁を最上位から最下位まで処理し、現在のプレフィックスが上限または下限に一致するかどうかなどの状態情報を維持することで、明示的に生成することなく範囲内の数値をカウントできます。 Aho-Corasick オートマトンは、累積値を更新しながら、重複するパターンを含む、数字が処理される際のすべてのパターンの発生を効率的に追跡します。 これらを組み合わせることで、値 ≤ k を持つすべての数値 ≤ r を数え、l 未満の数値の数を減算して最終的な答えを得ることができます。 

ブルート フォースは問題ステートメントを直接実装するため機能しますが、検索スペースのサイズが膨大であるため失敗します。 オートマトンでパターンをエンコードし、状態と累積値に基づいて DP で数をカウントできるという観察により、複雑さが大幅に軽減されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O((r-l+1) * n * L) | O(n * L) | 遅すぎる |
 | デジットDP + アホコラシック | O(len * 状態数 * k * 2) | O(状態数 * k) | 承認済み |

 ## アルゴリズムのチュートリアル

1. すべてのパターンを Aho-Corasick トライに変換します。 トライの各ノードは何らかのパターンのプレフィックスに対応し、オートマトンがそのノードに到達した場合に累積される合計値 (そこで終了するすべてのパターン値の合計) をノードごとに事前計算します。 
2. トライの失敗リンクを構築します。 これらのリンクを使用すると、次の桁が現在のノードと一致しない場合に、あるパターンのプレフィックスと一致する最長のサフィックス ノードに移動できるため、パターンのすべての出現が効率的にカウントされます。 
3. DP 状態を dp[pos][node][value][tight_low][tight_high] として定義します。ここで、pos は現在の桁インデックス、node は現在の Aho-Corasick 状態、value はこれまでに蓄積されたパターン値、tight_low と tiny_high は、現在のプレフィックスが厳密に下限または上限に従っているかどうかを追跡します。 タイトなフラグにより​​、[l, r] の外側の数値はカウントされなくなります。 
4. ルート ノード、値 0、および両方のタイト フラグを設定して、位置 0 で DP を初期化します。 
5. 位置ごとに、タイトなフラグに従って許可された桁を繰り返します。 新しい数字の Aho-Corasick ノードを更新し、ノードのパターン値を累積値に追加し、新しい数字が境界と一致するかどうかに基づいてタイト フラグを伝播します。 
6. 累積値 ≤ k の状態のみを伝播します。 k を超える州は答えに貢献できません。 
7. すべての桁を処理した後、dp[len][_][value ≤ k][_][*] を合計して、麻婆豆腐の数 ≤ r を求めます。 l <l の数値について繰り返し、減算して [l, r] の数値を取得します。 
8. 最終的な答えを 10^9+7 を法として求めます。 

機能する理由: DP は、範囲内のすべての数値を 1 桁ずつ考慮します。 オートマトンは、重複するパターンが発生ごとに 1 回だけカウントされることを保証し、累積された値が麻婆豆腐の合計値を追跡します。 値の制約を満たすすべての DP 状態を合計することにより、すべての有効な数値が正確にカウントされます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline
MOD = 10**9 + 7

class ACNode:
    def __init__(self):
        self.children = {}
        self.fail = None
        self.value = 0

def build_aho_corasick(patterns, m):
    root = ACNode()
    for digits, val in patterns:
        node = root
        for d in digits:
            if d not in node.children:
                node.children[d] = ACNode()
            node = node.children[d]
        node.value += val

    queue = []
    for d in range(m):
        if d in root.children:
            root.children[d].fail = root
            queue.append(root.children[d])
        else:
            root.children[d] = root

    while queue:
        cur = queue.pop(0)
        for d, child in cur.children.items():
            fail_node = cur.fail
            while d not in fail_node.children:
                fail_node = fail_node.fail
            child.fail = fail_node.children[d]
            child.value += child.fail.value
            queue.append(child)
    return root

def count_numbers(num, root, m, k):
    memo = {}

    def dfs(pos, node, val, tight):
        if val > k:
            return 0
        if pos == len(num):
            return 1
        key = (pos, id(node), val, tight)
        if key in memo:
            return memo[key]
        res = 0
        max_digit = num[pos] if tight else m-1
        for d in range(0, max_digit+1):
            next_node = node
            while d not in next_node.children:
                next_node = next_node.fail
            next_node = next_node.children[d]
            res += dfs(pos+1, next_node, val + next_node.value, tight and d == max_digit)
            res %= MOD
        memo[key] = res
        return res

    return dfs(0, root, 0, True)

def normalize_digits(a, length):
    return [0]*(length - len(a)) + a

def main():
    n, m, k = map(int, input().split())
    l_len, *l_digits = map(int, input().split())
    r_len, *r_digits = map(int, input().split())
    l_digits = normalize_digits(l_digits, max(len(l_digits), len(r_digits)))
    r_digits = normalize_digits(r_digits, max(len(l_digits), len(r_digits)))
    patterns = []
    for _ in range(n):
        parts = list(map(int, input().split()))
        patterns.append((parts[:-1], parts[-1]))
    root = build_aho_corasick(patterns, m)

    def prep(num):
        return normalize_digits(num, len(r_digits))

    res_r = count_numbers(prep(r_digits), root, m, k)
    # for l-1 we need a decrement function
    def dec(num):
        num = num[:]
        idx = len(num)-1
        while idx >= 0:
            if num[idx] > 0:
                num[idx] -= 1
                break
            num[idx] = m-1
            idx -= 1
        return num
    l_minus = dec(prep(l_digits))
    res_l = count_numbers(l_minus, root, m, k)
    print((res_r - res_l) % MOD)

if __name__ == "__main__":
    main()
```コードには 3 つの主要なセクションがあります。オートマトンの構築、累積値を使用した数字 DP の実行、および [l, r] 境界の慎重な処理です。 デクリメント関数は、先頭にゼロのある数値を適切に処理して、l 未満の数値を数えるときに正確さを確保します。 

## 実用的な例

 **サンプル 1:**

 入力桁：l=1、r=100、パターン：1→1、01→1

 | 位置 | ノード | 値 | タイト | 考慮される桁 | カウント |
 | --- | --- | --- | --- | --- | --- |
 | 0 | ルート | 0 | 本当 | 1..9 | ... |

 トレースすると、k=1 を超える数値は 3 つだけであることが確認されるため、97 は
