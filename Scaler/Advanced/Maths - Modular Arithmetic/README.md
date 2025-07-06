# Advanced/Maths - Modular Arithmetic

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [A3.py](A3.py)
- [A4.py](A4.py)
- [A5.py](A5.py)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)
- [Maths_1___Modular_Arithmetic.pdf](Maths_1___Modular_Arithmetic.pdf)

## Description

This section covers Modular Arithmetic, a system of arithmetic for integers where numbers "wrap around" upon reaching a certain value—the modulus. Topics include properties of modular operations (addition, subtraction, multiplication), modular exponentiation, modular inverse, and applications such as in cryptography and solving Diophantine equations.

### Pseudocode for A1.py
```pseudocode
// This function rearranges an array A such that for every index i,
// the new value of A[i] becomes the original value of A[A[i]].
// It assumes that all elements in A are in the range [0, n-1], where n is the length of A.
// This is achieved in-place using a mathematical encoding trick.

FUNCTION arrange_array_elements_in_place(A_list):
  n = length of A_list

  // If the list is empty, there's nothing to rearrange.
  IF n == 0 THEN
    RETURN A_list
  ENDIF

  // Step 1: Encode new and old values in each element.
  // For each element A_list[i], we want to store two pieces of information:
  // 1. Its original value (old_val = original A_list[i]).
  // 2. The new value that should be at this position (new_val = original A_list[old_val]).
  // This is done by transforming A_list[i] to: old_val + new_val * n.
  // To correctly get `new_val` (which is `original A_list[original A_list[i]]`),
  // we need to ensure that when we access `A_list[original A_list[i]]`, we get its
  // original value, not an already encoded one. This is done by `A_list[index] % n`.

  FOR i FROM 0 TO n - 1:
    // `original_A_list_i_value` is A_list[i] before any encoding in this loop iteration.
    // However, A_list[i] might have been used as an index by a *previous* iteration j < i
    // and thus A_list[i] itself could be an encoded value from a previous step if its index was A_list[j].
    // The standard robust way for this problem is:
    // A_list[i] = A_list[i] + (A_list[A_list[i] % n] % n) * n
    // The Python code `A[i] = (A[i] % n) + (A[A[i]]) * n` implies that A[i]%n correctly gives
    // the original value of A[i] to be used as an index, and A[A[i]] (without %n) directly gives
    // the original value that should be encoded. This is true if values are initially 0..n-1
    // and A[A[i]] always points to an unencoded cell or if it's an intended simplification.

    // Pseudocode for the standard, robust encoding:
    original_value_at_current_index_i = A_list[i] % n
    index_to_fetch_new_value_from = A_list[i] % n // This is original A[i]

    // Ensure index_to_fetch_new_value_from is valid before using it to access A_list.
    // (Assumed valid as elements are 0..n-1)
    original_new_value_component = A_list[index_to_fetch_new_value_from] % n

    A_list[i] = original_value_at_current_index_i + (original_new_value_component * n)
  ENDFOR

  // Step 2: Decode the new values from the encoded elements.
  // The new value for A_list[i] was stored as the multiplier of n.
  // It can be extracted by integer division: A_list[i] / n.
  FOR i FROM 0 TO n - 1:
    A_list[i] = A_list[i] / n // Integer division
  ENDFOR

  RETURN A_list // A_list is modified in-place, but Python style often returns it.
ENDFUNCTION
```

### Pseudocode for A2.py
```pseudocode
FUNCTION calculate_absolute_difference(A_num1, B_num2):
  // Calculate the difference between the two numbers.
  diff_value = A_num1 - B_num2

  // Return the absolute value of the difference.
  // Example: ABSOLUTE_VALUE(-5) is 5, ABSOLUTE_VALUE(5) is 5.
  RETURN ABSOLUTE_VALUE(diff_value)
ENDFUNCTION
```

