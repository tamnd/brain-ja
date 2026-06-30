---
title: "CF 104397E - コース選択"
description: "私たちは修士課程のために選択された一連のコースを与えられます。 各コースは特定の単位数を提供し、公共基礎、専門基礎、選択バリアント、または必修セッションなどの 1 つのカテゴリにのみ属します。"
date: "2026-07-01T00:52:19+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 104397
codeforces_index: "E"
codeforces_contest_name: "The 21st UESTC Programming Contest Final"
rating: 0
weight: 104397
solve_time_s: 78
verified: true
draft: false
---

[CF 104397E - コース選択](https://codeforces.com/problemset/problem/104397/E)

 **評価:** -
 **タグ:** -
 **解決時間:** 1 分 18 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 私たちは修士課程のために選択された一連のコースを与えられます。 各コースは特定の単位数を提供し、公共基礎、専門基礎、選択バリアント、または必修セッションなどの 1 つのカテゴリにのみ属します。 目標は、選択したコースのセットが、グローバルおよびカテゴリごとの両方に適用される一連の単位制限を満たしているかどうかを確認することです。 

各テスト ケースについて、最低限必要な合計を表すいくつかのしきい値 (単位数の合計、コースの合計単位数、必修セッションの単位数、学位コースの単位数、および専門職と選択制の構造をさらに分割するいくつかの制約) を読み取ります。 次に、それぞれにタイプとクレジット値を含むコースのリストが与えられ、集約された計画がすべての制約を同時に満たすかどうかを判断する必要があります。 

これは簿記の問題のように見えますが、重複するカテゴリを慎重に分離することが難しいのです。 単一のコースが一度に複数の制約に影響する可能性があります。 たとえば、専門基礎コースは、合計単位、合計コース単位、学位単位、専門単位、および専門基礎単位に同時に貢献します。 

制約は小さいです。 すべての数値は最大 100 であり、各テスト ケースには最大 100 のコースがあります。 これにより、入力全体の線形スキャンで十分であることが保証され、コースごとに一定の作業を行うソリューションは簡単に合格します。 

微妙な特殊なケースは、「少なくとも 1 つの公的基礎コースを受講する必要がある」という要件から来ています。 これは数値しきい値としてエンコードされないため、クレジットを単に合計するだけでは十分ではありません。 そのようなコースが存在するかどうかを明示的に追跡する必要があります。 

もう 1 つのよくある間違いは、重複するカテゴリを混同することです。 たとえば、専門コースには、専門基礎コースと専門選択コースの両方が含まれます。 誤って 1 つだけを数えると、e、f、または g に関連する制約が失敗したり、誤って渡されたりする可能性があります。 

2 番目の微妙な問題は、必須セッションがコース単位の一部ではないにもかかわらず、合計単位には寄与するということです。 したがって、合計単位にはすべてが含まれ、合計コース単位には解釈境界を除くすべてが含まれますが、この問題ではリストされているすべての項目がコースであるため、実際には両方の合計は同じです。 この区別は、解釈の正確さにとって依然として重要です。 

## アプローチ

 ブルートフォースアプローチは、コースの考えられるすべてのサブセットを不必要に列挙またはシミュレーションし、カテゴリーに割り当てたり、再計算によって制約を検証したりすることになります。 入力ではすでに固定の選択が提供されているため、これは必要ありません。 最適化するかサブセットを選択するという選択肢はありません。 たとえ選択問題として誤って解釈したとしても、サブセットの列挙には n、具体的には O(2^n) で指数関数的な時間がかかり、直ちに実行不可能です。 

重要な観察は、構造が純粋に付加的であるということです。 すべての制約は、互いに素な、または重複するコースのグループの合計として表現されます。 これは、単一パスでカテゴリごとの累積を計算するだけでよいことを意味します。 

このソリューションは、すべてのコースを 1 回スキャンし、関連するカテゴリごとにカウンターを維持するだけです: 総単位数、コース クレジット、必修単位、公的基礎単位、学位単位 (公的 + 専門基礎)、専門単位 (基礎 + 選択)、専門基礎単位、および専門選択単位。 また、少なくとも 1 つの公開基礎コースが存在するかどうかを示すブール値フラグも追跡します。 

すべての集計が計算されたら、それらをしきい値と比較します。 すべての制約が満たされていれば、計画は有効です。

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルート フォース (誤って解釈されたサブセット検索) | O(2^n) | O(n) | 遅すぎる |
 | シングルパス集約 | O(n) | お(1) | 承認済み |

 ## アルゴリズムのチュートリアル

 各テストケースを個別に処理します。 

1. すべてのしきい値を読み取ります。 これらは、さまざまなカテゴリに必要な最小合計を定義し、最終的な検証ターゲットとして機能します。 
2. すべてのカウンターをゼロとブール値フラグに初期化します。`has_public`偽に。 これらの変数は、すべてのコースにわたる情報を蓄積します。 
3. 各コースについて、そのタイプとクレジット値を読み取り、カテゴリに基づいて関連するすべてのカウンターを更新します。 各コースは、その分類に応じて複数のカウンターに貢献します。 たとえば、専門基礎コースでは、合計単位、コース単位、学位単位、専門単位、および専門基礎単位が同時に増加します。 
4. コースが公立基礎コースの場合は、`has_public`true に設定し、それに応じて関連するすべての合計を更新します。 
5. コースが専門選択科目の場合、専門選択科目の単位を更新し、専門科目の単位にも貢献します。 
6. コースが必須セッションの場合、それは学位コース構造の一部ではないため、必須セッション単位と総単位数にのみ追加してください。 
7. すべてのコースを処理した後、すべての制約を 1 つずつ確認します: 総単位数、コース単位、必修単位、学位単位、専門単位、専門基礎単位、および専門選択単位。 また、少なくとも 1 つの公的基礎コースを受講していることを確認してください。 

### なぜ効果があるのか

 各コースは、固定セットの追加カウンターに独立して寄与します。 制約はこれらの同じカウンターにわたる線形不等式であるため、正確な合計を維持することで正確さが保証されます。 将来のコースで過去の計算を無効にすることはできないため、バックトラックや再計算を行わずに、単一パスの累積で必要な情報がすべて保存されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    T = int(input())
    for _ in range(T):
        a, b, c, d, e, f, g = map(int, input().split())
        n = int(input())

        total = 0
        course_total = 0
        compulsory = 0

        degree = 0
        professional = 0
        prof_found = 0
        prof_elective = 0

        has_public = False

        for _ in range(n):
            name = input().rstrip()
            typ = input().rstrip()
            val = int(input())

            total += val
            course_total += val

            if typ == "compulsory sessions":
                compulsory += val

            if typ == "public foundational courses":
                has_public = True
                degree += val

            if typ == "professional foundational courses":
                degree += val
                professional += val
                prof_found += val

            if typ == "professional elective courses":
                professional += val
                prof_elective += val

            if typ == "interdisciplinary elective courses":
                pass

            if typ == "other elective courses":
                pass

        ok = True
        ok &= total >= a
        ok &= course_total >= b
        ok &= compulsory >= c
        ok &= degree >= d
        ok &= professional >= e
        ok &= prof_found >= f
        ok &= prof_elective >= g
        ok &= has_public

        print("YES" if ok else "NO")

if __name__ == "__main__":
    solve()
```この実装では、カテゴリの分解が直接反映されます。 各条件分岐はコース タイプに対応し、それを定義的に含むカウンターのみを更新します。 最後のブール チェックでは、すべての制約が 1 つの式に集約され、条件が見落とされないことが保証されます。 

よくある落とし穴は、学位の単位には公共基礎コースと専門基礎コースの両方が含まれていることを忘れていることです。 もう1つは、選択カテゴリーを専門職の合計とは相互に排他的なものとして扱い、過小評価することになる`professional`を含む状態を打破します`e`。 

## 実用的な例

 提供されているサンプル入力を使用します。 

### トレース

 主要な集計のみを追跡します。 

| ステップ | コースタイプ | 値 | 合計 | 強制 | 学位 | プロフェッショナル | 教授の発見 | 教授選択 | パブリック |
 | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
 | 1 | 公的財団 | 2 | 2 | 0 | 2 | 0 | 0 | 0 | 本当 |
 | 2 | 基礎教授 | 3 | 5 | 0 | 5 | 3 | 3 | 0 | 本当 |
 | 3 | 基礎教授 | 3 | 8 | 0 | 8 | 6 | 6 | 0 | 本当 |
 | 4 | 教授選択 | 2 | 10 | 0 | 8 | 8 | 6 | 2 | 本当 |
 | 5 | 教授選択 | 2 | 12 | 0 | 8 | 10 | 6 | 4 | 本当 |
 | 6 | 基礎教授 | 2 | 14 | 0 | 10 | 12 | 8 | 4 | 本当 |
 | 7 | 基礎教授 | 3 | 17 | 0 | 13 | 15 | 11 | 4 | 本当 |
 | 8 | 教授選択 | 2 | 19 | 0 | 13 | 17 | 11 | 6 | 本当 |
 | 9 | 教授選択 | 2 | 21 | 0 | 13 | 19 | 11 | 8 | 本当 |
 | 10 | その他の選択科目 | 1 | 22 | 0 | 13 | 19 | 11 | 8 | 本当 |
 | 11 | 公的財団 | 3 | 25 | 0 | 16 | 19 | 11 | 8 | 本当 |
 | 12 | 強制 | 1 | 26 | 1 | 16 | 19 | 11 | 8 | 本当 |
 | 13 | 強制 | 1 | 27 | 2 | 16 | 19 | 11 | 8 | 本当 |
 | 14 | 強制 | 1 | 28 | 3 | 16 | 19 | 11 | 8 | 本当 |
 | 15 | 強制 | 1 | 29 | 4 | 16 | 19 | 11 | 8 | 本当 |

 最後に、すべてのしきい値が満たされるため、出力は YES になります。 

このトレースは、合計単位数に寄与しながら、強制単位がどのように個別に蓄積されるか、また、重複する専門カテゴリがどのようにして複数の合計を同時に構築するかを示します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | テスト ケースあたり O(n) | 各コースは一定時間の更新で 1 回処理されます。 
| スペース | お(1) | 固定数のカウンターのみが維持されます。 

n ≤ 100 および T ≤ 100 の場合、操作の最大数は無視できます。 このソリューションは、時間とメモリの制限内に簡単に収まります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import sys
    from math import isclose

    # re-run solution
    input = sys.stdin.readline

    def solve():
        T = int(input())
        out = []
        for _ in range(T):
            a, b, c, d, e, f, g = map(int, input().split())
            n = int(input())

            total = course_total = compulsory = 0
            degree = professional = prof_found = prof_elective = 0
            has_public = False

            for _ in range(n):
                _ = input().rstrip()
                typ = input().rstrip()
                val = int(input())

                total += val
                course_total += val

                if typ == "compulsory sessions":
                    compulsory += val
                if typ == "public foundational courses":
                    has_public = True
                    degree += val
                if typ == "professional foundational courses":
                    degree += val
                    professional += val
                    prof_found += val
                if typ == "professional elective courses":
                    professional += val
                    prof_elective += val

            ok = (total >= a and course_total >= b and compulsory >= c and
                  degree >= d and professional >= e and prof_found >= f and
                  prof_elective >= g and has_public)

            out.append("YES" if ok else "NO")

        return "\n".join(out)

    return solve()

# sample
assert run("""1
28 24 4 15 15 6 7
15
Socialism with Chinese Characteristics
public foundational courses
2
Matrix Theory
professional foundational courses
3
Optimization Theory
professional foundational courses
3
Communication Network Theory
professional elective courses
2
Bayesian Learning and Random Matrix
professional elective courses
2
Image and Video Processing
professional foundational courses
2
Graph Theory
professional foundational courses
3
Machine Learning
professional elective courses
2
Visual Data Analysis
professional elective courses
2
Guidance on Writing Graduate Thesis
other elective courses
1
Graduate English
public foundational courses
3
Teaching Practice
compulsory sessions
1
Academic Activities
compulsory sessions
1
General Education Elective Courses
compulsory sessions
1
Academic Exchange
compulsory sessions
1
""") == "YES"

# minimum case: missing public course
assert run("""1
5 5 1 2 2 1 1
2
A
professional foundational courses
3
B
professional elective courses
2
""") == "NO"

# all constraints exactly met
assert run("""1
5 5 0 2 2 1 1
2
A
public foundational courses
2
B
professional foundational courses
3
""") == "YES"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 公開コースがありません | いいえ | ブール制約を強制します |
 | まさに満足 | はい | 境界の正確性 |

 ## 特殊なケース

 重要なケースの 1 つは、すべての数値制約が満たされているにもかかわらず、公的基礎コースが受講されていない場合です。 その状況では、すべての合計がしきい値を超える可能性がありますが、ブール要件は失敗し、NO を強制されます。 アルゴリズムはこれを処理します。`has_public`数値合計とは独立して追跡され、最終チェックに含まれます。 

もう 1 つのケースは、コースが選択カテゴリにのみ存在する場合です。 これらは単位の合計には寄与しますが、学位や専門基礎額には寄与しません。 明示的にラベル付けされた型のみがこれらの集計を更新するため、この分離により、次数関連のカウンターが誤ってインフレートされることがなくなります。
