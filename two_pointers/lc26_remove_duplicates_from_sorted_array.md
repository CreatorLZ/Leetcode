# LeetCode #26: Remove Duplicates from Sorted Array

## Problem Statement

Given an integer array `nums` sorted in **non-decreasing order**, remove the duplicates **in-place** such that each unique element appears only once. The relative order of the elements should be kept the same. Return the number of unique elements `k`, and modify `nums` so the first `k` elements contain the unique elements in order. The remaining elements are not important.

- **Constraints**:

  - `1 <= nums.length <= 3 * 10^4`
  - `-100 <= nums[i] <= 100`
  - `nums` is sorted in non-decreasing order.

- **Examples**:
  - **Example 1**:
    - Input: `nums = [1,1,2]`
    - Output: `2`, `nums = [1,2,_]`
    - Explanation: Return `k = 2`, with the first two elements being `1` and `2`. The `_` indicates elements beyond `k` don’t matter.
  - **Example 2**:
    - Input: `nums = [0,0,1,1,1,2,2,3,3,4]`
    - Output: `5`, `nums = [0,1,2,3,4,_,_,_,_,_]`
    - Explanation: Return `k = 5`, with the first five elements being `0,1,2,3,4`.

## Mental Model

To solve this problem, we need to remove duplicates from a sorted array in-place and return the count of unique elements. Here’s the thought process in plain English:

1. **Understand the Goal**:

   - We need to modify `nums` so the first `k` elements are unique numbers in their original order (e.g., `[0,1,2,3,4]`).
   - Return `k`, the number of unique elements.
   - Since the array is sorted, duplicates are next to each other (e.g., `[1,1,2]` has two `1`s together).

2. **Key Insight**:

   - We can use a pointer (`write`) to track where to place the next unique number.
   - Loop through the array, checking for unique numbers, and place them at the `write` position.
   - Since it’s sorted, we only need to compare with the previous number to detect duplicates.

3. **Brute Force Idea**:

   - Use a Set to track seen numbers.
   - Place unique numbers at the front of the array as we find them.
   - Count unique numbers.

4. **Optimized Idea**:
   - Since the array is sorted, we don’t need a Set. Just compare each number with the previous one.
   - Move unique numbers to the front using a pointer.

**Connection to Other Problems**:

- (Skipped, as no previously solved problems on two pointers yet.)

## Brute-Force Solution

