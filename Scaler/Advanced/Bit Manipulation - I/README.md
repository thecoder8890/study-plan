# Advanced/Bit Manipulation - I

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [A3.py](A3.py)
- [A4.py](A4.py)
- [Advance_Bit_Manipulation_I_.pdf](Advance_Bit_Manipulation_I_.pdf)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)
- [HW3.py](HW3.py)

## Description

This section introduces fundamental bit manipulation techniques, including operations on bits, understanding binary representations, and solving problems by leveraging bitwise logic at an advanced level.

### Pseudocode for A1.py
```pseudocode
FUNCTION singleNumber(A_list_of_integers):
  // Initialize a variable to store the XOR sum.
  // XORing with 0 does not change the other number (x ^ 0 = x).
  xor_accumulator = 0

  // Iterate through each number in the input list
  FOR EACH number IN A_list_of_integers:
    // Apply XOR operation between the accumulator and the current number.
    // If a number appears twice, it will be XORed with itself, resulting in 0 (x ^ x = 0).
    // The number that appears only once will remain.
    xor_accumulator = xor_accumulator XOR number
  ENDFOR

  // The final value in xor_accumulator is the single number that appears once.
  RETURN xor_accumulator
ENDFUNCTION
```

### Pseudocode for A2.py
```pseudocode
FUNCTION singleNumber_others_thrice(A_integer_list):
  // Assuming integers are typically represented within a fixed number of bits (e.g., 32 bits for standard integers).
  NUMBER_OF_BITS_IN_INTEGER = 32 // Example for 32-bit integers

  the_single_number_result = 0

  // Iterate through each bit position, from the least significant (0) to the most significant.
  FOR bit_idx FROM 0 TO NUMBER_OF_BITS_IN_INTEGER - 1:
    sum_of_bits_at_current_position = 0

    // For each number in the input list, check the value of its bit at `bit_idx`.
    FOR EACH num IN A_integer_list:
      // Check if the `bit_idx`-th bit of `num` is set (is 1).
      // This can be done by: (num RIGHT_SHIFT bit_idx) AND 1
      IF (num AND (1 LEFT_SHIFT bit_idx)) != 0 THEN // Checks if the bit_idx-th bit is 1
        sum_of_bits_at_current_position = sum_of_bits_at_current_position + 1
      ENDIF
    ENDFOR

    // If every number except one appears thrice, the sum of bits at any position `bit_idx`
    // will be `3k` or `3k + 1`.
    // If `sum_of_bits_at_current_position % 3` is 1 (or non-zero),
    // it means the single number has its `bit_idx`-th bit set to 1.
    IF sum_of_bits_at_current_position % 3 != 0 THEN
      // Set the `bit_idx`-th bit in our result.
      the_single_number_result = the_single_number_result OR (1 LEFT_SHIFT bit_idx)
    ENDIF
  ENDFOR

  RETURN the_single_number_result
ENDFUNCTION
```

### Pseudocode for HW2.py
```pseudocode
FUNCTION findMinXor(A_integer_list):
  n = length of A_integer_list

  // If the list has fewer than two elements, a pair cannot be formed.
  // The behavior for this case should be defined by problem constraints.
  // Assuming n >= 2 for typical scenarios.
  IF n < 2 THEN
    RETURN some_error_indicator_or_default // e.g., positive infinity, or raise error
  ENDIF

  // Step 1: Sort the input list.
  // Sorting helps because elements with potentially small XOR values (i.e., numbers
  // that are numerically close and might share many common higher bits)
  // will be adjacent after sorting.
  SORT A_integer_list ASCENDING

  // Step 2: Initialize a variable to keep track of the minimum XOR value found.
  // Set it initially to a very large number (positive infinity).
  min_xor_so_far = positive_infinity

  // Step 3: Iterate through the sorted list and compute XOR of adjacent elements.
  // The smallest XOR value among these adjacent pairs will be the overall minimum.
  FOR i FROM 0 TO n - 2: // Iterate up to the second-to-last element
    element1 = A_integer_list[i]
    element2 = A_integer_list[i+1]

    current_xor_value = element1 XOR element2

    // Update min_xor_so_far if the current pair's XOR value is smaller.
    IF current_xor_value < min_xor_so_far THEN
      min_xor_so_far = current_xor_value
    ENDIF
  ENDFOR

  RETURN min_xor_so_far
ENDFUNCTION
```

### Pseudocode for HW3.py
```pseudocode
FUNCTION sum_of_bit_differences_for_all_pairs(A_integer_list):
  n = length of A_integer_list
  IF n == 0 THEN
    RETURN 0
  ENDIF

  total_sum_of_differences = 0
  MODULO_CONSTANT = 10^9 + 7

  // Create a mutable copy of the input list, as elements will be modified (right-shifted).
  // In Python, lists are mutable, so A itself is modified if passed directly.
  // For clarity in pseudocode, let's use a working copy.
  current_numbers_being_processed = copy of A_integer_list

  // Assuming integers are represented by a fixed number of bits (e.g., 32)
  NUMBER_OF_BITS = 32

  // Iterate through each bit position (from LSB to MSB)
  FOR bit_position_idx FROM 0 TO NUMBER_OF_BITS - 1:
    // For the current effective bit_position_idx (due to right shifts),
    // count how many numbers have their current LSB set to 1.
    count_with_LSB_set = 0

    FOR j FROM 0 TO n - 1:
      // Check the LSB of the j-th number in its current (possibly shifted) state
      IF (current_numbers_being_processed[j] AND 1) == 1 THEN
        count_with_LSB_set = count_with_LSB_set + 1
      ENDIF
      // Right-shift the j-th number to prepare its next bit for the LSB position
      // in the next outer loop iteration (for next bit_position_idx).
      current_numbers_being_processed[j] = current_numbers_being_processed[j] RIGHT_SHIFT 1
    ENDFOR

    // For the original bit_position_idx:
    // `count_with_LSB_set` numbers had this bit as 1.
    // `n - count_with_LSB_set` numbers had this bit as 0.
    // Any pair of numbers where one has this bit as 1 and the other has 0 will contribute 1 to the difference at this bit position.
    // Number of such differing pairs = count_with_LSB_set * (n - count_with_LSB_set).
    // Since the problem implies sum over all ordered pairs (i,j) where A[i] and A[j] differ,
    // we multiply by 2 (for (x,y) and (y,x) contributing).
    contribution_at_this_bit = count_with_LSB_set * (n - count_with_LSB_set) * 2

    total_sum_of_differences = total_sum_of_differences + contribution_at_this_bit
    // Python code applies modulo at the very end. If intermediate sum can overflow,
    // modulo should be applied within the loop. Assuming sum fits in standard integer types before final modulo.
  ENDFOR

  RETURN total_sum_of_differences % MODULO_CONSTANT
ENDFUNCTION
```

