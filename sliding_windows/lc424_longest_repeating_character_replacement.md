# LeetCode #424: Longest Repeating Character Replacement

## Problem Statement

Given a string `s` of uppercase English letters and an integer `k`, you can change any character to another uppercase letter up to `k` times. Return the length of the **longest substring** where, after at most `k` replacements, all characters are the same.

- **Constraints**:

  - `1 <= s.length <= 10^5`
  - `s` consists of only uppercase English letters (A-Z).
  - `0 <= k <= s.length`

- **Examples**:
  - **Example 1**:
    - Input: `s = "ABAB", k = 2`
    - Output: `4`
    - Explanation: Replace two `'A'`s with `'B'`s to get `"BBBB"`, length `4`.
  - **Example 2**:
    - Input: `s = "AABABBA", k = 1`
    - Output: `4`
    - Explanation: Replace one `'A'` in `"ABBA"` with `'B'` to get `"BBBB"`, length `4`.

## Mental Model

To find the longest substring where we can make all letters the same with at most `k` changes, we need a dynamic window that tracks letter counts and adjusts when we need too many changes. Here’s my thought process in plain English, like solving a puzzle for the first time:

1. **Simplify the Problem**:

   - We want the longest chunk of the string where, by changing up to `k` letters, all letters become the same.
   - Example: For `s = "AABABBA", k = 1`, in `"ABBA"`, change one `'A'` to `'B'` to get `"BBBB"`, length `4`.
   - Checking every possible chunk (brute force) is too slow for a string with up to 100,000 letters.

2. **Recognize the Pattern**:

   - This is a **sliding window** problem, like **#3** (unique letters) and **#209** (sum >= target).
   - Use `left` and `right` pointers to form a window (like a movable frame over the string).
   - The window is “good” if we can make all letters the same by changing `k` or fewer letters.
   - Keep track of the longest good window.

3. **Key Insight**:

   - In a window, count how many times each letter appears (e.g., in `"AAB"`, `'A': 2, 'B': 1`).
   - To make all letters the same, keep the most common letter and change the others.
   - Number of changes needed = window length (total letters) - count of most common letter.
   - Example: In `"AAB"`, length `3`, `'A'` appears 2 times, changes to make all `'A'`s: `3 - 2 = 1`.
   - If changes needed `<= k`, the window is good. If > `k`, shrink the window by moving `left`.

