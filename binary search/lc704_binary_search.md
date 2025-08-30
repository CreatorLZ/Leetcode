## LeetCode #704: Binary Search - Documentation

### Problem Statement

Given a **sorted** array of integers `nums` (in **ascending order**) and an integer `target`, find the index of `target` in `nums`. If `target` is not found, return `-1`.

- **Input**:
  - `nums`: An array of integers, sorted in ascending order, with unique elements.
  - `target`: An integer to search for in `nums`.
- **Output**: The index of `target` in `nums`, or `-1` if not found.

### Constraints

- `1 <= nums.length <= 10^4`
- `-10^4 <= nums[i], target <= 10^4`
- All integers in `nums` are **unique**.
- `nums` is sorted in **ascending order**.

### Examples

- **Example 1**:
  - **Input**: `nums = [-1,0,3,5,9,12], target = 9`
  - **Output**: `4`
  - **Explanation**: `9` is at index `4` in `nums`.
- **Example 2**:
  - **Input**: `nums = [-1,0,3,5,9,12], target = 2`
  - **Output**: `-1`
  - **Explanation**: `2` is not in `nums`.
- **Example 3**:
  - **Input**: `nums = [5], target = 5`
  - **Output**: `0`
  - **Explanation**: `5` is at index `0`.

### Analogy

Imagine you’re looking for a specific book (e.g., “Book 9”) on a shelf where books are arranged in ascending order by number (`-1, 0, 3, 5, 9, 12`). Your solution checks each book from left to right (like browsing a shelf sequentially). Binary search, however, uses the sorted order to jump to the middle book, decide if “Book 9” is left or right, and repeat, checking fewer books each time.

---

## Solution 1: Linear Search Using `indexOf` (Your Solution)

My solution uses JavaScript’s `indexOf` method to find the target. Below is the corrected version of my code (fixing the syntax error with the `if` condition and adding the return for the not-found case . the original one actually worked ironically ! lmao heres it for fun and future investigation first):

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var search = function (nums, target) {
  if (nums.indexOf(target !== -1)) {
    return nums.indexOf(target);
  }
};
```

Heres the corrected version

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var search = function (nums, target) {
  if (nums.indexOf(target) !== -1) {
    return nums.indexOf(target);
  }
  return -1;
};
```

### How It Works

- **Logic**:
  - `nums.indexOf(target)` scans the array from index `0` to `nums.length - 1`, checking each element for `target`.
  - If `target` is found, `indexOf` returns its index (e.g., `9` at index `4`).
  - If not found, `indexOf` returns `-1`.
- **Analogy**: You’re checking each book on the shelf one by one: “Is it -1? No. Is it 0? No. Is it 3? No. Is it 5? No. Is it 9? Yes, found at position 4!” If you reach the end without finding the book, you say, “Not here.”
- **Walkthrough with Example** (`nums = [-1,0,3,5,9,12], target = 9`):
  - `indexOf(9)` checks: `-1` (no), `0` (no), `3` (no), `5` (no), `9` (found at index `4`).
  - Return `4`.
- **Walkthrough with Example** (`nums = [-1,0,3,5,9,12], target = 2`):
  - `indexOf(2)` checks all elements: `-1, 0, 3, 5, 9, 12` (not found).
  - Return `-1`.

### Time and Space Complexity

