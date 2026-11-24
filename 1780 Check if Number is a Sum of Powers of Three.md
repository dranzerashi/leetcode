# LeetCode 1780 — Check if Number is a Sum of Powers of Three

## 1. DFS + Bitmask + Memoization (My Solution)

**Idea:** Explore subsets of powers of three and check whether any subset sums to `n`.

- `mask` tracks which powers of three have already been used.
- Iterate through powers from largest to smallest.
- Skip powers already used.
- If a power is less than or equal to the remaining sum, select it and recurse.
- Memoize `(remaining_sum, mask)` to avoid repeated computations.

```python
from functools import lru_cache

class Solution:
    def checkPowersOfThree(self, n: int) -> bool:
        @lru_cache(maxsize=10000000)
        def solve(x, mask):
            if x == 0:
                return True

            for i in range(16, -1, -1):
                if mask & (1 << i):
                    continue

                p3 = 3 ** i
                if p3 <= x and solve(x - p3, mask | (1 << i)):
                    return True

            return False

        return solve(n, 0)
```

## 2. Mathematical Intuition — Base-3 Representation

Every number can be represented in a positional number system.

For base 3:

\[
n = a_0 3^0 + a_1 3^1 + a_2 3^2 + \ldots
\]

Each digit \(a_i\) can be `0`, `1`, or `2`.

- `0`: Don't use that power of 3.
- `1`: Use that power once.
- `2`: Use that power twice.

Since the problem requires **distinct powers of three**, every digit must be either `0` or `1`.

Therefore, a number is valid if and only if its base-3 representation contains no digit `2`.

### Why repeated division works

Repeated division by a base extracts its digits.

\[
n = Bq + r
\]

Here, `r = n % B` gives the last digit, and `q = n // B` gives the remaining number.

Repeating this process extracts all digits, from least significant to most significant.

For example, `91`:

| n | n % 3 | n // 3 |
|---:|---:|---:|
| 91 | 1 | 30 |
| 30 | 0 | 10 |
| 10 | 1 | 3 |
| 3 | 0 | 1 |
| 1 | 1 | 0 |

Remainders (read backwards): `10101`.

Thus:

\[
91 = 1(81)+0(27)+1(9)+0(3)+1(1)
\]

All digits are 0 or 1, so the answer is `True`.

## 3. Greedy Solution — Largest Power First

Repeatedly select the largest power of three that does not exceed the remaining `n`.

If we need to select the same power twice, the representation is invalid.

```python
class Solution:
    def checkPowersOfThree(self, n: int) -> bool:
        p = 1

        while p * 3 <= n:
            p *= 3

        while n > 0:
            if p <= n:
                n -= p

                if p * 3 <= n:
                    return False

            p //= 3

        return True
```
My Solution:
```python
class Solution:
    def checkPowersOfThree(self, n: int) -> bool:
        budget=n
        for i in range(16,-1,-1):
            p3= 3**i
            #print(p3,budget)
            if p3<=budget:
                budget-=p3
            if budget==0:
                return True
        return budget==0
```

**Complexity:** O(log₃ n) time, O(1) space.

## Key Takeaway

These three approaches solve the same problem at different levels:

1. **DFS + Bitmask:** Explore possible subsets.
2. **Greedy:** Select the largest available power without reusing it.
3. **Mathematical:** Recognize that distinct powers correspond to base-3 digits of only 0 and 1.

The mathematical observation eliminates the search entirely.
