# Advanced/Bit Manipulation - II

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [Adv_Bit_manipulation_II_.pdf](Adv_Bit_manipulation_II_.pdf)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)
- [HW3.py](HW3.py)
- [HW4.py](HW4.py)

## Description

This section delves into more advanced bit manipulation techniques and problem-solving, often involving clever tricks, properties of XOR, and applications in optimizing solutions or solving complex logical problems.

### Pseudocode for A1.py
```pseudocode
FUNCTION find_two_unique_numbers_in_list(A_list_integers):
  // Step 1: Calculate the XOR sum of all elements in the list.
  // If there are two unique numbers (say X and Y) and all others appear twice,
  // this sum will be X XOR Y, because duplicate numbers cancel each other out (n XOR n = 0).
  xor_sum_X_Y = 0
  FOR EACH number IN A_list_integers:
    xor_sum_X_Y = xor_sum_X_Y XOR number
  ENDFOR

  // Step 2: Find any set bit in xor_sum_X_Y. This bit position is one where X and Y differ.
  // The Python code uses `mask = (x & x-1) ^ x` which isolates the rightmost set bit.
  // If xor_sum_X_Y is 0, it implies an issue (e.g., not exactly two unique numbers, or they are same).
  // Assuming valid input where xor_sum_X_Y will be non-zero.
  IF xor_sum_X_Y == 0 THEN
    // This case should ideally not happen if input guarantees two distinct unique numbers.
    // Return a default or error. Python code might proceed and result in [0,0].
    RETURN sorted_list_of([0, 0])
  ENDIF

  // Isolate the rightmost set bit of xor_sum_X_Y. This bit will serve as a mask.
  // Example: if xor_sum_X_Y = 6 (binary 110), rightmost_set_bit_mask = 2 (binary 010).
  rightmost_set_bit_mask = (xor_sum_X_Y AND (xor_sum_X_Y - 1)) XOR xor_sum_X_Y
  // An alternative to get rightmost set bit: xor_sum_X_Y AND (-xor_sum_X_Y) using two's complement.

  // Step 3: Partition the original numbers into two groups based on the rightmost_set_bit_mask.
  // One group will have this bit set, the other will not.
  // The two unique numbers X and Y will fall into different groups because they differ at this bit.
  // XORing all numbers in each group will isolate X in one group and Y in the other,
  // as duplicates within each group will cancel out.

  unique_number_1 = 0
  unique_number_2 = 0

  FOR EACH number IN A_list_integers:
    // Check if the differentiating bit (from rightmost_set_bit_mask) is set in the current number.
    IF (number AND rightmost_set_bit_mask) != 0 THEN // Bit is set
      unique_number_1 = unique_number_1 XOR number
    ELSE // Bit is not set
      unique_number_2 = unique_number_2 XOR number
    ENDIF
  ENDFOR

  // unique_number_1 and unique_number_2 now hold the two unique numbers.
  // Return them in sorted order.
  IF unique_number_1 < unique_number_2 THEN
    RETURN [unique_number_1, unique_number_2]
  ELSE
    RETURN [unique_number_2, unique_number_1]
  ENDIF
ENDFUNCTION
```

