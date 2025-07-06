# Advanced/Maths - Prime Numbers

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [A3.py](A3.py)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)
- [HW3.py](HW3.py)
- [HW4.py](HW4.py)
- [Maths__Prime.pdf](Maths__Prime.pdf)

## Description

This section covers properties of Prime Numbers, methods for primality testing, algorithms for generating primes (like Sieve of Eratosthenes), prime factorization, and their applications in number theory and cryptography.

### Pseudocode for A1.py
```pseudocode
// This class structure calculates the number of divisors for each integer in a list.
CLASS Solution_CountDivisorsSieve:

  // Main public method.
  FUNCTION solve(A_input_list):
    RETURN this.count_divisors_for_list(A_input_list)
  ENDFUNCTION

  // Helper function to count divisors for each number in a list using a sieve-like method.
  // Note: The Python code names this method `solve` inside another `solve`, and `seive` for sieve.
  // Here, names are adjusted for clarity.
  FUNCTION count_divisors_for_list(A_numbers):
    IF A_numbers IS EMPTY THEN
      RETURN empty_list
    ENDIF

    // Determine the maximum number in the input list to set the sieve's upper limit.
    max_number_in_input = 0
    FOR EACH num IN A_numbers:
      IF num > max_number_in_input THEN
        max_number_in_input = num
    ENDFOR

    // `divisor_count_array[k]` will store the number of divisors of k.
    // Python initializes with 2, assuming 1 and k are divisors.
    // Then adjusts for 0 and 1.
    sieve_limit_n = max_number_in_input
    divisor_count_array = new array of size (sieve_limit_n + 1)

    FOR k FROM 0 TO sieve_limit_n:
      IF k == 0 THEN divisor_count_array[k] = 0 // Divisors of 0 (Python sets 0)
      ELSE IF k == 1 THEN divisor_count_array[k] = 1 // Divisor of 1 is 1
      ELSE divisor_count_array[k] = 2 // Initial count for numbers >= 2 (1 and itself)
      ENDIF
    ENDFOR

    // Sieve-like process to update divisor counts.
    // Iterate `i` from 2 up to sqrt(sieve_limit_n). `i` is a potential factor.
    i_factor = 2
    WHILE i_factor * i_factor <= sieve_limit_n:
      // Iterate `j_num` through multiples of `i_factor`, starting from `i_factor * i_factor`.
      // For each `j_num = i_factor * k_other_factor`, `i_factor` and `k_other_factor` are divisors.
      // If `i_factor != k_other_factor`, they add 2 to the count (1, j_num already counted).
      // If `i_factor == k_other_factor` (perfect square), they add 1.
      FOR j_num = i_factor * i_factor TO sieve_limit_n STEP i_factor:
        // Add 2 for the pair of divisors (i_factor, j_num / i_factor)
        // if they are different from 1 and j_num.
        // The Python code `fact[j] += 2` assumes `fact[j]` was initialized to count 1 and `j`.
        // It adds counts for `i` and `j/i`.
        divisor_count_array[j_num] = divisor_count_array[j_num] + 2
      ENDFOR

      // If `i_factor * i_factor` is within bounds, it's a perfect square.
      // The pair (i_factor, i_factor) was counted as two by `+=2`. Correct by subtracting 1.
      IF i_factor * i_factor <= sieve_limit_n THEN
        divisor_count_array[i_factor * i_factor] = divisor_count_array[i_factor * i_factor] - 1
      ENDIF

      i_factor = i_factor + 1
    ENDWHILE

    // Collect the precomputed divisor counts for numbers in the input list A_numbers.
    result_counts_list = empty list
    FOR EACH num_in_A IN A_numbers:
      // Handle potential out-of-bounds access if num_in_A is negative or too large,
      // though problem context usually implies non-negative integers within reasonable limits.
      IF num_in_A >= 0 AND num_in_A <= sieve_limit_n THEN
        ADD divisor_count_array[num_in_A] TO result_counts_list
      ELSE
        // Fallback for numbers outside the precomputed range (e.g., if problem allows negatives)
        // Or if num_in_A was 0 and sieve starts at 1 for divisor counts.
        // Python code would error if num_in_A is out of `fact` array bounds.
        // Based on `max(A)+1` sieve size, all valid positive A[i] are covered.
        // If A[i]=0, fact[0]=0 is appended.
        ADD 0 TO result_counts_list // Default/error value for out-of-range
      ENDIF
    ENDFOR

    RETURN result_counts_list
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for HW2.py
```pseudocode
// This function finds two prime numbers that sum up to a given integer A_sum_target.
// It assumes A_sum_target is an even number > 2 (as per Goldbach Conjecture context).

