# LeetCode #27: Remove Element

## Problem Statement

Given an integer array `nums` and an integer `val`, remove all occurrences of `val` **in-place**. The order of the remaining elements can be changed. Return the number of elements in `nums` that are not equal to `val`, denoted as `k`, and modify `nums` so the first `k` elements contain the non-`val` elements. The remaining elements are not important.

- **Constraints**:

  - `0 <= nums.length <= 100`
  - `0 <= nums[i] <= 50`
  - `0 <= val <= 100`

- **Examples**:
  - **Example 1**:
    - Input: `nums = [3,2,2,3]`, `val = 3`
    - Output: `2`, `nums = [2,2,_,_]`
    - Explanation: Return `k = 2`, with the first two elements being `2`. Elements beyond `k` don’t matter (shown as `_`).
  - **Example 2**:
    - Input: `nums = [0,1,2,2,3,0,4,2]`, `val = 2`
    - Output: `5`, `nums = [0,1,4,0,3,_,_,_]`

## Mental Model

To solve this problem, we need to remove all occurrences of `val` from the array in-place and return the count of non-`val` elements. Here’s my thought process in plain English:

1. **Simplify the Problem**:

   - We need to move all elements that are not `val` to the front of the array.
   - The order of non-`val` elements doesn’t matter.
   - Return `k`, the count of non-`val` elements, with `nums[0:k]` containing those elements.
   - Elements after `k` can be anything (don’t matter).

2. **Recognize the Pattern**:

   - This is similar to LeetCode #26, where we moved unique elements to the front using two pointers.
   - Instead of keeping unique elements, we keep elements that aren’t `val`.
   - A two-pointer approach works: one pointer to scan the array, another to place non-`val` elements.

3. **Key Insight**:

   - Use a `write` pointer to track where to place the next non-`val` element.
   - Use a read pointer (`i`) to check each element.
   - If `nums[i] !== val`, place it at `nums[write]` and increment `write`.
   - The array doesn’t need to be sorted, unlike #26, since order doesn’t matter.

4. **Initial Mistake**:
   - I initially checked `nums[i] == val` and tried to shift elements with `nums[i+1]`, which was wrong.
   - This counted `val` elements and risked out-of-bounds errors (e.g., accessing `nums[i+1]`).
   - Realized I needed to check `nums[i] !== val` and place `nums[i]` at `write`.

**Connection to Other Problems**:

- **LeetCode #26 (Remove Duplicates from Sorted Array)**: Both use two pointers to rearrange the array in-place. #26 keeps unique elements in a sorted array, while #27 keeps non-`val` elements, and order doesn’t matter.

## Optimized Solution

My corrected solution uses a two-pointer approach to place non-`val` elements at the front of the array in one pass.

```javascript
/**
 * @param {number[]} nums
 * @param {number} val
 * @return {number}
 */
var removeElement = function (nums, val) {
  // We need to modify the array in-place without redeclaring it
  let write = 0;
  for (let i = 0; i < nums.length; i++) {
    if (nums[i] !== val) {
      nums[write] = nums[i];
      write++;
    }
  }
  return write;
};
```

### Explanation

- **Initialize**: Set `write = 0` to track the position for the next non-`val` element.
- **Loop**: Iterate through `nums` with `i` from `0` to `nums.length - 1`.
  - If `nums[i] !== val`, place `nums[i]` at `nums[write]` and increment `write`.
  - If `nums[i] == val`, skip to ignore `val`.
- **Return**: `write` is the count of non-`val` elements, and `nums[0:write]` contains those elements.

### Line-by-Line Explanation

```javascript
var removeElement = function(nums, val) {
```

- Defines a function taking array `nums` and value `val` to remove.

```javascript
let write = 0;
```

- Initializes `write` to `0`, the position where the next non-`val` element will be placed.

```javascript
    for (let i = 0; i < nums.length; i++) {
```

- Loops through the array with `i` as the read pointer, checking each element.

```javascript
        if (nums[i] !== val) {
```

- Checks if the current number (`nums[i]`) is not equal to `val`, meaning we want to keep it.

```javascript
nums[write] = nums[i];
```

- Places the non-`val` number (`nums[i]`) at the position indexed by `write` (`nums[write]`), overwriting the previous value.

```javascript
write++;
```

- Increments `write` to point to the next position for a non-`val` element.

