# LeetCode #209: Minimum Size Subarray Sum

## Problem Statement

Given an array of positive integers `nums` and a positive integer `target`, return the **minimal length** of a subarray whose sum is greater than or equal to `target`. If no such subarray exists, return `0` instead.

- **Constraints**:

  - `1 <= target <= 10^9`
  - `1 <= nums.length <= 10^5`
  - `1 <= nums[i] <= 10^4`

- **Examples**:
  - **Example 1**:
    - Input: `target = 7, nums = [2,3,1,2,4,3]`
    - Output: `2`
    - Explanation: The subarray `[4,3]` has sum `7`, which is `>= 7`, and length `2` is minimal.
  - **Example 2**:
    - Input: `target = 4, nums = [1,4,4]`
    - Output: `1`
    - Explanation: The subarray `[4]` has sum `4 >= 4`, with minimal length `1`.
  - **Example 3**:
    - Input: `target = 11, nums = [1,1,1,1,1,1,1,1]`
    - Output: `0`
    - Explanation: No subarray has sum `>= 11`, so return `0`.

## Mental Model

To find the minimal length subarray with sum at least `target`, we need a dynamic window that tracks the sum of elements and shrinks when possible to minimize length. Here’s my thought process in plain English:

1. **Simplify the Problem**:

   - We want the shortest subarray where the sum of its elements is `>= target`.
   - Example: For `target = 7, nums = [2,3,1,2,4,3]`, `[4,3]` has sum `7` and length `2`.
   - Checking all subarrays (brute force) is O(n²), too slow for large arrays.

2. **Recognize the Pattern**:

   - This is a **sliding window** problem, like #3 (Longest Substring Without Repeating Characters), but instead of tracking unique characters, we track the sum of numbers.
   - Use `left` and `right` pointers to define a window, expanding with `right` to increase the sum and shrinking with `left` when the sum is `>= target` to find the smallest window.

3. **Key Insight**:

   - Track the window’s sum with a variable (`sum`), adding `nums[right]` to grow and subtracting `nums[left]` to shrink.
   - When `sum >= target`, record the window length (`right - left + 1`) and shrink to find a smaller valid window.
   - Use `Infinity` for `minSize` to ensure valid lengths update correctly, returning `0` if no valid subarray is found.

