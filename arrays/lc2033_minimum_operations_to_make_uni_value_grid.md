# LeetCode #2033: Minimum Operations to Make a Uni-Value Grid

## Problem Statement

Given a 2D integer grid of size `m x n` and an integer `x`, find the minimum number of operations to make all elements equal (a uni-value grid). In one operation, you can add or subtract `x` from any element. Return `-1` if it’s impossible.

- **Constraints**:

  - `1 <= grid.length, grid[0].length <= 10^5`
  - `1 <= m * n <= 10^5` (total elements)
  - `1 <= grid[i][j], x <= 10^4`

- **Examples**:
  - **Example 1**:
    - Input: `grid = [[2,4],[6,8]], x = 2`
    - Output: `4`
    - Explanation: Make all elements `4`:
      - `2` → `4`: `|2-4|/2 = 1` operation
      - `4` → `4`: `0` operations
      - `6` → `4`: `|6-4|/2 = 1` operation
      - `8` → `4`: `|8-4|/2 = 2` operations
      - Total: `1 + 0 + 1 + 2 = 4`
  - **Example 2**:
    - Input: `grid = [[1,5],[2,3]], x = 1`
    - Output: `5`
    - Explanation: Make all elements `3`: `|1-3|/1 + |5-3|/1 + |2-3|/1 + |3-3|/1 = 2 + 2 + 1 + 0 = 5`

## Mental Model

To solve this problem, we need to make all grid elements equal using the fewest operations, where each operation changes an element by `x`. Here’s the thought process in plain English:

1. **Simplify the Problem**:

   - The grid is 2D, but we can flatten it to a 1D array (e.g., `[[2,4],[6,8]]` → `[2,4,6,8]`).
   - Goal: Find a target value that all numbers become, minimizing total operations.

2. **Define Operations**:

   - To change a number `num` to a target `target`, we need `|num - target| / x` operations.
   - The difference `|num - target|` must be divisible by `x` (e.g., if `x = 2`, `|4-6| = 2` is valid, but `|4-5| = 1` isn’t).

