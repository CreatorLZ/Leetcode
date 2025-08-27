# LeetCode #3: Longest Substring Without Repeating Characters

## Problem Statement

Given a string `s`, find the length of the **longest substring** without duplicate characters.. A substring is a contiguous sequence of characters in the string. Return the length of the longest substring where all characters are unique.

- **Constraints**:

  - `0 <= s.length <= 5 * 10^4`
  - `s` consists of English letters, digits, symbols, and spaces.

- **Examples**:
  - **Example 1**:
    - Input: `s = "abcabcbb"`
    - Output: `3`
    - Explanation: The longest substring without repeating characters is `"abc"`, with length `3`.
  - **Example 2**:
    - Input: `s = "bbbbb"`
    - Output: `1`
    - Explanation: The longest substring is `"b"`, with length `1`.
  - **Example 3**:
    - Input: `s = "pwwkew"`
    - Output: `3`
    - Explanation: The longest substring is `"wke"`, with length `3`. Note: `"pwke"` is not valid due to repeating `'w'`.

## Mental Model

To find the longest substring without repeating characters, we need to track a substring that expands and contracts as we encounter new or repeated characters. Here’s my thought process in plain English:

1. **Simplify the Problem**:

   - We want the longest substring of `s` where all characters are unique.
   - Example: For `s = "abcabcbb"`, `"abc"` has length `3`, but `"abca"` is invalid due to repeating `'a'`.
   - Checking every substring (brute force) is too slow for large strings.

