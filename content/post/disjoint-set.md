+++
title = '并查集——我的心得与体会'
date = 2021-07-07T23:16:39+08:00
draft = false
categories = ['笔记']
tags = ['并查集','笔记']
+++

# 并查集模板
众所周知，并查集是一个非常简单的数据结构，在此，我不详细的展开论述。如果你想了解并查集的知识点，建议你去看[这篇博客](https://zhuanlan.zhihu.com/p/93647900/)。

并查集最基本的模板支持查询元素所在集合和合并两个集合。
```cpp
template <int size>
class disjoint_set
{
private:
    int father[size + 1];

public:
    void init()
    {
        for (int i = 1; i <= size; i++)
            father[i] = i;
    }
    int get_father(int node)
    {
        if (node == father[node])
            return node;
        return father[node] = get_father(father[node]);
    }
    void merge(int node_1, int node_2)
    {
        int _1 = get_father(node_1);
        int _2 = get_father(node_2);
        if (_1 != _2)
        {
            father[_1] = _2;
            item[_2] += item[_1];
        }
    }
};
```
此外，并查集还支持查询一个元素所在集合的元素个数，具体方法就是建立一个表示集合元素个数的数组 $Item$，并在每次合并的时候更新元素的个数。方法如下：
```cpp
void merge(int node_1, int node_2)
    {
        int _1 = get_father(node_1);
        int _2 = get_father(node_2);
        if (_1 != _2)
        {
            father[_1] = _2;
            item[_2] += item[_1];
        }
    }
```
查询元素所在集合的元素个数的方法是`item[get_father(node)]`。

注意在合并的过程中赋值和累加的方向是相反的，合并后也只需要对元素个数进行累加，不需要进行清零。因为在 `father[_1] = _2` 这一步中，集合 $Node_1$ 就已经变成了 $Node_2$ 的编号，之后对 $Node_1$ 和 $Node_2$ 中元素的所有查询中，`get_father()` 都只会返回 $Node_2$ 的编号，查询大小自然也是靠查询 $Node_2$ 的编号，$Node_1$ 的编号不会影响到后续的结果。
# 并查集的应用
## 1. 直接应用
这个就不必多说了，并查集最基本的用途就是提供一个可以合并集合，查询元素是否在同一集合的快速数据结构。

这一类问题的代表是 [亲戚问题 (P1551)](https://www.luogu.com.cn/problem/P1551)。
## 2. 查询关系间的矛盾
并查集还可以用来查询一些关系以及关系的矛盾，比较典型的是把一批数据分成两组，其中这些数据之间有着或多或少的联系，问这组数据能不能这样被分成两组。

数据之间的关系又可以这样被分成两类：
1. 同类型：两组数据必须置于同一组别。
2. 矛盾型：两组数据不能置于同一组别。

通过建立一个并查集，记录每一个元素所在的集合，可以解决这种关系是否矛盾的问题。第一类的关系比较简单，只需要将关系涉及的两组数据进行合并就可以记录。而第二类的关系稍微复杂一些，为了记录第二类的关系，需要开两倍大小的并查集，对于每一个元素 $x$ 记录一个虚拟的元素 $x' = x+n$，将一个元素和另外一个元素所对应虚拟元素互相连接，可以记录第二种状态。

在每次记录之前，先查询这个关系是否与之前的关系矛盾。查询和记录关系的顺序很多时候可以与贪心结合起来。

这一类问题的代表是 [关押罪犯](https://www.luogu.com.cn/problem/P1525)

这道题除了是查询关系矛盾问题的一个很好的模板，还稍微利用了贪心的思想。
```cpp
#include <iostream>
#include <algorithm>
using namespace std;
template <int size>
class disjoint_set
{
private:
    int father[size + 1];

public:
    void init()
    {
        for (int i = 1; i <= size; i++)
            father[i] = i;
    }
    int get_father(int node)
    {
        if (node == father[node])
            return node;
        return father[node] = get_father(father[node]);
    }
    void merge(int node_1, int node_2)
    {
        int _1 = get_father(node_1);
        int _2 = get_father(node_2);
        if (_1 != _2)
        {
            father[_1] = _2;
            item[_2] += item[_1];
        }
    }
};
int n, m;
struct relation
{
    int a;
    int b;
    int v;
} r[200007];
bool comp(relation a, relation b)
{
    return a.v > b.v;
}
disjoint_set<40000> a;
int main()
{
    cin >> n >> m;
    a.init();
    for (int i = 1; i <= m; i++)
    {
        cin >> r[i].a >> r[i].b >> r[i].v;
    }
    sort(r + 1, r + m + 1, comp);
    for (int i = 1; i <= m; i++)
    {
        if (a.get_father(r[i].a) == a.get_father(r[i].b) || a.get_father(r[i].a + n) == a.get_father(r[i].b + n))
        {
            cout << r[i].v << endl;
            return 0;
        }
        a.merge(r[i].a, r[i].b + n);
        a.merge(r[i].a + n, r[i].b);
    }
    cout << 0 << endl;
}
```
对于更复杂的关系，并查集也可以进行记录，具体的方法是开多倍大小的数组，再通过类似于旋转的方法记录状态。用不同的旋转角度，来记录不同的状态。见[食物链 (P2024)](https://www.luogu.com.cn/problem/P2024)
```cpp
#include <iostream>
using namespace std;
template <int size>
class disjoint_set
{
private:
    int father[size + 1];

public:
    void init()
    {
        for (int i = 1; i <= size; i++)
            father[i] = i;
    }
    int get_father(int node)
    {
        if (node == father[node])
            return node;
        return father[node] = get_father(father[node]);
    }
    void merge(int node_1, int node_2)
    {
        int _1 = get_father(node_1);
        int _2 = get_father(node_2);
        if (_1 != _2)
        {
            father[_1] = _2;
        }
    }
    bool query(int node_1, int node_2)
    {
        return get_father(node_1) == get_father(node_2);
    }
};
disjoint_set<150000> s;
int n, k, cnt;
int d, x, y;
int main()
{
    s.init();
    cin >> n >> k;
    for (int i = 1; i <= k; i++)
    {
        cin >> d >> x >> y;
        if (x > n || y > n)
        {
            cnt++;
            continue;
        }
        if (d == 1)
        {
            if (s.query(x, y + n) || s.query(x + n, y))
            {
                cnt++;
            }
            else
            {
                s.merge(x, y);
                s.merge(x + n, y + n);
                s.merge(x + n + n, y + n + n);
            }
        }
        else if (d == 2)
        {
            if (x == y)
            {
                cnt++;
            }
            else if (s.query(x, y) || s.query(x, y + n))
            {
                cnt++;
            }
            else
            {
                s.merge(x + n, y);
                s.merge(x + n + n, y + n);
                s.merge(x, y + n + n);
            }
        }
    }
    cout << cnt << endl;
}
```
# 并查集要注意的点
在并查集 `merge` 操作中，推荐以下写法：
```cpp
void merge(int node_1, int node_2)
{
    int _1 = get_father(node_1);
    int _2 = get_father(node_2);
    if (_1 != _2)
    {
        father[_1] = _2;
    }
}
```
而不是以下写法：
```cpp
void merge(int node_1, int node_2)
{
    father[get_father(node_1)] = father[node_2]
}
```
以上的写法在食物链一题中会MLE，我也不知道是什么原因。