- **Time Complexity**: `O(n)`, where `n` is `nums.length`. `indexOf` may check every element in the worst case (e.g., `target` is at the end or missing).
- **Space Complexity**: `O(1)`, as no extra space is used beyond variables.
- **Pros**:
  - Simple and concise.
  - Works for unsorted arrays (though #704 guarantees sorted).
  - Easy to understand for beginners.
- **Cons**:
  - Inefficient for large arrays (e.g., `n = 10^4`, could check 10,000 elements).
  - Doesn’t use the sorted property of `nums`, missing the problem’s intent for binary search.

---

## Solution 2: Binary Search

This solution uses the binary search algorithm, leveraging the sorted nature of `nums` to find `target` efficiently.

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var search = function (nums, target) {
  // Initialize pointers to the start and end of the array
  let left = 0;
  let right = nums.length - 1;

  // Continue searching while the search space is valid
  while (left <= right) {
    // Calculate the middle index
    let mid = Math.floor((left + right) / 2);

    // If target is found, return its index
    if (nums[mid] === target) {
      return mid;
    }
    // If target is smaller, search the left half
    else if (nums[mid] > target) {
      right = mid - 1;
    }
    // If target is larger, search the right half
    else {
      left = mid + 1;
    }
  }

  // Target not found
  return -1;
};
```

### How It Works

- **Logic**:
  - Use two pointers, `left` (start) and `right` (end), to define the search range.
  - Calculate the middle index: `mid = Math.floor((left + right) / 2)`.
  - Compare `nums[mid]` with `target`:
    - If equal, return `mid`.
    - If `nums[mid] > target`, search the left half (`right = mid - 1`).
    - If `nums[mid] < target`, search the right half (`left = mid + 1`).
  - Repeat until `left > right` (target not found, return `-1`).
- **Analogy**: You’re looking for “Book 9” on a sorted shelf. Check the middle book. If it’s “3” (too small), look at the right half (larger numbers). If it’s “12” (too big), look left. Each check cuts the shelf in half until you find the book or run out of books.
- **Walkthrough with Example** (`nums = [-1,0,3,5,9,12], target = 9`):
  - Initialize: `left = 0`, `right = 5` (indices 0 to 5).
  - **Iteration 1**:
    - `mid = Math.floor((0 + 5) / 2) = 2`.
    - `nums[2] = 3`, `3 < 9`, so `left = mid + 1 = 3`.
    - **Analogy**: Middle book is “3” (too small), check right half (indices 3-5).
  - **Iteration 2**:
    - `left = 3`, `right = 5`.
    - `mid = Math.floor((3 + 5) / 2) = 4`.
    - `nums[4] = 9`, matches `target`, return `4`.
    - **Analogy**: Middle book is “9”—found it!
- **Walkthrough with Example** (`nums = [-1,0,3,5,9,12], target = 2`):
  - Initialize: `left = 0`, `right = 5`.
  - **Iteration 1**: `mid = 2`, `nums[2] = 3`, `3 > 2`, `right = mid - 1 = 1`.
  - **Iteration 2**: `mid = Math.floor((0 + 1) / 2) = 0`, `nums[0] = -1`, `-1 < 2`, `left = 0 + 1 = 1`.
  - **Iteration 3**: `mid = Math.floor((1 + 1) / 2) = 1`, `nums[1] = 0`, `0 < 2`, `left = 1 + 1 = 2`.
  - **Exit**: `left = 2 > right = 1`, return `-1`.

### Time and Space Complexity

- **Time Complexity**: `O(log n)`, where `n` is `nums.length`. Each iteration halves the search space (e.g., for `n = 10^4`, at most `log_2(10^4) ≈ 13.3` steps).
- **Space Complexity**: `O(1)`, only uses a few variables (`left`, `right`, `mid`).
- **Pros**:
  - Highly efficient for large sorted arrays (e.g., 13 steps vs. 10,000 for `n = 10^4`).
  - Meets the problem’s intent by using the sorted property.
- **Cons**:
  - Slightly more complex than `indexOf`.
  - Only works for sorted arrays.

---

## Comparison of Solutions

| **Aspect**                 | **My Solution (`indexOf`)**                      | **Binary Search Solution**                             |
| -------------------------- | ------------------------------------------------ | ------------------------------------------------------ |
| **Approach**               | Linear search: Checks each element sequentially. | Binary search: Divides search space in half each step. |
| **Time Complexity**        | `O(n)` (up to `n` checks).                       | `O(log n)` (at most `log_2(n)` checks).                |
| **Space Complexity**       | `O(1)` (no extra space).                         | `O(1)` (no extra space).                               |
| **Code Simplicity**        | Very simple (2 lines).                           | More complex (requires pointers and loop).             |
| **Use of Sorted Property** | No (works for unsorted arrays).                  | Yes (requires sorted array).                           |
| **Performance Example**    | For `n = 10^4`, may check 10,000 elements.       | For `n = 10^4`, at most 14 steps.                      |
| **Meets Problem Intent**   | No (expects binary search).                      | Yes (designed for sorted arrays).                      |

- **Your Solution**: Great for simplicity and works for any array, but it’s slower because it doesn’t use the fact that `nums` is sorted. It’s like checking every book on the shelf, even though they’re in order.
- **Binary Search**: More efficient by leveraging the sorted order, cutting the search space in half each time. It’s like jumping to the middle of the shelf and narrowing down the search.

---

## Why Binary Search is Expected

**LeetCode #704** is named “Binary Search” because the problem’s constraints (sorted array, unique elements, up to 10^4 elements) make binary search the optimal approach. My `indexOf` solution is correct and passes the tests, but it’s less efficient (`O(n)` vs. `O(log n)`). For large arrays, binary search saves significant time, which is why the problem emphasizes this technique.

- **Example Efficiency**:
  - For `nums = [-1,0,3,5,9,12], target = 12`:
    - `indexOf`: Checks 6 elements (worst case).
    - Binary Search: Takes 3 steps (checks indices 2, 4, 5).
  - For `n = 10^4`, `indexOf` could check 10,000 elements, while binary search takes at most 14 steps.

---

## Debugging Tips

- **For Your Solution**:

  - **Check Edge Cases**:
    - `nums = [5], target = 5` → `0`
    - `nums = [5], target = 6` → `-1`
    - `nums = [1,2,3,4,5], target = 1` (first element)
    - `nums = [1,2,3,4,5], target = 5` (last element)
  - **Ensure Return**: Your original code missed `return -1` for the not-found case, which is fixed above.

- **For Binary Search**:
  - **Log Progress**:
    ```javascript
    console.log(
      `left=${left}, right=${right}, mid=${mid}, nums[mid]=${nums[mid]}`
    );
    ```
  - **Test Edge Cases**: Same as above.
  - **Avoid Overflow**: Use `Math.floor((left + right) / 2)` for `mid` (though less critical in JavaScript).
  - **Check Loop Condition**: Ensure `left <= right` to include the last possible index.

---

## Connection to Your Learning Journey

## My solution for #704 shows i am thinking practically, using a built-in method (`indexOf`) to solve the problem quickly. The binary search solution aligns with the problem’s intent.

## Final Takeaways

- **My initial Solution (`indexOf`)**:
  - Correct and simple, using linear search (`O(n)`).
  - Doesn’t leverage the sorted property, making it slower for large arrays.
  - Great for quick prototyping or unsorted arrays.
- **Binary Search Solution**:
  - Optimal for #704 (`O(log n)`), using the sorted property to halve the search space each step.
  - More complex but significantly faster for large arrays.
  - Meets the problem’s expectation for learning binary search.
- **Recommendation**: Use binary search for sorted arrays in problems like #704, especially with large inputs (up to 10^4). Your `indexOf` solution is a great starting point, and now you can add binary search to your toolkit!