4. **Initial Mistakes**:
   - **Used `while` to Grow Window**: In my first attempt, I used `while (sum < target)` to add `nums[right]`, causing repeated additions and incorrect sums.
   - **Used `currSum`**: Declared `currSum` but didn’t use it properly, subtracting `nums[left]` from it instead of `sum`.
   - **Set `minSize = 0`**: Initialized `minSize` to `0`, causing `Math.min` to always pick `0`, returning incorrect results.
   - **Unconditional Shrinking**: Shrunk the window every iteration, missing valid subarrays.
   - **Tracking Sums**: Wondered if a `Set` could track sums (like #3). Realized a single `sum` variable tracks the window’s sum by adding/subtracting.
   - **Why Not Modify `right` in `while`?**: Thought the `while` loop could adjust `right`, but learned it’s controlled by the `for` loop to ensure each element is processed.
   - **Why `Infinity`?**: Confused why not use `0` for `minSize`. Realized `0` prevents updating with valid lengths, while `Infinity` allows proper minimization.

**Connection to Other Problems**:

- **LeetCode #3**: Tracked unique characters with a `Set` and `right - left + 1` for length. #209 tracks sums with a variable and minimizes length.
- **LeetCode #11 (Container With Most Water)**: Used `right - left` for width, like #209’s `right - left + 1` for length.
- **LeetCode #125 (Valid Palindrome)**: Adjusted pointers to skip invalid characters, like #209’s shrinking for valid sums.
- **LeetCode #26/#27/#283**: Moved elements with pointers, like #209’s window adjustments.
- **LeetCode #344 (Reverse String)**: Swapped elements, unlike #209’s sum tracking.

## Optimized Solution

The sliding window approach uses `left` and `right` pointers to maintain a window where the sum is `>= target`, shrinking to find the minimal length.

```javascript
/**
 * @param {number} target
 * @param {number[]} nums
 * @return {number}
 */
var minSubArrayLen = function (target, nums) {
  // Initialize window sum and minimum length
  let sum = 0;
  let minSize = Infinity;
  let left = 0;

  // Iterate with right pointer to expand window
  for (let right = 0; right < nums.length; right++) {
    // Add current number to window sum
    sum += nums[right];

    // Shrink window while sum is at least target
    while (sum >= target) {
      // Update minimum length if current window is smaller
      minSize = Math.min(minSize, right - left + 1);
      // Remove leftmost number and shrink window
      sum -= nums[left];
      left++;
    }
  }

  // Return 0 if no valid subarray found, else minSize
  return minSize === Infinity ? 0 : minSize;
};
```

### Explanation

- **Initialize**: `sum = 0` for window sum, `minSize = Infinity` for smallest length, `left = 0` for window start.
- **Loop**: Move `right` to expand the window:
  - Add `nums[right]` to `sum`.
  - If `sum >= target`, update `minSize` with window length (`right - left + 1`) and shrink by subtracting `nums[left]` and moving `left`.
- **Return**: `0` if `minSize` is `Infinity` (no valid subarray), else `minSize`.

### Line-by-Line Explanation

```javascript
/**
 * @param {number} target
 * @param {number[]} nums
 * @return {number}
 */
var minSubArrayLen = function(target, nums) {
    // Initialize window sum and minimum length
    let sum = 0;
    let minSize = Infinity;
    let left = 0;
```

- Initializes `sum` for window sum, `minSize` for smallest valid length, `left` for window start.

```javascript
    // Iterate with right pointer to expand window
    for (let right = 0; right < nums.length; right++) {
        // Add current number to window sum
        sum += nums[right];
```

- Moves `right` to add `nums[right]` to the window’s sum.

```javascript
// Shrink window while sum is at least target
while (sum >= target) {
  // Update minimum length if current window is smaller
  minSize = Math.min(minSize, right - left + 1);
  // Remove leftmost number and shrink window
  sum -= nums[left];
  left++;
}
```

- When `sum >= target`, updates `minSize` with window length and shrinks by removing `nums[left]` and moving `left`.

```javascript
    }
    // Return 0 if no valid subarray found, else minSize
    return minSize === Infinity ? 0 : minSize;
};
```

- Returns `0` if no valid subarray, else `minSize`.

### Time Complexity

- **Time**: `O(n)`, where `n` is `nums.length`. Each element is added (`right` moves `n` times) and removed at most once (`left` moves `n` times).
- **Space**: `O(1)`, only uses `sum`, `minSize`, and `left`.

### Example

For `target = 7, nums = [2,3,1,2,4,3]`:

- Initialize: `sum = 0`, `minSize = Infinity`, `left = 0`.
- `right = 0`: `sum += 2 = 2`, `sum < 7`. Window: `[2]`.
- `right = 1`: `sum += 3 = 5`, `sum < 7`. Window: `[2,3]`.
- `right = 2`: `sum += 1 = 6`, `sum < 7`. Window: `[2,3,1]`.
- `right = 3`: `sum += 2 = 8`, `sum >= 7`, `minSize = min(Infinity, 3-0+1) = 4`, shrink: `sum -= 2 = 6`, `left = 1`. Window: `[3,1,2]`.
- `right = 4`: `sum += 4 = 10`, `sum >= 7`, `minSize = min(4, 4-1+1) = 4`, shrink: `sum -= 3 = 7`, `left = 2`, `sum >= 7`, `minSize = min(4, 4-2+1) = 3`, shrink: `sum -= 1 = 6`, `left = 3`. Window: `[2,4]`.
- `right = 5`: `sum += 3 = 9`, `sum >= 7`, `minSize = min(3, 5-3+1) = 3`, shrink: `sum -= 2 = 7`, `left = 4`, `sum >= 7`, `minSize = min(3, 5-4+1) = 2`, shrink: `sum -= 4 = 3`, `left = 5`. Window: `[3]`.
- Return: `minSize = 2` (for `[4,3]`).

## Debugging Tips

- **Log Progress**:
  ```javascript
  console.log(
    `right=${right}, nums[right]=${nums[right]}, left=${left}, sum=${sum}, minSize=${minSize}`
  );
  ```
- **Test Cases**:
  - `target=7, nums=[2,3,1,2,4,3]` → `2` (e.g., `[4,3]`)
  - `target=4, nums=[1,4,4]` → `1` (e.g., `[4]`)
  - `target=11, nums=[1,1,1,1,1,1,1,1]` → `0`
  - `target=15, nums=[1,2,3,4,5]` → `5` (e.g., `[1,2,3,4,5]`)
  - `target=1, nums=[1]` → `1`

## Bugs I Encountered

- **Misused `while` Loop**: Used `while (sum < target)` to add `nums[right]`, causing repeated additions and incorrect sums.
- **Unused `currSum`**: Declared `currSum` but subtracted `nums[left]` from it instead of `sum`, breaking the window’s sum.
- **Set `minSize = 0`**: Caused `Math.min` to always pick `0`, returning incorrect results.
- **Unconditional Shrinking**: Shrunk the window every iteration, missing valid subarrays.
- **Tracking Sums**: Considered a `Set` (like #3), but learned a single `sum` variable tracks the window’s sum by adding/subtracting.
- **Modifying `right`**: Wondered if `while` could adjust `right`. Realized `for` loop controls `right` for systematic expansion.
- **Why `Infinity`?**: Used `minSize = 0`, but `Infinity` ensures valid lengths update correctly, with `0` returned if no subarray is found.

## Key Takeaways

- **Sliding Window Pattern**: Uses `left` and `right` to maintain a window with `sum >= target`, shrinking to minimize length, like #3’s unique character window.
- **Tracking Sums**: Use a single `sum` variable, adding `nums[right]` and subtracting `nums[left]`, unlike #3’s `Set` for characters.
- **Why `while` Modifies `sum` and `left`**: `while` shrinks the window when `sum >= target`, updating `sum` and `left`, while `right` is controlled by the `for` loop for expansion.
- **Why `Infinity` for `minSize`**: Ensures `Math.min` picks valid lengths, unlike `0`, which blocks updates. Returns `0` if `minSize` remains `Infinity`.
- **Connection to #3**: #3 maximized length with `right - left + 1` and a `Set`. #209 minimizes length and tracks sums with a variable.
- **Connection to #11/#125/#283**: #11 used `right - left` for width, #125 adjusted pointers for validity, #283 moved elements, #209 adjusts for sum.
- **Personal Insight**: Confused by `while` loop and `minSize = 0`. Visualized window as a stretchy frame, with `sum` as weight and `Infinity` as a high limbo bar.
- **Why Efficient**: O(n) vs. brute force’s O(n²), as each element is processed once or twice.

## Related Problems

- **LeetCode #3**: Tracked unique characters with a `Set`, like #209’s sum tracking with a variable.
- **LeetCode #11**: Optimized area with `right - left`, like #209’s `right - left + 1`.
- **LeetCode #125**: Skipped invalid characters with pointers, like #209’s shrinking for valid sums.
- **LeetCode #26/#27/#283**: Moved elements with pointers, like #209’s window adjustments.
- **LeetCode #344**: Swapped elements, unlike #209’s sum tracking.

## Notes for Future Review

- **Why Sliding Window Works**: `right` expands the window, `left` shrinks when `sum >= target`, optimizing length, unlike #3’s repeat-based shrinking.
- **Personal Insight**: Clarified `sum` tracking, `while` loop roles, and `Infinity` through examples like `[2,3,1,2,4,3]`. Built on #3’s window logic.
- **Practice**: Test with single-element arrays, no valid subarrays, or large targets to master shrinking.
- **Debugging Note**: Logging `sum` and `minSize` helped verify window adjustments and length updates.

---
