# Advanced/Binary Search - II

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [A3.py](A3.py)
- [A4.py](A4.py)
- [Adv_Binary_Search_II.pdf](Adv_Binary_Search_II.pdf)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)
- [HW3.py](HW3.py)

## Description

This section explores more advanced applications of binary search, often involving searching on the answer space or applying binary search concepts to non-obvious problems.

### Pseudocode for A1.py
```pseudocode
FUNCTION integer_square_root(A_target_number):
  // Handle base cases for 0 and 1, as their square roots are themselves.
  IF A_target_number == 0 THEN
    RETURN 0
  ENDIF
  IF A_target_number == 1 THEN
    RETURN 1
  ENDIF

  // Initialize the search range for binary search.
  // The square root of N (for N > 1) will be between 1 and N/2 (for N >= 4).
  // Or more generally, between 1 and A_target_number.
  // Python code uses A_target_number // 2 as the initial high bound.
  low_bound = 1
  high_bound = A_target_number // 2
  // If A_target_number is 2 or 3, high_bound becomes 1. This range (1 to 1) is correct.

  best_root_found = 0 // Stores the largest integer `m` such that `m*m <= A_target_number`.
                      // Python initializes to -infinity and uses max(). Initializing to 0 or 1 works too.

  WHILE low_bound <= high_bound:
    mid_val = low_bound + FLOOR((high_bound - low_bound) / 2)
    square_of_mid = mid_val * mid_val

    IF square_of_mid == A_target_number THEN
      // Found the exact integer square root.
      RETURN mid_val
    ELSE IF square_of_mid < A_target_number THEN
      // mid_val * mid_val is less than A_target_number.
      // This means mid_val is a potential candidate for the integer square root.
      // Store it as the current best answer and try to find a larger root
      // by searching in the right half of the range.
      best_root_found = mid_val
      low_bound = mid_val + 1
    ELSE // square_of_mid > A_target_number
      // mid_val is too large, its square exceeds A_target_number.
      // Search in the left half of the range.
      high_bound = mid_val - 1
    ENDIF
  ENDWHILE

  // If the loop finishes, an exact integer square root was not found (i.e., A_target_number is not a perfect square).
  // `best_root_found` will hold the largest integer whose square is less than A_target_number.
  // This is the floor of the actual square root.
  RETURN best_root_found
ENDFUNCTION
```

### Pseudocode for A2.py
```pseudocode
FUNCTION search_in_rotated_array(A_rotated_sorted_list, B_target_value):
  n = length of A_rotated_sorted_list
  IF n == 0 THEN
    RETURN -1 // Target cannot be found in an empty list
  ENDIF

  low_idx = 0
  high_idx = n - 1

  WHILE low_idx <= high_idx:
    mid_idx = low_idx + FLOOR((high_idx - low_idx) / 2)

    IF A_rotated_sorted_list[mid_idx] == B_target_value THEN
      RETURN mid_idx // Target found
    ENDIF

    // Check if the left portion of the array (from low_idx to mid_idx) is sorted
    IF A_rotated_sorted_list[low_idx] <= A_rotated_sorted_list[mid_idx] THEN
      // Left portion is sorted.
      // Check if the target lies within this sorted left portion.
      IF B_target_value >= A_rotated_sorted_list[low_idx] AND B_target_value < A_rotated_sorted_list[mid_idx] THEN
        // Target is in the sorted left portion.
        high_idx = mid_idx - 1
      ELSE
        // Target is not in the sorted left portion, so it must be in the right portion.
        low_idx = mid_idx + 1
      ENDIF
    ELSE
      // Right portion of the array (from mid_idx to high_idx) must be sorted.
      // (This occurs if A_rotated_sorted_list[low_idx] > A_rotated_sorted_list[mid_idx])
      // Check if the target lies within this sorted right portion.
      IF B_target_value > A_rotated_sorted_list[mid_idx] AND B_target_value <= A_rotated_sorted_list[high_idx] THEN
        // Target is in the sorted right portion.
        low_idx = mid_idx + 1
      ELSE
        // Target is not in the sorted right portion, so it must be in the left portion.
        high_idx = mid_idx - 1
      ENDIF
    ENDIF
  ENDWHILE

  RETURN -1 // Target not found
ENDFUNCTION
```

