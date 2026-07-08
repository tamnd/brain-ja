---
title: "CF 102994C - 不確実なキャッチ"
description: "(1) と同様に $n=s+t$ とし、$(s,t)$ の組み合わせを (3) を満たす $ct cdots c2 c1$ の形式で記述する、つまり $n ct cdots c2 c1 ge 0 とします。"
date: "2026-07-04T02:26:20+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102994
codeforces_index: "C"
codeforces_contest_name: "Moscow Pre-Finals Workshop 2020 - Legilimens+Coffee Chicken Contest (XX Open Cup, Grand Prix of Nanjing)"
rating: 0
weight: 102994
solve_time_s: 137
verified: false
draft: false
---

[CF 102994C - 不確かな問題](https://codeforces.com/problemset/problem/102994/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 17 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$n=s+t$(1) と同様に、$(s,t)$-組み合わせをフォームに記入してください$c_t \cdots c_2 c_1$(3)を満たす、つまり$n > c_t > \cdots > c_2 > c_1 \ge 0.$関連する非負の整数を定義します$q_t, \ldots, q_0$(11) と (12) により、次のようになります。$q_t = s - d_t,\quad q_{t-1} = d_t - d_{t-1},\quad \ldots,\quad q_1 = d_2 - d_1,\quad q_0 = d_1,$ここで、整数は$d_j$は (7) の組み合わせに関連しています。$c_j = d_j + j - 1 \quad (1 \le j \le t).$これらの関係から、$q_t + \cdots + q_1 + q_0 = s,$だからすべての$(s,t)$-組み合わせにより構成が決まります$s$の中へ$t+1$マイナスではない部分。 

圧縮補題 (85) を証明するには、この対応が全単射であることを示せば十分です。 

再構成から単射性が得られる$d_1, \ldots, d_t$から独自に$q_0, \ldots, q_t$。 (12)より、$d_1 = q_0,$

$d_2 = q_1 + d_1,$

$d_3 = q_2 + d_2,$そして一般的に$d_j = q_{j-1} + d_{j-1} \quad (2 \le j \le t).$したがって、それぞれの$d_j$によって一意に決定されます$q$-シーケンス、そしてそれぞれ$c_j = d_j + j - 1$一意に決まる。 したがって、異なる組み合わせにより異なる結果が得られます$q$-シーケンス。 

全射性は構築を逆にすることで得られます。 させて$q_t, \ldots, q_0$の任意の構成である$s$の中へ$t+1$マイナスではない部分。 定義する$d_1 = q_0$そして再帰的に$d_j = q_{j-1} + d_{j-1} \quad (2 \le j \le t),$次に設定します$c_j = d_j + j - 1 \quad (1 \le j \le t).$再帰が意味するのは、$d_1 \le d_2 \le \cdots \le d_t$したがって、$c_1 < c_2 < \cdots < c_t,$そしてまた$c_t \le (q_0 + \cdots + q_{t-1}) + (t-1) = (s - q_t) + (t-1) < s + t = n,$それで$n > c_t > \cdots > c_1 \ge 0$保持、作成$c_t \cdots c_1$有効な$(s,t)$-組み合わせ。 

したがって、あらゆる組成物は、$s$は一意の組み合わせから生じ、すべての組み合わせが構成を生成するため、対応関係は全単射的になります。 これにより、圧縮補題 (85) が確立されます。 ∎