### Pseudocode for HW3.py
```pseudocode
// Helper function to determine `l` as in Python's `len(bin(A)) - 2`
// `l` represents the number of bits in A's binary form (e.g., A=4 (100) -> l=3; A=5 (101) -> l=3)
FUNCTION get_l_bit_length_python_style(num):
  IF num == 0 THEN RETURN 1 ENDIF // bin(0) is "0b0", len=3, l=1
  length = 0
  temp = num
  WHILE temp > 0:
    temp = temp RIGHT_SHIFT 1
    length = length + 1
  RETURN length
ENDFUNCTION

// Main function to construct the number
FUNCTION solve_construct_num_with_budget(A_template_num, B_op_budget):
  ans_num = 0
  current_budget = B_op_budget

  // Determine `l_bits` which is used as upper limit for bit indices in Python's loops
  // Python's `l = len(bin(A)) - 2`. If A=4 (100), l=3. Python loop `range(l, -1, -1)` means i=3,2,1,0.
  // This `l` is 1 more than the highest bit index if A is power of 2 (e.g. A=4, MSB index 2, l=3).
  // Or it's highest bit index if A is not power of 2 (e.g. A=5 (101), MSB index 2, l=3).
  // Let's use `l_py` for this specific Python length.
  l_py = get_l_bit_length_python_style(A_template_num)

  // Phase 1: Try to match set bits of A_template_num from its MSB down to LSB, using budget.
  // Python loop `for i in range(l, -1, -1)` means `i` from `l_py` down to `0`.
  // This range is problematic for bit indexing if `l_py` is length. Max index is `l_py-1`.
  // Correcting loop to iterate bit indices from (l_py-1) down to 0.
  FOR bit_idx FROM l_py - 1 DOWNTO 0:
    IF current_budget == 0 THEN BREAK FOR ENDIF // No budget left

    mask = 1 LEFT_SHIFT bit_idx
    IF (A_template_num AND mask) != 0 THEN // If bit_idx is set in A_template_num
      ans_num = ans_num OR mask          // Set this bit in ans_num
      current_budget = current_budget - 1
    ENDIF
  ENDFOR

  IF current_budget == 0 THEN RETURN ans_num ENDIF

  // Phase 2: If budget remains, try to set bits in ans_num that were 0 in A_template_num,
  // from LSB up to bit (l_py - 1).
  // Python loop `for i in range(l)` means `i` from `0` to `l_py-1`.
  FOR bit_idx FROM 0 TO l_py - 1:
    IF current_budget == 0 THEN BREAK FOR ENDIF // No budget left

    mask = 1 LEFT_SHIFT bit_idx
    // Check if bit is 0 in A_template_num AND also 0 in current ans_num (to avoid double counting budget for a bit already set in Phase 1)
    IF (A_template_num AND mask) == 0 THEN // Bit is 0 in A_template_num
        IF (ans_num AND mask) == 0 THEN // And also not yet set in ans_num (it wouldn't be from Phase 1)
            ans_num = ans_num OR mask      // Set this bit in ans_num
            current_budget = current_budget - 1
        ENDIF
    ENDIF
  ENDFOR

  IF current_budget == 0 THEN RETURN ans_num ENDIF

  // Phase 3: If budget still remains, make the number larger by appending 1s.
  // Python: `ans = ans << 1; ans = ans | 1`
  // This means `ans_new = (ans_old * 2) + 1`. This is done `current_budget` times.
  WHILE current_budget > 0:
    ans_num = (ans_num LEFT_SHIFT 1) OR 1
    current_budget = current_budget - 1
  ENDWHILE

  RETURN ans_num
ENDFUNCTION
```

### Pseudocode for HW4.py
```pseudocode
FUNCTION reverse_32bit_unsigned_integer_bits(A_input_uint32):
  reversed_integer_result = 0

  // Iterate 32 times, corresponding to the 32 bits of an unsigned integer.
  // The Python code iterates `i` from 32 down to 0.
  // Let's map this to standard bit indices 0 (LSB) to 31 (MSB).
  // Python's `i` effectively corresponds to a bit position.
  // When Python's `i` is 31 (MSB of input A), the target bit in `ans` is position 0 (LSB).
  // When Python's `i` is 0 (LSB of input A), the target bit in `ans` is position 31 (MSB).
  // The formula for target bit position is `32 - python_i - 1`.

  // The loop `for i in range(32, -1, -1)` in Python means `i` takes values 32, 31, ..., 0.
  // The iteration for i=32:
  //   `A & (1<<32)`: If A is a 32-bit int, this is 0. Bit 32 is not set.
  //   `ans | 1<<(32-32-1)` which is `ans | 1<<(-1)`. Behavior of `1<<-1` can be language-specific
  //   (e.g. 0 or error in some, or 0.5 conceptually). In Python, it raises error for negative shift if not using gmpy2.
  //   However, since `A & (1<<32)` is 0, this part of OR is not executed.
  // So, the i=32 iteration is effectively a no-op if A is 32-bit.
  // We can consider the loop effectively for i from 31 down to 0.

  FOR input_bit_pos FROM 0 TO 31: // Iterate through bits of A (0=LSB, 31=MSB)
    // Check if the `input_bit_pos`-th bit is set in A_input_uint32
    IF (A_input_uint32 RIGHT_SHIFT input_bit_pos) AND 1 THEN
      // If the `input_bit_pos`-th bit of A is 1,
      // then set the corresponding bit in `reversed_integer_result`.
      // The `input_bit_pos`-th bit of A becomes the `(31 - input_bit_pos)`-th bit of the result.
      target_bit_pos_in_result = 31 - input_bit_pos
      reversed_integer_result = reversed_integer_result OR (1 LEFT_SHIFT target_bit_pos_in_result)
    ENDIF
  ENDFOR

  RETURN reversed_integer_result
ENDFUNCTION
```