### Pseudocode for A3.py
```pseudocode
// Calculates (A_base ^ B_exponent) MOD C_modulus using recursion.
// This is a common algorithm for modular exponentiation (exponentiation by squaring).

FUNCTION modular_exponentiation_recursive(A_base, B_exponent, C_modulus):
  // Base Case 1: If the exponent B_exponent is 0.
  IF B_exponent == 0 THEN
    // Any non-zero number raised to the power of 0 is 1.
    // The specific problem might define 0^0. This code returns 0 for 0^0.
    IF A_base != 0 THEN
      RETURN 1
    ELSE
      RETURN 0 // Behavior for 0^0 as per Python code.
    ENDIF
  ENDIF

  // Recursive Step:
  // If B_exponent is even: A^B = (A^2)^(B/2)
  IF B_exponent MOD 2 == 0 THEN
    // Calculate (A_base * A_base) % C_modulus to prevent overflow and keep numbers small.
    base_squared_mod_C = (A_base * A_base) % C_modulus
    // Recursively compute the power for B_exponent / 2.
    result_half_exponent = this.modular_exponentiation_recursive(base_squared_mod_C, B_exponent / 2, C_modulus)
    // The result of recursive call is already modulo C, but Python code applies % C again.
    RETURN result_half_exponent % C_modulus
  ELSE // B_exponent is odd: A^B = A * A^(B-1) = A * (A^2)^((B-1)/2)
    // Calculate (A_base * A_base) % C_modulus.
    base_squared_mod_C = (A_base * A_base) % C_modulus
    // Recursively compute the power for (B_exponent - 1) / 2.
    result_half_exponent = this.modular_exponentiation_recursive(base_squared_mod_C, (B_exponent - 1) / 2, C_modulus)

    // Combine: (A_base * result_half_exponent) % C_modulus
    // Python: (A * self.pow((A*A)%C, (B-1)//2, C) % C )%C
    // This implies intermediate products are also taken modulo C.
    // (A_base % C_modulus * result_half_exponent % C_modulus) % C_modulus
    // Since result_half_exponent is already mod C, it simplifies.
    final_result = ( (A_base % C_modulus) * result_half_exponent) % C_modulus
    // Ensure result is non-negative if language's % can be negative. Python handles this.
    // IF final_result < 0 THEN final_result = final_result + C_modulus ENDIF
    RETURN final_result
  ENDIF
ENDFUNCTION
```

### Pseudocode for A4.py
```pseudocode
// This function calculates the modular multiplicative inverse of an integer `A_num`
// with respect to a prime modulus `B_prime_mod`.
// It relies on Fermat's Little Theorem, which states that if `B_prime_mod` is a prime number,
// then for any integer `A_num` not divisible by `B_prime_mod`, A_num^(B_prime_mod - 1) % B_prime_mod = 1.
// From this, the modular inverse A_num^(-1) % B_prime_mod = A_num^(B_prime_mod - 2) % B_prime_mod.

FUNCTION calculate_modular_multiplicative_inverse(A_num, B_prime_mod):
  // Prerequisites for Fermat's Little Theorem based inverse:
  // 1. `B_prime_mod` must be a prime number.
  // 2. `A_num` must not be a multiple of `B_prime_mod` (i.e., GCD(A_num, B_prime_mod) = 1).
  //    If A_num % B_prime_mod == 0, the inverse does not exist.
  //    The Python code `pow(A, B-2, B)` might return 0 if A is multiple of B.

  // Calculate the exponent: B_prime_mod - 2.
  exponent = B_prime_mod - 2

  // Handle edge case for exponent (e.g., if B_prime_mod is 1 or 2).
  IF B_prime_mod <= 1 THEN
    // Modular inverse is not well-defined for modulus <= 1.
    RETURN error_or_undefined_value
  ENDIF
  // If B_prime_mod is 2, exponent is 0. A_num^0 mod 2 = 1 mod 2 (if A_num is odd).
  // If A_num is even (multiple of 2), inverse mod 2 does not exist.

  // Calculate (A_num ^ exponent) % B_prime_mod using modular exponentiation.
  // Assumes a `MODULAR_POWER(base, exp, mod)` function is available.
  inverse_value = MODULAR_POWER(A_num, exponent, B_prime_mod)

  // The Python code `pow(A, B-2, B)%B` has an additional `%B` at the end.
  // The built-in `pow(base, exp, mod)` in Python 3 already returns the result
  // within the range [0, mod-1] for positive mod, or appropriate range for negative mod.
  // So, the final `% B` is often redundant but harmless for positive B.
  RETURN inverse_value
ENDFUNCTION

// Assumed helper function for modular exponentiation (can be from A3.py)
// FUNCTION MODULAR_POWER(base, exponent, modulus): ...
```

