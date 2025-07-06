# Advanced/Queues and Deques

## Files in this topic:

- [Deque_and_problems_on_stacks.pdf](Deque_and_problems_on_stacks.pdf)
- [First non-repeating character.py](First non-repeating character.py)
- [Maximum Frequency stack.py](Maximum Frequency stack.py)
- [Min Stack.py](Min Stack.py)
- [Sliding Window Maximum.py](Sliding Window Maximum.py)
- [Sum of min and max.py](Sum of min and max.py)

## Description

This section covers problems and applications involving Queues (FIFO) and Deques (Double-Ended Queues). Topics include implementing specialized queue/deque structures (like Min/Max Stack, Max Frequency Stack) and using them to solve problems like finding first non-repeating characters in a stream or sliding window maximums.

### Pseudocode for First non-repeating character.py
```pseudocode
// Assumes Deque data structure with PEEK_FRONT, POP_FROM_FRONT, ADD_TO_END, IS_EMPTY operations.
// Assumes DefaultDictionary (or HashMap) for frequency counting, defaulting to 0.

FUNCTION stream_first_non_repeating_character(A_character_stream_string):
  // `ordered_chars_queue` maintains characters in the order they appeared,
  // filtering out those that have become repeating.
  ordered_chars_queue = new Deque()

  // `char_counts_map` stores the frequency of each character encountered so far.
  char_counts_map = new DefaultDictionary with default_value 0

  // `output_result_string` accumulates the answer for each character processed from the stream.
  output_result_string = ""

  // Process each character `ch` from the input stream string `A_character_stream_string`.
  FOR EACH ch IN A_character_stream_string:
    // 1. Add the current character `ch` to the queue.
    ordered_chars_queue.ADD_TO_END(ch)
    // 2. Increment its frequency count.
    char_counts_map[ch] = char_counts_map[ch] + 1

    // 3. Find the first non-repeating character from the current state of the queue.
    //    Remove any characters from the front of the queue that are now repeating
    //    (i.e., their count in `char_counts_map` is greater than 1).
    WHILE ordered_chars_queue IS NOT EMPTY:
      front_char_in_queue = ordered_chars_queue.PEEK_FRONT()
      IF char_counts_map[front_char_in_queue] > 1 THEN
        // This character is repeating, remove it from consideration as the "first non-repeating".
        ordered_chars_queue.POP_FROM_FRONT()
      ELSE
        // `front_char_in_queue` is non-repeating (count is 1). This is our current answer.
        BREAK WHILE // Found the first non-repeating character
      ENDIF
    ENDWHILE

    // 4. Append to the output result string.
    IF ordered_chars_queue IS NOT EMPTY THEN
      // The character at the front of the queue is the first non-repeating one.
      APPEND ordered_chars_queue.PEEK_FRONT() TO output_result_string
    ELSE
      // No non-repeating character currently in the stream.
      APPEND "#" TO output_result_string
    ENDIF
  ENDFOR

  RETURN output_result_string
ENDFUNCTION
```