### Pseudocode for HW2.py
```pseudocode
FUNCTION divide_integers_bitwise(dividend, divisor):
  // Define standard 32-bit signed integer limits
  MAX_INT = (1 LEFT_SHIFT 31) - 1 // 2^31 - 1
  MIN_INT = -(1 LEFT_SHIFT 31)    // -2^31

  // Handle edge case: division by zero
  IF divisor == 0 THEN
    RETURN MAX_INT // Or as per specific problem constraints for division by zero
  ENDIF

  // Handle edge case: dividend is MIN_INT and divisor is -1, which overflows
  IF dividend == MIN_INT AND divisor == -1 THEN
    RETURN MAX_INT
  ENDIF

  // Step 1: Determine the sign of the result.
  // Sign is negative if dividend and divisor have different signs.
  sign = 1
  IF (dividend < 0 AND divisor > 0) OR (dividend > 0 AND divisor < 0) THEN
    sign = -1
  ENDIF

  // Step 2: Work with absolute values for the division logic.
  abs_dividend = absolute_value_of(dividend)
  abs_divisor = absolute_value_of(divisor)

  // Step 3: Initialize quotient and a temporary variable to build parts of the dividend.
  quotient = 0
  current_sum_of_divisor_parts = 0 // Represents sum of (abs_divisor * 2^i) terms

  // Step 4: Iterate from the most significant bit downwards (e.g., 31 for 32-bit integers).
  // The goal is to find how many times `abs_divisor * (2^i)` can fit into `abs_dividend`.
  FOR bit_position FROM 31 DOWNTO 0:
    potential_part_to_add = abs_divisor LEFT_SHIFT bit_position // abs_divisor * (2^bit_position)

    // Check if adding this `potential_part_to_add` to `current_sum_of_divisor_parts`
    // does not exceed `abs_dividend`. This is equivalent to checking if
    // `abs_dividend - current_sum_of_divisor_parts >= potential_part_to_add`.
    IF current_sum_of_divisor_parts + potential_part_to_add <= abs_dividend THEN
      current_sum_of_divisor_parts = current_sum_of_divisor_parts + potential_part_to_add
      // If it fits, then `2^bit_position` is part of the quotient.
      // Set the corresponding bit in the quotient.
      quotient = quotient OR (1 LEFT_SHIFT bit_position)
    ENDIF
  ENDFOR

  // Step 5: Apply the sign to the calculated quotient.
  quotient = quotient * sign

  // Step 6: Ensure the result is within the 32-bit signed integer range.
  // (This check is in the Python code, though MAX_INT/MIN_INT might differ slightly if it's for general int vs fixed 32-bit)
  // Python's integers handle arbitrary size, so this check is specific to contest platforms.
  // The provided Python explicitly checks against -2**31 and 2**31-1.
  IF quotient < MIN_INT OR quotient > MAX_INT THEN
    RETURN MAX_INT
  ELSE
    RETURN quotient
  ENDIF
ENDFUNCTION
```

### Pseudocode for HW1.py
```pseudocode
// Helper function to determine `l` as in Python's `len(bin(A)) - 2`
FUNCTION get_binary_length_l(number_A):
  IF number_A == 0 THEN RETURN 1 ENDIF // Corresponds to bin(0) -> "0b0", len=3, l=1

  // Calculate number of bits for positive integers
  length = 0
  temp_val = number_A
  WHILE temp_val > 0:
    temp_val = temp_val RIGHT_SHIFT 1
    length = length + 1
  RETURN length
ENDFUNCTION


FUNCTION solve_custom_bit_op(A_input_num):
  X_result_part = 0

  // Calculate `l` based on the number of bits in A_input_num's binary representation
  l_bit_count = get_binary_length_l(A_input_num)

  // Calculate Y_result_part = 2 to the power of l_bit_count
  Y_result_part = 1 LEFT_SHIFT l_bit_count

  // Construct X_result_part:
  // Iterate from bit position 0 up to l_bit_count - 1.
  // If the i-th bit of A_input_num is 0, set the i-th bit of X_result_part.
  FOR i_bit_index FROM 0 TO l_bit_count - 1:
    bit_mask = 1 LEFT_SHIFT i_bit_index
    // Check if the i-th bit of A_input_num is NOT set (is 0)
    IF (A_input_num AND bit_mask) == 0 THEN
      X_result_part = X_result_part OR bit_mask // Set the i-th bit in X_result_part
    ENDIF
  ENDFOR
  // X_result_part is now effectively the bitwise NOT of A_input_num, but only considering up to l_bit_count bits,
  // and only where bits were originally 0 in A_input_num.
  // More accurately: X_result_part = ( ( (1 LEFT_SHIFT l_bit_count) - 1 ) XOR A_input_num ) AND ( (1 LEFT_SHIFT l_bit_count) - 1 )
  // which is the bitwise complement of A_input_num within its significant `l_bit_count` bits.

  // The final result is X_result_part XOR Y_result_part
  RETURN X_result_part XOR Y_result_part
ENDFUNCTION

```