3. **Minimize Operations**:

   - We need to minimize `sum(|num - target| / x)` over all numbers.
   - This is similar to minimizing `sum(|num - target|)` in a sorted array, which the **median** achieves (like in LeetCode #462 and the original sum minimization problem).

4. **Handle Impossibility**:

   - If any `|num - target| % x !== 0`, the target is invalid, and we return `-1`.

5. **Key Insight**:
   - Flatten the grid, sort it, and use the median as the target to minimize operations.
   - Check if all differences to the median are divisible by `x`.
   - Sum `|num - median| / x` to get the total operations.

**Connection to Previous Problems**:

- **Original Problem**: Minimized `sum(|a[i] - x|)` for a sorted array, returning the median element.
- **LeetCode #462**: Minimized `sum(|nums[i] - target|)` for any target, using the median after sorting.
- **LeetCode #2033**: Minimizes `sum(|num - target| / x)` with the `x` divisibility constraint, using the median.

## Brute-Force Solution

The brute-force approach tests each unique element in the grid as a target and finds the smallest valid sum of operations.

```javascript
/**
 * @param {number[][]} grid
 * @param {number} x
 * @return {number}
 */
var minOperations = function (grid, x) {
  const nums = grid.flat(); // Flatten grid to 1D array
  const uniqueTargets = [...new Set(nums)]; // Get unique elements
  let minValue = Number.MAX_VALUE;

  for (let target of uniqueTargets) {
    let operations = 0;
    let isPossible = true;

    for (let num of nums) {
      const diff = Math.abs(num - target);
      if (diff % x !== 0) {
        isPossible = false;
        break;
      }
      operations += diff / x;
    }

    if (isPossible) {
      minValue = Math.min(minValue, operations);
    }
  }

  return minValue === Number.MAX_VALUE ? -1 : minValue;
};
```

## Explanation

- Flatten Grid: grid.flat() converts the 2D grid to a 1D array (e.g., [[2,4],[6,8]] → [2,4,6,8]).
- Unique Targets: [...new Set(nums)] gets unique elements to avoid redundant checks.
- Loop Over Targets:
- Compute diff = |num - target| for each number.
- Check if diff % x === 0. If not, mark isPossible = false.
- Sum diff / x to get operations.
- If isPossible, update minValue with Math.min.

- **Return**: If minValue is still Number.MAX_VALUE, no valid target was found, so return -1. Otherwise, return minValue.

Time Complexity

Time: O(n _ m) where n = m _ n (grid size) and m = unique elements (up to n). Too slow for m \* n <= 10^5.
Space: O(n) for the flattened array and unique targets.

Example

For grid = [[2,4],[6,8]], x = 2:
nums = [2,4,6,8], uniqueTargets = [2,4,6,8].
Target 4: operations = |2-4|/2 + |4-4|/2 + |6-4|/2 + |8-4|/2 = 1 + 0 + 1 + 2 = 4.
minValue = 4 (smallest valid sum).

## Bug I Encountered

Initially, I declared operations outside the outer loop, causing it to accumulate across targets. Moving let operations = 0 inside the loop fixed incorrect sums.

## Median-Based Solution

The optimized solution uses the median to minimize operations, avoiding the need to test multiple targets.

```javascript
/**
 * @param {number[][]} grid
 * @param {number} x
 * @return {number}
 */
var minOperations = function (grid, x) {
  const nums = grid.flat().sort((a, b) => a - b); // Flatten and sort
  const median = nums[Math.floor((nums.length - 1) / 2)]; // Get lower median
  let operations = 0;
  for (let num of nums) {
    const diff = Math.abs(num - median);
    if (diff % x !== 0) return -1; // Check divisibility
    operations += diff / x; // Sum operations
  }
  return operations;
};
```

## Explanation

- Flatten and Sort: grid.flat().sort((a, b) => a - b) creates a sorted 1D array.
- Get Median: nums[Math.floor((nums.length - 1) / 2)] picks the lower median.
- Loop:
- Compute diff = |num - median|.
- Check diff % x === 0. If not, return -1.
- Add diff / x to operations.

- Return: operations is the minimum number of operations if valid.

## Why Median?

The median minimizes sum(|num - target|) in a sorted array (like in #462).
Dividing by x gives operations: sum(|num - median| / x).
If all differences are divisible by x, the median gives the smallest sum.

## Time Complexity

- Time: O(n log n) for sorting + O(n) for the loop = O(n log n).
- Space: O(n) for the flattened array.

**Example**

- For grid = [[2,4],[6,8]], x = 2:
- nums = [2,4,6,8], median = 4.
- num=2: diff=2, 2 % 2 = 0, operations += 2/2 = 1.
- num=4: diff=0, 0 % 2 = 0, operations += 0/2 = 0.
- num=6: diff=2, 2 % 2 = 0, operations += 2/2 = 1.
- num=8: diff=4, 4 % 2 = 0, operations += 4/2 = 2.
- Total: 1 + 0 + 1 + 2 = 4.

**Debugging Tips**

- **To understand the solution, add logs to the median solution:**

```javascript
for (let num of nums) {
  const diff = Math.abs(num - median);
  console.log(
    `num=${num}, median=${median}, diff=${diff}, operations=${diff / x}`
  );
  if (diff % x !== 0) return -1;
  operations += diff / x;
}
```

**Test Cases**

- [[2,4],[6,8]], x = 2 → 4
- [[1,5],[2,3]], x = 1 → 5
  [[1,2],[3,4]], x = 2 → -1 (impossible)

## Key Takeaways

- **Median Pattern**: Minimizing sum(|num - target|) or sum(|num - target| / x) in a sorted array uses the median.
- **Divisibility Constraint**: Check |num - median| % x === 0 due to the x constraint.
- **From Brute-Force to Optimized**: Brute-force tests all targets with Math.min; median uses one optimal target.
- **Connection to #462**: #462 minimized moves by 1 (no divisibility check); #2033 divides by x and checks divisibility.

## Related Problems

- LeetCode #462: Minimum Moves to Equal Array Elements II (1D version without x constraint).
- LeetCode #296: Best Meeting Point (2D median without x constraint).

## Notes for Future Review

- Why Median Works: The median balances distances on a number line, minimizing total operations.
- Debugging Bug: Reset operations for each target in brute-force to avoid accumulating sums.
- Practice: Run both solutions on test cases and compare outputs to reinforce understanding.
