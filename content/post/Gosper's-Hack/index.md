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

先來看一個範例，假設有數字 $10011010_2$，他的下一個數字應該是多少？\
首先，這個數字要比 $10011010_2$ 大，但是要維持 4 個 1，因此會考慮把「最右邊的 1」左邊的 0 變成 1。\
也就是如果把 $1\textcolor{red}{00}11\textcolor{red}{0}10_2$ 中其中一個紅色的 0 變成 1，都可以變得比原本的數字大。但是如果要找到最小的，當然要把最右邊的紅色 0 變成 1。如果直接變的話，1 的數量會變多，爲了保持 1 的數量不變，再把紅色 0 右邊的 1 變成 0 即可。\
也就是 $10011\textcolor{red}{0}\textcolor{yellow}{1}0_2$ 變成 $10011\textcolor{red}{1}\textcolor{yellow}{0}0_2$。

你可能會問，萬一這個 0 的右邊沒有 1 呢？事實上，一定會有，讀者可以想想看。

所以現在知道了 $10011010_2$ 的下一個數字是 $10011100_2$。

$10011100_2$ 的下一個呢？\
照剛剛的方法，找到的會是 $10\textcolor{red}{1}\textcolor{yellow}{0}1100_2$，著色的數字是變動的數字。但是這個數字並不是最小的，可以把紅色數字右邊的 1 全部推到最右邊去，也就是變成 $10\textcolor{red}{1}\textcolor{yellow}{0}0011_2$。這個數字比 $10101100_2$ 小，比 $10011100_2$ 大。

所以要找到下一個數字，分成兩個步驟
1. 把紅色 0 變成 1，把紅色 0 右邊的 1 變成 0
2. 紅色 1 （剛剛的紅色 0）右邊的 1 全部推到最右邊去

先來看一下程式的部分
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

一樣用 $10011100_2$ 來舉例

$$
\begin{align*}
\text{state} &= 10\textcolor{red}{0}\textcolor{yellow}{11}\textcolor{red}{1}00_2\\\\
\text{c} &= 00000100_2\\\\
\text{r} &= 10\textcolor{red}{1}\textcolor{yellow}{00}\textcolor{red}{0}00_2\\\\
\text{r \\^ state} &= 00\textcolor{red}{1}\textcolor{yellow}{11}\textcolor{red}{1}00_2\\\\
\text{((r \\^ state) / c) >> 2} &= 000000\textcolor{yellow}{11}_2\\\\
\text{((r \\^ state) / c) >> 2 | r} &= 10\textcolor{red}{1}000\textcolor{yellow}{11}_2\\\\
\end{align*}
$$

`c` 得到的數字是「最右邊的 1」\
`r` 得到的數字是「最右邊的 1」左邊的 0 變成 1，然後右邊都變成 0。看起來有點饒口，其實就是紅色 0 變成 1，然後紅色 0 右邊都變成 0。\
`r ^ state` 就是 `r` 跟 `state` 之間哪幾個 bit 不一樣。會不一樣的 bit 其實就是紅色數字之間（包含紅色數字）的數字，但是需要的其實只要紅色數字之間（不包含紅色數字）的數字，也就是 `r ^ state` 1 bit 的數量比需要的多 2。

`((r ^ state) / c) >> 2` 就是把這些 1 推到最右邊，然後再減少 2 個 1，就是步驟 2 在做的事情。

最後再把 `r` 跟 `((r ^ state) / c) >> 2` 做 OR，就可以得到結果。

可是程式寫的是 `((r ^ state) >> 2) / c` 而不是 `((r ^ state) / c) >> 2`，[第二個網站](https://programmingforinsomniacs.blogspot.com/2018/03/gospers-hack-explained.html)有寫到，

> Gosper chose to use ((r ^ set) >> 2) / c) instead of ((r ^ set) / c) >> 2.   Why?  I believe he did so because some CPUs use the shift-and-subtract method to perform division and stop as soon as the remainder is 0, so dividing by a small number is faster.  Thus, in some CPUs, we can expect ((r ^ set) >> 2) / c) to be marginally faster than ((r ^ set) / c) >> 2.

看起來是因爲寫 `((r ^ state) >> 2) / c` 效率會更好，所以才這樣做。

1: https://zhuanlan.zhihu.com/p/360512296 \
2: https://programmingforinsomniacs.blogspot.com/2018/03/gospers-hack-explained.html