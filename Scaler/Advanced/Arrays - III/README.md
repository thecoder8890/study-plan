# Advanced/Arrays - III

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [A3.py](A3.py)
- [A4.py](A4.py)
- [Adv_Arrays_3.pdf](Adv_Arrays_3.pdf)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)
- [HW3.py](HW3.py)
- [HW4.py](HW4.py)

## Description

This section delves into more complex array manipulations, algorithms, and problem-solving techniques as part of Arrays - III.

### Pseudocode for A1.py
```pseudocode
FUNCTION solve(A_square_matrix):
  n = dimension of A_square_matrix // Number of rows (assuming n x n matrix)
  IF n == 0 THEN
    RETURN 0 // No elements if matrix dimension is 0
  ENDIF

  total_sum = 0

  FOR i FROM 0 TO n - 1: // Iterate through each row index
    FOR j FROM 0 TO n - 1: // Iterate through each column index
      // Calculate how many submatrices contain the element A_square_matrix[i][j]

      // Number of choices for the top-left corner (row_start, col_start) of a submatrix
      // such that row_start <= i and col_start <= j.
      // row_start can be any from 0 to i (i+1 choices).
      // col_start can be any from 0 to j (j+1 choices).
      choices_for_top_left_corner = (i + 1) * (j + 1)

      // Number of choices for the bottom-right corner (row_end, col_end) of a submatrix
      // such that row_end >= i and col_end >= j.
      // row_end can be any from i to n-1 (n-i choices).
      // col_end can be any from j to n-1 (n-j choices).
      choices_for_bottom_right_corner = (n - i) * (n - j)

      num_submatrices_containing_element = choices_for_top_left_corner * choices_for_bottom_right_corner

      // Add the contribution of A_square_matrix[i][j] from all these submatrices
      total_sum = total_sum + (num_submatrices_containing_element * A_square_matrix[i][j])
    ENDFOR
  ENDFOR

  RETURN total_sum
ENDFUNCTION
```

