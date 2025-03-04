---
title: "Digital Root / Repeated digital sum"
date: 2023-04-26T15:34:53+08:00
draft: false
math: true
categories: [Math]
---

今天寫到 leetcode 258. Add Digits (https://leetcode.com/problems/add-digits/)，發現了這個有趣的題目。題目本身不難，但是 follow up 要求寫出 O(1) 時間複雜度的解法，因此卡了一下。

看到 wiki (https://en.wikipedia.org/wiki/Digital_root#Congruence_formula) 才發現這樣的問題被叫做 Digital Root，而且還可以在不同進位下討論，挺有趣的。

先看一個簡單的範例：\
有一個 5 進位 的數字 14324 （這是用 5 進位表示），把這個數字的每一位相加然後用 5 進位表示，直到剩下一位，這個數字會是多少？

1 + 4 + 3 + 2 + 4 = 14（10 進位） = 24（5 進位）\
2 + 4 = 6（10 進位）  = 11（5 進位）\
1 + 1 = 2（5 進位）

這個數字會是 2。但是計算很麻煩，因爲要在 5 進位上面做運算，是人類很不收悉的進位。

簡單的方法就是把所有的計算都換到 10 進位上面。
有小標的 5 就是指這個數字是 5 進位的。

$$ 14324_5 = 1 \times 5^4 + 4 \times 5^3 + 3 \times 5^2 + 2 \times 5^1 + 4 \times 5^0 $$

$$
\begin{align*}
14324_5 \mod 4 &=  1 \times 5^4 + 4 \times 5^3 + 3 \times 5^2 + 2 \times 5^1 + 4 \times 5^0 \mod 4\\\\
&= 1 \times (4 + 1)^4 + 4 \times (4 + 1)^3 + 3 \times (4 + 1)^2 + 2 \times (4 + 1)^1 + 4 \times (4 + 1)^0 \mod 4 \\\\
&= 1 + 4 + 3 + 2 + 4 \mod 4 \\\\
&= 14 \mod 4  = 24_5 \mod 4 \\\\
&= 2 \times 5^1 + 4 \times 5^0 \mod 4 \\\\
&= 2 + 4 \mod 4 \\\\
&= 6 \mod 4 = 11_5 \mod 4 \\\\
&= 1 \times 5^1 + 1 \times 5^0 \mod 4 \\\\
&= 2 \mod 4
\end{align*}
$$

因爲 $(n + 1)^k \mod n = 1 \mod n$，所以把 5 進位換到 10 進位再模 4，其實就會等於 5 進位的每一位相加再模 4。反過來說，10 進位模 4，其實等於換到 5 進位後再每一位相加。

結論就是，換到 10 進位再模 4 就是我們要的結果。


總結一下，因爲$(n + 1)^k \mod n = 1 \mod n$，所以對於 $n + 1$ 進位的數字，換成 10 進位再模 $n$ 就會是要的結果。

但是還有一些小地方要處理，例如 10 進位的 18，應該要算出 9 (1 + 8)，但是直接模的話會得出 0。所以對於模完爲 0 的數字，需要特別處理，才會有 wiki 上面的式子

$$
dr_b(n)=
\begin{cases}
0   &n = 0,\\\\
b-1 &n\neq 0, n \equiv 0 \mod (b - 1),\\\\
n  \mod (b - 1)  &n\not\equiv 0 \mod (b - 1)
\end{cases}
$$