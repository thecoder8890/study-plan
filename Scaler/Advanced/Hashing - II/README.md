# Advanced/Hashing - II

## Files in this topic:

- [Common Elements.py](Common Elements.py)
- [Compare Sorted Subarrays.py](Compare Sorted Subarrays.py)
- [Count Rectangles.py](Count Rectangles.py)
- [Count Right Triangles.py](Count Right Triangles.py)
- [Hashing_II_.pdf](Hashing_II_.pdf)
- [Points on same line.py](Points on same line.py)
- [Replicating Substring.py](Replicating Substring.py)

## Description

This section covers more advanced applications and problem-solving techniques using Hashing. Topics may include complex counting problems, geometric problems solvable with hashing, string matching algorithms, or optimizations involving custom hash functions and collision handling.

### Pseudocode for Count Rectangles.py
```pseudocode
FUNCTION count_axis_parallel_rectangles(A_x_coordinates, B_y_coordinates):
  num_total_points = length of A_x_coordinates

  // A rectangle requires at least 4 points.
  IF num_total_points < 4 THEN
    RETURN 0
  ENDIF

  points_list = empty list
  points_lookup_set = new Set()
  FOR i FROM 0 TO num_total_points - 1:
    current_point = (x: A_x_coordinates[i], y: B_y_coordinates[i])
    ADD current_point TO points_list
    ADD current_point TO points_lookup_set
  ENDFOR
  // Note: Problem statement implies (A[i],B[i]) pairs are unique points.

  rectangle_count_accumulator = 0 // This will be divided by 2 at the end.

  // Iterate through all unique pairs of points (P1, P2) from points_list.
  // These two points are considered as potential diagonal corners of a rectangle.
  // The Python code iterates j from i, which counts each rectangle's pair of diagonals.
  FOR i FROM 0 TO num_total_points - 1:
    P1_x = points_list[i].x
    P1_y = points_list[i].y

    FOR j FROM i TO num_total_points - 1: // Python's loop structure
      P2_x = points_list[j].x
      P2_y = points_list[j].y

      // Check if P1 and P2 can form a diagonal.
      // Their x-coordinates must be different AND y-coordinates must be different.
      IF P1_x == P2_x OR P1_y == P2_y THEN
        CONTINUE // These points lie on the same horizontal or vertical line, or are the same point.
      ENDIF

      // If P1=(x1, y1) and P2=(x2, y2) are diagonal corners,
      // the other two corners of the rectangle would be P3=(x1, y2) and P4=(x2, y1).
      required_corner_P3 = (x: P1_x, y: P2_y)
      required_corner_P4 = (x: P2_x, y: P1_y)

      // Check if these other two required corner points exist in our set of input points.
      IF required_corner_P3 IS PRESENT IN points_lookup_set AND
         required_corner_P4 IS PRESENT IN points_lookup_set THEN
        // All four corners exist, forming a valid axis-parallel rectangle.
        // This pair of diagonal points (P1, P2) defines one such rectangle.
        rectangle_count_accumulator = rectangle_count_accumulator + 1
      ENDIF
    ENDFOR
  ENDFOR

  // Each rectangle is defined by two pairs of diagonal points (e.g., (P1, P3) and (P2, P4)).
  // The nested loops (j from i) will find both ((P1_x,P1_y), (P2_x,P2_y)) and ((P1_x,P2_y), (P2_x,P1_y))
  // as valid diagonal pairs for the same rectangle if all four points exist.
  // Thus, each rectangle is counted twice. So, divide the final count by 2.
  RETURN rectangle_count_accumulator / 2 // Integer division
ENDFUNCTION
```

