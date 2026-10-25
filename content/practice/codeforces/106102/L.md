---
title: "CF 106102L - テキストメッセージ送信"
description: "いくつかのテキストメッセージが届きます。 大文字と小文字を区別せずに、少なくとも 3 つの連続した文字 a が含まれるメッセージは「バトル」とみなされます。 つまり、aaa、AaA、aAAA、AAA などのシーケンスはすべて適格です。"
date: "2026-06-25T11:50:18+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 106102
codeforces_index: "L"
codeforces_contest_name: "AGM 2025, Final Round, Day 1"
rating: 0
weight: 106102
solve_time_s: 36
verified: true
draft: false
---

[CF 106102L - テキストメッセージ](https://codeforces.com/problemset/problem/106102/L)

 **評価:** -
 **タグ:** -
 **解決時間:** 36 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 いくつかのテキストメッセージが届きます。 メッセージに少なくとも 3 つの連続した文字が含まれている場合、メッセージは「バトル」とみなされます。`a`、大文字小文字を無視します。 それは次のようなシーケンスを意味します`aaa`、`AaA`、`aAA`、 そして`AAA`全員が合格します。 

メッセージごとに、そのようなランがテキスト内のどこかに存在するかどうかを判断する必要があります。 

適格な実行が表示された場合は、次のように出力します。```
Love is war!
```それ以外の場合は、次のように出力します。```
Just friends <3
```メッセージにはスペースやその他の文字が含まれる場合があるため、各行全体を完全な文字列として読み取る必要があります。 

制約は小さいです。 各メッセージの長さは最大 100 文字であるため、すべての文字を単純にスキャンするだけでも十分に高速です。 あれば`n`メッセージの合計作業量は、すべてのメッセージの合計文字数に比例します。 

間違いを引き起こす主な実装の詳細は、大文字と小文字を区別しないマッチングです。 次のようなシーケンス:```
AAa
```以下とまったく同じように扱う必要があります。```
aaa
```もう 1 つの簡単な間違いは、非対応の場合に連続カウントを誤ってリセットすることです。`a`キャラクター。 

次のメッセージを考えてみましょう。```
aabaa
```最長連続運転`a`長さは 2 なので、正しい出力は次のようになります。```
Just friends <3
```合計数をカウントするだけのソリューション`a`登場人物はこれを誤って戦闘として分類してしまいます。 

別の例として:```
xAAAy
```連続したものが3つ含まれています`a`小文字に変換された後の文字なので、正しい出力は次のようになります。```
Love is war!
```大文字と小文字を区別する実装ではこれが見逃されます。 

## アプローチ

 ブルートフォースのアイデアは、長さ 3 以上の考えられるすべての部分文字列を調べ、その部分文字列内のすべての文字がその文字であるかどうかを確認することです。`a`大文字と小文字を区別しない比較の下で。 

長文のメッセージの場合`m`、O(m²) 個の部分文字列があり、それぞれをチェックするとさらに時間がかかる可能性があります。 ここでの制限はわずかであり、これでも問題はありませんが、必要以上に多くの作業が実行されます。 

問題の構造から、より簡単な観察が得られます。 実際には、実行がどこで開始され、どこで終了するかは気にしません。 知る必要があるのは、`a`長さ3に達します。 

これは、進行中の実行の長さを維持しながら、メッセージを 1 回スキャンすることを示唆しています。`a`文字。 私たちが見るとき、`a`(文字を小文字に変換した後)、ランレングスを長くします。 他の文字はランを中断し、カウンターをゼロにリセットします。 

カウンタが 3 になった瞬間、そのメッセージに対する答えはすでにわかっています。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(㎡) | お(1) | 不要 |
 | 最適 | O(m) | お(1) | 承認済み |

 ここ`m`は 1 つのメッセージの長さを示します。 

## アルゴリズムのチュートリアル

 1. 整数を読み取る`n`。 
2. 繰り返します`n`毎回 1 行の完全なメッセージ行を読み取ります。 
3. カウンタを初期化する`run = 0`。 
4. メッセージを 1 文字ずつ確認します。 
5. 現在の文字を小文字に変換します。 
6. キャラクターが`'a'`、 増加`run`1 によって設定されます。それ以外の場合は設定されます`run = 0`。 
7. もし`run`が 3 以上になると、メッセージを戦闘としてマークし、そのメッセージのスキャンを停止します。 
8. 出力`"Love is war!"`戦闘が見つかった場合。 それ以外の場合は出力`"Just friends <3"`。 

### なぜ効果があるのか

 変数`run`完全に連続したもので構成される現在のサフィックスの長さを常に格納します。`a`大文字と小文字を区別しない文字。 

非`a`文字が表示されると、前の位置で終わる連続したシーケンスは壊れるので、リセットすると`run`ゼロにするのが正しいです。 

メッセージに有効な戦闘が含まれるのは、`a`長さは少なくとも 3 に達します。 アルゴリズムはそのイベントを初めて検出します`run >= 3`そして、それが起こらなかった場合、メッセージのどこにも予選実行は存在しません。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n = int(input())

for _ in range(n):
    s = input().rstrip("\n")

    run = 0
    battle = False

    for ch in s:
        if ch.lower() == 'a':
            run += 1
            if run >= 3:
                battle = True
                break
        else:
            run = 0

    if battle:
        print("Love is war!")
    else:
        print("Just friends <3")
```最初の行はメッセージの数を読み取ります。 後続の各行は独立して処理されます。 

カウンター`run`現在の連続したシーケンスを追跡します`a`文字。 使用する`ch.lower()`大文字と小文字のバージョンが同じように扱われることを保証します。 

初期の`break`正確さを保つためには必要ありませんが、答えがすでにわかっている場合は、余分な作業を避けることができます。 

使用する`rstrip("\n")`メッセージ自体の一部であるスペースは保持しながら、入力によって生成された末尾の改行のみを削除します。 

## 実用的な例

 ### 例 1

 入力メッセージ:```
THIS MEANS WAAAAAARRRRRRRRRR
```| キャラクター | 小文字 | 走る |
 | --- | --- | --- |
 | W | w | 0 |
 | あ | | 1 |
 | あ | | 2 |
 | あ | | 3 |

 3回連続で`a`、条件は満たされており、答えは次のようになります。```
Love is war!
```この例は、大文字を小文字と同じように扱う必要があることを示しています。 

### 例 2

 入力メッセージ:```
what is a aardvark
```| キャラクター | 小文字 | 走る |
 | --- | --- | --- |
 | | | 1 |
 | スペース | スペース | 0 |
 | | | 1 |
 | | | 2 |
 | r | r | 0 |

 The longest consecutive run has length 2, so no battle exists.

 出力：```
Just friends <3
```この例は、次の合計出現回数をカウントする理由を示しています。`a`では不十分です。 連続した出現のみが重要です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(m) | 各文字は一度検査されます。 
| スペース | お(1) | 少数の変数のみが保存されます。 

各メッセージは正確に 1 回スキャンされ、メッセージは短いため、ソリューションは妥当なコンテスト制限内に簡単に適合します。 

## テストケース```python
import sys
import io

def solve():
    input = sys.stdin.readline

    n = int(input())

    out = []

    for _ in range(n):
        s = input().rstrip("\n")

        run = 0
        battle = False

        for ch in s:
            if ch.lower() == 'a':
                run += 1
                if run >= 3:
                    battle = True
                    break
            else:
                run = 0

        out.append("Love is war!" if battle else "Just friends <3")

    sys.stdout.write("\n".join(out))

def run(inp: str) -> str:
    backup_stdin = sys.stdin
    backup_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    sys.stdout = io.StringIO()

    solve()

    result = sys.stdout.getvalue()

    sys.stdin = backup_stdin
    sys.stdout = backup_stdout

    return result

# sample-style cases
assert run(
"""5
How are you doing Kaguya?
THIS MEANS WAAAAAARRRRRRRRRR
oops sorry wrong person
LlLmmAAaOoo that's soooo funnnyyy
what is a aardvark
"""
) == (
"""Just friends <3
Love is war!
Just friends <3
Love is war!
Just friends <3"""
)

# minimum size
assert run("1\na\n") == "Just friends <3"

# exactly three consecutive letters
assert run("1\naaa\n") == "Love is war!"

# mixed case
assert run("1\nAaA\n") == "Love is war!"

# separated runs
assert run("1\naa baa aa\n") == "Just friends <3"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`a`|`Just friends <3`| 最小サイズのメッセージ |
 |`aaa`|`Love is war!`| 正確なしきい値 3 |
 |`AaA`|`Love is war!`| 大文字と小文字を区別しない一致 |
 |`aa baa aa`|`Just friends <3`| 非記号で区切られた実行`a`キャラクター |

 ## 特殊なケース

 次のメッセージを考えてみましょう。```
AaA
```このアルゴリズムは、スキャン中に各文字を小文字に変換します。 

の値`run`なる：```
1 -> 2 -> 3
```出来るだけ早く`run`3 に達すると、アルゴリズムは次のように出力します。```
Love is war!
```これにより、大文字混合が正しく処理されます。 

次に、次のことを考えてみましょう。```
aabaa
```スキャンは次のように進行します。```
a -> run = 1
a -> run = 2
b -> run = 0
a -> run = 1
a -> run = 2
```カウンタが 3 に達することはないため、結果は次のようになります。```
Just friends <3
```これにより、連続した出現のみが重要であることがわかります。 

最後に、次のことを考慮してください。```
aaaa
```カウンターは次のように進化します。```
1 -> 2 -> 3
```アルゴリズムは戦闘を即座に認識し、スキャンを停止します。 長さ 4、5、またはそれ以上のランは必ず最初に長さ 3 を通過するため、より長いランは自動的に処理されます。