The brute-force approach uses a Set to track unique numbers and moves them to the front of the array.

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
function removeDuplicates(nums) {
  let seen = new Set();
  let write = 0;
  for (let i = 0; i < nums.length; i++) {
    if (!seen.has(nums[i])) {
      seen.add(nums[i]);
      nums[write] = nums[i];
      write++;
    }
  }
  return write;
}
```

### Explanation

- **Initialize**:
  - Create a `Set` (`seen`) to track unique numbers.
  - Set `write = 0` to track the position for the next unique number.
- **Loop**:
  - Iterate through `nums` with index `i`.
  - If `nums[i]` is not in the Set, it’s unique:
    - Add it to the Set.
    - Place it at `nums[write]`.
    - Increment `write`.
- **Return**: `write` is the count of unique numbers, and `nums[0:write]` contains the unique numbers in order.

### Line-by-Line Explanation

```javascript
function removeDuplicates(nums) {
```

- Defines a function that takes an array `nums`.

```javascript
let seen = new Set();
```

- Creates a Set to store unique numbers.

```javascript
let write = 0;
```

- Initializes `write` to `0`, the index where the next unique number goes.

```javascript
    for (let i = 0; i < nums.length; i++) {
```

- Loops through the array from index `0` to the end.

```javascript
        if (!seen.has(nums[i])) {
```

- Checks if the number at index `i` (`nums[i]`) is not in the Set (i.e., it’s unique).

```javascript
seen.add(nums[i]);
```

- Adds the unique number to the Set to mark it as seen.

```javascript
nums[write] = nums[i];
```

- Places the unique number (`nums[i]`) at the position indexed by `write` (`nums[write]`), overwriting the previous value.

```javascript
write++;
```

- Increments `write` to point to the next position for a unique number.

```javascript
        }
```

- Closes the `if` block. Duplicates are skipped.

```javascript
    }
```

- Closes the loop.

```javascript
return write;
```

- Returns `write`, the count of unique numbers, with `nums[0:write]` containing unique numbers.

### Time Complexity

- **Time**: `O(n)`, where `n` is `nums.length`, due to one loop through the array.
- **Space**: `O(n)` for the Set, which could store up to `n` unique numbers.

### Example

For `nums = [0,0,1,1,1,2,2,3,3,4]`:

- Initialize: `seen = {}`, `write = 0`.
- `i = 0`: `nums[0] = 0`, not in Set. Add `0`, set `nums[0] = 0`, `write = 1`. Array: `[0,0,1,1,1,2,2,3,3,4]`.
- `i = 1`: `nums[1] = 0`, in Set, skip.
- `i = 2`: `nums[2] = 1`, not in Set. Add `1`, set `nums[1] = 1`, `write = 2`. Array: `[0,1,1,1,1,2,2,3,3,4]`.
- `i = 3,4`: `nums[3,4] = 1`, in Set, skip.
- `i = 5`: `nums[5] = 2`, not in Set. Add `2`, set `nums[2] = 2`, `write = 3`. Array: `[0,1,2,1,1,2,2,3,3,4]`.
- `i = 6`: `nums[6] = 2`, in Set, skip.
- `i = 7`: `nums[7] = 3`, not in Set. Add `3`, set `nums[3] = 3`, `write = 4`. Array: `[0,1,2,3,1,2,2,3,3,4]`.
- `i = 8`: `nums[8] = 3`, in Set, skip.
- `i = 9`: `nums[9] = 4`, not in Set. Add `4`, set `nums[4] = 4`, `write = 5`. Array: `[0,1,2,3,4,2,2,3,3,4]`.
- Return: `5`, `nums = [0,1,2,3,4,2,2,3,3,4]`.

**Bug I Encountered**: Initially, I thought we could just count unique numbers using the Set’s size, but the problem requires modifying the array in-place. Realized `nums[write] = nums[i]` is needed to place unique numbers at the front, not just count them.

## Optimized Solution

The optimized solution leverages the sorted property, comparing adjacent elements to detect duplicates, avoiding extra space.

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
function removeDuplicates(nums) {
  let write = 1;
  for (let i = 1; i < nums.length; i++) {
    if (nums[i] !== nums[i - 1]) {
      nums[write] = nums[i];
      write++;
    }
  }
  return write;
}
```

### Explanation

- **Initialize**: Set `write = 1` since `nums[0]` is always unique (no previous number to compare).
- **Loop**:
  - Start from `i = 1`.
  - If `nums[i]` differs from `nums[i-1]` (the previous number), it’s unique:
    - Place it at `nums[write]`.
    - Increment `write`.
- **Return**: `write` is the count of unique numbers, with `nums[0:write]` containing unique numbers.

### Line-by-Line Explanation

```javascript
function removeDuplicates(nums) {
```

- Defines a function that takes an array `nums`.

```javascript
let write = 1;
```

- Initializes `write` to `1`, since `nums[0]` is unique, and the next unique number goes at index `1`.

```javascript
    for (let i = 1; i < nums.length; i++) {
```

- Loops from index `1` to the end, skipping `nums[0]` (already unique).

```javascript
        if (nums[i] !== nums[i - 1]) {
```

- Checks if the current number (`nums[i]`) differs from the previous number (`nums[i-1]`), indicating it’s unique.

```javascript
nums[write] = nums[i];
```

- Places the unique number (`nums[i]`) at the position indexed by `write` (`nums[write]`).

```javascript
write++;
```

- Increments `write` to point to the next position for a unique number.

```javascript
        }
```

- Closes the `if` block. Duplicates are skipped.

```javascript
    }
```

- Closes the loop.

```javascript
return write;
```

- Returns `write`, the count of unique numbers, with `nums[0:write]` containing unique numbers.

### Why Compare Adjacent Elements?

- Since `nums` is sorted, duplicates are adjacent (e.g., `[1,1,2]`).
- Comparing `nums[i]` with `nums[i-1]` detects unique numbers without needing a Set.
- The first occurrence of each number is kept by placing it at `write`.

### Time Complexity

- **Time**: `O(n)`, one loop through the array.
- **Space**: `O(1)`, only uses `write` and `i` variables.

### Example

For `nums = [0,0,1,1,1,2,2,3,3,4]`:

- Initialize: `write = 1`.
- `i = 1`: `nums[1] = 0`, `nums[0] = 0`, same, skip.
- `i = 2`: `nums[2] = 1`, `nums[1] = 0`, different. Set `nums[1] = 1`, `write = 2`. Array: `[0,1,1,1,1,2,2,3,3,4]`.
- `i = 3,4`: `nums[3,4] = 1`, same as previous (`1`), skip.
- `i = 5`: `nums[5] = 2`, `nums[4] = 1`, different. Set `nums[2] = 2`, `write = 3`. Array: `[0,1,2,1,1,2,2,3,3,4]`.
- `i = 6`: `nums[6] = 2`, same as previous (`2`), skip.
- `i = 7`: `nums[7] = 3`, `nums[6] = 2`, different. Set `nums[3] = 3`, `write = 4`. Array: `[0,1,2,3,1,2,2,3,3,4]`.
- `i = 8`: `nums[8] = 3`, same as previous (`3`), skip.
- `i = 9`: `nums[9] = 4`, `nums[8] = 3`, different. Set `nums[4] = 4`, `write = 5`. Array: `[0,1,2,3,4,2,2,3,3,4]`.
- Return: `5`, `nums = [0,1,2,3,4,2,2,3,3,4]`.

## Debugging Tips

- **Log Progress**:
  ```javascript
  if (!seen.has(nums[i])) {
    console.log(
      `Unique number ${nums[i]} at i=${i}, placing at write=${write}`
    );
    seen.add(nums[i]);
    nums[write] = nums[i];
    write++;
  }
  ```
  - For optimized solution:
  ```javascript
  if (nums[i] !== nums[i - 1]) {
    console.log(
      `Unique number ${nums[i]} at i=${i}, placing at write=${write}`
    );
    nums[write] = nums[i];
    write++;
  }
  ```
- **Test Cases**:
  - `[1]` → `1`, `nums = [1]`
  - `[1,1,1]` → `1`, `nums = [1,_,_]`
  - `[1,2,3]` → `3`, `nums = [1,2,3]`

## Key Takeaways

- **Sorted Array Advantage**: Duplicates are adjacent, so we can compare with the previous number (optimized) or use a Set (brute force).
- **In-Place Requirement**: Must modify `nums` to place unique numbers in the first `k` positions, not just count them.
- **Assignment Insight**: `nums[write] = nums[i]` places the unique number (`nums[i]`) at the position indexed by `write`.
- **From Brute-Force to Optimized**: Brute force uses O(n) space with a Set; optimized uses O(1) space by leveraging the sorted property.
- **Personal Insight**: Initially confused `nums[i]` as a position, but realized it’s the number being placed at position `nums[write]`.

## Related Problems

- (No previously solved problems on two pointers yet.)

## Notes for Future Review

- **Why `nums[write] = nums[i]`**: `nums[i]` is the unique number, and `nums[write]` is the position where it’s placed. Misunderstood `nums[i]` as a position initially.
- **Why Compare Adjacent Elements**: In the optimized solution, `nums[i] !== nums[i-1]` detects unique numbers because duplicates are adjacent in a sorted array.
- **Practice**: Test with edge cases like single elements or all duplicates to confirm understanding.
- **Debugging Note**: Logging `nums[i]` and `write` helped clarify that `nums[i]` is the value being moved to position `write`.

---
