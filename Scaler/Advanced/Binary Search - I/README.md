# Advanced/Binary Search - I

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [A3.py](A3.py)
- [Binary_Search_I.pdf](Binary_Search_I.pdf)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)

## Description

This section introduces the core concepts of binary search algorithms, a highly efficient searching technique applicable to sorted data structures. It covers foundational implementations and problem-solving approaches.

### Pseudocode for A1.py
```pseudocode
FUNCTION searchInsert(A_sorted_list, B_target_value):
  low_idx = 0
  high_idx = length of A_sorted_list - 1

  // Continue searching as long as the [low_idx...high_idx] range is valid
  WHILE low_idx <= high_idx:
    // Calculate middle index; (low + high) / 2 can overflow for large low/high,
    // so low + (high - low) / 2 is safer.
    mid_idx = low_idx + FLOOR((high_idx - low_idx) / 2)

    IF A_sorted_list[mid_idx] == B_target_value THEN
      // Target value found at mid_idx
      RETURN mid_idx
    ELSE IF A_sorted_list[mid_idx] < B_target_value THEN
      // If middle element is less than target, target must be in the right half
      low_idx = mid_idx + 1
    ELSE // A_sorted_list[mid_idx] > B_target_value
      // If middle element is greater than target, target must be in the left half
      high_idx = mid_idx - 1
    ENDIF
  ENDWHILE

  // If the loop terminates, B_target_value was not found in A_sorted_list.
  // The value of `low_idx` at this point is the index where B_target_value
  // should be inserted to maintain the sorted order of the list.
  RETURN low_idx
ENDFUNCTION
```

### Pseudocode for A2.py
```pseudocode
// Main function to find the range
FUNCTION searchRange(A_sorted_list, B_target_value):
  n = length of A_sorted_list
  IF n == 0 THEN
    RETURN [-1, -1] // Target cannot be in an empty list
  ENDIF

  // Find the first occurrence (lower bound)
  first_occurrence = find_first_occurrence(A_sorted_list, B_target_value)

  // Find the last occurrence (upper bound)
  last_occurrence = find_last_occurrence(A_sorted_list, B_target_value)

  // Validate if the target was found.
  // The Python code checks `if A[l] == B`. If `first_occurrence` points to an element
  // that is indeed B_target_value, then the target exists.
  // `first_occurrence` (from Python's `search_lower_end` returning `pos`) might be 0
  // even if target is not found (if target < A[0] or A is e.g. [10,20] target 5).
  // So, check A_sorted_list[first_occurrence].
  // Also, ensure first_occurrence is a valid index before checking A_sorted_list[first_occurrence].

  IF first_occurrence >= 0 AND first_occurrence < n AND A_sorted_list[first_occurrence] == B_target_value THEN
    // If first_occurrence is valid and points to the target, then target exists.
    // The last_occurrence found by find_last_occurrence will be the correct end of range.
    RETURN [first_occurrence, last_occurrence]
  ELSE
    RETURN [-1, -1] // Target not found
  ENDIF
ENDFUNCTION


// Helper function to find the first occurrence (lower bound) of B_target_value
FUNCTION find_first_occurrence(A_list, B_target):
  n = length of A_list
  low = 0
  high = n - 1
  ans_index = 0 // Python's `pos` is initialized to 0. This stores the potential answer.
                // This will be the index if B_target is found, otherwise its value is
                // only meaningful if validated by checking A_list[ans_index] == B_target.

  WHILE low <= high:
    mid = low + FLOOR((high - low) / 2)
    IF A_list[mid] == B_target THEN
      ans_index = mid    // Found a potential first occurrence
      high = mid - 1     // Try to find an earlier one in the left half
    ELSE IF A_list[mid] < B_target THEN
      low = mid + 1      // Target must be in the right half
    ELSE // A_list[mid] > B_target
      high = mid - 1     // Target must be in the left half
    ENDIF
  ENDFOR
  RETURN ans_index // Returns the leftmost index where B_target was found, or initial 0 if not.
ENDFUNCTION


// Helper function to find the last occurrence (upper bound) of B_target_value
FUNCTION find_last_occurrence(A_list, B_target):
  n = length of A_list
  low = 0
  high = n - 1
  ans_index = 0 // Python's `pos` is initialized to 0.

  WHILE low <= high:
    mid = low + FLOOR((high - low) / 2)
    IF A_list[mid] == B_target THEN
      ans_index = mid    // Found a potential last occurrence
      low = mid + 1      // Try to find a later one in the right half
    ELSE IF A_list[mid] < B_target THEN
      low = mid + 1      // Target must be in the right half
    ELSE // A_list[mid] > B_target
      high = mid - 1     // Target must be in the left half
    ENDIF
  ENDFOR
  RETURN ans_index // Returns the rightmost index where B_target was found, or initial 0 if not.
ENDFUNCTION
```

