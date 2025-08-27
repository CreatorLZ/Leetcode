# LeetCode #11: Container With Most Water

## Problem Statement

You are given an integer array` height` of length `n`. There are `n` vertical lines drawn such that the two endpoints of the `ith` line are `(i, 0)` and `(i, height[i])`.

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return the maximum amount of water a container can store.

Notice that you may not slant the container.

- **Constraints**:

  - `n == height.length`
  - `2 <= n <= 10^5`
  - `0 <= height[i] <= 10^4`

- **Examples**:
  - **Example 1**:
    - Input: `height = [1,8,6,2,5,4,8,3,7]`
    - Output: `49`
    - Explanation: Maximum area is formed by lines at indices `1` (height `8`) and `8` (height `7`). Width = `8 - 1 = 7`, area = `7 * min(8,7) = 7 * 7 = 49`.
  - **Example 2**:
    - Input: `height = [1,1]`
    - Output: `1`
    - Explanation: Width = `1`, area = `1 * min(1,1) = 1`.

## Mental Model

To maximize the area of water between two lines, we compute `(right - left) * min(height[left], height[right])` for pairs of indices. Here’s my thought process in plain English:

1. **Simplify the Problem**:

   - Each `height[i]` is a vertical line at position `i` on the x-axis.
   - Area = width (distance between indices) \* height (minimum of the two line heights).
   - Try different pairs to find the maximum area.
   - Brute force (all pairs) is O(n²), too slow for large arrays.

2. **Recognize the Pattern**:

   - Two-pointer problem, like #125 (Valid Palindrome) and #344 (Reverse String), using `left` and `right` pointers moving inward.
   - Start with `left = 0`, `right = length - 1` for maximum width.
   - Compute area, move the pointer at the shorter line to try increasing height.

3. **Key Insight**:

   - **Area Calculation**: Area = `(right - left) * min(height[left], height[right])`. Width is `right - left` (index difference, as indices are x-axis positions). Height is the minimum, as water spills over the shorter line.
   - Move the shorter line’s pointer to potentially increase height, balancing reduced width.
   - Track maximum area.

4. **Initial Confusion**:
   - **Width Calculation**: Wondered if width could be `height[left] - height[right]`. Realized this gives the height difference (vertical), not the horizontal distance between positions. Width uses indices (`right - left`), as `height[i]` is the vertical height at position `i`.
   - **Area Formula**: Was confused why we use `minHeight`. Visualized water spilling over the shorter line, confirming `min(height[left], height[right])`.
   - Considered brute force, but two pointers are O(n), like #125’s inward movement.

**Connection to Other Problems**:

- **LeetCode #26 (Remove Duplicates from Sorted Array)**: Uses `write` and `i` to place unique elements. #11 uses `left` and `right` for area calculation.
- **LeetCode #27 (Remove Element)**: Moves non-`val` elements with pointers, like #11’s iteration.
- **LeetCode #125 (Valid Palindrome)**: Uses `left` and `right` for symmetry, like #11’s inward movement.
- **LeetCode #283 (Move Zeroes)**: Moves non-zero elements, like #11’s array processing.
- **LeetCode #344 (Reverse String)**: Swaps elements with pointers, like #11’s traversal.

## Optimized Solution

My solution uses a two-pointer approach to maximize the area by starting with the widest container and moving the shorter line’s pointer.

```javascript
/**
 * @param {number[]} height
 * @return {number}
 */
var maxArea = function (height) {
  // Initialize left pointer at start and right pointer at end
  let left = 0;
  let right = height.length - 1;
  // Track maximum area found
  let maxArea = 0;

  // Continue until pointers meet
  while (left < right) {
    // Calculate width as distance between indices
    let width = right - left;
    // Use minimum height, as water spills over shorter line
    let minHeight = Math.min(height[left], height[right]);
    // Calculate area as width * minHeight
    let area = width * minHeight;
    // Update maxArea if current area is larger
    maxArea = Math.max(maxArea, area);

    // Move pointer at shorter line to try increasing height
    if (height[left] <= height[right]) {
      left++;
    } else {
      right--;
    }
  }

  // Return maximum area found
  return maxArea;
};
```

### Explanation

- **Initialize**: `left = 0`, `right = height.length - 1`, `maxArea = 0`.
- **Loop**: While `left < right`:
  - Width: `right - left`, as indices are x-axis positions.
  - Height: `min(height[left], height[right])`, limited by the shorter line.
  - Area: `width * minHeight`.
  - Update `maxArea` if larger.
  - Move shorter line’s pointer to try increasing height.
- **Return**: `maxArea`.

### Line-by-Line Explanation

```javascript
/**
 * @param {number[]} height
 * @return {number}
 */
var maxArea = function(height) {
    // Initialize left pointer at start and right pointer at end
    let left = 0;
    let right = height.length - 1;
    // Track maximum area found
    let maxArea = 0;
```