### Pseudocode for A3.py
```pseudocode
FUNCTION numSetBits(A_input_integer):
  // Initialize a counter that will store the number of set bits (bits with value 1).
  count_of_set_bits = 0

  // Create a mutable variable to work with, as we will be modifying it.
  // In Python, integers are immutable, so `A = A >> 1` creates a new integer.
  // For pseudocode clarity, using a temporary variable.
  working_integer = A_input_integer

  // Loop as long as the working_integer is greater than 0.
  // This ensures all bits are checked for a non-negative integer.
  WHILE working_integer > 0:
    // Check the least significant bit (LSB) of working_integer.
    // (working_integer AND 1) results in 1 if LSB is 1, and 0 if LSB is 0.
    IF (working_integer AND 1) == 1 THEN
      // If LSB is 1, increment the count of set bits.
      count_of_set_bits = count_of_set_bits + 1
    ENDIF

    // Right-shift the working_integer by 1 bit.
    // This discards the LSB that was just checked, and moves the next bit
    // into the LSB position for the next iteration.
    working_integer = working_integer RIGHT_SHIFT 1
  ENDWHILE

  // Return the total count of set bits found.
  RETURN count_of_set_bits
ENDFUNCTION
```

### Pseudocode for A4.py
```pseudocode
FUNCTION addBinaryStrings(binary_str_A, binary_str_B):
  // Pointers to iterate from the end (LSB) of both strings
  idx_A = length of binary_str_A - 1
  idx_B = length of binary_str_B - 1

  current_carry = 0
  result_chars_list = empty list // To store bits of the sum, will be reversed at the end

  // Loop as long as there are digits in either string to process, or a carry exists
  WHILE idx_A >= 0 OR idx_B >= 0 OR current_carry > 0:
    sum_of_bits_for_current_pos = current_carry

    // Add bit from binary_str_A if available
    IF idx_A >= 0 THEN
      sum_of_bits_for_current_pos = sum_of_bits_for_current_pos + integer_value_of(binary_str_A[idx_A])
      idx_A = idx_A - 1 // Move to the next bit to the left
    ENDIF

    // Add bit from binary_str_B if available
    IF idx_B >= 0 THEN
      sum_of_bits_for_current_pos = sum_of_bits_for_current_pos + integer_value_of(binary_str_B[idx_B])
      idx_B = idx_B - 1 // Move to the next bit to the left
    ENDIF

    // Determine the bit for the current position in the result string
    // (sum_of_bits_for_current_pos will be 0, 1, 2, or 3)
    current_result_bit = sum_of_bits_for_current_pos MOD 2

    // Determine the new carry for the next (more significant) bit position
    current_carry = sum_of_bits_for_current_pos / 2 // Integer division

    // Prepend the current_result_bit to the result list (or append and reverse later)
    // Python code appends then reverses.
    ADD character_representation_of(current_result_bit) TO result_chars_list
  ENDWHILE

  // The result_chars_list has bits in LSB-first order due to appending. Reverse it.
  // If result_chars_list is empty (e.g., A="", B=""), result should be "0" if that's the convention.
  // Python returns "" for A="", B="". If A="0", B="0", Python returns "0".
  IF length of result_chars_list == 0 THEN
      // This case happens if both input strings were empty and carry was 0 initially.
      // Typically, sum of two empty binary numbers could be "0".
      // Python's behavior for A="", B="" is to return "".
      // If A="0", B="0", result_chars_list becomes ['0'], reversed is ['0'], joined is "0".
      RETURN "" // Matching Python's behavior for A="", B=""
  ENDIF

  REVERSE result_chars_list
  final_result_string = JOIN characters in result_chars_list

  RETURN final_result_string
ENDFUNCTION
```

### Pseudocode for HW1.py
```pseudocode
FUNCTION solve_xor_sum_parity_check(A_list_of_integers):
  // Initialize an accumulator for the XOR sum.
  // The identity element for XOR is 0 (x XOR 0 = x).
  xor_total = 0

  // Calculate the XOR sum of all elements in the list.
  FOR EACH integer_element IN A_list_of_integers:
    xor_total = xor_total XOR integer_element
  ENDFOR

  // Check if the final XOR sum is even or odd.
  // An integer is even if its least significant bit is 0.
  // An integer is odd if its least significant bit is 1.
  // (xor_total MOD 2 == 0) checks if it's even.
  IF xor_total MOD 2 == 0 THEN
    RETURN "Yes" // XOR sum is even
  ELSE
    RETURN "No"  // XOR sum is odd
  ENDIF
ENDFUNCTION
```
