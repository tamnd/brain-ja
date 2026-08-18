---
title: "CF 102262F - \u0422\u0440\u0430\u043d\u0441\u0444\u043e\u0440\u043c\u0430\u0446\u0438\u044f \u0434\u0438\u0440\u0435\u043a\u0442\u043e\u0440\u0438\u0438"
description: "同じディレクトリ ツリーの 2 つのスナップショット、初期状態 A と最終状態 B があります。リストされたすべてのオブジェクトは、末尾の / で認識されるディレクトリ、または関連付けられたハッシュを持つファイルのいずれかです。 ルート ディレクトリ自体は暗黙的であり、入力には表示されません。"
date: "2026-08-17T20:22:27+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102262
codeforces_index: "F"
codeforces_contest_name: "\u0427\u0435\u043c\u043f\u0438\u043e\u043d\u0430\u0442 \u043f\u043e \u043f\u0440\u043e\u0433\u0440\u0430\u043c\u043c\u0438\u0440\u043e\u0432\u0430\u043d\u0438\u044e - \u0444\u0438\u043d\u0430\u043b (\u042f\u043d\u0434\u0435\u043a\u0441)"
rating: 0
weight: 102262
solve_time_s: 148
verified: true
draft: false
---

[CF 102262F - \u0422\u0440\u0430\u043d\u0441\u0444\u043e\u0440\u043c\u0430\u0446\u0438\u044f \u0434\u0438\u0440\u0435\u043a\u0442\u043e\u0440\u0438\u0438](https://codeforces.com/problemset/problem/102262/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 28 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 同じディレクトリ ツリーの 2 つのスナップショット、初期状態 A と最終状態 B があります。リストされたすべてのオブジェクトは、末尾のディレクトリによって認識されるディレクトリです。`/`、または関連付けられたハッシュを持つファイル。 ルート ディレクトリ自体は暗黙的であり、入力には表示されません。 

許可される操作は意図的に制限されています。 ディレクトリは、その親がすでに存在する場合にのみ作成でき、ディレクトリが空の場合にのみ削除できます。 ファイルを最初から作成することはできません。 新しいファイル名を取得する唯一の方法は、既存のファイルへのハードリンクを作成することです。その結果、新しい名前のハッシュはソースとまったく同じになります。 既存のハードリンクは次のように削除できます。`unlink`。 

入力は最大でも`10^4`各スナップショットにオブジェクトがあるため、二次スキャンは最大で実行されます。`10^8`オブジェクトの比較。 これは Python の 2 秒の制限にはすでに多すぎます。また、パス文字列を比較すると、それぞれの比較自体が非定常になる可能性があります。 本質的に線形または線形に近い処理が必要です。 パスとハッシュの最大長はわずか 256 であるため、このサイズの文字列のハッシュ化と並べ替えは実​​用的です。 

いくつかのケースでは、不注意な実装を欺いてしまう可能性があります。 ソース ファイルが、最終的には消えるディレクトリ内にある場合、必要なハードリンクがすべて作成されるまでソースを削除することはできません。 例えば、```
1 1
/old/x h
/new/x h
```2 つの操作が必要ですが、`link /old/x /new/x`に続く`unlink /old/x`。 削除中`/old/x`まず必要なリンクが不可能になります。 

ネストされたディレクトリでは、同様の順序制約が作成されます。 のために```
2 2
/old/
/old/x/
/new/
/new/x/
```正しい最小値は 2 つの操作です。`mkdir /new/`の場合のみすでに十分です`/new/x/`は、2 番目の新しいディレクトリとして直接表されます。 より正確には、指定された 4 つのエントリを使用すると、`/old/`そして`/new/`ディレクトリはこれらのみであるため、出力は次のようになります。`mkdir /new/`に続く`rmdir /old/`。 コンテンツを処理する前に古いディレクトリを削除する不注意な実装は、次のようなより深いバージョンで失敗する可能性があります。```
3 3
/old/
/old/x/
/old/x/f
/new/
/new/x/
/new/x/g
```古いサブツリーを下から上に空にする必要があります。 

3 番目のエッジ ケースは、同じハッシュを持つ複数のファイルです。 仮定する```
2 2
/a h
/b h
/c h
/d h
```単一の元のファイルを両方の新しいハードリンクのソースとして使用できます。 最小値は 4 つの操作、2 つの操作です`link`操作とその2`unlink`操作。 古いファイルと新しいファイルの間で 1 対 1 の一致を検索する必要はありません。 

最後に、同じハッシュを持つ別のファイルが移動されている場合でも、変更されていないファイルには触れてはなりません。 のために```
1 1
/a h
/a h
```答えは単純です`0`。 共通名はすでに正しいハードリンクを示しており、そのハッシュは一致することが保証されています。 

## アプローチ

 直接的な解決策では、1 つのスナップショットで他のスナップショット内のすべてのオブジェクトを繰り返し検索できます。 A のすべてのパスについて、B をスキャンしてそれが残っているかどうかを判断し、ファイル ハッシュを照合するために別のスキャンを実行できます。 これは正しいです。必要な操作はすべて、対応するオブジェクトを明示的に見つけた後に導出できるためです。ただし、最初の比較段階だけで必要になる場合があります。`n * m = 10^8`両方のスナップショットに次のものが含まれる場合のパス比較`10^4`オブジェクト。 最大 256 文字のパスを使用すると、必要以上に多くの作業が必要になります。 

オブジェクトの ID はその完全なパスであるため、ブルート フォース アプローチが機能します。 有益な観察は、パスがすでに一意であり、このステートメントにより、あるスナップショット内のファイルが他のスナップショット内のディレクトリと同じパスを持たないことが保証されるということです。 すべてのオブジェクトをハッシュ テーブルに入れ、正確なパスによってすぐに分類できます。 

パスが分類されると、ファイル操作の数は固定されます。 A にのみ存在するすべてのファイルは最終的に削除する必要があるため、そのようなファイルごとに少なくとも 1 つのコストがかかります。`unlink`。 B にのみ存在するすべてのファイルはハードリンクとして作成する必要があるため、各ファイルには少なくとも 1 つのコストがかかります。`link`。 適切なソースがまだ存在する間に必要なターゲット ハードリンクをすべて作成し、古いファイル名をすべて削除するという 2 つの操作で常に十分です。 

ハッシュは、新しいハードリンクのソースを選択する場合にのみ必要です。 ハッシュごとに、そのハッシュを持つ A からの 1 つのファイルを記憶します。 変更されていないファイルでハッシュが発生した場合、そのファイルをソースとして優先すると、そのファイルは削除されないため便利です。 それ以外の場合は、そのハッシュを持つ A-only ファイルが一時ソースとして機能します。 A-only ファイルを削除する前にすべての新しいハードリンクを作成するため、削除が予定されているソースであっても、十分な期間利用可能な状態が維持されます。 

ディレクトリには同じ固定の下限があります。 B にのみ存在するすべてのディレクトリには 1 つが必要です`mkdir`、A にのみ存在するすべてのディレクトリには 1 つが必要です`rmdir`。 唯一の難点は注文です。 新しいディレクトリは浅いものから深いものに作成する必要があり、古いディレクトリは深いものから浅いものに削除する必要があります。 すべての新しいディレクトリが存在すると、すべてのターゲット ファイルには有効な親があり、すべての古いファイルが削除されると、最終的にはすべての古いディレクトリが空になる可能性があります。 

したがって、操作の最小数は、A と B の間でパスが異なるオブジェクトの数と同じになります。アルゴリズムは、これらの避けられない操作の正当な順序を見つけるだけで済みます。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nm・L) | O(n + m) | 遅すぎる |
 | 最適 | O((n + m) log(n + m) · L) | O(n + m) | 承認済み |

 ここ`L`は最大パス長で、最大 256 です。パス長が制限されている場合、これは事実上`O((n + m) log(n + m))`。 

