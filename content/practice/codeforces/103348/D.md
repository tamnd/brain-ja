---
title: "CF 103348D - 魔女の大釜 I"
description: "演習 57 の制約と追加条件 $c{j+1} cj + 1 qquad (t j ge 1) を使用して $n ct cdots c1 ge 0$ とします。$ シフトされた変数 $dj = cj - (j-1), qquad 1 le j le t を定義します。"
date: "2026-07-03T13:40:35+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103348
codeforces_index: "D"
codeforces_contest_name: "UTPC Contest 10-15-21 Div. 1 (Advanced)"
rating: 0
weight: 103348
solve_time_s: 119
verified: false
draft: false
---

[CF 103348D - 魔女の大釜 I](https://codeforces.com/problemset/problem/103348/D)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 59 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$n > c_t > \cdots > c_1 \ge 0$演習 57 の制約と追加の条件を使用して$c_{j+1} > c_j + 1 \qquad (t > j \ge 1).$シフトされた変数を定義する$d_j = c_j - (j-1), \qquad 1 \le j \le t.$それから$t > j \ge 1$、$d_{j+1} = c_{j+1} - j \ge (c_j + 2) - j = (c_j - (j-1)) + 1 = d_j + 1,$それで$n - t + 1 > d_t > \cdots > d_1 \ge 0.$上限は次から得られます。$c_t \le n-1$したがって、$d_t = c_t - (t-1) \le (n-1) - (t-1) = n - t.$したがって、マッピングは$c \mapsto d$許容されるシーケンス間の全単射です$c_1 < \cdots < c_t$隣接するインデックスがなく、通常の$t$-から導き出される組み合わせ${0,1,\dots,n-t}$。 

スパン コンストレイントも同様に変形します。 から$c_t - c_1 < m$、$c_t - c_1 = (d_t + t - 1) - d_1 = (d_t - d_1) + (t-1),$それで$d_t - d_1 < m - (t-1).$したがって、許容されるコードはシーケンスに正確に対応します$n' > d_t > \cdots > d_1 \ge 0,$と$n' = n - t + 1,$スパン短縮条件と合わせて$d_t - d_1 < m - (t-1).$これにより、問題はパラメータに適用される練習問題 57 のピアノ奏者の問題に帰着します。$n'$そして$m' = m - (t-1)$。 

生成にはアルゴリズムを適用$L$セクション 7.2.1.3 から変数へ$d_t \cdots d_1$変更された境界を使用して$n' = n - t + 1$、スパン条件については同じ受け入れテストを維持します。 アルゴリズムは辞書編集順にすべての組み合わせを調べ、有効なコードはそれぞれ次の変換によって取得されます。$c_j = d_j + (j-1), \qquad 1 \le j \le t.$正しさは、許容可能な間の全単射から導き出されます。$c$- シーケンスと許容される$d$-シーケンス。各変換は順序付けの制約とスパンの不等式の両方を保存および反映するためです。 これで証明は完了です。 ∎
