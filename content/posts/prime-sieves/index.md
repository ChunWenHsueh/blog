---
title: "Prime Sieves"
date: 2025-02-18T00:00:00-07:00
draft: false
showToc: true
TocOpen: true
categories: [Data Structures and Algorithms]
---

# How to find all primes under `n`?

## Sieve of Eratosthenes

This algorithm is a very intuitive way to find all primes under `n`. We first create a list of numbers from 2 to `n` and then iterate over the list. For each number `i`, we mark all its multiples as non-prime. At the end, all numbers that are not marked are prime.

```c++ {linenos=true}
vector<int> find_primes(int n) {
    vector<int> primes;
    vector<bool> is_prime(n + 1, true);
    for (int i = 2; i * i <= n; i++) {
        if (is_prime[i]) {
            primes.push_back(i);
            for (int j = i * i; j <= n; j += i) {
                is_prime[j] = false;
            }
        }
    }
    return primes;
}
```

Let's analyze the time complexity. For each prime number `p`, we will iterate from `p * p` to `n`. Which means that the time complexity is $\sum_{p \text{ is prime}}^{p \leq n} \frac{n}{p} = n\sum_{p \text{ is prime}}^{p \leq n} \frac{1}{p} = n\log\log n$ (prime harmonic series).

Of course, the actual time complexity should be better than $n\log\log n$. Since we only need to iterate over prime numbers that is smaller or equal to sqrt(n) (`for (int i = 2; i * i <= n; i++)`). This is sufficient to find all primes under `n`, since every composite number must have a prime factor that is smaller or equal to sqrt(n). And for each prime number, we actaully start from `p * p` instead of `2 * p` (`for (int j = i * i; j <= n; j += i)`).

Can we do better? In this algorithm, for each composite number `c`, we ran `is_prime[c] = false` multiple times. Take `12` as an example, `is_prime[12]` is set to false when `i` is `2, 3`. Is it possible to set `is_prime[12]` to false only once?

## Euler's Sieve

In Euler's sieve, every composite number is marked to false by its greatest proper factor (more accurately, we would mark it to false when it is `smallest prime factor * greatest proper factor`). Proper factor means the factor that is not the number itself. 

Take `12` as an example. `is_prime[12]` should be marked as false when the number we are iterating (the first for loop in Sieve of Eratosthenes) is `6`, the greatest proper factor of `12`. Let's take a look how can we accomplish this.

Let's say the number we are iterating is `c`, a composite number. Our goal is to find every prime `p` such that the product `p * c` can be expressed as `smallest prime factor * greatest proper factor`, then mark `is_prime[p * c]` as false. Remember, the reason why we are doing this is because for each composite number, we only want to mark it as false once.

We could write `c = p * q`, where `p` is the smallest prime factor of `c`, and `q` is the greatest proper factor of `c`. If we have another prime number `k` that is greater than `p`, then `k * c` will not be in the form of `smallest prime factor * greatest proper factor`, since we could write the product as `p * (k * q)`. If `k` is smaller or equal to `p`, then `k * c` will be in the form of `smallest prime factor * greatest proper factor`, we can mark `k * c` as non prime. It is not that trivial, you might need to think about it for a while.

Basically, for each number `i` we are iterating, we find the product of `i` with all prime numbers smaller than the smallest prime factor of `i`.

Let's run the algorithm with an example. In the following example, `n` is `12`.

```
numbers = [2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
primes = []
```

We start from `i = 2`. Since `2` is prime, we add it to `primes`. Then we iterate from `2 * 2` to `2 * 2`, since `2` is the largest prime that is `<= 2`. In this round of iteration, we mark `4` as non-prime. I use a asterisk to mark numbers as non-prime.

```
numbers = [2, 3, *4, 5, 6, 7, 8, 9, 10, 11, 12]
primes = [2]
```

Next, we start from `i = 3`. Since `3` is prime, we add it to `primes`. Then we iterate from `3 * 2` to `3 * 3`. In this round of iteration, we mark `6, 9` as non-prime.

```
numbers = [2, 3, *4, 5, *6, 7, 8, *9, 10, 11, 12]
primes = [2, 3]
```

Next, we start from `i = 4`. Since `4` is not prime, we will not add it to `primes`. We iterate from `4 * 2` to `4 * 2`. In this round of iteration, we mark `8` as non-prime.

```
numbers = [2, 3, *4, 5, *6, 7, *8, *9, 10, 11, 12]
primes = [2, 3]
```

Next, we start from `i = 5`. Since `5` is prime, we add it to `primes`. Then we iterate from `5 * 2` to `5 * 5`. In this round of iteration, we mark `10` as non-prime.

```
numbers = [2, 3, *4, 5, *6, 7, *8, *9, *10, 11, 12]
primes = [2, 3, 5]
```

Next, we start from `i = 6`. Since `6` is not prime, we will not add it to `primes`. We iterate from `6 * 2` to `6 * 2`. In this round of iteration, we mark `12` as non-prime.

```
numbers = 2, 3, *4, 5, *6, 7, *8, *9, *10, 11, *12
primes = [2, 3, 5]
```

We just do the same thing over and over. For each number, we will only mark it as non-prime once.

```c++ {linenos=true}
vector<int> find_primes(int n) {
    vector<int> primes;
    vector<bool> is_prime(n + 1, true);
    for (int i = 2; i <= n; i++) { // i is greatest proper factor
        if (is_prime[i]) {
            primes.push_back(i);
        }
        for (int prime: primes) {
            if (i * prime > n) {
                break;
            }
            is_prime[i * prime] = false;
            if (i % prime == 0) { // prime is the smallest prime factor of i
                break;
            }
        }
    }
    return primes;
}
```

The time complexity of Euler's sieve is $O(n)$ since we marked each number as non-prime only once. The interesting thing about Euler’s sieve is that it is called Euler’s sieve instead of sieve of Euler.