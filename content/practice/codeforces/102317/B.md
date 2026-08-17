---
title: "CF 102317B - 音素回文"
description: "通常の回文は、どちらの方向からでも同じように読み取れます。 ここでは、2 つの異なる文字が同じ音を表すこともあります。"
date: "2026-08-16T18:44:31+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102317
codeforces_index: "B"
codeforces_contest_name: "UCF Locals 2016"
rating: 0
weight: 102317
solve_time_s: 179
verified: true
draft: false
---

[CF 102317B - 音素回文](https://codeforces.com/problemset/problem/102317/B)

 **評価:** -
 **タグ:** -
 **解決時間:** 2 分 59 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 通常の回文は、どちらの方向からでも同じように読み取れます。 ここでは、2 つの異なる文字が同じ音を表すこともあります。 たとえば、次の場合`c`そして`k`同等であると宣言された場合、`cak`は外側の文字であるため音素回文です。`c`そして`k`、同じように聞こえますが、真ん中の`a`それ自体と一致します。 

入力には、いくつかの独立したテスト ケースが含まれています。 各テスト ケースでは、まず、結合されていない小文字のペアのコレクションを受け取ります。 それぞれのペアは、その 2 つの文字が同じ音であることを示しています。 文字はそのようなペアに最大 1 つだけ属するため、同値関係は特に単純です。 ペアの後、いくつかの文字列を受け取ります。 すべての文字列について、すべての文字が反対側の対称位置にある文字と同じ音を持つかどうかを判断する必要があります。 出力では、元の文字列とその後に続く文字列が再現される必要があります。`YES`または`NO`、ヘッダーとテスト ケースを区切る空行が含まれます。 

同等のペアの数は最大 13 で、最大 26 文字をカバーします。 各テスト文字列の長さは最大 50 で、1 つのテスト ケースには最大 100 個の文字列が含まれます。 これらの制限は十分小さいため、各対称文字ペアの 13 ペアすべてをチェックするだけでも簡単に十分に高速です。 最適な実装では、各文字をそのサウンド クラスの代表に割り当て、すべての比較を一定時間に短縮することで、より良い結果を得ることができます。 

長さ 1 の文字列は常に音素回文です。 たとえば、任意のサウンドペアのセットでは、入力```
1
1
c k
1
a
```生成する```
Test case #1:
a YES
```反対する反対の文字がないため、少なくとも 1 組の位置を必要とする不用意な実装は、誤って拒否される可能性があります。 

2 番目のエッジ ケースは、文字が異なるが同等である場合に発生します。 と`c`そして`k`同等であると宣言された文字列`ck`有効です:```
1
1
c k
1
ck
```正しい結果は```
Test case #1:
ck YES
```音声ではなく生の文字を比較すると、誤った結果が生成されます。`NO`。 

逆の状況も重要です。 と`c`そして`k`同等、`cab`外側の文字は次のとおりであるため、音素回文ではありません。`c`そして`b`、それぞれ異なるサウンドがあります。```
1
1
c k
1
cab
```正しい結果は```
Test case #1:
cab NO
```対応する位置をチェックするのではなく、文字列に既知の同等の文字が含まれているかどうかだけをチェックする不注意な実装では、文字列が誤って受け入れられる可能性があります。 

## アプローチ

 直接的な総当りのアプローチでは、サウンドに相当するペアが保存され、すべての文字列について、両端から中心に向かって位置が調べられます。 2 つの文字が等しい場合、そのペアは直ちに有効になります。 それらが異なる場合は、宣言されたすべての音声ペアをスキャンして、これら 2 つの文字がいずれかのペアを形成しているかどうかを確認します。 一致するペアがない場合、その文字列は音素回文ではありません。 これは正しいです。対称的な位置のペアがすべて同じ音を持つ場合、文字列は正確に音素回文になるからです。 

最悪の場合、テスト ケースには長さ 50 の文字列が 100 個含まれます。文字列ごとに最大 25 の対称位置比較があり、失敗した比較ごとに 13 のサウンド ペアすべてが検査される可能性があります。 それはせいぜい`100 * 25 * 13 = 32,500`1 つのテスト ケースのペア チェック。 これは、指定された範囲内では非常に小さいため、総当たり法でも実際には十分に高速です。 

よりクリーンな解決策は、サウンドの関係がテスト ケース全体で固定されていることに気づくことから生まれます。 ペアのリストを繰り返し検索する代わりに、すべての文字を正規の代表者に割り当てます。 もし`c`そして`k`同じように聞こえますが、両方をマッピングできます`c`。 相手のいない手紙は自分自身にマッピングされます。 したがって、2 つの文字の代表値が等しい場合、その文字はまったく同じ音になります。 

これにより、各対称比較が定数時間の配列検索に変わります。 アルゴリズムは引き続き各文字列を 1 回スキャンしますが、最大 13 ペアの内部検索は行われなくなります。 重要な点は、サウンド情報は静的であるため、文字比較のたびに再検出するのではなく、一度前処理する必要があるということです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(q・L・p) | O(p) | 指定された制限内で受け入れられます |
 | 最適 | O(q · L + p) | お(26) | 承認済み |

 ここ`q`は文字列の数です。`L`は文字列の最大長であり、`p`は音に相当するペアの数です。 

## アルゴリズムのチュートリアル

 1. テスト ケースの数を読み取り、それぞれを個別に処理します。 1 つのテスト ケースからの音の関係が、後のテスト ケースに影響を与えてはなりません。 
2. 26 文字すべての小文字のマッピングを作成し、最初はすべての文字をそれ自体にマッピングします。 これは、文字は常にそれ自体のように聞こえるというデフォルトのルールを表します。 
3. 宣言されたすべてのペアについて`(a, b)`、両方の手紙に同じ代表者を割り当てます。 文字が複数のペアで出現することはないため、単純に選択することができます。`a`を代表として両方を設定します`a`そして`b`に`a`。 
4. 各文字列を読み取り、インデックスを使用してその文字を対称的に比較します。`left`そして`right`。 最初と最後の文字から始めて、両方のインデックスを中心に向かって移動します。 
5. 対称ペアごとに、比較します。`representative[s[left]]`と`representative[s[right]]`。 それらが異なる場合、2 つの文字の音が異なるため、文字列全体が即座に失敗します。 
6. すべての対称ペアが等しい代表値を持つ場合、元の文字列に続いて出力します。`YES`。 不一致が見つかった場合は、元の文字列に続けて出力します。`NO`。 

### なぜ効果があるのか

 不変条件は、2 つの文字が同じ音を持つ場合、正確に同じ表現を持つということです。 最初はすべての文字がそれ自体を表し、宣言された同等のペアごとに両方の文字が同じ代表に割り当てられます。 各文字は最大でも 1 つのペアに属するため、競合する割り当ては発生しません。 

アルゴリズムは、文字列内の対称位置のペアごとに、これらの代表値が等しいかどうかをチェックします。 等しいとは 2 つの文字が同じに聞こえることを意味し、不等号は同じに聞こえないことを意味します。 音素回文は、すべての対称ペアで等しい音を持つことによって正確に定義されるため、そのようなすべての比較が成功した時点を正確に受け入れるのは正しいことです。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t = int(input())
    output = []

    for case_no in range(1, t + 1):
        p = int(input())

        representative = list(range(26))

        for _ in range(p):
            a, b = input().split()
            x = ord(a) - ord('a')
            y = ord(b) - ord('a')

            representative[y] = x
            representative[x] = x

        q = int(input())

        output.append(f"Test case #{case_no}:")

        for _ in range(q):
            s = input().strip()

            left = 0
            right = len(s) - 1
            ok = True

            while left < right:
                x = representative[ord(s[left]) - ord('a')]
                y = representative[ord(s[right]) - ord('a')]

                if x != y:
                    ok = False
                    break

                left += 1
                right -= 1

            output.append(f"{s} {'YES' if ok else 'NO'}")

        output.append("")

    sys.stdout.write("\n".join(output))

if __name__ == "__main__":
    solve()
```の`representative`配列には、小文字ごとに 1 つずつ、合計 26 のエントリがあります。 文字列の代わりに整数のインデックスを保持すると、サウンドの比較が単純な配列検索になります。 

のようなペアの場合、`c k`読まれて、`c`は両方の文字の代表になります。 間の比較`c`そして`k`したがって、同じ整数間の比較となるため、文字自体が異なっていても成功します。 

2 ポインター ループでは、文字列の前半を調べるだけで済みます。 一度`left >= right`、すべての対称ペアはすでにチェックされています。 奇数長の文字列の場合、中央の文字は他の文字と比較されません。これは正しいことです。単一の文字が常にそれ自体と一致するためです。 

以降の位置では失敗した対称ペアを修復できないため、コードは最初の不一致で停止します。 元の文字列は変更されずに保存されるため、必要な出力でそれを正確に再現できます。 

出力はリストに蓄積され、最後に一度書き込まれます。 これにより、繰り返しの書き込みが回避され、各テスト ケースの後に必要な空白行を簡単に配置できるようになります。 

## 実用的な例

 公式サンプル入力には 2 つのテスト ケースが含まれています。 

### サンプル 1```
1
1
c k
6
a
cac
ck
cab
kaak
ckckkcck
```マッピングは`c -> c`、`k -> c`、他のすべての文字はそれ自体にマップされます。 

のために`cac`、外側の文字は両方とも次のように表されます。`c`、真ん中の文字は無関係です。 

| 文字列 | 左 | 右 | 左の音 | 正しい音 | 結果 |
 | --- | --- | --- | --- | --- | --- |
 |`a`| 0 | 0 | | | はい |
 |`cac`| 0 | 2 | c | c | 続ける |
 |`cac`| 1 | 1 | | | はい |
 |`ck`| 0 | 1 | c | c | はい |
 |`cab`| 0 | 2 | c | b | いいえ |
 |`kaak`| 0 | 3 | c | c | 続ける |
 |`kaak`| 1 | 2 | | | はい |
 |`ckckkcck`| 0 | 7 | c | c | 続ける |
 |`ckckkcck`| 1 | 6 | c | c | 続ける |
 |`ckckkcck`| 2 | 5 | c | c | 続ける |
 |`ckckkcck`| 3 | 4 | c | c | 続ける |
 |`ckckkcck`| 4 | 3 | | | はい |

 結果の出力は次のようになります。```
Test case #1:
a YES
cac YES
ck YES
cab NO
kaak YES
ckckkcck YES
```トレースは、生の文字の等価性が不十分である理由を示しています。 で`ck`、登場人物は異なりますが、その代表者は同等です。

 ### サンプル 2```
1
2
a z
x s
5
abbbz
asxz
cx
sxxabzxss
ks
```ここ`a`そして`z`同様に代表者を共有する`x`そして`s`。 

| 文字列 | 対称ペア | 左の音 | 正しい音 | 結果 |
 | --- | --- | --- | --- | --- |
 |`abbbz`|`a`、`z`| | | 続ける |
 |`abbbz`|`b`、`b`| b | b | はい |
 |`asxz`|`a`、`z`| | | 続ける |
 |`asxz`|`s`、`x`| × | × | はい |
 |`cx`|`c`、`x`| c | × | いいえ |
 |`sxxabzxss`|`s`、`s`| × | × | 続ける |
 |`sxxabzxss`|`x`、`s`| × | × | 続ける |
 |`sxxabzxss`|`x`、`x`| × | × | 続ける |
 |`sxxabzxss`|`a`、`z`| | | 続ける |
 |`sxxabzxss`|`b`、`b`| b | b | はい |
 |`ks`|`k`、`s`| k | × | いいえ |

 結果の出力は次のようになります。```
Test case #1:
abbbz YES
asxz YES
cx NO
sxxabzxss YES
ks NO
```この例では、同値対の両方向を実行します。 代表的なマッピングにより、`x`そして`s`元のペアリストを検索することなく交換可能です。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(p + q · L) | マッピングの構築には O(p) かかり、すべての文字列は O(L) 時間で両端からスキャンされます。 
| スペース | お(26) | サウンド マッピングには、出力バッファ | とは別に、小文字ごとに 1 つのエントリが含まれます。 

と`p <= 13`、`q <= 100`、 そして`L <= 50`, 実際の作業量は非常に少ないです。 ブルートフォース ソリューションでも、最大の 1 つのテスト ケースでは 32,500 件のサウンド ペア チェックしか実行されませんが、代表的なマッピングではこれが最大 2,500 件の対称文字比較にさらに削減されます。 このソリューションは、コンテストの問題について報告された 1 秒と 256 MB の制限に問題なく適合します。 

## テストケース

 公式サンプルはコンテスト資料に記載されています。```python
# helper: run solution on input string, return output string
import sys
import io

def solve():
    input = sys.stdin.readline
    t = int(input())
    output = []

    for case_no in range(1, t + 1):
        p = int(input())
        representative = list(range(26))

        for _ in range(p):
            a, b = input().split()
            x = ord(a) - ord('a')
            y = ord(b) - ord('a')
            representative[y] = x
            representative[x] = x

        q = int(input())
        output.append(f"Test case #{case_no}:")

        for _ in range(q):
            s = input().strip()
            left, right = 0, len(s) - 1
            ok = True

            while left < right:
                if representative[ord(s[left]) - 97] != representative[ord(s[right]) - 97]:
                    ok = False
                    break
                left += 1
                right -= 1

            output.append(f"{s} {'YES' if ok else 'NO'}")

        output.append("")

    return "\n".join(output)

def run(inp: str) -> str:
    global input
    old_stdin = sys.stdin
    old_input = input

    sys.stdin = io.StringIO(inp)
    input = sys.stdin.readline

    try:
        return solve()
    finally:
        sys.stdin = old_stdin
        input = old_input

# provided sample 1
sample1 = """1
1
c k
6
a
cac
ck
cab
kaak
ckckkcck
"""

expected1 = """Test case #1:
a YES
cac YES
ck YES
cab NO
kaak YES
ckckkcck YES
"""

assert run(sample1) == expected1, "sample 1"

# provided sample 2
sample2 = """1
2
a z
x s
5
abbbz
asxz
cx
sxxabzxss
ks
"""

expected2 = """Test case #1:
abbbz YES
asxz YES
cx NO
sxxabzxss YES
ks NO
"""

assert run(sample2) == expected2, "sample 2"

# Minimum-size input, a single character.
assert run("""1
1
a b
1
z
""") == """Test case #1:
z YES
""", "single-character string"

# All characters are equivalent in the only declared pair.
assert run("""1
1
a z
4
az
za
aaaa
azaa
""") == """Test case #1:
az YES
za YES
aaaa YES
azaa YES
""", "equivalent outer characters"

# Boundary case where the first comparison fails immediately.
assert run("""1
1
c k
3
cab
babc
kc
""") == """Test case #1:
cab NO
babc NO
kc YES
""", "early mismatch and equivalent pair"

# Maximum-size string and all-equal values.
large = "a" * 50
assert run(f"""1
1
b c
1
{large}
""") == f"""Test case #1:
{large} YES
""", "length 50 all-equal string"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一文字`z`|`z YES`| 文字列の最小長と`left < right`境界 |
 |`az`、`za`、`aaaa`、`azaa`と`a z`同等 | 全て`YES`| 同じ音の異なる文字とすべてが等しい文字 |
 |`cab`、`babc`、`kc`と`c k`同等 |`NO`、`NO`、`YES`| 直接の不一致と同等の境界文字 |
 | 50部`a`|`YES`| 文字列の最大長と同じ文字の繰り返し |

 ## 特殊なケース

 1 文字の文字列の場合、ループは実行されません。`left == right`。 アルゴリズムは文字列をすぐに受け入れます。 例えば、```
1
1
c k
1
a
```生成する`a YES`。 比較する音は 1 つだけであるため、これはまさに音素レベルでの回文の定義です。 

異なるが同等の文字の場合、代表的なマッピングは特別なロジックを使用せずにケースを処理します。 と```
1
1
c k
1
ck
```マッピングには以下が含まれます`representative[c] = c`そして`representative[k] = c`。 したがって、唯一の比較は`c == c`したがって、出力は次のようになります`ck YES`。 

本物の不一致の場合、アルゴリズムは不一致を見つけるとすぐに停止します。 と```
1
1
c k
1
cab
```最初の比較は`c`そして`b`。 彼らの代表者は、`c`そして`b`、アルゴリズムは次のように設定します`ok`false に設定して出力します`cab NO`。 中央の文字を検査する必要はありません。 

偶数長の文字列の場合、すべての文字は対称ペアに属します。 と```
1
1
c k
1
kaak
```最初の比較は`k`に対して`k`で表される`c`に対して`c`、そして2番目は`a`に対して`a`。 どちらも成功し、`kaak YES`。 

奇数長の文字列の場合、中心文字には回文を無効にする対応する文字がありません。 で`cac`、外側`c`文字が一致し、中央`a`決して比較されません。 アルゴリズムは正しく返します`cac YES`。 

最後に、サウンドのペアは独立しています。 と`a z`そして`x s`、間の比較`a`そして`z`間の比較中に成功します`a`そして`x`失敗します。 マッピングはこれらの独立した関係を正確にエンコードするため、キャラクターが無関係なペアのサウンドを誤って継承することはできません。