## アルゴリズムのチュートリアル

 1. A と B のすべてのオブジェクトを読み取り、ディレクトリからファイルを分離します。 ファイルを次のように保存します`path -> hash`パスのセットとしてのディレクトリ。 同時に、ハッシュごとに 1 つの A ファイルを覚えておいてください。 
2. 次のコマンドを実行して、作成する必要があるディレクトリを見つけます。`B_dirs - A_dirs`。 ディレクトリの深さを増やすことによってそれらを並べ替え、パスを二次キーとして使用します。 親はその子の前に存在する必要があるため、新しく作成されたすべてのディレクトリは、その操作が発行されるときに有効な親を持つことになります。 
3. 次のコマンドを実行して、削除する必要があるディレクトリを見つけます。`A_dirs - B_dirs`。 深さを減らして並べ替えます。 親が空になる前に子が消える必要があるため、この順序ではすべての`rmdir`法律上の。 
4. B でのみ発生するファイルを検索します。そのようなファイルごとに、A から構築されたソース マップでそのハッシュを検索し、`link source target`手術。 すべてのターゲット ディレクトリはすでに作成されており、A のすべてのソース ファイルはまだ存在しています。`unlink`まだ起こってしまった。 
5. A でのみ発生するファイルを検索し、`unlink`それぞれの操作。 この時点で、すべての新しいハードリンクが作成されているため、ソースとして使用されていた古いファイルであっても安全に削除できます。 
6. を出力します。`rmdir`古い専用ディレクトリに対する操作を深さの降順で実行します。 古いファイルはすべてすでに消えているため、ディレクトリは空になる可能性があります。 
7. 発行された操作の合計数とその後に操作自体を出力します。 共通ファイルと共通ディレクトリは、すでに必要な状態を正確に備えているため、表示されることはありません。 

