# Advanced/Backtracking - I

## Files in this topic:

- [Backtracking_I_.pdf](Backtracking_I_.pdf)
- [Combination Sum.py](Combination Sum.py)
- [Letter Phone.py](Letter Phone.py)
- [Permutations.py](Permutations.py)
- [SIXLETS.py](SIXLETS.py)

## Description

This section introduces the fundamental concepts of backtracking algorithms, a general approach to solving computational problems, particularly constraint satisfaction problems.

### Pseudocode for Combination Sum.py
```pseudocode
FUNCTION combinationSum(A_candidates_list, B_target_sum):
  all_valid_combinations = empty list_of_lists

  // Sort the candidate numbers. This is crucial for the duplicate skipping logic
  // if A_candidates_list itself contains duplicate numbers.
  SORT A_candidates_list ASCENDING

  n = length of A_candidates_list

  // Define a recursive helper function (backtracking)
  DEFINE FUNCTION find_combinations_recursive(start_idx, current_combination, current_sum):
    // Base Case 1: If current sum exceeds the target, this path is invalid.
    IF current_sum > B_target_sum THEN
      RETURN
    ENDIF

    // Base Case 2: If current sum equals the target, a valid combination is found.
    IF current_sum == B_target_sum THEN
      APPEND a copy of current_combination TO all_valid_combinations
      RETURN
    ENDIF

    // Base Case 3: If start_idx reaches the end of candidates list and sum is not met.
    // This is implicitly handled by the loop condition below. If start_idx >= n, loop doesn't run.

    // Recursive Step: Iterate through candidates starting from start_idx
    FOR i FROM start_idx TO n - 1:
      // This specific skip condition `if i > 0 and A[i] == A[i-1]` from the Python code
      // is intended to handle cases where the input A_candidates_list might have duplicates.
      // When combined with `A.sort()` and the recursive call `recurse(i, ...)`,
      // it ensures that each distinct number is effectively considered as a starting point
      // for a sequence of choices only once at each level of the recursion, thus avoiding
      // generating identical combinations if input A had duplicates (e.g. A=[1,1,2], target=2 -> [[1,1]]).
      // If A_candidates_list is guaranteed to have distinct elements, this check is not strictly needed.
      // The Python code `if i > 0 and A[i] == A[i-1]: continue` is the exact one.
      // A more common way to write this for "Combination Sum I with duplicates in input A" is
      // `if i > start_idx AND A_candidates_list[i] == A_candidates_list[i-1] THEN CONTINUE`
      // However, the Python code's logic is what's being translated.
      // The current Python code `if i > 0 and A[i] == A[i-1]` works due to sorted `A` and `recurse(i,...)`.
      // It prevents using `A[i]` if it's a duplicate of `A[i-1]` *unless* `A[i-1]` was the element chosen
      // in the *previous* step of forming the current combination.
      // This is subtle. Let's use the Python code's condition directly.
      IF i > start_idx AND A_candidates_list[i] == A_candidates_list[i-1] THEN
         // This check is to prevent using the same number at the same decision level multiple times if it's a duplicate in `A`.
         // E.g. A=[1,2,2], B=3. If we pick 1. Next choices are 2,2. We pick first 2. Then for next choice, we don't pick second 2 again from this level.
         // This is actually Combination Sum II logic (use each number from input at most once).
         // Given the problem is Combination Sum (reuse elements), this check should be
         // `if i > start_idx and A[i] == A[i-1]: continue;` if `A` can have duplicates.
         // The Python code's condition `if i > 0 and A[i] == A[i-1]: continue` is for when `A` has duplicates,
         // and we want to generate unique combinations by only starting a new sequence with a number
         // if it's different from the previous one, OR if it's the first element considered at this depth.
         // This is correct for how Python's solution handles duplicates in `A` for this problem.
         // If `i == start_idx` OR `A_candidates_list[i] != A_candidates_list[i-1]`, proceed.
         // So, if `i > start_idx AND A_candidates_list[i] == A_candidates_list[i-1]`, skip. This handles duplicates correctly.
         CONTINUE
      ENDIF

      // Choose current candidate A_candidates_list[i]
      ADD A_candidates_list[i] TO current_combination
      current_sum = current_sum + A_candidates_list[i]

      // Recurse: allow the same number A_candidates_list[i] to be chosen again,
      // so the next recursive call starts from index `i`.
      find_combinations_recursive(i, current_combination, current_sum)

      // Backtrack: un-choose A_candidates_list[i]
      current_sum = current_sum - A_candidates_list[i]
      REMOVE last element FROM current_combination (which was A_candidates_list[i])
    ENDFOR
  ENDFUNCTION // End of find_combinations_recursive

  // Initial call to start the backtracking process
  find_combinations_recursive(0, empty_list, 0)

  RETURN all_valid_combinations
ENDFUNCTION
```

