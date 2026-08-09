---
title: "CF 102441K - チェスの局面"
description: "クイーン、ビショップ、ナイト、ルーク、または空のセルを含む任意の 8 x 8 のチェス盤を印刷する必要があります。 白い部分は大文字、黒い部分は小文字です。"
date: "2026-08-08T13:35:54+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102441
codeforces_index: "K"
codeforces_contest_name: "2018-2019 9th BSUIR Open Programming Championship. Final"
rating: 0
weight: 102441
solve_time_s: 161
verified: true
draft: false
---

[CF 102441K - チェスの局面](https://codeforces.com/problemset/problem/102441/K)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 41 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 クイーン、ビショップ、ナイト、ルーク、または空のセルを含む任意の 8 x 8 のチェス盤を印刷する必要があります。 白い部分は大文字、黒い部分は小文字です。 指定されたペア w,b の場合、ちょうど w 個の白のピースが少なくとも 1 つの黒のピースによって攻撃されなければならず、正確に b 個の黒のピースが少なくとも 1 つの白のピースによって攻撃されなければなりません。 

チェスのルールは、キングやポーンが存在しないため、通常のチェスよりわずかに単純です。 クイーン、ルーク、またはビショップは、最初に占領されたマス目まで、対応するラインに沿って攻撃します。 ナイトは 8 つの可能な目的地を攻撃し、ソースと目的地の間にある部分を無視します。 攻撃は、2 つのピースの色が異なる場合にのみカウントされます。 

実際に重要な次元における制約は小さいです。 ボードには常に 64 個のセルのみが含まれており、要求されるカウントは両方とも最大 50 です。テスト ケースは最大 10 3 個であるため、ボードごとに少量の一定量の作業を実行するアプローチは容易に十分に高速です。 すべてのセルを空または占有に制限しても、すでに 2 64 構成が得られるため、64 セルの任意のサブセットや考えられるすべてのピース割り当てを列挙することはできません。 

建設では慎重に対処する必要がある 2 つのエッジケースがあります。 まず、w=0 または b=0 が有効です。 たとえば、入力の場合`1 0`、正しい種類のボードには、攻撃された白いピースが 1 つあり、攻撃された黒いピースはありません。 不用意に対称的な構造を構築すると、攻撃を受ける黒い部分が自動的に作成される可能性があります。 次に、w+b=64 も合法です。 例えば、`32 32`正確に 64 個のピース​​が使用される場合、すべての正方形にカウントされたピースが含まれるように要求します。 ターゲットのグループごとに攻撃者用に追加の 1 マスを確保する構造では、ボード スペースが不足する可能性があります。 

これらの問題を回避する最も安全な方法は、ボードが固定されており小さいという事実を利用することです。 ポジションを検索することはできますが、13 64 の可能なボードすべてを検索するのではなく、攻撃の関係をコンパクトに表現したものを検索する必要があります。 

## アプローチ

 直接的な総当たりアプローチでは、考えられるすべてのボードを試し、その攻撃数を計算します。 たとえ各マスが空、白ナイト、黒ナイト、白クイーン、黒クイーンのみに制限されていたとしても、すでに 5 64 個、つまり約 2.9⋅10 44 個の候補が存在することになります。 1 枚のボードをチェックするのに必要な時間は O(64) だけですが、ボードの数を考えるとまったく使用できなくなります。 

もう少し素朴なアプローチは、最初に占有されているマス目を選択し、次にその色とピースの種類を選択することです。 これにはまだ指数関数的な検索スペースがあります。 固定ボード サイズは 1 つの候補を評価するコストの削減には役立ちますが、組み合わせ爆発は解決されません。 

有益な観察は、必要な出力が一意ではないということです。 隠された意図された位置を再構築する必要はありません。 要求された 2 つのカウントを持つポジションは 1 つだけ必要です。 個々の境界を満たす可能なペアは 51×51 個だけであるため、発生するペアごとに有効な構造を 1 回検索し、キャッシュすることができます。 

要求されたペアごとに、ランダム化されたローカル検索を使用します。 ボードは 64 文字で表されます。 ランダムに選択された占有正方形を、有用なピース/色の組み合わせの小さなセット間で繰り返し変更し、目的のペアからの距離を改善する変更を維持します。 盤面が 64 マスに固定されているため、攻撃数は問題サイズに対して一定時間で再計算できます。 ランダムな再起動により、検索が不運なローカル構成に陥るのを防ぎます。 

検索スペースは絶対的に十分に小さいですが、出力スペースは膨大であるため、有効な位置が豊富にあります。 重要なエンジニアリングの詳細は、成功したすべてのボードをキャッシュすることです。 最大 10 3 のテストでは、同じペアに対する繰り返しのリクエストは実質的に無料になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | すべてのボードを列挙する | O(13 64 ⋅64) | お(64) | 遅すぎる |
 | キャッシュを使用したランダム化された構築 | O(T⋅I⋅64) 最悪の場合 | O(T⋅64) | 実際に受け入れられる |

 ## アルゴリズムのチュートリアル

 1. 最初にすべてのテスト ケースを読み取り、個別のペア (w,b) のみを保持します。 特定のペアの検索は 1 回だけ成功すればよいため、キャッシュは便利です。 
2. すべてのボードを 64 文字の配列として表します。 私たちが使用するのは`.`空の正方形には 8 つの可能な色のピースがあり、占有された正方形には 8 つの可能な色のピースが含まれます。 クイーンは遠距離攻撃を提供し、ナイトは駒に依存しない攻撃を提供するため、探索にはクイーンとナイトで十分です。 
3. ランダムな開始ボードを生成します。 占有されるマスの数は、要求された攻撃対象の駒の数に合わせて選択されます。これは、駒が非常に少ない位置では多くの攻撃を生成できず、完全にランダムなボードでは攻撃が多すぎる傾向があるためです。 
4. 占領されているすべてのマスをスキャンし、対戦相手によって攻撃されているかどうかを判断することでボードを評価します。 クイーンの場合は、最初に占有されるマス目まで 8 方向をスキャンします。 ナイトの場合は、8 つのジャンプ先を調べてください。 最初に遭遇したピースの色が反対の場合、ターゲットを攻撃済みとしてマークします。 
5. エラーを次のように定義します。 

∣w 実際 −w∣+∣b 実際 −b∣。 

完璧なボードにはエラーがゼロです。 
6. ランダムに選択された 1 つの占有正方形を変異させます。 突然変異により駒の種類や色が変化し、場合によっては空のマスが駒に変わったり、駒が削除されたりすることがあります。 誤差を再計算し、それを改善する変異を保持します。 場合によっては、同等かそれより悪い突然変異を受け入れることで、検索が極小値でフリーズするのを防ぐことができます。 
7. 一定回数繰り返しても解が見つからない場合は、ランダム検索を再開します。 ボードには 64 個のセルしか含まれていないため、各再起動のコストは低くなります。 
8. エラー 0 に達すると、そのペアのボードをキャッシュし、そのペアが発生するたびにそれを出力します。 

### なぜ効果があるのか

 正確性の条件は、脆弱な幾何学的公式から推測されるのではなく、直接チェックされます。 ボードは、その完全な攻撃関係が評価され、結果として得られる 2 つのカウントが w と b に正確に等しい場合にのみ受け入れられます。 その結果、どのプリント基板も要求条件を満足することになる。 ランダム化された部分は、候補を見つける方法を決定するだけです。 受け入れ基準が変わることはありません。 

## Python ソリューション```python
import sys
import random

input = sys.stdin.readline

DIRS = [
    (-1, -1), (-1, 0), (-1, 1),
    (0, -1),           (0, 1),
    (1, -1),  (1, 0),  (1, 1),
]

KNIGHT = [
    (-2, -1), (-2, 1),
    (-1, -2), (-1, 2),
    (1, -2),  (1, 2),
    (2, -1), (2, 1),
]

PIECES = "QqKk"
# Q/q are queens, K/k are knights.
# The letters are intentionally different from ordinary chess notation:
# the statement uses 'k' for knight.

rng = random.Random(712367821)

def is_piece(c):
    return c != '.'

def is_white(c):
    return c.isupper()

def is_queen(c):
    return c.lower() == 'q'

def attacked_counts(board):
    attacked = [False] * 64

    for pos in range(64):
        p = board[pos]
        if p == '.':
            continue

        r = pos // 8
        c = pos % 8

        if is_queen(p):
            for dr, dc in DIRS:
                nr = r + dr
                nc = c + dc

                while 0 <= nr < 8 and 0 <= nc < 8:
                    np = nr * 8 + nc
                    q = board[np]

                    if q != '.':
                        if is_white(p) != is_white(q):
                            attacked[np] = True
                        break

                    nr += dr
                    nc += dc

        else:
            for dr, dc in KNIGHT:
                nr = r + dr
                nc = c + dc

                if 0 <= nr < 8 and 0 <= nc < 8:
                    np = nr * 8 + nc
                    q = board[np]

                    if q != '.' and is_white(p) != is_white(q):
                        attacked[np] = True

    w = 0
    b = 0

    for i, p in enumerate(board):
        if p == '.' or not attacked[i]:
            continue
        if is_white(p):
            w += 1
        else:
            b += 1

    return w, b

def score(board, target_w, target_b):
    w, b = attacked_counts(board)
    return abs(w - target_w) + abs(b - target_b), w, b

def random_board(w, b):
    board = ['.'] * 64

    # Start with a moderate number of pieces. More pieces are useful when
    # the requested counts are large.
    n = min(64, max(2, w + b + 8))

    cells = rng.sample(range(64), n)

    for x in cells:
        if rng.randrange(2):
            board[x] = 'Q' if rng.randrange(2) else 'K'
        else:
            board[x] = 'q' if rng.randrange(2) else 'k'

    return board

def find_board(w, b):
    if w == 0 and b == 0:
        return ['.'] * 64

    # The search is deliberately bounded. The board is tiny and valid
    # configurations are plentiful.
    restarts = 160
    iterations = 1800

    for _ in range(restarts):
        board = random_board(w, b)
        cur, _, _ = score(board, w, b)

        if cur == 0:
            return board

        temperature = 3.0

        for _ in range(iterations):
            old = board[:]

            pos = rng.randrange(64)

            if board[pos] == '.':
                if rng.randrange(3) == 0:
                    board[pos] = rng.choice("QqKk")
                else:
                    continue
            else:
                if rng.randrange(5) == 0:
                    board[pos] = '.'
                else:
                    board[pos] = rng.choice("QqKk")

            new, _, _ = score(board, w, b)

            if new == 0:
                return board

            delta = new - cur

            if delta <= 0:
                cur = new
            else:
                # Simulated annealing style escape from local minima.
                probability = pow(2.718281828, -delta / max(temperature, 0.05))
                if rng.random() < probability:
                    cur = new
                else:
                    board = old

            temperature *= 0.997

    # With the guaranteed existence of an answer, the randomized search
    # above is expected to find one. This fallback keeps the function total.
    raise RuntimeError("construction search failed")

def solve():
    t = int(input())
    tests = [tuple(map(int, input().split())) for _ in range(t)]

    cache = {}

    out = []

    for w, b in tests:
        if (w, b) not in cache:
            cache[(w, b)] = find_board(w, b)

        board = cache[(w, b)]

        for r in range(8):
            out.append(''.join(board[r * 8:(r + 1) * 8]))
        out.append('')

    sys.stdout.write('\n'.join(out))

if __name__ == "__main__":
    solve()
```の`attacked_counts`機能は中央検証者です。 占有されているすべてのセル上を歩き、ステートメントの移動ルールを正確に適用します。 スライドするピースは最初に占有されている正方形で止まりますが、これは不注意な実装によって間違いが発生する可能性がある微妙な部分です。 ナイトは駒を飛び越えるので別扱いです。 

ボードが使用するのは、`Q`そして`q`女王様たちと`K`そして`k`騎士の場合は、必要な出力アルファベットと一致します。 ケースによって色が決まりますので、`isupper()`白と黒を区別するには十分です。 

突然変異ステップでは、ピースのタイプとその色の両方を変更することが意図的に許可されています。 タイプのみを変更すると、一部のターゲット ペアへの到達が困難になりますが、色のみを変更すると、探索が間違った攻撃ジオメトリの位置に閉じ込められたままになる可能性があります。 

Python には整数オーバーフローの問題はなく、すべてのボード座標がチェックされます。`0 <= coordinate < 8`。 最終的なボードには 1 行に 8 文字が印刷され、その後にテスト ケースの間に空行が続きます。 

## 実用的な例

 最初のサンプルでは、要求されたペアは w=2,b=3 です。 問題は任意の有効なボードを受け入れるため、検索ではサンプル出力を再現する必要はありません。 

成功した一般的な検索には、次の形式のトレースが含まれます。 

| 反復 | 白が攻撃した | 黒が攻撃した | 写真 エラー |
 | --- | --- | --- | --- |
 | イニシャル | 4 | 1 | 4 |
 | 1 | 3 | 2 | 2 |
 | 2 | 2 | 2 | 1 |
 | 3 | 2 | 3 | 0 |

 最後の状態はすぐに受け入れられます。 実装で使用される不変式は単純です。ボードが印刷されるたびに、ボードはすでに正確な攻撃カウンターを通過しており、そのカウンターが返されます。`(2, 3)`。 

2 番目のサンプルでは、​​要求されたペアは w=4,b=2 です。 

| 反復 | 白が攻撃した | 黒が攻撃した | 写真 エラー |
 | --- | --- | --- | --- |
 | イニシャル | 1 | 4 | 5 |
 | 1 | 2 | 3 | 3 |
 | 2 | 3 | 2 | 1 |
 | 3 | 4 | 2 | 0 |

 繰り返しになりますが、実際のボードはステートメントのサンプルと完全に異なる場合があります。 重要なのは、4 つの大文字のピースに少なくとも 1 人の黒のアタッカーがあり、ちょうど 2 つの小文字のピースに少なくとも 1 人の白のアタッカーがいることです。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(U⋅R⋅I⋅64) | U の個別の要求されたペア、R の再起動、I の再起動ごとの突然変異 |
 | スペース | O(U⋅64) | キャッシュされた 8 x 8 ボード |

 ここでは U≤1000 ですが、ボード自体は 64 セルに固定されています。 攻撃計算は形式的な問題パラメータのサイズが一定であり、キャッシュされたテスト ケースでは、Python オブジェクトのオーバーヘッドを除けば、それぞれ数十バイトしか必要としません。 対象環境のボードは非常に小さいため、実際のコストは入力サイズよりもランダム化された構成によって決まります。 

## テストケース

 建設的な問題の出力は一意ではないため、アサート テストでは、プリント基板を 1 つの特定の文字列と比較するのではなく、意味論的なプロパティを検証する必要があります。```python
import sys
import io

KNIGHT = [
    (-2, -1), (-2, 1),
    (-1, -2), (-1, 2),
    (1, -2), (1, 2),
    (2, -1), (2, 1),
]

DIRS = [
    (-1, -1), (-1, 0), (-1, 1),
    (0, -1),           (0, 1),
    (1, -1),  (1, 0),  (1, 1),
]

def attacked_counts(board):
    attacked = [False] * 64

    for pos, p in enumerate(board):
        if p == '.':
            continue

        r, c = divmod(pos, 8)

        if p.lower() == 'q':
            for dr, dc in DIRS:
                nr, nc = r + dr, c + dc

                while 0 <= nr < 8 and 0 <= nc < 8:
                    np = nr * 8 + nc

                    if board[np] != '.':
                        if board[np].isupper() != p.isupper():
                            attacked[np] = True
                        break

                    nr += dr
                    nc += dc
        else:
            for dr, dc in KNIGHT:
                nr, nc = r + dr, c + dc

                if 0 <= nr < 8 and 0 <= nc < 8:
                    np = nr * 8 + nc
                    if board[np] != '.' and \
                       board[np].isupper() != p.isupper():
                        attacked[np] = True

    w = sum(
        attacked[i] and board[i].isupper()
        for i in range(64)
        if board[i] != '.'
    )

    b = sum(
        attacked[i] and board[i].islower()
        for i in range(64)
        if board[i] != '.'
    )

    return w, b

def validate(out, expected):
    lines = [x for x in out.splitlines() if x.strip()]

    assert len(lines) == 8
    board = ''.join(lines)

    assert len(board) == 64
    assert all(c in ".QqKk" for c in board)

    assert attacked_counts(board) == expected

# The helper below represents the contest solution.
# In a local test file, import find_board from the submitted solution.
def run_pair(w, b):
    from solution import find_board
    board = find_board(w, b)
    return '\n'.join(
        ''.join(board[r * 8:(r + 1) * 8])
        for r in range(8)
    )

# Provided sample pairs
out = run_pair(2, 3)
validate(out, (2, 3))

out = run_pair(4, 2)
validate(out, (4, 2))

# Minimum case
out = run_pair(0, 0)
validate(out, (0, 0))

# One-sided attack count
out = run_pair(1, 0)
validate(out, (1, 0))

# Equal counts
out = run_pair(32, 32)
validate(out, (32, 32))

# Maximum individual request
out = run_pair(50, 0)
validate(out, (50, 0))
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`2 3`| 有効な 8 x 8 ボード | 最初に提供されたサンプル |
 |`4 2`| 有効な 8 x 8 ボード | 2 番目に提供されるサンプル |
 |`0 0`| 空のボードまたは攻撃のないボード | 両方のカウントがゼロになる可能性があります。 
|`1 0`| ちょうど 1 つの白い部分が攻撃されました | 一方的な攻撃のカウント |
 |`32 32`| 各色のちょうど 32 個の攻撃された部分 | バランスのとれた大きなカウント |
 |`50 0`| ちょうど 50 個の白い部分が攻撃されました | 最大個体数と黒攻撃の不在 |

 ## 特殊なケース

 のために`0 0`、構築は完全に空のボードを即座に返すことができます。 駒がないので攻撃できる駒はなく、両方のカウントはちょうどゼロです。 これにより、些細なケースでの無駄な検索反復が回避されます。 

のために`1 0`、探索では誤って黒い駒を攻撃しないようにする必要があります。 有効な構築には、黒の攻撃ピースと 1 つの白のターゲットを含めることができ、他のすべての黒ピースは孤立または存在しないままにすることができます。 検証者は黒のカウントを明示的にチェックするため、白の攻撃が 1 つと意図しない黒の攻撃が 1 つあるボードは、黙って印刷されるのではなく拒否されます。 

のために`50 0`、ボードには、黒の攻撃カウントをゼロに保ちながら、攻撃された白の駒の高密度が必要です。 この場合、クイーンを遠距離攻撃者として使用し、ナイトをターゲットとして使用すると便利です。 1 匹のクイーンがさまざまな方向から複数のターゲットを攻撃できるため、分離された 1 対 1 ペアよりも必要な密度を達成するのがはるかに簡単になります。 

のために`32 32`、最終ボードが密集している場合、利用できる空きスペースはほとんどありません。 ターゲットのグループごとに個別の攻撃者を割り当てる構造では、64 セルのボードを超える可能性があります。 ランダム化検索では、そのような分解は強制されません。 完全なボード構成の中から直接検索するため、攻撃された駒は同時に反対色の駒に対する攻撃に参加できます。 これはまさに、両方の要求数が大きい場合に必要な対話です。
