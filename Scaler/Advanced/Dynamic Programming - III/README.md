# Advanced/Dynamic Programming - III

## Files in this topic:

- [Dynamic_programming_3..pdf](Dynamic_programming_3..pdf)
- [Grid Unique Paths II.py](Grid Unique Paths II.py)
- [Maximum Path in Triangle.py](Maximum Path in Triangle.py)
- [Min Sum Path in Matrix.py](Min Sum Path in Matrix.py)
- [Min Sum Path in Triangle.py](Min Sum Path in Triangle.py)
- [Unique Paths in a Grid.py](Unique Paths in a Grid.py)

## Description

This section further advances the study of Dynamic Programming (DP), likely tackling more intricate problems, multi-dimensional DP, state-space reductions, or DP combined with other algorithmic paradigms.

### Pseudocode for Grid Unique Paths II.py
```pseudocode
// This solution uses recursion with memoization (Dynamic Programming approach).
// A global or class-level cache (e.g., a 2D array or dictionary) is assumed for memoization,
// similar to what @functools.lru_cache provides in Python.
// Let's call this `memo_cache`.

FUNCTION solve_unique_paths_with_obstacles(A_grid_matrix):
  num_rows = length of A_grid_matrix
  IF num_rows == 0 THEN RETURN 0 ENDIF
  num_cols = length of A_grid_matrix[0]
  IF num_cols == 0 THEN RETURN 0 ENDIF

  MODULO_VALUE = 10^9 + 7 // For the final result, as per typical problem constraints.

  // Initialize memo_cache here if not handled globally (e.g., fill with -1 or a "not computed" marker)
  // For example: memo_cache = create_2D_array(num_rows, num_cols, initial_value = -1)

  // Call the recursive helper to find paths to the bottom-right cell.
  // The Python code uses (m-1, n-1) for an m x n grid.
  number_of_paths = find_paths_recursive(num_rows - 1, num_cols - 1, A_grid_matrix, memo_cache)

  RETURN number_of_paths % MODULO_VALUE // Python applies modulo only at the end.
ENDFUNCTION


// Recursive helper function to find paths from (0,0) to (target_row, target_col)
DEFINE FUNCTION find_paths_recursive(target_row, target_col, grid, memo_cache):
  // Base Case 1: Out of grid boundaries (invalid path)
  IF target_row < 0 OR target_col < 0 THEN
    RETURN 0
  ENDIF

  // Base Case 2: Current cell is an obstacle
  IF grid[target_row][target_col] == 1 THEN
    RETURN 0 // No paths can go through an obstacle
  ENDIF

  // Base Case 3: Reached the starting cell (0,0)
  // If grid[0][0] was 1 (obstacle), it would have been caught by Base Case 2.
  // So, if we reach here, (0,0) is a valid start.
  IF target_row == 0 AND target_col == 0 THEN
    RETURN 1 // One way to reach the start (by being there)
  ENDIF

  // Check memoization cache: If result for (target_row, target_col) is already computed, return it.
  // IF memo_cache[target_row][target_col] != -1 (or "not computed") THEN
  //   RETURN memo_cache[target_row][target_col]
  // ENDIF
  // (This check is automatically handled by @functools.lru_cache in Python)

  // Recursive Step: Number of paths to (target_row, target_col) is the sum of:
  // 1. Paths from the cell above: (target_row - 1, target_col)
  // 2. Paths from the cell to the left: (target_row, target_col - 1)

  paths_from_above = find_paths_recursive(target_row - 1, target_col, grid, memo_cache)
  paths_from_left = find_paths_recursive(target_row, target_col - 1, grid, memo_cache)

  total_paths = paths_from_above + paths_from_left
  // Note: Python's integers handle arbitrary size, so intermediate `total_paths` won't overflow.
  // In languages with fixed-size integers, modulo might be needed here if sums are large:
  // total_paths = (paths_from_above % MODULO_VALUE + paths_from_left % MODULO_VALUE) % MODULO_VALUE

  // Store the computed result in the cache before returning.
  // memo_cache[target_row][target_col] = total_paths

  RETURN total_paths
ENDFUNCTION
```

