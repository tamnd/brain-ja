---
title: "CF 102280F - \u041d\u0435\u043e\u0436\u0438\u0434\u0430\u043d\u043d\u0430\u044f \u0437\u0438\u043c\u0430"
description: "ノートの各行にはドライバーの姓のみが含まれています。 ドライバーは車庫から出るときと戻るときに一度姓を書きます。 ノートは、どの出来事が出発でどれが帰還であるかを教えてくれません。"
date: "2026-08-13T15:59:50+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102280
codeforces_index: "F"
codeforces_contest_name: "2010, \u0422\u0440\u0435\u043d\u0438\u0440\u043e\u0432\u043a\u0430 \u0421\u0413\u0410\u0423 aka \u041a\u043e\u043d\u0442\u0435\u0441\u0442 \u043f\u0440\u043e \u043c\u0430\u0440\u0448\u0440\u0443\u0442\u043a\u0438"
rating: 0
weight: 102280
solve_time_s: 146
verified: true
draft: false
---

[CF 102280F - \u041d\u0435\u043e\u0436\u0438\u0434\u0430\u043d\u043d\u0430\u044f \u0437\u0438\u043c\u0430](https://codeforces.com/problemset/problem/102280/F)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 26 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 ノートの各行にはドライバーの姓のみが含まれています。 ドライバーは車庫から出るときと戻るときに一度姓を書きます。 ノートは、どの出来事が出発でどれが帰還であるかを教えてくれません。 

すべての旅行を完了したドライバーの場合、姓は偶数回表示されます。 まだどこかで立ち往生している運転手は、一度だけ不一致で出発したため、その姓が奇数回出現します。 課題はその姓を見つけることです。 一致しない姓がない場合、必要な出力は次のとおりです。`FAIL`。 

たとえば、ノートブックに次の内容が含まれている場合、```
Yakubov
Abramov
Yakubov
```それから`Yakubov`2つの記録があり、`Abramov`1つありますので、`Abramov`戻ってこなかった運転手です。 

の値`n`は 150,000 に達することができ、姓には最大 255 文字を含めることができます。 多くのレコードのペアを比較するアルゴリズムは高価すぎます。 150,000 レコードの場合、二次アルゴリズムはおおよそのパフォーマンスを発揮します。 

[
 \frac{150000\cdot149999}{2}\約 11.25\cdot10^9
 ]

 2 秒の制限をはるかに超えた比較です。 各レコードを一定の回数だけ処理する必要があるため、期待される線形時間のソリューションが得られます。 

不注意な実装が失敗する可能性のあるエッジケースがいくつかあります。 可能な最小の入力は単一レコードです。```
1
Petrov
```答えは`Petrov`なぜなら、その単一の出現を別のレコードと組み合わせることができないからです。 

姓の繰り返しも正しく処理する必要があります。 のために```
5
Ivanov
Ivanov
Ivanov
Ivanov
Ivanov
```答えは`Ivanov`5 回発生すると 1 つの不一致レコードが残るためです。 単に 1 回だけ出現する姓を検索する実装では、このケースは誤って拒否されます。 

姓は最大 255 文字であるため、文字や小さな数値としてではなく、任意の文字列として扱う必要があります。 例えば、```
1
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
```最大長の有効な姓が 1 つ含まれており、その正確な文字列を出力する必要があります。 

最後に、答えはレコードの順序ではなくパリティによって決まります。 シーケンス```
Askerov
Shumacher
Askerov
Abalkin
Abalkin
```それらの行がどこに現れるかに関係なく、関連するペアまたは一致しない出現が 3 つあります。 正しい答えは、`Shumacher`。 

## アプローチ

 直接的なアプローチは、すべての姓を取得し、それとペアとなる別の同一の姓を検索することです。 ペアが見つかると、両方の記録を削除でき、残った記録で戻らなかったドライバーが特定されます。 これは正しいです。完了した旅行ごとに、正確に 2 つの同じ姓が含まれるからです。 

問題は捜索費用です。 最悪の場合、すべてのレコードについて、ほぼすべてのレコードを検査する可能性があります。 と`n = 150000`、これにより、約 112 億 5,000 万件の比較が行われます。 それぞれの比較は単純ですが、その作業量は制限時間をはるかに超えています。 

有益な観察は、ノートブックのエントリの実際の順序は重要ではないということです。 重要なのは、それぞれの姓が何回出現するかです。 完成したすべてのドライバーは偶数カウントを提供しますが、欠落したリターンは 1 つのカウントを偶数から奇数に変更します。 

これをセットで直接利用できます。 姓が初めて登場する場合は、その姓をセットに入れます。 再度表示されたら削除してください。 入力の接頭辞を処理した後、姓は、これまでに奇数回出現した時点でセットに含まれます。 すべてのレコードが処理された後、セットには合計頻度が奇数の姓が正確に含まれます。 

意図した入力条件では、そのような姓は 1 つあります。 セットに値が 1 つだけ含まれていれば、それが答えになります。 空の場合は、すべてのレコードがペアになっているので、出力します。`FAIL`。 不正な入力によって複数の奇数頻度の姓が生成された場合、`FAIL`これは、識別する固有のドライバーがないため、安全な動作でもあります。 

同じアイデアを頻度辞書でも実装できますが、正確なカウントが必要ないため、セットを切り替える方が簡単です。 現在のカウントが奇数か偶数かを知る必要があるだけです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n²) | O(n) | 遅すぎる |
 | 最適 | O(n) が予想されます | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1.読む`n`そして、という空のセットを作成します`odd`。 
