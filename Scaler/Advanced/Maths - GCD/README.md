# Advanced/Maths - GCD

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [A3.py](A3.py)
- [A4.py](A4.py)
- [HW2.py](HW2.py)
- [HW3.py](HW3.py)
- [HW4.py](HW4.py)
- [Maths___GCD.pdf](Maths___GCD.pdf)

## Description

This section focuses on the Greatest Common Divisor (GCD), also known as the Highest Common Factor (HCF). It covers properties of GCD, algorithms for its computation (like the Euclidean algorithm), and its applications in number theory and problem-solving.

### Pseudocode for A1.py
```pseudocode
CLASS Solution_GCD_Operations:

  // Helper function: Computes GCD of two numbers using Euclidean algorithm.
  FUNCTION gcd(a, b):
    IF b == 0 THEN
      RETURN a
    ELSE
      RETURN this.gcd(b, a MOD b) // Recursive call
    ENDIF
  ENDFUNCTION

  // Main function: Finds the maximum GCD among subsets of N-1 elements.
  FUNCTION solve_max_gcd_of_n_minus_one_elements(A_list):
    n = length of A_list

    // Handle edge cases for list size
    IF n == 0 THEN
      RETURN 0 // Or based on problem constraints for empty list
    ENDIF
    IF n == 1 THEN
      // GCD of "all other elements" is undefined.
      // Python code's behavior with ans=1 and gcd=-1 results in 1.
      // If the single element itself is considered, return A_list[0].
      // Let's assume problem implies n >= 2 for meaningful operation.
      // For strict Python translation:
      RETURN 1 // As per loop logic if ans=1 and gcd computation yields -1.
    ENDIF

    max_overall_gcd = 1 // Initialize with a value that any positive GCD would replace.
                        // Python code initializes ans = 1.

    // Iterate through each element A_list[i_exclude].
    // In each iteration, calculate GCD of all *other* elements.
    FOR i_exclude FROM 0 TO n - 1:
      current_elements_gcd = -1 // Sentinel for the first element in GCD calculation for this subset

      FOR j_include FROM 0 TO n - 1:
        IF i_exclude == j_include THEN
          CONTINUE // Skip the element A_list[i_exclude]
        ENDIF

        IF current_elements_gcd == -1 THEN
          // This is the first element for the current subset's GCD calculation.
          current_elements_gcd = A_list[j_include]
        ELSE
          // Update GCD with the current element A_list[j_include].
          current_elements_gcd = this.gcd(current_elements_gcd, A_list[j_include])
        ENDIF
      ENDFOR // End inner loop (j_include)

      // After iterating through all other elements for a given i_exclude,
      // current_elements_gcd holds the GCD of that N-1 element subset.
      // Update max_overall_gcd if current_elements_gcd is larger.
      // (Handle case where current_elements_gcd might remain -1 if list had < 2 elements for "others")
      IF current_elements_gcd != -1 THEN // Ensure a GCD was actually computed
          IF current_elements_gcd > max_overall_gcd THEN
            max_overall_gcd = current_elements_gcd
          ENDIF
      ENDIF
    ENDFOR // End outer loop (i_exclude)

    RETURN max_overall_gcd
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for HW4.py
```pseudocode
// Assumes a GCD function is available, e.g., math.gcd in Python.
// Assumes Dictionary/HashMap for frequency or requirements tracking.

