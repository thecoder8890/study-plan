# Advanced/Hashing - I

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [A3.py](A3.py)
- [A4.py](A4.py)
- [A5.py](A5.py)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)
- [HW3.py](HW3.py)
- [Hashing_March.pdf](Hashing_March.pdf)

## Description

This section introduces Hashing as a technique for efficient data storage and retrieval. It covers hash functions, collision resolution strategies, and applications of hash tables (hash maps, dictionaries, sets) in solving various problems.

### Pseudocode for A1.py
```pseudocode
FUNCTION longestConsecutiveSequence(A_integer_list):
  num_elements_in_list = length of A_integer_list

  // If the input list is empty, the longest consecutive sequence length is 0.
  IF num_elements_in_list == 0 THEN
    RETURN 0
  ENDIF

  // Step 1: Convert the list to a set for efficient O(1) average time complexity
  // for checking existence of elements. This also implicitly handles duplicates.
  element_set = new Set()
  FOR EACH num IN A_integer_list:
    ADD num TO element_set
  ENDFOR

  max_len_found = 0
  // Python code initializes max_len = 1. If list is not empty, min length is 1.
  // So, if num_elements_in_list > 0, max_len_found can start at 1.
  // If the loop doesn't find any sequence longer than 1, it'll return 1.
  // Initializing to 0 and then setting to 1 if list not empty is also fine.
  // Python's `max_len = 1` combined with initial `if len(A)==0: return 0` is correct.
  // Let's follow that structure:
  IF num_elements_in_list > 0 THEN
      max_len_found = 1
  ELSE
      max_len_found = 0 // Should be caught by the first IF though.
  ENDIF


  // Step 2: Iterate through each unique number in the set.
  FOR EACH num_from_set IN element_set:
    // Check if `num_from_set - 1` exists in the set.
    // If it does not exist, then `num_from_set` is a potential start of a new consecutive sequence.
    // This check ensures each sequence is processed only once, starting from its smallest element.
    IF (num_from_set - 1) IS NOT IN element_set THEN
      current_sequence_start_num = num_from_set
      current_sequence_length = 1

      // Step 3: Try to extend the sequence from `current_sequence_start_num`.
      // Check if (current_sequence_start_num + 1), (current_sequence_start_num + 2), etc., exist in the set.
      WHILE (current_sequence_start_num + 1) IS IN element_set:
        current_sequence_start_num = current_sequence_start_num + 1
        current_sequence_length = current_sequence_length + 1
      ENDWHILE

      // Update the overall maximum length found so far.
      IF current_sequence_length > max_len_found THEN
        max_len_found = current_sequence_length
      ENDIF
    ENDIF
  ENDFOR

  RETURN max_len_found
ENDFUNCTION
```

### Pseudocode for A3.py
```pseudocode
FUNCTION check_for_subarray_with_zero_sum(A_list_of_integers):
  // Initialize the running sum of elements.
  running_sum = 0

  // Create a set to store the prefix sums encountered so far.
  // Add 0 to the set initially. This is a crucial step:
  // - If a prefix sum `P[i]` itself becomes 0, it means the subarray A[0...i] sums to 0.
  //   Checking `0 IN prefix_sum_set` will detect this.
  // - If `P[i] == P[j]` for `i > j`, then the subarray A[j+1...i] sums to 0.
  //   This is detected when the later `P[i]` is found in the set (as it was added when it was `P[j]`).
  prefix_sum_set = new Set()
  ADD 0 TO prefix_sum_set

  // Iterate through each number in the input list.
  FOR EACH number IN A_list_of_integers:
    // Update the running sum.
    running_sum = running_sum + number

    // Check if the current `running_sum` has been seen before.
    IF running_sum IS PRESENT IN prefix_sum_set THEN
      // If `running_sum` is already in the set, it means either:
      // 1. `running_sum` is 0 (and 0 was initially added), so subarray from start sums to 0.
      // 2. `running_sum` was a prefix sum at an earlier index `j`, meaning the
      //    subarray between that earlier index and the current index sums to 0.
      RETURN 1 // Represents true (a subarray with sum zero exists)
    ELSE
      // If this `running_sum` is new, add it to the set of encountered prefix sums.
      ADD running_sum TO prefix_sum_set
    ENDIF
  ENDFOR

  // If the loop completes and no such prefix sum condition was met,
  // then no subarray with a sum of zero exists.
  RETURN 0 // Represents false
ENDFUNCTION
```

