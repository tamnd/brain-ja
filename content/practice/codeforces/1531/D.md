---
title: "CF 1531D - \u0420\u0435\u0434\u0430\u043a\u0442\u0438\u0440\u0443\u0435\u043c \u0417\u0438\u043d\u0433\u0435\u0440 | 色"
description: "ボットメッセージの時系列リストがあります。 ボットは 2 つの状態を保持します。 1 つ目は、色の変更が現在ロックされているかどうかです。 2つ目は現在のドームの色で、虹色の7色のうちの1つです。 最初はドームは青色で、色の変更はロック解除されています。"
date: "2026-06-10T16:48:08+07:00"
tags: ["codeforces", "competitive-programming", "*special"]
categories: ["algorithms"]
codeforces_contest: 1531
codeforces_index: "D"
codeforces_contest_name: "VK Cup 2021 - \u041a\u0432\u0430\u043b\u0438\u0444\u0438\u043a\u0430\u0446\u0438\u044f (Engine)"
rating: 0
weight: 1531
solve_time_s: 127
verified: true
draft: false
---

[CF 1531D - \u0420\u0435\u0434\u0430\u043a\u0442\u0438\u0440\u0443\u0435\u043c \u0417\u0438\u043d\u0433\u0435\u0440 | カラー](https://codeforces.com/problemset/problem/1531/D)

 **評価:** -
 **タグ:** *特別な
 **解決時間:** 2 分 7 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 ボットメッセージの時系列リストがあります。 ボットは 2 つの状態を保持します。 

1 つ目は、色の変更が現在ロックされているかどうかです。 

2つ目は現在のドームの色で、虹色の7色のうちの1つです。 

最初はドームは青色で、色の変更はロック解除されています。 

各メッセージは現在の状態に基づいて動作します。 

あ`lock`システムがまだロックされていない場合、このメッセージはシステムをロック状態にします。 

アン`unlock`システムがまだロック解除されていない場合、このメッセージはシステムをロック解除状態にします。 

システムが現在ロック解除されている場合にのみ、カラー メッセージによってドームの色が変わります。 システムがロックされている場合、カラー メッセージは無視されます。 

最初の履歴の後、最大で次の情報を受け取ります。$10^5$編集します。 編集するたびに、履歴内の 1 つのメッセージが置き換えられます。 編集のたびに、履歴全体を元の初期状態から再処理する必要があり、結果のドーム カラーを出力する必要があります。 

制約こそが本当の課題なのです。 メッセージ数と編集数の両方が到達可能$10^5$。 すべての編集後に履歴全体を再生するには、おおよその時間がかかります$10^{10}$最悪の場合、コンテストの制限時間を大幅に超えてメッセージを申請することになります。 

重要な観察は、ボットの状態空間が小さいということです。 7 つの色と 2 つのロック状態しかないため、可能な状態は 14 つだけです。 

ロック中にカラー メッセージが実行されると、微妙なエッジ ケースが発生します。 

例：```
lock
red
unlock
```最終的な色はまだ青です。 の`red`コマンドは、到着時にシステムがロックされていたため無視されます。 

もう一つの簡単な間違いは治療です`lock`そして`unlock`無条件の割り当てとして。 

例：```
lock
lock
unlock
```2番目`lock`何もしません。 メッセージはメッセージ履歴自体ではなく現在の状態に基づいて動作するため、動作は依然として明確に定義されています。 

3 番目のエッジ ケースは、古いメッセージを編集する場合です。 

例：```
1: red
2: lock
3: green
```最初の結果は次のとおりです`red`。 

メッセージ 2 を編集すると`unlock`、その後、メッセージ 3 が突然有効になり、答えは次のようになります。`green`。 

状態遷移構造を再考せずにサフィックスの色だけを更新しようとする解決策は失敗します。 

## アプローチ

 ブルートフォースの解決策は簡単です。 メッセージリストをプログラムとして扱います。 初期状態から全ての処理を行う$n$メッセージを受け取り、最終的な色を取得します。 編集するたびにメッセージを変更し、シミュレーション全体を再度実行します。 

これが機能するのは、ボットのルールが決定的であるためです。 問題はコストです。 各クエリに必要なのは$O(n)$仕事、そして、$10^5$クエリ。 全体の複雑さは次のようになります。$O(nt)$に達する$10^{10}$操作。 

重要な観察は、すべてのメッセージが実際には 14 の可能な状態のセットの関数であるということです。 

例えば、`lock`すべてのロック解除状態を対応するロック状態にマップし、ロック状態を変更しないままにします。 

次のようなカラーコマンド`red`すべてのロック解除状態をロック解除状態の赤の状態にマップし、すべてのロック状態を変更しないままにします。 

各メッセージは状態遷移関数であるため、メッセージのセグメント全体は単にそれらの関数の構成にすぎません。 

これはすぐにセグメント ツリーを示唆します。 

ノードごとに、そのセグメントによって表される関数を保存します。 状態空間には 14 個の状態しか含まれていないため、関数は長さ 14 の配列として格納できます。`f[s]`セグメントを状態に適用した後の結果の状態です`s`。 

2 つの子セグメントを結合するときに、それらの関数を構成します。 左側のセグメントが表す場合`L`そして右側のセグメントは`R`、親は次のことを表します`R ∘ L`これは、左半分からのメッセージが右半分からのメッセージより前に実行されるためです。 

ポイント更新では 1 つのメッセージが変更されるため、$O(\log n)$セグメント ツリー ノードを再計算する必要があります。 各再計算はサイズ 14 の 2 つの関数を構成し、これは一定の作業です。 

答えは初期状態に root 関数を適用することで得られます。`(unlocked, blue)`そして結果の色を抽出します。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(nt)$|$O(1)$| 遅すぎる |
 | 最適 |$O(14 \cdot (n + t \log n))$|$O(14n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 考えられるすべてのボット状態を 0 ～ 13 の整数としてエンコードします。 
2. 状態を次のように表現します。`(locked, color)`。 
3. 考えられるすべてのメッセージ タイプについて、14 状態すべてについてその遷移関数を事前計算します。 
4. 各リーフに 1 つのメッセージに対応する関数を格納するセグメント ツリーを構築します。 
5. 内部ノードの場合、その子の関数を構成します。 もし`left`そして`right`が子関数である場合、次のようになります。`parent[s] = right[left[s]]`左側のセグメントが最初に実行されるためです。 
6. 現在の答えを取得するには、root 関数を初期状態に適用します。`(unlocked, blue)`。 
7. 結果の状態の色コンポーネントを抽出し、その名前を出力します。 
8. 編集操作の場合は、対応するリーフ関数を置き換えます。 
9. 同じ合成ルールを使用して、ルートまでのすべての祖先を再計算します。 
10. ルートを再度クエリし、結果の色を出力します。 

### なぜ効果があるのか

 各メッセージは、14 状態のオートマトンの決定論的変換を定義します。 関数の構成は、メッセージの順次実行と正確に一致します。 セグメント ツリー ノードには、その区間全体によって生成された変換が格納されます。 ツリー構造上の帰納法により、すべてのノードは、そのセグメント内のすべてのメッセージを処理した結果を正確に表します。 

ルート間隔は履歴全体をカバーするため、その機能はまさに初期状態から完全なメッセージ シーケンスを再生する効果となります。 ポイントの更新後は、その位置を含むセグメントのみが変更され、コンポジションを再計算すると、影響を受けるすべてのノードの正しい変換が復元されます。 したがって、報告されたすべての色は完全な再生の結果と一致します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

colors = ["red", "orange", "yellow", "green", "blue", "indigo", "violet"]
color_id = {c: i for i, c in enumerate(colors)}

STATE_COUNT = 14

def state_id(locked, color):
    return locked * 7 + color

# Precompute transition function for every possible message.
funcs = {}

for msg in ["lock", "unlock"] + colors:
    f = [0] * STATE_COUNT

    for locked in range(2):
        for color in range(7):
            s = state_id(locked, color)

            if msg == "lock":
                ns = state_id(1, color) if locked == 0 else s
            elif msg == "unlock":
                ns = state_id(0, color) if locked == 1 else s
            else:
                if locked == 0:
                    ns = state_id(0, color_id[msg])
                else:
                    ns = s

            f[s] = ns

    funcs[msg] = f

def compose(left, right):
    # right ∘ left
    return [right[left[i]] for i in range(STATE_COUNT)]

n = int(input())
messages = [input().strip() for _ in range(n)]

size = 1
while size < n:
    size <<= 1

identity = list(range(STATE_COUNT))
seg = [identity[:] for _ in range(2 * size)]

for i in range(n):
    seg[size + i] = funcs[messages[i]]

for i in range(size - 1, 0, -1):
    seg[i] = compose(seg[i * 2], seg[i * 2 + 1])

initial_state = state_id(0, color_id["blue"])

def answer():
    final_state = seg[1][initial_state]
    color = final_state % 7
    return colors[color]

out = [answer()]

t = int(input())

for _ in range(t):
    pos, msg = input().split()
    pos = int(pos) - 1

    p = size + pos
    seg[p] = funcs[msg]

    p //= 2
    while p:
        seg[p] = compose(seg[p * 2], seg[p * 2 + 1])
        p //= 2

    out.append(answer())

sys.stdout.write("\n".join(out))
```状態エンコーディングは最も重要な実装の詳細です。 すべての状態は、ロック フラグと現在の色の両方を一意に表す必要があります。 使用する`locked * 7 + color`0 ～ 13 のコンパクトな範囲が得られます。 

各メッセージは長さ 14 の遷移テーブルに変換されます。 これにより、問題はメッセージのシミュレーションから関数の作成へと変わります。 

セグメント ツリーには、スカラー値ではなく完全な関数が格納されます。 構成順序は重要です。 左側のセグメントが右側のセグメントの前に実行される場合、親は次のセグメントを表す必要があります。`right[left[s]]`。 この順序を逆にすると、不正確な答えが生成されます。 

未使用のリーフには恒等関数が使用されます。 ID を使用して構成しても何も変わらないため、ツリーをパディングしても安全になります。 

## 実用的な例

 ### サンプル 1

 初期履歴:

 | ステップ | メッセージ | ロック中 | 色 |
 | --- | --- | --- | --- |
 | 開始 | - | いいえ | 青 |
 | 1 | 赤 | いいえ | 赤 |
 | 2 | バイオレット | いいえ | バイオレット |
 | 3 | ロックを解除する | いいえ | バイオレット |
 | 4 | 赤 | いいえ | 赤 |
 | 5 | オレンジ | いいえ | オレンジ |
 | 6 | ロック | はい | オレンジ |
 | 7 | インディゴ | はい | オレンジ |

 最終的な答えは、`orange`。 

最後`indigo`システムがロックされているため、無視されます。 

メッセージ 5 を編集後、`green`、同じリプレイが生成します`green`、これはサンプル出力と一致します。 

### カスタム例```
3
lock
red
unlock
```| ステップ | メッセージ | ロック中 | 色 |
 | --- | --- | --- | --- |
 | 開始 | - | いいえ | 青 |
 | 1 | ロック | はい | 青 |
 | 2 | 赤 | はい | 青 |
 | 3 | ロックを解除する | いいえ | 青 |

 結果は残る`blue`。 

この例は、カラー コマンドを無条件の割り当てとして扱うことができない理由を示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(14 \cdot (n + t \log n))$| ビルドには関数の構成が必要で、各アップデートはタッチします$O(\log n)$ノード |
 | スペース |$O(14n)$| すべてのセグメント ツリー ノードには、サイズ 14 | の関数が格納されます。 

14 は固定定数であるため、実際の複雑さは本質的に次のようになります。$O(n + t \log n)$快適に扱える$10^5$メッセージと$10^5$編集します。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    colors = ["red", "orange", "yellow", "green", "blue", "indigo", "violet"]
    color_id = {c: i for i, c in enumerate(colors)}

    STATE_COUNT = 14

    def state_id(locked, color):
        return locked * 7 + color

    funcs = {}

    for msg in ["lock", "unlock"] + colors:
        f = [0] * STATE_COUNT
        for locked in range(2):
            for color in range(7):
                s = state_id(locked, color)

                if msg == "lock":
                    ns = state_id(1, color) if locked == 0 else s
                elif msg == "unlock":
                    ns = state_id(0, color) if locked == 1 else s
                else:
                    ns = state_id(0, color_id[msg]) if locked == 0 else s

                f[s] = ns
        funcs[msg] = f

    def compose(a, b):
        return [b[a[i]] for i in range(STATE_COUNT)]

    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline

    n = int(input())
    msgs = [input().strip() for _ in range(n)]

    size = 1
    while size < n:
        size <<= 1

    identity = list(range(STATE_COUNT))
    seg = [identity[:] for _ in range(2 * size)]

    for i in range(n):
        seg[size + i] = funcs[msgs[i]]

    for i in range(size - 1, 0, -1):
        seg[i] = compose(seg[i * 2], seg[i * 2 + 1])

    initial = state_id(0, color_id["blue"])

    def ans():
        s = seg[1][initial]
        return colors[s % 7]

    out = [ans()]

    t = int(input())
    for _ in range(t):
        p, msg = input().split()
        p = int(p) - 1

        idx = size + p
        seg[idx] = funcs[msg]

        idx //= 2
        while idx:
            seg[idx] = compose(seg[idx * 2], seg[idx * 2 + 1])
            idx //= 2

        out.append(ans())

    return "\n".join(out)

# provided sample
assert run(
"""7
red
violet
unlock
red
orange
lock
indigo
6
5 green
6 lock
6 yellow
4 lock
1 lock
5 unlock
"""
) == """orange
green
green
indigo
violet
blue
indigo"""

# minimum size
assert run(
"""1
blue
1
1 red
"""
) == """blue
red"""

# locked color ignored
assert run(
"""2
lock
red
1
2 green
"""
) == """blue
blue"""

# unlock enables later color
assert run(
"""3
lock
red
unlock
1
2 green
"""
) == """blue
blue"""

# edit old message changes future behavior
assert run(
"""3
red
lock
green
1
2 unlock
"""
) == """red
green"""
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一メッセージが編集されました | 青→赤 | 最小サイズ |
 |`lock`次に色を付けます | 青 | ロック中は色が無視される |
 |`lock`、 色、`unlock`| 青 | ロックを解除しても古い色は再生されません。 
| 編集`lock`の中へ`unlock`| 赤→緑 | 以降のメッセージに影響を与える過去の編集 |

 ## 特殊なケース

 次のことを考慮してください。```
2
lock
red
```カラーコマンドが到着する前にシステムがロックされます。 トランジション関数`red`すべてのロック状態は変更されないため、最終的な色は青色のままになります。 セグメント ツリー構成では、この動作が自然に保存されます。 

考慮する：```
3
lock
lock
unlock
```2番目`lock`ロック状態をそれ自体にマップします。 その遷移関数は冪等です。 最初のもので構成する`lock`単一のロック操作とまったく同じ効果が得られます。 

考慮する：```
3
red
lock
green
```答えは`red`。 メッセージ 2 が次のように編集された場合`unlock`、その後 3 番目のメッセージが有効になり、応答が次のように変わります。`green`。 更新では 1 つのリーフのみが変更されますが、再計算された構成により、その効果が履歴全体に自動的に伝播されます。
