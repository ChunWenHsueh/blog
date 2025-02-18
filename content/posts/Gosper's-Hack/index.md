---
title: "Gosper's Hack"
date: 2023-05-15T20:00:00+08:00
draft: false
math: true
---

這是在寫 [1799. Maximize Score After N Operations](https://leetcode.com/problems/maximize-score-after-n-operations/) 時遇到的問題。

簡單來說，我想要解決的問題是「找到 n 個物品中取 k 個的所有組合」。而 Gosper's Hack 可以有效率地找到 n 個 bit 中 k 個 bit 爲 1 的所有組合，剛剛好對應了我的問題。

Gosper's Hack 的原理就是，從滿足條件（n 個 bit 中 k 個 bit 爲 1）的數字中，從最小的數字，一路找到最大的。

如果想找「5 個 bit 中 2 個 bit 爲 1」的組合，那麼 Gosper's Hack 會依序找到
```
00011
00101
00110
01001
01010
01100
10001
10010
10100
11000
```

首先，滿足條件的數字中，最小的數字很容易就找到，就是把所有的 1 塞到最右邊去，也就是

```c++
int state = (1 << k) - 1;
```

接下來比較難的問題是，要如何有效率地找到下一個數字。

找到下一個數字，分成兩個步驟
1. 把最右邊的 $01$ 變成 $10$
2. 把剛剛 $10$ 右邊的 $1$ 全部推到最右邊去

看一個範例：有數字 $10011110_2$，他的下一個數字應該是多少？\
根據上面的步驟，$10011100_2$ 的下一個數字就是 $10100111_2$

爲什麼這樣可以找到下一個數字？\
對於一個二進位的數字，想要找到比他大而且也是擁有 k 個 1 的數字，一定是把最後一個 1 之前的 0 變成 1，然後這個 1 （原本是 0 的 1）後面的數字越小越好，同時也要維持總共 k 個 1。

例如： 要找到比 $10011110_2$ 大的數字，有以下選擇\
$11$xxxxxx：把第一個 $0$ 變成 $1$\
$101$xxxxx：把第二個 $0$ 變成 $1$\
$111$xxxxx：把第一、二個 $0$ 變成 $1$\
後面的 x 代表填入 $0$ 或是 $1$ 都可以。\
但是要從這些選擇裡面找到最小的，一定是 
1. 只把一個 $0$ 變成 $1$
2. 這個 $0$ 要越右邊越好

從上面的三個選擇中，最小選項就是 「把第二個 $0$ 變成 $1$」。\
接下來只需要煩惱後面的 x 要填入什麼數字。\
爲了要維持 5 個 $1$，這些 x 裡面應該要出現 3 個 $1$（$101$xxxxx 已經有 2 個 $1$），而想要讓這個數字最小，一定是把這 3 個 $1$ 放到最右邊。\
所以最後才有 $10100111_2$。

步驟 1 中選擇「最右邊」的原因，是因爲要從比當前數字大的選擇中找到最小的數字，而 「$01$ 變成 $10$」 則是爲了維持 1 的數量不變。\
步驟 2 中把 「$1$ 全部推到最右邊去」則是爲了讓後面的數字越小越好，而最小數字的就是 1 全部都在後面。

接下來看一下程式的部分
```c++
void GospersHack(int k, int n)
{
    int state = (1 << k) - 1;
    while (state < (1 << n))
    {
        // do something in current state
        int c = state & -state;
        int r = state + c;
        state = (((r ^ state) >> 2) / c) | r;
    }
}
```

一樣用 $10011110_2$ 來舉例
$$
\begin{align*}
\text{state} &= 10\textcolor{red}{01}\textcolor{yellow}{111}0_2\\\\
\text{c} &= 00000010_2\\\\
\text{r} = \text{state} + \text{c} &= 10\textcolor{red}{10}0000_2\\\\
\text{r \\^ state} &= 00\textcolor{red}{11}\textcolor{yellow}{111}0_2\\\\
\text{((r \\^ state) / c) >> 2} &= 00000\textcolor{yellow}{111}_2\\\\
\text{((r \\^ state) / c) >> 2 | r} &= 10\textcolor{red}{10}0\textcolor{yellow}{111}_2\\\\
\end{align*}
$$
紅色的數字就是 「最右邊的 $01$」，黃色則是步驟 2 中 「右邊的 $1$」。

`c` 得到的數字是「最右邊的 1」\
`r` 得到的數字是「最右邊的 $01$ 變成 $10$」，然後右邊都變成 0。\
`r ^ state` 就是 `r` 跟 `state` 之間哪幾個 bit 不一樣。會不一樣的 bit 其實就是紅色跟黃色數字。

`((r ^ state) / c) >> 2` 就是把這些 1 推到最右邊，然後再減少 2 個 1，就是步驟 2 在做的事情。

最後再把 `r` 跟 `((r ^ state) / c) >> 2` 做 OR，就可以得到結果。

可是程式寫的是 `((r ^ state) >> 2) / c` 而不是 `((r ^ state) / c) >> 2`，[第二個網站](https://programmingforinsomniacs.blogspot.com/2018/03/gospers-hack-explained.html)有寫到，

> Gosper chose to use ((r ^ set) >> 2) / c) instead of ((r ^ set) / c) >> 2.   Why?  I believe he did so because some CPUs use the shift-and-subtract method to perform division and stop as soon as the remainder is 0, so dividing by a small number is faster.  Thus, in some CPUs, we can expect ((r ^ set) >> 2) / c) to be marginally faster than ((r ^ set) / c) >> 2.

看起來是因爲寫 `((r ^ state) >> 2) / c` 效率會更好，所以才這樣做。

1: https://zhuanlan.zhihu.com/p/360512296 \
2: https://programmingforinsomniacs.blogspot.com/2018/03/gospers-hack-explained.html