### Pseudocode for Maximum Path in Triangle.py
```pseudocode
// This solution uses recursion with memoization (Dynamic Programming).
// A cache (e.g., 2D array or map) `memo_cache` is assumed for storing results of subproblems.

FUNCTION solve_max_path_sum_in_triangle(A_triangle_grid):
  num_total_rows = length of A_triangle_grid
  IF num_total_rows == 0 THEN
    RETURN 0 // Or handle as an error for an empty triangle
  ENDIF

  // Initialize memo_cache, e.g., with a special value like NULL or NEGATIVE_INFINITY
  // to indicate that a state (r,c) has not been computed yet.
  // memo_cache = create_cache_for_triangle(num_total_rows)

  // Start the recursive calculation from the top of the triangle (row 0, col 0).
  max_sum = calculate_max_path_recursive(0, 0, A_triangle_grid, num_total_rows, memo_cache)

  RETURN max_sum
ENDFUNCTION


// Recursive helper function to calculate the maximum path sum from cell (r, c) to the last row.
DEFINE FUNCTION calculate_max_path_recursive(r_current_row, c_current_col,
                                           triangle_grid_data, total_num_rows, memo_cache):

  // Base Case 1: Out of bounds (row index too large)
  IF r_current_row >= total_num_rows THEN
    RETURN negative_infinity // This path is invalid and should not contribute to a max sum.
  ENDIF

  // Base Case 2: Out of bounds (column index invalid for the current row)
  // In a triangle, row `r` has `r+1` elements (cols 0 to `r`).
  // So, `c_current_col` should be `<= r_current_row`.
  // The Python code uses `c >= len(A[r])`. If `len(A[r]) = r+1`, then `c >= r+1` is out of bounds.
  IF c_current_col >= length of triangle_grid_data[r_current_row] OR c_current_col < 0 THEN
    RETURN negative_infinity // Invalid column for this row.
  ENDIF

  // Base Case 3: Reached the last row of the triangle.
  // The path ends here, so the sum from this cell is simply its own value.
  IF r_current_row == total_num_rows - 1 THEN
    RETURN triangle_grid_data[r_current_row][c_current_col]
  ENDIF

  // Check memoization cache: If the result for state (r_current_row, c_current_col) is already computed, return it.
  // IF memo_cache contains result for (r_current_row, c_current_col) THEN
  //   RETURN memo_cache[r_current_row][c_current_col]
  // ENDIF
  // (This part is handled by @functools.lru_cache in Python)

  // Recursive Step: Calculate the maximum path sum by choosing the better of two possible moves:
  // 1. Move down to cell (r_current_row + 1, c_current_col).
  // 2. Move diagonally down-right to cell (r_current_row + 1, c_current_col + 1).

  max_sum_from_down_path = calculate_max_path_recursive(r_current_row + 1, c_current_col,
                                                       triangle_grid_data, total_num_rows, memo_cache)
  max_sum_from_diagonal_path = calculate_max_path_recursive(r_current_row + 1, c_current_col + 1,
                                                           triangle_grid_data, total_num_rows, memo_cache)

  // The maximum sum from the current cell (r_current_row, c_current_col) is its own value
  // plus the maximum of the sums obtained from the two possible next moves.
  result_for_current_cell = triangle_grid_data[r_current_row][c_current_col] + maximum(max_sum_from_down_path, max_sum_from_diagonal_path)

  // Store the computed result in the cache before returning.
  // memo_cache[r_current_row][c_current_col] = result_for_current_cell

  RETURN result_for_current_cell
ENDFUNCTION
```