2. 各姓を一度に 1 つずつ処理します。 姓が入っていない場合`odd`、挿入します。 すでに存在する場合は削除します。 2 つのケースは、出現回数を偶数から奇数、または奇数から偶数に変更することに正確に対応します。 
3. 結局のところ`n`記録は処理されました、検査してください`odd`。 残っている姓はすべて奇数回出現しています。 
4. 姓が 1 つだけ残っている場合は、それを印刷します。 この姓には一致しないものが 1 つあるため、その運転手は戻ってきません。 
5. セットが空の場合は、印刷します。`FAIL`。 すべての姓は偶数回出現するため、ノートブックのすべてのレコードを同一のレコードと組み合わせることができます。 
6. 複数の姓が残っている場合は、それも印刷します`FAIL`これは、入力によって一意の不一致ドライバーが識別されないためです。 

### なぜ効果があるのか

 不変条件は、任意の数のレコードを処理した後、姓が属するということです。`odd`まさに、処理されたオカレンスの数が奇数の場合です。 最初はすべてのカウントがゼロであるため、不変式が成り立ちます。 姓を読むとパリティが 1 ずつ変わります。 前回のカウントが偶数の場合は姓が挿入され、奇数の場合は姓が削除されます。 したがって、不変条件はすべてのレコードの後でも true のままです。 

最終的に、すべての旅行を完了したすべてのドライバーは偶数のノートブック記録を持ち、セットには参加しません。 戻らなかったドライバーは奇数の記録を持ち、セットに残ります。 したがって、姓が 1 つ残っている場合、それはまさに必要な答えになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())
    odd = set()

    for _ in range(n):
        surname = input().strip()

        if surname in odd:
            odd.remove(surname)
        else:
            odd.add(surname)

    if len(odd) == 1:
        print(next(iter(odd)))
    else:
        print("FAIL")

if __name__ == "__main__":
    solve()
```セット`odd`現在の頻度が奇数である姓のみを保存します。 メンバーシップのテストと挿入または削除は O(1) であることが予想されるため、1 つのレコードの処理には予想される一定の時間がかかります。 

使用する`input().strip()`によって残された改行を削除します`readline()`。 姓内の文字は変更されないため、大文字と小文字は区別されません。 例えば、`Ivanov`そして`ivanov`は異なる文字列であるため、異なるドライバーとして扱う必要があります。 

関係する整数演算はありません`n`ループ カウンタを超えているため、整数オーバーフローは Python では問題になりません。 ループは正確に実行されます`n`これにより、1 つずつのあいまいさが回避されます。 

条件`len(odd) == 1`単に任意の要素を取得するよりも望ましいです。 後者は、複数の奇数頻度の姓を含む不正な入力を隠します。 空集合の場合は、要求されたものに直接対応します。`FAIL`出力。 

## 実用的な例

 ### サンプル 1

 The first sample contains three records:```
3
Yakubov
Yakubov
Abramov
```セットの状態は次のように変化します。 

| 記録 | 姓 | アクション |`odd`after record |
 | --- | --- | --- | --- |
 | 1 |`Yakubov`| insert |`{Yakubov}`|
 | 2 |`Yakubov`| 削除 |`{}`|
 | 3 |`Abramov`| 挿入 |`{Abramov}`|`Yakubov`は 2 回発生するため、その 2 つのレコードは互いにキャンセルされます。`Abramov`一度発生するとセット内に残り、出力が得られます`Abramov`。 

### サンプル 2

 2つ目のサンプルは、```
7
Askerov
Shumacher
Askerov
Askerov
Shumacher
Abalkin
Abalkin
```状態はこのように進化します。 

| 記録 | 姓 | アクション |`odd`記録後 |
 | --- | --- | --- | --- |
 | 1 |`Askerov`| 挿入 |`{Askerov}`|
 | 2 |`Shumacher`| 挿入 |`{Askerov, Shumacher}`|
 | 3 |`Askerov`| 削除 |`{Shumacher}`|
 | 4 |`Askerov`| 挿入 |`{Shumacher, Askerov}`|
 | 5 |`Shumacher`| 削除 |`{Askerov}`|
 | 6 |`Abalkin`| 挿入 |`{Askerov, Abalkin}`|
 | 7 |`Abalkin`| 削除 |`{Askerov}`|`Shumacher`2回登場し、`Abalkin`が2回出現します。`Askerov`は 3 回出現するため、ちょうど 1 つの出現が一致しないままになります。 出力は`Askerov`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) が予想されます | 各姓は O(1) 集合演算で 1 回処理されます。 |
 | スペース | O(n) | 最悪の場合、多くの異なる姓が奇数の頻度を持ち、セットに残る可能性があります。 |

 と`n`最大 150,000 の場合、予想される線形時間アルゴリズムは、入力行ごとに数回のハッシュ テーブル操作のみを実行します。 これは、2 秒の制限内で意図されたアルゴリズムの複雑さの範囲内に十分収まります。 メモリ使用量は、個別に保存されたレコードの数ではなく、異なる姓の数に応じて増加します。 

## テストケース```python
import sys
import io