### Pseudocode for HW1.py
```pseudocode
// Assumes Counter-like functionality for frequency mapping.
// Operations: GET_FREQUENCY(map, key), KEY_EXISTS(map, key), DELETE_KEY(map, key).

FUNCTION solve_relative_sort(A_main_list, B_preference_list):
  // `result_ordered_part` will store elements from A_main_list that are also in B_preference_list,
  // in the order they appear in B_preference_list, maintaining their original frequencies from A_main_list.
  result_ordered_part = empty list

  // Step 1: Create a frequency map of all elements in A_main_list.
  freq_map_of_A = new FrequencyMap()
  FOR EACH element IN A_main_list:
    INCREMENT_FREQUENCY(freq_map_of_A, element)
  ENDFOR

  // Step 2: Build the first part of the result based on B_preference_list.
  FOR EACH element_from_B IN B_preference_list:
    IF KEY_EXISTS(freq_map_of_A, element_from_B) THEN
      count = GET_FREQUENCY(freq_map_of_A, element_from_B)
      // Append all occurrences of this element to the result.
      FOR _ FROM 1 TO count:
        ADD element_from_B TO result_ordered_part
      ENDFOR
      // Remove this element from the frequency map as it has been processed.
      DELETE_KEY(freq_map_of_A, element_from_B)
    ENDIF
  ENDFOR

  // Step 3: Collect remaining elements from A_main_list that were not in B_preference_list.
  // These are elements still present in freq_map_of_A.
  remaining_elements_for_sorting = empty list

  // The Python code iterates through the original A_main_list to pick up remaining elements.
  // A temporary set `distinct_rem_added` ensures each distinct remaining element's full count
  // is added only once from its first appearance in A_main_list during this pass.
  // This preserves the relative order of first appearances of distinct remaining items before sorting.
  // However, since `rem.sort()` is called, this initial order doesn't matter for the final `rem` part.
  // A simpler way to collect remaining is to iterate through keys of `freq_map_of_A`.
  // Python code does:
  //   rem = []
  //   for n in A: (original A)
  //     if n in aset: (updated freq_map_of_A)
  //       rem.extend([n]*aset[n])
  //       del aset[n] // Important: delete after adding all occurrences
  //   rem.sort()
  // This correctly collects all occurrences of remaining distinct elements.

  // Pseudocode reflecting Python's collection of remaining items:
  temp_list_of_remaining = empty list
  // Create a list of items to iterate over from freq_map_of_A to avoid issues with deleting during iteration.
  // Or, iterate original A and check against modified freq_map_of_A.
  // Python's `for n in A: if n in aset:` is safe because `aset` is modified.
  // Let's simulate the effect of iterating original A with checks against the modified map:

  // To accurately model Python's behavior for collecting remaining elements:
  // We need to ensure that we add elements to `temp_list_of_remaining` based on their presence
  // in the *current state* of `freq_map_of_A`.
  // A list of distinct elements remaining in `freq_map_of_A` can be obtained.

  distinct_remaining_elements = get_keys_from(freq_map_of_A)
  FOR EACH element_val IN distinct_remaining_elements:
    count = GET_FREQUENCY(freq_map_of_A, element_val) // Get its original total count
    FOR _ FROM 1 TO count:
      ADD element_val TO temp_list_of_remaining
    ENDFOR
  ENDFOR
  // The above collects all items correctly. Python's iteration of original A with `del aset[n]`
  // effectively does this by ensuring each distinct remaining number's items are added once.

  SORT temp_list_of_remaining ASCENDING

  // Step 4: Concatenate the ordered part with the sorted remaining part.
  final_result_list = result_ordered_part + temp_list_of_remaining

  RETURN final_result_list
ENDFUNCTION
```