### Pseudocode for A2.py
```pseudocode
FUNCTION solve(A_matrix, B_r1_coords, C_c1_coords, D_r2_coords, E_c2_coords):
  num_rows = length of A_matrix
  IF num_rows == 0 THEN RETURN empty_list ENDIF
  num_cols = length of A_matrix[0]
  IF num_cols == 0 THEN RETURN empty_list ENDIF

  MOD_CONSTANT = 10^9 + 7

  // --- Initialize and Build 2D Prefix Sum (PS) Matrix ---
  // PS_matrix[r][c] will store sum of submatrix from (0,0) to (r,c) modulo MOD_CONSTANT
  PS_matrix = new 2D array of size num_rows x num_cols, initialized to 0

  // Step 1: Calculate prefix sums for each row independently
  FOR r FROM 0 TO num_rows - 1:
    row_sum_so_far = 0
    FOR c FROM 0 TO num_cols - 1:
      // Ensure A_matrix values are taken modulo MOD_CONSTANT if they can be large
      // Though problem context usually means they are within reasonable integer limits before this step.
      // Python code applies % mod to A[i][j] during this.
      current_val = A_matrix[r][c] % MOD_CONSTANT
      row_sum_so_far = (row_sum_so_far + current_val) % MOD_CONSTANT
      PS_matrix[r][c] = row_sum_so_far
  ENDFOR

  // Step 2: Extend row prefix sums to 2D prefix sums by summing down columns
  FOR c FROM 0 TO num_cols - 1: // For each column
    FOR r FROM 1 TO num_rows - 1: // For each row starting from the second
      PS_matrix[r][c] = (PS_matrix[r-1][c] + PS_matrix[r][c]) % MOD_CONSTANT
  ENDFOR
  // Note: The Python code does this in two separate loops, first row-wise sums into PS,
  // then column-wise sums on PS. The above pseudocode combines this slightly for clarity
  // but achieves the same resulting PS_matrix.
  // Let's match Python's two-pass construction for directness:

  // --- Python-like PS Matrix Construction ---
  // Pass 1: Row-wise prefix sums
  FOR r FROM 0 TO num_rows - 1:
    FOR c FROM 0 TO num_cols - 1:
      val_A = A_matrix[r][c] % MOD_CONSTANT
      IF c == 0 THEN
        PS_matrix[r][c] = val_A
      ELSE
        PS_matrix[r][c] = (PS_matrix[r][c-1] + val_A) % MOD_CONSTANT
      ENDIF
    ENDFOR
  ENDFOR

  // Pass 2: Column-wise prefix sums on the already row-summed PS_matrix
  FOR c FROM 0 TO num_cols - 1: // For each column
    FOR r FROM 1 TO num_rows - 1: // For each row starting from the second
        PS_matrix[r][c] = (PS_matrix[r-1][c] + PS_matrix[r][c]) % MOD_CONSTANT
    ENDFOR
  ENDFOR
  // --- End of PS Matrix Construction ---

  query_results = empty_list
  num_queries = length of B_r1_coords

  FOR q_idx FROM 0 TO num_queries - 1:
    r1 = B_r1_coords[q_idx] - 1  // Convert to 0-indexed
    c1 = C_c1_coords[q_idx] - 1
    r2 = D_r2_coords[q_idx] - 1
    c2 = E_c2_coords[q_idx] - 1

    // Sum of submatrix (r1, c1) to (r2, c2) is:
    // PS(r2,c2) - PS(r1-1,c2) - PS(r2,c1-1) + PS(r1-1,c1-1)
    // All terms must be handled carefully with modulo.

    sum_r2_c2 = PS_matrix[r2][c2]

    sum_r1_minus_1_c2 = 0
    IF r1 > 0 THEN
      sum_r1_minus_1_c2 = PS_matrix[r1-1][c2]
    ENDIF

    sum_r2_c1_minus_1 = 0
    IF c1 > 0 THEN
      sum_r2_c1_minus_1 = PS_matrix[r2][c1-1]
    ENDIF

    sum_r1_minus_1_c1_minus_1 = 0
    IF r1 > 0 AND c1 > 0 THEN
      sum_r1_minus_1_c1_minus_1 = PS_matrix[r1-1][c1-1]
    ENDIF

    current_submatrix_sum = sum_r2_c2
    current_submatrix_sum = (current_submatrix_sum - sum_r1_minus_1_c2 + MOD_CONSTANT) % MOD_CONSTANT
    current_submatrix_sum = (current_submatrix_sum - sum_r2_c1_minus_1 + MOD_CONSTANT) % MOD_CONSTANT
    current_submatrix_sum = (current_submatrix_sum + sum_r1_minus_1_c1_minus_1) % MOD_CONSTANT

    APPEND current_submatrix_sum TO query_results
  ENDFOR

  RETURN query_results
ENDFUNCTION
```

### Pseudocode for A3.py
```pseudocode
FUNCTION solve(A_matrix, B_submatrix_size):
  n = dimension of A_matrix // Assuming A_matrix is square (n x n)

  // Validate inputs for sensible operation
  IF n == 0 OR B_submatrix_size <= 0 OR B_submatrix_size > n THEN
    // Depending on problem constraints, return error, 0, or negative_infinity
    RETURN negative_infinity // Indicates no valid submatrix or error
  ENDIF

  // --- Build 2D Prefix Sum (PS) Matrix ---
  // PS_matrix[r][c] will store the sum of elements in the rectangle from (0,0) to (r,c)
  PS_matrix = new 2D array of size n x n

  // Pass 1: Calculate prefix sums for each row
  FOR r FROM 0 TO n - 1:
    FOR c FROM 0 TO n - 1:
      IF c == 0 THEN
        PS_matrix[r][c] = A_matrix[r][c]
      ELSE
        PS_matrix[r][c] = PS_matrix[r][c-1] + A_matrix[r][c]
      ENDIF
    ENDFOR
  ENDFOR

  // Pass 2: Add prefix sums down each column to complete the 2D prefix sum
  // Python code iterates columns (outer 'i') then rows (inner 'j')
  FOR c_col FROM 0 TO n - 1:
    FOR r_row FROM 1 TO n - 1: // Start from the second row
      PS_matrix[r_row][c_col] = PS_matrix[r_row-1][c_col] + PS_matrix[r_row][c_col]
    ENDFOR
  ENDFOR
  // --- End of PS Matrix Construction ---

  max_found_submatrix_sum = negative_infinity

  // Iterate over all possible top-left corners (r1, c1) of a B_submatrix_size x B_submatrix_size submatrix
  FOR r1 FROM 0 TO n - B_submatrix_size:
    FOR c1 FROM 0 TO n - B_submatrix_size:
      // Define bottom-right corner (r2, c2) of the current submatrix
      r2 = r1 + B_submatrix_size - 1
      c2 = c1 + B_submatrix_size - 1

      // Calculate sum of submatrix from (r1, c1) to (r2, c2) using the PS_matrix
      // Sum(submatrix) = PS[r2][c2] - PS[r1-1][c2] - PS[r2][c1-1] + PS[r1-1][c1-1]
      // Handle boundary conditions for terms being subtracted.

      sum_at_r2_c2 = PS_matrix[r2][c2]

      sum_above_submatrix = 0 // Represents PS[r1-1][c2]
      IF r1 > 0 THEN
        sum_above_submatrix = PS_matrix[r1-1][c2]
      ENDIF

      sum_left_of_submatrix = 0 // Represents PS[r2][c1-1]
      IF c1 > 0 THEN
        sum_left_of_submatrix = PS_matrix[r2][c1-1]
      ENDIF

      sum_top_left_overlap = 0 // Represents PS[r1-1][c1-1]
      IF r1 > 0 AND c1 > 0 THEN
        sum_top_left_overlap = PS_matrix[r1-1][c1-1]
      ENDIF

      current_submatrix_sum = sum_at_r2_c2 - sum_above_submatrix - sum_left_of_submatrix + sum_top_left_overlap

      IF current_submatrix_sum > max_found_submatrix_sum THEN
        max_found_submatrix_sum = current_submatrix_sum
      ENDIF
    ENDFOR
  ENDFOR

  RETURN max_found_submatrix_sum
ENDFUNCTION
```

