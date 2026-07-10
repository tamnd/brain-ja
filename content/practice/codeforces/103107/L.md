---
title: "CF 103107L - ラビリビ"
description: "整数 $t ge 1$ を修正します。 $N ge 0$ を与えます。 $kappat N$ を離散的な意味で (セクション 7.2.1.3 の前半のように) $$binom{m}{t} le N < binom{m+1}{t},$$ となるような一意の整数 $m ge t-1$ として定義し、$$kappat N = binom{m}{t-1} を設定します。"
date: "2026-07-03T21:40:23+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103107
codeforces_index: "L"
codeforces_contest_name: "The 16th Heilongjiang Provincial Collegiate Programming Contest"
rating: 0
weight: 103107
solve_time_s: 143
verified: false
draft: false
---

[CF 103107L - ラビ・リビ](https://codeforces.com/problemset/problem/103107/L)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 23 秒
 **確認済み:** いいえ

 ## 解決策
 ## セットアップ

 整数を修正する$t \ge 1$。 させて$N \ge 0$与えられる。 定義する$\kappa_t N$(セクション 7.2.1.3 の前半部分のように) 離散的な意味で、一意の整数として$m \ge t-1$そのような$$\binom{m}{t} \le N < \binom{m+1}{t},$$そしてセット$$\kappa_t N = \binom{m}{t-1}.$$ここで、連続拡張を次のように定義します。 のために$x \ge t-1$、関数$x \mapsto \binom{x}{t}$は厳密に増加しているため、反転可能です$[0,\infty)$。 それぞれについて$N \ge 0$、 させて$x \ge t-1$満足する$$N = \binom{x}{t},$$そして定義します$$\widetilde{\kappa}_t N = \binom{x}{t-1}.$$目標は証明することです$$\kappa_t N \le \widetilde{\kappa}_t N$$すべての整数に対して$t \ge 1$そして$N \ge 0$。 

## 解決策

 しましょう$N \ge 0$そして選択してください$x \ge t-1$そのような$N = \binom{x}{t}$。 させて$m$によって決定される整数になります$$\binom{m}{t} \le \binom{x}{t} < \binom{m+1}{t}.$$以来$x \mapsto \binom{x}{t}$厳密に増加しています$[t-1,\infty)$、不等式が意味するのは$m \le x < m+1$。 

機能$x \mapsto \binom{x}{t-1}$も厳密に増加しています$[t-2,\infty)$。 実際に$x \ge m \ge t-1$、単調性は次のようになります。$$\binom{m}{t-1} \le \binom{x}{t-1}.$$の定義によれば、$\kappa_t N$離散的な意味では、$$\kappa_t N = \binom{m}{t-1}.$$連続拡張の定義により、$$\widetilde{\kappa}_t N = \binom{x}{t-1}.$$不等式に代入すると、$$\kappa_t N \le \widetilde{\kappa}_t N.$$これで証明は完了です。 ∎

 ## 検証

 本当に$x \ge t-1$、式$$\binom{x}{t} = \frac{x(x-1)\cdots(x-t+1)}{t!}$$の製品です$t$線形因子、それぞれ非減少$x$の上$[t-1,\infty)$、したがって、製品は厳密に増加しています。 同じ議論が当てはまります$\binom{x}{t-1}$の上$[t-2,\infty)$。 

もし$m$によって定義されます$\binom{m}{t} \le \binom{x}{t} < \binom{m+1}{t}$、単調性の力$m \le x < m+1$そうしないと、厳密な増加が順序と矛盾するためです。 

不平等$\binom{m}{t-1} \le \binom{x}{t-1}$次の単調性から直接導かれます$m \le x$。 

すべての置換は次の定義と一致します。$\kappa_t N$そして$\widetilde{\kappa}_t N$。 

## 注意事項

 この議論は、一般化二項係数の単調性にのみ依存します。$[t-1,\infty)$凸性や微分可能性は必要ありません。 平等が成り立つとき$x$は整数なので、$N = \binom{x}{t}$力$m = x$、したがって、両方の定義は次のようになります$\binom{x}{t-1}$。