FUNCTION solve_gcd_related_subset_construction(A_input_numbers_list):
  // Step 1: Sort the input list in descending order.
  // This often helps in greedy approaches or when considering elements for a subset.
  SORT A_input_numbers_list DESCENDING

  // `resulting_subset_ans` will store the elements of the subset being built.
  resulting_subset_ans = empty list

  // `gcd_iou_map` tracks GCD values that arise from pairs in `resulting_subset_ans`
  // and how many times they are "owed" or "required" by the construction logic.
  // Key: gcd_value, Value: count of outstanding requirements.
  gcd_iou_map = new empty Dictionary/HashMap

  // Iterate through each number `x_num` from the (reverse sorted) input list.
  FOR EACH x_num IN A_input_numbers_list:
    IF x_num IS A KEY IN gcd_iou_map AND GET_VALUE(gcd_iou_map, x_num) > 0 THEN
      // If `x_num` is found in `gcd_iou_map` with a positive count, it means
      // `x_num` itself was a GCD required by a previously formed pair in `resulting_subset_ans`.
      // Using this `x_num` from the input list satisfies one such requirement.
      DECREMENT_VALUE_BY_ONE(gcd_iou_map, x_num)
      // If the count for this GCD value becomes zero, it can be removed from the map.
      IF GET_VALUE(gcd_iou_map, x_num) == 0 THEN
        REMOVE_KEY x_num FROM gcd_iou_map
      ENDIF
    ELSE
      // If `x_num` is not in `gcd_iou_map` (or its count is 0), it's considered a new, primary element
      // to be added to `resulting_subset_ans`.

      // For every element `y_num_already_in_subset` that is already in `resulting_subset_ans`,
      // the GCD of `x_num` and `y_num_already_in_subset` now becomes a "required" GCD.
      // The Python code increments the count for this GCD by 2 in `gcd_iou_map`.
      // The exact meaning of "+2" depends on the specific problem definition this code solves.
      FOR EACH y_num_already_in_subset IN resulting_subset_ans:
        calculated_gcd = GCD(x_num, y_num_already_in_subset)
        current_requirement_count = GET_VALUE_OR_DEFAULT(gcd_iou_map, calculated_gcd, 0)
        SET_VALUE(gcd_iou_map, calculated_gcd, current_requirement_count + 2)
      ENDFOR

      // Add `x_num` to the `resulting_subset_ans`.
      ADD x_num TO resulting_subset_ans
    ENDIF
  ENDFOR

  // The function returns the constructed `resulting_subset_ans`.
  // The interpretation of whether this subset is "valid" if `gcd_iou_map` is not empty
  // at the end depends on the specific problem statement (e.g., if all requirements must be met
  // from within A_input_numbers_list for the subset to be valid).
  RETURN resulting_subset_ans
ENDFUNCTION

// Helper GCD function (iterative Euclidean algorithm)
FUNCTION GCD(val1, val2):
    WHILE val2 != 0:
        temp = val2
        val2 = val1 MOD val2
        val1 = temp
    ENDWHILE
    RETURN val1
