# Pseudocode for HW1.py

```pseudocode
CLASS Solution:
  METHOD flip(self, A_binary_string):
    n = length of A_binary_string
    IF n == 0 THEN
      RETURN empty_list // No operation possible on an empty string.
    ENDIF

    // Step 1: Convert the binary string `A_binary_string` into a "gain" array `arr_gain`.
    // If `A_binary_string[i]` is '0', flipping it to '1' increases the count of 1s by 1 (gain = +1).
    // If `A_binary_string[i]` is '1', flipping it to '0' decreases the count of 1s by 1 (gain = -1).
    // The problem then becomes finding a subarray in `arr_gain` with the maximum possible sum.
    // This sum represents the maximum net change in the number of 1s.
    arr_gain = array of size n
    FOR i FROM 0 TO n - 1:
      IF A_binary_string[i] == '1' THEN
        arr_gain[i] = -1
      ELSE // A_binary_string[i] == '0'
        arr_gain[i] = 1
      ENDIF
    ENDFOR

    // Step 2: Apply Kadane's algorithm to `arr_gain` to find the subarray with the maximum sum.
    max_overall_gain_found = negative_infinity // Stores the maximum sum (gain) found. Python uses `float('-inf')`.
    current_gain_ending_here = 0           // Sum of the current subarray (gain segment) ending at the current position.

    // `best_flip_start_idx_0based` and `best_flip_end_idx_0based` store the 0-based indices
    // of the start and end of the subarray in `arr_gain` that yields `max_overall_gain_found`.
    // Python initializes these as `global_start, global_end = 0, 0`.
    best_flip_start_idx_0based = 0
    best_flip_end_idx_0based = 0

    // `current_segment_start_idx_0based` tracks the 0-based starting index of the current segment being considered.
    // Python initializes this as `start = 0`.
    current_segment_start_idx_0based = 0

    FOR current_idx_0based FROM 0 TO n - 1:
      current_gain_ending_here = current_gain_ending_here + arr_gain[current_idx_0based]

      IF current_gain_ending_here > max_overall_gain_found THEN
        max_overall_gain_found = current_gain_ending_here
        // This segment provides a better gain. Record its start and end indices.
        best_flip_start_idx_0based = current_segment_start_idx_0based
        best_flip_end_idx_0based = current_idx_0based // Python uses an intermediate `end = i` variable.
      ENDIF

      // If `current_gain_ending_here` becomes negative, it means this segment, if extended,
      // will only decrease the sum. So, we reset `current_gain_ending_here` to 0 and
      // consider a new segment starting from the next position (`current_idx_0based + 1`).
      IF current_gain_ending_here < 0 THEN
        current_segment_start_idx_0based = current_idx_0based + 1
        current_gain_ending_here = 0
      ENDIF
    ENDFOR

    // Step 3: Determine the return value based on Kadane's algorithm results.
    // "If you don't want to perform the operation, return an empty array."
    // This implies returning an empty array if the best flip doesn't improve the count of 1s,
    // or if the "best" flip is actually detrimental (e.g., max_overall_gain_found <= 0).
    // The Python code has a specific condition: `if global_start == global_end and arr[global_start] == -1:`
    // This condition checks if the segment found by Kadane's corresponds to flipping a single '1'
    // (which means `arr_gain[best_flip_start_idx_0based]` was -1, and `max_overall_gain_found` would be -1).
    // This is typically the case for strings like "1" or "111" (all '1's).

    IF max_overall_gain_found == negative_infinity THEN
        // This implies the input string was empty (n=0, handled) or some other edge case
        // where Kadane's didn't update `max_overall_gain_found`.
        RETURN empty_list
    ENDIF

    // Apply the specific condition from the Python solution for returning an empty list:
    IF best_flip_start_idx_0based == best_flip_end_idx_0based AND arr_gain[best_flip_start_idx_0based] == -1 THEN
      // This means the "best" operation found was to flip a single '1' to '0', resulting in a loss.
      // In this case, no operation is performed.
      RETURN empty_list
    ELSE
      // Otherwise, a flip operation is chosen (either it's beneficial, or it's the lexicographically
      // smallest among those with max gain, even if gain is 0 or negative but not the single '1' case).
      // Convert 0-based indices to 1-based for the output.
      RETURN [best_flip_start_idx_0based + 1, best_flip_end_idx_0based + 1]
    ENDIF
  ENDMETHOD
ENDCLASS
```
