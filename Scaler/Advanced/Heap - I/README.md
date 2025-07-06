# Advanced/Heap - I

## Files in this topic:

- [B Closest Points to Origin.py](B Closest Points to Origin.py)
- [Connect ropes.py](Connect ropes.py)
- [Heap_1.pdf](Heap_1.pdf)
- [Magician and Chocolates.py](Magician and Chocolates.py)
- [Maximum array sum after B negations.py](Maximum array sum after B negations.py)
- [Product of 3.py](Product of 3.py)

## Description

This section introduces Heap data structures (Priority Queues), covering their properties (min-heap, max-heap), common operations (insert, delete-min/max, peek), and applications in solving problems related to finding k-th smallest/largest elements, scheduling, and more.

### Pseudocode for B Closest Points to Origin.py
```pseudocode
// Requires a Min-Heap data structure that can store tuples/pairs.
// When comparing tuples, it typically compares based on the first element.
// To simulate a Max-Heap for distances, we store (-distance, point) in the Min-Heap.

FUNCTION find_B_closest_points(A_points_list, B_count):
  num_points = length of A_points_list

  // Handle edge cases
  IF B_count == 0 THEN
    RETURN empty_list // Requesting 0 closest points
  ENDIF
  IF B_count >= num_points THEN
    // Requesting all or more points than available; return all points.
    // The order might not matter or should be clarified by problem statement.
    RETURN A_points_list
  ENDIF

  // `closest_points_max_heap` will store up to `B_count` elements.
  // Each element is a tuple: (negative_squared_distance, point_coordinates).
  // Using negative distance allows a Min-Heap to function as a Max-Heap for actual distances.
  closest_points_max_heap = new MinHeap()

  FOR EACH point IN A_points_list:
    x = point[0]
    y = point[1]

    // Calculate squared Euclidean distance to origin (0,0).
    // Using squared distance avoids `sqrt` and maintains order of distances.
    sq_dist = (x*x) + (y*y)

    IF length of closest_points_max_heap < B_count THEN
      // If the heap is not yet full (has fewer than B_count points),
      // add the current point with its negative squared distance.
      ADD (-sq_dist, point) TO closest_points_max_heap
    ELSE
      // Heap is full. Check if the current point is closer to the origin
      // than the furthest point currently in the heap.
      // `PEEK_MIN(closest_points_max_heap).first_element` gives the smallest negative squared distance,
      // which corresponds to the largest actual squared distance in the heap.
      largest_sq_dist_in_heap = -(PEEK_MIN(closest_points_max_heap).first_element)

      IF sq_dist < largest_sq_dist_in_heap THEN
        // Current point is closer than the furthest one currently in the heap.
        // Remove the furthest point (root of conceptual max-heap / min of min-heap of negatives).
        REMOVE_MIN from closest_points_max_heap
        // Add the current point.
        ADD (-sq_dist, point) TO closest_points_max_heap
        // Python's `heapq.heappushpop` combines these two operations efficiently.
      ENDIF
    ENDIF
  ENDFOR

  // Extract the actual points (second element of tuples) from the heap.
  final_B_closest_points_list = empty list
  WHILE closest_points_max_heap IS NOT EMPTY:
    heap_entry = REMOVE_MIN from closest_points_max_heap // (-sq_dist, point)
    ADD heap_entry.second_element TO final_B_closest_points_list // Add point
  ENDWHILE

  // The list order might not be by distance; if needed, sort final_B_closest_points_list.
  RETURN final_B_closest_points_list
ENDFUNCTION
```