FUNCTION find_two_primes_summing_to_A(A_sum_target):
  // Handle edge cases where a solution might not exist or is trivial.
  // E.g., if A_sum_target < 4, or if A_sum_target is odd.
  // The provided Python code doesn't explicitly handle these before sieve,
  // but the subsequent loop might not find a pair.
  IF A_sum_target < 4 THEN
    RETURN empty_list // Or error: e.g., 2+2=4 is smallest sum of two primes.
  ENDIF
  // If A_sum_target is odd, one prime must be 2. Check if (A_sum_target - 2) is prime.
  // The generic loop below will also find this.

  // Step 1: Generate primes up to A_sum_target using Sieve of Eratosthenes.
  // `is_prime_array[i]` will be true if `i` is prime, false otherwise.
  is_prime_array = new boolean array of size (A_sum_target + 1), initialized to true.

  // Mark 0 and 1 as not prime.
  IF A_sum_target >= 0 THEN is_prime_array[0] = false ENDIF
  IF A_sum_target >= 1 THEN is_prime_array[1] = false ENDIF

  // Sieve implementation
  p_val = 2
  WHILE p_val * p_val <= A_sum_target:
    IF is_prime_array[p_val] IS TRUE THEN // If p_val is prime
      // Mark all multiples of p_val (starting from p_val*p_val) as not prime.
      // Python code loop `range(i*i, A, i)` goes up to A-1. Max index needed is A.
      FOR multiple_val = p_val * p_val TO A_sum_target STEP p_val:
        is_prime_array[multiple_val] = false
      ENDFOR
    ENDIF
    p_val = p_val + 1
  ENDWHILE

  // Step 2: Iterate to find a prime `p1` such that `p2 = A_sum_target - p1` is also prime.
  // Iterate `p1_candidate` from 2 up to A_sum_target / 2.
  // If we find such a pair, it will be the lexicographically smallest because we start `p1_candidate` from smallest.
  FOR p1_candidate FROM 2 TO (A_sum_target / 2) : // Integer division for loop limit
    IF is_prime_array[p1_candidate] IS TRUE THEN // Check if p1_candidate is prime
      p2_candidate = A_sum_target - p1_candidate
      // Check if p2_candidate is also prime.
      // Ensure p2_candidate is within the bounds of is_prime_array (it will be if p1 >= 2 and p2 >= p1).
      IF p2_candidate <= A_sum_target AND is_prime_array[p2_candidate] IS TRUE THEN
        // Found a pair of primes (p1_candidate, p2_candidate) that sum to A_sum_target.
        RETURN [p1_candidate, p2_candidate]
      ENDIF
    ENDIF
  ENDFOR

  // If the loop completes and no such pair is found.
  // (This shouldn't happen for even A_sum_target >= 4 according to Goldbach Conjecture,
  // but might for odd A_sum_target or if constraints are different).
  // Python code implicitly returns None if loop finishes.
  RETURN empty_list // Or indicate no solution found
ENDFUNCTION
```

### Pseudocode for HW3.py
```pseudocode
// Assumes ORD(character) function returns its ASCII/Unicode integer value.

FUNCTION excel_title_to_column_number(A_excel_title_str):
  // Initialize the resulting column number to 0.
  column_number = 0

  // Iterate through each character of the Excel title string (e.g., "A", "B", "AA", "AB").
  FOR EACH char_c IN A_excel_title_str:
    // Convert the character to its corresponding 1-based numeric value:
    // 'A' -> 1, 'B' -> 2, ..., 'Z' -> 26.
    // This is done by taking its ASCII/Unicode value, subtracting the value of 'A', and adding 1.
    char_numeric_value = (ORD(char_c) - ORD('A')) + 1

    // Treat the Excel title as a base-26 number.
    // For each character processed from left to right:
    // Multiply the current accumulated column_number by 26 (the base)
    // and then add the numeric_value_of_char for the current character.
    column_number = (column_number * 26) + char_numeric_value
  ENDFOR

  // After iterating through all characters, column_number holds the final result.
  RETURN column_number
ENDFUNCTION
```

### Pseudocode for A2.py
```pseudocode
FUNCTION calculate_integer_part_of_square_root(A_input_number):
  // Step 1: Calculate the square root of A_input_number.
  // The result might be a floating-point number.
  float_square_root = SQUARE_ROOT(A_input_number) // e.g., math.sqrt()

  // Step 2: Convert the floating-point result to an integer.
  // In Python, int() truncates towards zero. For positive numbers, this is floor().
  // For the context of integer square root, floor is usually intended.
  integer_square_root_part = FLOOR(float_square_root)
  // Or, if directly translating Python's int(): TRUNCATE_TO_INTEGER(float_square_root)

  RETURN integer_square_root_part
