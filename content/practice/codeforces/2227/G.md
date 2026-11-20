---
title: "CF 2227G - 溺死"
description: "Ta có một mảng số nguyên dương。 あなたのことを考えて、あなたはあなたのことを知っていますか？ Khi đó bộ ba $$(c{i-1},ci,c{i+1})$$ được thay bằng một giá trị duy nhất $$x=c{i-1}-ci+c{i+1}.$$ Mỗi lần thực hiện, độ dài mảng giảm đi đúng 2."
date: "2026-06-01T00:00:00+07:00"
tags: ["codeforces", "competitive-programming", "binary-search", "data-structures", "math"]
categories: ["algorithms"]
codeforces_contest: 2227
codeforces_index: "G"
codeforces_contest_name: "Codeforces Round 1096 (Div. 3)"
rating: 0
weight: 2227
solve_time_s: 177
verified: false
draft: false
---

[CF 2227G - 溺死](https://codeforces.com/problemset/problem/2227/G)

 **評価:** -
 **タグ:** 二分探索、データ構造、数学
 **解決時間:** 2 分 57 秒
 **確認済み:** いいえ

 ## 解決策
 ## 問題の理解

 Ta có một mảng số nguyên dương。 あなたのことを考えて、あなたはあなたのことを知っていますか？ キ ジョ ボー バ$$(c_{i-1},c_i,c_{i+1})$$được thay bằng một giá trị duy nhất$$x=c_{i-1}-c_i+c_{i+1}.$$Mỗi lần thực hiện, độ dài mảng giảm đi đúng 2.

 あなたのことを考えてください だめです。 Nhiệm vụ là đếm bao nhiêu đoạn con của mảng ban đầu là tốt.

 Điểm khó nằm ở chỗ định nghĩa của phép biến đổi có vẻ rất cục bộ, nhưng số cách thực hiện lại khổng lồ。 ヴォイ$n$レントイ$2\cdot 10^5$、トン$n$trên tất cả test cũng đạt$2\cdot 10^5$、nên mọi lời giải dạng xét từng đoạn con rồi mô phỏng đều không khả thi。 あなたのことは何も考えていません。 

あなたのことは何もありません。 Vì mỗi phép giảm làm độ dài giảm đúng 2、tính chẵn lẻ của độ dài không bao giờ thay đổi. Muốn kết thúc ở độ dài 1 thì độ dài ban đầu bắt buộc phải là số lẻ.

 目的:```
[1, 1]
```không có cách nao giảm xuống một phần tử dù mọi giá trị đều nhỏ.

 3. Khi đó chỉ có đúng một phép có thể thực hiện, nên điều kiện trở thành:```
a1 + a3 > a2
```ヴィドゥ:```
[10, 20, 10]
```コン・トゥ・ヴィ$10+10=20$、không phải bất đẳng thức nghiêm。 

あなたのことを考えてください。 chẳng hạn:```
[100, 1, 1]
```あなたのことを考えてください。 ドンナイット。 Nhưng để xử lý các đoạn dài lớn hơn, ta cần một tiêu chuẩn mạnhơn nhiều.

 ## アプローチ

 総当たりの攻撃を行ってください。 Với mỗi đoạn có độ dài lẻ, ta thử tất cả các chuỗi phép giảm có thể xảy ra để kiểm tra xem có thể về một phần tử hay không。 

あなたのことは何もありません。 Vấn đề là số trạng thái tăng theo cấp số nhân. Chỉ riêng việc có$O(n^2)$đoạn con đã khiến bài toán không thể xử lý ở$n=2\cdot10^5$。 

あなたのことは何もありません。 

Địnhĩa tổng luân phiên:$$S(c)=c_1-c_2+c_3-c_4+\cdots$$キーテー$$(c_{i-1},c_i,c_{i+1})$$バイ$$x=c_{i-1}-c_i+c_{i+1},$$đóng góp của ba phần tử nay trong tổng luân phiên chính xác bằng đóng góp của phần tử mới. ヴィ・トゥ・トゥン・ル・アン・フィエン・コン・ジ・ス・モ・ペ・ビン・ジ。 

何も考えずに、自分の人生を歩むことができます。 Do bất biến、tổng luân phiên ban đầu cũng phải dương。 Đây là điều kiện cần.

 Điều bất ngờ là điều kiện đó cũng là điều kiện đủ. Ta có thể chứng minh rằng với một mảng độ dài lẻ, nếu tổng luân phiên dương thì luôn tồn tại ít nhất một phép うp lệ。 Sau khi thực hiện phép đó、tổng luân phiên vẫn dương、và ta tiếp tục quy nạp cho tới khi còn một phần tử。 

Vì thế một đoạn con là tốt khi và chỉ khi:

 1. Độ dài của no là lẻ.
 2. Tổng luân phiên của nó dương。 

あなたの人生は、あなたの人生に影響を与えます。 

Ta đưa tổng luân phiên về dạng prefix sum và đếm các cặp chỉ số bằng Fenwick Tree sau khi nén tọa độ.

 | アプローチ | 時間計算量 | 空間の複雑さ | 評決 |
 | --- | --- | --- | --- |
 | ブルートフォース | 指数関数的関数$O(n^3)$| ラン | 遅すぎる |
 | 最適 |$O(n\log n)$|$O(n)$| 承認済み |

 ## アルゴリズムのチュートリアル

 ### ブーク 1

 Xây dựng 接頭辞の交互合計:$$pref[0]=0$$

$$pref[i]=a_1-a_2+a_3-\cdots+(-1)^{i-1}a_i.$$Khi đó mọi tổng luân phiên của đoạn con đều có thể tính từ hai giá trị 接頭辞。 

### ブック 2

 Xét một đoạn con$[l,r]$có độ dài lẻ。 

Độ dài lẻ tương đương với$l$ヴァ$r$パリティ。 

ヌー$l$lẻ:$$alt(l,r)=pref[r]-pref[l-1].$$タカン:$$pref[r]>pref[l-1].$$ヌー$l$チェン：$$alt(l,r)=pref[l-1]-pref[r].$$タカン:$$pref[r]<pref[l-1].$$### ブック 3

 Đặt:$$p=l-1.$$キジョ:

 ヌー$r$ローティ$p$ラ・チャン・ヴァ・タ・カン$$pref[p]<pref[r].$$ヌー$r$チェンティ$p$ラ・ラ・ヴァ・タ・カン$$pref[p]>pref[r].$$Bài toán trở thành đếm các cặp$(p,r)$với パリティ xác định và bất đẳng thức trên giá trị プレフィックス。 

### ブーク 4

 Nén tọa độ toàn bộ giá trị$pref[i]$。 

Giá trị có thể lớn tới khoảng$2\cdot10^{14}$、nhưng chỉ có$n+1$giá trị khác nhau cần xét。 

### ブック 5

 デュイ・トリ・ハイ・フェンウィック・ツリー。 

フェンウィック thứ nhất lưu các 接頭辞 ở chỉ số chẵn。 

フェンウィック thứ hai lưu các 接頭辞 ở chỉ số lẻ。 

Khi đang xử lý vị trí$r$:

 ヌー$r$lẻ, cần số lượng chỉ số chẵn trước đó có:$$pref[p]<pref[r].$$フェンウィックとの出会いを楽しみにしています。 

ヌー$r$chẵn、cần số lượng chỉ số lẻ trước đó có:$$pref[p]>pref[r].$$話:$$\text{tổng phần tử lẻ đã thấy} - \text{số phần tử}\le pref[r].$$### ブーク 6

 Sau khi cộng đóng góp của vị trí$r$、チェン$pref[r]$フェンウィックがパリティを持っていることを確認します$r$。 

### なぜ効果があるのか

 とても楽しいです。 あなたのことは何も考えていません。 Ngược lại, với độ dài lẻ, nếu tổng luân phiên dương thì luôn tồn tại một phép hợp lệ, nên c thể quy nạp giảm dần tới độ dài 1. Vì vậy điều kiện "độ dài lẻ và tổng luân phiên dương" là tương đương với "đoạn コント」。 

Biến đổi sing mảng prefix cho phép biểu diễn tổng luân phiên của mọi đoạn bằng quan hệ giữa hai giá trị 接頭語。 フェンウィック ツリー クリスマス ツリー chính là số đoạn con tốt。 

## Python ソリューション```python
import sys
input = sys.stdin.readline

class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [0] * (n + 1)

    def add(self, idx, val):
        n = self.n
        while idx <= n:
            self.bit[idx] += val
            idx += idx & -idx

    def sum(self, idx):
        res = 0
        while idx > 0:
            res += self.bit[idx]
            idx -= idx & -idx
        return res

def solve():
    t = int(input())

    for _ in range(t):
        n = int(input())
        a = list(map(int, input().split()))

        pref = [0] * (n + 1)

        for i in range(1, n + 1):
            if i & 1:
                pref[i] = pref[i - 1] + a[i - 1]
            else:
                pref[i] = pref[i - 1] - a[i - 1]

        vals = sorted(set(pref))
        m = len(vals)

        def get_id(x):
            return 1 + __import__("bisect").bisect_left(vals, x)

        even_bit = Fenwick(m)
        odd_bit = Fenwick(m)

        even_cnt = 0
        odd_cnt = 0

        ans = 0

        idx0 = get_id(pref[0])
        even_bit.add(idx0, 1)
        even_cnt = 1

        for r in range(1, n + 1):
            pos = get_id(pref[r])

            if r & 1:
                ans += even_bit.sum(pos - 1)
            else:
                ans += odd_cnt - odd_bit.sum(pos)

            if r & 1:
                odd_bit.add(pos, 1)
                odd_cnt += 1
            else:
                even_bit.add(pos, 1)
                even_cnt += 1

        print(ans)

if __name__ == "__main__":
    solve()
```Phần đầu tiên xây dựng mảng`pref`、chính là tổng luân phiên của tiền tố。 

Sau đó ta nén tọa độ vì các giá trị 接頭辞 có thể rất lớn。 フェンウィック ツリーは、すべての作品を鑑賞することができます。 

ハイ フェンウィック ツリー được tách theo parity của chỉ số 接頭辞。 Đây là chi tiết quan trọng nhất。 Điều kiện của bài toán không chỉ phụ thuộc vào giá trị 接頭語 mà còn phụ thuộc vào parity của hai đầu đoạn con.

 Với chỉ số lẻ、ta đếm số 接頭語 chẵn trước đó nhỏ hơn giá trị hiện tại。 Với chỉ số chẵn、ta đếm số 接頭語 lẻ trước đó lớn hơn giá trị hiện tại。 

64 ビット版。 Trong Python điều nay tự động an toàn.

 ## 実用的な例

 ### 例 1```
a = [10, 20, 10]
```プレフィックス:$$pref=[0,10,-10,0]$$| r | 優先[r] | r のパリティ | クエリ結果 | 合計回答 |
 | --- | --- | --- | --- | --- |
 | 1 | 10 | 奇数 | 1 | 1 |
 | 2 | -10 | 偶数 | 1 | 2 |
 | 3 | 0 | 奇数 | 1 | 3 |

 Kết quả là 3.

 Ba đoạn tốt chính là ba đoạn độ dài 1. Đoạn`[10,20,10]`0 年以内に luân phiên bằng を確認してください。 

### 例 2```
a = [1,1,1,1,1]
```プレフィックス:$$[0,1,0,1,0,1]$$| r | 優先[r] | パリティ | クエリ結果 | 合計 |
 | --- | --- | --- | --- | --- |
 | 1 | 1 | 奇数 | 1 | 1 |
 | 2 | 0 | 偶数 | 1 | 2 |
 | 3 | 1 | 奇数 | 2 | 4 |
 | 4 | 0 | 偶数 | 2 | 6 |
 | 5 | 1 | 奇数 | 3 | 9 |

 Đáp án cuối cùng là 9.

 Ví dụ nay cho thấy những đoạn độ dài 3 và độ dài 5 đều được đếm chính xác thông qua điều kiện tổng luân フィエン・ズオン。 

## 複雑さの分析

 | 測定 | 複雑さ | 説明 |
 | --- | --- | --- |
 | 時間 |$O(n \log n)$| Nén tọa độ và mỗi 接頭辞 thực hiện vài thao tác フェンウィック |
 | スペース |$O(n)$| 接頭語、mảng nén và フェンウィック |

 トン$n$テストを実行してください$2\cdot10^5$。 Với độ phức tạp$O(n\log n)$、 số thao tác thực tế nằm rất xa giới hạn thời gian 2 giây.

 ## テストケース```python
# helper: run solution on input string, return output string
import sys
import io

def run(inp: str) -> str:
    from bisect import bisect_left

    class Fenwick:
        def __init__(self, n):
            self.n = n
            self.bit = [0] * (n + 1)

        def add(self, idx, val):
            while idx <= self.n:
                self.bit[idx] += val
                idx += idx & -idx

        def sum(self, idx):
            res = 0
            while idx > 0:
                res += self.bit[idx]
                idx -= idx & -idx
            return res

    data = io.StringIO(inp)
    t = int(data.readline())
    out = []

    for _ in range(t):
        n = int(data.readline())
        a = list(map(int, data.readline().split()))

        pref = [0]
        cur = 0
        for i, x in enumerate(a, start=1):
            cur += x if i & 1 else -x
            pref.append(cur)

        vals = sorted(set(pref))
        m = len(vals)

        even = Fenwick(m)
        odd = Fenwick(m)

        even_cnt = 1
        odd_cnt = 0

        p0 = bisect_left(vals, pref[0]) + 1
        even.add(p0, 1)

        ans = 0

        for r in range(1, n + 1):
            pos = bisect_left(vals, pref[r]) + 1

            if r & 1:
                ans += even.sum(pos - 1)
            else:
                ans += odd_cnt - odd.sum(pos)

            if r & 1:
                odd.add(pos, 1)
                odd_cnt += 1
            else:
                even.add(pos, 1)
                even_cnt += 1

        out.append(str(ans))

    return "\n".join(out)

# provided samples
assert run(
"""4
3
10 20 10
5
1 1 1 1 1
4
5 1 5 1
1
100
"""
) == "3\n9\n5\n1"

# custom cases
assert run(
"""1
1
7
"""
) == "1"

assert run(
"""1
2
1 1
"""
) == "2"

assert run(
"""1
3
1 100 1
"""
) == "3"

assert run(
"""1
5
2 2 2 2 2
"""
) == "9"
```| テスト入力 | 期待される出力 | 検証内容 |
 | --- | --- | --- |
 |`[7]`|`1`| Kích thước nhỏ nhất |
 |`[1,1]`|`2`| Đoạn độ dài chẵn không được tính |
 |`[1,100,1]`|`3`| Tổng luân phiên không dương |
 |`[2,2,2,2,2]`|`9`| Nhiều 接頭辞 チュンニャウ |

 ## 特殊なケース

 ### ダイチェン

 入力:```
1
2
1 1
```Không có phép nao làm độ dài 2 trở thành độ dài 1 vì mỗi lần chỉ giảm đi đúng 2 phần tử。 トゥアン チュン コン ドン ドゥム ドン`[1,1]`パリティを確認してください。 

ケットクエ:```
2
```chỉ gồm hai đoạn đơn。 

### Tổng luân phiên bằng 0

 入力:```
1
3
10 20 10
```Tổng luân phiên của cả đoạn la:$$10-20+10=0.$$Điều kiện cần là phải dương、nên đoạn độ dài 3 không được tính. Thuật toán chỉ đếm ba đoạn độ dài 1.

 ケットクエ:```
3
```### Tổng luân phiên dương nhưng có vẻ khó giảm

 入力:```
1
5
1 1 1 1 1
```Tổng luân phiên của toàn bộ đoạn là:$$1-1+1-1+1=1.$$Điều kiện lý thuyết khẳng định đoạn này tốt. 考えたこと:```
[1,1,1,1,1]
→ [1,1,1]
→ [1]
```Thuật toán đếm chính xác đoạn độ dài 5 cùng các đoạn độ dài 3 và độ dài 1, cho đáp án 9。