### Pseudocode for Connect ropes.py
```pseudocode
// Requires a Min-Heap data structure.
// Basic heap operations needed: ADD (insert), EXTRACT_MIN (remove and return min), GET_SIZE.

FUNCTION calculate_min_cost_to_connect_ropes(A_list_of_rope_lengths):
  num_initial_ropes = length of A_list_of_rope_lengths

  // Edge cases:
  IF num_initial_ropes == 0 THEN
    RETURN 0 // No ropes, so no cost to connect.
  ENDIF
  IF num_initial_ropes == 1 THEN
    RETURN 0 // Only one rope, no connections needed.
  ENDIF

  // Step 1: Initialize a min-heap and add all rope lengths to it.
  // The min-heap will always give us the shortest rope(s) available.
  rope_lengths_min_heap = new MinHeap()
  FOR EACH length_val IN A_list_of_rope_lengths:
    ADD length_val TO rope_lengths_min_heap
  ENDFOR

  total_accumulated_cost = 0

  // Step 2: Greedily connect the two shortest ropes available until only one rope remains.
  // The loop continues as long as there is more than one rope segment in the heap.
  WHILE GET_SIZE(rope_lengths_min_heap) > 1:
    // Extract the two shortest ropes from the heap.
    rope1_length = EXTRACT_MIN(rope_lengths_min_heap)
    rope2_length = EXTRACT_MIN(rope_lengths_min_heap)

    // The cost of connecting these two ropes is the sum of their lengths.
    cost_of_current_connection = rope1_length + rope2_length

    // Add this cost to the total accumulated cost.
    total_accumulated_cost = total_accumulated_cost + cost_of_current_connection

    // The newly formed rope (with length `cost_of_current_connection`)
    // is added back into the heap to be considered for future connections.
    ADD cost_of_current_connection TO rope_lengths_min_heap
  ENDWHILE

  // When the loop finishes, all ropes have been combined into a single rope.
  // `total_accumulated_cost` holds the minimum total cost to achieve this.
  RETURN total_accumulated_cost
ENDFUNCTION
```

### Pseudocode for Magician and Chocolates.py
```pseudocode
// Requires a Max-Heap data structure (or a Min-Heap to simulate one by storing negative values).
// Heap Operations: ADD_TO_HEAP, EXTRACT_MAX, PEEK_MAX, IS_EMPTY.
// Assumes FLOOR division for halving chocolates.

FUNCTION maximize_eaten_chocolates(A_num_tries, B_chocolates_in_bags):
  // Initialize a max-heap to always pick the bag with the most chocolates.
  // If using a min-heap, store negative counts of chocolates.
  chocolate_bags_heap = new MaxHeap()

  // Add initial chocolate counts of all bags to the heap.
  FOR EACH count_choc IN B_chocolates_in_bags:
    IF count_choc > 0 THEN // Only consider bags that have chocolates
      ADD count_choc TO chocolate_bags_heap
      // If using min-heap for simulation: ADD (-count_choc) TO chocolate_bags_heap
    ENDIF
  ENDFOR

  total_chocolates_consumed = 0
  MODULO_CONSTANT = 10^9 + 7

  // Perform A_num_tries operations (picking a bag each time).
  FOR try_num FROM 1 TO A_num_tries:
    // If there are no more bags with chocolates in the heap, stop.
    IF IS_EMPTY(chocolate_bags_heap) THEN
      BREAK FOR
    ENDIF

    // Select the bag with the maximum number of chocolates.
    chocolates_in_selected_bag = PEEK_MAX(chocolate_bags_heap)
    EXTRACT_MAX_FROM_HEAP(chocolate_bags_heap)
    // If using min-heap for simulation:
    //   chocolates_in_selected_bag = -PEEK_MIN(chocolate_bags_heap)
    //   EXTRACT_MIN_FROM_HEAP(chocolate_bags_heap)

    // Add the eaten chocolates to the total.
    total_chocolates_consumed = total_chocolates_consumed + chocolates_in_selected_bag
    // Python code applies modulo at the end. For large sums, apply per addition if needed.
    // total_chocolates_consumed = (total_chocolates_consumed + chocolates_in_selected_bag) % MODULO_CONSTANT

    // Calculate the number of chocolates the bag gets refilled with.
    refilled_chocolates_count = FLOOR(chocolates_in_selected_bag / 2)

    // If the refilled bag has chocolates, add it back to the heap.
    IF refilled_chocolates_count > 0 THEN
      ADD refilled_chocolates_count TO chocolate_bags_heap
      // If using min-heap for simulation: ADD (-refilled_chocolates_count) TO chocolate_bags_heap
    ENDIF
  ENDFOR

  // Return the total chocolates consumed, modulo 10^9 + 7.
  RETURN total_chocolates_consumed % MODULO_CONSTANT
ENDFUNCTION
```