### Pseudocode for A3.py
```pseudocode
FUNCTION find_peak_element(A_list):
  n = length of A_list
  low = 0
  high = n - 1

  // Handle edge case for a single element array
  IF n == 1 THEN
    RETURN A_list[0]
  ENDIF

  // Binary search for a peak element
  WHILE low <= high:
    mid = low + FLOOR((high - low) / 2)

    // Case 1: `mid` is an interior element (not the first or last)
    IF mid > 0 AND mid < n - 1 THEN
      // Check if A_list[mid] is a peak: greater than both neighbors
      IF A_list[mid] > A_list[mid-1] AND A_list[mid] > A_list[mid+1] THEN
        RETURN A_list[mid]
      // If on a downward slope or A_list[mid-1] is part of a peak/plateau
      ELSE IF A_list[mid-1] >= A_list[mid] THEN // Python: A[mid-1] >= A[mid] and A[mid] >= A[mid+1]
                                            // Simplified: if left is greater or equal, peak could be left
        high = mid - 1
      // If on an upward slope or A_list[mid+1] is part of a peak/plateau
      ELSE // A_list[mid-1] < A_list[mid] (and not a peak)
           // This implies A_list[mid] <= A_list[mid+1] for it not to be a peak
           // Python: A[mid+1] >= A[mid] and A[mid] > A[mid-1]
           // Simplified: if right is greater or equal (and left is smaller), peak could be right
        low = mid + 1
      ENDIF

    // Case 2: `mid` is the first element (index 0)
    ELSE IF mid == 0 THEN
      // Compare with the right neighbor only (A_list[mid+1])
      // Requires n > 1 for A_list[mid+1] to be valid, which is true due to n==1 check earlier.
      IF A_list[mid] >= A_list[mid+1] THEN
        RETURN A_list[mid] // A_list[0] is a peak
      ELSE // A_list[mid] < A_list[mid+1], so peak must be to the right
        low = mid + 1
      ENDIF

    // Case 3: `mid` is the last element (index n-1)
    ELSE // mid == n - 1
      // Compare with the left neighbor only (A_list[mid-1])
      // Requires n > 1 for A_list[mid-1] to be valid.
      IF A_list[mid] >= A_list[mid-1] THEN
        RETURN A_list[mid] // A_list[n-1] is a peak
      ELSE // A_list[mid] < A_list[mid-1], so peak must be to the left
        high = mid - 1
      ENDIF
    ENDIF
  ENDWHILE

  // Standard peak finding algorithms guarantee a peak is found if the array is non-empty.
  // The loop should ideally always return a peak. If it exits, it implies an issue
  // or an edge case not perfectly handled by the specific Python logic's translation.
  // The Python code itself has no return statement after the while loop.
  RETURN -1 // Placeholder for "should not be reached" or error
ENDFUNCTION
```

