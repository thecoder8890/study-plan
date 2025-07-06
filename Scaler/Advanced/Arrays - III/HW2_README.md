# Pseudocode for HW2.py

```pseudocode
CLASS Solution:
  METHOD solve(self, A_integer_list, B_threshold):
    // A_integer_list: The list of integers.
    // B_threshold: The value to compare elements against.
    // Goal: Find the minimum number of swaps to bring all elements in A_integer_list
    //       that are less than or equal to B_threshold together.

    n = length of A_integer_list

    // Step 1: Count the total number of "target elements" (elements <= B_threshold).
    // This count, `num_target_elements`, also determines the size of the final
    // contiguous block of target elements we are aiming for.
    num_target_elements = 0
    FOR EACH element IN A_integer_list:
      IF element <= B_threshold THEN
        num_target_elements = num_target_elements + 1
      ENDIF
    ENDFOR

    // If there are no target elements, or if all elements are target elements,
    // no swaps are needed.
    IF num_target_elements == 0 OR num_target_elements == n THEN
      RETURN 0
    ENDIF

    // Step 2: Use a sliding window of size `num_target_elements`.
    // We want to find a window (a contiguous subsegment of the array) of this size
    // that already contains the maximum number of target elements.

    current_window_start_idx = 0
    max_target_elements_in_a_window = 0 // Maximum number of target elements found in any window.
    target_elements_in_current_window = 0 // Number of target elements in the current sliding window.

    // Iterate through the array with `current_window_end_idx` marking the end of the window.
    FOR current_window_end_idx FROM 0 TO n - 1:
      // Add the element entering the window from the right.
      IF A_integer_list[current_window_end_idx] <= B_threshold THEN
        target_elements_in_current_window = target_elements_in_current_window + 1
      ENDIF

      // Check if the window has reached the target size (`num_target_elements`).
      window_length = current_window_end_idx - current_window_start_idx + 1
      IF window_length == num_target_elements THEN
        // The window is now full. Compare its count of target elements with the max found so far.
        IF target_elements_in_current_window > max_target_elements_in_a_window THEN
          max_target_elements_in_a_window = target_elements_in_current_window
        ENDIF

        // Prepare to slide the window:
        // The element `A_integer_list[current_window_start_idx]` is about to leave the window from the left.
        // If this leaving element was a target element, decrement the count for the window.
        IF A_integer_list[current_window_start_idx] <= B_threshold THEN
          target_elements_in_current_window = target_elements_in_current_window - 1
        ENDIF
        // Slide the window by incrementing its start index.
        current_window_start_idx = current_window_start_idx + 1
      ENDIF
    ENDFOR

    // The number of swaps needed is the total count of target elements
    // minus the maximum number of target elements we could find in any ideal window position.
    // `max_target_elements_in_a_window` represents the target elements that are already in a "good" spot.
    // The remaining `num_target_elements - max_target_elements_in_a_window` target elements
    // are outside this "best" window and need to be swapped in. Each such swap
    // ideally replaces a non-target element within that window.
    RETURN num_target_elements - max_target_elements_in_a_window
  ENDMETHOD
ENDCLASS

// Example: A = [1, 12, 10, 3, 14, 10, 5], B = 8
// 1. Target elements (<=8): [1, 3, 5]. num_target_elements = 3. Window size = 3.
// 2. Sliding window:
//    - Window [1, 12, 10]: target_elements_in_current_window = 1 (for element 1). max_target_elements_in_a_window = 1.
//    - Window [12, 10, 3]: target_elements_in_current_window = 1 (for element 3). max_target_elements_in_a_window = 1.
//    - Window [10, 3, 14]: target_elements_in_current_window = 1 (for element 3). max_target_elements_in_a_window = 1.
//    - Window [3, 14, 10]: target_elements_in_current_window = 1 (for element 3). max_target_elements_in_a_window = 1.
//    - Window [14, 10, 5]: target_elements_in_current_window = 1 (for element 5). max_target_elements_in_a_window = 1.
// 3. Result = num_target_elements - max_target_elements_in_a_window = 3 - 1 = 2.
```
