# LeetCode #125: Valid Palindrome

## Problem Statement

Given a string `s`, return `true` if it is a palindrome, or `false` otherwise. A palindrome is a string that reads the same forward and backward, considering only **alphanumeric characters** (letters and digits) and ignoring case (e.g., "A" and "a" are the same). Non-alphanumeric characters (like spaces, punctuation) are ignored.

- **Constraints**:

  - `1 <= s.length <= 2 * 10^5`
  - `s` consists of printable ASCII characters.

- **Examples**:
  - **Example 1**:
    - Input: `s = "A man, a plan, a canal: Panama"`
    - Output: `true`
    - Explanation: Ignoring non-alphanumeric characters and case, the string is `"amanaplanacanalpanama"`, which reads the same forward and backward.
  - **Example 2**:
    - Input: `s = "race a car"`
    - Output: `false`
    - Explanation: The string becomes `"raceacar"`, which is not a palindrome.
  - **Example 3**:
    - Input: `s = " "`
    - Output: `true`
    - Explanation: After ignoring non-alphanumeric characters, the string is empty, which is a palindrome.

## Mental Model

To determine if a string is a palindrome, we need to check if it reads the same forward and backward, considering only alphanumeric characters and ignoring case. Here’s my thought process in plain English:

1. **Simplify the Problem**:

   - We only care about letters (a-z, A-Z) and digits (0-9), ignoring spaces, punctuation, and case.
   - A palindrome is symmetric: characters at the start and end must match when moving inward.
   - We need to compare characters from both ends, skipping non-alphanumeric ones.

2. **Recognize the Pattern**:

   - This feels like a two-pointer problem, similar to LeetCode #26 and #27, where pointers manipulated arrays.
   - Use two pointers: `left` starting at the beginning, `right` at the end.
   - Move pointers toward each other, comparing alphanumeric characters and skipping others.

3. **Key Insight**:

   - Check if characters are alphanumeric using a regex (`/[a-zA-Z0-9]/`).
   - Compare `s[left]` and `s[right]` in lowercase. If they match, move inward; if not, it’s not a palindrome.
   - Skip non-alphanumeric characters by incrementing `left` or decrementing `right`.
   - An empty or single-character string (after ignoring non-alphanumeric) is a palindrome.

4. **Why Two Pointers?**:
   - Palindromes are about symmetry, so checking from both ends with `left` and `right` pointers is intuitive.
   - Like #26 (keeping unique elements) and #27 (keeping non-`val` elements), pointers here filter and compare in-place.
   - Avoids creating a new string, keeping space at O(1).

**Connection to Other Problems**:

- **LeetCode #26 (Remove Duplicates from Sorted Array)**: Uses two pointers (`write`, `i`) to place unique elements at the front of a sorted array. #125 uses `left` and `right` to check string symmetry, skipping non-alphanumeric characters.
- **LeetCode #27 (Remove Element)**: Uses two pointers to place non-`val` elements at the front. #125 similarly skips irrelevant characters (non-alphanumeric) while comparing.

## Optimized Solution

My solution uses a two-pointer approach to check if the string is a palindrome in-place, efficiently handling case and non-alphanumeric characters.

```javascript
/**
 * @param {string} s
 * @return {boolean}
 */
var isPalindrome = function (s) {
  let left = 0;
  let right = s.length - 1;
  isAlphaNumeric = function (character) {
    return /[a-zA-Z0-9]/.test(character);
  };
  while (left < right) {
    while (left < right && !isAlphaNumeric(s[left])) {
      left++;
    }
    while (left < right && !isAlphaNumeric(s[right])) {
      right--;
    }
    if (s[left].toLowerCase() !== s[right].toLowerCase()) {
      return false;
    }
    left++;
    right--;
  }
  return true;
};
```

### Explanation

- **Initialize**: Set `left = 0` (start of string) and `right = s.length - 1` (end).
- **Helper Function**: Define `isAlphaNumeric` to check if a character is a letter or digit using regex.
- **Loop**: While `left < right`:
  - Skip non-alphanumeric characters at `left` by incrementing until an alphanumeric character is found.
  - Skip non-alphanumeric characters at `right` by decrementing.
  - Compare `s[left]` and `s[right]` in lowercase. If they differ, return `false`.
  - Move `left` right and `right` left to check the next pair.
- **Return**: If all comparisons pass, return `true` (it’s a palindrome).

### Line-by-Line Explanation

```javascript
var isPalindrome = function(s) {
```

- Defines a function taking string `s`.

```javascript
let left = 0;
let right = s.length - 1;
```

- Initializes two pointers: `left` at the start, `right` at the end of the string.

```javascript
isAlphaNumeric = function (character) {
  return /[a-zA-Z0-9]/.test(character);
};
```

- Defines a helper function to check if a character is alphanumeric (letter a-z, A-Z, or digit 0-9) using regex.

```javascript
    while (left < right) {
```

- Loops while `left` is less than `right`, comparing characters from both ends.

```javascript
while (left < right && !isAlphaNumeric(s[left])) {
  left++;
}
```

