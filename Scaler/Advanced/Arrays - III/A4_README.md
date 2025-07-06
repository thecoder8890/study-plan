# Pseudocode for A4.py

```pseudocode
CLASS Solution:
  METHOD solve(self, A_matrix, B_target_value):
    // A_matrix: A 2D list where each row is sorted from left to right,
    //           and each column is sorted from top to bottom.
    // B_target_value: The integer value to search for in the matrix.

    num_rows = length of A_matrix
    IF num_rows == 0 THEN
      RETURN -1 // Target cannot exist in an empty matrix.
    ENDIF
    num_cols = length of A_matrix[0]
    IF num_cols == 0 THEN
      RETURN -1 // Target cannot exist in a matrix with no columns.
    ENDIF

    // Strategy: Start searching from the top-right corner of the matrix.
    // `current_row_idx` (0-based) starts at the first row.
    // `current_col_idx` (0-based) starts at the last column.
    current_row_idx = 0
    current_col_idx = num_cols - 1

    // Continue the search as long as the current indices are within the matrix boundaries.
    WHILE current_row_idx < num_rows AND current_col_idx >= 0:
      element_at_current_position = A_matrix[current_row_idx][current_col_idx]

      IF element_at_current_position == B_target_value THEN
        // Target value found at (current_row_idx, current_col_idx).
        // The problem requires the lexicographically smallest pair (row, col).
        // Since we might find the target multiple times, and this search pattern
        // doesn't guarantee finding the top-most, left-most first,
        // the Python code, upon finding B_target_value, specifically searches
        // to the left in the current_row_idx to find the smallest column index.

        leftmost_col_in_this_row_0_idx = current_col_idx
        // Move left in the current row as long as the element is still B_target_value.
        WHILE leftmost_col_in_this_row_0_idx >= 0 AND A_matrix[current_row_idx][leftmost_col_in_this_row_0_idx] == B_target_value:
          leftmost_col_in_this_row_0_idx = leftmost_col_in_this_row_0_idx - 1
        ENDFOR

        // After the loop, `leftmost_col_in_this_row_0_idx` is one position to the left
        // of the actual smallest column index where B_target_value was found.
        actual_smallest_col_0_idx = leftmost_col_in_this_row_0_idx + 1

        // The result is an encoded value: 1009 * (1-based row) + (1-based column).
        row_1_based = current_row_idx + 1
        col_1_based = actual_smallest_col_0_idx + 1

        RETURN (1009 * row_1_based) + col_1_based

      ELSE IF element_at_current_position < B_target_value THEN
        // If the current element is smaller than the target value:
        // Since the current row is sorted, all elements to the left of `current_col_idx`
        // in this `current_row_idx` will also be smaller or equal.
        // The target, if it exists, must be in a row below (for larger values).
        // So, move to the next row.
        current_row_idx = current_row_idx + 1
      ELSE // element_at_current_position > B_target_value
        // If the current element is larger than the target value:
        // Since the current column is sorted, all elements below `current_row_idx`
        // in this `current_col_idx` will also be larger or equal.
        // The target, if it exists, must be in a column to the left (for smaller values).
        // So, move to the previous column.
        current_col_idx = current_col_idx - 1
      ENDIF
    ENDWHILE

    // If the loop terminates, it means `current_row_idx` went out of bounds (bottom)
    // or `current_col_idx` went out of bounds (left), and the target was not found.
    RETURN -1
  ENDMETHOD
ENDCLASS

// Example Usage:
IF SCRIPT_IS_RUN_DIRECTLY THEN
  solution_object = new Solution()
  // A_matrix_example = [[1, 2, 8, 8, 8], [2, 3, 9, 9, 9], [4, 5, 10, 10, 10]]
  // B_target = 8
  // PRINT solution_object.solve(A_matrix_example, B_target) // Expected: 1009*(1) + (3) = 1012
ENDIF
```
