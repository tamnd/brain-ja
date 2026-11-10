---
 タイトル：「CF 1575I - 砂漠の幻想」
 説明: 「迷宮は木だ。どの部屋にも価値がある」$ai$、錯覚率と呼ばれます。 エッジを通過する$(x,y)$費用$$max( Queries either change the value of one room or ask for the minimum energy needed to travel between two rooms." date: "2026-06-10T10:56:33+07:00" tags: ["codeforces", "competitive-programming", "data-structures", "trees"] categories: ["algorithms"] codeforces_contest: 1575 codeforces_index: "I" codeforces_contest_name: "COMPFEST 13 - Finals Online Mirror (Unrated, ICPC Rules, Teams Preferred)" rating: 2300 weight: 1575 solve_time_s: 43 verified: false draft: false --- [CF 1575I - Illusions of the Desert](https://codeforces.com/problemset/problem/1575/I) **Rating:** 2300   **Tags:** data structures, trees   **Solve time:** 43s   **Verified:** no   ## Solution ## Problem Understanding The labyrinth is a tree. Every room has a value $a_i$, called its illusion rate. Moving through an edge $(x,y)$ costs$$\max(|a_x+a_y|,\ |a_x-a_y|)。$$
Queries either change the value of one room or ask for the minimum energy needed to travel between two rooms.
Because the graph is a tree, there is exactly one path between any two rooms. A type 2 query is simply asking for the total edge cost along that unique path.
The first challenge is understanding the edge cost formula. For any two numbers $p$ and $q$,
$$\max(|p+q|,\ |p-q|)=|p|+|q|。$$
One way to see this is to assume $|p|\ge |q|$. Then $p+q$ and $p-q$ have absolute values whose maximum is exactly $|p|+|q|$.
After this simplification, every edge $(x,y)$ has weight
$$|a_x|+|a_y|.$$
 更新中に値が変化するため、エッジの重みは動的になります。 
制約は大きく、両方とも$n$そして$q$到達できる$10^5$。 単一のクエリには、次のパスが含まれる場合があります。$O(n)$頂点。 すべてのクエリに対してパス全体を再計算すると、おおよそ次のようになります。$10^{10}$最悪の場合、3 秒の制限をはるかに超えて操作が行われる可能性があります。 クエリごとに対数に近い時間が必要です。 
見逃しやすいいくつかのケースは注目に値します。 
ノードからノード自体へのクエリを考えてみましょう。```
1
|
(only one node)
2 1 1
```エッジは通過されないため、答えは 0 です。パスの重みを盲目的に合計する式は、これを正しく処理する必要があります。
