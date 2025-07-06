# Advanced/Recursion - I

## Files in this topic:

- [A1.py](A1.py)
- [A2.py](A2.py)
- [A3.py](A3.py)
- [HW1.py](HW1.py)
- [HW2.py](HW2.py)
- [HW3.py](HW3.py)
- [HW4.py](HW4.py)
- [HW5.py](HW5.py)
- [Recursion_I_.pdf](Recursion_I_.pdf)

## Description

This section introduces the fundamental principles of Recursion, a problem-solving technique where a function calls itself to solve smaller instances of the same problem. It covers base cases, recursive steps, and applications in various algorithms like factorial, Fibonacci, and basic tree traversals.

### Pseudocode for A1.py
```pseudocode
// This function calculates the sum of the digits of a non-negative integer `A_number` using recursion.
FUNCTION calculate_sum_of_digits_recursive(A_number):
  // Base Case: If the number becomes 0, it means all digits have been processed.
  // The sum of digits of 0 is 0.
  IF A_number == 0 THEN
    RETURN 0
  ENDIF

  // Recursive Step:
  // 1. Get the last digit of A_number using the modulo operator (A_number MOD 10).
  last_digit = A_number MOD 10

  // 2. Get the rest of the number by removing the last digit (A_number / 10 using integer division).
  remaining_digits_part = A_number / 10 // Integer division

  // 3. Recursively call the function with the `remaining_digits_part`
  //    and add the `last_digit` to the result of this recursive call.
  sum_of_other_digits = this.calculate_sum_of_digits_recursive(remaining_digits_part)

  RETURN last_digit + sum_of_other_digits
ENDFUNCTION
```

### Pseudocode for HW3.py
```pseudocode
// This function calculates the factorial of a non-negative integer `A_num` using recursion.
// Definition: N! = N * (N-1) * ... * 1 for N > 0. And 0! = 1.

FUNCTION calculate_factorial(A_num):
  // Base Case 1: Factorial of 0 is 1.
  IF A_num == 0 THEN
    RETURN 1
  ENDIF

  // Base Case 2: Factorial of 1 is 1.
  // (This is also handled by the recursive step: 1 * factorial(0) = 1 * 1 = 1)
  // The Python code includes this explicit base case.
  IF A_num == 1 THEN
    RETURN 1
  ENDIF

  // Recursive Step: For A_num > 1, N! = N * (N-1)!
  // Recursively call the function for A_num - 1.
  result_for_A_minus_1_factorial = this.calculate_factorial(A_num - 1)

  // Multiply by A_num to get A_num!
  RETURN A_num * result_for_A_minus_1_factorial
ENDFUNCTION
```

### Pseudocode for HW4.py
```pseudocode
// This script defines a function to reverse a string using recursion
// and a main block to read input, call the reverse function, and print the output.

// Recursive function to reverse a string `s`.
FUNCTION reverse_string_recursive(s_string_to_reverse):
  // Base Case: If the string is empty, its reverse is an empty string.
  IF s_string_to_reverse IS EMPTY THEN
    RETURN "" // Return empty string
  ENDIF

  // Recursive Step:
  // The reverse of string `s` is:
  //   (reverse of the substring s[1:]) CONCATENATED WITH (the first character s[0]).
  // Example: reverse("abc") = reverse("bc") + "a"
  //                     = (reverse("c") + "b") + "a"
  //                     = ((reverse("") + "c") + "b") + "a"
  //                     = (("" + "c") + "b") + "a"
  //                     = ("c" + "b") + "a"
  //                     = "cb" + "a"
  //                     = "cba"

  first_character = CHARACTER_AT(s_string_to_reverse, 0)
  substring_from_second_char = SUBSTRING of s_string_to_reverse from index 1 to end

  reversed_substring = reverse_string_recursive(substring_from_second_char)

  RETURN reversed_substring + first_character
ENDFUNCTION


// Main execution block (simulating Python's `if __name__ == '__main__': main()`)
FUNCTION main_program_logic():
  // Read a string from standard input.
  input_string_s = READ_LINE_FROM_STDIN()

  // Call the recursive function to get the reversed string.
  reversed_string_result = reverse_string_recursive(input_string_s)

  // Print the reversed string to standard output.
  PRINT reversed_string_result

  RETURN 0 // Standard exit code indicating successful execution
ENDFUNCTION

// (Implicitly, main_program_logic() would be called if this were an executable script)
```