### Pseudocode for A4.py
```pseudocode
FUNCTION solve(A_matrix, B_target_value):
  num_rows = length of A_matrix
  IF num_rows == 0 THEN
    RETURN -1 // Target cannot be in an empty matrix
  ENDIF
  num_cols = length of A_matrix[0]
  IF num_cols == 0 THEN
    RETURN -1 // Target cannot be in a matrix with no columns
  ENDIF

  // Start search from the top-right corner of the matrix
  current_r = 0      // Current row index
  current_c = num_cols - 1 // Current column index

  WHILE current_r < num_rows AND current_c >= 0:
    matrix_element = A_matrix[current_r][current_c]

    IF matrix_element == B_target_value THEN
      // Target value found. Now find the leftmost occurrence in the current row.
      // The problem asks for an encoded value of the first occurrence (smallest column index).
      leftmost_c_in_row = current_c
      WHILE leftmost_c_in_row >= 0 AND A_matrix[current_r][leftmost_c_in_row] == B_target_value:
        leftmost_c_in_row = leftmost_c_in_row - 1
      ENDFOR

      // After the loop, `leftmost_c_in_row` is the index to the left of the actual leftmost 'B_target_value'.
      // So, `leftmost_c_in_row + 1` is the 0-indexed column of the first occurrence.
      actual_col_0_indexed = leftmost_c_in_row + 1

      // The result is encoded as 1009 * (row_1_based) + (col_1_based).
      // Python: 1009*(i+1) + (j+1+1) where i is current_r, and j is `leftmost_c_in_row`.
      // (j+1+1) means ( (leftmost_c_in_row + 1)_as_0_indexed_col + 1 ) which is col_1_based.
      row_1_based = current_r + 1
      col_1_based = actual_col_0_indexed + 1

      RETURN (1009 * row_1_based) + col_1_based

    ELSE IF matrix_element < B_target_value THEN
      // If the current element is less than target, target cannot be in this column (above) or to its left.
      // Move to the next row.
      current_r = current_r + 1
    ELSE // matrix_element > B_target_value
      // If the current element is greater than target, target cannot be in this row (to its right) or below.
      // Move to the previous column.
      current_c = current_c - 1
    ENDIF
  ENDFOR

  RETURN -1 // Target value not found
ENDFUNCTION
```