### Pseudocode for Min Stack.py
```pseudocode
// This class implements a stack that supports push, pop, top, and retrieving the minimum element in O(1) time.
// It uses two internal lists (acting as stacks): one for all elements, and one to track minimums.

CLASS MinStack:
  ATTRIBUTES:
    main_stack_list: List // Stores all the elements pushed onto the stack.
    min_values_stack: List // Auxiliary stack; its top always holds the current minimum in main_stack_list.

  // Constructor
  CONSTRUCTOR():
    this.main_stack_list = new empty List
    this.min_values_stack = new empty List
  ENDFUNCTION

  // Pushes element `x` onto the stack.
  FUNCTION push(x_element_to_push):
    // If the min_values_stack is empty, or if x_element_to_push is less than or equal to
    // the current minimum (which is at the top of min_values_stack),
    // then push x_element_to_push onto the min_values_stack.
    IF this.min_values_stack.IS_EMPTY() OR x_element_to_push <= this.min_values_stack.GET_LAST_ELEMENT() THEN
      this.min_values_stack.ADD_TO_END(x_element_to_push) // Push x to min_values_stack
    ENDIF

    // Always push x_element_to_push onto the main_stack_list.
    this.main_stack_list.ADD_TO_END(x_element_to_push)

    // Python code returns the pushed element; usually push is void or returns nothing specific.
    // RETURN x_element_to_push (optional, based on function signature if any)
  ENDFUNCTION

  // Removes the element on top of the stack.
  FUNCTION pop():
    // Check if the main_stack_list is not empty before attempting to pop.
    IF this.main_stack_list.IS_EMPTY() IS FALSE THEN
      // Get the value of the element about to be popped from the main stack.
      top_element_value = this.main_stack_list.GET_LAST_ELEMENT()

      // If this top_element_value is also the current minimum (i.e., it's at the top of min_values_stack),
      // then it must also be popped from min_values_stack to maintain correctness of getMin().
      IF this.min_values_stack.IS_EMPTY() IS FALSE AND top_element_value == this.min_values_stack.GET_LAST_ELEMENT() THEN
        this.min_values_stack.REMOVE_FROM_END() // Pop from min_values_stack
      ENDIF

      // Pop the element from the main_stack_list.
      this.main_stack_list.REMOVE_FROM_END()
    ENDIF
    // Pop operation typically does not return a value, or returns the popped element.
    // Python code has no explicit return for pop.
  ENDFUNCTION

  // Gets the top element of the stack.
  FUNCTION top():
    IF this.main_stack_list.IS_EMPTY() IS FALSE THEN
      RETURN this.main_stack_list.GET_LAST_ELEMENT()
    ELSE
      // If stack is empty, return a sentinel value (Python code returns -1).
      RETURN -1
    ENDIF
  ENDFUNCTION

  // Retrieves the minimum element in the current stack in O(1) time.
  FUNCTION getMin():
    // The Python code checks `len(self.st) > 0`. A more direct check on `min_values_stack` is appropriate.
    IF this.min_values_stack.IS_EMPTY() IS FALSE THEN
      // The top of min_values_stack always holds the current minimum element.
      RETURN this.min_values_stack.GET_LAST_ELEMENT()
    ELSE
      // If stack is empty (or min_values_stack is unexpectedly empty), return sentinel.
      RETURN -1
    ENDIF
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for Sum of min and max.py
```pseudocode
// Assumes Deque data structure with operations:
//   ADD_TO_REAR (append), REMOVE_FROM_REAR (pop_last),
//   POP_FROM_FRONT (pop_first), PEEK_FRONT, PEEK_REAR, IS_EMPTY.

FUNCTION sum_of_min_max_for_all_windows(A_list_items, B_window_len):
  n = length of A_list_items
  MODULO = 10^9 + 7
  cumulative_sum_of_min_plus_max = 0

  // Handle edge cases where no valid windows can be formed.
  IF n == 0 OR B_window_len == 0 OR B_window_len > n THEN
    RETURN 0
  ENDIF

  // `max_val_deque` stores elements from current window, in decreasing order. Front is max.
  max_val_deque = new Deque()
  // `min_val_deque` stores elements from current window, in increasing order. Front is min.
  min_val_deque = new Deque()

  window_start_index = 0

  // Iterate through the list, with `window_end_index` marking the right end of the sliding window.
  FOR window_end_index FROM 0 TO n - 1:
    current_item = A_list_items[window_end_index]

    // --- Maintain `max_val_deque` (for window maximum) ---
    // Remove elements from the rear of `max_val_deque` that are smaller than `current_item`.
    WHILE max_val_deque IS NOT EMPTY AND current_item > max_val_deque.PEEK_REAR():
      max_val_deque.REMOVE_FROM_REAR()
    ENDWHILE
    max_val_deque.ADD_TO_REAR(current_item) // Add current_item to maintain decreasing order.

    // --- Maintain `min_val_deque` (for window minimum) ---
    // Remove elements from the rear of `min_val_deque` that are larger than `current_item`.
    WHILE min_val_deque IS NOT EMPTY AND current_item < min_val_deque.PEEK_REAR():
      min_val_deque.REMOVE_FROM_REAR()
    ENDWHILE
    min_val_deque.ADD_TO_REAR(current_item) // Add current_item to maintain increasing order.

    // --- When window reaches size `B_window_len` ---
    IF (window_end_index - window_start_index + 1) == B_window_len THEN
      // Get max and min for the current window from the front of the deques.
      window_maximum = max_val_deque.PEEK_FRONT()
      window_minimum = min_val_deque.PEEK_FRONT()

      // Add (window_maximum + window_minimum) to the cumulative sum, applying modulo.
      sum_for_current_window = (window_maximum + window_minimum) % MODULO
      cumulative_sum_of_min_plus_max = (cumulative_sum_of_min_plus_max + sum_for_current_window) % MODULO

      // --- Slide the window: Remove A_list_items[window_start_index] ---
      element_leaving_window = A_list_items[window_start_index]

      // If the leaving element was the maximum, remove it from `max_val_deque`.
      IF max_val_deque IS NOT EMPTY AND element_leaving_window == max_val_deque.PEEK_FRONT() THEN
        max_val_deque.POP_FROM_FRONT()
      ENDIF

      // If the leaving element was the minimum, remove it from `min_val_deque`.
      IF min_val_deque IS NOT EMPTY AND element_leaving_window == min_val_deque.PEEK_FRONT() THEN
        min_val_deque.POP_FROM_FRONT()
      ENDIF

      // Advance the window's start.
      window_start_index = window_start_index + 1
    ENDIF
  ENDFOR

  // Ensure the final result is non-negative after modulo operations.
  final_result = (cumulative_sum_of_min_plus_max + MODULO) % MODULO
  RETURN final_result