### Pseudocode for Maximum array sum after B negations.py
```pseudocode
// Requires a Min-Heap data structure.
// Operations: ADD_TO_HEAP, EXTRACT_MIN, GET_ALL_ELEMENTS (for sum).

FUNCTION maximize_sum_after_negations(A_list_numbers, B_negation_operations_count):
  num_elements = length of A_list_numbers
  IF num_elements == 0 THEN
    RETURN 0 // Sum of an empty list is 0
  ENDIF

  // Step 1: Build a min-heap from all numbers in A_list_numbers.
  // This allows efficient access to the smallest element.
  min_priority_queue = new MinHeap()
  FOR EACH num IN A_list_numbers:
    ADD num TO min_priority_queue
  ENDFOR

  // Step 2: Perform B_negation_operations_count negations.
  // In each step, extract the smallest element from the heap, negate it,
  // and insert the negated value back into the heap.
  FOR operation_count FROM 1 TO B_negation_operations_count:
    IF IS_HEAP_EMPTY(min_priority_queue) THEN
      // This would only happen if the input list A was empty, handled above.
      BREAK FOR
    ENDIF

    smallest_current_number = EXTRACT_MIN(min_priority_queue)
    negated_number = -smallest_current_number // Negate it
    ADD negated_number TO min_priority_queue // Add the negated element back
  ENDFOR

  // Step 3: Calculate the sum of all elements currently in the heap.
  // This sum is the maximum possible sum after B negations.
  final_sum = 0
  // (Python's `sum(h)` works on the list representation of the heap.
  // For pseudocode, assume a way to iterate or get all elements.)
  elements_in_heap = GET_ALL_ELEMENTS(min_priority_queue)
  FOR EACH num IN elements_in_heap:
    final_sum = final_sum + num
  ENDFOR

  RETURN final_sum
ENDFUNCTION
```

### Pseudocode for Product of 3.py
```pseudocode
// Requires a Min-Heap data structure.
// Operations: ADD_TO_HEAP, REMOVE_MIN_FROM_HEAP, GET_HEAP_SIZE, GET_ALL_ELEMENTS_FROM_HEAP.

FUNCTION product_of_three_largest_in_prefixes(A_list):
  n = length of A_list
  output_results_list = empty list

  // If the list has 2 or fewer elements, all prefix results are -1.
  IF n <= 2 THEN
    FOR _ FROM 1 TO n:
      ADD -1 TO output_results_list
    ENDFOR
    RETURN output_results_list
  ENDIF

  // `current_top_3_elements_heap` will store the 3 largest elements seen so far
  // in the prefix A[0...i]. It's a min-heap, so the smallest of these 3 is at the root.
  current_top_3_elements_heap = new MinHeap()

  // Iterate through the input list A_list, element by element.
  FOR i FROM 0 TO n - 1:
    current_num = A_list[i]

    // Add the current_num to the heap.
    ADD current_num TO current_top_3_elements_heap

    // If the heap size exceeds 3, remove the smallest element to maintain only the 3 largest.
    WHILE GET_HEAP_SIZE(current_top_3_elements_heap) > 3:
      REMOVE_MIN_FROM_HEAP(current_top_3_elements_heap)
    ENDWHILE

    // After processing A_list[i]:
    IF GET_HEAP_SIZE(current_top_3_elements_heap) < 3 THEN
      // If the current prefix (and thus the heap) has fewer than 3 elements,
      // the product of the 3 largest cannot be formed. Append -1.
      ADD -1 TO output_results_list
    ELSE
      // The heap contains exactly the 3 largest elements of the prefix A_list[0...i].
      // Calculate their product.
      // (Note: Python's `heapq` stores elements in a list; `h[0]`, `h[1]`, `h[2]` after heap operations
      // would give the three elements if size is 3. For a generic heap, one might need to extract all.)

      // Get the three elements from the heap.
      // For a min-heap of size 3, these are the 3 largest seen so far.
      temp_list_of_top_3 = GET_ALL_ELEMENTS_FROM_HEAP(current_top_3_elements_heap)
      // Ensure there are exactly 3 elements.
      IF length of temp_list_of_top_3 == 3 THEN
        product = temp_list_of_top_3[0] * temp_list_of_top_3[1] * temp_list_of_top_3[2]
        ADD product TO output_results_list
      ELSE
        // This case should not be reached if heap size management is correct.
        ADD -1 TO output_results_list // Fallback
      ENDIF
    ENDIF
  ENDFOR

  RETURN output_results_list
ENDFUNCTION
```
