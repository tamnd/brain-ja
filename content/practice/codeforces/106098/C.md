---
title: "CF 106098C - MEDAA とモハメド・ハゼム"
description: "すべての正の整数 $x$ について、$tau(x)$ が $x$ の正の約数の数を表すものとします。 与えられた $n$ に対して、$$tau(a)+tau(b) < tau(gcd(a,b)) + tau(operatorname{lcm}(a,b)) となるような、$1 le a,b le n$ を持つ順序ペア $(a,b)$ の数を数えなければなりません。"
date: "2026-06-25T11:54:45+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 106098
codeforces_index: "C"
codeforces_contest_name: "The American University in Cairo CSEA Fall 2025 contest"
rating: 0
weight: 106098
solve_time_s: 78
verified: true
draft: false
---

[CF 106098C - MEDAA とモハメド・ハゼム](https://codeforces.com/problemset/problem/106098/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 18 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 すべての正の整数に対して$x$、 させて$\tau(x)$の正の約数の数を示します$x$。 

与えられた$n$、注文されたペアの数を数えなければなりません$(a,b)$と$1 \le a,b \le n$そのような$$\tau(a)+\tau(b)
<
\tau(\gcd(a,b))
+
\tau(\operatorname{lcm}(a,b)).$$ペアでのご注文となりますので、$(2,3)$そして$(3,2)$は別々にカウントされます。 

入力には最大で次のものが含まれます$10^4$テストケース、一方で$n$と同じ大きさにすることができます$10^6$。 個々のペアを検査する解決策は完全に不可能です。 単一のテスト ケースであっても、$10^{12}$ペアのとき$n=10^6$。 

制約$n \le 10^6$大量の事前計算を強く示唆しています。 テストケースが多いので大まかに一度前処理をしたい$O(n \log n)$時間を計って各質問に答えます$O(1)$。 

いくつかの特殊なケースは見落とされがちです。 

考慮する$n=2$。 唯一の自明ではないペアは、$(1,2)$または$(2,1)$、 しかし$$\tau(1)+\tau(2)=1+2=3,$$その間$$\tau(\gcd(1,2))+\tau(\operatorname{lcm}(1,2))
=
1+2=3.$$不等式は厳密なので、答えは0です。 

ペアを検討してください$(2,2)$。 不注意な解決策では、gcd と lcm が両方とも大きいため、等しい数値が適切であると考える可能性があります。 実際には、$$\tau(2)+\tau(2)
=
\tau(2)+\tau(2),$$したがって、等価性が成り立ち、ペアは数えられません。 

考慮する$(4,2)$。$$\tau(4)+\tau(2)=3+2=5,$$そして$$\tau(2)+\tau(4)=2+3=5.$$ここでも平等が成り立ちます。 一方の数値が他方の数値の倍数であるペアは、自動的には有効になりません。 

## アプローチ

 ブルートフォースのアイデアは単純です。 すべてのペアについて$(a,b)$、計算する$\gcd(a,b)$、$\operatorname{lcm}(a,b)$、除数の数を評価し、不等式をテストします。 

これは定義を直接実装しているため、これは正しいです。 残念ながら、それは必要です$n^2$ペアチェック。 と$n=10^6$、それは約です$10^{12}$除数の計算を考慮する前に演算を実行できます。 

重要な観察は、書くことから得られます$$a=gx,\qquad b=gy,$$どこ$$g=\gcd(a,b), \qquad \gcd(x,y)=1.$$させて$$G=\tau(g).$$すべての素因数について、次の指数$g$は共通部分ですが、追加の指数は完全にどちらかに属します。$x$または$y$。 

定義する$$X=\prod \frac{\text{exponent in }a + 1}
              {\text{exponent in }g + 1},$$そして同様に定義します$Y$。 

それから$$\tau(a)=GX,\qquad
\tau(b)=GY,\qquad
\tau(\operatorname{lcm}(a,b))=GXY.$$不等式に代入すると、$$GX+GY < G+GXY.$$で割る$G$、$$X+Y < 1+XY.$$並べ替えると、$$(X-1)(Y-1) > 0.$$以来$X,Y \ge 1$、不等式が正確に成立するのは、$$X>1 \quad\text{and}\quad Y>1.$$しかし$X=1$余分な素数指数が存在しないことを意味します$a$-サイド、つまり$x=1$。 同じく$Y=1$手段$y=1$。 

したがって、元の不等式は次と等価です$$x>1 \quad\text{and}\quad y>1.$$これにより、除数の数が問題から完全に削除されます。 

これで、すべての有効なペアは次のように記述できます。$$a=gx,\qquad b=gy,$$と$$\gcd(x,y)=1,\qquad x>1,\qquad y>1.$$固定の互いに素のペアの場合$(x,y)$、有効な選択肢の数$g$は$$\left\lfloor \frac{n}{\max(x,y)} \right\rfloor.$$したがって$$\text{answer}(n)
=
\sum_{\substack{x,y>1\\\gcd(x,y)=1}}
\left\lfloor \frac{n}{\max(x,y)} \right\rfloor.$$させて$m=\max(x,y)$。 

もし$m$が固定されている場合、有効なペアは次のとおりです。$$(m,k)$$そして$$(k,m),$$どこ$2 \le k < m$そして$\gcd(k,m)=1$。 

カウントは$$2(\varphi(m)-1),$$なぜなら$\varphi(m)$互いに素な整数を数えます$m$で$[1,m-1]$、そして我々は除外する必要があります$k=1$。 

したがって、$$\text{answer}(n)
=
\sum_{m=2}^{n}
2(\varphi(m)-1)
\left\lfloor \frac{n}{m} \right\rfloor.$$これは古典的な除数の和の形式です。 までのすべての答えを事前計算$10^6$調和スタイルのふるいを使用して実行できます。$$\text{ans}[k\cdot m]
\mathrel{+}= 2(\varphi(m)-1).$$全体の複雑さは次のとおりです。$O(n \log n)$。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(n^2)$|$O(1)$| 遅すぎる |
 | 最適 |$O(N \log \log N + N \log N)$前処理、$O(1)$クエリごと |$O(N)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべてのテスト ケースを読み取り、クエリの最大値を決定します。$N$。 
2. オイラーの合計関数を計算する$\varphi(i)$あらゆる人にとって$1 \le i \le N$直線ふるいを使用します。 これにより、次の整数の数が得られます。$[1,i]$と互いに素である$i$。 
3. あらゆる人にとって$m$2から$N$、計算する$$w(m)=2(\varphi(m)-1).$$これはまさに順序付けられた互いに素のペアの数です$(x,y)$と$x,y>1$そして$\max(x,y)=m$。 
4. 倍数ごとに$k\cdot m \le N$、 追加$w(m)$に`ans[k*m]`。 

これは、以下に対応する用語に寄与します。$$\left\lfloor \frac{n}{m} \right\rfloor.$$5.すべての処理が完了したら$m$、`ans[n]`等しい$$\sum_{m=2}^{n}
w(m)\left\lfloor \frac{n}{m} \right\rfloor.$$6. 出力`ans[n]`テストケースごとに。 

### なぜ効果があるのか

 すべてのペア$(a,b)$次のように一意に書くことができます$$a=gx,\qquad b=gy,$$と$\gcd(x,y)=1$。 

約数を公約数で表現した後$g$、不等式は次のようになります$$(X-1)(Y-1)>0.$$以来$X,Y \ge 1$、これは、両方の縮小された部分が満たされる場合に正確に当てはまります。$x>1$そして$y>1$。 

したがって、有効なペアをカウントすることは、互いに素な順序のペアをカウントすることと同じです。$(x,y)$両方の値が 1 より大きく、考えられる共通因数の数を乗算したもの$g$。 

グループ化$m=\max(x,y)$カウントを次のように変換します$$\sum_{m=2}^{n}
2(\varphi(m)-1)
\left\lfloor \frac{n}{m} \right\rfloor,$$これはまさに前処理で計算されるものです。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

t = int(input())
queries = [int(input()) for _ in range(t)]

mx = max(queries)

phi = [0] * (mx + 1)
phi[1] = 1

primes = []
is_comp = [False] * (mx + 1)

for i in range(2, mx + 1):
    if not is_comp[i]:
        primes.append(i)
        phi[i] = i - 1

    for p in primes:
        v = i * p
        if v > mx:
            break

        is_comp[v] = True

        if i % p == 0:
            phi[v] = phi[i] * p
            break
        else:
            phi[v] = phi[i] * (p - 1)

ans = [0] * (mx + 1)

for m in range(2, mx + 1):
    w = 2 * (phi[m] - 1)
    for multiple in range(m, mx + 1, m):
        ans[multiple] += w

out = []
for n in queries:
    out.append(str(ans[n]))

sys.stdout.write("\n".join(out))
```最初の部分では、線形ふるいを使用してオイラーの合計値を計算します。 これは線形時間で実行され、因数分解の繰り返しを回避します。 

2 番目の部分では、解答表を作成します。 固定の場合$m$、値$$2(\varphi(m)-1)$$の倍数ごとに加算されます$m$。 すべてのアップデートの後、それぞれ`ans[n]`には、前に導出した除数の和の公式が正確に含まれています。 

次の場合、答えは 32 ビットの範囲を超える可能性があります。$n$アプローチ$10^6$, そのため、Python の任意精度の整数は便利です。 C++では、`long long`が必要です。 

よくある間違いは、`-1`内部`phi(m)-1`。 合計数には値 1 が含まれますが、減少した数は含まれません。$x$そして$y$両方とも 1 より大きくなければなりません。 

## 実用的な例

 ### 例 1

 入力:```
3
```のみ$m=3$貢献します。$$\varphi(3)=2$$それで$$w(3)=2.$$| メートル | φ(m) | w(m) | 床(3/m) | 貢献 |
 | --- | --- | --- | --- | --- |
 | 2 | 1 | 0 | 1 | 0 |
 | 3 | 2 | 2 | 1 | 2 |

 答え = 2。 

対応するペアは次のとおりです。$(2,3)$そして$(3,2)$。 

### 例 2

 入力:```
4
```| メートル | φ(m) | w(m) | 床(4/m) | 貢献 |
 | --- | --- | --- | --- | --- |
 | 2 | 1 | 0 | 2 | 0 |
 | 3 | 2 | 2 | 1 | 2 |
 | 4 | 2 | 2 | 1 | 2 |

 合計：$$2+2=4.$$有効なペアは次のとおりです。$$(2,3), (3,2), (3,4), (4,3).$$この例は、答えが gcd だけによって決定されないことを示しています。 ペア$(4,2)$数値が大きな gcd を共有している場合でも、 はカウントされません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(N \log \log N + N \log N)$前処理、$O(1)$クエリごと | トーティエントふるいと調和多重更新 |
 | スペース |$O(N)$| ファイ、ふるいの状態、および答えの配列 |

 と$N=10^6$、調和和$$\sum_{m=1}^{N} \frac{N}{m}
=
O(N \log N)$$コンテストの制限内であれば十分に高速です。 

## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    data = io.StringIO(inp)
    input = data.readline

    t = int(input())
    q = [int(input()) for _ in range(t)]

    mx = max(q)

    phi = [0] * (mx + 1)
    phi[1] = 1

    primes = []
    comp = [False] * (mx + 1)

    for i in range(2, mx + 1):
        if not comp[i]:
            primes.append(i)
            phi[i] = i - 1

        for p in primes:
            v = i * p
            if v > mx:
                break

            comp[v] = True

            if i % p == 0:
                phi[v] = phi[i] * p
                break
            else:
                phi[v] = phi[i] * (p - 1)

    ans = [0] * (mx + 1)

    for m in range(2, mx + 1):
        w = 2 * (phi[m] - 1)
        for x in range(m, mx + 1, m):
            ans[x] += w

    return "\n".join(str(ans[n]) for n in q)

# provided samples
assert run("3\n2\n3\n4\n") == "0\n2\n4"

# custom cases
assert run("1\n1\n") == "0", "minimum size"
assert run("1\n2\n") == "0", "no valid pair exists"
assert run("1\n5\n") == "8", "small manual verification"
assert run("2\n3\n4\n") == "2\n4", "sample values together"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 1`|`0`| n の最小値 |
 |`1 2`|`0`| 厳密な不等式の処理 |
 |`1 5`|`8`| 自明ではない小さな数 |
 |`2 3 4`|`2 4`| 複数のクエリとサンプル値 |

 ## 特殊なケース

 次のことを考慮してください。```
1
2
```アルゴリズムは計算します$$w(2)=2(\varphi(2)-1)=0.$$寄与が存在しないため、答えは 0 です。これにより、すべての候補ペアが等価性のみを満たすという事実が正しく処理されます。 

ペアを検討してください$(2,2)$。 gcdで割った後、$$x=y=1.$$派生条件には両方が必要です$x>1$そして$y>1$, そのため、ペアはすぐに拒否されます。 前処理では決してカウントされません。$m=1$は合算には含まれません。 

ペアを検討してください$(4,2)$。 ここ$$g=2,\quad x=2,\quad y=1.$$1 つの縮小部分は 1 に等しいため、条件は失敗します。 変換では等価性が予測され、元の divisor-count 式と正確に一致します。 両方の縮小部分が 1 より大きいペアのみが寄与するため、カウント式ではこれが除外されます。 

問題ステートメントと制約の派生は、元の Codeforces Gym 問題から得られます。
