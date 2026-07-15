---
layout: post
title: "Algorithmic Complexity & Matrix Properties"
date: 2026-07-15
---

When designing efficient algorithms, we need a mathematical framework to describe their performance bounds. Today we will look at analyzing a simple divide-and-conquer strategy.

For a deeper dive into formal proofs, check out the [MIT Introduction to Algorithms Course](https://ocw.mit.edu/courses/introduction-to-algorithms-fall-2011/) or browse the excellent collections on [GeeksforGeeks](https://www.geeksforgeeks.org/).

---

### The Mathematical Model

Suppose we have a recursive recurrence relation for an algorithm's runtime $T(n)$. We can write this relation as:

$$
T(n) = 2T\left(\frac{n}{2}\right) + O(n)
$$

Using the **Master Theorem**, we can determine the asymptotic bound. If we match this to the general form $T(n) = aT(n/b) + f(n)$, we identify:

* $a = 2$
* $b = 2$
* $f(n) = O(n^1) \implies d = 1$

Since $\log_b(a) = \log_2(2) = 1$, which is equal to $d$, we fall into the critical case where the complexity is:

$$
T(n) = \Theta(n \log n)
$$

---

### Complexity Classification Table

Here is a quick reference table showing common recurrence behaviors and their resulting complexity classes:

| Case | Recurrence | Complexity Class | Description |
| :--- | :--- | :--- | :--- |
| **Linear Search** | $T(n) = T(n-1) + O(1)$ | $O(n)$ | Scans elements one by one |
| **Binary Search** | $T(n) = T(n/2) + O(1)$ | $O(\log n)$ | Divides problem size in half each step |
| **Merge Sort** | $T(n) = 2T(n/2) + O(n)$ | $\Theta(n \log n)$ | Divides in half, linear merge work |
| **Strassen's Multiplication** | $T(n) = 7T(n/2) + O(n^2)$ | $O(n^{\log_2 7}) \approx O(n^{2.81})$ | Sub-cubic matrix multiplication |

---

### Implementation in C++

Here is how we can implement a classic divide-and-conquer method (like the merge step of Merge Sort) that runs within this precise $\Theta(n \log n)$ time complexity bound:

```cpp
#include <iostream>
#include <vector>

// Merges two sorted subarrays into a single sorted array
void merge(std::vector<int>& arr, int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;

    std::vector<int> L(n1), R(n2);

    for (int i = 0; i < n1; i++) L[i] = arr[left + i];
    for (int j = 0; j < n2; j++) R[j] = arr[mid + 1 + j];

    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j]) {
            arr[k++] = L[i++];
        } else {
            arr[k++] = R[j++];
        }
    }

    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];
}
```

> **Performance Note:** The space complexity of this implementation is $O(n)$ due to the temporary vectors `L` and `R` created during the merge phase.