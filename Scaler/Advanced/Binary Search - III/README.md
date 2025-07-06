# Advanced/Binary Search - III

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [Adv_Binary_Search_III.pdf](Adv_Binary_Search_III.pdf)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)

## Description

This section covers highly advanced binary search problems and techniques, potentially including applications in geometry, dynamic programming optimization, or complex data structures.

### Pseudocode for A1.py
```pseudocode
// Main function to find the minimum time to paint all boards
FUNCTION paint(A_num_painters, B_time_per_unit_board, C_board_lengths_list):
  num_boards = length of C_board_lengths_list
  IF num_boards == 0 THEN
    RETURN 0 // No boards, no time needed
  ENDIF

  // --- Determine search range for binary search (on time) ---
  // low_bound_time: Min time is when the longest board is painted (cost of that board).
  // Each board's cost is C[i] * B_time_per_unit_board.
  max_single_board_cost = 0
  FOR EACH board_len IN C_board_lengths_list:
    cost = board_len * B_time_per_unit_board
    IF cost > max_single_board_cost THEN
      max_single_board_cost = cost
  ENDFOR
  low_bound_time = max_single_board_cost

  // high_bound_time: Max time is when one painter paints all boards.
  total_length_of_all_boards = 0
  FOR EACH board_len IN C_board_lengths_list:
    total_length_of_all_boards = total_length_of_all_boards + board_len
  ENDFOR
  high_bound_time = total_length_of_all_boards * B_time_per_unit_board

  min_time_result = high_bound_time // Initialize with the maximum possible time

  // Binary search for the minimum possible time `k`
  WHILE low_bound_time <= high_bound_time:
    candidate_max_time_k = low_bound_time + FLOOR((high_bound_time - low_bound_time) / 2)

    // Check if it's possible to paint all boards using A_num_painters
    // such that no painter works more than candidate_max_time_k
    IF check_if_paintable(A_num_painters, B_time_per_unit_board, C_board_lengths_list, candidate_max_time_k) THEN
      // If paintable with candidate_max_time_k, this is a possible answer.
      // Try for a smaller time.
      min_time_result = candidate_max_time_k
      high_bound_time = candidate_max_time_k - 1
    ELSE
      // Not paintable with candidate_max_time_k, need more time.
      low_bound_time = candidate_max_time_k + 1
    ENDIF
  ENDWHILE

  RETURN min_time_result % 10000003 // As per problem's modulo requirement
ENDFUNCTION


// Helper function: Checks if all boards can be painted by A_p painters,
// with each painter working at most k_max_time_allowed.
FUNCTION check_if_paintable(A_p_painters_available, B_tpu_board_time_unit, C_boards, k_max_time_allowed):
  painters_required = 1
  current_painter_workload = 0 // Time spent by the current painter

  FOR EACH board_len IN C_boards:
    time_for_this_board = board_len * B_tpu_board_time_unit

    // This check is important: if a single board's time exceeds k_max_time_allowed,
    // it's impossible. The main `paint` function's `low_bound_time` setup ensures `k_max_time_allowed`
    // will always be >= `time_for_this_board` when a new painter starts a board.
    // IF time_for_this_board > k_max_time_allowed THEN RETURN false ENDIF (Implicitly handled by low_bound_time)

    IF current_painter_workload + time_for_this_board <= k_max_time_allowed THEN
      // Current painter can paint this board
      current_painter_workload = current_painter_workload + time_for_this_board
    ELSE
      // Current painter cannot take this board; assign to a new painter
      painters_required = painters_required + 1
      current_painter_workload = time_for_this_board // New painter starts with this board

      // If all available painters are used up and we still need more
      IF painters_required > A_p_painters_available THEN
        RETURN false
      ENDIF
      // Also, if this new assignment itself violates time (already covered by how k is chosen)
      // IF current_painter_workload > k_max_time_allowed THEN RETURN false ENDIF (implicitly handled)
    ENDIF
  ENDFOR

  // If loop finishes, all boards could be assigned to painters within the time limit k_max_time_allowed
  RETURN true
ENDFUNCTION
```

