---
title: "CF 102168E - \u041a\u0443\u0431\u0438\u043a\u0438"
description: "寸法 x × y × z の単位立方体の長方形の箱があります。 立方体は座標 (x、y、z) によって識別されます。 3 つの 2 次元配列は、3 つの座標投影でどの位置が表示されるかを記述します。"
date: "2026-08-19T07:22:12+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102168
codeforces_index: "E"
codeforces_contest_name: "\u041b\u0438\u0447\u043d\u044b\u0439 \u0447\u0435\u043c\u043f\u0438\u043e\u043d\u0430\u0442 \u0421\u0430\u043c\u0430\u0440\u0441\u043a\u043e\u0433\u043e \u0443\u043d\u0438\u0432\u0435\u0440\u0441\u0438\u0442\u0435\u0442\u0430 \u0441\u0440\u0435\u0434\u0438 \u043d\u043e\u0432\u0438\u0447\u043a\u043e\u0432 2018-2019"
rating: 0
weight: 102168
solve_time_s: 138
verified: true
draft: false
---

[CF 102168E - \u041a\u0443\u0431\u0438\u043a\u0438](https://codeforces.com/problemset/problem/102168/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 18 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 寸法を持つ単位立方体の長方形の箱があります。`x × y × z`。 立方体は座標によって識別されます`(x, y, z)`。 3 つの 2 次元配列は、3 つの座標投影でどの位置が表示されるかを記述します。 

左側の投影にはサイズがあります`z × y`、 それで`left[z][y]`は`#`少なくとも 1 つのキューブが修正されたとき`y`そして`z`座標が存在します。 前面投影にはサイズがあります`z × x`、 それで`front[z][x]`は`#`それらを使ったキューブがあるとき`x`そして`z`座標が存在します。 上部の突起にはサイズがあります`y × x`、 それで`top[y][x]`は`#`それらを使ったキューブがあるとき`x`そして`y`座標が存在します。 

3 つの投影が指定された配列とまったく同じである実際の 3 次元図形を再構築する必要があります。 このようなすべての図形の中で、できるだけ多くの立方体を含む図形が必要です。 

特定のポジションについて`(x, y, z)`、立方体は、対応する 3 つの投影セルがすべて存在する場合にのみ存在できます。`#`。 そのうちの一つでもあれば`.`、そこに立方体を置くと、すぐにその投影に不要なオブジェクトが含まれるようになります。`#`。 

寸法は最大でも`100`, したがって、最大でも`100^3 = 1,000,000`可能なキューブの位置。 すべてのポジションにわたる直線的なパスが簡単に実現可能です。 これは、キューブのサブセットを列挙したり、指数関数的に多くの構成を探索したりするアルゴリズムを避ける必要があることも意味します。 3 次元空間全体にわたる二次作業でさえ、すでに不必要にコストがかかることになります。 

主なエッジケースは、必要条件と十分条件を混同することから発生します。 たとえば、次のように考えてみましょう```
2 2 1#..#
.##.
#...
```唯一の`#`左の投影では、`(y=0,z=0)`、したがって、それを実現する立方体は次のものを持たなければなりません`y=0`。 唯一の`#`正面投影では次のことが必要です`x=1`、一方、唯一の`#`上部の投影で`y=0`必要`x=0`。 単一の立方体で 3 つの要件すべてを満たすことはできないため、正しい答えは次のとおりです。`NO`。 すべての投影に何らかの要素が含まれているかどうかを確認するだけの不注意な解決策`#`間違って受け入れてしまうだろう。 

もう 1 つのエッジ ケースは、次のような投影セルです。`#`他のいくつかのキューブを配置できる場合でも、互換性のあるキューブがありません。 例えば、```
2 2 1##..
.#..
#...
```左の投影には両方が必要です`y=0`そして`y=1`立方体を入れる。 前面投影では、`x=1`、一方、上部の突起では、`x=0`で`y=0`。 の`y=0`左のセルは実現できないので、答えは次のようになります。`NO`。 2 つの投影によって許可されるすべての位置を単純に埋める構成では、間違った 3 番目の投影が黙って作成される可能性があります。 

反対の極端な場合、3 つの投影すべてが完全に構成されている場合、`#`、それぞれの`x*y*z`ポジションを埋めることができます。 答えは、可能な最大数の立方体に達する、完全に満たされたボックスです。 

## アプローチ

 最も直接的な強引なアプローチは、考えられるすべての 3 次元図形を考慮し、その投影をチェックすることです。 がある`x*y*z`可能な立方体の位置なので、異なる数字の数は次のようになります。`2^(x*y*z)`。 最大のケースではこれは`2^1,000,000`処理可能な構成をはるかに超えた、可能な構成が可能です。 

より有用な素朴なアプローチは、考えられるすべての立方体を調べて、それが 3 つの投影と互換性があるかどうかを判断することです。 これはすでに重要な観察を示唆しています。 キューブの場合`(x, y, z)`、その存在はまさに次の場合に許可されます```
left[z][y] = '#'front[z][x] = '#'top[y][x] = '#'
```このような許可されたキューブがすべて配置されているとします。 この構造では、`#`元々あった任意の投影セルに`.`なぜなら、配置されたすべての立方体は、3 つの投影すべてに一致することが明示的に要求されたからです。 

残りの問題は、すべてがオリジナルかどうかです。`#`実際には、少なくとも 1 つの配置された立方体によって表されます。 同じスキャン中にこれに答えることができます。 位置が 3 つの条件をすべて満たす場合は常に、その 3 つの投影セルをカバー済みとしてマークします。 全部処理したら`x*y*z`ポジションごとに`#`すべての投影がカバーされている必要があります。 多少なら`#`その投影セルをカバーできる立方体はすべて、他の 2 つの投影も満たさなければならないため、有効な図形は存在できません。 

同じ観察は最大性も証明します。 有効な解のすべての立方体は、3 つの投影法すべてと互換性のある位置のセットに属している必要があります。 私たちの建築にはそのようなあらゆる立場が含まれています。 したがって、他の有効な図形にはそれ以上の立方体を含めることはできません。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | 数値に対するブルートフォース |`O(2^(xyz))`それより悪い | 指数 | 遅すぎる |
 | すべての立方体をチェックし、投影をカバーする |`O(xyz)`|`O(xy + xz + yz)`出力以外にも | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 3 つの投影配列を読み取ります。 実際の投影行には空の区切り線のみが含まれるため、空の区切り線は無視します。`#`そして`.`。 
2. 入力投影と同じ形状を持つ 3 つのブール カバレッジ配列を割り当てます。`covered_left[z][y]`これは、投影がそのレフトビュー セルをカバーする有効な立方体がすでに見つかったことを意味します。 他の 2 つの配列も同様の意味を持ちます。 
3. 可能なすべての立方体位置を反復処理します。`(z, y, x)`。 立方体が候補となるのは、まさに次のような場合です。`left[z][y]`、`front[z][x]`、 そして`top[y][x]`すべてです`#`。 
4. すべての候補立方体について、その 3 つの投影セルをカバー済みとしてマークします。 候補を追加しても予測が損なわれることはないため、候補自体も最終回答の一部です。 
5. スキャン後、すべての項目を検査します。`#`それぞれの投影で。 そのようなセルがカバーされていない場合は、印刷します。`NO`。 そのセルをカバーするすべての可能な立方体はすでにテストされており、少なくとも 1 つの他の投影によって拒否されているため、これを修正できる代替配置はありません。 
6. もしすべてが`#`セルが覆われている、印刷する`YES`。 同じ互換性条件を使用して、すべての出力セルを再度生成します。 ポジションというのは、`#`対応する 3 つの投影セルがすべて揃ったとき、正確に`#`; それ以外の場合はそうです`.`。 
7. レイヤーを増加させて印刷します。`z`連続するレイヤーの間に空行を入れて順序付けします。 これは、必要なレイヤー形式と一致します。 

### なぜ効果があるのか

 セットを検討してください`C`3 つの投影セルが存在するすべての立方体位置の`#`。 すべての有効な Figure には、次の立方体のみを含めることができます。`C`、外に立方体があるので、`C`を作成します`#`空いているはずの投影位置に。 私たちの構造にはあらゆる立方体が含まれています`C`したがって、少なくとも有効な図形と同じ数の立方体が含まれています。 

この構造は、すべての入力が行われたときに正確な投影を生成します。`#`少なくとも 1 つのキューブに属します`C`。 カバレッジ配列はこの条件を正確にテストします。 すべての場合`#`がカバーされ、必要なすべての投影セルが生成されますが、禁止されたものはありません`.`配置されたすべてのキューブが所属しているため生産可能`C`。 多少なら`#`はカバーされておらず、それを作成できる互換性のあるキューブがないため、有効な数値は存在しません。 これは実現可能性と最大性の両方を証明します。 

## Python ソリューション```python
Pythonimport sysinput = sys.stdin.readline

def solve():    x, y, z = map(int, input().split())
    # Empty lines are separators between projections.    lines = [line.strip() for line in sys.stdin if line.strip()]
    pos = 0
    left = lines[pos:pos + z]    pos += z
    front = lines[pos:pos + z]    pos += z
    top = lines[pos:pos + y]
    covered_left = [[False] * y for _ in range(z)]    covered_front = [[False] * x for _ in range(z)]    covered_top = [[False] * x for _ in range(y)]
    # Find every cube that is compatible with all three projections.    for zz in range(z):        for yy in range(y):            if left[zz][yy] != '#':                continue
            for xx in range(x):                if front[zz][xx] != '#':                    continue                if top[yy][xx] != '#':                    continue
                covered_left[zz][yy] = True                covered_front[zz][xx] = True                covered_top[yy][xx] = True
    # Every '#' in every projection must be represented.    for zz in range(z):        for yy in range(y):            if left[zz][yy] == '#' and not covered_left[zz][yy]:                print("NO")                return
    for zz in range(z):        for xx in range(x):            if front[zz][xx] == '#' and not covered_front[zz][xx]:                print("NO")                return
    for yy in range(y):        for xx in range(x):            if top[yy][xx] == '#' and not covered_top[yy][xx]:                print("NO")                return
    print("YES")
    # Every compatible cube is present in the maximum construction.    for zz in range(z):        for yy in range(y):            row = []            for xx in range(x):                if (                    left[zz][yy] == '#'                    and front[zz][xx] == '#'                    and top[yy][xx] == '#'                ):                    row.append('#')                else:                    row.append('.')            print(''.join(row))
        if zz + 1 < z:            print()

if __name__ == "__main__":    solve()
```実装の最初の部分では、ディメンションの後の空でない行をすべて読み取ります。 入力では 3 つの投影が空白行で明示的に区切られますが、これらの区切り文字には情報が含まれないため、これは便利です。 

3 つのカバレッジ配列には、3 次元図形全体ではなく、投影情報のみが格納されます。 それらの合計サイズは`xy + xz + yz`、これは可能な値よりもはるかに小さいです`xyz`立方体の位置を調べ、要求されたすべての投影セルが実現されたかどうかを判断するにはすでに十分です。 

ネストされたループは、中心条件を直接実装します。 インデックスは意図的に次のように書かれています`zz`、`yy`、 そして`xx`したがって、3 つの投影との関係は目に見えるままになります。 左側の投影では、`(zz, yy)`、前面投影は使用します`(zz, xx)`、上部の投影は使用します`(yy, xx)`。 

構築した図を保存する必要はありません。 実現可能性が確立された後、印刷中に同じ 3 つの条件を再度評価できます。 これにより実装が単純になり、100 万要素の 3 次元構造を新たに割り当てる必要がなくなりました。 

3 つの予測のいずれかが失敗すると全体の再構築が不可能になるため、カバレッジ チェックは 3 つの予測に対して個別に行われます。 Python には整数オーバーフローの問題はなく、最大のループには 100 万回の反復のみが含まれます。 

## 実用的な例

 ### サンプル 1

 最初のサンプルの寸法は次のとおりです。`x=4`、`y=3`、`z=2`。 最初のレイヤーには、次の投影互換の行があります。```
#####.#####.
```2番目の層は```
####....###.
```アルゴリズムはそれぞれに到達します`#`少なくとも 1 つの互換性のある立方体を介した投影内。 

|`z`|`y`|`x`| 左 | フロント | トップ | キューブ |
 | --- | --- | --- | --- | --- | --- | --- |
 | 0 | 0 | 0 | # | # | # | # |
 | 0 | 0 | 1 | # | # | # | # |
 | 0 | 0 | 2 | # | # | # | # |
 | 0 | 0 | 3 | # | # | # | # |
 | 0 | 1 | 0 | # | # | # | # |
 | 0 | 1 | 1 | # | 。 | # | 。 |
 | 0 | 1 | 2 | # | # | # | # |
 | 0 | 1 | 3 | # | # | # | # |
 | 0 | 2 | 0 | # | # | # | # |
 | 0 | 2 | 1 | # | # | # | # |
 | 0 | 2 | 2 | # | # | # | # |
 | 0 | 2 | 3 | # | # | 。 | 。 |

 同じテストが実行されます`z=1`。 必要なすべての投影セルがカバーされるため、答えは次のようになります。`YES`。 互換性のあるすべての位置を埋めると、可能な限り最大の数値が得られます。 

### サンプル 2

 すべてのために-`#` `2 × 2 × 2`たとえば、すべての立方体はすべての投影セルと互換性があります。 

|`z`| 候補者のポジション | 覆われた左側のセル | カバーされたフロントセル | カバーされた上部セル |
 | --- | --- | --- | --- | --- |
 | 0 | 4 | 全2 | 全2 | 全4 |
 | 1 | 4 | 全2 | 全2 | 全4 |

 互換性のあるキューブは 8 つあるので、8 つすべてが配置されます。 出力は 2 つのレイヤーで構成され、それぞれに次の内容が含まれます。```
####
```この例は、最大性の特性を特に明確に示しています。 投影が含まれていないため、`.`、互換性のあるキューブを空のままにしておく理由はありません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(xyz)`| すべての可能な立方体の位置が 1 回検査され、その後、`O(xy + xz + yz)`投影チェックと出力。 |
 | スペース |`O(xy + xz + yz)`| 入力投影と 3 つのカバレッジ配列が保存されます。 三次元図形は出力時に直接生成されます。 |

 と`x,y,z <= 100`、メイン ループは最大 100 万回のキューブ チェックを実行します。 生成される出力自体には約 100 万文字が含まれる可能性があるため、実行時間はプログラムが印刷する必要があるデータ量に自然に比例します。 

## テストケース

 有効な再構成は必ずしも一意であるとは限らないため、テスト ハーネスは任意の再構成を比較すべきではありません。`YES`文字ごとに出力します。 以下のヘルパーは、返された Figure を 3 つの投影に対して検証し、Figure に最大数の立方体があることもチェックします。```python
Python# helper: run solution on input string, return output stringimport sysimport io

def solve():    x, y, z = map(int, input().split())
    lines = [line.strip() for line in sys.stdin if line.strip()]    pos = 0
    left = lines[pos:pos + z]    pos += z
    front = lines[pos:pos + z]    pos += z
    top = lines[pos:pos + y]
    covered_left = [[False] * y for _ in range(z)]    covered_front = [[False] * x for _ in range(z)]    covered_top = [[False] * x for _ in range(y)]
    for zz in range(z):        for yy in range(y):            if left[zz][yy] != '#':                continue            for xx in range(x):                if front[zz][xx] == '#' and top[yy][xx] == '#':                    covered_left[zz][yy] = True                    covered_front[zz][xx] = True                    covered_top[yy][xx] = True
    for zz in range(z):        for yy in range(y):            if left[zz][yy] == '#' and not covered_left[zz][yy]:                print("NO")                return
    for zz in range(z):        for xx in range(x):            if front[zz][xx] == '#' and not covered_front[zz][xx]:                print("NO")                return
    for yy in range(y):        for xx in range(x):            if top[yy][xx] == '#' and not covered_top[yy][xx]:                print("NO")                return
    print("YES")
    for zz in range(z):        for yy in range(y):            print(''.join(                '#'                if left[zz][yy] == '#'                and front[zz][xx] == '#'                and top[yy][xx] == '#'                else '.'                for xx in range(x)            ))        if zz + 1 < z:            print()

def run(inp: str) -> str:    global input
    old_stdin = sys.stdin    old_stdout = sys.stdout    old_input = input
    sys.stdin = io.StringIO(inp)    sys.stdout = io.StringIO()    input = sys.stdin.readline
    try:        solve()        return sys.stdout.getvalue()    finally:        sys.stdin = old_stdin        sys.stdout = old_stdout        input = old_input

def parse_result(inp: str, out: str):    data = [line.strip() for line in inp.splitlines() if line.strip()]    x, y, z = map(int, data[0].split())
    p = 1    left = data[p:p + z]    p += z    front = data[p:p + z]    p += z    top = data[p:p + y]
    out_lines = out.splitlines()    assert out_lines, "empty output"
    if out_lines[0] == "NO":        return False, None, (left, front, top, x, y, z)
    assert out_lines[0] == "YES"
    figure = []    p = 1
    for zz in range(z):        layer = []        for yy in range(y):            row = out_lines[p]            p += 1            assert len(row) == x            assert all(c in ".#" for c in row)            layer.append(row)        figure.append(layer)
        if zz + 1 < z:            assert out_lines[p] == ""            p += 1
    return True, figure, (left, front, top, x, y, z)

def validate(inp: str, out: str) -> bool:    ok, figure, info = parse_result(inp, out)    left, front, top, x, y, z = info
    expected_exists = True
    for zz in range(z):        for yy in range(y):            if left[zz][yy] == '#':                if not any(                    figure[zz][yy][xx] == '#'                    for xx in range(x)                ):                    expected_exists = False
    for zz in range(z):        for xx in range(x):            if front[zz][xx] == '#':                if not any(                    figure[zz][yy][xx] == '#'                    for yy in range(y)                ):                    expected_exists = False
    for yy in range(y):        for xx in range(x):            if top[yy][xx] == '#':                if not any(                    figure[zz][yy][xx] == '#'                    for zz in range(z)                ):                    expected_exists = False
    if not ok:        return not expected_exists
    # The construction must contain exactly every position compatible    # with all three projections.    for zz in range(z):        for yy in range(y):            for xx in range(x):                allowed = (                    left[zz][yy] == '#'                    and front[zz][xx] == '#'                    and top[yy][xx] == '#'                )                assert (figure[zz][yy][xx] == '#') == allowed
    return expected_exists

# Provided Sample 1.sample1 = """\4 3 2####.##############.#####."""
out = run(sample1)assert out == """\YES#####.#####.####....###.""", "sample 1"

# Provided NO sample, reconstructed from the three projection groups# shown in the statement.sample_no = """\3 3 3#...#...#.#...##....##...#."""
assert run(sample_no).strip() == "NO", "provided NO sample"

# Minimum-size valid instance.minimum = """\1 1 1#
#
#"""
assert run(minimum) == """\YES#""", "minimum valid instance"

# Minimum-size impossible instance. At least one projection requests# a cube, but the three requests cannot refer to the same cube.minimum_no = """\1 1 1.
#
#"""
assert run(minimum_no).strip() == "NO", "minimum impossible instance"

# Boundary case where every projection is full.all_full = """\2 2 2####
####
####"""
assert validate(all_full, run(all_full)), "all projections full"

# A compatibility conflict: every projection has '#', but no cube can# satisfy all three projections simultaneously.conflict = """\2 2 1#...
.#..
#..."""
assert run(conflict).strip() == "NO", "incompatible projections"

# A larger boundary case with one compatible cube and many empty cells.single_cube = """\3 3 2#........
.#.......
.#......."""
assert validate(single_cube, run(single_cube)), "single compatible cube"
```この実装では、最初のサンプルの決定論的な構造が一意の結果になるため、最初のアサーションは完全な出力を比較します。 残りの陽性テストでは、`validate`、建設的な問題では通常、いくつかの異なる有効な出力が許可されるためです。 

all-full テストは、すべてのループの上限をチェックする場合に特に便利です。 競合テストは最も一般的な論理的間違いを検出し、すべてのプロジェクションに要求されたものが含まれているという理由だけでインスタンスを受け入れます。`#`細胞は独立しています。 単一立方体テストでは、ボックス内の非ゼロ座標を実行し、空の投影セルによって対応する 3 次元位置が強制的に空のままであることを確認します。 

| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 最初に提供されたサンプル | ちょうど`YES`建設 | 通常の再構成と最大限の充填 |
 | 提供された`NO`サンプル |`NO`| 互換性のない投影制約 |
 |`1 × 1 × 1`、 全て`#`| 1 つの立方体 | 最小寸法 |
 |`1 × 1 × 1`、1つの投影`.`|`NO`| 最小の不可能な例 |
 |`2 × 2 × 2`、 全て`#`| キューブ 8 個入りフルボックス | 完全な境界と最大限の構造 |
 | 矛盾している`2 × 2 × 1`予測 |`NO`| 互換性のないものを検出しています`#`セル |
 | まばらな`3 × 3 × 2`インスタンス |`YES`、互換性のあるキューブ 1 つ | 座標マッピングと空のセル |

 ## 特殊なケース

 最小有効入力の場合、```
1 1 1#
#
#
```可能な立方体は 1 つだけです。 3 つの投影すべてがそれを要求するため、トリプル`(0,0,0)`という3つの条件を満たしています。 すべての投影セルがカバーされているとマークされ、出力は次のようになります。```
YES#
```このアルゴリズムでは、次元に対する特別な処理は必要ありません。`1`; 通常のループは当然 1 回だけ実行されます。 

最小限の不可能な入力に対して、```
1 1 1.
#
#
```唯一の可能な立方体は、左の投影が次のとおりであるため、すぐに拒否されます。`.`。 二人`#`他の投影内のセルはその立方体なしでは実現できないため、カバレッジ チェックでカバーされていないセルが検出されます。`#`そして戻ってきます`NO`。 

紛争事件```
2 2 1#...
.#..
#...
```は、3 つの投影をすべて一緒にチェックする必要がある理由を示しています。 左の投影は次のことを求めます`y=0`、正面投影は次のことを求めます`x=1`、上部の投影は次のことを要求します`x=0`で`y=0`。 唯一の高さレイヤーでは、`(x,y)`ペアは 3 つの条件をすべて満たします。 その結果`covered_left[0][0]`false のままで、アルゴリズムはインスタンスを拒否します。 

完全に満たされた場合`2 × 2 × 2`箱、```
2 2 2####
####
####
```あらゆる組み合わせ`x`、`y`、 そして`z`3つの条件をすべて満たしています。 スキャンはすべての投影セルをカバーし、出力には 8 つの立方体すべてが含まれます。 考えられるすべてのキューブに互換性があるため、有効なソリューションには 8 個を超えるキューブを含めることはできず、構築は最適になります。 

スパースケースも特別なケースなしで処理されます。 マークされた投影セル`.`その上にマッピングされるすべての 3 次元位置を削除します。 最終的な図は正確に 3 つのリフトされた投影の交差点であるため、1 次元の境界と内部の空の領域は同様に扱われます。 これは、構造全体の背後にある中心的な不変条件です。