### Pseudocode for HW1.py
```pseudocode
FUNCTION find_single_element_in_sorted_paired_array(A_list):
  n = length of A_list
  low = 0
  high = n - 1

  // Handle edge cases
  IF n == 0 THEN
    RETURN -1 // Or appropriate error/default for empty list
  ENDIF
  IF n == 1 THEN
    RETURN A_list[0] // The only element is the single one
  ENDIF
  // If n is even, problem statement is violated (one element must be single)
  // Assuming n is odd as per typical problem constraints.

  WHILE low <= high:
    mid = low + FLOOR((high - low) / 2)

    // Case 1: `mid` is an interior point (not first or last element)
    IF mid > 0 AND mid < n - 1 THEN
      IF A_list[mid] != A_list[mid-1] AND A_list[mid] != A_list[mid+1] THEN
        // A_list[mid] is unique as it's different from both neighbors
        RETURN A_list[mid]
      ELSE IF A_list[mid] == A_list[mid+1] THEN
        // A_list[mid] is paired with its right neighbor A_list[mid+1]
        // Check the pattern of pairs: (A[0],A[1]), (A[2],A[3]), ...
        // If mid is even, this pair (A[mid],A[mid+1]) is correctly positioned if no single element before it.
        // So, the single element must be to the right. Skip this pair.
        IF mid % 2 == 0 THEN
          low = mid + 2
        ELSE
        // If mid is odd, this pair (A[mid],A[mid+1]) means (A[odd_idx],A[odd_idx+1]).
        // This indicates the single element has occurred before this pair, disrupting the pattern.
        // Search in the left part.
          high = mid - 1
        ENDIF
      ELSE // A_list[mid] == A_list[mid-1]. A_list[mid] is paired with its left neighbor.
        // Pair is (A[mid-1], A[mid]).
        IF mid % 2 == 0 THEN
        // mid is even, so mid-1 is odd. Pair is (A[odd_idx], A[even_idx]).
        // This implies single element is to the left, disrupting pattern. Search left.
          high = mid - 2
        ELSE
        // mid is odd, so mid-1 is even. Pair is (A[even_idx], A[odd_idx]).
        // This pair is correctly positioned if no single element before it. Single element is to the right.
          low = mid + 1
        ENDIF
      ENDIF

    // Case 2: `mid` is the first element (index 0)
    ELSE IF mid == 0 THEN
      // Compare with only the right neighbor A_list[mid+1]
      // (n must be > 1, which is true if we didn't return from n==1 case)
      IF A_list[mid] != A_list[mid+1] THEN
        RETURN A_list[mid] // A_list[0] is the single element
      ELSE // A_list[mid] == A_list[mid+1] (forms a pair)
        // Single element must be to the right of this pair (A[0],A[1])
        low = mid + 2
      ENDIF

    // Case 3: `mid` is the last element (index n-1)
    ELSE // mid == n - 1
      // Compare with only the left neighbor A_list[mid-1]
      // (n must be > 1)
      IF A_list[mid] != A_list[mid-1] THEN
        RETURN A_list[mid] // A_list[n-1] is the single element
      ELSE // A_list[mid] == A_list[mid-1] (forms a pair)
        // Single element must be to the left of this pair (A[n-2],A[n-1])
        high = mid - 2
      ENDIF
    ENDIF
  ENDWHILE

  RETURN -1 // Should ideally not be reached if a single element is guaranteed to exist.
ENDFUNCTION
```

### Pseudocode for HW2.py
```pseudocode
FUNCTION searchMatrix(A_matrix, B_target_value):
  num_rows = length of A_matrix
  // Handle empty matrix cases
  IF num_rows == 0 THEN
    RETURN 0 // Target cannot exist
  ENDIF
  num_cols = length of A_matrix[0]
  IF num_cols == 0 THEN
    RETURN 0 // Target cannot exist
  ENDIF

  // Perform binary search on the matrix as if it were a flattened 1D sorted array
  // The total number of elements is num_rows * num_cols.
  // Indices for this conceptual 1D array range from 0 to (num_rows * num_cols - 1).

  low_boundary_flat = 0
  high_boundary_flat = (num_rows * num_cols) - 1

  WHILE low_boundary_flat <= high_boundary_flat:
    mid_flat_index = low_boundary_flat + FLOOR((high_boundary_flat - low_boundary_flat) / 2)

    // Convert the 1D `mid_flat_index` back to 2D matrix coordinates (row, col)
    // Note: `num_cols` must be > 0, which is ensured by the initial checks.
    row_idx = mid_flat_index / num_cols  // Integer division gives the row
    col_idx = mid_flat_index % num_cols  // Modulo gives the column

    current_matrix_element = A_matrix[row_idx][col_idx]

    IF current_matrix_element == B_target_value THEN
      RETURN 1 // Target found
    ELSE IF current_matrix_element < B_target_value THEN
      // Target, if present, must be in the "right" half of the conceptual flattened array
      low_boundary_flat = mid_flat_index + 1
    ELSE // current_matrix_element > B_target_value
      // Target, if present, must be in the "left" half of the conceptual flattened array
      high_boundary_flat = mid_flat_index - 1
    ENDIF
  ENDWHILE

  RETURN 0 // Target not found after completing the binary search
ENDFUNCTION
```
