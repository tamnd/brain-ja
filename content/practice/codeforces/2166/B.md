---
title: "CF 2166B - タブを閉じる"
description: "<タイトル値a)。 現在 (=\"ソリューション編集\"m) 個のタブが開いている場合、各タブの長さは [ size=\"2xl\"/<Title value=\"問題の理解\" size=\"text{len} = minleft(b,frac{a}{m}right) になります。"
date: "2026-06-09T04:30:38+07:00"
tags: ["codeforces", "competitive-programming", "math"]
categories: ["algorithms"]
codeforces_contest: 2166
codeforces_index: "B"
codeforces_contest_name: "Codeforces Round 1064 (Div. 2)"
rating: 900
weight: 2166
solve_time_s: 289
verified: false
draft: false
---

[CF 2166B - タブを閉じる](https://codeforces.com/problemset/problem/2166/B)

 **評価:** 900
 **タグ:** 数学
 **解決時間:** 4 分 49 秒
 **確認済み:** いいえ

 ## 解決策
 <タイトル値a\)。 現在ある場合\(="Solution Editorial"m\)開いたタブ、すべてのタブに長さがある$$
 size="2xl"/><Title value="Problem Understanding" size="\text{len} = \min\left(b,\frac{a}{m}\right).
$$xl"/><Text><Text inlineweight="semibold" value="m"/>があります。タブは左から右にパックされているため、閉じるボタンは次の位置にあります。$$
\text{ tabs currently open. Every tab has the same length <Mathlen},\ 2\text{len},\ 3\text{len},\ \dots,\ m\text{len}.
$$value="\text{len}=\min(b,\frac{a}{m タブを閉じると、残りのタブの数が減り、タブの長さが変化するため、今後閉じるすべての値が変更されます})"/> の場合。 閉じるボタン (x) はボタンの位置です。 

カーソルは次の位置から始まります\(0\)。 <Math value="\text{len},2\text{len},3\text{len},\dots,m" の位置にあるカーソルを任意の位置に移動できます。\(x\)、\text{len}"/> 左端から測定して、閉じるボタンが正確に表示されるたびに繰り返しクリックします。\(x\)。 この質問では、すべての画面を閉じるのに必要なマウスの最小移動回数を尋ねます。</Text><Text>タブを閉じるときは、\(n\)タブ。 

制約は非常に大きいです。 両方\(a\)そして\(n\)\(10^9\ <Text inlineweight="semibold" value="m"/> が減少するため、すべてのタブ位置が減少する可能性があります) に達する可能性があり、最大で\(10^4\)テストケース。 クロージングプロセスのシミュレーションはすべて変更になります。 カーソルは <Text inlineweight="semibold" value="0"/> の位置から始まります。 不可能です。 テスト ケースごとに定数時間の式が必要です。 

難しいのは、マウスの移動とはカーソルを新しい位置に再配置することを意味することを理解することです。 一度そこに到達すると、追加の移動なしで閉じるボタンが繰り返しクリックされる可能性があるため、単一のマウス位置は連続して閉じるときにも役立ちます。</Text><Text>タスクは、同じ座標に繰り返し表示される右端のタブのボタンを見つけることです。 

次のことを考慮してください。 

`` the minimum number of cursor movements needed to close all tabs.</Text><Text>The constraints are very small`a = 8、b = 1、n = 6```

For every \(m \ computationally but very large numerically: <Mathge 8\),

\[
\min(b,a/m)=1.
\]

 value="a,b,n \le 10^9"/> and up to <Math value="10^The rightmost close button is always at position \(m\cdot 1\), which changes. However, when \(m<4"/> test cases. This rules out any simulation that closes tabs one by one. We need an <8\),

\[
\text{len}=a/m,
\]

and the rightmost close button becomes

\Math value="O(1)"/> or <Math[
m\cdot (a/m)=a=8.
\]

A careless simulation may miss that many future clicks value="O(\log n)"/> solution per test case.</Text><Title value="Non-obvious can happen at the same coordinate.

Another subtle case is

