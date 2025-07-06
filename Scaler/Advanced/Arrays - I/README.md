# Advanced/Arrays - I

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [A3.py](A3.py)
- [A4.py](A4.py)
- [A5.py](A5.py)
- [Arrays_1_Advance_.pdf](Arrays_1_Advance_.pdf)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)

## Description

This section covers foundational concepts and problems related to Arrays - I at an advanced level.

### Pseudocode for A1.py
```pseudocode
FUNCTION maxArr(A):
  INITIALIZE s as an empty list
  INITIALIZE d as an empty list

  FOR i FROM 0 TO length(A) - 1:
    n = A[i]
    APPEND (n + i) TO s
    APPEND (n - i) TO d
  ENDFOR

  max_s = maximum value in s
  min_s = minimum value in s

  max_d = maximum value in d
  min_d = minimum value in d

  result1 = max_s - min_s
  result2 = max_d - min_d

  IF result1 > result2 THEN
    RETURN result1
  ELSE
    RETURN result2
  ENDIF
ENDFUNCTION
```

### Pseudocode for HW2.py
```pseudocode
FUNCTION maxset(A_list):
  n = length of A_list
  loop_idx = 0

  best_subarray_found = empty_list
  current_processing_subarray = empty_list

  current_segment_sum = 0
  max_segment_sum_recorded = 0 // Python's `prev_sum` initialized to 0

  WHILE loop_idx < n:
    current_element = A_list[loop_idx]

    // If current element is non-negative, add it to the current segment
    IF current_element >= 0 THEN
      current_segment_sum = current_segment_sum + current_element
      APPEND current_element TO current_processing_subarray
    ENDIF

    // Condition to evaluate the `current_processing_subarray` based on Python's logic:
    // Evaluate if the current element breaks the non-negative sequence (is negative),
    // OR if it's the last element of A_list AND that last element is strictly positive.
    // Note: This specific condition in Python (A[i] > 0 at the end) can lead to bugs
    // if the array ends with a zero that should be part of the max segment, or if A=[0].

    evaluate_now = false
    IF current_element < 0 THEN
      evaluate_now = true
    ELSE IF loop_idx == n - 1 AND current_element > 0 THEN // Python's (i == n-1 and A[i] > 0)
      evaluate_now = true
    ENDIF

    IF evaluate_now THEN
      // Compare current_segment_sum with max_segment_sum_recorded
      IF current_segment_sum > max_segment_sum_recorded THEN
        best_subarray_found = current_processing_subarray (make a copy)
        max_segment_sum_recorded = current_segment_sum
      ELSE IF current_segment_sum == max_segment_sum_recorded THEN
        IF length of current_processing_subarray > length of best_subarray_found THEN
          best_subarray_found = current_processing_subarray (make a copy)
          // Tie-breaking by minimum starting index is implicitly handled by taking the first one.
        ENDIF
      ENDIF

      // Reset for the next potential segment
      current_processing_subarray = empty_list
      current_segment_sum = 0
    ENDIF

    loop_idx = loop_idx + 1
  ENDFOR

  // The Python code directly returns `ans` (best_subarray_found).
  // If the loop finishes and a segment was being built but not evaluated
  // (e.g. A=[1,0] due to the A[i]>0 condition), that segment is lost.
  // For A=[0], the loop runs, A[0]=0. current_sum=0, current_processing_subarray=[0].
  // The evaluate_now condition (0<0 OR (0==0 AND 0>0)) is FALSE.
  // Loop ends. Returns empty list. This is the behavior of the Python code.
  RETURN best_subarray_found
ENDFUNCTION
```

### Pseudocode for HW1.py
```pseudocode
FUNCTION plusOne(A_digits):
  n = length of A_digits

  IF n == 0 THEN
    // Behavior for empty input depends on problem specification,
    // Python code might error or produce unintended result.
    // Assuming valid non-empty A_digits based on typical problem context.
    // For robustness, one might return [1] or raise error.
    RETURN [1] // Placeholder for actual behavior or error
  ENDIF

  carry = 0
  ans_list = empty_list

  // Determine the starting index for processing, skipping leading zeros
  start_processing_idx = 0
  WHILE start_processing_idx < n AND A_digits[start_processing_idx] == 0:
    start_processing_idx = start_processing_idx + 1
  ENDFOR

  // Iterate from the rightmost digit of the original array `A_digits`
  // down to the `start_processing_idx`.
  // Note: If `A_digits` was all zeros (e.g., [0,0,0]), `start_processing_idx` becomes `n`.
  // The loop `FOR i FROM n - 1 DOWNTO n` would be empty.
  FOR i FROM n - 1 DOWNTO start_processing_idx:
    sum_val = carry + A_digits[i]

    IF i == n - 1 THEN // This condition applies to the last element of the input array `A_digits`
      sum_val = sum_val + 1 // Add 1 to the number
    ENDIF

    current_digit = sum_val % 10
    carry = sum_val / 10  // Integer division

    INSERT current_digit AT THE BEGINNING of ans_list
  ENDFOR

  // If there's a remaining carry after processing all digits (e.g., [9,9] + 1 = [1,0,0])
  IF carry != 0 THEN
    INSERT carry AT THE BEGINNING of ans_list
  ENDIF

  // The provided Python code returns an empty list `[]` if the input `A_digits`
  // consists of all zeros (e.g., [0] or [0,0,0]).
  // This is because the main loop doesn't run, `ans_list` remains empty, and `carry` remains 0.
  // A typical expectation for `plusOne([0])` would be `[1]`.
  // The pseudocode here reflects the Python code's behavior.
  RETURN ans_list
ENDFUNCTION
```

