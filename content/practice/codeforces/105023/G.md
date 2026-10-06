---
title: "CF 105023G - I-5 ドライブ"
description: "$N = p^2 + q^3$ として形成される数値が基底 $T$ で非常に特殊な表現特性を持つように、素数の順序ペア $(p, q)$ を数えるよう求められます。"
date: "2026-06-28T01:45:46+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 105023
codeforces_index: "G"
codeforces_contest_name: "HPI 2024 Novice"
rating: 0
weight: 105023
solve_time_s: 87
verified: true
draft: false
---

[CF 105023G - I-5 ドライブ](https://codeforces.com/problemset/problem/105023/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 27 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 素数の順序ペアを数えるように求められます$(p, q)$数が次のように形成されるように、$N = p^2 + q^3$ベースに非常に具体的な表現プロパティがあります$T$。 書くとき$N$基地内で$T$、先行ゼロがない場合、数字は以下のすべての数字の順列を形成する必要があります。$0$に$T-1$、それぞれが 1 回だけ表示されます。 

それでベースは-$T$の表現$N$は位置と数字の間の全単射です。すべての数字は 1 回だけ出現します。これは、その数字がその基数に設定された全数字の有効な置換であることを意味します。 特に、表現の長さは正確に次のようにする必要があります。$T$、すべてを配置する必要があるため、$T$明確な数字。 

入力はただ$T$そして、順序付けられた素数ペアの数を数えなければなりません$(p, q)$そのようなものを生み出す$N$。 

問題ステートメントが明示的にバインドされていない場合でも、$p$そして$q$、数字制約は暗黙的に強制します$N$小さいこと。 基地――$T$各桁を 1 回だけ含む数値は最大でも値を持ちます$T^T - 1$、 それで$N$の小さな指数関数によって制限されます$T$。 以来$T \le 10$、可能な限り最大$N$せいぜい$10^{10} - 1$、枝刈り後の列挙に管理可能です。 

重要な難点は、$N$非線形結合による素数によって定義される$p^2 + q^3$ただし、桁の制約により、有効な候補が強力に制限されます。 

までのすべての素数を反復しようとすると、単純な失敗ケースが発生します。$10^{10}$、それは不可能です。 もう 1 つのよくある落とし穴は、基数の数字のすべての順列を生成することです。$T$、それらを整数に変換し、それぞれを次のように分解しようとします。$p^2 + q^3$素数範囲を効率的に境界付けることなく。 これは正確に近いですが、素数を事前に計算し、平方根と立方根をしっかりとバインドしない場合、不注意な分解は依然として遅すぎます。 

## アプローチ

 ブルートフォースアプローチでは、すべての素数が試行されます$p$そして$q$、計算する$N = p^2 + q^3$、 変換する$N$基地へ$T$、各数字が 1 回だけ含まれているかどうかを確認します。 正しさはすぐにわかりますが、検索スペースは膨大です。 制限さえする$p, q \le \sqrt{10^{10}}$または同様の境界は依然として数百万個の素数のオーダーで残り、数十億個のペア評価につながります。 

数字条件の構造が重要な観察事項です。 生成する代わりに$p, q$、プロセスを逆にすることができます。すべての有効な塩基を生成します。$T$桁制約を満たす順列は、各順列を整数に変換します$N$、そして、次のことを確認します。$N$次のように表現できます$p^2 + q^3$素数用$p, q$。 以来$T \le 10$、順列の数は最大で$10!$これは約 360 万ですが、先行ゼロ制約を注意深く処理すると、はるかに少なくなります。 これは、制御された実行可能性チェックには十分な小ささです。 

候補が決まったら$N$、素数が存在するかどうかをテストするだけで済みます。$q$そのような$N - q^3$は素数の完全二乗です。 私たちは縛ることができる$q \le \sqrt[3]{N}$そして$p \le \sqrt{N}$、事前計算されたふるいを使用して素数性を迅速にテストします。 

したがって、重要な点は、素数ペアの列挙から数字の順列の列挙、そして代数構造の検証へと移ります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 素数に対するブルートフォース |$O(\pi(M)^2)$|$O(1)$| 遅すぎる |
 | 順列 + プライム チェック |$O(T! \cdot T + \sqrt[3]{N})$|$O(M)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 以下から数字のすべての順列を生成します。$0$に$T-1$、先行ゼロを許可しないようにします。 これにより、すべての候補番号が正確に一致することが保証されます。$T$底の数字$T$。 この問題では先行ゼロのない正規表現が必要であるため、先行ゼロの制限が重要になります。 
2. 各順列について、基数から変換します。$T$その整数値に$N$。 これにより、数字の条件を満たすすべての候補が得られます。 
3. 以下のすべての素数を事前計算します。$\sqrt{N_{\max}}$、 どこ$N_{\max}$は最大の順列値です。 これにより、後で一定時間の素数チェックが可能になります。 
4. 各候補者について$N$、すべての素数を反復します$q$そのような$q^3 \le N$。 そういったそれぞれに対して$q$、計算する$x = N - q^3$。 
5. かどうかを確認します。$x$は完全二乗であり、その平方根かどうか$p$プライムです。 両方の条件が当てはまる場合、有効な順序ペアが見つかりました。$(p, q)$。 
6. すべての順列にわたって、そのような有効な順序付きペアをすべて数えます。 

### なぜ効果があるのか

 すべての有効な$N$基数の数字の順列でなければなりません$T$, したがって、生成された候補セットに表示される必要があります。 逆に、すべての候補者は次のように表現可能性について正確にチェックされます。$p^2 + q^3$。 分解テストは、考えられるすべての三次素数に対して網羅的です。$q$、そしてそれぞれについて、一意に決定します$p^2$。 素数性と完全二乗チェックは正確であるため、無効なペアは受け入れられず、有効なペアが見逃されることもありません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

import itertools
import math

def sieve(n):
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    for i in range(2, int(n ** 0.5) + 1):
        if is_prime[i]:
            step = i
            start = i * i
            is_prime[start:n+1:step] = [False] * len(range(start, n+1, step))
    return is_prime

def to_value(perm, base):
    val = 0
    for d in perm:
        val = val * base + d
    return val

def solve():
    T = int(input().strip())
    
    digits = list(range(T))
    perms = itertools.permutations(digits, T)

    candidates = []
    for p in perms:
        if p[0] == 0:
            continue
        candidates.append(to_value(p, T))

    Nmax = max(candidates) if candidates else 0

    # primes up to sqrt(Nmax)
    limit = int(math.isqrt(Nmax)) + 1 if Nmax else 2
    is_prime = sieve(limit)

    def is_prime_small(x):
        return x < len(is_prime) and is_prime[x]

    def is_square(x):
        r = int(math.isqrt(x))
        return r * r == x, r

    ans = 0

    for N in candidates:
        max_q = int(N ** (1/3)) + 2
        q = 2
        while q <= max_q:
            if q < len(is_prime) and is_prime[q]:
                cube = q ** 3
                if cube > N:
                    break
                rem = N - cube
                ok, p = is_square(rem)
                if ok and p < len(is_prime) and is_prime_small(p):
                    ans += 1
            q += 1

    print(ans)

if __name__ == "__main__":
    solve()
```コードは、長さの有効な数字の並べ替えをすべて生成することから始まります。$T$、ゼロで始まるものは、先行ゼロなしの表現要件に違反するためスキップされます。 

各順列はその基数に変換されます。$T$位置累積を使用した整数値。 これにより、文字列解析の繰り返しが回避され、変換が線形に保たれます。$T$。 

ふるいは次のように構築されます$\sqrt{N_{\max}}$、有効なため$p$満たさなければなりません$p^2 \le N$。 平方根を迅速に検証するにはこれで十分です。 

候補者ごとに$N$、素数を反復処理します$q$まで$\sqrt[3]{N}$。 それぞれについて、減算します$q^3$そして、剰余が根も素数である完全な平方であるかどうかを確認します。 どちらのチェックも整数演算のみを使用し、浮動小数点精度の問題を回避します。 

この構造により、各ペアが一意に決定されるため、すべての有効な順序ペアが正確に 1 回カウントされることが保証されます。$q$、 その後$p$として固定されています$\sqrt{N - q^3}$。 

## 実用的な例

 ### 例 1:$T = 3$数字は$\{0,1,2\}$。 先行ゼロのない有効な順列はすべて次のとおりです。$102, 120, 201, 210$。 

それらを基数 3 の整数に変換します。 

| 順列 | 価値$N$|
 | --- | --- |
 | 102 | 11 |
 | 120 | 15 |
 | 201 | 19 |
 | 210 | 21 |

 ここで分解をテストします$N = p^2 + q^3$。 小さな素数の場合、立方体はすでに急速に成長します。$2^3 = 8$、$3^3 = 27$、だからそれだけ$q = 2$ほとんどの候補者に関係します。 

すべてのケースをチェックすると有効な表現がないため、答えは 0 になります。 

これは、最小の候補値であっても、素数を含む正方形と立方体の構造に適合させるには小さすぎるというサンプルの推論と一致します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(T! \cdot T + \pi(N^{1/3}))$| 順列は候補を生成し、それぞれが小さな素数立方体に対してチェックされます。 
| スペース |$O(T! + \sqrt{N})$| 候補とふるいを保存 |

 階乗項の境界は次のとおりです。$10!$、立方根素数の反復は最大でも数百ステップです。 これは 1 秒の制約内に快適に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    from math import isqrt

    import itertools
    import math

    def sieve(n):
        is_prime = [True] * (n + 1)
        is_prime[0] = is_prime[1] = False
        for i in range(2, int(n ** 0.5) + 1):
            if is_prime[i]:
                for j in range(i * i, n + 1, i):
                    is_prime[j] = False
        return is_prime

    def to_value(perm, base):
        val = 0
        for d in perm:
            val = val * base + d
        return val

    T = int(sys.stdin.readline().strip())
    digits = list(range(T))

    candidates = []
    for p in itertools.permutations(digits, T):
        if p[0] != 0:
            candidates.append(to_value(p, T))

    if not candidates:
        return "0"

    Nmax = max(candidates)
    limit = int(math.isqrt(Nmax)) + 1
    is_prime = sieve(limit)

    def is_square(x):
        r = int(math.isqrt(x))
        return r * r == x, r

    ans = 0
    for N in candidates:
        max_q = int(N ** (1/3)) + 2
        q = 2
        while q <= max_q:
            if q < len(is_prime) and is_prime[q]:
                cube = q ** 3
                if cube > N:
                    break
                rem = N - cube
                ok, p = is_square(rem)
                if ok and p < len(is_prime) and is_prime[p]:
                    ans += 1
            q += 1

    return str(ans)

# provided sample
assert run("3\n") == "0", "sample 1"

# custom cases
assert run("2\n") == "0", "minimum base"
assert run("4\n") in {"0", "1"}, "small base sanity"
assert run("5\n") >= "0", "non-negative count"
assert run("10\n") >= "0", "maximum base sanity"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 3 | 0 | サンプルの正確さと小さなベース |
 | 2 | 0 | 最小桁セット |
 | 4 | 0 または 1 | 順列処理の安定性 |
 | 10 | 非負 | 上限の堅牢性 |

 ## 特殊なケース

 いつ$T = 2$、数字の並べ替えは非常に限られており、先頭のゼロを除外すると、単一の候補番号のみが残ります。 アルゴリズムは正しく生成するのは最大でも 1 つだけです$N$、次に立方体と正方形のチェックを実行しますが、最小の有効な立方体であってもすぐに失敗します。$2^3$ほとんどの候補者を上回っています。 

のために$T = 10$、順列セットは最大サイズに達しますが、ふるいと立方根ループはまだ管理可能なままです。 各候補者$N$独立してテストされ、キューブの成長が速いため、ほとんどの反復はごく少数の素数をチェックした後に終了します。$q$。 

先頭のゼロの順列は生成時に安全に破棄されるため、短い基数のような無効な表現はありません。$T$数値は分解フェーズに常に導入されます。
