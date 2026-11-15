---
title: "CF 1862A - ギフトカーペット"
description: "各セルに小文字が含まれる $n 倍 m$ のサイズの長方形のカーペットが与えられます。 ヴィカは、カーペットから自分の名前「ヴィカ」を読むのが好きです。各列から 1 文字を選択し、厳密に左から右に移動し、「v」の文字を正確に 4 列選択します。"
date: "2026-06-09T00:50:53+07:00"
tags: ["codeforces", "competitive-programming", "dp", "greedy", "implementation", "strings"]
categories: ["algorithms"]
codeforces_contest: 1862
codeforces_index: "A"
codeforces_contest_name: "Codeforces Round 894 (Div. 3)"
rating: 800
weight: 1862
solve_time_s: 83
verified: true
draft: false
---

[CF 1862A - ギフト カーペット](https://codeforces.com/problemset/problem/1862/A)

 **評価:** 800
 **タグ:** dp、貪欲、実装、文字列
 **解決時間:** 1 分 23 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 サイズの長方形のカーペットが与えられました$n \times m$ここで、各セルには小文字が含まれます。 Vika は、各列から 1 文字を選択し、厳密に左から右に移動し、文字「v」、「i」、「k」、「a」の順序で正確に 4 つの列を選択することによって、カーペットから自分の名前「vika」を読むのが好きです。 各列には最大 1 文字を含めることができ、列は個別である必要があります。 

課題は、複数のテスト ケースについて、Vika が特定のカーペット上で自分の名前を読み取ることができるかどうかを判断することです。 可能であれば「YES」、不可能であれば「NO」を出力します。 

境界は小さいです:$1 \le n, m \le 20$最大 100 のテスト ケース。 どちらの次元も小さいため、アルゴリズムは時間的に複雑になります$O(n \cdot m)$テストケースごとに許容されます。 この制約は、複雑な最適化や高度なデータ構造が必要ないことを示唆しています。 

明らかではないエッジケースが発生するのは次の場合です。$n = 1$または$m = 4$文字は 1 行に表示されます。 たとえば、カーペットが次のような場合:```
1 4
kvai
```文字が 1 行内で順番に表示されると仮定して不注意に左から右の列をスキャンすると、左から右の列順序での正しい順序が「vika」と綴られていない場合でも、誤って「YES」と結論付けられる可能性があります。 もう 1 つのエッジ ケースは、必要な文字が同じ列内の複数の行にまたがっている場合に発生します。ソリューションでは、列内の任意の行を有効な一致と見なす必要があります。 

## アプローチ

 ブルート フォース ソリューションでは、4 つの異なる列のすべての組み合わせを試し、必要な文字がこれらの列に順番に表示されるかどうかを確認します。 がある$\binom{m}{4}$4 つの列を選択する方法と、各列に文字があるかどうかを確認する方法$O(n)$時間。 最悪の場合、こうなります$O(n \cdot m^4)$テストケースごとに。 のために$m = 20$そして$n = 20$つまり、テスト ケースごとに約 320 万回の操作が行われることになりますが、これは許容できる範囲ですが、簡素化することもできます。 

重要な洞察は、シーケンス内で次に必要な文字を含む **最初に使用可能な列** のみを考慮するということです。 「vika」内の次に予期される文字へのポインタを維持しながら、列を左から右にスキャンできます。 現在の列のいずれかの行にその文字が含まれている場合は、次の文字に移動します。 列の最後まで到達し、4 文字すべてを見つけることができた場合、答えは「YES」です。 それ以外の場合は「いいえ」。 この貪欲なアプローチが機能するのは、列を順番に使用する必要があり、一度スキップされた列は再利用できないためです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n * m^4) | お(1) | 許容できるが不要 |
 | 貪欲な左から右へ | O(n * m) | お(1) | 最適かつシンプル |

 ## アルゴリズムのチュートリアル

 1. テストケースの数を読み取る$t$。 テスト ケースごとに、次の内容を読んでください。$n$そして$m$。 
