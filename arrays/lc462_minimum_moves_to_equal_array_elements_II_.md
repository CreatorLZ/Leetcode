# LeetCode #462: Minimum Moves to Equal Array Elements II

## Problem Statement

Given an integer array `nums` of size `n`, return the minimum number of moves required to make all array elements equal. In one move, you can increment or decrement an element by 1.

- **Constraints**:

  - `1 <= nums.length <= 10^5`
  - `-10^9 <= nums[i] <= 10^9`

- **Examples**:

  - **Example 1**:

    - Input: `nums = [1,2,3]`
    - Output: `2`
    - Explanation: Make all elements `2`:
      - Only two moves are needed (remember each move increments or decrements one element):
        - `[1,2,3]  =>  [2,2,3]  =>  [2,2,2]`

  - **Example 2**:
    - Input: `nums = [1,10,2,9]`
    - Output: `16`

## Mental Model

To solve this problem, we need to make all array elements equal using the fewest moves, where each move increments or decrements an element by 1. Here’s the thought process in plain English:

1. **Simplify the Problem**:

   - We need to find a target value that all elements become, minimizing the total moves.
   - The number of moves for each element is `|nums[i] - target|`.
   - Goal: Minimize `sum(|nums[i] - target|)` over all elements.

2. **Recognize the Pattern**:

   - This is about minimizing the sum of absolute differences, similar to the original sum minimization problem.
   - In a sorted array, the median minimizes `sum(|nums[i] - target|)` for any target value.

3. **Why Median?**:

   - The median is the middle value in a sorted array, balancing the distances to all other elements.
   - Unlike #2033, there’s no divisibility constraint (moves are by 1), so any target is valid.

4. **Key Insight**:
   - Sort the array and pick the median as the target.
   - Sum `|nums[i] - median|` to get the minimum moves.

**Connection to Other Problems**:

- **Original Sum Minimization**: Minimized `sum(|a[i] - x|)` for a sorted array, returning the median element.
- **LeetCode #462**: Minimizes `sum(|nums[i] - target|)` for a 1D array, returning the sum of moves, using the median.
- **LeetCode #2033**: Minimizes `sum(|num - target| / x)` for a 2D grid with `x` divisibility, using the median.

## Brute-Force Solution

The brute-force approach tests each element in the sorted array as a target and finds the smallest sum of moves.

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var minMoves2 = function (nums) {
  let minMovesMinus = Number.MAX_VALUE;
  let sort = nums.sort((a, b) => a - b);

  for (let i = 0; i < sort.length; i++) {
    let moves = 0;
    for (let j = 0; j < sort.length; j++) {
      moves += Math.abs(sort[j] - sort[i]);
    }
    minMovesMinus = Math.min(minMovesMinus, moves);
  }
  return minMovesMinus;
};
```

### Explanation

- **Sort Array**: `nums.sort((a, b) => a - b)` sorts the array to simplify calculations (though not strictly necessary for brute-force).
- **Loop Over Targets**: For each element `sort[i]` as the target:
  - Compute `moves = sum(|sort[j] - sort[i]|)` for all elements.
  - Update `minMovesMinus` with `Math.min(minMovesMinus, moves)`.
- **Return**: `minMovesMinus` is the smallest sum of moves.

### Time Complexity

- **Time**: `O(n^2)` where `n = nums.length`, due to nested loops over all elements. Too slow for `n <= 10^5`.
- **Space**: `O(1)` if sorting in place, or `O(n)` if a copy is made.

### Example

For `nums = [1,2,3]`:

- `sort = [1,2,3]`.
- Target `1`: `moves = |1-1| + |2-1| + |3-1| = 0 + 1 + 2 = 3`.
- Target `2`: `moves = |1-2| + |2-2| + |3-2| = 1 + 0 + 1 = 2`.
- Target `3`: `moves = |1-3| + |2-3| + |3-3| = 2 + 1 + 0 = 3`.
- `minMovesMinus = 2`.

**Bug I Encountered**: Initially, I didn’t realize sorting wasn’t necessary for brute-force, but it simplified debugging. The solution worked correctly, but I could optimize it by using unique targets with `[...new Set(nums)]` to reduce iterations if duplicates exist.

## Median-Based Solution

The optimized solution uses the median of the sorted array to minimize moves, avoiding the need to test multiple targets.

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var minMoves2 = function (nums) {
  const sorted = nums.sort((a, b) => a - b); // Sort array
  const median = sorted[Math.floor((sorted.length - 1) / 2)]; // Get lower median
  let moves = 0;
  for (let num of sorted) {
    moves += Math.abs(num - median);
  }
  return moves;
};
```

