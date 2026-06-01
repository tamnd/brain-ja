---
title: "チートシート"
weight: 1
description: "Markdown、ショートコード、前付のライブ プレビューを備えたクイック リファレンス。"
date: 2026-05-02T11:50:02+07:00
---

# チートシート

 各セクションでは、構文とその後のライブ プレビューが表示されます。 
## アラート````markdown
> [!NOTE]
> General info the reader should not miss.
````

 > [!NOTE]
 > 読者が見逃してはならない一般情報。````markdown
> [!TIP]
> Helpful suggestion.
````

 > [!ヒント]
 > 役立つ提案。````markdown
> [!IMPORTANT]
> Must-know info.
````

 > [!重要]
 > 知っておくべき情報。````markdown
> [!WARNING]
> Could cause problems.
````

 > [!警告]
 > 問題が発生する可能性があります。````markdown
> [!CAUTION]
> Risk of harm or data loss.
````

 > [!注意]
 > 損害やデータ損失のリスク。 
＃＃ 詳細````markdown
{{</* details title="Click to expand" */>}}
Hidden content revealed on click.
{{</* /details */>}}
````{{< details title="Click to expand" >}}クリックすると隠されたコンテンツが表示されます。{{< /details >}}

````markdown
{{</* details title="Open by default" open=true */>}}
Visible immediately.
{{</* /details */>}}
````{{< details title="Open by default" open=true >}}すぐに見えます。{{< /details >}}## タブ````markdown
{{</* tabs */>}}
{{</* tab "Go" */>}}
```行きます
 fmt.Println("こんにちは")```
{{</* /tab */>}}
{{</* tab "Python" */>}}
```パイソン
 print("こんにちは")```
{{</* /tab */>}}
{{</* tab "Rust" */>}}
```錆びる
 println!("こんにちは");```
{{</* /tab */>}}
{{</* /tabs */>}}
````{{< tabs >}}
{{< tab "Go" >}}
```go
fmt.Println("hello")
```
{{< /tab >}}
{{< tab "Python" >}}
```python
print("hello")
```
{{< /tab >}}
{{< tab "Rust" >}}
```rust
println!("hello");
```
{{< /tab >}}
{{< /tabs >}}## ステップ````markdown
{{</* steps */>}}

1. **Install Hugo**

   Download the extended binary from gohugo.io.

2. **Clone the repo**

   `git clone git@github.com:tamnd/brain.git`

3. **Run locally**

   `hugo server`

{{</* /steps */>}}
````{{< steps >}}1. **Hugo をインストール**

 gohugo.io から拡張バイナリをダウンロードします。 

2. **リポジトリのクローンを作成します**`git clone git@github.com:tamnd/brain.git`3. **ローカルで実行**`hugo server`

{{< /steps >}}## バッジ````markdown
{{</* badge content="stable" type="info" */>}}
{{</* badge content="beta" type="warning" */>}}
{{</* badge content="deprecated" type="error" */>}}
{{</* badge content="tag" */>}}
````{{< badge content="stable" type="info" >}}
{{< badge content="beta" type="warning" >}}
{{< badge content="deprecated" type="error" >}}
{{< badge content="tag" >}}## 人魚図````markdown
```人魚
 グラフLR
 A[メモを書く] --> B[brain_on.sh が変更を検出]
 B --> C[git コミット + プッシュ]
 C --> D[GitHub Actions ビルド]
 D --> E[GitHub ページでライブ]```
```````mermaid
graph LR
    A[Write note] --> B[brain_on.sh detects change]
    B --> C[git commit + push]
    C --> D[GitHub Actions builds]
    D --> E[Live on GitHub Pages]
```

````markdown
```人魚
 シーケンス図
 クライアント->>サーバー: GET /api/notes
 サーバー-->>クライアント: 200 OK + JSON```
```````mermaid
sequenceDiagram
    Client->>Server: GET /api/notes
    Server-->>Client: 200 OK + JSON
```## KaTeX 数学

 インライン:```markdown
$E = mc^2$
```

$E = mc^2$ブロック (中央揃え):```markdown
$$
\int_0^\infty e^{-x^2}\,dx = \frac{\sqrt{\pi}}{2}
$$
```

$$
\int_0^\infty e^{-x^2}\,dx = \frac{\sqrt{\pi}}{2}
$$## コードブロック````markdown
```行きます
 パッケージメイン

 「fmt」をインポートします

 関数 main() {
 fmt.Println("こんにちは、脳")
 }```
```````go
package main

import "fmt"

func main() {
    fmt.Println("hello, brain")
}
```

```python
def greet(name: str) -> str:
    return f"hello, {name}"
```

```sql
SELECT title, date
FROM notes
WHERE tags @> ARRAY['go']
ORDER BY date DESC;
```

```bash
hugo server --buildDrafts --buildFuture
```## マークダウンのクイックリファレンス

 ### テキスト

 | 構文 | 結果 |
 |----------|----------|
 |`**bold**`| **太字** |
 |`*italic*`| *斜体* |
 | `` `コード` `` | `コード` |
 |`~~strike~~`| ~~ストライク~~ |

 ### リンク```markdown
[external](https://gohugo.io)
[internal]({{</* relref "cheatsheet.md" */>}})
```[外部](https://gohugo.io)

 ### タスクリスト```markdown
- [x] Published cheatsheet
- [x] Added dark mode toggle
- [ ] Write more notes
```- [x] 公開されたチートシート
 - [x] ダークモードの切り替えを追加しました
 - [ ] さらにメモを書きます

 ### テーブル```markdown
| Left | Center | Right |
|:-----|:------:|------:|
| a    |   b    |     c |
| 1    |   2    |     3 |
```| 左 | センター | 右 |
 |:-----|:------:|------:|
 | | b | c |
 | 1 | 2 | 3 |

 ### 脚注```markdown
This is a claim.[^source]

[^source]: The source for this claim.
```これは主張です。[^出典]

 [^出典]: この主張の出典。 
## 前付参照```yaml
title: "Note title"
date: 2026-05-02
weight: 1                    # sidebar order, lower = higher
tags: ["go", "db"]
draft: false

math: true                   # enable KaTeX on this page
```
