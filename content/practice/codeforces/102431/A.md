---
title: "CF 102431A - キックスタート"
description: "各テスト ケースには、2019 年のキック スタート ラウンドのスケジュールと今日を表す日付があります。 スケジュールされた日付は任意の順序で表示できます。 厳密に今日以降のできるだけ早い予定日を見つける必要があります。"
date: "2026-08-08T17:14:21+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102431
codeforces_index: "A"
codeforces_contest_name: "2019 China Collegiate Programming Contest Final (CCPC-Final 2019)"
rating: 0
weight: 102431
solve_time_s: 194
verified: true
draft: false
---

[CF 102431A - キックスタート](https://codeforces.com/problemset/problem/102431/A)

 **評価:** -
 **タグ:** -
 **解決時間:** 3 分 14 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 各テスト ケースには、2019 年のキック スタート ラウンドのスケジュールと今日を表す日付があります。 スケジュールされた日付は任意の順序で表示できます。 厳密に今日以降のできるだけ早い予定日を見つける必要があります。 予定されているすべてのラウンドが今日またはそれ以前の場合、2019 年には残りのラウンドがないため、必要な答えは次のようになります。`See you next year`。 

日付には次のような月の名前が使用されます。`Jan`、`Feb`、 そして`Sept`、その後に次のような序数日の数字が続きます。`1st`、`22nd`、 または`31st`。 すべての日付は 2019 年に属するため、2 つの日付を比較するには、月と日を比較するだけで済みます。 各日付をペアに変換できます`(month_number, day)`通常の辞書編集的な比較を使用します。 

1 つのテスト ケースにスケジュールされたラウンドは最大 20 個、テスト ケースは最大 100 個あります。 これは入力サイズが非常に小さいため、テスト ケースごとに数百または数千の単純な操作を実行するアプローチでも快適に高速です。 特に、高度なデータ構造や高度な並べ替えアルゴリズムは必要ありません。 スケジュールされた日付を直接スキャンするには、`O(n)`時間、場所`n <= 20`。 

主な境界条件は、今日自体を次のラウンドとみなしてはならないということです。 たとえば、スケジュールに次の内容が含まれている場合、`Jan 2nd`そして今日は`Jan 2nd`、答えはそうではありません`Jan 2nd`。 それ以降の日付がない場合、正しい結果は次のようになります。`See you next year`。 

2 番目のエッジケースは、次のラウンドが後の月に発生する場合に発生します。 例えば：```
1
2
Jan 31st
Feb 1st
Jan 31st
```正しい出力は次のとおりです。```
Case #1: Feb 1st
```日数のみに基づく比較は誤って優先されます。`Jan 31st`または日付を正しく順序付けできません。 月は比較の一部である必要があります。 

3 番目のエッジケースは、今日が予定されているすべてのラウンドの後で発生します。 例えば：```
1
2
Jan 10th
Mar 20th
Dec 31st
```正しい出力は次のとおりです。```
Case #1: See you next year
```不注意な実装では、後のラウンドが存在するかどうかを明示的にチェックする代わりに、調査した最後の日付を返す可能性があります。 

入力ステートメントは、スケジュールされた日付が異なることを保証するため、スケジュールされた 2 つのラウンドが同じ日付を占めることはできません。 ただし、今日を表す日付は予定された日付の 1 つと一致する可能性があり、この問題では厳密に後のラウンドが求められているため、その一致は除外する必要があります。 

## アプローチ

 最も直接的な総当たりアプローチは、今日以降、明日から 2019 年末までのすべての暦日を検査することです。候補日ごとに、予定されているすべてのラウンドをスキャンし、その候補者が存在するかどうかを確認できます。 最初に一致した日付が答えです。 これが機能するのは、日付が自然に順序付けされており、今日の後に最初に発生する予定日が可能な限り早い日付である必要があるためです。 

年間最大 365 日、スケジュールされたラウンド数は最大で 20 回であるため、パフォーマンスは最大で約`365 * 20 = 7300`テストケースごとの日付比較。 100 のテスト ケース全体で、最大でおよそ 730,000 件の比較が行われ、これは十分に高速です。 したがって、それほどエレガントではないにもかかわらず、この強引な方法は、所定の制約の下では受け入れられます。 

よりクリーンなアプローチは、実際にはカレンダーを列挙する必要がないことを観察することから生まれます。 予定された日付ごとに、次の 1 つの質問をすることができます。この日付は厳密に今日より後の日付ですか? あれば候補です。 すべての候補の中で、最小のものを保持します。 これにより、問題はただちに 1 回のスキャンに軽減されます。`n`予定された日付。 

日付を次のように変換すると比較が簡単になります。`(month, day)`。 例えば、`Mar 24th`になる`(3, 24)`、 その間`Apr 20th`になる`(4, 20)`。 Python はタプルを辞書編集的に比較するため、`(4, 20)`は正しくは次の期間以降とみなされます`(3, 24)`。 

この強引な方法が機能するのは、カレンダーの日数がごくわずかに固定されているにもかかわらず、スケジュールされていない日付にも作業が実行されるためです。 ダイレクト スキャンにより、その不必要な作業が完全に回避されます。 以来`n`がわずか 20 である場合、最適な解決策は、スケジュールされた各ラウンドを 1 回調べて、最も早い有効な候補を維持することです。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース |`O(365n)`|`O(n)`| 受け入れられたが、不必要な作業 |
 | 最適 |`O(n)`|`O(n)`| 承認済み |

 ## アルゴリズムのチュートリアル

 1. 月名を日付順に割り当てて保存します。`Jan`1に、`Feb`から 2 まで。 入力は次を使用します`Sept`そのため、正確なスペルをマッピングに含める必要があります。 
2. 今日の日付をペアに変換します`(month, day)`。 この方法で表された日付は、月が最初の構成要素であり、日が 2 番目の構成要素であるため、別の日付と直接比較できます。 
3. それぞれの内容を読みます。`n`予定された日付を同じに変換します`(month, day)`表現。 出力では次のような元の序数形式を使用する必要があるため、元のテキストも保持します。`Feb 2nd`。 
4. スケジュールされた日付ごとに、`(month, day)`ペアは今日のペアよりも厳密に大きくなります。 今日行われるラウンドは将来のラウンドではないため、厳密な比較が必要です。 
5. 厳密に今日より後のすべての日付の中で、日付のペアが最も小さい日付を保持します。 これはまさに次に予定されているラウンドです。以前の候補者はすべてすでに拒否されているか、後の候補者に置き換えられていますが、残っている最小の候補者が今日以降の最初の予定日となるからです。 
6. 厳密に今日以降の予定日がない場合は、印刷します。`See you next year`。 それ以外の場合は、選択した予定日の元のテキストを印刷します。 

### なぜ効果があるのか

 スケジュールのプレフィックスを処理した後は、不変条件を維持します。`best`は、そのプレフィックス内で厳密に今日以降に発生する最も早いスケジュールされた日付です。 新しい日付が今日以降の場合、それは答えではないため無視されます。 今日より遅くなると、`best`前の候補者が存在しない場合、または代わりの候補者がいない場合`best`現在の候補より前に発生した場合。 すべてのスケジュールされた日付が処理された後、不変式は次のようになります。`best`今日以降に予定されている最も早いラウンドです。 候補者が見つからなかった場合、今日以降は予定されたラウンドは行われません。`See you next year`は正しいです。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

MONTH = {
    "Jan": 1,
    "Feb": 2,
    "Mar": 3,
    "Apr": 4,
    "May": 5,
    "Jun": 6,
    "Jul": 7,
    "Aug": 8,
    "Sept": 9,
    "Oct": 10,
    "Nov": 11,
    "Dec": 12,
}

def parse_date(s):
    month, day = s.split()
    day = int(day.rstrip("stndrh"))
    return MONTH[month], day

def solve():
    t = int(input())

    for case in range(1, t + 1):
        n = int(input())

        schedule = []
        for _ in range(n):
            s = input().strip()
            schedule.append((parse_date(s), s))

        today_text = input().strip()
        today = parse_date(today_text)

        best_date = None
        best_text = None

        for date, text in schedule:
            if date > today:
                if best_date is None or date < best_date:
                    best_date = date
                    best_text = text

        if best_text is None:
            answer = "See you next year"
        else:
            answer = best_text

        print(f"Case #{case}: {answer}")

if __name__ == "__main__":
    solve()
```の`MONTH`辞書は、テキストの月の略称をその時系列の位置に変換します。 これにより、次のような文字列を比較しようとすることが回避されます。`Apr`そして`Feb`、そのアルファベット順はカレンダーの順序とは無関係です。`parse_date`月と日を区切ります。 序数接尾辞を削除すると整数の日が残るため、`2nd`になる`2`そして`24th`になる`24`。 接尾辞は日付比較とは意味的に関係ありません。 

各スケジュール エントリには、正規化された日付のペアと元のテキストの両方が保存されます。 正規化されたペアは比較に使用されますが、出力では必要なスペルと序数接尾辞が保持されるように、元の文字列が直接返されます。 

条件`date > today`重要な境界チェックです。 使用する`>=`今日自体が予定日である場合、誤って今日のラウンドが選択されてしまいます。 2 番目の条件は候補を比較し、最小のものを保持し、次のラウンドの定義を実装します。 

関連する最大値は月番号 12 と日番号 31 であるため、整数オーバーフローの問題はありません。入力も小さいため、標準です。`sys.stdin.readline`必要な I/O ボリュームにはすでに十分すぎるほどです。 

## 実用的な例

 最初のサンプルのスケジュールは次のとおりです。`Jan 1st`、`Feb 2nd`、 そして`Mar 3rd`、今日は`Jan 2nd`。 

| 予定日 | 解析された日付 | 今日より遅いですか？ | 現在のベスト |
 | --- | --- | --- | --- |
 |`Jan 1st`|`(1, 1)`| いいえ | なし |
 |`Feb 2nd`|`(2, 2)`| はい |`Feb 2nd`|
 |`Mar 3rd`|`(3, 3)`| はい |`Feb 2nd`|

 最初の日付は今日より前であるため、破棄されます。`Feb 2nd`が最初の有効な候補です。`Mar 3rd`も将来ですが、現在の候補よりも遅いため、答えは残ります`Feb 2nd`。 

2 番目のサンプルの予定日は次のとおりです。`Mar 24th`、`Apr 20th`、`May 26th`、`Jul 28th`、`Aug 25th`、`Sept 29th`、`Oct 19th`、 そして`Nov 17th`。 今日は`Nov 17th`。 

| 予定日 | 解析された日付 | 今日より遅いですか？ | 現在のベスト |
 | --- | --- | --- | --- |
 |`Mar 24th`|`(3, 24)`| いいえ | なし |
 |`Apr 20th`|`(4, 20)`| いいえ | なし |
 |`May 26th`|`(5, 26)`| いいえ | なし |
 |`Jul 28th`|`(7, 28)`| いいえ | なし |
 |`Aug 25th`|`(8, 25)`| いいえ | なし |
 |`Sept 29th`|`(9, 29)`| いいえ | なし |
 |`Oct 19th`|`(10, 19)`| いいえ | なし |
 |`Nov 17th`|`(11, 17)`| いいえ | なし |

 すべての予定日は今日かそれ以前です。 したがって、候補は空のままになり、`See you next year`。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |`O(n)`テストケースごと | スケジュールされた各ラウンドは 1 回解析され、検査されます。 
| スペース |`O(n)`| スケジュールは元のテキストとともに保存されます。 

と`n <= 20`テスト ケースが最大 100 件の場合、アルゴリズムは合計で数千件の日付比較しか実行しません。 各テスト ケースには最大 20 個のスケジュール エントリが保存されるため、メモリ使用量もわずかです。 

## テストケース```python
import sys
import io

MONTH = {
    "Jan": 1,
    "Feb": 2,
    "Mar": 3,
    "Apr": 4,
    "May": 5,
    "Jun": 6,
    "Jul": 7,
    "Aug": 8,
    "Sept": 9,
    "Oct": 10,
    "Nov": 11,
    "Dec": 12,
}

def parse_date(s):
    month, day = s.split()
    day = int(day.rstrip("stndrh"))
    return MONTH[month], day

def solve():
    input = sys.stdin.readline
    t = int(input())

    out = []

    for case in range(1, t + 1):
        n = int(input())

        schedule = []
        for _ in range(n):
            s = input().strip()
            schedule.append((parse_date(s), s))

        today = parse_date(input().strip())

        best_date = None
        best_text = None

        for date, text in schedule:
            if date > today:
                if best_date is None or date < best_date:
                    best_date = date
                    best_text = text

        if best_text is None:
            answer = "See you next year"
        else:
            answer = best_text

        out.append(f"Case #{case}: {answer}")

    return "\n".join(out) + "\n"

def run(inp: str) -> str:
    old_stdin = sys.stdin
    sys.stdin = io.StringIO(inp)
    try:
        return solve()
    finally:
        sys.stdin = old_stdin

# Sample 1
assert run(
    """1
3
Jan 1st
Feb 2nd
Mar 3rd
Jan 2nd
"""
) == "Case #1: Feb 2nd\n"

# Sample 2
assert run(
    """1
8
Mar 24th
Apr 20th
May 26th
Jul 28th
Aug 25th
Sept 29th
Oct 19th
Nov 17th
Nov 17th
"""
) == "Case #1: See you next year\n"

# Minimum-size input, with today equal to the only scheduled round
assert run(
    """1
1
Jan 1st
Jan 1st
"""
) == "Case #1: See you next year\n"

# Boundary between months, catches day-only comparison mistakes
assert run(
    """1
2
Jan 31st
Feb 1st
Jan 31st
"""
) == "Case #1: Feb 1st\n"

# Today is the earliest date, so the next round is the minimum
# scheduled date strictly after today
assert run(
    """1
4
Dec 31st
Feb 1st
Jan 2nd
Jun 15th
Jan 1st
"""
) == "Case #1: Jan 2nd\n"

# Maximum-size schedule with all 20 scheduled dates distinct
assert run(
    """1
20
Jan 1st
Jan 2nd
Jan 3rd
Jan 4th
Jan 5th
Jan 6th
Jan 7th
Jan 8th
Jan 9th
Jan 10th
Jan 11th
Jan 12th
Jan 13th
Jan 14th
Jan 15th
Jan 16th
Jan 17th
Jan 18th
Jan 19th
Jan 20th
Jan 10th
"""
) == "Case #1: Jan 11th\n"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`n = 1`、予定`Jan 1st`、 今日`Jan 1st`|`See you next year`| 最小サイズと厳密な不等式 |
 |`Jan 31st`、`Feb 1st`、 今日`Jan 31st`|`Feb 1st`| 月の境界での正しい月の順序 |
 | 今日の 4 つの未分類の日付`Jan 1st`|`Jan 2nd`| 最初の入力日ではなく、将来の最小の日付を選択する |
 | 今日は 1 月の 20 の異なる日付`Jan 10th`|`Jan 11th`| 最大`n`と正しいリニアスキャン |
 | 1 つの入力で複数のテスト | 対応する`Case #x`行 | 独立した処理とケース番号付け |

 「すべてが等しい値」という表現には、この問題に対して少しの条件が必要です。 スケジュールされた日付は別個であることが保証されているため、テスト ケースに複数の同一のスケジュールされた日付を法的に含めることはできません。 最も近い有効な等価のケースは、上記の最小サイズのテストのように、今日の日付がスケジュールされた日付と等しい場合です。 この場合は、厳密な値を直接テストするため、ここではより便利です。`>`状態。 

## 特殊なケース

 今日自体が予定日である場合、条件が必要であるため、アルゴリズムはそれを拒否します。`date > today`。 例えば：```
1
2
Jan 2nd
Feb 2nd
Jan 2nd
```

`Jan 2nd`現在と等しいため無視されます。`Feb 2nd`が後から候補となります。 出力は次のとおりです。```
Case #1: Feb 2nd
```将来の日付が月の境界を越える場合、正規化されたペアの両方の要素が重要になります。 のために：```
1
2
Jan 31st
Feb 1st
Jan 31st
```日付は`(1, 31)`そして`(2, 1)`。 まず月を比較するので、`(2, 1)`後です。 答えは、`Feb 1st`日の数字は小さくなりますが。 

すべての予定日が今日より前の場合、候補は保存されません。 のために：```
1
3
Jan 10th
Jun 20th
Nov 30th
Dec 31st
```3つの日付はすべて失敗しました`date > today`テスト。`best_text`残る`None`したがって、アルゴリズムは次のように出力します。```
Case #1: See you next year
```スケジュールがソートされていない場合、アルゴリズムは入力順序に依存しません。 考慮する：```
1
4
Dec 31st
Feb 1st
Jan 2nd
Jun 15th
Jan 1st
```候補者は順番に表示されます`Dec 31st`、`Feb 1st`、`Jan 2nd`、 そして`Jun 15th`。 見た後`Dec 31st`、現状のベストとなります。`Feb 1st`それを置き換えて、`Jan 2nd`再度交換します。 最終的な答えは、`Jan 2nd`。 これがまさに、最初に見つかった将来の日付を返すよりも、将来の最小日付を維持する方が望ましい理由です。 

今日が年の最終日である場合、2019 年にそれ以降の日付は存在しません。例:```
1
1
Dec 31st
Dec 31st
```唯一のスケジュールされた日付は今日に等しいため、除外され、出力は次のようになります。```
Case #1: See you next year
```同じロジックは、12 月の特別なケースを必要とせずに、多くの月の日付を含むスケジュールも処理します。 正規化された`(month, day)`この表現では、2019 年のすべての日付が時系列順に並べられるため、1 つの比較ルールでカレンダー全体を処理できます。