### Pseudocode for HW3.py
```pseudocode
FUNCTION solve(A_permutation_list):
  n = length of A_permutation_list
  // It's assumed that A_permutation_list contains a permutation of integers from 0 to n-1.

  number_of_swaps = 0

  // Iterate through each index of the list
  FOR current_index FROM 0 TO n - 1:
    // While the element at current_index is not what it should be
    // (i.e., A_permutation_list[current_index] should be equal to current_index in a sorted state)
    WHILE A_permutation_list[current_index] != current_index:
      // Let value_at_current_index be the element that is currently at current_index.
      // This value_at_current_index rightfully belongs at index value_at_current_index.
      value_at_current_index = A_permutation_list[current_index]

      // Store the element that is currently sitting at value_at_current_index's correct spot.
      // This element will be moved to current_index.
      element_to_move_to_current_index = A_permutation_list[value_at_current_index]

      // Place value_at_current_index into its correct position
      A_permutation_list[value_at_current_index] = value_at_current_index

      // Place the displaced element (element_to_move_to_current_index) into current_index
      A_permutation_list[current_index] = element_to_move_to_current_index

      // Increment swap counter for this operation
      number_of_swaps = number_of_swaps + 1
    ENDWHILE
    // At the end of this WHILE loop, the element at A_permutation_list[current_index] is `current_index`.
  ENDFOR

  RETURN number_of_swaps
ENDFUNCTION
```

### Pseudocode for HW4.py
```pseudocode
FUNCTION generateMatrix(A_side_length):
  IF A_side_length == 0 THEN
    RETURN empty_matrix // Or handle as per specific requirements for A=0
  ENDIF

  // Initialize an A_side_length x A_side_length matrix with zeros
  matrix = new 2D array of size A_side_length x A_side_length, all elements set to 0

  current_number_to_place = 1
  total_elements = A_side_length * A_side_length

  row_idx = 0
  col_idx = 0

  // Initial state: moving right. Flags indicate the current direction of movement.
  is_direction_right = true
  is_direction_down = false
  is_direction_left = false
  is_direction_up = false

  // Place the first number
  matrix[row_idx][col_idx] = current_number_to_place
  current_number_to_place = current_number_to_place + 1

  WHILE current_number_to_place <= total_elements:
    IF is_direction_right THEN
      col_idx = col_idx + 1 // Tentatively move to next cell in current direction
      // Fill cells moving right as long as within bounds and cell is empty
      WHILE col_idx < A_side_length AND matrix[row_idx][col_idx] == 0:
        matrix[row_idx][col_idx] = current_number_to_place
        current_number_to_place = current_number_to_place + 1
        IF current_number_to_place > total_elements THEN BREAK WHILE ENDIF // All numbers placed
        col_idx = col_idx + 1
      ENDFOR
      // Correct position after overshooting or hitting boundary/filled cell
      col_idx = col_idx - 1
      // Change direction
      is_direction_right = false
      is_direction_down = true

    ELSE IF is_direction_down THEN
      row_idx = row_idx + 1 // Tentatively move
      WHILE row_idx < A_side_length AND matrix[row_idx][col_idx] == 0:
        matrix[row_idx][col_idx] = current_number_to_place
        current_number_to_place = current_number_to_place + 1
        IF current_number_to_place > total_elements THEN BREAK WHILE ENDIF
        row_idx = row_idx + 1
      ENDFOR
      row_idx = row_idx - 1
      is_direction_down = false
      is_direction_left = true

    ELSE IF is_direction_left THEN
      col_idx = col_idx - 1 // Tentatively move
      WHILE col_idx >= 0 AND matrix[row_idx][col_idx] == 0:
        matrix[row_idx][col_idx] = current_number_to_place
        current_number_to_place = current_number_to_place + 1
        IF current_number_to_place > total_elements THEN BREAK WHILE ENDIF
        col_idx = col_idx - 1
      ENDFOR
      col_idx = col_idx + 1
      is_direction_left = false
      is_direction_up = true

    ELSE IF is_direction_up THEN
      row_idx = row_idx - 1 // Tentatively move
      WHILE row_idx >= 0 AND matrix[row_idx][col_idx] == 0:
        matrix[row_idx][col_idx] = current_number_to_place
        current_number_to_place = current_number_to_place + 1
        IF current_number_to_place > total_elements THEN BREAK WHILE ENDIF
        row_idx = row_idx - 1
      ENDFOR
      row_idx = row_idx + 1
      is_direction_up = false
      is_direction_right = true
    ENDIF

    // Ensure loop terminates if all numbers are placed (already handled by BREAKs inside)
    IF current_number_to_place > total_elements THEN BREAK WHILE ENDIF
  ENDWHILE

  RETURN matrix
ENDFUNCTION
```

