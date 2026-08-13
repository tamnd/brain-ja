---
title: "CF 102428H - 保留しますか? 続行しますか?"
description: "すべての決定点で、キャトリンは永久スコア C と一時的なターン合計 X を持ちます。ホスト側は永久スコア H を持ちます。キャトリンは現在のターン合計をバンクするか、もう一度サイコロを振るかを選択する必要があります。"
date: "2026-08-12T07:18:44+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102428
codeforces_index: "H"
codeforces_contest_name: "2019-2020 ACM-ICPC Latin American Regional Programming Contest"
rating: 0
weight: 102428
solve_time_s: 175
verified: true
draft: false
---

[CF 102428H - 保留または続行?](https://codeforces.com/problemset/problem/102428/H)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 55 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 すべての決定ポイントで、Catelyn には永続的なスコアが与えられます`C`そして一時的なターン合計`X`。 ホストには永続的なスコアがあります`H`。 キャトリンは、現在のターンの合計をバンクするか、もう一度サイコロを振るかのどちらかを選択する必要があります。 目的は、即時スコアを最大化することではなく、両方のプレイヤーが最適な決定を下したと仮定して、キャトリンが最終的にホスターよりも先にちょうど 75 に到達する確率を高めることです。 

キャトリンがホールドした場合、彼女のスコアは次のようになります。`C + X`。 それがちょうど 75 であれば、彼女は即座に勝ちます。 75 未満の場合、ホスターが次のターンを取得します。 キャトリンが継続した場合、1 のロールはどちらの永久スコアも変更せずに直ちにターンを通過します。 2から6までの目は増加します`X`。 新しい合計が`C + X`75 を超えると、そのターンは得点されずに失われます。 

プレイヤーが 75 に到達するまでに関連する永続スコアは最大 74 であり、ターンの合計も 75 までの距離によって制限されます。 公式問題ページには、制限時間 5 秒とメモリ制限 1024 MB が記載されています。 これらの境界は絶対的な観点からは寛大ですが、確率的ゲームには 2 人のプレイヤー間に循環的な依存関係があるため、単純な再帰検索だけでは十分ではありません。 75 という小さな目標値が、スコア ペアに対する完全な動的なプログラムが可能な主な理由です。 

最初の微妙なケースは正確にヒットします。 例えば、```
1
73 0 2
```生産しなければならない`H`。 ホールディングのスコアはちょうど 75 で、すぐに勝ちます。 ホールドを単に「対戦相手にターンを渡す」ものとして扱う不用意な実装は、誤ってこの勝ちのトランジションを失うことになります。 

2 番目の微妙なケースは 74 に達しています。考えてみましょう。```
1
72 0 2
```キャトリンがホールドした場合、彼女のスコアは 74 になります。1 以外のロールは少なくとも 2 を加算するため、正確に 75 に達することはあり得ません。そのため、この決定の最終的な勝利確率はゼロになります。 不用意に実装すると、74 を通常の未完成スコアとして扱い、将来の 1 のロールまたは何らかの人為的な増分が 75 に達することを許可する可能性があります。 

3 番目の微妙なケースは、ターンをバストするロールです。 キャトリンが持っている場合`C = 70`そして`X = 4`さらに 2 を出すと一時的な合計は 6 になり、永続的なスコアは 76 になります。その結果は何も得点せず、ターンは終了します。 これはスコア 76 の状態ではないため、それを 1 つの状態として扱うと再発が発生します。 

4 番目の微妙なケースは、プレーヤー間の循環依存関係です。 すべての一時的なターン ステートが降順で順序付けされた後でも、`X`、キャトリンのターン開始時の値は、ホスターがプレイしているときのターン開始時の値に依存します。 プレーンな非巡回 DP は、これら 2 つの値を独立して解決できません。 

## アプローチ

 直接的な総当たりアプローチでは、ゲーム ツリー全体が拡張されます。 すべてのロールで 6 つの可能な結果があり、1 つ以外の結果が出るたびに、保留するか継続するかの 2 つの選択肢があります。 プレイヤーはスコアを変えずに 1 をロールしたりバストを繰り返したりする可能性があるため、一定回数ロールした後にゲームが終了するという保証さえありません。 後で人為的に止めたら`D`ロールの場合、ロール シーケンスだけの数は`6^D`。 のために`D = 20`、それは約です`3.66 × 10^15`戦略の選択を検討する前に、その場を離れます。 したがって、徹底的なシミュレーションでは正確な解決策を得ることができません。 

より構造化されたアプローチは動的プログラミングです。 させて`dp[c][h][x]`順番が回ってきたプレイヤーが得点したときのキャトリンの勝利確率`c`、相手に得点がある`h`、現在のターンの合計は`x`。 対戦相手の次のターンの確率がわかれば、すべての一時的な合計の値を大きな値から計算できます。`x`小さなものまで`x`、継続するだけなので、`x`上向きに。 

難点はある状態です`x = 0`。 させて`A = dp[c][h][0]`。 ホールドまたは 1 のロールの後、ゲームは相手に切り替わるので、`dp[h][c][0]`。 これにより、2 つのターン開始状態の間にサイクルが作成されます。 

重要な観察は、ゲームはゼロサムであるということです。 2 つの通常の非最終スコア状態から開始すると、最終的には 1 人のプレイヤーが確率 1 で勝ちます。`dp[c][h][0] + dp[h][c][0] = 1`。 

したがって、2 つの未知の確率を推測する代わりに、1 つの値を見つけるだけで済みます。`A`。 ターン開始時のキャトリンの勝利確率を仮に推測すると、`A`の場合、ホスターの対応する確率は次のようになります。`1 - A`。 その値が固定されているため、Catelyn の一時的なターン状態はすべて、ターンの合計が大きいものから小さいものまで決定論的に計算できます。 これにより関数が得られます`F(A)`、 どこ`F(A)`は、最適な決定の下で実際に最初のサイコロを振ることによって得られる確率です。 

真の価値は満たす`A = F(A)`。 

ターンから得られる値は、推測される相手確率に対して単調であるため、この固定点は二分探索で求めることができます。 スコア制限が小さいため、これは実用的です。 スコアペアを降順で処理します。`c + h`したがって、ホールドが現在の永久スコアを移動するたびに、`c`に`c + x`、必要なターン開始状態は厳密に大きいスコア合計を持ち、すでに計算されています。 

これにより、総当り的な方法から最終的な方法への明確な進行が得られます。 すべての可能な未来が明示的に表現されているため、ブルート フォースが機能しますが、ツリーは指数関数的に成長し、終了しない可能性があります。 動的プログラミングでは繰り返しのサブツリーが削除されますが、固定点観測では残りの 2 状態サイクルが削除されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(6^D)`のために`D`-ロールの切り捨て |`O(D)`DFS を使用する | 遅すぎて正確ではありません |
 | 最適 |`O(75^3 log(1/ε))`|`O(75^2)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 定義する`dp[c][h]`恒久的なスコアが次の場合に、そのターンのプレイヤーが勝つ確率として`c`そして相手の永久スコアは`h`、空の一時的なターン。 

ゲームは対称的であるため、すべての非終端ペアについて次のようになります。`dp[c][h] = 1 - dp[h][c]`。 したがって、等しいスコアには正確な価値があります`0.5`。 
2. すべてのペアを処理する`(c, h)`降順で`c + h`。 

合計スコアを持つペアを解決していると仮定します。`c + h`。 キャトリンが蓄積後にホールドした場合`x > 0`、彼女の新しいスコアは`c + x`、したがって、相手のターン開始時の状態はスコア合計を持ちます。`h + c + x`、厳密にはこれより大きくなります。 その状態はすでに計算されています。 
3. 固定推測の場合`A`のために`dp[c][h]`、 使用`1 - A`相手のターン開始時の勝利確率として。 

現在のターンが終了するたびに何が起こるかがわかりました。 1 のロール、またはターンを無効にするロールは、対戦相手に次のターンを与えるため、現在のプレイヤーにとっての値は次のようになります。`1 - (1 - A) = A`相手の状態が自身の勝率で表される場合。 実装ではより直接的に、対戦相手の勝つ確率が`p`、すべての即時のターン損失には価値があります`1 - p`。 
4. 可能な最大合計から 2 までの一時的なターン値を計算します。 

しましょう`v[x]`現在のプレーヤーが正確に蓄積した後の最適な勝率になる`x`ターン中のポイント。 保持すると与える`1 - dp[h][c+x]`、次の場合を除く`c+x = 75`、ここで保持するとすぐに勝ちとなり、値は 1 になります。 

継続すると、平均して 6 つの結果が得られます。 1の目が出ると、`1 - p`。 2 から 6 までの出目は次のいずれかに移動します。`v[x+d]`結果のスコアが 75 を超えない場合、または`1 - p`ターンが失敗したとき。 

したがって、`v[x] = max(hold, continue)`。 

以来、`continue`一時的な合計が大きくなり、降順に移行します`x`この繰り返しを非周期的にします。 
5. すべての一時的な状態が計算されたら、ターンの開始を評価します。 

キャトリンは 1 回ロールする必要があります。 結果が 1 の場合は直ちにターンが終了します。 2 から 6 までの各結果は、すでに計算されているいずれかの結果に達します。`v[d]`州またはバスト。 それらの平均は、現在の推測によって生成された値です。 
6. ターン開始確率を二分探索します。 

現在の推測を次のようにします`A`。 結果の確率を計算する`F(A)`。 もし`F(A) > A`、推測が小さすぎるため、下限を上に移動します。 それ以外の場合は、上限を下に移動します。 

50 回の反復により、数値間隔が必要な間隔を大幅に下回ります。`10^-5`2 つのアクション間の分離。 
7. スコアペアの結果の値を保存します。 

のために`c < h`、計算された値をに保存します`dp[c][h]`とその補足`dp[h][c]`。 のために`c = h`、 店`0.5`直接。 
8. 完全なテーブルが利用可能になったら、そのテーブルの一時的なターン値を再構築して各クエリに答えます。`(C, H)`ペア。 

ホールド値は`1`いつ`C + X = 75`、そうでない場合は、`1 - dp[H][C+X]`。 コンティニュー値は、事前に計算された一時的なターン値を使用し、すべてのバストを即時のターン損失として扱う、6 つの可能な次のロールの平均です。 出力`H`ホールド値が大きい場合、および`C`さもないと。 

計算の背後にある不変条件は、次のようになります。`(c, h)`、ホールドに必要なすべての永続スコア状態は、厳密に大きなスコア合計を持ち、すでに正確です。 唯一の未解決の依存関係は対戦相手のターン開始確率であり、対称関係によりその依存関係は 1 つのスカラーに減ります。 二分探索は一意の固定小数点に収束するため、そこから計算された一時的な値は、そのスコア ペアの最適な値とまったく同じになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MAX_SCORE = 73
TARGET = 75

def solve(data):
    it = iter(data.split())
    q = int(next(it))
    queries = []

    for _ in range(q):
        c = int(next(it))
        h = int(next(it))
        x = int(next(it))
        queries.append((c, h, x))

    # dp[c][h] = probability that the player to move wins
    # with permanent scores c and h and an empty turn.
    #
    # Columns 74 and 75 are useful boundary states:
    # dp[c][74] = 1 for c < 74, because the opponent at 74
    # can never reach exactly 75.
    # dp[c][75] = 0 because the opponent has already won.
    dp = [[0.0] * 76 for _ in range(74)]

    for c in range(74):
        dp[c][74] = 1.0
        dp[c][75] = 0.0

    def turn_value(c, h, opponent_win):
        """
        Compute the optimal value of the current turn when:
          current permanent score = c
          opponent permanent score = h
          opponent's beginning-of-turn win probability = opponent_win

        Returns the beginning-of-turn value for the current player.
        """
        max_x = TARGET - c
        lose_turn = 1.0 - opponent_win

        v = [0.0] * (max_x + 8)
        suffix = [0.0] * (max_x + 9)

        # Reaching exactly 75 means the player can hold and win.
        v[max_x] = 1.0
        suffix[max_x] = 1.0

        row = dp[h]

        for x in range(max_x - 1, 1, -1):
            # Holding scores c + x.
            hold = 1.0 - row[c + x]

            # Continue:
            # roll 1 is always a turn loss.
            # Rolls 2..6 either reach a known state or bust.
            left = x + 2
            right = min(x + 6, max_x)

            if left <= right:
                known = right - left + 1
                future = suffix[left] - suffix[right + 1]
            else:
                known = 0
                future = 0.0

            continue_value = (
                future + (6 - known) * lose_turn
            ) / 6.0

            v[x] = max(hold, continue_value)
            suffix[x] = suffix[x + 1] + v[x]

        # First roll of a new turn.
        left = 2
        right = min(6, max_x)

        if left <= right:
            known = right - left + 1
            future = suffix[left] - suffix[right + 1]
        else:
            known = 0
            future = 0.0

        return (future + (6 - known) * lose_turn) / 6.0

    # Solve score pairs from larger c+h to smaller c+h.
    for total in range(146, -1, -1):
        lo_c = max(0, total - 73)
        hi_c = min(73, total)

        for c in range(lo_c, hi_c + 1):
            h = total - c

            if c > h:
                continue

            if c == h:
                dp[c][h] = 0.5
                continue

            # We solve for A = dp[c][h].
            # The swapped state has value 1-A.
            lo = 0.0
            hi = 1.0

            for _ in range(50):
                mid = (lo + hi) * 0.5

                # If dp[c][h] = mid, then the opponent's
                # beginning-of-turn probability is 1-mid.
                got = turn_value(c, h, 1.0 - mid)

                if got > mid:
                    lo = mid
                else:
                    hi = mid

            a = (lo + hi) * 0.5
            dp[c][h] = a
            dp[h][c] = 1.0 - a

    def turn_states(c, h, opponent_win):
        """
        Same recurrence as turn_value, but keeps all temporary
        turn states because queries need v[X].
        """
        max_x = TARGET - c
        lose_turn = 1.0 - opponent_win

        v = [0.0] * (max_x + 8)
        suffix = [0.0] * (max_x + 9)

        v[max_x] = 1.0
        suffix[max_x] = 1.0

        row = dp[h]

        for x in range(max_x - 1, 1, -1):
            hold = 1.0 - row[c + x]

            left = x + 2
            right = min(x + 6, max_x)

            if left <= right:
                known = right - left + 1
                future = suffix[left] - suffix[right + 1]
            else:
                known = 0
                future = 0.0

            continue_value = (
                future + (6 - known) * lose_turn
            ) / 6.0

            v[x] = max(hold, continue_value)
            suffix[x] = suffix[x + 1] + v[x]

        return v

    answer = []

    for c, h, x in queries:
        opponent_win = dp[h][c]
        v = turn_states(c, h, opponent_win)

        if c + x == TARGET:
            hold = 1.0
        else:
            hold = 1.0 - dp[h][c + x]

        lose_turn = 1.0 - opponent_win

        left = x + 2
        right = min(x + 6, TARGET - c)

        if left <= right:
            known = right - left + 1
            future = sum(v[d] for d in range(left, right + 1))
        else:
            known = 0
            future = 0.0

        continue_value = (
            future + (6 - known) * lose_turn
        ) / 6.0

        answer.append("H" if hold > continue_value else "C")

    return "\n".join(answer)

if __name__ == "__main__":
    data = sys.stdin.buffer.read()
    print(solve(data))
```テーブル`dp`ターン開始の確率のみを保存します。 一時的な合計はオンデマンドで計算されます。`(c, h, x)`値を指定すると、スコア ペアの計算を支援せずにメモリが増加します。 

境界列７５は、すでに完了したゲームを表すため、そうでなければターンが開始されるプレイヤーにとっては値０を有する。 列 74 はさらに微妙です。 対戦相手が 74 ポイントを持っている場合、その対戦相手は正確に 75 を獲得することはできないため、74 未満のスコアを持つプレイヤーが最終的に確率 1 で勝ちます。これら 2 つの境界値は、特殊なケースがメインの繰り返しに漏れることを防ぎます。 

内部`turn_value`、`max_x = 75 - c`これは、現在でも合法である一時的な合計の最大値です。 まさにその時の状態`max_x`保持が 75 に達するため、1 の価値があります。それより大きな合計は状態として表示されません。これらの結果はバーストとなり、すぐにターンが終了するためです。 

の`suffix`配列は小さな最適化です。 からの続き`x`には 5 つの値が必要です`x+2`を通して`x+6`。 再発は逆方向に処理されるため、それらはすべて既知です。 サフィックス合計により、この部分は状態ごとに 5 回の加算から定数時間に減ります。 

二分探索では 50 回の反復が使用され、これは二分探索よりもはるかに正確です。`10^-5`ステートメントで必要な区別。 ここでは Python の整数はオーバーフローせず、確率に関係するすべての算術演算では倍精度浮動小数点が使用されます。 

最終的なクエリ評価では、任意のしきい値と比較するのではなく、意図的に 2 つのアクション値を直接比較します。 正しい決定は現在のターンの合計と対戦相手の最適な応答の両方に依存するため、これは重要です。 

## 実用的な例

 付属のサンプルは、```
15 0 3
35 50 40
15 0 30
```そして出力は```
C
H
H
```最初のクエリの場合、関連する状態は次のとおりです。`(C,H,X) = (15,0,3)`。 事前計算により、より大きなスコア合計を持つすべてのターン開始状態がすでに決定されています。`15`。 次に、アルゴリズムはスコア ペアの Catelyn の一時的なターン値を再構築します。`(15,0)`。 

| 状態 | 意味 | 意思決定の比較 | 結果 |
 | --- | --- | --- | --- |
 |`(15, 0, 3)`| Catelyn は 15 永久ポイントを持ち、ターンに 3 ポイントを持ちます |`continue_value > hold`|`C`|
 |`(35, 50, 40)`| 保持すると、Catelyn のスコアは 75 になります。`hold = 1`|`H`|
 |`(15, 0, 30)`| ターン合計が大きいため継続リスクが支配的 |`hold > continue_value`|`H`|

 2 番目のクエリは、正確なターゲット境界を実行します。 Catelyn の永久ポイントは 35 で、現在のターンでは 40 なので、ホールドするとちょうど 75 になります。即時の勝利を改善する確率計算はありません。`H`強制的に。 

2 番目のトレースでは、カスタム入力を検討します。```
2
73 0 2
72 0 3
```どちらのクエリも完全にヒットします。 

| クエリ | C | H | X | C + X | 値を保持 | 出力 |
 | --- | --- | --- | --- | --- | --- | --- |
 | 1 | 73 | 0 | 2 | 75 | 1 |`H`|
 | 2 | 72 | 0 | 3 | 75 | 1 |`H`|

 最初のクエリでは、すぐに獲得できる最小の一時的な合計も実行されます。 2 つ目は、実装で使用されていることを確認します。`C + X == 75`のような 1 つずつ異なる条件ではなく、`C + X >= 75`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(75^3 log(1/ε))`| がある`O(75^2)`スコアペア。それぞれは次の二分探索を使用します。`O(log(1/ε))`反復し、各反復で計算します`O(75)`一時的な状態 |
 | スペース |`O(75^2)`| 永久スコア DP テーブルにはターン開始時の値のみが含まれます。 

50 回の二分探索反復では、数値精度は必要な精度よりもはるかに厳しくなります。`10^-5`。 最大の次元はわずか 75 であるため、スコア ペアのおよそ 2 次数と線形の一時状態の計算は、公式の 5 秒と 1024 MB の制限内に問題なく収まります。 

## テストケース```python
import io
import sys

def solve(inp: str) -> str:
    data = inp.encode()
    it = iter(data.split())

    q = int(next(it))
    queries = []

    for _ in range(q):
        c = int(next(it))
        h = int(next(it))
        x = int(next(it))
        queries.append((c, h, x))

    TARGET = 75

    dp = [[0.0] * 76 for _ in range(74)]

    for c in range(74):
        dp[c][74] = 1.0
        dp[c][75] = 0.0

    def turn_value(c, h, opponent_win):
        max_x = TARGET - c
        lose_turn = 1.0 - opponent_win

        v = [0.0] * (max_x + 8)
        suffix = [0.0] * (max_x + 9)

        v[max_x] = 1.0
        suffix[max_x] = 1.0

        row = dp[h]

        for x in range(max_x - 1, 1, -1):
            hold = 1.0 - row[c + x]

            left = x + 2
            right = min(x + 6, max_x)

            if left <= right:
                known = right - left + 1
                future = suffix[left] - suffix[right + 1]
            else:
                known = 0
                future = 0.0

            cont = (future + (6 - known) * lose_turn) / 6.0
            v[x] = max(hold, cont)
            suffix[x] = suffix[x + 1] + v[x]

        left = 2
        right = min(6, max_x)

        if left <= right:
            known = right - left + 1
            future = suffix[left] - suffix[right + 1]
        else:
            known = 0
            future = 0.0

        return (future + (6 - known) * lose_turn) / 6.0

    for total in range(146, -1, -1):
        lo_c = max(0, total - 73)
        hi_c = min(73, total)

        for c in range(lo_c, hi_c + 1):
            h = total - c

            if c > h:
                continue

            if c == h:
                dp[c][h] = 0.5
                continue

            lo = 0.0
            hi = 1.0

            for _ in range(50):
                mid = (lo + hi) * 0.5
                got = turn_value(c, h, 1.0 - mid)

                if got > mid:
                    lo = mid
                else:
                    hi = mid

            a = (lo + hi) * 0.5
            dp[c][h] = a
            dp[h][c] = 1.0 - a

    def turn_states(c, h, opponent_win):
        max_x = TARGET - c
        lose_turn = 1.0 - opponent_win

        v = [0.0] * (max_x + 8)
        suffix = [0.0] * (max_x + 9)

        v[max_x] = 1.0
        suffix[max_x] = 1.0

        row = dp[h]

        for x in range(max_x - 1, 1, -1):
            hold = 1.0 - row[c + x]

            left = x + 2
            right = min(x + 6, max_x)

            if left <= right:
                known = right - left + 1
                future = suffix[left] - suffix[right + 1]
            else:
                known = 0
                future = 0.0

            cont = (future + (6 - known) * lose_turn) / 6.0
            v[x] = max(hold, cont)
            suffix[x] = suffix[x + 1] + v[x]

        return v

    ans = []

    for c, h, x in queries:
        opponent_win = dp[h][c]
        v = turn_states(c, h, opponent_win)

        if c + x == TARGET:
            hold = 1.0
        else:
            hold = 1.0 - dp[h][c + x]

        lose_turn = 1.0 - opponent_win

        left = x + 2
        right = min(x + 6, TARGET - c)

        if left <= right:
            known = right - left + 1
            future = sum(v[d] for d in range(left, right + 1))
        else:
            known = 0
            future = 0.0

        cont = (future + (6 - known) * lose_turn) / 6.0

        ans.append("H" if hold > cont else "C")

    return "\n".join(ans)

def run(inp: str) -> str:
    return solve(inp)

# Provided sample
assert run(
    """3
15 0 3
35 50 40
15 0 30
"""
) == "C\nH\nH", "sample 1"

# Minimum-size input and exact-hit boundary
assert run(
    """1
73 0 2
"""
) == "H", "minimum query, exact 75"

# Off-by-one boundary: 72 + 3 is exactly 75
assert run(
    """2
72 0 3
73 0 2
"""
) == "H\nH", "exact-hit boundaries"

# Equal permanent scores, including the maximum allowed input scores
assert run(
    """2
73 73 2
73 73 2
"""
) == "H\nH", "equal scores and maximum scores"

# Maximum Q
big_input = "1000\n" + "73 73 2\n" * 1000
assert run(big_input) == "\n".join(["H"] * 1000), "maximum Q"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`3 / 15 0 3 / 35 50 40 / 15 0 30`|`C H H`| 提供されたサンプルと両方のアクション |
 |`1 / 73 0 2`|`H`| 最小限のクエリと即時の勝利 |
 |`2 / 72 0 3 / 73 0 2`|`H H`| 正確な 75 境界とオフバイワンの処理 |
 |`2 / 73 73 2 / 73 73 2`|`H H`| 等しいスコアと最大スコア値 |
 | 1000部`73 73 2`| 1000行`H`| クエリの最大数と繰り返し状態の処理 |

 ## 特殊なケース

 即座に正確な勝利を得るには、入力```
1
73 0 2
```もっている`C + X = 75`。 クエリ エバリュエーターは、別の DP 状態を参照せずに、完全一致分岐を選択し、保持値を 1 に設定します。 継続しても確率 1 は向上しないため、答えは次のとおりです。`H`。 

到達不可能なスコア 74 については、からの移行を検討してください。```
1
72 0 2
```保持すると永久スコア 74 が作成されます。対応する相手の状態は次のとおりです。`dp[0][74] = 1`これは、74 のプレイヤーが正確に 75 に到達することはできないため、最終的には対戦相手が勝つことを意味します。したがって、ホールド値は次のようになります。`1 - 1 = 0`。 この境界は DP テーブルで明示的に表現されるため、74 が誤って 75 の有効な先行値として扱われることはありません。 

バストの場合、現在の状態が次のようになっていると仮定します。`C = 70`そして`X = 4`。 6 のロールは一時的な合計を 10 にし、永続的なスコアは 80 になります。繰り返しはアクセスしません。`v[10]`, なぜならその状態は法定範囲外だからです。 代わりに、現在のターンを失う価値に貢献します。`1 - opponent_win`。 75 を超えるすべてのロールに同じ処理が適用されます。 

スコアが等しい場合、状態は対称です。 両方のプレイヤーが同じ永久スコアを持っており、ターンの開始時である場合、アイデンティティを交換しても何も変わりません。 したがって、各プレイヤーの勝率は 0.5 になります。 この実装では、既知の状態に対して二分探索を実行する代わりに、この正確な対称性を使用します。 

循環依存関係の場合、解決する際に`(c,h)`、二分探索では再帰的に解決しようとしません。`(h,c)`。 ゼロサムアイデンティティを使用します`dp[h][c] = 1 - dp[c][h]`、およびholdingによって作成された他のすべての依存関係は、厳密に大きなスコア合計を持ちます。 これは、ゲームを周期的な確率過程から一連の一次元の固定小数点計算に変換するものです。