ENDFUNCTION
```

### Pseudocode for HW3.py
```pseudocode
CLASS Solution_CountCommonMultiples:

  // Helper function: Computes GCD of two numbers `val1` and `val2`.
  // Uses the recursive Euclidean algorithm.
  DEFINE FUNCTION calculate_gcd(val1, val2):
    IF val2 == 0 THEN
      RETURN val1
    ELSE
      RETURN this.calculate_gcd(val2, val1 MOD val2)
    ENDIF
  ENDFUNCTION

  // Main function: Counts how many numbers in the range [1, A_upper_bound]
  // are divisible by both B_num1 and C_num2.
  FUNCTION solve_count_divisible_by_both(A_upper_bound, B_num1, C_num2):
    // A number divisible by both B_num1 and C_num2 must be divisible by their LCM.
    // Assume B_num1 and C_num2 are positive integers. If they can be zero or negative,
    // edge cases for GCD and LCM need to be handled (e.g., LCM with 0).
    IF B_num1 <= 0 OR C_num2 <= 0 THEN
        // Or based on problem constraints, if LCM is considered undefined or infinite for 0.
        // If B_num1 or C_num2 is 0, no positive integer is divisible by it.
        RETURN 0
    ENDIF

    // Step 1: Calculate GCD(B_num1, C_num2).
    gcd_of_B_and_C = this.calculate_gcd(B_num1, C_num2)

    // Step 2: Calculate LCM(B_num1, C_num2) using the formula:
    // LCM(x,y) = (x * y) / GCD(x,y).
    // Ensure no division by zero if gcd_of_B_and_C could be zero (not possible if B_num1, C_num2 > 0).
    lcm_of_B_and_C = (B_num1 * C_num2) / gcd_of_B_and_C // Use integer division

    // If lcm_of_B_and_C is 0 (e.g., if B_num1 or C_num2 was 0 and not caught by initial checks),
    // then no positive number is a multiple of 0 in the typical sense.
    // (This path shouldn't be hit if B_num1, C_num2 are positive).
    IF lcm_of_B_and_C == 0 THEN
        RETURN 0 // No positive multiples of 0.
    ENDIF

    // Step 3: Count how many multiples of `lcm_of_B_and_C` exist up to `A_upper_bound`.
    // This is simply `A_upper_bound` divided by `lcm_of_B_and_C` using integer division.
    // The Python code uses a loop: `for i in range(lcm, A+1, lcm): count += 1`.
    // This is equivalent to `A_upper_bound // lcm_of_B_and_C`.

    count_of_common_multiples = A_upper_bound / lcm_of_B_and_C // Integer division

    RETURN count_of_common_multiples
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for HW2.py
```pseudocode
CLASS Solution_Largest_Coprime_Factor:

  // Helper function to compute the Greatest Common Divisor (GCD) of two numbers.
  // This uses the recursive Euclidean algorithm.
  DEFINE FUNCTION gcd_recursive_helper(val_a, val_b):
    IF val_b == 0 THEN
      RETURN val_a
    ELSE
      RETURN this.gcd_recursive_helper(val_b, val_a MOD val_b)
    ENDIF
  ENDFUNCTION

  // Main function to find the largest divisor of number_A that is coprime to number_B.
  // This means finding the largest X such that:
  // 1. number_A % X == 0 (X divides number_A)
  // 2. GCD(X, number_B) == 1 (X is coprime to number_B)
  FUNCTION find_coprime_factor(number_A, number_B):
    // Start with X = number_A.
    // Repeatedly divide X by GCD(X, number_B) until GCD(X, number_B) becomes 1.
    // Each division removes common factors between X and number_B, ensuring that
    // the remaining X is still a divisor of the original number_A, and eventually
    // becomes coprime with number_B.

    current_value_of_A = number_A // This is the candidate for X, initially number_A itself.

    // Calculate the initial GCD between current_value_of_A and number_B.
    shared_common_divisor = this.gcd_recursive_helper(current_value_of_A, number_B)

    // Loop as long as the shared_common_divisor is not 1 (i.e., they are not coprime).
    WHILE shared_common_divisor != 1:
      // Divide current_value_of_A by their shared_common_divisor.
      // This removes the common factors from current_value_of_A.
      current_value_of_A = current_value_of_A / shared_common_divisor // Integer division

      // Recalculate the GCD with the new, reduced current_value_of_A.
      shared_common_divisor = this.gcd_recursive_helper(current_value_of_A, number_B)
    ENDWHILE

    // When the loop terminates, shared_common_divisor is 1.
    // This means current_value_of_A is now coprime with number_B.
    // Since we only divided by factors common to the original number_A (through its evolving form current_value_of_A)
    // and number_B, the final current_value_of_A is the largest divisor of the original number_A
    // that is coprime to number_B.
    RETURN current_value_of_A
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for A4.py
```pseudocode
CLASS Solution_GCD_Of_List_Elements:

  // Helper function to compute the Greatest Common Divisor (GCD) of two numbers.
  // Uses the iterative Euclidean algorithm.
  DEFINE FUNCTION calculate_gcd_for_pair(number1, number2):
    // Let `a` and `b` be local copies or direct parameters for calculation.
    a = number1
    b = number2
    WHILE b != 0:
      temp_val_for_b = b
      b = a MOD b
      a = temp_val_for_b
    ENDWHILE
    // When `b` is 0, `a` holds the GCD.
    RETURN a
  ENDFUNCTION

  // Main function to compute the GCD of all numbers in a given list `A_numbers`.
  FUNCTION solve_gcd_of_list(A_numbers):
    count_of_numbers = length of A_numbers

    // Handle edge cases for empty or single-element lists.
    IF count_of_numbers == 0 THEN
      // GCD of an empty set is often defined as 0, or could be an error.
      RETURN 0
    ENDIF
    IF count_of_numbers == 1 THEN
      // GCD of a single number is the absolute value of the number itself.
      // Assuming positive numbers based on typical GCD problem context.
      RETURN A_numbers[0]
    ENDIF

    // Initialize the overall GCD with the first element of the list.
    overall_gcd_result = A_numbers[0]

    // Iterate through the rest of the numbers in the list (starting from the second element).
    FOR i FROM 1 TO count_of_numbers - 1:
      current_num = A_numbers[i]
      // Update the overall GCD by computing GCD of `overall_gcd_result` and `current_num`.
      overall_gcd_result = this.calculate_gcd_for_pair(overall_gcd_result, current_num)
    ENDFOR

    RETURN overall_gcd_result
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for A2.py
```pseudocode
FUNCTION solve_simple_string_comparison(A_input_string1, B_input_string2):
  // Check if the two input strings are identical.
  IF A_input_string1 IS EQUAL TO B_input_string2 THEN
    // If they are the same, return the first string (A_input_string1).
    RETURN A_input_string1
  ELSE
    // If they are different, return the string "1".
    RETURN "1"
  ENDIF
ENDFUNCTION
```

### Pseudocode for A3.py
```pseudocode
CLASS Solution_GCD_Implementation:
  // Method to calculate the Greatest Common Divisor (GCD) of two integers, `a` and `b`,
  // using the iterative version of the Euclidean algorithm.
  FUNCTION gcd(a, b):
    // The loop continues as long as `b` is not zero.
    WHILE b != 0:
      // Store the current value of `b` in a temporary variable.
      temp_b_value = b
      // Update `b` to be the remainder of `a` divided by `b`.
      b = a MOD b
      // Update `a` to the previous value of `b` (which was stored in `temp_b_value`).
      a = temp_b_value
    ENDWHILE

    // When `b` becomes 0, the value of `a` is the GCD of the original two numbers.
    RETURN a
  ENDFUNCTION
ENDCLASS
```
