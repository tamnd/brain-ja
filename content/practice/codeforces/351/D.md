---
title: "CF 351D - ジェフとピリオドの削除"
description: "クエリごとに、指定された配列のサブ配列を調べ、その美しさを調べます。 1 回の操作で、位置が等差数列を形成する複数の等しい値を選択し、それらを削除して、残ったすべてのものを並べ替えることができます。 最後の一文が鍵です。"
date: "2026-06-06T22:08:08+07:00"
tags: ["codeforces", "competitive-programming", "data-structures"]
categories: ["algorithms"]
codeforces_contest: 351
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 204 (Div. 1)"
rating: 2700
weight: 351
solve_time_s: 159
verified: true
draft: false
---

[CF 351D - ジェフとピリオドの削除](https://codeforces.com/problemset/problem/351/D)

 **評価:** 2700
 **タグ:** データ構造
 **解決時間:** 2 分 39 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 クエリごとに、指定された配列のサブ配列を調べ、その **美しさ** を求めます。 

1 回の操作で、位置が等差数列を形成する複数の等しい値を選択し、それらを削除して、残ったすべてのものを並べ替えることができます。 

最後の一文が鍵です。 最初の操作の後、残りの要素の順序は完全に制御可能になります。 値が表示された場合$c$すべてを配置できます$c$等差数列を形成する位置をコピーし、1 回の操作で削除します。 それ以降は、それぞれの個別の値を常に 1 回の操作で削除できます。 

唯一の難しい部分は**最初**の操作です。これは、クエリされた部分配列の元の順序を使用する必要があるためです。 

配列の長さとクエリの数は両方とも最大です。$10^5$。 クエリを独立して処理するソリューションは直ちに除外されます。 平$O(\sqrt n)$クエリごとの作業量はすでに限界に近づいています。 オフライン データ構造ソリューションが必要です。$O((n+q)\sqrt n)$全体的な複雑さ。 

クエリ間隔内で値が 1 回だけ出現する場合、微妙なエッジ ケースが発生します。```
[5, 7, 5]
```インターバルに関しては$[2,2]$、値$7$が一度出現します。 単一の位置は自明の等差数列であるため、その値はすぐに削除できます。 少なくとも 2 回出現する値のみをチェックするソリューションでは、これが見逃されます。 

もう 1 つの簡単な間違いは、並べ替えた後、何らかの方法で異なる値を 1 つの操作に組み合わせることができると考えることです。```
[1, 1, 2, 2]
```操作では常に等しい値のみが削除されます。 並べ替えても全部削除するのがせいぜい$1$は 1 回の操作ですべて完了します$2$は別のものです。 答えは$2$、 ない$1$。 

最も重要な例外ケースは、すべての値が複数回出現するが、まだ等差数列を形成していない場合です。```
positions of 1: 1, 3, 6
positions of 2: 2, 5, 9
```どちらの位置セットも等差数列ではありません。 最初の操作では、値を完全に削除することはできません。 最初の操作が失敗すると、答えが 1 つ増えます。 

## アプローチ

 総当たりの視点は驚くほど役に立ちます。 

クエリ間隔内のすべての値の出現がすべてわかっているとします。 間隔内のすべての値の出現が等差数列上にある場合、その値はすぐに削除できます。 このような値を最初に削除すると、並べ替え後に残りの個別の値を 1 回の操作でそれぞれ削除できます。 

させて$D$間隔内の個別の値の数になります。 

値をすぐに削除できる場合は、その値を削除するのに 1 回の操作を費やします。$D-1$残りの個別の値に対してさらに操作を行います。 答えは$D$。 

すぐに削除できる値がない場合、最初の操作では値の一部のみを削除できます。 その手術の後は、すべて$D$明確な価値観は依然として存在します。 並べ替えが必要になる$D$追加の操作。 答えは$D+1$。 

したがって、すべてのクエリは次の 2 つの量に減ります。 

1. 個別の値の数。 
2. 間隔内で等差数列を形成する値が少なくとも 1 つ存在するかどうか。 

この観察が問題のすべてです。 これは多くの受け入れられているソリューションに含まれており、標準的な Mo のアルゴリズム アプローチの基礎です。 

単純な実装ではまだ遅すぎます。 クエリごとに、すべての値の出現を収集し、等差数列条件を最初からテストする必要があります。 最悪の場合こうなります$O(n)$クエリごとにさらに悪いことに、これははるかに超えています$10^5$クエリが許可します。 

間隔のエンドポイントは、Mo のアルゴリズムに従って徐々に移動します。 個別の値の数を維持するのは簡単です。 本当の課題は、現在出現する値が等差数列を形成する値の数を維持することです。 

受け入れられた解決策は、1 つの出現を追加または削除すると、値が等差数列でなくなるか、または再び等差数列になり始める時期を正確に知らせる補助配列を事前計算します。 その後、すべての Mo アップデートは次のようになります。$O(1)$。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |$O(nq)$それより悪い |$O(n)$| 遅すぎる |
 | 最適 (Mo + AP 維持) |$O((n+q)\sqrt n)$|$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 ### 重要な特徴付け

 しましょう$D$現在の間隔内の個別の値の数になります。 

させて$G$間隔内で等差数列を形成する値の数になります。 

もし$G>0$、答えは$D$。 

もし$G=0$、答えは$D+1$。 

したがって、タスク全体が保守になります$D$そして$G$。 

### 前処理オカレンス構造

 あらゆるポジションについて$i$:

 1. コンピューティング`pre[i]`、同じ値の前の出現。 
2. コンピューティング`bak[i]`、同じ値が次に出現する。 

次に、2 つのヘルパー配列を構築します。`fl[i]`等しい値のチェーンが終わる左端の位置を格納します。$i$等差数列ではなくなります。`fr[i]`右に見たときの対称情報を格納します。 

これらの配列は、同じ値が連続して発生する場合の動的再帰によって計算されます。 

### 注文します

 1. 標準の Mo 順序を使用してすべてのクエリを並べ替えます。 
2. 現在の間隔を維持する$[L,R]$。 
3. 移動$L$そして$R$各クエリと一致します。 

### 明確な値を維持する

 1.キープ`cnt[value]`。 
2. 周波数が変化したとき$0$に$1$、 増加$D$。 
3. 周波数が変化したとき$1$に$0$、 減少$D$。 

### 等差数列値の維持

 1.キープ$G$、現在出現する値が等差数列を形成する値の数。 
2. 境界が移動すると、1 つのオカレンスのみが挿入または削除されます。 
3. 使用方法`fl`そして`fr`で決定します$O(1)$その更新により、対応する値の AP ステータスが変更されるかどうか。 
4. 増減$G$それに応じて。 

事前に計算された配列は、新しく公開されたオカレンスによって最初の等間隔違反がいつ発生するのか、またはオカレンスを削除することで最後の違反が解消されるのかを正確に教えてくれます。 

### 質問に答える

 1. 間隔を調整すると、次のことがわかります。$D$そして$G$。 
2. もし$G>0$、出力$D$。 
3. それ以外の場合は出力$D+1$。 

### なぜ効果があるのか

 最初の操作の後は、任意の並べ替えが許可されます。 残りの個別の値はすべて、そのすべてのコピーが等差数列上に配置され、1 回の操作で削除できるように配置できます。 

並べ替えが行われる前にすでに完全に削除できる値が存在する場合、クエリ間隔に必要な操作は 1 つだけ少なくなります。 この条件は、間隔内でのその値の出現が等差数列を形成していると言うのと同じです。 

したがって、答えは、個別の値の数と、少なくとも 1 つの値が現在「良好」であるかどうかによってのみ決まります。 Mo 構造はこれら 2 つの情報を正確に保持しているため、報告されたすべての答えは正しいです。 

## Python ソリューション```python
import sys
from math import isqrt

input = sys.stdin.readline

def solve():
    n = int(input())
    a = [0] + list(map(int, input().split()))

    maxv = 100000

    pre = [0] * (n + 1)
    bak = [0] * (n + 1)
    fl = [0] * (n + 1)
    fr = [0] * (n + 2)

    last = [0] * (maxv + 1)

    for i in range(1, n + 1):
        pre[i] = last[a[i]]
        last[a[i]] = i

        if pre[i] == 0 or pre[pre[i]] == 0:
            fl[i] = 0
        elif pre[pre[i]] - pre[i] == pre[i] - i:
            fl[i] = fl[pre[i]]
        else:
            fl[i] = pre[pre[i]]

    last = [0] * (maxv + 1)

    for i in range(n, 0, -1):
        bak[i] = last[a[i]]
        last[a[i]] = i

        if bak[i] == 0 or bak[bak[i]] == 0:
            fr[i] = n + 1
        elif bak[bak[i]] - bak[i] == bak[i] - i:
            fr[i] = fr[bak[i]]
        else:
            fr[i] = bak[bak[i]]

    q = int(input())
    block = isqrt(n) + 1

    queries = []
    for idx in range(q):
        l, r = map(int, input().split())
        queries.append((l, r, idx))

    queries.sort(key=lambda x: (x[0] // block, x[1]))

    cnt = [0] * (maxv + 1)
    ans = [0] * q

    distinct = 0
    good = 0

    def add_left(pos, cur_r):
        nonlocal distinct, good
        x = a[pos]

        cnt[x] += 1
        if cnt[x] == 1:
            distinct += 1
            good += 1
        elif fr[pos] <= cur_r and fr[bak[pos]] > cur_r:
            good -= 1

    def remove_left(pos, cur_r):
        nonlocal distinct, good
        x = a[pos]

        cnt[x] -= 1
        if cnt[x] == 0:
            distinct -= 1
            good -= 1
        elif fr[pos] <= cur_r and fr[bak[pos]] > cur_r:
            good += 1

    def add_right(pos, cur_l):
        nonlocal distinct, good
        x = a[pos]

        cnt[x] += 1
        if cnt[x] == 1:
            distinct += 1
            good += 1
        elif fl[pos] >= cur_l and fl[pre[pos]] < cur_l:
            good -= 1

    def remove_right(pos, cur_l):
        nonlocal distinct, good
        x = a[pos]

        cnt[x] -= 1
        if cnt[x] == 0:
            distinct -= 1
            good -= 1
        elif fl[pos] >= cur_l and fl[pre[pos]] < cur_l:
            good += 1

    L = 1
    R = 0

    for l, r, idx in queries:
        while L > l:
            L -= 1
            add_left(L, R)

        while R < r:
            R += 1
            add_right(R, L)

        while L < l:
            remove_left(L, R)
            L += 1

        while R > r:
            remove_right(R, L)
            R -= 1

        ans[idx] = distinct if good > 0 else distinct + 1

    sys.stdout.write("\n".join(map(str, ans)))

if __name__ == "__main__":
    solve()
```前処理ステージでは、すべての値の前と次の出現リンクを構築します。 これらのリンクは計算に使用されます。`fl`そして`fr`、等差数列プロパティが最初に切れる場所をエンコードします。 

Mo構造は滑り間隔を維持します。 すべての更新では 1 つの位置のみが変更されるため、変更できる値のステータスは 1 つだけです。 ヘルパー配列を使用すると、その変化を一定時間で検出できます。 

バグの一般的な原因は、更新関数が処理中のクエリ境界ではなく、**現在の**間隔境界を使用する必要があることを忘れていることです。 もう 1 つのよくある間違いは、移行条件を評価する前に間隔のエンドポイントを更新することです。 

## 実用的な例

 ### サンプル 1

 入力間隔:```
[2, 2, 1, 1, 2]
```| 値 | ポジション |
 | --- | --- |
 | 1 | 3、4 |
 | 2 | 1、2、5 |

 個別の値:

 | D | 良い値 |
 | --- | --- |
 | 2 | 値 1 |

 価値のある立場$1$は$(3,4)$、これは等差数列です。 

適切な値が存在するため、答えは次のようになります。$D=2$。 

この例は、中心的な観察を示しています。つまり、1 つの値をすぐに削除でき、残りの値は並べ替え後に削除されます。 

### カスタム例

 配列:```
[1, 2, 1, 2, 2, 1]
```クエリ:```
[1, 6]
```| 値 | ポジション |
 | --- | --- |
 | 1 | 1、3、6 |
 | 2 | 2、4、5 |

 等間隔を確認します。 

| 値 | 違い | AP？ |
 | --- | --- | --- |
 | 1 | 2、3 | いいえ |
 | 2 | 2、1 | いいえ |

 ここ$D=2$そして$G=0$。 

答え：$$D + 1 = 3$$最初の操作では値を完全に削除できないため、追加の操作は避けられません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O((n+q)\sqrt n)$| 標準的な Mo のアルゴリズム$O(1)$更新情報 |
 | スペース |$O(n)$| 出現リンク、ヘルパー配列、頻度、クエリ |

 と$n,q \le 10^5$、 だいたい$O((n+q)\sqrt n)$操作は制限時間内に問題なく収まり、メモリ使用量も 256 MB をはるかに下回っています。 

## テストケース```python
# helper: run solution on input string, return output string
import sys, io

def run(inp: str) -> str:
    from math import isqrt

    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out

    input = sys.stdin.readline

    n = int(input())
    a = [0] + list(map(int, input().split()))

    maxv = 100000

    pre = [0] * (n + 1)
    bak = [0] * (n + 1)
    fl = [0] * (n + 1)
    fr = [0] * (n + 2)

    last = [0] * (maxv + 1)

    for i in range(1, n + 1):
        pre[i] = last[a[i]]
        last[a[i]] = i

        if pre[i] == 0 or pre[pre[i]] == 0:
            fl[i] = 0
        elif pre[pre[i]] - pre[i] == pre[i] - i:
            fl[i] = fl[pre[i]]
        else:
            fl[i] = pre[pre[i]]

    last = [0] * (maxv + 1)

    for i in range(n, 0, -1):
        bak[i] = last[a[i]]
        last[a[i]] = i

        if bak[i] == 0 or bak[bak[i]] == 0:
            fr[i] = n + 1
        elif bak[bak[i]] - bak[i] == bak[i] - i:
            fr[i] = fr[bak[i]]
        else:
            fr[i] = bak[bak[i]]

    q = int(input())
    block = isqrt(n) + 1

    qs = []
    for idx in range(q):
        l, r = map(int, input().split())
        qs.append((l, r, idx))

    qs.sort(key=lambda x: (x[0] // block, x[1]))

    cnt = [0] * (maxv + 1)
    res = [0] * q

    distinct = 0
    good = 0

    def add_left(pos, cur_r):
        nonlocal distinct, good
        x = a[pos]
        cnt[x] += 1
        if cnt[x] == 1:
            distinct += 1
            good += 1
        elif fr[pos] <= cur_r and fr[bak[pos]] > cur_r:
            good -= 1

    def remove_left(pos, cur_r):
        nonlocal distinct, good
        x = a[pos]
        cnt[x] -= 1
        if cnt[x] == 0:
            distinct -= 1
            good -= 1
        elif fr[pos] <= cur_r and fr[bak[pos]] > cur_r:
            good += 1

    def add_right(pos, cur_l):
        nonlocal distinct, good
        x = a[pos]
        cnt[x] += 1
        if cnt[x] == 1:
            distinct += 1
            good += 1
        elif fl[pos] >= cur_l and fl[pre[pos]] < cur_l:
            good -= 1

    def remove_right(pos, cur_l):
        nonlocal distinct, good
        x = a[pos]
        cnt[x] -= 1
        if cnt[x] == 0:
            distinct -= 1
            good -= 1
        elif fl[pos] >= cur_l and fl[pre[pos]] < cur_l:
            good += 1

    L, R = 1, 0

    for l, r, idx in qs:
        while L > l:
            L -= 1
            add_left(L, R)
        while R < r:
            R += 1
            add_right(R, L)
        while L < l:
            remove_left(L, R)
            L += 1
        while R > r:
            remove_right(R, L)
            R -= 1

        res[idx] = distinct if good > 0 else distinct + 1

    return "\n".join(map(str, res))

# sample 1
assert run(
"""5
2 2 1 1 2
5
1 5
1 1
2 2
1 3
2 3
"""
) == "2\n1\n1\n2\n2"

# minimum size
assert run(
"""1
7
1
1 1
"""
) == "1"

# all equal
assert run(
"""4
5 5 5 5
1
1 4
"""
) == "1"

# two distinct singletons
assert run(
"""2
1 2
1
1 2
"""
) == "2"

# AP positions present
assert run(
"""5
1 2 1 3 1
1
1 5
"""
) == "3"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一要素 | 1 | 可能な最小間隔 |
 | すべて等しい値 | 1 | 1 つの操作で 1 つの値が削除される |
 | 2 つの異なるシングルトン | 2 | シングルトン値は常に適切です |
 | 同じ値の位置 1、3、5 | 3 | 等差数列検出 |

 ## 特殊なケース

 次のことを考慮してください。```
1
7
1
1 1
```間隔には、1 回出現する 1 つの値が含まれます。 単一の位置は等差数列であるため、$G=1$、$D=1$、そして答えは$1$。 

考慮する：```
4
5 5 5 5
1
1 4
```すべての出現は 1 つの値に属します。 それらの位置は等差数列を形成するため、間隔全体が 1 回の操作で消えます。 アルゴリズムは維持します$D=1$そして$G=1$、答えを生み出す$1$。 

考慮する：```
6
1 2 1 2 2 1
1
1 6
```価値$1$地位を占める$1,3,6$、違い$2,3$。 価値$2$地位を占める$2,4,5$、違い$2,1$。 値がないのは良くありません。 アルゴリズムは次で終了します$D=2$そして$G=0$、戻る$3$、最適な戦略に一致します。
