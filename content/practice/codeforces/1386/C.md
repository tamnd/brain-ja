---
title: "CF 1386C - ジョーカー"
description: "$N = pq$ とします。ここで、$p は 3 pmod 8$ に相当し、$q は 7 pmod 8$ に相当します。 まず、ヤコビのシンボルを含む主張された同一性を証明します。"
date: "2026-06-11T10:39:50+07:00"
tags: ["codeforces", "competitive-programming", "*special", "bitmasks", "data-structures", "divide-and-conquer", "dsu"]
categories: ["algorithms"]
codeforces_contest: 1386
codeforces_index: "C"
codeforces_contest_name: "Baltic Olympiad in Informatics 2020, Day 1 (IOI, Unofficial Mirror Contest, Unrated)"
rating: 2800
weight: 1386
solve_time_s: 144
verified: false
draft: false
---

[CF 1386C - ジョーカー](https://codeforces.com/problemset/problem/1386/C)

 **評価:** 2800
 **タグ:** *特殊、ビットマスク、データ構造、分割統治、DSU
 **解決時間:** 2 分 24 秒
 **確認済み:** いいえ

 ## 解決策
 ## 解決策

 しましょう$N = pq$どこ$p \equiv 3 \pmod 8$そして$q \equiv 7 \pmod 8$。 まず、ヤコビのシンボルを含む主張された同一性を証明します。 ヤコビ記号は乗算的であり、次の条件を満たすことを思い出してください。$$\left(\frac{a}{N}\right) = \left(\frac{a}{p}\right) \left(\frac{a}{q}\right)$$任意の整数に対して$a$。 私たちは計算します$\left(\frac{-1}{N}\right)$そして$\left(\frac{2}{N}\right)$別に。 

二次相反性の法則と標準公式によると、$-1$そして$2$分子として、$$\left(\frac{-1}{p}\right) = (-1)^{(p-1)/2} = (-1)^{(3-1)/2} = (-1)^1 = -1,$$

$$\left(\frac{-1}{q}\right) = (-1)^{(q-1)/2} = (-1)^{(7-1)/2} = (-1)^3 = -1.$$したがって、$$\left(\frac{-1}{N}\right) = \left(\frac{-1}{p}\right)\left(\frac{-1}{q}\right) = (-1)(-1) = 1.$$次に分子付きの記号について$2$、思い出してください$$\left(\frac{2}{p}\right) = (-1)^{(p^2-1)/8}, \qquad \left(\frac{2}{q}\right) = (-1)^{(q^2-1)/8}.$$コンピューティング$$(p^2 - 1)/8 = (3^2 - 1)/8 = (9 - 1)/8 = 1,$$

$$(q^2 - 1)/8 = (7^2 - 1)/8 = (49 - 1)/8 = 48/8 = 6.$$したがって$$\left(\frac{2}{p}\right) = (-1)^1 = -1, \qquad \left(\frac{2}{q}\right) = (-1)^6 = 1.$$乗算すると$$\left(\frac{2}{N}\right) = \left(\frac{2}{p}\right)\left(\frac{2}{q}\right) = (-1)(1) = -1.$$私たちはそれを確立しました$$\left(\frac{-1}{N}\right) = 1 = -(-1) = -\left(\frac{2}{N}\right),$$これは最初の主張を証明します。 

この特性により、曖昧さのない Rabin タイプの暗号化スキームの設計が可能になります。 平文メッセージを送信しましょう$m$満足する$0 \le m < N$。 暗号文を定義する$$c \equiv m^2 \pmod N.$$復号化には解決が必要です$x^2 \equiv c \pmod N$。 モジュロは 4 つの解があります$N$なぜなら$N$は 2 つの異なる奇数素数の積です。 ヤコビ記号のモジュロを考慮することでそれらを区別できます。$N$。 コンピューティング$$\left(\frac{x}{N}\right) \quad \text{and} \quad \left(\frac{x - 1}{2}\right)$$または、単一ビットの冗長性を同等にエンコードします。$m$したがって、4 つの平方根のうちの 1 つだけが所定のヤコビ記号条件を満たすことになります。 アイデンティティ$\left(\frac{-1}{N}\right) = -\left(\frac{2}{N}\right)$からのマッピングが確実に行われるようにします$m$4 つのルートは、ヤコビ記号が異なるペアに分割できるため、適切な記号を持つルートを選択すると、元のメッセージが復元されます。 

明示的に、$m$そのようにエンコードされる$$\left(\frac{m}{N}\right) = 1, \quad m \text{ even or odd according to a chosen convention}.$$計算後$x \equiv \sqrt{c} \pmod N$、ヤコビ記号を使用して一意のソリューションを選択します$1$および所定のパリティ。 これにより、決定論的なデコード関数が得られます。$$m \equiv \text{Dec}(c) \pmod N$$曖昧さなく。 暗号化は残る$$c \equiv m^2 \pmod N$$復号化には、固定のヤコビ記号条件を満たすルートを選択するだけで済みます。 同一性により、4 つのルートのうちの 1 つが基準を満たすことが保証され、元の Rabin SQRT ボックスに存在する 2 つの曖昧さが排除されます。 

これで証明は完了です。 

∎