### Pseudocode for A4.py
```pseudocode
// Main function provided in the class structure
FUNCTION solve(A_kth_term, B_val, C_val):
  // Note: The Python code calls get_kth_magic_number with B and C swapped from solve's perspective.
  // So, if solve gets (A, B, C), get_kth_magic_number gets (A, C, B).
  // Let's use original names for clarity in get_kth_magic_number's own pseudocode.
  RETURN get_kth_magic_number(A_kth_term, C_val, B_val) // Parameters mapped as per Python's call
ENDFUNCTION


// Calculates Least Common Multiple (LCM) of two numbers a and b
FUNCTION calculate_lcm(num_a, num_b):
  IF num_a == 0 OR num_b == 0 THEN
    RETURN 0 // LCM with 0 is typically 0 or undefined. Consistent with potential GCD(0,x)=x.
  ENDIF
  // LCM(a,b) = (|a*b|) / GCD(a,b). Assuming positive a, b.
  gcd_of_a_b = calculate_gcd(num_a, num_b) // Assumes calculate_gcd function is available
  RETURN (num_a * num_b) / gcd_of_a_b // Integer division
ENDFUNCTION
// Note: `math.gcd` is used in Python. Pseudocode implies a GCD function.


// Finds the k-th positive integer that is divisible by div1 or div2 (or both)
FUNCTION get_kth_magic_number(k_target_term, div1, div2):
  // Set the search range for the k-th magic number (X).
  // Lower bound can be 1 (or min(div1, div2)).
  // Upper bound can be estimated: k_target_term * min(div1, div2) is a safe upper limit.
  search_low_X = 1
  search_high_X = k_target_term * minimum(div1, div2)

  lcm_of_divs = calculate_lcm(div1, div2)

  result_X = search_high_X // Initialize with a value, will be refined by binary search.
                           // Python's loop `while l < h` ensures `l` is the answer.

  WHILE search_low_X < search_high_X:
    mid_X_candidate = search_low_X + FLOOR((search_high_X - search_low_X) / 2)

    // Count how many numbers <= mid_X_candidate are divisible by div1 or div2
    // Using Principle of Inclusion-Exclusion:
    // Count = (count divisible by div1) + (count divisible by div2) - (count divisible by LCM(div1,div2))

    count_div_by_div1 = mid_X_candidate / div1 // Integer division
    count_div_by_div2 = mid_X_candidate / div2 // Integer division
    count_div_by_lcm = 0
    IF lcm_of_divs > 0 THEN // Avoid division by zero if lcm was 0 (e.g., if div1 or div2 was 0)
        count_div_by_lcm = mid_X_candidate / lcm_of_divs // Integer division
    ENDIF

    num_magic_numbers_up_to_mid_X = count_div_by_div1 + count_div_by_div2 - count_div_by_lcm

    IF num_magic_numbers_up_to_mid_X < k_target_term THEN
      // mid_X_candidate is too small; there are fewer than k_target_term magic numbers up to it.
      // The k-th magic number must be larger.
      search_low_X = mid_X_candidate + 1
    ELSE // num_magic_numbers_up_to_mid_X >= k_target_term
      // mid_X_candidate is a potential answer, or the actual k-th magic number might be smaller.
      // We want the smallest X that satisfies this, so try the left half (including mid_X_candidate).
      search_high_X = mid_X_candidate
    ENDIF
  ENDWHILE

  // The loop terminates when search_low_X == search_high_X.
  // This value is the smallest X such that count_magic_numbers(X) >= k_target_term.
  RETURN search_low_X
ENDFUNCTION
```

