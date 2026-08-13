---
title: "CF 102426A - \u81ea\u7136\u8bed\u8a00\u5904\u7406"
description: "各テキストはすでに周波数ベクトルに変換されています。 したがって、文字列処理部分は完全になくなります。 1 つのテスト ケースでは、それぞれが (m) 個の整数座標を持つ (n) 個のベクトルの集合を調べて、それらのベクトルが線形依存しているかどうかを判断するだけで済みます。"
date: "2026-08-12T19:20:28+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102426
codeforces_index: "A"
codeforces_contest_name: "The 7-th BIT Campus Programming Contest for Junior Grade Group"
rating: 0
weight: 102426
solve_time_s: 336
verified: true
draft: false
---

[CF 102426A - \u81ea\u7136\u8bed\u8a00\u5904\u7406](https://codeforces.com/problemset/problem/102426/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 5 分 36 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各テキストはすでに周波数ベクトルに変換されています。 したがって、文字列処理部分は完全になくなります。 1 つのテスト ケースでは、それぞれが (m) 個の整数座標を持つ (n) 個のベクトルの集合を調べて、それらのベクトルが線形依存しているかどうかを判断するだけで済みます。 

この質問は、すべてがゼロではなく、次のような係数 (c_1,c_2,\ldots,c_n) が存在するかどうかを尋ねることと同じです。 

[
 c_1A_1+c_2A_2+\cdots+c_nA_n=0。 
]

 このような係数が存在する場合、ベクトルは線形依存しており、答えは次のようになります。`YES`。 それ以外の場合、それらは線形独立であり、答えは次のようになります。`NO`。 

寸法は非常に小さいです。 ベクトルは最大 10 個あり、各ベクトルには最大 4 つの座標があります。 これにより、有用な必要条件がすぐに得られます。(m) 次元のベクトル空間では、(m) 個以下のベクトルが線形独立であることができます。 したがって、(n>m) の場合、答えは自動的に次のようになります。`YES`。 

(n\le m) の場合でも、ベクトルが実際にフルランクであるかどうかを判断する必要があります。 (m\le4) なので、ガウス消去法は十分すぎるほど高速です。 ケースごとの入力数は最大 (10\times4=40) であるため、漸近複雑度がかなり劣るアルゴリズムでも、指定された制限に適合します。 小さな境界は実装の選択には役立ちますが、行列のランクを計算する必要があるという根本的な数学的問題は変わりません。 

慎重に扱う価値のある入力形式の詳細が 2 つあります。 まず、ゼロベクトルはすぐにベクトルグループを線形依存させます。 例えば、```
1 2
0 0
```答えがあります`YES`ゼロ ベクトルの係数は 1 として選択できるため、重複する行のみを検索するランクの実装では、独立性が誤って報告される可能性があります。 

第 2 に、重複ベクトルまたは比例ベクトルも依存関係を作成します。 例えば、```
2 2
1 2
2 4
```答えがあります`YES`2 番目のベクトルは最初のベクトルの 2 倍であるためです。 行が単に異なっているかどうかを確認するだけでは十分ではありません。 重要なのは、ある行を他の行の線形結合として表現できるかどうかです。 

指定されたステートメントでは、最初の入力値が (T) であると示されていますが、表示されるサンプルは直接次から始まります。`n m`。 この 2 つの部分は矛盾しています。 以下のソリューションは、正式なマルチテストケース形式を受け入れ、表示されるサンプル形式も認識するため、アルゴリズム自体はこの形式の不一致の影響を受けません。 

## アプローチ

 直接的な総当りのアイデアは、ベクトルの空でないサブセットをすべて列挙し、そのサブセットが線形依存しているかどうかをテストすることです。 いずれかのサブセットが依存している場合、ベクトル グループ全体が依存しています。 すべてのサブセットについて、その行に対してガウス消去法を実行できます。 

ベクトル ファミリは、空ではない線形依存サブファミリーが含まれている場合に正確に線形依存するため、このアプローチは正しいです。 (2^n-1) 個の空ではないサブセットとランク計算コスト (O(m^3)) があるため、最悪の場合の複雑さは (O(2^n m^3)) です。 実際の最大値 (n=10) と (m=4) を使用すると、これはテスト ケースごとにせいぜいおおよそ (1024\cdot64=65536) 個の基本的な消去スケール操作であるため、この総当りアプローチでも問題なく通過できます。 

このアプローチの問題は、指定された制限ではなく、(n) に対する不必要な指数依存性です。 同じタスクで数千または数十万のベクトルが許可される場合、サブセットの列挙は直ちに不可能になります。 線形依存の構造により、より明確なルートが得られます。すべてのベクトルを 1 つの行列に配置でき、独立したベクトルの数が正確に行列のランクになります。 

重要な観察は、ガウス消去法によってこのランクが直接計算されるということです。 成功した各ピボットは 1 つの独立した方向を識別します。 正確に (n) 個のピボットが見つかった場合、(n) 個のベクトルのすべてが新しい独立した方向に寄与するため、ベクトルは独立しています。 存在するピボットの数が (n) より少ない場合、一部のベクトルは以前の独立した方向の組み合わせであるため、ファミリは依存します。 

(m) は列の数であるため、ランクが (m) を超えることはありません。 これは、即時 (n>m) の場合も説明します。(m) 列のみに (n) 個のピボットが存在する可能性はありません。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(2^n m^3)) | (O(nm)) | ここでは受け入れられますが、不必要に指数関数的になります。 
| 最適 | (O(nm^2)) | (O(nm)) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. (n)、(m)、および (n) ベクトルを読み取り、ベクトルを (n\times m) 行列の行として扱います。 これにより、元の質問が直接行列ランク問題に変わります。 
2. (n>m) の場合は、すぐに出力します。`YES`。 (m) 次元のベクトル空間には、(m) 個を超える線形独立ベクトルを含めることはできないため、依存性が保証されます。 
3. 現在のピボット行を維持します。 最初は行 0 です。列ごとに、残りの行からこの列の値が 0 以外の行を検索します。 そのような行が存在しない場合、この列は別の独立した方向を提供できないため、次の列に移動します。 
4. ゼロ以外のピボットが見つかったら、その行を現在のピボット位置に交換します。 ピボット行をピボット値で除算して、ピボットを 1 にします。ここでは、結果が浮動小数点の精度に依存しないように、正確な有理演算が使用されています。 
5. 現在のピボット列を 1 行おきの行から削除します。 消去後、以前に処理されたすべてのピボット列のピボット行の外側にはゼロが含まれます。 これにより、成功したすべてのピボットが 1 つの独立したディメンションに対応する行エシュロン スタイルの表現が得られます。 
6. ランクを上げ、ピボット行を次の位置に移動します。 すべての列が処理されると、成功したピボットの数が行列のランクとなります。 
7. ランクを (n) と比較します。 もし`rank == n`、すべて (n) のベクトルは線形独立であるため、出力`NO`。 それ以外の場合、ランクはベクトルの数より小さいため、出力`YES`。 

