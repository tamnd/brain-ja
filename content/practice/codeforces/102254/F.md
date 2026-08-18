---
title: "CF 102254F - 友情は重要"
description: "(n) 人の生徒がおり、それぞれが固有の名前で識別されます。 最初は、すべての生徒が別々のチームに所属します。 タイプ 1 の操作では、指定された 2 人の生徒を含むチームが結合されます。 タイプ 2 の操作では、これら 2 人の学生が現在同じチームに属しているかどうかが尋ねられます。"
date: "2026-08-17T21:20:34+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102254
codeforces_index: "F"
codeforces_contest_name: "IME++ Starters Try-outs 2019"
rating: 0
weight: 102254
solve_time_s: 446
verified: false
draft: false
---

[CF 102254F - 友情は重要](https://codeforces.com/problemset/problem/102254/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 7 分 26 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 (n) 人の生徒がおり、それぞれが固有の名前で識別されます。 最初は、すべての生徒が別々のチームに所属します。 タイプ 1 の操作では、指定された 2 人の生徒を含むチームが結合されます。 タイプ 2 の操作では、これら 2 人の学生が現在同じチームに属しているかどうかが尋ねられます。 

名前は文字列であるため、操作を処理する前に、各名前をコンパクトな整数の識別子に変換する方法が必要です。 それが完了すると、実際の問題は純粋に、マージ下で接続されたグループを維持し、接続クエリに応答することになります。 

入力には、最大 (10^5) 個の生徒と (10^5) 個の演算が含まれます。 このサイズでは、(O(nq)) ソリューションは (10^{10}) 個もの基本演算を実行できますが、これは 1 秒の時間制限をはるかに超えています。 (O(n^2)) 個の前処理ステップも不可能です。 各クエリに対して、事実上一定時間、またはそれに非常に近い時間の操作が必要です。 

不注意に実装すると誤った結果が生じる可能性があるケースがいくつかあります。 学生は、以前に別のグループと結合されたグループに結合することができます。 例えば：```
3 3
Ana
Bob
Cat
1 Ana Bob
1 Bob Cat
2 Ana Cat
```正しい出力は次のとおりです。```
yes
```各生徒の最新のパートナーのみを記憶するソリューションでは、誤答する可能性があります。`no`というのも、Ana は Cat と直接合併したことがないからです。 チームのメンバーシップは推移的であるため、接続されているコンポーネント全体が重要です。 

タイプ 1 のクエリには、既に同じチームに所属している 2 人の学生が関与することもあります。```
2 2
Ana
Bob
1 Ana Bob
1 Ana Bob
```2 回目の変更は必要ありません。 ルートが異なると想定した不用意なマージ実装は、このケースを処理しないとデータ構造を破損する可能性があります。 

最後に、クエリは、チームのサイズが大きく異なる学生を参照できます。```
4 2
Ana
Bob
Cat
Dan
1 Ana Bob
2 Cat Dan
```答えは、`no`, なぜなら、キャットもダンもお互いにつながっていないからです。 デフォルトの親値のために誤って関連のないインデックスを接続されたものとして扱うソリューションは、ここで失敗する可能性があります。 すべての学生は、独自のコンポーネントのルートとして開始する必要があります。 

## アプローチ

 最も直接的なアプローチは、すべてのチームのメンバーを明示的に保存することです。 最初は各チームに 1 人の生徒が含まれます。 マージによって (x) と (y) のチームを結合するように要求された場合、一方のチームからすべての生徒を取り出し、そのチーム識別子をもう一方のチームの識別子に変更できます。 次に、タイプ 2 クエリは、保存されている 2 つのチーム ID を比較します。 

これは正しいです。マージのたびに、結果として得られるチームのすべての生徒が同じ識別子を受け取るからです。 問題は、マージに必要な作業量です。 最悪の場合、チームには (10^5) 人の学生が含まれる可能性があり、一連の長いクエリによって大規模なチームを繰り返し検査する必要が生じる可能性があります。 (10^5) クエリを使用すると、単純な実装で (10^5 \times 10^5 = 10^{10}) 個の生徒の更新に到達できます。 

クエリで実際に必要な情報は 2 人の生徒が同じコンポーネント識別子を持っているかどうかだけであるため、総当たり法が機能します。 この失敗は、マージのたびにすべてのメンバーの情報を物理的に維持することに起因します。 すでにチームに所属している学生の数に関係なく、2 つのチームに参加しても、保存されている情報が少量だけ変更されるという表現が必要です。 

重要な観察は、チームが互いに素な接続コンポーネントを形成しているということです。 学生はチーム内の他の学生全員を知る必要はありません。 必要なのは、そのチームの代表者に私たちを導くことだけです。 2 人の生徒が最終的に同じ代表にたどり着いた場合、彼らは同じコンポーネントに属します。 

これはまさに、DSU または Union-find とも呼ばれる、素セット共用体構造の設定です。 各コンポーネントはルートによって表されます。 マージは、どちらかのコンポーネントのすべてのメンバーを書き換えるのではなく、2 つのルートを結合します。 パス圧縮により、今後の検索が非常に短くなりますが、サイズまたはランクによる結合により、内部ツリーが不必要に深くなるのを防ぎます。 

どちらの最適化でも、各操作には償却 (O(\alpha(n))) 時間がかかります。ここで、(\alpha) は逆アッカーマン関数であり、実際のすべての入力サイズに対して事実上一定です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(nq)) 最悪の場合 | (O(n)) | 遅すぎる |
 | パス圧縮とサイズによる結合を備えた DSU | (O((n+q)\alpha(n))) | (O(n)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 名前から ID までの辞書を使用して、すべての生徒に (0) から (n-1) までの整数 ID を割り当てます。 整数 ID を使用すると、DSU 配列がコンパクトになり、データ構造内で文字列を繰り返し格納したり比較したりすることがなくなります。 
2.`parent`配列`parent[i] = i`すべての生徒のために。 最初は各生徒がチームの唯一のメンバーであるため、すべての生徒が自分の代表になります。 
3.`size`配列は (1) に初期化されます。 この値は、各ルートで表されるコンポーネントに属する生徒の数を記録します。 これは、マージ中にどのルートが親になるかを決定するために使用されます。 
4. タイプ 1 クエリの場合、両方の名前を整数 ID に変換し、次のコマンドでそのルートを見つけます。`find`。 ルートが等しい場合、学生はすでに同じチームに属しているため、操作は何も変わりません。 
5. ルートが異なる場合は、コンポーネントのサイズを比較し、小さいコンポーネントのルートが大きいコンポーネントのルートを指すようにします。 小さい方のサイズを大きい方のルートのサイズに加算します。 大きいツリーの下に小さいツリーを接続すると、DSU ツリーが浅くなります。 
6. タイプ 2 のクエリでは、両方の生徒のルートを見つけます。 印刷する`yes`根が等しい場合と`no`さもないと。 ルーツは現在のチームの代表者であるため、ルーツの平等とはまさに生徒が同じチームに所属する条件となります。 
7. で`find`、それ自体の親である頂点に到達するまで、親ポインターをたどります。 戻るときに、訪問した各生徒の親をそのルートに置き換えます。 これはパス圧縮であり、それらの学生が関与する今後のクエリが大幅に高速化されます。 

### なぜ効果があるのか

 不変条件は、すべての DSU コンポーネントが正確に 1 つの現在のチームを表し、そのチームのすべての生徒が最終的に同じルートに到達するということです。 最初はこれが当てはまります。なぜなら、すべての学生は孤独だからです。 マージでは 2 つの異なるルートのみが接続されるため、無関係なチームを接続することなく、対応する 2 つのチームを 1 つのコンポーネントに正確に結合します。 パス圧縮によって変更されるのはコンポーネントの内部表現のみであり、コンポーネントに属する頂点は変更されません。 その結果、タイプ 1 の一連の操作を通じてチームが参加したとき、2 人の学生はまったく同じルートを持っているため、タイプ 2 のすべての答えが正しいことになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n, q = map(int, input().split())

    name_to_id = {}
    for i in range(n):
        name = input().strip()
        name_to_id[name] = i

    parent = list(range(n))
    size = [1] * n

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x

    out = []

    for _ in range(q):
        t, sx, sy = input().split()
        x = name_to_id[sx]
        y = name_to_id[sy]

        rx = find(x)
        ry = find(y)

        if t == '1':
            if rx == ry:
                continue

            if size[rx] < size[ry]:
                rx, ry = ry, rx

            parent[ry] = rx
            size[rx] += size[ry]

        else:
            out.append("yes" if rx == ry else "no")

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```後続のすべてのクエリでは学生を名前で識別するため、辞書が最初に作成されます。 各名前は 1 つの安定した整数 ID を受け取るため、DSU が文字列を直接操作する必要はありません。 

の`parent`配列にはフォレスト構造が格納されます。 根は満たす`parent[root] == root`、それは与える`find`正確な停止状態。 反復実装により、Python の再帰の深さの問題が回避されます。`parent[x] = parent[parent[x]]`検索中にパスの半分を実行します。 

結合の場合、何かを変更する前にルートが見つかります。 それらがすでに等しい場合、操作は無視されます。 それ以外の場合は、小さいコンポーネントが大きいコンポーネントに接続されます。 新しいルートのサイズは、親関係が変更された後にのみ増加し、子ルートに格納されているサイズは重要ではなくなります。 

クエリの場合、構造は、によって実行されるパス圧縮を超えて意図的に変更されません。`find`。 2 つのルートが直接比較されます。 ID の範囲は (0) から (n-1) までであり、Python の整数にはオーバーフローの問題がないため、インデックス作成の境界の問題はありません。 

出力はリストに蓄積され、最後に一度書き込まれます。 最大 (10^5) 個のクエリで、不必要な出力呼び出しの繰り返しが回避され、I/O が制限内に快適に維持されます。 

## 実用的な例

 ### サンプル 1

 重要な DSU の状態は、コンポーネント セットによって表すことができます。 以下のルート名は概念的にコンポーネントを説明しますが、実装では整数のルートが格納されます。 

| クエリ | 操作 | 操作後のコンポーネント | 出力 |
 | --- | --- | --- | --- |
 | 1 |`1 Naum Rebeca`|`{Naum, Rebeca}`、`{Navarro}`、`{Arnon}`、`{Matheus}`、`{Xavier}`| |
 | 2 |`2 Rebeca Naum`| 変更なし |`yes`|
 | 3 |`1 Matheus Xavier`|`{Matheus, Xavier}`、`{Naum, Rebeca}`、`{Navarro}`、`{Arnon}`| |
 | 4 |`1 Navarro Arnon`|`{Navarro, Arnon}`、`{Matheus, Xavier}`、`{Naum, Rebeca}`| |
 | 5 |`2 Matheus Navarro`| 変更なし |`no`|
 | 6 |`2 Rebeca Matheus`| 変更なし |`no`|
 | 7 |`1 Navarro Matheus`|`{Navarro, Arnon, Matheus, Xavier}`、`{Naum, Rebeca}`| |
 | 8 |`2 Xavier Arnon`| 変更なし |`yes`|
 | 9 |`2 Xavier Rebeca`| 変更なし |`no`|
 | 10 |`1 Rebeca Arnon`|`{Navarro, Arnon, Matheus, Xavier, Naum, Rebeca}`| |
 | 11 |`2 Naum Rebeca`| 変更なし |`yes`|
 | 12 |`2 Naum Matheus`| 変更なし |`yes`|
 | 13 |`2 Naum Xavier`| 変更なし |`yes`|

 興味深いのはクエリ 10 です。Rebeca は Naum を含むコンポーネントに属し、Arnon は Navarro、Matheus、および Xavier を含むコンポーネントに属します。 組合はこれら 2 つのルーツに加わり、現在 6 人の学生全員が同じ代表者を擁しています。 最後の 3 つのクエリは推移性を示しています。Naum は Matheus または Xavier と直接マージされることはありませんでしたが、3 つすべてが同じコンポーネントのメンバーになりました。 

### サンプル 2

 | クエリ | 操作 | 操作後のコンポーネント | 出力 |
 | --- | --- | --- | --- |
 | 1 |`1 Sergio Mateus`|`{Sergio, Mateus}`、`{Cesar}`、`{Gustavo}`、`{Caio}`、`{Yu}`| |
 | 2 |`1 Cesar Yu`|`{Cesar, Yu}`、`{Sergio, Mateus}`、`{Gustavo}`、`{Caio}`| |
 | 3 |`1 Cesar Gustavo`|`{Cesar, Yu, Gustavo}`、`{Sergio, Mateus}`、`{Caio}`| |
 | 4 |`2 Cesar Sergio`| 変更なし |`no`|
 | 5 |`1 Caio Mateus`|`{Caio, Sergio, Mateus}`、`{Cesar, Yu, Gustavo}`| |
 | 6 |`1 Gustavo Yu`| 変更されていない、すでに同じコンポーネント | |
 | 7 |`2 Caio Sergio`| 変更なし |`yes`|
 | 8 |`2 Gustavo Sergio`| 変更なし |`no`|

 クエリ 6 は、有用なエッジ ケースです。 Gustavo と Yu はすでに Cesar を通じて接続されているため、結合によって新しいコンポーネントは作成されません。 両方の名前が同じルートを生成するため、DSU はこれを検出します。 次に、クエリ 7 は、Caio を Mateus に接続すると、既存のコンポーネントを介して Caio と Sergio も接続することを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O((n+q)\alpha(n))) | ネーム マップの構築には (O(n)) かかり、DSU 操作ごとに (O(\alpha(n))) のコストが償却されます。 |
 | スペース | (O(n)) | 名前マップ、親配列、サイズ配列、および出力ストレージはすべて、入力サイズに応じて直線的に増加します。 |

 (n,q \le 10^5) の場合、DSU の動作は事実上一定時間です。 このソリューションは、クエリごとに線形量の前処理と非常に少数の操作のみを実行するため、1 秒と 256 MB の制限内に問題なく収まります。 保存されるスチューデントとクエリの最大数も、Python の辞書と整数配列に対して十分小さいです。 

## テストケース

 以下のテストヘルパーは同じものを使用します`solve`ロジックは送信されたプログラムと同じですが、文字列を受け入れてその出力をキャプチャするため、アサーションでケースをチェックできます。```python
import sys
import io

def solve():
    input = sys.stdin.readline

    n, q = map(int, input().split())

    name_to_id = {}
    for i in range(n):
        name_to_id[input().strip()] = i

    parent = list(range(n))
    size = [1] * n

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x

    out = []

    for _ in range(q):
        t, sx, sy = input().split()
        x = name_to_id[sx]
        y = name_to_id[sy]

        rx = find(x)
        ry = find(y)

        if t == '1':
            if rx == ry:
                continue

            if size[rx] < size[ry]:
                rx, ry = ry, rx

            parent[ry] = rx
            size[rx] += size[ry]
        else:
            out.append("yes" if rx == ry else "no")

    return "\n".join(out)

def run(inp: str) -> str:
    old_stdin = sys.stdin
    sys.stdin = io.StringIO(inp)
    try:
        return solve()
    finally:
        sys.stdin = old_stdin

sample1 = """\
6 13
Navarro
Arnon
Matheus
Xavier
Rebeca
Naum
1 Naum Rebeca
2 Rebeca Naum
1 Matheus Xavier
1 Navarro Arnon
2 Matheus Navarro
2 Rebeca Matheus
1 Navarro Matheus
2 Xavier Arnon
2 Xavier Rebeca
1 Rebeca Arnon
2 Naum Rebeca
2 Naum Matheus
2 Naum Xavier
"""

assert run(sample1) == """\
yes
no
no
yes
no
yes
yes
yes""", "sample 1"

sample2 = """\
6 8
Sergio
Yu
Mateus
Cesar
Gustavo
Caio
1 Sergio Mateus
1 Cesar Yu
1 Cesar Gustavo
2 Cesar Sergio
1 Caio Mateus
1 Gustavo Yu
2 Caio Sergio
2 Gustavo Sergio
"""

assert run(sample2) == """\
no
yes
no""", "sample 2"

minimum_case = """\
2 4
Aa
Bb
2 Aa Bb
1 Aa Bb
2 Aa Bb
1 Aa Bb
"""

assert run(minimum_case) == """\
no
yes""", "minimum size and repeated union"

transitive_case = """\
5 8
Aa
Bb
Cc
Dd
Ee
1 Aa Bb
1 Cc Dd
2 Aa Dd
1 Bb Cc
2 Aa Dd
2 Bb Dd
1 Aa Dd
2 Aa Ee
"""

assert run(transitive_case) == """\
no
yes
yes
no""", "transitive connectivity"

repeated_queries_case = """\
4 7
Aa
Bb
Cc
Dd
2 Aa Bb
2 Cc Dd
1 Aa Bb
2 Aa Bb
1 Aa Bb
1 Cc Dd
2 Cc Dd
"""

assert run(repeated_queries_case) == """\
no
no
yes
yes""", "repeated queries and no-op unions"

n = 100000
names = [f"A{i}" for i in range(n)]
maximum_case = [f"{n} 3"]
maximum_case.extend(names)
maximum_case.append(f"1 {names[0]} {names[-1]}")
maximum_case.append(f"2 {names[0]} {names[-1]}")
maximum_case.append(f"2 {names[1]} {names[-2]}")
maximum_case = "\n".join(maximum_case) + "\n"

assert run(maximum_case) == """\
yes
no""", "maximum size"

print("all tests passed")
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | サンプル 1 |`yes`、`no`、`no`、`yes`、`no`、`yes`、`yes`、`yes`| 複数のマージ、推移的な接続、および 2 つの大きなコンポーネントのマージ |
 | サンプル 2 |`no`、`yes`、`no`| 操作なしのユニオンと個別のコンポーネント |
 | 最小のケース |`no`、`yes`| 生徒の最小数と既存のコンポーネントの繰り返し結合 |
 | 推移的な場合 |`no`、`yes`、`yes`、`no`| 数人の中級者による接続 |
 | 繰り返されるクエリのケース |`no`、`no`、`yes`、`yes`| ユニオンの前後のクエリ (ユニオンの繰り返しを含む) |
 | 最大ケース |`yes`、`no`| (10^5) 個の生徒と配列の両端の境界 ID |

 ## 特殊なケース

 ユニオンのチェーンは、ソリューションが直接的な関係ではなく接続性を理解しているかどうかをテストします。 例えば：```
3 3
Ana
Bob
Cat
1 Ana Bob
1 Bob Cat
2 Ana Cat
```最初の結合の後、アナとボブは同じルートを持ちます。 2 番目の結合の後、Bob のコンポーネントが Cat のコンポーネントと結合されます。 その結果、Ana と Cat は同じルートに到達するため、出力は次のようになります。```
yes
```DSU は、すべての生徒のペアを明示的に接続せずにこれを処理します。 

結合を繰り返すと、新しいコンポーネントが作成されたり、そのサイズが破損したりしてはなりません。 考慮する：```
2 4
Aa
Bb
1 Aa Bb
1 Aa Bb
2 Aa Bb
2 Bb Aa
```最初の結合は 1 つのコンポーネントを作成します。 2 番目の結合は同一のルートを見つけて、すぐに戻ります。 次に、両方のクエリが同じルートを比較し、次の結果を生成します。```
yes
yes
```ユニオンの前に作成されたクエリでは、個別のシングルトン コンポーネントを区別する必要があります。 例えば：```
2 1
Aa
Bb
2 Aa Bb
```どちらの生徒も最初は自分自身のルートであるため、ルートは異なり、出力は次のようになります。```
no
```これにより、次の初期化がチェックされます。`parent`、すべての生徒に誤って共通のデフォルトのルートを割り当てると、誤って生成されるためです。`yes`。 

最大サイズの境界の場合は、最初と最後の学生 ID を使用します。```
4 3
Aaaa
Bbbb
Cccc
Dddd
1 Aaaa Dddd
2 Aaaa Dddd
2 Bbbb Dddd
```最初の手術後のみ、`Aaaa`そして`Dddd`コンポーネントを共有します。 出力は次のとおりです。```
yes
no
```この実装では、学生の ID が辞書マッピングを超えてその名前や位置に関連しているとは想定されていないため、ID 範囲の両端の学生は同様に処理されます。 

最後の微妙なケースは、既に多くのスチューデントが含まれている 2 つのコンポーネントをユニオンが接続する場合です。```
5 6
Aa
Bb
Cc
Dd
Ee
1 Aa Bb
1 Cc Dd
1 Aa Cc
2 Bb Dd
2 Aa Dd
2 Bb Ee
```3 回目の結合の後、最初の 4 人の学生は 1 つのコンポーネントに属します。 両方`Bb Dd`そして`Aa Dd`したがって、生産します`yes`、 その間`Bb Ee`生成する`no`。 DSU は、4 人のスチューデント全員を個別に更新するのではなく、1 つのルート ポインターを変更することでこれを実現します。これが、メソッドが (10^5) 演算にスケールアップする主な理由です。