ENDFUNCTION
```

### Pseudocode for Sliding Window Maximum.py
```pseudocode
// Assumes Deque data structure with operations:
//   ADD_TO_REAR (append/add_last), REMOVE_FROM_REAR (pop_last),
//   POP_FROM_FRONT (pop_first), PEEK_FRONT, PEEK_REAR, IS_EMPTY.

FUNCTION find_sliding_window_maximums(A_input_array, B_window_size_k):
  n_array_length = length of A_input_array
  list_of_window_maximums = empty list

  // Handle edge cases: window size invalid or larger than array length.
  IF n_array_length == 0 OR B_window_size_k <= 0 OR B_window_size_k > n_array_length THEN
    RETURN list_of_window_maximums // Or error, depending on problem specification
  ENDIF

  // `window_deque` will store elements (or their indices) from the current window.
  // It is maintained such that elements are in decreasing order, so the element
  // at the front of the deque is always the maximum in the current window.
  // The Python code stores actual values from A_input_array in the deque.
  window_deque = new Deque()

  current_window_start_index = 0

  // Iterate through the array with `current_window_end_index` marking the end of the sliding window.
  FOR current_window_end_index FROM 0 TO n_array_length - 1:
    element_entering_window = A_input_array[current_window_end_index]

    // Step 1: Maintain the deque's property.
    // Remove elements from the rear of the deque that are smaller than `element_entering_window`.
    // These smaller elements cannot be the maximum if `element_entering_window` is part of the window.
    WHILE window_deque IS NOT EMPTY AND window_deque.PEEK_REAR() < element_entering_window:
      window_deque.REMOVE_FROM_REAR()
    ENDWHILE

    // Step 2: Add the `element_entering_window` to the rear of the deque.
    window_deque.ADD_TO_REAR(element_entering_window)

    // Step 3: Once the window has reached its full size `B_window_size_k`,
    // the maximum for this window can be recorded, and the window needs to slide.
    length_of_current_window = current_window_end_index - current_window_start_index + 1
    IF length_of_current_window == B_window_size_k THEN
      // The maximum element in the current window is at the front of the deque.
      ADD window_deque.PEEK_FRONT() TO list_of_window_maximums

      // Slide the window: The element `A_input_array[current_window_start_index]` is now leaving the window.
      // If this leaving element was the maximum (i.e., it's at the front of the deque), remove it from deque.
      IF window_deque IS NOT EMPTY AND window_deque.PEEK_FRONT() == A_input_array[current_window_start_index] THEN
        window_deque.POP_FROM_FRONT()
      ENDIF

      // Advance the start of the window for the next iteration.
      current_window_start_index = current_window_start_index + 1
    ENDIF
  ENDFOR

  RETURN list_of_window_maximums
ENDFUNCTION
```

### Pseudocode for Maximum Frequency stack.py
```pseudocode
// This describes a Frequency Stack (or Max Frequency Stack) data structure.
// It supports push and pop. Pop removes the element with the highest frequency.
// If multiple elements have the same highest frequency, the one pushed most recently is popped.