### なぜ効果があるのか

 すべてのパスを独立して検討してください。 共通パスのオブジェクト タイプは両方のスナップショットで同じであり、共通ファイルのハッシュは同じであるため、それにアクセスしても操作の数をゼロ未満に減らすことはできません。 B にのみ存在するファイルには、少なくとも 1 つのファイルが必要です`link`ただし、A にのみ存在するファイルには少なくとも 1 つのファイルが必要です。`unlink`。 私たちのアルゴリズムはこれらの操作を正確に実行し、考えられるソースを削除する前にすべての新しいリンクを作成するため、それらはすべて合法です。 

同じ議論がディレクトリにも当てはまります。 すべての B 専用ディレクトリには 1 つ必要です`mkdir`、すべての A-only ディレクトリには 1 つ必要です`rmdir`。 作成を深さによってソートすると、親が最初に存在することが保証されます。 逆の深さで削除をソートすると、子が最初に消えることが保証されます。 したがって、発行されたすべての操作は正当であり、操作の数が避けられない下限に達し、シーケンスが最小限になります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n, m = map(int, input().split())

    a_files = {}
    a_dirs = set()

    for _ in range(n):
        parts = input().split()
        path = parts[0]

        if path.endswith('/'):
            a_dirs.add(path)
        else:
            a_files[path] = parts[1]

    b_files = {}
    b_dirs = set()

    for _ in range(m):
        parts = input().split()
        path = parts[0]

        if path.endswith('/'):
            b_dirs.add(path)
        else:
            b_files[path] = parts[1]

    common_files = a_files.keys() & b_files.keys()

    source_by_hash = {}

    # Prefer files that survive in B as sources.
    for path in common_files:
        source_by_hash[a_files[path]] = path

    # If a hash has no surviving source, an obsolete A-file can be used
    # until all required links have been created.
    for path, h in a_files.items():
        if h not in source_by_hash:
            source_by_hash[h] = path

    add_dirs = sorted(
        b_dirs - a_dirs,
        key=lambda p: (p.count('/'), p)
    )

    remove_dirs = sorted(
        a_dirs - b_dirs,
        key=lambda p: (-p.count('/'), p)
    )

    add_files = sorted(
        b_files.keys() - a_files.keys()
    )

    remove_files = sorted(
        a_files.keys() - b_files.keys()
    )

    operations = []

    for path in add_dirs:
        operations.append(f"mkdir {path}")

    for target in add_files:
        source = source_by_hash[b_files[target]]
        operations.append(f"link {source} {target}")

    for path in remove_files:
        operations.append(f"unlink {path}")

    for path in remove_dirs:
        operations.append(f"rmdir {path}")

    out = [str(len(operations))]
    out.extend(operations)
    sys.stdout.write('\n'.join(out) + '\n')

