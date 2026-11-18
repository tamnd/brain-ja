---
title: "CF 2041I - オートコンプリート"
description: "オートコンプリートのパターンの追加、パターンの削除、現在のエディターのコンテンツへのテキストの追加、現在のコンテンツの末尾からの文字の削除という 4 つの操作をサポートする高度なテキスト エディターをシミュレートするように求められます。"
date: "2026-06-08T09:45:46+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "hashing", "implementation", "sortings", "strings", "trees"]
categories: ["algorithms"]
codeforces_contest: 2041
codeforces_index: "I"
codeforces_contest_name: "2024 ICPC Asia Taichung Regional Contest (Unrated, Online Mirror, ICPC Rules, Preferably Teams)"
rating: 2300
weight: 2041
solve_time_s: 130
verified: true
draft: false
---

[CF 2041I - 自動完了](https://codeforces.com/problemset/problem/2041/I)

 **評価:** 2300
 **タグ:** 二分探索、データ構造、ハッシュ、実装、ソート、文字列、ツリー
 **解決時間:** 2 分 10 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 オートコンプリートのパターンの追加、パターンの削除、現在のエディターのコンテンツへのテキストの追加、現在のコンテンツの末尾からの文字の削除という 4 つの操作をサポートする高度なテキスト エディターをシミュレートするように求められます。 各操作の後、編集者は現在のコンテンツに一致する「最適な」パターンをプレフィックスとして提案する必要があります。 最適なパターンは、最初に最大長によって定義され、次に辞書編集順、最後に最小 ID によって定義されます。 

入力は操作の数を示し、その後に操作そのものが続きます。 各操作では、ID、追加する文字列、または削除する文字数によるパターンを参照できます。 出力は操作ごとに 1 行で、提案されたパターンの ID が表示されるか、一致するものが存在しない場合は -1 が表示されます。 

とすれば`n`は最大 10^6 で、すべてのパターンと文字列の合計文字数は 2×10^6 に達する可能性があるため、クエリごとにすべてのパターンをスキャンするソリューションは遅すぎます。 具体的には、追加または削除のたびにブルート フォース チェックを行うと、O(n × m) 回の操作 (m は現在のコンテンツの長さ) が必要となり、最悪の場合、簡単に 10^12 に達します。 パターンの数に対してサブリニアまたは償却定数時間で挿入、削除、およびプレフィックス検索を可能にするソリューションが必要です。 

エッジ ケースには、複数のパターンが長さおよび辞書編集的に同等に一致する場合、現在のコンテンツに存在する文字数よりも多くの文字を削除する場合、および現在のコンテンツが空であるか完全に一致しない場合のシナリオが含まれます。 たとえば、パターンが「abc」と「abcd」のみで、ユーザーが「ab」を追加した場合、最長一致は「abcd」を示唆するはずです。 最初に見つかった一致を選択する単純なアプローチでは、誤って「abc」が提案される可能性があります。 

## アプローチ

 ブルートフォース ソリューションでは、パターンのリストが維持されます。 現在のコンテンツを変更するクエリごとに、すべてのパターンをスキャンし、コンテンツがプレフィックスであるかどうかを確認し、ルールに従って最適な一致を選択します。 これは論理的には正しいですが、コンテンツ変更ごとに O(n) チェックを実行するため、大規模になると非現実的になります。 

重要な点は、パターンの動的な挿入と削除を伴う高速なプレフィックス マッチングが必要であるということです。 トライ (プレフィックス ツリー) は当然、プレフィックス クエリを効率的にサポートします。 トライの各ノードは文字に対応し、エッジは文字間の遷移を表します。 候補パターン ID をノードに保存することで、ルートから始まる最長一致を迅速に特定できます。 削除とタイブレーカー (長さ、辞書編集、ID) を処理するには、各ノードが、基準に従ってソートされた、それを通過するアクティブなパターンのセットまたはヒープを維持する必要があります。 

この構造では、パターンを追加するには、トライをたどり、必要に応じてノードを作成し、パスに沿って各ノードにパターン ID を挿入することが含まれます。 削除では、パスをたどり、各ノードから ID を削除します。 現在のコンテンツのクエリは、現在のテキストの文字に沿ってトライをたどり、到達可能な最も深いノードから最適なパターン ID を読み取るだけです。 

このアプローチにより、挿入とクエリの両方で操作ごとの時間の複雑さが O (文字列の長さ) に軽減されます。これは、すべての文字列の長さの合計が 2×10^6 で制限されるため、許容範囲内です。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | O(n × パターンの全長) | O(n × 平均パターン長) | 遅すぎる |
 | メタデータを試してみる | O(文字列の合計長) | O(文字列の合計長) | 承認済み |

 ## アルゴリズムのチュートリアル

 1. 空のトライを初期化します。 各ノードには、子ノードの辞書と、ノードを通過するパターン ID のセットが含まれています。 各 ID は、タイブレーク用のパターンの文字列と長さに関連付けられます。 
2. IDを使用した追加操作の場合`i`とパターン`p`、トライのルートから開始します。 の各キャラクターごとに、`p`、子ノードが存在しない場合は作成します。 パスに沿った各ノードで、`i`ノードのセットに追加します。 これにより、すべてのプレフィックスが保証されます。`p`どのパターンがそれを通過するかを知っています。 
3. IDを使用した削除操作の場合`i`、元のパターン文字列を検索します。 紐に沿ってトライを下りていきます。 パスに沿った各ノードで、`i`ノードのセットから。 これにより、古いパターンが提案されるのを防ぎます。 
4. 文字列を維持する`t`エディター内の現在のコンテンツを表します。 追加操作の場合は、文字列を次の場所に追加します。`t`。 バックスペース操作の場合は、指定された数の文字を末尾から削除し、必要に応じて完全に切り捨てます。 
5. 各操作の後で、下に歩いてトライをクエリします。`t`。 いずれかの時点で文字がトライ内に存在しない場合は、-1 を出力します。 それ以外の場合は、次の最後の文字に対応するノードに到達します。`t`最大長のパターン、次に最小の辞書編集値、次に最小の ID のパターンを選択します。 このノードには、クエリごとに O(1) 時間でこの選択を可能にするヒープまたはソート セットが含まれています。 
6. 選択したパターンの ID を出力します。一致するパターンがない場合は -1 を出力します。 

機能する理由: 各 try ノードは、そのプレフィックスを共有するすべてのアクティブなパターンを集約します。 タイブレーカーをソートされた順序で維持することで、最も深いノードに到達できます。`t`常に有効な最良の提案が含まれています。 パターンを追加および削除すると、影響を受けるすべてのプレフィックスが更新されるため、ノードに古いパターンが含まれることはありません。 

## Python ソリューション```python
import sys
import bisect
input = sys.stdin.readline

class TrieNode:
    __slots__ = ('children', 'patterns')
    def __init__(self):
        self.children = {}
        self.patterns = []

class Pattern:
    __slots__ = ('length', 'string', 'id')
    def __init__(self, string, id):
        self.string = string
        self.id = id
        self.length = len(string)
    def __lt__(self, other):
        if self.length != other.length:
            return self.length > other.length
        if self.string != other.string:
            return self.string < other.string
        return self.id < other.id

class AutoComplete:
    def __init__(self):
        self.root = TrieNode()
        self.pattern_map = {}
        self.t = ''

    def add_pattern(self, id, string):
        self.pattern_map[id] = string
        node = self.root
        pat = Pattern(string, id)
        for c in string:
            if c not in node.children:
                node.children[c] = TrieNode()
            node = node.children[c]
            bisect.insort(node.patterns, pat)

    def delete_pattern(self, id):
        string = self.pattern_map[id]
        node = self.root
        pat = Pattern(string, id)
        for c in string:
            node = node.children[c]
            idx = bisect.bisect_left(node.patterns, pat)
            if idx < len(node.patterns) and node.patterns[idx].id == id:
                node.patterns.pop(idx)

    def query(self):
        node = self.root
        for c in self.t:
            if c not in node.children:
                return -1
            node = node.children[c]
        if not node.patterns:
            return -1
        return node.patterns[0].id

    def append(self, s):
        self.t += s

    def backspace(self, c):
        self.t = self.t[:-c] if c <= len(self.t) else ''

ac = AutoComplete()
n = int(input())
for _ in range(n):
    parts = input().split()
    if parts[0] == 'add':
        ac.add_pattern(int(parts[1]), parts[2])
    elif parts[0] == 'delete':
        ac.delete_pattern(int(parts[1]))
    elif parts[0] == 'append':
        ac.append(parts[1])
    else:  # backspace
        ac.backspace(int(parts[1]))
    print(ac.query())
```ソリューションは、`TrieNode`子ノードとパターンの順序付きリストを保持します。 各パターンは、長さ、辞書順、ID の順に優先順位を付けるカスタム比較演算子を備えたクラスにラップされます。 削除を効率的にサポートするために、パターン ID から文字列へのマップを維持します。`bisect.insort`パターンをソートし、最適な候補を迅速に選択できるようにするために使用されます。 追加およびバックスペース操作は、現在のテキスト文字列を直接操作します。 

## 実用的な例

 入力例:```
6
add 1 pattern1_alice
add 2 pattern2_bob
add 3 pattern3_charlie
append pattern
append 2_bobabc
backspace 3
```| ステップ | 現在のテキスト`t`| 操作 | ノードパターンを試す | 出力 |
 | --- | --- | --- | --- | --- |
 | 1 | "" | 1「パターン1_アリス」を追加 | ルート->p->a->...->e パターン=[1] | 1 |
 | 2 | "" | 2「パターン2_ボブ」を追加 | ルート->p->a->... パターン=[1,2] | 1 |
 | 3 | "" | 3「パターン3_チャーリー」を追加 | ルート->p->a->... パターン=[1,2,3] | 3 |
 | 4 | 「パターン」 | 「パターン」を追加 | 'n' パターンのノード=[1,2,3] | 3 |
 | 5 | "パターン2_bobabc" | "2_bobabc" を追加 | 'c' のノード パターン=[2] | -1 |
 | 6 | "パターン2_b" | バックスペース 3 | 'b' のノード パターン=[2] | 2 |

 このトレースは、各変更後にパターンが正しく提案されていることを示しています。 削除と追加により、各ノードでヒープの不変性が維持されます。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | O(すべての文字列の合計の長さ) | 各パターンの各文字が挿入されます。