### なぜ効果があるのか

 中心的な不変条件は、ピボットが成功するたびに、処理されたピボット行が相互に独立した方向を表し、処理されたすべての列が他の行から削除されることです。 新しいピボットは、前のピボット行では生成できない情報を含む行がまだ存在する場合にのみ存在できます。 

その結果、ピボットごとにランクが 1 つずつ増加します。 ガウス消去は、入力ベクトルのスパンの次元と正確に同じ数のピボットで終了します。 サイズ (n) のベクトル ファミリは、そのスパンの次元が (n) である場合、正確に線形独立です。`rank == n`まさに必要な条件です`NO`答え。 

## Python ソリューション```python
import sys
from fractions import Fraction

input = sys.stdin.readline

def independent(vectors, m):
    n = len(vectors)

    if n > m:
        return False

    a = [[Fraction(x) for x in row] for row in vectors]

    rank = 0

    for col in range(m):
        pivot = -1

        for row in range(rank, n):
            if a[row][col] != 0:
                pivot = row
                break

        if pivot == -1:
            continue

        a[rank], a[pivot] = a[pivot], a[rank]

        pivot_value = a[rank][col]
        for j in range(col, m):
            a[rank][j] /= pivot_value

        for row in range(n):
            if row == rank:
                continue

            factor = a[row][col]
            if factor == 0:
                continue

            for j in range(col, m):
                a[row][j] -= factor * a[rank][j]

        rank += 1

        if rank == n:
            return True

    return False