### Pseudocode for HW2.py
```pseudocode
// Main function to find the maximum k (food units per entity)
FUNCTION solve_food_distribution(A_food_available_list, B_entities_to_feed):
  n_sources = length of A_food_available_list

  // Handle edge cases for empty list or zero entities
  IF B_entities_to_feed == 0 THEN
    // If no entities to feed, technically any k is possible or it's undefined.
    // Python's default ans=-1 might lead to 0. Let's assume problem implies B > 0.
    RETURN some_max_possible_k_or_0 // Or based on constraints for B=0
  ENDIF
  IF n_sources == 0 THEN
    RETURN 0 // No food, cannot feed any entities if B > 0
  ENDIF

  // Determine binary search range for k.
  // low_k_val: Smallest possible food per entity (Python uses 0).
  low_k_val = 0
  // high_k_val: Largest possible food per entity. Python uses min(A).
  // A more robust upper bound could be max(A) or sum(A)/B_entities_to_feed if B_entities_to_feed > 0.
  // Let's follow Python's choice for direct translation.
  min_food_in_any_source = A_food_available_list[0]
  FOR idx FROM 1 TO n_sources - 1:
    IF A_food_available_list[idx] < min_food_in_any_source THEN
      min_food_in_any_source = A_food_available_list[idx]
  ENDFOR
  high_k_val = min_food_in_any_source
  // If A_food_available_list was empty, this would error. Handled by n_sources check.

  max_k_found_so_far = -1 // Python's initial `ans`

  WHILE low_k_val <= high_k_val:
    candidate_k_val = low_k_val + FLOOR((high_k_val - low_k_val) / 2)

    IF check_feasibility(A_food_available_list, B_entities_to_feed, candidate_k_val) THEN
      // If it's possible to feed B_entities_to_feed with candidate_k_val food each,
      // this candidate_k_val is a potential answer. Try for a larger k.
      max_k_found_so_far = candidate_k_val
      low_k_val = candidate_k_val + 1
    ELSE
      // candidate_k_val is too high (cannot feed all entities).
      // Need to try a smaller k.
      high_k_val = candidate_k_val - 1
    ENDIF
  ENDWHILE

  // Python's final return logic:
  IF max_k_found_so_far == -1 THEN
    RETURN 0
  ELSE
    RETURN max_k_found_so_far
  ENDIF
ENDFUNCTION


// Helper function: Checks if B_target_num_entities can be supplied
// if each entity receives k_food_amount_per_entity.
FUNCTION check_feasibility(A_food_sources_list, B_target_num_entities, k_food_amount_per_entity):
  num_sources = length of A_food_sources_list
  total_entities_can_be_supplied = 0

  FOR i FROM 0 TO num_sources - 1:
    IF k_food_amount_per_entity == 0 THEN
      // Python code's specific behavior for k=0:
      // It sums A_food_sources_list[i], implying each unit of food from source i
      // can supply one entity if k=0. This is an unusual interpretation.
      total_entities_can_be_supplied = total_entities_can_be_supplied + A_food_sources_list[i]
    ELSE // k_food_amount_per_entity > 0
      // Number of entities this source can supply
      total_entities_can_be_supplied = total_entities_can_be_supplied + (A_food_sources_list[i] / k_food_amount_per_entity) // Integer division
    ENDIF

    // Optimization: If already enough entities can be supplied, can return true early.
    // (Not present in the Python code, but a common optimization)
    // IF total_entities_can_be_supplied >= B_target_num_entities THEN RETURN true ENDIF
  ENDFOR

  RETURN (total_entities_can_be_supplied >= B_target_num_entities)
ENDFUNCTION
```

### Pseudocode for HW1.py
```pseudocode
// Main function to find the minimum of the maximum pages any student reads
FUNCTION allocate_books_min_max_pages(A_book_pages, B_num_students):
  num_books = length of A_book_pages

  // Edge case: If more students than books, it's impossible according to problem's usual interpretation (return -1)
  // or if B_num_students is 0.
  IF num_books == 0 THEN RETURN 0 ENDIF // No books, 0 pages.
  IF B_num_students == 0 THEN RETURN -1 ENDIF // No students, impossible.
  IF num_books < B_num_students THEN
    RETURN -1
  ENDIF

  // --- Determine search range for binary search (on maximum pages `k` per student) ---
  // low_k: Minimum possible k is the page count of the largest single book.
  max_pages_single_book = 0
  FOR EACH pages IN A_book_pages:
    IF pages > max_pages_single_book THEN
      max_pages_single_book = pages
  ENDFOR
  low_k = max_pages_single_book

  // high_k: Maximum possible k is the sum of pages of all books (if 1 student reads all).
  sum_all_pages = 0
  FOR EACH pages IN A_book_pages:
    sum_all_pages = sum_all_pages + pages
  ENDFOR
  high_k = sum_all_pages

  min_max_pages_result = high_k // Initialize with a valid upper bound for the answer

  // Binary search for the smallest `k` (max pages for any student)
  WHILE low_k <= high_k:
    candidate_k_max_pages = low_k + FLOOR((high_k - low_k) / 2)

    IF is_allocation_feasible(A_book_pages, B_num_students, candidate_k_max_pages) THEN
      // If books can be allocated with `candidate_k_max_pages` as the limit,
      // this `candidate_k_max_pages` is a possible answer. Try for a smaller limit.
      min_max_pages_result = candidate_k_max_pages
      high_k = candidate_k_max_pages - 1
    ELSE
      // Cannot allocate with `candidate_k_max_pages` limit (it's too restrictive).
      // Need to allow more pages per student (increase k).
      low_k = candidate_k_max_pages + 1
    ENDIF
  ENDWHILE

  RETURN min_max_pages_result // Python code does not apply modulo here, unlike A1.py.
ENDFUNCTION


// Helper function: Checks if books in `A_pages_list` can be allocated
// to `B_s_students` such that no student reads more than `k_max_limit_pages`.
FUNCTION is_allocation_feasible(A_pages_list, B_s_available_students, k_max_limit_pages):
  students_needed_count = 1
  pages_assigned_to_current_student = 0

  FOR EACH book_p IN A_pages_list:
    // Implicit check: k_max_limit_pages must be >= book_p for this book to be assignable at all.
    // This is guaranteed by the `low_k` initialization in the main function.
    // IF book_p > k_max_limit_pages THEN RETURN false ENDIF (Good to note)

    IF pages_assigned_to_current_student + book_p <= k_max_limit_pages THEN
      // Current student can read this book without exceeding the limit
      pages_assigned_to_current_student = pages_assigned_to_current_student + book_p
    ELSE
      // Current student cannot read this book; assign it to a new student
      students_needed_count = students_needed_count + 1
      pages_assigned_to_current_student = book_p // New student starts with this book

      // If the number of students needed exceeds the available students
      IF students_needed_count > B_s_available_students THEN
        RETURN false // Not feasible
      ENDIF
    ENDIF
  ENDFOR

  // If the loop completes, all books have been successfully assigned.
  RETURN true
ENDFUNCTION
```