ENDFUNCTION

// Note: Assumes availability of SQUARE_ROOT (like math.sqrt) and FLOOR or TRUNCATE_TO_INTEGER functions.
```

### Pseudocode for HW4.py
```pseudocode
// Assumes ORD(char) returns ASCII/Unicode value of a character,
// and CHR(value) converts an ASCII/Unicode value back to a character.

FUNCTION convert_excel_column_number_to_title(A_column_num_integer):
  // Handle invalid input if necessary (e.g., A_column_num_integer <= 0).
  // The problem typically implies A_column_num_integer is a positive integer.
  IF A_column_num_integer <= 0 THEN
    RETURN "" // Or raise an error, based on problem constraints.
  ENDIF

  excel_title_result = "" // Initialize an empty string to build the title.
  current_processing_num = A_column_num_integer

  // Loop as long as the current_processing_num is greater than 0.
  // This process is akin to converting a number to base 26, but with digits 1-26 (A-Z).
  WHILE current_processing_num > 0:
    // Step 1: Adjust the number to make it 0-indexed for the modulo operation.
    // For a 1-indexed system (A=1, ..., Z=26), subtracting 1 maps it to 0-25.
    // Example: 1 becomes 0 ('A'), 26 becomes 25 ('Z'), 27 becomes 26.
    current_processing_num = current_processing_num - 1

    // Step 2: Get the remainder when divided by 26. This gives the 0-indexed value (0-25)
    // for the current character (rightmost character of the current part of the title).
    remainder = current_processing_num MOD 26

    // Step 3: Convert this 0-indexed value (0 for 'A', 1 for 'B', ..., 25 for 'Z')
    // to its corresponding uppercase letter character.
    current_char = CHR(ORD('A') + remainder)

    // Step 4: Prepend this character to the result string, as we are generating
    // the title from right to left (least significant "digit" to most significant).
    excel_title_result = current_char + excel_title_result

    // Step 5: Update the number for the next iteration by integer division by 26.
    // This moves to the next "digit" position to the left.
    current_processing_num = current_processing_num / 26 // Integer division
  ENDWHILE

  RETURN excel_title_result