if __name__ == "__main__":
    solve()
```最初の 2 つのディクショナリとセットは、2 つのスナップショットを直接表します。 完全なパスをキーとして使用すると、オブジェクトが共通であるかどうかのテストが平均的な O(1) 操作で行われます。 

の`source_by_hash`マップはハードリンクに必要な情報のみを取得します。 共通ファイルは最後まで存続するため、ソースとして推奨されます。 必要なハッシュを持つ共通ファイルがない場合は、A-only ファイルがソースになります。 このようなファイルは、新しいハードリンクが作成されるたびに意図的に存続させられます。 

ディレクトリの並べ替えには、`path.count('/')`深さの目安として。 すべてのディレクトリ パスは次で終わるため、`/`、子のスラッシュ数は常に親よりも多くなります。 正確な数値の深さは関係なく、順序だけが重要です。 

操作グループは固定された順序で発行されます。 リンクとネストされたディレクトリはディレクトリに依存する可能性があるため、ディレクトリの作成が最初に行われます。 古いソースが必要になる可能性があるため、リンクはリンク解除の前に置かれます。 リンクを解除すると前に来ます`rmdir`削除する前にディレクトリが空である必要があるためです。 

Python には整数オーバーフローの問題はなく、操作の最大数は最大でも`2(n + m)`、指定された制限では 40000 未満です。 出力自体はリストに保存されるため、操作の前に操作カウントを印刷できます。 

## 実用的な例

 提供されたサンプルの場合、共通ディレクトリは次のとおりです。`/a/`。 ディレクトリ`/a/e/`作成する必要があり、`/f/`消えなければなりません。 ファイル`/a/b.txt`新しいファイルの利用可能なソースです`/a/e/c.txt`、 その間`/a/d.txt`は時代遅れです。 

| ステップ | 操作 | 新しいディレクトリ | 新しいファイル | 残っている古いファイル |
 | --- | --- | --- | --- | --- |
 | 1 |`mkdir /a/e/`|`/a/e/`存在します | なし |`/a/b.txt`、`/a/d.txt`|
 | 2 |`link /a/b.txt /a/e/c.txt`|`/a/e/`存在します |`/a/e/c.txt`存在します |`/a/b.txt`、`/a/d.txt`|
 | 3 |`unlink /a/b.txt`| 変更なし |`/a/e/c.txt`存在します |`/a/d.txt`|
 | 4 |`unlink /a/d.txt`| 変更なし |`/a/e/c.txt`存在します | なし |
 | 5 |`rmdir /f/`| 最終ディレクトリは残ります | 最終ファイルが残ります | なし |

 結果として得られるシーケンスには、サンプル出力と同じ最小数の 5 つの演算が含まれます。 この順序はステートメントの例とは異なりますが、最小の有効なシーケンスが受け入れられるため許可されます。 

2 番目の例として、2 つのネストされたディレクトリ ツリー間でファイルを移動することを考えてみましょう。```
6 6
/old/
/old/sub/
/old/sub/file h
/new/
/new/sub/
/new/sub/file2 h
```2 つのディレクトリ ツリーには共通のディレクトリはなく、唯一のファイルの名前が変更されます。 

| ステップ | 操作 | 作成されたディレクトリ | 既存のソース | 古いファイル |
 | --- | --- | --- | --- | --- |
 | 1 |`mkdir /new/`|`/new/`|`/old/sub/file`| なし |
 | 2 |`mkdir /new/sub/`|`/new/`、`/new/sub/`|`/old/sub/file`| なし |
 | 3 |`link /old/sub/file /new/sub/file2`| 両方とも新しいディレクトリです。`/old/sub/file`| なし |
 | 4 |`unlink /old/sub/file`| 変更なし | 何も必要ありません | なし |
 | 5 |`rmdir /old/sub/`| 変更なし | なし | 古いサブツリーが部分的に削除されました。 
| 6 |`rmdir /old/`| 最後のツリーに到達しました | なし | 古いサブツリーがなくなった |

 この例では、両方の深さの順序を示しています。`/new/`先行する必要があります`/new/sub/`、 その間`/old/sub/`先行する必要があります`/old/`削除シーケンスで。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O((n + m) log(n + m) · L) | ハッシュ テーブルの分類は平均して線形であり、ディレクトリ/ファイルのソートが優勢です。 
| スペース | O(n + m) | すべてのパス、ハッシュ、生成された操作は保存されます。 

ここ`L <= 256`は最大パス長です。 せいぜい`10^4`各スナップショット内のオブジェクト、最大でソート`2 * 10^4`短い文字列は簡単に制限内に収まり、生成される操作数は以下になります`4 * 10^4`。 

## テストケース```python
import sys
import io

