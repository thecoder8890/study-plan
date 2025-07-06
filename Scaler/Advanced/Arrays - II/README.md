# Advanced/Arrays - II

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [A3.py](A3.py)
- [Adv_Arrays_2.pdf](Adv_Arrays_2.pdf)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)

## Description

This section covers intermediate to advanced concepts and problems related to Arrays - II, building upon foundational array knowledge.

### Pseudocode for A1.py
```pseudocode
FUNCTION solve(A_size, B_operations):
  // Initialize an array 'result_array' of size A_size with all zeros.
  // This array will first function as a difference array.
  INITIALIZE result_array as an array of size A_size with all 0s

  // Process each operation to update the difference array
  FOR EACH operation IN B_operations:
    start_idx_1_based = operation[0]  // 1-indexed start of range
    end_idx_1_based = operation[1]    // 1-indexed end of range
    value_to_add = operation[2]

    // Apply the value at the start of the range (0-indexed)
    // Assuming 1 <= start_idx_1_based <= A_size
    result_array[start_idx_1_based - 1] = result_array[start_idx_1_based - 1] + value_to_add

    // If the range does not extend to the very last element,
    // subtract the value at the position immediately after the end of the range.
    // This ensures the value is only effectively applied within the [start, end] range.
    // end_idx_1_based < A_size means result_array[end_idx_1_based] is a valid index.
    IF end_idx_1_based < A_size THEN
        result_array[end_idx_1_based] = result_array[end_idx_1_based] - value_to_add
    ENDIF
  ENDFOR

  // Calculate prefix sums to convert the difference array into final values
  FOR i FROM 1 TO A_size - 1:
    result_array[i] = result_array[i] + result_array[i-1]
  ENDFOR

  RETURN result_array
ENDFUNCTION
```

### Pseudocode for A2.py
```pseudocode
FUNCTION maxSubArray(A_list):
  // Assuming A_list is non-empty based on typical problem constraints (e.g., N >= 1)
  // and gsum initialization with A_list[0].
  IF A_list is empty THEN
    // Define behavior for empty list, e.g., return 0 or error.
    // Python code would error if A_list is empty at A_list[0].
    RETURN 0 // Or appropriate error/default value
  ENDIF

  global_max_sum = A_list[0]
  current_max_ending_here = 0 // Max sum of a subarray ending at the current position

  FOR EACH number IN A_list:
    // Decide whether to extend the current subarray or start a new one with 'number'.
    // If current_max_ending_here is negative, (current_max_ending_here + number) < number.
    // So, current_max_ending_here effectively resets to 'number'.
    current_max_ending_here = maximum(current_max_ending_here + number, number)

    // Update the overall maximum sum found so far.
    global_max_sum = maximum(global_max_sum, current_max_ending_here)
  ENDFOR

  RETURN global_max_sum
ENDFUNCTION
```

### Pseudocode for A3.py
```pseudocode
FUNCTION solve(A_list, B_elements_to_pick):
  n = length of A_list

  // Edge case: if B_elements_to_pick is 0, sum is 0 (or handle as per problem spec for B=0)
  IF B_elements_to_pick == 0 THEN RETURN 0 ENDIF
  // Edge case: if B_elements_to_pick > n, sum all elements (or handle as error)
  IF B_elements_to_pick > n THEN
    sum_all = 0
    FOR EACH element IN A_list: sum_all = sum_all + element
    RETURN sum_all
  ENDIF

  // 1. Calculate sum of first B_elements_to_pick elements (all from left)
  current_sum = 0
  idx = 0
  WHILE idx < B_elements_to_pick:
    current_sum = current_sum + A_list[idx]
    idx = idx + 1
  ENDFOR

  max_possible_sum = current_sum

  // 2. Slide the window:
  //    Remove one element from the right end of the 'left part'
  //    Add one element from the right end of the array A_list

  // `left_idx_to_remove` points to the element to be removed from the left part's sum.
  // Starts at (B_elements_to_pick - 1).
  left_idx_to_remove = B_elements_to_pick - 1

  // `right_idx_to_add` points to the element from the end of A_list to be added.
  // Starts at (n - 1).
  right_idx_to_add = n - 1

  // Loop B_elements_to_pick times. Each iteration considers one more element from the right
  // and one less element from the left.
  FOR count_from_right FROM 1 TO B_elements_to_pick:
    // Remove A_list[left_idx_to_remove] from current_sum
    current_sum = current_sum - A_list[left_idx_to_remove]
    left_idx_to_remove = left_idx_to_remove - 1

    // Add A_list[right_idx_to_add] to current_sum
    current_sum = current_sum + A_list[right_idx_to_add]
    right_idx_to_add = right_idx_to_add - 1

    max_possible_sum = maximum(max_possible_sum, current_sum)
  ENDFOR

  RETURN max_possible_sum
ENDFUNCTION
```