### Pseudocode for A5.py
```pseudocode
// Assumes DefaultDictionary for frequency map, defaulting to 0 for missing keys.

FUNCTION count_pairs_with_sum_divisible_by_B_divisor(A_numbers_list, B_divisor_val):
  MOD_CONST = 10^9 + 7

  // Step 1: Create a frequency map of (number % B_divisor_val).
  remainder_counts_map = new DefaultDictionary with default value 0
  FOR EACH num IN A_numbers_list:
    remainder = num % B_divisor_val
    remainder_counts_map[remainder] = remainder_counts_map[remainder] + 1
  ENDFOR

  // `ans_pairs_special_cases` for pairs from same remainder group (rem=0 or rem=B/2).
  ans_pairs_special_cases = 0
  // `sum_prod_distinct_remainders` for pairs from different remainder groups (rem1, rem2) where rem1+rem2=B.
  // This will double count, so needs division by 2 later.
  sum_prod_distinct_remainders = 0

  // Step 2: Iterate through each unique remainder `k_rem` and its frequency `v_freq`.
  FOR EACH (k_rem, v_freq) IN ITEMS_OF(remainder_counts_map):

    // Case 1: Pairs chosen from the same remainder group.
    // This happens if (k_rem + k_rem) % B_divisor_val == 0.
    // This means either k_rem == 0, or 2 * k_rem == B_divisor_val (i.e., k_rem == B_divisor_val / 2).
    // Python code: `if B-k == B or B-k == k:`
    //   `B-k == B` implies `k == 0`.
    //   `B-k == k` implies `B == 2k`.
    IF k_rem == 0 OR (2 * k_rem == B_divisor_val) THEN
      // Number of pairs from `v_freq` items is v_freq * (v_freq - 1) / 2.
      pairs_in_group = (v_freq * (v_freq - 1) / 2) // Integer division
      ans_pairs_special_cases = (ans_pairs_special_cases + pairs_in_group) % MOD_CONST
    ELSE
      // Case 2: Pairs chosen from two different remainder groups (k_rem and B_divisor_val - k_rem).
      // Only proceed if the complementary remainder (B_divisor_val - k_rem) exists in the map.
      complementary_rem = (B_divisor_val - k_rem + B_divisor_val) % B_divisor_val // Ensure positive before mod
      IF KEY_EXISTS(remainder_counts_map, complementary_rem) THEN
        // Product m[k] * m[B-k]
        // This sum will be divided by 2 later as it counts (k, B-k) and (B-k, k) separately.
        product_of_counts = (v_freq * GET_FREQUENCY(remainder_counts_map, complementary_rem))
        sum_prod_distinct_remainders = (sum_prod_distinct_remainders + product_of_counts) % MOD_CONST
      ENDIF
    ENDIF
  ENDFOR

  // Divide sum_prod_distinct_remainders by 2 because each pair (rem_a, rem_b) where rem_a + rem_b = B
  // (and rem_a != rem_b) was counted twice (once for rem_a, once for rem_b).
  actual_distinct_remainder_pairs_count = (sum_prod_distinct_remainders / 2) // Integer division

  // Total pairs is sum of special case pairs and distinct remainder pairs.
  final_ans = (ans_pairs_special_cases + actual_distinct_remainder_pairs_count) % MOD_CONST

  // Ensure final result is non-negative.
  IF final_ans < 0 THEN final_ans = final_ans + MOD_CONST ENDIF

  RETURN final_ans
ENDFUNCTION
```

