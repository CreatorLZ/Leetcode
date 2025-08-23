## Analyzing Your Submission

Here’s your code:

```javascript
var reverseString = function (s) {
  let left = 0;
  let right = s.length - 1;
  while (left < right) {
    let reversed = [];
    reversed.push(s[right]);
    left++;
    right--;
  }
};
```

### What’s Correct

- **Two-Pointer Approach**: You correctly use `left` (starting at the beginning) and `right` (starting at the end) to traverse the array, aiming to reverse it by moving toward the center, just like in #125.
- **Loop Condition**: The `while (left < right)` condition is perfect, ensuring you stop when the pointers meet or cross, which is ideal for reversing.
- **In-Place Intent**: You’re working with the input array `s`, showing you understand the in-place requirement.

### What’s Missing

Your code doesn’t actually reverse the string in-place due to these issues:

1. **Creating a New Array (`reversed`)**:

   - You create a new array `reversed` inside the loop and push `s[right]` to it, but this doesn’t modify the input array `s`.
   - The problem requires modifying `s` in-place, meaning you must swap elements within `s` itself, not store them elsewhere.
   - The `reversed` array is also reset each iteration, so it doesn’t accumulate the reversed string.

2. **No Swapping**:

   - To reverse the array, you need to swap `s[left]` and `s[right]` at each step. Your code only pushes `s[right]` to `reversed` without touching `s[left]` or modifying `s`.
   - Without swapping, `s` remains unchanged.

3. **No Effect on `s`**:
   - The problem expects `s` to be reversed (e.g., `["h","e","l","l","o"]` becomes `["o","l","l","e","h"]`), but your code leaves `s` as is because `reversed` is a separate array.

**Example Run with `s = ["h","e","l","l","o"]`**:

- Initialize: `left = 0`, `right = 4`.
- First iteration: `left = 0`, `right = 4`, `s[4] = "o"`, push `"o"` to `reversed = ["o"]`, `left = 1`, `right = 3`.
- Second iteration: `left = 1`, `right = 3`, `s[3] = "l"`, `reversed = ["l"]` (new array), `left = 2`, `right = 2`.
- Loop ends (`left = 2`, `right = 2`).
- Result: `s = ["h","e","l","l","o"]` (unchanged), `reversed` is unused, and no output is returned (but the problem expects `s` to be `["o","l","l","e","h"]`).

**Expected**: `s = ["o","l","l","e","h"]` after in-place reversal.

**What’s Happening**: You’re collecting right-side characters in a new array but not modifying `s`. You need to swap `s[left]` and `s[right]` to reverse the array in-place.

---

## Mental Model

To reverse a character array in-place, we need to swap elements from the start and end until the pointers meet. Here’s my thought process in plain English:

1. **Simplify the Problem**:

   - We need to reverse the array `s` (e.g., `["h","e","l","l","o"]` → `["o","l","l","e","h"]`).
   - Must modify `s` in-place without using extra space (like `.reverse()` or a new array).
   - No return value; the array itself is modified.