### Pseudocode for HW2.py
```pseudocode
FUNCTION is_number_colorful(A_input_num):
  // Step 1: Convert the input number into a list of its digits.
  // Example: If A_input_num = 236, digits_list becomes [2, 3, 6].
  digits_list = empty list
  temp_num_for_digits = A_input_num

  IF temp_num_for_digits == 0 THEN
    ADD 0 TO digits_list // Handle the case of 0 if it's a valid input.
  ENDIF

  WHILE temp_num_for_digits > 0:
    digit = temp_num_for_digits MOD 10
    INSERT digit AT BEGINNING of digits_list // Prepend to maintain order
    temp_num_for_digits = temp_num_for_digits / 10 // Integer division
  ENDWHILE

  // Step 2: Initialize a set to keep track of products of contiguous subsequences of digits.
  // This set will be used to check for uniqueness of these products.
  products_seen_set = new Set()

  num_of_digits = length of digits_list

  // Step 3: Generate all contiguous subsequences of digits, calculate their products,
  // and check for uniqueness.
  FOR i_start_index FROM 0 TO num_of_digits - 1:
    current_product_for_subsequence = 1 // Reset product for each new starting digit

    FOR j_end_index FROM i_start_index TO num_of_digits - 1:
      // Extend the current subsequence by including digits_list[j_end_index]
      // and update the product.
      current_product_for_subsequence = current_product_for_subsequence * digits_list[j_end_index]

      // Check if this product has already been seen.
      IF current_product_for_subsequence IS PRESENT IN products_seen_set THEN
        // If product is already in the set, it's a duplicate. Number is not colorful.
        RETURN 0 // Represents false (not colorful)
      ELSE
        // If product is new, add it to the set.
        ADD current_product_for_subsequence TO products_seen_set
      ENDIF
    ENDFOR
  ENDFOR

  // If all contiguous subsequence products are unique, the number is colorful.
  RETURN 1 // Represents true (is colorful)
ENDFUNCTION
```

### Pseudocode for A4.py
```pseudocode
FUNCTION find_longest_subarray_sum_zero(A_list):
  n = length of A_list

  // `prefix_sum_map` will store {prefix_sum_value: first_occurrence_index}.
  prefix_sum_map = new empty Dictionary/HashMap

  current_running_sum = 0
  max_len_of_zero_sum_subarray = 0
  // `result_ans_subarray` will store the actual subarray.
  result_ans_subarray = empty_list

  // Initialize map with sum 0 at index -1. This correctly handles subarrays
  // starting from index 0 that sum to 0. For such a subarray ending at index `i`,
  // its length is `i - (-1) = i + 1`.
  prefix_sum_map[0] = -1

  FOR current_idx FROM 0 TO n - 1:
    current_running_sum = current_running_sum + A_list[current_idx]

    IF current_running_sum IS PRESENT AS A KEY IN prefix_sum_map THEN
      // If current_running_sum has been seen before at `prev_idx_with_same_sum`,
      // it means the subarray A_list[prev_idx_with_same_sum + 1 ... current_idx] sums to 0.
      prev_idx_with_same_sum = prefix_sum_map[current_running_sum]
      length_of_current_zero_sum_subarray = current_idx - prev_idx_with_same_sum

      IF length_of_current_zero_sum_subarray > max_len_of_zero_sum_subarray THEN
        max_len_of_zero_sum_subarray = length_of_current_zero_sum_subarray
        // Extract the subarray from (prev_idx_with_same_sum + 1) to current_idx (inclusive).
        result_ans_subarray = sublist of A_list from index (prev_idx_with_same_sum + 1) to current_idx
      ENDIF
    ELSE
      // This is the first time this `current_running_sum` is encountered.
      // Store it in the map with its current index.
      prefix_sum_map[current_running_sum] = current_idx
    ENDIF
  ENDFOR

  RETURN result_ans_subarray
ENDFUNCTION
```

### Pseudocode for HW3.py
```pseudocode
// Assumes a Counter or FrequencyMap data structure/utility is available.
// Operations:
//   - Initialize from string: `new FrequencyMap from A_string`
//   - Iterate items: `FOR EACH (character, frequency) IN ITEMS_OF(frequency_map)`

FUNCTION can_permutation_form_palindrome(A_string):
  // Step 1: Count the frequency of each character in the input string.
  character_counts = new FrequencyMap from A_string

  // Step 2: Count how many characters have an odd frequency.
  number_of_odd_frequency_chars = 0

  FOR EACH (char_key, char_frequency_value) IN ITEMS_OF(character_counts):
    IF char_frequency_value MOD 2 == 1 THEN // Check if frequency is odd
      number_of_odd_frequency_chars = number_of_odd_frequency_chars + 1
    ENDIF

    // Optimization: If at any point we find more than one character
    // with an odd frequency, a palindrome cannot be formed.
    IF number_of_odd_frequency_chars > 1 THEN
      RETURN 0 // Represents false (cannot form a palindrome)
    ENDIF
  ENDFOR

  // If the loop completes, it means the number of characters with odd frequencies
  // is either 0 (all characters appear an even number of times) or 1 (exactly one
  // character appears an odd number of times). In both these cases, a palindrome can be formed.
  RETURN 1 // Represents true (can form a palindrome)
ENDFUNCTION
```

