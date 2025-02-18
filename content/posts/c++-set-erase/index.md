---
title: "C++ 中遍歷 set 的同時刪除元素"
date: 2023-04-02T19:14:03+08:00
draft: false
---

今天在打 code 的時候遇到一個問題：\
我想要遍歷 set 的同時，把符合條件的元素刪掉。

假設我想要刪除 set 中所有小於 10 的元素。一開始我寫出這樣的東西，
```c++
set<int> new_set = {1, 3, 4, 6, 7, 10, 15};
for (auto it = new_set.begin(); it != new_set.end() && it* < 10; it++){
    new_set.erase(it);
}
```
跑了之後發現雖然沒有報錯，但是 set 似乎沒有變成我預想的樣子。\
簡單來說當 `erase(it)` 跑完之後， `it` 就不見了。`it++` 的時候會有無法預期的錯誤。

上網找了一下[^1]，發現了有人跟我一樣遇到一樣的問題。\
一開始看到了這個[^2]解決的辦法：

```c++
set<int> new_set = {1, 3, 4, 6, 7, 10, 15};

// C++03
for (auto it = new_set.begin(); it != new_set.end() && it* < 10;){
    new_set.erase(it++);
}
```
我就很納悶，`new_set.erase(it++)` 跟 `new_set.erase(it), it++` 這樣不是一樣的效果嗎？

繼續查下去發現 [^3] 這段話，
> i is incremented before calling erase, and the previous value is passed to the function. A function's arguments have to be fully evaluated before the function is called.

對於一個 function `f(int i)`， `f(i++)` 做的其實是先 `i = i + 1`，但是進到 `f` 的時候會傳入原本的 `i`，也就是 +1 之前的 `i`。\
這就是上面 `new_set.erase(it++)` 會對的原因。

當然 C++ 有提供比較好的工具。C++11之後， `erase` 事實上會回傳下一個 iterator，所以可以改成這樣：

```c++
set<int> new_set = {1, 3, 4, 6, 7, 10, 15};

// C++11
for (auto it = new_set.begin(); it != new_set.end() && it* < 10;){
    it = new_set.erase(it);
}
```

或是更一般的寫法：
```c++
for (auto it = new_set.begin(); it != new_set.end();){
    if (it satisfies some condition){
        it = new_set.erase(it);
    }
    else {
        it++;
    }
}
```

[^1]: https://stackoverflow.com/questions/2874441/deleting-elements-from-stdset-while-iterating
[^2]: https://stackoverflow.com/questions/263945/what-happens-if-you-call-erase-on-a-map-element-while-iterating-from-begin-to/263958#263958
[^3]: https://stackoverflow.com/questions/596162/can-you-remove-elements-from-a-stdlist-while-iterating-through-it