ENDFUNCTION
```

### Pseudocode for HW1.py
```pseudocode
CLASS Solution_CountNumbersWithTwoDistinctPrimeFactors:

  // Main method to count numbers up to A_limit with exactly two distinct prime factors.
  FUNCTION solve(A_limit):
    IF A_limit <= 1 THEN
      RETURN 0 // Numbers <= 1 do not have two distinct prime factors.
    ENDIF

    // Step 1: Generate Smallest Prime Factor (SPF) array up to A_limit.
    // spf_array[k] will store the smallest prime factor of k.
    spf_array = new array of size (A_limit + 1)
    FOR k FROM 0 TO A_limit:
      spf_array[k] = k // Initialize spf[k] = k
    ENDFOR
    // SPF of 0 and 1 are not typically used for factorization of numbers >= 2.

    // Sieve process to populate spf_array correctly.
    // Iterate i_factor from 2 up to sqrt(A_limit).
    // The Python code iterates i up to int(A**0.5)+1.
    FOR i_factor FROM 2 TO FLOOR(SQUARE_ROOT(A_limit)) + 1:
      // Iterate j_multiple through multiples of i_factor, starting from i_factor itself.
      // (Python code `for j in range(i, A+1, i)`)
      FOR j_multiple FROM i_factor TO A_limit STEP i_factor:
        // If i_factor is smaller than the current recorded SPF of j_multiple, update it.
        // Since i_factor iterates from smallest, this ensures spf_array[j_multiple] gets the smallest prime factor.
        IF spf_array[j_multiple] > i_factor THEN
          spf_array[j_multiple] = i_factor
        ENDIF
      ENDFOR
    ENDFOR
    // Note: A more optimized SPF sieve usually checks `if spf[i_factor] == i_factor`
    // before iterating multiples, and starts inner loop from `i_factor * i_factor`.
    // However, the above pseudocode matches the provided Python's sieve logic.

    count_of_target_numbers = 0

    // Step 2: Iterate from 2 up to A_limit to check each number.
    FOR num_to_analyze FROM 2 TO A_limit:
      current_num_for_factorization = num_to_analyze
      distinct_prime_factors_found = new Set()

      // Factorize `current_num_for_factorization` using the precomputed SPF array.
      WHILE current_num_for_factorization > 1:
        smallest_prime_factor = spf_array[current_num_for_factorization]
        ADD smallest_prime_factor TO distinct_prime_factors_found

        // Divide out all occurrences of this smallest_prime_factor.
        // The Python code `while n > 1 and n % spf[n] == 0: ... n = n // spf[n]`
        // effectively does this by repeatedly dividing by the current SPF of the changing n.
        WHILE current_num_for_factorization MOD smallest_prime_factor == 0:
          current_num_for_factorization = current_num_for_factorization / smallest_prime_factor // Integer division
        ENDWHILE
        // If after division, num_for_factorization is still > 1 but spf[num_for_factorization] is 0 or 1 (error in SPF for 0/1)
        // this implies an issue. Assume spf_array is correctly populated for numbers > 1.
      ENDWHILE

      // Check if exactly two distinct prime factors were found.
      IF size of distinct_prime_factors_found == 2 THEN
        count_of_target_numbers = count_of_target_numbers + 1
      ENDIF
    ENDFOR

    RETURN count_of_target_numbers
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for A3.py
```pseudocode
// Assumes DefaultDictionary for frequency map and POWER for modular exponentiation.

FUNCTION solve_subsequences_by_prime_counts(A_input_list):
  MODULO_CONSTANT = 10^9 + 7
  list_len = length of A_input_list
  IF list_len == 0 THEN RETURN 0 ENDIF

  // Step 1: Find the maximum value in A_input_list to set Sieve limit.
  max_value_in_A = 0
  FOR EACH val IN A_input_list:
    IF val > max_value_in_A THEN max_value_in_A = val
  ENDFOR

  // Ensure sieve limit is at least 1 for array indexing.
  sieve_upper_limit = maximum(max_value_in_A, 1)

  // Step 2: Generate primes up to sieve_upper_limit using Sieve of Eratosthenes.
  is_prime_flags = new boolean array of size (sieve_upper_limit + 1), all true
  IF sieve_upper_limit >= 0 THEN is_prime_flags[0] = false ENDIF
  IF sieve_upper_limit >= 1 THEN is_prime_flags[1] = false ENDIF

  p_num = 2
  WHILE p_num * p_num <= sieve_upper_limit:
    IF is_prime_flags[p_num] IS TRUE THEN
      FOR multiple = p_num * p_num TO sieve_upper_limit STEP p_num:
        is_prime_flags[multiple] = false
      ENDFOR
    ENDIF
    p_num = p_num + 1
  ENDWHILE

  // Step 3: Calculate prefix sums of prime counts.
  // prime_count_prefix_sum[i] = number of primes <= i.
  prime_count_prefix_sum = new integer array of size (sieve_upper_limit + 1), all 0
  FOR i FROM 2 TO sieve_upper_limit:
    prime_count_prefix_sum[i] = prime_count_prefix_sum[i-1]
    IF is_prime_flags[i] IS TRUE THEN
      prime_count_prefix_sum[i] = prime_count_prefix_sum[i] + 1
    ENDIF
  ENDFOR

  // Step 4: For each number in A_input_list, find its associated prime count (number of primes <= it).
  // Store the frequency of these prime counts.
  // map_of_prime_counts_to_frequencies: (prime_count_value) -> (frequency_of_this_prime_count_value)
  map_of_prime_counts_to_frequencies = new DefaultDictionary with default value 0
  FOR EACH number_val IN A_input_list:
    IF number_val == 1 THEN // Python code explicitly skips 1.
      CONTINUE
    ENDIF
    // Ensure number_val is within the bounds of prime_count_prefix_sum array.
    // (Numbers <= 0 are not handled by prime_count_prefix_sum as defined, assume positive A)
    IF number_val >= 0 AND number_val <= sieve_upper_limit THEN
        count_of_primes_for_number_val = prime_count_prefix_sum[number_val]
        map_of_prime_counts_to_frequencies[count_of_primes_for_number_val] = map_of_prime_counts_to_frequencies[count_of_primes_for_number_val] + 1
    ENDIF
  ENDFOR

  // Step 5: Calculate the total number of valid subsequences.
  // A valid subsequence is non-empty and all its elements have the same prime_count.
  // If `v` elements have the same prime_count, they form (2^v - 1) non-empty subsequences.
  total_result_subsequences = 0
  FOR EACH (prime_count_key, frequency_value) IN ITEMS_OF(map_of_prime_counts_to_frequencies):
    // Calculate (2^frequency_value - 1) % MODULO_CONSTANT
    power_of_2 = POWER(2, frequency_value, MODULO_CONSTANT)
    subsequences_for_this_group = (power_of_2 - 1 + MODULO_CONSTANT) % MODULO_CONSTANT // Ensure positive before final mod

    total_result_subsequences = (total_result_subsequences + subsequences_for_this_group) % MODULO_CONSTANT
  ENDFOR

  RETURN total_result_subsequences
ENDFUNCTION

// Assumed helper: FUNCTION POWER(base, exp, mod) for modular exponentiation.
```