### Pseudocode for HW2.py
```pseudocode
CLASS Solution_A_Power_B_Factorial_Mod_P:

  // Main function: Calculates (A_base ^ (B_num!)) % MOD_PRIME
  FUNCTION solve_main(A_base, B_num):
    MOD_PRIME = 10^9 + 7

    // According to Fermat's Little Theorem, if MOD_PRIME is prime,
    // A_base^P % MOD_PRIME = A_base^(P % (MOD_PRIME - 1)) % MOD_PRIME.
    // So, we need to calculate B_num! % (MOD_PRIME - 1).
    exponent_modulus = MOD_PRIME - 1
    exponent_value = this.calculate_factorial_mod_m(B_num, exponent_modulus)

    // Now calculate (A_base ^ exponent_value) % MOD_PRIME.
    final_result = this.power_recursive_mod(A_base, exponent_value, MOD_PRIME)

    RETURN final_result
  ENDFUNCTION

  // Helper function: Calculates N_val! % m_mod.
  // Python's specific implementation detail for B=0 needs to be matched if strictly translating.
  FUNCTION calculate_factorial_mod_m(N_val, m_mod):
    // Python code: ans = B; while B > 1: ans = (ans * (B-1)) % (mod-1); B--.
    // If N_val = 0, Python returns 0.
    // If N_val = 1, Python returns 1.
    // This behavior for N_val=0 is to make pow(A,0,mod) yield 1 in the main function.
    IF N_val == 0 THEN
      RETURN 0 // Matches Python's factorial(0, ...) returning 0
    ENDIF

    result_factorial = N_val
    current_n = N_val
    WHILE current_n > 1:
      result_factorial = (result_factorial * (current_n - 1)) % m_mod
      current_n = current_n - 1
    ENDWHILE
    // If N_val was 1, loop doesn't run, result_factorial = 1. Correct.
    RETURN result_factorial
  ENDFUNCTION

  // Helper function: Calculates (base_val ^ exp_val) % mod_val using modular exponentiation (recursive).
  FUNCTION power_recursive_mod(base_val, exp_val, mod_val):
    // Base cases from Python code
    IF base_val == 0 THEN RETURN 0 ENDIF // 0^exp = 0 for exp > 0. (0^0 handled by exp_val==0)
    IF exp_val == 0 THEN RETURN 1 ENDIF   // base_val^0 = 1 (for base_val != 0)
    IF exp_val == 1 THEN RETURN base_val % mod_val ENDIF // base_val^1 = base_val

    // Recursive step for modular exponentiation
    // p = ( (base_val % mod_val) ^ (exp_val / 2) ) % mod_val
    // Python code: p = self.pow(A%mod, B//2, mod)
    // The base A%mod in recursive call is important.
    recursive_half_power = this.power_recursive_mod(base_val % mod_val, exp_val / 2, mod_val) // Integer division for exp_val/2

    squared_half_power = (recursive_half_power * recursive_half_power) % mod_val

    IF exp_val IS EVEN THEN
      RETURN squared_half_power
    ELSE // exp_val IS ODD
      RETURN (squared_half_power * (base_val % mod_val)) % mod_val
    ENDIF
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for HW1.py
```pseudocode
// This class structure and logic calculates the number of divisors for each integer in a list.
CLASS Solution_Count_Number_Of_Divisors:

  // Main public method, typically called 'solve' as per the class structure.
  FUNCTION solve(A_integer_list_input):
    // Delegates the main work to the countDivisors_using_sieve method.
    RETURN this.countDivisors_using_sieve(A_integer_list_input)
  ENDFUNCTION

  // Helper function: Generates an array where spf_sieve_array[i] stores the
  // Smallest Prime Factor (SPF) of the number `i`.
  // This is a variation of the Sieve of Eratosthenes.
  // Python method name is `seive`.
  FUNCTION generate_spf_sieve_array(limit_n):
    // Initialize spf_sieve_array such that spf_sieve_array[i] = i for all i.
    spf_sieve_array = new array of size limit_n
    FOR i FROM 0 TO limit_n - 1:
      spf_sieve_array[i] = i
    ENDFOR
    // Note: SPF for 0 and 1 are not standardly defined for factorization purposes.
    // The array usage typically starts from index 2.

    // Iterate from the first prime candidate, 2.
    prime_candidate_p = 2
    // Optimization: Only need to iterate up to sqrt(limit_n).
    WHILE prime_candidate_p * prime_candidate_p < limit_n:
      // If spf_sieve_array[prime_candidate_p] is still prime_candidate_p,
      // it means prime_candidate_p is a prime number.
      IF spf_sieve_array[prime_candidate_p] == prime_candidate_p THEN
        // Mark prime_candidate_p as the SPF for all its multiples
        // that haven't already been marked by a smaller prime factor.
        FOR multiple_val = prime_candidate_p * prime_candidate_p TO limit_n - 1 STEP prime_candidate_p:
          IF spf_sieve_array[multiple_val] == multiple_val THEN // If SPF of multiple_val is still itself
            spf_sieve_array[multiple_val] = prime_candidate_p   // Mark prime_candidate_p as its SPF
          ENDIF
        ENDFOR
      ENDIF
      prime_candidate_p = prime_candidate_p + 1
    ENDWHILE
    RETURN spf_sieve_array
  ENDFUNCTION

  // Helper function: Counts the number of divisors for each number in A_input_list
  // using the precomputed SPF sieve.
  FUNCTION countDivisors_using_sieve(A_input_list):
    IF A_input_list IS EMPTY THEN
      RETURN empty_list
    ENDIF

    // Determine the maximum number in A_input_list to set the sieve's upper limit.
    max_val_in_A = 0
    FOR EACH num_val IN A_input_list:
      IF num_val > max_val_in_A THEN max_val_in_A = num_val
    ENDFOR
    // Sieve needs to go up to max_val_in_A, so array size max_val_in_A + 1.

    spf_array = this.generate_spf_sieve_array(max_val_in_A + 1)

    list_of_divisor_counts = empty list

    FOR EACH number_to_analyze IN A_input_list:
      // Handle edge cases for divisor counting.
      IF number_to_analyze <= 0 THEN
        ADD 0 TO list_of_divisor_counts // Divisors of 0 or negatives are typically 0 or undefined.
        CONTINUE
      ENDIF
      IF number_to_analyze == 1 THEN
        ADD 1 TO list_of_divisor_counts // 1 has one divisor: 1.
        CONTINUE
      ENDIF

      current_num_for_factorization = number_to_analyze
      num_divisors_for_current_num = 1

      // Factorize `current_num_for_factorization` using the SPF array.
      // If prime factorization is p1^e1 * p2^e2 * ... * pk^ek,
      // then the number of divisors is (e1+1) * (e2+1) * ... * (ek+1).
      WHILE current_num_for_factorization > 1:
        current_prime_factor = spf_array[current_num_for_factorization]
        exponent_for_current_prime = 0

        // Count occurrences of `current_prime_factor`.
        WHILE current_num_for_factorization MOD current_prime_factor == 0:
          exponent_for_current_prime = exponent_for_current_prime + 1
          current_num_for_factorization = current_num_for_factorization / current_prime_factor // Integer division
        ENDWHILE

        // Update the total count of divisors.
        num_divisors_for_current_num = num_divisors_for_current_num * (exponent_for_current_prime + 1)
      ENDWHILE

      ADD num_divisors_for_current_num TO list_of_divisor_counts
    ENDFOR

    RETURN list_of_divisor_counts
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for HW1.py
```pseudocode
CLASS Solution_Count_Number_Of_Divisors:

  // Main public method, delegates to helper.
  FUNCTION solve(A_integer_list):
    RETURN this.countDivisors_using_sieve(A_integer_list)
  ENDFUNCTION

  // Helper: Generates an array where spf_sieve_array[i] stores the Smallest Prime Factor (SPF) of i.
  // This is a variation of the Sieve of Eratosthenes.
  FUNCTION generate_spf_sieve(upper_limit_n):
    // Initialize spf_sieve_array[i] = i for all i from 0 to upper_limit_n-1.
    spf_sieve_array = new array of size upper_limit_n
    FOR i FROM 0 TO upper_limit_n - 1:
      spf_sieve_array[i] = i
    ENDFOR
    // SPF of 0 and 1 are not typically defined in this context, but array is 0-indexed.
    // Values for spf_sieve_array[0] and spf_sieve_array[1] won't affect factorization of numbers >= 2.

    // Iterate starting from prime candidate 2.
    p_candidate = 2
    WHILE p_candidate * p_candidate < upper_limit_n:
      // If spf_sieve_array[p_candidate] is still p_candidate, then p_candidate is a prime number.
      IF spf_sieve_array[p_candidate] == p_candidate THEN
        // Mark p_candidate as the SPF for all its multiples that haven't been marked yet
        // by an even smaller prime factor.
        FOR multiple = p_candidate * p_candidate TO upper_limit_n - 1 STEP p_candidate:
          IF spf_sieve_array[multiple] == multiple THEN // If SPF of multiple is still itself
            spf_sieve_array[multiple] = p_candidate   // Mark p_candidate as its SPF
          ENDIF
        ENDFOR
      ENDIF
      p_candidate = p_candidate + 1
    ENDWHILE
    RETURN spf_sieve_array
  ENDFUNCTION

  // Helper: Counts divisors for each number in A_list using the precomputed SPF sieve.
  FUNCTION countDivisors_using_sieve(A_list_of_numbers):
    IF A_list_of_numbers IS EMPTY THEN RETURN empty_list ENDIF

    // Determine the maximum number in A_list_of_numbers to set the sieve limit.
    max_number_in_list = 0
    FOR EACH num IN A_list_of_numbers:
      IF num > max_number_in_list THEN max_number_in_list = num
    ENDFOR

    // Precompute Smallest Prime Factors (SPF) up to max_number_in_list + 1.
    // +1 because sieve array is often 0-indexed up to `n`, so needs size `n+1`.
    spf_array = this.generate_spf_sieve(max_number_in_list + 1)

    divisor_counts_result_list = empty list

    FOR EACH original_num IN A_list_of_numbers:
      // Handle edge cases for divisor counting if necessary (e.g., for 0 or 1).
      IF original_num == 0 THEN
        ADD 0 TO divisor_counts_result_list // Or based on problem definition for divisors of 0
        CONTINUE
      ENDIF
      IF original_num == 1 THEN
        ADD 1 TO divisor_counts_result_list // 1 has one divisor: 1
        CONTINUE
      ENDIF

      num_being_factored = original_num
      count_of_divisors_for_num = 1

      // Factorize num_being_factored using the SPF array to find prime factorization.
      // If num = p1^a1 * p2^a2 * ... * pk^ak, then number of divisors = (a1+1)*(a2+1)*...*(ak+1).
      WHILE num_being_factored > 1:
        smallest_prime_factor = spf_array[num_being_factored]
        exponent_of_this_prime = 0

        // Count how many times smallest_prime_factor divides num_being_factored.
        WHILE num_being_factored MOD smallest_prime_factor == 0:
          exponent_of_this_prime = exponent_of_this_prime + 1
          num_being_factored = num_being_factored / smallest_prime_factor // Integer division
        ENDWHILE

        // Update the total count of divisors using the exponent.
        count_of_divisors_for_num = count_of_divisors_for_num * (exponent_of_this_prime + 1)
      ENDWHILE

      ADD count_of_divisors_for_num TO divisor_counts_result_list
    ENDFOR

    RETURN divisor_counts_result_list
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for A5.py
```pseudocode
// Assumes DefaultDictionary that defaults to 0 for missing keys if using GET_FREQUENCY.
// Or check KEY_EXISTS before GET_FREQUENCY.

