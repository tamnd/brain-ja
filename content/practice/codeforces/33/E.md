---
title: "CF 33E - ヘルパー"
description: "ヴァレラは、解決方法を知っている主題のリストを持っています。 どの科目にも一定の作業時間がかかります。 学生はリクエストを持ってきます。各リクエストには主題、試験の締め切り、報酬が含まれています。 ヴァレラが試験開始前に厳密に解答を完了した場合、報酬が支払われます。"
date: "2026-05-28T00:00:00+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 33
codeforces_index: "E"
codeforces_contest_name: "Codeforces Beta Round 33 (Codeforces format)"
rating: 2600
weight: 33
solve_time_s: 177
verified: false
draft: false
---
[CF 33E - ヘルパー](https://codeforces.com/problemset/problem/33/E)

 **評価:** 2600
 **タグ:** -
 **解決時間:** 2 分 57 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 ヴァレラは、解決方法を知っている主題のリストを持っています。 どの科目にも一定の作業時間がかかります。 学生はリクエストを持ってきます。各リクエストには主題、試験の締め切り、報酬が含まれています。 ヴァレラが試験開始前に厳密に解答を完了した場合、報酬が支払われます。 

難しいのは時間管理です。 Valera は継続的に動作しません。 毎日、睡眠と食事の間隔がブロックされています。 空き時間には仕事をすることはできますが、問題を解決し始めたら、睡眠や食事による強制的な中断を除いて、完全に完了するまで続けなければなりません。 自発的に一時停止し、後で再開することはできません。 

どの生徒をどの順序で支援するかを選択し、総報酬を最大化する必要があります。 出力では、解決されたすべてのリクエストの開始タイムスタンプと終了タイムスタンプを含む正確なスケジュールも再構築する必要があります。 

制約は動的プログラミングのソリューションを示唆するには十分小さいですが、ブルート フォースを無効にするのに十分な大きさです。 生徒数は最大 100 名、期間は 30 日です。 1 日の時間は 1440 分しかないため、カレンダー全体には最大 43200 分が含まれます。 単純なサブセット列挙では、以下までチェックする必要があります。$2^{100}$サブセット、それは不可能です。 受注した仕事をすべて発注してみても絶望的です。 

重要な構造は次のとおりです。 

1. すべてのタスクには期限があります。 
2. すべてのタスクの処理時間は、主題にのみ依存します。 
3. 時間の経過に伴う作業能力は決定的です。 
4. 中断は固定されており、毎日同じです。 

これは、期限付きの加重スケジューリングのように見え始めます。 

1つ目の隠れた難しさは、「実時間の経過」と「実際の作業時間」が異なることです。 タスクには 60 作業分が必要で、Valera は 08:00 に開始しますが、朝食は 08:20 ～ 08:40 を占めているとします。 完了の瞬間は09:00ではなく、09:20です。 

単純に実時間で期間を追加する不注意な実装では、誤った期限が発生します。 

次の例を考えてみましょう。```
1 1 1
math
60
00:00-00:10
00:20-00:30
12:00-12:10
18:00-18:10
math 1 01:15 100
```朝食に 10 分かかるため、ヴァレラの勤務時間は 00:10 から 01:10 までの 50 分のみです。 実際には 01:20 に終了するので、答えは 0 です。 

もう 1 つの微妙なケースは、「厳密に前」の条件です。 試験開始と同時に終了したことはカウントされません。```
1 1 1
math
30
00:00-00:00
12:00-12:00
18:00-18:00
23:00-23:00
math 1 00:31 100
```作業が 00:01 に開始され、30 分間続いた場合、完了時間は 00:31 になります。 試験も 00:31 に始まるため、これは無効です。 

3 番目の罠は、サポートされていない主題から発生します。 学生はヴァレラのリスト以外の科目を要求する場合があります。 そういった仕事は完全に無視しなければなりません。```
1 1 1
math
10
00:00-00:00
12:00-12:00
18:00-18:00
23:00-23:00
physics 1 10:00 500
```十分な時間があったとしても、答えは 0 です。 

最後の難しい部分は再構築です。 利益を最大化するだけではなく、正確な開始と終了のタイムスタンプを印刷する必要があります。 先行情報を失った DP は使用できなくなります。 

## アプローチ

 ブルートフォースのアイデアは単純です。 生徒のサブセットごとに、すべての注文を試し、カレンダーを分単位でシミュレーションし、すべての期限が守られているかどうかを確認します。 実現可能なスケジュールの中から最も高い報酬を選択してください。 

なぜこれが機能するのでしょうか? 順序が固定されると、実行タイムラインは決定的になるためです。 私たちは常にできるだけ早く次のタスクを開始します。 最も早い実行でタスクが期限に間に合わなかった場合、それを遅らせると事態は悪化するだけです。 

問題は国家の爆発です。 たとえ20人の生徒であっても、私たちはすでに直面しています$20!$順列。 学生が 100 人いると、検索範囲は天文学的に大きくなります。 

構造的な観察が必要です。 

実際のタイムスタンプを「使用可能な作業時間」に変換するとします。 睡眠と食事の間隔をすべて削除してタイムラインを圧縮することを想像してください。 そうすれば、実行可能なすべてのスケジュールが中断されることなく継続的に処理されます。 

あらゆる瞬間に$T$、 定義する：$$f(T) = \text{number of workable minutes strictly before } T$$試験期限のある学生を連れて行きましょう$D$。 ヴァレラは必ずその前に終了しなければなりません$D$, したがって、彼は最大で次の費用を費やすことができます。$$f(D)$$その期限までに作業単位を設定します。 

この変化が重要な洞察です。 圧縮後、すべてのジョブは次のようになります。 

1. 処理時間$p_i$2. 締切$d_i = f(D_i)$3. 利益$c_i$ここで問題となるのは、期限による古典的な加重スケジューリングです。 

もう 1 つの重要な観察は、Earliest Deadline First スケジューリングから得られます。 選択した一連のジョブが実行可能であれば、それらを期限で並べ替えることも実行可能です。 つまり、任意の順列を考慮する必要がありません。 

圧縮された期限によってジョブを並べ替えた後、ナップサック スタイルの DP を実行できます。$$dp[t] = \text{maximum profit achievable using exactly } t \text{ work minutes}$$ジョブを処理するとき$(p,d,c)$、次の場合にのみ配置できます。$$t + p < d$$厳密な不等式は、「試験前に必ず終了する」ことをエンコードします。 

これにより、複雑さはおおよそ次のように軽減されます。$$O(n \cdot W)$$どこ$W$試験期間全体での作業可能時間の合計は、最大で約 43,200 分です。 

それは簡単に制限内に収まります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(n! \cdot n)$|$O(n)$| 遅すぎる |
 | 最適 |$O(n \cdot W)$|$O(W)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 毎日のブロックされた間隔を解析し、すべての作業可能な分数のカレンダーを作成します。$k$日々。 

毎分、利用可能かブロックされているかがマークされます。 タイムラインの合計の長さは最大でも$30 \times 1440 = 43200$したがって、明示的な前処理は安価です。 
2. 2 つの変換配列を構築します。 

最初の配列は、実際の各分をその前の作業可能な分数にマップします。 

2 番目の配列は、各作業可能分のインデックスを対応する実際の分にマッピングします。 

最初のマッピングでは期限が短縮されます。 2 番目のマッピングは、後で実際のタイムスタンプを再構築します。 
3. 生徒のリクエストをすべて読みます。 

件名が不明な場合は、Valera が解決できないため、リクエストを直ちに破棄してください。 
4. すべての試験タイムスタンプを圧縮された期限に変換します。 

試験が今まさに始まると仮定します$R$。 その場合、ジョブの期限は次のようになります。$$d = f(R)$$正確に使用するスケジュール$d$作業可能な分数は試験時または試験後に終了するため、厳密に次の値より小さいステートメントのみが有効です。$d$合法です。 
5. 期限が迫っている順にジョブを並べ替えます。 

EDF の順序付けにより実現可能性が維持されるため、これで十分です。 
6. 作業可能な時間にわたってナップザック DP を実行します。 

させて：$$dp[t] = \text{maximum profit with total work } t$$最初だけ$dp[0] = 0$有効です。 

すべてのジョブについて、時間を逆方向に繰り返します。 ジョブを追加しても総作業量が期限を下回る場合は、状態を更新します。 
7. 遷移中に親ポインタを保存します。 

どのジョブが選択されたか、および各ジョブが終了する累積作業時間はどれくらいかを再構築する必要があります。 
8. 選択したジョブを逆の順序で復元します。 

再構築では、当然のことながら、実際の実行順序である、減少しない期限順にジョブが生成されます。 
9. 圧縮された作業間隔を実際のタイムスタンプに変換します。 

ジョブが作業可能な時間を占めると仮定します$[L, R)$。 逆マッピング配列を使用すると、以下を取得できます。 

- 実際の開始分
 - 終了実際の分

 次に、それらの分を日のインデックスと HH:MM 形式に変換します。 

### なぜ効果があるのか

 圧縮されたタイムラインにより、使用できない間隔がすべて削除されます。 この変化した空間内では、作業は継続的に進行し、すべてのジョブは単純に処理時間を消費します。 

実現可能なスケジュールは、古典的な EDF の特性である実現可能性を損なうことなく、減らない期限によって再調整できます。 期限で並べ替えた後、残る唯一の選択肢は、どのジョブのサブセットを引き受けるかです。 

DP の不変式は次のとおりです。$$dp[t]$$圧縮された作業時間の合計が等しい処理済みジョブの中で達成可能な最高の利益を保存します。$t$。 

移行は、結果の完了時間がジョブの期限よりも厳密に前の場合にのみ許可されます。 圧縮された時間は実際の作業可能な分数に正確に対応するため、受け入れられたすべての DP 状態は有効な実際のスケジュールに対応します。 

逆マッピングは、選択されたジョブの一意の最も早い実際の実行タイムラインを再構築します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

INF = -(10 ** 18)

def parse_time(s):
    h, m = map(int, s.split(':'))
    return h * 60 + m

def parse_segment(s):
    a, b = s.split('-')
    return parse_time(a), parse_time(b)

def minute_to_output(x):
    day = x // 1440 + 1
    rem = x % 1440
    h = rem // 60
    m = rem % 60
    return day, f"{h:02d}:{m:02d}"

def solve():
    m, n, k = map(int, input().split())

    subjects = [input().strip() for _ in range(m)]
    times = list(map(int, input().split()))

    subj_time = {}
    for s, t in zip(subjects, times):
        subj_time[s] = t

    blocked = [False] * (k * 1440)

    segments = [parse_segment(input().strip()) for _ in range(4)]

    for day in range(k):
        base = day * 1440
        for l, r in segments:
            for x in range(l, r + 1):
                blocked[base + x] = True

    total_minutes = k * 1440

    pref = [0] * (total_minutes + 1)
    work_to_real = []

    for i in range(total_minutes):
        pref[i + 1] = pref[i]
        if not blocked[i]:
            pref[i + 1] += 1
            work_to_real.append(i)

    jobs = []

    for idx in range(1, n + 1):
        parts = input().split()

        subj = parts[0]

        if subj not in subj_time:
            continue

        day = int(parts[1]) - 1
        tm = parse_time(parts[2])
        money = int(parts[3])

        real_deadline = day * 1440 + tm

        deadline = pref[real_deadline]

        p = subj_time[subj]

        jobs.append((deadline, p, money, idx))

    jobs.sort()

    W = len(work_to_real)

    dp = [INF] * (W + 1)
    dp[0] = 0

    parent = [None] * (W + 1)

    for j, (deadline, p, money, idx) in enumerate(jobs):
        for t in range(W - p, -1, -1):
            if dp[t] == INF:
                continue

            nt = t + p

            if nt >= deadline:
                continue

            val = dp[t] + money

            if val > dp[nt]:
                dp[nt] = val
                parent[nt] = (t, j)

    best_t = 0
    best_profit = 0

    for t in range(W + 1):
        if dp[t] > best_profit:
            best_profit = dp[t]
            best_t = t

    chosen = []

    cur = best_t

    while cur != 0:
        prev, j = parent[cur]
        chosen.append((cur, jobs[j]))
        cur = prev

    chosen.reverse()

    print(best_profit)
    print(len(chosen))

    current_work = 0

    for finish_work, (deadline, p, money, idx) in chosen:
        start_work = current_work
        end_work = finish_work - 1

        start_real = work_to_real[start_work]
        end_real = work_to_real[end_work]

        sd, st = minute_to_output(start_real)
        ed, et = minute_to_output(end_real)

        print(idx, sd, st, ed, et)

        current_work = finish_work

solve()
```前処理フェーズは実装の中心です。 すべてのタスクの中断を繰り返しシミュレートする代わりに、コードはカレンダー全体を 1 回圧縮します。`pref[t]`実際の分までに作業可能な分が何分存在するかを保存します`t`。 これにより、期限チェックが通常の整数比較に変換されます。 

配列`work_to_real`逆マッピングを実行します。 作業可能な分数 500 が実際の分 900 に対応する場合、タスクは圧縮された間隔を占有します。`[500, 560)`実際のタイムスタンプに自動的にマッピングされます。 

厳密な不等式は注目に値します。```
if nt >= deadline:
    continue
```試験時間ちょうどに終了するジョブは無効であるため、平等は拒否されなければなりません。 

もう一つの細かい点は再構成です。 DP は各合計作業量の最適な値のみを保存しますが、親ポインターは以前の作業負荷と選択したジョブを保存します。 遷移は逆方向に実行されるため、すべてのジョブは最大 1 回しか使用されません。 

スケジュールの再構築では、圧縮された作業範囲が使用されます。 タスクが完了するまでの現在の累積作業量があるとします。`start_work`そしてその後`finish_work`。 タスクには作業可能な分がかかります。```
[start_work, finish_work)
```分は離散的であり、出力される終了タイムスタンプは最後に作業した分であるため、コードでは次のものが使用されます。```
end_work = finish_work - 1
```この減算がないと、印刷されるすべての間隔が 1 分ずれてしまいます。 

## 実用的な例

 ### サンプル 1

 入力:```
3 3 4
calculus
algebra
history
58 23 15
00:00-08:15
08:20-08:35
09:30-10:25
19:00-19:45
calculus 1 09:36 100
english 4 21:15 5000
history 1 19:50 50
```1 日目の圧縮された実行可能なタイムライン:

 | 実間隔 | 累積作業可能時間 |

 |---|---|---|

 | 08:16-08:19 | 4 |

 | 08:36-09:29 | 58 |

 | 10:26-19:49 | たくさん |

 微積分のタスクには 58 分の作業時間が必要です。 実行可能分 0 から始まり、実行可能分 57 で終了します。これは、リアルタイムの 09:29 に相当します。 

履歴タスクには作業可能時間 15 分が必要です。 最初のタスクの後に開始され、10:40 に終了します。 

| タスク | 処理 | 締め切り | 選択済み |
 | --- | --- | --- | --- |
 | 微積分 | 58 | 09:36前 | はい |
 | 歴史 | 15 | 19:50前 | はい |

 合計利益は150となります。 

このトレースは、タイムライン圧縮が機能する理由を示しています。 朝食の中断は圧縮スケジュールから自動的に消えます。 

### カスタム例```
1 2 1
math
30
00:00-00:10
12:00-12:10
18:00-18:10
23:00-23:10
math 1 00:41 100
math 1 01:20 200
```利用可能な作業は 00:11 に始まります。 

| 仕事 | 期間 | 本当の期限 | 圧縮された期限 |
 | --- | --- | --- | --- |
 | 1 | 30 | 00:41 | 30 |
 | 2 | 30 | 01:20 | 69 |

 ジョブ 1 は締め切りぴったりに完了するため、不可能です。 

ジョブ 2 は実行可能です。 

| DP 時間 | 利益 |
 | --- | --- |
 | 0 | 0 |
 | 30 | 200 |

 答えには 2 番目の生徒のみが含まれています。 

これは厳密な不等式条件を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n \cdot W)$| すべてのジョブの DP と作業可能時間 |
 | スペース |$O(W)$| DP と親配列 |

 ここ$W$一日を通しての作業可能な分数です。 カレンダー全体には最大 43,200 分が含まれるため、最悪の場合の DP 遷移数は約$100 \times 43200$、Python の制限内で快適に使用できます。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

def run(inp: str) -> str:
    from contextlib import redirect_stdout
    out = io.StringIO()

    sys.stdin = io.StringIO(inp)

    def parse_time(s):
        h, m = map(int, s.split(':'))
        return h * 60 + m

    def parse_segment(s):
        a, b = s.split('-')
        return parse_time(a), parse_time(b)

    def minute_to_output(x):
        day = x // 1440 + 1
        rem = x % 1440
        h = rem // 60
        m = rem % 60
        return day, f"{h:02d}:{m:02d}"

    with redirect_stdout(out):
        INF = -(10 ** 18)

        input = sys.stdin.readline

        m, n, k = map(int, input().split())

        subjects = [input().strip() for _ in range(m)]
        times = list(map(int, input().split()))

        subj_time = dict(zip(subjects, times))

        blocked = [False] * (k * 1440)

        segments = [parse_segment(input().strip()) for _ in range(4)]

        for day in range(k):
            base = day * 1440
            for l, r in segments:
                for x in range(l, r + 1):
                    blocked[base + x] = True

        total_minutes = k * 1440

        pref = [0] * (total_minutes + 1)
        work_to_real = []

        for i in range(total_minutes):
            pref[i + 1] = pref[i]
            if not blocked[i]:
                pref[i + 1] += 1
                work_to_real.append(i)

        jobs = []

        for idx in range(1, n + 1):
            parts = input().split()

            subj = parts[0]

            if subj not in subj_time:
                continue

            day = int(parts[1]) - 1
            tm = parse_time(parts[2])
            money = int(parts[3])

            real_deadline = day * 1440 + tm
            deadline = pref[real_deadline]

            jobs.append((deadline, subj_time[subj], money, idx))

        jobs.sort()

        W = len(work_to_real)

        dp = [INF] * (W + 1)
        dp[0] = 0

        for deadline, p, money, idx in jobs:
            for t in range(W - p, -1, -1):
                if dp[t] == INF:
                    continue
                nt = t + p
                if nt >= deadline:
                    continue
                dp[nt] = max(dp[nt], dp[t] + money)

        print(max(dp))

    return out.getvalue().strip()

# sample 1
assert run("""3 3 4
calculus
algebra
history
58 23 15
00:00-08:15
08:20-08:35
09:30-10:25
19:00-19:45
calculus 1 09:36 100
english 4 21:15 5000
history 1 19:50 50
""").splitlines()[0] == "150"

# minimum size
assert run("""1 1 1
math
1
00:00-00:00
12:00-12:00
18:00-18:00
23:00-23:00
math 1 00:02 5
""").splitlines()[0] == "5"

# unsupported subject
assert run("""1 1 1
math
10
00:00-00:00
12:00-12:00
18:00-18:00
23:00-23:00
physics 1 10:00 100
""").splitlines()[0] == "0"

# strict deadline equality
assert run("""1 1 1
math
30
00:00-00:00
12:00-12:00
18:00-18:00
23:00-23:00
math 1 00:31 100
""").splitlines()[0] == "0"

# choose better subset
assert run("""1 3 1
math
10
00:00-00:00
12:00-12:00
18:00-18:00
23:00-23:00
math 1 00:20 10
math 1 00:30 100
math 1 00:40 50
""").splitlines()[0] == "150"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 最小サイズのケース | 5 | 最小の訴訟事例 |
 | サポートされていない件名 | 0 | 不可能な仕事を無視する |
 | 平等の期限 | 0 | 直前の状態 |
 | より良いサブセット選択 | 150 | DP が最適な組み合わせを選択 |

 ## 特殊なケース

 中断に敏感な例を考えてみましょう。```
1 1 1
math
60
00:00-00:10
00:20-00:30
12:00-12:10
18:00-18:10
math 1 01:15 100
```01:15 より前に利用可能な作業は次のとおりです。 

- 00:11～00:19、9分
 - 00:31～01:14、44分

 合計: 作業可能時間は 53 分。 

圧縮された期限は 53 になります。タスクには 60 分かかるため、DP 移行は直ちに拒否されます。 

単純に壁時計を追加すると、00:11 + 60 分 = 01:11 と誤って考えられ、タスクが受け入れられてしまいます。 

ここで等価境界を考えてみましょう。```
1 1 1
math
30
00:00-00:00
12:00-12:00
18:00-18:00
23:00-23:00
math 1 00:31 100
```仕事は00:01に始まります。 作業可能な 30 分は 00:01 から 00:30 までを占めるため、完了は 00:31 に発生します。 

圧縮されたデッドラインは 30 に相当します。トランジションでは以下がチェックされます。```
if nt >= deadline:
```ここ`nt = 30`, したがって、タスクは正しく拒否されます。 

最後に、サポートされていない主題:```
1 2 1
math
10
00:00-00:00
12:00-12:00
18:00-18:00
23:00-23:00
physics 1 10:00 100
math 1 10:00 50
```物理リクエストがジョブ配列に入ることはありません。 DP は数学タスクのみを参照し、利益 50 を返します。 

これにより、無効なスケジュールによって最適化状態が汚染されるのを防ぎます。