### Pseudocode for HW1.py
```pseudocode
FUNCTION kthsmallest_implementation_analysis(A_list, B_k_value):
  // NOTE: The problem title is "kthsmallest". However, the provided Python code,
  // which uses a min-heap of size B_k_value and replaces the heap's minimum
  // if a new element is larger, effectively finds the B_k_value-th LARGEST element.
  // This pseudocode describes the behavior of the GIVEN Python code.

  n = length of A_list
  // Basic validation for B_k_value (Python code might error if B_k_value is out of typical bounds like 0 or >n)
  IF B_k_value <= 0 OR B_k_value > n THEN
    RETURN error_or_undefined // Depending on expected behavior for invalid B_k_value
  ENDIF

  // Initialize a min-heap (Python's heapq uses a list for this)
  min_heap = empty_min_heap

  // Step 1: Fill the heap with the first B_k_value elements from A_list
  FOR i FROM 0 TO B_k_value - 1:
    ADD A_list[i] TO min_heap
  ENDFOR
  // After this, min_heap contains B_k_value elements.
  // The smallest of these is at the root (min_heap.peek_min()).

  // Step 2: Iterate through the rest of the elements in A_list
  FOR i FROM B_k_value TO n - 1:
    current_array_element = A_list[i]
    smallest_element_in_heap = min_heap.peek_min()

    // If the current_array_element is greater than the smallest element currently in the heap,
    // it means this current_array_element is larger than at least one of the
    // B_k_value elements that are currently considered among the "largest B_k_value seen so far".
    // So, remove the smallest from the heap and add this new, larger element.
    IF current_array_element > smallest_element_in_heap THEN
      REMOVE_MIN from min_heap // Removes the smallest element
      ADD current_array_element TO min_heap // Adds the new element, heap re-balances
    ENDIF
  ENDFOR

  // After processing all elements, the min_heap contains the B_k_value largest elements from A_list.
  // The root of this min-heap (min_heap.peek_min()) is the smallest among these B_k_value largest elements.
  // Therefore, this is the B_k_value-th largest element of the entire A_list.
  RETURN min_heap.peek_min()
ENDFUNCTION
```

### Pseudocode for HW2.py
```pseudocode
// Note: The provided Python code for findMedian is incomplete.
// This pseudocode describes the standard algorithm to find the median
// in a row-wise sorted matrix using binary search on the answer space (the possible median values).

FUNCTION findMedian_in_row_sorted_matrix(A_matrix):
  num_rows = length of A_matrix
  IF num_rows == 0 THEN
    RETURN error_or_undefined // Or handle as per problem constraints for empty matrix
  ENDIF
  num_cols = length of A_matrix[0]
  IF num_cols == 0 THEN
    RETURN error_or_undefined // Or handle for matrix with empty rows
  ENDIF

  // Step 1: Determine the search range for the median value.
  // The median must be between the smallest and largest element in the matrix.
  min_val_in_matrix = A_matrix[0][0]
  max_val_in_matrix = A_matrix[0][0]
  FOR r FROM 0 TO num_rows - 1:
    // Smallest in row r is A_matrix[r][0]
    IF A_matrix[r][0] < min_val_in_matrix THEN
      min_val_in_matrix = A_matrix[r][0]
    ENDIF
    // Largest in row r is A_matrix[r][num_cols-1]
    IF A_matrix[r][num_cols-1] > max_val_in_matrix THEN
      max_val_in_matrix = A_matrix[r][num_cols-1]
    ENDIF
  ENDFOR

  // Step 2: The median is the element that would be at the middle position
  // if all (num_rows * num_cols) elements were sorted.
  // We need (num_rows * num_cols) / 2 + 1 elements to be less than or equal to the median
  // (for 1-based median definition, or count of elements <= median should be > (total_elements/2)).
  // Let k = (num_rows * num_cols + 1) / 2 (for 1-based k-th smallest).
  // We are looking for the smallest number `x` such that at least `k` elements are `<= x`.
  kth_smallest_target_count = ((num_rows * num_cols) / 2) + 1 // Integer division

  binary_search_low_val = min_val_in_matrix
  binary_search_high_val = max_val_in_matrix
  median_candidate = min_val_in_matrix // Initialize with a possible value

  WHILE binary_search_low_val <= binary_search_high_val:
    mid_val_to_check = binary_search_low_val + FLOOR((binary_search_high_val - binary_search_low_val) / 2)

    // Count how many elements in the entire matrix are less than or equal to `mid_val_to_check`.
    count_elements_le_mid = 0
    FOR r FROM 0 TO num_rows - 1:
      // For each row, count elements <= mid_val_to_check using binary search (upper_bound).
      // `count_in_row` = index of first element > mid_val_to_check in A_matrix[r]
      // (or num_cols if all elements are <= mid_val_to_check).
      // This is equivalent to `bisect_right` in Python.

      // Binary search within the row A_matrix[r]
      row_l, row_h = 0, num_cols - 1
      elements_in_row_le = 0
      WHILE row_l <= row_h:
        row_m = row_l + FLOOR((row_h - row_l) / 2)
        IF A_matrix[r][row_m] <= mid_val_to_check THEN
          elements_in_row_le = row_m + 1 // This many elements (0 to row_m) are <= mid_val_to_check
          row_l = row_m + 1 // Try to find more in the right part
        ELSE
          row_h = row_m - 1 // Element is too large, search in left part
        ENDIF
      ENDWHILE
      count_elements_le_mid = count_elements_le_mid + elements_in_row_le
    ENDFOR // End of counting for mid_val_to_check

    // Adjust binary search range based on the count
    IF count_elements_le_mid < kth_smallest_target_count THEN
      // mid_val_to_check is too small, fewer than k elements are <= it.
      // The median must be larger.
      binary_search_low_val = mid_val_to_check + 1
    ELSE // count_elements_le_mid >= kth_smallest_target_count
      // mid_val_to_check is a potential median (or median is smaller).
      // Store it and try for a smaller value that still satisfies the condition.
      median_candidate = mid_val_to_check
      binary_search_high_val = mid_val_to_check - 1
    ENDIF
  ENDWHILE

  // The loop finds the smallest `mid_val_to_check` (stored in median_candidate eventually, or binary_search_low_val)
  // for which the count of elements less than or equal to it is at least `kth_smallest_target_count`.
  RETURN median_candidate
ENDFUNCTION
```