FUNCTION count_pairs_sum_divisible_by_K(A_list, K_divisor):
  MOD_OPERATION_CONSTANT = 10^9 + 7

  // Step 1: Store frequencies of remainders of A_list[i] % K_divisor.
  remainder_frequencies = new DefaultDictionary with default value 0
  FOR EACH number IN A_list:
    remainder = number % K_divisor
    remainder_frequencies[remainder] = remainder_frequencies[remainder] + 1
  ENDFOR

  total_valid_pairs_count = 0

  // Step 2: Iterate through all possible remainders `r1` from 0 to K_divisor - 1.
  // (Or iterate through keys of remainder_frequencies for efficiency).
  FOR EACH (r1_remainder, count_r1) IN ITEMS_OF(remainder_frequencies):

    // We need to find r2_remainder such that (r1_remainder + r2_remainder) % K_divisor == 0.
    // This means r2_remainder = (K_divisor - r1_remainder) % K_divisor.
    // If r1_remainder is 0, then r2_remainder is also 0.
    // If r1_remainder is not 0, then r2_remainder is K_divisor - r1_remainder.

    r2_remainder_needed = (K_divisor - r1_remainder) % K_divisor
    // Example: K=5, r1=2 -> r2_needed = (5-2)%5 = 3.
    // Example: K=5, r1=0 -> r2_needed = (5-0)%5 = 0.

    count_r2 = 0
    IF KEY_EXISTS(remainder_frequencies, r2_remainder_needed) THEN
      count_r2 = GET_FREQUENCY(remainder_frequencies, r2_remainder_needed)
    ENDIF

    IF r1_remainder == r2_remainder_needed THEN
      // This happens if r1_remainder = 0 or if 2 * r1_remainder = K_divisor (e.g., K=4, r1=2).
      // We need to choose 2 distinct numbers from the `count_r1` numbers that have this remainder.
      // Number of pairs = count_r1 * (count_r1 - 1) / 2.
      num_pairs_this_group = (count_r1 * (count_r1 - 1) / 2) // Integer division
      total_valid_pairs_count = (total_valid_pairs_count + num_pairs_this_group) % MOD_OPERATION_CONSTANT
    ELSE IF r1_remainder < r2_remainder_needed THEN
      // To avoid double counting (e.g., (r1,r2) and later (r2,r1) if r1!=r2),
      // only process pairs where r1 < r2_needed.
      // Number of pairs is count_r1 * count_r2.
      num_pairs_these_groups = count_r1 * count_r2
      total_valid_pairs_count = (total_valid_pairs_count + num_pairs_these_groups) % MOD_OPERATION_CONSTANT
    ENDIF
    // If r1_remainder > r2_remainder_needed, this pair was already handled when the smaller remainder was r1.
  ENDFOR

  // Ensure result is non-negative (Python's % handles positive modulus correctly).
  // IF total_valid_pairs_count < 0 THEN total_valid_pairs_count = total_valid_pairs_count + MOD_OPERATION_CONSTANT ENDIF

  RETURN total_valid_pairs_count
ENDFUNCTION
```
