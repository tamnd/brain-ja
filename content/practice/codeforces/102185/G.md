---
title: "CF 102185G - \u0413\u0438\u0440\u043b\u044f\u043d\u0434\u0430"
description: "ガーランドは 2 つのパラメータによって制御されます。 整数時間 (S) でスイッチがオンになった後、(A) 分間点灯し、(A) 分間消灯し、このサイクルを永久に繰り返します。 時間(S)前はまだガーランドのスイッチが入っていないので真っ暗です。"
date: "2026-08-20T00:39:32+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102185
codeforces_index: "G"
codeforces_contest_name: "Southern Russia Open Championship \u2013 ContestSFedU 2019, Team Final."
rating: 0
weight: 102185
solve_time_s: 306
verified: true
draft: false
---

[CF 102185G - \u0413\u0438\u0440\u043b\u044f\u043d\u0434\u0430](https://codeforces.com/problemset/problem/102185/G)

 **評価:** -
 **タグ:** -
 **解決時間:** 5 分 6 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 ガーランドは 2 つのパラメータによって制御されます。 整数時間 (S) でスイッチがオンになった後、(A) 分間点灯し、(A) 分間消灯し、このサイクルを永久に繰り返します。 時間(S)前はまだガーランドのスイッチが入っていないので真っ暗です。 

休日は間隔 ([0,T]) を占めます。 この間隔中、少なくとも半分の時間は点灯する必要があります。 同時に、おじいさんはいくつかのばらばらな間隔 ([L_i,R_i]) の間在宅しているため、花輪の点灯部分との交差部分の全長を最小限に抑えたいと考えています。 

答えは、避けられない最小のオーバーラップ、選択された値 (A)、およびスイッチング時間 (S) で構成されます。 等しいオーバーラップの場合は小さい方 (A) が優先され、等しい (A) の場合はスイッチング時間が遅い方が優先されます。 

すべてのエンドポイントは整数であるため、各分 ([t,t+1)) を 1 つの離散位置とみなすことができます。 祖父区間 ([L,R]) は、正確に位置 (L,L+1,\ldots,R-1) を占めます。 

境界 (T\le 5000) が主な手がかりです。 (O(T^2)) の解決策は現実的ですが、すべてのペア ((A,S)) についてすべて (T) 分をスキャンするアプローチはコストがかかりすぎます。 祖父区間の数は最大 (T/2) であるため、(O(NT)) コンポーネントも (O(T^2)) 解の一部として受け入れられます。 

一見正しい実装を簡単に破ってしまう境界例がいくつかあります。 1 つ目は、ガーランドが電源をオンにする前に定期的に実行されないことです。 例えば、```
4 0
```答えがあります```
0 1 1
```(A=1) と (S=1) の場合、花輪は休日のちょうど半分である ([1,2)) と ([3,4))] の間に点灯するためです。 (S) の前にパターンを周期的として扱うと、(S=1) が (S=-1) と同等であると誤ってみなされてしまいます。 

2 番目のエッジケースは、休暇前の切り替え時期です。 のために```
8 2
1 3
5 7
```答えは```
0 2 -1
```(A=2) と (S=-1) の場合、ガーランドは ([0,1))、([3,5))、および ([7,8))] の間点灯し、両方の祖父間隔を回避しながら正確に 4 分間点灯します。 (S\ge0) だけを考慮した解法では、最適解が得られません。 

3 番目のエッジ ケースは、スイッチング時間がちょうどグランド インターバルの開始時に発生します。 のために```
4 1
1 2
```答えは```
0 2 2
```ガーランドは点灯しており ([2,4))、2 分間点灯し、重複はありません。 ここでよくある間違いは、エンドポイント (2) を区間 ([1,2]) に属するものとして扱うことです。 これらは時間間隔であるため、それらの交差の長さはゼロになります。 

最後に、(A) は (T) を超えて考慮する必要はありません。 (A>T) の場合、休日と交差できる点灯セグメントは最大 1 つです。 このようなセグメントは、プレフィックス、サフィックス、または休日全体のいずれかであり、同じ点灯セグメントは (A=T) で再現できます。 小さい (A) が同点なので、(A\le T) を考慮するだけで十分です。 

## アプローチ

 直接的なブルート フォースは概念的には単純です。 (1) から (T) までのすべての (A)、関連するすべての整数スイッチング時間 (S) を試し、ガーランドを分ごとにシミュレートし、その点灯時間をカウントし、グランド インターバルに属する点灯分数を個別にカウントできます。 すべての候補が正確にチェックされるため、方法は正しいです。 