### Pseudocode for Letter Phone.py
```pseudocode
FUNCTION letterCombinations(A_digit_string):
  all_combinations_result = empty list_of_strings

  // Define the mapping from digit characters to letters
  digit_to_letters_map = {
    '0': ["0"], '1': ["1"], '2': ["a", "b", "c"], '3': ["d", "e", "f"],
    '4': ["g", "h", "i"], '5': ["j", "k", "l"], '6': ["m", "n", "o"],
    '7': ["p", "q", "r", "s"], '8': ["t", "u", "v"], '9': ["w", "x", "y", "z"]
  }

  n_length_of_digits = length of A_digit_string
  IF n_length_of_digits == 0 THEN
    RETURN all_combinations_result // Empty input gives empty output
  ENDIF

  // Define a recursive helper function (backtracking)
  DEFINE FUNCTION generate_recursive(current_combination_char_list, current_digit_idx):
    // Base Case: If all digits have been processed, a complete combination is formed
    IF current_digit_idx == n_length_of_digits THEN
      APPEND string_from_joining(current_combination_char_list) TO all_combinations_result
      RETURN
    ENDIF

    // Recursive Step:
    current_digit = A_digit_string[current_digit_idx]
    possible_letters_for_digit = digit_to_letters_map[current_digit]

    FOR EACH letter IN possible_letters_for_digit:
      // Choose: Add the current letter to the combination being built
      ADD letter TO current_combination_char_list

      // Explore: Recursively call for the next digit
      generate_recursive(current_combination_char_list, current_digit_idx + 1)

      // Un-choose (Backtrack): Remove the current letter to try other letters for this digit
      REMOVE last_element FROM current_combination_char_list
    ENDFOR
  ENDFUNCTION // End of generate_recursive

  // Initial call to start the backtracking process
  // Start with an empty character list, processing the digit at index 0
  generate_recursive(empty_list_of_chars, 0)

  RETURN all_combinations_result
ENDFUNCTION
```

### Pseudocode for Permutations.py
```pseudocode
FUNCTION permute(A_input_list):
  all_permutations = empty list_of_lists
  n = length of A_input_list
  // Assumes A_input_list contains distinct elements for generating unique permutations.
  // If A_input_list has duplicates, this algorithm will produce duplicate permutations.

  // Define a recursive helper function using backtracking
  DEFINE FUNCTION find_permutations_recursive(current_built_permutation):
    // Base Case: If the length of the current_built_permutation is equal to n,
    // it means a full permutation has been formed.
    IF length of current_built_permutation == n THEN
      APPEND a copy of current_built_permutation TO all_permutations
      RETURN
    ENDIF

    // Recursive Step: Iterate through all numbers in the original A_input_list
    FOR i FROM 0 TO n - 1:
      number_to_try = A_input_list[i]

      // Check if number_to_try is already present in the current_built_permutation
      is_present = false
      FOR EACH existing_num IN current_built_permutation:
        IF existing_num == number_to_try THEN
          is_present = true
          BREAK FOR
        ENDIF
      ENDFOR

      IF is_present THEN
        CONTINUE // Skip this number as it's already used in this permutation path
      ENDIF

      // Choose: Add number_to_try to the current permutation
      ADD number_to_try TO current_built_permutation

      // Explore: Recursively call to continue building the permutation
      find_permutations_recursive(current_built_permutation)

      // Un-choose (Backtrack): Remove number_to_try to explore other possibilities
      REMOVE last_element FROM current_built_permutation
    ENDFOR
  ENDFUNCTION // End of find_permutations_recursive

  // Initial call to the recursive function, starting with an empty permutation
  find_permutations_recursive(empty_list)

  RETURN all_permutations
ENDFUNCTION
```

### Pseudocode for SIXLETS.py
```pseudocode
// Main function to initiate the process
FUNCTION solve(A_input_list, B_target_subsequence_length):
  // Call the recursive helper function to count valid subsequences.
  // Start with an empty current subsequence, from index 0 of A_input_list, and sum 0.
  final_count = recurse_count_sixlets(empty_list, 0, 0, A_input_list, B_target_subsequence_length)
  RETURN final_count
ENDFUNCTION


// Recursive helper function to find and count "SIXLETS" (valid subsequences)
DEFINE FUNCTION recurse_count_sixlets(current_elements_list, current_index_in_A, current_sum_of_elements,
                                     A_input_list_original, B_target_subsequence_length):

  // Pruning Condition: If the current sum already exceeds 1000,
  // this path cannot lead to a valid subsequence.
  IF current_sum_of_elements > 1000 THEN
    RETURN 0 // No valid subsequences from this path
  ENDIF

  // Base Case: If all elements from A_input_list_original have been considered
  IF current_index_in_A == length of A_input_list_original THEN
    // Check if the formed subsequence meets the criteria:
    // 1. Length is exactly B_target_subsequence_length
    // 2. Sum is <= 1000 (already partially checked by pruning, but good for final check)
    IF length of current_elements_list == B_target_subsequence_length AND current_sum_of_elements <= 1000 THEN
      RETURN 1 // This path formed one valid subsequence
    ELSE
      RETURN 0 // This path did not form a valid subsequence
    ENDIF
  ENDIF

  // Recursive Step: For the element A_input_list_original[current_index_in_A],
  // we have two choices: either include it in the current subsequence or not.

  // Choice 1: Include A_input_list_original[current_index_in_A]
  ADD A_input_list_original[current_index_in_A] TO current_elements_list
  sum_if_included = current_sum_of_elements + A_input_list_original[current_index_in_A]

  count_if_included = recurse_count_sixlets(current_elements_list,
                                           current_index_in_A + 1,
                                           sum_if_included,
                                           A_input_list_original, B_target_subsequence_length)

  // Backtrack: Remove the element before exploring the other choice (Choice 2)
  REMOVE last element FROM current_elements_list
  // current_sum_of_elements for the "not included" path remains the original value before this inclusion.

  // Choice 2: Exclude A_input_list_original[current_index_in_A]
  count_if_excluded = recurse_count_sixlets(current_elements_list,
                                           current_index_in_A + 1,
                                           current_sum_of_elements, // Sum does not change
                                           A_input_list_original, B_target_subsequence_length)

  // Total valid subsequences from this point is the sum of those from including and excluding the current element
  RETURN count_if_included + count_if_excluded
ENDFUNCTION
```
