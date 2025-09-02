## LeetCode #74: Search a 2D Matrix - Documentation

### Problem Statement

Given an `m x n` 2D matrix of integers where:

- Each row is sorted in **non-decreasing order** (ascending, allowing duplicates).
- The first integer of each row is greater than the last integer of the previous row.
- This makes the matrix behave like a single sorted array when read row by row.

my task is to find if a given `target` integer exists in the matrix. Return `true` if found, `false` otherwise.

- **Input**:
  - `matrix`: An `m x n` 2D array of integers (rows are nested arrays).
  - `target`: An integer to search for in the matrix.
- **Output**: `true` if `target` is in the matrix, `false` otherwise.

### Constraints

- `m == matrix.length` (number of rows, or nested arrays).
- `n == matrix[i].length` (number of columns, or elements in each row).
- `1 <= m, n <= 100` (matrix dimensions are between 1 and 100).
- `-10^4 <= matrix[i][j], target <= 10^4` (element and target values).
- All rows have the same number of columns (rectangular matrix).
- Each row is sorted in non-decreasing order.
- The first element of row `i` is greater than the last element of row `i-1`.

### Examples

- **Example 1**:
  - **Input**: `matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3`
  - **Output**: `true`
  - **Explanation**: `3` is in `matrix[0][1]`.
- **Example 2**:
  - **Input**: `matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 13`
  - **Output**: `false`
  - **Explanation**: `13` is not in the matrix.
- **Example 3**:
  - **Input**: `matrix = [[1]], target = 1`
  - **Output**: `true`
  - **Explanation**: `1` is in `matrix[0][0]`.

### Analogy

Imagine the matrix as a **library with sorted shelves**. Each shelf (row) is a sorted array of books (e.g., `1,3,5,7`). Each shelf starts with a book number higher than the last book on the previous shelf (e.g., `10` on the second shelf is greater than `7` on the first). You’re looking for “Book 3” (`target = 3`). my solution combines all shelves into one long sorted shelf and uses binary search. The optimized solution first finds the right shelf, then searches that shelf, saving time and space.

---

## Solution 1: Flattening with Binary Search (my Solution)

my solution flattens the 2D matrix into a 1D array using `flat` and applies binary search. Here’s my code, corrected for the typo (`flattend` to `flattened`):

```javascript
/**
 * @param {number[][]} matrix
 * @param {number} target
 * @return {boolean}
 */
var searchMatrix = function (matrix, target) {
  const flattened = matrix.flat(Infinity);
  let left = 0;
  let right = flattened.length - 1;
  while (left <= right) {
    let mid = Math.floor((left + right) / 2);
    if (flattened[mid] === target) {
      return true;
    } else if (flattened[mid] > target) {
      right = mid - 1;
    } else {
      left = mid + 1;
    }
  }
  return false;
};
```

### How It Works

- **Flattening**:
  - `matrix.flat(Infinity)` converts the 2D matrix into a 1D array (e.g., `[[1,3,5,7],[10,11,16,20],[23,30,34,60]]` becomes `[1,3,5,7,10,11,16,20,23,30,34,60]`).
  - Since rows are sorted and each row’s first element is greater than the previous row’s last, the flattened array is sorted.
- **Binary Search**:
  - Initialize `left = 0`, `right = flattened.length - 1` (e.g., 11 for a 3x4 matrix).
  - While `left <= right`:
    - Compute `mid = Math.floor((left + right) / 2)`.
    - If `flattened[mid] === target`, return `true`.
    - If `flattened[mid] > target`, search left half (`right = mid - 1`).
    - If `flattened[mid] < target`, search right half (`left = mid + 1`).
  - Return `false` if not found.