### Pseudocode for A2.py
```pseudocode
// Main function to find the largest minimum distance
FUNCTION solve_aggressive_cows(A_stall_positions, B_num_cows):
  n_stalls = length of A_stall_positions

  // Basic input validation
  IF B_num_cows == 0 THEN RETURN 0 ENDIF // Or some large value if maximizing min distance
  IF n_stalls < B_num_cows THEN RETURN 0 ENDIF // Cannot place more cows than stalls
  IF n_stalls == 0 THEN RETURN 0 ENDIF

  SORT A_stall_positions ASCENDING

  // Binary search for the maximum possible minimum distance `k`.
  // `low_k_dist`: Smallest possible distance (0 or 1, depending on interpretation). Python uses 0.
  // `high_k_dist`: Largest possible distance (difference between last and first stall).
  low_k_dist = 0
  high_k_dist = A_stall_positions[n_stalls-1] - A_stall_positions[0]

  best_max_min_distance = 0 // Stores the largest `k` for which placement is possible

  WHILE low_k_dist <= high_k_dist:
    candidate_k_dist = low_k_dist + FLOOR((high_k_dist - low_k_dist) / 2)

    // Check if it's possible to place B_num_cows with at least `candidate_k_dist` separation.
    IF can_place_cows(A_stall_positions, B_num_cows, candidate_k_dist) THEN
      // If possible, `candidate_k_dist` is a valid minimum distance.
      // Try for an even larger minimum distance. Store this candidate.
      best_max_min_distance = candidate_k_dist // Python: ans = max(ans, k)
      low_k_dist = candidate_k_dist + 1
    ELSE
      // Not possible with `candidate_k_dist`. It's too large.
      // Need to try a smaller minimum distance.
      high_k_dist = candidate_k_dist - 1
    ENDIF
  ENDWHILE

  RETURN best_max_min_distance
ENDFUNCTION


// Helper function: Checks if `num_cows_to_place` can be placed in `sorted_stall_positions`
// such that the minimum distance between any two cows is at least `min_required_dist`.
FUNCTION can_place_cows(sorted_stall_positions, num_cows_to_place, min_required_dist):
  num_stalls = length of sorted_stall_positions

  // If trying to place 0 cows or fewer, it's trivially true (or handle based on constraints).
  // If no stalls and trying to place cows, false.
  // The Python code's main function handles B=0 or n_stalls < B.
  // Assume num_cows_to_place >= 1 and num_stalls >= num_cows_to_place here for core logic.

  cows_successfully_placed = 1 // Place the first cow in the first stall
  position_of_last_placed_cow = sorted_stall_positions[0]

  // Iterate through the rest of the stalls to place remaining cows
  FOR i FROM 1 TO num_stalls - 1:
    current_stall_pos = sorted_stall_positions[i]

    // Check if a cow can be placed at current_stall_pos
    IF current_stall_pos - position_of_last_placed_cow >= min_required_dist THEN
      cows_successfully_placed = cows_successfully_placed + 1
      position_of_last_placed_cow = current_stall_pos

      // If all required cows have been placed
      IF cows_successfully_placed == num_cows_to_place THEN
        RETURN true
      ENDIF
    ENDIF
  ENDFOR

  // If the loop finishes and not all cows were placed, it's not possible.
  // (Python code returns False outside the loop, which is equivalent to this if count didn't reach B)
  RETURN (cows_successfully_placed >= num_cows_to_place) // More robust check
ENDFUNCTION
```
