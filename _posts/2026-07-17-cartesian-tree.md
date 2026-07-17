---
layout: post
title: "Cartesian Trees"
gui_folder: "Upsolving"
gui_subfolder: "CF"
---

Consider building the min cartesian tree of the permutation $p$. Then $count[i]$ is just the number of elements "popped out" from the stack while trying to insert $p[i]$ into the cartesian tree.

The structure of the cartesian tree means that for any node $u$, it pops everything that remains in its left subtree, and $p[u]$ will remain in the stack for the entirety of its right subtree (and hence will not be popped out).

Consider the following connected component DP approach: let $dp[l][r][rem]$ be the answer to the original problem if the input were $a[l..r]$, and that we additionally require there to be exactly $rem$ elements in the stack after processing the last element $a[r]$.

To calculate the dp value, we exhaust the root node $m \in [l,r]$, to see that
$$dp[l][r][rem] = \sum_{m=l}^r {{r-l} \choose {m-l}} dp[l][m-1][a[m]] \times dp[m+1][r][rem-1]$$ If $a[m]=-1$, then we use $\sum_i dp[l][m-1][i]$ instead to replace $dp[l][m-1][a[m]]$.

This gives a solution with $\mathcal O(n^4)$ time and $\mathcal O(n^3)$ memory. We need to optimise both.

Note that the $rem$ dimension is quite wasteful. We try to optimise it away by defining $dp2[l][r] := \sum_{rem} dp[l][r][rem]$, i.e. we no longer care about how many elements there are in the stack.

$$dp2[l][r] = \sum_{m=l}^r {{r-l} \choose {m-l}} dp[l][m-1][a[m]] \times dp[m+1][r][rem-1]$$

The issue is that we only know how to express the left term $dp[l][m-1][a[m]]$ in terms of $dp2$ when $a[m]=-1$. If $a[m] \ge 0$, we don't have a good way to do so.

Actually we don't need to fix this issue: we only need the values of $dp[l][m-1][a[m]]$ for all $l$ and $a[m] \ge 0$. Note that the sum of all such $a[m]$ is at most $n-1$ (otherwise no permutations satisfy the requirements). **More specifically, it suffices to compute** the states $dp[l][r][rem]$ that satisfy $rem \le a[r+1]$. This gives a solution with $\mathcal O(n^3)$ time and $\mathcal O(n^2)$ memory.