### Pseudocode for HW3.py
```pseudocode
// Main function to search in a bitonic array
FUNCTION solve_search_in_bitonic(A_bitonic_list, B_target):
  n = length of A_bitonic_list
  IF n == 0 THEN
    RETURN -1 // Cannot find target in an empty list
  ENDIF

  // Step 1: Find the index of the peak element (the bitonic point).
  pivot_index = find_bitonic_pivot_index(A_bitonic_list)

  // Step 2: Perform binary search in the increasing part of the array (from index 0 to pivot_index).
  index_in_increasing_part = binary_search_increasing_portion(A_bitonic_list, B_target, 0, pivot_index)

  IF index_in_increasing_part != -1 THEN
    // Target found in the increasing portion.
    RETURN index_in_increasing_part
  ELSE
    // Target not found in the increasing portion.
    // Search in the decreasing part (from pivot_index + 1 to n - 1).
    // Ensure there is a decreasing part to search.
    IF pivot_index + 1 < n THEN
      index_in_decreasing_part = binary_search_decreasing_portion(A_bitonic_list, B_target, pivot_index + 1, n - 1)
      RETURN index_in_decreasing_part // Will be -1 if not found here either
    ELSE
      RETURN -1 // No decreasing portion to search, and not found in increasing portion.
    ENDIF
  ENDIF
ENDFUNCTION


// Helper function to find the index of the peak element in a bitonic array
FUNCTION find_bitonic_pivot_index(A_list_local):
  n_len = length of A_list_local
  IF n_len == 0 THEN RETURN -1 // Or handle error appropriately
  IF n_len == 1 THEN RETURN 0  // Single element is its own peak

  low_ptr = 0
  high_ptr = n_len - 1

  WHILE low_ptr < high_ptr:
    mid_ptr = low_ptr + FLOOR((high_ptr - low_ptr) / 2)
    // Compare element at mid_ptr with its right neighbor A_list_local[mid_ptr+1]
    // This comparison helps determine if we are on an upward or downward slope.
    IF A_list_local[mid_ptr] < A_list_local[mid_ptr+1] THEN
      // We are on an upward slope, or mid_ptr+1 is the peak. Peak is to the right.
      low_ptr = mid_ptr + 1
    ELSE // A_list_local[mid_ptr] >= A_list_local[mid_ptr+1]
      // We are on a downward slope, or mid_ptr is the peak. Peak is mid_ptr or to its left.
      high_ptr = mid_ptr
    ENDIF
  ENDWHILE

  // When loop terminates, low_ptr (or high_ptr) is the index of the peak element.
  RETURN low_ptr
ENDFUNCTION


// Standard binary search for an increasing sorted segment
FUNCTION binary_search_increasing_portion(A_sub_list, B_val_to_find, start_range_idx, end_range_idx):
  low = start_range_idx
  high = end_range_idx

  WHILE low <= high:
    mid = low + FLOOR((high - low) / 2)
    IF A_sub_list[mid] == B_val_to_find THEN
      RETURN mid // Target found
    ELSE IF A_sub_list[mid] < B_val_to_find THEN
      low = mid + 1 // Target is in the right half
    ELSE // A_sub_list[mid] > B_val_to_find
      high = mid - 1 // Target is in the left half
    ENDIF
  ENDWHILE
  RETURN -1 // Target not found
ENDFUNCTION


// Binary search for a decreasing sorted segment
FUNCTION binary_search_decreasing_portion(A_sub_list, B_val_to_find, start_range_idx, end_range_idx):
  low = start_range_idx
  high = end_range_idx

  WHILE low <= high:
    mid = low + FLOOR((high - low) / 2)
    IF A_sub_list[mid] == B_val_to_find THEN
      RETURN mid // Target found
    ELSE IF A_sub_list[mid] > B_val_to_find THEN // Array is decreasing
      low = mid + 1 // If A_sub_list[mid] is too large, target is to the right (smaller values)
    ELSE // A_sub_list[mid] < B_val_to_find
      high = mid - 1 // If A_sub_list[mid] is too small, target is to the left (larger values)
    ENDIF
  ENDWHILE
  RETURN -1 // Target not found
ENDFUNCTION
```