### Pseudocode for HW2.py
```pseudocode
FUNCTION solve(A_list, B_threshold):
  n = length of A_list

  // Step 1: Count the total number of elements in A_list that are <= B_threshold.
  // This count determines the size of the sliding window.
  num_good_elements = 0
  FOR EACH element IN A_list:
    IF element <= B_threshold THEN
      num_good_elements = num_good_elements + 1
    ENDIF
  ENDFOR

  // If there are no "good" elements, or all elements are "good" (i.e., num_good_elements is 0 or n),
  // then no swaps are needed. The formula at the end will correctly result in 0.
  // The Python code doesn't have an explicit early exit for this, but it's a valid observation.
  // For example, if num_good_elements = 0, max_good_in_window will be 0, result 0 - 0 = 0.

  // Step 2: Use a sliding window of size `num_good_elements` to find the
  // window containing the maximum number of "good" elements.

  window_start = 0
  max_good_in_any_window = 0    // Max count of "good" elements found in any window
  current_good_in_window = 0 // Count of "good" elements in the current window

  FOR window_end FROM 0 TO n - 1:
    // Add the element entering the window from the right
    IF A_list[window_end] <= B_threshold THEN
      current_good_in_window = current_good_in_window + 1
    ENDIF

    // Check if the current window has reached the target size `num_good_elements`
    current_window_length = window_end - window_start + 1
    IF current_window_length == num_good_elements THEN
      // Update the overall maximum count of "good" elements found in a window
      IF current_good_in_window > max_good_in_any_window THEN
        max_good_in_any_window = current_good_in_window
      ENDIF

      // Slide the window: remove the element leaving from the left
      IF A_list[window_start] <= B_threshold THEN
        current_good_in_window = current_good_in_window - 1
      ENDIF
      window_start = window_start + 1 // Move window start to the right
    ENDIF
  ENDFOR

  // The minimum number of swaps required is the total number of "good" elements
  // minus the maximum number of "good" elements that can be found in any
  // contiguous subsegment of length `num_good_elements`.
  // Each "missing" good element from that optimal segment needs one swap.
  RETURN num_good_elements - max_good_in_any_window
ENDFUNCTION
```

### Pseudocode for HW1.py
```pseudocode
FUNCTION maximumGap(A_list):
  n = length of A_list
  IF n < 2 THEN // A gap requires at least two elements
    RETURN 0
  ENDIF

  // Step 1: Create a list of pairs, pairing each element with its original index
  elements_with_indices = empty list
  FOR i FROM 0 TO n - 1:
    APPEND (value: A_list[i], original_index: i) TO elements_with_indices
  ENDFOR

  // Step 2: Sort this list of pairs based on the element values
  // If values are equal, the relative order of original indices is maintained (stable sort)
  // or does not strictly matter for this algorithm's variant.
  SORT elements_with_indices ASCENDING based on 'value'

  // Step 3: Iterate through the sorted list to find the maximum gap (j - i)
  // such that A[i] <= A[j]. After sorting, for any pair elements_with_indices[k] (value_k, index_k)
  // and elements_with_indices[l] (value_l, index_l) where l < k, we have value_l <= value_k.
  // We want to maximize (index_k - index_l).
  // So, for each index_k, we need the minimum index_l encountered so far (for l <= k).

  max_calculated_gap = 0

  // Initialize with the original index of the element that is smallest (first in sorted list)
  min_original_idx_encountered_so_far = elements_with_indices[0].original_index

  // Iterate through the sorted list (Python code includes the 0th element in loop)
  FOR k FROM 0 TO n - 1:
    current_element_original_idx = elements_with_indices[k].original_index

    // Calculate the gap with the minimum original index found so far from elements
    // that are less than or equal to the current element (due to sorting).
    gap = current_element_original_idx - min_original_idx_encountered_so_far

    IF gap > max_calculated_gap THEN
      max_calculated_gap = gap
    ENDIF

    // Update the minimum original index encountered so far in the sorted list.
    IF current_element_original_idx < min_original_idx_encountered_so_far THEN
      min_original_idx_encountered_so_far = current_element_original_idx
    ENDIF
  ENDFOR

  RETURN max_calculated_gap
ENDFUNCTION
```
