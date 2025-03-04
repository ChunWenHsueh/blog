---
title: "Majority Voting Algorithm 多數投票算法"
date: 2023-11-07T12:00:00+08:00
draft: false
math: true
categories: [Data Structures and Algorithms]
---
問題描述

給定一個大小為 `n` 的整數陣列，如何找到出現頻率大於（必須嚴格大於）`n/2` 的元素？  
例如：`arr = [1, 1, 2]`，那麼 `1` 就是出現頻率大於 `n/2` 的元素。  
如果 `arr = [1, 1, 3, 4]`，那麼不存在這樣的元素。

Boyer–Moore majority vote algorithm

一個很直覺的方法就是用一個 hash map 計算所有數字出現的頻率，然後找出出現最多次的元素。需要 O(n) 的空間複雜度以及 O(n) 的時間複雜度。但是用 Boyer-Moore algorithm，只要 O(1) 的空間複雜度即可。來看看這個演算法是如何辦到的。

```c++ {linenos=true}
int majorityElement(vector<int>& nums) {
    int candidate = 0;
    int freq = 0;
    for (int i = 0; i < nums.size(); i++){
        if (candidate == nums[i]){
            freq++;
        }
        else if (freq == 0){
            candidate = nums[i];
            freq = 1;
        }
        else {
            freq--;
        }
    }
    return candidate;
}
```

`candidate` 是答案的候選人，而 `freq` 則是 `candidate` 出現的頻率。

用 `arr = [1, 2, 2, 1, 1, 1, 1]` 當範例。我在這邊把各個時候的 `candidate` 跟 `freq` 寫下來，為了對齊，我只打 `can, fre`。  
`arr = [1, 2, 2, 1, 1, 1, 1]`  
`can = [1, 1, 2, 2, 1, 1, 1]`  
`fre = [1, 0, 1, 0, 1, 2, 3]`

接下來跟著演算法實際跑一次。  
`i = 0` 時，`can = 1, fre = 1`，代表目前候選人是 `1`，並且出現的頻率為 `1` 次。  
`i = 1` 時，`can = 1, fre = 0`，此時頻率變成 `0`，代表我們把剛剛的候選人進行配對，也就是配成 `[1, 2]`。  
`i = 2` 時，`can = 2, fre = 1`，代表目前候選人是 `2`，並且出現的頻率為 `1`。  
`i = 3` 時，`can = 2, fre = 0`，此時頻率變成 `0`，代表我們把剛剛的候選人進行配對，也就是配成 `[2, 1]`。  
`i = 4` 時，`can = 1, fre = 1`，代表目前候選人是 `1`，並且出現的頻率為 `1`。  
`i = 5` 時，`can = 1, fre = 2`，代表目前候選人是 `1`，並且出現的頻率為 `2`。  
`i = 6` 時，`can = 1, fre = 3`，代表目前候選人是 `1`，並且出現的頻率為 `3`。  
此時跳出迴圈，回傳 `1`。

問題是，為什麽這個演算法是正確的呢？

大家可以發現，這個演算法是基於配對進行的。假設陣列中存在頻率出現超過 `n/2` 的元素，把它叫做 `x`，因為 `x` 的出現頻率超過 `n/2`，代表我們可以把 `x` 跟非 `x` 的元素進行配對，配對完之後還會剩下 `x` 沒有配對到。

例如：`arr = [1, 2, 2, 1, 1, 3, 1]`，可以配對成 `[1, 2], [1, 2], [1, 3]` 並且剩下 `[1]` 沒有配對到。

這個演算法還有一個特殊的地方，就是如果要找到出現頻率大於 `n/3` 或是 `n/4` 的元素，可以用一樣的方法找。只需要增加候選人的人數，就可以用一樣的概念解決。此外，還需要再花 O(n) 的時間檢查 candidate 是否為 majority element。

以下是找到出現頻率大於 `n/3` 的方法
```c++ {linenos=true}
int majorityElement(vector<int>& nums) {
    int cand1 = 0, cand2 = 0, freq1 = 0, freq2 = 0;
        int n = nums.size();
        for (int i = 0; i < n; i++){
            if (nums[i] == cand1){
                freq1++;
            }
            else if (nums[i] == cand2){
                freq2++;
            }
            else if (freq1 == 0){
                cand1 = nums[i];
                freq1 = 1;
            }
            else if (freq2 == 0){
                cand2 = nums[i];
                freq2 = 1;
            }
            else {
                freq1--;
                freq2--;
            }
        }
        freq1 = 0, freq2 = 0;
        for (int i = 0; i < n; i++){
            freq1 += nums[i] == cand1;
            freq2 += nums[i] == cand2;
        }
        vector<int> res;
        if (freq1 > n / 3){
            res.push_back(cand1);
        }
        if (freq2 > n / 3 && cand1 != cand2){
            res.push_back(cand2);
        }
        return res;
}
```

參考資料

1. https://gregable.com/2013/10/majority-vote-algorithm-find-majority.html