### Pseudocode for Replicating Substring.py
```pseudocode
// Assumes a Counter or FrequencyMap data structure/utility is available.
// Operations:
//   - Initialize from string: `new FrequencyMap from B_string`
//   - Iterate items: `FOR EACH (character, frequency) IN ITEMS_OF(frequency_map)`

FUNCTION can_rearrange_to_A_similar_strings(A_number_of_parts, B_original_string):
  // Step 1: Calculate the frequency of each character in the B_original_string.
  character_frequencies = new FrequencyMap from B_original_string

  // Step 2: For the string B_original_string to be formable by concatenating
  // A_number_of_parts identical substrings, the count of each character in
  // B_original_string must be evenly divisible by A_number_of_parts.
  // This ensures that each of the A_number_of_parts substrings can have an equal
  // number of occurrences of that character.

  FOR EACH (char_key, char_freq_value) IN ITEMS_OF(character_frequencies):
    IF char_freq_value MOD A_number_of_parts != 0 THEN
      // If the frequency of any character is not perfectly divisible by A_number_of_parts,
      // then it's impossible to form A_number_of_parts identical substrings.
      RETURN -1 // Represents false (not possible)
    ENDIF
  ENDFOR

  // If all character frequencies are divisible by A_number_of_parts,
  // then it is possible.
  RETURN 1 // Represents true (possible)
ENDFUNCTION
```

### Pseudocode for Count Right Triangles.py
```pseudocode
// Assumes Counter-like functionality for frequency mapping from a list.
// Operations: GET_FREQUENCY(map, key_value).

FUNCTION count_axis_aligned_right_triangles(A_x_coords, B_y_coords):
  num_points = length of A_x_coords // Should be equal to length of B_y_coords
  MODULO_VAL = 10^9 + 7

  // A triangle requires 3 points.
  IF num_points < 3 THEN
    RETURN 0
  ENDIF

  // Step 1: Create frequency maps for x-coordinates and y-coordinates.
  // x_coord_freq_map: Stores x_coordinate -> count of points with this x-coordinate.
  // y_coord_freq_map: Stores y_coordinate -> count of points with this y-coordinate.
  x_coord_freq_map = new FrequencyMap from A_x_coords
  y_coord_freq_map = new FrequencyMap from B_y_coords

  total_triangles_found = 0

  // Step 2: Iterate through each point P_i = (A_x_coords[i], B_y_coords[i]).
  // Consider P_i as the potential vertex where the right angle of a triangle is formed.
  FOR i FROM 0 TO num_points - 1:
    current_x = A_x_coords[i]
    current_y = B_y_coords[i]

    // Number of other points that share the same x-coordinate as P_i.
    // These points can form the vertical side of a right triangle with P_i.
    // (Frequency of current_x) - 1 (to exclude P_i itself).
    count_other_points_on_same_x_line = GET_FREQUENCY(x_coord_freq_map, current_x) - 1

    // Number of other points that share the same y-coordinate as P_i.
    // These points can form the horizontal side of a right triangle with P_i.
    // (Frequency of current_y) - 1 (to exclude P_i itself).
    count_other_points_on_same_y_line = GET_FREQUENCY(y_coord_freq_map, current_y) - 1

    // To form a right-angled triangle with P_i at the vertex of the right angle,
    // we need to choose one point from the `count_other_points_on_same_x_line`
    // and one point from the `count_other_points_on_same_y_line`.
    // The number of ways to do this is their product.
    // Ensure counts are not negative if a coordinate was unique (freq 1, so count-1 = 0).
    IF count_other_points_on_same_x_line < 0 THEN count_other_points_on_same_x_line = 0 ENDIF
    IF count_other_points_on_same_y_line < 0 THEN count_other_points_on_same_y_line = 0 ENDIF

    num_triangles_with_P_i_as_vertex = count_other_points_on_same_x_line * count_other_points_on_same_y_line

    // Add this to the total count.
    // Python code adds to `count` and then applies modulo at the end.
    // If intermediate sums are large, modulo at each step is safer.
    total_triangles_found = total_triangles_found + num_triangles_with_P_i_as_vertex
    // total_triangles_found = (total_triangles_found + num_triangles_with_P_i_as_vertex) % MODULO_VAL (safer)
  ENDFOR

  // Return the final count modulo MODULO_VAL.
  RETURN total_triangles_found % MODULO_VAL
ENDFUNCTION
```