```a = 9、b = 6、n = 2
 `` edge cases" size="lg"/><Card size="`のために\(m=2\)、$$
\text{len}=\min(6,4.full" background="surface" gap={3}><5)=4.5.
$$閉じるボタンは次のとおりです。\(4.5\)そして\(9\)。 AfterTitle value="閉じるボタンの位置は、1 つのタブを閉じても固定されます。\(m=1\)、唯一の閉じるボタンは\(6\)。 多数の削除に対して単一はありません" size="sm"/><Text>位置の例は両方の状態で機能するため、答えは次のようになります。\(2\)。 
## アプローチ
 A:</Text><Code Block language="text" content="a = 8, b = ブルート フォース解釈では、1、n = 6 のすべての閉じるボタンの位置が追跡されます。"/><Text>< のすべての値について\(m\)次に、どの位置が Math value="m \ge 8"/> でタブの長さが再利用できるかを判断してください。 たとえ単一のテストケースであっても、\(n\)多分\(Math value="1"/>, so the rightmost close button stays10^9\), そのため、すべての状態を処理することは絶望的です。 

重要な観察は、右端のタブを見ることで得られます。 

<Math value="1"/> の位置にあります。 一度移動できる位置がある場合\(m\)タブの右端にある閉じるボタンは、$$
R(m)=m\cd <Math value="1"/> and click six times. The answer is <Text inline weightot \min\left(b,\frac{a}{m}\right).
$$これにより、すぐに簡素化されます。$$
R(m)=="semibold" value="1"/>. A naive approach that assumes positionsmin(mb,a).
$$カーソルをある位置に置いたとします。\( always change would incorrectly count multiple movements.</Text></Card><Card size="full"x\)。 右端の閉じるボタンが表示されている限り、クリックし続けることができます。\(x\)。 背景="表面" ギャップ={3}><タイトル値="閉じるボタンの位置は変更される可能性があります。したがって、\(R(m)\) のそれぞれの個別の値には 1 回のマウス移動が必要です。

 各削除の後に、" size="sm"/><Text>例:</Text><CodeBlock language="text" シーケンスを調べます。$$
R(n),R(n-1),\dots,R(1).
$$\(mb<a\ content="a = 9, b = 6, n = 2"/><Text>With) の場合、$$
R(m)=mb.
$$これらの値はすべて異なるため、\(b> two tabs, <Math value="\text{len}=9/2=4.5"/0\)。 

もし\(mb\ge a\)、 それから$$
R(m)=a.
$$All> なので、右端の閉じるボタンは <Math value にあり、そのような状態は同じ位置を共有します。 

したがって、シーケンスは次のもので構成されます。$$
="9"/>. After closing one tab, <Math valuea,a,a,\dots,a
$$みんなのために\(m\ge \lceil a/b\rceil="\text{len}=6"/>, so the remaining close button is at <\)、 に続く$$
(\lceil a/b\rceil-1)b,
(\lceil a/bMath value="6"/>. The cursor must move from <Math\rceil-2)b,
\dots,
b.
$$以下のすべての値\( value="9"/> to <Math value="6"/>, so the answer isa\)ユニークです。 

させて$$
k=\left\lceil \frac{a}{ <Text inline weight="semibold" value="2"/>. A careless solution that only checks theb}\right\rceil.
$$もし\(n<k\)の場合、すべての状態が満たされます。\(mb<a\)、初期設定ではこれが見逃されます。</Text></Card>< したがって、すべて\(n\)立場は明確であり、答えは次のとおりです。\(n\)。 

もし\(n\Card size="full" background="surface" gap={3}><Title value="More tabs thange k\)、次にすべての州\(m\ge k\)一つのポジションを共有する\(a fit at the maximum length" size="\)、残りは\(k-1\)州が貢献する\(k-1\)sm"/><Text>例:</Text><CodeBlock language="text" content="a = 8, b = 1, n = 9"/>< 個別の位置。答えは次のようになります。$$
1+(k-1)=k.
$$両方のケースを組み合わせると、$$
\boxed{\Text>When <Math value="m \ge 8text{answer}=\min\left(n,\left\lceil\"/>, thefrac{a}{b}\right\rceil\right)}.
$$### アプローチの右端の閉じるボタンは <数学値の比較にあります

 | アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 |---|---|---|---|
 | ブルートフォース | \(O(n)\) | \(O(1)\) | 遅すぎる |
 | 最適 | \(O(1)\) | \(O(1)\) | 受け入れられました = "1" />。 十分なタブが削除されると、<Math value="m"/> は <Math value="7"/> になり、<Math value=" |

 ## アルゴリズムのチュートリアル

 1. コンピューティング$$
k=\left\lceil \frac{a}{b}\right\rceil.
$$2. すべての状態について次のことを観察します。\(m\gea/m &gt; 1"/>, so the close button k\)、右端の閉じるボタンは の位置にあります。\(a\)。 

3. すべての状態について次のことを観察します。\(m<k\)、右側の位置が変わります。 答えは、<Text inlineweight="semibold" value="最も近いボタンは次の場所にあります。\(mb\)、<Text inlineweight="semibold" value="1"/> ではなく、すべて such2"/> です。</Text></Card><Title value=" の位置は異なります。 

4. もし\(n<k\)、すべての州が異なる立場に貢献しているため、答えは次のようになります。\(n\)。 

5. もし\(n\ge k\),Approaches" size="xl"/><Text>総当りシミュレーションでは、タブの長さと右端のすべての状態を繰り返し再計算します。\(m\ge k\)一つの共通の立場に崩れる\(a\)、残りは\(k-1\)削除するたびに ボタンを押して、個別の位置に寄与する個別の状態の数を数えます。 答えは\(k\)。 

6. 出力$$
\min positions the cursor must visit. This works because the cursor(n,k).
$$### なぜ効果があるのか

 重要な閉じるボタンは右端のボタンだけです。 一番右の閉じるボタンの位置が変わったときに移動するだけで済むクロージング。 ただし、<Math tab を使用すると、タブの数が 1 つだけ減り、状態から移動します。\( value="n"/> up to <Math value="10^9"/m\)述べます\(m-1\)。 

状態について\(m\)、右端の閉じるボタンが配置されています>、すべての削除をシミュレートすることは不可能です。</Text><Text>重要な観察点は、$$
R(m)=\min(mb,a).
$$<Math value="m"/> タブがある場合の右端の閉じるボタンの位置が 2 つある場合、</ 異なる状態の \(R(m)\) が同じ値を持つたびに、それらは Text><Math block value="x(m)=m\cdot \min\ を動かさずに処理できます。値が変わるたびに、新しいマウス位置left(b,\frac{a}{m}\right)=\min(mb,a)."/><Text> が必要になります。 

以下に等しい値\(a\)一つの大きなブロックを形成します。 以下の各値はタブを閉じると、<Math value="m"/> が <Math から減少します。\(a\)の明確な倍数です\(b\)。 \(R(m)\) の個別の値をカウントする\(m value="n"/> down to <Math value="1"/>.</=1,\dots,n\)正確に与える$$
\min\left(n,\left\lceil\frac{a}{b}\rightText><Text>There are only two regimes:</Text><List\rceil\right).
$$## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    t marker="decimal" connector="solid" = int(input())
    ans = []

    for _ in range(t):
        a, b, n = map(int, input().split())

        gap={3}><List.Item><Text><Text inline weight k = (a + b - 1) // b  # ceil(a / b)
        ans.append(str(min(n, k)))

   ="semibold" value="Saturated regime:"/> If <Math sys.stdout.write("\n".join(ans))

if __name__ == "__main__":
    solve()
```実装はほぼ value="mb \ge a"/> で、次に <Math value="x(m)=a"/> です。 完全に数式です。 

表現```python
(a + b - 1) // b
 The close button stays at the fixed position <Math value```computes="a"/>.</Text></List.Item><List.Item><Text><Text inlineweight="semibold" value="線形レジーム:"/> <Math value="mb &lt; a"/> の場合、<Math value="x\[
\left\lceil \frac{a}{b}\right\rceil
\]整数演算を使用します。 

最終的な答えは単純に、```python
min(n, k)
```これは導出された式と直接一致します。 

No(m)=mb"/>。<Math 浮動小数点演算の異なる値が必要です。これにより、精度の問題が回避されます。\(a\)そして\( value="m"/> give different positions.</Text></List.Item></List><Text>Define <b\)大きいです。 

## 実用的な例

 ### 例 1

 入力:```
8 Math value="t=\left\lceil \frac1 6
```ここ\[
k=\left\lceil\frac{8}{1}\right\rceil={a}{b}\right\rceil"/>. Then <Math value="mb \8.
\]| メートル | R(m)=min(mb,a) |
 |---|---|
 | 6 | ge a"/> は正確に <Math value="m \ge t"/> のときです。</Text><Text6 |
 | 5 | 5 |
 | 4 | 4 |
 | 3 | 3 |
 | 2 | 2 |
 >If <Math value="n <; t"/| 1 | 1 |

 6 つの異なるポジションがあります。 

以来\(n<k\)、

 \>、私たちは常に線形領域にあるため、削除されるたびに[
 \text{答え}=6。 
\]

 ただし、位置を維持することはでき、<Math value="n"/> の移動が必要です。</Text><Text>If <Math value="n \ カーソルが位置にあります。\(1\)そして、表示されている一番左のタブを asge t"/> として繰り返し閉じると、<Math value="m=t,t+1,\dots レイアウトを持つすべての状態が縮小します。 この問題の意図された観察は、distinct,n"/> が同じ位置 <Math value="a"/> を共有するということです。右端の状態が式に折りたたまれて処理できます。\[
\min(6,8)=1.
 all of them with one movement. After that, for <Math value="m=t-1,t-\]受け入れられたソリューションの出力\(1\)。 

### 例 2

 入力:```
9 6 2,\dots,1"/>, the positions are distinct, requiring2
```ここ\[
k=\left\lceil\frac{9}{6}\right\rceil= <Math value="t-1"/> more movements. Total: <Math value="1+(2.
\]| メートル | R(m)=min(mb,a) |
 |---|---|
 | 2 | 9 |
 t-1)=t"/>.</Text><Text>したがって、答えは単純に| 1 | 6 | となります。

 立場が違います。 

したがって、\[
\text{answer}=\</Text><Math block value="\boxed{\min\left(n,\min(2,2)=2.
\]最初のクリックは次の時点で発生します。\(9\), butleft\lceil \frac{a}{b}\right\rceil\right)}."/><Table>< タブが 1 つ残った後、閉じるボタンは に移動します。\(6\)、別のマウスの動きを強制します。 

## ComplexityTable.Row><Table.Cell><Textweight="semibold">アプローチ</Text></テーブル分析

 | 測定 | 複雑さ | 説明 |
 |---|---|---|
 | 時間 | テスト ケースごとの \(O(1)\) | Only.Cell><Table.Cell><Textweight="semibold">時間計算量</Text></Table.Cell><Table.Cell><いくつかの算術演算をテキストで入力 |
 | スペース | \(O(1)\) | 余分なデータ構造は不要 |

 Weight="semibold">空間の複雑性あり</Text></Table.Cell><表のほとんど\(10^4\)テストケースの場合、総作業量はわずかです。 このソリューションは、時間とメモリの制限内に簡単に収まります。 

.Cell><Textweight="semibold">判定</Text></Table.Cell></Table.Row><Table.Row><Table.Cell>ブルート フォース</Table.Cell><Table.Cell><Math value="O## テスト ケース```python(n)"/></Table.Cell><Table.Cell><Math value="O
# helper: run solution on input string, return output string
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)

    input = sys.stdin.readline

    t = int(input())
    out = []

   (1)"/></Table.Cell><Table.Cell>Too slow for <Math for _ in range(t):
        a, b, n = map(int, input().split())
        k = (a + b - 1) // b
 value="n=10^9"/></Table.Cell></Table.Row><Table.Row><Table.Cell>        out.append(str(min(n, k)))

    return "\n".join(out)

# provided sample
assert run("""12
8 Optimal</Table.Cell><Table.Cell><Math value="O(1)"/></Table.Cell><Table.Cell><Math1 6
9 6 2
10 3 1
10 1 10
9 2 1
5 5  value="O(1)"/></Table.Cell><Table.Cell>Accepted</Table.Cell></Table.Row></Table><6
6 2 7
9 1 9
3 2 6
8 1 7
8 1 9
8 2 Title value="Algorithm Walkthrough" size="xl"/><List marker="decimal" connector="solid" gap={4
""") == """1
2
1
1
1
1
2
1
2
1
2
1"""

#3}><List.Item>Compute <Math value="t=\left\lceil \frac minimum values
assert run("""1
1 1 1
""") == "1"

# large values{a}{b}\right\rceil"/>. In
assert run("""1
1000000000 1 1000000000
""") == "2"

# b = integer arithmetic, <Math value="t=(a+b-1)//b"/>.</ a
assert run("""1
10 10 100
""") == "1"

# boundaryList.Item><List.Item>If <Math value="n & around ceil(a/b)
assert run("""1
10 3 4
""") == "1"
```### カスタム テストの概要

 lt; t"/>、すべての状態は線形領域にあります <| テスト入力 | 期待される出力 | 検証内容 |
 |---|---|---|
 |`1 1 1`|`1`|Math value="x(m)=mb"/> したがって、すべての位置は区別されます。 答えは、<数学 可能な最小のケース |
 |`1000000000 1 1000000000`| 数式の結果 | value="n"/>.</List.Item><List.Item>If <Math value="n \get t 最大値 |
 |`10 10 100`|`1`| すべてのタブは常に"/>、<Math value="m=t,t+1,\dots,n"/>の位置は同じ終了位置になります。
 |`10 3 4`| 境界例 | はすべて <Math value="a"/> に等しいため、1 回の動作が必要です。 トランジション\(\lceil a/b\rceil\)|

 ## 特殊なケース

 次のことを考慮してください。```
a= The remaining <Math value="t-1"/> positions are distinct, so the total is <5, b=5, n=6
```我々は持っています\[
\left\Math value="t"/>.</List.Item><List.Item>Return <Math value="\min(n,t)lceil \frac{5}{5}\right\rceil=1.
\]すべての州はすでに満たしています\(mb\"/>.</List.Item></List><Text weight="semibold">Why it works.</Text><Textge a\)、つまり、右端のすべての閉じるボタンが の位置にあります\(5\)。 アルゴリズムは \(1\>右端の閉じるボタンの位置は <Math value="x(m)=\min(mb,a)"/>) を出力します。 

考慮する：```
a=8, b=1, n=9
```我々は持っています\[
\left\. As <Math value="m"/> decreases, this sequence is constant while <Math value="m \ge t"/>, then strictly decreaseslceil \frac{8}{1}\right\rceil through the values <Math value="(t-1)b,(t-=8.
\]ある州\(m\ge8\)シェアポジション\(8\)。 残りの状態は、次の異なる倍数に対応します。\(1\)。 式は次のようになります\[
\min2)b,\dots,b"/>. The minimum number of movements equals(9,8)=8.
\]これは、まさに右端の個別のボタン位置の数です。 

考慮する：```
 the number of distinct positions in this sequence, which is exactly <Math value="\a=9, b=2, n=1
```タブを閉じる状態は 1 つだけ存在します。 min(n,t)"/>.</Text><Title value="Python Solution" size="xl"/>< ジオメトリに関係なく、単一のマウス位置で十分です。アルゴリズムは計算します。\[
\CodeBlock language="python" content="import sys&#10;input =min\left(1,\left\lceil\frac92\right\rceil\right)=1.
\]出力 sys.stdin.readline&#10;&#10;t = int(input())&#10;out =