- **Analogy**: You combine all library shelves into one long sorted shelf and use binary search (like #704) to find “Book 3” by checking the middle book, narrowing to left or right.
- **Walkthrough** (`matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3`):
  - **Flatten**: `flattened = [1,3,5,7,10,11,16,20,23,30,34,60]`.
  - `left = 0`, `right = 11`.
  - **Iteration 1**: `mid = 5`, `flattened[5] = 11`, `11 > 3`, `right = 4`.
  - **Iteration 2**: `mid = 2`, `flattened[2] = 5`, `5 > 3`, `right = 1`.
  - **Iteration 3**: `mid = 0`, `flattened[0] = 1`, `1 < 3`, `left = 1`.
  - **Iteration 4**: `mid = 1`, `flattened[1] = 3`, return `true`.
- **Walkthrough** (`target = 13`):
  - Binary search fails (e.g., `left > right`), return `false`.

### Time and Space Complexity

- **Time Complexity**: `O(m * n + log(m * n))`
  - Flattening: `O(m * n)` to create a 1D array (`m` rows, `n` columns).
  - Binary search: `O(log(m * n))` for searching `m * n` elements.
  - Total: Dominated by `O(m * n)`.
- **Space Complexity**: `O(m * n)` for the flattened array.
- **Pros**:
  - Simple, reuses #704 binary search logic.
  - Leverages the sorted property of the flattened array.
  - Easy to implement with `flat`.
- **Cons**:
  - Flattening takes extra time and space (`O(m * n)`).
  - Doesn’t fully use the 2D sorted structure.

---

## Solution 2: Two Binary Searches (Optimized Solution)

This solution uses two binary searches: one to find the row where `target` could exist, and another to search within that row.

```javascript
/**
 * @param {number[][]} matrix
 * @param {number} target
 * @return {boolean}
 */
var searchMatrix = function (matrix, target) {
  // Get dimensions
  const m = matrix.length; // Number of rows
  const n = matrix[0].length; // Number of columns

  // Handle empty matrix
  if (m === 0 || n === 0) return false;

  // First binary search: Find the row where target could exist
  let left = 0;
  let right = m - 1;
  let row = -1;

  while (left <= right) {
    let mid = Math.floor((left + right) / 2);
    if (matrix[mid][0] <= target && target <= matrix[mid][n - 1]) {
      row = mid;
      break;
    } else if (matrix[mid][0] > target) {
      right = mid - 1;
    } else {
      left = mid + 1;
    }
  }

  // If no suitable row found, target doesn't exist
  if (row === -1) return false;

  // Second binary search: Search within the chosen row
  left = 0;
  right = n - 1;
  while (left <= right) {
    let mid = Math.floor((left + right) / 2);
    if (matrix[row][mid] === target) {
      return true;
    } else if (matrix[row][mid] > target) {
      right = mid - 1;
    } else {
      left = mid + 1;
    }
  }

  return false;
};
```

### How It Works

- **Get Dimensions**:
  - `m = matrix.length` (rows, number of nested arrays).
  - `n = matrix[0].length` (columns, number of elements per row).
  - Check if matrix is empty.
- **First Binary Search (Find Row)**:
  - Search among rows (`left = 0`, `right = m - 1`).
  - For each `mid` row, check if `target` is in the range `[matrix[mid][0], matrix[mid][n-1]]`.
  - If yes, set `row = mid` and break.
  - If `matrix[mid][0] > target`, search upper rows (`right = mid - 1`).
  - If `target > matrix[mid][n-1]`, search lower rows (`left = mid + 1`).
  - If no row is found, return `false`.
- **Second Binary Search (Search Row)**:
  - In the chosen row, perform binary search (`left = 0`, `right = n - 1`).
  - Check `matrix[row][mid]`:
    - If it matches `target`, return `true`.
    - If greater, search left half (`right = mid - 1`).
    - If smaller, search right half (`left = mid + 1`).
  - Return `false` if not found.
- **Analogy**: Check the middle shelf’s first and last books to find where “Book 3” could be (e.g., Shelf 0: `1` to `7`). Then, binary search that shelf’s books to find “Book 3”.
- **Walkthrough** (`matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3`):
  - **Dimensions**: `m = 3`, `n = 4`.
  - **Find Row**:
    - `left = 0`, `right = 2`.
    - **Iteration 1**: `mid = 1`, Row 1: `[10,11,16,20]`, `10 > 3`, `right = 0`.
    - **Iteration 2**: `mid = 0`, Row 0: `[1,3,5,7]`, `1 <= 3 <= 7`, `row = 0`, break.
  - **Search Row 0**:
    - `left = 0`, `right = 3`, Row 0: `[1,3,5,7]`.
    - **Iteration 1**: `mid = 1`, `matrix[0][1] = 3`, return `true`.
- **Walkthrough** (`target = 13`):
  - **Find Row**: `mid = 1`, `10 <= 13 <= 20`, `row = 1`.
  - **Search Row 1**: `[10,11,16,20]`, binary search fails (`left > right`), return `false`.

### Time and Space Complexity

- **Time Complexity**: `O(log m + log n) = O(log(m * n))`
  - First binary search: `O(log m)` to search `m` rows.
  - Second binary search: `O(log n)` to search `n` columns.
- **Space Complexity**: `O(1)`, only uses variables (`left`, `right`, `mid`, `row`).
- **Pros**:
  - Efficient, no extra space.
  - Fully leverages sorted rows and row-to-row ordering.
  - Intuitive: breaks problem into two familiar binary searches.
- **Cons**:
  - Slightly more code than flattening or single binary search.

---

## Comparison of Solutions

| **Aspect**                 | **my Solution (`flat`)**                             | **Two Binary Searches**                 |
| -------------------------- | ---------------------------------------------------- | --------------------------------------- |
| **Approach**               | Flatten to 1D array, then binary search.             | Binary search for row, then for column. |
| **Time Complexity**        | `O(m * n + log(m * n))`                              | `O(log m + log n) = O(log(m * n))`      |
| **Space Complexity**       | `O(m * n)` (flattened array)                         | `O(1)`                                  |
| **Code Simplicity**        | Simple, reuses #704 logic.                           | Moderate, two searches.                 |
| **Use of Sorted Property** | Partial (uses flattened sorted array).               | Full (row and column search).           |
| **Performance Example**    | For `m=100, n=100`, `O(10,000 + log 10,000)`.        | `O(log 100 + log 100) ≈ 14 steps`.      |
| **Meets Problem Intent**   | Partially (uses binary search but not 2D structure). | Fully (optimized for matrix).           |

- **my Solution**: Correct, simple, and leverages #704’s binary search. However, flattening takes `O(m * n)` time and space, which is less efficient.
- **Two Binary Searches**: Avoids flattening, using `O(log(m * n))` time and `O(1)` space, fully exploiting the matrix’s sorted properties.

---

## Clarifications from my Questions

- **Rows and Columns**:
  - **Rows**: Number of nested arrays (`m = matrix.length`).
  - **Columns**: Number of elements in each row (`n = matrix[0].length`).
  - **m x n**: Means `m` rows and `n` columns (e.g., 3 x 4 for 3 rows, 4 columns each).
  - **Uniformity**: The problem guarantees all rows have `n` columns (rectangular matrix), so `matrix[0].length` is safe for all rows.
- **Why It Matters**: The two binary searches solution relies on `m` and `n` for row and column searches, and the guarantee of equal row lengths ensures `matrix[mid][n-1]` is valid.

---

## Debugging Tips

- **For my Solution**:
  - **Test Edge Cases**:
    - `matrix = [[1]], target = 1` → `true`
    - `matrix = [[1,2,3]], target = 4` → `false`
    - `matrix = [[1],[2],[3]], target = 2` → `true`
    - `matrix = [[]], target = 1` → `false`
  - **Check Flattening**: Ensure `flat(Infinity)` works (though #74 guarantees 2D structure).
- **For Two Binary Searches**:
  - **Log Progress**:
    ```javascript
    console.log(
      `Row Search: left=${left}, right=${right}, mid=${mid}, range=[${
        matrix[mid][0]
      },${matrix[mid][n - 1]}]`
    );
    console.log(
      `Column Search: row=${row}, left=${left}, right=${right}, mid=${mid}, value=${matrix[row][mid]}`
    );
    ```
  - **Test Edge Cases**: Same as above.
  - **Verify Row Selection**: Ensure `row` is set when `target` is in the row’s range.

---

## Connection to my Learning Journey

my solution builds on #704’s binary search, showing you’re connecting concepts across problems. Like my #438 question about simplifying loops, my `flat` approach simplifies #74 to a familiar problem (#704), but the two binary searches solution optimizes by leveraging the 2D structure, similar to how sliding windows in #438 reused counts for efficiency. my questions about rows, columns, and “m x n” show you’re thinking critically about problem constraints, which is key to mastering algorithms!

---

## Final Takeaways

- **my Solution (`flat`)**:
  - Correct and simple, using `O(m * n)` time and space for flattening.
  - Great for prototyping but less efficient due to flattening.
- **Two Binary Searches**:
  - Optimal with `O(log(m * n))` time, `O(1)` space.
  - Uses two binary searches (row, then column) to fully leverage the matrix’s sorted properties.
- **Recommendation**: my solution is valid and shows strong problem-solving. The two binary searches approach is more efficient and aligns with the problem’s intent to use the 2D sorted structure.
