---
title: "CF 102431J - ワイヤ互換プロトコル バッファ"
description: "protobuf メッセージは、エンコードされたフィールドのシーケンスです。 フィールド名がワイヤ上に表示されることはありません。 フィールドを識別するのはその数値タグであり、ワイヤ タイプはデコーダにそのフィールドに属するバイト数を伝えます。"
date: "2026-08-09T00:07:56+07:00"
tags: ["codeforces", "competitive-programming"]
categories: ["algorithms"]
codeforces_contest: 102431
codeforces_index: "J"
codeforces_contest_name: "2019 China Collegiate Programming Contest Final (CCPC-Final 2019)"
rating: 0
weight: 102431
solve_time_s: 842
verified: true
draft: false
---

[CF 102431J - ワイヤ互換プロトコル バッファ](https://codeforces.com/problemset/problem/102431/J)

 **評価:** -
 **タグ:** -
 **解決時間:** 14 分 2 秒
 **確認済み:** はい

 ## 解決策
 ## 問題の理解

 protobuf メッセージは、エンコードされたフィールドのシーケンスです。 フィールド名がワイヤ上に表示されることはありません。 フィールドを識別するのはその数値タグであり、ワイヤ タイプはデコーダにそのフィールドに属するバイト数を伝えます。 この単純化された問題では、`double`はワイヤ タイプ 1 を使用しますが、両方ともワイヤ タイプ 1 を使用します。`string`埋め込みメッセージはワイヤ タイプ 2 を使用します。 

2 つのメッセージ記述子がワイヤ フォーマット互換であるためには、まったく同じ種類のシリアル化されたフィールド シーケンスを受け入れる必要があります。 これにより、すぐにいくつかのローカル要件が得られます。 特定のタグを持つフィールドは両方のメッセージに存在する必要があります。 そのルールは一致する必要があります。`required`、`optional`、 そして`repeated`異なる数の出現を許可します。 ワイヤレベルのタイプも一致する必要があります。 あ`double`両方ともワイヤ タイプ 2 を使用するという理由だけで、文字列またはメッセージで置き換えることはできません。また、文字列を埋め込みメッセージで置き換えることもできません。 

埋め込みメッセージの場合は、もう 1 つ条件があります。 両方のメッセージに、タイプが別のメッセージであるオプションのフィールド番号 3 があるとします。 2 つの外側のメッセージは、2 つのネストされたメッセージ タイプ自体に互換性がある場合にのみ互換性があります。 これにより、互換性要件の再帰的なグラフが作成されます。 独自のタイプのオプションのフィールドを含むメッセージで示されるように、グラフにはサイクルが含まれる場合があります。 

記述子はテキスト行数として指定され、その後に最大 50,000 個のクエリが続きます。 メッセージ タイプは最大 1,000 個あり、各メッセージには最大 16 個のフィールドが含まれます。 メッセージ数が少ないことが重要な制約です。 これにより、メッセージの数におよそ 2 次の時間を費やすことができますが、クエリの数が多いため、クエリごとにコストのかかる再帰的比較を新たに実行することはできません。 

メッセージをラベル付きの有向グラフ ノードとして表示する便利な方法です。 すべてのフィールドは、タグ、ルール、およびワイヤレベルの種類によってラベル付けされた発信エッジです。 プリミティブ フィールドの場合、エッジは次のような固定端子タイプで終了します。`string`または`double`; メッセージ フィールドの場合は、別のメッセージ ノードで終了します。 2 つのメッセージ ノードは、送信されるラベル付きエッジが一致し、対応するメッセージ エッジが互換性のあるノードにつながる場合に、正確に互換性があります。 

いくつかの特殊なケースは、誤って処理されやすいものです。 

同じタグを持つ異なるフィールド名を検討してください。```
message A {
optional string first = 1 ;
}
message B {
optional string second = 1 ;
}
2
A B
```正しい出力は次のとおりです。```
Wire-format compatible.
```フィールド名を比較する不用意なソリューションではこのペアが拒否されますが、フィールド名はシリアル化されません。 

ここで、異なるタグを持つ同じフィールド名を考えてみましょう。```
message A {
optional string value = 1 ;
}
message B {
optional string value = 2 ;
}
1
A B
```正しい出力は次のとおりです。```
Wire-format incompatible.
```デコーダは数値タグによってフィールドを検索するため、同じ名前は役に立ちません。 

フィールドのルールも重要です。```
message A {
optional string value = 1 ;
}
message B {
repeated string value = 1 ;
}
1
A B
```正しい出力は次のとおりです。```
Wire-format incompatible.
```繰り返しフィールドは複数回出現する可能性がありますが、オプションのフィールドは最大 1 回出現することができます。 デコーダは、一方のスキーマのすべての有効なインスタンスを、もう一方のスキーマの有効なインスタンスとして解釈することはできません。 

最後に、埋め込みメッセージはどちらもワイヤ タイプ 2 を使用するため、単純に文字列として扱うことはできません。```
message Empty {
}
message Holder {
optional Empty value = 1 ;
}
message Text {
optional string value = 1 ;
}
1
Holder Text
```正しい出力は次のとおりです。```
Wire-format incompatible.
```文字列には任意の有効な UTF-8 データを含めることができますが、`Empty`シリアル化されている必要があります`Empty`メッセージはさらに制限されています。 

1,000 メッセージとメッセージごとに 16 フィールドという制限により、(O(M^2F)) 前処理アルゴリズムが実用的になります。ここで、(M) はメッセージ数、(F) はフィールドの最大数です。 最大サイズでは、50,000 のクエリに対して同等の作業を個別に実行するのではなく、リファインメント パス構造ごとに約 1,600 万のフィールド レベルの操作が可能になります。 50,000 クエリの制限は、まさに各クエリを個別に解決することを排除するものです。 

## アプローチ

 直接的なアプローチは、2 つのメッセージ タイプを再帰的に比較することです。 まず、フィールド番号、ルール、およびプリミティブ型のセットを比較します。 両側にメッセージ フィールドが含まれる場合は常に、参照された 2 つのメッセージ タイプを再帰的に比較します。 メッセージのペアのメモ テーブルは、記述子にサイクルが含まれている場合の無限再帰を防ぎます。 

これは正しいです。メッセージの互換性は、対応するフィールドの互換性によって完全に決定されるからです。 ただし、このような比較の状態はメッセージ タイプのペアであるため、最大 (M^2) 個の異なる状態が存在する可能性があります。 (M=1000) の場合、1 つの難しいクエリで最大 100 万のメッセージ ペアにアクセスできます。 ペアあたり最大 16 フィールドの場合、約 1,600 万のフィールド比較が必要になる可能性があります。 これを 50,000 クエリに対して繰り返すと、最悪の場合でも約 8,000 億回のフィールド チェックが行われます。 

このブルート フォース手法は、互換性関係が再帰的であるため機能しますが、多くのクエリに対して同じメッセージ タイプのペアが個別に再検出される可能性があるため失敗します。 より高速な解決策を明らかにする観察は、互換性はすべてのメッセージの完全なラベル付き近傍によって決定される等価関係であるということです。 すべての同値クラスを一度に計算できます。 

まず、直接のワイヤレベル構造に従ってメッセージをクラスに配置します。 フィールドは、そのタグ、そのルール、およびその値が`double`、`string`、または別のメッセージを送信します。 メッセージ値フィールドの場合、ターゲットの ID は一時的に無視されます。 

その後、クラスを繰り返し改良します。 フィールドが別のメッセージを指している場合、ターゲット メッセージを現在のクラス番号で置き換えます。 2 つのメッセージは、それらのフィールドが同一のタグとルール、同一のプリミティブの種類を持ち、対応するメッセージ フィールドが同じ現在のクラスを指している場合にのみ、同じクラスに残ります。 

このプロセスはパーティションのリファインメントです。 各反復ではクラスの分割のみが可能であり、すでに分離されている 2 つのメッセージをマージすることはできません。 メッセージは (M) 個しかないため、最大で (M-1) 個の厳密な調整の後、パーティションは安定する必要があります。 この時点で、2 つのメッセージは、ワイヤ形式と互換性がある場合、まったく同じクラスになります。 すべての反復では前の反復で計算されたクラスのみが使用されるため、循環定義は自然に処理されます。 

1,000 個のメッセージとそれぞれ最大 16 個のフィールドがある場合、単純な絞り込みの実装でも十分に高速です。 前処理の後、すべてのクエリは 2 つの整数クラス識別子の比較にすぎません。 

| アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | (O(QM^2F)) クエリごとのメモ化の最悪のケース | (O(M^2)) クエリごと | 遅すぎる |
 | 最適 | (O(M^2F)) 最悪の場合 | (O(MF)) | 承認済み |

 ここで、(M) はメッセージの数、(F) はメッセージ内のフィールドの最大数、(Q) はクエリの数です。 

## アルゴリズムのチュートリアル

 1. すべてのメッセージを解析し、整数 ID を割り当てます。 各フィールドについて、そのタグ、そのルール、およびそのタイプを保存します。 タイプが他のメッセージの場合は、参照されるメッセージ ID を格納します。 
2. 各メッセージをタグによってソートされたフィールドとして表します。 ソース記述子ではフィールドを任意の順序でリストできますが、フィールドの順序は protobuf シリアル化では意味がないため、タグによるソートにより標準的なローカル順序が得られます。 
3. すべてのメッセージに同じ初期クラスを与えます。 最初は、ネストされたメッセージ タイプの ID を意図的に無視しています。 最初の改良では、ローカルですでに決定できるすべてのものを使用してメッセージを区別します。 
4. 現在のクラスを使用して、すべてのメッセージの署名を作成します。 プリミティブ フィールドの場合、署名にはそのタグ、ルール、およびプリミティブ タイプが含まれます。 メッセージ フィールドの場合、タグ、ルール、メッセージであることを示すマーカー、およびターゲットの現在のクラスが含まれます。 
5. 等しい整数 ID を等しい署名に割り当てます。 これらの ID は、メッセージの新しいパーティションを形成します。 2 つのメッセージの署名が異なる場合、フィールド レベルのワイヤ動作の一部が異なるため、互換性はありません。 同じ署名を持つ場合、互換性の候補として残ります。 
6. クラス割り当てが変更されなくなるまで、署名の構築を繰り返します。 すべてのリファインメントは、少なくとも 1 つの以前は等しいペアを分離するか、固定点に到達します。 メッセージは (M) 個しかないため、最大で (M-1) 個の厳密な絞り込みが可能です。 
7. 各クエリについて、その 2 つのメッセージ名のクラス ID を検索します。 ID が等しいということは、メッセージが再帰的に定義されたワイヤ動作が同一であることを意味するため、次のように出力します。`Wire-format compatible.`それ以外の場合は印刷します`Wire-format incompatible.`### なぜ効果があるのか

 不変条件は、改良を行うたびに、異なるクラスのメッセージにワイヤ形式の互換性を持たせることができないということです。 タグ、ルール、プリミティブ ワイヤ タイプ、またはネストされたメッセージの現在のクラスの違いにより、2 つの記述子が受け入れることができるシリアル化されたデータに具体的な違いが生じます。 

プロセスが安定すると、同じクラス内のすべてのメッセージ ペアに一致するフィールドがあり、対応するすべてのネストされたメッセージ フィールドが同じ安定したクラスを指します。 したがって、2 つのメッセージはまったく同じ再帰互換性条件を満たします。 逆に、互換性のあるフィールドには同じタグ、ルール、プリミティブの種類、または互換性のあるネストされたターゲットがなければならないため、2 つの互換性のあるメッセージを絞り込みによって分離することはできません。 したがって、安定したクラスは、まさにワイヤ形式の互換性クラスになります。 

以前の反復クラスを使用することで、サイクルが安全になります。 たとえば、次の場合`A`オプションが含まれています`A`、その署名は次のクラスを参照します。`A`前回の反復から。 メッセージを再帰的に永久に拡張する必要はありません。 次のような相互再帰的なグループ`B -> C -> B`観察可能なワイヤ構造が同等である場合、結果的に同じクラスに落ち着くことができます。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

def solve():
    n = int(input())
    tokens = []

    for _ in range(n):
        tokens.extend(input().split())

    pos = 0
    messages = []
    name_to_id = {}

    while pos < len(tokens):
        assert tokens[pos] == "message"
        pos += 1

        name = tokens[pos]
        pos += 1

        mid = len(messages)
        name_to_id[name] = mid
        messages.append({
            "name": name,
            "fields": []
        })

        assert tokens[pos] == "{"
        pos += 1

        while tokens[pos] != "}":
            label = tokens[pos]
            typ = tokens[pos + 1]
            field_name = tokens[pos + 2]
            assert tokens[pos + 3] == "="
            tag = int(tokens[pos + 4])
            assert tokens[pos + 5] == ";"
            pos += 6

            messages[mid]["fields"].append(
                [tag, label, typ]
            )

        pos += 1

    # Resolve message type names to integer IDs.
    for msg in messages:
        fields = []
        for tag, label, typ in msg["fields"]:
            if typ == "double":
                fields.append((tag, label, 0, -1))
            elif typ == "string":
                fields.append((tag, label, 1, -1))
            else:
                fields.append((tag, label, 2, name_to_id[typ]))

        fields.sort(key=lambda x: x[0])
        msg["fields"] = fields

    m = len(messages)

    # Initially all messages are in one class.
    cls = [0] * m

    while True:
        signatures = []

        for msg in messages:
            sig = []

            for tag, label, kind, target in msg["fields"]:
                if kind == 2:
                    sig.append((tag, label, kind, cls[target]))
                else:
                    sig.append((tag, label, kind))

            signatures.append(tuple(sig))

        ids = {}
        new_cls = [0] * m

        for i, sig in enumerate(signatures):
            if sig not in ids:
                ids[sig] = len(ids)
            new_cls[i] = ids[sig]

        if new_cls == cls:
            break

        cls = new_cls

    q = int(input())
    out = []

    for _ in range(q):
        a, b = input().split()

        if cls[name_to_id[a]] == cls[name_to_id[b]]:
            out.append("Wire-format compatible.")
        else:
            out.append("Wire-format incompatible.")

    sys.stdout.write("\n".join(out))

if __name__ == "__main__":
    solve()
```パーサーはまず最初の記述子トークンからすべての記述子トークンを読み取ります。`n`線。 中括弧、セミコロン、フィールド名、型名は入力形式に応じてすでにスペースで区切られているため、これは便利です。 したがって、フィールドはラベルの開始後、常にちょうど 6 つのトークンを占めます。 

最初の表現では、参照を解決する前にすべてのメッセージ名を登録できるように、メッセージ タイプ名を文字列として保持します。 解析が完了すると、すべてのメッセージ参照が整数 ID に変換されます。 これにより、絞り込みループ中の辞書検索が回避されます。 

フィールドはタグによってソートされます。 記述子の順序はワイヤ形式に影響を与えないため、これが必要です。 ソートを行わないと、宣言が異なる順序で表示されるという理由だけで、他の点では同一の 2 つのメッセージが異なる署名を受け取る可能性があります。 

整数`kind`は、考えられる 3 つのフィールド値カテゴリを区別します。`0`を表します`double`、`1`を表します`string`、 そして`2`埋め込まれたメッセージを表します。 メッセージ フィールドにはターゲットの現在のクラスが含まれますが、プリミティブ フィールドにはターゲット クラスがありません。 

改良ループは、クラス 0 のすべてのメッセージから始まります。 各反復で、すべてのメッセージは、その現在の監視可能な構造を記述する完全な署名を受け取ります。 等しい署名は等しいクラス ID を受け取ります。 新しいクラス配列が前のクラス配列と同一である場合、パーティションは固定点に達しています。 

比較`new_cls == cls`クラス ID は、ID 順にメッセージをスキャンすることによって決定的に割り当てられるため、安全です。 パーティションが変更されていない場合、対応する署名も変更されていないため、別の反復では何も調整できません。 

Python では整数オーバーフローの問題はありません。 フィールド番号は 536,870,911 まで大きくすることができ、Python の整数で簡単に処理できます。 最大フィールド数は 16 のみであるため、各署名は小さいままです。 

クエリフェーズは意図的に簡単なものになっています。 すべての再帰的な作業は前処理中にすでに実行されているため、最大 50,000 のクエリのそれぞれに一定の時間がかかります。 

## 実用的な例

 ### サンプル 1

 関連するメッセージ構造は次のとおりです。```
Test1:       tag 1, optional, string
Test2:       tag 1, optional, string
Test3:       tag 2, optional, string
Test4:       tag 1, required, string
StringMessage: tag 1, optional, string
Test5:       tag 1, optional, message StringMessage
```精製状況は次のように要約できます。 クラス ID の正確な数値は、署名が割り当てられる順序によって異なりますが、重要なのは ID 間の同等性です。 

| 反復 | メッセージ | 署名形状 | クラス |
 | --- | --- | --- | --- |
 | イニシャル | テスト1 | すべてのメッセージは最初は等しい | 0 |
 | イニシャル | テスト2 | すべてのメッセージは最初は等しい | 0 |
 | イニシャル | テスト3 | すべてのメッセージは最初は等しい | 0 |
 | イニシャル | テスト4 | すべてのメッセージは最初は等しい | 0 |
 | イニシャル | 文字列メッセージ | すべてのメッセージは最初は等しい | 0 |
 | イニシャル | テスト5 | すべてのメッセージは最初は等しい | 0 |
 | 1 | テスト1 |`(1, optional, string)`| 0 |
 | 1 | テスト2 |`(1, optional, string)`| 0 |
 | 1 | テスト3 |`(2, optional, string)`| 1 |
 | 1 | テスト4 |`(1, required, string)`| 2 |
 | 1 | 文字列メッセージ |`(1, optional, string)`| 0 |
 | 1 | テスト5 |`(1, optional, message, class(Test1))`| 3 |
 | 2 | テスト1 | 変更なし | 0 |
 | 2 | テスト2 | 変更なし | 0 |
 | 2 | テスト3 | 変更なし | 1 |
 | 2 | テスト4 | 変更なし | 2 |
 | 2 | 文字列メッセージ | 変更なし | 0 |
 | 2 | テスト5 | 変更なし | 3 |

 その結果、クエリは次のようになります`Test1`そして`Test2`同じクラスでありながら、`Test3`、`Test4`、 そして`Test5`それぞれ異なります。 最初のクエリは、フィールド名が異なっていても、署名内に名前が出現しないため、互換性があります。 

### サンプル 2

 ここでの入れ子構造は次のとおりです。```
A -> B -> C
D -> E
C and E are empty
```改良プロセスは次のように動作します。 

| 反復 | メッセージ | フィールド署名 | クラス |
 | --- | --- | --- | --- |
 | 1 | あ |`(1, optional, message, 0)`| 0 |
 | 1 | B |`(1, optional, message, 0)`| 0 |
 | 1 | C | 空 | 1 |
 | 1 | D |`(1, optional, message, 0)`| 0 |
 | 1 | E | 空 | 1 |
 | 2 | あ |`(1, optional, message, 0)`| 0 |
 | 2 | B |`(1, optional, message, 1)`| 1 |
 | 2 | C | 空 | 2 |
 | 2 | D |`(1, optional, message, 1)`| 1 |
 | 2 | E | 空 | 2 |
 | 3 | あ |`(1, optional, message, 1)`| 0 |
 | 3 | B |`(1, optional, message, 2)`| 1 |
 | 3 | C | 空 | 2 |
 | 3 | D |`(1, optional, message, 2)`| 1 |
 | 3 | E | 空 | 2 |`B`そして`D`どちらもターゲットが空のメッセージである同じオプションのメッセージ フィールドを含むため、最終的には同じクラスを受け取ります。`A`ネストされているため異なります`B`は空のメッセージと同等ではありません。 

これにより、要求された 2 つの回答が得られます。```
B D
```互換性がありますが、```
A D
```互換性がありません。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 | (O(M^2F + Q)) | 最大 (M-1) 個の厳密な絞り込み、それぞれ (M) 個のメッセージと最大 (F) 個のフィールドをスキャンし、その後に定数時間のクエリを実行します。 
| スペース | (O(MF)) | 記述子とメッセージごとに 1 つの署名には、最大 (MF) フィールド エントリが含まれます。 

(M \le 1000) と (F \le 16) を使用すると、前処理限界は、すべての可能な絞り込み反復にわたって最大で 1,600 万フィールド エントリ程度になります。 クエリ フェーズでは、クエリあたり 2 つの配列ルックアップのみで 50,000 のクエリを処理するため、クエリ数が多くても漸近的な前処理コストは変わりません。 

## テストケース```python
import sys
import io

def solve_text(inp: str) -> str:
    data = inp.split()
    p = 0

    n = int(data[p])
    p += 1

    messages = []
    name_to_id = {}

    for _ in range(n):
        assert data[p] == "message"
        p += 1

        name = data[p]
        p += 1

        mid = len(messages)
        name_to_id[name] = mid
        messages.append([])

        assert data[p] == "{"
        p += 1

        while data[p] != "}":
            label = data[p]
            typ = data[p + 1]
            p += 2

            p += 1  # field name

            assert data[p] == "="
            p += 1

            tag = int(data[p])
            p += 1

            assert data[p] == ";"
            p += 1

            messages[mid].append((tag, label, typ))

        p += 1

    for i in range(len(messages)):
        converted = []

        for tag, label, typ in messages[i]:
            if typ == "double":
                converted.append((tag, label, 0, -1))
            elif typ == "string":
                converted.append((tag, label, 1, -1))
            else:
                converted.append((tag, label, 2, name_to_id[typ]))

        converted.sort()
        messages[i] = converted

    m = len(messages)
    cls = [0] * m

    while True:
        groups = {}
        new_cls = [0] * m

        for i in range(m):
            sig = []

            for tag, label, kind, target in messages[i]:
                if kind == 2:
                    sig.append((tag, label, kind, cls[target]))
                else:
                    sig.append((tag, label, kind))

            sig = tuple(sig)

            if sig not in groups:
                groups[sig] = len(groups)

            new_cls[i] = groups[sig]

        if new_cls == cls:
            break

        cls = new_cls

    q = int(data[p])
    p += 1

    ans = []

    for _ in range(q):
        a = data[p]
        b = data[p + 1]
        p += 2

        if cls[name_to_id[a]] == cls[name_to_id[b]]:
            ans.append("Wire-format compatible.")
        else:
            ans.append("Wire-format incompatible.")

    return "\n".join(ans)

def run(inp: str) -> str:
    return solve_text(inp)

sample1 = """18
message Test1 {
optional string field = 1 ;
}
message Test2 {
optional string field_string = 1 ;
}
message Test3 {
optional string field = 2 ;
}
message Test4 {
required string field = 1 ;
}
message StringMessage {
optional string field = 1 ;
}
message Test5 {
optional StringMessage field = 1 ;
}
4
Test1 Test2
Test1 Test3
Test1 Test4
Test1 Test5
"""

assert run(sample1) == """Wire-format compatible.
Wire-format incompatible.
Wire-format incompatible.
Wire-format incompatible.""", "sample 1"

sample2 = """5
message A { optional B nest = 1 ; }
message B { optional C nest = 1 ; }
message C { }
message D { optional E nest = 1 ; }
message E { }
2
B D
A D
"""

assert run(sample2) == """Wire-format compatible.
Wire-format incompatible.""", "sample 2"

sample3 = """1
message A { }
1
A A
"""

assert run(sample3) == """Wire-format compatible.""", "minimum empty message"

sample4 = """3
message A {
optional string x = 536870911 ;
}
message B {
optional string y = 536870911 ;
}
message C {
optional string z = 536870910 ;
}
3
A B
A C
B C
"""

assert run(sample4) == """Wire-format compatible.
Wire-format incompatible.
Wire-format incompatible.""", "maximum field number"

sample5 = """2
message A {
repeated string a = 1 ;
repeated string b = 2 ;
repeated string c = 3 ;
repeated string d = 4 ;
}
message B {
repeated string x = 1 ;
repeated string y = 2 ;
repeated string z = 3 ;
repeated string w = 4 ;
}
2
A B
A A
"""

assert run(sample5) == """Wire-format compatible.
Wire-format compatible.""", "all matching repeated fields"

# A larger generated case, close to the maximum number of messages.
parts = ["1000"]
for i in range(1000):
    parts.append(
        f"message M{i} {{ optional string value = 1 ; }}"
    )
parts.append("3")
parts.append("M0 M999")
parts.append("M123 M456")
parts.append("M0 M0")

large_input = "\n".join(parts) + "\n"

assert run(large_input) == """Wire-format compatible.
Wire-format compatible.
Wire-format compatible.""", "large descriptor"

# Recursive cycle case.
cycle_input = """3
message A { optional A next = 1 ; }
message B { optional C next = 1 ; }
message C { optional B next = 1 ; }
3
A B
A C
B C
"""

assert run(cycle_input) == """Wire-format compatible.
Wire-format compatible.
Wire-format compatible.""", "recursive cycles"
```最小サイズの場合は、空の記述子本体が空の署名を持つ通常のメッセージを生成すること、およびメッセージが常にそれ自体と互換性があることをチェックします。 

最大タグの場合は、最大の有効なフィールド番号が通常の整数として扱われること、およびそれを 1 つ変更するとワイヤレベルのフィールド ID が変更されることをチェックします。 

all-repeat の場合は、宣言でまったく異なる名前が使用されている場合でも、フィールド名が無視され、繰り返されるルールの互換性が維持されるかどうかがチェックされます。 

生成された 1,000 メッセージのケースでは、最大のメッセージ数ディメンションが適用され、前処理によって構造的に同一のすべてのメッセージに対して 1 つのクラスが生成されることが検証されます。 

再帰サイクルのケースでは、単純な再帰拡張ではなく反復改良を使用する主な理由を確認します。 ネストされたメッセージ定義が有限に拡張されない場合でも、アルゴリズムは安定したパーティションに到達します。 

| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`message A { }`、クエリ`A A`| 互換性のある | 空のメッセージと最小限の記述子 |
 | タグ`536870911`対`536870910`| 互換性がある、その後互換性がない | 有効なフィールドの最大数とタグの機密性 |
 | 4 つの繰り返される文字列フィールドを持つ 2 つのメッセージ | 互換性のある | フィールド名は無視され、繰り返されるルールは保持されます。 
| 1,000 の構造的に等しいメッセージ | クエリされたすべてのペアに互換性あり | 最大メッセージ数と前処理 |
 | 3 つの相互に再帰的なメッセージ | 3 つのペアすべてに互換性あり | 循環メッセージの参照 |

 ## 特殊なケース

 異なるフィールド名は、すべての署名からフィールド名を除外することによって処理されます。 のために```
message A { optional string first = 1 ; }
message B { optional string second = 1 ; }
```どちらのメッセージもまったく同じ署名を生成します。`(1, optional, string)`, したがって、彼らは同じクラスに入ります。 アルゴリズムは、記述子のソースレベルの名前ではなく、ワイヤー形式に正確に従います。 

さまざまなタグは、すべてのフィールド署名の最初の要素によって直接処理されます。 のために```
message A { optional string value = 1 ; }
message B { optional string value = 2 ; }
```署名は`(1, optional, string)`そして`(2, optional, string)`。 これらは最初の改良中に分離され、後で互換性が得られることはありません。 

ネストされたメッセージ内を調べなくても、さまざまなルールが同様に表示されます。 オプションのフィールドが貢献します`optional`繰り返しフィールドがその署名に寄与します`repeated`。 その結果```
message A { optional string value = 1 ; }
message B { repeated string value = 1 ; }
```すぐに別れる。 これにより、多重性を忘れてタグとワイヤ タイプのみをチェックするというよくある間違いを回避できます。 

文字列と埋め込みメッセージは両方ともワイヤ タイプ 2 を使用しますが、署名では両者の区別が保持されます。`string`そして`message`。 メッセージ フィールドには、ネストされたターゲットの現在のクラスも含まれます。 したがって、次のような宣言```
message Empty { }
message Holder { optional Empty value = 1 ; }
message Text { optional string value = 1 ; }
```与える`Holder`メッセージ値フィールドと`Text`文字列値のフィールドなので、ワイヤ タイプ番号が両方とも 2 であっても、異なるクラスを受け取ります。 

再帰定義は、Python 実装では再帰なしで処理されます。 のために```
message A { optional A next = 1 ; }
```最初の改良では共通の形状が見られます`(1, optional, message, 0)`。 後続の反復では引き続き同じターゲット クラスが参照されるため、パーティションは安定します。 アルゴリズムは、無限にネストされた表現を構築しようとすることはありません。`A`。 

相互再帰定義も同じ理由で機能します。 で```
message B { optional C next = 1 ; }
message C { optional B next = 1 ; }
```両方のターゲットは最初は同じクラスに属します。 したがって、それらの署名は等しいままであり、ペアはあらゆる改良を通じて一緒に保たれます。 固定小数点は、特別なサイクル検出の場合を必要とするのではなく、再帰的等価性を直接取得します。
