---
title: "CF 102800B - 問題の選択"
description: "このタスクは、コンテストの問題を選択することです。 各問題は URL によって識別されますが、有用な情報は URL の中に隠されており、最後の整数の問題 ID が表示されます。"
date: "2026-07-28T22:52:34+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102800
codeforces_index: "B"
codeforces_contest_name: "The 14th Jilin Provincial Collegiate Programming Contest"
rating: 0
weight: 102800
solve_time_s: 56
verified: true
draft: false
---

[CF 102800B - 問題の選択](https://codeforces.com/problemset/problem/102800/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 56 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 このタスクは、コンテストの問題を選択することです。 各問題は URL によって識別されますが、有用な情報は URL の中に隠されており、最後の整数の問題 ID が表示されます。 すべてのテスト ケースについて、一意の問題 URL のコレクションを受け取り、それらの ID を抽出し、並べ替えて、最小のものを出力する必要があります。`k`ID を昇順に並べます。 

テスト ケース内の URL の数は最大でも 1000 です。これは十分に小さいため、単純な並べ替えアプローチでも十分に高速です。 典型的な`O(n log n)`sort は約数千件の比較のみを実行します。`n = 1000`したがって、より複雑なデータ構造は必要ありません。 ID 範囲も 1 ～ 10000 に制限されているため、カウントベースの方法も可能ですが、その必要はありません。 

URL は数値ではなく文字列であるため、入力サイズを慎重に解析する必要があります。 よくある間違いは、ID の桁数が常に固定であると想定することです。 たとえば、次で終わる URL は、`501`で終わる URL`1001`どちらも有効であるため、ソリューションでは最後のスラッシュ以降をすべて抽出する必要があります。 

もう 1 つのエッジケースは次のような場合です`k`に等しい`n`。 この状況では、答えはソートされた ID セット全体です。 データを部分的にのみ処理するソリューションでは、誤って値が省略される可能性があります。 

たとえば、次のことを考えてみましょう。```
1
3 3
http://acm.hit.edu.cn/problemset/9
http://acm.hit.edu.cn/problemset/2
http://acm.hit.edu.cn/problemset/7
```正しい出力は次のとおりです。```
2 7 9
```を前提とした不注意な実装`k`は常により小さいです`n`すべての値を出力できない場合があります。 

もう 1 つのケースは、ID に含まれる桁数が他の ID よりも少ない場合です。```
1
4 2
http://acm.hit.edu.cn/problemset/501
http://acm.hit.edu.cn/problemset/50
http://acm.hit.edu.cn/problemset/500
http://acm.hit.edu.cn/problemset/1000
```正しい出力は次のとおりです。```
50 500
```URL 文字列を直接並べ替えると、文字列の比較が整数の比較と一致しないため、間違った順序が与えられます。 ソリューションでは、並べ替える前に、抽出されたテキストを整数に変換する必要があります。 

## アプローチ

 簡単なアプローチは、すべての URL を読み取り、ID を抽出し、すべての ID を配列に格納し、配列を並べ替えて、最初の ID を取得することです。`k`要素。 これは、必要な答えが ID の番号順にのみ依存するため、機能します。 すべての ID がソートされると、最小の ID が`k`値はまさに最初です`k`ポジション。 

ブルートフォースの代替手段では、最小の未使用 ID を繰り返し検索します。 それぞれについて、`k`回答が必要な場合は、残りのすべての ID をスキャンして、次の最小値を見つけます。 これは正しいですが、最悪の場合、パフォーマンスは約`n * k`比較。 以来`k`に等しいことができます`n`、最悪の場合に達します`1000 * 1000 = 1,000,000`テストケースごとの比較。 これはこれらの制約ではまだ許容可能ですが、問題の標準的な構造を無視しているため、より大きなバージョンでは悪い習慣になります。 

さらに良い観察は、この問題が数値セットの最小要素を正確に求めているということです。 URL が ID に変換されると、特別な構造は残りません。 並べ替えでは、1 回の操作で完全な順序付けが行われます。`k`要素は望ましい答えです。 

ブルートフォース手法は、次に小さい要素の選択を直接シミュレートするため機能しますが、同様の検索が繰り返されます。 並べ替え観察では、すべての値を一度に整理することで、繰り返しの作業が不要になります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nk)、最悪の場合 O(n²) | O(n) | ここでは受け入れられますが、非効率です |
 | 最適 | O(n log n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. テスト ケースの数を読み取ります。 テスト ケースごとに、次の内容を読んでください。`n`そして`k`, 次に、抽出された問題 ID を保存するリストを準備します。 URL 自体は、数値 ID を取得した後は必要ありません。 
2. それぞれの処理を行います。`n`URL。 最後の部分の後の部分を見つける`/`文字を入力して整数に変換します。 URL の最後のコンポーネントは問題 ID であることが保証されているため、この抽出により必要な値が正確に得られます。 
3. ID のリストを昇順に並べ替えます。 並べ替えは最小の ID をリストの先頭に配置するため、重要な操作です。 
4. Output the first`k`並べ替えられたリストの値をスペースで区切って並べます。 リストはすでに順序付けされているため、追加の処理は必要ありません。 

なぜ効果があるのか:

 抽出後、アルゴリズムには元の問題とまったく同じ一連の数値が含まれ、文字列ではなく整数としてのみ表現されます。 並べ替えでは、すべての値が保持され、最小値から最大値の順に並べられます。 最初`k`この順序の位置には、`k`残りのすべての位置には、選択されたものと少なくとも同じ大きさの値が含まれるためです。 したがって、生成される出力は常に、必要な問題 ID のセットになります。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    ans = []

    for _ in range(t):
        n, k = map(int, input().split())
        ids = []

        for _ in range(n):
            url = input().strip()
            ids.append(int(url.rsplit('/', 1)[1]))

        ids.sort()
        ans.append(" ".join(map(str, ids[:k])))

    sys.stdout.write("\n".join(ans))

if __name__ == "__main__":
    solve()
```このソリューションは、まずテスト ケースの数を読み取り、各ケースを個別に処理します。 リスト`ids`ID が抽出された後は URL 形式が無関係であるため、整数のみが格納されます。 

表現`url.rsplit('/', 1)[1]`最後のスラッシュでのみ文字列を分割します。 これにより、URL の前半部分の正確な構造に依存することがなくなります。 次のような、異なる桁長の ID を正しく処理します。`9`、`501`、 そして`10000`。 

全てのIDが集まりましたら、`sort()`それらを数値的に並べます。 Python は整数を値で比較します。これは問題で必要な順序です。 最後にスライスして、`ids[:k]`必要な数の最小 ID を正確に選択します。 以来`k`等しいことができます`n`、これは、並べ替えられたリスト全体を印刷する必要がある場合も正しく処理します。 

出力は次のように蓄積されます。`ans`最後に一度印刷します。 これにより、不必要なフラッシュが回避され、入力と出力の効率が維持されます。 

## 実用的な例

 ### サンプル 1

 入力:```
1
3 2
http://acm.hit.edu.cn/problemset/1003
http://acm.hit.edu.cn/problemset/1002
http://acm.hit.edu.cn/problemset/1001
```トレース:

 | ステップ | 抽出された ID | ソートされた ID | 出力 |
 | --- | --- | --- | --- |
 | 最初の URL を読む | 1003 | | |
 | 2 番目の URL を読む | 1003、1002 | | |
 | 3 番目の URL を読む | 1003、1002、1001 | | |
 | 並べ替え | 1003、1002、1001 | 1001、1002、1003 | |
 | 先取 2 | | 1001、1002、1003 | 1001 1002 |

 トレースは、元の URL の順序が影響を及ぼさないことを示しています。 解析後は、抽出された数値 ID のみが重要になります。 

### サンプル 2

 入力:```
1
4 1
http://acm.hit.edu.cn/problemset/1001
http://acm.hit.edu.cn/problemset/2001
http://acm.hit.edu.cn/problemset/3001
http://acm.hit.edu.cn/problemset/501
```トレース:

 | ステップ | 抽出された ID | ソートされた ID | 出力 |
 | --- | --- | --- | --- |
 | URL を読む | 1001、2001、3001、501 | | |
 | 並べ替え | | 501、1001、2001、3001 | |
 | 先取 1 | | 501、1001、2001、3001 | 501 |

 この例は、整数変換が重要である理由を示しています。 ID が文字列として比較された場合、`1001`前に誤って表示される可能性があります`501`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n log n) | ID の抽出には O(n) がかかり、ソートがランタイムの大半を占めます。 
| スペース | O(n) | リストには、1 つのテスト ケースについて抽出されたすべての ID が格納されます。 

最大`n`は 1000 個しかないので、制限時間内に簡単に並べ替えることができます。 数値 ID のみを保存する必要があるため、メモリ使用量も少なくなります。 

## テストケース```python
import sys
import io

def solve(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    output = io.StringIO()
    sys.stdout = output

    def main():
        input = sys.stdin.readline
        t = int(input())
        ans = []

        for _ in range(t):
            n, k = map(int, input().split())
            ids = []
            for _ in range(n):
                url = input().strip()
                ids.append(int(url.rsplit('/', 1)[1]))
            ids.sort()
            ans.append(" ".join(map(str, ids[:k])))

        print("\n".join(ans), end="")

    main()

    sys.stdin = old_stdin
    sys.stdout = old_stdout
    return output.getvalue()

assert solve("""3
3 2
http://acm.hit.edu.cn/problemset/1003
http://acm.hit.edu.cn/problemset/1002
http://acm.hit.edu.cn/problemset/1001
4 1
http://acm.hit.edu.cn/problemset/1001
http://acm.hit.edu.cn/problemset/2001
http://acm.hit.edu.cn/problemset/3001
http://acm.hit.edu.cn/problemset/501
1 3
http://acm.hit.edu.cn/problemset/9
http://acm.hit.edu.cn/problemset/2
http://acm.hit.edu.cn/problemset/7
""") == """1001 1002
501
2 7 9""", "samples"

assert solve("""1
1 1
http://acm.hit.edu.cn/problemset/10000
""") == "10000", "minimum size case"

assert solve("""1
5 5
http://acm.hit.edu.cn/problemset/5
http://acm.hit.edu.cn/problemset/4
http://acm.hit.edu.cn/problemset/3
http://acm.hit.edu.cn/problemset/2
http://acm.hit.edu.cn/problemset/1
""") == "1 2 3 4 5", "k equals n"

assert solve("""1
4 2
http://acm.hit.edu.cn/problemset/50
http://acm.hit.edu.cn/problemset/500
http://acm.hit.edu.cn/problemset/501
http://acm.hit.edu.cn/problemset/1000
""") == "50 500", "different digit lengths"

assert solve("""1
6 3
http://acm.hit.edu.cn/problemset/8
http://acm.hit.edu.cn/problemset/8
http://acm.hit.edu.cn/problemset/8
http://acm.hit.edu.cn/problemset/8
http://acm.hit.edu.cn/problemset/8
http://acm.hit.edu.cn/problemset/8
""") == "8 8 8", "all equal values"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 提供されたサンプル |`1001 1002`、`501`、`2 7 9`| 基本的な抽出、並べ替え、選択 |
 | 単一の URL |`10000`| 最小入力サイズと大きな境界 ID |
 |`k = n`|`1 2 3 4 5`| 完全なソート済みリストを印刷する |
 | 混合桁長 |`50 500`| 文字列の順序ではなく整数の順序 |
 | すべて等しい値 |`8 8 8`| 一般化された実装での重複値の処理 |

 ## 特殊なケース

 いつ`k`URL の数と等しい場合、アルゴリズムには特別な分岐は必要ありません。 入力の場合:```
1
3 3
http://acm.hit.edu.cn/problemset/9
http://acm.hit.edu.cn/problemset/2
http://acm.hit.edu.cn/problemset/7
```抽出されたリストは`[9, 2, 7]`。 並べ替えると得られるもの`[2, 7, 9]`、そしてスライス`[:3]`すべての要素を返します。 出力は次のとおりです。```
2 7 9
```要求される値が常に少ないと想定したソリューションでは、誤って早期に停止してしまう可能性があります。 

桁数が異なる ID の場合、アルゴリズムは並べ替え前にテキストを整数に変換します。 と：```
1
4 2
http://acm.hit.edu.cn/problemset/501
http://acm.hit.edu.cn/problemset/50
http://acm.hit.edu.cn/problemset/500
http://acm.hit.edu.cn/problemset/1000
```抽出された値は`[501, 50, 500, 1000]`。 数値的に並べ替えると、`[50, 500, 501, 1000]`したがって、最初の 2 つの値が出力されます。```
50 500
```文字列ベースのソリューションでは文字が比較され、値が間違った順序で配置される可能性があります。 

すべての ID が等しい場合でも、並べ替え操作は正しく動作します。 元の問題では一意の ID が保証されていますが、このケースをテストすると、選択ロジック自体が一意性に依存していないことが確認されます。 IDを含む6つのURLの場合`8`そして`k = 3`、並べ替えでは値がそのまま残ります。`[8, 8, 8, 8, 8, 8]`、答えは最初の 3 つの値です。```
8 8 8
```
