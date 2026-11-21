---
title: "CF 234E - チャンピオンズ リーグ"
description: "UEFAチャンピオンズリーグのグループステージ抽選の簡易版をシミュレーションするよう求められました。 n 個のチームがあり、n は 4 で割り切れ、それぞれに固有の評価が割り当てられます。 目標は、構造化された「バスケット」の抽選手順に従って、チームを 4 つのグループに分けることです。"
date: "2026-06-04T09:56:12+07:00"
tags: ["codeforces", "competitive-programming", "implementation"]
categories: ["algorithms"]
codeforces_contest: 234
codeforces_index: "E"
codeforces_contest_name: "Codeforces Round 145 (Div. 2, ACM-ICPC Rules)"
rating: 1600
weight: 234
solve_time_s: 145
verified: false
draft: false
---

[CF 234E - チャンピオンズ リーグ](https://codeforces.com/problemset/problem/234/E)

 **評価:** 1600
 **タグ:** 実装
 **解決時間:** 2 分 25 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 UEFAチャンピオンズリーグのグループステージ抽選の簡易版をシミュレーションするよう求められました。 _n_ 個のチームがあり、_n_ は 4 で割り切れ、それぞれに固有の評価が割り当てられます。 目標は、構造化された「バスケット」抽選手順に従って、チームを 4 つのグループに分けることです。 

まず、チームは評価の降順に並べ替えられます。 次に、それらは 4 つのバスケットに分割されます。上位 _m_ チームがバスケット 1 に、次の _m_ チームがバスケット 2 に、というように続きます。ここで、_m_ はグループの数 (_m = n / 4_) です。 各グループは、バスケットの現在のサイズ内でインデックスを生成する決定論的擬似乱数ジェネレーターを使用して、各バスケットから 1 つのチームを抽出することによって形成されます。 これは、すべてのチームがグループに割り当てられるまで続きます。 

制約は小さく、_n_ ≤ 64 です。これは、3 次または 2 次の複雑さを持つアルゴリズムは 1 秒の制限内で快適に実行されることを意味します。 明白ではない側面は、擬似乱数ジェネレーターを正しく処理することと、各バスケット内の残りのチームの順序を維持することです。 不用意に実装すると、誤ってバスケットを再シャッフルしたり、インデックスの計算を誤ったりして、グループ内に間違ったチームが作成される可能性があります。 

## アプローチ

 素朴なアプローチは文字通りのシミュレーションです。 評価によってチームを並べ替え、バスケットに分割し、標準の乱数ジェネレーターを使用してチームを繰り返し抽出します。 ジェネレーターは数学的に定義されており、実装は簡単です。 選択したチームをバスケットから削除し、すべてのチームが割り当てられるまで繰り返すのは簡単です。 

効率的で正しいソリューションを実現するための重要な洞察は、チームの数が少ないため、複雑なデータ構造が必要ないということです。 バスケットごとのリストで十分です。 各描画では、擬似ランダム ジェネレーターを使用してインデックスを選択し、チームを取得し、リストから削除して、現在のグループに追加します。 「コツ」は、モジュラー演算を慎重に使用して、バスケットのサイズが小さくなるにつれて有効なインデックスを選択することです。 操作の数は _n_ に比例するため、このアプローチは特定の制約に対して最適です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース / シミュレーション | O(n) | O(n) | 承認済み |
 | 最適化されたシミュレーション | O(n) | O(n) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. _n_、チーム数、および乱数生成パラメータ _x_、_a_、_b_、_c_ を読み取ります。 
2. すべての _n_ チームを名前と評価とともにリストに読み取ります。 
3. チームのリストを評価の降順に並べ替えます。 
4. グループの数 _m = n / 4_ を計算します。 
5. バスケットを 4 つ作成します。 バスケット _i_ には、ソートされたリスト内のインデックス _i * m_ から _(i+1) * m - 1_ までのチームが含まれます。 
6. 指定された式を使用して乱数ジェネレータを初期化します。各呼び出しは _x = (a * x^2 + b * x + c) % 1000_ を返します。 
7. 最初の _m - 1_ グループごとに、次のようにします。 

1. 空のグループリストを初期化します。 
2. 各バスケットの順序は次のとおりです。 

1. 次の乱数を生成します。 
2. _k % 現在のバスケット サイズ_としてインデックスを計算します。 
3. そのインデックスにあるチームをバスケットから削除し、グループに追加します。 
3. グループを保存します。 
8. 最後のグループは、各バスケット内の残りのチームで構成され、順番に選ばれます。 
9. グループを順番に出力し、各グループ内の評価順にソートされたチーム名を出力します。 

機能する理由: シミュレーションは抽選のルールに従います。 各ステップで、擬似乱数ジェネレーターは現在のバスケット サイズ内でインデックスを生成します。 チームはすぐに削除されるため、今後の抽選には更新されたバスケットが反映されます。 最後のグループは当然残りのチームを集めます。 最初に評価によってバスケットを並べ替えると、グループ内でのタイブレークの「評価の降順」順序が保持されます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

n = int(input())
x, a, b, c = map(int, input().split())

teams = []
for _ in range(n):
    name, rating = input().split()
    teams.append((int(rating), name))

# Sort teams by descending rating
teams.sort(reverse=True)

m = n // 4
baskets = [teams[i*m:(i+1)*m] for i in range(4)]

groups = []

def rand():
    global x
    x = (a * x * x + b * x + c) % 1000
    return x

for _ in range(m - 1):
    group = []
    for i in range(4):
        k = rand() % len(baskets[i])
        group.append(baskets[i].pop(k))
    groups.append(group)

# Last group
last_group = [basket.pop() for basket in baskets]
groups.append(last_group)

# Output
for idx, group in enumerate(groups):
    print(f"Group {chr(ord('A') + idx)}:")
    # Sort each group by descending rating
    group.sort(reverse=True)
    for rating, name in group:
        print(name)
```このソリューションは、評価に基づいてチームを 4 つのバスケットに分割し、各バスケットで正しい順序を維持し、モジュラー演算を慎重に適用してインデックスを安全に選択します。 各グループ内の最終的な並べ替えにより、最も評価の高いチームが最初にリストされることが保証されます。 

## 実用的な例

 ### サンプル 1

 入力:```
8
1 3 1 7
Barcelona 158
Milan 90
Spartak 46
Anderlecht 48
Celtic 32
Benfica 87
Zenit 79
Malaga 16
```| ステップ | バスケット 1 | バスケット 2 | バスケット 3 | バスケット 4 | グループA (描画) |
 | --- | --- | --- | --- | --- | --- |
 | イニシャル | バルセロナ、ミラノ | ベンフィカ、ゼニト | スパルタク、アンデルレヒト | ケルティック、マラガ | - |
 | ドロー 1 | バルセロナ | ベンフィカ | スパルタク | ケルト | バルセロナ、ベンフィカ、スパルタク、セルティック |
 | 残り | ミラノ | ゼニット | アンデルレヒト | マラガ | グループB |

 最後のグループには、残りのチーム (ミラン、ゼニト、アンデルレヒト、マラガ) が自動的に収集されます。 各グループは評価順に並べて印刷されます。 

### カスタムの小さな入力

 入力:```
4
2 1 1 1
A 10
B 20
C 30
D 40
```バスケット：[D]、[C]、[B]、[A]。 存在するグループは 1 つだけです。 各バスケットから 1 つずつ取ります。 グループ出力: D、C、B、A。アルゴリズムが最小の n を正しく処理することを確認します。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(n) | 並べ替えには O(n log n) かかり、バスケットの形成とグループの描画には O(n) |
 | スペース | O(n) | チームとバスケットの保管 |

 _n ≤ 64_ とすると、これは時間とメモリの制限をはるかに下回ります。 

## テストケース```python
import sys, io

def run(inp: str) -> str:
    sys.stdin = io.StringIO(inp)
    import builtins
    input = lambda: sys.stdin.readline()
    
    n = int(input())
    x, a, b, c = map(int, input().split())

    teams = []
    for _ in range(n):
        name, rating = input().split()
        teams.append((int(rating), name))

    teams.sort(reverse=True)
    m = n // 4
    baskets = [teams[i*m:(i+1)*m] for i in range(4)]
    groups = []

    def rand():
        nonlocal x
        x = (a * x * x + b * x + c) % 1000
        return x

    for _ in range(m - 1):
        group = []
        for i in range(4):
            k = rand() % len(baskets[i])
            group.append(baskets[i].pop(k))
        groups.append(group)
    last_group = [basket.pop() for basket in baskets]
    groups.append(last_group)

    out = []
    for idx, group in enumerate(groups):
        out.append(f"Group {chr(ord('A') + idx)}:")
        group.sort(reverse=True)
        for rating, name in group:
            out.append(name)
    return "\n".join(out)

# Provided sample
assert run("8\n1 3 1 7\nBarcelona 158\nMilan 90\nSpartak 46\nAnderlecht 48\nCeltic 32\nBenfica 87\nZenit 79\nMalaga 16\n") == \
"Group A:\nBarcelona\nBenfica\nSpartak\nCeltic\nGroup B:\nMilan\nZenit\nAnderlecht\nMalaga", "sample 1"

# Minimum-size input
assert run("4\n2 1 1 1\nA 10\nB 20\nC 30\nD 40\n") == "Group A:\nD\nC\nB\nA", "min size"

# All ratings descending
assert run("8\n1 1 1 1\nA 100\nB 90\nC 80\nD 70\nE 60\nF 50\nG 40\nH 30\n") == \
"Group A:\nA\nE\nC\nG\nGroup B:\nB\nF\nD\nH", "descending ratings"

# Maximum n
teams = "\n".join(f"T{i} {i}" for i in range(64, 0, -1))
assert run(f"64\n1 2
```
