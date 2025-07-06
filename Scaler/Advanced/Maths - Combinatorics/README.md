# Advanced/Maths - Combinatorics

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)
- [Maths__Combinatorics_.pdf](Maths__Combinatorics_.pdf)

## Description

This section covers Combinatorics, a branch of mathematics focusing on counting, both as an end in itself and as a means to solving problems. Topics include permutations, combinations, binomial coefficients, and applications in probability and algorithmic problem-solving.

### Pseudocode for A1.py
```pseudocode
FUNCTION calculate_nCr_modulo_p(N_items, R_to_choose, P_mod):
  // Apply identity C(n, r) = C(n, n-r) to use smaller r for efficiency.
  effective_R = R_to_choose
  IF (N_items - R_to_choose) < R_to_choose THEN
    effective_R = N_items - R_to_choose
  ENDIF

  // Handle invalid inputs for R (e.g., R < 0 or R > N). C(N,R) is 0 in such cases.
  IF effective_R < 0 THEN
    RETURN 0
  ENDIF

  dp_pascal_row = new array of size (effective_R + 1), initialized to 0
  dp_pascal_row[0] = 1

  FOR current_row_n FROM 1 TO N_items:
    upper_bound_for_k = minimum(current_row_n, effective_R)
    FOR current_col_k FROM upper_bound_for_k DOWNTO 1:
      dp_pascal_row[current_col_k] = (dp_pascal_row[current_col_k] + dp_pascal_row[current_col_k-1]) % P_mod
    ENDFOR
  ENDFOR

  RETURN dp_pascal_row[effective_R]
ENDFUNCTION
```

### Pseudocode for A2.py
```pseudocode
CLASS Solution_nCr_Modulo_Prime:

  FUNCTION calculate_modular_power(base, exponent, modulus):
    IF exponent < 0 THEN
      RETURN base
    ENDIF
    IF exponent == 0 THEN
      IF base != 0 THEN RETURN 1 % modulus
      ELSE RETURN 0 % modulus
    ENDIF

    IF exponent % 2 == 0 THEN
        temp_result = this.calculate_modular_power((base * base) % modulus, exponent / 2, modulus)
        RETURN temp_result % modulus
    ELSE
        temp_result = this.calculate_modular_power((base * base) % modulus, (exponent - 1) / 2, modulus)
        RETURN (base * temp_result) % modulus
    ENDIF
  ENDFUNCTION

  FUNCTION solve_nCr_mod_p_fermat(N_total, R_choose, P_prime):
    effective_R = R_choose
    IF (N_total - R_choose) < R_choose THEN
      effective_R = N_total - R_choose
    ENDIF

    IF effective_R < 0 THEN RETURN 0 ENDIF
    IF effective_R == 0 THEN RETURN 1 ENDIF

    numerator_mod_P = 1
    FOR i FROM 0 TO effective_R - 1:
      numerator_mod_P = (numerator_mod_P * (N_total - i)) % P_prime
    ENDFOR

    denominator_mod_P = 1
    FOR i FROM 1 TO effective_R:
      denominator_mod_P = (denominator_mod_P * i) % P_prime
    ENDFOR

    inverse_of_denominator_mod_P = this.calculate_modular_power(denominator_mod_P, P_prime - 2, P_prime)

    nCr_result = (numerator_mod_P * inverse_of_denominator_mod_P) % P_prime

    RETURN nCr_result
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for HW1.py
```pseudocode
// Assumes availability of a FACTORIAL function (math.factorial in Python).
// Assumes string characters can be sorted lexicographically.