固定 (A) の場合、ガーランドがすでにオンになった後は (S) を (2A) だけシフトしても周期パターンは変わらないため、負のスイッチング時間は ([-2A,-1]) からの代表のみが必要です。 非負 (S) の場合、残り時間はせいぜい (T-S) 分であるため、実行可能な解は (S>\lfloor T/2\rfloor) を持つことはできません。 これにより、1 つ (A) に対して (O(2A+T)) 個の開始候補が得られます。 すべての候補者がすべて (T) 分間スキャンした場合、最悪のケースは約

 [
 T\left(\sum_{A=1}^{T} (2A+T/2)\right)
 】

 これは、(T=5000) での約 (1.9\cdot10^{11}) 回の操作です。 これは 1 秒の制限には遠く及ばない。 

重要な観察は、固定 (A) の場合、無限の周期パターンは (S\bmod 2A) にのみ依存するということです。 すべての剰余モジュロ (2A) について、その剰余が休日分と祖父分のうち何分あるかがわかっている場合、(S) を 1 つ移動すると、点灯した半サイクルから 1 つの剰余クラスが削除され、別の剰余クラスが追加されるだけです。 したがって、完全な相関値は (O(1)) で更新できます。 

複雑な問題が 1 つあります。 (S\ge0) の場合、周期式は (S) より前の時間を誤って示します。 これは、プレフィックス ([0,S)) に含まれる点灯ウェイトの量を維持することによって処理されます。 (S) が 1 つ増加すると、点灯した剰余クラスのセットが 1 つシフトするため、既に処理されたプレフィックスに対して蓄積された剰余合計を使用して、プレフィックス寄与も (O(1)) で更新できます。 

(2A>T) についてはさらに簡略化されています。 期間 (2A) は休日全体よりも長いため、休日には点灯セグメントが 1 つだけ含まれます。 その (A) の問題は、単純な区間交差問題になり、祖父占有のプレフィックス合計から直接評価できます。 

結果として得られるアプローチは (O(T^2)) であり、(O(T)) 個のメモリのみを使用します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(T^3)) | (O(T)) | 遅すぎる |
 | 最適 | (O(T^2)) | (O(T)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. アレイを構築する`g[t]`for (0\le t<T)、ここで`g[t]=1`ちょうど、祖父が数分間 ([t,t+1)] の間に家にいるときです。 また、そのプレフィックスの合計も構築します。 これにより、(O(1)) 内の通常の区間の祖父の重複を見つけることができます。 
2. (1) から (T) までのすべての (A) を検討します。 (T) を超える値は不要です。そのような (A) によって生成される実行可能なパターンは (A=T) で再現できるためです。 
3. (2A>T) の場合、期間は休日より長くなります。 ([0,T])内の点灯部分が単一区間です。 実行可能なすべてのスイッチング時間 (S) について、([S,S+A)) と ([0,T]) を交差させ、その長さを計算し、プレフィックスの合計との祖父オーバーラップを計算します。 負の値 (S) の場合、ガーランドは休暇前にすでにオンになっているため、左の端点はゼロにクリップされます。 
4. (2A\le T) については、(P=2A) とします。 建てる`cnt[r]`、時間が (r\pmod P) と一致する休日の分数、および`home[r]`、同じ剰余を持つ祖父の分数。 残基 (S\bmod P) で始まる周期パターンの点灯部分は、正確に (A) の連続した残基で構成されます。 
5. (S=0) の周期的点灯継続時間と周期的祖父オーバーラップを構築します。 これらは剰余 (0,\ldots,A-1) の合計です。 (S) が (S) から (S+1) に変化すると、残差 (S\bmod P) が点灯した半分から出て、残差 ((S+A)\bmod P) がそこに入ります。 したがって、両方の合計は、配列に 2 回アクセスするだけで変化します。 
6. 代表値 (S=-P+1,\ldots,-1) を使用して負のスイッチング時間を列挙します。 マイナスのスタートはガーランドがすでに始まっていることを意味するため、定期的な計算が休日の実際の動作となります。 代表的な（-P）は（0）と同じ休日パターンですが、早いのでスキップできます。 
7. 非負のスイッチング時間を処理します (S=0,\ldots,\lfloor T/2\rfloor)。 周期的な合計は、(S) より前に広がる仮想パターンを記述しているため、([0,S)) にあるそのパターンの部分を減算します。 このプレフィックスの貢献を段階的に維持します。 (S) から (S+1) に移動すると、古いプレフィックスは剰余 (S\bmod P) を失い、剰余 ((S+A)\bmod P) を獲得します。また、新しく追加された分 (S) は、相対時間がゼロであるため、新しいパターンでは点灯しません。 
8. すべての候補者に対して、次のことを要求します`2 * lit >= T`。 可能であれば、その祖父の重複と現在の答えを比較してください。 比較では、まずオーバーラップを最小化し、次に (A)、次に最大化します (S)。 
9. 最良のトリプルを出力します。 

周期部分の背後にある不変条件は次のとおりです。`period_lit`そして`period_home`現在のスイッチング位相を伴う無限周期パターンを休日期間全体に適用することによって得られる合計と常に等しくなります。 プレフィックス変数は、実際の切り替え時間の数分前に制限された同じ合計に常に等しくなります。 それらの違いは、スイッチを入れる前は暗い本物のガーランドの動作とまったく同じです。 関連するすべてのフェーズが列挙され、関連するすべての非負のスイッチング時間が処理されるため、最適な実現可能な候補が確実に見つかることが保証されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve(data=None):
    if data is None:
        T, N = map(int, input().split())
        intervals = [tuple(map(int, input().split())) for _ in range(N)]
    else:
        it = iter(map(int, data.split()))
        T = next(it)
        N = next(it)
        intervals = [(next(it), next(it)) for _ in range(N)]

    # g[t] = 1 iff the grandfather is home during minute [t, t+1).
    diff_g = [0] * (T + 1)
    for l, r in intervals:
        diff_g[l] += 1
        diff_g[r] -= 1

    g = [0] * T
    cur = 0
    for t in range(T):
        cur += diff_g[t]
        g[t] = 1 if cur else 0

    # Prefix sum of grandfather occupancy.
    pref_g = [0] * (T + 1)
    for t in range(T):
        pref_g[t + 1] = pref_g[t] + g[t]

    half_floor = T // 2
    half_ceil = (T + 1) // 2

    best_cost = T + 1
    best_a = 0
    best_s = 0

    def consider(cost, a, s):
        nonlocal best_cost, best_a, best_s
        if cost < best_cost:
            best_cost = cost
            best_a = a
            best_s = s
        elif cost == best_cost and a == best_a and s > best_s:
            best_s = s

    for A in range(1, T + 1):
        # If the period is longer than the holiday, there can be
        # only one lit segment inside [0, T].
        if 2 * A > T:
            lo = half_ceil - A

            for s in range(lo, half_floor + 1):
                left = max(0, s)
                right = min(T, s + A)

                lit = right - left
                if 2 * lit < T:
                    continue

                cost = pref_g[right] - pref_g[left]
                consider(cost, A, s)

            continue

        P = 2 * A

        # home[r] = number of grandfather minutes t with t % P == r.
        #
        # Each grandfather interval contributes q to every residue,
        # plus one to a cyclic range of length rem.
        home_diff = [0] * (P + 1)
        base = 0

        for l, r in intervals:
            length = r - l
            q, rem = divmod(length, P)
            base += q

            if rem:
                start = l % P
                end = start + rem

                if end <= P:
                    home_diff[start] += 1
                    home_diff[end] -= 1
                else:
                    home_diff[start] += 1
                    home_diff[P] -= 1
                    home_diff[0] += 1
                    home_diff[end - P] -= 1

        home = [0] * P
        cur = 0
        for r in range(P):
            cur += home_diff[r]
            home[r] = cur + base

        # cnt[r] = number of holiday minutes with t % P == r.
        q, rem = divmod(T, P)
        cnt = [q + (1 if r < rem else 0) for r in range(P)]

        # Phase S = 0, whose lit residues are [0, A).
        period_lit = sum(cnt[:A])
        period_home = sum(home[:A])

        # Negative starts. For s < 0 the periodic pattern is real,
        # because the garland has already been switched on.
        cur_lit = period_lit
        cur_home = period_home

        for r in range(1, P):
            cur_lit += cnt[(r + A - 1) % P] - cnt[r - 1]
            cur_home += home[(r + A - 1) % P] - home[r - 1]

            s = -P + r

            if 2 * cur_lit >= T:
                consider(cur_home, A, s)

        # Nonnegative starts.
        cur_lit = period_lit
        cur_home = period_home

        # pref_cnt[r] and pref_home[r] contain the already processed
        # prefix [0, s), grouped by residue modulo P.
        pref_cnt = [0] * P
        pref_home = [0] * P

        # s = 0: nothing has to be removed from the periodic pattern.
        if 2 * cur_lit >= T:
            consider(cur_home, A, 0)

        for s in range(half_floor):
            r1 = s % P
            r2 = (s + A) % P

            # Shift the infinite periodic phase by one.
            cur_lit += cnt[r2] - cnt[r1]
            cur_home += home[r2] - home[r1]

            # Shift the part lying before the actual switch time.
            removed_lit = pref_cnt[r2] - pref_cnt[r1]
            removed_home = pref_home[r2] - pref_home[r1]

            pref_cnt[r1] += 1
            pref_home[r1] += g[s]

            actual_lit = cur_lit - removed_lit
            actual_home = cur_home - removed_home

            ns = s + 1

            if 2 * actual_lit >= T:
                consider(actual_home, A, ns)

    return f"{best_cost} {best_a} {best_s}"

if __name__ == "__main__":
    sys.stdout.write(solve() + "\n")
```最初の部分では、分レベルの祖父占有率とそのプレフィックスの合計を構築します。 すべての間隔のエンドポイントは整数であるため、この表現は正確であり、連続時間の近似ではありません。 

支店`2 * A > T`期間（2A）が休日全体よりも長いことを利用しています。 休日内に 2 番目に点灯するセグメントは存在できないため、ガーランドは 1 つの間隔で表されます。 候補範囲は次から始まります`half_ceil - A`これは、点灯している交差点が休日の半分に達する可能性がある最も早い負の切り替え時間です。 

のために`2 * A <= T`、コードは剰余カウントを法として構築します`P = 2 * A`。 祖父剰余配列は、差分配列範囲の更新によって構築されます。 長さの間隔`q * P + rem`貢献する`q`すべての残基と 1 つの追加単位に`rem`連続した環状残基。 これにより、(A) のすべての値についてすべて (T) 分をスキャンする必要がなくなります。 

2 つの変数`period_lit`そして`period_home`仮想の無限パターンを説明します。 ガーランドは時間ゼロより前にすでにオンになっているため、ネガティブスタート ループはそれらを直接使用できます。 

ポジティブスタートループはより微妙です。`pref_cnt`そして`pref_home`実際のスイッチの前にある周期的パターンの部分を記述します。 分を挿入する前に更新が行われます`s`これらの配列に。 その順序は重要です。`s`新しい切り替え時刻より前ではない`s+1`、ただし、相対時間 0 の新しいフェーズによって点灯することもありません。 

実現可能性テストでは、`2 * lit >= T`浮動小数点除算の代わりに。 これにより、(T) の奇数の値が正確に処理されます。 たとえば、(T=5) の場合、少なくとも 3 分間の点灯が必要です。 

タイブレーク コードは、(A) を昇順に繰り返すことに依存します。 コストが等しい場合、より大きな (A) がすでに保存されているソリューションを置き換えることはありません。 同じ (A) の場合、候補は、切り替え時間が遅い場合にのみ、現在の候補を置き換えます。 

## 実用的な例

 ### サンプル 1

 入力は```
10 2
1 4
7 10
```(A=1) を考えてみましょう。 周期は(2)なので、1分おきに周期的に点灯します。 

| 開始(S) | 周期点灯 | 削除されたプレフィックスが点灯 | 実際の点灯 | 祖父の重なり | 写真 実現可能 |
 | --- | --- | --- | --- | --- | --- |
 | -1 | 5 | 0 | 5 | 4 | はい |
 | 0 | 5 | 0 | 5 | 2 | はい |
 | 1 | 5 | 0 | 5 | 4 | はい |
 | 2 | 5 | 1 | 4 | 2 | いいえ |

 (A=1) の最良の候補は (S=0) で、重複 (2) があります。 (A) を大きくしても答えは改善されないため、最終結果は次のようになります。```
2 1 0
```ここで重要なのは、(S=-1) と (S=1) の違いです。 それらの周期的な段階は関連していますが、実際のガーランドは実際の切り替え時間の前には暗くなります。 接頭辞の減算によってその区別が得られます。 

### サンプル 2

 入力は```
8 2
1 3
5 7
```(A=2) の場合、周期は (4) になります。 負の位相 (S=-1) は分 (0、3、4、7)、つまりちょうど 4 分を点灯します。 

| 開始(S) | 周期点灯 | 削除されたプレフィックスが点灯 | 実際の点灯 | 祖父の重なり | 写真 実現可能 |
 | --- | --- | --- | --- | --- | --- |
 | -1 | 4 | 0 | 4 | 0 | はい |
 | 0 | 4 | 0 | 4 | 2 | はい |
 | 1 | 4 | 0 | 4 | 4 | はい |
 | 2 | 4 | 1 | 3 | 2 | いいえ |

 ネガティブスタートは、休日のちょうど半分を点灯しながら、両方の祖父の間隔を完全に回避します。 したがって、答えは次のとおりです```
0 2 -1
```この例では、正に負のスイッチング時間を非負の位相代表に置き換えるのではなく、真に負のスイッチング時間を保存する必要があるアルゴリズムの部分を実行します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(T^2)) | それぞれの (A) について、剰余の構築とすべての候補の更新には (O(N+T)) および (N\le T/2) がかかります。 |
 | スペース | (O(T)) | すべての剰余配列は、周期分岐内で最大 (2A\le T) の長さを持ちます。 |

 (T) の最大値は (5000) のみであるため、(O(T^2)) は、アルゴリズムの主要な部分で約 2500 万回のスケールの操作を意味します。 このソリューションでは、個々の切り替え時間ごとにすべて (T) 分をスキャンすることはありません。これが、受け入れられているアプローチとブルート フォースの違いです。 

## テストケース```python
import io
import sys

def solve(data=None):
    if data is None:
        T, N = map(int, input().split())
        intervals = [tuple(map(int, input().split())) for _ in range(N)]
    else:
        it = iter(map(int, data.split()))
        T = next(it)
        N = next(it)
        intervals = [(next(it), next(it)) for _ in range(N)]

    diff_g = [0] * (T + 1)
    for l, r in intervals:
        diff_g[l] += 1
        diff_g[r] -= 1

    g = [0] * T
    cur = 0
    for t in range(T):
        cur += diff_g[t]
        g[t] = 1 if cur else 0

    pref_g = [0] * (T + 1)
    for t in range(T):
        pref_g[t + 1] = pref_g[t] + g[t]

    half_floor = T // 2
    half_ceil = (T + 1) // 2

    best_cost = T + 1
    best_a = 0
    best_s = 0

    def consider(cost, a, s):
        nonlocal best_cost, best_a, best_s
        if cost < best_cost:
            best_cost = cost
            best_a = a
            best_s = s
        elif cost == best_cost and a == best_a and s > best_s:
            best_s = s

    for A in range(1, T + 1):
        if 2 * A > T:
            lo = half_ceil - A

            for s in range(lo, half_floor + 1):
                left = max(0, s)
                right = min(T, s + A)
                lit = right - left

                if 2 * lit < T:
                    continue

                cost = pref_g[right] - pref_g[left]
                consider(cost, A, s)

            continue

        P = 2 * A

        home_diff = [0] * (P + 1)
        base = 0

        for l, r in intervals:
            length = r - l
            q, rem = divmod(length, P)
            base += q

            if rem:
                start = l % P
                end = start + rem

                if end <= P:
                    home_diff[start] += 1
                    home_diff[end] -= 1
                else:
                    home_diff[start] += 1
                    home_diff[P] -= 1
                    home_diff[0] += 1
                    home_diff[end - P] -= 1

        home = [0] * P
        cur = 0
        for r in range(P):
            cur += home_diff[r]
            home[r] = cur + base

        q, rem = divmod(T, P)
        cnt = [q + (1 if r < rem else 0) for r in range(P)]

        period_lit = sum(cnt[:A])
        period_home = sum(home[:A])

        cur_lit = period_lit
        cur_home = period_home

        for r in range(1, P):
            cur_lit += cnt[(r + A - 1) % P] - cnt[r - 1]
            cur_home += home[(r + A - 1) % P] - home[r - 1]

            s = -P + r
            if 2 * cur_lit >= T:
                consider(cur_home, A, s)

        cur_lit = period_lit
        cur_home = period_home

        pref_cnt = [0] * P
        pref_home = [0] * P

        if 2 * cur_lit >= T:
            consider(cur_home, A, 0)

        for s in range(half_floor):
            r1 = s % P
            r2 = (s + A) % P

            cur_lit += cnt[r2] - cnt[r1]
            cur_home += home[r2] - home[r1]

            removed_lit = pref_cnt[r2] - pref_cnt[r1]
            removed_home = pref_home[r2] - pref_home[r1]

            pref_cnt[r1] += 1
            pref_home[r1] += g[s]

            actual_lit = cur_lit - removed_lit
            actual_home = cur_home - removed_home

            ns = s + 1

            if 2 * actual_lit >= T:
                consider(actual_home, A, ns)

    return f"{best_cost} {best_a} {best_s}"

def run(inp: str) -> str:
    return solve(inp)

assert run("""10 2
1 4
7 10
""") == "2 1 0", "sample 1"

assert run("""8 2
1 3
5 7
""") == "0 2 -1", "sample 2"

assert run("""6 1
0 4
""") == "1 3 3", "sample 3"

assert run("""5 1
0 5
""") == "3 1 0", "sample 4"

assert run("""4 0
""") == "0 1 1", "sample 5"

assert run("""1 0
""") == "0 1 0", "minimum-size input"

assert run("""4 1
1 2
""") == "0 2 2", "boundary endpoint"

assert run("""6 2
0 2
4 6
""") == "2 1 1", "equal intervals"

assert run("""5000 0
""") == "0 1 1", "maximum T"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 0`|`0 1 0`| 可能な限り最小の休日と切り替え時間の境界 |
 |`4 1 / 1 2`|`0 2 2`| 正確な間隔のエンドポイントと祖父が去った後の切り替え |
 |`6 2 / 0 2 / 4 6`|`2 1 1`| 等しい長さの祖父インターバルと最後のスタートによるタイブレーク |
 |`5000 0`|`0 1 1`| 休暇の最長期間と遅刻開始のタイブレーク |

 ## 特殊なケース

 最低限の入力に関しては```
1 0
```唯一の分は、1 分間の休日の少なくとも半分、つまり丸 1 分間点灯する必要があります。 (A=1) の場合、(S=0) でのスイッチングは ([0,1)) を点灯し、祖父オーバーラップがゼロになります。 (S=1) に切り替えると、休日には何も点灯しないため、答えは次のようになります。```
0 1 0
```アルゴリズムは直ちに (2A>T) 分岐に到達し、テスト (S=0) し、点灯時間がゼロであるため拒否 (S=1) されます。 

マイナススタートの場合```
8 2
1 3
5 7
```関連する候補は (A=2,S=-1) です。 (2A=T) なので、周期的分岐が使用されます。 このフェーズでは、(4) を法として残基 (0) と (3) が点灯し、時間 (0、3、4、7) で 4 分間点灯します。 どれも祖父区間に属さないため、コストはゼロです。 逆相列挙では、この候補を直接見つけます。 

エンドポイントの場合```
4 1
1 2
```候補 (A=2,S=2) は点灯間隔 ([2,4)] を生成します。 祖父区間 ([1,2)) との交差部分は空ですが、休日にはちょうど 2 つの点灯時間が含まれます。 大周期ブランチの計算`left=2`、`right=4`、コストを取得します`pref_g[4] - pref_g[2] = 0`。 

等間隔の場合```
6 2
0 2
4 6
```最良の解決策は (A=1,S=1) です。 ガーランドは 1、3、5 分間点灯し、ちょうど 3 分間点灯し、半休日の条件を満たします。 分 (1) と (5) の間に祖父の間隔と交差するため、コストは 2 です。 (S=0) での切り替えでもコスト 2 が与えられますが、タイブレーク ルールにより、後の開始 (S=1) が選択されます。 

休暇の最長期間について```
5000 0
```おじいさんがいないので目的はゼロです。 可能な最小の (A) は (1) です。 (S=1) の場合、ガーランドは (1) から (4999) まで 1 分おきに点灯し、正確に (2500) 分間点灯します。 開始が遅くなると、点灯するのは休日の半分未満になります。 結果として得られる答えは、```
0 1 1
```この最後のケースでは、多くの構成のコストがゼロである場合にもタイブレーク ルールが適用されます。つまり、アルゴリズムは (A=1) を維持し、その (A) に対して実行可能な最新の切り替え時間を選択します。