def solve():
    data = sys.stdin.buffer.read().split()
    if not data:
        return

    # The formal statement uses T test cases.
    # The displayed sample omits T and starts directly with n m.
    # Detect both formats from the first input line.
    lines = sys.stdin.buffer.read().splitlines()

    # Re-read using the raw data above if possible.
    # For the formal format, the first line contains only T.
    first_line = lines[0].split()

    if len(first_line) == 1:
        t = int(first_line[0])
        pos = 1

        answers = []

        for _ in range(t):
            n = int(data[pos])
            m = int(data[pos + 1])
            pos += 2

            vectors = []
            for _ in range(n):
                vectors.append(list(map(int, data[pos:pos + m])))
                pos += m

            answers.append("NO" if independent(vectors, m) else "YES")

        sys.stdout.write("\n".join(answers))
    else:
        # Format used by the displayed sample: n m followed by n vectors.
        pos = 0
        n = int(data[pos])
        m = int(data[pos + 1])
        pos += 2

        vectors = []
        for _ in range(n):
            vectors.append(list(map(int, data[pos:pos + m])))
            pos += m

        sys.stdout.write("NO\n" if independent(vectors, m) else "YES\n")

if __name__ == "__main__":
    solve()
```の`independent`関数は最初に次元引数を処理します。 (n>m) の場合、戻り値`False`なぜならベクトルは独立できないからです。 これは、チュートリアルで使用したものと同じ数学的ショートカットです。 

マトリックスは次のように変換されます。`Fraction`消去前の値。 入力は整数のみで構成されますが、ガウス消去時の除算により有理値を作成できます。 通常、浮動小数点数の使用はこれらの小さな境界に対して機能しますが、正確な算術演算により依存性テストが数学的に堅牢になり、値がゼロかどうかを判断するために任意のイプシロンを選択する必要がなくなります。 

外側のループは、各列を可能なピボット位置として処理します。 検索は次から始まります`rank`これは、その位置より上の行にすでに確立されたピボットが含まれているためです。 ゼロ以外の値が見つかると、対応する行が所定の位置に交換されます。 

ピボット行は、ピボットが 1 に等しくなるように正規化されます。その後、実装により、1 行おきの行からピボット列が削除されます。 ピボットの上下の両方を削除するのは、通常の行階層形式に必要な最小限の作業よりもわずかに多くの作業が必要ですが、これにより行列が縮小された形式に保たれ、ランクが不変になることが特に簡単になります。 

初期の`rank == n`ランクが上がるだけなので復帰も安心です。 すでに (n) 個のピボットが存在すると、すべての (n) ベクトルは独立し、後の列でその結論を変更することはできません。 

(T) が存在するかどうかに関して、提供されたステートメントと表示されたサンプルが一致しないため、パーサーには小さな互換性レイヤーが含まれています。 最初の行に 1 つの整数が含まれている場合、それは (T) として扱われます。 2 つの整数が含まれている場合、それらは (n,m) として扱われ、表示されているサンプルと一致します。 実際の線形代数はどちらの形式でも同じです。 

## 実用的な例

 ### サンプル 1

 表示されたサンプルには 2 つのベクトルが含まれています。```