2. **Recognize the Pattern**:

   - This is a two-pointer problem, similar to #125 (Valid Palindrome), where we used `left` and `right` pointers to check symmetry.
   - Instead of comparing characters (as in #125), we swap `s[left]` and `s[right]` to reverse the array.
   - Like #26 and #27, we’re manipulating an array in-place, but here we’re flipping positions.

3. **Key Insight**:

   - Use two pointers: `left` starts at index `0`, `right` at `s.length - 1`.
   - While `left < right`, swap `s[left]` and `s[right]`, then move `left` right and `right` left.
   - Stop when `left` meets or crosses `right` (middle of the array).
   - No need to process the entire array, just up to the middle, as swaps cover both ends.

4. **Initial Mistake**:
   - I tried creating a new array (`reversed`) to store characters, but realized this doesn’t modify `s` in-place.
   - Understood that swapping `s[left]` and `s[right]` directly in the array is needed, similar to how #27 placed elements at `write`.

**Connection to Other Problems**:

- **LeetCode #26 (Remove Duplicates from Sorted Array)**: Uses two pointers (`write`, `i`) to place unique elements at the front. #344 uses `left` and `right` to swap elements for reversal.
- **LeetCode #27 (Remove Element)**: Uses two pointers to place non-`val` elements at the front. #344 swaps elements in-place, but both manipulate arrays directly.
- **LeetCode #125 (Valid Palindrome)**: Uses two pointers to compare characters for symmetry. #344 uses pointers to swap characters, achieving the opposite (reversing).

## Optimized Solution

My corrected solution uses a two-pointer approach to reverse the array in-place by swapping elements from both ends.

```javascript
/**
 * @param {character[]} s
 * @return {void} Do not return anything, modify s in-place instead.
 */
var reverseString = function (s) {
  let left = 0;
  let right = s.length - 1;
  while (left < right) {
    // Swap s[left] and s[right]
    let temp = s[left];
    s[left] = s[right];
    s[right] = temp;
    left++;
    right--;
  }
};
```

### Explanation

- **Initialize**: Set `left = 0` (start of array) and `right = s.length - 1` (end).
- **Loop**: While `left < right`:
  - Swap `s[left]` and `s[right]` using a temporary variable.
  - Increment `left` and decrement `right` to move toward the center.
- **No Return**: The array `s` is modified in-place, as required.
- **Why No Extra Array**: Unlike my initial attempt, swapping directly in `s` avoids extra space.

### Line-by-Line Explanation

```javascript
var reverseString = function(s) {
```

- Defines a function taking character array `s`.

```javascript
let left = 0;
let right = s.length - 1;
```

- Initializes two pointers: `left` at the start, `right` at the end.

```javascript
    while (left < right) {
```

- Loops while `left` is less than `right`, ensuring we swap elements until the pointers meet.

```javascript
let temp = s[left];
s[left] = s[right];
s[right] = temp;
```

- Swaps `s[left]` and `s[right]`:
  - Stores `s[left]` in `temp`.
  - Sets `s[left]` to `s[right]`.
  - Sets `s[right]` to `temp`.

```javascript
left++;
right--;
```

- Moves `left` right and `right` left to process the next pair.

```javascript
};
```

- Closes the function. No return, as `s` is modified in-place.

### Time Complexity

- **Time**: `O(n/2) = O(n)`, where `n` is `s.length`. We process half the array (each swap handles two elements).
- **Space**: `O(1)`, only uses `left`, `right`, and a `temp` variable.

### Example

For `s = ["h","e","l","l","o"]`:

- Initialize: `left = 0`, `right = 4`.
- First iteration: `left = 0`, `right = 4`, swap `s[0] = "h"` and `s[4] = "o"`. Array: `["o","e","l","l","h"]`, `left = 1`, `right = 3`.
- Second iteration: `left = 1`, `right = 3`, swap `s[1] = "e"` and `s[3] = "l"`. Array: `["o","l","l","e","h"]`, `left = 2`, `right = 2`.
- Loop ends (`left = 2`, `right = 2`).
- Final: `s = ["o","l","l","e","h"]`.

**Bug I Encountered**:

- Initially created a new array `reversed` to store characters, but this didn’t modify `s` in-place.
- Realized I needed to swap `s[left]` and `s[right]` directly, similar to how #27 used `nums[write] = nums[i]` to place elements.

## Debugging Tips

- **Log Progress**:
  ```javascript
  console.log(
    `Swapping s[${left}]='${s[left]}' with s[${right}]='${s[right]}'`
  );
  let temp = s[left];
  s[left] = s[right];
  s[right] = temp;
  console.log(`Array after swap: ${s}`);
  ```
- **Test Cases**:
  - `["h","e","l","l","o"]` → `["o","l","l","e","h"]`
  - `["H","a","n","n","a","h"]` → `["h","a","n","n","a","H"]`
  - `["a"]` → `["a"]`
  - `[]` → `[]`

## Key Takeaways

- **Two-Pointer Pattern**: Uses `left` and `right` pointers to swap elements from both ends, reversing the array in-place. Unlike #125 (comparing for symmetry), #344 modifies the array by swapping.
- **In-Place Requirement**: Must modify `s` directly, avoiding extra arrays like my initial `reversed`.
- **Personal Insight**: Tried using a new array (`reversed`), but learned in-place swapping is key, similar to #27’s placement of non-`val` elements.
- **Connection to #26/#27/#125**: #26/#27 use pointers to place elements at the front, #125 compares characters, #344 swaps elements. All manipulate data in-place with two pointers.
- **Why Two Pointers Work**: Swapping from both ends ensures the array is reversed efficiently, leveraging symmetry like #125.

## Related Problems

- **LeetCode #26 (Remove Duplicates from Sorted Array)**: Uses two pointers (`write`, `i`) to place unique elements. #344 uses `left` and `right` to swap elements.
- **LeetCode #27 (Remove Element)**: Uses two pointers to place non-`val` elements. #344 swaps elements in-place, both modifying arrays directly.
- **LeetCode #125 (Valid Palindrome)**: Uses two pointers to check symmetry, skipping non-alphanumeric characters. #344 uses pointers to reverse by swapping.

## Notes for Future Review

- **Why Two-Pointer Works**: `left` and `right` pointers swap elements from both ends, reversing the array in-place, leveraging symmetry like #125’s palindrome check.
- **Personal Insight**: Initially created a new array, missing the in-place requirement. Learned to swap `s[left]` and `s[right]`, connecting to #27’s in-place placement.
- **Practice**: Test with odd-length, even-length, single-element, and empty arrays to confirm swapping works.
- **Debugging Note**: Logging swaps helped verify the array was reversing correctly. My initial `reversed` array approach showed I wasn’t modifying `s`.
