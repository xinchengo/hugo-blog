+++
title = 'NOIP 2021 游记'
date = 2021-12-04T19:06:49+08:00
draft = false
categories = ['游记']
tags = ['NOIP 2021','游记']
+++

# 简介

由于在 CSP-2021 中失常的表现，我来到 NOIP 考场，准备一雪前耻。数月的勤加练习，在这次考试中一定会展现出来吧。我这样想着，心里充满了期待和愉悦。

然而当我走出考场后我却不再这样想了。我的发挥还是一如既往的不好，但只是比上次要好点了吧。想到这，我又感到有几分确幸。我好歹第一题想出了正解，分数总不会很难看。

然而，

> `puts("-1\n");`
>
> 毁我青春
>
> 断我前途
>
> 遗恨终生

# 正文

## T1 [报数](https://www.luogu.com.cn/problem/P7960) (100' $\rightarrow$ 0')

一看到 T1，难道不就是埃氏筛的变形？

二话不说，写好了，大约 5 分钟。

然而觉得不够快，想要再优化一点， `cin` 和 `cout` 换成了 `scanf` 和 `printf`（或 `puts`）。

写下了这样的代码。

```cpp
#include<stdio.h>
int v[10003007];
int nums[800000], cnt;
int t, x, u;
int p(int x)
{
    while(x)
        if(x%10==7)
            return 1;
        else
            x/=10;
    return 0;
}
int main()
{
    for(int i=1;i<=10003000;i++)
        if(p(i))
        {
            for(int j=1;i*j<=10003000;j++)
                v[i*j]=-1;
        }
    for(int i=1;i<=10003000;i++)
        if(v[i]==0)
            nums[++cnt]=i, v[i]=cnt;
    scanf("%d",&t);
    while(t--)
    {
        scanf("%d",&x);
        if(v[x]==-1)
            puts("-1\n");
        else
            printf("%d\n",nums[v[x]+1]);
    }
}
```

然而很不幸，问题就出在 `puts` 上。

`puts` 和 Python 的 `print` 很类似，每输出一次就换一次行，所以我的 `puts("-1\n")` 的 `\n`，属于画蛇添足。

很不幸爆零了。

## T2 [数列](https://www.luogu.com.cn/problem/P7961) (0')

式子让人看麻，直接放弃。

## T3 [方差](https://www.luogu.com.cn/problem/P7962) (16')

我怎么就没有意识到这题是交换差分序列呢？

我死了

爆搜走人

## T4 [棋局](https://www.luogu.com.cn/problem/P7963) (0')

最后才看这题

没时间写暴力

没时间骗分了

# 总结

这一次 NOIP2021 是一个悲伤的经历，我很庆幸作为一名初三学生，我挥霍了最后一次非正式参加的机会。新的一年，渡尽劫波，相逢一笑，历尽艰险，未来可期。