### Pseudocode for A5.py
```pseudocode
FUNCTION count_distinct_elements_in_sliding_windows(A_input_list, B_window_length):
  n_list_size = length of A_input_list

  // Handle edge cases where valid windows cannot be formed.
  IF B_window_length > n_list_size OR B_window_length <= 0 THEN
    RETURN empty_list
  ENDIF

  // `frequency_map_in_window` will store element -> count of its occurrences in the current window.
  frequency_map_in_window = new empty Dictionary/HashMap

  // `distinct_counts_per_window_list` will store the result.
  distinct_counts_per_window_list = empty list

  window_start_pointer = 0

  // Iterate through the input list with `window_end_pointer` to define the end of the sliding window.
  FOR window_end_pointer FROM 0 TO n_list_size - 1:
    // Add the element entering the window (at `window_end_pointer`) to the frequency map.
    element_entering = A_input_list[window_end_pointer]
    IF element_entering IS NOT A KEY IN frequency_map_in_window THEN
      frequency_map_in_window[element_entering] = 0 // Initialize if new
    ENDIF
    frequency_map_in_window[element_entering] = frequency_map_in_window[element_entering] + 1

    // Check if the current window has reached the specified `B_window_length`.
    current_window_actual_length = window_end_pointer - window_start_pointer + 1
    IF current_window_actual_length == B_window_length THEN
      // The window is now of size B. The number of distinct elements in it
      // is the number of unique keys currently in `frequency_map_in_window`.
      ADD (count of unique keys in frequency_map_in_window) TO distinct_counts_per_window_list

      // Prepare to slide the window: Remove the element leaving the window from the left.
      element_leaving = A_input_list[window_start_pointer]
      // Decrement its frequency.
      frequency_map_in_window[element_leaving] = frequency_map_in_window[element_leaving] - 1

      // If the frequency of the leaving element becomes 0, remove it from the map
      // as it's no longer present in the window.
      IF frequency_map_in_window[element_leaving] == 0 THEN
        REMOVE KEY element_leaving FROM frequency_map_in_window
      ENDIF

      // Slide the window forward by incrementing the start pointer.
      window_start_pointer = window_start_pointer + 1
    ENDIF
  ENDFOR

  RETURN distinct_counts_per_window_list
ENDFUNCTION
```

### Pseudocode for A2.py
```pseudocode
FUNCTION find_minimum_distance_between_identical_elements(A_list):
  list_length = length of A_list

  // If list is empty or has one element, no duplicates possible.
  IF list_length < 2 THEN
    RETURN -1
  ENDIF

  // `element_to_last_index_map` stores the last seen index of each element.
  // Key: element_value, Value: index_in_A_list
  element_to_last_index_map = new empty Dictionary/HashMap

  // Initialize `min_distance_so_far`. A value greater than any possible distance
  // (e.g., list_length) ensures any found distance will be smaller.
  min_distance_so_far = list_length

  // Iterate through the list with index `current_idx` and element `current_element`.
  FOR current_idx FROM 0 TO list_length - 1:
    current_element = A_list[current_idx]

    // Check if `current_element` has been encountered before.
    IF current_element IS A KEY IN element_to_last_index_map THEN
      // Element is a duplicate. Calculate distance to its previous occurrence.
      previous_occurrence_idx = element_to_last_index_map[current_element]
      distance_to_previous = current_idx - previous_occurrence_idx

      // Update `min_distance_so_far` if this new distance is smaller.
      IF distance_to_previous < min_distance_so_far THEN
        min_distance_so_far = distance_to_previous
      ENDIF
    ENDIF

    // Update (or add) the last seen index for `current_element`.
    element_to_last_index_map[current_element] = current_idx
  ENDFOR

  // After iterating through all elements:
  // If `min_distance_so_far` is still its initial large value (list_length),
  // it means no duplicate elements were found.
  IF min_distance_so_far == list_length THEN
    RETURN -1 // No duplicates found
  ELSE
    RETURN min_distance_so_far // Return the minimum distance found
  ENDIF
ENDFUNCTION
```
