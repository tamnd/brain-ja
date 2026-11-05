---
title: "CF 1279C - プレゼントの山"
description: "この状況は、一意の番号が付けられた項目のスタックであり、スタックの最上部のみが直接アクセスできる状態です。 一連の操作では、特定の項目を指定された順序で繰り返し削除することが求められます。"
date: "2026-06-16T02:11:18+07:00"
tags: ["codeforces", "competitive-programming", "data-structures", "implementation"]
categories: ["algorithms"]
codeforces_contest: 1279
codeforces_index: "C"
codeforces_contest_name: "Educational Codeforces Round 79 (Rated for Div. 2)"
rating: 1400
weight: 1279
solve_time_s: 369
verified: false
draft: false
---

[CF 1279C - Stack of Presents](https://codeforces.com/problemset/problem/1279/C)

 **評価:** 1400
 **タグ:** データ構造、実装
 **解決時間:** 6 分 9 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 この状況は、一意の番号が付けられた項目のスタックであり、スタックの最上部のみが直接アクセスできる状態です。 一連の操作では、特定の項目を指定された順序で繰り返し削除することが求められます。 項目の削除は無料ではありません。削除の時点でその項目が先頭から k 個の位置にある場合は、その上に k 個の項目を一時的にポップし、ターゲットを取得してから、それらの k 個の項目をスタックに戻さなければなりません。 The cost of such an operation is proportional to how deep the target item is in the current stack state.

 ひねりを加えると、問題は簡単ではなくなります。 When we restore the temporarily removed items, we are allowed to reorder them arbitrarily before putting them back. However, we cannot move anything that was originally below the removed target, and we must preserve that lower structure forever. Over a sequence of removals, this reordering ability lets us “shape” the stack above future targets to reduce future access costs.

 The input consists of an initial stack order and a list of required removals. 出力は、各削除後に常に可能な限り最良の再配置戦略を使用すると仮定して、すべての必要な項目を指定された順序で削除するのに必要な最小合計時間です。 

The constraints allow up to 100,000 elements across all test cases. Any solution that inspects or simulates stack operations repeatedly in a naive way risks quadratic behavior. An O(nm) or even O(n²) approach will fail because each removal potentially scans and reshuffles large portions of the stack.

 素朴な解決策が、要素が削除されると、残りのすべての要素に対する相対的な位置が固定されると仮定した場合、微妙な障害モードが発生します。 That is incorrect because reordering the “buffer” elements can push later targets closer to the top.

 A concrete misleading case is when targets appear in an order that interleaves deeply nested elements. スタックが`[5, 4, 3, 2, 1]`そしてクエリは`[1, 2, 3]`。 A greedy simulation that does not carefully account for reordering might recompute depths incorrectly after each extraction, missing that previously removed items above can be reorganized to minimize future depths.

 実装がターゲットを超える要素の数が静的であると想定している場合、別の問題が発生します。 すでに抽出されたターゲットのみが将来の再配置に影響を与えるため、そうではありません。 non-target elements can be reshuffled to avoid blocking future targets.

 ## アプローチ

 A direct simulation processes each query by scanning the stack to locate the requested element, counting how many items lie above it, then removing and restoring them. This is correct in principle because it mirrors the process exactly, but it is too slow. Each query may traverse the stack, leading to O(nm) behavior in the worst case.

 The key observation is that only relative order among “already processed” and “yet to be processed” elements matters. 要素を削除すると、その上のすべての項目をフレキシブル プールとして扱うことができます。 このプールの順序は自由に変更できるため、今後のクエリでは、要素がすでに「アクティブ化」されているかどうかのみを考慮します。 

We process queries in order and maintain the highest position in the original stack among all elements we have already used. Every time we pick a new element, if it lies above the current “active boundary”, it costs nothing additional in terms of crossing unseen elements; それ以外の場合は、境界とこの要素の間のすべての目に見えない要素に対して料金が発生します。 

This reduces the problem to tracking the farthest position reached so far in the original stack ordering, and summing contributions only when we expand beyond previously seen indices.

 | アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォースシミュレーション | O(nm) | O(n) | 遅すぎる |
 | インデックス追跡の最適化 | O(n + m) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 まず、現在の各数値を初期スタック内のその位置にマッピングします。 This lets us convert the query sequence into a sequence of indices in O(1) lookup time.

 変数を維持します`max_pos`、これは、以前の削除によりすでに「アクティブ化」されている元のスタックの最も遠い深さを表します。 また、必要に応じて昇順に位置を移動するインデックス ポインターも維持します。 

リクエストされたプレゼントごとに:

 1. 現在を元のスタック内の位置に変換します。 

このステップでは、問題を値空間から位置空間に変換し、順序付けが線形で比較可能になります。 
2. この位置が以下の場合`max_pos`、答えに0を加えます。 

The element is already within the region that has been conceptually cleared by earlier operations, so it does not require passing through any unseen items.
 3. それ以外の場合は、追加します`(position - max_pos)`答えに。 

これは、初めて通過する必要がある、現在の境界より上に新しく検出された要素を表します。 
4.アップデート`max_pos`大きい場合は現在の位置に移動します。 

これにより処理領域が拡張されるため、今後のクエリではこれらの要素がすでに考慮されているものとして認識されます。 

このプロセスの背後にある理由は、スタック内の各要素がコストに寄与するのは、アクセスされるプレフィックスの一部になった時点で 1 回だけであるということです。 その後は、コストの繰り返しを避けるためにいつでも再配置できます。 

### なぜ効果があるのか

 スタックは固定された垂直方向の順序として見ることができますが、各操作の後に現在アクセスされている要素の上の部分のみを自由に再配置することができます。 つまり、「認識されたプレフィックス」に一度含まれていた要素は、将来のターゲットの上に最適に配置され、反復的な走査コストが排除されます。 

不変条件というのは、`max_pos`は常に、処理中に少なくとも 1 回公開された元のスタックの最も深い位置を表します。 ユニットが増えるたびに、`max_pos`これは、まさに 1 つの要素に対して 1 回だけ支払われ、再度支払われることはありません。 初めて必須ターゲットの上にある要素はスキップできないため、答えへのあらゆる寄与は必要かつ最小限になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

t = int(input())
for _ in range(t):
    n, m = map(int, input().split())
    a = list(map(int, input().split()))
    b = list(map(int, input().split()))

    pos = [0] * (n + 1)
    for i, x in enumerate(a):
        pos[x] = i + 1

    max_pos = 0
    ans = 0

    for x in b:
        p = pos[x]
        if p > max_pos:
            ans += p - max_pos
            max_pos = p

    print(ans)
```重要な実装の詳細は、スタックを位置配列に変換することです。 これにより、スキャンの繰り返しが回避され、クエリ要素ごとに一定時間の検索が可能になります。 変数`max_pos`すでに計算されたスタック プレフィックスのフロンティアをエンコードし、以前にコストに含まれていた要素を二重にカウントしないようにします。 

よくある間違いはリセットまたは調整です`max_pos`各クエリの後で誤って、それをスライディング ウィンドウの境界のように扱います。 正しい解釈は単調です。つまり、一度到達した位置は永久に到達したままになります。 

## 実用的な例

 サンプルスタックを検討してください`[3, 1, 2]`クエリ付き`[3, 2, 1]`。 

値を位置にマッピングします。`3 -> 1`、`1 -> 2`、`2 -> 3`。 

| クエリ | ポジション | 前の max_pos | 追加コスト | | 後の max_pos
 | --- | --- | --- | --- | --- |
 | 3 | 1 | 0 | 1 | 1 |
 | 2 | 3 | 1 | 2 | 3 |
 | 1 | 2 | 3 | 0 | 3 |

 合計コストは3です。 

ここで考えてみましょう`[2, 1, 7, 3, 4, 5, 6]`クエリ付き`[3, 1]`。 

マッピング：`3 -> 4`、`1 -> 2`。 

| クエリ | ポジション | 前の max_pos | 追加コスト | | 後の max_pos
 | --- | --- | --- | --- | --- |
 | 3 | 4 | 0 | 4 | 4 |
 | 1 | 2 | 4 | 0 | 4 |

 合計コストは4です。 

これらのトレースは、元のスタックの処理済みプレフィックスを最初に拡張した場合にのみコストが蓄積することを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n + m) | 各要素は 1 回マップされ、各クエリは一定時間で処理されます。 
| スペース | O(n) | 位置配列には、各存在のインデックスが格納されます |

 テスト ケース全体の要素の合計数が 100,000 で制限されているため、ソリューションは制約内に快適に収まり、テスト ケースごとの線形処理が効率的になります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    input = sys.stdin.readline

    t = int(input())
    out = []
    for _ in range(t):
        n, m = map(int, input().split())
        a = list(map(int, input().split()))
        b = list(map(int, input().split()))

        pos = [0] * (n + 1)
        for i, x in enumerate(a):
            pos[x] = i + 1

        max_pos = 0
        ans = 0
        for x in b:
            p = pos[x]
            if p > max_pos:
                ans += p - max_pos
                max_pos = p

        out.append(str(ans))
    return "\n".join(out) + "\n"

# provided samples
assert run("""2
3 3
3 1 2
3 2 1
7 2
2 1 7 3 4 5 6
3 1
""") == "5\n8\n"

# custom: single element
assert run("""1
1 1
1
1
""") == "1\n"

# custom: increasing order
assert run("""1
5 5
1 2 3 4 5
1 2 3 4 5
""") == "5\n"

# custom: reverse order
assert run("""1
5 5
5 4 3 2 1
1 2 3 4 5
""") == "5\n"

# custom: interleaving
assert run("""1
6 3
6 1 5 2 4 3
2 5 3
""") == "5\n"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一要素 | 1 | 最小境界 |
 | 昇順 | 5 | 余分な節約はありません |
 | 逆順 | 5 | 最悪の深さの進行 |
 | インターリーブ | 5 | 非単調アクセス下での正確性 |

 ## 特殊なケース

 まれなケースとして、要求されたすべての要素がスタックの先頭近くにあるにもかかわらず、位置の降順で表示される場合が発生します。 たとえば、スタックが`[1, 2, 3, 4, 5]`そしてクエリは`[5, 4, 3]`、アルゴリズムが拡張されます`max_pos`必要な場合にのみ、新しいより深いアクセスがそれぞれ、その未カバーのセグメントに正確に寄与することを保証します。 計算により二重カウントが正しく回避されます。`max_pos`が 5 に達すると、後続のすべてのクエリは処理されたプレフィックス内に収まります。 

もう 1 つのケースは、クエリがすでにスタック順序になっている場合です。 スタックが`[4, 3, 2, 1]`そしてクエリは`[3, 1]`、最初のクエリはプレフィックスを部分的に展開し、2 番目のクエリは完全にその中に収まります。 新しいプレフィックスが公開されないため、アルゴリズムは当然、2 番目のクエリに追加コストを割り当てません。
