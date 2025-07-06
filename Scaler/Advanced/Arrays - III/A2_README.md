# Pseudocode for A2.py

```pseudocode
CLASS Solution:
  METHOD solve(self, A_matrix, B_r1_list, C_c1_list, D_r2_list, E_c2_list):
    // A_matrix: The input 2D matrix.
    // B_r1_list, C_c1_list: Lists of 1-based top-left (r1, c1) coordinates for queries.
    // D_r2_list, E_c2_list: Lists of 1-based bottom-right (r2, c2) coordinates for queries.

    num_rows = length of A_matrix
    IF num_rows == 0 THEN RETURN empty_list ENDIF // Handle empty matrix
    num_cols = length of A_matrix[0]
    IF num_cols == 0 THEN RETURN empty_list ENDIF // Handle matrix with empty rows

    MOD_VALUE = 10^9 + 7 // Modulo constant for all sums.

    // --- Step 1: Construct the 2D Prefix Sum (PS) matrix ---
    // PS_matrix[i][j] will store the sum of all elements in the rectangle
    // from A_matrix[0][0] to A_matrix[i][j], modulo MOD_VALUE.
    PS_matrix = new 2D array of size num_rows x num_cols, initialized to 0

    // Part 1.1: Calculate row-wise prefix sums.
    // After this, PS_matrix[r][c] = sum(A_matrix[r][0...c]) % MOD_VALUE.
    FOR r FROM 0 TO num_rows - 1:
      FOR c FROM 0 TO num_cols - 1:
        element_val_mod = A_matrix[r][c] % MOD_VALUE
        IF c == 0 THEN // First element in the row
          PS_matrix[r][c] = element_val_mod
        ELSE
          PS_matrix[r][c] = (PS_matrix[r][c-1] + element_val_mod) % MOD_VALUE
        ENDIF
      ENDFOR
    ENDFOR

    // Part 1.2: Extend to full 2D prefix sums by summing column-wise.
    // After this, PS_matrix[r][c] = sum of rectangle (0,0) to (r,c) % MOD_VALUE.
    // Python code iterates columns first (outer loop `i`), then rows (inner loop `j`).
    FOR c FROM 0 TO num_cols - 1:      // Python's `i` (column index)
      FOR r FROM 0 TO num_rows - 1:  // Python's `j` (row index)
        IF r == 0 THEN
          // For the first row (r=0), PS_matrix[0][c] already holds the correct 2D prefix sum
          // (as sum from (0,0) to (0,c) is just the row sum up to (0,c)).
          // Python code `pass` effectively means no change here.
        ELSE
          // For other rows, add the prefix sum from the cell directly above
          // to the current cell's row-prefix-sum.
          PS_matrix[r][c] = (PS_matrix[r-1][c] + PS_matrix[r][c]) % MOD_VALUE
        ENDIF
      ENDFOR
    ENDFOR
    // --- End of PS_matrix construction ---

    results_for_queries = empty list
    num_queries = length of B_r1_list // Assuming all coordinate lists B,C,D,E are of the same length.

    // --- Step 2: Process each query ---
    FOR query_index FROM 0 TO num_queries - 1:
      // Get 1-based query coordinates and convert them to 0-based indices.
      r1 = B_r1_list[query_index] - 1
      c1 = C_c1_list[query_index] - 1
      r2 = D_r2_list[query_index] - 1
      c2 = E_c2_list[query_index] - 1

      // Calculate sum of the submatrix defined by (r1,c1) and (r2,c2) using the PS_matrix.
      // Sum(submatrix) = PS[r2][c2] - PS[r1-1][c2] - PS[r2][c1-1] + PS[r1-1][c1-1]
      // All subtractions must handle potential negative results before modulo using `+ MOD_VALUE`.

      sum_total_rect_to_r2_c2 = PS_matrix[r2][c2]

      sum_rect_above_submatrix = 0 // Represents PS[r1-1][c2]
      IF r1 > 0 THEN
        sum_rect_above_submatrix = PS_matrix[r1-1][c2]
      ENDIF

      sum_rect_left_of_submatrix = 0 // Represents PS[r2][c1-1]
      IF c1 > 0 THEN
        sum_rect_left_of_submatrix = PS_matrix[r2][c1-1]
      ENDIF

      sum_rect_top_left_overlap = 0 // Represents PS[r1-1][c1-1]
      IF r1 > 0 AND c1 > 0 THEN
        sum_rect_top_left_overlap = PS_matrix[r1-1][c1-1]
      ENDIF

      // Calculate the submatrix sum with modulo arithmetic at each step.
      current_query_sum = sum_total_rect_to_r2_c2
      current_query_sum = (current_query_sum - sum_rect_above_submatrix + MOD_VALUE) % MOD_VALUE
      current_query_sum = (current_query_sum - sum_rect_left_of_submatrix + MOD_VALUE) % MOD_VALUE
      current_query_sum = (current_query_sum + sum_rect_top_left_overlap) % MOD_VALUE

      APPEND current_query_sum TO results_for_queries
    ENDFOR

    RETURN results_for_queries
  ENDMETHOD
ENDCLASS

// Example Usage:
IF SCRIPT_IS_RUN_DIRECTLY THEN
  solution_object = new Solution()
  // Test with A = [[1, 2, 3], [4, 5, 6], [7, 8, 9]], B = [1, 2], C = [1, 2], D = [2, 3], E = [2, 3]
  // Expected output would be based on sums of submatrices.
ENDIF
```