4. **Initial Mistakes**:
   - **Brute Force**: Thought about checking all substrings, but it’s too slow (O(n²)), like my initial idea for **#11**.
   - **Used `Set`**: Considered a `Set` (like #3), but we need counts, not just presence.
   - **Misunderstood Hash Map**: Was confused by `freq[s[right]] = (freq[s[right]] || 0) + 1`. Learned it’s like a notebook tallying letter counts.
   - **While vs. If**: Wondered if `while` could replace `if` for shrinking . Both work, but `if` is simpler since one shrink per iteration is enough.
   - **Recalculating Length**: Questioned why not use `windowLength` for `maxLength` . Learned it’s outdated if `left` moves, so `right - left + 1` is safer.

**Connection to Other Problems**:

- **#3**: Tracked unique letters with a `Set`, shrinking on repeats. #424 tracks letter counts with a hash map, shrinking when changes > `k`.
- **#209**: Tracked sum of numbers, shrinking when sum >= target. #424 tracks letter counts, shrinking when changes > `k`.
- **#11**: Used `right - left` for width. #424 uses `right - left + 1` for length.
- **#125/#26/#27/#283**: Adjusted pointers for valid states, like #424’s window adjustments.
- **#344**: Swapped letters, unlike #424’s counting.

## Optimized Solution

The sliding window approach uses `left` and `right` pointers to maintain a window where the number of changes needed (`window length - max frequency`) is `<= k`, maximizing the length.

```javascript
/**
 * @param {string} s - String of uppercase letters
 * @param {number} k - Max number of letters we can change
 * @return {number} - Length of longest substring with same letters after k changes
 */
var characterReplacement = function (s, k) {
  // Create a notebook (hash map) to count letters in the window
  const freq = {};
  // Start the window at position 0
  let left = 0;
  // Track the longest good window
  let maxLength = 0;
  // Track the count of the most common letter
  let maxFreq = 0;

  // Move right pointer to add letters to the window
  for (let right = 0; right < s.length; right++) {
    // Add current letter to notebook (add 1 to its count)
    freq[s[right]] = (freq[s[right]] || 0) + 1;
    // Update count of most common letter
    maxFreq = Math.max(maxFreq, freq[s[right]]);

    // If we need too many changes, shrink the window
    if (right - left + 1 - maxFreq > k) {
      // Remove leftmost letter from notebook
      freq[s[left]]--;
      // Move left side of window forward
      left++;
    }

    // Update longest window seen
    maxLength = Math.max(maxLength, right - left + 1);
  }

  // Return length of longest good window
  return maxLength;
};
```

### Explanation

- **Initialize**: Use a hash map (`freq`) to count letters, `left = 0` for window start, `maxLength = 0` for longest window, `maxFreq = 0` for most common letter’s count.
- **Loop**: Move `right` to add letters:
  - Add `s[right]` to `freq` and update `maxFreq`.
  - Check if changes needed (`right - left + 1 - maxFreq`) > `k`. If so, shrink by removing `s[left]` and moving `left`.
  - Update `maxLength` with current window length.
- **Return**: `maxLength`.

### Line-by-Line Explanation

```javascript
/**
 * @param {string} s - String of uppercase letters
 * @param {number} k - Max number of letters we can change
 * @return {number} - Length of longest substring with same letters after k changes
 */
var characterReplacement = function(s, k) {
    // Create a notebook (hash map) to count letters in the window
    const freq = {};
    // Start the window at position 0
    let left = 0;
    // Track the longest good window
    let maxLength = 0;
    // Track the count of the most common letter
    let maxFreq = 0;
```

- Initializes `freq` as a notebook for letter counts, `left` for window start, `maxLength` for result, `maxFreq` for most common letter’s count.

```javascript
    // Move right pointer to add letters to the window
    for (let right = 0; right < s.length; right++) {
        // Add current letter to notebook (add 1 to its count)
        freq[s[right]] = (freq[s[right]] || 0) + 1;
        // Update count of most common letter
        maxFreq = Math.max(maxFreq, freq[s[right]]);
```

- Moves `right` to add `s[right]` to `freq`. If `s[right]` isn’t in `freq`, start at `0` and add `1`. Updates `maxFreq` if the current letter’s count is higher.

```javascript
// If we need too many changes, shrink the window
if (right - left + 1 - maxFreq > k) {
  // Remove leftmost letter from notebook
  freq[s[left]]--;
  // Move left side of window forward
  left++;
}
```

- Checks if changes needed (`window length - maxFreq`) > `k`. If true, subtracts `s[left]`’s count and moves `left`.

```javascript
// Update longest window seen
maxLength = Math.max(maxLength, right - left + 1);
```

- Updates `maxLength` with current window length (`right - left + 1`), ensuring we use the latest `left` value.

```javascript
    }
    // Return length of longest good window
    return maxLength;
};
```

- Returns `maxLength`.

### Time Complexity

- **Time**: `O(n)`, where `n` is `s.length`. Each letter is added (`right` moves `n` times) and removed at most once (`left` moves `n` times).
- **Space**: `O(1)`, as `freq` stores at most 26 uppercase letters (constant size).

### Example

For `s = "AABABBA", k = 1`:

- Initialize: `freq = {}`, `left = 0`, `maxLength = 0`, `maxFreq = 0`.
- `right = 0`: `s[0] = 'A'`, `freq = {A:1}`, `maxFreq = 1`, `1-0+1-1 = 1 <= 1`, `maxLength = 1`. Window: `"A"`.
- `right = 1`: `s[1] = 'A'`, `freq = {A:2}`, `maxFreq = 2`, `2-0+1-2 = 1 <= 1`, `maxLength = 2`. Window: `"AA"`.
- `right = 2`: `s[2] = 'B'`, `freq = {A:2, B:1}`, `maxFreq = 2`, `3-0+1-2 = 2 > 1`, shrink: `freq[A]=1`, `left = 1`, `3-1+1-2 = 1 <= 1`, `maxLength = 3`. Window: `"AB"`.
- `right = 3`: `s[3] = 'A'`, `freq = {A:2, B:1}`, `maxFreq = 2`, `4-1+1-2 = 2 > 1`, shrink: `freq[A]=1`, `left = 2`, `4-2+1-2 = 1 <= 1`, `maxLength = 3`. Window: `"BA"`.
- `right = 4`: `s[4] = 'B'`, `freq = {A:1, B:2}`, `maxFreq = 2`, `5-2+1-2 = 2 > 1`, shrink: `freq[B]=1`, `left = 3`, `5-3+1-2 = 1 <= 1`, `maxLength = 3`. Window: `"AB"`.
- `right = 5`: `s[5] = 'B'`, `freq = {A:1, B:2}`, `maxFreq = 2`, `6-3+1-2 = 2 > 1`, shrink: `freq[A]=0`, `left = 4`, `6-4+1-2 = 1 <= 1`, `maxLength = 3`. Window: `"BB"`.
- `right = 6`: `s[6] = 'A'`, `freq = {A:1, B:2}`, `maxFreq = 2`, `7-4+1-2 = 2 > 1`, shrink: `freq[B]=1`, `left = 5`, `7-5+1-2 = 1 <= 1`, `maxLength = 4`. Window: `"BBA"`.
- Return: `4` (for `"ABBA"`, replace one `'A'` to get `"BBBB"`).

## Debugging Tips

- **Log Progress**:
  ```javascript
  console.log(
    `right=${right}, s[right]=${s[right]}, left=${left}, window=${s.slice(
      left,
      right + 1
    )}, freq=${JSON.stringify(freq)}, maxFreq=${maxFreq}, changes=${
      right - left + 1 - maxFreq
    }, maxLength=${maxLength}`
  );
  ```
- **Test Cases**:
  - `"ABAB", k=2` → `4` (e.g., `"BBBB"`)
  - `"AABABBA", k=1` → `4` (e.g., `"BBBB"`)
  - `"AAAA", k=0` → `4` (no changes needed)
  - `"AB", k=0` → `1`
  - `"AABBA", k=1` → `4` (e.g., `"AABB"`, replace one `'B'`)

## Bugs I Encountered

- **Used `Set`**: Tried a `Set` (like #3), but needed counts, so used a hash map (your hash map question).
- **Misunderstood Hash Map**: Confused by `freq[s[right]] = (freq[s[right]] || 0) + 1`. It’s like tallying letters in a notebook.
- **While vs. If**: Thought `while` was needed (like #3’s repeats or #209’s sum). Learned `if` is simpler since one shrink per iteration is enough (your `while` vs. `if` question).
- **Outdated `windowLength`**: Wondered why not use `windowLength` for `maxLength`. Realized it’s outdated if `left` moves, so `right - left + 1` is safer (your latest question).
- **Typo in Example**: Incorrectly said `'B'` appeared 3 times in `"AABBA"`. Corrected to 2 times, needing `5 - 2 = 3` changes.
- **Brute Force**: Considered checking all substrings, but O(n²) was too slow.

## Key Takeaways

- **Sliding Window Pattern**: Uses `left` and `right` to maintain a window where `right - left + 1 - maxFreq <= k`, maximizing length, like #3’s unique letters and #209’s sum >= target.
- **Hash Map**: Tracks letter counts (e.g., `{A:2, B:1}`), unlike #3’s `Set` (unique letters) or #209’s `sum` (total). `freq[s[right]] = (freq[s[right]] || 0) + 1` adds a tally (your question).
- **Why `if` vs. `While`**: `if` shrinks once per iteration, sufficient since `for` loop rechecks. `while` works but is unnecessary (your question).
- **Why Recalculate `right - left + 1`**: Ensures current window length after `left` changes, avoiding outdated `windowLength` (your question).
- **Connection to #3**: #3 used `Set` for uniqueness, #424 uses hash map for counts, both use `right - left + 1`.
- **Connection to #209**: #209 minimized length with `sum >= target`, #424 maximizes with changes `<= k`.
- **Connection to #11/#125/#283**: #11 used `right - left`, #125 adjusted pointers, #283 moved elements, #424 adjusts window for changes.
- **Personal Insight**: Was confused by hash map, `while` vs. `if`, and `windowLength`. Visualized window as a sliding frame, with `freq` as a notebook and `right - left + 1` as a ruler.

## Related Problems

- **#3**: Tracked unique letters, shrinking on repeats. #424 tracks counts, shrinking when changes > `k`.
- **#209**: Minimized length with sum >= target. #424 maximizes with changes <= `k`.
- **#11**: Optimized area with `right - left`. #424 uses `right - left + 1` for length.
- **#125/#26/#27/#283**: Adjusted pointers for valid states, like #424’s window.
- **#344**: Swapped letters, unlike #424’s counting.

## Notes for Future Review

- **Why Sliding Window Works**: Expands with `right`, shrinks with `left` when changes > `k`, like #3’s repeat-based shrinking.
- **Personal Insight**: Clarified hash map, `if` vs. `while`, and `right - left + 1` through examples like `"AABABBA"`. Built on #3’s uniqueness and #209’s sum tracking.
- **Practice**: Test with `k=0`, single letters, or large `k` to master window validity.
- **Debugging Note**: Logging `freq`, `maxFreq`, and changes helped verify shrinking and length updates.

---