### Pseudocode for Unique Paths in a Grid.py
```pseudocode
// This solution uses recursion with memoization (Dynamic Programming approach).
// A cache (e.g., 2D array or dictionary) `memo_cache` is assumed for storing results of subproblems.
// This logic is identical to `Grid Unique Paths II.py` except for the final modulo.

FUNCTION uniquePathsWithObstacles(A_grid_matrix):
  num_rows = length of A_grid_matrix
  IF num_rows == 0 THEN RETURN 0 ENDIF
  num_cols = length of A_grid_matrix[0]
  IF num_cols == 0 THEN RETURN 0 ENDIF

  // Initialize memo_cache (e.g., fill with -1 or a "not computed" marker)
  // For example: memo_cache = create_2D_array(num_rows, num_cols, initial_value = -1)

  // Call the recursive helper to find paths to the bottom-right cell.
  number_of_paths = find_paths_recursive_variant(num_rows - 1, num_cols - 1, A_grid_matrix, memo_cache)

  RETURN number_of_paths // No modulo applied in this version of the function
ENDFUNCTION


// Recursive helper function to find paths from (0,0) to (target_row, target_col)
DEFINE FUNCTION find_paths_recursive_variant(target_row, target_col, grid, memo_cache):
  // Base Case 1: Out of grid boundaries (invalid path)
  IF target_row < 0 OR target_col < 0 THEN
    RETURN 0
  ENDIF

  // Base Case 2: Current cell is an obstacle
  IF grid[target_row][target_col] == 1 THEN
    RETURN 0 // No paths can go through an obstacle
  ENDIF

  // Base Case 3: Reached the starting cell (0,0)
  IF target_row == 0 AND target_col == 0 THEN
    // If grid[0][0] was 1, already caught by Base Case 2.
    RETURN 1 // One way to reach the start
  ENDIF

  // Check memoization cache
  // IF memo_cache[target_row][target_col] IS "computed" THEN
  //   RETURN memo_cache[target_row][target_col]
  // ENDIF
  // (@functools.lru_cache handles this in Python)

  // Recursive Step:
  paths_from_top_cell = find_paths_recursive_variant(target_row - 1, target_col, grid, memo_cache)
  paths_from_left_cell = find_paths_recursive_variant(target_row, target_col - 1, grid, memo_cache)

  total_paths_to_current = paths_from_top_cell + paths_from_left_cell

  // Store result in cache
  // memo_cache[target_row][target_col] = total_paths_to_current

  RETURN total_paths_to_current
ENDFUNCTION
```

### Pseudocode for Min Sum Path in Matrix.py
```pseudocode
// This pseudocode describes the bottom-up DP approach (`minPathSum` method).
// The file also contains a top-down memoized recursive approach (`top_down`).

FUNCTION minPathSum_bottom_up(A_cost_grid):
  num_rows = length of A_cost_grid
  IF num_rows == 0 THEN
    RETURN 0 // Or appropriate value for an empty grid
  ENDIF
  num_cols = length of A_cost_grid[0]
  IF num_cols == 0 THEN
    RETURN 0 // Or appropriate value for a grid with no columns
  ENDIF

  // dp_sum_table[r][c] will store the minimum sum of a path
  // from the top-left cell (0,0) to cell (r,c).
  dp_sum_table = new 2D array of size num_rows x num_cols

  // Initialize the starting cell (0,0)
  dp_sum_table[0][0] = A_cost_grid[0][0]

  // Initialize the first row:
  // To reach any cell (0,c) in the first row, the only way is from (0,c-1).
  FOR c_col FROM 1 TO num_cols - 1:
    dp_sum_table[0][c_col] = dp_sum_table[0][c_col-1] + A_cost_grid[0][c_col]
  ENDFOR

  // Initialize the first column:
  // To reach any cell (r,0) in the first column, the only way is from (r-1,0).
  FOR r_row FROM 1 TO num_rows - 1:
    dp_sum_table[r_row][0] = dp_sum_table[r_row-1][0] + A_cost_grid[r_row][0]
  ENDFOR

  // Fill the rest of the dp_sum_table.
  // For any cell (r,c), the path to it must come either from the cell above (r-1,c)
  // or from the cell to its left (r,c-1). We choose the path with the minimum sum.
  FOR r_row FROM 1 TO num_rows - 1:
    FOR c_col FROM 1 TO num_cols - 1:
      min_sum_from_top = dp_sum_table[r_row-1][c_col]
      min_sum_from_left = dp_sum_table[r_row][c_col-1]

      dp_sum_table[r_row][c_col] = minimum(min_sum_from_top, min_sum_from_left) + A_cost_grid[r_row][c_col]
    ENDFOR
  ENDFOR

  // The value in the bottom-right cell of dp_sum_table is the minimum path sum
  // from (0,0) to (num_rows-1, num_cols-1).
  RETURN dp_sum_table[num_rows - 1][num_cols - 1]
ENDFUNCTION
```