### Pseudocode for A3.py
```pseudocode
// Main function that uses binary search on the answer (possible length k)
FUNCTION solve_max_k_for_subarray_sum_constraint(A_list, B_sum_upper_bound):
  n = length of A_list

  // The possible values for k (subarray length) range from 0 to n.
  // We are looking for the maximum k such that all subarrays of length k have sum <= B_sum_upper_bound.
  binary_search_low_k = 0
  binary_search_high_k = n

  max_k_found = 0 // Stores the largest k that satisfies the condition

  WHILE binary_search_low_k <= binary_search_high_k:
    candidate_k = binary_search_low_k + FLOOR((binary_search_high_k - binary_search_low_k) / 2)

    // Check if candidate_k is a valid length (i.e., all subarrays of this length meet the sum constraint)
    IF check_condition_for_length_k(A_list, B_sum_upper_bound, candidate_k) THEN
      // candidate_k is valid. It might be our answer, or we might find a larger valid k.
      max_k_found = candidate_k // Python uses max(ans, k)
      binary_search_low_k = candidate_k + 1 // Try for a larger k
    ELSE
      // candidate_k is not valid (some subarray of length candidate_k has sum > B_sum_upper_bound).
      // We need to try smaller lengths.
      binary_search_high_k = candidate_k - 1
    ENDIF
  ENDWHILE

  RETURN max_k_found
ENDFUNCTION


// Helper function to check if all subarrays of a given length `k_val`
// in `A_data_list` have their sum less than or equal to `B_limit`.
FUNCTION check_condition_for_length_k(A_data_list, B_limit, k_val):
  n_len = length of A_data_list

  // A subarray of length 0 has sum 0, which is always <= B_limit (assuming B_limit >= 0).
  IF k_val == 0 THEN
    RETURN true
  ENDIF
  // If k_val is greater than n_len, no such subarray exists, so the condition is vacuously true.
  IF k_val > n_len THEN
    RETURN true
  ENDIF

  current_window_sum = 0
  window_start_pointer = 0

  // Iterate through A_data_list with a sliding window of size k_val
  FOR window_end_pointer FROM 0 TO n_len - 1:
    // Add the current element to the window sum
    current_window_sum = current_window_sum + A_data_list[window_end_pointer]

    // When the window has reached the size k_val
    IF (window_end_pointer - window_start_pointer + 1) == k_val THEN
      IF current_window_sum > B_limit THEN
        RETURN false // Found a subarray of length k_val whose sum exceeds B_limit
      ENDIF
      // Slide the window: subtract the element that is leaving the window from the left
      current_window_sum = current_window_sum - A_data_list[window_start_pointer]
      window_start_pointer = window_start_pointer + 1 // Move the start of the window
    ENDIF
  ENDFOR

  // If the loop completes, all subarrays of length k_val were checked and satisfied the condition
  RETURN true
ENDFUNCTION
```