### Pseudocode for A2.py
```pseudocode
FUNCTION trap(A):
  n = length of A
  IF n == 0 THEN
    RETURN 0
  ENDIF

  INITIALIZE lgt as an array of size n with all 0s
  INITIALIZE rgt as an array of size n with all 0s

  prev_lgt = 0
  FOR i FROM 0 TO n - 1:
    IF i == 0 THEN
      lgt[i] = 0
    ELSE
      lgt[i] = prev_lgt
    ENDIF
    prev_lgt = maximum(prev_lgt, A[i])
  ENDFOR

  prev_rgt = 0
  FOR i FROM n - 1 DOWNTO 0:
    IF i == n - 1 THEN
      rgt[i] = 0
    ELSE
      rgt[i] = prev_rgt
    ENDIF
    prev_rgt = maximum(prev_rgt, A[i])
  ENDFOR

  INITIALIZE total_water_trapped = 0
  FOR i FROM 0 TO n - 1:
    water_boundary = minimum(lgt[i], rgt[i])
    current_water = water_boundary - A[i]
    IF current_water > 0 THEN
      total_water_trapped = total_water_trapped + current_water
    ENDIF
  ENDFOR

  RETURN total_water_trapped
ENDFUNCTION
```

### Pseudocode for A3.py
```pseudocode
FUNCTION maxSubArray(A):
  // Assuming A is non-empty as per problem description (N >= 1)
  global_max_sum = A[0]
  current_local_sum = 0 // Max sum of subarray ending at current position

  FOR EACH number IN A:
    // Option 1: Extend the current subarray ending at previous position by adding 'number'
    // Option 2: Start a new subarray consisting only of 'number'
    current_local_sum = maximum(current_local_sum + number, number)

    // Update the overall maximum sum found so far
    global_max_sum = maximum(global_max_sum, current_local_sum)
  ENDFOR

  RETURN global_max_sum
ENDFUNCTION
```

### Pseudocode for A4.py
```pseudocode
FUNCTION solve(A_num_beggars, B_donations):
  // Initialize an array 'final_amounts' of size A_num_beggars with all zeros.
  // This array will first be used as a difference array.
  INITIALIZE final_amounts as an array of size A_num_beggars with all 0s

  // Process each donation to update the difference array
  FOR EACH donation IN B_donations:
    start_index = donation[0]  // 1-indexed start of range
    end_index = donation[1]    // 1-indexed end of range
    amount = donation[2]

    // Apply the donation amount at the start of the range (0-indexed)
    IF (start_index - 1) < A_num_beggars THEN
        final_amounts[start_index - 1] = final_amounts[start_index - 1] + amount
    ENDIF

    // If the range does not extend to the very last beggar,
    // subtract the amount at the position immediately after the end of the range.
    IF end_index < A_num_beggars THEN
        final_amounts[end_index] = final_amounts[end_index] - amount
    ENDIF
  ENDFOR

  // Calculate the prefix sums to get the final amount for each beggar
  FOR i FROM 1 TO A_num_beggars - 1:
    final_amounts[i] = final_amounts[i] + final_amounts[i-1]
  ENDFOR

  RETURN final_amounts
ENDFUNCTION
```

### Pseudocode for A5.py
```pseudocode
FUNCTION flip(A_string):
  n = length of A_string
  IF n == 0 THEN
    RETURN empty_list // Assuming N >= 1 from constraints
  ENDIF

  // Create an auxiliary array: 1 for '0' (gain +1), -1 for '1' (gain -1)
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

  // Stores 0-indexed L and R for the segment with max_overall_gain
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

  // Check Python's specific return condition:
  // If the best segment found corresponds to flipping a single '1' (value -1 in arr_gain),
  // it implies the original string was all '1's or this single '1' flip was the 'max gain' (-1).
  // In such cases, no operation is performed.
  // Note: `best_L_idx` and `best_R_idx` are initialized to 0. If `max_overall_gain` remains
  // `negative_infinity` (e.g., if `n` was 0 and not handled), these indices would be 0.
  // However, for `n > 0`, `max_overall_gain` will be updated, and `best_L_idx`, `best_R_idx`
  // will point to the segment that yielded this `max_overall_gain`.

  IF (best_L_idx == best_R_idx) AND (arr_gain[best_L_idx] == -1) THEN
    RETURN empty_list
  ELSE
    // If max_overall_gain is negative_infinity, it implies an issue (e.g. n=0 not caught).
    // For valid n > 0, max_overall_gain will have a concrete value.
    // The Python code returns a range unless the specific single '1' flip condition is met.
    // This means even if max_overall_gain is 0 or negative (but not from a single '1' flip),
    // a range is returned, which might maximize 1s if multiple solutions give the same max count.
    IF max_overall_gain == negative_infinity THEN // Fallback, should not be hit if n > 0
        RETURN empty_list
    ELSE
        RETURN [best_L_idx + 1, best_R_idx + 1] // Convert to 1-indexed
    ENDIF
  ENDIF
ENDFUNCTION
```