2 2
1 1
0 1
```マトリックスは

 [
 \begin{p行列}
 1&1\
 0&1
 \end{p行列}。 
]

 消去は次のように進みます。 

| コラム | ピボット行 | マトリックスの状態 | ランク |
 | --- | --- | --- | --- |
 | 0 | 0 | (\begin{pmatrix}1&1\0&1\end{pmatrix}) | 1 |
 | 1 | 1 | (\begin{pmatrix}1&0\0&1\end{pmatrix}) | 2 |

 最終的なランクは 2 で、これはベクトルの数と同じです。 したがって、ベクトルは線形独立であり、答えは次のようになります。`NO`。 

この例は、基本的な不変条件を示しています。成功したすべてのピボットは 1 つの独立した方向に寄与します。 2 番目のベクトルは最初のベクトルの倍数ではないため、2 番目のピボットが存在します。 

### サンプル 2

 2 つの比例ベクトルを考えてみましょう。```
1
2 2
1 2
2 4
```マトリックスは

 [
 \begin{p行列}
 1&2\
 2&4
 \end{p行列}。 
]

 | コラム | ピボット行 | マトリックスの状態 | ランク |
 | --- | --- | --- | --- |
 | 0 | 0 | (\begin{pmatrix}1&2\2&4\end{pmatrix}) | 1 |
 | 1 | なし | (\begin{pmatrix}1&2\0&0\end{pmatrix}) | 1 |

 ピボットが 1 つだけ見つかります。 2 行目は最初の行の 2 倍であるため、ゼロになります。 したがって、ランクは 1 で (n=2) より小さいため、答えは次のようになります。`YES`。 

このトレースは、ベクトルが別個であるものの線形に依存している場合を例にしています。 2 つの行が等しいかどうかを単にチェックするだけではこの例は見逃されますが、rank は即座にそれを検出します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(nm^2)) | 最大 (m) 個のピボット列が処理され、ピボット タッチを削除すると (O(nm)) 個の行列エントリが処理されます。 
| スペース | (O(nm)) | 行列にはすべて (n) 個のベクトルが格納されます。 

(n\le10) と (m\le4) の場合、行列には​​最大 40 個のエントリが含まれます。 正確でも`Fraction`演算はこれらの制限に対して十分に高速であり、メモリ使用量は 64 MB の制限に比べて無視できます。 

漸近限界は、これらの小さな制約を超えても適切です。 このソリューションでは、サブセットの列挙を回避し、1 回のエリミネーション パスでランク全体を計算します。 

## テストケース```python
import sys
import io
from fractions import Fraction

def independent(vectors, m):
    n = len(vectors)

    if n > m:
        return False

    a = [[Fraction(x) for x in row] for row in vectors]
    rank = 0

    for col in range(m):
        pivot = -1

        for row in range(rank, n):
            if a[row][col] != 0:
                pivot = row
                break

        if pivot == -1:
            continue

        a[rank], a[pivot] = a[pivot], a[rank]

        p = a[rank][col]
        for j in range(col, m):
            a[rank][j] /= p

        for row in range(n):
            if row == rank:
                continue

            factor = a[row][col]
            if factor == 0:
                continue

            for j in range(col, m):
                a[row][j] -= factor * a[rank][j]

        rank += 1

        if rank == n:
            return True

    return False

def run(inp: str) -> str:
    lines = inp.strip().splitlines()
    data = inp.split()

    if not data:
        return ""

    first_line = lines[0].split()

    if len(first_line) == 1:
        t = int(first_line[0])
        pos = 1
        out = []

        for _ in range(t):
            n = int(data[pos])
            m = int(data[pos + 1])
            pos += 2

            vectors = []
            for _ in range(n):
                vectors.append(list(map(int, data[pos:pos + m])))
                pos += m

            out.append("NO" if independent(vectors, m) else "YES")

        return "\n".join(out) + "\n"

    n = int(data[0])
    m = int(data[1])
    pos = 2

    vectors = []
    for _ in range(n):
        vectors.append(list(map(int, data[pos:pos + m])))
        pos += m

    return ("NO\n" if independent(vectors, m) else "YES\n")

