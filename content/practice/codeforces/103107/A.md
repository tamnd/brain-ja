---
title: "CF 103107A - および RMQ"
description: "実数 $x ge t-1$ について、一般化二項係数 $$binom{x}{t} = frac{x(x-1)cdots(x-t+1)}{t!}、qquad binom{x}{t-1} = frac{x(x-1)cdots(x-t+2)}{(t-1)!} を定義します。"
date: "2026-07-03T21:27:42+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 103107
codeforces_index: "A"
codeforces_contest_name: "The 16th Heilongjiang Provincial Collegiate Programming Contest"
rating: 0
weight: 103107
solve_time_s: 158
verified: false
draft: false
---

[CF 103107A - および RMQ](https://codeforces.com/problemset/problem/103107/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 38 秒
 **確認済み:** いいえ

 ## 解決策
 ## セットアップ

 本当に$x \ge t-1$、一般化された二項係数を定義します$$\binom{x}{t} = \frac{x(x-1)\cdots(x-t+1)}{t!}, \qquad 
\binom{x}{t-1} = \frac{x(x-1)\cdots(x-t+2)}{(t-1)!}.$$機能$x \mapsto \binom{x}{t}$厳密に増加しています$[t-1,\infty)$以来$$\frac{\binom{x+1}{t}}{\binom{x}{t}} = \frac{x+1}{x-t+1} > 1 \quad (x \ge t-1).$$したがって、各整数に対して$N \ge 0$独特のリアルが存在する$x \ge t-1$そのような$$N = \binom{x}{t}.$$実数値関数を定義する$$\kappa_t^{(\mathbb{R})}(N) = \binom{x}{t-1} \quad \text{where } N = \binom{x}{t}.$$整数バージョンにしましょう$\kappa_t^{(\mathbb{Z})}(N)$次のように定義されます: 一意の整数を選択してください$m \ge t-1$そのような$$\binom{m}{t} \le N < \binom{m+1}{t},$$そしてセット$$\kappa_t^{(\mathbb{Z})}(N) = \binom{m}{t-1}.$$目標は証明することです$$\kappa_t^{(\mathbb{R})}(N) \ge \kappa_t^{(\mathbb{Z})}(N)
\quad \text{for all integers } t \ge 1, \; N \ge 0.$$平等が成り立つとき$x$は整数です。 

## 解決策

 修正$t \ge 1$そして$N \ge 0$。 させて$x \ge t-1$次のような一意の実数になります。$$N = \binom{x}{t}.$$させて$m$によって決定される整数になります$$\binom{m}{t} \le \binom{x}{t} < \binom{m+1}{t}.$$以来$x \mapsto \binom{x}{t}$厳密に増加しています$[t-1,\infty)$、不等式$$\binom{m}{t} \le \binom{x}{t}$$暗示する$m \le x$、および厳密な単調性の力$m \le x < m+1$。 

したがって$$m \le x.$$機能を考えてみる$$f(x) = \binom{x}{t-1}.$$のために$x \ge t-1$、比率を計算します$$\frac{f(x+1)}{f(x)} = \frac{\binom{x+1}{t-1}}{\binom{x}{t-1}} = \frac{x+1}{x-t+2}.$$以来$x \ge t-1$、1つは持っています$x+1 \ge t$そして$x-t+2 \le x+1$したがって、$$\frac{x+1}{x-t+2} > 1,$$それで$f(x)$厳密に増加しています$[t-1,\infty)$。 

から$m \le x$と単調性$f$、ということになります。$$\binom{m}{t-1} \le \binom{x}{t-1}.$$定義を置き換えると、$$\kappa_t^{(\mathbb{Z})}(N) \le \kappa_t^{(\mathbb{R})}(N).$$もし$x$が整数である場合、必然的に$x=m$, したがって、平等が成り立ちます。 

これで証明は完了です。 ∎

 ## 検証

 この議論では 2 つの単調性事実のみを使用しており、どちらも一般化二項係数の明示的な比から直接導出されます。 不平等$m \le x$の厳密な単調性から導かれる$\binom{x}{t}$の上$[t-1,\infty)$、との比較$\kappa$値は単調性になります$\binom{x}{t-1}$。 追加の仮定は使用されません。