### Explanation

- **Sort Array**: `nums.sort((a, b) => a - b)` sorts the array to find the median.
- **Get Median**: `sorted[Math.floor((sorted.length - 1) / 2)]` picks the lower median.
- **Loop**:
  - Compute `diff = |num - median|` for each element.
  - Add `diff` to `moves`.
- **Return**: `moves` is the minimum number of moves.

### Why Median?

- To find the minimum number of moves to make all elements in the array equal, where each move adds or subtracts 1, we use the median of the sorted array. The median is the middle value that balances distances to all numbers on the number line, minimizing the total sum of absolute differences. We sort the array, pick the lower median (for simplicity), and sum the absolute differences |num - median| for each element. This sum is the minimum number of moves needed to make all elements equal to the median.
- The median minimizes `sum(|num - target|)` in a sorted array for any target value.
- No divisibility constraint (unlike #2033), so the median is always valid.
- For even-length arrays, the lower or upper median works (we use lower for simplicity).

### Time Complexity

- **Time**: `O(n log n)` for sorting + `O(n)` for the loop = `O(n log n)`.
- **Space**: `O(1)` if sorting in place, or `O(n)` if a copy is made.

### Example

For `nums = [1,10,2,9]`:

- `sorted = [1,2,9,10]`, `median = sorted[1] = 2`.
- `num=1`: `diff = |1-2| = 1`, `moves += 1`.
- `num=2`: `diff = |2-2| = 0`, `moves += 0`.
- `num=9`: `diff = |9-2| = 7`, `moves += 7`.
- `num=10`: `diff = |10-2| = 8`, `moves += 8`.
- Total: `1 + 0 + 7 + 8 = 16`.

## Debugging Tips

To understand the solution:

- Add logs to the median solution:
  ```javascript
  for (let num of sorted) {
    const diff = Math.abs(num - median);
    console.log(`num=${num}, median=${median}, diff=${diff}`);
    moves += diff;
  }
  console.log(`Total moves=${moves}`);
  ```
- Test cases:
  - `[1,2,3]` → `2`
  - `[1,10,2,9]` → `16`
  - `[1]` → `0` (single element)

## Key Takeaways

- **Median Pattern**: Minimizing `sum(|num - target|)` in a sorted array uses the median.
- **No Divisibility Constraint**: Unlike #2033, moves are by 1, so no need to check `diff % x`.
- **From Brute-Force to Optimized**: Brute-force tests all elements with `Math.min`; median uses one optimal target.
- **Connection to #2033**: #2033 extends this to a 2D grid with `x` divisibility; #462 is simpler (1D, no `x`).
- **Connection to Original Sum Minimization**: The original problem returned the median element; #462 returns the sum of moves.

## Related Problems

- Original Sum Minimization: Minimized `sum(|a[i] - x|)` for a sorted array, returning the median element.
- LeetCode #2033: Minimum Operations to Make a Uni-Value Grid (2D median with `x` constraint).
- LeetCode #296: Best Meeting Point (2D median for Manhattan distance) too hard for now. i dont have access to it anyways .

## Notes for Future Review

- **Why Median Works**: The median balances distances on a number line, minimizing total moves.
- **Personal Insight**: I initially used a brute-force approach with nested loops, similar to #2033, but learned the median is optimal, saving time.
- **Practice**: Run both solutions on test cases and compare outputs to confirm the median’s optimality.
- **Debugging Note**: Logging sums in the brute-force solution helped me verify the median gives the smallest sum.