### Pseudocode for A2.py
```pseudocode
// Note: This pseudocode describes the logic of the `solve` method, which counts set bits from 1 to A.
// The `simple` and `dp` methods in the Python file are alternative approaches and not detailed here.

FUNCTION countSetBitsFrom1ToA_recursive(A_limit):
  // Base Cases for the recursion
  IF A_limit == 0 THEN
    RETURN 0 // Sum of set bits for numbers up to 0 is 0.
  ENDIF
  IF A_limit == 1 THEN
    RETURN 1 // Sum of set bits for numbers up to 1 (only number 1) is 1.
  ENDIF

  // Step 1: Find x, the bit position of the Most Significant Bit (MSB) of A_limit.
  // This means 2^x is the largest power of 2 less than or equal to A_limit.
  // Example: If A_limit = 11 (binary 1011), x = 3 (since 2^3 = 8).
  // The Python code finds `x` (named `max_power_of2_inrange` there) like this:
  //   p = 0
  //   WHILE (1 LEFT_SHIFT p) < A_limit: p = p + 1
  //   p = p - 1 (This makes `p` the exponent of the largest power of 2 strictly less than A_limit,
  //              unless A_limit is a power of 2, then it's one less.
  //              A simpler way: find p such that 2^p <= A_limit < 2^(p+1) )
  // Let's use a direct way to find x (position of MSB, 0-indexed):
  x = 0
  temp_A = A_limit
  WHILE (1 LEFT_SHIFT (x + 1)) <= temp_A: // Find p such that 2^(p+1) > A_limit
    x = x + 1
  ENDFOR
  // Now, x is the highest power for 2^x <= A_limit. E.g., A_limit=11 (1011), x=3. A_limit=8 (1000), x=3.

  // Part 1: Count set bits for all numbers from 1 up to (2^x - 1).
  // For numbers from 0 to 2^x - 1, there are x bit positions. Each bit position (0 to x-1)
  // is set for exactly half of these 2^x numbers.
  // So, total set bits in this range [0, 2^x - 1] is x * (2^x / 2) = x * 2^(x-1).
  // Python uses `max_power_of2_inrange` which is `x`.
  // `count_part1 = x * (1 LEFT_SHIFT (x - 1))`
  // This needs x > 0. If x=0 (A_limit=1), this term is 0.
  count_part1 = 0
  IF x > 0 THEN
    count_part1 = x * (1 LEFT_SHIFT (x - 1))
  ELSE // x is 0, means A_limit was 1 (already handled by base case) or error.
        // If A_limit=1, x=0. This formula gives 0 * 2^-1 which is problematic.
        // Base cases handle A_limit=0 and A_limit=1. So if we reach here, A_limit >= 2, so x >= 1.
        // Thus, x > 0 check is actually for x=0, but x will be >=1 if A_limit >=2.
        // The Python code's ((1<<(x-1))*x) is correct for x>=1.
    count_part1 = x * (1 LEFT_SHIFT (x-1)) // This line should be inside IF x > 0 for strictness with 2^(x-1)
                                        // But since A_limit >= 2 => x >= 1, x-1 >=0.
  ENDIF


  // Part 2: Count the MSBs (the bit at position x) for numbers from 2^x up to A_limit.
  // All these numbers have their x-th bit set.
  // The count of such numbers is A_limit - (2^x) + 1.
  count_part2_msbs = A_limit - (1 LEFT_SHIFT x) + 1

  // Part 3: Recursively count set bits for the remaining parts of numbers from 2^x up to A_limit.
  // These are the bits in numbers from 0 to (A_limit - 2^x).
  // Example: For A_limit=11 (1011), 2^x=8 (1000). Remaining part is for numbers 0 to (11-8=3).
  // We need to count set bits in (000, 001, 010, 011) which corresponds to the lower bits of (1000, 1001, 1010, 1011).
  remaining_value_for_recursion = A_limit - (1 LEFT_SHIFT x)
  count_part3_recursive = countSetBitsFrom1ToA_recursive(remaining_value_for_recursion)

  total_set_bits_count = count_part1 + count_part2_msbs + count_part3_recursive

  RETURN total_set_bits_count
  // Note: The problem context (other methods in file) might imply a modulo operation on the final result.
  // This recursive `solve` function does not apply modulo.
ENDFUNCTION
```