CLASS MostFrequentStack:
  ATTRIBUTES:
    // `element_to_frequency_map`: Stores how many times each element `val` is currently in the stack.
    //   Example: {element_value: count}
    element_to_frequency_map: Dictionary

    // `current_max_frequency`: Tracks the highest frequency any element has reached so far.
    current_max_frequency: Integer

    // `frequency_to_stacks_map`: Maps a frequency count to a list (acting as a stack)
    // of elements that currently have that frequency. Elements are added to the end of the list (pushed).
    // When popping for tie-breaking (same max frequency), element is popped from the end of this list.
    //   Example: {frequency_value: [element1_at_this_freq, element2_at_this_freq, ...]}
    frequency_to_stacks_map: Dictionary (where values are Lists/Stacks)

  // Constructor
  CONSTRUCTOR():
    this.element_to_frequency_map = new empty Dictionary
    this.current_max_frequency = 0
    this.frequency_to_stacks_map = new empty Dictionary
  ENDFUNCTION

  // Pushes `val_to_insert` onto the stack.
  FUNCTION push_element(val_to_insert):
    // 1. Update the frequency of `val_to_insert`.
    new_frequency = GET_VALUE_OR_DEFAULT(this.element_to_frequency_map, val_to_insert, 0) + 1
    this.element_to_frequency_map[val_to_insert] = new_frequency

    // 2. Update `current_max_frequency` if this new frequency is higher.
    IF new_frequency > this.current_max_frequency THEN
      this.current_max_frequency = new_frequency
    ENDIF

    // 3. Ensure there's a stack (list) for this `new_frequency` level.
    IF new_frequency IS NOT A KEY IN this.frequency_to_stacks_map THEN
      this.frequency_to_stacks_map[new_frequency] = new empty List
    ENDIF

    // 4. Add `val_to_insert` to the stack corresponding to its `new_frequency`.
    //    This maintains the LIFO order for elements with the same frequency.
    ADD val_to_insert TO END of this.frequency_to_stacks_map[new_frequency]

    // The problem often specifies push returns -1 or is void.
    RETURN -1
  ENDFUNCTION

  // Pops and returns the most frequent element.
  // If there's a tie in frequency, returns the element that was pushed most recently among them.
  FUNCTION pop_most_frequent_element():
    // Check if the stack structure is empty (e.g., current_max_frequency is 0 or no stack for it).
    IF this.current_max_frequency == 0 OR
       (NOT KEY_EXISTS(this.frequency_to_stacks_map, this.current_max_frequency)) OR
       (IS_EMPTY_LIST(this.frequency_to_stacks_map[this.current_max_frequency])) THEN
      RETURN -1 // Indicates stack is empty or no elements at max frequency.
    ENDIF

    // 1. Get the stack of elements that currently have the `current_max_frequency`.
    stack_at_max_frequency = this.frequency_to_stacks_map[this.current_max_frequency]

    // 2. Pop the element from this stack (most recently added with this frequency).
    element_popped = REMOVE_LAST_ELEMENT from stack_at_max_frequency

    // 3. Decrement the frequency of the popped element in `element_to_frequency_map`.
    this.element_to_frequency_map[element_popped] = this.element_to_frequency_map[element_popped] - 1

    // 4. If the `stack_at_max_frequency` becomes empty after popping:
    IF IS_EMPTY_LIST(stack_at_max_frequency) THEN
      // Remove this frequency level from `frequency_to_stacks_map`.
      REMOVE_KEY this.current_max_frequency FROM this.frequency_to_stacks_map
      // Decrement `current_max_frequency`.
      // This is safe if there was at least one element with frequency (current_max_frequency - 1).
      // If not, current_max_frequency might need more complex recalculation (e.g. find new max key in map).
      // Python code simply decrements; this pseudocode matches that.
      this.current_max_frequency = this.current_max_frequency - 1
      // Ensure it doesn't go below 0 if stack becomes completely empty.
      IF this.current_max_frequency < 0 THEN this.current_max_frequency = 0 ENDIF
    ENDIF

    RETURN element_popped
  ENDFUNCTION
ENDCLASS

// Main Solution class that uses MostFrequentStack
CLASS Solution:
  FUNCTION solve_operations_on_freq_stack(A_list_of_operations):
    freq_stack = new MostFrequentStack()
    operation_results_list = empty list

    FOR EACH operation_data IN A_list_of_operations:
      operation_type = operation_data[0]
      value_parameter = operation_data[1] // Value parameter for the operation

      result_of_op = 0
      IF operation_type == 1 THEN // Push operation
        result_of_op = freq_stack.push_element(value_parameter)
      ELSE // Pop operation (assuming any other type means pop)
        result_of_op = freq_stack.pop_most_frequent_element()
      ENDIF
      ADD result_of_op TO operation_results_list
    ENDFOR

    RETURN operation_results_list
  ENDFUNCTION
ENDCLASS
```