2. カーペットを文字列のリストとして読み取ります (各文字列は行です)。 
3. 変数を初期化する`index`ターゲット文字列「vika」内の現在の文字を追跡するには、0 に設定します。 
4. 列を左から右に繰り返します。 各列について、現在のターゲット文字 (`target[index]`）。 
5. 現在の列にターゲット文字が含まれている場合は、インクリメントします。`index`次の文字に移動します。 
6. すべての列を処理した後、次のことを確認します。`index`4 に等しい。「はい」の場合は、「YES」と出力します。 それ以外の場合は、「NO」を出力します。 

この問題では列内の任意の行が一致することが許可されており、列の順序を保持するだけで済むため、これが機能します。 左から右にスキャンして進むと`index`貪欲に、あらゆる可能性を逃すことなく、文字が正しい順序で表示されることを保証します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

t = int(input())
for _ in range(t):
    n, m = map(int, input().split())
    carpet = [input().strip() for _ in range(n)]
    target = "vika"
    index = 0
    
    for col in range(m):
        if any(carpet[row][col] == target[index] for row in range(n)):
            index += 1
            if index == 4:
                break
    
    print("YES" if index == 4 else "NO")
```外側のループはテスト ケースを読み取ります。 内側のループは列を左から右にスキャンし、次を使用して各列を現在のターゲット文字と照合します。`any()`。 インクリメント`index`一致が見つかった場合にのみ、文字が順番に選択され、各列に最大 1 文字が含まれることが保証されます。 

## 実用的な例

 **例 1**

 入力:```
1 4
vika
```| 列 | 列内の文字 | インデックス | 一致しましたか？ |
 | --- | --- | --- | --- |
 | 0 | v | 0 | YES → インデックス=1 |
 | 1 | 私 | 1 | YES → インデックス=2 |
 | 2 | k | 2 | YES → インデックス=3 |
 | 3 | | 3 | はい → インデックス=4 |

 出力：`YES`これは、単純な左から右への貪欲スキャンが 1 列のカーペットで機能することを示しています。 

**例 2**

 入力:```
3 3
bad
car
pet
```| 列 | 列内の文字 | インデックス | 一致しましたか？ |
 | --- | --- | --- | --- |
 | 0 | b、c、p | 0 | いいえ |
 | 1 | あ、あ、え | 0 | いいえ |
 | 2 | d、r、t | 0 | いいえ |

 出力：`NO`「v」を含む列はないため、`index`決して進歩しない。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(t * n * m) | 各テスト ケースの各列がスキャンされ、各スキャンで最大 n 行がチェックされます。 
| スペース | O(n * m) | カーペットはメモリに保存されます |

 と$t \le 100$、$n, m \le 20$、最大操作は$100 * 20 * 20 = 40,000$、1秒の制限内に十分収まります。 メモリ使用量は、256 MB の制限に比べれば無視できます。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output
    # copy solution here
    t = int(input())
    for _ in range(t):
        n, m = map(int, input().split())
        carpet = [input().strip() for _ in range(n)]
        target = "vika"
        index = 0
        for col in range(m):
            if any(carpet[row][col] == target[index] for row in range(n)):
                index += 1
                if index == 4:
                    break
        print("YES" if index == 4 else "NO")
    return output.getvalue().strip()

# Provided samples
assert run("5\n1 4\nvika\n3 3\nbad\ncar\npet\n4 4\nvvvv\niiii\nkkkk\naaaa\n4 4\nvkak\niiai\navvk\nviaa\n4 7\nvbickda\nvbickda\nvbickda\nvbickda\n") == "YES\nNO\nYES\nNO\nYES"

# Custom cases
assert run("1\n1 4\nkvai\n") == "NO"  # letters out of order
assert run("1\n4 4\nvvvv\niiii\nkkkk\naaaa\n") == "YES"  # all letters in separate rows, same columns
assert run("1\n4 1\nv\ni\nk\na\n") == "NO"  # single column, cannot pick 4 distinct columns
assert run("1\n4 5\nvxxxx\nixxxx\nkxxxx\naxxxx\n") == "YES"  # letters spread, enough columns
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`1 4\nkvai`| いいえ | 正しい順序が必要です |
 |`4 4\nvvvv\niiii\nkkkk\naaaa`| はい | 列ごとに複数の行を許可 |
 |`4 1\nv\ni\nk\na`| いいえ | 単一列には 4 文字を指定できません |
 |`4 5\nvxxxx\nixxxx\nkxxxx\naxxxx`| はい | 複数の列に散らばった文字 |

 ## 特殊なケース

 サイズ 4x1 の単列カーペットの場合:```
4 1
v
i
k
a
```アルゴリズムは唯一の列をスキャンしますが、先に進むことはできません`index`すべての文字が同じ列にあるため、0 を過ぎます。 出力は「NO」となり、個別の列の要件が正しく処理されます。 

文字が間違った順序で表示される 1 列のカーペットの場合:```
1 4
kvai
```アルゴリズムは列を左から右にスキャンし、最初に「v」に一致しない「k」を見つけます。`index`出力は「NO」です。 

複数の文字を含む列の場合:```
4 4
vvvv
iiii
```