- Initializes `left`, `right`, `maxArea`.

```javascript
    // Continue until pointers meet
    while (left < right) {
        // Calculate width as distance between indices
        let width = right - left;
        // Use minimum height, as water spills over shorter line
        let minHeight = Math.min(height[left], height[right]);
        // Calculate area as width * minHeight
        let area = width * minHeight;
        // Update maxArea if current area is larger
        maxArea = Math.max(maxArea, area);
```

- Computes width (index difference), minimum height, area, and updates `maxArea`.

```javascript
// Move pointer at shorter line to try increasing height
if (height[left] <= height[right]) {
  left++;
} else {
  right--;
}
```

- Moves the shorter line’s pointer to potentially increase height.

```javascript
    }
    // Return maximum area found
    return maxArea;
```

- Returns the maximum area.

### Time Complexity

- **Time**: `O(n)`, pointers move at most `n/2` times, total O(n).
- **Space**: `O(1)`, uses `left`, `right`, `maxArea`.

### Example

For `height = [1,8,6,2,5,4,8,3,7]`:

- Initialize: `left = 0`, `right = 8`, `maxArea = 0`.
- Iteration 1: `left = 0` (`height[0] = 1`), `right = 8` (`height[8] = 7`), width = `8 - 1 = 7`, minHeight = `min(1,7) = 1`, area = `7 * 1 = 7`, `maxArea = 7`. `height[0] <= height[8]`, `left = 1`.
- Iteration 2: `left = 1` (`height[1] = 8`), `right = 8` (`height[8] = 7`), width = `7`, minHeight = `min(8,7) = 7`, area = `7 * 7 = 49`, `maxArea = 49`. `height[1] > height[8]`, `right = 7`.
- Iteration 3: `left = 1` (`height[1] = 8`), `right = 7` (`height[7] = 3`), width = `6`, minHeight = `min(8,3) = 3`, area = `6 * 3 = 18`, `maxArea = 49`. `height[1] > height[7]`, `right = 6`.
- Continue until `left` meets `right`. Largest area is `49`.
- Return: `49`.

**Bug I Encountered**:

- Confused why width isn’t `height[left] - height[right]`. Realized `height[i]` is vertical height, not position. Width uses indices (`right - left`), confirmed by visualizing lines as fences.
- Was unsure why `minHeight` for area. Visualized water spilling over the shorter line.
- Considered brute force (O(n²)), but two pointers are O(n), like #125’s efficiency.

## Debugging Tips

- **Log Progress**:
  ```javascript
  console.log(
    `left=${left}, right=${right}, width=${width}, minHeight=${minHeight}, area=${area}, maxArea=${maxArea}`
  );
  ```
- **Test Cases**:
  - `[1,8,6,2,5,4,8,3,7]` → `49`
  - `[1,1]` → `1`
  - `[4,3,2,1,4]` → `16`
  - `[1,2,1]` → `2`
  - `[2,3,4,5,18,17,6]` → `17`

## Key Takeaways

- **Two-Pointer Pattern**: Uses `left` and `right` to maximize area, moving inward like #125/#344, optimizing a computed value (area).
- **Width Calculation**: Width = `right - left`, as indices are x-axis positions. `height[left] - height[right]` gives height difference, not distance.
- **Area Calculation**: Area = `width * min(height[left], height[right])`, as water is limited by the shorter line.
- **Personal Insight**: Was confused by width (`height[left] - height[right]`) and `minHeight`. Visualized lines as fences and water spilling to confirm formulas, building on #125/#283’s index-based logic.
- **Connection to #26/#27/#125/#283/#344**: #26/#27/#283 move elements, #125 compares, #344 swaps, #11 optimizes area, all using two pointers.
- **Why Two Pointers Work**: Maximizes width initially, moves shorter line to optimize height, balancing trade-offs.

## Related Problems

- **LeetCode #26 (Remove Duplicates from Sorted Array)**: Moves unique elements. #11 computes areas with pointers.
- **LeetCode #27 (Remove Element)**: Moves non-`val` elements, like #11’s iteration.
- **LeetCode #125 (Valid Palindrome)**: Checks symmetry with `left` and `right`, like #11’s inward movement.
- **LeetCode #283 (Move Zeroes)**: Moves non-zero elements, like #11’s array processing.
- **LeetCode #344 (Reverse String)**: Swaps elements, like #11’s traversal.

## Notes for Future Review

- **Why Two-Pointer Works**: `left` and `right` maximize width, moving the shorter line to optimize height, unlike #283’s forward placement.
- **Personal Insight**: Clarified width as `right - left` after confusing it with `height[left] - height[right]`. Visualized indices as x-axis positions and water levels for `minHeight`, building on #125/#283.
- **Practice**: Test with peaks, valleys, or equal heights to understand pointer decisions.
- **Debugging Note**: Logging width, minHeight, and area helped confirm correct formulas and pointer movement.

---
