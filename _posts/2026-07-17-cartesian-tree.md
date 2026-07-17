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
$$dp[l][r][rem] = \sum_{m=l}^r { {r-l} \choose {m-l}} dp[l][m-1][a[m]] \times dp[m+1][r][rem-1]$$ If $a[m]=-1$, then we use $\sum_i dp[l][m-1][i]$ instead to replace $dp[l][m-1][a[m]]$.

This gives a solution with $\mathcal O(n^4)$ time and $\mathcal O(n^3)$ memory. We need to optimise both.

Note that the $rem$ dimension is quite wasteful. We try to optimise it away by defining $dp2[l][r] := \sum_{rem} dp[l][r][rem]$, i.e. we no longer care about how many elements there are in the stack.

$$dp2[l][r] = \sum_{m=l}^r { {r-l} \choose {m-l}} dp[l][m-1][a[m]] \times dp[m+1][r][rem-1]$$

The issue is that we only know how to express the left term $dp[l][m-1][a[m]]$ in terms of $dp2$ when $a[m]=-1$. If $a[m] \ge 0$, we don't have a good way to do so.

Actually we don't need to fix this issue: we only need the values of $dp[l][m-1][a[m]]$ for all $l$ and $a[m] \ge 0$. Note that the sum of all such $a[m]$ is at most $n-1$ (otherwise no permutations satisfy the requirements). **More specifically, it suffices to compute** the states $dp[l][r][rem]$ that satisfy $rem \le a[r+1]$. This gives a solution with $\mathcal O(n^3)$ time and $\mathcal O(n^2)$ memory.

```cpp
vector <int> dp[510][510];
int dp2[510][510];

int get_dp(int l,int r,int rem){
    if (rem>=dp[l][r].size()||rem<0) return 0;
    return dp[l][r][rem];
}

void solve(){
    for (int i=1; i<=n+1; i++){
        dp[i][i-1].pb(1);
        dp2[i][i-1]=1;
    }

    for (int d=0; d<n; d++){
        for (int l=1; l+d<=n; l++){
            int r=l+d;
            for (int m=l; m<=r; m++){
                dp2[l][r]+=ncr(r-l,m-l)*(a[m]==-1?dp2[l][m-1]:get_dp(l,m-1,a[m]))%mod*dp2[m+1][r];
                dp2[l][r]%=mod;
            }
            if (r<n&&a[r+1]!=-1){
                for (int rem=0; rem<=a[r+1]; rem++){
                    int ths=0;
                    for (int m=l; m<=r; m++){
                        ths+=ncr(r-l,m-l)*(a[m]==-1?dp2[l][m-1]:get_dp(l,m-1,a[m]))%mod*get_dp(m+1,r,rem-1);
                        ths%=mod;
                    }
                    dp[l][r].pb(ths);
                }
            }
        }
    }
    cout<<dp2[1][n]<<'\n';
}
```