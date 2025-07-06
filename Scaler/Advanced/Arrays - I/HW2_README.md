# Pseudocode for HW2.py

```pseudocode
CLASS Solution:
  METHOD maxset(self, A_list_integers):
    n = length of A_list_integers
    current_idx = 0

    best_subarray_overall = empty list      // Stores the best subarray found according to problem rules.
    current_segment_being_built = empty list // Stores elements of the current contiguous non-negative segment.

    current_segment_sum = 0
    max_sum_found_so_far = 0 // Stores the sum of `best_subarray_overall`. Python's `prev_sum`.

    WHILE current_idx < n:
      current_element = A_list_integers[current_idx]

      IF current_element >= 0 THEN
        // Current element is non-negative, so extend the current segment.
        current_segment_sum = current_segment_sum + current_element
        APPEND current_element TO current_segment_being_built
      ENDIF

      // --- Evaluation Condition (reflecting Python's logic) ---
      // The current segment (`current_segment_being_built`) is evaluated if:
      // 1. `current_element < 0`: The non-negative sequence is broken.
      // 2. OR `(current_idx == n-1 AND current_element > 0)`: It's the last element, AND it's positive.
      //    This specific condition in Python means if the array ends with '0', an accumulated segment
      //    might not be evaluated within this `IF` block.
      //    Example: A=[1,0]. Loop finishes, `ans` is [], which is incorrect. `current_segment_being_built` would be [1,0].
      //    The pseudocode below first reflects the Python code's behavior, then suggests a correction.

      should_evaluate_segment_now = false
      IF current_element < 0 THEN
        should_evaluate_segment_now = true
      // Python's original condition for end of list:
      ELSE IF (current_idx == n - 1 AND current_element > 0 AND length(current_segment_being_built) > 0) THEN
        // Note: `length(current_segment_being_built) > 0` added to ensure there's something to evaluate.
        // If A=[0], current_element=0, this part `A[i]>0` is false.
        // If A=[5] (last element), current_element=5, `A[i]>0` is true. Segment [5] is evaluated.
        should_evaluate_segment_now = true
      ENDIF
      // For a more robust solution, evaluation should happen if `current_element < 0` OR `current_idx == n-1`,
      // provided `current_segment_being_built` is not empty after processing `current_element`.

      IF should_evaluate_segment_now THEN
        // Compare `current_segment_sum` with `max_sum_found_so_far`.
        IF current_segment_sum > max_sum_found_so_far THEN
          best_subarray_overall = copy of current_segment_being_built
          max_sum_found_so_far = current_segment_sum
        ELSE IF current_segment_sum == max_sum_found_so_far THEN
          // Tie in sum: compare lengths.
          IF length of current_segment_being_built > length of best_subarray_overall THEN
            best_subarray_overall = copy of current_segment_being_built
          ENDIF
          // If lengths are also tied, the problem implies keeping the one with the minimum starting index.
          // This is naturally handled by not updating if a new segment has same sum and same length
          // as an earlier one.
        ENDIF

        // Reset for the next segment.
        current_segment_being_built = empty list
        current_segment_sum = 0
      ENDIF

      current_idx = current_idx + 1
    ENDWHILE

    // --- Post-Loop Check (to address the potential issue with Python's end condition) ---
    // If the loop finished and `current_segment_being_built` is not empty,
    // it means the last segment was non-negative but might not have been evaluated
    // by the specific `(i == n-1 and A[i] > 0)` condition (e.g., if it ended with a 0).
    // This check ensures such a segment is considered.
    // To strictly follow Python code, this block would be omitted. For problem correctness, it's needed.
    IF length(current_segment_being_built) > 0 THEN
        IF current_segment_sum > max_sum_found_so_far THEN
          best_subarray_overall = copy of current_segment_being_built
          // max_sum_found_so_far = current_segment_sum // Not strictly needed if only returning array
        ELSE IF current_segment_sum == max_sum_found_so_far THEN
          IF length of current_segment_being_built > length of best_subarray_overall THEN
            best_subarray_overall = copy of current_segment_being_built
          ENDIF
        ENDIF
    ENDIF

    RETURN best_subarray_overall
  ENDMETHOD
ENDCLASS

// Example Usage:
IF SCRIPT_IS_RUN_DIRECTLY THEN
  solution_object = new Solution()
  // PRINT s.maxset(A=[1, 2, 5, -7, 2, 3]) // Expected: [1, 2, 5]
  // PRINT s.maxset(A=[0, 0, -1, 0]) // Python: [0,0]. Corrected: [0,0]
  // PRINT s.maxset(A=[1,0]) // Python: []. Corrected: [1,0]
ENDIF
```