### Pseudocode for HW1.py
```pseudocode
FUNCTION flip(A_string):
  n = length of A_string
  IF n == 0 THEN
    RETURN empty_list // Assuming N >= 1 from typical problem constraints
  ENDIF

  // Create an auxiliary array: 1 for '0' (represents a gain of +1 if flipped), -1 for '1' (gain of -1 if flipped)
  arr_gain = new integer_array of size n
  FOR i FROM 0 TO n-1:
    IF A_string[i] == '1' THEN
      arr_gain[i] = -1
    ELSE
      arr_gain[i] = 1
    ENDIF
  ENDFOR

  max_overall_gain = negative_infinity
  current_gain_ending_here = 0

  // Stores 0-indexed L and R for the segment that yields max_overall_gain
  // Initialized like in Python code (global_start, global_end = 0,0)
  best_L_idx = 0
  best_R_idx = 0

  // Stores the 0-indexed start of the current subarray being considered by Kadane's
  current_subarray_start_L_idx = 0

  FOR current_idx FROM 0 TO n - 1:
    current_gain_ending_here = current_gain_ending_here + arr_gain[current_idx]

    IF current_gain_ending_here > max_overall_gain THEN
      max_overall_gain = current_gain_ending_here
      best_L_idx = current_subarray_start_L_idx
      best_R_idx = current_idx
    ENDIF

    IF current_gain_ending_here < 0 THEN
      current_subarray_start_L_idx = current_idx + 1
      current_gain_ending_here = 0
    ENDIF
  ENDFOR

  // Python's specific return condition:
  // If the best segment found by Kadane's is a single element that was originally '1' (value -1 in arr_gain),
  // then no operation is performed. This happens when max_overall_gain is -1.
  IF (best_L_idx == best_R_idx) AND (arr_gain[best_L_idx] == -1) THEN
    RETURN empty_list
  ELSE
    // If max_overall_gain is still negative_infinity (e.g., if n=0 was not handled, though n>0 here),
    // this indicates an issue or empty input not caught.
    // For any n > 0, max_overall_gain will be updated.
    IF max_overall_gain == negative_infinity THEN // Safety check, should not be hit if n > 0
        RETURN empty_list
    ELSE
        RETURN [best_L_idx + 1, best_R_idx + 1] // Convert to 1-indexed
    ENDIF
  ENDIF
ENDFUNCTION
```

### Pseudocode for HW2.py
```pseudocode
FUNCTION solve(A_list):
  n = length of A_list

  IF n == 0 THEN
    RETURN -1 // No equilibrium index in an empty array
  ENDIF
  // For n=1, Python code would error due to out-of-bounds access.
  // A correct equilibrium definition usually means index 0 for n=1.
  // The pseudocode below reflects Python's structure which implicitly assumes n is large enough for its checks.

  // --- Prefix Sum Calculation ---
  prefix_sums = new array of size n
  FOR i FROM 0 TO n - 1:
    IF i == 0 THEN
      prefix_sums[i] = A_list[i]
    ELSE
      prefix_sums[i] = prefix_sums[i-1] + A_list[i]
    ENDIF
  ENDFOR

  // --- Suffix Sum Calculation ---
  suffix_sums = new array of size n
  FOR i FROM n - 1 DOWNTO 0:
    IF i == n - 1 THEN
      suffix_sums[i] = A_list[i]
    ELSE
      suffix_sums[i] = suffix_sums[i+1] + A_list[i]
    ENDIF
  ENDFOR

  // --- Find Equilibrium Index ---
  // The Python code iterates i from 0 to n-1.
  // Its direct indexing (e.g., ss[i+1] when i=0) implies certain assumptions about n.
  // For example, if n=1, ss[1] is an error.

  FOR i FROM 0 TO n - 1:
    IF i == 0 THEN
      // Condition for A_list[0] to be equilibrium: sum of A_list[1...n-1] must be 0.
      // This is suffix_sums[1] if n > 1.
      IF n == 1 THEN RETURN 0 ENDIF // Single element is always equilibrium
      IF n > 1 AND suffix_sums[i+1] == 0 THEN
        RETURN 0
      ENDIF
    ELSE IF i == n - 1 THEN
      // Condition for A_list[n-1] to be equilibrium: sum of A_list[0...n-2] must be 0.
      // This is prefix_sums[n-2] if n > 1. (Note: Python uses ps[i-1])
      // (Python code already handled n=1 if i==0 was true, so n > 1 here)
      IF prefix_sums[i-1] == 0 THEN
        RETURN n - 1
      ENDIF
    ELSE
      // For 0 < i < n-1
      // Condition for A_list[i] to be equilibrium: sum(A_list[0...i-1]) == sum(A_list[i+1...n-1])
      // This is prefix_sums[i-1] == suffix_sums[i+1]
      IF prefix_sums[i-1] == suffix_sums[i+1] THEN
        RETURN i
      ENDIF
    ENDIF
  ENDFOR

  RETURN -1 // No equilibrium index found
ENDFUNCTION
```