```javascript
        }
```

- Closes the `if` block. Elements equal to `val` are skipped.

```javascript
    }
```

- Closes the loop.

```javascript
return write;
```

- Returns `write`, the count of non-`val` elements, with `nums[0:write]` containing those elements.

### Time Complexity

- **Time**: `O(n)`, where `n` is `nums.length`, due to one loop through the array.
- **Space**: `O(1)`, only uses `write` and `i` variables, modifying `nums` in-place.

### Example

For `nums = [3,2,2,3]`, `val = 3`:

- Initialize: `write = 0`.
- `i = 0`: `nums[0] = 3`, `3 == val`, skip.
- `i = 1`: `nums[1] = 2`, `2 !== val`, set `nums[0] = 2`, `write = 1`. Array: `[2,2,2,3]`.
- `i = 2`: `nums[2] = 2`, `2 !== val`, set `nums[1] = 2`, `write = 2`. Array: `[2,2,2,3]`.
- `i = 3`: `nums[3] = 3`, `3 == val`, skip.
- Return: `2`, `nums = [2,2,2,3]`.
- First `k=2` elements are `[2,2]`, as required.

**Bug I Encountered**:

- Initially used `if (nums[i] == val)` and set `nums[write] = nums[i+1]`, which counted `val` elements and accessed `nums[i+1]`, risking out-of-bounds errors (e.g., `nums[4]` doesn’t exist).
- Realized I needed `nums[i] !== val` to keep non-`val` elements and place `nums[i]` at `write`.
- The `console.log(write)` helped debug but confirmed I was incrementing `write` for the wrong elements.

## Debugging Tips

- **Log Progress**: To understand the solution, add logs:
  ```javascript
  if (nums[i] !== val) {
    console.log(`Keeping nums[${i}]=${nums[i]}, placing at write=${write}`);
    nums[write] = nums[i];
    write++;
  }
  ```
- **Test Cases**:
  - `[3,2,2,3]`, `val = 3` → `2`, `[2,2,_,_]`
  - `[0,1,2,2,3,0,4,2]`, `val = 2` → `5`, `[0,1,4,0,3,_,_,_]`
  - `[]`, `val = 1` → `0`, `[]`
  - `[1]`, `val = 1` → `0`, `[_]`
  - `[1]`, `val = 2` → `1`, `[1]`

## Key Takeaways

- **Two-Pointer Pattern**: Uses `write` (destination for non-`val` elements) and `i` (scanning the array) to rearrange `nums` in-place. `write` moves slower, only for non-`val` elements.
- **In-Place Requirement**: Must modify `nums` without extra space, placing non-`val` elements in the first `k` positions.
- **Assignment Insight**: `nums[write] = nums[i]` places the non-`val` number (`nums[i]`) at the position indexed by `write`. Initially confused, thinking I should act on `val` elements.
- **Connection to #26**: Both use two pointers to rearrange arrays in-place. #26 keeps unique elements in a sorted array (order matters), #27 keeps non-`val` elements (order doesn’t matter).
- **Personal Insight**: My initial submission checked `nums[i] == val` and used `nums[i+1]`, which was wrong. Learned to focus on non-`val` elements and use `nums[i]`.

## Related Problems

- **LeetCode #26 (Remove Duplicates from Sorted Array)**: Uses two pointers (`write` and `i`) to place unique elements at the front of a sorted array, maintaining order. #27 is similar but filters out a specific value, and order is flexible.
- **LeetCode #283 (Move Zeroes)**: Uses two pointers to move non-zero elements to the front, similar to filtering out `val` in #27.

## Notes for Future Review

- **Why Two-Pointer Works**: `write` tracks the position for non-`val` elements, `i` scans the array. Only non-`val` elements are placed, similar to #26’s unique elements.
- **Personal Insight**: Misunderstood the condition, acting on `val` instead of non-`val` elements. Accessing `nums[i+1]` caused errors. Learned to use `nums[i] !== val` and place `nums[i]` at `write`.
- **Practice**: Test edge cases like empty arrays, all `val` elements, or no `val` elements to confirm the solution handles everything.
- **Debugging Note**: Logging `write` in the wrong condition (`nums[i] == val`) showed I was counting `val` elements. Switching to `nums[i] !== val` and logging helped clarify the correct flow.

---