- Skips non-alphanumeric characters at `left` by incrementing until an alphanumeric character is found or `left >= right`.

```javascript
while (left < right && !isAlphaNumeric(s[right])) {
  right--;
}
```

- Skips non-alphanumeric characters at `right` by decrementing until an alphanumeric character is found or `left >= right`.

```javascript
if (s[left].toLowerCase() !== s[right].toLowerCase()) {
  return false;
}
```

- Compares the lowercase characters at `left` and `right`. If they differ, it’s not a palindrome, return `false`.

```javascript
left++;
right--;
```

- Moves `left` right and `right` left to check the next pair of characters.

```javascript
    }
```

- Closes the loop.

```javascript
return true;
```

- If all comparisons pass, the string is a palindrome, return `true`.

### Time Complexity

- **Time**: `O(n)`, where `n` is `s.length`. Each character is visited at most twice (once from `left`, once from `right`) while skipping or comparing.
- **Space**: `O(1)`, only uses two pointers and constant space (excluding input).

### Example

For `s = "A man, a plan, a canal: Panama"`:

- Initialize: `left = 0`, `right = 30` (length - 1).
- `left = 0`: `s[0] = 'A'`, alphanumeric.
- `right = 30`: `s[30] = 'a'`, alphanumeric. Compare `'A'.toLowerCase() = 'a'` vs. `'a'`, match. `left = 1`, `right = 29`.
- `left = 1`: `s[1] = ' '`, skip. `left = 2`: `s[2] = 'm'`, alphanumeric.
- `right = 29`: `s[29] = 'm'`, alphanumeric. Compare `'m'` vs. `'m'`, match. `left = 3`, `right = 28`.
- `left = 3`: `s[3] = 'a'`, alphanumeric.
- `right = 28`: `s[28] = 'a'`, alphanumeric. Compare `'a'` vs. `'a'`, match. `left = 4`, `right = 27`.
- Continue skipping non-alphanumeric (`,`, `:`, spaces). Compare `n` vs. `n`, `a` vs. `a`, `p` vs. `p`, etc.
- All pairs match, `left` meets `right`, return `true`.

**Bug I Encountered**:

- Initially considered preprocessing the string to remove non-alphanumeric characters and convert to lowercase, but that uses O(n) space.
- Realized the two-pointer approach skips characters in-place, saving space. My solution already handles this efficiently.

## Debugging Tips

- **Log Progress**:
  ```javascript
  if (left < right) {
    console.log(
      `Comparing s[${left}]='${s[left]}' vs. s[${right}]='${s[right]}'`
    );
    if (s[left].toLowerCase() !== s[right].toLowerCase()) {
      console.log(`Mismatch at left=${left}, right=${right}`);
      return false;
    }
  }
  ```
- **Test Cases**:
  - `"A man, a plan, a canal: Panama"` → `true`
  - `"race a car"` → `false`
  - `" "` → `true`
  - `"0P"` → `false`
  - `"a"` → `true`
  - `".,"` → `true` (all non-alphanumeric, empty palindrome)

## Key Takeaways

- **Two-Pointer Pattern**: Uses `left` and `right` pointers to check palindrome symmetry by comparing characters from both ends, skipping non-alphanumeric ones. Unlike #26/#27 (array rearrangement), #125 checks string symmetry.
- **In-Place Check**: Avoids creating a new string, keeping space at O(1).
- **Personal Insight**: Recognized the palindrome’s symmetry as a two-pointer problem after solving #26 and #27. The key was skipping non-alphanumeric characters and handling case with `toLowerCase()`.
- **Connection to #26/#27**: #26 uses pointers to keep unique elements, #27 keeps non-`val` elements, #125 checks symmetry by comparing characters, all in-place.
- **Why Two Pointers Work**: Comparing from both ends leverages the palindrome’s symmetry, efficiently skipping irrelevant characters.

## Related Problems

- **LeetCode #26 (Remove Duplicates from Sorted Array)**: Uses two pointers (`write`, `i`) to place unique elements in a sorted array, maintaining order. #125 uses `left` and `right` to compare string characters, ignoring non-alphanumeric ones.
- **LeetCode #27 (Remove Element)**: Uses two pointers to place non-`val` elements at the front, similar to #125’s skipping of non-alphanumeric characters.
- **LeetCode #283 (Move Zeroes)**: Uses two pointers to move non-zero elements to the front, like filtering in #27 and #125.

## Notes for Future Review

- **Why Two-Pointer Works**: `left` and `right` pointers check palindrome symmetry by moving inward, skipping non-alphanumeric characters, similar to filtering in #27.
- **Personal Insight**: Connected this to #26/#27’s two-pointer pattern. Initially thought preprocessing the string was necessary, but learned in-place checking with pointers is more efficient.
- **Practice**: Test edge cases like empty strings, single characters, or only non-alphanumeric characters to confirm robustness.
- **Debugging Note**: Added `console.log` during development to track `right` pointer, which helped verify skipping and comparison logic. Removed for submission.

---