# Provided sample, whose displayed format omits T.
assert run("""\
2 2
1 1
0 1
""") == "NO\n", "sample 1"

# Minimum-size case: one nonzero vector is independent.
assert run("""\
1
1 1
7
""") == "NO\n", "minimum nonzero vector"

# Zero vector is always dependent.
assert run("""\
1
1 3
0 0 0
""") == "YES\n", "zero vector"

# Two proportional vectors are dependent.
assert run("""\
1
2 2
1 2
2 4
""") == "YES\n", "proportional vectors"

# Maximum dimensions, with four independent vectors.
assert run("""\
1
4 4
1 0 0 0
0 1 0 0
0 0 1 0
0 0 0 100
""") == "NO\n", "maximum-size independent case"

# More vectors than dimensions must be dependent.
assert run("""\
1
5 4
1 0 0 0
0 1 0 0
0 0 1 0
0 0 0 1
1 1 1 1
""") == "YES\n", "n greater than m"

# Several test cases in the formal format.
assert run("""\
3
2 2
1 1
0 1
2 2
1 2
2 4
3 2
1 0
0 1
1 1
""") == "NO\nYES\nYES\n", "multiple test cases"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / 1 1 / 7`|`NO`| 1 つの非ゼロベクトルを持つ最小サイズのケース |
 |`1 / 1 3 / 0 0 0`|`YES`| ゼロベクトル |
 |`1 / 2 2 / 1 2 / 2 4`|`YES`| 比例ベクトル |
 |`1 / 4 4 / ...`|`NO`| 4 つの独立ベクトルによる最大次元 |
 |`1 / 5 4 / ...`|`YES`| 境界条件 (n>m) |
 | 3 つの正式なテスト ケース |`NO YES YES`| 複数のテストケースの解析と混合依存の結果 |

 ## 特殊なケース

 最初のエッジ ケースはゼロ ベクトルです。 考慮する```
1
1 3
0 0 0
```アルゴリズムはランク 0 から始まります。列 0 にはゼロ以外のエントリはなく、列 1 と 2 でも同じことが起こります。ピボットが見つからないため、最終ランクは 0 のままです。(0<1) であるため、ベクトルは依存しており、出力は次のようになります。`YES`。 ガウス消去法では自然にゼロ行がランクに寄与しないものとして扱われるため、これは特別なゼロベクトル チェックを行わなくても機能します。 

2 番目のエッジ ケースは、異なるが比例するベクトルのペアです。```
1
2 2
1 2
2 4
```最初の行は列 0 にピボットを提供し、ランクを 1 に増やします。2 番目の行から列 0 を削除すると、次のようになります。`(2, 4)`に`(0, 0)`。 2 番目の列にはゼロ以外の候補が残っていないため、ランクは 1 のままです。ベクトルは 2 つありますが、独立した方向は 1 つだけであるため、答えは次のようになります。`YES`。 

3 番目のエッジ ケースは、座標よりもベクトルの方が多い場合に発生します。```
1
5 4
1 0 0 0
0 1 0 0
0 0 1 0
0 0 0 1
1 1 1 1
```アルゴリズムは戻ります`YES`(5>4) なのですぐに。 排除する必要はありません。 使用できる座標方向は 4 つだけなので、5 つのベクトルを独立させることはできません。 

4 番目のエッジ ケースはフルランク正方行列です。```
1
4 4
1 0 0 0
0 1 0 0
0 0 1 0
0 0 0 100
```このアルゴリズムは、各列で 1 つのピボットを見つけます。 ランクはベクトルの数に等しい 4 に達するため、次の値が返されます。`NO`。 値 100 では、消去では正確な有理演算が使用されるため、特別な処理は行われません。 

これらのケースでは、表面的な解決策が失敗する可能性のある主な方法を取り上げています。個別のベクトルを独立したベクトルと混同する、ゼロ ベクトルを見落とす、(n>m) 次元の境界を忘れる、正確なゼロ検出を考慮せずに近似算術を使用するなどです。