def solve():
    input = sys.stdin.readline
    n = int(input())
    odd = set()

    for _ in range(n):
        surname = input().strip()
        if surname in odd:
            odd.remove(surname)
        else:
            odd.add(surname)

    if len(odd) == 1:
        print(next(iter(odd)))
    else:
        print("FAIL")

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    try:
        sys.stdin = io.StringIO(inp)
        sys.stdout = io.StringIO()
        solve()
        return sys.stdout.getvalue().strip()
    finally:
        sys.stdin = old_stdin
        sys.stdout = old_stdout

# Provided sample 1
assert run(
    """3
Yakubov
Yakubov
Abramov
"""
) == "Abramov", "sample 1"

# Provided sample 2
assert run(
    """7
Askerov
Shumacher
Askerov
Askerov
Shumacher
Abalkin
Abalkin
"""
) == "Askerov", "sample 2"

# Minimum-size input
assert run(
    """1
Petrov
"""
) == "Petrov", "minimum n"

# All records have the same surname, with an odd number of occurrences
assert run(
    """5
Ivanov
Ivanov
Ivanov
Ivanov
Ivanov
"""
) == "Ivanov", "all equal values"

# Maximum valid odd n, all records have the same surname
max_n = 149999
assert run(
    str(max_n) + "\n" + ("Z" * 255 + "\n") * max_n
) == "Z" * 255, "maximum n and maximum surname length"

# No unique unmatched surname, representing the FAIL case
assert run(
    """6
A
B
A
B
C
C
"""
) == "FAIL", "all records paired"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 / Petrov`|`Petrov`| 最小の有効な入力と単一の不一致レコード |
 | 5部`Ivanov`|`Ivanov`| 繰り返しの発生と頻度ではなくパリティが 1 に等しい |
 | 255 文字の姓のコピーが 149999 個 | 同じ姓 | 最大有効奇数`n`姓の最大長 |
 |`A B A B C C`|`FAIL`| すべての出現はペアになり、セットは空になります。 

## 特殊なケース

 最小のケースは```
1
Petrov
```セットは空から始まります。`Petrov`が挿入されるため、唯一のレコードの後に​​セットが`{Petrov}`。 サイズは 1 で、アルゴリズムは次のように出力します。`Petrov`。 

姓が 1 回だけ出現する必要はありません。 考慮する```
5
Ivanov
Ivanov
Ivanov
Ivanov
Ivanov
```最初の出現により挿入されます`Ivanov`、2 番目はそれを削除し、3 番目はそれを再度挿入し、4 番目はそれを削除し、5 番目はそれを挿入します。 最終セットの内容は、`Ivanov`、したがって、答えは次のとおりです`Ivanov`。 これが、周波数 1 を具体的に検索するよりもパリティを追跡する方が適切である理由です。 

すべてが返された場合は次のように表すことができます。```
6
A
B
A
B
C
C
```

`A`挿入され、取り外されると、`B`挿入され、削除され、最後に`C`挿入され、削除されます。 最終セットは空であるため、プログラムは次のように出力します。`FAIL`。 

最大長の姓は、通常の Python 文字列として処理されます。 例えば、```
1
ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
```姓全体を変更せずに生成します。 このアルゴリズムは姓にインデックスを付けたり、その長さについて何も仮定したりしないため、255 文字の境界には特別な分岐は必要ありません。 

レコードの順序も影響しません。 のために```
5
Askerov
Abalkin
Askerov
Abalkin
Shumacher
```2 つのペアは、その位置に関係なくキャンセルされます。 最初の 4 つのレコードの後、セットは空になり、`Shumacher`最後のレコードによって挿入されます。 結果は`Shumacher`。 

最後に、宣言された入力サイズが奇数です。 ストーリーの有効な条件の下では、完全に完了した旅行ごとに 2 つのレコードが提供され、未完了の旅行が 1 つだけあれば追加のレコードが 1 つ提供されるため、奇数頻度の姓は 1 つだけ存在する必要があります。 の`FAIL`ブランチが保持されるのは、出力仕様で明示的に許可されていることと、入力が意図した構造を満たさない場合に最終セット サイズをチェックすることで実装が堅牢になるためです。
