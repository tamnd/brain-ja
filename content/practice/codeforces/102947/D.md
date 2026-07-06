---
title: "CF 102947D - 薪"
description: "$n,m を 1$ とします。 目標は、$n$ のすべてのパーティションを最大 $m$ 部分に生成することです。つまり、シーケンス $a1 ge a2 ge cdots ge ak ge 1,quad k le m,quad a1+cdots+ak=n になります。"
date: "2026-07-04T07:29:08+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102947
codeforces_index: "D"
codeforces_contest_name: "UTPC Contest 02-05-21 Div. 1 (Advanced)"
rating: 0
weight: 102947
solve_time_s: 125
verified: false
draft: false
---

[CF 102947D - 薪](https://codeforces.com/problemset/problem/102947/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 5 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$n,m \ge 1$。 目標は、すべてのパーティションを生成することです。$n$せいぜい$m$部分、つまりシーケンス$a_1 \ge a_2 \ge \cdots \ge a_k \ge 1,\quad k \le m,\quad a_1+\cdots+a_k=n.$重要な観察は、そのようなパーティションが次のパーティションに全単射的に対応するということです。$n+m$正確に$m$均一なシフトによるプラスの部分。 

任意のパーティションを指定すると、$n$せいぜい$m$部分的には、次のように埋め込まれた形式で記述します。$m$-タプル$a_1 \ge a_2 \ge \cdots \ge a_m \ge 0,\quad a_1+\cdots+a_m=n.$定義する$b_i = a_i + 1.$それから$b_1 \ge b_2 \ge \cdots \ge b_m \ge 1,$そして$b_1+\cdots+b_m = n+m.$逆に、すべてのパーティション$b_1 \ge \cdots \ge b_m \ge 1$の$n+m$正確に$m$パーツは、固有のパーティションを生成します。$n$せいぜい$m$引き算による部分$1$各コンポーネントから。 内のゼロエントリの数$a_i$は正確に、$b_i$、ゼロを削除すると、最大でパーティションが回復されます。$m$ポジティブな部分。 したがって、この対応は全単射的です。 

アルゴリズム H は、整数のすべての分割を正確に生成します。$m$ポジティブな部分。 それを適用すると、$n+m$の代わりに$n$すべてを生み出す$m$-タプル$b_1,\dots,b_m$と$b_1+\cdots+b_m=n+m,\quad b_i\ge 1.$したがって、必要な変更はステップ H1 に限定され、入力の元の初期化が置き換えられます。$n$入力用の初期化により$n+m$。 

アルゴリズム H のステップ H1 では、代入$a_1 \leftarrow n - m + 1,\quad a_j \leftarrow 1 \ (1<j\le m)$を代入して得られる代入によって置き換えられます。$n+m$のために$n$、つまり$a_1 \leftarrow (n+m) - m + 1 = n+1,\quad a_j \leftarrow 1 \ (1<j\le m),$番兵と一緒に$a_{m+1} \leftarrow -1$変わらない。 

次に、アルゴリズムは次のすべてのパーティションを生成します。$n+m$正確に$m$部品。 各出力後にそれぞれを置き換えます$a_i$による$a_i-1$増加しない$m$- 非負の整数のタプルの合計$n$、ゼロを削除すると、正確に次のパーティションが生成されます。$n$せいぜい$m$部品。 

この変更は、アルゴリズム H の後のステップには影響しません。そこで使用されるすべての不変式は、部分間の相対的な違いと合計の保存にのみ依存し、両方とも次のような均一な変換の下では変化しません。$1$。 ∎
