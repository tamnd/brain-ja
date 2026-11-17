---
title: "CF 200D - プログラミング言語"
description: "プロシージャ宣言のコレクションと変数のコレクションが与えられます。 各プロシージャ宣言は、名前とパラメータ タイプのリストで構成されます。 パラメーターの型は、int、string、または double の具象型のいずれか、またはワイルドカード型 T の場合があります。"
date: "2026-06-03T16:27:03+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "brute-force", "expression-parsing", "implementation"]
categories: ["algorithms"]
codeforces_contest: 200
codeforces_index: "D"
codeforces_contest_name: "Codeforces Round 126 (Div. 2)"
rating: 1800
weight: 200
solve_time_s: 129
verified: true
draft: false
---

[CF 200D - プログラミング言語](https://codeforces.com/problemset/problem/200/D)

 **評価:** 1800
 **タグ:** 二分探索、ブルート フォース、式解析、実装
 **解決時間:** 2 分 9 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 プロシージャ宣言のコレクションと変数のコレクションが与えられます。 

各プロシージャ宣言は、名前とパラメータ タイプのリストで構成されます。 パラメータの型は具体的な型の 1 つである可能性があります。`int`、`string`、 または`double`、またはワイルドカードタイプである可能性があります`T`。 種類`T`任意の変数タイプに一致します。 

その後、プロシージャ呼び出しが行われます。 呼び出しには、プロシージャ名と変数名のリストが含まれます。 すべての変数は既知の型を持っているため、各呼び出しは引数の型の順序付きリストとともにプロシージャ名として見ることができます。 

呼び出しごとに、宣言されたプロシージャと互換性があるプロシージャの数をカウントする必要があります。 

次の 3 つの条件がすべて満たされる場合、プロシージャは互換性があります。 

1. 名前は同じです。 
2. パラメータの数は同じです。 
3. すべての位置について、プロシージャ パラメータのタイプは次のいずれかです。`T`、または、対応する引数の型と正確に一致します。 

主な問題はロジックのマッチングではありません。 難しいのは、入力を正しく解析することです。これは、キーワード、カンマ、括弧の周囲のほぼすべての場所に任意のスペースが表示される可能性があるためです。 

制約はかなり小さいです。 プロシージャ宣言は最大 1000 個、呼び出しは最大 1000 個あります。 すべてのパラメータ リストの長さは最大 5 です。呼び出しとすべてのプロシージャ間の単純な比較には、すでに約$$1000 \times 1000 \times 5 = 5 \cdot 10^6$$最悪の場合の型比較。 

500 万の単純な文字列比較は 2 秒以内に簡単に許容されます。 これは、問題がアルゴリズムの最適化の問題ではなく、主に実装と解析の問題であることを意味します。 

いくつかのエッジケースでは、不注意なパーサーが静かに破壊される可能性があります。 

考慮する：```
void  f ( int , T )
```そして```
f(a,b)
```宣言には、すべてのトークンの前後に任意のスペースが含まれます。 固定位置またはスペースによる単純な分割に基づくパーサーは失敗します。 

もう 1 つの微妙なケースは一致しています`T`。```
void f(T)
void f(int)

int a
f(a)
```正しい答えは次のとおりです。```
2
```どちらの宣言も適切であるためです。 よくある間違いは治療することです`T`ワイルドカードではなく具体的​​なタイプとして。 

もう 1 つの特殊なケースは、パラメーター数が異なることです。```
void f(T)
void f(T,T)

int a
f(a)
```正しい答えは次のとおりです。```
1
```それでも`T`何でも一致する場合でも、パラメータの数は正確に一致する必要があります。 

最後に、プロシージャ名には数字が含まれる場合があります。```
void foo123(int)
```識別子に文字のみが含まれると想定するパーサーは、有効な名前を誤って拒否します。 

## アプローチ

 最も直接的な解決策は、すべての宣言を解析して保存し、呼び出しごとにすべてのプロシージャ宣言をテストすることです。 

単一の比較では 3 つのことを確認します。 名前は一致する必要があります。 パラメータの数は一致する必要があります。 次に、各パラメータの位置は、型の完全な同一性またはワイルドカードのいずれかを満たさなければなりません。`T`。 

この強引なアプローチは、適合性の定義を文字通りに実装しているため、明らかに正しいです。 呼び出しごとにすべての宣言が検査され、ルールを満たす宣言が正確にカウントされます。 

当然の懸念は効率です。 1000 個の宣言と 1000 個の呼び出しがある場合、最大 100 万回の宣言と呼び出しの比較が実行されます。 各宣言に含まれるパラメータは最大 5 つであるため、合計作業量はおよそ 500 万回のパラメータ チェックになります。 それは簡単に十分に速いです。 

高度なインデックス作成、ハッシュ スキーム、またはバイナリ検索は必要ありません。 パターンの解析と照合は最適化できる場合があるため、この問題はいくつかのタグに分類されますが、与えられた制約により直接実装で完全に十分になります。 

実際の所見は、パラメータ数の上限が 5 であるということです。 すべての通話のすべての手順を完全にスキャンしても、制限内に問題なく収まります。 これを理解すると、作業は慎重に解析し、一致ルールを忠実に実装することになります。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(nkL) | O(n + m) | 承認済み |
 | 最適 | O(nkL) | O(n + m) | 承認済み |

 ここ$L \le 5$パラメータの最大数です。 

## アルゴリズムのチュートリアル

 1. すべてのプロシージャ宣言を読みます。 
2. 各宣言について、周囲の空白を削除して解析します。 

- プロシージャ名
 - パラメータタイプの順序付きリスト
 3. すべての宣言をペアとして保存します。 

- プロシージャ名
 - パラメータタイプリスト
 4. すべての変数宣言を読み取ります。 
5. 変数名から変数タイプへのマッピングを保存します。 
6. プロシージャ呼び出しごとに、以下を解析します。 

- 呼び出されるプロシージャ名
 - 変数名の順序付きリスト
 7. 変数テーブルを使用して、変数名を対応する型に変換します。 
8. この呼び出しに対する応答をゼロに初期化します。 
9. すべてのストアド プロシージャ宣言を繰り返します。 
10. 名前が異なる場合は、すぐにスキップします。 
11. パラメータ数が異なる場合は、すぐにスキップします。 
12. 対応する位置を 1 つずつ比較します。 

宣言タイプが次の場合に位置が一致します。`T`または引数の型と等しい場合。 
13. すべての位置が一致する場合、答えをインクリメントします。 
14. 呼び出しの最終カウントを出力します。 

### なぜ効果があるのか

 呼び出しごとに、アルゴリズムは宣言されたすべてのプロシージャを 1 回だけ検査します。 プロシージャは、定義のすべての条件が満たされた場合にのみカウントに影響します。 

名前チェックでは、プロシージャ名が同一であることが強制されます。 長さチェックでは、同一のパラメータ数が強制されます。 位置比較では、宣言に含まれるいずれかの完全一致ルールが適用されます。`T`または具体的な型が等しい。 

すべての宣言がテストされ、すべてのルールが当てはまらない限り宣言はカウントされないため、結果のカウントは適切なプロシージャの数と正確に一致します。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def parse_signature(line):
    line = line.strip()

    if line.startswith("void"):
        line = line[4:].strip()

    pos_l = line.find('(')
    pos_r = line.rfind(')')

    name = line[:pos_l].strip()

    inside = line[pos_l + 1:pos_r].strip()

    if inside == "":
        params = []
    else:
        params = [x.strip() for x in inside.split(',')]

    return name, params

def solve():
    n = int(input())

    procedures = []

    for _ in range(n):
        procedures.append(parse_signature(input()))

    m = int(input())

    var_type = {}

    for _ in range(m):
        parts = input().strip().split()
        var_type[parts[1]] = parts[0]

    k = int(input())

    answers = []

    for _ in range(k):
        name, vars_used = parse_signature(input())

        arg_types = [var_type[v] for v in vars_used]

        cnt = 0

        for proc_name, proc_types in procedures:
            if proc_name != name:
                continue

            if len(proc_types) != len(arg_types):
                continue

            ok = True

            for p, a in zip(proc_types, arg_types):
                if p != "T" and p != a:
                    ok = False
                    break

            if ok:
                cnt += 1

        answers.append(str(cnt))

    sys.stdout.write("\n".join(answers))

if __name__ == "__main__":
    solve()
```パーサーは実装の中で最もデリケートな部分です。 

宣言と呼び出しはどちらも同じ一般構造を共有します。```
name(type1,type2,...)
```唯一の違いは、宣言がキーワードで始まる場合があることです。`void`。 ヘルパー関数は、そのプレフィックスが存在する場合はそれを削除し、プロシージャ名と括弧内のカンマ区切りの内容を抽出します。 

抽出されたすべてのトークンは個別に空白文字を取り除かれます。 これにより、パーサーはカンマ、括弧、キーワードの周囲の任意のスペースの影響を受けなくなります。 

マッチングループは定義に直接続きます。 名前とパラメータ数は早期に拒否できるため、最初にチェックされます。 その後のみ、対応するパラメータの位置を比較します。 

ワイルドカード ルールは次のように実装されます。```
if p != "T" and p != a:
```これは、宣言パラメータがワイルドカードでも正しい具象型でもない場合にのみ拒否されます。 

最大パラメータ数は 5 つだけであるため、追加の最適化は必要ありません。 

## 実用的な例

 ### サンプル 1

 入力:```
4
void f(int,T)
void f(T, T)
void foo123(int,double,string,string)
void p(T,double)
3
int a
string s
double x123
5
f(a,a)
f(s,a)
foo(a,s,s)
f(s,x123)
proc(a)
```手順:

 | 手順 | 種類 |
 | --- | --- |
 | f | [int, T] |
 | f | [T、T] |
 | foo123 | [int、double、string、string] |
 | p | [T、ダブル] |

 変数:

 | 変数 | タイプ |
 | --- | --- |
 | | 整数 |
 | s | 文字列 |
 | x123 | ダブル |

 電話`f(a,a)`:

 | 候補者 | 一致 |
 | --- | --- |
 | f(int,T) | はい |
 | f(T,T) | はい |

 答え = 2。 

電話をかける`f(s,a)`:

 | 候補者 | 一致 |
 | --- | --- |
 | f(int,T) | いいえ |
 | f(T,T) | はい |

 答え = 1。 

電話をかける`foo(a,s,s)`:

 | 候補者 | 一致 |
 | --- | --- |
 | foo123(...) | 名前が一致しません |

 答え = 0。 

このトレースは、プロシージャ名が正確に一致する必要があることと、`T`ワイルドカードとして動作します。 

### カスタム例

 入力:```
3
void g(T)
void g(int)
void g(string)
2
int a
string b
2
g(a)
g(b)
```のために`g(a)`:

 | 候補者 | パラメータのタイプ | 一致 |
 | --- | --- | --- |
 | g(T) | [た] | はい |
 | g(整数) | [int] | はい |
 | g(文字列) | [文字列] | いいえ |

 答え = 2。 

のために`g(b)`:

 | 候補者 | パラメータのタイプ | 一致 |
 | --- | --- | --- |
 | g(T) | [た] | はい |
 | g(整数) | [int] | いいえ |
 | g(文字列) | [文字列] | はい |

 答え = 2。 

この例は、複数の宣言が同じ呼び出しに同時に一致する可能性があることを示しています。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(nkL) | すべての呼び出しですべてのプロシージャがチェックされ、それぞれのパラメータ比較は最大で L ≤ 5 です。 
| スペース | O(n + m) | ストアド プロシージャと変数テーブル |

 と$n \le 1000$、$k \le 1000$、 そして$L \le 5$、合計作業量は最大でも約 500 万回のパラメーター比較です。 これなら制限時間内に余裕で収まります。 宣言と変数の型マッピングのみが保存されるため、メモリの使用量はわずかです。 

## テストケース```python
# helper: run solution on input string, return output string
import io
import sys

def run(inp: str) -> str:
    old_stdin = sys.stdin
    old_stdout = sys.stdout

    sys.stdin = io.StringIO(inp)
    out = io.StringIO()
    sys.stdout = out

    def parse_signature(line):
        line = line.strip()

        if line.startswith("void"):
            line = line[4:].strip()

        l = line.find("(")
        r = line.rfind(")")

        name = line[:l].strip()

        inside = line[l + 1:r].strip()

        if inside:
            params = [x.strip() for x in inside.split(",")]
        else:
            params = []

        return name, params

    n = int(input())
    procedures = [parse_signature(input()) for _ in range(n)]

    m = int(input())
    vars_ = {}

    for _ in range(m):
        t, v = input().split()
        vars_[v] = t

    k = int(input())
    ans = []

    for _ in range(k):
        name, vars_used = parse_signature(input())
        arg_types = [vars_[v] for v in vars_used]

        cur = 0

        for pname, ptypes in procedures:
            if pname != name:
                continue

            if len(ptypes) != len(arg_types):
                continue

            ok = True

            for p, a in zip(ptypes, arg_types):
                if p != "T" and p != a:
                    ok = False
                    break

            if ok:
                cur += 1

        ans.append(str(cur))

    sys.stdin = old_stdin
    sys.stdout = old_stdout

    return "\n".join(ans)

# sample 1
assert run(
"""4
void f(int,T)
void f(T, T)
void foo123(int,double,string,string)
void p(T,double)
3
int a
string s
double x123
5
f(a,a)
f(s,a)
foo(a,s,s)
f(s,x123)
proc(a)
"""
) == "2\n1\n0\n1\n0"

# minimum case
assert run(
"""1
void f(T)
1
int a
1
f(a)
"""
) == "1"

# parameter count mismatch
assert run(
"""2
void f(T)
void f(T,T)
1
int a
1
f(a)
"""
) == "1"

# wildcard and exact match together
assert run(
"""3
void g(T)
void g(int)
void g(string)
2
int a
string b
2
g(a)
g(b)
"""
) == "2\n2"

# many spaces
assert run(
"""1
  void   abc123 (  int , T )   
2
int x
double y
1
 abc123 ( x , y )
"""
) == "1"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 | 単一宣言`T`| 1 | 最小有効インスタンス |
 | 異なるパラメータ数 | 1 | 正確なアリティ一致 |
 | 正確な型とワイルドカード | 2、2 | 複数の一致する宣言 |
 | 過剰な空白 | 1 | 堅牢な解析 |
 | サンプル入力 | 2,1,0,1,0 | 完全な問題行動 |

 ## 特殊なケース

 異なるパラメータ数を持つ宣言を検討してください。```
2
void f(T)
void f(T,T)
1
int a
1
f(a)
```この呼び出しには引数が 1 つあります。 最初の宣言は長さチェックを通過します。 2 番目の宣言は 2 つのパラメーターを期待しているため、すぐに拒否されます。 アルゴリズムは次を出力します。```
1
```それは正しいです。 

ワイルドカード一致を考慮してください。```
2
void f(T)
void f(string)
1
int a
1
f(a)
```通話タイプのリストは次のとおりです。`[int]`。 

のために`f(T)`、比較は成功します。`T`あらゆるタイプを受け入れます。 

のために`f(string)`、比較は失敗します。`string != int`。 

答えは次のとおりです。```
1
```言語規則に一致します。 

数字を含む名前を考慮してください。```
1
void foo123(int)
1
int a
1
foo123(a)
```パーサーは抽出します`foo123`アルファベットのみの制限を課すことなく、名前として使用できます。 宣言は呼び出しと一致し、以下が生成されます。```
1
```これにより、識別子の解析で有効な名前の完全なセットが処理されることが確認されます。
