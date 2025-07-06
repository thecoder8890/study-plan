# Advanced/Backtracking - II

## Files in this topic:

- [Rat In a Maze.py](Rat In a Maze.py)

## Description

This section likely continues with more advanced or complex problems solvable using backtracking techniques, building upon the foundational concepts from Backtracking - I.

### Pseudocode for Rat In a Maze.py
```pseudocode
FUNCTION solve(A_maze_grid):
  n_dimension = length of A_maze_grid // Assuming a square maze
  IF n_dimension == 0 THEN
    RETURN empty_matrix // Or handle as per specific problem constraints
  ENDIF

  // path_matrix will store the solution path; initialized with all 0s.
  path_matrix = new 2D array of size n_dimension x n_dimension, all elements set to 0

  // Define a recursive helper function (backtracking).
  // It attempts to find a path from (current_row, current_col) to the destination.
  // Returns true if a path is found, false otherwise.
  DEFINE FUNCTION find_path_recursive(current_row, current_col):
    // Base Case: If the rat reaches the destination (bottom-right corner).
    // The Python code marks the destination and returns true without checking if A_maze_grid[dest_row][dest_col] is 1.
    // This assumes if destination is reachable, it's a valid path cell.
    IF current_row == n_dimension - 1 AND current_col == n_dimension - 1 THEN
      IF A_maze_grid[current_row][current_col] == 1 THEN // Check if destination itself is open
          path_matrix[current_row][current_col] = 1 // Mark as part of the path
          RETURN true
      ELSE
          RETURN false // Destination is blocked
      ENDIF
    ENDIF

    // Check if the current cell (current_row, current_col) is a valid position to explore:
    // 1. Within matrix boundaries.
    // 2. The cell in A_maze_grid is open (value is 1).
    // 3. Not already visited in the current path exploration (Python code handles this by setting path_matrix[curr][curr] = 1
    //    and then if a recursive call tries to re-enter, A_maze_grid condition might still be 1 but path_matrix[curr][curr] is already 1.
    //    The Python code structure: if valid_and_open: mark path_matrix; try moves; if fail, unmark.

    is_valid_and_open_cell = false
    IF current_row >= 0 AND current_row < n_dimension AND current_col >= 0 AND current_col < n_dimension THEN
      IF A_maze_grid[current_row][current_col] == 1 THEN
        is_valid_and_open_cell = true
      ENDIF
    ENDIF

    IF is_valid_and_open_cell THEN
      // Choose: Mark this cell as part of the current path being explored.
      // This also prevents re-visiting this cell in the current recursive path through other means if not unmarked.
      // The Python code does path_matrix[current_row][current_col] = 1 here.
      // Crucially, if a path is NOT found via this cell, it's reset to 0 (backtracking).
      IF path_matrix[current_row][current_col] == 1 THEN // Already visited in current path exploration by another route that led here
          RETURN false // Avoid cycles or redundant work on current path
      ENDIF
      path_matrix[current_row][current_col] = 1

      // Explore: Try moving in allowed directions (Python code: Down, Right, Left)
      // Try Down
      IF find_path_recursive(current_row + 1, current_col) == true THEN
        RETURN true // Path found
      ENDIF

      // Try Right
      IF find_path_recursive(current_row, current_col + 1) == true THEN
        RETURN true // Path found
      ENDIF

      // Try Left (as per the specific Python implementation)
      IF find_path_recursive(current_row, current_col - 1) == true THEN
        RETURN true // Path found
      ENDIF

      // Un-choose (Backtrack): If no move from (current_row, current_col) led to the destination,
      // then this cell is not part of the final solution path. Unmark it.
      path_matrix[current_row][current_col] = 0
      RETURN false // No path found through this cell with the attempted moves
    ELSE
      // Cell is out of bounds, a wall, or already part of the current explored path that failed.
      RETURN false
    ENDIF
  ENDFUNCTION // End of find_path_recursive

  // Initial call to start the search from (0,0).
  // The final state of path_matrix is the result.
  find_path_recursive(0, 0)

  RETURN path_matrix
ENDFUNCTION
```