def solve():
    input = sys.stdin.readline

    n, m = map(int, input().split())

    a_files = {}
    a_dirs = set()

    for _ in range(n):
        parts = input().split()
        path = parts[0]
        if path.endswith('/'):
            a_dirs.add(path)
        else:
            a_files[path] = parts[1]

    b_files = {}
    b_dirs = set()

    for _ in range(m):
        parts = input().split()
        path = parts[0]
        if path.endswith('/'):
            b_dirs.add(path)
        else:
            b_files[path] = parts[1]

    source_by_hash = {}

    for path in a_files.keys() & b_files.keys():
        source_by_hash[a_files[path]] = path

    for path, h in a_files.items():
        if h not in source_by_hash:
            source_by_hash[h] = path

    add_dirs = sorted(
        b_dirs - a_dirs,
        key=lambda p: (p.count('/'), p)
    )
    remove_dirs = sorted(
        a_dirs - b_dirs,
        key=lambda p: (-p.count('/'), p)
    )
    add_files = sorted(b_files.keys() - a_files.keys())
    remove_files = sorted(a_files.keys() - b_files.keys())

    operations = []

    for path in add_dirs:
        operations.append(f"mkdir {path}")

    for target in add_files:
        operations.append(
            f"link {source_by_hash[b_files[target]]} {target}"
        )

    for path in remove_files:
        operations.append(f"unlink {path}")

    for path in remove_dirs:
        operations.append(f"rmdir {path}")

    sys.stdout.write(
        str(len(operations)) + '\n' +
        '\n'.join(operations) +
        ('\n' if operations else '')
    )

def run(inp: str) -> str:
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

sample1 = """\
4 3
/a/
/a/b.txt hash1
/a/d.txt hash2
/f/
/a/
/a/e/
/a/e/c.txt hash1
"""

expected1 = """\
5
mkdir /a/e/
link /a/b.txt /a/e/c.txt
unlink /a/b.txt
unlink /a/d.txt
rmdir /f/
"""

assert run(sample1) == expected1, "provided sample"

assert run("0 0\n") == "0\n", "minimum-size empty snapshots"

sample2 = """\
4 4
/a h
/b h
/c/
c
"""

# The previous test deliberately is not valid path input, so use a valid
# all-equal-hash case instead.
sample2 = """\
2 2
/a h
/b h
/c h
/d h
"""

expected2 = """\
4
link /a /c
link /a /d
unlink /a
unlink /b
"""

assert run(sample2) == expected2, "all equal hashes"

sample3 = """\
3 3
/old/
/old/sub/
/old/sub/file h
/new/
/new/sub/
/new/sub/file2 h
"""

expected3 = """\
6
mkdir /new/
mkdir /new/sub/
link /old/sub/file /new/sub/file2
unlink /old/sub/file
rmdir /old/sub/
rmdir /old/
"""

assert run(sample3) == expected3, "nested directory ordering"

deep_name = "/" + "a/" * 126
deep_file_a = deep_name + "x"
deep_file_b = deep_name + "y"