CLASS Solution_StringRank:

  FUNCTION count_smaller_chars_in_string(S_input_str, char_reference):
    sorted_chars_of_S = SORT_CHARACTERS_ASCENDING(S_input_str)
    count_of_smaller = 0
    FOR EACH char_iter IN sorted_chars_of_S:
      IF char_iter == char_reference THEN
        RETURN count_of_smaller
      ENDIF
      count_of_smaller = count_of_smaller + 1
    ENDFOR
    RETURN count_of_smaller
  ENDFUNCTION

  FUNCTION findRank_of_permutation(A_string_to_rank):
    n_length = length of A_string_to_rank
    current_calculated_rank = 1
    MODULO_CONSTANT = 1000003

    FOR i_current_pos FROM 0 TO n_length - 1:
      current_char = A_string_to_rank[i_current_pos]
      suffix_string = SUBSTRING of A_string_to_rank from index i_current_pos to end
      num_smaller_chars_in_suffix = this.count_smaller_chars_in_string(suffix_string, current_char)

      IF num_smaller_chars_in_suffix > 0 THEN
        num_chars_remaining_for_permutation = n_length - 1 - i_current_pos
        factorial_of_remaining_chars = FACTORIAL(num_chars_remaining_for_permutation)
        rank_to_add = (factorial_of_remaining_chars * num_smaller_chars_in_suffix)
        current_calculated_rank = current_calculated_rank + rank_to_add
      ENDIF
    ENDFOR

    RETURN current_calculated_rank % MODULO_CONSTANT
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for HW2.py
```pseudocode
// Assumes Counter-like FrequencyMap, Factorial array, and Modular Inverse function.

CLASS Solution_RankWithDuplicateChars:

  FUNCTION precompute_factorials_mod(n_val, mod_val):
    fact_list = [1]
    FOR i FROM 1 TO n_val:
      next_fact = (fact_list[i-1] * i) % mod_val
      ADD next_fact TO fact_list
    ENDFOR
    RETURN fact_list
  ENDFUNCTION

  FUNCTION modular_inverse_fermat(num_val, prime_mod_val):
    RETURN POWER(num_val, prime_mod_val - 2, prime_mod_val)
  ENDFUNCTION

  DEFINE FUNCTION POWER(base, exponent, modulus):
    result = 1
    base = base % modulus
    WHILE exponent > 0:
        IF exponent IS ODD THEN result = (result * base) % modulus ENDIF
        base = (base * base) % modulus
        exponent = exponent / 2
    ENDWHILE
    RETURN result
  ENDFUNCTION

  FUNCTION findRank_with_duplicates_support(A_input_string):
    MOD = 1000003
    n_str_length = length of A_input_string
    IF n_str_length == 0 THEN RETURN 1 ENDIF

    precomputed_factorials = this.precompute_factorials_mod(n_str_length, MOD)
    char_frequencies = new FrequencyMap from A_input_string
    calculated_rank = 1

    FOR i_pos FROM 0 TO n_str_length - 1:
      char_actually_at_pos_i_in_A = A_input_string[i_pos]

      length_of_suffix_to_permute = n_str_length - 1 - i_pos
      perms_numerator_for_suffix = precomputed_factorials[length_of_suffix_to_permute]

      denominator_for_n_minus_i_items = 1
      FOR EACH (char_k_in_map, freq_k_in_map) IN ITEMS_OF(char_frequencies):
        denominator_for_n_minus_i_items = (denominator_for_n_minus_i_items * precomputed_factorials[freq_k_in_map]) % MOD
      ENDFOR
      inverse_denominator_suffix_perms = this.modular_inverse_fermat(denominator_for_n_minus_i_items, MOD)

      sum_frequencies_of_smaller_chars = 0
      FOR EACH (char_ch_iterate, freq_ch_iterate) IN ITEMS_OF(char_frequencies):
          IF char_ch_iterate < char_actually_at_pos_i_in_A THEN
              sum_frequencies_of_smaller_chars = sum_frequencies_of_smaller_chars + freq_ch_iterate
          ENDIF
      ENDFOR

      IF sum_frequencies_of_smaller_chars > 0 THEN
        term_to_add_to_rank = (sum_frequencies_of_smaller_chars * perms_numerator_for_suffix) % MOD
        term_to_add_to_rank = (term_to_add_to_rank * inverse_denominator_suffix_perms) % MOD
        calculated_rank = (calculated_rank + term_to_add_to_rank) % MOD
      ENDIF

      IF GET_FREQUENCY(char_frequencies, char_actually_at_pos_i_in_A) > 0 THEN
        DECREMENT_FREQUENCY(char_frequencies, char_actually_at_pos_i_in_A)
        IF GET_FREQUENCY(char_frequencies, char_actually_at_pos_i_in_A) == 0 THEN
          DELETE_KEY(char_frequencies, char_actually_at_pos_i_in_A)
        ENDIF
      ELSE
        BREAK FOR
      ENDIF
    ENDFOR

    RETURN calculated_rank % MOD
  ENDFUNCTION
ENDCLASS
```