### Pseudocode for HW5.py
```pseudocode
// This function calculates a term in a sequence defined by a specific recurrence relation:
// S(0) = 1
// S(1) = 1
// S(2) = 2
// S(A) = S(A-1) + S(A-2) + S(A-3) + A, for A > 2
// Note: This direct recursive implementation can be inefficient for larger A due to re-computation.

FUNCTION solve_defined_recurrence(A_term_index):
  // Base Case 1: Corresponds to S(0)
  IF A_term_index == 0 THEN
    RETURN 1
  ENDIF

  // Base Case 2: Corresponds to S(1)
  IF A_term_index == 1 THEN
    RETURN 1
  ENDIF

  // Base Case 3: Corresponds to S(2)
  IF A_term_index == 2 THEN
    RETURN 2
  ENDIF

  // Recursive Step: For A_term_index > 2
  // Calculate S(A-1)
  val_A_minus_1 = this.solve_defined_recurrence(A_term_index - 1)
  // Calculate S(A-2)
  val_A_minus_2 = this.solve_defined_recurrence(A_term_index - 2)
  // Calculate S(A-3)
  val_A_minus_3 = this.solve_defined_recurrence(A_term_index - 3)

  // Apply the recurrence: S(A) = S(A-1) + S(A-2) + S(A-3) + A
  RETURN val_A_minus_1 + val_A_minus_2 + val_A_minus_3 + A_term_index
ENDFUNCTION
```

### Pseudocode for A2.py
```pseudocode
CLASS Solution_CheckIfDigitalRootIsOne:

  // Helper function: Recursively calculates the sum of digits of a non-negative integer `num`.
  DEFINE FUNCTION get_sum_of_digits(num):
    // Base case: If number is 0, sum of digits is 0.
    IF num == 0 THEN
      RETURN 0
    ENDIF
    // Recursive step: (last digit) + (sum of digits of remaining part).
    RETURN (num MOD 10) + this.get_sum_of_digits(num / 10) // Integer division for num/10
  ENDFUNCTION

  // Main function: Determines if the ultimate single-digit sum (digital root) of A_input_num is 1.
  // Returns 1 if true, 0 if false.
  FUNCTION solve_check_digital_root(A_input_num):
    // Base Case for the main recursion: If A_input_num is a single digit number.
    IF A_input_num < 10 THEN
      // If this single digit is 1, the condition is met.
      IF A_input_num == 1 THEN
        RETURN 1 // True
      ELSE
        RETURN 0 // False
      ENDIF
    ELSE
      // Recursive Step: If A_input_num has more than one digit.
      // First, calculate the sum of its digits.
      sum_of_current_digits = this.get_sum_of_digits(A_input_num)
      // Then, recursively call `solve_check_digital_root` with this sum.
      // This process repeats until a single-digit sum is obtained,
      // which is then checked by the base case.
      RETURN this.solve_check_digital_root(sum_of_current_digits)
    ENDIF
  ENDFUNCTION
ENDCLASS
```