### Pseudocode for Min Sum Path in Triangle.py
```pseudocode
// This solution uses recursion with memoization (Dynamic Programming).
// A cache (e.g., 2D array or map) `memo_cache` is assumed for storing results of subproblems.

FUNCTION solve_min_path_sum_in_triangle(A_triangle_grid):
  num_total_rows = length of A_triangle_grid
  IF num_total_rows == 0 THEN
    RETURN 0 // Or handle as an error for an empty triangle
  ENDIF

  // Initialize memo_cache, e.g., with a special value like NULL or a very large number
  // to indicate that a state (r,c) has not been computed yet.
  // memo_cache = create_cache_for_triangle(num_total_rows)

  // Start the recursive calculation from the top of the triangle (row 0, col 0).
  min_sum = calculate_min_path_recursive(0, 0, A_triangle_grid, num_total_rows, memo_cache)

  // If min_sum is positive_infinity, it means no valid path (e.g., disconnected triangle, though unlikely for this problem).
  IF min_sum == positive_infinity THEN
    RETURN 0 // Or appropriate error based on constraints.
  ELSE
    RETURN min_sum
  ENDIF
ENDFUNCTION


// Recursive helper function to calculate the minimum path sum from cell (r, c) to the last row.
DEFINE FUNCTION calculate_min_path_recursive(r_current_row, c_current_col,
                                           triangle_grid_data, total_num_rows, memo_cache):

  // Base Case 1: Out of bounds (row index too large)
  IF r_current_row >= total_num_rows THEN
    RETURN positive_infinity // This path is invalid and should not contribute to a min sum.
  ENDIF

  // Base Case 2: Out of bounds (column index invalid for the current row)
  // In a triangle, row `r` has `r+1` elements (cols 0 to `r`).
  // Python code: `c >= len(A[r])`. If `len(A[r]) = r+1`, then `c >= r+1` is out of bounds.
  IF c_current_col >= length of triangle_grid_data[r_current_row] OR c_current_col < 0 THEN
    RETURN positive_infinity // Invalid column for this row.
  ENDIF

  // Base Case 3: Reached the last row of the triangle.
  // The path ends here; the sum from this cell is simply its own value.
  IF r_current_row == total_num_rows - 1 THEN
    RETURN triangle_grid_data[r_current_row][c_current_col]
  ENDIF

  // Check memoization cache: If the result for state (r_current_row, c_current_col) is already computed, return it.
  // IF memo_cache contains result for (r_current_row, c_current_col) THEN
  //   RETURN memo_cache[r_current_row][c_current_col]
  // ENDIF
  // (This part is handled by @functools.lru_cache in Python)

  // Recursive Step: Calculate the minimum path sum by choosing the better of two possible moves:
  // 1. Move down to cell (r_current_row + 1, c_current_col).
  // 2. Move diagonally down-right to cell (r_current_row + 1, c_current_col + 1).

  min_sum_from_down_path = calculate_min_path_recursive(r_current_row + 1, c_current_col,
                                                       triangle_grid_data, total_num_rows, memo_cache)
  min_sum_from_diagonal_path = calculate_min_path_recursive(r_current_row + 1, c_current_col + 1,
                                                           triangle_grid_data, total_num_rows, memo_cache)

  // The minimum sum from the current cell (r_current_row, c_current_col) is its own value
  // plus the minimum of the sums obtained from the two possible next moves.
  result_for_current_cell = triangle_grid_data[r_current_row][c_current_col] + minimum(min_sum_from_down_path, min_sum_from_diagonal_path)

  // Store the computed result in the cache before returning.
  // memo_cache[r_current_row][c_current_col] = result_for_current_cell

  RETURN result_for_current_cell
ENDFUNCTION
```