2. **Recognize the Pattern**:

   - This is a **sliding window** problem, different from my two-pointer problems (#26, #27, #125, #344, #283, #11), where pointers had fixed roles.
   - Use a window defined by `left` and `right` pointers that expands by moving `right` to add characters and contracts by moving `left` when a repeat is found.
   - Track characters in the window with a `Set` to detect repeats quickly.

3. **Key Insight**:

   - Use a `Set` to store characters in the window.
   - Move `right` to add a character. If it’s new, add it to the `Set` and update the max length.
   - If it’s a repeat, move `left` to remove characters until the repeat is gone, updating the `Set`.
   - Compute window length as `right - left + 1` and track the maximum.

4. **Initial Mistakes**:
   - **Tried Brute Force**: Considered checking all substrings (O(n²)), but too slow, like my initial thought for #11 before using two pointers.
   - **Reset Array Each Iteration**: Tried creating a new array each loop (like my first attempt), resetting the window and missing longer substrings.
   - **Used `Math.MAX_VALUE`**: Set `maxLength` to `Infinity`, causing incorrect comparisons (like my #209 mistake with `minSize = 0`).
   - **Checked Index Instead of Character**: Used `seen.has([right])` instead of `seen.has(s[right])`, missing repeats (my bug).
   - **Confused About Window Length**: Wondered why not use `seen.length` for window size. Learned `right - left + 1` counts the window’s characters.
   - **Why Remove `s[left]`?**: Was unsure why we remove characters at `left` on repeats. Realized it removes the earlier duplicate to keep the window valid.
   - **Why `while` Instead of `if`?**: Thought an `if` could handle repeats, but learned multiple characters might need removal (e.g., `"abba"`).
   - **Why `Set`?**: Considered an array for `seen`, but its length doesn’t give window size, and `indexOf` is slower than `Set`’s O(1) lookup.

**Connection to Other Problems**:

- **LeetCode #26 (Remove Duplicates from Sorted Array)**: Moved unique elements with pointers. #3 tracks unique characters in a window.
- **LeetCode #27 (Remove Element)**: Moved non-`val` elements, like #3’s window adjustments.
- **LeetCode #125 (Valid Palindrome)**: Used `left` and `right` for symmetry, like #3’s window boundaries.
- **LeetCode #283 (Move Zeroes)**: Moved non-zero elements, like #3’s character tracking.
- **LeetCode #344 (Reverse String)**: Swapped elements with pointers, unlike #3’s substring tracking.
- **LeetCode #11 (Container With Most Water)**: Optimized area with `right - left`, like #3’s `right - left + 1` for length.

## Optimized Solution

The sliding window approach uses `left` and `right` pointers to maintain a window of unique characters, shrinking when a repeat is found.

```javascript
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function (s) {
  // Initialize a set to store characters in the current window
  const seen = new Set();
  // Initialize left pointer for window start and max length
  let left = 0;
  let maxLength = 0;

  // Iterate with right pointer to expand window
  for (let right = 0; right < s.length; right++) {
    // Shrink window until no repeat by removing characters from left
    while (seen.has(s[right])) {
      seen.delete(s[left]);
      left++;
    }
    // Add current character to set
    seen.add(s[right]);
    // Update max length if current window is larger
    maxLength = Math.max(maxLength, right - left + 1);
  }

  // Return the length of the longest substring
  return maxLength;
};
```

### Explanation

- **Initialize**: Use a `Set` (`seen`) for characters, `left = 0` for window start, `maxLength = 0` for result.
- **Loop**: Move `right` to expand the window:
  - If `s[right]` is in `seen`, shrink by removing `s[left]` and moving `left` until no repeat.
  - Add `s[right]` to `seen`.
  - Update `maxLength` with window length (`right - left + 1`).
- **Return**: `maxLength`.

### Line-by-Line Explanation

```javascript
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function(s) {
    // Initialize a set to store characters in the current window
    const seen = new Set();
    // Initialize left pointer for window start and max length
    let left = 0;
    let maxLength = 0;
```

- Initializes `Set` for characters, `left` for window start, `maxLength` for result.

```javascript
    // Iterate with right pointer to expand window
    for (let right = 0; right < s.length; right++) {
        // Shrink window until no repeat by removing characters from left
        while (seen.has(s[right])) {
            seen.delete(s[left]);
            left++;
        }
```

- Moves `right` to add characters. If `s[right]` is in `seen`, removes `s[left]` and moves `left` until no repeat.

```javascript
// Add current character to set
seen.add(s[right]);
// Update max length if current window is larger
maxLength = Math.max(maxLength, right - left + 1);
```

- Adds `s[right]` to `seen`. Updates `maxLength` with window size.

```javascript
    }
    // Return the length of the longest substring
    return maxLength;
};
```

- Returns `maxLength`.

### Time Complexity

- **Time**: `O(n)`, where `n` is `s.length`. Each character is added (`right` moves `n` times) and removed at most once (`left` moves `n` times).
- **Space**: `O(min(m, n))`, where `m` is the character set size (e.g., 128 for ASCII), for the `Set`.

### Example

For `s = "abcabcbb"`:

- Initialize: `seen = {}`, `left = 0`, `maxLength = 0`.
- `right = 0`: `s[0] = 'a'`, `seen.has('a') = false`, add `'a'`, `seen = {'a'}`, `maxLength = max(0, 0-0+1) = 1`. Window: `"a"`.
- `right = 1`: `s[1] = 'b'`, `seen.has('b') = false`, add `'b'`, `seen = {'a', 'b'}`, `maxLength = max(1, 1-0+1) = 2`. Window: `"ab"`.
- `right = 2`: `s[2] = 'c'`, `seen.has('c') = false`, add `'c'`, `seen = {'a', 'b', 'c'}`, `maxLength = max(2, 2-0+1) = 3`. Window: `"abc"`.
- `right = 3`: `s[3] = 'a'`, `seen.has('a') = true`, remove `s[0] = 'a'`, `left = 1`, `seen = {'b', 'c'}`, add `'a'`, `seen = {'b', 'c', 'a'}`, `maxLength = max(3, 3-1+1) = 3`. Window: `"bca"`.
- `right = 4`: `s[4] = 'b'`, `seen.has('b') = true`, remove `s[1] = 'b'`, `left = 2`, `seen = {'c', 'a'}`, add `'b'`, `seen = {'c', 'a', 'b'}`, `maxLength = max(3, 4-2+1) = 3`. Window: `"cab"`.
- `right = 5`: `s[5] = 'c'`, `seen.has('c') = true`, remove `s[2] = 'c'`, `left = 3`, `seen = {'a', 'b'}`, add `'c'`, `seen = {'a', 'b', 'c'}`, `maxLength = max(3, 5-3+1) = 3`. Window: `"abc"`.
- `right = 6`: `s[6] = 'b'`, `seen.has('b') = true`, remove `s[3] = 'a'`, `left = 4`, `seen = {'b', 'c'}`, remove `s[4] = 'b'`, `left = 5`, `seen = {'c'}`, add `'b'`, `seen = {'c', 'b'}`, `maxLength = max(3, 6-5+1) = 3`. Window: `"cb"`.
- `right = 7`: `s[7] = 'b'`, `seen.has('b') = true`, remove `s[5] = 'c'`, `left = 6`, `seen = {'b'}`, remove `s[6] = 'b'`, `left = 7`, `seen = {}`, add `'b'`, `seen = {'b'}`, `maxLength = max(3, 7-7+1) = 3`. Window: `"b"`.
- Return: `3` (for `"abc"`, `"bca"`, etc.).

## Debugging Tips

- **Log Progress**:
  ```javascript
  console.log(
    `right=${right}, s[right]=${s[right]}, left=${left}, window=${s.slice(
      left,
      right + 1
    )}, seen=${[...seen]}, maxLength=${maxLength}`
  );
  ```
- **Test Cases**:
  - `"abcabcbb"` → `3` (e.g., `"abc"`)
  - `"bbbbb"` → `1` (e.g., `"b"`)
  - `"pwwkew"` → `3` (e.g., `"wke"`)
  - `""` → `0`
  - `"dvdf"` → `3` (e.g., `"vdf"`)
  - `"abba"` → `2` (e.g., `"ab"` or `"ba"`)

## Bugs I Encountered

- **Reset Array Each Iteration**: Created a new array each loop, resetting the window (my first attempt).
- **Used `Math.MAX_VALUE`**: Set `maxLength` to `Infinity`, causing incorrect comparisons.
- **Checked Index**: Used `seen.has([right])` instead of `seen.has(s[right])`, missing repeats.
- **Tried `if` for Repeats**: Used `if` instead of `while`, leaving duplicates in cases like `"abba"`.
- **Array for `seen`**: Considered an array, but `indexOf` is slow, and its length doesn’t give window size.
- **Brute Force**: Thought about checking all substrings, but O(n²) was too slow.

## Key Takeaways

- **Sliding Window Pattern**: Uses `left` and `right` to maintain a window of unique characters, expanding with `right` and contracting with `left` on repeats, unlike #11’s fixed pointer roles.
- **Why Remove `s[left]`**: Removes the earlier occurrence of a repeated character to keep the window valid (no duplicates).
- **Why `Set`**: Fast O(1) lookup (`has`, `add`, `delete`) vs. array’s O(n) `indexOf`. `Set` ensures uniqueness, but its size isn’t the window length.
- **Why `right - left + 1`**: Counts characters in the window (indices `left` to `right` inclusive), unlike `seen.length`, which counts unique characters.
- **Why `while`**: Removes multiple characters if needed (e.g., `"abba"`) to clear repeats, unlike `if`, which might leave duplicates.
- **Why Not `seen.has([right])`**: `seen` stores characters, not indices. Checking `s[right]` detects repeats correctly.
- **Why Not Brute Force**: Brute force (O(n²)) checks all substrings, while sliding window is O(n), processing each character once or twice.
- **Connection to #26/#27/#125/#283/#344/#11**: #26/#27/#283 move elements, #125 compares, #344 swaps, #11 optimizes area, #3 maintains a dynamic window.
- **Personal Insight**: Was confused by repeats, window length, and `Set`. Visualized the window as a sliding frame and used logs to confirm `while` loop and `right - left + 1`.

## Related Problems

- **LeetCode #26 (Remove Duplicates from Sorted Array)**: Moves unique elements. #3 tracks unique characters in a window.
- **LeetCode #27 (Remove Element)**: Moves non-`val` elements, like #3’s window adjustments.
- **LeetCode #125 (Valid Palindrome)**: Uses pointers for symmetry, like #3’s window.
- **LeetCode #283 (Move Zeroes)**: Moves non-zero elements, like #3’s character tracking.
- **LeetCode #344 (Reverse String)**: Swaps elements, unlike #3’s substring tracking.
- **LeetCode #11 (Container With Most Water)**: Optimizes area with `right - left`, like #3’s `right - left + 1`.

## Notes for Future Review

- **Why Sliding Window Works**: `left` and `right` define a dynamic window, contracting on repeats, unlike #11’s fixed roles.
- **Personal Insight**: Clarified repeats (`while`), `Set`, and `right - left + 1` through examples like `"abba"`. Built on #125/#11’s pointer logic.
- **Practice**: Test with repeats (`"abba"`), empty strings, or single characters to master window contraction.
- **Debugging Note**: Logging `seen` and window size clarified repeat handling and length updates.

---