### Pseudocode for A3.py
```pseudocode
// This function calculates (A_base ^ B_exponent) MOD C_modulus
// using a recursive approach based on exponentiation by squaring.

FUNCTION modular_power_recursive(A_base, B_exponent, C_modulus):
  // Base Case 1: If A_base is 0.
  // 0 raised to any positive power is 0. 0^0 is handled by the next base case.
  IF A_base == 0 THEN
    RETURN 0
  ENDIF

  // Base Case 2: If B_exponent is 0.
  // Any number (except 0, handled above) raised to the power of 0 is 1.
  IF B_exponent == 0 THEN
    RETURN 1
  ENDIF

  // Recursive Step:
  // Calculate result for (A_base ^ (B_exponent / 2)).
  // The base used in the recursive call is (A_base % C_modulus) to keep numbers small.
  // `p_half_exponent_result` will be ((A_base % C_modulus)^(B_exponent/2)) % C_modulus.
  // The Python code does `self.pow(A%C, B//2, C) % C`. The final `%C` in Python's line for `p`
  // ensures `p` is within [0, C-1] if C is positive.
  p_half_exponent_result = this.modular_power_recursive(A_base % C_modulus,
                                                       B_exponent / 2, // Integer division
                                                       C_modulus)
  // The result from recursive call is already modulo C_modulus.
  // Python code: p = self.pow(A%C, B//2, C) % C -- this extra % C on p is redundant if self.pow correctly returns value mod C.
  // Assuming recursive call returns value already mod C_modulus.

  // Calculate (p_half_exponent_result * p_half_exponent_result) % C_modulus
  p_squared = (p_half_exponent_result * p_half_exponent_result) % C_modulus

  IF B_exponent IS EVEN THEN
    // If B_exponent is even, A^B = (A^(B/2))^2
    RETURN p_squared
  ELSE // B_exponent IS ODD
    // If B_exponent is odd, A^B = A * (A^(B/2))^2 = A * p_squared
    // Ensure A_base is also taken modulo C_modulus for the final multiplication.
    RETURN ((A_base % C_modulus) * p_squared) % C_modulus
  ENDIF
ENDFUNCTION
```

### Pseudocode for HW1.py
```pseudocode
// This function calculates the A-th Fibonacci number using a direct recursive definition.
// Fibonacci sequence: F(0) = 0, F(1) = 1, F(n) = F(n-1) + F(n-2) for n > 1.
// Note: This direct recursive approach is highly inefficient for larger values of A
// due to redundant calculations of the same Fibonacci numbers.

FUNCTION findAthFibonacci_recursive(A_term_index):
  // Base Case 1: If the requested term index is 0.
  IF A_term_index == 0 THEN
    RETURN 0 // F(0) = 0
  ENDIF

  // Base Case 2: If the requested term index is 1.
  IF A_term_index == 1 THEN
    RETURN 1 // F(1) = 1
  ENDIF

  // Recursive Step: For A_term_index > 1.
  // The Fibonacci number is the sum of the previous two Fibonacci numbers.
  // The lines `a, b = 0, 1` present in the Python source code are not utilized
  // by the recursive calculation and seem to be remnants or unused.

  fibonacci_A_minus_1 = this.findAthFibonacci_recursive(A_term_index - 1)
  fibonacci_A_minus_2 = this.findAthFibonacci_recursive(A_term_index - 2)

  RETURN fibonacci_A_minus_1 + fibonacci_A_minus_2
ENDFUNCTION
```

### Pseudocode for HW2.py
```pseudocode
// This function calculates the sum of the digits of a non-negative integer `A_number` using recursion.
// This logic is identical to A1.py in this directory.
FUNCTION calculate_sum_of_digits_recursive_v2(A_number):
  // Base Case: If the number becomes 0, it means all digits have been processed.
  // The sum of digits of 0 is 0.
  IF A_number == 0 THEN
    RETURN 0
  ENDIF

  // Recursive Step:
  // 1. Get the last digit of A_number using the modulo operator (A_number MOD 10).
  last_digit = A_number MOD 10

  // 2. Get the rest of the number by removing the last digit (A_number / 10 using integer division).
  remaining_digits_part = A_number / 10 // Integer division

  // 3. Recursively call the function with the `remaining_digits_part`
  //    and add the `last_digit` to the result of this recursive call.
  sum_of_other_digits = this.calculate_sum_of_digits_recursive_v2(remaining_digits_part)

  RETURN last_digit + sum_of_other_digits
ENDFUNCTION
```