sample4 = (
    "1 1\n"
    + deep_file_a + " h\n"
    + deep_file_b + " h\n"
)

out4 = run(sample4).splitlines()
assert out4[0] == "2", "deep path operation count"
assert out4[1] == f"link {deep_file_a} {deep_file_b}"
assert out4[2] == f"unlink {deep_file_a}"

# Maximum-size test: 10000 old files and 10000 new files,
# all having the same hash.
old_files = [f"/a{i}" for i in range(10000)]
new_files = [f"/b{i}" for i in range(10000)]

max_input = (
    "10000 10000\n"
    + ''.join(path + " h\n" for path in old_files)
    + ''.join(path + " h\n" for path in new_files)
)

max_out = run(max_input).splitlines()

assert max_out[0] == "20000", "maximum-size operation count"
assert len(max_out) == 20001, "maximum-size output length"

link_count = sum(line.startswith("link ") for line in max_out[1:])
unlink_count = sum(line.startswith("unlink ") for line in max_out[1:])

assert link_count == 10000, "maximum-size link count"
assert unlink_count == 10000, "maximum-size unlink count"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`0 0`|`0`| 最小サイズの入力と何も変化しない場合 |
 | ハッシュ付きの 2 つの古いファイルと 2 つの新しいファイル`h`|`4`オペレーション | 1 つのソースを複数のハードリンクに再利用し、そのリンク解除を遅らせる |
 | 入れ子になった`/old/sub/`に`/new/sub/`|`6`オペレーション | 親第一主義`mkdir`そして子供第一`rmdir`|
 | 256 文字の制限に近いファイル パス |`2`オペレーション | パス長境界と正確なパスの処理 |
 | 同じハッシュを持つ 10000 個の古いファイルと 10000 個の新しいファイル |`20000`オペレーション | 最大入力サイズ、すべて等しいハッシュ、およびスケーラビリティ |

 ## 特殊なケース

 空のケースは特別な操作を必要としません。 のために```
0 0
```ディレクトリとファイルの両方のセットが空であるため、4 つの差分セットはすべて空です。 アルゴリズムは操作を発行せず、出力します。`0`。 

ソース ファイルが削除されるディレクトリ内にある場合、古いディレクトリを削除する前にファイルをリンクする必要があります。 のために```
3 3
/old/
/old/sub/
/old/sub/file h
/new/
/new/sub/
/new/sub/file2 h
```アルゴリズムが最初に作成する`/new/`そして`/new/sub/`、次に作成します`/new/sub/file2`から`/old/sub/file`、その後にのみ古いファイルを削除します。 逆の深さのディレクトリ順序により、`/old/sub/`前に`/old/`。 6 つの操作は、まさに 4 つの避けられないディレクトリ変更と 1 つの操作です。`link`そして1つ`unlink`。 

同じハッシュを持つ複数の新しいファイルには、複数の独立したオリジナル ソースは必要ありません。 のために```
2 2
/a h
/b h
/c h
/d h
```ソースマップが選択します`/a`ハッシュ用`h`。 アルゴリズムは両方を作成します`/c`そして`/d`へのハードリンクとして`/a`、その後削除します`/a`そして`/b`。 出力は 4 つの演算です。 という事実`/a`それを使用するすべてのリンクはすでに作成されているため、最終的には削除されますが、問題はありません。 

未変更のファイルは、両方のファイル差分セットから除外する必要があります。 のために```
1 1
/a h
/a h
```

`/a`両方の辞書で同じハッシュが出現するため、どちらも`link`または`unlink`が生成されます。 出力は`0`。 これが、ハッシュだけを比較するのが正しくない理由です。ファイル名が望ましい状態で既に存在するかどうかは、パス自体によって決まります。 

最も深い有効なパスは再帰なしで処理されます。 実装では次の数を使用します。`/`文字数はディレクトリ操作のソートにのみ使用されるため、256 文字の制限に近いパスでも通常の文字列として処理されます。 親のスラッシュは子孫よりも少ないため、必要な作成順序と削除順序を確立するにはこれで十分です。
