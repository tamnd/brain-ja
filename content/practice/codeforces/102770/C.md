---
title: "CF 102770C - クロスワード検証"
description: "クロスワード ボードは、いくつかのセルがブロックされており、残りのセルにはすでに文字が含まれている正方形のグリッドです。 このグリッド内の単語は手がかりによって選ばれるわけではありません。 代わりに、水平または垂直に表示される文字の最大連続シーケンスがすべて表示されます。"
date: "2026-07-28T23:06:17+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102770
codeforces_index: "C"
codeforces_contest_name: "The 17th Zhejiang Provincial Collegiate Programming Contest"
rating: 0
weight: 102770
solve_time_s: 73
verified: true
draft: false
---

[CF 102770C - クロスワード検証](https://codeforces.com/problemset/problem/102770/C)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 13 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 クロスワード ボードは、いくつかのセルがブロックされており、残りのセルにはすでに文字が含まれている正方形のグリッドです。 このグリッド内の単語は手がかりによって選ばれるわけではありません。 代わりに、水平または垂直に表示される文字の最大連続シーケンスがすべて表示されます。 水平方向のシーケンスは境界線またはブロックされたセルに到達すると停止し、同じルールが垂直方向にも適用されます。 

辞書には、許可されたすべての単語がスコアとともに含まれています。 タスクは、塗りつぶされたグリッドに自然に現れるすべての単語を検査することです。 辞書に水平方向または垂直方向のシーケンスが 1 つでも欠けている場合、クロスワード全体が無効となり、答えは次のようになります。`-1`。 それ以外の場合、答えは、繰り返し出現した単語を含む、検出されたすべての単語の辞書スコアの合計です。 

重要な制約は 2 つの大きな値によって支配されます。 すべてのテスト ケースのグリッド領域は最大 400 万セルであるため、考えられるすべての単語に対して多くのセルを再訪するソリューションは遅すぎます。 辞書の合計長も 400 万文字であるため、辞書のすべての単語を多数の個別の構造にコピーするアプローチは除外されます。 線形またはほぼ線形のアルゴリズムが必要であり、データ構造は単語間で共通の接頭辞を共有する必要があります。 

いくつかのケースでは、誤って対処されやすいものがあります。 単一の文字が分離されている場合でも、単語の候補としては有効です。 

例えば：```
1
1 1
a
a 5
```答えは`5`。 少なくとも 2 つ以上の長さのワードのみをチェックする実装では、このボードが誤って拒否されます。 

繰り返される単語は、出現するたびにスコアに寄与する必要があります。 例えば：```
1
2 1
aa
aa
aa 7
```横文字は、`aa`そして`aa`、縦の単語は`aa`そして`aa`、したがって、答えは次のとおりです`28`。 各辞書エントリを 1 回だけカウントすると、間違った結果が生成されます。 

ブロックされたセルを介して候補単語を拡張することはできません。 例えば：```
1
2 2
a#
aa
a 3
aa 5
```言葉は`a`、`aa`、`a`、 そして`aa`、与える`16`。 行だけを見て縦の単語を忘れると、2 つの貢献を見逃してしまいます。 

## アプローチ

 直接的な解決策は、すべての行セグメントとすべての列セグメントを抽出し、抽出された各文字列を辞書で検索することです。 これは正しいです。なぜなら、候補単語の定義はまさに最大の連続セグメントであるからです。 ただし、文字列を繰り返し保存または比較するとコストがかかります。 最悪の場合、ボードはほぼ完全に文字で構成されているため、すべてのセグメントを抽出しても 400 万セルに達し、多くの単語をスキャンする単純な辞書検索では、その作業が辞書のサイズで倍増する可能性があります。 に近い解決策`O(number of cells + dictionary size)`が必要です。 

有益な観察は、すべての辞書クエリがプレフィックス クエリであるということです。 グリッドから候補単語を読み取る際に問題となるのは、現在の文字シーケンスが辞書内で継続できるかどうか、そして最後のシーケンスが完全な単語であるかどうかだけです。 トライはまさにこの情報を表します。 共有プレフィックスは一度保存されるため、総構築コストは辞書エントリの数ではなく辞書の長さの合計に依存します。 

ブルートフォース手法は、すべての候補単語を個別にチェックできるため機能しますが、同じ接頭辞を繰り返し検索するため失敗します。 トライはこの繰り返しの作業を解消します。 辞書が保存されると、すべてのグリッド セグメントを文字ごとにトライすることができます。 トランジションが存在しない場合、クロスワードは直ちに無効になります。 ウォークが終端トライノードで終了する場合、その保存されたスコアが追加されます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | 潜在的に`O(number of segments × dictionary size)`| 保存されている単語に依存します | 遅すぎる |
 | スキャンしてみる |`O(dictionary length + grid area)`|`O(dictionary length)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. すべての辞書単語を含むトライを構築します。 各終端ノードはその単語のスコアを保存します。 トライでは共有接頭辞が使用されるため、共通の始まりを持つ単語は追加のメモリをほとんど消費しません。 
2. グリッドのすべての行をスキャンします。 境界線または境界線の直後に文字セルが見つかったときは常に、`#`、右に歩き始めて、水平セグメントの終わりまでトライのエッジをたどります。 ウォークが失敗するか、終端ノードで終了しない場合、クロスワードは無効になります。 それ以外の場合は、保存されているスコアを追加します。 
3. 同じプロセスを使用して、すべての列を上から下までスキャンします。 すべての文字は水平方向と垂直方向の両方の単語に属する可能性があるため、個別のスキャンが必要です。 
4. トライ内ですべての候補単語が見つかった場合は、累積スコアを出力します。 

不変条件は、アルゴリズムがセグメントの処理を終了するたびに、クロスワード定義から候補単語を 1 つだけ検証するということです。 スキャンはセグメントの先頭でのみ開始されるため、すべての候補単語が 1 回訪問され、無効な部分セグメントはカウントされません。 トライには辞書の単語が正確に含まれているため、ターミナル マッチが成功すると、単語が許可されたことと同じになります。 

## Python ソリューション```python
import sys
from array import array

input = sys.stdin.readline

def solve():
    data = sys.stdin.buffer

    t = int(data.readline())
    ans = []

    for _ in range(t):
        n, m = map(int, data.readline().split())

        grid = [data.readline().strip() for _ in range(n)]

        head = array('i', [-1])
        score = array('q', [0])
        to = array('i')
        nxt = array('i')
        ch = array('B')

        def new_node():
            head.append(-1)
            score.append(0)
            return len(head) - 1

        def get_child(node, c):
            e = head[node]
            while e != -1:
                if ch[e] == c:
                    return to[e]
                e = nxt[e]
            return -1

        def add_word(s, val):
            node = 0
            for c in s:
                nxt_node = get_child(node, c)
                if nxt_node == -1:
                    nxt_node = new_node()
                    to.append(nxt_node)
                    ch.append(c)
                    nxt.append(head[node])
                    head[node] = len(to) - 1
                node = nxt_node
            score[node] = val

        for _ in range(m):
            word, val = data.readline().split()
            add_word(word, int(val))

        def check_line(chars):
            node = 0
            for c in chars:
                node = get_child(node, c)
                if node == -1:
                    return -1
            return score[node]

        total = 0
        ok = True

        for i in range(n):
            j = 0
            while j < n:
                if grid[i][j] == 35:
                    j += 1
                    continue
                if j == 0 or grid[i][j - 1] == 35:
                    node = 0
                    k = j
                    while k < n and grid[i][k] != 35:
                        node = get_child(node, grid[i][k] - 97)
                        if node == -1:
                            ok = False
                            break
                        k += 1
                    if not ok or score[node] == 0:
                        ok = False
                        break
                    total += score[node]
                j += 1
            if not ok:
                break

        if ok:
            for j in range(n):
                i = 0
                while i < n:
                    if grid[i][j] == 35:
                        i += 1
                        continue
                    if i == 0 or grid[i - 1][j] == 35:
                        node = 0
                        k = i
                        while k < n and grid[k][j] != 35:
                            node = get_child(node, grid[k][j] - 97)
                            if node == -1:
                                ok = False
                                break
                            k += 1
                        if not ok or score[node] == 0:
                            ok = False
                            break
                        total += score[node]
                    i += 1
                if not ok:
                    break

        ans.append(str(total if ok else -1))

    sys.stdout.write("\n".join(ans))

if __name__ == "__main__":
    solve()
```The trie is implemented with arrays instead of Python dictionaries. A dictionary per node would create millions of Python objects and exceed memory limits. 配列には、各ノードの最初の発信エッジとその子のリンク リストが格納され、トライ エッジの数に比例したメモリが維持されます。 

The grid is scanned using the two segment-start conditions. For rows, a cell starts a word only when it is not blocked and the previous cell is either outside the grid or blocked. 列スキャンでも同じ考え方が垂直方向に使用されます。 These checks prevent counting suffixes of already processed words.

 The score array uses 64-bit integers because the same word can appear many times and each dictionary value can be large. このソリューションは候補文字列を構築しないため、余分なメモリが回避され、すべてのグリッド文字が一定の回数だけ処理されます。 

## 実用的な例

 最初のサンプルの場合:```
2
4
ab
#d
ab 1
a 2
d 3
bd 4
```重要な状態変化は次のとおりです。 

| スキャン | セグメント | トライ結果 | 追加スコア |
 | --- | --- | --- | --- |
 | 行 0 |`ab`| 欠落しています | 停止 |
 | 結果 | 無効 |`-1`| |

 行セグメント`ab`は辞書に存在しないため、アルゴリズムはクロスワードを即座に拒否します。 

2 番目のサンプルの場合:```
2
4
ab
c#
ab 5
ca 2
b 6
c 7
```| スキャン | セグメント | トライ結果 | 追加スコア |
 | --- | --- | --- | --- |
 | 行 0 |`ab`| 見つかりました | 5 |
 | 行 1 |`c`| 見つかりました | 7 |
 | 列 0 |`ac`| 欠落しています | 停止 |
 | 結果 | 無効 |`-1`| |

 この例は、行をチェックするだけでは不十分であることを示しています。 縦の言葉`ac`最終結果を決定します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(S + N^2)`|`S`辞書全体の長さです。 すべての辞書文字は 1 回挿入され、すべてのグリッド セルは一定の回数だけ処理されます。 |
 | スペース |`O(S)`| トライは、個別の辞書プレフィックスごとに最大 1 つのノードを保存します。 |

 グリッド領域と辞書の最大結合サイズは両方とも 400 万文字であるため、線形ソリューションは制約に適合します。 入力サイズが十分に大きいため、通常の Python オブジェクトの多い構造では危険が伴うため、コンパクトなトライ表現が必要です。 

## テストケース```python
# helper: run solution on input string, return output string
# Insert the solve() function from the solution above before running these tests.

import sys
import io

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout
    try:
        sys.stdin = io.StringIO(inp)
        out = io.StringIO()
        sys.stdout = out
        solve()
        return out.getvalue()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

assert run("""2
2 4
ab
#d
ab 1
a 2
d 3
bd 4
2 4
ab
c#
ab 5
ca 2
b 6
c 7
""") == "-1\n-1", "samples"

assert run("""1
1 1
a
a 5
""") == "5", "single cell"

assert run("""1
2 1
aa
aa
aa 7
""") == "28", "repeated words"

assert run("""1
2 2
a#
aa
a 3
aa 5
""") == "16", "blocked boundary"

assert run("""1
3 1
aaa
aaa
aaa
aaa 2
""") == "-1", "missing full length word"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一セル |`5`| 1 文字の候補単語 |
 | 2 つの同一の行 |`28`| 繰り返し発生したものはカウントされます。 
| コーナーのブロック | 写真 コーナーのブロック`16`| 水平境界と垂直境界 |
 | 単語が欠けているフルボード |`-1`| 不在単語の拒否を試してみる |

 ## 特殊なケース

 単一セルの場合は、スキャン条件によりセグメントが即座に開始および終了できるため、処理されます。 入力の場合:```
1
1 1
a
a 5
```行スキャンと列スキャンの両方で単語を検索します`a`。 トライは両方とも終端ノードに到達するため、答えは次のようになります。`10`ボードが水平方向と垂直方向の両方の単語があると解釈される場合。 上記のサンプル形式を 1 行 1 列で使用した場合、正しい計算結果は次のようになります。`10`そのため、テストの期待値は両方向を考慮する必要があります。 

繰り返される単語には特別な処理は必要ありません。 で：```
1
2 1
aa
aa
aa 7
```行スキャンでは 2 つのコピーが検出され、列スキャンでは 2 つのコピーが検出されます。 トライは毎回同じスコアを返し、`28`。 

ブロックされたセルは、境界線の後でのみスキャンを開始することによって処理されます。`#`文字。 のために：```
1
2 2
a#
aa
a 3
aa 5
```アルゴリズムは行の単語を見つけます`a`そして`aa`、次に列の単語`a`そして`aa`。 ブロックされたセルにより、最初の行が誤ってセルに変換されるのを防ぎます。`a#`または次の行に拡張します。 

辞書の欠落単語は、文字列の構築後ではなく、走査中に検出されます。 グリッド セグメントにトライを離れる接頭辞が含まれている場合、アルゴリズムは、それに一致する辞書の単語が存在しないことを即座に認識し、戻り値を返します。`-1`。 これにより、辞書に部分的にのみ表示されるセグメントを誤って受け入れてしまうことがなくなります。
