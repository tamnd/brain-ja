---
title: "CF 102280E - \u0428\u0442\u0440\u0430\u0444"
description: "私たちは個別の紙幣のコレクションを持っています。 各紙幣には額面があり、どの紙幣も最大 1 回しか使用できません。 細かい金額 p が与えられると、利用可能な紙幣のうち、総額が少なくとも p 以上でありながら可能な限り小さい紙幣を​​選択する必要があります。"
date: "2026-08-13T09:48:48+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102280
codeforces_index: "E"
codeforces_contest_name: "2010, \u0422\u0440\u0435\u043d\u0438\u0440\u043e\u0432\u043a\u0430 \u0421\u0413\u0410\u0423 aka \u041a\u043e\u043d\u0442\u0435\u0441\u0442 \u043f\u0440\u043e \u043c\u0430\u0440\u0448\u0440\u0443\u0442\u043a\u0438"
rating: 0
weight: 102280
solve_time_s: 189
verified: true
draft: false
---

[CF 102280E - \u0428\u0442\u0440\u0430\u0444](https://codeforces.com/problemset/problem/102280/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 9 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは個別の紙幣のコレクションを持っています。 各紙幣には額面があり、どの紙幣も最大 1 回しか使用できません。 罰金を与えられる`p`、利用可能な紙幣の中で、少なくとも合計額が可能な限り小さいものを選択する必要があります。`p`。 

出力には、最低支払額と、それを取得するために使用された実際の額面が含まれている必要があります。 紙幣の一部が届かない場合`p`、答えは`-1`。 

境界は、ソリューションを非常に強力に形成します。 多くてもあります`1000`紙幣、罰金は最大でも`100000`宗派は次のように大きくなります。`1000000`。 状態を含む従来のサブセット合計動的プログラム`0`に`p`そして紙幣ごとのコストの移行`O(n p)`、約に達することができます`10^8`状態の更新。 1.5 秒の制限では、特に Python では高すぎます。 状態セットがビットセットであるため、多くのサブセット合計遷移を同時に実行できるという事実を利用する必要があります。 

高額な額面の制限も重要です。 単純にDP範囲を作ることはできません`0..sum(q)`、合計値は次のように大きくなる可能性があるため、`10^9`。 幸いなことに、合計は`p`サブセット合計フェーズ中に表現する必要はありません。 最後の紙幣が合計を押し上げる可能性があります`p`。 

もっともらしい実装を黙って壊してしまう可能性のあるエッジケースがいくつかあります。 もし`p = 0`、空のセットはすでに有効な支払いであるため、答えは次のようになります。`0`、紙幣はありません。 例えば、`0 2`宗派と`5 10`生産しなければならない`0`、 ない`5`。 

重複した額面も別の紙幣になります。 のために`p = 6`そして`n = 3`と`3 3 10`、正解は`6`、両方を使用して`3`紙幣。 入力を宗派のセットとして扱うと、そのうちの 1 つが誤って失われます。 

罰金より大きい紙幣自体が最適な答えである可能性があります。 のために`p = 7`そして宗派`10 20`、答えは`10`。 DP は最大でも合計に制限されます`p`代表できない`10`したがって、アルゴリズムは紙幣を最終ステップとして個別に考慮する必要があります。 

ついに到達`p`より大きいすべての解を正確に上回る必要があります`p`。 のために`p = 10`そして宗派`6 4 20`、答えは`10`を使用して`6 + 4`。 より大きい最初の合計のみを検索するアプローチ`p`この最適なケースを見逃してしまいます。 

## アプローチ

 直接的なアプローチは、通常の 0/1 サブセット和動的計画法です。 させて`dp[s]`処理された紙幣の総額が正確にあるかどうかを教えてください`s`。 価値のある紙幣ごとに`q`、すべての合計を繰り返してマークします`s + q`到達可能なものとして。 これは正しいです。なぜなら、すべてのサブセットは現在の紙幣を除外するか含めるかのいずれかであり、合計を降順で繰り返すことで同じ紙幣を複数回使用することを防ぐからです。 

問題は操作回数です。 まであります`1000`紙幣と最大`100000`関連する金額を大まかに計算すると、`100000000`最悪の場合は移行します。 これでは制限時間が多すぎるため、Python はそのようなループには特に適していません。 

重要な観察は、DP 状態は単なる整数のセットであるということです。 このセットは 1 つの大きな整数のビットで表すことができます。 噛まれたら`s`設定されている、合計`s`到達可能です。 価値のある紙幣を追加する`q`単一の整数シフトになります。`bits | (bits << q)`シフトは現在の紙幣を受け取ることを表し、元の紙幣は`bits`スキップすることを表します。 Python の任意精度の整数は、機械語に対してこの演算を内部的に実行するため、処理の代わりに`p`状態を個別に処理するため、遷移では多くの状態が並行して処理されます。 

最低額だけではなく実際の紙幣が必要なので、もう 1 つ問題があります。 復興も同時に解決します。 初めて金額に到達するたびに、どの紙幣がその金額を作成したかを保存します。 合計額は、以前に到達不能だった場合にのみ記録されるため、現在の紙幣が処理される前に、その前任者はすでに到達可能でした。 これらの保存された先行データに従うと、有効なサブセットが再構築されます。 

また、答えがより大きい可能性にも対処する必要があります。`p`。 各紙幣を DP に追加する前に、この紙幣と組み合わせて少なくとも到達できる、現在到達可能なすべての金額を調べます。`p`。 紙幣を 1 枚ずつ処理するため、これらの合計には以前の紙幣のみが使用されるため、現在の紙幣が誤って 2 回使用されることはありません。 このような到達可能な最小の金額を選択し、現在の紙幣に関係する最小の候補を与えます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(n p)`|`O(p)`| 遅すぎる |
 | 最適なビットセット DP |`O(n p / W + p)`単語操作 |`O(p)`ビットと再構成データ | 承認済み |

 ここ`W`Python の大きな整数によって内部的に使用されるマシンワードのサイズです。 正確な実装コストは、DP 状態ごとの 1 つの操作ではなく、関係する整数のサイズによって決まります。 

## アルゴリズムのチュートリアル

 1.読む`p`そして紙幣のリスト。 もし`p = 0`、すぐに出力`0`なぜなら、何も支払わないということはすでに可能な最低額だからです。 
2. 現在到達可能なすべてのサブセットの合計を 1 つの整数で表します`bits`。 少し`s`合計のときに正確に設定されます`s`これまでに処理された紙幣から形成できます。 最初は合計のみ`0`到達可能なので、`bits = 1`。 
3.`parent`の合計によってインデックス付けされた配列`0`を通して`p`。 新たに到達可能な合計額ごとに`s`、それを作成した紙幣のインデックスを保存します。 の前身`s`それなら`s - q[index]`。 
4. 紙幣を左から右に処理します。 現在の紙幣を追加する前に`q`、現在を検索します`bits`到達可能な最小額`s`満足のいく`s + q >= p`。 このような合計が存在する場合、`s + q`は、現在の紙幣と以前の紙幣のみを使用した有効な回答候補です。 
5. 見つかった最小の候補を保持します。 候補者がまさにそうだったら`p`、グローバルに最適であるため、すぐに再構築します。 正確な支払いでは、これ以上の金額を改善することはできません。 
6. 次を使用して、現在の紙幣でサブセット合計ビットセットを更新します。`shifted = bits << q`。 結果を最大でも合計に制限する`p`より大きな中間合計は不要であるためです。 新たに到達可能な金額は次のとおりです。`shifted & ~bits`。 
7. 新たに到達可能な合計額ごとに、現在の紙幣インデックスを次の場所に保存します。`parent`。 これらの合計は初めて作成されるため、以前の状態にすでに含まれている可能性はありません。 
8. シフトされた状態をマージします。`bits`。 噛まれたら`p`設定されたら、正確な支払いを再構築します`parent[p]`なぜなら`p`それ自体は現在到達可能です。 
9. すべての紙幣が届かずに処理された場合`p`上記の候補が見つからないまま`p`、出力`-1`。 それ以外の場合は、保管されている親紙幣を繰り返し取り出し、現在の合計額からその額面を差し引くことによって、最良の候補を再構築します。 

機能する理由: 紙幣を処理する前`i`、`bits`より小さいインデックスを持つ紙幣を使用して取得できる金額が正確に含まれます。`i`。 候補者を検査する場合`s + q[i]`、紙幣`i`の一部ではありません`s`したがって、結果として得られるサブセットは有効です。 すべての紙幣についてそのような最小の候補を選択すると、その最大インデックス紙幣に従ってあらゆる可能な解決策が考慮されます。 正確な合計`p`はサブセット合計 DP によって直接処理されます。 再構築の場合、保存されているすべての親は、対応する紙幣が追加される前にすでに到達可能な合計額を指しているため、親を繰り返したどることで最終的に合計額に到達します。`0`そして有効なサブセットを生成します。 可能な支払いはすべて正確に次のいずれかであるため、`p`または追加が交差する最後の紙幣がある`p`、見つかった最小の候補が大域的最適値になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    first = input().split()
    if not first:
        return

    p, n = map(int, first)

    if n:
        q = list(map(int, input().split()))
    else:
        q = []

    if p == 0:
        print(0)
        print()
        return

    # Bit s is 1 iff sum s is reachable.
    # We only need sums from 0 through p.
    limit_mask = (1 << (p + 1)) - 1
    bits = 1  # sum 0

    # parent[s] = index of the banknote that first made s reachable.
    parent = [-1] * (p + 1)

    best_sum = None
    best_last = -1
    best_base = -1

    for i, value in enumerate(q):
        # Find the smallest currently reachable s such that
        # s + value >= p.
        threshold = max(0, p - value)

        candidates = bits >> threshold
        if candidates:
            # Position of the lowest set bit in candidates.
            offset = (candidates & -candidates).bit_length() - 1
            base = threshold + offset
            candidate = base + value

            if best_sum is None or candidate < best_sum:
                best_sum = candidate
                best_last = i
                best_base = base

        # If we already have an exact payment, it is optimal.
        if best_sum == p:
            break

        # Add this banknote to the subset-sum DP.
        shifted = (bits << value) & limit_mask
        new_bits = shifted & ~bits

        # Record reconstruction information for sums that are
        # becoming reachable for the first time.
        x = new_bits
        while x:
            low = x & -x
            s = low.bit_length() - 1
            parent[s] = i
            x -= low

        bits |= shifted

        # An exact payment is always better than every payment > p.
        if (bits >> p) & 1:
            best_sum = p
            best_last = -1
            best_base = p
            break

    if best_sum is None:
        print(-1)
        return

    answer = []

    if best_sum == p and best_last == -1:
        # Reconstruct an exact subset ending at sum p.
        cur = p
        while cur > 0:
            i = parent[cur]
            if i == -1:
                print(-1)
                return
            answer.append(q[i])
            cur -= q[i]
    else:
        # The last banknote is best_last, and the earlier banknotes
        # form best_base.
        answer.append(q[best_last])

        cur = best_base
        while cur > 0:
            i = parent[cur]
            if i == -1:
                print(-1)
                return
            answer.append(q[i])
            cur -= q[i]

    print(best_sum)
    print(*answer)

if __name__ == "__main__":
    solve()
```の`bits`integer は中心的な DP 構造です。 空のサブセットの合計が 0 であるため、ビット 0 が最初に設定されます。 宗派の場合`value`、シフトする`bits`が残した`value`その紙幣を受け取ることで得られるあらゆる金額を計算します。 シフトされた値と古いビットセットの OR 演算は、紙幣の両方の選択肢を表します。 

マスク`(1 << (p + 1)) - 1`より大きい合計を破棄します`p`。 上記の最適解はいずれも存在するため、このような合計は中間状態として必要ありません。`p`以下の到達可能な合計として表示できます`p`その後に最後の紙幣が続きます。 

表現`bits >> threshold`以下の到達可能な合計をすべて削除します`threshold`。 設定された最小ビットは、到達可能な最小ビットに対応します。`s >= threshold`。 これにより、可能な限り最小の合計が得られます`s + value`現在の最後の紙幣の場合。 

再構成配列には、次のもののみが埋められます。`new_bits`、すべての設定ビットからではありません`shifted`。 これは不可欠です。 すでに到達可能な金額は、その以前の親を保持する必要があります。これは、その親が現在の紙幣なしで形成されたサブセットを表すためです。 新たに到達可能な合計のみを処理することにより、保存されているすべての状態の前の状態がすでに確立されていることも保証されます。 

Python の整数はオーバーフローしないため、単位の合計自体は安全です。 DP 整数は明示的にマスクされます。`p + 1`これにより、サイズが制限され、大きな単位によって不必要に大きな中間整数が作成されるのを防ぎます。 

再構築では紙幣のインデックスを間接的に使用します。`parent`。 出力には金種の値のみが含まれている場合でも、各オカレンスには DP 中に個別のインデックスがあるため、金種が重複しても問題は発生しません。 

## 実用的な例

 ### サンプル 1

 入力:```
15 8
20 10 5 5 3 2 1 1
```最適な支払い方法は、`15`、 例えば`10 + 5`。 

| ステップ | 紙幣 | しきい値 | 最良の交差点候補 | 更新後の到達可能な合計 |
 | --- | --- | --- | --- | --- |
 | 1 | 20 | 0 | 20 | 0 |
 | 2 | 10 | 5 | なし | 0、10 |
 | 3 | 5 | 10 | 15 | 0、5、10、15 |

 3枚目の紙幣の合計`10`すでに2枚目の紙幣を使用して到達可能でした。 追加`5`正確に与える`15`、アルゴリズムが停止する可能性があります。 保存された親が再構築される`10`そして`5`。 

これは、現在の紙幣がビットセットに挿入される前にチェックされる理由を示しています。 候補者`10 + 5`2 つの異なる紙幣を使用します。 

### サンプル 2

 入力:```
2 3
10 3 3
```答えは`3`、シングルなので`3`少なくとも利用可能な最小の合計です`2`。 

| ステップ | 紙幣 | しきい値 | 最良の交差点候補 | 更新後の到達可能な合計 |
 | --- | --- | --- | --- | --- |
 | 1 | 10 | 0 | 10 | 0 |
 | 2 | 3 | 0 | 3 | 0 |
 | 3 | 3 | 0 | 3 | 0 |

 最初の紙幣は候補者を与える`10`。 2番目は候補を与えます`3`、どちらの方が良いです。 3番目も与えます`3`、しかし答えは改善されません。 

興味深い詳細は、上記の合計です`p`には保存されていません`bits`。 宗派`10`たとえビットであっても、最終紙幣として正しくみなされます。`10`DPには一切登場しません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(n p / W + p)`単語操作、プラス`O(p)`再構成と親の割り当ての合計 | 各ビットセット遷移プロセス`p`マシンワードのチャンク内のビットを受け取りますが、到達可能なすべての合計は最大 1 回だけ親を受け取ります。 
| スペース |`O(p)`DP のビットと`O(p)`親の整数 | 以下の合計のみ`0`を通して`p`保存されています |

 と`p <= 100000`、ビットセット自体は、生のビットとして表した場合、わずか約 12.5 KB です。 Python の整数はオブジェクトであるため、親配列は大きくなりますが、それでも格納されるのは`p + 1`エントリ。 アルゴリズムは次のことを回避します。`O(n p)`Python レベルのネストされたループは、1.5 秒の制限の下で主なパフォーマンスの問題となります。 

## テストケース

 ステートメントでは最適なサブセットが許可されているため、正確な出力はサンプルとは当然異なる可能性があります。 そのため、以下のテスト ヘルパーは、1 つの特定の順序やサブセットを必要とするのではなく、出力を意味的に検証します。```python
import sys
import io

def solve_io(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    try:
        solve()
        return sys.stdout.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

def validate(inp: str, out: str):
    data = inp.split()
    p = int(data[0])
    n = int(data[1])
    bills = list(map(int, data[2:2 + n]))

    lines = out.strip().splitlines()

    if p == 0:
        assert lines[0] == "0"
        return

    if lines[0] == "-1":
        # Verify that no subset can reach p by brute force.
        reachable = {0}
        for x in bills:
            reachable |= {s + x for s in list(reachable)}
        assert all(s < p for s in reachable)
        return

    total = int(lines[0])
    used = list(map(int, lines[1].split())) if len(lines) > 1 and lines[1].strip() else []

    assert total == sum(used)
    assert total >= p

    remaining = bills[:]
    for x in used:
        assert x in remaining
        remaining.remove(x)

    # Verify optimality independently for these small test cases.
    reachable = {0}
    for x in bills:
        reachable |= {s + x for s in list(reachable)}

    optimum = min((s for s in reachable if s >= p), default=None)
    assert optimum == total

def solve():
    first = input().split()
    if not first:
        return

    p, n = map(int, first)
    q = list(map(int, input().split())) if n else []

    if p == 0:
        print(0)
        print()
        return

    limit_mask = (1 << (p + 1)) - 1
    bits = 1
    parent = [-1] * (p + 1)

    best_sum = None
    best_last = -1
    best_base = -1

    for i, value in enumerate(q):
        threshold = max(0, p - value)

        candidates = bits >> threshold
        if candidates:
            offset = (candidates & -candidates).bit_length() - 1
            base = threshold + offset
            candidate = base + value

            if best_sum is None or candidate < best_sum:
                best_sum = candidate
                best_last = i
                best_base = base

        if best_sum == p:
            break

        shifted = (bits << value) & limit_mask
        new_bits = shifted & ~bits

        x = new_bits
        while x:
            low = x & -x
            s = low.bit_length() - 1
            parent[s] = i
            x -= low

        bits |= shifted

        if (bits >> p) & 1:
            best_sum = p
            best_last = -1
            best_base = p
            break

    if best_sum is None:
        print(-1)
        return

    answer = []

    if best_sum == p and best_last == -1:
        cur = p
        while cur:
            i = parent[cur]
            assert i != -1
            answer.append(q[i])
            cur -= q[i]
    else:
        answer.append(q[best_last])
        cur = best_base
        while cur:
            i = parent[cur]
            assert i != -1
            answer.append(q[i])
            cur -= q[i]

    print(best_sum)
    print(*answer)

# Provided sample 1
sample1 = """15 8
20 10 5 5 3 2 1 1
"""
out = solve_io(sample1)
validate(sample1, out)

# Provided sample 2
sample2 = """2 3
10 3 3
"""
out = solve_io(sample2)
validate(sample2, out)

# p = 0, empty payment is optimal.
case3 = """0 0
"""
out = solve_io(case3)
validate(case3, out)

# Exact boundary, requires two equal banknotes.
case4 = """6 3
3 3 10
"""
out = solve_io(case4)
validate(case4, out)

# No possible payment.
case5 = """100 3
20 30 40
"""
out = solve_io(case5)
validate(case5, out)

# Large denomination should be considered as a final banknote.
case6 = """7 2
10 20
"""
out = solve_io(case6)
validate(case6, out)
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`15 8 / 20 10 5 5 3 2 1 1`| 任意のサブセットの合計`15`| 提供されたサンプルと正確な支払いの再構築 |
 |`2 3 / 10 3 3`|`3`1つで`3`| 罰金および重複値よりも大きい額面 |
 |`0 0`|`0`| 最小のファインおよび空のサブセット |
 |`6 3 / 3 3 10`|`6`両方とも`3`紙幣 | 同じ額面の複数のコピー |
 |`100 3 / 20 30 40`|`-1`| 不可能なターゲット |
 |`7 2 / 10 20`|`10`1つで`10`| 厳密に次より大きいと答えてください`p`|

 ## 特殊なケース

 のために`p = 0`、入力`0 0`DP が開始される前に処理されます。 空のサブセットには合計があります`0`、アルゴリズムは次のように出力します`0`そして空の 2 行目。 少なくとも 1 枚の紙幣を強制しようとすると、最小限ではない答えが得られます。 

宗派が重複する場合は、次の点を考慮してください。`6 3`と`3 3 10`。 最初`3`合計を計算します`3`到達可能、そして 2 番目`3`その後合計を計算します`6`到達可能な。 合計の親`6`sum の親は 2 番目の紙幣を指します。`3`最初のものを指します。 チェーンをたどると、2 枚の別々の紙幣が得られ、どちらも価値があります。`3`。 

目標額を超える額面の場合は、次のことを検討してください。`7 2`と`10 20`。 最初の紙幣が追加される前に合計`0`到達可能です。 以来`0 >= 7 - 10`、候補者`0 + 10 = 10`すぐに記録されます。 合計の DP 状態がありません`10`が必要です。 これがまさに、ビットセットの移行前に最終紙幣チェックが実行される理由です。 

正確な支払いについては、次の点を考慮してください。`10 3`と`6 4 20`。 加工前`4`、合計`6`はすでに到達可能です。 のしきい値`4`は`6`、アルゴリズムは候補を見つけます`6 + 4 = 10`。 アルゴリズムは正確で紙幣で止まります`6`そして`4`。 次のような解決策`20`すべての合計が次の値に等しいため、それを置き換えることは決して許可されません。`p`が最適です。 

不可能な支払いについては、次のことを検討してください。`100 3`と`20 30 40`。 すべてのサブセットは最大でも合計を持ちます`90`したがって、ビットセットはビットを設定しません`100`、最終紙幣候補は到達しない`100`。 その結果、アルゴリズムは次のように出力します。`-1`。
